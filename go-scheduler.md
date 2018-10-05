---
title: go scheduler
date: 2018-09-04 15:48:19
tags:
---

会用goroutine，也要了解其实现原理。这里是对[go scheduler](http://morsmachine.dk/go-scheduler)的翻译的概要

## Why go scheduler?
已经有OS内核中的调度器了，为什么还要自己写一个调度器？

1. posix方案中的上下文很重，切换较慢，go不需要这么重的上下文.
2. go的GC需要stall the world，使得内存处在一个一致的状态。GC的时间是不确定的，因此把这个责任交给OS内核，是不太可靠的

单独开发一个调度器，可以让它知道什么时候内存是一致，在开始GC时，runtime只需要等待当前在CPU上运行的那个程序即可，而不比等所有线程

用户态线程到内核线程有N:1, 1:1, M:N三种关系

#### N:1
多个用户线程跑在一个内核线程上，上下文切换很快，但是无法利用多核

#### 1:1
一个用户线程只在一个内核线程上跑，这样可以利用多核，但上下文切换很慢

#### M:N
多个goroutine在多个内核上跑，增加了调度的难度

### Go调度器
有三个结构:M P G
M:machine，代表内核线程，真正干活的人
P:调度的上下文，局部的调度器，使goroutine在一个线程上跑，它实现了N:1 -> M:N
G:一个goroutine,它有自己的栈，EIP，其他信息(如陷入等待的channel)，用于调度

每个M都有一个P，也有一个正在运行的G。还有一些在runqueue中的goroutine

为何要维护多个上下文？因为当一个OS线程被阻塞时，P可以转投奔另一个M
当阻塞返回时，它会偷一个context，如果没偷到就进入global runqueue
还有一种情况是context很快执行完了，它会去偷任务，要么从global runqueue要么从其他上下文，一般偷一半












