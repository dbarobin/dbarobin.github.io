---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之热备"
category: 数据库
summary: "在上两篇文章（MySQL备份与恢复之冷备，MySQL备份与恢复之真实环境使用冷备）中，我们提到了冷备和真实环境中使用冷备。那从这篇文章开始我们看下热备。显然热备和冷备是两个相对的概念，冷备是把数据库服务，比如MySQL，Oracle停下来，然后使用拷贝、打包或者压缩命令对数据目录进行备份；那么我们很容易想到热备就是在MySQL或者其他数据库服务在运行的情况下进行备份。"
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

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 热备 ##

在上两篇文章（<a href="https://dbarobin.com/2013/11/02/mysql-cold-standby/" target="_blank">MySQL备份与恢复之冷备</a>，<a href="https://dbarobin.com/2013/11/03/mysql-cold-standby-in-production-environment/" target="_blank">MySQL备份与恢复之真实环境使用冷备</a>）中，我们提到了冷备和真实环境中使用冷备。那从这篇文章开始我们看下热备。显然热备和冷备是两个相对的概念，冷备是把数据库服务，比如MySQL，Oracle停下来，然后使用拷贝、打包或者压缩命令对数据目录进行备份；那么我们很容易想到热备就是在MySQL或者其他数据库服务在运行的情况下进行备份。但是，这里存在一个问题，因为生产库在运行的情况下，有对该库的读写，读写频率有可能高，也可能低，不管频率高低，总会就会造成备份出来的数据和生产库中的数据不一致的情况。热备这段时间，其他人不可以操作是不现实的，因为你总不可能终止用户访问Web程序。要解决这个问题，可以采用指定备份策略，比如哪个时间段进行备份，备份哪些数据等等，总之，保证数据的完整性和一致性，切记，**备份重于一切！！！**

热备采用的是使用mysqldump命令进行备份，此工具是MySQL内置的备份和恢复工具，功能强大，它可以对整个库进行备份，可以对多个库进行备份，可以对单张表或者某几张表进行备份。但是无法同时备份多个库多个表，只有分开备份。下面我们看下热备的示意图，并进行热备模拟。

## 二 示意图 ##

