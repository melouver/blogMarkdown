---
title: raft log replication
date: 2018-08-26 20:40:32
tags: raft
---

上一节我们讲了选主的实现，这一次写的是Lab2的Part B: log replication。在尝试part B的测试样例后，我修改了之前的部分设计和bug，使得更吻合论文所要求的意思


## 从Leader同步log到follower
这个操作应该有一个单独的协程，在这里面会类似心跳包的实现，不过不同之处在于，它不会睡眠，而是不断尝试发AE RPC，直到返回success，这个时候Nextindex也到了log末尾。
仍然要注意RPC前后term改变和reply term过大时，我们应该放弃处理这次rpc结果。当返回success时，会更新matchindex 和nextindex，然后让增加commitindex的协程起来更新commitindex
commitindex更新完毕后，会让apply线程开始做事，不断apply到状态机
Start函数应该只append，然后通过条件变量触发这些协程干事情，

在AE handler里面，严格按照论文所述，先舍弃冲突及其之后的log，然后追加，同时更新follower的commitindex，也就是说， commitindex是由leader流向follower

心跳包不要掺和nextindex的事情，不然会发生错误的多次递减，从而越过了共同前缀

## 合并重复功能的代码，以避免维护多处导致错误
在退回到follower时，执行很多相同的代码，抽出来作为一个函数。考虑到candidate会select在两个channel上，因此注意先改变state再传入channel，这样一来，主线程会阻塞在timer的channel里，这个顺序很重要。
抽出来的这个函数，不一定每次都会reset timer，只有在给别人投票时，才会重置

## 2C
持久化部分应该说是十分简单了，就是encode/decode需要持久化的内容，然后在这些内容被更新时，立即写(这里是写内存)

## 论文中提到的nextIndex优化
当leader和follower没找到共享前缀时，leader会逐个entry回退，这十分低效，论文有提到让follower返回一些信息，这样一步回退。这个优化手段，在失败经常发生时，是十分有必要的，而且能显著减少RPC次数，不过我还没有实现，下一篇我会描述一下实现的坑

## leader的状态
主线程处于leader状态时，不要睡一会起来看一下，因为会耽误睡眠，应该也等在条件变量，一旦收到消息，立即回到follower状态


## 展望 Lab3
lab3会实现一个客户端，初步了解到的内容是需要retry，记录每个command的uuid，避免多次append

## 总结
各个协程的功能:

1. sendAE
从Leader同步entry到follower

2. sendRV
成为candidate之后请求投票

3. sendPeriodHeartBeat
成为Leader之后发送心跳

4. applyLoop
在commitindex更新后进行逐个apply

5. advanceCommitIdx
在更新matchindex之后(sendAE中RPC返回成功时)，更新commitindex(超过半数即可更新commitindex)


