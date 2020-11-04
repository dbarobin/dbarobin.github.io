---
published: true
author: Robin Wen
layout: post
title: 运维 DBA 的消亡
category: 数据库
summary: 早在 2013 年，好友 waterbin 就写过一篇文章，标题叫做「DBA 的职业发展机会」。在这篇文章中，waterbin 提到了几个话题，亦即 一，OldSQL、NoSQL、NewSQL；二，DevOps；三，数据可视化；四，DBA 还是 DA。文章中的不少观点，现在看来依然不过时。而这篇文章，笔者想讲讲运维 DBA 的消亡。运维 DBA 为什么会消亡，笔者做下不成熟的分析，理由如下：第一，运维的成本越来越低。第二，企业对 DBA 人才的要求越来越高。第三，数据层的丰富性。数据库的发展今后依然会变化不穷，依然会马不停蹄。但有一样东西不会变，那就是数据的核心地位。
tags:
- 数据库
- Database
- 运维
- DBA
- 想法
comments:
  - author:
      type: full
      displayName: sunswayne
      url: 'https://github.com/sunswayne'
      picture: 'https://avatars.githubusercontent.com/u/12680874?v=3&s=73'
    content: '&#x611F;&#x89C9;&#x53D7;&#x76CA;&#x532A;&#x6D45;~'
    date: 2016-03-28T09:26:43.952Z
  - author:
      type: github
      displayName: nutto
      url: 'https://github.com/nutto'
      picture: 'https://avatars.githubusercontent.com/u/5317202?v=3&s=73'
    content: '&#x611F;&#x89C9;&#x5199;&#x5F97;&#x4E0D;&#x9519;'
    date: 2016-04-13T05:59:42.308Z
  - author:
      type: github
      displayName: nutto
      url: 'https://github.com/nutto'
      picture: 'https://avatars.githubusercontent.com/u/5317202?v=3&s=73'
    content: '&#x672C;&#x6765;&#x8FD8;&#x60F3;&#x505A;&#x4E00;&#x4E0B;DBA&#x65B9;&#x9762;&#x7684;&#x5C1D;&#x8BD5;,&#x4F46;&#x662F;&#x73B0;&#x5728;&#x8FD9;&#x4E2A;&#x5FF5;&#x5934;&#x6709;&#x70B9;&#x52A8;&#x6447;&#x4E86;'
    date: 2016-04-13T06:00:41.519Z

---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

