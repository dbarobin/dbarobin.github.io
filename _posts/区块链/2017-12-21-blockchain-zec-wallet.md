---
published: true
author: Robin Wen
layout: post
title: "Zcash 钱包浅谈"
category: 区块链
summary: "Zcash 代号 ZEC。Zcash 是建立在区块链上的隐私保密技术。和比特币不同， Zcash 交易自动隐藏区块链上所有交易的发送者、接受者和数额。只用那些拥有查看秘钥的人才能看到交易的内容。用户拥有完全的控制权，用户可以自己选择性地向其他人提供查看秘钥。Zcash 钱包资金分 2 种：透明资金、私有资金，透明资金类似比特币资金；私有资金加强了隐私性，涉及到私有资金的交易是保密不可查的，透明资金与透明资金的交易是公开可查的。本文不构成投资建议。笔者以后会针对某个币做系列文章，关注区块链背后的技术。"
tags:
- 区块链
- Blockchain
- 钱包
- Wallet
- Zcash
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 9 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

### Zcash 是什么？
***

Zcash 代号 ZEC。Zcash 是建立在区块链上的隐私保密技术。和比特币不同， Zcash 交易自动隐藏区块链上所有交易的发送者、接受者和数额。只用那些拥有查看秘钥的人才能看到交易的内容。用户拥有完全的控制权，用户可以自己选择性地向其他人提供查看秘钥。

Zcash 钱包资金分 2 种：透明资金、私有资金，透明资金类似比特币资金；私有资金加强了隐私性，涉及到私有资金的交易是保密不可查的，透明资金与透明资金的交易是公开可查的。

![Zcash Investment Trust has officially Filed with the SEC](https://cdn.dbarobin.com/winFlax.jpg)

> 题图来自: © Chuck / Zcash Investment Trust has officially Filed with the SEC / medium.com

### Zcash 背后的技术是什么？
***

Zcash 使用一个被称为 **zk-SNARK 的零知识证明架构**，该架构是由经验丰富的密码学家团队 开发的。这个框架允许网络在不公开交易参与方或者交易数额的情况下维护一个安全的账户余额账本。Zcash 交易的元数据是加密的，而不是公开地展示交易参与方和交易数额，zk-SNARK 被用来证明没有人进行欺骗或者偷窃。

Zcash 用户也可以发送与比特币类似的透明交易。 通过对屏蔽和透明地址的支持，用户可以选择私人或公开发送 Zcash。通过同时支持隐私和透明地址，用户可以选择使用隐私或公共方式发送 Zcash。 从隐私地址发到透明地址的 Zcash 交易会显示收到的余额，而从透明地址发到隐私地址的支付隐藏接收到的价值。

相信读者也明白了，Zcash 更加适合于匿名支付场景。

### Zcash ZEC 币种信息
***

Zcash 区块链将于 2016 年 10 月 28 出现创世块。

缓慢挖矿规则：最初的 20,000 个块的奖励很少。从第 1 个块开始到第 20,000 个块的奖励线性增加，在第 20,000 个块处奖励 12.5 个 ZEC。这样做的目的是为防止早期的快速挖矿对 Zcash 系统造成伤害。 Zcash 系统每 2.5 分钟一个块， 20,000 个块之后，每个块奖励 12.5 个 ZEC。

Zcash 总量 2100 万枚，每 4 年减半一次。

在第一次减半前的四年时间里，有 20％ 的挖矿收益会分配给 Zcash 团队和其投资人，总数是 210 万枚。第一次减半后，100% 的挖矿收益归矿工所有。

### 什么是 zk-SNARK 零知识证明架构
***

Zcash 是 zk-SNARKs 的第一个广泛应用，这是零知识密码学的一种新颖形式。Zcash 的强大的隐私保证来自于 Zcash 的屏蔽交易可以在块上完全加密，但仍然通过使用 zk-SNARK 证明在网络协商一致的规则下被验证为有效的事实。

首字母缩略词 zk-SNARK 代表「零知识无知互不信任知识」，并且是指证明结构，其中可以证明拥有某些信息，例如秘密密钥，而不透露该信息，并且没有任何交互证明者和验证者。

关于 zk-SNARK 的详细说明，可以参考 [官方文档](https://z.cash/zh/technology/zksnarks.html)。

### Zcash 官网是什么
***

[https://z.cash](https://z.cash)

### Zcash 钱包
***

* 在线钱包
* 基于透明地址的钱包：[Jaxx](http://jaxx.io)、[Cryptonator](https://www.cryptonator.com)、[Bitpie](http://bitpie.com/#services)、[Coinomi](https://coinomi.com)、[HolyTransaction](https://holytransaction.com)、[Freewallet](https://freewallet.org)、[coinBR](https://www.coinbr.net)
* 硬件钱包：Ledger Nano S、Trezor 等
* 本地钱包：Zcash 客户端，需要同步所有网络节点
* 使用 Zcash 私有地址
    - [Linux](https://github.com/zcash/zcash/wiki/1.0-User-Guide)，官方钱包
    - [Windows](https://zcash4win.com)，David Mercier 维护
    - [Mac](https://zcash4mac.com)，David Mercier 维护
* 交易所钱包，支持 ZEC 交易的交易所都提供此功能，需要注意安全问题，具体可以参考 [区块链资产安全攻略](https://dbarobin.com/2017/12/18/blockchain-security-notice)，需要注意的是，目前还没有交易所支持 Zcash 私有地址。

### 为什么要关注 Zcash
***

Zcash 采用的 zk-SNARKs 零知识证明解决了比特币半匿名性的问题，使得即使像 CIA 之类的机构也难以追踪金融交易的私密性。Zcash 团队豪华，各大顶级理工学院的密码学大师，做过 Java 虚拟机安全漏洞的 Daira 和 I2P 的 Jack 等等。Zcash 源于比特币，总发行数量固定。Equihash 算法实现公平挖矿。从 CoinMarketCap 得知，目前 Zcash 市值排名 20，单价 $729.54。

### 写在最后
***

本文不构成投资建议。笔者以后会针对某个币做系列文章，关注区块链背后的技术。

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
