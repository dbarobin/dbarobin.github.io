---
published: true
author: Robin Wen
layout: post
title: "Siacoin 钱包浅谈"
category: 区块链
summary: "Sia 最初的设计目的是：让云储存去中心化。在这个平台上，您可以存放和提取各种各样的文件，并不需要为您的文件隐私和安全担心。通过运用加密技术，加密合约，和重复备份，Sia 能够使一群互不信任的和互不了解的计算机节点联合起来成为一种有统一运行逻辑和程序的云储存平台。Sia 使用 Reed Solomon 编码技术（erasure coding），这意味着即使大量的主机离线也不会损坏文件。为避免从中心化的存储供应商那里租赁存储空间带来的诸多问题，Sia 提供 P2P 的空间租赁平台。Sia 只存储用户建立的合约。空间提供商若能提供合约证明则获得奖励，若丢失证明则受到惩罚。由于这些合约都被区块链所公开发表，因此存储合约的公信度被有效加强。"
tags:
- 区块链
- Blockchain
- Siacoin
- 钱包
- Wallet
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 12 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

### Siacoin 是什么？
***

**Siacoin 代号 SC。**

Sia 最初的设计目的是：让云储存去中心化。在这个平台上，您可以存放和提取各种各样的文件，并不需要为您的文件隐私和安全担心。通过运用加密技术，加密合约，和重复备份，Sia 能够使一群互不信任的和互不了解的计算机节点联合起来成为一种有统一运行逻辑和程序的云储存平台。

![Siacoin](https://cdn.dbarobin.com/KedYD7P.jpg)

> 题图来自: © Jonathan Tompkins / Crypto Review — Siacoin (SC) / hackernoon.com

这种平台将比传统的云储存平台更快，更便宜，和更可靠。因为这些互不信任的计算机节点分布于世界各地，Sia 可以在无需添加成本的情况下成为一个有效的文件及其内容的分销网络。文件上传者可以自由选择他们所使用的节点，这意味着他们可以避开一定区域内的节点，或只用那些他们认为可信的节点。去中心化是指把上传的一个文件分成许多小块并把每个小块存放在不同的计算机节点上。在这些存放文件的节点中，只需一小部分节点是可信任的，就可以使上传文件既安全又可靠。

每一个托管主机都受到加密文件合约的约束。当一个文件上载时，同时形成的合约将确保托管主机只有在完成了在预定的时间段里保管文件的条件后才能拿到支付款。托管主机也需要提交一定的押金: 如果一个托管主机没有完成合约，它不仅得不到支付款，而且还会失去押金。文件上传时，上传者清楚这个系统有很强的抗虚假托管主机，以及这些虚假托管主机会受到很大的金钱惩罚。

Sia 使用 Reed Solomon 编码技术（erasure coding），这意味着即使大量的主机离线也不会损坏文件。为避免从中心化的存储供应商那里租赁存储空间带来的诸多问题，Sia 提供 P2P 的空间租赁平台。Sia 只存储用户建立的合约。空间提供商若能提供合约证明则获得奖励，若丢失证明则受到惩罚。由于这些合约都被区块链所公开发表，因此存储合约的公信度被有效加强。

### Siacoin 的特点
***

**1.加密与奖励机制：**

在数据传到网络前进行加密，解密仅在下载后进行。对租用者来说，上传与下载都要付费。对托管主机，在合约达成时交付押金，在合约结束时收回押金和租用者提供的付费。在这个网络中的货币是 Siacoin 。如果托管主机在线率低于 95%，它们将受到罚款。

**2.去中心化与备份：**

将文件拆分，并传递给多个托管主机 (Reed-Solomn 算法)。系统标准是将文件分配给 30 个托管主机，只要 10 个主机就可以恢复文件。10 对 30 意味着文件做了三个备份，假设每个主机可靠性 90% ，文件可靠性达到 99.99% 。如果用户想拥有更强保护性，可选择 3 对 30 ，或者 2 对 100 .

**3.Sia 如何应对 Sybil 攻击：**

Sybil 攻击是指一个人伪装成多个人。在网络中，一个人很容易伪装成 10 个甚至 100 个人。在 Sia 系统里这意味着一个攻击者可能注册了一万个貌似诚实的个机器，然后再进行欺骗的行为。 Sia 应对 Sybil 攻击的方式是销毁证明 (proof-of-burn)。托管主机把收入的 4% 发给一个公认的不可再消费的地址来证明其是真正的托管主机。租用者选择有过销毁证明的托管主机，销毁证明越多的主机也就享有更多租用者。这种机制意味着攻击者必须销毁足够多的币使其看起来只值得信任。对于一个有 3 倍备份的文件，攻击者至少需要 2.1 倍的备份，这意味着攻击者必须要销毁足够多得到币以到达看起来像 67% 的网络。那样需要销毁币是其他主机加在一起的 1.5 倍。特别当系统成长起来和成熟以后，收集这么多币几乎是不可能的。攻击者不仅要收集这些币，而且需要销毁他们，这就意味着他们没法收回那些投资。虽然说不是完全地不可能，但对 Sia 实施 Sybil 攻击将比对比特币实施 51% 攻击代价更高。

### Siacoin 的发行方式
***

第一个区块奖励 300,000 个 Siacoin ，每挖出一个块减少一个币，当到达 270,000 个区块以后，所有的区块只产生 30,000 的云储币（Siacoin）。总币量约：44,550,000,000。每 10 分钟一个块，总共需要挖掘 5.136 年。目前的云储币（Siacoin）数量可以在 [https://explore.sia.tech](https://explore.sia.tech/) 上查看。

### Siacoin 与相关云存储币种的比较
***

有关云存储概念的数字货币有：Storj 和 MaidSAFE 。

Storj 的目标与 Sia 相似，但 Storj 没有区块链内置的智能合约。Storj 用一种边走边付的方式，在这种方式里租用者频繁地给托管主机付款。如果用户不见了或不在线，托管主机将得不到报酬。Storj 依然在测试组内部测试，还没有实施一个你可以上载文件的网络。

MaidSafe 是一个非常有野心的项目，它的目标超出了去中心化的储存系统。他们对效率方面并不太专注。由于 Sia 使用了比较智慧的备份管理， Sia 网络应该既能便宜地上传文件又能让托管主机得到更多的报酬。同样 MaidSafe 至今还没有一个可以上传或下载文件的网络。 MaidSafe 使用一种全新的共识机制来产生共识（不同于区块链），它不是工作证明（PoW），也还没有像比特币的机制那样被审核过。

### Siacoin 钱包
***

可以前往 [官网网站](https://sia.tech/apps/) 下载 Sia-UI 钱包，目前提供如下版本：Linux、macOS 和 Windows。Sia-UI 是一个完全节点同步钱包，需要占用数 G 的硬盘空间。目前来看除了部分交易所支持 SC，还没有体验较好的轻钱包。SC 钱包中每次获取的地址都不一样，但每个地址都是有效的，均指向当前的钱包。在转账前，获取了一个钱包地址，在转账完成后，再一次点击获取地址按钮时，却发现地址不一样了。读者可能会担心会不会转错，这个是为了安全和隐私考虑，钱包的每个公开地址都会从读者的钱包的 xPub（或扩展公钥）中产生，一旦读者的公开地址收到付款，在点击接收的时候，新的地址就会自动生成并显示，所以读者不用担心资产安全问题，只需要确认转账地址完全正确。

### 小结
***

本文不构成投资建议。笔者以后会针对某个币做系列文章，关注区块链背后的技术。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
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
