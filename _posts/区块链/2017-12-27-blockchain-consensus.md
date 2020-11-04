---
published: true
author: Robin Wen
layout: post
title: "加密数字货币和传统分布式系统共识机制"
category: 区块链
summary: "现有的银行体系是一个中心化的系统，中心化的系统具有绝对的权威，人们信赖银行，相信银行是安全的，也相信银行是不会做假账的。银行是共识，法币也是共识，换句话说，具有等价交换的介质都有共识。由于加密货币多数采用去中心化的区块链设计，节点是各处分散且平行的，所以必须设计一套制度，来维护系统的运作顺序与公平性，统一区块链的版本，并奖励提供资源维护区块链的使用者，以及惩罚恶意的危害者。这样的制度，必须依赖某种方式来证明，是由谁取得了一个区块链的打包权（或称记账权），并且可以获取打包这一个区块的奖励；又或者是谁意图进行危害，就会获得一定的惩罚，这就是共识机制。本文从传统银行共识概念出发，阐述了分布式系统为什么需要共识机制。接着引出了分布式系统的的基本理论，然后阐述了加密数字货币的共识算法，最后给出了传统分布式系统里的共识算法。共识算法也是一个 Trade-off 的机制。"
tags:
- 区块链
- Blockchain
- Consensus
- Crypto
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 13 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

## 0x00 共识机制
***

现有的银行体系是一个中心化的系统，中心化的系统具有绝对的权威，人们信赖银行，相信银行是安全的，也相信银行是不会做假账的。银行是共识，法币也是共识，换句话说，具有等价交换的介质都有共识。

由于加密货币多数采用去中心化的区块链设计，节点是各处分散且平行的，所以必须设计一套制度，来维护系统的运作顺序与公平性，统一区块链的版本，并奖励提供资源维护区块链的使用者，以及惩罚恶意的危害者。这样的制度，必须依赖某种方式来证明，是由谁取得了一个区块链的打包权（或称记账权），并且可以获取打包这一个区块的奖励；又或者是谁意图进行危害，就会获得一定的惩罚，这就是加密数字货币的 **共识机制**。[1]

