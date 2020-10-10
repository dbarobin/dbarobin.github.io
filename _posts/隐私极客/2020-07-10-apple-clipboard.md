---
published: true
author: Robin Wen
layout: post
title: 隐私极客之 Apple 剪贴板
category: 隐私极客
summary: 剪切板是指操作系统提供的一个暂存数据，并且提供共享的一个模块。通常情况下，我们在搜索信息，输入网站的过程中常常会进行复制，而这些复制的信息则会暂存在剪切板里。每次打开应用程序时，许多流行的应用程序都会静默读取剪贴板上的所有内容。比如某信复制了某口令，某宝会自动读取，然后可以快捷地跳转到商品页；再比如某信复制了银行卡号，打开网银会自动弹出是否要去转账。这些大家觉得很平常的交互，实际上在吞噬着我们的隐私。iOS 设备的剪贴板一直是个历史遗留问题。笔者希望苹果能够重视剪贴板的权限，在 iOS 系统层面加入剪贴板权限控制，以此解决剪贴板滥用的难题。与此同时，也希望 App 开发商有所收敛，在 iOS 14 发布之前就做好相应的整改。隐私至上，不要觉得 App 读取剪贴板是个很正常的事情。随波逐流，这是不对的。
tags:
  - 互联网
  - 隐私极客
  - 安全
  - 隐私
  - Privacy
  - Apple
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 前言
***

![](https://cdn.dbarobin.com/ub8htcp.png)

剪切板是指操作系统提供的一个暂存数据，并且提供共享的一个模块。通常情况下，我们在搜索信息，输入网站的过程中常常会进行复制，而这些复制的信息则会暂存在剪切板里。每次打开应用程序时，许多流行的应用程序都会静默读取剪贴板上的所有内容。比如某信复制了某口令，某宝会自动读取，然后可以快捷地跳转到商品页；再比如某信复制了银行卡号，打开网银会自动弹出是否要去转账。这些大家觉得很平常的交互，实际上在吞噬着我们的隐私。

诚然自动读取剪贴板，对使用 App 有较大的便利，但是其中的隐私问题实在是可怕。

## iOS 14
***

Apple 生态的设备，联动性非常好，比如熟知的 Airdrop、通用剪贴板。iOS 14 Beta 版本，其中一项功能是每次有应用读取剪贴板内容它会警告用户。如你所愿，笔者升级到 iOS 14 Beta 后，国产的 App，80% 以上都会在打开的时候自动读取剪贴板，甚至是一些跟剪贴板八杆子打不着的 App 也会读取。相比之下，欧美的 App，自动读取剪贴板的情况就会少很多。

读取剪贴板有哪些隐患呢？其一，大多数 App 在打开应用的时候会自动读取，但不清楚常驻后台会不会读取；其二，不清楚读取之后是存储到本地，还是上传到云端；其三，如果上传到云端，不清楚是会对这些数据做怎样的应用；其四，如果剪贴板内的内容是银行卡、手机号、身份证号，甚至是一些加密货币私钥之类的，被 App 读取后完全不明白会拿来做什么。也就是说，这是个黑盒，黑盒的内部怎么运作，只有开发者最清楚。

今年 3 月，研究员 Talal Haj Bakry 和 Tommy Mysk 发现，数十个 iOS 应用存在获取用户隐私数据的严重问题，其中之一就是大受欢迎的短视频应用 TikTok。TikTok 回应称访问剪贴板是为了识别垃圾信息。它在一封声明中表示，它已经向苹果应用商店 ‌App Store‌ 递交了新版本，移除了反垃圾信息功能，以避免任何潜在的混淆。下载的新版本确认它不再访问剪贴板。放到国内，解决类似的问题会很难，因为大众也没有意识到这是个很严重的问题。

## 如何应对
***

在 App 自动读取剪贴板泛滥成灾的环境下，我们这些用户应该如何应对？

第一，尽可能地关闭通用剪贴板功能。Apple 设备的剪贴板内容会在关联设备之间共享，这项功能被称为通用剪贴板，允许一台设备复制的内容可以在另一台设备上使用。通用剪贴板确实很方便，但是对于同时使用 Mac 和 iPhone 设备的读者来说，隐患非常大。因为 Mac 作为工作学习的工具，在 Mac 端复制内容是个很平常的事情，如果开启了通用剪贴板，啥内容都能同步到 iPhone 或者 iPad 设备。如果一定要使用，切记在使用完成之后，将 Mac 的通用剪贴板功能关闭，如下图：

![](https://cdn.dbarobin.com/syllfzo.png)

第二，敏感信息，尽可能地输入而不是复制。对于敏感信息，尽可能的使用系统输入法输入，而不是图一时之便，复制粘贴。注意，是系统输入法，其他乱七八糟的输入法还是谨慎使用。

第三，如果 iOS 14 正式版发布了，第一时间升级，然后留意哪些 App 在打开的时候，会有读取剪贴板的提示。在无法阻止 App 读取剪贴板的情况下，至少能够做到心里有数。

第四，可以借助第三方工具清除剪贴板。iOS 端，Pin 这款第三方工具提供手动清除剪贴板的功能。此外，使用 Launch Center Pro、快捷指令，还可以做到在打开应用前，自动清理剪贴板。具体的技巧，读者感兴趣可以自行 Google。macOS 端，可以在终端输入 `pbcopy < /dev/null` 清理剪贴板，还可以结合 Alfred 做成快捷指令。

## 小结
***

iOS 设备的剪贴板一直是个历史遗留问题。笔者希望苹果能够重视剪贴板的权限，在 iOS 系统层面加入剪贴板权限控制，以此解决剪贴板滥用的难题。与此同时，也希望 App 开发商有所收敛，在 iOS 14 发布之前就做好相应的整改。

**隐私至上，不要觉得 App 读取剪贴板是个很正常的事情。随波逐流，这是不对的**。

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
* [隐私极客之 Apple 剪贴板](https://dbarobin.com/2020/07/10/apple-clipboard)
* [隐私极客之 Wi-Fi](https://dbarobin.com/2020/07/15/wifi/)
* [隐私极客之 Namecheap](https://dbarobin.com/2020/07/23/namecheap/)
* [隐私极客之 Tuber](https://dbarobin.com/2020/10/10/tuber/)

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>