---
title: raft_leader_election
date: 2018-08-15 16:25:23
tags:
---

### Why this post?
这是MIT 6.824的lab2 part A，使用golang实现选主功能。原框架提供了修改过的rpc，通过channel模拟网络，加入了丢包，超时，网络隔离的情况。
在这部分实验中，让我对基于goroutine模型的并发编程有了实践

### 难点
在part A中，最重要的是明确一个raft server中多个goroutine的功能，它们的通信，并正确实现状态机的切换

### 一些缩写
AE: Append Entries RPC
用于心跳包和leader向follower发送新log entries
RV: Request Vote RPC
用于Candidate请求投票

### 踩过的坑

1. RPC(AE RV)没有设置超时，在某个机器被网络隔离的情况下，RPC一段时间后才超时返回，
又由于循环的设置不当，导致新leader没有压制住follower，放任其开启新一轮选举

解决方案:
对于心跳包，我们在单独一个goroutine里面实现。
raft server创建之初，就会开这个goroutine，在其中为每个peer开一个单独的goroutine，每一个新goroutine会等shouldSendHB变为非0，才进行一次心跳，然后睡眠。等待操作
使用cond实现。对于超时的RPC，会直接进入睡眠。这里可以知道，每个心跳包的最长时间为rpcTimeout+heartbeat interval。
超时操作使用两个goroutine+channel实现，一个goroutine里面进行rpc，一个进行time.Sleep，外部阻塞在channel，根据channel的返回结果判断这次RPC是否超时。

超时机制显著提升选举速度，也减少了RPC次数

2. 循环的设置问题
对于RV和心跳，使用不同同步策略(这个策略可能隐藏着问题，但在目前我的思考下这样符合要求，以后如果看过etcd等实现，可能会发现好的方案)

#### RV
一个candidate的一次election，只会向每个peer请求一次投票，如果RPC超时，那我们就认为这个票拿不到了，直接结束(wg.Done())
代码结构表现为：同样用cond等待一次election(iff follower timeout OR candidate election timeout)，进入后，使用waitgroup在外面等待所有goroutine返回，
如果拿到超过一半的票，就进化为leader，并且让状态机转换
```go
func sendRV() {
    for {
        wait for next election
        for each peer {
            go func() {
                send RV RPC
                wg.Done()
            }
            
        }
        wg.wait()
    }
}


```
#### HB
对每个peer的HB是独立的，并没有waitgroup等待新一轮HB然后一起发送。
大致结构如下:
``` go
初始化raft server时启动 go sendHB()
func sendHB() {
    for each peer {
        go func () {
            wait for cond
            send HB
            sleep(..)
        }()
    }
}

```

##### 条件变量的触发
对于RV的模式，使用Signal即可，因为只有一个goroutine等它
对于HB的模式，多个goroutine同时等在一个条件变量，显然需要broadcast
(这个bug在第一次出现时就通过日志一眼发现了，发生这样的错误说明以前没用过cond写代码 :( )

##### 反思
在我一开始实现时，HB用了RV一样的策略，一轮一轮地发HB，并且没有设置超时，导致某个节点被隔离后，新Leader根本压不住follower
现在来看，加入了超时以后，其实是可以一轮一轮发HB的。最终的选择是RV和HB用了不同的循环方式.


3. 状态机实现
raft paper中给出了非常准确的状态图，但想了两三天才摸到门道。
选主中状态最复杂的是Candidate，它不仅要响应election timeout，也要在选举成功或失败(遇到了higher term的RPC)时，切换状态。
##### Candidate
刚开始进入Candidate状态，会发送一轮RV(使用cond触发),然后陷入无限循环，使用select等两个channel:
一个是election timer，用于开启新一轮选举（这里并没有判断state是否为candidate，因为状态变化时，会先触发下一个channel)
一个是winElectionCh，接受true/false，接受true说明选举成功，可以转入leader状态，接受false说明选举失败，需要回到follower
``` go
			CandiLoop:
            for {
                select {
                case <-rf.timer.C:
                    DPrintf("%d election time out, cont\n", rf.me)
                    rf.mu.Lock()
                    rf.currentTerm = rf.currentTerm + 1
                    rf.votes = 1
                    rf.votedFor = rf.me
                    rf.rvArgs = RequestVoteArgs{rf.currentTerm, rf.me, rf.log[len(rf.log)-1].CommandIndex, rf.log[len(rf.log)-1].Term}
                    rf.shouldCont = 1
                    rf.contCond.Signal()
                    rf.mu.Unlock()
                    rf.resetTimer()
                case res := <-rf.winElectionCh:
                    DPrintf("%d got %v from winChannel\n", rf.me, res)
                    if res {
                        //赢得选举
                        rf.becomeLeader()
                        rf.stopTimer()
                        break CandiLoop
                    } else {
                        //收到有高term的leader的AE 或者 RV
                        //rf.backToFollower()
                        //rf.resetTimer()
                        break CandiLoop
                    }
                }
            }

```
##### Follower
只等待election timeout channel，如果等到，说明这段时间都没有被reset timer，那么进入candidate状态
``` go
        case Follower:
				<-rf.timer.C
				// 超时前没有收到AE 和RV RPC，所以变为candidate
				rf.becomeCandidate()


```
##### Leader
在选主过程中,leade的状态并不复杂。进入leader后， 先触发心跳goroutine。进入死循环，如果状态变为follower(注意，leader是不能变为candidate的！)，
那么停止心跳，重置timer，转为follower
```go
rf.shouldCont = 0 // may be useless
rf.shouldSendHB = 1
rf.hbCond.Broadcast()
			LdLoop:
				for {
					rf.mu.Lock()
					switch rf.state {
					case Leader:
						if rf.debugFlag {
							DPrintf("%d is leader now ~~", rf.me)
						}
						rf.mu.Unlock()
						time.Sleep(200 * time.Millisecond)
					case Follower:

						rf.shouldSendHB = 0
						rf.shouldCont = 0

						rf.mu.Unlock()
						DPrintf("%d is no longer leader.Back to follower \n", rf.me)
						break LdLoop
					}
				}
			}


```
4.重置timer的时间

raft在处理很多方面的问题时(如random election timeout)，都用的超时机制，来避免非常subtle的问题，由此可见良好的超时设计是实现raft正确行为的关键。
在什么时候应该重置timer?教学guide中有明确指出,reset timer iff:

a) 从current leader收到AE
current leader即如果AE中的term低，则不该reset自己的timer
b) 自己开启新一轮election
c) 你给别人成功投票

5.RPC中高term来临时，应该先退为follower状态,重置一些状态
RV中这样的实现，能减少RPC次数，不然会多一轮RV，才能给candidate投票

#### 测试
反复运行实验给的测试100次，发现均能够成功，初步认为part A ok。

## Summary
写完后来看，其实难度并不算高，但一开始确实遇到了非常大的阻力，一是来自于对golang并发模型的不熟练，二是状态机的切换设计一脸懵。
通过手动翻译论文和实验指导，反复阅读，终于想到了粗糙的做法，然后经过两三次改进，已经能够顺利通过实验中的测试，但这并不意味着没有问题，
只是测试样例不够多，在之后的实验中，可能会发现这些问题，并可能大改代码。


下一篇会描述part B的实现，会接受client的command，并在大多数节点replicate，再apply到状态机(这个是raft应用的状态机，如set,add某个变量)目前还在思考中，一些细节还不是很清楚，也涉及到单独的applier和client retry的实现(uuid标识).事情变得越来越有意思了 :) 








