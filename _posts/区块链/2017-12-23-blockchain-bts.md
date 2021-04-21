---
published: true
author: Robin Wen
layout: post
title: "去中心化交易所 BitShares 浅谈"
category: 区块链
summary: "比特股（BitShares，简称 BTS）是一个建立在区块链技术上的开源商业系统，由 3I 公司开发，结合了去中心化的全球支付系统（如支付宝），去中心化的数字货币交易所（如比特币中国），去中心化的证券交易所（如纳斯达克）的系统。BTS 是比特股系统中的计量单位。如果你是比特币的爱好者，可以理解为：BTS = BTC + BTCC + BitPay – Mining。它也是没有热钱包被盗风险、不会印白条发假币、没有作恶可能的交易所。如果你没有听说过比特币，那你可能需要先知道它能做什么：它没有每日限额的支付宝，它是手续费低至几乎为零、到账速度 10 秒钟的西联汇款，它是每年只需要数万美元基础安全维护成本、交易记录无法被任何人追踪监控的银行。"
tags:
- 区块链
- Blockchain
- Crypto
- 去中心化交易所
- DEX
- BitShares
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 11 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

### 什么是 BTS
***

比特股（BitShares，简称 BTS）是一个建立在区块链技术上的开源商业系统，由 3I 公司开发，结合了去中心化的全球支付系统（如支付宝），去中心化的数字货币交易所（如比特币中国），去中心化的证券交易所（如纳斯达克）的系统。BTS 是比特股系统中的计量单位。如果你是比特币的爱好者，可以理解为：BTS = BTC + BTCC + BitPay – Mining。它也是没有热钱包被盗风险、不会印白条发假币、没有作恶可能的交易所。如果你没有听说过比特币，那你可能需要先知道它能做什么：它没有每日限额的支付宝，它是手续费低至几乎为零、到账速度 10 秒钟的西联汇款，它是每年只需要数万美元基础安全维护成本、交易记录无法被任何人追踪监控的银行。

![BitShares](https://cdn.dbarobin.com/fxHNOkG.jpg)

> 题图来自: © sthitaprajna /What exactly the reason to delist Bitshares from Bittrex / steemit.com

### 什么是去中心化？
***

这个系统在最初设定完规则后就可以自主开始在互联网中运行，不依靠中央的服务器和监管机构，每个参与的节点都是平等和自由竞争的。比特股和比特币都是一种去中心化自治系统（DAC）。

### 什么是 PTS？什么是 AGS？
***

PTS 和 AGS 是 3I 公司发行的认购凭证，两者的数量皆为 200 万，可用来兑换 3I 公司发行的各类系统的股票，其中包括 BTS，目前 BTS 已经被兑换完毕，现在只能等待兑换 3I 公司其他系统的股票（Shares）。两者的区别是，PTS 是需要挖矿才能获得，AGS 需要通过捐赠才能获得，目前 AGS 已经认领完毕。

### 比特股的总量是多少？如何分配的？
***

比特股最初的总量是 20 亿个 BTS。根据预先发布的规则，在 2014 年 2 月 28 日分配给 PTS 和 AGS 的持有者。其中设计它的 3I 公司也是和社区公平竞争，他们目前占有 5% 左右的 BTS。相对于其他数字货币，筹码分布要分散很多。11 月 15 日，比特股将 KeyID、DNS、PTS、AGS 和 Vote 等几个项目正式整合，演变成一个超级 DAC 项目，并增发 5 亿 BTS 分配给这几个项目的持有者。这 5 亿 BTS 被系统冻结，在接下来的 2 年时间里逐步解锁。

### 比特股的核心竞争力？
***

**1、比特资产（比如比特美元）**

比特资产一种革命性的创新，通过抵押发行机制，规避了现有加密货币的最大缺陷，即价格波动性。通过抵押 3 倍价值的 BTS，保证比特资产价值紧跟真实资产价格，比如 1 比特美元 = 1 美元。具备价值稳定性，可以免除商家标价、支付和持有时的面临的贬值风险，具有巨大的支付应用潜力。

**2、去中心化证券交易所**

比特股除了有内置的 “比特资产” 之外，还允许用户创建自己的 “比特资产”。这种自己发行的 “比特资产” 可以将其视为一种全新的去中心化 “证券交易所”。目前世界上有多个企业表示希望能够在去中心化交易所上进行 IPO，以摆脱被华尔街剥削的局面。在 2014 年 7 月，世界最大的在线零售商之一的 Overstock（年销售达到 13 亿美金）在自己的网站上明确指出比特股正是他们的选择之一。

**3、DPOS 机制**

就像现实中的支付系统，核心在于支付货币的结算。比特股系统采用了 DPOS（Delegated Proof of Stake，授权股权证明机制）方式，每一个 BTS 代表一张选票，每个持有 BTS 的股东可以将其拥有的选票授予一个代表。获得票数最多的前 101 位代表将按照既定的时间表轮流对交易进行打包结算，并且产生一个区块。DPOS 具有低能高效安全的优势，交易确认时间仅需 10 秒，目前业界最高效（比特币为 10 分钟以上），网络维护成本每年只需要数万美元。

### BTS 钱包
***

从 [官方网站](https://bitshares.org/download/) 可以下载到如下版本的 BTS 钱包：Windows、Mac 和 Linux。

### BITCNY
***

**BITCNY** 是 BitShares 系统上的一种合约，1 BITCNY 的持有者有权在任意时候从市场上获得等值于 1 元人民币的 BTS。也就是人，通过 BITCNY，用户可以直接人民币进行加密货币投资。相信未来的发展趋势，很有可能比场外交易平台还要有吸引力。目前场外交易平台溢价 10% 以上的太正常不过了。

目前 **AEX** 和 **BigOne** 上线了 BITCNY，这也是这两家交易所吸引人的地方。目前鼓鼓钱包支持人民币购买 BITCNY，购买之后转账到 AEX 和 BigOne BITCNY 账户就可以进行交易。AEX 支持 BITCNY 交易 BTC、LTC、ETH、ETC 等 17 种币种，BigOne 支持 BTC、EOS、QTUM、ETH、BTS 5 种币种。读者可以尝试下，为后期的交易做下准备。

### 小结
***

BTS 引入的 **DPOS** 共识机制全称是委托权益证明，少量节点参与块的生成确认，提升性能，与 PoS 一样无需消耗大量资源。EOS 同样采用的是 DPOS 共识机制。关于不同共识机制的对比，笔者后续会有文章阐述。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

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
