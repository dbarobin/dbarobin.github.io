---
published: true
author: Robin Wen
layout: post
title: Bilibili UP 主「机智的党妹」被勒索了
category: 隐私极客
summary: 党妹，哔哩哔哩美妆区 UP 主， Bilibili 2019 百大 UP 主。4 月 27 日，党妹自曝「被勒索了」。事情经过大概是这样的：团队协作产生了大量的素材，普通电脑没法满足存储需求，于是在公司内网部署了一套 NAS，不料黑客入侵把所有的资料加密，而且留下需要付费才能解密这些文件，导致最新的视频无法按时更新，并且历史的素材也遭受损失。隐私和安全是个永恒的问题，对于个人而言，防范永远是最重要的，因为你永远不知道你的数据将会在 Cyberspace 怎么流转。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
  - Bilibili
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

党妹，哔哩哔哩美妆区 UP 主， Bilibili 2019 百大 UP 主。4 月 27 日，党妹自曝「[被勒索了](https://www.bilibili.com/video/BV1ii4y1t7i1/)」。事情经过大概是这样的：团队协作产生了大量的素材，普通电脑没法满足存储需求，于是在公司内网部署了一套 NAS，不料黑客入侵把所有的资料加密，而且留下需要付费才能解密这些文件，导致最新的视频无法按时更新，并且历史的素材也遭受损失。

![](https://cdn.dbarobin.com/nx3mhoa.jpg)

> 题图来自: © The Motley Fool / Alibaba Takes a Big Stake in China’s Gen Z Darling, Bilibili / nasdaq.com

## 分析
***

从党妹公开的视频来看，应该是黑客广撒网，并不是针对党妹个人的行为。极大的可能是 NAS 服务器开启了暴露在外网的功能，黑客通过外网攻破并勒索。由于勒索病毒通常都是采用了非对称加密算法，在没有私钥的情况下，完全没办法解密。这也就是黑客说不要去找数据恢复公司，有可能继续被诈骗的原因。

与此同时，最近有个消息。Shade（Troldesh）勒索软件的运营商已在周末关闭，并以此表示诚意，已释放了 75 万多个解密密钥，过去的受害者现在可以使用这些密钥来解密其文件。已在 GitHub 上发布了超过 750,000 个解密密钥：[https://github.com/shade-team/keys](https://github.com/shade-team/keys)。

这两个事情结合在一起看，是不是挺魔幻。

## 防范
***

为了避免此类事情发生在自己的身上，有什么防范工作可以做的呢？

第一，个人或者团队的 NAS 建议部署在内网，然后采用 frp 之类的内网穿透方案让公网访问。此外，像「群晖」之类的 NAS 有 IP 白名单功能，也建议启用。

第二，假如没有采用 NAS，建议采用 Dropbox 之类的云盘方案，解决好网络问题，是一个极佳的效率利器。不过值得注意的是，您不应该信任任何网盘，私密的资料还是应该加密后再上传。

第三，重要资料多重备份，具体的备份方案，可以阅读笔者的「[备份与恢复的一点思考](https://dbarobin.com/2017/11/17/thoughts-on-backup-and-recovery/)」一文。

第四，Windows 开启 Defender，此外把系统的更新功能打开，定期更新高危漏洞补丁。macOS 把系统的防火墙开启，

第五，注重个人隐私，尽可能地不要在互联网暴露自己。BTW，「隐私极客」已经更新了 10 来篇文章了，欢迎持续关注更新。

## 小结
***

隐私和安全是个永恒的问题，对于个人而言，防范永远是最重要的，因为你永远不知道你的数据将会在 Cyberspace 怎么流转。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/u4oonoo.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

最后，读者有五个渠道订阅本专栏，第一是本博客，RSS 订阅点击 [此处](https://dbarobin.com/feed.xml)；第二是公众号，微信搜索「隐私极客」；第三是在线小册，[https://privacy.dbarobin.com](https://privacy.dbarobin.com)；第四是 Telegram Channel，名叫 [隐私极客](https://t.me/privacygeek)；第五是 Mixin Channel，下载安装 [Mixin Messenger](https://mixin.one/messenger) 之后，访问 [https://mixin.one/codes/27ca9fb0-bbae-4260-ad37-97ec9f7fcbeb](https://mixin.one/codes/27ca9fb0-bbae-4260-ad37-97ec9f7fcbeb) 订阅。博客以文章为主，Telegram Channel 内容多样化且碎片化（图片、音频、视频、文字、链接等），Mixin Channel 作为同步渠道。归档页面点击 [此处](https://dbarobin.com/privacy/)。

### 「隐私极客」系列文章
***

* [隐私极客之开篇](https://dbarobin.com/2019/04/14/privacy-geek-prologue/)
* [隐私极客之 PGP](https://dbarobin.com/2019/05/02/privacy-geek-pgp/)
* [隐私极客之 Telegram](https://dbarobin.com/2019/05/14/privacy-geek-telegram/)
* [隐私极客之 DuckDuckGo](https://dbarobin.com/2019/06/07/privacy-geek-duckduckgo/)
* [隐私极客之 Firefox](https://dbarobin.com/2019/07/21/privacy-geek-firefox/)
* [隐私极客之 Google Voice](https://dbarobin.com/2019/08/10/privacy-geek-google-voice/)
* [隐私极客之国内手机号](https://dbarobin.com/2019/08/18/privacy-geek-mobile/)
* [隐私极客之 Gmail](https://dbarobin.com/2019/10/01/privacy-geek-gmail/)
* [隐私极客之 ProtonMail](https://dbarobin.com/2019/10/13/privacy-geek-protonmail/)
* [隐私极客之 Apple ID](https://dbarobin.com/2019/10/20/privacy-geek-appleid/)
* [隐私极客之 KeyBase](https://dbarobin.com/2020/04/24/privacy-geek-keybase/)
* [Bilibili UP 主「机智的党妹」被勒索了](https://dbarobin.com/2020/05/12/bilibili-up-blackmail/)

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>