---
published: true
author: Robin Wen
layout: post
title: "从 0 到 1 构建加密货币价格走势图"
category: 区块链
summary: "最近看到一篇文章 Build a Cryptocurrency Price Tracker in 5 Minutes，笔者对加密数字货币价格走势不是特别关心，但这篇文章非常有趣，本文想从技术的角度分析下实现思路。实现原理如下：采用发布订阅的功能，使用 PubNub 做消息发布；发布器根据设定的规则调用 CryptoCompare API 接口，这个可以理解成服务端；订阅器接收发布器采集到的数据，然后用图表框架 EON 展示。这只是一个最小化的例子，CryptoCompare API 还有很多功能，订阅器也可以做美观优化，感兴趣的读者不妨试试。笔者根据原作者项目修改的源码已经上传到 GitHub。加密数字货币价格走势曲线图是交易所的核心功能之一。当然，线上业务比这复杂得多，本文只是提供了一个雏形供读者参考。"
tags:
- 区块链
- Blockchain
- Crypto
---

`文/Robin`

> 本文由币乎(bihu.com)优质内容计划支持。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 25 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

最近看到一篇文章 [Build a Cryptocurrency Price Tracker in 5 Minutes](https://hackernoon.com/build-a-cryptocurrency-price-tracker-in-5-minutes-d66c3d37ad71)，笔者对加密数字货币价格走势不是特别关心，但这篇文章非常有趣，本文想从技术的角度分析下实现思路。

![2018-02-08-cryptocurrency-price-tracker](https://cdn.dbarobin.com/jqLBEMu.jpg)

> 题图来自: © Mr. Crowd / Tether Claims US$30 Million Worth of Token Stolen / mrcrowd.com

Joe Hanson 实现此功能使用了如下的 **技术栈**：

* [PubNub JavaScript SDK](https://www.pubnub.com/docs/web-javascript/pubnub-javascript-sdk)，消息接口
* [EON SDK](https://www.pubnub.com/developers/eon)，图表框架
* [CryptoCompare](https://www.cryptocompare.com/api)，加密数字货币价格 API 接口

**实现原理** 如下：

* 采用发布订阅的功能，使用 PubNub 做消息发布
* 发布器根据设定的规则调用 CryptoCompare API 接口，这个可以理解成服务端
* 订阅器接收发布器采集到的数据，然后用图表框架 EON 展示

具体来说，发布器的构建需要 [PubNub](https://admin.pubnub.com/#/register) 账号。发布器使用 JavaScript 实现，需要使用 PubNub 账号提供的 Publish Key 和 Subscribe Key 构建 PubNub 和 XMLHttpRequest 对象。

接下来创建 `processRequest` 函数，这个函数的功能是接收 CryptoCompare 接口的返回结果，然后发布不同币种的加密货币价格走势数据到不同的 Channel。接着需要创建 `main` 函数入口，这个函数的功能是定时调用 CryptoCompare 接口。比如如下接口的含义表示同时获取 BTC、ETH、EOS 的实时价格，并且以人民币 CNY 展示：

> https://min-api.cryptocompare.com/data/pricemulti?fsyms=BTC,ETH,EOS&tsyms=CNY

接下来我们再看下订阅器。发布器可以取到实时数据，我们需要订阅器来接收这些数据，并且用图表展示出来，此时就需要 EON 框架了。EOS 框架我们需要引入 eon.js 和 eon.css。然后我们创建 N 个 div 样式，接着我们仍然需要构建 PubNub 对象，不过此时只需要填写 Subscribe Key。接下来为了表格更加美观，我们需要设置一系列的参数。然后我们初始化 N 个 EON 图表，这些 EON 图表会接收来自发布器的不同 channel 的数据并展示给用户。

我们在浏览器同时打开发布器（cryptoPublisher.html）和订阅器（cryptoSubscriber.html），就可以在订阅器中看到实时展示的图表了。效果如图：

![2018-02-08-cryptocurrency-price-tracker](https://cdn.dbarobin.com/IJ2hJma.gif)

当然，生产环境我们还需要 [Access Manager](https://www.pubnub.com/docs/web-javascript/pam-security) 来做安全控制。

这只是一个最小化的例子，CryptoCompare API 还有很多功能，订阅器也可以做美观优化，感兴趣的读者不妨试试。笔者根据原作者项目修改的源码已经上传到 [GitHub](https://github.com/dbarobin/Cryptocurrency-Price-Tracker)。

94 之前，云币网是一个明星交易所，云币是基于开源的 [peatio](https://github.com/peatio/peatio-opensource) 构建的。peatio 是用 Ruby 实现的，对交易所感兴趣的读者不妨去读下 peatio 的源码。总之看了 peatio 提供的 feature，非常强大。

加密数字货币价格走势曲线图是交易所的核心功能之一。当然，线上业务比这复杂得多，本文只是提供了一个雏形供读者参考。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
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
