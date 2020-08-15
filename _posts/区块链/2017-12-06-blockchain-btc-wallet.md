---
published: true
author: Robin Wen
layout: post
title: "比特币钱包浅谈"
category: 区块链
summary: "BTC 是迄今为止最为成功的区块链项目，也是市值排名第一的虚拟货币。比特币诞生源于神秘人物中本聪 2008 年的白皮书《比特币：一种点对点的电子货币系统。比特币总数固定在 2100 万个，没有发行方，去中心化，这些特性让比特币成为一种革命性货币。基于区块链技术，比特币的每个交易都经过全网广播，人人可见，可以查到任何交易历史，无法伪造、篡改交易记录，数字加密货币解决了网络去中心化、无主权货币、避免双重支付、可靠高效、廉价匿名等一系列过去觉得不可能解决的问题。本文简单阐述了什么是比特币，讲解了比特币钱包的组成以及不同分类维度，还给出了笔者的选择钱包标准。后续的文章还会讲解其他 Token 的钱包，还会有详细的安全攻略。"
tags:
- 区块链
- Blockchain
- 比特币
- Bitcoin
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

这是「区块链技术指北」的第 2 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

**私钥即财富，这是区块链世界的金科定律。**

首先我们来看币中之王 BTC。BTC 是迄今为止最为成功的区块链项目，也是市值排名第一的虚拟货币。比特币诞生源于神秘人物中本聪 2008 年的白皮书《比特币：一种点对点的电子货币系统。比特币总数固定在 2100 万个，没有发行方，去中心化，这些特性让比特币成为一种革命性货币。基于区块链技术，比特币的每个交易都经过全网广播，人人可见，可以查到任何交易历史，无法伪造、篡改交易记录，数字加密货币解决了网络去中心化、无主权货币、避免双重支付、可靠高效、廉价匿名等一系列过去觉得不可能解决的问题。

