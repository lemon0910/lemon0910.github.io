---
layout:     post
title:      快速理解Paxos算法
date:       2016-02-06 16:18:00
author:     "lemon"
categories: 系统
---

Paxos算法是Lamport于1990年提出的一种基于消息传递的一致性算法，它是非常重要的分布式一致性算法。由于Chubby系统和Zookeeper都基于paxos算法实现，因此该算法名声大噪，被许多学习分布式系统的人学习。

##Paxos干什么

Paxos算法解决的问题是一个分布式系统如何就某个值（决议）达成一致。一个典型的场景是，在一个分布式数据库系统中，如果各节点的初始状态一致，每个节点都执行相同的操作序列，那么他们最后能得到一个一致的状态。为保证每个节点执行相同的命令序列，需要在每一条指令上执行一个「一致性算法」以保证每个节点看到的指令一致。

## Paxos介绍

### 一个Acceptor

最简单的方式就是只有一个Acceptor，让它做决定是否批准一个值。每一个proposer提议一个值给Acceptor来批准，然后Acceptor批准一个值作为最终的值。但是这种简单的方式，没有办法解决Acceptor crash的问题，如果唯一的Acceptor crash了，整个系统变无法使用了。

### 多个Acceptor

为了解决这个问题，就必须要用到一种多数选择的方法。使用一个Acceptor的集合。然后只有其中的多数（超过1半）批准了一个值，这个值才可以确实是被最终被批准的。

如果使用多个Acceptor，则需要确定如何批准提议值。论文[Paxos Made Simple](http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf)中讲述了需要面对的各种问题，更方便理解paxos算法内容为什么是那样。这里直接讲述结论。

两阶段提交算法：

准备：每台机器可以生成自己的唯一序列号n，不会与其它机器重复（一种简单的实现就是取模运算的思路）。Proposer需要准备好自己的序列号n和要提交的值v。Acceptor需要记录自己收到的最大的序列值minProposalId，自己批准的数值的序列号acceptedProposalId和自己批准的数值acceptedValue。

*第一阶段 Prepare*

*P1a：Proposer发送Prepare*

Proposer 生成全局唯一且递增的提案序列号n，向 Paxos 集群的所有Acceptor发送 PrepareRequest消息，消息内容只需要包含序列号n，无需携带数值value。

*P1b：Acceptor应答Prepare*

 Acceptor收到PrepareRequest消息后，做出“两个承诺，一个应答”。

*两个承诺：*
第一，不再应答n小于等于（注意：这里是 <= ）当前PrepareRequest中n值的PrepareRequest消息。具体实现是如果n大于Acceptor记录的minProposalId，将minProposalId设置为n，然后不再应答n值小于minProposalId的PrepareRequest；
第二，不再应答n小于（注意：这里是 < ）当前PrepareRequest中n值的AcceptRequest消息。AcceptRequest消息后面讲到。

*一个应答：*
返回自己已经Accept（阶段二，从这也可以看出每个Proposer的两个阶段可能交叉）过的acceptedProposalId和acceptedValue，如果没有则返回空值;
注意：应答当前请求前，也要按照“两个承诺”检查是否会违背之前处理 PrepareRequest 时做出的承诺；

*第二阶段 Accept*

*P2a：Proposer发送Accept*

提案生成规则：Proposer收集到多数派应答的 PrepareResponse后，从多个应答的PrepareResponse消息中选择acceptedProposalId最大的对应的acceptedValue作为要发起Accept 的提案值，如果这个提案值为空值，则可以自己随意决定提案内容。然后携带上当前机器的序列号n，向Paxos集群中的所有Acceptor发送AccpetRequest消息。

*P2b：Acceptor 应答 Accept*

Accpetor收到AccpetRequest 后，检查AccpetRequest消息中的n值，如果n和minProposalId相等,将acceptedProposalId设置为n，acceptedValue设为消息中的提议值v。向Proposer返回acceptedProposalId，Proposer收集到多数派应答的AcceptResponse后，形成决议。
整个过程如下图：

![paxos](/images/basic-paxos/1.png)
