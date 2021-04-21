---
published: true
author: Robin Wen
layout: post
title: "MySQL中同时存在创建和上次更新时间戳字段解决方法浅析"
category: 数据库
summary: "本文介绍的方法归根结底，就两条，**一是建表语句指定默认值和更新动作，二是使用触发器插入默认值和更新时间。**面对当前无法更改的事实，只能采取折中的办法或者牺牲更多来弥补。还有一条值得注意的是，遇到问题多想想不同的解决办法，尽可能地列出所有可能或者可行的方案，这样一来让自己学到更多，二来可以锻炼思维的广度，三来多种方案可以弥补某种方案在特定环境下不可行的不足。"
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

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 问题重现 ##

在写这篇文章之前，明确我的MySQL版本。

``` bash
mysql> SELECT VERSION();
+------------+
| VERSION()  |
+------------+
| 5.5.29-log |
+------------+
1 row in set (0.00 sec)
```

现在有这样的需求，一张表中有一个字段created_at记录创建该条记录的时间戳，另一个字段updated_at记录更新该条记录的时间戳。
我们尝试以下几个语句。

第一个，测试通过。

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    updated_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP \
    ON UPDATE CURRENT_TIMESTAMP
);
```

第二个，测试不通过。报ERROR 1293 (HY000)错误。（完整错误信息：ERROR 1293 (HY000): Incorrect table definition; there can be only one TIMESTAMP column with CURRENT_TIMESTAMP in DEFAULT or ON UPDATE clause）

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    created_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP \
    ON UPDATE CURRENT_TIMESTAMP
);
```

MySQL 5.5.29中有这样的奇葩限制，不明白为什么。既然有这样的限制，那么只有绕道而行，现在尝试给出如下几种解决办法。

## 解决方案一 ##

**第一种，created_at使用DEFAULT CURRENT_TIMESTAMP或者DEFAULT now()，updated_at使用触发器。**

具体解决方法如下：
1.temp表结构如下：

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    created_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NULL
);
```

2.插入测试数据：

``` bash
mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('robin',now(),now());
Query OK, 1 row affected (0.03 sec)

mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('wentasy',now(),now());
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM temp;
+----+---------+---------------------+---------------------+
| id | name    | created_at          | updated_at          |
+----+---------+---------------------+---------------------+
|  1 | robin   | 2014-09-01 14:00:39 | 2014-09-01 14:00:39 |
|  2 | wentasy | 2014-09-01 14:01:11 | 2014-09-01 14:01:11 |
+----+---------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

3.在temp上创建触发器，实现更新时记录更新时间；

``` bash
delimiter |
DROP TRIGGER IF EXISTS tri_temp_updated_at;
CREATE TRIGGER tri_temp_updated_at BEFORE UPDATE ON temp
FOR EACH ROW
BEGIN
    SET NEW.updated_at = now();
END;
|
delimiter ;
```

4.测试。

``` bash
mysql> UPDATE temp SET name='robinwen' WHERE id=1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

-- 可以看到已经记录了第一条数据的更新时间
mysql> SELECT * FROM temp;
+----+----------+---------------------+---------------------+
| id | name     | created_at          | updated_at          |
+----+----------+---------------------+---------------------+
|  1 | robinwen | 2014-09-01 14:00:39 | 2014-09-01 14:03:05 |
|  2 | wentasy  | 2014-09-01 14:01:11 | 2014-09-01 14:01:11 |
+----+----------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

## 解决方案二 ##

**第二种，created_at使用触发器，updated_at使用DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP或者DEFAULT now() ON UPDATE now()；**

具体解决方法如下：
1.temp表结构如下：

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    created_at timestamp NULL,
    updated_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP \
    ON UPDATE CURRENT_TIMESTAMP
);
```

2.在temp上创建触发器，实现插入数据记录创建时间；

``` bash
delimiter |
DROP TRIGGER IF EXISTS tri_temp_created_at;
CREATE TRIGGER tri_temp_created_at BEFORE INSERT ON temp
FOR EACH ROW
BEGIN
    IF new.created_at IS NULL
    THEN
        SET new.created_at=now();
    END IF;
END;
|
delimiter ;
```

3.插入测试数据：

``` bash
mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('robin',now(),now());
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('wentasy',now(),now());
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM temp;
+----+---------+---------------------+---------------------+
| id | name    | created_at          | updated_at          |
+----+---------+---------------------+---------------------+
|  1 | robin   | 2014-09-01 14:08:36 | 2014-09-01 14:08:36 |
|  2 | wentasy | 2014-09-01 14:08:44 | 2014-09-01 14:08:44 |
+----+---------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

4.测试。

``` bash
mysql> UPDATE temp SET name='robinwen' WHERE id=1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

-- 可以看到已经记录了第一条数据的更新时间
mysql> SELECT * FROM temp;
+----+----------+---------------------+---------------------+
| id | name     | created_at          | updated_at          |
+----+----------+---------------------+---------------------+
|  1 | robinwen | 2014-09-01 14:08:36 | 2014-09-01 14:09:09 |
|  2 | wentasy  | 2014-09-01 14:08:44 | 2014-09-01 14:08:44 |
+----+----------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

