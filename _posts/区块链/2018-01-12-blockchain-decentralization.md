---
published: true
author: Robin Wen
layout: post
title: "中心化与去中心化"
category: 区块链
summary: "完全去中心化的技术一定是小众且难以商业化的，技术人的理想，与赤裸的人性和利益对抗，从来没赢过，以后也不会。中心化交易所最大的问题是什么，第一是安全问题，第二是信任问题，第三是稳定性问题。而去中心化的交易所，它只是智能合约上面的一层壳（特指以德、DEW），本身并不控制私钥，而是用户自己对自己的资产负责。中心化意味着信息传递的成本低，而去中心化信息传递相当高，不同节点之间通信就会有协议来保证。分布式系统里的 CAP 定理里提出 C、A、P 三者最多只能满足其中两个。中心化和去中心化，技术和商业之间，本身也是一种权衡。"
tags:
- 区块链
- Blockchain
- 中心化
- 去中心化
- 想法
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 19 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

在 [上一篇文章](https://dbarobin.com/2018/01/10/blockchain-opportunity) 里，提到近日某互联网前辈就区块链去中心化问题有如下的言论：

> 完全去中心化的技术一定是小众且难以商业化的，技术人的理想，与赤裸的人性和利益对抗，从来没赢过，以后也不会。

本篇文章，笔者就中心化和去中心化问题做一个探讨。

![2018-01-12-blockchain-decentralization](https://cdn.dbarobin.com/ZRMkSIC.jpg)

> 题图来自: © admin / Beginners Guide to Ethereum / create-hub.com

站在互联网信息流的角度，**中心化代表着集权，去中心化代表着分权**，keso 的一席话讲得很通透：

> 中心化（Centralization）和去中心化（Decentralization）就是集权与分权，在互联网上，就是指从我说你听的广播模式，向人人有个小喇叭的广场模式转变。中心化的典型例子是门户网站，去中心化的典型例子是 blog、UGC、社交媒体等。很多人对去中心化有一些误解，比如说 Facebook、Twitter 等正在成为更集中的中心。去中心化不是说今后不再有大网站，也不是说大网站就一定是中心化的，去中心化主要是指技术对普通用户的赋权。另外，去中心化也不是人人绝对平等的意思，总会有人更善于利用技术赋予的可能性，有人则不善用或不在乎。[1]

UGC 模式，让一个系统可以集中群体智慧，用户参与的成本极低，信息的流转速度极高。系统可以从中获取利润，而这些内容的生产者却往往得不到什么收益。

站在信息源的角度来看，信息源却变得越来越集中。早期可能同类型的应用有成千上万个，用户可供的选择也会很多，但随着资本的运作、产品的更迭、用户的积累等因素，某个细分领域的应用供选择的可能只有几个，甚至只有一个。互联网信息源从分散到集中，从百家争鸣到寡头垄断，这种聚集的速度快得让你无法想象。对于寡头来说，多的是利好；对于用户而言，多的是伤害。中心化的系统转身越困难，作恶的成本也会更高。

**我们来看下区块链行业里的中心化和去中心化。**

首先来看 **区块链技术本身**。比特币 P2P 网络确实比较健壮，但随之带来的效率极低。比特币采用的是 **POW** 共识算法。比特币网络中任何一个节点，如果想生成一个新的区块并写入区块链，必须解出比特币网络出的工作量证明的迷题。这道题关键的三个要素是工作量证明函数、区块及难度值。工作量证明函数是这道题的计算方法，区块决定了这道题的输入数据，难度值决定了这道题的所需要的计算量。[2] 比特币采用的工作量证明函是 SHA256。截至目前，比特币网络共有 [11541](https://bitnodes.earn.com) 个节点。以太坊同比特币一样，同样采用的是 POW 共识算法，截至目前，以太坊网络共有 [33607](https://www.ethernodes.org) 个节点。

我们再来看比特股。BTS 采用的是 **DPOS** 共识算法。DPOS 算法被分为了两部分。选举一组区块生产者和调度生产。选举流程要尽量保证，权益所有者最终能控制整个网络，因为一旦网络出现问题，权益所有者损失的将会最大。如何选举恰当的人对共识能否最终达成几乎无影响。DPOS 在可以应对多种网络情形，比如少数群体的分叉、隔离环境下的重复块生产、网络碎片、少数群体重复生产等。

DPOS 这种投票机制存在中心化，但是中心化是受到控制的，因为每个客户端都有能力决定哪些节点可以被信任。系统通过公平选举，决定哪些节点可以成为委托人。这种机制天然会带来更好的响应速度，不信你试试 BTS 转账速度快，还是比特币转账速度快。

我们再来看交易所。中心化交易所，如 [币安](https://www.binance.com/?ref=11190872)、[OKEx](https://www.okex.com/share/invite/3956Ew.do)、火币、[OTCBTC](https://otcbtc.com/referrals/ROBINWENIO) 等，去中心化交易所，如以德、DEW、BTS 等。**中心化交易所最大的问题是什么，第一是安全问题，第二是信任问题，第三是稳定性问题。** 第一，安全问题。中心化的交易所完全掌控了你的资产，包括公钥和私钥。你只需要一个邮箱和密码，最多再加一个安全验证码（如果设置了 Google 二次验证或者手机验证码验证）就可以完全访问中心化的交易所。开启 Google 二次验证或者手机验证，至少提高了安全度，不然你的账号在互联网上就是弱鸡。如果密码又设置得非常简单，可想而知了。其二，中心化的交易所存在的另一个问题就是容易遭受到攻击，常见的攻击有 SQL 注入、XSS 攻击、DDoS 攻击、Fraud 欺骗、Phishing 钓鱼、Clickjacking、CSRF 等。特别是加密货币交易所离金钱就只有一步之遥，更加成为黑客的香饽饽。第二，信任问题。一个交易所凭什么让用户相信你，这是一个团队备受考验的问题。怎么样保证用户的资产安全？交易所掌控了你的所有资产，如果被盗，怎么处理？交易所平台怎么保证投资平台币用户的利益？太多问题摆在交易所团队面前了。第三，稳定性问题。这里提一些问题你就知道保证稳定性是多么的困难了。怎么保证用户量上来了，网站还能如丝般顺畅？网站架构怎么样随着用户量的上升做优化？交易所 7*24 小时，选择什么时间来维护？不能上架国区 App Store，怎么样利用蒲公英等平台上架，上架了怎么样快速解决证书失效的问题？问题太多了。

而去中心化的交易所，它只是智能合约上面的一层壳（特指以德、DEW），本身并不控制私钥，而是用户自己对自己的资产负责。以德目前可以交易任何 ERC20 代币，你的私钥就是你的账户，买卖双发直接发起交易，后期还会推出移动端。DEW 提供了一种叫做 **链托管** 的机制来保证用户的资产安全，目前移动端的体验可以说非常流畅。当然，以德目前也是以网站为入口，也会存在中心化交易所类似的攻击，比如 2017 年 12 月 21 日网站被黑客 DNS 劫持。如果 DNS 劫持的时间段没有在以德进行交易，你的资产是不会受到威胁的，因为以德并没有存储你的私钥。

中心化意味着信息传递的成本低，而去中心化信息传递相当高，不同节点之间通信就会有协议来保证。分布式系统里的 CAP 定理里提出 C、A、P 三者最多只能满足其中两个。**中心化和去中心化，技术和商业之间，本身也是一种权衡。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**参考**

* [1] 知乎 (2011-07-20). 什么是中心化和去中心化？. Retrieved from [https://www.zhihu.com/question/19744551](https://www.zhihu.com/question/19744551).
* [2] InfoQ. (2016-05-06). 揭秘比特币和区块链（二）：什么是工作量证明？. Retrieved from [http://www.infoq.com/cn/articles/bitcoin-and-block-chain-part02](http://www.infoq.com/cn/articles/bitcoin-and-block-chain-part02).

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
