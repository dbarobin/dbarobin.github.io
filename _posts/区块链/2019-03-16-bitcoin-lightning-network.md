---
published: true
author: Robin Wen
layout: post
title: 闪电网络初体验
category: 区块链
summary: 我们都知道，比特币交易需要确认时间，根据消耗的矿工费不同，确认时间也不同。从 BitInfoCharts 的 Bitcoin 平均交易确认时间图像我们可以得出，最近 3 个月平均交易确认时间在 10 分钟以内，但把时间维度扩大，最长平均交易时间到了 360 分钟。对比国内流行的支付宝、微信支付等秒级支付方式，比特币不确定的交易时间很难达到大规模利用，这也是比特币广为诟病的地方之一。闪电网络离比特币的 A Peer-to-Peer Electronic Cash System 又近了一步。闪电网络，未来已来。最后，本博客开通了闪电网络打赏，读者可以扫描下方的闪电网络二维码（博客右侧导航栏也有）进行打赏。
tags:
  - 区块链
  - Blockchain
  - 闪电网络
---

`文/Robin`

这是「区块链技术指北」的第 **48** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

我们都知道，比特币交易需要确认时间，根据消耗的矿工费不同，确认时间也不同。从 BitInfoCharts 的 [Bitcoin 平均交易确认时间图像](https://bitinfocharts.com/zh/comparison/bitcoin-confirmationtime.html#3m) 我们可以得出，最近 3 个月平均交易确认时间在 10 分钟以内，但把时间维度扩大，最长平均交易时间到了 360 分钟。对比国内流行的支付宝、微信支付等秒级支付方式，比特币不确定的交易时间很难达到大规模利用，这也是比特币广为诟病的地方之一。

幸运的是，比特币生态有闪电网络。闪电网络，英文 Lighting Network，是工作在比特币上的 Layer 2 支付协议，目的是实现交易双方的及时交易。[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 解决了通道中币单向流动问题，HTLC 解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。

![](https://cdn.dbarobin.com/SNzpY1G.png)

> 题图来自: © Eric Grisholm / Bitcoin Lightning Network Nodes: Are They Profitable? / crushthestreet.com

关于闪电网络的更多技术细节，读者可以阅读白皮书，以及从「区块链技术指北」社区 [闪电网络专题](https://bbs.chainon.io/t/lightning) 深入了解。

这篇文章，笔者想给大家讲讲闪电网络初体验。

讲到「闪电网络」，笔者给大家举几个真实案例。第一个，在 2010 年完成了世界上第一个比特币交易的人，[Laszlo Hanyecz](https://twitter.com/HanyeczLaszlo)，[2018 年 2 月 25 日](https://www.investopedia.com/news/bitcoins-pizza-guy-repeats-trick-lightning-network)，他已经使用比特币的闪电网络购买了另外两个披萨了。[1] 第二个，2019 年 1 月 19 日，比特币爱好者 [@Hodlonaut](https://twitter.com/hodlonaut) 在推特上开启了一项叫做「闪电火炬」的活动，该活动也被称为「闪电网络信任链」，旨在向人们展示闪电网络的力量。活动规则很简单「我发给第一个我信任的人 10 万聪比特币，他增加 1 万聪比特币后将 11 万聪比特币发给下一个人，然后继续这样下去」。这项活动随即在比特币社区内迅速走红，参与活动的人不仅包括推特联合创始人 [Jack Dorsey](https://twitter.com/jack)、Blockstream 首席战略官缪永权、「Mastering Bitcoin」一书作者 [Andreas Antonopoulos](https://twitter.com/aantonop)、[币安赵长鹏](https://twitter.com/cz_binance)、[波场孙宇晨](https://twitter.com/justinsuntron)，还有机构和组织的身影，如富达数字资产和哈佛商学院区块链与加密货币俱乐部。[2] 第三个，[BlueWallet](https://bluewallet.io) 是一款支持闪电网络的钱包 APP，在这款钱包 APP 中已经可以找到约 25 种 [闪电网络应用](https://bluewallet.io/lapps) 了。[3] 其中 Binary 还可以为国内三大运营商的手机号进行充值。第四个，Chrome 插件 [Tippin.me](https://chrome.google.com/webstore/detail/tippinme/knhkeligkfmclgkeedceenpopaleokfh?hl=en) 支持向 Twitter 通过闪电网络打赏。

一切的案例表明，闪电网络已经来了，而且声势浩荡。

根据 [1ML](https://1ml.com) 最新的闪电网络数据，闪电网络的节点数为 7386 个，在过去 30 天内增长了 17.97%；通道数为 39235 条，在过去 30 天内增长了 47.47%；网络容量为 1055.82 个比特币，在过去 30 天内增长了 54.15%，闪电网络还在持续蓬勃发展。

> 注意，这个数据有时效性，目前还在不断地变化中，当你看到这篇文章的时候，数据已经变了。

![](https://cdn.dbarobin.com/apYTARb.png)

闪电网络在国内的情况不是特别乐观，一方面根据 1ML 数据，来自 China 的节点占比较少，另一方面，支持的钱包较少。但截止发稿，已经有 Hoo、bitpie 支持闪电网络。体验闪电网络的快捷转账，目前有 BlueWallet、Hoo、bitpie 可以选择。教程分别如下：

* BlueWallet，[iOS 闪电网络钱包 BlueWallet 体验](https://www.chainnode.com/post/277461)
* Hoo，[虎符 HD 钱包闪电网络教程 (Andriod)](https://s1.rylink.com/info_detail/237)
* bitpie，暂无，后续有的话补上

此外，Hoo 钱包在微博发起 [闪电网络中国力量](https://weibo.com/6556553702/Hl2n3eS2r)，目前笔者已支持，链接点击 [此处](https://weibo.com/6480983158/Hle3ClFJn)。

想象一个场景，读者长期持有比特币。某一天人在海外，漫步大街，又累又渴，想买杯咖啡，可是身上没有足够的外汇现金，商家也不支持国内的支付方式。此时掏出支持闪电网络的钱包，挑选咖啡，打开钱包扫一扫，支付成功，类似微信支付宝的丝滑体验。在你喝到咖啡的那一刻，你会感谢比特币。幸运的是，这一天的到来已不再遥远。当然，这只是万千应用场景的小小的一个。

闪电网络离比特币的 **A Peer-to-Peer Electronic Cash System** 又近了一步。

**闪电网络，未来已来。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

**参考资料**

> [1] 36Kr (2018-02-26). 第一个用比特币买披萨的小伙又用闪电网络重现了历史. Retrieved from [https://36kr.com/p/5120949.html](https://36kr.com/p/5120949.html).
> [2] 金色财经 (2019-02-28). 除了传火炬和买披萨 我们还确定了闪电网络 4 个中国节点的身份. Retrieved from [https://www.jinse.com/blockchain/322089.html](https://www.jinse.com/blockchain/322089.html).
> [3] 金色财经 (2019-02-28). 除了传火炬和买披萨 我们还确定了闪电网络 4 个中国节点的身份. Retrieved from [https://www.jinse.com/blockchain/322089.html](https://www.jinse.com/blockchain/322089.html).

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