早在 2013 年，好友 [waterbin](http://blog.csdn.net/dba_waterbin) 就写过一篇文章，标题叫做**「[DBA 的职业发展机会](http://blog.csdn.net/dba_waterbin/article/details/17187257)」**。在这篇文章中，waterbin 提到了几个话题，亦即：一，OldSQL、NoSQL、NewSQL；二，DevOps；三，数据可视化；四，DBA 还是 DA。文章中的不少观点，现在看来依然不过时。而这篇文章，笔者想讲讲运维 DBA 的消亡。

整个信息时代，就是信息不断积累，不断膨胀的发展历程。「[失控](https://zh.wikipedia.org/wiki/%E5%A4%B1%E6%8E%A7)」一书提到：**万物源于比特（It from bit）**。还可以这样说，一切皆信息。那数据是什么，数据是信息的表现形式和载体。再者，数据库是什么，按照[维基百科](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%BA%93)的解释，「数据库指的是以一定方式储存在一起、能为多个用户共享、具有尽可能小的冗余度、与应用程序彼此独立的数据集合。」数据的规模呈指数级增长，规模与多样性是并存的，数据库的丛林也同步千姿百态。DBA 在这种规模效应中应运而生。DBA 的触角不断蔓延，从最开始的大型公司，再到中型，再到小型。从一家公司的 DBA 团队规模，大抵可以估摸这家公司的技术水平、行业影响力等等。

数据库的发展也趋渐多样化，从传统的关系型数据库、到 NoSQL，再到整合前两者的 NewSQL。关系型数据库中，有我们熟悉的 Oracle、SQL Server 和 MySQL，还有很多适用不同场景的关系型数据库。从 [NoSQL Databases](http://nosql-database.org/) 官网中可以看到，列表中已经有超过 225 个不同类型的 NoSQL，这些 NoSQL 有很多种类，包括但不限于 Wide Column Store / Column Families、Document Store、Key Value / Tuple Store、Graph Databases、Multimodel Databases、Object Databases 等等。数据库的总体发展趋势，从有型到无型，从硬盘到内存。当然，每种类型的数据库都有各自的适应场景，评判高下，就像争论什么是好的编程语言一样，没有意义。

DBA 在多年以前是一个很风光的行业，那时会搭 DG 都是件特别牛逼的事情。从之前 Oracle 的风生水起，再到现在 MySQL 的遍地开花，DBA 越来越贬值。物以稀为贵，这是亘古不变的真理。每个行业的人才都是如此，顶尖的很少，大部分的都是处在中间位置。DBA 的门槛相对其它岗位，目前还是偏高的，但这个门槛会越来越低。

[四火](http://www.raychase.net/)在**「[从淘汰 Oracle 数据库的事情说起](http://www.raychase.net/3689)」**一文中提到一个观点很有意思：**「维护」要么因为简单而能被机器和软件替代掉，要么因为复杂而被革命掉。**不少公司的 DBA，做的工作基本上是偏运维的，比如日常的变更工单、故障处理、隐患处理、备份恢复、SQL 优化等等。纯粹的没有开发能力的运维 DBA，是没有前途的。

![A Wheatfield, with Cypresses](https://cdn.dbarobin.com/AqBXPzz.jpg)

运维 DBA 为什么会消亡，笔者做下不成熟的分析，理由如下：

**第一，运维的成本越来越低。**目前各个公司都在建立或者说已经完成各自的运维平台，机械化程度越来越高，人工干预程度越来越低。随着开源的盛行，建立运维平台的成本也越来越低，有研发能力的运维团队会独立开发，研发能力不足的会拿开源产品二次开发，没有研发能力的干脆选用一款成熟的开源产品。既然运维平台就可以完成的事情，运维 DBA 的人才需求就会逐步减少。

**第二，企业对 DBA 人才的要求越来越高。**运维 DBA 从事的工作，技术含量偏低，可替代型较大。然而，一个人的核心竞争力，来自于不可替代性。一个 DBA 要想有很好的发展，必须懂业务，必须懂开发。懂业务，懂开发，才有可能根据业务特性选取适合的架构方案，才有可能对一个开源产品了如指掌，才有可能产出数据层的产品。

**第三，数据层的丰富性。**因为丰富，导致了运维 DBA 不能满足需求。运维，更多偏向于维护成熟的产品。而新技术不断蓬勃发展，对 DBA 的技术要求会更广。新技术的深入引入，需要对源码有所了解，运维 DBA 很难胜任。如果对一个开源产品无法从源码级别进行剖析，那平时遇到的莫名其妙的问题，很难定位到根本的原因，也无法提出解决方案。可以这样说，运维 DBA 对待开源产品，就像是盲人摸象，无法有全方位的掌控；就像是在黑夜中行走，稍不注意就掉坑里。要知道，DBA 这个行业，懂开发的是相对较少的。

未来运维 DBA 会越来越不值钱，会更加懂业务，会往研发型或者其它方向转型。

那么，运维 DBA 的机会在哪里？给出以下几个关键词，仅做抛砖引玉吧。

* DA
* BI
* 中间件
* 数据层产品

数据库的发展今后依然会变化不穷，依然会马不停蹄。但有一样东西不会变，那就是 **数据的核心地位。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

延伸阅读：

* [DBA 的职业发展机会](http://blog.csdn.net/dba_waterbin/article/details/17187257)
* [NoSQL Databases](http://nosql-database.org/)
* [从淘汰 Oracle 数据库的事情说起](http://www.raychase.net/3689)

–EOF–

题图：© Vincent Willem van Gogh/A Wheatfield, with Cypresses/Wikipedia

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
