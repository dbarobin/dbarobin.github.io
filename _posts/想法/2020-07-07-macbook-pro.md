---
published: true
author: Robin Wen
layout: post
title: 谈谈 MacBook Pro 的品控
category: 想法
summary: 笔者 6 月初回了趟老家，就在准备返回深圳的前两天，MacBook Pro 无法开机。在当地的 Apple 授权服务商进行了检测，初步反馈是电池异常。因维修时间过长，于是决定返回深圳后再做维修。MacBook Pro 是优秀的互联网从业者必备利器，但选择了却要付出高昂的维护成本。看完笔者的经历，相信你应该行动起来了，比如购买一块硬盘把 Time Machine 用起来，比如使用 Dropbox、Google Drive 之类的同步盘实时同步重要文件，比如使用 Mackup 备份配置文件等等。数据无价，不要因为自己的懒惰而将辛苦产出的劳动成果付之东流，因为 MacBook 损坏不是黑天鹅，而是灰犀牛。
tags:
  - 想法
  - 数据
  - Apple
  - MacBook
---

`文/Robin`

***

![](https://cdn.dbarobin.com/lmj7zle.png)

笔者 6 月初回了趟老家，就在准备返回深圳的前两天，MacBook Pro 无法开机。在当地的 Apple 授权服务商进行了检测，初步反馈是电池异常。因维修时间过长，于是决定返回深圳后再做维修。

回到深圳，笔者已经提前预约深圳益田假日广场的 Apple 零售店，发现最快也要一周后才有预约名额，于是决定去广州维修，毕竟广州有天环广场、珠江新城两家零售店，预约成功的概率会高很多。预约后，如约来到广州天环广场门店，官方的维修工程师检测结果反馈，主板损坏、电池鼓包，需要更换主板、电池，而且数据无法取出。

这里笔者交代下，笔者的 MacBook Pro 是 2018 款的，为什么这一款 Mac 在更换主板后，数据为什么无法取出呢？

> 自 2016 款全新 MacBook Pro 开始，苹果直接把 SSD 焊在主板（不带 Bar 版的 13 英寸入门版却可以更换），用户不再可以自行升级 SSD，但为了方便其售后天才吧（Genius Bar）的维修工作，苹果在主板上预留了一个接口，在机器无法工作时，苹果的天才们可使用一个名为 Customer Data Migration Tool 的维修工具接到这个接口，帮客户转移 SSD 上的数据到正常的 MacBook 笔记本上，所以用户即使电脑坏了也不用太担心自己的数据会丢失。只是到了 2018 款 MacBook Pro 上，情况就有所不同了，主板上已经被取消了这个备份数据用的接口。

笔者接受了数据无法取出的结果，不一会儿，官方维修工程师给出了维修报价，如图：

![](https://cdn.dbarobin.com/r9uxnmn.png)

如果没有购买 AppleCare 服务，拿到这份报价单估计要哭了，幸运的是，笔者有购买此服务，因此无需额外付费。最初工程师给笔者的反馈是需要 7～14 天才能维修好，实际上 3 天后就拿到了，维修之后的 MacBook Pro 就像新机一样，连键盘也焕然一新。回到家，打开 MacBook Pro，使用 Time Machine 恢复，数据如初，不过悲喜交加。

回到 MacBook Pro 数据问题，2018 款的型号更换主板无法找回数据，如果平时没有良好的备份习惯，估计又要哭了，毕竟数据无价。笔者很早之前写了一篇关于数据备份的文章，感兴趣可以找来看看。笔者重要的文件都是通过 Dropbox 同步，回老家之前有用 Time Machine 备份过，所以此次维修在数据上的损失，无非是对系统的一些设置以及以及一些软件缓存数据丢失，无关紧要。

笔者的 MacBook Pro 购买于 2019 年 3 年，至今也才一年多一点的时间，没有进水、没有摔过，却要来一次尺度很大的维修，可见 Apple 现在的品控实在是太差。相比笔者的备用 MacBook，购买于 2015 年，没有维修记录，从内到外一切正常，至今依然服役。笔者认为 Apple 把 MacBook Pro 越做越薄，必定带来的是散热问题。此外，TouchBar 很鸡肋，蝶式键盘也很垃圾，还有为了卖更多的配件而改造的 Type-C 接口。2015 款的 MacBook Pro，笔者至今认为非常优秀，还准备收一台 2015 款的高配机作为备用机。

MacBook Pro 是优秀的互联网从业者必备利器，但选择了却要付出高昂的维护成本。看完笔者的经历，相信你应该行动起来了，比如购买一块硬盘把 Time Machine 用起来，比如使用 Dropbox、Google Drive 之类的同步盘实时同步重要文件，比如使用 Mackup 备份配置文件等等。

**数据无价，不要因为自己的懒惰而将辛苦产出的劳动成果付之东流，因为 MacBook 损坏不是黑天鹅，而是灰犀牛。**

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

* 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
* 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)