---
published: true
author: Robin Wen
layout: post
title: 隐私极客 Privacy Geek | 0x10 KeyBase
category: 隐私极客
summary: 上一篇文章讲解了 Apple ID，提到 Apple ID 的一些使用技巧。本篇文章为大家讲讲 Keybase。Keybase 是一个基于 PGP 技术的 社交网络平台，它可以将用户的身份映射到公钥，反之亦然。目前在 keybase.io 上提供身份验证的网站和服务有：Twitter、GitHub、Reddit、Coinbase 和 Hacker News，另有网站管理员、 比特币、 Zcash 地址的验证。好了，Keybase 就介绍到这里。IM 满天飞的互联网世界，Keybase 是一个更极客般地存在。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
  - Keybase
---

`文/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

大家好，好久不见。上一篇文章讲解了 [Apple ID](https://dbarobin.com/2019/10/20/privacy-geek-appleid/)，提到 Apple ID 的一些使用技巧。本篇文章为大家讲讲 Keybase。

在讲解 Keybase 之前，给大家简单介绍 Keybase 是啥。

> Keybase 是一个基于 PGP 技术的 社交网络平台，它可以将用户的身份映射到公钥，反之亦然。目前在 keybase.io 上提供身份验证的网站和服务有：Twitter、GitHub、Reddit、Coinbase 和 Hacker News，另有网站管理员、 比特币、 Zcash 地址的验证。[1]

![](https://cdn.dbarobin.com/fheznwg.png)

> 题图来自: © Chris Brook / Keybase Extension Brings End-to-End Encrypted Chat To Twitter, Reddit, GitHub / threatpost.com

Keybase 是由哈佛数学系学生 [Chris Coyne](https://chriscoyne.com/) 和麻省理工博士 [Max Krohn](https://keybase.io/max) 共同创立。

除了身份验证之外，Keybase 还提供如下的扩展：

* 端对端加密聊天通讯；
* 类似 Slack 的加密团队聊天和协作；
* KBFS 加密文件存储服务，支持公共文件和私有文件，公共文件支持静态文件托管服务；
* 集成 Stellar（XLM）钱包（恒星币）功能，支持创建和导入 Stellar 账户，可以通过 Keybase 发送 XLM 给其它用户；
* 加密 Git 代码仓库托管服务，用户可以创建自己的私有仓库。

那么问题来了，Keybase 使用有哪些技巧呢？

第一，验证用户身份。这个功能也是 Keybase 最核心的功能。想象一种场景，某天读者的 Telegram 突然收到一条陌生人发来的消息，如果读者想继续和他沟通，那您可以要求他提供 Keybase 验证身份，并且聊天也迁移到 Keybase 进行。如果真得找你有什么事，对方自然不会推脱。Telegram 有太多的虚假信息，笔者建议读者务必谨慎。

第二，PGP 也是 Keybase 的核心功能。如果读者对 PGP 不了解，可以阅读之前关于 PGP 的文章，点击 [此处](https://dbarobin.com/2019/05/02/privacy-geek-pgp/) 阅读。

> Twitter [Song Gao](https://twitter.com/__songgao__) 补充：PGP 并不是 Keybase 的核心功能。App 里面的 feature 跟 PGP 没有关系。PGP 的设计无法满足多设备和多用户的安全需求，key rotation 也不友好。Keybase 使用的是另一套系统，详见 [此处](https://book.keybase.io/account)。

第三，Keybase 也提供 PGP 私钥托管保存功能，Keybase 声称 PGP 私钥必须通过用户的 passphrase 才能解密，解密是通过浏览器客户端本地完成，服务器并不会保存 passphrase。这个托管功能并不是强制的，上传与否要看你是否足够信任 Keybase 了。

第四，Keybase 提供 XLM、BTC、Zcash 地址验证，除了 XLM 是 Keybase 集成之外，BTC 和 Zcash 添加的都是外部地址。笔者建议，不要在 Keybase 上验证存储大额的 BTC 和 Zcash 地址。永远不要对外对外告知别人读者有多少比特币。

第五，恒星发展基金会 (Stellar Development Foundation) 和信息服务公司 Keybase 于 2019 年 9 月宣布，将联合空投 20 亿个恒星币（XLM），价值约 1.2 亿美元，在接下来的 20 个月里，Keybase 用户每月可以获得空投 1 亿个 XLM。可惜的是，XLM 空投被「羊毛党」薅停了，KeyBase 宣布提前中止 1.2 亿美元空投。如果读者早一些关注 Keybase，说不定也能获得 XLM 空投。

第六，Keybase 提供多个平台的客户端，包括 macOS、Linux、Windows、iOS、Android、Chrome、Firefox，具体的下载页面在这里：[https://keybase.io/download](https://keybase.io/download)。

好了，Keybase 就介绍到这里。IM 满天飞的互联网世界，Keybase 是一个更极客般地存在。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

最后，为了安全起见，读者只有四个渠道订阅本专栏，第一是本博客，RSS 订阅点击 [此处](https://dbarobin.com/feed.xml)。第二是在线小册，[https://privacy.dbarobin.com](https://privacy.dbarobin.com)。第三是 Telegram Channel，名叫 [隐私极客](https://t.me/privacygeek)。第四是 Mixin Channel，下载安装 [Mixin Messenger](https://mixin.one/messenger) 之后，访问 [mixin://users/b8f7e6e4-9ac2-4a85-9b17-257faac2d8d6](mixin://users/b8f7e6e4-9ac2-4a85-9b17-257faac2d8d6) 订阅。博客以文章为主，Telegram Channel 内容多样化且碎片化（图片、音频、视频、文字、链接等），Mixin Channel 作为同步渠道。归档页面点击 [此处](https://dbarobin.com/privacy/)。

### 「隐私极客」系列文章
***

* [隐私极客 Privacy Geek 0x00 开篇](https://dbarobin.com/2019/04/14/privacy-geek-prologue/)
* [隐私极客 Privacy Geek 0x01 PGP](https://dbarobin.com/2019/05/02/privacy-geek-pgp/)
* [隐私极客 Privacy Geek 0x02 Telegram](https://dbarobin.com/2019/05/14/privacy-geek-telegram/)
* [隐私极客 Privacy Geek 0x03 DuckDuckGo](https://dbarobin.com/2019/06/07/privacy-geek-duckduckgo/)
* [隐私极客 Privacy Geek 0x04 Firefox](https://dbarobin.com/2019/07/21/privacy-geek-firefox/)
* [隐私极客 Privacy Geek 0x05 Google Voice](https://dbarobin.com/2019/08/10/privacy-geek-google-voice/)
* [隐私极客 Privacy Geek 0x06 国内手机号](https://dbarobin.com/2019/08/18/privacy-geek-mobile/)
* [隐私极客 Privacy Geek 0x07 Gmail](https://dbarobin.com/2019/10/01/privacy-geek-gmail/)
* [隐私极客 Privacy Geek 0x08 ProtonMail](https://dbarobin.com/2019/10/13/privacy-geek-protonmail/)
* [隐私极客 Privacy Geek 0x09 Apple ID](https://dbarobin.com/2019/10/20/privacy-geek-appleid/)
* [隐私极客 Privacy Geek 0x10 KeyBase](https://dbarobin.com/2020/04/24/privacy-geek-keybase/)

### 参考资料
***

> [1] Wikipedia (2019-08-24). Keybase. Retrieved from [https://zh.wikipedia.org/wiki/Keybase](https://zh.wikipedia.org/wiki/Keybase).

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>

***

本博客开通了闪电网络打赏，读者可以扫描下方的闪电网络二维码进行打赏。

<center><img title="Bitcoin Lightning Network Donate" width="180" height="180" src="https://lnd.hoo.com/api/generate?openid=TruSwjrK2q57V484Tf0u&isimg=1" alt="Bitcoin Lightning Network Donate"/></center>