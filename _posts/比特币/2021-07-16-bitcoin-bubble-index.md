---
published: true
author: Robin Wen
layout: post
title: 比特币泡沫指数
category: 比特币
summary: "今天跟大家介绍一个指数，叫做 Bitcoin Bubble Index，中文名为比特币泡沫指数，简称 BBI。这个指数是由 @马超Terminal 提出，并且在 GitHub 开源，地址为 bitcoin-bubble-index。至于笔者的数据滞后一周，如果拉长时间维度来看这个事情，其实关系也不太大。了解一段时间的 BBI，可以很精准地掌握 Bitcoin 的热度，以及对投资决策做出一些指引。"
tags:
  - 比特币
  - Bitcoin
  - BBI
---

`文/Robin`

***

![](https://cdn.dbarobin.com/d1tvx8k.png)

今天跟大家介绍一个指数，叫做 Bitcoin Bubble Index，中文名为比特币泡沫指数，简称 BBI。

这个指数是由 @马超Terminal 提出，并且在 GitHub 开源，地址为 [bitcoin-bubble-index](https://github.com/aksnzhy/bitcoin-bubble-index)。

BBI 跟踪了如下的指标：

* 每天 BTC 的价格；
* 每天 BTC 的活跃地址数；
* 每天 BTC 的交易数；
* 每天挖矿的平均难度；
* 每天 Google Trends Bitcoin 相关的记录数；
* 每天带有 `#Bitcoin` 标签的 Tweets 数。

根据这个指标进行一定的处理后，就得出一个叫做 BBI 的指数。这个指数的区间不定，有可能是负的，也可能是正的，越低代表低估，越高代表泡沫，在这中间的表示估值合理。

构成 BBI 的指标，都是从 BitinfoCharts 抓取的。但是开源的代码并没有抓取这些数据的逻辑，笔者在之前自己去实现了一套，并且定期更新，网址为 [bubble.dbarobin.com](https://bubble.dbarobin.com/)。

根据 Google 的搜索结果，笔者维护的数据排在第二，第一个是 QKL123 的。QKL123 的数据还说什么这个指数是 ChaiNext 原创的，丝毫不尊重原作者。而且 QKL123 的数据居然可以查到昨天的数据，这一点很匪夷所思。

笔者维护的更新数据通常会延时一周，比如今天是 7 月 16 号，只能查看 7 月 9 号的数据。原因是 BitinfoCharts 上的 Google Trends 数据最新就是 7 月 9 号的。笔者也去跟 BitinfoCharts 的维护者反馈过，没有得到回复。

笔者还发现一个很有意思的点，笔者维护的数据最新到 7 月 9 号，如果在 QKL123 查询同样 7 月 9 号的数据，笔者维护的 BBI 是 52，QKL123 维护的是 113.75，这可相差很远啊，所以笔者对 QKL123 维护数据的正确性保持怀疑。

至于笔者的数据滞后一周，如果拉长时间维度来看这个事情，其实关系也不太大。了解一段时间的 BBI，可以很精准地掌握 Bitcoin 的热度，以及对投资决策做出一些指引。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)。如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

* 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
* 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

***

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)