---
published: true
author: Robin Wen
layout: post
title: "白鳝前辈分享的DBA常用软件"
category: 数据库
summary: "DBA 的电脑上需要安装什么软件？经常有人问老白，其实每个 DBA 都有自己喜欢使用的软件。对于使用什么软件，用的习惯，用的熟练就好。因为工具只是起到一个辅助的作用，工具的作用是帮助 DBA 思考，在 DBA 进行分析的时候提供辅助作用。因此每个 DBA 使用的软件一定是要和自己的分析习惯相吻合。对于初级的 DBA 来说， QUEST公司的 SPOTLIGHT 这样的的工具对于 DBA 来说是十分有效的工具，可以帮助 DBA 对数据库的总体情况做出一个初步的分析。而对于资深的 DBA ，SPOTLIGHT这样的工具能够提供的帮助就比较少了。"
tags:
- 数据库
- Database
- DBA
- Oracle
- 工具
---

`整理/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**【了解作者】**

> 白鳝，真名徐戟，国内资深的系统优化专家。著有《Oracle优化日记》、《OracleRAC日记》。本文摘自《DBA日志》。

**【DBA常用软件】**

DBA 的电脑上需要安装什么软件？经常有人问老白，其实每个 DBA 都有自己喜欢使用的软件。对于使用什么软件，用的习惯，用的熟练就好。因为工具只是起到一个辅助的作用，工具的作用是帮助 DBA 思考，在 DBA 进行分析的时候提供辅助作用。因此每个 DBA 使用的软件一定是要和自己的分析习惯相吻合。对于初级的 DBA 来说， QUEST公司的 SPOTLIGHT 这样的的工具对于 DBA 来说是十分有效的工具，可以帮助 DBA 对数据库的总体情况做出一个初步的分析。而对于资深的 DBA ，SPOTLIGHT这样的工具能够提供的帮助就比较少了。

实际上老白的电脑上的工具软件很少：
首先老白的电脑上肯定会装有数据库，oracle  9.2.0.8 和 oracle  10.2.0.3 的数据库各有一个，在9.2.0.8 的数据库里还配置了一个 OMS 的服务；

9i 的数据库里当然安装了老白的最爱OEM ，OEM 工具中老白经常使用的是诊断包和优化包；

**TOAD**是老白十分喜欢使用的工具，不过老白的电脑里安装的 TOAD 版本还是 7.6，对于工具，只要够用就行；

**SPOTLIGHT**是个不错的工具，当然应该安装。不过使用这个软件的机会不多，因为SPOTLIGHT要在用户的数据库中创建很多对象才能使用好分析功能，而对于老白服务的绝大多数客户，都不允许随便创建表，所以也只能忍痛割爱了。
实际上来说，老白的电脑中和ORACLE 相关的工具只有上面这几个了，其他工具陆续使用过，感觉和老白的分析问题的思路不一致，因此也就没有继续使用了。除了ORACLE 的工具外，其他一些系统工具也是十分重要的；

**lNETTERM**是 TELNET 的工具，而且有免费版的。NETTERM的兼容性很不错，主流的操作系统下表现都不错，而且这个软件可以免安装，所以每次老白换电脑的时候只要把安装目录拷贝过去就行了，里面的服务器定义不会丢失。NETTERM的 SESSION LOG 功能也十分好，每次老白连到客户系统上的时候手心会开启SESSION LOG，这样就可以把每次操作的情况记录下来，这回老白写 DBA 日记，很多细节都是借助SESSION LOG 才回忆起来的。老白不喜欢使用支持多页的TELNET 工具，这不是说多页支持不好，而是在老白的 DBA 生涯中越做越谨慎了。5 、6 年前老白还是很喜欢一次性开多个窗口来进行操作的。而随着 DBA 工作经历的增加，现在老白很怕在同时开多个窗口进行操作。如果必须开多个窗口，那么绝对要保证，多个窗口都是连到同一台服务器的同一个 UNIX 账号的。这是为什么呢？作为一个 DBA，没有过误操作经历的人可能很少，有时候误操作只能让你出身汗，而很多时候，误操作可以让你万劫不复。大概 7、8 年前吧，老白碰到过一件事，当时在一个客户那里做服务，和开发商的人坐在一个办公室里。当时开发商有个哥们刚刚做了火车回到客户那里，正好碰到两个事情，一个是生产库上有个进程 HANG 住了，另外是测试小组要开始新一轮测试，需要清理一下测试机的数据库。所以那个哥们开了两个窗口，分别进行操作，由于当时可能刚刚坐了一夜火车，在这两个系统上来回倒了几次后终于做了一个误操作，删除了一张重要的生产表的数据。从那次以后，老白在客户现场尽可能避免同时连到多个系统上，以避免不必要的误操作；

![DBA common tools by baishan](https://cdn.dbarobin.com/lkqPTDC.jpg)

**SSh secure shell**是连接 ssh 的服务器的工具，不需要老白做过多的解释了吧；

**Xmanager**这个恐怕不用说了，不过使用的机会并不多，主要是做数据库安装和升级的时候；

**Ultraedit**也是老白重要的武器，ultraedit的最大优点是打开的文件变化时能够捕捉到变化，并重新更新。因此老白经常使用 ultraedit 来读取netterm 的 session log 文件；

**Cygwin**一个可以在 WINDOWS 下模拟 LIUNX的软件，在这个软件里可以在 WINDOWS 下使用 LINUX 的命令，比如dd,awk ， gc++等等，直接在 windows 下用 awk 调用 ass 分析systemstate dump 是十分有用的，有时候老白需要写个简单的 c 程序，也可以用 cygwin 来调试；

**Wincvs**可能听说的朋友比较少，cvs 是著名的文档版本管理软件，老白用来管理文档的；

**Firefox+scrapboo**老白的知识库收集工具，在网上看到喝什么好的文档，立即拉到知识库里。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：<a href="http://www.carolynhampe.com/253671/3313623/work/common-tools-commencement-show-branding" target="_blank"><img src="https://cdn.dbarobin.com/klUhMnG.png" title="" height="16px" width="16px" border="0" alt="" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
