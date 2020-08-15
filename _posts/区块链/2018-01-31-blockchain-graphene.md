---
published: true
author: Robin Wen
layout: post
title: "BTS、Steem、EOS 背后的石墨烯技术"
category: 区块链
summary: "目前区块链生态系统主要分为三类，一类是比特币生态系，一类是以太坊生态系，而另外一种就是石墨烯生态系。比特币生态包括 BTC 以及其数量众多的分叉币，BTC 是加密数字货币的开山鼻祖，拥有最为广泛的共识。以太坊生态系又叫做 ERC20 Token，CoinMarketCap 上绝大多数的 Token 都是基于以太坊 ERC20。以太坊生态提供的智能合约，可以极为简便的发行 Token，项目再利用 ICO 的方式进行快速融资。石墨烯生态的代表有 BTS，Steem 和 EOS。石墨烯采用的是 DPOS 的共识机制，出快速度大约为 1.5s，石墨烯技术使得区块链应用更高的交易吞吐量，BTS 可以处理十万级别的 TPS，而 EOS 则是宣称百万级别的 TPS。同时石墨烯技术高并发处理能力也是比特币和 ETH 无法做到的。本文首先介绍了目前加密数字货币的三大生态，接着介绍了石墨烯技术，其中引用了币探专访对 YOYOW 创始人白菜的专访。然后介绍了石墨烯的三大代表项目 BTS、Steem 和 EOS，其中对每个项目做了些许点评。接着介绍了国内项目 YOYOW 和 GCS。希望本文能够给想了解石墨烯技术的读者一个指引。"
tags:
- 区块链
- Blockchain
- 石墨烯
- Graphene
- BitShares
- Steem
- EOS
---

`文/Robin`

> 本文由币乎(bihu.com)优质内容计划支持。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 23 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

## 0x00 前言
***

**目前区块链生态系统主要分为三类，一类是比特币生态系，一类是以太坊生态系，而另外一种就是石墨烯生态系。** 比特币生态包括 BTC 以及其数量众多的分叉币，BTC 是加密数字货币的开山鼻祖，拥有最为广泛的共识。以太坊生态系又叫做 ERC20 Token，CoinMarketCap 上绝大多数的 Token 都是基于以太坊 ERC20。以太坊生态提供的智能合约，可以极为简便的发行 Token，项目再利用 ICO 的方式进行快速融资。石墨烯生态的代表有 BTS，Steem 和 EOS。石墨烯采用的是 DPOS 的共识机制，出快速度大约为 1.5s，石墨烯技术使得区块链应用更高的交易吞吐量，BTS 可以处理十万级别的 TPS，而 EOS 则是宣称百万级别的 TPS。同时石墨烯技术高并发处理能力也是比特币和 ETH 无法做到的。

## 0x01 石墨烯技术
***

