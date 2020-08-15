---
published: true
author: Robin Wen
layout: post
title: Mixin Webhook 第三篇，RSS 订阅博客
category: 区块链
summary: 上一篇文章讲解了 mixin-log-cli，一个监控日志的工具。今天为大家讲解如何通过 Mixin Webhook 订阅博客。使用 Mixin Webhook 不仅可以订阅博客，只要是一切支持 RSS 的内容，都可以订阅。不过这种方式缺点在于，订阅的步骤较多，而且分发到 Mixin Messenger，格式可能没那么友好。希望大家能把 RSS 用起来，优化自己的阅读体验。
tags:
  - 区块链
  - Blockchain
  - Mixin
  - Mixin Network
  - Webhook
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 **73** 篇文章。

## 一 前言
***

上一篇文章讲解了 [mixin-log-cli](https://dbarobin.com/2020/04/19/webhook-mixin-log-cli/)，一个监控日志的工具。今天为大家讲解如何通过 Mixin Webhook 订阅博客。

## 二 RSS
***

关于 RSS，可以阅读笔者的文章：[RSS Never Die](https://dbarobin.com/2019/03/12/rss-never-die/)。

RSS 因为开放的内容分发模式，不可避免地受到不可抗力的打压，从国区 App Store 不断下架 RSS 相关软件就略知一二。也是由于 RSS 的特性，导致通过 RSS 变现尤其困难。由于这些原因，RSS 依然是小众的阅读方式。然而，笔者至今认为 RSS 依然是最友好的阅读工具。

![](https://cdn.dbarobin.com/vbg163w.jpg)

> 题图来自: © Siam Blockchain / RSS Feed / siamblockchain.com

笔者使用 Feedly 服务管理 RSS 订阅，使用 Reeder 阅读器进行阅读。

笔者目前使用 Mixin Messenger 的频率相当高，那么问题来了，有没有可能将经常阅读的博客，在 Mixin Messenger 也订阅一份呢？答案是肯定的，接下来讲讲实操。

## 三 实操
***

在 [Mixin Webhook 第一篇，零代码实现接收 GitHub Release 推送](https://dbarobin.com/2020/03/29/webhook-monitor-github-release/) 一文中，其实已经有完整的教程了，细心的读者可能已经发现了，GitHub Release 的页面也可以转换成 RSS 订阅链接。

RSS 订阅博客，只需要把对应的 RSS 链接填入到 IFTTT 的 RSS Feed 里就行。

以笔者的博客为例，IFTTT 的 RSS Feed，填写 [https://dbarobin.com/feed.xml](https://dbarobin.com/feed.xml) 即可，其他博客同理。

## 四 小结
***

使用 Mixin Webhook 不仅可以订阅博客，只要是一切支持 RSS 的内容，都可以订阅。不过这种方式缺点在于，订阅的步骤较多，而且分发到 Mixin Messenger，格式可能没那么友好。

希望大家能把 RSS 用起来，优化自己的阅读体验。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

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
