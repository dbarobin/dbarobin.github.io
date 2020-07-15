---
published: true
author: Robin Wen
layout: post
title: 博客现已支持闪电网络打赏
category: 区块链
summary: 笔者的博客很早的时候就支持 PayPal、BTC、支付宝、微信支付打赏。回到打赏这个事情，笔者认为是一种充分认可并且尊重他人劳动成功的行为。如果笔者在其他平台发表文章，可以使用 Markdown + Markdown Here 插件生成二维码。需要注意的是，闪电网络钱包二维码支付一次就失效了，所以不能使用固定的二维码。本博客开通了闪电网络打赏，读者可以扫描下方的闪电网络二维码（博客右侧导航栏也有）进行打赏。
tags:
  - 区块链
  - Blockchain
  - 闪电网络
---

`文/robin`

这是「区块链技术指北」的第 **50** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

笔者的博客很早的时候就支持 PayPal、BTC、支付宝、微信支付打赏。回到打赏这个事情，笔者认为是一种充分认可并且尊重他人劳动成功的行为。

在闪电网络诞生之前，狗狗币被广泛使用。狗狗币是什么呢？

> 多吉币（英文：Dogecoin，又称狗狗币、狗币，台湾也常叫旺旺币，币市代码为：DOGE，货币符号：Ð ）是一种继承自莱特币的虚拟货币（电子货币），诞生于 2013 年 12 月 6 日，由 IBM 工程师 Billy Markus 作一个玩笑而推出，所以币面符号中是一只日本 柴犬。起初只是 Billy Markus 用于证明，同时作为比特币的改进示范而创立的电子货币，但发布之后很快的在电子货币社区中流行。[1]

狗狗币总发行量 1000 亿，价格长期偏低，而且极其适用于小额支付，所以人们喜欢用狗狗币打赏。闪电网络有了之后，狗狗币可能会逐渐淡出视野。在 [BitInfoCharts](https://bitinfocharts.com) 网站以 Bitcoin、Ethereum、Dogecoin、
Monero 作为对比，Dogecoin 目前 24 小时内的交易数已经比较低了。

在之前的 [文章](https://dbarobin.com/2019/03/16/bitcoin-lightning-network) 中，笔者讲解了目前使用闪电网络有数个钱包供选择。其中 Hoo 钱包支持生成闪电网络收款码。

![](https://cdn.dbarobin.com/bW6w12P.png)

> 题图来自: © cripto2018 / Bitcoin: Lightning Network goes live / cryptoglobo.com

笔者的博客采用 GitHub Pages，支持 Markdown。添加闪电网络收款码的代码如下：

``` js
<center>
    <img title="Bitcoin Lightning Network Donate" width="180" height="180" src="https://lnd.hoo.com/api/generate?openid=TruSwjrK2q57V484Tf0u&isimg=1" alt="Bitcoin Lightning Network Donate"/>
</center>
```

openid 查找办法，打开 Hoo 钱包，切换到闪电网络钱包，点击顶部「三个点」，点击「通道账户」，输入 PIN 码，hoo://openid:xxxxxx，位于 // 和 : 之间的字符就是 openid。

如果笔者在其他平台发表文章，可以使用 Markdown + [Markdown Here](https://markdown-here.com) 插件生成二维码。需要注意的是，闪电网络钱包二维码支付一次就失效了，所以不能使用固定的二维码。

欢迎打赏，Enjoy！

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**参考资料**

> [1] 维基百科 (2018-07-21). 多吉币. Retrieved from [https://zh.wikipedia.org/wiki/%E5%A4%9A%E5%90%89%E5%B8%81](https://zh.wikipedia.org/wiki/%E5%A4%9A%E5%90%89%E5%B8%81).

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