![MySQL备份与恢复之热备示意图](https://cdn.dbarobin.com/UbrApUu.jpg)

## 三 热备模拟 ##

**对单个库进行备份**

第一步，移除LVM快照。（**如果没有创建，忽略此步**）

``` bash
lvremove /dev/data/smydata
Do you really want to remove active logical volume smydata? [y/n]: y
  Logical volume "smydata" successfully removed
```

第二步，设置MySQL的密码。

``` bash
mysql> set password=password("123456");
Query OK, 0 rows affected (0.00 sec)
```

第三步，查看MySQL是否启动。因为是热备，所以要求MySQL服务启动。

``` bash
/etc/init.d/mysqld status
 SUCCESS! MySQL running (2664)
```


第四步，导出单个数据库。

``` bash
cd /databackup/

# 本质是导出为SQL
mysqldump -uroot -p123456 --database larrydb
```

脚本内容如下：

``` bash
-- MySQL dump 10.13  Distrib 5.5.29, for Linux (x86_64)
--
-- Host: localhost    Database: larrydb
-- ------------------------------------------------------
-- Server version 5.5.29-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Current Database: `larrydb`
--

CREATE DATABASE /*!32312 IF NOT EXISTS*/ `larrydb`  \
/*!40100 DEFAULT CHARACTER SET latin1 */;

USE `larrydb`;

--
-- Table structure for table `class`
--

DROP TABLE IF EXISTS `class`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `class` (
  `cid` int(11) DEFAULT NULL,
  `cname` varchar(30) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `class`
--

LOCK TABLES `class` WRITE;
/*!40000 ALTER TABLE `class` DISABLE KEYS */;
INSERT INTO `class` VALUES (1,'linux'),(2,'oracle');
/*!40000 ALTER TABLE `class` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `stu`
--

DROP TABLE IF EXISTS `stu`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `stu` (
  `sid` int(11) DEFAULT NULL,
  `sname` varchar(30) DEFAULT NULL,
  `cid` int(11) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `stu`
--

LOCK TABLES `stu` WRITE;
/*!40000 ALTER TABLE `stu` DISABLE KEYS */;
INSERT INTO `stu` VALUES (1,'larry01',1),(2,'larry02',2);
/*!40000 ALTER TABLE `stu` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

  Dump completed on 2013-09-10 18:56:06
```

将输出结果保存到文件中。

``` bash
mysqldump -uroot -p123456 --database larrydb > larrydb.sql
```

第五步，模拟数据丢失，进入MySQL，删除数据库。

``` bash
mysql -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.5.29-log Source distribution

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| crm                |
| game               |
| hello              |
| larrydb            |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
9 rows in set (0.00 sec)

mysql> drop database larrydb;
Query OK, 2 rows affected (0.01 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| crm                |
| game               |
| hello              |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
8 rows in set (0.00 sec)

mysql> exit
Bye
```
[root@serv01 data]#

第六步，导入数据。

``` bash
mysql -uroot -p123456 <larrydb.sql
```

第七步，登录MySQL，查看数据是否正常。
``` bash
mysql -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.5.29-log Source distribution

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| crm                |
| game               |
| hello              |
| larrydb            |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
9 rows in set (0.00 sec)

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

**对多个库进行备份**

第一步，查看有哪些数据库。

``` bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| crm                |
| game               |
| hello              |
| larrydb            |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
9 rows in set (0.00 sec)
mysql> use game;
Database changed
mysql> show tables;
+----------------+
| Tables_in_game |
+----------------+
| country        |
| fight          |
| hero           |
+----------------+
3 rows in set (0.00 sec)

mysql> select * from country;
+-----+---------+----------+
| cno | cname   | location |
+-----+---------+----------+
|  10 | caowei  | luoyang  |
|  20 | shuhan  | chengdou |
|  30 | sunwu   | nanjing  |
|  40 | houhan  | luoyang  |
|  50 | beisong | kaifeng  |
|  60 | 魏国    | 洛阳     |
+-----+---------+----------+
6 rows in set (0.00 sec)
```

第二步，备份多个库。

``` bash
mysqldump -uroot -p123456 --databases larrydb game > larrydb_game.sql
ll larrydb_game.sql
```

第三步，模拟数据丢失。

``` bash
mysql> drop database game;
Query OK, 3 rows affected (0.01 sec)

mysql> drop database larrydb;
Query OK, 2 rows affected (0.00 sec)
mysql> use crm;
Database changed
mysql> show tables;
+---------------+
| Tables_in_crm |
+---------------+
| test          |
+---------------+
1 row in set (0.00 sec)

mysql> select * from test;
Empty set (0.00 sec)

mysql> drop database crm;
Query OK, 1 row affected (0.00 sec)
```

第四步，恢复数据。

``` bash
mysql -uroot -p123456 < larrydb_game.sql
```

第五步，查看数据是否正常。

``` bash
mysql -uroot -p123456
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 5.5.29-log Source distribution

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| game               |
| hello              |
| larrydb            |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
8 rows in set (0.00 sec)

mysql> use game;
Database changed
mysql> select * from country;
+-----+---------+----------+
| cno | cname   | location |
+-----+---------+----------+
|  10 | caowei  | luoyang  |
|  20 | shuhan  | chengdou |
|  30 | sunwu   | nanjing  |
|  40 | houhan  | luoyang  |
|  50 | beisong | kaifeng  |
|  60 | 魏国    | 洛阳     |
+-----+---------+----------+
6 rows in set (0.00 sec)

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
```

**备份所有的库**

``` bash
mysqldump --help | grep all-database
OR     mysqldump [OPTIONS] --all-databases [OPTIONS]
  -A, --all-databases Dump all the databases. This will be same as --databases
                      --databases= or --all-databases), the logs will be
                      --all-databases or --databases is given.
all-databases                     FALSE

mysqldump -uroot -p123456 --all-databases > all_databases.sql

ll all_databases.sql -h
```

**备份某张表或者某几张表**

第一步，备份某张表和某几张表

``` bash
mysqldump game hero country -uroot -p123456 > game_hero_country.sql
ll game_hero_country.sql
```

第二步，模拟数据丢失。

``` bash
mysql> use game;
Database changed
mysql> show tables;
+----------------+
| Tables_in_game |
+----------------+
| country        |
| fight          |
| hero           |
+----------------+
3 rows in set (0.00 sec)

mysql> drop table hero;
Query OK, 0 rows affected (0.00 sec)

mysql> drop table country;
Query OK, 0 rows affected (0.00 sec)
```

第三步，查看数据是否正常。

``` bash
mysql -uroot -p123456 --database game < game_hero_country.sql

mysql -uroot -p123456 -e "select * from game.country"
+-----+---------+----------+
| cno | cname   | location |
+-----+---------+----------+
|  10 | caowei  | luoyang  |
|  20 | shuhan  | chengdou |
|  30 | sunwu   | nanjing  |
|  40 | houhan  | luoyang  |
|  50 | beisong | kaifeng  |
|  60 | 魏国    | 洛阳     |
+-----+---------+----------+
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/14644549" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之热备" height="16px" width="16px" border="0" alt="MySQL备份与恢复之热备" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
