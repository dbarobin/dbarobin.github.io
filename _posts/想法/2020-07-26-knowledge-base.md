---
published: true
author: Robin Wen
layout: post
title: 构建知识库小谈
category: 想法
summary: 知识库又叫做 Knowledge Base，简称 KB，这个缩写在微软系列产品中最为常见。本文想聊聊笔者构建 Knowledge Base 都用到那些工具，以及关于知识库的一些思考。本文讲解了笔者选择文档库工具的演变过程。目前笔者的知识库软件组合：印象笔记 + Day One + Dropbox Paper。印象笔记用于文档的存储，Day One 记录零碎，Dropbox Paper 作为主力，主要是记录一些原创文档，以及存放需要经常修改的文档。今年尤为火爆的 All in One 方案 Notion，笔者在体验过程中觉得不适合，就暂时没用。看完笔者关于知识库的文章，您有什么想法，欢迎留言。
tags:
  - 想法
  - 知识库
---

`文/Robin`

***

![](https://cdn.dbarobin.com/87u915a.png)

知识库又叫做 Knowledge Base，简称 KB，这个缩写在微软系列产品中最为常见。本文想聊聊笔者构建 Knowledge Base 都用到哪些工具，以及关于知识库的一些思考。

## 知识库
***

构建知识库对于个人而言是一件非常重要但是经常被忽略的事情。一方面，有体系的知识库方便搜索，节省时间；另一方面，知识库记录的是个人的学习、思考、成长，是一种非常有价值的沉淀。笔者对知识库软件的要求如下：

* 支持 Markdown；
* 中文搜索友好；
* 文档有历史版本功能；
* 多端同步。

很显然以上几点就抛弃了大部分软件。笔者接下来讲讲对于知识库的软件选择都是怎么演变的。

## 印象笔记
***

印象笔记是 Evernote 的本地化版本。笔者使用印象笔记的时间是 2010 年，比较早期，至今已积累好几万笔记了。印象笔记有如下功能：

* 同步，多设备管理资料；
* 剪藏，一键存储网页和文章；
* 分享，笔记快速共享和协作；
* 搜索，智能检索全文及附件；
* 模板，海量模版库一键套用；
* 清单，高效管理待办事项；
* OCR 扫描，拍图识字、快捷存储；
* 思维导图，高效梳理思路和信息。

笔者目前最为常用的有如下功能：剪藏网页内容，归档微信文章、搜索笔记。归档微信文章可以通过「**我的印象笔记**」服务号实现。之前是通过转发文章到服务号完成归档，受限于微信接口，目前只能发送文章链接给服务号实现功能。至于为什么修改为现在的归档方式，很大概率是因为不可抗力吧。对于笔者而言，印象笔记更像是一个大型的文档库，对于长期不更新的内容非常适合。有一点值得一提，印象笔记支持 Markdown 还是在 2018 年 8 月，实在是太晚了。

## Day One
***

Day One 是一个非常出色的日记应用，以简洁优雅的日志记录体验而闻名。自 2011 年三月面世至今，下载量已经超过 500 万，其品质也得到了多方佐证。它曾摘得 2014 年的苹果设计奖，而 2016 年初新版上架便获得美区的编辑精选，几乎每一个报道 iOS 应用的媒体和博客都推荐过它。

如果笔者喜欢写日记，那 Day One 是最佳的选择。Day One 对于零碎的思考记录，也是非常适合的。Day One 适合短内容，而且多端同步的体验非常友好，同时也是一款支持 Markdown 的软件。Day One 至今也是笔者每天都在用的软件，时不时打开还能看到那年今日记录的点点滴滴。

## MWeb
***

MWeb 是一款专业的 Markdown 写作、记笔记、静态博客生成软件，有 iOS 和 macOS 版本。

MWeb 对 Markdown 的支持非常强大，使用 Github Flavored Markdown 语法，支持表格、TOC、LaTeX、代码块、任务列表、脚注等。图片是使用 Markdown 写作最为苦恼的，MWeb 强大之处就是对于图片的处理非常友好。MWeb 支持截图并粘贴、复制并粘贴、拖拽等方式插入图片并直接显示在编辑器内，而且还可以在发布时将图片上传到自定义图床。

MWeb 的多端同步是依赖 iCloud 或者 Dropbox 实现的，笔者在使用过程中发现 MWeb 体验不及笔者的需求，最后慢慢把 MWeb 淘汰了。

## GitHub
***

有一段时间，笔者将部分文档采用 GitHub 来同步。GitHub 管理文档有如下优势：

* 强大的历史版本控制；
* 对 Markdown 支持特别友好；
* 可以使用 GitHub Pages 托管和发布公开文档。

但是由于移动端编辑文档不方便，最后也是逐渐放弃了这种方式。

## Dropbox Paper
***

直到 2019 年，笔者发现 Dropbox Paper 才是文档库的最佳工具。Dropbox Paper 是 2015 年 10 月 15 日 Dropbox 发布的文件协同编辑服务。Dropbox Paper 在文档管理上有如下的优势：

* 支持 Markdown 语法编辑，格式实时渲染；
* 几乎完美的多人协作文档工具，虽然个人用作知识库对协作的要求不高；
* 支持文档历史版本功能；
* 几乎完美的中文搜索支持，甚至比印象笔记、Day One 等都更为友好；
* 从 Dropbox Paper 2020 开始，将会把创建的文档以文件的形式存放在 Dropbox；
* 支持多端同步，而且 Dropbox Paper 有单独的 App；
* Dropbox Paper 提供多个快捷键，而且都相当实用；
* Dropbox Paper 对 Markdown 表格的支持也特别友好；
* 支持粘贴、拉拽上传图片到 Dropbox Paper，自动上传到 Dropbox CDN；
* Dropbox Paper 支持插入日历，笔者的写作日历就是通过这个完成的；
* Dropbox Paper 的导出功能也特别好，支持导出为 PDF、Word、Markdown 文件。其中导出的 Markdown 文件，图片以链接的形式展现在文件中，用支持 Markdown 预览的编辑器打开，可以实时预览图片。

笔者目前已经把 Dropbox Paper 作为主要的知识库工具。因不可控力，访问 Dropbox Paper 没那么方便，不过无所谓了，笔者的网络天然就是 7*24 小时顺畅的。

值得一提的是，最近备受推崇的字节跳动出品的飞书，文档部分几乎完整地借鉴了 Dropbox Paper，想必飞书的产品经理也是 Dropbox Paper 的深度用户吧。

## 小结
***

本文讲解了笔者选择文档库工具的演变过程。目前笔者的知识库软件组合：印象笔记 + Day One + Dropbox Paper。印象笔记用于文档的存储，Day One 记录零碎，Dropbox Paper 作为主力，主要是记录一些原创文档，以及存放需要经常修改的文档。今年尤为火爆的 All in One 方案 Notion，笔者在体验过程中觉得不适合，就暂时没用。

看完笔者关于知识库的文章，您有什么想法，欢迎留言。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
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