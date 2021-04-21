---
published: true
author: Robin Wen
layout: post
title: macOS Big Sur
category: 工具
summary: "macOS Big Sur 正式版发布了。不过笔者在之前内测的时候就开始用了，经历 2 个 Candidate 的 Beta 版，直接就到正版版了，所以此次发布，内心毫无波澜。笔者的 Time Machine 是每天备份的，在内测期间出现较多的系统容与，于是干脆把备份盘格式化成 APFS 重新备份。根据笔者的实际测试，Time Machine 的硬盘格式，没法再选择 Mac OS Extended (Journaled, Encrypted)，而且格式化为 APFS (Encrypted)，执行备份时会自动变成 APFS (sensitive, Encrypted)。至于背后的原因，暂时不得而知。将 Time Machine 硬盘格式变更为 APFS 之后，备份的速度确实变快了很多。读者感兴趣可以看下这篇文章的测试。这个功能，Apple 没有在 Apple’s overview of Big Sur’s features 以及 WWDC 提及。以上就是笔者更新 macOS Big Sur 的体验，以及重点介绍了 APFS 格式的 Time Machine 备份，希望能帮助到大家。"
tags:
  - 工具
  - Apple
  - macOS
  - APFS
---

`文/Robin`

***

![](https://cdn.dbarobin.com/4v86in7.png)

macOS Big Sur 正式版发布了。不过笔者在之前内测的时候就开始用了，经历 2 个 Candidate 的 Beta 版，直接就到正版版了，所以此次发布，内心毫无波澜。

macOS Big Sur 除了 UI，似乎没有多大改变。升级到 macOS Big Sur 后，更像 iPadOS 了。作为近几年以来变化最大的 macOS 更新，macOS Big Sur 对内置应用的图标、应用的设计语言、桌面的程序坞和菜单栏等方面都进行了重新设计。macOS Big Sur 中的系统应用图标统一改为圆角矩形的设计样式，与 iOS /iPadOS 的图标样式统一。其它系统界面方面，macOS Big Sur 的程序坞改为了半透明样式，菜单栏样式也进行了调整，菜单栏图标之间的距离变得更大。交互方面，部分系统应用也与 iPadOS 进行了统一。

macOS Big Sur 引入了我们在 iOS 和 iPadOS 中熟悉的控制中心，Wi-Fi、蓝牙、音量、显示器亮度等都可以在这里进行调节，我们也能对加入家庭应用的设备进行控制。控制中心的各项功能还支持通过鼠标拖拽的方式固定在菜单栏，以便我们更快速地使用常用功能。

Safari 在 macOS Big Sur 中支持了更多自定义功能，对于扩展的兼容性也更为强大，开发者能够以更低的成本为 Safari 适配浏览器插件，当然，对于隐私方面应用也有多项新的安全措施以保障我们的数据安全。

除了界面上的更新，挺多软件的兼容性出现问题，比如 Bartender、Little Snitch 等，不过这些都可以通过升级新版本解决，问题不大。macOS 的软件就是这样的特性，通常每年一大版本，挺多软件都要升级。

此外，值得一提的是，macOS Big Sur 现在可以将 Time Machine 备份到 APFS 磁盘。在 macOS Catalina 和以前版本的操作系统中，Time Machine 只与 HFS+ 磁盘兼容，苹果在一篇支持文章中解释了这一点。

> 【重要事项】您可以将 HFS+ 或 APFS 格式磁盘的内容备份到 HFS+ 磁盘，但时间机器不能备份到 APFS 格式的磁盘。如果选择了 APFS 格式的备份磁盘，时间机器可将其重新格式化为 HFS+。如果磁盘使用主引导记录 (MBR) 分区类型，一些分区在时间机器中可能无法使用。如果备份磁盘位于网络上，网络服务器可以使用服务器信息块 (SMB) 文件共享。设置时间机器时，您的 Mac 必须接入 SMB 服务器。在时间机器偏好设置中选择网络磁盘后，时间机器会在需要备份或恢复数据时自动连接到该磁盘。

笔者的 Time Machine 是每天备份的，在内测期间出现较多的系统冗余，于是干脆把备份盘格式化成 APFS 重新备份。根据笔者的实际测试，Time Machine 的硬盘格式，没法再选择 Mac OS Extended (Journaled, Encrypted)，而且格式化为 APFS (Encrypted)，执行备份时会自动变成 APFS (sensitive, Encrypted)。至于背后的原因，暂时不得而知。将 Time Machine 硬盘格式变更为 APFS 之后，备份的速度确实变快了很多。读者感兴趣可以看下这篇文章的 [测试](https://arstechnica.com/gadgets/2020/11/macos-11-0-big-sur-the-ars-technica-review/9/)。这个功能，Apple 没有在 [Apple’s overview of Big Sur’s features](https://www.apple.com/macos/big-sur/features/) 以及 WWDC 提及。

以上就是笔者更新 macOS Big Sur 的体验，以及重点介绍了 APFS 格式的 Time Machine 备份，希望能帮助到大家。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

***

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)