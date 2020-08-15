---
published: true
author: Robin Wen
layout: post
title: "SQL Server截取字符串和处理中文技巧"
category: 数据库
summary: "在工作中，发现和总结这些小技巧会让你的工作事半功倍。"
tags:
- Database
- 数据库
- MSSQL
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 环境介绍 ##

**SQL  Server**

``` bash
PRINT @@VERSION
MicrosoftSQLServer2012-11.0.2100.60(X64)
Feb10201219:39:15
Copyright(c)MicrosoftCorporation
EnterpriseEdition:Core-basedLicensing(64-bit)onWindowsNT6.1(Build7601:ServicePack1)
```

**操作系统**

``` bash
------------------
System Information
------------------
Operating System: Windows 7 Ultimate 64-bit (6.1, Build 7601) \
Service Pack 1 (7601.win7sp1_gdr.130828-1532)
System Model: Aspire E1-471G
Processor: Intel(R) Core(TM) i5-3230M CPU @ 2.60GHz (4 CPUs), ~2.6GHz
Memory: 4096MB RAM
```

## 二 实现功能 ##

从一大堆有包含中文字符和编号的字符串中过滤出编号。

## 三 实现模拟 ##

首先，我们准备测试数据，注意，这里的数据全部都是模拟数据，无实际含义。语句如下：

``` bash
CREATE TABLE #temp
(
   name VARCHAR(80)
);

INSERT INTO #temp
VALUES     ('五道口店3059');

INSERT INTO #temp
VALUES     ('五羊邨店3060');

INSERT INTO #temp
VALUES     ('杨家屯店3061');

INSERT INTO #temp
VALUES     ('十里堤店3062');

INSERT INTO #temp
VALUES     ('中关村店3063');

INSERT INTO #temp
VALUES     ('丽秀店3064');

INSERT INTO #temp
VALUES     ('石门店3065');

INSERT INTO #temp
VALUES     ('黄村店3066');

INSERT INTO #temp
VALUES     ('东圃店3067');

INSERT INTO #temp
VALUES     ('天河店3068');

INSERT INTO #temp
VALUES     ('人民路广场3069');

INSERT INTO #temp
VALUES     ('社区中心3070');

INSERT INTO #temp
VALUES     ('珠海市3071');

INSERT INTO #temp
VALUES     ('丽都3072');

INSERT INTO #temp
VALUES     ('晓月3073');

INSERT INTO #temp
VALUES     ('旧区3074');

INSERT INTO #temp
VALUES     ('新城3075');

INSERT INTO #temp
VALUES     ('水井沟3076');
```

然后，我们观察数据，发现这些数据都有规律，编号是数字，占4个字符。数字前面包含店、场、心、市、都、月、区、城、沟共9个字符。
我们试着采用SQL Server内置的函数Substring、Charindex、Rtrim、Ltrim过滤掉出现次数最多（店）的字符串。
语句如下：

``` bash
SELECT Rtrim(Ltrim(Substring(name, Charindex('店', name) + 1, Len(name)))) AS name
INTO   #t1
FROM   #temp
```

以下是这几个函数的使用说明：

**Substring**

> **Returns the part of a character expression that starts at the specified position and has the specified length. The position parameter and the length parameter must evaluate to integers.**
>
> **Syntax**
> SUBSTRING(character_expression, position, length)
>
> **Arguments**
> character_expression
> Is a character expression from which to extract characters.
> position
> Is an integer that specifies where the substring begins.
> length
> Is an integer that specifies the length of the substring as number of characters.
>
> **Result Types**
> DT_WSTR

**Charindex**

> **Searches an expression for anOther expression and returns its starting position if found.**
>
> **Syntax**
> CHARINDEX ( expressionToFind ,expressionToSearch [ , start_location ] )
>
> **Arguments**
> expressionToFind
> Is a character expression that contains the sequence to be found. expressionToFind is limited to 8000 characters.
> expressionToSearch
> Is a character expression to be searched.
> start_location
> Is an integer or bigint expression at which the search starts. If start_location is not specified, is a negative number, or is 0, the search starts at the beginning of expressionToSearch.
>
> **Return Types**
> bigint if expressionToSearch is of the varchar(max), nvarchar(max), or varbinary(max) data types; Otherwise, int.

**Rtrim**

