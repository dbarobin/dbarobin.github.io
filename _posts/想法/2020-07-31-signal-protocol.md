---
published: true
author: Robin Wen
layout: post
title: 细数 Signal Protocol IM
category: 想法
summary: 隐私重要吗？当然重要。然而认为隐私重要并且想办法尽力保护的，这个比例却太少了。IM 的意思是「即时通讯」，这是我们日常生活中最常用的软件类别，比如 Telegram、WX 都是属于这个分类。然而这些软件并不安全，Telegram 是为了体验牺牲安全，然而 WX 不能做得很安全，原因在此不多说。在 IM 领域，有一个独特的开源加密协议存在，这就是 Signal Protocol。本文简单介绍了 Signal Protocol，以及使用了这个协议的 IM。如果您非常注重隐私，那 Signal 是不错的选择；如果您非常注重隐私，同时是加密货币、比特币的持有者，那 Mixin Messenger 是您最佳的选择。
tags:
  - 想法
  - Signal
  - Mixin
  - Mixin Messenger
---

`文/Robin`

***

![](https://cdn.dbarobin.com/xavhs5u.png)

## 前言
***

隐私重要吗？当然重要。然而认为隐私重要并且想办法尽力保护的，这个比例却太少了。IM 的意思是「即时通讯」，这是我们日常生活中最常用的软件类别，比如 Telegram、WX 都是属于这个分类。然而这些软件并不安全，Telegram 是为了体验牺牲安全，然而 WX 不能做得很安全，原因在此不多说。

在 IM 领域，有一个独特的开源加密协议存在，这就是 **Signal Protocol**。本文笔者先给大家介绍 Signal Protocol，然后说说哪些 IM 都用了这个协议。

## Signal Protocol
***

首先我们来看看什么是 Signal Protocol。提到 Signal Protocal，不得不提及 Open Whisper Systems。Open Whisper Systems 是一个非营利软件开发团体，由 Moxie Marlinspike 在 2013 年创办。他们主要专注在研发 Signal 通信协议及维护相关的加密即时通讯软件 Signal。此团体依靠捐款及资助营运，他们的产品都是自由软件。

Signal Protocol 是真正的端到端的通讯加密协议，号称是世界上最安全的通讯协议，任何第三方包括服务器都无法查看通讯内容。端到端加密，End-to-end encryption，是一个只有参与通讯的用户可以读取信息的通信系统。总的来说，它可以防止潜在的窃听者（包括电信供应商、互联网服务供应商甚至是该通讯系统的提供者），获取能够用以解密通讯的密钥。这个功能的重要性对于日常使用 IM 的用户而言不言而喻。

接下来笔者向大家介绍哪些 IM 都使用了 Signal Protocol。

## Signal
***

**Signal** 是由 Signal Foundation 和 Signal Messenger LLC 开发的跨平台加密消息服务。Signal 是一款开源的 IM，不同的客户端版本均是开源的。GitHub 链接：[github.com/signalapp](https://github.com/signalapp)。

2020 年 6 月，非裔男子乔治・弗洛伊德遭暴力执法致死一事所引发的大规模抗议活动仍在美国各地持续，受美国示威抗议活动的推动，大量 Twitter 用户和专注电子隐私的组织（如 Electronic Frontier Foundation）呼吁美国示威者使用 Signal 等加密应用进行交流。此外，HK Guo An Fa 的实施，也让 HK 用户对 Signal 的需求攀升。

值得一提的是，多位名人为 Signal 站台，包括斯诺登、约翰・霍普金斯大学的密码学教授 Matt Green，知名安全专家 Bruce Schneier、Twitter 首席执行官 Jack Dorsey。此外，美国 FBI 也曾为 Signal 正名，FBI 曾试图传讯调查两个 Signal 用户，结果最后没有拿到任何重要信息，Signal 称最后保留的数据只有用户的注册日期和最近的登陆日期。

以上，Signal 是一款非常值得使用的 IM。

## WhatsApp
***

WhatsApp Messenger，简称 **WhatsApp**，是 Facebook 公司的旗下一款用于智能手机的跨平台加密即时通信应用程序，发布于 2009 年 11 月。2014 年 2 月 Facebook 耗资约 193 亿 美元收购 WhatsApp。WhatsApp 目前的用户数为 20 亿。

2014 年 11 月，Open Whisper Systems 宣布和 WhatsApp 合作将 Signal 协议引入到客户端中。但是 WhatsApp 是闭源的。

平时跟一些国外朋友交流，基本上都是用 WhatsApp。不过也有一些朋友对 WhatsApp 是 Facebook 旗下的产品，多少对 WhatsApp 的隐私问题担心，见仁见智吧。

## Facebook Messenger
***

**Facebook Messenger** 是一个提供文字和语音服务的即时通讯软件，于 2011 年 8 月 9 日发布。同样，Facebook Messenger 用户数也在 20 亿级别。

2016 年 10 月， Facebook Messenger 引入私密会话，使用 Signal Protocol 协议实现端对端加密。同样，Facebook Messenger 闭源。

由于 Facebook Messenger 和 WhatsApp 功能趋同，而且都是 Facebook 旗下产品，不少用户还是倾向于使用 WhatsApp。

## Skype
***

**Skype** 于 2003 年 8 月问世。该软件由瑞典人 Niklas Zennström，丹麦人 Janus Friis 以及开发软件后端的爱沙尼亚人 Ahti Heinla、Priit Kasesalu 和 Jaan Tallinn 合作创建。2005 年 9 月，eBay 以 26 亿美元的价格购得 Skype。2009 年 9 月，Silver Lake、Andreessen Horowitz 和 Canada Pension Plan Investment Board 宣布向 eBay 以 19 亿美元购入 Skype 的 65% 的股份。此次收购折合的 Skype 估价为 27.5 亿美元。2011 年 5 月，微软以 85 亿美元的价格收购 Skype。

2018 年 1 月，Open Whisper Systems 和微软宣布 Skype 私密会话引入 Signal Protocol 协议实现端对端加密。

Skype 引入端对端加密比较晚，在用户的心理建设上还不充分，所以提到端对端加密，Skype 的提及率比较低。同样，Skype 是闭源产品。

## Mixin Messenger
***

**Mixin Messenger** 是 Mixin 团队 2018 年发布的产品。Mixin Messenger 提供 iOS、Android、macOS、Windows、Linux 版本。Mixin Messenger 各个版本的客户端都是开源的，GitHub 地址：[github.com/MixinNetwork](https://github.com/MixinNetwork/)。同样，Mixin Messenger 采用 Signal Protocol 实现端对端加密，还开源了一个 Dart/Flutter 的 Signal Protocol 库。

Mixin Messenger 跟其他端对端加密的产品都不一样，因为这是一个对加密货币友好的跨链项目。Mixin 是最有创意也最有前途由国人主导的区块链创业项目，包括 Mixin Messenger、Mixin Network、Mixin API。然而由于偏见，大多数的人对这个项目却视而不见。

## 曾经采用但已停产
***

曾经采用 Signal Protocol 但已停产的项目也有一些，比如 G Data Software 推出的 G Data、Google 开发的 Allo。

## 小结
***

本文简单介绍了 Signal Protocol，以及使用了这个协议的 IM。如果您非常注重隐私，那 Signal 是不错的选择；如果您非常注重隐私，同时是加密货币、比特币的持有者，那 Mixin Messenger 是您最佳的选择。

最后，我的 Mixin ID **26930**，欢迎沟通。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)