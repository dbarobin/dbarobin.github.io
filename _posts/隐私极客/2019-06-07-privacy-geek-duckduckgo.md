---
published: true
author: Robin Wen
layout: post
title: 隐私极客之 DuckDuckGo
category: 隐私极客
summary: 谈到搜索引擎，不得不提到 Google（注意，某度不在讨论范围）。笔者每天使用 Google 的频率相当高，但 DuckDuckGo 依然是 Google 之外的有力补充。关于 DuckDuckGo 的故事，可以阅读 @virushuo 的文章，穿越寒冬的独行者。当然，更多的技巧需要读者去探索，DuckDuckGo 可以作为 Google 有力的补充或者替代。不过不幸的是，DuckDuckGo 像 Google 一样无法摆脱被墙的命运。DuckDuckGo 不过滤结果，不追踪你的搜索记录，把搜索引擎隐私发挥到了极致，帮助你在 Cyberspace 中自由流荡。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
---

`文/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

上一篇文章给大家介绍了 [Telegram](https://dbarobin.com/2019/05/14/privacy-geek-telegram) 以及值得注意的一些技巧。本文给大家讲讲搜索引擎 DuckDuckGo。

谈到搜索引擎，不得不提到 Google（注意，某度不在讨论范围）。笔者每天使用 Google 的频率相当高，但 DuckDuckGo 依然是 Google 之外的有力补充。关于 DuckDuckGo 的故事，可以阅读 [@virushuo](https://twitter.com/virushuo) 的文章，[穿越寒冬的独行者](https://jhuo.ca/post/ddg)。

![](https://cdn.dbarobin.com/v9mT70u.jpg)

> 题图来自: © DuckDuckGo / DuckDuckGo Privacy Browser / play.google.com

Google 每秒 4 万～8 万次搜索量，强大的计算能力和数据采集能力，可以产生巨大的商业价值。不幸的是，Google track you. DuckDuckGo 主推隐私功能，Yes, [Google tracks you. We don’t.](https://donttrack.us)。此外，读者还可以订阅 DuckDuckGo 的 [博客](https://spreadprivacy.com)。

本文想给读者介绍的，DuckDuckGo 的使用技巧（参考了 [少数派](https://sspai.com/post/40117) 的文章）。

第一，高级搜索技巧。DuckDuckGo 最基础的搜索功能与其他主流产品别无二致，对常见的 OR、-、site:、filetype: 等语法也有良好支持，搜索结果页亦支持通过语言、更新时间等参数进一步过滤结果，不会给从其他搜索引擎切换来的用户施加额外的学习成本。对于进阶用户，DuckDuckGo 也支持通过在 URL 上附加参数（parameter）的方式进行高级搜索。关于支持的参数，参考 [URL 参数](https://duckduckgo.com/params) 页面。

第二，隐私保护中的细节。首先，DuckDuckGo 既不会记录你的 User Agent（用户识别符，用于识别你所用的浏览器）、IP 地址、搜索关键词等被动传来的信息，也不会主动通过 Cookies（网页存储在本地的缓存）等机制识别你的身份。其次，DuckDuckGo 在转发你的访问时，会对请求进行处理，使得第三方网站无法知道你是从其搜索结果页跳转而来。再次，DuckDuckGo 还会自动修改一些主要站点索引的 URL，在你点击访问时指向它们的 HTTPS 地址，以进一步提高安全性。然后，它允许用户通过 POST 方法而非 GET 方法发送搜索请求。启用该选项后，从浏览器地址栏的 URL 中将无法看出你的搜索关键词，因而搜索历史也不会呈现在历史记录中，实现了类似于「隐身模式」的效果。最后，DuckDuckGo 提供 Tor 站点，域名 [3g2upl4pq6kufc4m.onion](https://3g2upl4pq6kufc4m.onion)。如果可以自由地访问 Tor 网络，DuckDuckGo 将是最佳的搜索引擎。

第三，DuckDuckGo 提供 Instant Answers 搜索，极大地减少用户的搜索时间。目前，Instant Answers 支持的各类功能已经有 [1200](https://duck.co/ia) 多个，而且还在不断增加。

第四，DuckDuckGo 有数个自定义配置，除了外观外，还有以下有用的设置：

* General > Safe Search：即其他搜索引擎中的结果过滤，默认为 Moderate（适中），可按需调为严格或关闭。
* General > Advertisements：尽管 DuckDuckGo 上的广告已经非常克制，你仍可在此将其全部关闭。
* General > New Window：开启后，在搜索结果页面点击链接会在新窗口中打开，如果你习惯一口气打开多个结果链接再慢慢筛选，该选项非常实用。
* Privacy > HTTPS：默认为开启。如果关闭，可能会访问到部分网站的非 HTTPS 版本。
* Privacy > GET requests：默认为开启。如果关闭，DuckDuckGo 将使用 HTTP 中的 POST 方法而非 GET 方法来发送你的搜索请求。

第五，设置的同步。如果想将自己的偏好设置在多个设备上使用，就需要同步机制的帮助。对此，几乎所有网站采取的都是账号机制。但多一个账号就多一分隐私外泄的风险，以保护隐私为己任的 DuckDuckGo 显然不会放过这个漏洞。在 DuckDuckGo 设置页面的 「Cloud Save」（云端保存）部分点击 「Save Settings」 按钮，网页会要求你输入一段密码，或者直接点击「Suggest a pass phrase」自动生成一个，然后点击「Save」保存。这样，你的偏好设置就被保存在了 DuckDuckGo 的服务器中；其他设备上，只要在同一页面上点击「Load Settings」并输入密码，即可载入设置，而新的更改也会在设备间同步。

第六，支持 Bangs 功能。Bang 的运行机制很简单，只要在你的搜索关键词之前（或结尾）加上一个以半角感叹号！开头的标识符（称为 bang），DuckDuckGo 就会替你调用这个 bang 所指向的搜索引擎或网站进行搜索。这个功能有意思的是，DuckDuckGo 里使用其他搜索引擎进行搜索，并且 Bangs 转发的搜索是完全匿名的。

第七，短域名 [dgg.gg](https://dgg.gg) 可以跳转到 DuckDuckGo 搜索引擎。

第八，可以直接搜索 Twitter 用户，比如输入「**@vrwio**」可以在 DuckDuckGo 展示笔者的 Profile。

第九，可以直接生成随机密码，关键词 password 或 random password。比如输入「**password 20**」或者「**random password 20**」，将会生成一个长度 20 的随机密码。在某些一次性注册的网站上，这个功能比较好使。

第十，支持为链接生成二维码，关键词 QR。比如输入「QR https://dbarobin.com」将会生成笔者博客链接的二维码。

十一，快速改变大小写，关键词 lowercase 和 uppercase。比如「**lowercase ROBIN**」将会把 ROBIN 改为小写，「**uppercase robin**」将会把 robin 改为大写。

十二，快速展示日历，关键词 calendar。比如输入「**calendar 20190607**」将会展示今天的日历。

十三，支持缩短 URL，关键词 shorten，比如输入「shorten https://dbarobin.com」将会获得短域名。

十四，快速查找 HTML 代码，关键词 html，比如输入「**html >**」将会获得大于符号的 HTML 代码。

最后，DuckDuckGo 配合 Firefox，可以打造最佳隐私搜索利器。下一篇文章将为大家讲讲 Firefox。

当然，更多的技巧需要读者去探索，DuckDuckGo 可以作为 Google 有力的补充或者替代。不过不幸的是，DuckDuckGo 像 Google 一样无法摆脱被墙的命运。

DuckDuckGo 不过滤结果，不追踪你的搜索记录，把搜索引擎隐私发挥到了极致，帮助你在 Cyberspace 中自由流荡。

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