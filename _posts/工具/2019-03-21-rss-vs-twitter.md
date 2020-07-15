---
published: true
author: Robin Wen
layout: post
title: 我用 RSS 刷 Twitter
category: 工具
summary: >-
  关于 RSS，笔者之前有一篇文章叫做 RSS Never Die，文章的观点认为 RSS 是互联网阅读的最佳方式。此外，笔者还是 Twitter 用户，但
  Twitter 的信息流设计，总让人有一种信息焦虑感。那么问题来了？RSS 能不能刷 Twitter
  呢？笔者一番折腾，有了一个比较靠谱的解决方案，于是有了今天的文章。RSSHub 的 Slogan 是「万物皆可
  RSS」，提供了比较多的订阅源，读者也可以按照 RSSHub 的规范个性化定制。RSS 在个人信息订阅服务这个事情上大有作为，感兴趣的读者可以自行研究。
tags:
  - 互联网
  - 工具
  - RSS
  - Twitter
comments:
  - author:
      type: github
      displayName: liewzy
      url: 'https://github.com/liewzy'
      picture: 'https://avatars3.githubusercontent.com/u/3881873?v=4&s=73'
    content: >-
      &#x53EF;&#x4EE5;&#x8BD5;&#x8BD5; feedbin
      &#x5B83;&#x652F;&#x6301;twitter&#x7528;&#x6237;&#x7684;&#x5173;&#x6CE8;
      https://feedbin.com/
    date: 2019-04-07T08:11:07.596Z
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: >-
      @liewzy &#x611F;&#x8C22;&#xFF0C;&#x770B;&#x4E86;&#x4E0B;&#x6BCF;&#x6708;
      $5&#xFF0C;&#x8FD8;&#x662F;&#x559C;&#x6B22;&#x6781;&#x5BA2;&#x4E00;&#x70B9;&#x7684;&#x5904;&#x7406;&#x65B9;&#x5F0F;&#x3002;
    date: 2019-04-09T08:47:09.836Z

---

`文/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 前言
***

关于 RSS，笔者之前有一篇文章叫做 [RSS Never Die](https://dbarobin.com/2019/03/12/rss-never-die)，文章的观点认为 RSS 是互联网阅读的最佳方式。此外，笔者还是 Twitter 用户，但 Twitter 的信息流设计，总让人有一种信息焦虑感。

那么问题来了？RSS 能不能刷 Twitter 呢？笔者一番折腾，有了一个比较靠谱的解决方案，于是有了今天的文章。

![](https://cdn.dbarobin.com/m0JcSk4.png)

> 题图来自: © Heather Allen / 6 ways PR pros can use Feedly / prdaily.com

## 方案调研
***

笔者使用的 RSS 阅读组合是 Feedly + Reeder。Feedly 有提供一键导入 OPML 功能。OPML 是什么呢？

> OPML（Outline Processor Markup Language）是建立在 XML 基础上的「大纲处理标记语言」。在 RSS 中，OPML 其实就是订阅列表；使用 OPML 文件，可以帮助您实现 RSS 的批量订阅。[1] 目前多数 RSS 阅读器包括 Feedly、Inoreader 都支持 OPML 格式的导入导出。

于是方案就集中在如何将 Twitter 关注转换为 RSS 源以及如何生成 OPML 文件。Twitter 的 API 有很多玩法，当然提供获取关注列表的功能。关于如何将 Twitter 关注转换为 RSS 源，之前笔者介绍过一款工具叫做 RSSHub，它支持这个功能。文档链接点击 [此处](https://docs.rsshub.app/#twitter)。

## 方案实现
***

笔者写了一个脚本快速获取某个 Twitter 用户的关注。要正确地运行脚本，读者需要一个 Twitter App，申请链接点击 [此处](https://apps.twitter.com/app/new)。

脚本已经托管到 GitHub，点击 [此处](https://github.com/dbarobin/twitter) 获取。其中提供两个脚本，[twitter-following.py](https://github.com/dbarobin/twitter/blob/master/twitter-following.py) 和 [twitter-opml.py](https://github.com/dbarobin/twitter/blob/master/twitter-opml.py)。twitter-following.py 将指定 Twitter 账号的关注用户获取下来，以 RSS 订阅源的链接保存为文件，读者需要使用类似 [OPML generator](https://opml-gen.ovh) 的 OPML 生成服务生成 OPML 文件，不过缺点在于没法配置 Title（Feedly 根据 Title 保存文件夹）。twitter-opml.py 可以直接生成 OPML 文件，并且可以指定 Title，推荐使用。

OPML 文件生成之后，接着访问 Feedly 的 [OPML Import](https://feedly.com/i/cortex) 页面一键导入。[2] 之后就可以在 Reeder 内愉快地刷 Twitter 了。

## 小结
***

RSSHub 的 Slogan 是「**万物皆可 RSS**」，提供了比较多的订阅源，读者也可以按照 RSSHub 的规范个性化定制。RSS 在个人信息订阅服务这个事情上大有作为，感兴趣的读者可以自行研究。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 参考资料
***

> [1] 开源中国 (2010-09-11). OPML. Retrieved from [https://www.oschina.net/p/opml](https://www.oschina.net/p/opml).
> [2] EDWIN K (2017-07-27). New OPML Import. Retrieved from [https://blog.feedly.com/opml](https://blog.feedly.com/opml).

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
