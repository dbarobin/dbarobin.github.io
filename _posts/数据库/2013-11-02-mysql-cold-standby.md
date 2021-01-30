---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之冷备"
category: 数据库
summary: "用一句话概括冷备，就是把数据库服务，比如MySQL，Oracle停下来，然后使用拷贝、打包或者压缩命令对数据目录进行备份。如果数据出现异常，则可以通过备份数据恢复。冷备一般需要定制计划，比如什么时候做备份，每次对哪些数据进行备份等等。但是由于这样的备份占用过多的空间，对大数据量的环境下不一定适合，故生产环境很少使用。"
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

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 什么是冷备 ##

用一句话概括冷备，就是把数据库服务，比如MySQL，Oracle停下来，然后使用拷贝、打包或者压缩命令对数据目录进行备份。如果数据出现异常，则可以通过备份数据恢复。冷备一般需要定制计划，比如什么时候做备份，每次对哪些数据进行备份等等。但是由于这样的备份占用过多的空间，对大数据量的环境下不一定适合，故生产环境很少使用。

## 二 冷备示意图 ##

![冷备示意图](https://cdn.dbarobin.com/55Y66DN.jpg)

## 三 冷备实验 ##

第一步，创建测试数据库，插入测试数据。

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| access            |
+-------------------+
1 row in set (0.00 sec)

mysql> drop table access;
Query OK, 0 rows affected (0.00 sec)

mysql> clear
mysql> show tables;
Empty set (0.00 sec)

mysql>
mysql> create table class(
    -> cid int,
    -> cname varchar(30));
Query OK, 0 rows affected (0.01 sec)

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

mysql> create table stu(
    -> sid int,
    -> sname varchar(30),
    -> cid int) engine=myisam;
Query OK, 0 rows affected (0.00 sec)

mysql> show create table stu \G;
*************************** 1. row ***************************
       Table: stu
Create Table: CREATE TABLE `stu` (
  `sid` int(11) DEFAULT NULL,
  `sname` varchar(30) DEFAULT NULL,
  `cid` int(11) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> insert into class values(1,'linux'),(2,'oracle');
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> desc class;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| cid   | int(11)     | YES  |     | NULL    |       |
| cname | varchar(30) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> desc stu;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| sid   | int(11)     | YES  |     | NULL    |       |
| sname | varchar(30) | YES  |     | NULL    |       |
| cid   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into stu values(1,'larry01',1),(2,'larry02',2);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
```

第二步，停掉MySQL。

``` bash
/etc/init.d/mysqld stop
Shutting down MySQL... SUCCESS!
```

第三步，创建备份目录，并修改拥有者和所属组。

``` bash
mkdir /databackup
chown mysql.mysql /databackup/ -R
ll /databackup/ -d
cd /databackup/
```

第四步，冷备（使用tar命令）。

``` bash
cd /usr/local/mysql/data/
tar -cvPzf mysql01.tar.gz
```

第五步，测试冷备的数据是否正常，我们删除掉data下的所有数据。

``` bash
rm -rf /usr/local/mysql/data/*
```

第六步，删除所有数据后数据库不能启动。

``` bash
/etc/init.d/mysqld start
Starting MySQL.. ERROR! \
The server quit without updating PID file \
 (/usr/local/mysql/data/serv01.host.com.pid).
```

第七步，恢复数据（使用tar命令）。

``` bash
tar -xvPf mysql01.tar.gz
```

第八步，启动MySQL，然后登录MySQL，查看数据是否丢失，如果数据正常代表冷备成功。

``` bash
/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!

mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.5.29-log Source distribution
```

查看数据。

``` bash
mysql> use larrydb;
Database changed
mysql> select * from class;
+------+--------+
| cid  | cname  |
+------+--------+
|    1 | linux  |
|    2 | oracle |
+------+--------+
2 rows in set (0.00 sec)

mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/14053345" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之冷备" height="16px" width="16px" border="0" alt="MySQL备份与恢复之冷备" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
