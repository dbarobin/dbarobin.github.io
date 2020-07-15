---
published: true
author: Robin Wen
layout: post
title: Mixin Webhook 第一篇，零代码实现接收 GitHub Release 推送
category: 区块链
summary: Webhook 的应用相当广泛，比如 GitHub 的 Webhook 实现自动化构建，Slack 的 Webhook 实现各种通知，企业微信使用 Webhook 实现群机器人等。可以简单理解 Webhook 就是「用户定义的 HTTP 回调」。本文向读者介绍了什么是 Webhook，然后介绍了 Mixin Webhook，接着向大家介绍了如何利用 Mixin Webhook 实现零代码接收 GitHub Release 推送。Webhook 的使用相当灵活，读者感兴趣也可以基于 Webhook 实现自己想要的功能。
tags:
  - 区块链
  - Blockchain
  - Mixin
  - Mixin Network
  - Webhook
  - GitHub
---

`文/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 **70** 篇文章。

## 一 前言
***

关于 Webhook，维基百科是这样介绍的：网页开发中的网络钩子（Webhook）是一种通过自定义回调函数来增加或更改网页表现的方法。这些回调可被可能与原始网站或应用相关的第三方用户及开发者保存、修改与管理。术语「网络钩子」由杰夫・林德塞（Jeff Lindsay）于 2007 年通过给计算机编程术语「钩子」（Hook）加上前缀得来。[1]

Webhook 的应用相当广泛，比如 GitHub 的 Webhook 实现自动化构建，Slack 的 Webhook 实现各种通知，企业微信使用 Webhook 实现群机器人等。可以简单理解 Webhook 就是「用户定义的 HTTP 回调」。

![](https://cdn.dbarobin.com/webhook/webhook-rbxx26k.jpg)

> 题图来自：© Ant Media Server / MEDIA SERVER / flipboard.com

## 二 Mixin Webhook
***

Mixin Messenger 是一款优秀的基于 Signal 协议的端对端加密即时通讯软件。Exin 团队基于 Mixin API 实现了 Webhook，可以给自己以及管理的小群发送消息，目前支持 PLAIN_TEXT、PLAIN_CONTACT、APP_BUTTON_GROUP、APP_CARD 几种消息类型。具体的使用示例代码可以查看 [https://github.com/ExinOne/webhook-samples](https://github.com/ExinOne/webhook-samples)，目前提供了 Go、PHP、Python 和 Shell 4 种语言的实现。

本文是 Mixin Webhook 的第一篇文章，之后笔者还会为大家介绍 Webhook 的更多玩法。

## 三 实践
***

好了，到此读者应该了解 Webhook 有什么用了，笔者在这里给大家展示一个 Webhook 的使用场景，监控 GitHub 的 Release，这样开发者发布新版就可以及时地知道了。

本文介绍的是零代码实现接收 GitHub Release 推送，所以我们需要借助 IFTTT 来监控 GitHub Release 的 RSS 更新，然后再调用 Webhook，将消息发送给自己。

OK，我们现在开始。

第一步，点击 [https://ifttt.com](https://ifttt.com) 注册 IFTTT 账号。

第二步，注册 Mixin Messenger，下载链接：[https://mixin.one/messenger](https://mixin.one/messenger)。如果您已经有的话，打开即可。

第三步，搜索 Webhook，机器人 ID：**7000000012**，点击底部机器人按钮授权，授权只需要提供头像和昵称。

第四步，授权后打开 Webhook，会看到如下的页面，如图一：

![](https://cdn.dbarobin.com/webhook/webhook-01-n1rl2fu.png)

图一：Webhook 授权成功页面

如果您直接打开 Webhook，这个 Access Token 可以发给你自己；如果您将 Webhook 拉到一个 Mixin 小群，消息将发送给群里的成员。

第五步，登录 IFTTT，点击右侧顶部头像，然后点击「Create」，如图二：

![](https://cdn.dbarobin.com/webhook/webhook-02-3xq9vst.png)

图二：Create 页面

第六步，Step 1 of 6，Service 选择 RSS，如图三：

![](https://cdn.dbarobin.com/webhook/webhook-03-m36mnf6.png)

图三：Step 1 of 6，选择 Service 页面

第七步，Step 2 of 6，触发器选择「New feed item」，如图四：

![](https://cdn.dbarobin.com/webhook/webhook-04-9x2j6vf.png)

图四：Step 2 of 6，选择 Trigger 页面

第八步，Step 2 of 6，Feed URL 填写对应 GitHub 仓库的 RSS 链接。比如我想监控 Mixin Messenger 安卓版的发布，原始的仓库链接是 [https://github.com/MixinNetwork/android-app](https://github.com/MixinNetwork/android-app)，那么对应的 RSS 链接就是 [https://github.com/MixinNetwork/android-app/releases.atom](https://github.com/MixinNetwork/android-app/releases.atom)，如图五：

![](https://cdn.dbarobin.com/webhook/webhook-05-2cm2mxs.png)

图五：Step 3 of 6，Service 选择 RSS 页面

第九步，If 填写好了，接下来看下 Then 部分，如图六：

![](https://cdn.dbarobin.com/webhook/webhook-06-5cmcfwu.png)

图六：If...Then... 页面

第十步，Step 3 of 6，Action Service 选择「Webhook」，如图七：

![](https://cdn.dbarobin.com/webhook/webhook-07-reifzuf.png)

图七：选择 Action Service 页面

第十一步，Step 4 of 6，Action 选择「Make a web request」，如图八：

![](https://cdn.dbarobin.com/webhook/webhook-08-8iayry0.png)

图八：选择 Action 页面

第十二步，Step 5 of 6，Action Fields 填写，URL 填写第四步获取到的 Webhook 链接，需要把完整的 Access Token 带上；Method 选择「POST」，Content Type 选择「application/json」，如图九：

![](https://cdn.dbarobin.com/webhook/webhook-09-vav6fh2.png)

图九：Action Fields 填写页面 1

最重要的 Body 部分，如下：

``` bash
{"category":"PLAIN_TEXT","data":"{{EntryAuthor}} released Mixin Messenger Android {{EntryTitle}} at {{EntryPublished}}"}
```

其中的 ingredient 可以根据自己的需求定义，但是 category 和 data 的格式不能错，如图十：

![](https://cdn.dbarobin.com/webhook/webhook-10-rpvrk5z.png)

图十：Action Fields 填写页面 2

第十三步，Step 6 of 6，Review，名字命名为「Mixin Messenger Android Release」，点击「Finish」保存，如图十一。保存成功，如图十二：

![](https://cdn.dbarobin.com/webhook/webhook-11-dl72ihb.png)

图十一：Review 页面

![](https://cdn.dbarobin.com/webhook/webhook-12-dwkelpd.png)

图十二：保存成功提示页面

待 Mixin Messenger Android 发布新版，您就可以在 Mixin Messenger 接收到推送消息了。

## 四 小结
***

本文向读者介绍了什么是 Webhook，然后介绍了 Mixin Webhook，接着向大家介绍了如何利用 Mixin Webhook 实现零代码接收 GitHub Release 推送。Webhook 的使用相当灵活，读者感兴趣也可以基于 Webhook 实现自己想要的功能。

**参考资料**

> [1] 维基百科 (2019-07-05). 网络钩子. Retrieved from [https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E9%92%A9%E5%AD%90](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E9%92%A9%E5%AD%90).

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

![区块链技术指北](https://cdn.dbarobin.com/3YzonTR.png)

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
