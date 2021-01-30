---
published: true
author: Robin Wen
layout: post
title: "关于近期币安事件的思考"
category: 区块链
summary: "2 月 10 日，币安 CEO Changpeng Zhao 在 LinkedIn 发布了文章 Binance Incident Recap，FlockFleecer 做了翻译，阅读文章点击此处。从这篇文章中，我们可以分析出很多东西，也可以感悟到运营一个中心化交易所的不易。大的交易所出现故障，导致服务停摆，这也会是中心化交易所面临的极大信任问题。当然，宕机对交易所本身以及用户都会造成很大的影响。建立信任是很困难的，然而摧毁信任就很容易。换个角度，去中心化交易所是否会存在一些垄断性的项目，至少现在没有，但是这有可能是未来的一个趋势。再者，怎么样衡量去中心化还是个问题。过度追求高可用，带来的就是性能损失。过度追求性能，带来的又是高可用和稳定性的难题。未来的很大一段时间，还会存在中心化交易所和去中心化交易所并存的局势，并且会相互借鉴各自的实现。"
tags:
  - 区块链
  - Blockchain
  - 交易所
  - Exchange
  - 思考
  - 币安
  - Binance
---

`文/Robin`

> 本文由币乎(bihu.com)优质内容计划支持。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 26 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

2 月 10 日，币安 CEO Changpeng Zhao 在 LinkedIn 发布了文章 [Binance Incident Recap](https://www.linkedin.com/pulse/binance-incident-recap-changpeng-zhao/)，FlockFleecer 做了翻译，阅读文章点击 [此处](https://mp.weixin.qq.com/s/5VGkzYoPm6oN3Myu6RdPng)。

![2018-02-13-binance-upgrade](https://cdn.dbarobin.com/WgmaS6z.jpg)

> 题图来自: © B.C. / Binance Exchange and Token Summary (BNB) / boxmining.com

从这篇文章中，我们可以分析出很多东西，也可以感悟到运营一个中心化交易所的不易。

1、币安核心数据库不出意外采用的是 **MySQL**，高可用方案使用的是 MHA，但不排除做过定制优化。主库硬件故障的时候，会 failover 到从库，如果期间发生大量的 transaction，会导致主从数据不一致。涉及交易数据的数据库，牺牲掉用户体验，也要保证数据的完全一致。至于数据修复方案，为了保证数据地绝对一致，有可能是从头开始利用备份数据和增量 binlog 进行恢复。

2、runtime 数据应该是热点数据，为了保证性能，有可能使用的是 **Redis**。具体应用场景，比如价格走势图的及时更新、个人数字货币资产信息等。另外，不排除缓存数据与持久化数据之间有交互。

3、因数据无法同步就让整个交易所彻底瘫痪，证明 **降级服务和解耦** 做得比较糟糕。不能交易，但至少可以让用户登录、查询个人资产、显示价格走势等。有朋友会说，交易所无法交易，数字货币资产的价格不是最新的，这个没有问题，打开个人资产页面，可以显示故障之前最新的一次快照数据，让用户感受到自己的资产不受影响，这是很重要的。

4、**DDos** 攻击导致网站受到很到影响，说明币安很有可能自己没有做或者没有使用第三方的防护系统，遭受这种攻击，有第三的服务可以做清洗。

5、后续的升级优化，还有可能出现宕机，这对用户体验也是伤害，信任值也会大幅降低。

6、大的交易所出现故障，导致服务停摆，这也会是中心化交易所面临的极大信任问题。当然，宕机对交易所本身以及用户都会造成很大的影响。建立信任是很困难的，然而摧毁信任就很容易。

7、换个角度，去中心化交易所是否会存在一些垄断性的项目，至少现在没有，但是这有可能是未来的一个趋势。再者，怎么样衡量去中心化还是个问题。过度追求高可用，带来的就是性能损失。过度追求性能，带来的又是高可用和稳定性的难题。未来的很大一段时间，还会存在中心化交易所和去中心化交易所并存的局势，并且会相互借鉴各自的实现。

8、最后，提供一条消息，连 Bitfinex 都开始构建基于 EOS 的去中心化交易所 **[EOSfinex](https://medium.com/bitfinex/announcing-eosfinex-69eea273369f)**。

**祝大家新年快乐，平安喜顺！**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
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
