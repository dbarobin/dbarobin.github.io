---
published: true
author: Robin Wen
layout: post
title: Mixin Network 主网节点分析
category: 区块链
summary: 根据金色财经消息，北京时间 2 月 28 日上午 08:00:00（UTC 时间 00:00:00），Mixin Network 主网全球启动运营。本文根据公开的资料，对 Mixin Network 主网进行了溯源。节点过于集中，对生态好坏需要时间来检验。但是节点采用云服务商过于集中的问题，这个需要大力重视。Mixin 是我非常喜欢的应用，真心祝愿 Mixin Network 主网上线能够让 Mixin 生态逐渐成长。
tags:
  - 区块链
  - Blockchain
  - Mixin
---

`文/Robin`

这是「区块链技术指北」的第 **45** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

![](https://cdn.dbarobin.com/fIWTrII.png)

> 题图来自: © Mixin Network / All Cryptocurrencies Accepted Here / bitcointalk.org

## Mixin Network 主网上线
***

根据金色财经消息，北京时间 2 月 28 日上午 08:00:00（UTC 时间 00:00:00），Mixin Network 主网全球启动运营。[1]

Mixin Network 是何方神圣呢？笔者简单介绍下：

> Mixin Network 是一个面向数字资产的闪电网络，采用 BFT-DAG 机制，实现了转账 0 费用、1 秒到账、支付匿名、可无限扩展。基于 POS 共识的 Mixin Network 节点不采取竞选方式，抵押 10000 XIN 并拥有一定技术实力的团队即可运营节点，最多节点数量为 50 个。主网上线后，基于 ERC 20 发行的代币 XIN 不受影响。

## 节点分析
***

笔者是 Mixin Messager 第一批用户，拥有一个 2 开头的 5 位靓号（被 QQ 号洗脑了）。笔者持续关注 Mixin 动态，Mixin 主网上线，笔者想谈谈 Mixin Network 主网节点。

根据 GitHub 公开的 [Mixin Network Nodes](https://github.com/MixinNetwork/mixin/blob/master/config/nodes.json)，笔者进行了溯源，主要追踪了节点所属、IP 地址、云服务商、IP 所属地，做了思维导图，如图一：

![](https://cdn.dbarobin.com/Fn2bz8O.png)

图一 Mixin Network 主网节点

> 注：高清图点击「[此处](https://raw.githubusercontent.com/dbarobin/dbarobin.github.io/master/images/mixin-network-nodes.png)」下载。

根据此思维导图分析，目前 Mixin 主网有 15 个节点，BigONE 有 5 个，占比 33.33%，EOSLaoMao 有 2 个，占比 13.33%，Candy.ONE 有 1 个，占比 6.67%，ExinPool 有 1 个，占比 6.67%，Fox.ONE 有 1 个，占比 6.67%。其他只有 IP 没有域名的，暂时没法得知所属。15 个节点中，93.33% 的节点都在使用 Google Cloud，仅 ExinPool 使用香港腾讯云。

节点所属如图二：

![](https://cdn.dbarobin.com/whTO9xb.png)

图二 Mixin Network 主网节点所属

## 主网担忧
***

笔者对这样的结果表示很担忧。

第一，节点过于集中。我们把 Mixin Network 和 EOS 主网进行对比，EOS 主网前 21 节点，同一个超级节点团队拥有 2 个节点的情况基本没有，节点所属国家、采用的云服务商也相对分散。当然 Mixin Network 是抵押 XIN 建节点，EOS 是投票竞选节点，这一点是很不一样的。BigONE 目前拥有 5 个节点，占比 33.33%。同时，根据 BigONE 的「[共建 Mixin 节点生态](https://b1.run/mixin)」，未来 BigONE 还会运营不止 5 个的节点。

第二，93.33% 的节点都在使用 Google Cloud，这让笔者对 Mixin Network 的稳定性非常担忧。运营节点选择云服务商无可厚非，毕竟 IDC 服务成本过高，一般的节点团队完全没法承受。在全球市场，根据 Gartner 最新数据显示，亚马逊 AWS 占全球份额的 51.8%；微软 Azure 位列第二位，占比 13.3%；阿里云位列第三位，占比 4.6%；谷歌 Cloud 云服务占比 3.3%；随后是 IBM，占比 1.9%。[2] 为了避免某国不可抗力，大多数节点都会选择国外的云服务商，那可选项集中在亚马逊 AWS、微软 Azure、谷歌 Cloud 云服务。但有个非常严峻的事实是，云服务商高可用性并不能保证 100%。根据 InfoQ「[2018 年十大云宕机事故盘点：主流无一幸免！](https://www.infoq.cn/article/4pSNXHT4PuI4T*L8g1Sk)」一文，仅 2018 年，基本上市面上所有的云服务商都面临大大小小的宕机或者事故，造成了不等的经济损失。当然，Mixin Network 大多数的节点都在 Google Cloud，网络延时会相对较小，这也可能是集中的原因之一。就长远来看，这一定不是好事，节点应该把高可用性排在相当高的优先级，例如在其他云服务商部署备用节点等。

第三，国际化还有很长的路要走。Mixin 主网 15 个节点中，已知的节点全部是中国团队或者国人参与的团队，暂不清楚是否有原生的国外节点。Mixin Messager 的短板也是国际化运营。笔者观察过 Mixin Messager 的活跃度，基本上都是国人参与。主网上线，将会以社区式运营，融入更多世界各地的节点，对 Mixin 生态好处多多，然而可惜的是，抵押 1 万 Mixin 才能成为节点，这将会进一步提高其他国家加入 Mixin 主网的门槛。

## 小结
***

本文根据公开的资料，对 Mixin Network 主网进行了溯源。节点过于集中，对生态好坏需要时间来检验。但是节点采用云服务商过于集中的问题，这个需要大力重视。

Mixin Messager 是我非常喜欢的应用，真心祝愿 Mixin Network 主网上线能够让 Mixin 生态逐渐成长。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 参考资料
***

> [1] 金色财经 (2019-02-28). 动态 Mixin Network 主网今日启动. Retrieved from [https://www.jinse.com/lives/82559.htm](https://www.jinse.com/lives/82559.htm).
> [2] InfoQ (2018-12-27). 2018 年十大云宕机事故盘点：主流无一幸免！. Retrieved from [http://bit.ly/2IGrrP2](http://bit.ly/2IGrrP2).

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

![区块链技术指北](https://cdn.dbarobin.com/RBmpxTL.jpg)

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