![Bitcoin](https://cdn.dbarobin.com/82BCW5k.jpg)

> 题图来自: © WAN ASRAFF / Soal Jawab Tentang Mining Bitcoin / goo.gl/xVbMxR

拥有和保存比特币，需要通过客户端，通常把该软件称为钱包。钱包通常包含如下信息：

* 公钥、私钥、地址
* 与钱包中地址相关的交易信息
* 其他辅助数据

私钥可以生成公钥，公钥可以生成比特币地址，相反则不行。因为比特币是由一个不可逆的算法完成这个流程的。比特币是使用椭圆曲线乘法作为其公钥的基本算法。如果某人公开了他的比特币地址，我们可以通过 [BlockExplorer](https://blockexplorer.com/) 或者 [Blockchain.info](https://blockchain.info) 网站输入比特币地址查询到他的资产和交易记录。然而我们只能瞻仰他的资产，却没法通过比特币地址反推他的私钥。私钥就像保险柜的钥匙，我们拥有私钥也就拥有资产，如果私钥丢失或者被盗，这部分资产也就永远地找不回了。

钱包创建后，我们想恢复怎么办？此时就需要**助记词**。助记词创建过程如下：

1. 创造一个 128 到 256 位的随机顺序（熵）。
2. 提出 SHA256 哈希前几位，就可以创造一个随机序列的校验和。
3. 把校验和加在随机顺序的后面。
4. 把顺序分解成 11 位的不同集合，并用这些集合去和一个预先已经定义的 2048 个单词字典做对应。
5. 生成一个 12 至 24 个词的助记码。

128 位熵产生 12 位单词的助记词，助记词产生 512 位的种子。什么是种子呢？种子就是能生成主密钥的数据，钱包所有的数据都衍生自这个种子。通常某些钱包还会包含一个密码，这个密码是进入钱包的凭证。

比特币钱包实际上是不包含比特币的，比特币钱包是由私钥和公钥所组成的数据库。比特币本身是存储在区块链中的。用户用私钥来签名交易，从而证明他们有这笔交易。当你用私钥签名一笔交易之后，那些交易里面提到的比特币就会有记录，这些记录所有人都可以查询。矿工们则负责验证这笔交易，同时也会收取一些费用，这个过程叫做 Proof of Work（POW，工作量证明）。区块链是一个庞大的数据库账本，这笔交易就会保留在区块链数据库里，没人能篡改。

市面上有成千上万种 Token，截至目前，其中有 18607 种 [ERC20 Token](https://etherscan.io/tokens)，当然 ERC20 Token 数量还在不断地增加。我们面对种类众多的 Token，有可能会有疑惑，某种 Token 应该放在哪一种钱包比较合适呢。需要注意的是，假如我们往不支持该 Token 的钱包里转 Token，很有可能 Token 再也找不回，所以选择正确的钱包也是非常关键的。

**对于一个钱包而言，最核心的数据就是私钥。**钱包不一定需要包含完整的区块链数据，不包含区块链数据的钱包我们称之为轻钱包（Light Weight Wallet）。对于个人而言，轻钱包完全可以满足需求。

根据同步数据类型，钱包分类如下：

* 完全节点型 (Full Node)：含有 BlockChain 所有完整数据
* 简易节点型 (SPV Node)：Header-Only Clients，仅有 Block 头部信息，无需交易数据

根据访问钱包数据类型，钱包分类如下：

* CS 型 (Server-Client)：服务端-客户端模式，大部分数据存储在服务端
* BS 型：所有数据均通过浏览器在线使用

根据如下定义：**可以安全存放比特币私钥，可以用私钥签名以发送比特币，并能够查询区块链显示比特币余额，如果符合以上条件就是链上钱包，而链下钱包不具备如上功能。**链上钱包包含离线钱包（冷钱包）、本地钱包、在线钱包、多重签名钱包。离线钱包私钥存储在离线设备，比如电脑、手机、专业硬件设备等等，产品如 Armory 离线端、Electrum 离线端、比太冷钱包、Trezor 硬件钱包、HardBit 硬件钱包、Ledger Nano S 硬件钱包、KeepKey 硬件钱包等。本地钱包私钥存储在用户本地设备，如电脑、手机等，产品如 Bitcoin-QT、Multibit、Armory 在线端、Electrum 在线端、比太热钱包等。在线钱包私钥存储在钱包服务器，产品如 Blockchain、OpenBlock 等。多重签名钱包对于私钥是这样处理的，服务器和本地各一份私钥，产品如 BitGo、GreenAddress、快钱包、Armory。对普通用户而言，妥善地存储私钥是一件比较头疼的事情，这样链下钱包也就应运而生。链下钱包最显著的特点就是用户不掌握私钥，统一有服务端存储。这类产品如 Coinbase、币付宝、币加锁等。

另外一个维度的分类标准是终端类型，我们可以将钱包分为 PC/Mac 钱包、手机钱包、Web 钱包、硬件钱包等。官方钱包叫做 Bitcoin Qt，这是一款完整的钱包软件，使用此钱包需要下载数 GB 的 BlockChain 数据，对于大部分用户而言，没有必要。目前 Mac 或者 PC 体验比较好的轻钱包有 [Electrum](https://electrum.org) 和 [Bither](https://bither.net)。iOS、Android 目前体验比较好的钱包有 [bread](http://breadapp.com)、[比特派](http://bitpie.com)。另外，很多线上的交易所还提供了在线钱包。如果比特币数量比较大，对安全性要求高，还可以使用硬件钱包。

除了以上介绍的钱包外，[Bitcoin 官网](https://bitcoin.org/zh_CN/wallets/desktop/mac/)还有钱包的选择列表，读者不妨去看看。

至于要选择什么样的钱包，笔者这里有个建议，尽量选择自己可以掌握私钥的钱包，比如笔者使用的是 Electrum。笔者选择钱包的标准如下：**可以掌握私钥、开源、国外产品**，给读者一个参考。值得一提的是，知名 ERC20 Token 钱包 imToken，在 2.0 版本会加入比特币支持。读者需要注意的是，并不是某个钱包生成的 seed 并不是在所有的钱包都可以恢复的，选择钱包之前需要关注下钱包之间的兼容性。具体的表格可以参考：[https://git.io/vbn16](https://git.io/vbn16)。

本文简单阐述了什么是比特币，讲解了比特币钱包的组成以及不同分类维度，还给出了笔者的选择钱包标准。后续的文章还会讲解其他 Token 的钱包，还会有详细的安全攻略。

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
