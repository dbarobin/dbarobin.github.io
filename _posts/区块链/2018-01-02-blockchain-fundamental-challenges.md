---
published: true
author: Robin Wen
layout: post
title: "「Fundamental challenges with public blockchains」解读"
category: 区块链
summary: "区块链技术要想成为主流应用需要克服的关键挑战。区块链工程师 Preethi Kasireddy 给出了以下说法：伸缩性限制、隐私限制、缺少形式化的合同审核、存储限制、不可持续的协商机制、缺乏监管和标准、工具不足和量子计算威胁。Medium 原文配图特别有意思，这是一个卡通图片，图中的主人公是全球首屈一指的新闻媒体大亨默多克。时至 2012 年 2 月，默多克旗下英国《太阳报》多名记者编辑被捕，彼时内部危机，默多克就像这张图片那样，如履薄冰。用这张图片来形容当前的区块链大规模应用，恰当无比。Kasireddy 明年（2018 年）的目标是：提高对这些问题的认识、倾注尽可能多的时间来为这些解决方案作出贡献、帮助其他研究人员和开发人员也这样做。无论现在的投资环境是否成为泡沫，Kasireddy 都是区块链坚定的信仰者。作为开发者，有义务投入精力去解决这些问题，将区块链带向主流人群。同时也需要投资者来发现和资助这些工作。"
tags:
- 区块链
- Blockchain
- 好文品鉴
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 14 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

区块链技术要想成为主流应用需要克服的关键挑战。区块链工程师 **Preethi Kasireddy** 给出了以下说法：

* 伸缩性限制
* 隐私限制
* 缺少形式化的合同审核
* 存储限制
* 不可持续的协商机制
* 缺乏监管和标准
* 工具不足
* 量子计算威胁

