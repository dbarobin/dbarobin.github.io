---
published: true
author: Robin Wen
layout: post
title: 隐私极客之 PGP
category: 隐私极客
summary: 4 月份这个专栏开了个头，这算是「隐私极客」的第一篇文章。想了许久，第一篇文章写什么主题呢？列了很多大纲，最后还是选定 PGP。OpenPGP 是由 PGP 衍生出的开源规范，而 GnuPG（简称 GPG）就是遵循 OpenPGP 规范的 GNU 实现。在不需要特别说明差异的情况下，三者统称为 PGP。只要自己的私钥安全、密码安全，可以说只有信息交互的双方才能解密，再顶级的黑客也无能为力。保护隐私非常重要，PGP 就是一个非常实用的利器，希望本文能给注重隐私的读者带来一些帮助。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
  - PGP
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

4 月份这个专栏开了个头，这算是「**隐私极客**」的第一篇文章。想了许久，第一篇文章写什么主题呢？列了很多大纲，最后还是选定 PGP。

PGP 是什么呢？不少从事互联网行业的可能也未必知道，这个技术不断地从我们的视野中消失，然而这却是一个相当有用的技术。可能读者会好奇独立博客博主为什么会在自己的博客上留 PGP 公钥，也在某些电影看到 PGP 用于发送邮件。诚然，虽然普通网民越来越少地使用 PGP，但 PGP 用到的非对称加密是加密学领域非常重要的技术。

OK，言归正传，可能部分读者对 PGP 比较陌生，容笔者简单介绍下。

> PGP 是 [Pretty Good Privacy](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) 的缩写，由 [Philip R. Zimmermann](http://philzimmermann.com/EN/background/index.html) 于 1991 年编写并上传到 USENET 而在全球流行，被广泛用于文本、电子邮件、文件甚至整个磁盘分区的签名、加密和解密。上传行为因涉嫌违反美国的加密软件出口限制而使 Zimmermann 陷入长达三年的犯罪调查。政府的调查终止之后，Zimmermann 创立了 PGP 公司，PGP 由此成为商业软件（PGP 公司在辗转多次之后最终被 Symantec 收购）。[1]

[OpenPGP](https://www.openpgp.org) 是由 PGP 衍生出的开源规范，而 [GnuPG](https://gnupg.org)（简称 GPG）就是遵循 OpenPGP 规范的 GNU 实现。在不需要特别说明差异的情况下，三者统称为 PGP。

![](https://cdn.dbarobin.com/eWAUxsB.jpg)

> 题图来自: © DARKNETMARKETS / Have an Anonymous Chat / darknetmarkets.co

PGP 使用的是非对称加密算法，关于 PGP 的更多知识，可以去阅读 [维基百科](https://zh.wikipedia.org/wiki/PGP) 或者 Google 搜索「PGP」。对于普通网民来说，PGP 有什么用呢？在此简单描述下使用方法。A 生成公私钥对，B 也生成公私钥对，A 将自己的公钥给 B，B 也将自己的公钥给 A，也就是 A 和 B 互相交换自己的公钥。然后 A 使用 B 的公钥并且用自己的私钥加密文本，将加密后的文本发给 B，B 用自己的私钥解密文本，反之亦然。当然了，PGP 加密解密文件也是类似的。加密解密过程示意图如下：

![](https://cdn.dbarobin.com/cJVocVj.png)

> 图片来自: © GO ANYWHERE / Open PGP Encryption / goanywhere.com

PGP 的工作原理是端对端加密，[Telegram](https://telegram.org) Secret Chat、[Singal](https://signal.org)、[Wire](https://wire.com) 等 IM 也提供端对端加密聊天功能。但为什么要用 PGP 呢？因为 PGP 是相当靠谱的加密工具，其安全性得到了充分的验证。

PGP 适用于大多数加密传输的场景。有了 PGP，读者甚至可以在接近裸奔的微信里愉快地聊天，也不怕隔空拦截或者和谐封号。文本、文件、邮件……任何涉及到加密传输的场景都有用武之地。以区块链行业为例，可能不少用户平时会在不同设备之间传输助记词、密码甚至私钥，根据笔者的观察，不少团队使用了微信、钉钉、云笔记、网盘等工具，然而这是相当危险的。更稳妥的办法，读者需要 PGP。简单讲一下使用方法，读者自行注册两个 Gmail 邮箱（当然也可以是其他非国内的邮箱，具体的以后再讲），使用这两个邮箱生成公私钥对，在自己的这两个公私钥对之间进行加密解密即可。

可能读者又有疑惑了，PGP 讲解了这么多，具体怎么用？读者感兴趣可以参考 OpenPGP 网站提供的 [软件](https://www.openpgp.org/software) 页面，针对不同的终端选择不同的软件。根据笔者的经验，不同终端推荐使用如下的软件：

* macOS：[GPG Suite](https://gpgtools.org)
* Windows：[Gpg4win](https://www.gpg4win.org)
* iOS：[PGP Everywhere](https://itunes.apple.com/us/app/pgp-everywhere/id1011677987?mt=8)，付费软件，美区 $4.99
* Android：[OpenKeychain: Easy PGP](https://play.google.com/store/apps/details?id=org.sufficientlysecure.keychain&hl=en)
* Chrome：[Mailvelope](https://chrome.google.com/webstore/detail/mailvelope/kajibbejlbohfaggdiogboambcijhkke)
* Firefox：[Mailvelope](https://addons.mozilla.org/firefox/downloads/latest/mailvelope)

至于操作教程，没那么难，Google 下即可，当然也可以使用 DuckDuckGo 搜索。不过笔者在此列一些注意事项供读者参考，同时以下注意事项会不定期更新。

* 公私钥对创建之后，不要将公钥上传到公共的的服务器。建议保存在本地，有需要的时候才将自己的公钥发给别人；
* macOS 部分编辑器或者软件无法调用系统的 OpenGPG 服务，所以可以配合浏览器插件 Mailvelope 使用；
* 可以将 PGP Everywhere 设置成键盘，输入文字、选择文本的时候就可以进行加密、解密操作，非常方便；
* Mailvelope 插件注意不要勾选「Remember password temporarily」，建议每一次解密都输入密码；
* 重要邮件使用 PGP 发送，是一个很好的习惯；
* 部分 Cryptocurrency Exchange 支持添加 PGP 公钥，这样 Exchange 发给你的邮件需要读者解密才能查看；
* 隐私是无价的，PGP 的学习成本和使用成本没有想象中那么高；
* 务必保存好私钥、密码。

只要自己的私钥安全、密码安全，可以说只有信息交互的双方才能解密，再顶级的黑客也无能为力。保护隐私非常重要，PGP 就是一个非常实用的利器，希望本文能给注重隐私的读者带来一些帮助。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/u4oonoo.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
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
* [隐私极客之 Apple 剪贴板](https://dbarobin.com/2020/07/10/apple-clipboard/)
* [隐私极客之 Wi-Fi](https://dbarobin.com/2020/07/15/wifi/)
* [隐私极客之 Namecheap](https://dbarobin.com/2020/07/23/namecheap/)
* [隐私极客之 Tuber](https://dbarobin.com/2020/10/10/tuber/)

***

### 参考资料
***

> [1] yestyle (2017-08-02). PGP 自我扫盲. Retrieved from [https://www.jianshu.com/p/0e1e66423055](https://www.jianshu.com/p/0e1e66423055).

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>