## 解决方案三 ##

**第三种，created_at指定timestamp DEFAULT '0000-00-00 00:00:00'，updated_at指定DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP或者timestamp DEFAULT now() ON UPDATE now()；**

具体解决方法如下：
1.temp表结构如下：

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    created_at timestamp NULL \
    DEFAULT '0000-00-00 00:00:00',
    updated_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP \
    ON UPDATE CURRENT_TIMESTAMP
);
```

2.插入测试数据：

``` bash
mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('robin',now(),now());
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO temp(name,created_at,updated_at) \
VALUES('wentasy',now(),now());
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM temp;
+----+---------+---------------------+---------------------+
| id | name    | created_at          | updated_at          |
+----+---------+---------------------+---------------------+
|  1 | robin   | 2014-09-01 14:10:43 | 2014-09-01 14:10:43 |
|  2 | wentasy | 2014-09-01 14:10:57 | 2014-09-01 14:10:57 |
+----+---------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

3.测试。

``` bash
mysql> UPDATE temp SET name='robinwen' WHERE id=1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

-- 可以看到已经记录了第一条数据的更新时间
mysql> SELECT * FROM temp;
+----+----------+---------------------+---------------------+
| id | name     | created_at          | updated_at          |
+----+----------+---------------------+---------------------+
|  1 | robinwen | 2014-09-01 14:10:43 | 2014-09-01 14:11:24 |
|  2 | wentasy  | 2014-09-01 14:10:57 | 2014-09-01 14:10:57 |
+----+----------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

## 解决方案四 ##

**第四种，更换MySQL版本，MySQL 5.6已经去除了此限制。**

我们可以看下MySQL 5.5和5.6帮助文档对于这个问题的解释。

**From the MySQL 5.5 documentation:**
One TIMESTAMP column in a table can have the current timestamp as the default value for initializing the column, as the auto-update value, or both. It is not possible to have the current timestamp be the default value for one column and the auto-update value for another column.
Changes in MySQL 5.6.5:
Previously, at most one TIMESTAMP column per table could be automatically initialized or updated to the current date and time. This restriction has been lifted. Any TIMESTAMP column definition can have any combination of DEFAULT CURRENT_TIMESTAMP and ON UPDATE CURRENT_TIMESTAMP clauses. In addition, these clauses now can be used with DATETIME column definitions. For more information, see Automatic Initialization and Updating for TIMESTAMP and DATETIME.

我们确定下MySQL的版本。

``` bash
mysql> SELECT VERSION();
+---------------------------------------+
| VERSION()                             |
+---------------------------------------+
| 5.6.20-enterprise-commercial-advanced |
+---------------------------------------+
1 row in set (0.00 sec)
```

我们把文首测试不通过的SQL语句在MySQL 5.6下执行，可以看到没有任何错误。

``` bash
CREATE TABLE temp
(
    id INT(11) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    created_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp NULL \
    DEFAULT CURRENT_TIMESTAMP \
    ON UPDATE CURRENT_TIMESTAMP
);
Query OK, 0 rows affected (0.28 sec)
```

接着我们插入测试语句，并作测试。

``` bash
mysql> INSERT INTO temp(name) VALUES('robin');
Query OK, 1 row affected (0.07 sec)

mysql> INSERT INTO temp(name) VALUES('wentasy');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM temp;
+----+---------+---------------------+---------------------+
| id | name    | created_at          | updated_at          |
+----+---------+---------------------+---------------------+
|  1 | robin   | 2014-09-01 15:05:57 | 2014-09-01 15:05:57 |
|  2 | wentasy | 2014-09-01 15:06:02 | 2014-09-01 15:06:02 |
+----+---------+---------------------+---------------------+
2 rows in set (0.01 sec)

mysql> UPDATE temp SET name='robinwen' WHERE id=1;
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

-- 可以看到已经记录了第一条数据的更新时间
mysql> SELECT * FROM temp;
+----+----------+---------------------+---------------------+
| id | name     | created_at          | updated_at          |
+----+----------+---------------------+---------------------+
|  1 | robinwen | 2014-09-01 15:05:57 | 2014-09-01 15:06:45 |
|  2 | wentasy  | 2014-09-01 15:06:02 | 2014-09-01 15:06:02 |
+----+----------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

## 总结 ##

本文介绍的方法归根结底，就两条，**一是建表语句指定默认值和更新动作，二是使用触发器插入默认值和更新时间。**面对当前无法更改的事实，只能采取折中的办法或者牺牲更多来弥补。还有一条值得注意的是，遇到问题多想想不同的解决办法，尽可能地列出所有可能或者可行的方案，这样一来让自己学到更多，二来可以锻炼思维的广度，三来多种方案可以弥补某种方案在特定环境下不可行的不足。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/38981477" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL中同时存在创建和上次更新时间戳字段解决方法浅析" height="16px" width="16px" border="0" alt="MySQL中同时存在创建和上次更新时间戳字段解决方法浅析" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