![2017-12-27-blockchain-consensus](https://cdn.dbarobin.com/0pFDjEc.jpg)

> 题图来自: © Cliff Küle / How Blockchain Technology & Gold Can Work Together / cliffkule.com

## 0x01 基本理论
***

### 1.1 一致性 (consensus)
***

何为一致性问题？简单而言，一致性问题就是相互独立的节点之间如何达成一项决议的问题。分布式系统中，进行数据库事务提交 (commit transaction)、Leader 选举、序列号生成等都会遇到一致性问题。[2]

假设一个具有 N 个节点的分布式系统，当其满足以下条件时，我们说这个系统满足一致性：

* 全认同 (agreement): 所有 N 个节点都认同一个结果
* 值合法 (validity): 该结果必须由 N 个节点中的节点提出
* 可结束 (termination): 决议过程在一定时间内结束，不会无休止地进行下去

分布式系统实现起来并不轻松，因为它面临着这些问题：

* 消息传递异步无序 (asynchronous): 现实网络不是一个可靠的信道，存在消息延时、丢失，节点间消息传递做不到同步有序 (synchronous)
* 节点宕机 (fail-stop): 节点持续宕机，不会恢复
* 节点宕机恢复 (fail-recover): 节点宕机一段时间后恢复，在分布式系统中最常见
* 网络分化 (network partition): 网络链路出现问题，将 N 个节点隔离成多个部分
* 拜占庭将军问题 (byzantine failure): 节点或宕机或逻辑失败，甚至不按套路出牌抛出干扰决议的信息

一致性还具备两个属性，一个是强一致 (safety)，它要求所有节点状态一致、共进退；一个是可用 (liveness)，它要求分布式系统 24*7 无间断对外服务。**FLP 定理 (FLP impossibility)** 已经证明在一个收窄的模型中 (异步环境并只存在节点宕机)，不能同时满足 safety 和 liveness。

### 1.2 2PC
***

2PC(tow phase commit) **两阶段提交**，顾名思义它分成两个阶段，先由一方进行提议 (propose) 并收集其他节点的反馈 (vote)，再根据反馈决定提交 (commit) 或中止 (abort) 事务。我们将提议的节点称为协调者 (coordinator)，其他参与决议节点称为参与者 (participants, 或 cohorts)。

### 1.3 3PC
***

3PC(three phase commit) 即 **三阶段提交**。

在 2PC 中一个 participant 的状态只有它自己和 coordinator 知晓，假如 coordinator 提议后自身宕机，在 watchdog 启用前一个 participant 又宕机，其他 participant 就会进入既不能回滚、又不能强制 commit 的阻塞状态，直到 participant 宕机恢复。这引出两个疑问：

* 能不能去掉阻塞，使系统可以在 commit/abort 前回滚 (rollback) 到决议发起前的初始状态
* 当次决议中，participant 间能不能相互知道对方的状态，又或者 participant 间根本不依赖对方的状态

相比 2PC，3PC 增加了一个准备提交 (prepare to commit) 阶段来解决以上问题。

### 1.4 CAP
***

CAP 由 Eric Brewer 在 2000 年 PODC 会议上提出，是 Eric Brewer 在 Inktomi[3] 期间研发搜索引擎、分布式 web 缓存时得出的关于数据一致性 (consistency)、服务可用性 (availability)、分区容错性 (partition-tolerance) 的猜想：

> It is impossible for a web service to provide the three following guarantees : Consistency, Availability and Partition-tolerance.

该猜想在提出两年后被证明成立，成为我们熟知的 **CAP 定理**：

* 数据一致性 (consistency)：如果系统对一个写操作返回成功，那么之后的读请求都必须读到这个新数据；如果返回失败，那么所有读操作都不能读到这个数据，对调用者而言数据具有强一致性 (strong consistency) (又叫原子性 atomic、线性一致性 linearizable consistency)
* 服务可用性 (availability)：所有读写请求在一定时间内得到响应，可终止、不会一直等待
* 分区容错性 (partition-tolerance)：在网络分区的情况下，被分隔的节点仍能正常对外服务

在某时刻如果满足 AP，分隔的节点同时对外服务但不能相互通信，将导致状态不一致，即不能满足 C；如果满足 CP，网络分区的情况下为达成 C，请求只能一直等待，即不满足 A；如果要满足 CA，在一定时间内要达到节点状态一致，要求不能出现网络分区，则不能满足 P。

C、A、P 三者最多只能满足其中两个，和 FLP 定理一样，CAP 定理也指示了一个不可达的结果 (impossibility result)。[3]

### 1.5 选举 (electioin)
***

一致性问题 (consistency) 是独立的节点间如何达成决议的问题，选出大家都认可的 leader 本质上也是一致性问题，因而如何应对宕机恢复、网络分化等在 leader 选举中也需要考量。[4]

比如 MHA 在主节点故障时会根据配置文件的配置来决定谁来当选新的主节点，如果想要某个节点作为备用的主节点，可以设置 **candidate_master = 1**。如果不希望这个节点被选举为主节点，设置 **no_master = 1**。

再比如 Redis Sentinel 集群里，也会根据 Redis 从节点的 **slave-priority** 配置来决定选举优先级。

### 1.6 多数派 (quorum)
***

在网络分化的场景下以上 Bully 算法会遇到一个问题，被分隔的节点都认为自己具有最大的序号、将产生多个 leader，这时候就需要引入多数派 (quorum)。多数派的思路在分布式系统中很常见，其确保网络分化情况下决议唯一。

多数派的原理说起来很简单，假如节点总数为 2f+1，则一项决议得到多于 f 节点赞成则获得通过。leader 选举中，网络分化场景下只有具备多数派节点的部分才可能选出 leader，这避免了多 leader 的产生。

比如 Redis Sentinel 集群里，假如有 5 个节点，quorum 设置为 4，也就是说有大于等于 4 个 Sentinel 节点确认某个 Redis Master 出现 SDOWN，那么 Redis Maser 进入 ODOWN 的状态。

SDOWM 表示 Objectively Down，单个 Sentinel 节点在 down-after-milliseconds 时间内没有收到 Redis masterping 的有效回复，这个 Redis Master 进入 SDOWN 主观下线状态。

ODOWN 表示 Objectively Down，接收多个（>=quorum) Sentinel 节点的 SDOWN 的确认，这个 Redis Master 进入 O_DOWN 客观下线状态。O_DOWN 将会触发 Sentinel leader 执行 Failover。Failover 操作会切换到优先级较高的从节点，然后保证业务的高可用。