> **Returns a character expression after removing trailing spaces.**
>
> RTRIM does not remove white space characters such as the tab or line feed characters. Unicode provides code points for many different types of spaces, but this function recognizes only the Unicode code point 0x0020. When double-byte character set (DBCS) strings are converted to Unicode they may include space characters Other than 0x0020 and the function cannot remove such spaces. To remove all kinds of spaces, you can use the Microsoft Visual Basic .NET RTrim method in a script run from the Script component.
>
> **Syntax**
> RTRIM(character expression)
>
> **Arguments**
> character_expression
> Is a character expression from which to remove spaces.
>
> **Result Types**
> DT_WSTR

**Ltrim**

> **Returns a character expression after removing leading spaces.**
>
> LTRIM does not remove white-space characters such as the tab or line feed characters. Unicode provides code points for many different types of spaces, but this function recognizes only the Unicode code point 0x0020. When double-byte character set (DBCS) strings are converted to Unicode they may include space characters Other than 0x0020 and the function cannot remove such spaces. To remove all kinds of spaces, you can use the Microsoft Visual Basic .NET LTrim method in a script run from the Script component.
>
> **Syntax**
> LTRIM(character expression)
>
> **Arguments**
> character_expression
> Is a character expression from which to remove spaces.
>
> **Result Types**
> DT_WSTR

好了，我们查看处理完后的结果，可以看到包含店的字符串已经全部过滤出编号。

``` bash
SELECT * FROM #t1
3059
3060
3061
3062
3063
3064
3065
3066
3067
3068
人民路广场3069
社区中心3070
珠海市3071
丽都3072
晓月3073
旧区3074
新城3075
水井沟3076
```

接着我们依次处理包含场、心、市、都、月、区、城、沟的字符串，语句和处理结果如下：

``` bash
SELECT *
FROM   #t1
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
人民路广场3069
社区中心3070
珠海市3071
丽都3072
晓月3073
旧区3074
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('场', name) + 1, Len(name)))) AS name
INTO   #t2
FROM   #t1
SELECT *
FROM   #t2
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
社区中心3070
珠海市3071
丽都3072
晓月3073
旧区3074
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('心', name) + 1, Len(name)))) AS name
INTO   #t3
FROM   #t2
SELECT *
FROM   #t3
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
珠海市3071
丽都3072
晓月3073
旧区3074
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('市', name) + 1, Len(name)))) AS name
INTO   #t4
FROM   #t3
SELECT *
FROM   #t4
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
丽都3072
晓月3073
旧区3074
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('都', name) + 1, Len(name)))) AS name
INTO   #t5
FROM   #t4
SELECT *
FROM   #t5
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
晓月3073
旧区3074
新城3075
水井沟3076


SELECT Rtrim(Ltrim(Substring(name, Charindex('月', name) + 1, Len(name)))) AS name
INTO   #t6
FROM   #t5
SELECT *
FROM   #t6
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
旧区3074
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('区', name) + 1, Len(name)))) AS name
INTO   #t7
FROM   #t6
SELECT *
FROM   #t7
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
新城3075
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('城', name) + 1, Len(name)))) AS name
INTO   #t8
FROM   #t7
SELECT *
FROM   #t8
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
水井沟3076

SELECT Rtrim(Ltrim(Substring(name, Charindex('沟', name) + 1, Len(name)))) AS name
INTO   #t9
FROM   #t8
SELECT *
FROM   #t9
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
--无记录
```

这是最终的处理结果，过滤出编号后，我就可以利用这些编号和数据库表进行关联，获得想要的数据。

``` bash
SELECT *
INTO   #result
FROM   #t9
SELECT *
FROM   #result
name
3059
3060
3061
3062
3063
3064
3065
3066
3067
3068
3069
3070
3071
3072
3073
3074
3075
3076

SELECT s.xxx,
       s.xxx
FROM   xx s
       JOIN #result r
         ON s.xxx = r.name
WHERE  s.xxx = 0;
```

## 四 总结 ##

本文过滤编号实际上核心代码就两个，第一个是利用SQL Server的内置函数过滤出指定编号，语句如下：

``` bash
SELECT Rtrim(Ltrim(Substring(name, Charindex('店', name) + 1, Len(name)))) AS name
INTO   #t1
FROM   #temp
```

第二个是判断是否包含中文，语句如下：

``` bash
SELECT *
FROM   #t1
WHERE  name LIKE N'%[一-龥]%' COLLATE Chinese_PRC_BIN
```

**在工作中，发现和总结这些小技巧会让你的工作事半功倍。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/24516997" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="SQL Server截取字符串和处理中文技巧" height="16px" width="16px" border="0" alt="SQL Server截取字符串和处理中文技巧" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
