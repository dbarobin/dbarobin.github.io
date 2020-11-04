---
published: true
author: Robin Wen
layout: post
title: "SQL Server快速导入数据分享"
category: 数据库
summary: "SQL Server快速导入数据，可以尝试的方法如下：CTE、OpenRowSet/OpenDataSource、BULK INSERT、bcp、Shell。"
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

SQL Server快速导入数据，可以尝试的方法如下：**CTE、OpenRowSet/OpenDataSource、BULK INSERT、bcp、Shell**。

下面依次介绍这几种办法。

## 1.CTE ##

首先，我们看看什么是CTE。公用表表达式（Common Table Expression)是SQL SERVER 2005版本之后引入的一个特性。CTE可以看作是一个临时的结果集，可以在接下来的一个SELECT，INSERT，UPDATE，DELETE，MERGE语句中被多次引用。使用公用表达式可以让语句更加清晰简练。CTE 与派生表类似，具体表现在不存储为对象，并且只在查询期间有效。与派生表的不同之处在于，CTE 可自引用，还可在同一查询中引用多次。
更多请点击：<a href="http://technet.microsoft.com/zh-cn/library/ms190766(v=sql.105).aspx" target="_blank"><img src="https://cdn.dbarobin.com/5zY3SER.png" title="technet" height="16px" width="100px" border="0" alt="technet" /></a>


示例如下：

``` bash
USE AdventureWorks2008R2;
GO
-- Define the CTE expression name and column list.
WITH Sales_CTE (SalesPersonID, SalesOrderID, SalesYear)
AS
-- Define the CTE query.
(
    SELECT SalesPersonID, SalesOrderID, YEAR(OrderDate) AS SalesYear
    INTO #temp1
    FROM Sales.SalesOrderHeader
    WHERE SalesPersonID IS NOT NULL
)
-- Define the outer query referencing the CTE name.
SELECT SalesPersonID, COUNT(SalesOrderID) AS TotalSales, SalesYear
INTO #temp2
FROM Sales_CTE
GROUP BY SalesYear, SalesPersonID
ORDER BY SalesPersonID, SalesYear;
GO
```

## 2.OpenRowSet/OpenDataSource ##

OpenRowSet和OpenDataSource都可以访问远程的数据库，但具体表现上，二者还是有差别的。OpenDataSource 不使用链接的服务器名，而提供特殊的连接信息，并将其作为四部分对象名的一部分。 而OpenRowSet 包含访问 OLE DB 数据源中的远程数据所需的全部连接信息。当访问链接服务器中的表时，这种方法是一种替代方法，并且是一种使用 OLE DB 连接并访问远程数据的一次性的、特殊的方法。可以在查询的 FROM 子句中像引用表那样引用 OpenRowSet 函数。依据 OLE DB 提供程序的能力，还可以将 而OpenRowSet 函数引用为 INSERT、UPDATE 或 DELETE 语句的目标表。尽管查询可能返回多个结果集，然而OPENROWSET 只返回第一个。更多请点击：<a href="http://technet.microsoft.com/en-us/library/ms179856.aspx" target="_blank"><img src="https://cdn.dbarobin.com/5zY3SER.png" title="technet" height="16px" width="100px" border="0" alt="technet" /></a>


示例如下：

``` bash
--启用Ad Hoc Distributed Queries
EXEC SP_CONFIGURE 'show advanced options',1
RECONFIGURE
EXEC SP_CONFIGURE 'Ad Hoc Distributed Queries',1
RECONFIGURE
--使用OpenDataSource导入数据
INSERT INTO IMP_DATA.dbo.t_goods
SELECT *
FROM OpenDataSource( 'Microsoft.Jet.OLEDB.12.0',
'Data Source="E:/Report1.txt";User ID=Admin;Password=;
Extended properties=Excel 12.0')...[Sheet1$]
--使用完毕后，切记关闭它，因为这是一个安全隐患
EXEC SP_CONFIGURE 'Ad Hoc Distributed Queries',0
RECONFIGURE
EXEC SP_CONFIGURE  'show advanced options',0
RECONFIGURE
```

## 3.BULK INSERT ##

BULK INSERT允许用户以其指定的格式将数据文件导入到数据库表或视图中。更多请点击：<a href="http://msdn.microsoft.com/zh-cn/library/ms188365.aspx" target="_blank"><img src="https://cdn.dbarobin.com/5zY3SER.png" title="technet" height="16px" width="100px" border="0" alt="technet" /></a>

示例如下：

``` bash
--定义导入目的和导入源
BULK INSERT IMP_DATA.dbo.t_goods FROM 'E:/Report1.txt'
WITH (
  --列分隔符
  FIELDTERMINATOR = ',',
  --行分隔符
  ROWTERMINATOR = '\n'
)
```

## 4.bcp ##

bcp 实用工具可以在 Microsoft SQL Server 实例和用户指定格式的数据文件间大容量复制数据。 使用 bcp 实用工具可以将大量新行导入 SQL Server 表，或将表数据导出到数据文件。 除非与 queryout 选项一起使用，否则使用该实用工具不需要了解 Transact-SQL 知识。 若要将数据导入表中，必须使用为该表创建的格式文件，或者必须了解表的结构以及对于该表中的列有效的数据类型。
更多请点击：<a href="http://msdn.microsoft.com/zh-cn/library/ms162802.aspx" target="_blank"><img src="https://cdn.dbarobin.com/5zY3SER.png" title="technet" height="16px" width="100px" border="0" alt="technet" /></a>

示例如下：

``` bash
--打开高级选项
EXEC SP_CONFIGURE 'show advanced options', 1;
RECONFIGURE;
--启用执行CMD命令
EXEC SP_CONFIGURE 'xp_cmdshell', 1;
RECONFIGURE;

--指定导入目的和导入源
EXEC master..xp_cmdshell 'BCP IMP_DATA.dbo.t_goods in E:\report.txt -c -T'
```

## 5.Shell ##

Shell通过拼接插入字符串的方法非常灵活，并且出错较少，但插入的内容包含很多非法字符的话会很恼。可以参考以前写的文章：<a href="https://dbarobin.com/2014/03/15/create-test-data-in-mssql-without-import-privilege/" target="_blank">缺乏导入数据权限，SQL Server创建测试数据</a>

最后，贴张前段时间做的图，导入数据总结：

![导入数据总结](https://cdn.dbarobin.com/UK4yGEJ.jpg)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="" height="16px" width="16px" border="0" alt="" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