### 1.7 租约 (lease)
***

选举中很重要的一个问题，以上尚未提到：怎么判断 leader 不可用、什么时候应该发起重新选举？最先可能想到会通过心跳 (heart beat) 判别 leader 状态是否正常，但在网络拥塞或瞬断的情况下，这容易导致出现双主。

租约 (lease) 是解决该问题的常用方法，其最初提出时用于解决分布式缓存一致性问题，后面在分布式锁等很多方面都有应用。

## 0x02 拜占庭将军问题
***

区块链是一个分布式系统。李笑来老师的 [INB Principles](https://github.com/xiaolai/INB-Principles) 里提到几条原则：[5]

* 这世界真的需要这东西吗？
* 它解决了什么原本没有被解决的问题？
* 去中心化在这件事儿上真的必要吗？
* 它真的必须账务公开吗？
* 账务公开的存在真的会提高它的效率吗？
* 它在多大程度上更接近一个 DAC (Decentralized autonomous corporation， 去中心化自治公司)？
* 如果我们决定投资，那么我们应该用我们资金的多大比例去投资？

数据库里也有分布式数据库。分布式数据库的基本特点包括：物理分布性、逻辑整体性和站点自治性。从这三个基本特点还可以导出的其它特点有：数据分布透明性、按既定协议达成共识的机制、适当的数据冗余度和事务管理的分布性。一个分布式系统一定会有共识机制，随之而来的就是 **拜占庭将军问题**。

我们来看看维基百科是怎么解释这个问题的。[6]

> 一组拜占庭将军分别各率领一支军队共同围困一座城市。为了简化问题，将各支军队的行动策略限定为进攻或撤离两种。因为部分军队进攻部分军队撤离可能会造成灾难性后果，因此各位将军必须通过投票来达成一致策略，即所有军队一起进攻或所有军队一起撤离。因为各位将军分处城市不同方向，他们只能通过信使互相联系。在投票过程中每位将军都将自己投票给进攻还是撤退的信息通过信使分别通知其他所有将军，这样一来每位将军根据自己的投票和其他所有将军送来的信息就可以知道共同的投票结果而决定行动策略。

系统的问题在于，将军中可能出现叛徒，他们不仅可能向较为糟糕的策略投票，还可能选择性地发送投票信息。假设有 9 位将军投票，其中 1 名叛徒。8 名忠诚的将军中出现了 4 人投进攻，4 人投撤离的情况。这时候叛徒可能故意给 4 名投进攻的将领送信表示投票进攻，而给 4 名投撤离的将领送信表示投撤离。这样一来在 4 名投进攻的将领看来，投票结果是 5 人投进攻，从而发起进攻；而在 4 名投撤离的将军看来则是 5 人投撤离。这样各支军队的一致协同就遭到了破坏。

由于将军之间需要通过信使通讯，叛变将军可能通过伪造信件来以其他将军的身份发送假投票。而即使在保证所有将军忠诚的情况下，也不能排除信使被敌人截杀，甚至被敌人间谍替换等情况。因此很难通过保证人员可靠性及通讯可靠性来解决问题。

上述的故事映射到计算机系统里，将军便成了计算机，而信差就是通信系统。再映射到区块链技术里，每个节点便是将军，而这个通信系统就是共识机制。而共识机制究竟是什么，它是计算机里的算法，由算法来保证这个共识机制。

## 0x03 加密数字货币共识算法
***

加密数字货币主流的共识算法有：

* PoW：Proof of Work，工作量证明
* PoS：Proof of Stake，权益证明
* DPoS：Delegated Proof of Stake，委托权益证明
* PBFT：Practical Byzantine Fault Tolerance，实用拜占庭容错算法
* PoSpace/PoC：Proof-of-space/Proof-of-Capacity，容量证明
* PoA：Proof of activity，活动证明
* PoB：Proof of burn，销毁证明
* PoET：Proof of elapsed time，运行时间证明
* Casper：Ethereum 提出

我们一起来了解这些算法是什么，以及这些算法有什么优劣。

### 3.1 PoW：Proof of Work，工作量证明
***

工作量证明（Proof-of-Work，PoW）是一种对应服务与资源滥用、或是阻断服务攻击的经济对策。一般是要求用户进行一些耗时适当的复杂运算，并且答案能被服务方快速验算，以此耗用的时间、设备与能源做为担保成本，以确保服务与资源是被真正的需求所使用。PoW 原理是由各节点以算力解题的方式来竞争当前区块的写入权，最快解得答案者将答案与他自己选择好的交易内容（也就是当前区块内容）发布给其它节点验证，若其它节点验证区块内容正确且同意他是最早解题者，则加入此链并将此区块内容储存起来，并以此区块内容作为依据开始解下个题目。[7]

**POW 的优点有：**
(1) 为最安全的公有链共识机制，比特币采用的是此共识算法
(2) 机制较简单容易实行
(3) 相对公平的挖矿机制（也就是加密货币的产生与分配）

**POW 的缺点为：**
(1) 消耗大量能源，算力是以能源消耗为代价制造出来的，比如现在的矿机挖矿需要消耗巨大的电力
(2) 区块的确认时间较难缩短，比特币网络拥堵问题日益严重
(3) 可能产生分叉，需要等待多个确认才能完成交易
(4) 基于（3），理论上我们可说 POW 没有最终性，因为永远可能出现更长链来取代当前帐本，但实际上六个确认后其机率已趋近于 0

**使用 POW 共识算法的数字货币，代表的有 BTC、LTC、ETH 等。**

### 3.2 PoS：Proof of Stake，权益证明
***

POS 即拥有越多加密货币（权益）的锻工越容易拿到当前区块的写入权（挖到矿）。它与 POW 不同，不需要消耗大量的算力，因为没有算力竞争的需求。原始版本的 POS 在创世初期就已产生出所有的货币，意即新的区块并不会诞生新的货币，因此执行节点验证者不称为矿工，改称锻工，但他们仍然可拿到交易手续费作为奖励。后来版本的 POS 则亦有出现区块挖矿奖励的设计，尤其若要将 POS 应用在公有链上的话。[8]

而 POS 具体而言如何选出当前区块的写入者？若总是选择权益最高者，则拥有最多货币的成员将永远独占写入权，因此通常实行底下两种方法：

**(1) 乱数化的选择方法：**
产生随机乱数，并以乱数搭配特定公式来选出写入者，唯权益高者仍有较大机率被选中。

**(2) 依据货币年龄的选择方法：**
此方法被用于改善大者恒大的情况，具体可参考 Peercoin 的做法，持有的货币超过 30 天后可加入写入权竞争，将每批货币的数量乘以持有天数加总起来，可得到一个数字，此数字越大则有越高的机率被选为写入者，且一旦获得写入权则此批货币的币龄将被归零，需再持有 30 天才能重新加入计算。唯上述所提的数字有上限值，币龄超过 90 天将无法进一步提高机率，持有大量的货币也可能达到机率最大值。这个方法可有效改善集中化的情形，也大幅增加了攻击的难度。

**POS 的优点有：**
(1) 不须竞争算力，因此低耗能
(2) 竞争写入权的成员必定拥有货币，因此相较于破坏，他们宁可选择守护系统，以避免币值蒸发
(3) 相较于 POW，同样规模的硬体预算，可保护更多的链上资产

**POS 的缺点为：**
(1) 拥有权益的成员未必希望参与记帐
(2) 拿到写入权的坏人若想改写出另一条假链，仅需花费很小的算力，这可能导致 double spending 攻击成功
(3) 做坏事的代价很低，没有惩罚机制
(4) 基于上述（2）和（3），实行 POS 需要搭配其它机制来改善此种情况，也因此比起 POW 较为复杂

**使用 POS 共识算法的数字货币，代表的有 BLK、QTUM 等。**[9]

### 3.3 DPoS：Delegated Proof of Stake，委托权益证明
***

POS 的缺点之一为拥有权益的成员未必希望参与记帐，而 DPOS 便可以解决这个问题，类似于民主的代议制度，它先通过权益证明选出记帐参与者（验证节点），再透过运作机制让这些验证节点竞争区块写入权。同时由于验证节点数量大幅缩减的缘故，可快速达成共识。

**DPOS 除了具备 POS 的优点以外，还有：**
(1) 缩小参与验证节点的数量，大幅提高共识速度，这也是 EOS 可以支撑高并发应用的关键原因之一

**DPOS 的缺点为：**
(1) 必须依赖加密货币，然而现今联盟链中许多情况并不存在加密货币
(2) 撇除 Casper 的话，多数 DPOS 仍无法有区块最终性

**使用 DPOS 共识算法的数字货币，代表的有 BTS、EOS 等。**

### 3.4 PBFT：Practical Byzantine Fault Tolerance，实用拜占庭容错算法
***

以上共识算法适用于公有链，PBFT 则是一种较适合用于联盟链的共识机制。PBFT 以数学证明模型来验证其取得共识的速度与容错能力，在其容错范围内时可得证系统无法被分叉，上述的 POW 或是 POS 在制度上皆能阻止骇客伪造大量有效益的验证节点，然而原生 PBFT 并无此能力，但若使用在联盟链，则由于联盟链的成员与节点原本就受到筛选与验证，自然地解决了此一问题。

我们再来看下什么是 **公有链、私有链和联盟链**。[10]

* **公有链**：世界上所有人都可以阅读和发送交易。如果他们合法都有希望看到自己被包括在内。世界上任何人都能参与到共识形成过程——决定在链条上添加什么区块以及现状是怎样的。作为中心化或准中心化信任的替代品，公有链受加密经济的保护，加密经济是经济激励和加密图形验证的结合，用类似工作量证明或权益证明的机制，遵循的总原则是人们影响共识形成的程度和他们能够影响的经济资源数量成正比。这类区块链通常被认为是 "完全去中心化"。
* **联盟链**：共识形成过程由预先选择的一系列的节点所掌控，例如，设想一个有 15 个金融机构的团体，每个机构都操作一个节点，为了使区块生效，其中的 10 个必须签署那个区块。阅读区块链的权利可能是公开的，或仅限于参与者，也有混合的路径，比如区块的根散表和应用程序编程接口一起公开，使公共成员可以进行一定量的查询，重获一部分区块链状态的加密图形证明。这类区块链被认为是 "部分去中心化"。
* **私人区块链**：书写许可对一个组织保持中心化。阅读许可可能是公开的或者限制在任意程度。应用很可能包含对单个公司内部的数据库管理，审查等，因此公共的可读性在很多情况下根本不必要，但在另一些情况下人们又想要公共可读性。

**PBFT 的优点有：**
(1) 系统在容错范围内无法被分叉
(2) 系统在容错范围内可容忍任何类型的错误
(3) 验证与共识速度极快
(4) 不须竞争算力，因此低耗能
(5) 基于前述点（1），区块具备最终性

**PBFT 的缺点为：**
(1) 若超过 1／3 的验证节点故障时，则系统无法继续运作
(2) 无防范骇客伪造大量验证节点的能力

### 3.5 PoC：Proof-of-Capacity，容量证明
***

绝大部分的替代协议都使用了某些类型的付费参与的模式。容量证明也无例外，但是你需要支付的是硬盘空间。你拥有的硬盘容量越多，那么你挖矿下一个区块并获得区块奖励的概率就越大。[11]

在容量证明系统中挖矿之前，该机制会产生大量的 plots 数据，将会存储在你的硬件中。你拥有的 plots 越多，你发现下一个区块的概率就越大。

通过投入大量的硬盘空间，你可以创建复制区块，并分叉系统。因此仍然存在无啊识别 nothing at stake 的问题。

容量证明的变种有高快存储证明和 [空间证明](https://eprint.iacr.org/2013/796.pdf)。

### 3.6 PoA：Proof of activity，活动证明
***

为了避免恶性通货膨胀，比特币最终将只有 2100 个币。这意味着，在某个时间点，比特币区块奖励将会停止，比特币的矿工只能获得交易费。

一些人认为这会导致严重的问题，人们的行为会是利己主义，而且会破坏系统。因此，活动证明（Proof of activity）作为比特币的一种替代激励结构而出现了。活动证明是一种结合了工作量证明和权益证明的混合方法。

在活动证明中，挖矿是以传统的工作量证明形式进行的，矿工互相竞争来解决密码学难题。挖矿产生的区块（它们更像是模板）不包含任何交易，因此获胜的区块只含有头部和矿工奖励地址。

这个时候，系统切换到权益证明。基于头部信息，选择随机的验证者来签名新的区块。验证者拥有的系统种的代币越多，它就越有可能被选择。只要所有的验证者都对其签名，模板就成为成熟的区块。

如果被选中的某些验证者没有完成区块，那么就会选择下一个获胜的区块，然后选择一群新的验证者，以此类推，直到区块获得正确数量的签名。交易费分发给了矿工和所有签名区块的验证者。

活动证明的弊端在于既存在工作量证明的弊端（需要很多的能量来挖矿区块），也存在权益证明的弊端（无法检测双重签名的验证者）。

**Decred 是目前唯一使用活动证明的币种。**

### 3.7 PoB：Proof of burn，销毁证明
***

在销毁证明中，不需要昂贵的计算设备，而是通过将币发送至一个地址来销毁它们，而且这是不可复原的。通过将你的币发送至想象中的地址，根据随机选择过程你获得了在系统中进行永久挖矿的权利。

根据销毁证明实施的不同，矿工可能会销毁原生代币或其它链的代币，例如比特币。你销毁的币越多，你获得挖矿下一个区块链的机会就越大。

随着时间的进行，你在系统中的股份会降低，因此最终你将会销毁更多的币来增加你挖矿区块的概率。（这有点类似比特币的挖矿过程，你需要持续不断地投入更先进的算力设备来维持算力。）

尽管销毁证明是一个有趣的替代方案，但它仍然需要浪费能源。另外一个问题是，算力流向了那些愿意销毁更多资金的人。

### 3.8 PoET：Proof of elapsed time，运行时间证明
***

芯片制造商因特尔发布了自己的共识协议运行时间证明。该系统的工作原理类似于工作量证明系统，但能量消耗很少。而且，参与者无需解决密码学难题，相反该机制使用了可信执行环境（TEE） – 例如 SGX – 来确保区块是以随机方式产生的，但是无需工作量。

因特尔的方法是基于 TEE 提供的保证等待时间的。根据因特尔的说法，该机制可以扩展至上千个节点，而且可以有效地运行在任何支持 SGX 的因特尔处理器中。

### 3.9 Casper：Ethereum 提出
***

Ethereum 为了解决上述 POS 共识中做坏事代价很低的缺点，提出的做法为要求有意参与验证节点者缴纳保证金（加密货币）才能参加，而如果验证节点违反规则参与了作假或攻击，甚至仅只是做了系统认为 “无效” 的事，则保证金将会被没收，这个共识机制则称为 Casper，Ethereum 宣称在未来某个适当的时机点将采用 Casper 作为共识算法。Casper 并不是单纯的 DPOS，实际上还参考了 PBFT 的机制来做改良。

单纯的 DPOS 和 POS 一样，会有分叉存在，无法有最终性，而 Casper 透过改良机制使区块的最终性获得保障，如果真的发生攻击使得两个同样高度的区块都被最终确认的话，则存在至少三分之一的验证节点违反了规则，这些节点的保证金将被没收，价值可能高达数千万美金，而由于这些加密货币从市场上消失的缘故，将导致货币的价格上升，这可能取代以往启用紧急硬分叉来修正攻击的手段。

## 0x04 传统分布式系统共识算法
***

* Paxos
* Raft
* Zab

### 4.1 Paxos
***

Paxos 是一种 **基于消息传递且具有高度容错特性的一致性算法**。[12] 1990 年由 Leslie Lamport 提出的 Paxos 共识算法，在工程角度实现了一种最大化保障分布式系统一致性（存在极小的概率无法实现一致）的机制。Paxos 被广泛应用在 Chubby、ZooKeeper 这样的系统中，Leslie Lamport 因此获得了 2013 年度图灵奖。[13]

作为一类协议，**Paxos 中包含 Basic Paxos、Multi-Paxos、Cheap Paxos 和其他的变种**。[14]

算法本身用语言描述极其精简：

**phase 1**
a) proposer 向网络内超过半数的 acceptor 发送 prepare 消息
b) acceptor 正常情况下回复 promise 消息
**phase 2**
a) 在有足够多 acceptor 回复 promise 消息时，proposer 发送 accept 消息
b) 正常情况下 acceptor 回复 accepted 消息

