---
published: true
author: Robin Wen
layout: post
title: 推荐 EasyOCR
category: 工具
summary: "最近笔者有识别图片内容的需求，需要用到 OCR。这类工具倒是挺多的，App、网页端、浏览器插件，什么都有。不过笔者出于隐私的考虑，不准备用线上的 OCR 工具，因为这类工具都需要将图片上传到服务器识别，然后再返回结果到客户端。如果有开源离线的命令行工具就更好了，于是笔者去 GitHub 搜索了下，发现一个叫做 EasyOCR 的工具，还不错，于是体验了下，基本满足笔者的需求。以后再找 OCR 工具，又想离线使用，EasyOCR 或许是你不错的选择。"
tags:
  - 工具
  - 利器
  - OCR
  - EasyOCR
---

`文/Robin`

***

![](https://cdn.dbarobin.com/ek7zx1g.png)

最近笔者有识别图片内容的需求，需要用到 OCR。这类工具倒是挺多的，App、网页端、浏览器插件，什么都有。不过笔者出于隐私的考虑，不准备用线上的 OCR 工具，因为这类工具都需要将图片上传到服务器识别，然后再返回结果到客户端。

如果有开源离线的命令行工具就更好了，于是笔者去 GitHub 搜索了下，发现一个叫做 EasyOCR 的工具，还不错，于是体验了下，基本满足笔者的需求。

EasyOCR 的 GitHub 地址为 [github.com/JaidedAI/EasyOCR](https://github.com/JaidedAI/EasyOCR)，目前获得了接近 1.2 万的 Star，1400 的 Fork，合并了 128 个 PR，共有 340 个 issue（包括 Open 状态的），68 个 Contributions。从这些数据来看，作为一个开源项目还是很不错的。

EasyOCR 支持 80 多种语言，还提供商用版，Subscription Plan 199 美金一个月。商用版还提供 RESTful API，方便程序集成。此外，还支持定制企业版。

EasyOCR 背后的团队叫做 Jaided AI，这里把他们团队的基本情况给大家介绍下。

> Jaided AI was founded in 2020 by Rakpong Kittinaradorn. Our goal is to distribute the benefits of AI to the world. The first project is an open source OCR library called EasyOCR. We build software with the philosophy that it has to be very easy to use while providing state-of-the-art performance. This is to maximize AI accessiblity for everyone in preparation to the upcoming AI revolution.

EasyOCR 由 Python 实现，使用也非常简单，跑以下两条命令即可开箱即用。EasyOCR 识别的结果，可以说相当准确，至少比那些网页版的 OCR 强太多。

``` bash
$ pip3 install easyocr
$ easyocr -l ch_sim en -f chinese.jpg --detail=1 --gpu=True
```

以后再找 OCR 工具，又想离线使用，EasyOCR 或许是你不错的选择。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)。如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

* 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
* 邀请码: **11190872**

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