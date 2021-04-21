---
published: true
author: Robin Wen
layout: post
title: "MariaDB, Replacement for MySQL, 此时Oracle态度"
category: 数据库
summary: "众说纷纭，不过对企业、用户来说未尝不是好事，数据库技术肯定是不断地向前发展，企业、用户也会有更多的选择。"
tags:
- 数据库
- Database
- MySQL
- MariaDB
- Oracle
- 想法
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

2013年9月22日Oracle官方发布MySQL5.7.2，功能增加了很多，也有很多优化，该版本提供了更快的连接速度，更高的事务吞吐量，提升了复制速度，带来了内存仪表和其他增强功能，从而实现更高的性能和增强的可管理性。

但另外一条新闻是“MySQL再度失势：继维基百科之后，Google也迁移到了MariaDB”，不知此时Oracle怎么想。早在今年4月份就有报道MySQL原始团队已重整旗鼓。Oracle于09年收购了Sun，其中必不可少的原因就是获得MySQL这个最热门开源DBMS的控制权。然而这一收购似乎并未完全达到Oracle的目的：早在08年MySQL被Sun收购之后，MySQL旧部（一些创始人及顶级工程师）就离开了MySQL并成立新公司SkySQL；而在Sun被Oracle收购后，同样有一批高层出去创立了Monty Program Ab（MariaDB的母公司）。

![Imgur](https://cdn.dbarobin.com/WiY6p7L.png)

我们来看看什么是MariaDB。MariaDB官网写了这样一句话：Anenhanced, drop-in replacement for MySQL。Oracle看到这个标题肯定瞬间凌乱了，不过有很多说法，文末会给出。MariaDB是一个向后兼容、替代MySQL的数据库服务器。它包含所有主要的开源存储引擎。与MySQL 相比较，MariaDB 更强的地方在于：Maria 存储引擎、PBXT 存储引擎、XtraDB存储引擎、FederatedX存储引擎、更快的复制查询处理、线程池、更少的警告和bug、运行速度更快、更多的 Extensions(More index parts, new startup options etc)、更好的功能测试、数据表消除、慢查询日志的扩展统计、支持对 Unicode 的排序。相对于MySQL最新的版本5.6来说，在性能、功能、管理、NoSQL扩展方面，MariaDB包含了更丰富的特性。。比如微秒的支持、线程池、子查询优化、组提交、进度报告等。

毋庸置疑，Oracle是当今数据库的老大，现在坐拥收费的Oracle和开源免费的MySQL，不用说是数据库的王者。有说法Oracle会把MySQL一步步搞死，MySQL走向闭源；又有说法Oracle会大力扶持MySQL，甚至加入Oracle中优秀的元素，从而牵制其他数据库的发展。现在MariaDB的发展迅猛，势必会对MySQL构成威胁，一方面Oracle很担心会使MySQL的占有率下降，另一方面Oracle也很期望有这样的产品对MySQL造成威胁，这样自家的Oracle会更有优势。不过前途并不见得光明，现在互联网有去Oracle化的趋势，也就是说很多互联网企业会抛弃Oracle，转向MySQL或者其他一些开源的数据库，或者在MySQL源码上进行二次开发，Oracle只是一些传统企业，比如电信、移动、金融、一些国企使用。而且云计算、大数据时代，刚发布的12c（关键词：”Plug into the Cloud”）虽加入Multitenant组件（PluggableDatabase特性），也就是Oracle对云计算发起突击，但传统关系型数据库怎么适应这个变化前途也不是那么明朗，想必这些也是Oracle现在担心的吧。

众说纷纭，不过对企业、用户来说未尝不是好事，数据库技术肯定是不断地向前发展，企业、用户也会有更多的选择。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：Google Images

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
