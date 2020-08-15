---
published: true
author: Robin Wen
layout: post
title: 隐私极客之 Firefox
category: 隐私极客
summary: 上一篇文章讲解了 DuckDuckGo，提到 DuckDuckGo 适合 Firefox 配合使用，本篇文章就为大家讲解 Firefox。Firefox 可以说是黑客利器，连 Tor Browser 都是基于 Firefox ESR 的。Firefox 相比其他浏览器有什么优势呢（国内毫无操守的浏览器就不再此讨论范围）？注重隐私未来是大势所趋，Firefox 浏览器将成为这部分用户的首选。Chrome 和 Google 的服务绑定太深，抛开 Chrome，选择还有很多。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

上一篇文章讲解了 [DuckDuckGo](https://dbarobin.com/2019/06/07/privacy-geek-duckduckgo)，提到 DuckDuckGo 适合 Firefox 配合使用，本篇文章就为大家讲解 Firefox。

![](https://cdn.dbarobin.com/kkqHnTR.jpg)

> 题图来自: © Tim Murray / Firefox: The Evolution Of A Brand / blog.mozilla.org

Firefox 可以说是黑客利器，连 Tor Browser 都是基于 Firefox ESR 的。Firefox 相比其他浏览器有什么优势呢（国内毫无操守的浏览器就不再此讨论范围）？

第一，开发 Firefox 的 Mozilla 是一个「非营利组织」；而开发 Chrome 的 Google 是一家「商业公司」。商业公司想要的是从用户的数据获取更多的利益，而非赢利组织可以坚持操守。Google 的利润，90% 以上是来自于广告收入。广告讲究「精准投放」，Google 必须要采集海量数据才能做到，因为，对于追求隐私的用户而言，Chrome 并不是什么好的选择。

第二，2017 年 11 月 14 日，Firefox Quantum 的第一个通用版本发布，意味着 Firefox 更快了。Firefox Quantum 引入了 Servo 引擎，该引擎由 Rust 开发。在之前的版本，Firefox 使用的引擎叫做 Gecko，由 C++ 开发。 Rust 作为一门新的编程语言，它一方面可以达到与 C/C++ 相同的性能，另一方面又避免 C/C++ 在内存和多线程方面的弊端。

第三，Firefox 使用自带 CA 证书，而 Chrome 使用了操作系统提供的 CA 证书。这样有什么好处呢？自带的 CA 证书不会受到操作系统及其厂商的影响。

第四，Firefox 定制化比 Chrome 强，读者可以按照自己的需求定制强大无比的浏览器。

讲解完 Firefox 的优势，接下来为大家说说使用 Firefox 都有哪些技巧。

第一，将默认引擎设置为 [DuckDuckGo](https://duckduckgo.com)，原因不再赘述。

第二，将 Firefox 打造为隐私浏览器，设置方法如下：

* Firefox 打开 about:preferences#privacy，Content Blocking 选择 Custom，然后勾选「Trackers」，接着选择「In all windows」；
* Send websites a “Do Not Track” signal that you don’t want to be tracked 选择 「Always」；
* Cookies and Site Data，如果是永久的隐身模式，「Delete cookies and site data when Firefox is closed」会默认勾选；
* History 勾选「Never remember history」；
* Permissions 勾选：「Block websites from automatically playing sound」、「Block pop-up windows」、「Warn you when websites try to install add-ons」、「Prevent accessibility services from accessing your browser」；
* Firefox Data Collection and Use，所有的选型都不勾选，不允许采集数据；
* Security，Deceptive Content and Dangerous Software Protection 都勾选；
* Certificates，When a server requests your personal certificate 选择「Ask you every time」，「Query OCSP responder servers to confirm the current validity of certificates」勾选。

做完上述的设置，让 Firefox 浏览器成为真正的无痕浏览。

第三，安装必备插件，如 DuckDuckGo Privacy Essentials、Mailvelope、Greasemonkey、NoScript、HTTPS Everywhere，安装完之后，大部分的插件，Run in Private Windows 需要勾选「Allow」。其他插件感兴趣可以 DuckDuckGo 搜索下。

第四，Firefox 有 Firefox Monitor 服务，可用来查询使用者的电子邮件地址是否曾经出现在外泄的数据库中。该服务由 Mozilla 和 HaveIBeenPwned.com 合作推出，当用户在 [https://monitor.firefox.com](https://monitor.firefox.com) 网站上输入自己的邮箱地址时，系统就会比对由 Have I Been Pwned 所搜集的外泄资料库（包含来自中国网站和服务泄漏的数据），倘若查询到符合的资料，会具体显示该电子邮件曾出现在哪些外泄事件中，用户则应该立即去修改密码。此外，从 Firefox 70 开始，如果浏览器保存的登录信息出现在已暴露数据库内 Mozilla 将会发出警告。

注重隐私未来是大势所趋，Firefox 浏览器将成为这部分用户的首选。Chrome 和 Google 的服务绑定太深，抛开 Chrome，选择还有很多。

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
* [隐私极客之 Apple 剪贴板](https://dbarobin.com/2020/07/10/apple-clipboard/)
* [隐私极客之 Wi-Fi](https://dbarobin.com/2020/07/15/wifi/)
* [隐私极客之 Namecheap](https://dbarobin.com/2020/07/23/namecheap/)

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>