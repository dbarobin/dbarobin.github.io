---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之percona-xtrabackup实现增量备份及恢复"
category: 数据库
summary: "在上一篇文章，我们讲到 percona-xtrabackup软件的使用，这一篇文章我们讲解percona-xtrabackup实现增量备份及恢复。"
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

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 文章回顾 ##

在上一篇文章，我们讲到<a href="https://dbarobin.com/2013/12/01/the-usage-of-percona-xtrabackup/" target="_blank">percona-xtrabackup软件的使用</a>，这一篇文章我们讲解percona-xtrabackup实现增量备份及恢复。

## 二 增量备份示意图 ##

![MySQL备份与恢复之percona-xtrabackup实现增量备份及恢复示意图 ](https://cdn.dbarobin.com/FJfvnLm.jpg)

## 三 percona-xtrabackup实现增量备份及恢复原理 ##

首先，使用percona-xtrabackup工具对数据库进行全备，然后再每次数据库的数据更新后对数据进行增量备份，每次增量备份均在上一次备份的基础上。恢复时依次把每次增量备份的数据恢复到全备中，最后使用合并的数据进行数据恢复。

## 四 percona-xtrabackup实现增量备份及恢复 ##

第一步，全备。

``` bash
innobackupex --user=root --password=123456 /databackup/
```

第二步，查看数据。

``` bash
mysql> use larrydb;
Database changed
mysql> select * from class;
+------+-------+
| cid  | cname |
+------+-------+
|    1 | linux |
|    2 | dab   |
|    3 | Devel |
+------+-------+
3 rows in set (0.00 sec)

mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
+------+----------+------+
1 row in set (0.00 sec)
```

第三步，更新数据。

``` bash
mysql> insert into stu values(2,'larry02',1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
|    2 | larry02  |    1 |
+------+----------+------+
2 rows in set (0.00 sec)
```

第四步，增量备份，进行了全备和第一次增量备份，所以有两个备份文件夹。我们每次增量备份都是针对上一次备份。

``` bash
# --incremental：增量备份的文件夹
# --incremental-dir：针对哪个做增量备份
innobackupex --user=root --password=123456 \
--incremental /databackup/ \
--incremental-dir /databackup/2013-09-10_22-12-50/

InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.
……
innobackupex: Backup created in directory '/databackup/2013-09-10_22-15-45'
innobackupex: MySQL binlog position: filename 'mysql-bin.000004', position 353
130910 22:16:04  innobackupex: completed OK!

ls
2013-09-10_22-12-50
2013-09-10_22-15-45
```

第五步，再次插入数据。

``` bash
mysql> insert into stu values(3,'larry03',1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
|    2 | larry02  |    1 |
|    3 | larry03  |    1 |
+------+----------+------+
3 rows in set (0.00 sec)
```

第六步，再次增量备份。

``` bash
ls
2013-09-10_22-12-50
2013-09-10_22-15-45

innobackupex --user=root --password=123456 \
--incremental /databackup/ \
--incremental-dir /databackup/2013-09-10_22-15-45/
```

第七步，再次插入数据。

``` bash
mysql> insert into stu values(4,'larry04',1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
|    2 | larry02  |    1 |
|    3 | larry03  |    1 |
|    4 | larry04  |    1 |
+------+----------+------+
4 rows in set (0.00 sec)
```

第八步，再次增量备份。一次全备，三次增量备份，所以有四个备份文件夹。

``` bash
innobackupex --user=root --password=123456 \
--incremental /databackup/ \
--incremental-dir /databackup/2013-09-10_22-19-21/

ls
2013-09-10_22-12-50
2013-09-10_22-15-45
2013-09-10_22-19-21
2013-09-10_22-21-42
```

第九步，模拟数据丢失。

``` bash
mysql> drop database larrydb;
Query OK, 2 rows affected (0.02 sec)
```

第十步，对全部的数据进行检查。可以看到增量备份和全备的文件占用磁盘大小有很大的差别，显然全备占用磁盘空间多，增量备份占用磁盘空间少。

``` bash
innobackupex --apply-log --redo-only /databackup/2013-09-10_22-12-50/

InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.
……
xtrabackup: starting shutdown with innodb_fast_shutdown = 1
130910 22:23:35  InnoDB: Starting shutdown...
130910 22:23:36  InnoDB: Shutdown completed; log sequence number 2098700
130910 22:23:36  innobackupex: completed OK!

du -sh ./*
22M ./2013-09-10_22-12-50
1.5M  ./2013-09-10_22-15-45
1.5M  ./2013-09-10_22-19-21
1.5M  ./2013-09-10_22-21-42
```

第十一步，对第一次做的增量备份数据进行合并到全备份中去。

``` bash
innobackupex --apply-log --redo-only \
--incremental /databackup/2013-09-10_22-12-50/ \
--incremental-dir=/databackup/2013-09-10_22-15-45/

InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.
……
innobackupex: Copying '/databackup/2013-09-10_22-15-45/hello/db.opt' to
'/databackup/2013-09-10_22-12-50/hello/db.opt'
130910 22:32:26  innobackupex: completed OK!
```

第十二步，对第二次做的增量备份数据进行合并到全备份中去

``` bash
innobackupex --apply-log --redo-only \
--incremental /databackup/2013-09-10_22-12-50/ \
--incremental-dir=/databackup/2013-09-10_22-19-21/
```

第十三步，对第三次做的增量备份数据进行合并到全备份中去

``` bash
innobackupex --apply-log --redo-only \
--incremental /databackup/2013-09-10_22-12-50/ \
--incremental-dir=/databackup/2013-09-10_22-21-42/
```

第十四步，恢复时需要停掉MySQL，所以我们停掉MySQL

``` bash
/etc/init.d/mysqld stop
 ERROR! MySQL server PID file could not be found!

pkill -9 mysql
```

第十五步，恢复数据。注意这里指定的文件夹是2013-09-10_22-12-50。

``` bash
innobackupex --copy-back /databackup/2013-09-10_22-12-50/

InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.

This software is published under
the GNU GENERAL PUBLIC LICENSE Version 2, June 1991.

IMPORTANT: Please check that the copy-back run completes successfully.
           At the end of a successful copy-back run innobackupex
           prints "completed OK!".

Original data directory is not empty! at /usr/bin/innobackupex line 571.
```

报以上错需要删除数据目录下的东西。

``` bash
pwd
/usr/local/mysql/data

ls
rm -rf  *
```

再次恢复数据，并更改数据库数据目录的拥有者和所属组。

``` bash
innobackupex --copy-back /databackup/2013-09-10_22-12-50/

ll
chown mysql.mysql /usr/local/mysql/data/ -R
```

第十六步，启动服务。

``` bash
/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!
```

第十七步，登录数据库，然后查看数据。

``` bash
mysql -uroot -p123456
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

mysql> select * from larrydb.class;
+------+-------+
| cid  | cname |
+------+-------+
|    1 | linux |
|    2 | dab   |
|    3 | Devel |
+------+-------+
3 rows in set (0.00 sec)

mysql> select * from larrydb.stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
|    2 | larry02  |    1 |
|    3 | larry03  |    1 |
|    4 | larry04  |    1 |
+------+----------+------+
4 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/17054667" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之percona-xtrabackup实现增量备份及恢复" height="16px" width="16px" border="0" alt="MySQL备份与恢复之percona-xtrabackup实现增量备份及恢复" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
