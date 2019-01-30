---
title:      浅谈basic-paxos,multi-paxos和raft
categories:
  - 系统
---

最近看了一些分布式一致性算法的相关文章，有basic-paxos相关的[文章](http://oceanbase.org.cn/?p=90)和multi-paxos相关的[文章](http://oceanbase.org.cn/?p=111)，结合自己前期学习的raft论文，总结一下它们的关系记录一下。

## 关于basic-paxos和multi-paxos

basic-paxos算法讲的是在分布式系统中如何对一个值达成一致的问题。在lamport的paxos made simple论文中也主要是解释如何解决这个问题。然后在结尾的Implementing a State Machine 的章节介绍了对一堆连续的问题达成一致, 这也是我们最常用的场景。最简单的方法就是对每一个问题独立运行一个Paxos 的过程，成为一次paxos的Instance, 但是这样每一个问题都需要Prepare和Accept两个阶段才能够完成。为了进行优化，比较好的办法就是只有一个proposer进行提议，同时省略掉prepare过程，直接进行accept的流程。这里的proposer在其它系统中也叫作leader。这样就渐渐形成了multi-paxos算法的思路，首先要运行一次完整的paxos算法选举出leader，有leader处理所有的读写请求，然后省略掉prepare过程。这是multi-paxos的主要思想。

## 关于multi-paxos和raft

从上面的描述中可以看出basic-paxos无法和raft进行比较，一次basic-paxos的过程只相当于同步raft算法中的一个日志项的过程。multi-paxos和raft的流程才是相似的。根据我上面的描述可以看出，raft算法其实是对multi-paxos的流程做了更加严格的限制：

1. raft中发送的请求（日志项）必须是连续的,而paxos中使可以并发的（就是说paxos可以同时发送日志项2,3，3不需要等待2处理完才能发送，但在状态集中日志顺序还是不变的，有点类似于TCP中的数据发送）
2. raft选主是有限制的, 必须有最新，最全的日志节点才可以当选. 而multi-paxos中是随意的。所以raft 可以看成是简化版本的multi-paxos(这里multi-paxos因为允许并发的写日志,因此不存在一个最新，最全的日志节点，因此只能这么做。这样带来的麻烦就是选主以后, 需要将主里面没有的日志给补全, 并执行commit 过程)

根据上面可以看出，raft更加的简单，paxos支持日志并发，更加的灵活。看一下raft和paxos中的日志结构图。

![paxos-raft-log](/images/paxos-raft/1.png)

从上图可以看出，raft里面follower的日志一定是leader的日志的子集, 而paxos不做这个保证。