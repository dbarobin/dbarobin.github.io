---
published: true
author: Robin Wen
layout: post
title: "网站优化日记（一）"
category: 前端
summary: "本站 2015 年初建立，当时访问速度很慢。电脑上花很长时间才能打开，手机上基本上打不开。当时我就在想，一个静态博客不至于那么慢吧。我的网站就是一些博文，文字居多，图片也很少，怎么加载速度那么慢呢？从用户体验来看，一个网站在 5 秒以内打开都可以忍受，超过这个时间体验降一个档次，时间越长，体验越遭。如果加载速度超过 20 秒，用户会残忍地关掉。所以，这是个问题。"
tags:
- 前端
- 建站
- 博客
- 网站优化
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

* Table of Contents
{:toc}

## 一 写在前面 ##
***

本站 2015 年初建立，当时访问速度很慢。电脑上花很长时间才能打开，手机上基本上打不开。当时我就在想，一个静态博客不至于那么慢吧。我的网站就是一些博文，文字居多，图片也很少，怎么加载速度那么慢呢？从用户体验来看，一个网站在 5 秒以内打开都可以忍受，超过这个时间体验降一个档次，时间越长，体验越遭。如果加载速度超过 20 秒，用户会残忍地关掉。所以，这是个问题。

## 二 优化策略 ##
***

开班，有些空余时间，便琢磨怎么入手优化网站。分析了一些网站后，还是找到一些共同点。最初使用 DNSPod 解析域名，并且使用安全宝进行加速。但我的网站没有备案，加速宝只给我分配了海外节点。我曾经怀疑过节点在海外是导致网站加载速度很慢的原因，但我 Ping 域名，基本上能保持在 100 ms 左右。我试着把加速宝关闭，但网站的加载速度仍然很慢。加速宝已经帮我做得很好了，只有从网站自身找原因。我使用 Chrome，打开开发者工具，发现加载速度慢的罪魁祸首，原来是 Google Analytics 的 JavaScript 脚本，自此心里骂道，万恶的 GFW，还让不让人玩了。

经过一番折腾，找到如下的优化策略：

* 暂时关闭加速宝，着手优化网站；
* 忍痛割爱，去除 Google Analytics，使用国内产品替代；
* 使用 Dareboost 进行网站质量和性能分析；
* 根据 Dareboost 报告逐次优化；
* 使用百度云加速。

## 三 优化细节 ##
***

### 3.1 去除 Google Analytics，使用国内产品替代 ###

**人在笼中，怎能自由。你若阻碍，我必逃脱。**Google Analytics 我是非常喜欢的，但现实往往是很无奈，你喜欢的东西往往不能如愿。我有 VPN，但绝大多数的网民都不知道如何科学上网。所以，只好采用国内产品替代。对比过百度统计、CNZZ、51la、量子统计等产品，最后选择了百度统计。使用百度统计功能也很简单，只需要在网页的 Head 中添加一段 JavaScript 即可。另外，推荐使用`异步加载`方式。试用了一段时间，感觉百度云统计的数据维度还是挺完善的，将就用吧。

以下是 GitHub 中的 Commits：

* <a href="https://github.com/dbarobin/dbarobin.github.io/commit/aa260fb6bda1bfc4c711ff4797e29ac4b00938b1" target="_blank">[Enhance] Remove Google Analytics Support temporarily</a>
* <a href="https://github.com/dbarobin/dbarobin.github.io/commit/ae8f08004d41b27813e8ff658140b8008220361d" target="_blank">[Enhance] Add Baidu Analytics Support</a>

### 3.2 使用 Dareboost 进行网站质量和性能分析 ###

<a href="https://www.dareboost.com" target="_blank">**Dareboost** </a>是国外一个对网站进行在线评测的平台，很赞。使用此网站也很简单，英语基本过关就可以胜任。点击网站右上角的 **New analysis**，输入域名，选择浏览器和测试地址，点击 **Analyze my page** 即可。

### 3.3 根据 Dareboost 报告逐次优化 ###

下面，我根据 Dareboost 生成的报告，逐次优化。

#### 3.3.1 Optimize images ####

