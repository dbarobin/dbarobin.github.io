---
published: true
author: Robin Wen
layout: post
title: "MySQL 判断中文字符"
category: 数据库
summary: "在生产环境中，经常会有这样的场景：获得中文数据。那问题就来了，怎么才能匹配出中文字符呢？本文提供两种方法。"
tags:
- 数据库
- Database
- MySQL
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 引子 ##

在生产环境中，经常会有这样的场景：获得中文数据。那问题就来了，怎么才能匹配出中文字符呢？

本文提供两种方法。

## 二 演示 ##

### 2.1 环境 ###

``` bash
mysql> SHOW VARIABLES LIKE "%version%";
+-------------------------+------------------------------+
| Variable_name           | Value                        |
+-------------------------+------------------------------+
| protocol_version        | 10                           |
| version                 | 5.1.73                       |
| version_comment         | MySQL Community Server (GPL) |
| version_compile_machine | i386                         |
| version_compile_os      | apple-darwin10.3.0           |
+-------------------------+------------------------------+
5 rows in set (0.00 sec)
```

### 2.2 创建测试表和插入测试数据 ###

``` bash
mysql -S /tmp/mysql_5173.sock -uroot -proot
```

创建测试表和插入测试数据。

``` bash
mysql> USE test;
Database changed

mysql> CREATE TABLE user
    -> (name VARCHAR(20)
    ->  ) DEFAULT CHARSET = utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.10 sec)

mysql> SHOW TABLE STATUS LIKE 'user' \G;
*************************** 1. row ***************************
           Name: user
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 2
 Avg_row_length: 8192
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2015-01-16 18:01:36
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: 
        Comment: 
1 row in set (0.00 sec)

ERROR: 
No query specified

mysql> INSERT INTO user VALUES('robin');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO user VALUES('robin');
Query OK, 1 row affected (0.00 sec)
```

## 三 实现 ##

### 3.1 方法一 正则表达式 ###

``` bash
mysql> SELECT * FROM user \G;
*************************** 1. row ***************************
name: robin
*************************** 2. row ***************************
name: robin
2 rows in set (0.00 sec)

mysql> SELECT name,
    ->     CASE name REGEXP "[\u0391-\uFFE5]"
    ->         WHEN 1 THEN "不是中文字符"
    ->         ELSE "是中文字符"
    ->     END AS "判断是否是中文字符"
    -> FROM user;
+-----------+-----------------------------+
| name      | 判断是否是中文字符 |
+-----------+-----------------------------+
| robin     | 不是中文字符          |
| robin | 是中文字符             |
+-----------+-----------------------------+
2 rows in set (0.00 sec)

mysql> SELECT name FROM user WHERE NOT (name REGEXP "[\u0391-\uFFE5]");
+-----------+
| name      |
+-----------+
| robin |
+-----------+
1 row in set (0.00 sec)
```

### 3.2 方法二 length() 和 char_length() ###

``` bash
mysql> SELECT name, length(name), char_length(name) FROM user;
+-----------+--------------+-------------------+
| name      | length(name) | char_length(name) |
+-----------+--------------+-------------------+
| robin     |            5 |                 5 |
| robin |           20 |                 9 |
+-----------+--------------+-------------------+
2 rows in set (0.00 sec)

mysql> SELECT name FROM user WHERE length(name) <> char_length(name);
+-----------+
| name      |
+-----------+
| robin |
+-----------+
1 row in set (0.00 sec)
```

## 四 总结 ##

方法一中，`[\u0391-\uFFE5]` 匹配中文以外的字符。

方法二中，当字符集为UTF-8，并且字符为中文时，`length()` 和 `char_length()` 两个方法返回的结果不相同。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

参考官方文档：

>  **LENGTH()**
>  Return the length of a string in bytes
> Returns the length of the string str, measured in bytes. A multibyte character counts as multiple bytes. This means that for a string containing five 2-byte characters, LENGTH() returns 10, whereas CHAR_LENGTH() returns 5.

> **CHAR_LENGTH()**
> Return number of characters in argument
> Returns the length of the string str, measured in characters. A multibyte character counts as a single character. This means that for a string containing five 2-byte characters, LENGTH() returns 10, whereas CHAR_LENGTH() returns 5.

## 五 Ref ##

* <a href="http://dev.mysql.com/doc/refman/5.1/en/string-functions.html" target="_blank">12.5 String Functions</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
