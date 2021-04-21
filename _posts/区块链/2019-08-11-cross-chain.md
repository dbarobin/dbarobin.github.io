---
published: true
author: Robin Wen
layout: post
title: 值得关注的跨链项目
category: 区块链
summary: 跨链、DeFi、Staking，这几个名词成为 2019 年区块链行业讨论最为广泛的话题。这三者互相关联，跨链是底层基础，Staking 是网络治理和经济模型，DeFi 是金融应用。这三者往往又可以相互转化，心系一个共同的目标。笔者介绍的这 6 个项目，基本上已经在 2019 年上线主网或者即将在 2019 年年底上线主网，可以说 2019 年是跨链元年。Polkadot 和 Cosmos 都采用 PoS 共识协议，Polkadot 有 Substrate，Cosmos 有 Cosmos SDK。然而 Nervos 却走了 PoW 的路线，这就衍生出了 Mining or Staking 的话题。笔者认为这两者将会在相当长的时间共存，毕竟 Mining or Staking 各自都有优劣，在这个快速发展的行业，还需要时间去实践和检验。此外，Cosmos 背靠币安，Nervos 依仗火币，跨链对于交易所也有非常重要的意义。不管跨链怎么发展，最终的目标都只有一个，Mass Adoption。
tags:
  - 区块链
  - Blockchain
  - 跨链

---

`文/Robin`

这是「区块链技术指北」的第 **65** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 前言
***

跨链、DeFi、Staking，这几个名词成为 2019 年区块链行业讨论最为广泛的话题。这三者互相关联，跨链是底层基础，Staking 是网络治理和经济模型，DeFi 是金融应用。这三者往往又可以相互转化，心系一个共同的目标。

