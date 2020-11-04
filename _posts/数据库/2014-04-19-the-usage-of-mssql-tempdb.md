---
published: true
author: Robin Wen
layout: post
title: "深刻的教训-SQL Server关于TempDB的使用"
category: 数据库
summary: "由于tempdb是存储在SSD上，且总大小为270G。所以，在显式使用临时表时一定要注意数据大小。避免把tempdb空间耗尽，影响整个SQLServer的正常运行。好在设置了tempdb的最大空间，并且最大空间小于SSD硬盘的最大容量，不然服务器的盘就会挂掉，从而导致服务器宕机，多么痛的领悟！切忌犯如此低级错误，作下此文提醒和鞭策自己，凡事三思而后行！"
tags:
- 数据库
- Database
- MSSQL
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 场景现象 ##

中午查询了流水，因未与业务人员沟通好，忘了删选条件，导致TempDB不能分配空间，SQL Server高负载运行。

## 错误分析 ##

我们来看看错误日志：

![错误日志](https://cdn.dbarobin.com/fwtY0xt.jpg)

再来看看TempDB自增长记录：

![TempDB自增长记录](https://cdn.dbarobin.com/exMPjmh.png)

## 导致原因 ##

查询语句未指定删选条件，语句如下：

``` bash
--得到流水，因数据敏感问题，已将字段使用’xx’代替。
IF EXISTS (SELECT *
           FROM   tempdb..sysobjects
           WHERE  id = Object_id(N'tempdb..#t_scfw')
                  AND type = 'U')
  DROP TABLE #t_scfw;

IF NOT EXISTS (SELECT *
               FROM   tempdb..sysobjects
               WHERE  id = Object_id(N'tempdb..#t_scfw')
                      AND type = 'U')
  SELECT tsvr.*,
         bsl.xx AS xxx,
         bsl.xx,
         bsl.xx
  INTO   #t_scfw
  FROM   #t1 AS tsvr
         JOIN t2 AS bsl
           ON tsvr.xx = bsl.xx
              AND tsvr.xx = bsl.xx
              AND tsvr.xx = bsl.xx
              AND tsvr.xx = bsl.xx
              AND bsl.xx > 0;
```

## 总结 ##

由于tempdb是存储在SSD上，且总大小为270G。所以，在显式使用临时表时一定要注意数据大小。避免把tempdb空间耗尽，影响整个SQLServer的正常运行。好在设置了tempdb的最大空间，并且最大空间小于SSD硬盘的最大容量，不然服务器的盘就会挂掉，从而导致服务器宕机，多么痛的领悟！切忌犯如此低级错误，作下此文提醒和鞭策自己，凡事三思而后行！

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/24097741" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="深刻的教训-SQL Server关于TempDB的使用" height="16px" width="16px" border="0" alt="深刻的教训-SQL Server关于TempDB的使用" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