![Cartoon: Murdoch Walking on Thin Ice](https://cdn.dbarobin.com/RPHVOoP.png)

> 题图来自: © Preethi Kasireddy / Fundamental challenges with public blockchains / medium.com

Medium 原文配图特别有意思，这是一个卡通图片，图中的主人公是全球首屈一指的新闻媒体大亨默多克。时至 2012 年 2 月，默多克旗下英国《太阳报》多名记者编辑被捕，彼时内部危机，默多克就像这张图片那样，**如履薄冰**。用这张图片来形容当前的区块链大规模应用，恰当无比。

### 伸缩性限制
***

区块链共识协议要求网络中的每个节点都对每一个事务进行处理。这是由于区块链使用一种去中心化的共识机制来保证必要的特性，如安全保证、政治中立、审查阻力等等。

这里有两个事实：

* 低吞吐量
* 缓慢的事务时间，比如 Bitcoin block times 为 10 分钟，Ethereum block times 是 14 秒

但是笔者认为，Bitcoin 和 Ethereum 均采用 POW 共识机制，而运行良好的去中心化交易所 BitShares 可以达到十万级别的 TPS，所以 block times 可以达到秒级。吞吐量和事物时间跟共识机制息息相关。

Kasireddy 根据她之前做的一些工作提出了几项正在研究中的改进可伸缩性的建议。其中包括将多项交互或速度比较慢的计算从区块链中去除（Off-chain 支付通道和 Off-chain 计算）；把区块链的总体状态分到不同的「分片（shards）」中，每个碎片包含状态的一个部分，可以和其他碎片并行修改状态；使用有向无环图（DAGs，Directed Acyclic Graph）控制交互在区块链节点上的传播。

### 隐私限制
***

所有的交易结果都记录在一本公共账本上，让追溯成为可能。比如上传电子病历、身份认证数据、凭证信息、财务文档到公链，黑客、竞争者或者未经认证的第三方会利用这些信息。Kasireddy 列出了多种可能的解决方案，包括使用 ECDHM 地址；把交易汇聚到私有池（混币器）中；使用群签名（Group Signature）加密所有的交易，比如 Monero（门罗币）；使用零知识证明（Zero-knowledge proofs）；使用代码混淆（Code Obfuscation）；使用语言机（Oracles）；使用可信任执行环境（Trusted Execution Environments）。

零知识证明方面，Kasireddy 列出了几种例子，如：

* Challenge / response 游戏
* zkSNARKs
* zkSNARKs + Zcash
* zkSNARKs + Ethereum
* zkSTARKs

### 缺少形式化的合同审核
***

这与通常很难构建出本身没有缺陷的形式化证明有关。Kasireddy 没有提出多少建议来解决这个问题，但她提到了 Yoichi Hirai 为 Ethereum 所做的工作。

### 存储限制
***

存储数据也是由区块链数据复制和最终一致性导致的。这个问题的解决方案的思想基础是，不是每个存储节点都是一个完整节点，而是将数据切割或者分布到不同节点。类似的项目包括 Swarm 点到点文件共享协议、分布式存储解决方案 Storj、IPFS、去中心化内容分享平台 Decent等。

### 不可持续的协商机制
***

这是由目前广泛采用的 **工作量证明（proof-of-work）** 机制所导致的。POW 共识机制会有如下问题：定制的硬件会有挖矿的优势、采矿池集中、资源浪费。Kasireddy 列出的其中一个方案是采用 **权益证明（proof-of-stake）** 协商作为挖矿替代方案，计算协商过程中每个节点的权重。但是 POS 也有它自身的问题，比如没有任何权益问题、远程攻击、Cartel formation 等。

### 缺乏监管和标准
***

不言而喻，一个公共的、去中心化的区块链网络没有中心化的权威或者机构来做出决策。一方面，这给我们提供了我们所有人都梦寐以求的期望，一个完全信任，开源，无许可的系统。另一方面，协议没有安全的升级途径，也没有人负责创立和维护标准。

总体而言，区块链治理是一个极为棘手的问题，在集中控制和分布式控制之间寻求平衡将是保持发展正确的关键。

### 工具不足
***

充足的工具对于开发人员的工作是必不可少的，特别是如果开发人员希望有效地开展工作。

毫无疑问，当前可用于区块链生态系统的开发人员工具是不可接受的。 即使对于当今最经验丰富的开发人员来说，在区块链上开发功能协议或分布式应用也是一项艰巨的任务。

作为一名 Solidity 和区块链开发人员，以下是 Kasireddy 个人从工具生态系统中找不到的东西：

* IDE
* 构建工具和编译器
* 部署工具
* 技术文档
* 调试工具
* 日志工具
* 安全审计
* 区块查询和分析工具

当然，如上名次前都有形容词，区块链技术相关的工具才处在一个幼童期，并不代表这些工具完全不存在，而是并没有达到作者的要求。

### 量子计算威胁
***

量子计算机的问题是加密货币和密码学面临的迫在眉睫的威胁之一。

尽管今天的量子计算机在解决什么类型的问题方面仍然有限，但并不总是如此。 可怕的事实是，大多数流行的公钥算法可以被一个足够大的量子计算机有效地破解。

当我们设计和构建区块链和密码系统时，我们需要思考如何使这些属性具有量子保护能力，这一点很重要。

### 小结
***

Kasireddy 明年（2018 年）的目标是：

* 提高对这些问题的认识
* 倾注尽可能多的时间来为这些解决方案作出贡献
* 帮助其他研究人员和开发人员也这样做

无论现在的投资环境是否成为泡沫，Kasireddy 都是区块链坚定的信仰者。作为开发者，有义务投入精力去解决这些问题，将区块链带向主流人群。同时也需要投资者来发现和资助这些工作。

### 笔者附言
***

本文提出了不少问题，也提出了很多解决方案。一个技术从诞生到普及需要时间的积累，现在没有人说 Android 不成熟。

想要了解本文的完整内容，[点此](https://medium.com/@preethikasireddy/fundamental-challenges-with-public-blockchains-253c800e9428) 阅读。

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