根据 Dareboost 的报告，我有两张图片没有做优化，一张来自本站，另一张来自第三方。我测试过这两张图片的加载速度，都挺快，于是这一条忽略，不做优化。如果读者感兴趣，可以参考以下链接：

* <a href="https://developers.google.com/speed/docs/insights/OptimizeImages" target="_blank">Optimize Images</a>
* <a href="http://jpegclub.org/" target="_blank">jpegtran</a>
* <a href="http://optipng.sourceforge.net/" target="_blank">OptiPNG</a>

#### 3.3.2 Specify a 'Vary: Accept-Encoding' header ####

`Vary: Accept-Encoding header` 允许缓存代理资源的两种版本：一种压缩的，一种未压缩的。因此，不能正确解压这些文件的客户端可以通过代理访问您的页面，却使用的是未压缩的版本。然而，另外的用户将会得到已压缩的版本。因此，有必要设置此 header。可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/0a9dfd40b8164c2705f56c86fe4126ca56b7909d" target="_blank">[Bug] Fix bug by add htaccess file</a>（此 Commit 包含一个 Typo，后面的 Commits 中已修复）。

#### 3.3.3 Set a lang for your page ####

为了网站的可读性，应该为网站设置 lang 属性。修复这个也很简单，只需要在 head.html 中添加该属性即可。可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/d19eb85363c92a6a0fe12227d27e9fbc6dfffb62" target="_blank">[Optimize] Set a lang for my page</a>。

#### 3.3.4 Defer parsing of JavaScript ####

这个提示指出了 JavaScript 的解析方式，提供了如下三种解析方法：

* 在 onload 事件中添加 JavaScript 脚本到 DOM 中；
* 使用 <a href="https://developer.mozilla.org/en/docs/Web/HTML/Element/script#attr-defer" target="_blank">defer</a> 属性；
* 使用 <a href="https://developer.mozilla.org/en/docs/Web/HTML/Element/script#attr-async" target="_blank">async</a> 属性。

我使用了第二种方法，可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/9d0c3cdb3cc481cd6c8cd2204f129cf938ec2c51" target="_blank">[Optimize] Optimize the import way of JavaScripts</a>。

#### 3.3.5 Set a far future cache policy in 2 requests ####

为网站的静态资源设定 Cache 缓存可以减少服务器的负载。通常，我们使用 `expires header` 来解决这个问题。示例如下：

> Expires: Thu, 25 Dec 2014 20:00:00 GMT

可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/132e869535271ac120a99ba65793ca5207d110f3" target="_blank">[Optimize] Set a far future cache policy in 2 requests</a>。

#### 3.3.6 The Content Security Policy is missing ####

这个提示说明我的网站缺少 **CSP**（Content Security Policy，内容安全策略）。提到 CSP，就要提 XSS 攻击。什么是 XSS 攻击呢？也就是攻击者利用跨站式脚本在你的网站上注入东西。为了预防 XSS 攻击，我们可以通过设置 `Content-Security-Policy (CSP) HTTP header`。但是，这个设定只是相对安全的，对于真正的黑客而言只不过是烟云罢了。我尝试设定了 CSP HTTP header，但网站很多内容都不能正确加载，于是在 CSP HTTP header 中保持空内容。我的网站放在 GitHub 上，GitHub 的安全防范足以让我放心。

#### 3.3.7 Add alt attribute on <img\> tags ####

图片的 alt 属性是 SEO 的重要标准之一。此外，alt 属性在以下集中情况下使用：

* 使用阅读器；
* 网站连接缓慢；
* src 属性中出现错误。

当然，你也可以设置 alt 属性为空，参考：<a href="http://www.w3.org/TR/WCAG-TECHS/H67.html" target="_blank">H67: Using null alt text and no title attribute on img elements for images that AT should ignore</a>。

可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/c1acf26fb98b8a9c513f95d2adead4bbd77b9b28" target="_blank">[Optimize] Add alt attribute on <img> tags</a>。

#### 3.3.8 Avoid CSS @import ####

