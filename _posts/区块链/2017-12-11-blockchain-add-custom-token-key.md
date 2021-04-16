---
published: true
author: Robin Wen
layout: post
title: "MyEtherWallet 手动添加币乎代币（KEY）"
category: 区块链
summary: "币乎（bihu.com）是代币驱动的代币投资者垂直社区。在币乎，用户的付出和贡献将获得 相应的回报。币乎将引入币乎 ID，以实现平台的透明化运作。KEY 是币乎的区块链代币，代 表币乎及其周边生态的使用权。币乎是为币圈量身定制的垂直社区平台。币乎通过代币激励的方式，使得用户的付出获得相应的回报。币乎是代币投资者的信息集散地，也是各个“币官方”与“币友”交流的平台。本文比较基础，主要是给读者一个手动添加 Token 的指引，希望对读者有所帮助。"
tags:
- 区块链
- Blockchain
- Ethereum
- 币乎
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 5 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

首先我们来看看币乎是什么。

> 币乎（bihu.com）是代币驱动的代币投资者垂直社区。在币乎，用户的付出和贡献将获得 相应的回报。币乎将引入币乎 ID，以实现平台的透明化运作。KEY 是币乎的区块链代币，代 表币乎及其周边生态的使用权。币乎是为币圈量身定制的垂直社区平台。币乎通过代币激励的方式，使得用户的付出获得相应的回报。币乎是代币投资者的信息集散地，也是各个“币官方”与“币友”交流的平台。

我们通过 [ethplorer.io](https://ethplorer.io) 查询自己的 ERC20 Token 资产，会发现币乎代币暂时是 N/A 状态。ethplorer.io 里无法更改 N/A 状态，但我们在 MyEtherWallet 钱包里可以手动添加币乎代币 KEY。接下来我们就来演示下操作流程。

打开 MyEtherWallet，输入私钥后 [查看自己的钱包](https://www.myetherwallet.com/#view-wallet-info)。

接着点击「Add Custom Token」。根据币乎 [Contract Address](https://bihu.com/contractAddress.html) 页面提示。

![Add Custom Token](https://cdn.dbarobin.com/x4bX0EG.png)

**Token Contract Address** 输入 `0x4cd988afbad37289baaf53c13e98e2bd46aaea8c`。

**Token Symbol** 输入 `KEY`。

**Decimals** 输入 `18`，点击 Save 保存。

![Bihu Contract Address](https://cdn.dbarobin.com/s77ukaM.png)

最后 **Load Tokens** 即可看到添加的币乎代币 KEY。

笔者讲解下 Token Contract Address、Token Symbol 和 Decimals 分别表示什么。

参考 [ERC20 Token Standard](https://theethereum.wiki/w/index.php/ERC20_Token_Standard)，Token Contract 有如下定义：

``` java
string public constant name = "Token Name";
string public constant symbol = "SYM";
// 18 is the most common number of decimal places
uint8 public constant decimals = 18;
```

Token Contract Address 表示合约地址，Token Symbol 表示 Token 的代号。比如以太坊的名字是 Ethereum，代号是 ETH。Decimals 表示 Token 精确到小数点后几位。18 是大多数 Token 使用的精度。通过 MEW 的 **[换算页面](https://www.myetherwallet.com/helpers.html)** 显示，1 ether = 10^18 wei，1 ether = 10^9 gwei，可以说 Decimals 等于 18 是精度最高的 Token 了。

我们还可以通过 [Ethereum Based Tokens](https://theethereum.wiki/w/index.php/Ethereum_Based_Tokens) 查看知名项目的 Token 信息。

本文比较基础，主要是给读者一个手动添加 Token 的指引，希望对读者有所帮助。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
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