石墨烯是区块链工具组，由 **Cryptonomex** 公司开发，GitHub 项目地址：[https://github.com/cryptonomex/graphene](https://github.com/cryptonomex/graphene)，采用 C++ 编写，丹尼尔 • 拉里默（Dan Larimer）是 Cryptonomex 的创始人，而他的父亲斯坦•拉里默（Stan Larimer）是 Cryptonomex 的主席。Cryptonomex 基本上都是在石墨烯区块链库基础上做开发的，石墨烯区块链库已经被多个区块链所采纳，比如 BitShares，Muse，Identabit，Play 等。[1] 与大多数数字货币类似，Graphene (石墨烯) 使用区块链来记录参与者的转账信息及市场行为。由于每个区块总是指向前一个区块，因此一个区块链条包含了所有在网络上发生的交易信息。区块链是一个公开的、可审计的账簿，每个人都能够查看详细数据，并验证交易、市场订单和买卖盘数据。

![2018-01-30-blockchain-graphene](https://cdn.dbarobin.com/0xt24BW.jpg)

> 题图来自: © theschrammhit001 / Why EOS may represent your best bet if you dream of turning $10K into a million / steemit.com

根据 1 月 29 号币探对 YOYOW 创始人白菜的采访，我们得知石墨烯有以下特点：[2]

第一是 **转账速度特别快**。现在的平均确认时间是 1.5 秒，出块时间是 3 秒，在石墨烯进一步进化的 EOS 上可能到了零点几秒，所有的延迟仅仅只是来源于网络，而不是处理本身，所以它的性能是非常强大的。我们对比一下：比特币是 10 分钟出块，以太坊大约是 1 分钟；确认时间上比特币是 1 小时，以太坊是十几分钟，石墨烯只需要秒级的时间。

第二是 **吞吐量比较高**。石墨烯的吞吐量现在实测大约是 3300 笔每秒，理论上可以到 10 万次，甚至可以扩展到百万次，比如按照 EOS 的规划就可以达到百万次。对比一下：比特币大约每秒七笔，以太坊每秒三四十笔，这完全不是一个数量级。在真正解决实际问题时，很明显每秒几笔是不符合要求的，那每秒 3000 多笔基本上已经赶上了 VISA 的处理能力，已经算一个工业级的区块链产品。

第三是 **石墨烯极其稳定**。石墨烯技术开发运行了这么久，从来没有出过明显的 BUG，也没有资产被盗的情况。

第四是 **功能非常强大、完备、容易操作**。如果我们用过一些桌面端的钱包就会发现，比特股钱包的应用性是最强的。以多重签名来举例：比特币也有多重签名，但是比较复杂，而且功能特别简单，只有 M/N 这种模式，就是说如果是 5 个人做多重签名，3 个人同意就可以通过，这是一个很简单的多重签名。石墨烯上的多重签名功能是可以用作公司治理的，它可以设定两个参数：首先它可以设置百分比，每个人占多少百分比，无论多少人都可以随便设。第二个是阈值，就是超过多少个签名就可以生效。假设说现在想做一个 7 个人的理事会管理，有这样一些要求：任何 2 个人出事都不能影响资金的使用；至少 3 个人同意才可以动用资金；非核心成员至少 4 个人同意才能动用资金。这些条件设置好之后，可以很快的算出每个人的占比，这个多签就设置完成了，而且这是在 UI 上直接实现，在操作界面上的，而不是用命令行来实现的。

## 0x02 石墨烯三大代表项目
***

**介绍完石墨烯技术，我们来看下它的代表项目。**

### 2.1 BTS
***

**第一，BTS。** 比特股（BitShares，简称 BTS）是一个建立在区块链技术上的开源商业系统，由 3I 公司开发，结合了去中心化的全球支付系统（如支付宝），去中心化的数字货币交易所（如比特币中国），去中心化的证券交易所（如纳斯达克）的系统。BTS 是比特股系统中的计量单位。如果你是比特币的爱好者，可以理解为：BTS = BTC + BTCC + BitPay – Mining。它也是没有热钱包被盗风险、不会印白条发假币、没有作恶可能的交易所。如果你没有听说过比特币，那你可能需要先知道它能做什么：它没有每日限额的支付宝，它是手续费低至几乎为零、到账速度 10 秒钟的西联汇款，它是每年只需要数万美元基础安全维护成本、交易记录无法被任何人追踪监控的银行。从官方下载的 BitShares 软件就是个去中心化的交易所，我们把它叫做比特股内盘。国内鼓鼓时代开发的产品 [鼓鼓钱包](https://www.magicw.net)，可以理解成比特股内盘的移动端，目前提供 iOS 版和 Android 版。鼓鼓钱包可以人民币点对点充值 BITCNY，目前部分交易所支持 BITCNY，这也是人民币入金的一种渠道。

除了 BTS 内盘以及鼓鼓钱包，基于石墨烯技术的去中心化交易所还有 [OpenLedger](https://openledger.io)、[CYBEX](https://cybex.io)、[HelloBTS](https://www.hellobts.com) 等。

### 2.2 Steem
***

**第二，Steem。** Steem 是一个基于区块链技术的去中心化社交网络平台。在 [Steemit](https://steemit.com) 中，参与者可以得到数字货币形式的奖励。所谓参与，指的就是在 Steem 上发帖、回帖、讨论、点赞等等。而你的帖子质量越高、点赞的越多，收到的奖励就越高。Steem 上的文章多种多样，来自各个国家各个领域的作者在这里分享，并从中获取奖励。Steem 存在的问题，个人认为有以下几点。第一，网站体验欠佳，比如排版、字体等等。还有就是编辑器不够友好，当然有较好的 [busy.org](https://busy.org) 可以替代。第二，没有官方的移动端，作为一个内容平台，移动端是必不可少的。第三，大户过于集中，导致普通小虾米很难有较高的收益。第四，Steem 上奖励多的作者，发表的文章数特别多。Steem 发表文章，文章质量只是其一，文章的网络效应以及文章的数量才是关键。这样来说，Steem 高质量的沉淀其实是个问题。

就在近日，[Fenng](https://steemit.com/@fenng) 开通了 Steem，相信他会在这里分享不一样的内容。另外，我的 Steem：[https://steemit.com/@robinwen](https://steemit.com/@robinwen)，欢迎 Follow。

### 2.3 EOS
***

**第三，EOS。** EOS.IO 软件引入了一种区块链架构。 它为满足去中心应用纵向，横向扩展需求而设计。它通过一个可以在上面构建应用程序，类似操作系统的方式来实现。 该软件提供了账号，身份认证，数据库，异步通信，以及跨多核 CPU 或集群调度应用程序等功能。 由此而形成一种区块链架构， 它能达到每秒百万交易量的处理，用户可免费使用，且很容易快速部署去中心化应用。EOS 采用 DPOS 算法，在该共识算法下，在采用 EOS.IO 软件的区块链上，那些持有代币的人可以通过了一个连续的赞成投票系统来选择矿工。任何人可以选择参与挖矿，并有机会获得其相应比例的区块奖励，而这是跟其获得票数与其他所有矿工所获得票数成比例。如果读者对 EOS 感兴趣，可以去 [GitHub](https://github.com/EOSIO/Documentation) 阅读下它的相关文档。

## 0x03 国内项目
***

**目前除了 BTS、Steem、EOS，国内项目 [YOYOW](https://yoyow.org)、[GCS](http://blockchain.game) 也是基于石墨烯技术。这里笔者做一个简单的介绍。**

**YOYOW** 的名称来自英文 You Own Your Own Words，其目标是建立一个利用区块链技术，使用 去中心化的共识方式为内容生产领域进行贡献定价和权益回报的网络，使内容生产者、内容投资 者、内容筛选者和生态建设者都能得到合理的激励与回报。YOYOW 的设计初衷是构建一套合理的内容收益分配机制，同时构建一个基于用户内容评价的价值 网络。无论是文本、视频、图片、音频甚至直播类为主题的内容平台（包括并不局限于网站、Apps 等表现形式）都可以使用 YOYOW 网络构建出对应主题的内容激励平台。各平台之间，使用统一的 内容评价算法对内容进行评价。内容生产者、内容投资者、内容筛选者以及平台建设者等都将围绕 YOYOW 生态，基于用户对内容的评价来获得相应的合理的回报。

关于 YOYOW，读者不妨阅读下近日币探对其创始人的 [专访](https://mp.weixin.qq.com/s/aO--OCT2ZLepa--KcCXraw)，可以更深入地了解这个项目。

**Gamechain System（GCS 游戏链）** 是一个基于石墨烯框架开发的去中心游戏生态系统，旨在建立一个全球化、去中心、跨平台、资产具备统一价值保障且有高流通性的发行渠道平台，为全球游戏发行商提供便捷的资产发行渠道，为游戏资产发行、游戏虚拟资产交易、区块链游戏孵化、游戏 IP 版权追溯等提供底层公链生态服务。 GCS 游戏链将打造一个高质量的泛游社群，为游戏用户提供优质的社群交流、玩家社区衍生增值和虚拟资产交易等多平台服务。 GCS 游戏链着眼于重构游戏产业价值链，通过共享与共赢，赋能全球游戏开发者，予利每个玩家用户，为传统游戏行业打造区块链生态，为整个区块链游戏行业提供基础支撑。

## 0x04 小结
***

本文首先介绍了目前加密数字货币的三大生态，接着介绍了石墨烯技术，其中引用了币探专访对 YOYOW 创始人白菜的专访。然后介绍了石墨烯的三大代表项目 BTS、Steem 和 EOS，其中对每个项目做了些许点评。接着介绍了国内项目 YOYOW 和 GCS。**希望本文能够给想了解石墨烯技术的读者一个指引。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x05 参考
***

* [1] 8btc.com (2015-07-07). 区块链公司 Cryptonomex 创始人谈公司发展状况. Retrieved from [http://www.8btc.com/cryptonomex-dan-larimer](http://www.8btc.com/cryptonomex-dan-larimer).
* [2] 微信 (2018-01-29). 币探专访 | YOYOW 创始人白菜: 创建基于区块链的内容激励网络. Retrieved from [https://mp.weixin.qq.com/s/aO\-\-OCT2ZLep\-\-KcCXraw](https://mp.weixin.qq.com/s/aO--OCT2ZLepa--KcCXraw).

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