在我的 CSS 中，使用了 `@import`。使用  CSS @import 允许添加外部的样式表。事实上，浏览器不能同时下载它们，这样就会导致渲染页面延时。通常情况下，最好使用 **link** 标签。可以参考此文章：<a href="http://www.stevesouders.com/blog/2009/04/09/dont-use-import/" target="_blank">don’t use @import</a>。

可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/cb4cde63d6c2c44d862868ddd49b77292d811fa1" target="_blank">[Optimize] Avoid CSS @import</a>。

#### 3.3.9 Avoid http-equiv <meta\> tags ####

通常情况下，HTTP 头比 `http-equiv meta` 标签更高效。可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/e9a6c2199df1fae368cb95a0f830adee0dc490ff" target="_blank">[Optimize] Avoid http-equiv <meta> tags</a>。

#### 3.3.10 Separate the CSS styles from the HTML tags ####

分离 HTML 标签和 CSS 指令可以提供代码的可读性和促进网站的解析。定义 CSS 样式可以采用如下三种方式：

* 在指定的 CSS 文件中定义样式；
* 定义 inline 样式（HTML 中的 <style\> 标签）；
* 在 HTML 标签中使用 style 属性定义样式。

可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/900fad0c45fbf1cd52e0f4a620b29b83997e27b9" target="_blank">[Optimize] Separate the CSS styles from the HTML tags</a>。

#### 3.3.12 Help the social networks to understand your content ####

让自己的页面融入社交网络对 SEO 有很大的帮助。我们可以使用 `Open Graph` 属性来优化。可以参考此文章：<a href="http://ogp.me/" target="_blank">The Open Graph protocol</a>。

我定义了如下属性：**og:title、og:type、og:url、og:image**。可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/7e10eedfc9f8f415c3677435454d7244221c7147" target="_blank">[Optimize] Help the social networks to understand your content</a>。

#### 3.3.13 No <noscript\> tag is detected ####

根据优化提示，如果使用了 script 标签，那至少应该有一个 `noscript` 标签。这个优化也很简单，可以参考我的 GitHub Commits：<a href="https://github.com/dbarobin/dbarobin.github.io/commit/0c4909c127bf5b0b1155400e3021615dc8fc1c3b" target="_blank">[Optimize] No <noscript\> tag is detected</a>。

### 3.4 使用百度云加速 ###

之前网站使用的是安全宝加速，但由于网站未备案，只能分配海外节点，并且免费用户很多功能无法使用。于是，就放弃使用安全宝。直到有一天，偶然看到篇文章，讲的是采用百度云加速来加速网站，于是自己也折腾弄了下。yunjiasu.baidu.com 和 next.su.baidu.com 两者都是百度云加速，网站未备案均可以使用，但区别在于，前者付费才能体验到更多功能，后者是尝鲜版，不须付费也可以使用额外的功能，读者根据自己的需要选择吧。加速方式有两种，一是 CNAME，二是 NS。我的网站采用 DNSPod 解析，而且不想抛弃 DNSPod，所以采用了 CNAME 方式。至于怎么配置，可以参考此文：<a href="http://next.su.baidu.com/help/#NS修改教程/id/545c957f88c12f7ebefd4d14" target="_blank">DNSPod CNAME接入云加速</a>。添加网站完成后，稍等片刻，就可以看到效果了。在配置选项中，有加速缓存、安全防护、SEO 相关和其他四个模块，读者可以需要自行配置，在此不赘述。

读者可以感受下加速后的效果。

![ping-dbarobin.com](https://cdn.dbarobin.com/JtYdyjB.png)

## 四 写在后面 ##
***

这次加速可以说是收获很大，每遇到知识盲点，便不停的 Google。经过这段时间的折腾，网站访问速度有了大幅的提升，赞一个先。以后会给博客添加其他功能，那是后话。不满足现状，力争让自己的博客做到精益求精。**生命在于折腾，不折腾永远不知道自己能做成什么样。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 五 拓展阅读 ##
***

* <a href="http://www.zhihu.com/question/20663045" target="_self">爱范儿的网站是如何实现秒开的？</a>
* <a href="http://yulijia.net/cn/%E7%BD%91%E7%BB%9C%E8%A7%82%E5%AF%9F/2014/12/28/baidu-cdn.html" target="_self_">采用百度云加速免费来加速站点</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
