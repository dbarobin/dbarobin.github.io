---
published: true
author: Robin Wen
layout: post
title: "MySQL事务中的元数据锁"
category: 数据库
summary: "MySQL 5.5中表的“元数据锁”一直到整个”事务”全部完成后才会释放，而5.1中，一个事务请求表的“元数据锁”直到“语句”执行完毕。这个特性的好处在于可以避免复制过程中日志顺序错误的问题。"
tags:
- 数据库
- Database
- MySQL
---

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

首先看两个例子:

``` bash
mysql -uroot -p
```

``` bash
mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 5.5.41    |
+-----------+
1 row in set (0.00 sec)

# Session 1
mysql> USE test;
Database changed
mysql> DROP TABLE t1;
ERROR 1051 (42S02): Unknown table 't1'
mysql> CREATE TABLE t1
    -> (id int auto_increment primary key,
    -> name varchar(20),
    -> password varchar(20),
    -> age int) ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.12 sec)

mysql> DROP TABLE t2;
ERROR 1051 (42S02): Unknown table 't5'
mysql> CREATE TABLE t2 
    -> (id int auto_increment primary key,
    -> name varchar(20),
    -> password varchar(20),
    -> age int) ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.09 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t2(name, password, age) VALUES('robin', '123456', '18');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT COUNT(*) FROM t1;
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT SLEEP(30);
+-----------+
| SLEEP(30) |
+-----------+
|         0 |
+-----------+
1 row in set (30.00 sec)

```

在Sleep中，打开另一个窗口，开始另一个会话。

``` bash
mysql -uroot -p
```

``` bash
# Session 2
mysql> USE test;

Database changed

mysql> DROP TABLE t1;
# 发生锁等待
```

Session 1 Sleep完成后，Commit。

``` bash
# Session 1
mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)
```

此时可以看到Session 2中的删表操作完成。

``` bash
mysql> DROP TABLE t1;
Query OK, 0 rows affected (24.17 sec)
```

同样，在MySQL 5.1中做相同的测试。
``` bash
/usr/local/mysql_5.1/bin/mysqld_multi \
--defaults-extra-file=/etc/my_mutli.cnf \
start 5173

mysql --socket=/tmp/mysql5173.sock -uroot -p
```

``` bash
mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 5.1.73    |
+-----------+
1 row in set (0.00 sec)

# Session 1
mysql> USE test;
Database changed
mysql> DROP TABLE t1;
ERROR 1051 (42S02): Unknown table 't1'
mysql> CREATE TABLE t1
    -> (id int auto_increment primary key,
    -> name varchar(20),
    -> password varchar(20),
    -> age int) ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.12 sec)

mysql> DROP TABLE t2;
ERROR 1051 (42S02): Unknown table 't5'
mysql> CREATE TABLE t2 
    -> (id int auto_increment primary key,
    -> name varchar(20),
    -> password varchar(20),
    -> age int) ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.09 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t2(name, password, age) VALUES('robin', '123456', '18');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT COUNT(*) FROM t1;
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT SLEEP(30);
+-----------+
| SLEEP(30) |
+-----------+
|         0 |
+-----------+
1 row in set (30.00 sec)

```

在Sleep中，打开另一个窗口，开始另一个会话。

``` bash
mysql --socket=/tmp/mysql5173.sock -uroot -p
```

``` bash
# Session 2
mysql> USE test;
Database changed

#  不会发生锁等待，直接删除。
mysql> DROP TABLE t1;
Query OK, 0 rows affected (0.00 sec)
```

Session 1 Sleep完成后，Commit。

``` bash
# Session 1
mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)
```

可以删除，MySQL 5.1和MySQL 5.5在元数据锁中的实现略有不同，5.1删表不会发生锁等待，而5.5会。

具体的原因，我查了下官方文档。

> To ensure transaction serializability, the server must not permit one session to perform a data definition language (DDL) statement on a table that is used in an uncompleted explicitly or implicitly started transaction in another session. The server achieves this by acquiring metadata locks on tables used within a transaction and deferring release of those locks until the transaction ends. A metadata lock on a table prevents changes to the table's structure. This locking approach has the implication that a table that is being used by a transaction within one session cannot be used in DDL statements by other sessions until the transaction ends.

也就是说，5.5中表的“元数据锁”一直到整个”事务”全部完成后才会释放，而5.1中，一个事务请求表的“元数据锁”直到“语句”执行完毕。这个特性的好处在于可以避免复制过程中日志顺序错误的问题。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**参考资料**

<a href="http://dev.mysql.com/doc/refman/5.5/en/metadata-locking.html" target="_blank">8.10.4 Metadata Locking</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
