---
published: true
author: Robin Wen
layout: post
title: "ERC20 协议 Token 钱包浅谈"
category: 区块链
summary: "以太坊，Ethereum 是一个分布式的计算机，有许多的节点，其中的每一个节点，都会执行字节码（其实就是智能合约），然后把结果存在区块链上。由于整个网络是分布式的，且应用就是一个个的状态组成，存储了状态就有了服务；所以它就能永不停机，没有一个中心化的结点（没有任何一个节点说了算，去中心化的），任何第三方不能干预。本文讲述了什么是以太坊，以及相关的一些概念，并给出相应的钱包选择和优劣对比。希望对读者有所帮助。"
tags:
- 区块链
- Blockchain
- Ethereum
- 钱包
- Wallet
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 3 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

以太坊，[Ethereum](https://www.ethereum.org) 是一个分布式的计算机，有许多的节点，其中的每一个节点，都会执行字节码（其实就是智能合约），然后把结果存在区块链上。由于整个网络是分布式的，且应用就是一个个的状态组成，存储了状态就有了服务；所以它就能永不停机，没有一个中心化的结点（没有任何一个节点说了算，去中心化的），任何第三方不能干预。

![Ethereum](https://cdn.dbarobin.com/vW0Z1oB.jpg)

> 题图来自: © José Domingues / Ethereum / goo.gl/uHDq2M

我们一提及以太坊，就会提到 **智能合约**。那什么是智能合约呢？智能合约与平时的代码其实没有什么区别，只是运行于一个以太坊这样的分布式平台上而已。这个运行的平台，赋予了这些代码不可变，确定性，分布式和可自校验状态等特点。代码运行过程中状态的存储，是不可变的。每一个人，都可以开一个自己的节点，重放整个区块链，将会获得同样的结果。在以太坊中，每个合约都有一个唯一的地址来标识它自己（由创建者的哈希地址和曾经发送过的交易的数量推算出来）。客户端可以与这个地址进行交互，可以发送 ether，调用函数，查询当前的状态等。智能合约，本质上来说就是代码，以及代码运行后存储到区块链上的状态两个元素组成。比如，你用来收发 ETH 的钱包，本质上就是一个智能合约，只是外面套了一个界面。

智能合约，就是一些代码，运行整个分布式网络中。网络中的每一个节点都是一个全节点。这样的好处是容错性强，坏处是效率低，消耗资源与时间。因为执行计算要花钱，而要执行的运算量与代码直接相关。所以，每个在网络运行的底层操作都需要一定量的 gas。gas 只是一个名字，它代表的是执行所需要花费的成本。gas 的价格由市场决定，类似于比特币的交易费机制。**我们使用 ERC20 Token 钱包交易时，会有 gas、gas price、gas limit 和 data。我们试图来搞清楚这些是什么。**在以太坊上，发送代币或调用智能合约，在区块链上执行写入操作，需要支付矿工计算费用，计费是按照 gas 计算的，gas 使用 ETH 来支付。无论您的调用的方法是成功还是失败，都需要支付计算费用。即使失败，矿工也验证并执行你的交易（计算），因此必须和成功交易一样支付矿工费。 一笔转账需要花费矿工费 = gas limit * gas price. 一笔标准的转账需要花费 21000 gas 和 0.00000002 ETH gas price , 所以总的矿工费是 0.00042 Ether. 通常情况下，如果有人只说 gas ，指的就是 gas limit，gas limit 相当于汽车需要加多少汽油， gas price 相当于每升汽油的价格。gas limit 之所以称为限额，因为它是你愿意在一笔交易中花费 gas 的最大数量。交易所需的 gas 是通过调用智能合约执行多少代码来定义。 如果你不想花太多的 gas，通过降低 gas limit 将不会有太大的帮助。因为你必须包括足够的 gas 来支付的计算资源，否则由于 gas 不够报错 Out of gas。所有未使用的 gas 将在转账结束时退还给您。通过降低 gas price 可以节省矿工费用，但是也会减慢矿工打包的速度。矿工会优先打包 gas price 设置高的交易，如果你想加快转账，你可以把 gas price 设置得更高，这样你就可以排队靠前。如果你不急，你只需要设置一个安全的 gas price，矿工也会打包你的交易查看矿工可以接受的最低 gas price : [http://ethgasstation.info](http://ethgasstation.info)。Data 是可选项，用来调用合约。你需要把对应字符串转换成 16 进制，工具：[http://string-functions.com/string-hex.aspx](http://string-functions.com/string-hex.aspx)。转换之后，我们将在字符串前面加上 0x，然后填入即可。如果想熟练这部分操作，可以去 **MyEtherWallet** 网站进行一次操作。

现在你应该知道我们可以通过写智能合约，并将状态存到区块链上了？那如果，在状态这块，我们存的是一个 Map 类型，键是地址，值是整数。然后我们将这些整数值叫做余额，谁的余额呢？它就是我们要说的代币。每个人都开始定义自己与代币的交互协议，但这些很快显得陈旧，所以一些人开始集结起来，创建了 [ERC20 代币接口标准](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md)。大概意思是说，我们定义这些接口，这样大家可以相互统一调用，比如转帐定义为 transfer，第一个参数为要转去的帐户地址 `address _to`，第二个参数为要发送的 ether 的 `uint256 _value` 数量。有些人觉得 ERC20 协议过于复杂了，所以他们提议了 [ERC197](https://github.com/ethereum/EIPs/issues/179)，稍微简单一点。由于在 ERC20 中存在的一个小问题，有人提议了一个新的 [ERC23](https://github.com/ethereum/EIPs/issues/223)。ERC23 是向后兼容 ERC20 的。

代币也叫做 Token。市面上有成千上万种 Token，截至目前，其中有 18809 种 [ERC20 Token](https://etherscan.io/tokens)，当然 ERC20 Token 数量还在不断地增加，可见区块链技术是有多火。

接着我们来看兼容 ERC20 Token 协议的虚拟货币使用什么样的钱包。所有兼容 ERC20 Token 协议的数字货币都可以放在同一个钱包，同一个地址可以显示不同的 Token。**[「比特币钱包浅谈」](https://dbarobin.com/2017/12/06/blockchain-btc-wallet)**文章已讲解了钱包的分类，本文不再赘述。ERC20 有在线钱包 MyEtherWallet，以太坊官方图形界面钱包 Mist，Parity 钱包，Exodus 钱包，imToken 钱包，Jaxx 钱包，MetaMask 浏览器插件钱包，硬件钱包 Ledger Nano S、Trezor、KeepKey 等。目前体验比较好的 PC/Mac 钱包有 Mist 官方钱包，不过这个需要同步区块链所有节点数据，如果想用轻钱包，可以考虑 Jaxx。iOS/Android 体较好的钱包有 imToken 和 Jaxx。体验较好的 Web 钱包有 MyEtherWallet，还有基于浏览器（如 Chrome、Firefox）插件的 MetaMask。另外，交易所也支持存储 ERC20 Token，如果数量比较多，还是建议放在钱包。另外，笔者使用的是 MyEtherWallet，读者可以尝试下。

Mist 是一个全节点钱包，需要同步全部的以太坊区块信息。优势在于安全度高，不需要经过第三方发起交易。劣势一是无法调整 gas price；二是对网络要求高，需要连接节点，才能发起交易；三是点未同步完成之前无法查看地址余额。Parity 也是一个全节点钱包，优势在于安全度高，不需要经过第三方发起交易，劣势在于对网络要求高，需要连接节点才能发起交易。MyEtherWallet 作为一个轻钱包，上手难道不大，无需下载，在直接在网页上就可以完成所有的操作。在 MyEtherWallet 上生成的私钥由用户自我保管，平台方并无备份。优势在于方便快捷，连网即可发起交易，劣势在于交易时需要上传私钥，所以需要警惕钓鱼网站。imToken 是一款移动端钱包，操作简便，容易上手，功能齐全，在 imToken 上生成的钱包私钥保存在手机本地，平台方并无备份。优势在于第一是移动端钱包，操作界面十分友好，连网即可发起交易；第二是中国团队，客服好沟通，反应速度快。劣势在于未开源，这也是笔者不选择 imToken 的原因。MetaMask 的钱包属性偏弱，更多的是起到使 Chrome 浏览器兼容以太坊网络的作用。优点在于通过添加钱包插件将 Chrome 变成兼容以太坊的浏览器，缺点在于不支持自动显示 ERC20 代币。Ledger Nano S 是一款硬件钱包，安全性颇高的钱包，官方提供的软件功能较为局限。可以配合 MyEtherWallet 使用。优点在于安全性高，缺点一是官方软件功能差，无法调整 gas limit 和 gas price，二是价格贵并且较难买到。

**本文讲述了什么是以太坊，以及相关的一些概念，并给出相应的钱包选择和优劣对比。希望对读者有所帮助。**

下一篇文章是一篇译文，敬请期待。

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
