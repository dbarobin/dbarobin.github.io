---
published: true
author: Robin Wen
layout: post
title: 「区块链技术指北」官方社区 bbs.chainon.io 上线
category: 区块链
summary: >-
  从 2017 年 12 月 4
  号创建「区块链技术指北」专栏开始，陆续收到读者的反馈，于是在朋友的建议下建立微信群。运营微信群的过程中，越来越懂得有用户有读者的地方才有价值。于是有个想法，做个咱自己的社区。社区初衷：「区块链技术指北」官方社区。这里可以看到常见价值虚拟货币的相关的教程、区块链技术前沿信息、区块链技术好文品鉴和分享、区块链技术相关工具、区块链项目跟踪等。聚合区块链爱好者，创造持久价值，希望我们在区块链时代自由地行走。本站基于开源的
  Flarum，存储使用 MySQL，Web 服务器是 Nginx，VPS 采用 Vultr，服务器位于日本东京，域名购买自 GoDaddy，域名解析使用
  CloudFlare。网站支持 HTTPS，用户密码采用 bcrypt
  加密存储，所有话题、评论不做审核、不做删除。互联网本来的样子应该是开放的、自由的。兜兜转转写博客写了 7
  年，该是建立社区的时候了。「区块链技术指北」官方社区 bbs.chainon.io 于 2018 年 1 月 6 日上线，欢迎试用。
tags:
  - 区块链
  - Blockchain
  - 社区
comments:
  - author:
      type: twitter
      displayName: waterzeong37
      url: 'https://twitter.com/waterzeong37'
      picture: >-
        https://pbs.twimg.com/profile_images/897555652930707456/JajOpROS_bigger.jpg
    content: >-
      &#x6210;&#x4E3A;&#x4E92;&#x8054;&#x7F51;&#x8282;&#x70B9;&#x662F;&#x53CD;&#x6297;&#x793E;&#x4F1A;&#x539F;&#x5B50;&#x5316;&#x3002;
    date: 2018-01-07T14:43:39.876Z

---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 16 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

从 2017 年 12 月 4 号创建 **「区块链技术指北」** 专栏开始，陆续收到读者的反馈，于是在朋友的建议下建立微信群。运营微信群的过程中，越来越懂得 **有用户有读者的地方才有价值**。于是有个想法，做个咱自己的社区。

有想法容易，但要落地，要做成产品，还是要花功夫。

罗胖在昨天的罗辑思维音频节目中讲了如下的故事：在中国传媒大学电视系读书的时候，有一次，朱羽君教授讲评大家的摄影作业。她刚说到某张照片，哪儿哪儿有毛病的时候，那位同学就插嘴说，当时条件不允许按您说的那样拍。

朱教授停下来说：我不是在跟你讨论你的想法，我是在讨论你的作品。你将来到电视台做节目，你能站在每台电视机旁边跟观众解释，我这个镜头拍得不好，是因为当时的条件不允许拍好吗？**你的作品和产品是你和世界交流的唯一方式。你看，这就是想法和产品的区别。**

仔细品读这句话，再反观这一周利用业余时间上线这个社区，真是醍醐灌顶啊。

微信群的局限在于，第一，封闭，互联网上的一座孤岛。第二，沉淀特别困难，好在之前每周都有在做价值信息汇总，要不然部分价值信息就这样石沉海底了。第三，很难降噪。一个几百人的微信群，你一句我一句，然后就淹没到信息洪流中了。微信对群消息不友好，如果能做到像 Telegram 那样，可能体验会好一些。第四，内容审核。虽然官方声称这是没有的事，可是这事我真不信，要不然你在微信群发一个 diduan 人口试试。

于是构建社区的想法就来了，笔者想做一个没有审核、不会删帖、没有广告、干净、简洁易用、用户体验好、响应速度快、移动端支持好的社区。即使做这个事情不赚钱，能聚集一大帮区块链爱好者，这也是一大幸事。

筹备社区不是一个让人省心的事情，OmniOutliner 列表如下：

* 购买 bbs.chainon.io 域名
* 购买 VPS
* 开源社区筛选
* 部署环境
* Flarum 部署
* 域名解析
* HTTPS 支持
* 网站测试与优化
* 撰写 FAQ
* ...

开源社区选了十来款，最终敲定 **Flarum**，其他的要么太丑，要么太臃肿。Flarum 是一款优雅简洁的轻论坛。它有如下优点：

* **快速**。不臃肿、不复杂、不过度依赖。几分钟之内构建属于自己的社区。Flarum 基于 PHP 构建，因此很容易安装部署。界面使用 [Mithril](https://mithril.js.org) 构建，Mithril 是一个高性能的 JavaScript 框架。更少的等待，更多的交流。
* **精美的响应式布局**。Flarum 在设计之初就考虑了跨平台，开箱即用。并且 Flarum 的界面风格使用了 [LESS](http://lesscss.org)，这样很容易更换主题。
* **强大**。简约，并不简陋。定制性、可扩展性，都集成在了 Flarum 中。构建可扩展的软件是很困难的。值得庆幸的是，作者有多年开发 [esoTalk](https://esotalk.org) 和 [FluxBB](https://fluxbb.org) 的经验。Flarum 的开源架构是非常灵活的，它拥有完善的 API 和文档。

网站测试优化部分，笔者测试了手机终端兼容性，做了汉化，测试了邮箱、GitHub、Twitter 登录方式，修改了网站的相关信息……这种日夜兼程，忙得忘记时间的感觉真好。后续的运营、网站技术支持才是重点，未来还要花大力气打磨，咱一步一个脚印，慢慢优化。

![Google](https://cdn.dbarobin.com/3lyEZWR.jpg)

> 题图来自: © Google Images / Tech / google.com

这是笔者在网站 FAQ 上写的：

**社区初衷**：「区块链技术指北」官方社区。这里可以看到常见价值虚拟货币的相关的教程、区块链技术前沿信息、区块链技术好文品鉴和分享、区块链技术相关工具、区块链项目跟踪等。聚合区块链爱好者，创造持久价值，希望我们在区块链时代自由地行走。

本站基于开源的 [Flarum](https://github.com/flarum/flarum)，存储使用 MySQL，Web 服务器是 Nginx，VPS 采用 Vultr，服务器位于日本东京，域名购买自 GoDaddy，域名解析使用 CloudFlare。网站支持 HTTPS，用户密码采用 bcrypt 加密存储，所有话题、评论不做审核、不做删除。

互联网本来的样子应该是开放的、自由的。兜兜转转写博客写了 7 年，该是建立社区的时候了。

「区块链技术指北」官方社区 **[bbs.chainon.io](https://bbs.chainon.io)** 于 2018 年 1 月 6 日上线，欢迎注册试用。

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
