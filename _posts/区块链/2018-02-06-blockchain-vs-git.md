---
published: true
author: Robin Wen
layout: post
title: 从 Git 到区块链
category: 区块链
summary: >-
  GitHub 网站在程序员心目中的地位不言而喻，这个基于 Git 的托管网站可以说是开源的风向标。我们在 GitHub 中的 Git repo 中还可以找到
  2005 年 4 月 8 日 Linus Torvalds 提交的第一版代码。再者，区块链的特性是分布式的，而 Git 天然就是分布式的，不过 Git
  是依赖文件系统。GitHub 助力全球化协作，代码或者文档一旦提交到 GitHub，这个操作将是无法撤销的。GitHub 本身会将其网站上的 repo
  利用分布式存储做好容灾；如果某程序员 clone 该 repo，只要它不删除，将会永久存储在自身的电脑，除非自身电脑文件系统崩溃；如果某程序员 fork 该
  repo，只要它的账户不被删除，这个 repo 将永久保留在它的账户之下；另外，某个 repo fork、clone
  次数越多，被摧毁的概率也就越低；再者，某个 repo 即使最近一次操作清空了所有的代码，还可以通过 git log 恢复。这样一分析，科学上网的
  GitHub repo 将是野火烧不尽，春风吹又生。也就是说，Git 是简化的 blockchain，用 Git 来理解区块链，这个事情是不是特别有趣？
tags:
  - 区块链
  - Blockchain
  - 想法
comments:
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: >-
      &#x533A;&#x5757;&#x94FE;&#x4E0E; Git
      &#x7248;&#x672C;&#x5DE5;&#x5177;&#x7684;&#x6BD4;&#x8F83;

      https://www.jianshu.com/p/b96b98983df6
    date: 2018-02-06T13:06:16.274Z
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: >-
      Git &#x548C;
      &#x533A;&#x5757;&#x94FE;&#x7684;&#x76F8;&#x4F3C;&#x6027;&#x548C;&#x5DEE;&#x5F02;&#x6027;&#x6BD4;&#x8F83;

      https://zhuanlan.zhihu.com/p/33927320
    date: 2018-02-24T06:58:52.351Z
  - author:
      type: twitter
      displayName: HuCheung
      url: 'https://twitter.com/HuCheung'
      picture: >-
        https://pbs.twimg.com/profile_images/467812487539093504/6e-2aacg_bigger.jpeg
    content: >-
      &#x522B;&#x95F9;&#x597D;&#x5417;&#xFF1F;&#xFF01;
      &#x4E8C;&#x8005;&#x6709;&#x672C;&#x8D28;&#x7684;&#x533A;&#x522B;&#xFF0C;
      &#x4E00;&#x4E2A;&#x662F;decentralized&#xFF0C;
      &#x4E00;&#x4E2A;&#x4E0D;&#x662F;&#x3002;&#x8BF4;&#x9500;&#x6BC1;&#x4F60;&#x7684;repo&#xFF0C;github&#x5206;&#x5206;&#x949F;&#x949F;&#x4E8B;&#xFF0C;blockchain&#x5374;&#x51E0;&#x4E4E;&#x4E0D;&#x53EF;&#x80FD;&#xFF01;
    date: 2018-05-21T01:01:24.216Z

---

`文/Robin`

> 本文由币乎(bihu.com)优质内容计划支持。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 24 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

**GitHub 网站在程序员心目中的地位不言而喻，这个基于 Git 的托管网站可以说是开源的风向标。** 我们在 GitHub 中的 Git repo 中还可以找到 2005 年 4 月 8 日 Linus Torvalds 提交的 [第一版](https://github.com/git/git/tree/e83c5163316f89bfbde7d9ab23ca2e25604af290) 代码。

正是 GitHub 网站在全球的成功运营，加密数字货币开源项目才得以爆发性地增长。这个世界上最顶尖的程序员都在为这些开源项目提交 PR，这种社区式连接的力量，是无法衡量的。我们可以在 GitHub 上看到 [Bitcoin](https://github.com/bitcoin/bitcoin)、[Ethereum](https://github.com/ethereum/go-ethereum)、[EOS](https://github.com/EOSIO/eos) 等项目的最新更新状态，每天都有无数的程序员为这些项目添砖加瓦。**加密数字货币的共识，很大一部分来自 GitHub 社区。**

![2018-02-06-blockchain-vs-git](https://cdn.dbarobin.com/fgXyzdP.jpg)

> 题图来自: © EarlGrey / Migrate to Qcloud Git Server / codingpy.com

那 Git 究竟是什么呢？简单讲就是一个 **分布式版本控制软件**。如果用三句话阐述什么是 Git 运行原理，那就是：[1]

* 生成修改过的文件
* 生成当前目录 tree 文件，关联当前状态文件
* 生成 commit 文件，关联到当前目录 tree 文件，并记下父 commit

我们来试着理解这句话。当我们在 GitHub 创建一个新项目再 git clone 到本地（当然也可以本地 git init 初始化项目），把本地的代码放到这个目录，然后我们要怎么样提交到 GitHub 呢？我们在配置相应的 SSH 公钥之后，会执行如下的几个步骤来提交到 GitHub：

* git add -A .
* git commit -m "[Blockchain] ..."
* git push origin master

简单讲就是，先本地提交，累积几次后再一次 push 到 remote。本次提交会关联到上一次提交，这跟区块链的 chain 是不是有些类似？比如本次提交的标志为 ab88b43，上一次提交的标志为 297f29a，那么本次提交的信息里就会有 parent commit 297f29a 的信息。这种类似链条的记录，是不是变得可追溯了？版本控制最重要的是什么，就是可追溯，如果某次错误提交，还可以回退到历史版本。同样，**可追溯也是区块链的重要特性。**

再者，**区块链的特性是分布式的，而 Git 天然就是分布式的**，不过 Git 是依赖文件系统。GitHub 助力全球化协作，代码或者文档一旦提交到 GitHub，这个操作将是无法撤销的。GitHub 本身会将其网站上的 repo 利用分布式存储做好容灾；如果某程序员 clone 该 repo，只要它不删除，将会永久存储在自身的电脑，除非自身电脑文件系统崩溃；如果某程序员 fork 该 repo，只要它的账户不被删除，这个 repo 将永久保留在它的账户之下；另外，某个 repo fork、clone 次数越多，被摧毁的概率也就越低；再者，某个 repo 即使最近一次操作清空了所有的代码，还可以通过 git log 恢复。这样一分析，科学上网的 GitHub repo 将是 **野火烧不尽，春风吹又生**。

我的 [博客](https://dbarobin.com) 所有文章都存放在 GitHub，读者觉得我的文章还会被删吗？最近一天世界的一篇关于微信的文章很有趣，不妨 [看看](https://blog.yitianshijie.net/2018/02/02/wechat-equals-gfw)。

另外，区块链的另一个特性是不可篡改，也就是只能 INSERT。Git 呢？GitHub 托管的 repo 里的内容本身是可以修改的，然而这个 commit 历史却是无法修改的。每一次 commit 都有唯一标志，本次 commit 会有 parent commit 的信息。Git 产生的 log 也可以通区块链数据库进行类比。

**也就是说，Git 是简化的 blockchain，用 Git 来理解区块链，这个事情是不是特别有趣？**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**参考**

* [1] douban.com (2013-07-18). 三句话的 git internal. Retrieved from [https://www.douban.com/note/289253352](https://www.douban.com/note/289253352).

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
