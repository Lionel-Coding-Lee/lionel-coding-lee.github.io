---
layout: post
title:  "Zookeeper的一致性与选举!"
date:   2018-03-07 09:36:54 +0800
categories: zookeeper
---

# 一致性算法Paxos
提到一致性算法（协议），无法绕开Paxos——Paxos Wiki，这是过去十年分布式一致性协议上的主流算法

首先将议员的角色分为proposers，acceptors，和learners（允许身兼数职）。proposers提出提案，提案信息包括提案编号和提议的value；acceptor收到提案后可以接受（accept）提案，若提案获得多数acceptors的接受，则称该提案被批准（chosen）；learners只能“学习”被批准的提案。划分角色后，就可以更精确的定义问题：

- 决议（value）只有在被proposers提出后才能被批准（未经批准的决议称为“提案（proposal）”）
- 在一次Paxos算法的执行实例中，只批准（chosen）一个value
- learners只能获得被批准（chosen）的value

在以上3个约束基础上，强化约束如下既可得到Paxos算法

- P1：一个acceptor必须接受（accept）第一次收到的提案
    - P1a：当且仅当acceptor没有回应过编号大于n的prepare请求时，acceptor接受（accept）编号为n的提案
- P2：一旦一个具有value v的提案被批准（chosen），那么之后批准（chosen）的提案必须具有value v
    - P2a：一旦一个具有value v的提案被批准（chosen），那么之后任何acceptor再次接受（accept）的提案必须具有value v
    - P2b：一旦一个具有value v的提案被批准（chosen），那么以后任何proposer提出的提案必须具有value v
    - P2c：如果一个编号为n的提案具有value v，那么存在一个多数派，要么他们中所有人都没有接受（accept）编号小于n的任何提案，要么他们已经接受（accept）的所有编号小于n的提案中编号最大的那个提案具有value v

Paxos依靠vote上的多数派原则，进行演化收敛——副作用就是当一个主题的proposal有多个value时收敛速度下降快，比如全局leader election

# Zab
而Zookeeper基于Paxos进行了改造——Zab（Zookeeper Automatic BroadCast Protocol）

Zab vs. Paxos

简单来讲，Zab采用了Paxos所使用的vote机制——多数派原则和消息传递特征

而从设计上，Paxos是针对state machine replication，既有限状态机，无法优秀的支持primary-backup replication

俩种replication的区别在于state machine的state set和state change是确定的，需要一致的是各个replications对state change request的一致顺序处理，而primary-back是指state change from last state，而且state set往往是没有边界的，也就不存在清晰的state change set
前者的replica在某一个时间点可能状态不同，但是保证在定序执行完所有request后状态一致，要求一致性算法做到request的定序（即使request是分散凌乱的），而后者是状态一个接一个的primary updates，要求一致性算法做到在updates过程中的容错
Paxos如果想应用在primary-backup replication中，必须以primary为leader，然后可以通过一次只接受一个request来保证primary update动作的原子性，但是这样会非常损耗性能

所以Zab对Paxos leader模式做的改进有：

- 从历史中恢复——保证了request的uncommit与commit状态
- 增加prefix ordering properties以支持primary/backup的并发性能——所有request被杂乱地送往leader
- 在更换leader期间保证proposal是定序的——增加了数据同步和数据广播

而Zab leader选择的依据，则是ZXid最高，意味着处理过最多的request，leader election本质上是全局范围内最大ZXid持有者的收敛：

- leader election（认知范围内收敛完成后，资历最老的带头闹革命）
- 建立epoch（向全国确立新年号，以保证没有前朝皇室余孽，如果没有收到所有人的确立回复，返回leader election）
- leader与follower同步（整顿内务，把前朝的帐算清楚，确定一下哪些follower可以上岗哪些不行，follower差不多就绪了，开始挂着新epoch搞生产）
- 数据广播（日常工作，发号施令，以[ZXid, data]向follower传达）

（貌似和kafka log replication的模式略相似）

