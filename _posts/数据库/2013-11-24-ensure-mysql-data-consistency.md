---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之保证数据一致性"
category: 数据库
summary: "在上一篇文章中我们提到热拷贝（MySQL备份与恢复之热拷贝），热拷贝也就是在MySQL或者其他数据库服务在运行的情况下使用mysqlhotcopy命令进行备份。这篇文章我们讲解怎样保证数据一致性。现在假设有这样一种情况，我们总是在凌晨对数据库进行备份，假设在凌晨之后发生数据库异常，并且导致数据丢失。这样凌晨之前的数据我们已经做了备份，但是凌晨到发生异常这段时间的数据就会丢失（没有binlog的情况下）。好在InnoDB存储引擎支持事务，也支持Binlog，凌晨到发生异常这段时间的数据就可以通过日志文件进行备份。所以，日志文件是非常重要，非常关键的。我们备份不仅要对数据进行备份，如果条件允许还需要对二进制文件进行备份。当然备份好数据之后，可以清空二进制文件，但如果为了长远考虑，比如恢复出来的数据并不是我们想要的，我们就需要备份二进制文件了。还有一点切记，恢复数据需要转到测试数据库中做，不要在生产环境中做。待测试库中测试没有问题，再在生产环境中做。"
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

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 数据一致性 ##

在上一篇文章中我们提到热拷贝（<a href="https://dbarobin.com/2013/11/10/mysql-hot-copy/" target="_blank">MySQL备份与恢复之热拷贝</a>），热拷贝也就是在MySQL或者其他数据库服务在运行的情况下使用mysqlhotcopy命令进行备份。这篇文章我们讲解怎样保证数据一致性。现在假设有这样一种情况，我们总是在凌晨对数据库进行备份，假设在凌晨之后发生数据库异常，并且导致数据丢失。这样凌晨之前的数据我们已经做了备份，但是凌晨到发生异常这段时间的数据就会丢失（没有binlog的情况下）。好在InnoDB存储引擎支持事务，也支持Binlog，凌晨到发生异常这段时间的数据就可以通过日志文件进行备份。所以，日志文件是非常重要，非常关键的。我们备份不仅要对数据进行备份，如果条件允许还需要对二进制文件进行备份。当然备份好数据之后，可以清空二进制文件，但如果为了长远考虑，比如恢复出来的数据并不是我们想要的，我们就需要备份二进制文件了。还有一点切记，恢复数据需要转到测试数据库中做，不要在生产环境中做。待测试库中测试没有问题，再在生产环境中做。

## 二 示意图 ##

![MySQL备份与恢复之保证数据一致性示意图](https://cdn.dbarobin.com/qeulS0W.jpg)

## 三 保证数据一致性模拟 ##

第一步，验证数据。

``` bash
rm -rf *
ls
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

第二步，备份数据。

``` bash
mysqldump -uroot -p123456 --database larrydb > larrydb.sql
ll larrydb.sql
```

第三步，清空日志，因为已经做了备份，所以不需要以前的日志。

``` bash
mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     27320 |
| mysql-bin.000002 |   1035309 |
| mysql-bin.000003 |      1010 |
| mysql-bin.000004 |     22809 |
| mysql-bin.000005 |      9860 |
| mysql-bin.000006 |      5659 |
| mysql-bin.000007 |       126 |
| mysql-bin.000008 |     10087 |
| mysql-bin.000009 |      8293 |
| mysql-bin.000010 |       476 |
| mysql-bin.000011 |       218 |
| mysql-bin.000012 |       126 |
| mysql-bin.000013 |      1113 |
| mysql-bin.000014 |      1171 |
| mysql-bin.000015 |       126 |
| mysql-bin.000016 |       107 |
| mysql-bin.000017 |       107 |
| mysql-bin.000018 |     13085 |
+------------------+-----------+
18 rows in set (0.00 sec)

mysql> reset master;
Query OK, 0 rows affected (0.01 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       107 |
+------------------+-----------+
1 row in set (0.00 sec)
```


第四步，更新数据。

``` bash
mysql> insert into class values(3,'Devel');
Query OK, 1 row affected (0.01 sec)

mysql> update class set cname="dab" where cid=2;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

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
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)

mysql> delete from stu where cid=2;
Query OK, 1 row affected (0.00 sec)

mysql> update stu set sname="larry007" where sid=1;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
+------+----------+------+
1 row in set (0.00 sec)
```

记录当前时间。
``` bash
date
Tue Sep 10 19:38:24 CST 2013
```

第五步，模拟数据丢失，删除库。

``` bash
rm -rf /usr/local/mysql/data/larrydb/
```

``` bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| game               |
| hello              |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
7 rows in set (0.00 sec)
```

``` bash
cd /usr/local/mysql/data/

# 可以使用mysqlbinlog命令查看日志文件
mysqlbinlog mysql-bin.000001
```

``` bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| game               |
| hello              |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
7 rows in set (0.00 sec)

mysql> drop database larrydb;
Query OK, 0 rows affected (0.01 sec)
```

第六步，导入更新之前的数据。

``` bash
mysql -uroot -p123456 < larrydb.sql
```

``` bash
mysql> use larrydb;
Database changed
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

第七步，根据日志恢复数据。

``` bash
mysqlbinlog --stop-datetime "2013-09-10 19:37:45" \
mysql-bin.000001 | mysql -uroot -p123456
```

``` bash
mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
+------+---------+------+
1 row in set (0.00 sec)

mysql> select * from class;
+------+-------+
| cid  | cname |
+------+-------+
|    1 | linux |
|    2 | dab   |
|    3 | Devel |
+------+-------+
3 rows in set (0.00 sec)
```

一般规律：恢复的时间点（或者是Commit之后的那个时间点）是发生事故的那个点再加上一秒。

``` bash
mysqlbinlog --stop-datetime "2013-09-10 19:37:46" \
mysql-bin.000001 | mysql -uroot -p123456
```

``` bash
mysql> select * from stu;
+------+----------+------+
| sid  | sname    | cid  |
+------+----------+------+
|    1 | larry007 |    1 |
+------+----------+------+
1 row in set (0.00 sec)

mysql> select * from class;
+------+-------+
| cid  | cname |
+------+-------+
|    1 | linux |
|    2 | dab   |
|    3 | Devel |
|    3 | Devel |
+------+-------+
4 rows in set (0.00 sec)
```

查看日志文件内容。

``` bash
mysqlbinlog mysql-bin.000001
```

``` bash
# at 7131
#130910 19:37:45 server id 1  end_log_pos 7240
Query thread_id=20  exec_time=996 error_code=0
SET TIMESTAMP=1378813065/*!*/;
update stu set sname="larry007" where sid=1
/*!*/;
# at 7240
#130910 19:37:45 server id 1  end_log_pos 7312
Query thread_id=20  exec_time=996 error_code=0
SET TIMESTAMP=1378813065/*!*/;
COMMIT
/*!*/;
DELIMITER ;
# End of log file
ROLLBACK /* added by mysqlbinlog */;
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/16916831" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之保证数据一致性" height="16px" width="16px" border="0" alt="MySQL备份与恢复之保证数据一致性" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