Paxos 被用于分布式系统中典型的例子就是 Zookeeper，他是第一个被证明的共识算法，其原理基于两阶段提交并扩展。

**Paxos 算法中将节点分为三种类型：**

* proposer：提出一个提案，等待大家批准为结案。往往是客户端担任该角色
* acceptor：负责对提案进行投票。往往是服务端担任该角色
* learner：被告知结案结果，并与之统一，不参与投票过程。

可能为客户端或服务端基本过程包括 proposer 提出提案，先争取大多数 acceptor 的支持，超过一半支持时，则发送结案结果给所有人进行确认。一个潜在的问题是 proposer 在此过程中出现故障，可以通过超时机制来解决。极为凑巧的情况下，每次新的一轮提案的 proposer 都恰好故障，系统则永远无法达成一致（概率很小）。Paxos 能保证在超过 50% 的正常节点存在时，系统能达成共识。[15]

### 4.2 Raft
***

Raft 算法是对 Paxos 算法的一种简单实现。Raft 核心思想很容易理解，如果数个数据库，初始状态一致，只要之后的进行的操作一致，就能保证之后的数据一致。由此 Raft 使用的是 Log 进行同步，并且将服务器分为三中角色：Leader，Follower，Candidate，相互可以互相转换。

**其基本过程为：**

