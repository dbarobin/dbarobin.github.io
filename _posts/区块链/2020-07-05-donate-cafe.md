---
published: true
author: Robin Wen
layout: post
title: Donate Cafe，让加密货币打赏更优雅
category: 区块链
summary: Bitcoin 的愿景是做「Peer-to-Peer Electronic Cash System」，然而事实上这个目标已经被 USDT 实现了。闪电网络作为原教旨主义对比特币支付的寄托，做了这么多年却不尽如意。根据 1ml.com 数据，目前有 13014 个节点，37569 个通道，网络容量却只有 990.03 BTC。Donate Cafe 只需要简单的几步就可以完成打赏设置，使用 Mixin Messenger 打赏还可以快速免费到账，同时还支持 Mixin Messenger 外的钱包，让打赏这个事变得简单优雅。如果读者有独立博客或者在其他平台写作，可以考虑使用 Donate Cafe 作为打赏工具。
tags:
  - 区块链
  - Blockchain
  - 比特币
  - Bitcoin
  - Mixin
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 **77** 篇文章。

## 前言
***

Bitcoin 的愿景是做「Peer-to-Peer Electronic Cash System」，然而事实上这个目标已经被 USDT 实现了。闪电网络作为原教旨主义对比特币支付的寄托，做了这么多年却不尽如意。根据 1ml.com 数据，目前有 13014 个节点，37569 个通道，网络容量却只有 990.03 BTC。

## 捐赠和打赏
***

美国的小费文化表示的是「感谢」，是一种对方给自己服务的回馈。中国的社交产品基本上都有「打赏」功能，本质上是一种「喜爱」，更像中国的红包文化。像 Twitter、Facebook 并没有打赏这个功能，发表观点并不构成任何服务。互联网有一部分网站是支持加密货币捐赠的，比如知名的 Wikipedia。Donate 是个神奇的英语单词，本意捐赠，适应到中国文化又可以翻译成打赏。

![](https://cdn.dbarobin.com/a69e2b5.png)

> 题图来自: © Jonas Chokun / How to Accept Bitcoin Donations on Your Blog or Site / 99bitcoins.com

## 工具
***

Dogecoin（狗狗币）总发行量 1000 亿，价格长期偏低，而且极其适用于小额支付，所以人们喜欢用狗狗币打赏。

支持加密货币 Donate 的网站，基本上都是附带 BTC 或者其他加密货币地址，少数会集成 [BitPay](https://bitpay.com/docs/donations) 的 Donate 服务。跟 BitPay 类似的服务还有 [engiven](https://engiven.com)。

基于比特币闪电网络的打赏工具有 [虎符闪电网络 API](https://dbarobin.com/2019/03/19/lightning-network-donate)、[Tippin.me](https://tippin.me)。前者集成了闪电网络原始协议，每访问一次会生成一个待支付二维码，如果超过了有效时间还未支付，则会变成已失效，所以读者在虎符 HD 钱包内会看到很多已失效的记录。后者支持 Twitter 登录，需要使用支持闪电网络的钱包进行支付。

此外还有处理累计超过 21695339 笔交易的加密货币打赏工具 [CCTip](https://cctip.io/index)。CCTip 支持 Twitter、Telegram、Reddit 和 Discord 登录，可以在 Twitter、Telegram、微信、Reddit 和 Discord 上，将加密货币轻松发给他人。

## Donate Cafe
***

介绍完如此多的打赏工具，笔者想说的是以上工具都太复杂了，而且不那么去中心化。

接下来笔者为大家介绍一款体验超级好的加密货币打赏工具，那就是 **[Donate Cafe](https://donate.cafe)**，域名也就是 donate.cafe，可以翻译成请我喝咖啡。

Donate Cafe 只需要简单的几步，就可以生成接受打赏的链接，类似 PayPal.me，比如笔者的是 [https://donate.cafe/robin](https://donate.cafe/robin)。同时，读者还可以在需要接收打赏的地方放一个打赏按钮，甚至还可以放一段 JS 代码，无需跳转新页面就可以完成打赏。Donate Cafe 打赏用的是 P2P 模式，如果使用 Mixin Messenger 进行打赏，实际上是打赏方发起一笔对应数量对应币种的转账给被打赏方。Donate Cafe 还支持统计功能，具体要在 Mixin Messenger 内搜索 **7000103066** 查看。

![](https://cdn.dbarobin.com/wvfab6x.png)

> 题图来自: © Robin / Robin in Donate Cafe / donate.cafe

Donate Cafe 支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。[1]

Donafe Cafe 支持以下加密货币：Bitcoin（BTC）、Ether（ETH）、Monero（XMR）、Horizen（ZEN）、Dogocoin（DOGE）、Litecoin（LTC）、Bitcoin Cash（BCH）、Bitcoin SV（BSV）、Dash（DASH）、Binance Chain（BNB）、Stellar（XLM）、Ripple（XRP）、Zcash（ZEC）。

Donate Cafe 无需 KYC，不托管资产，而且代码 [开源](https://github.com/MixinNetwork/donate.cafe)。

## 小结
***

Donate Cafe 只需要简单的几步就可以完成打赏设置，使用 Mixin Messenger 打赏还可以快速免费到账，同时还支持 Mixin Messenger 外的钱包，**让打赏这个事变得简单优雅**。

如果读者有独立博客或者在其他平台写作，可以考虑使用 Donate Cafe 作为打赏工具。

## 参考
***

> [1] 长不老 (2020-06-25). DonateCafe 是接受 BTC 打赏的最佳方式. Retrieved from [https://w3c.group/c/1593069405645194](https://w3c.group/c/1593069405645194).

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/w0wignb.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

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