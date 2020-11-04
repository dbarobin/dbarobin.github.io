---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之热拷贝"
category: 数据库
summary: "在上一篇文章中我们提到热备，热备也就是在MySQL或者其他数据库服务在运行的情况下进行备份。本文分享另外一种备份的方法，也就是热拷贝。热拷贝跟热备很类似，只不过热备使用mysqldump命令，热拷贝使用mysqlhotcopy命令。热拷贝的优势在于支持服务运行中进行备份，速度快，性能好；劣势在于只能备份MyIsam的表，无法备份InnoDB的表。所以在生产环境中应该酌情使用。"
tags:
- 数据库
- Database
- MySQL
- 安全
- Security
- 数据安全
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

## 一 热拷贝 ##

在上一篇文章中我们提到热备，热备也就是在MySQL或者其他数据库服务在运行的情况下进行备份。本文分享另外一种备份的方法，也就是热拷贝。热拷贝跟热备很类似，只不过热备使用mysqldump命令，热拷贝使用mysqlhotcopy命令。热拷贝的优势在于支持服务运行中进行备份，速度快，性能好；劣势在于只能备份MyIsam的表，无法备份InnoDB的表。所以在生产环境中应该酌情使用。

## 二 示意图 ##

![MySQL备份与恢复之热拷贝示意图](https://cdn.dbarobin.com/CUdQKmX.jpg)

## 三 热拷贝模拟 ##

第一步，热拷贝。

``` bash
mysqlhotcopy -uroot -p123456 --database larrydb > larrydb_hostcopy.sql
```

第二步，报错。因为这个命令是用perl写的或者此命令需要perl支持，所以需要perl支持。
如果出现如下错误：

> Can't locate DBI.pm in @INC (@INC contains: /usr/local/lib64/perl5
> /usr/local/share/perl5 /usr/lib64/perl5/vendor_perl
> /usr/share/perl5/vendor_perl /usr/lib64/perl5 /usr/share/perl5 .)
> at /usr/local/mysql/bin/mysqlhotcopy line 25.
> BEGIN failed--compilation aborted at /usr/local/mysql/bin/mysqlhotcopy line 25.

那么安装perl相关包。

``` bash
yum install perl* -y
```

第三步，对数据库larrydb热拷贝。

``` bash
mysqlhotcopy --help

# 第一种写法
mysqlhotcopy --user=root --password=123456 larrydb /databackup/

# 第二种写法
mysqlhotcopy -u root -p 123456 larrydb /databackup/
Flushed 2 tables with read lock (`larrydb`.`class`, `larrydb`.`stu`) in 0 seconds.
Locked 0 views () in 0 seconds.
Copying 5 files...
Copying indices for 0 files...
Unlocked tables.
mysqlhotcopy copied 2 tables (5 files) in 0 seconds (0 seconds overall).
```

第四步，模拟数据丢失。

``` bash
ll larrydb
```

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| class             |
| stu               |
+-------------------+
2 rows in set (0.00 sec)

mysql> show create table class \G;
*************************** 1. row ***************************
       Table: class
Create Table: CREATE TABLE `class` (
  `cid` int(11) DEFAULT NULL,
  `cname` varchar(30) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> show create table stu \G;
*************************** 1. row ***************************
       Table: stu
Create Table: CREATE TABLE `stu` (
  `sid` int(11) DEFAULT NULL,
  `sname` varchar(30) DEFAULT NULL,
  `cid` int(11) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1
1 row in set (0.00 sec)

ERROR:

mysql> drop table class,stu;
Query OK, 0 rows affected (0.01 sec)

mysql> show tables;
Empty set (0.00 sec)
```

**注意：这样删除会出错，不要这样删除**

``` bash
rm -rf /usr/local/mysql/data/larrydb/*
rm -rf /usr/local/mysql/data/larrydb/
```

第五步，恢复数据。

``` bash
cp larrydb /usr/local/mysql/data/ -arvf
`larrydb' -> `/usr/local/mysql/data/larrydb'
`larrydb/stu.MYI' -> `/usr/local/mysql/data/larrydb/stu.MYI'
`larrydb/stu.MYD' -> `/usr/local/mysql/data/larrydb/stu.MYD'
`larrydb/stu.frm' -> `/usr/local/mysql/data/larrydb/stu.frm'
`larrydb/db.opt' -> `/usr/local/mysql/data/larrydb/db.opt'
`larrydb/class.frm' -> `/usr/local/mysql/data/larrydb/class.frm'
```

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| class             |
| stu               |
+-------------------+
2 rows in set (0.00 sec)

mysql> select * from class;
ERROR 1146 (42S02): Table 'larrydb.class' doesn't exist
mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)

mysql> drop database larrydb;
Query OK, 2 rows affected (0.00 sec)
```

再次导入。

``` bash
mysql -uroot -p123456 < larrydb.sql
```

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| class             |
| stu               |
+-------------------+
2 rows in set (0.00 sec)

mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)

mysql> select * from class;
+------+--------+
| cid  | cname  |
+------+--------+
|    1 | linux  |
|    2 | oracle |
+------+--------+
2 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/15026833" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之热拷贝" height="16px" width="16px" border="0" alt="MySQL备份与恢复之热拷贝" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
