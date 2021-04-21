---
published: true
author: Robin Wen
layout: post
title: "我眼里的SQL Server"
category: 数据库
summary: "我眼里的MSSQL是图形化界面数据库的翘楚，有着良好的交互，在整体上并不比Oracle、MySQL逊色多少。"
tags:
- 数据库
- Database
- MSSQL
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

本文站在一个初级DBA的角度来讲下我眼里的SQL Server。

最早接触MSSQL是在大二下期，那时有门课叫做《数据库系统概论》，想必只要是计算机相关专业，都会开设这门课程。这门课程使用的教材是王珊、萨师煊主编的《数据库系统概论》，这部书堪称国内经典，相信大多数在校学生最初学习数据库都是接触这本书。同大多数学校讲解数据库理论使用MSSQL一样，我们专业也是如此。至于为什么使用MSSQL想必这道理都懂。这里说个现象，网上充斥的MSSQL问题，基本上都是较简单的，为什么呢？因为大学教这个。

学习数据库理论时就会了基本的SQL查询，MSSQL安装和简单使用，比如登录数据库、程序使用ODBC连接数据库、SQL查询分析器的使用。还记得当初能把MSSQL安装上在同学之间就很牛逼了。授课老师使用MSSQL也是用一些基本的功能，教授SQL也是为实现具体的功能，从未考虑过SQL的优化。

![SQL Server in my eye](https://cdn.dbarobin.com/iI2jZCK.jpg)

这两周工作接触到的MSSQL和当初学习又是两番境地。第一，作为生产库，数据量是相当庞大，其中一张表百亿级别，另一张表十亿级别。最开始我不知道有那么大的数据量，就用count(*)统计了下记录数，结果等了半个多小时还没出结果，只好终止查询；第二，目前的工作主要是数据查询，每天接触大量的SQL，其中就涉及到SQL优化。最近把SQL优化好好地学习下，也总结了很多优化技巧。实现同样的功能，使用不同的SQL，哪怕是语句的顺序调整下，效果也是千差万别的。目前优化过程中使用较多的是查看执行计划，也一直在看优化相关的书；第三，更加坚信了这个信念：思维不局限与哪个数据库。以前有篇文章讲的是数据库思维，里面提到我们更应关注的是数据本身，而不是某个具体的数据库；第四，T-SQL使用灵活，也是工作的一大利器，对于重复性的操作尽量编写T-SQL完成；第五，MSSQL也有高可用、复制等等，其他数据库有的MSSQL基本上有，只不过实现的方式不同罢了。

这两周的深入接触MSSQL，思考了MSSQL受中小企业欢迎的原因。第一，MSSQL也需要收费的，但和Oracle、DB2相比，便宜太多；第二，中小企业数据量相对较少，MSSQL作为企业级的数据库还是可以胜任的；第三，MSSQL秉承微软便捷操作的基因，优秀的图形化设计减少了大量的维护成本。说实话，MSSQL使用确实很方便，不过我还是习惯了我的做法，任何可以使用命令操作的绝不使用图形化界面；第四，MSSQL经过这么多年的市场检验，还算非常成功的商业化数据库，优秀齐全的联机文档，快捷方便灵活的社区，良好的售后服务，这些足以让中小企业优先选择MSSQL。

同时，以前接触Oracle 和MySQL较多，也发现了MSSQL和他们的很多相似之处和不同。第一，如前所述，MSSQL图形界面优秀，可以让你摆脱纯命令行的操作，而MySQL、OracleDBA的维护99%以上都是使用命令行；第二，MSSQL下的T-SQL和Oracle的PL/SQL非常类似，这也印证了那一点，原理相通的东西表现形式也差不到哪里去；第三，都说MySQL、Oracle优秀，其实上MSSQL也是很优秀的。MSSQL的开发运营团队确实花了大量的功夫，才让MSSQL在市场上占有一席之地；第四，和Oracle一样，深入MSSQL相对困难，封闭式的环境下研究实现过程、原理性探讨确实比MySQL要难太多；第五，前面说到，优秀的图形化设计让维护MSSQL的成本变低，就拿执行计划来说，MSSQL下那是点下鼠标，再用用眼睛，动动脑就能知道是哪里是最大的性能问题，而Oracle还要打开执行计划，然后执行语句，最后再一大堆行中利用优先执行原则一一分析，这也是MSSQL DBA较Oracle DBA低廉的原因之一。

综上所述，我眼里的MSSQL是图形化界面数据库的翘楚，有着良好的交互，在整体上并不比Oracle、MySQL逊色多少。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：<a href="http://invisibleflamelight.wordpress.com/2013/03/09/sql-server-como-resolver-problemas-relacionados-a-conflitos-de-collation/" target="_blank"><img src="https://cdn.dbarobin.com/kG2Wr20.png" title="sql server in my eye" border="0" alt="sql server in my eye" height="16px" width="16px" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
