---
published: true
author: Robin Wen
layout: post
title: "高并发Insert Ignore存储引擎的选择"
category: 数据库
summary: "如果不是对事务要求非常的强，高并发写推荐选择MyISAM。"
tags:
- 数据库
- Database
- MySQL
---

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**「问题」**

高并发写的时候是选择innodb还是myisam呢？或者说如何提高insert效率？
每秒大概有5w - 7w的量(更正下，大概2w - 3w的量)，简单的3个int型字段，3个字段是唯一索引，如何提高insert ignore的速度？

简单描述下应用场景，每天的uv和ip分表，uv 4m ip 33m 的数据量，可以忽略查询，只有insert ignore， 3个字段 t d s唯一索引

**「我的回答」**

**「选择InnoDB还是MyISAM」**

如果不是对事务要求非常的强，高并发写推荐选择MyISAM。理由如下：
MyISAM的索引和数据是分开的，并且索引是有压缩的，内存使用率就对应提高了不少，能加载更多索引，而Innodb是索引和数据是紧密捆绑的，没有使用压缩从而会造成Innodb比MyISAM体积庞大不小；
InnoDB存储引擎在插入数据时会花更多的开销在维护完整性、维持事务上，所以效率比MyISAM低；
根据题主的描述，主要是插入数据，并且只有一张表，后期对该表的操作也主要是查询吧，就查询速度而言，MyISAM比InnoDB更优越，并且还有MyISAM索引，可以很好的优化查询速度。

![MySQL insert of high concurrency](https://cdn.dbarobin.com/T6RM2E6.jpg)

**「如何提高INSERT效率」**

根据题主的描述，每秒有5w - 7w的量，诚然这个数据量是相当惊人的。并且数据是实时产生的，那mysqlimport或者load data就无法派上用场。提供几个提高INSERT效率的办法吧。
批量插入VALUES，而不是每一次插入都是一条数据；
删除MySQL的索引，有索引存在插入速度会受很大的影响；
题主的表有3个int字段，并且都是唯一索引，可以指定一个主键。

**「是否选择insert ignore」**

我们知道有三种插入方法，insert into、replace into及insert ignore。既然题主提供表的3个字段都是唯一的，那不会出现重复的数据，为什么还用insert ignore呢？insert into此时跟insert ignore不是一样。

如上，个人拙见，仅供参考。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：<a href="http://infodecisionnel.com/sgbd/bdd/myisam-vs-innodb/
infodecisionnel" target="_blank"><img src="https://cdn.dbarobin.com/nh8cHh4.gif" title="MySQL insert of high concurrency" border="0" alt="MySQL insert of high concurrency" height="16px" width="16px" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>