* Leader 选举：每个 Candidate 随机经过一定时间都会提出选举方案，最近阶段中得票最多者被选为 Leader
* 同步 Log：Leader 会找到系统中 Log 最新的记录，并强制所有的 Follower 来刷新到这个记录，这里的 Log 指的是各种事件的发生记录

### 4.3 Zab
***

Zab 的全称是 Zookeeper atomic broadcast protocol，是 Zookeeper 内部用到的一致性协议。相比 Paxos，Zab 最大的特点是保证强一致性 (strong consistency，或叫线性一致性 linearizable consistency)。

和 Raft 一样，Zab 要求唯一 Leader 参与决议，Zab 可以分解成 discovery、sync、broadcast 三个阶段：

* discovery: 选举产生 PL(prospective leader)，PL 收集 Follower epoch(cepoch)，根据 Follower 的反馈 PL 产生 newepoch(每次选举产生新 Leader 的同时产生新 epoch，类似 Raft 的 term)
* sync: PL 补齐相比 Follower 多数派缺失的状态、之后各 Follower 再补齐相比 PL 缺失的状态，PL 和 Follower 完成状态同步后 PL 变为正式 Leader(established leader)
* broadcast: Leader 处理 Client 的写操作，并将状态变更广播至 Follower，Follower 多数派通过之后 Leader 发起将状态变更落地 (deliver/commit)
Leader 和 Follower 之间通过心跳判别健康状态，正常情况下 Zab 处在 broadcast 阶段，出现 Leader 宕机、网络隔离等异常情况时 Zab 重新回到 discovery 阶段。

