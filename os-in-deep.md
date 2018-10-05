---
title: os in deep
date: 2018-09-12 22:50:45
tags: os
---
关于进程和线程的区别:

fork is expensive. Memory is copied from the parent to the child, all descriptors are duplicated in the child, and so on. Current implementations use a technique called copy-on-write, which avoids a copy of the parent’s data space to the child until the child needs its own copy. But, regardless of this optimization, fork is expensive.

IPC is required to pass information between the parent and child after the fork. Passing information from the parent to the child before the fork is easy, since the child starts with a copy of the parent’s data space and with a copy of all the parent’s descriptors. But, returning information from the child to the parent takes more work.

Threads help with both problems. Threads are sometimes called lightweight processes since a thread is “lighter weight” than a process. That is, thread creation can be 10–100 times faster than process creation.

All threads within a process share the same global memory. This makes the sharing of information easy between the threads, but along with this simplicity comes the problem