![](https://cdn.dbarobin.com/vibNxIo.jpeg)

> 题图来自: © EVEN Foundation / Analysis of crosschain platforms / medium.com

围绕跨链技术，有相当多的项目，较早可以追溯到 Ripple 使用的 Interledger 协议。谈到跨链，又不得不提到侧链。为了对现有的区块链网络进行扩容，不同的区块链网络都在探索和实践。从比特币网络的增大区块容量之争、Lightning Network、RSK、Liquid 等等，到以太坊的 Casper，再到基于 EOS 的 Worbli、WAX、Telos、BOSCore 等。然而，侧链只是当前链的补充，每一条链依然是信息孤岛。这就有了跨链，实现不同链的整合，让价值得以快速多渠道中转和流通。

Polkadot 和 Cosmos 无疑是跨链项目中的明星，然而跨链项目实在是太多，本文聊聊值得关注的跨链项目。

## 二 跨链项目
***

### 2.1 Polkadot
***

Polkadot 是一个可伸缩的异构多链系统。这意味着不像以往那些专注于不同程度潜在应用功能的单个区块链实现，Polkadot 本身被设计成不提供任何内在的功能应用。Polkadot 提供了中继链(relay-chain)，在其上可以存在大量的可验证的、全局依赖的 动态数据结构。我们称这些平行的结构化的区块链为平行链(parachains)，尽管也不要求它们必须是一条链。[1]

Polkadot 将采用 PoS 协议，其生态内的项目也会采用此协议，由此可以遇见，Polkadot 生态将会诞生大量的 Staking 应用场景。了解 Polkadot 以及其创始人 Gavin Wood，可以观看 8btc 做得视频专访（[《8 问》Polkadot 创始人 Gavin Wood：是时候将 POW 替换为 POS 了](https://www.8btc.com/video/394911)）。

此外，Polkadot 主网预计将于 2019 年 Q4 上线。BlockArk 还制作了 Polkadot 生态图，如下图：

![](https://cdn.dbarobin.com/cyW2vUV.jpg)

### 2.2 Cosmos
***

Cosmos 是一种独立平行的区块链网络，其中每条区块链通过 Tendermint 这样的经典拜占庭容错共识算法来运行。

网络中第一条区块链将会是 Cosmos 中心。Cosmos 中心通过全新区块链间通信协议来连接其他众多区块链(或将其称之为空间)。中心可以追踪无数代币种类，并且在各个连接的空间里记录代币总数。代币可以安全快速地从一个空间传递到另一个空间，两者之间无需体现汇兑流动性，因为所有空间之间的代币传输都会经过 Cosmos 中心。[2]

Cosmos 与 2019 年 3 月 14 正式上线主网。值得一提的是，币安链基于 Cosmos 开发，Binance Dex 就是币安链最大的应用。在 Cosmos 官网的 [生态图](https://cosmos.network/ecosystem) 中，可以看到除了币安还有大量基于 Cosmos 开发的应用。

关于 Polkadot 和 Cosmos 对比，建议阅读 [此文](https://www.chainnews.com/articles/406309243446.htm)。

### 2.3 Nervos
***

Nervos CKB (Common Knowledge Base，公共知识库) 是一 条 layer1 的区块链，它是为网络提供公共知识托管的去中心化的安全层。这里的公共知识是指通过全球共识验证的状态。比如，加密资产就是一种公共知识。

在 Nervos 中，CKB 和所有的 layer2 协议共同协作，一起为 加密经济服务。CKB(或者说 layer1)是定义和存储状态的地方， 而 layer2 (生成层，或者说计算层，这两个名词可互换) 则是处 理大多数交易以及生成新状态的地方。Layer2 的参与者最终会 在必要时，提交一些新生成的状态到 CKB 上。如果这些状态通 过了全节点的验证，则 CKB 会将它们安全地存储到全节点中。[3]

Nervos 是国内为数不多的具有全球影响力的公链项目。其 layer1 依然基于 PoW 共识。Nervos 预计将会在 2019 Q4 发布主网。值得一提的是，火币公链将由 Nervos 和火币共同打造。「火币公链」主要会做这几件事情：打造全球性的金融基础设施，搭建链上合规和监管的框架，支持各类资产上链，发展子链技术，提供 DeFi 服务。[4]

### 2.4 Mixin
***

这里提到的 Mixin，更多是指 Mixin Network，而不是我们熟知的 Mixin Messenger。

Mixin Network 是聚焦于区块链数字资产点对点交易的项目，提供了一整套闪电网络式的底层解决方案，目前支持 21 条公链、1000 多币种的交易。Mixin Network 项目同时推出自身的加密社交工具与去中心化交易所撮合引擎 Ocean.one，基于 Mixin Network 开发的 DApp 可以搭载于内部聊天工具中，用户在 Mixin Network 平台内账户转账是完全免费的。[5]

Mixin 生态笔者了解最多，所以笔者会在这个项目做更多的阐述。

Mixin Network 简单理解是最易用的跨链闪电网络，只聚焦于资产跨链。目前支持的 21 条公链（可能马上就会更新）可以在 [这个](https://mixin.one/network/chains) 页面看到。支持的数字资产种类和个数可以在 [这里](https://mixin.one/snapshots) 看到。普通用户接触 Mixin Network，最早接触的是 Mixin Messenger，这是一个基于 Signal 协议的默认端对端的 IM 工具。Mixin Network 目前有 30 个 [节点](https://mixin.network)，ExinPool 也是其验证节点。目前已切换至主网的资产有 XIN、CNB、ETH、OOO，后续会有更多的资产切换至主网。

Mixin Network 开放了开发者友好的 DApp 接口，笔者所在的团队 Exin 开发了 Mixin 生态最具活力的应用 ExinOne。加密货币买卖、秒级匿名免费转账、理财需求、聊天资讯、直播打赏……Mixin 的开放生态提供了对用户最为友好的入口。相比其他跨链项目，Mixin 可能是最为低调而且极具爆发力的项目，毕竟产品是给普通用户用的，程序员和 Geek 占少数。

### 2.5 ChainX
***

ChainX 重点是做数字资产网关，通俗点讲就是对已有的 BTC，ETH，BNB 等数字货币链做资产跨链。Polkadot 是任意消息跨链，数字资产跨链的这个子类别主要由 ChainX 来做。 Polkadot 上线以后，通过 ChainX 跨链过来的所有资产，可以流通到 Polkadot 网络的任何一条平行链（parachain） 。目前由于 Polkadot 还没有上线，ChainX 会以独立的链运行。未来 Polkadot 上线以后，我们会通过 ChainX parachain 的形式将当前跨链过来的所有资产再 Relay 到 Polkadot 网络。[6]

ChainX 团队是国内最为了解 Polkadot 技术的团队，ChainX 主网于 2019 年 5 月 25 上线，笔者负责的 ExinPool 也是 ChainX 的深度参与方。ChainX 支持的 BTC 自锁仓挖矿，在用户自行托管 BTC 的前提下，通过 OP_RETURN 就能参与到 ChainX 生态。

### 2.6 Edgeware
***

Edgeware 旨在通过采用完全不同的体系结构来解决可伸缩性问题。Edgeware 的 token 持有者可以使用链上投票来升级网络，然后节点自动下载运行时的新版本。关键决策是在链上做出的，创建一个具有更低协调开销和透明流程的系统，用于决定改进。[7]

Edgeware 也是 Polkadot 生态的项目，与 ChainX 不同的是，Edgeware 最早锁 ETH，ChainX 最早锁 BTC。Edgeware 是一个基于 WASM 运行、链上监管的智能合约平台，意味着开发者可以用他们习惯的语言进行编写，另外代币持有者和社区成员可以以编程方式在链本身上做出决策。[8]

### 三 小结
***

笔者介绍的这 6 个项目，基本上已经在 2019 年上线主网或者即将在 2019 年年底上线主网，可以说 2019 年是跨链元年。Polkadot 和 Cosmos 都采用 PoS 共识协议，Polkadot 有 Substrate，Cosmos 有 Cosmos SDK。然而 Nervos 却走了 PoW 的路线，这就衍生出了 Mining or Staking 的话题。笔者认为这两者将会在相当长的时间共存，毕竟 Mining or Staking 各自都有优劣，在这个快速发展的行业，还需要时间去实践和检验。此外，Cosmos 背靠币安，Nervos 依仗火币，跨链对于交易所也有非常重要的意义。不管跨链怎么发展，最终的目标都只有一个，**Mass Adoption**。

### 四 白皮书下载
***

笔者将本文介绍项目相关的中英文白皮书上传到了 Dropbox，方便读者下载阅读。

> 链接: https://www.dropbox.com/sh/7dalrji5iswb2dh/AABG6Ijyxl5Qlo0MSTSBo2w_a?dl=0
> 密码: JtkzRbd3WsC@W+vZ+Xhsh99iHMFf2;oK

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

### 五 参考资料
***

> [1] Polkadot（波卡链）：畅想一种异构的多链架构 (2017-08-11). 岳利鹏. Retrieved from [https://chainx.org/polkadot_cn.pdf](https://chainx.org/polkadot_cn.pdf).
> [2] Cosmos (2018-05-13). Jae Kwon. Retrieved from [https://github.com/irisnet/translation/blob/master/Cosmos/Whitepaper_Chinese.md](https://github.com/irisnet/translation/blob/master/Cosmos/Whitepaper_Chinese.md).
> [3] Nervos CKB 加密经济的共同知识库 (2019-02-26). Nervos Foundation. Retrieved from [http://www.nervoscommunity.com/files/Nervos%20%E7%99%BD%E7%9A%AE%E4%B9%A6.pdf](http://www.nervoscommunity.com/files/Nervos%20%E7%99%BD%E7%9A%AE%E4%B9%A6.pdf).
> [4] Nervos 与火币联手打造的金融公链究竟有何亮点？ (2019-07-31). NervosCommunity. Retrieved from [https://www.chainnews.com/articles/010036443810.htm](https://www.chainnews.com/articles/010036443810.htm).
> [5] Mixin Network 李林：如何为区块链开发者提供更好的在线支付体验？ (2019-06-27). 链茶馆. Retrieved from [https://www.chainnews.com/articles/289453499102.htm](https://www.chainnews.com/articles/289453499102.htm).
> [6] 基于波卡 Substrate 开发的首个资产跨链 ChainX，有多神奇？ (2019-05-31). HashBang. Retrieved from [https://www.chainnews.com/articles/280756136289.htm](https://www.chainnews.com/articles/280756136289.htm).
> [7] Edgeware 简介 / 白皮书中文翻译 (2019-05-21). 木南. Retrieved from [https://munan.tech/2019/05/21/edgeware](https://munan.tech/2019/05/21/edgeware).
> [8] 白话区块链访谈 Edgeware : 跨链生态开启，Edgeware 项目成为 “佼佼者”. 白话区块链. Retrieved from [https://www.hellobtc.com/kp/ft/06/1887.html](https://www.hellobtc.com/kp/ft/06/1887.html).

***

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

![区块链技术指北](https://cdn.dbarobin.com/3YzonTR.png)

「区块链技术指北」相关资讯渠道：

> 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
> 官网，[https://chainon.io](https://chainon.io)
> 官方博客，[https://blog.chainon.io](https://blog.chainon.io)
> 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
> Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
> Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
> Facebook，[https://www.facebook.com/chainone.org](https://www.facebook.com/chainone.org)
> 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

> 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
> 个人博客，[https://dbarobin.com](https://dbarobin.com)
> 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
> 简书，[https://www.jianshu.com/c/a37698a12ba9](https://www.jianshu.com/c/a37698a12ba9)
> Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
> Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
> 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
> EOS LIVE，[https://eos.live/user/robin](https://eos.live/user/robin)
> 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

> BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
> ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
> PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
> 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