### 4.4 对比
***

除 Paxos、Raft 和 Zab 外，Viewstamped Replication(简称 VR 也是讨论比较多的一致性协议。这些协议包含很多共同的内容 (Leader、quorum、state machine 等)，因而我们不禁要问：Paxos、Raft、Zab 和 VR 等分布式一致性协议区别到底在哪，还是根本就是一回事？[16]

Paxos、Raft、Zab 和 VR 都是解决一致性问题的协议，Paxos 协议原文倾向于理论，Raft、Zab、VR 倾向于实践，一致性保证程度等的不同也导致这些协议间存在差异。相比 Raft、Zab、VR，Paxos 更纯粹、更接近一致性问题本源，尽管 Paxos 倾向理论，但不代表 Paxos 不能应用于工程。基于 Paxos 的工程实践，须考虑具体需求场景 (如一致性要达到什么程度)，再在 Paxos 原始语意上进行包装。

## 0x05 小结
***

本文从传统银行共识概念出发，阐述了分布式系统为什么需要共识机制。接着引出了分布式系统的的基本理论，然后阐述了加密数字货币的共识算法，最后给出了传统分布式系统里的共识算法。共识算法也是一个 **Trade-off** 的机制。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x06 参考
***

* [1] 维基百科 (2017-09-20). 共识机制. Retrieved from [https://goo.gl/KFnuRi](https://goo.gl/KFnuRi).
* [2] bangerlee (2016-03-14). 分布式系统理论基础 - 一致性、2PC 和 3PC. Retrieved from [https://goo.gl/6uzyd5](https://goo.gl/6uzyd5).
* [3] bangerlee (2016-04-04). 分布式系统理论基础 - CAP. Retrieved from [https://goo.gl/P7r5Qh](https://goo.gl/P7r5Qh).
* [4] bangerlee (2016-08-21). 分布式系统理论基础 - 选举、多数派和租约. Retrieved from [https://goo.gl/PnU4T6](https://goo.gl/PnU4T6).
* [5] 李笑来 (2017-06-13). INBlockchain 的开源区块链 ICO 投资原则. Retrieved from [https://goo.gl/8bSyKc](https://goo.gl/8bSyKc).
* [6] 维基百科 (2008-08-01). 拜占庭将军问题. Retrieved from [https://goo.gl/AtZHzW](https://goo.gl/AtZHzW)
* [7] 维基百科 (2017-09-22). 工作量证明. Retrieved from [https://goo.gl/sqX6Gn](https://goo.gl/sqX6Gn).
* [8] 工研院资通所 高靖钧 丁川伟 陈耀鑫 马金沟 陈泽世 (106-05-09). 区块链简介与技术探讨. Retrieved from [https://goo.gl/9AVp4t](https://goo.gl/9AVp4t).
* [9] Proof-of-Stake (PoS) Coins list. [http://www.poslist.org](http://www.poslist.org)
* [10] 汪晓明 (2017-12-26). 公有链、联盟链、私有链及网络配置介绍. Retrieved from [https://goo.gl/R7Cro7](https://goo.gl/R7Cro7).
* [11] 区块链财经 (2017-03-05). 区块链共识协议指南. Retrieved from [https://goo.gl/RA7A1m](https://goo.gl/RA7A1m)
* [12] 维基百科 (2007-08-09). Paxos 算法. Retrieved from [https://goo.gl/QCoKj5](https://goo.gl/QCoKj5).
* [13] yeasy (2017-12-17). Paxos 与 Raft. Retrieved from [https://goo.gl/QNpr5j](https://goo.gl/QNpr5j).
* [14] Draveness (2017-12-18). 分布式一致性与共识算法. Retrieved from [https://goo.gl/5d97Uc](https://goo.gl/5d97Uc).
* [15] jerry81333 (2017-07-04). 区块链共识算法 PBFT（拜占庭容错）、PAXOS、RAFT 简述. Retrieved from [https://goo.gl/azGbks](https://goo.gl/azGbks).
* [16] bangerlee (2016-04-04). 分布式系统理论进阶 - Paxos. Retrieved from [https://goo.gl/6ggDHz](https://goo.gl/6ggDHz)

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。BTW，**李笑来老师也加入了**。

![区块链技术指北](https://cdn.dbarobin.com/pQxlDqF.jpg)

「区块链技术指北」相关资讯渠道：

* 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
* 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
* Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
* Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
* 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

* 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
* 个人博客，[https://dbarobin.com](https://dbarobin.com)
* 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
* Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
* Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
* 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
* 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

* BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
* ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
* PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
* 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
