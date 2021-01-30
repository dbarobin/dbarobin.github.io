---
published: true
author: Robin Wen
layout: post
title: "网站优化日志（二）"
category: 前端
summary: "在第一篇网站优化日志中，写到了一些优化策略。本篇文章从主题格式、增加功能方面讲解下网站的优化。第一，博客主题由两列显示改为一列显示，右侧导航栏完全取消，包括关于、Google 搜索、分类、Blogroll。第二，改变宽度。第三，去除无用页面，包括赞赏页、分类页。第四，在页脚添加邮箱、GitHub、Twitter、Instagram 和 RSS 图标。第五，顶部导航栏添加 Blogroll。第六，在文章和关于页面添加赞赏功能。第七，关于页面添加 PGP 和 公钥。最后。经过这么一番折腾，下面就是成果。生命在于折腾，不会前端的我，通过建站以来的折腾，对前端多少有些了解了。目前美中不足的是，之前提供的 Google 搜索也下掉了，博客暂时没有搜索功能，留个坑吧。"
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

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

在第一篇网站优化日志中，写到了一些优化策略。本篇文章从主题格式、增加功能方面讲解下网站的优化。

**第一，博客主题由两列显示改为一列显示**，右侧导航栏完全取消，包括关于、Google 搜索、分类、Blogroll。

之前博客采用两列展示，左列是文章，右列是关于等页面。就像 Medium 所提倡的，内容服务应该给阅读一种沉浸式的体验，显然右侧的页面给读者太多干扰。在 `_config.yml` 中，将 default_column 参数改为 one 即可实现。

**第二，改变宽度。**

将博客展示为一列之后，顿时清爽了。不过糟糕的是，文章占屏幕的比例太高，不美观，于是将博客主题的宽度更改了。具体方法是修改 `css/main.scss` 文件，将以下参数修改为满意的比例。

``` scss
// Width of the content area
$content-width:    810px;
$page-width:       850px;

$on-palm:          550px;
$on-laptop:        750px;
```

**第三，去除无用页面，包括赞赏页、分类页。**

写文章对于笔者而言，能够得到读者的认同、能够给读者启发，这已是最大的恩惠。倘若得到读者的赞赏，对于笔者而言是最大的激励，那一瞬间心里比喝了蜜还要甜。为了鸣谢那些曾经给我赞赏的读者，我做了一个赞赏页，贴了微信赞赏二维码，列出曾经给我赞赏读者的微信号（当然微信号是加密处理的）。因为此赞赏页面不够美观，于是把它去掉了，当然，不展示不代表此文件不更新。

分类和标签，本质上是同一个东西，都是为了将事物分类，便于后期查找。之前分类和标签在同一页面重复 3 次，显得太过臃肿，于是把导航栏的分类页去掉了。

**第四，在页脚添加邮箱、GitHub、Twitter、Instagram 和 RSS 图标。**

如你所见，之前右侧的展示页给出了我的邮箱、GitHub 账号图标、Twitter 账号图标。现在做了一个调整，在页脚添加邮箱、GitHub、Twitter、Instagram 和 RSS 图标。做完调整之后，还是挺酷的。

**第五，顶部导航栏添加 Blogroll。**

之前右侧的展示页还有 Blogroll，这部分功能还是挺有意思的，于是保留了，将 Blogroll 移到了顶部导航栏。

**第六，在文章和 [关于](https://dbarobin.com/about) 页面添加赞赏功能。**

按照 [在博客中添加打赏功能](http://lilian.info/blog/2016/12/AddPayFunction.html) 这篇文章添加了赞赏功能，花了很大功夫把 css 调通了，结果提交之后，瞬间 Chrome 将我的网站标记为不安全。于是找原因，原来是新增的赞赏功能使用了 Flash 和 Popups，这对于 HTTPS 的网站来说默认是不允许的，于是把此功能去掉了。

接着继续寻找替代品，终于在 GitHub 找到一个不错的。根据 [Donate-Page](https://github.com/Kaiyuan/donate-page) 提供的代码，将 Paypal 链接、微信、支付宝二维码替换成自己的，然后把默认的 BitCoin 账号去掉了（因为暂时还没有）。目前在文章和关于页面都可以看到这个赞赏页面，样式还是挺美观的。

**第七，[关于](https://dbarobin.com/about) 页面添加 PGP 和 公钥。**

PGP 可以进行安全验证，因为 Gmail 也不安全，发私密邮件，可以使用 PGP 加密。据权威安全人士推荐，Gmail 之外，邮箱可以使用 ProtonMail。

最后。经过这么一番折腾，下面就是成果。生命在于折腾，不会前端的我，通过建站以来的折腾，对前端多少有些了解了。目前美中不足的是，之前提供的 Google 搜索也下掉了，博客暂时没有搜索功能，留个坑吧。

![Screenshots of dbarobin.com at 2017.02.26](https://dbarobin.com/images/dbarobin.com.screenshots.170226.png)

Enjoy!

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
