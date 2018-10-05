---
title: raft cluster membership changes
date: 2018-09-12 11:47:59
tags: raft
---

以下为论文纯翻译:

之前我们讨论的集群成员都是静态的。有时有必要调整配置，如换下坏了的机器或者修改replica的数目
当然，我们可以下线集群进行更新，但更希望做到热更新，保证可用性。并且，这有人为风险

为了保证配置变更过程的安全性，变更过程中不允许一个term内两个leader被选出。不幸的是，任何方法都不能保证安全，因为不可能一次调整所有的机器

Raft使用two-phase的方式,有很多方法来实现two-phase。在Raft中，集群首先进入joint consesus，一旦joint consesus被commit,集群就进入新配置

joint consesus包括了新旧配置:

1. 所有log entries被复制到新旧配置并集里
2. 来自新旧配置的节点都可以被选为leader
3. 
