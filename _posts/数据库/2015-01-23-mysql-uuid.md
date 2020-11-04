---
published: true
author: Robin Wen
layout: post
title: "MySQL UUID() 函数"
category: 数据库
summary: "MySQL 实现了 UUID，并且提供 UUID() 函数方便用户生成 UUID。在 MySQL 的 UUID() 函数中，前三组数字从时间戳中生成，第四组数字暂时保持时间戳的唯一性，第五组数字是一个 IEEE 802 节点标点值，保证空间唯一。使用 UUID() 函数，可以生成时间、空间上都独一无二的值。据说只要是使用了 UUID，都不可能看到两个重复的 UUID 值。当然，这个只是在理论情况下。"
tags:
- 数据库
- Database
- MySQL
- 复制
- Replication
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

## 一 引子 ##

在 MySQL 中，可以有如下几种途径实现唯一值：

* 自增序列
* UUID() 函数
* 程序自定义

UUID 基于 16 进制，由 32 位小写的 16 进制数字组成，如下：

> aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee

比如 `123e4567-e89b-12d3-a456-426655440000` 就是一个典型的 UUID。

MySQL 实现了 UUID，并且提供 UUID() 函数方便用户生成 UUID。在 MySQL 的 UUID() 函数中，前三组数字从时间戳中生成，第四组数字暂时保持时间戳的唯一性，第五组数字是一个 IEEE 802 节点标点值，保证空间唯一。使用 UUID() 函数，可以生成时间、空间上都独一无二的值。据说只要是使用了 UUID，都不可能看到两个重复的 UUID 值。当然，这个只是在理论情况下。

## 二 MySQL UUID() 函数 ##

``` bash
mysql -uroot -proot
```

``` bash
mysql> SHOW VARIABLES LIKE '%version%';
+-------------------------+------------------------------+
| Variable_name           | Value                        |
+-------------------------+------------------------------+
| innodb_version          | 5.5.40                       |
| protocol_version        | 10                           |
| slave_type_conversions  |                              |
| version                 | 5.5.40-log                   |
| version_comment         | MySQL Community Server (GPL) |
| version_compile_machine | x86_64                       |
| version_compile_os      | linux2.6                     |
+-------------------------+------------------------------+
7 rows in set (0.00 sec)

mysql> SELECT UUID(), UUID(), LENGTH(UUID()), CHAR_LENGTH(UUID()) \G
*************************** 1. row ***************************
             UUID(): 19a87b1a-a298-11e4-aa3c-08002735e4a4
             UUID(): 19a87b26-a298-11e4-aa3c-08002735e4a4
     LENGTH(UUID()): 36
CHAR_LENGTH(UUID()): 36
1 row in set (0.00 sec)

mysql> SELECT UUID(), UUID(), LENGTH(UUID()), CHAR_LENGTH(UUID()) \G
*************************** 1. row ***************************
             UUID(): 450e1572-a298-11e4-aa3c-08002735e4a4
             UUID(): 450e157c-a298-11e4-aa3c-08002735e4a4
     LENGTH(UUID()): 36
CHAR_LENGTH(UUID()): 36
1 row in set (0.00 sec)
```

可以看到，同一个 SQL 语句中，多处调用 UUID() 函数得到的值不相同。也就是说每次调用 UUD 函数都会生成一个唯一的值。并且多次调用或执行得到的后两组值相同。另外，本身 UUID 是 32 位，因为 MySQL 生成的 UUID 有四个中划线，所以在 utf8 字符集里，长度为 36 位。

我们关闭 MySQL，然后启动。

``` bash
/etc/init.d/mysqld stop
Shutting down MySQL.                                       [  OK  ]

/etc/init.d/mysqld start
Starting MySQL..                                           [  OK  ]
```

再次调用 UUID() 函数。

``` bash
mysql> SELECT UUID(), UUID(), LENGTH(UUID()), CHAR_LENGTH(UUID()) \G
*************************** 1. row ***************************
             UUID(): 586546b2-a298-11e4-b0fc-08002735e4a4
             UUID(): 586546c5-a298-11e4-b0fc-08002735e4a4
     LENGTH(UUID()): 36
CHAR_LENGTH(UUID()): 36
1 row in set (0.00 sec)
```

可以看到，第四组的值与重启之前发生变化，直到下一次重启 MySQL。

我们连接到另一台服务器，再次调用 UUID() 函数。

``` bash
mysql> SELECT UUID(), UUID(), LENGTH(UUID()), CHAR_LENGTH(UUID()) \G
*************************** 1. row ***************************
             UUID(): 8fa81275-a298-11e4-8302-0800276f77f9
             UUID(): 8fa81291-a298-11e4-8302-0800276f77f9
     LENGTH(UUID()): 36
CHAR_LENGTH(UUID()): 36
1 row in set (0.00 sec)
```

可以看到跟之前的数据不同，包括第五组数据。因为第五组的值跟机器相关，所以，同一台机器第五组值不变，不同机器则变。

## 三 复制中的 UUID() ##

### 3.1 实验环境介绍 ###

|----------+------------+-----------------+-----------|
| 主机           | IP地址             |主机名                      | 备注              |
|:----------:|:------------|:-----------------|:-----------:|
|rhel-01：   |10.10.2.231     | rhel-01  | master            |
|rhel-02：   |10.10.2.227     | rhel-02  | slave          |
|----------+------------+-----------------+-----------|

**操作系统版本：**RHEL 6.5 <br/>
**所需要的软件包：**mysql-5.5.40-linux2.6-x86_64.tar.gz

### 3.2 搭建复制环境 ###

在此不赘述，请参考：<a href="https://dbarobin.com/2013/10/27/mysql-replication/" target="_blank">MySQL AB 复制</a>

### 3.3 基于 STATEMENT 模式###

rhel-01 中做如下设置，设置为 STATEMENT 模式。

``` bash
mysql> SET tx_isolation="REPEATABLE-READ";
Query OK, 0 rows affected (0.00 sec)

mysql> SET binlog_format="STATEMENT";
Query OK, 0 rows affected (0.00 sec)
```

rhel-02 也做如下设置：

``` bash
mysql> SET tx_isolation="REPEATABLE-READ";
Query OK, 0 rows affected (0.00 sec)

mysql> SET binlog_format="STATEMENT";
Query OK, 0 rows affected (0.00 sec)
```

rhel-01 创建测试表，插入测试数据。在插入数据之后，还可以看到一个警告。

``` bash
mysql> USE test;
Database changed
mysql> CREATE TABLE user
    -> (name VARCHAR(36),
    -> en_name VARCHAR(20),
    -> job VARCHAR(10),
    -> addr VARCHAR(20)
    -> ) DEFAULT CHARSET=utf8 ENGINE=InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO user(name, en_name, job, addr) \
VALUES(UUID(), "robin", "dba", "GZ");
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> SHOW WARNINGS;
| Level | Code | Message |
+-------+------+-----------------------------------------------------+
| Note  | 1592 | Unsafe statement written to the binary log using statement \
format since BINLOG_FORMAT = STATEMENT. Statement is unsafe because \
it uses a system function that may return a different value on the slave. |
+-------+------+-----------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24d785a2-a29c-11e4-b0fc-08002735e4a4
en_name: robin
    job: dba
   addr: GZ
1 row in set (0.00 sec)
```

rhel-02 查看复制的数据。

``` bash
mysql> USE test;
Database changed

mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24cd38fe-a29c-11e4-8302-0800276f77f9
en_name: robin
    job: dba
   addr: GZ
1 row in set (0.00 sec)
```

可以看到，rhel-01 中的 UUID 值为`24d785a2-a29c-11e4-b0fc-08002735e4a4`，rhel-02 中的值为 `24cd38fe-a29c-11e4-8302-0800276f77f9`，两个值居然不相同，亦即主从不一致。那这样的复制是没有什么意义的。因为 UUID() 函数属于不确定函数，所以不支持 **STATEMENT** 模式。

### 3.4 基于 MIXED 模式###

rhel-01 中做如下设置，设置为 MIXED 模式。

``` bash
mysql> SET binlog_format="MIXED";
Query OK, 0 rows affected (0.00 sec)
```

rhel-02 中做如下设置：

``` bash
mysql> SET binlog_format="MIXED";
Query OK, 0 rows affected (0.00 sec)
```

rhel-01 插入测试数据。

``` bash
mysql> INSERT INTO user(name, en_name, job, addr) \
VALUES(UUID(), "Wentasy", "dba", "GZ");
Query OK, 1 row affected (0.06 sec)

mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24d785a2-a29c-11e4-b0fc-08002735e4a4
en_name: robin
    job: dba
   addr: GZ
*************************** 2. row ***************************
   name: 8dc2c93c-a29c-11e4-b0fc-08002735e4a4
en_name: Wentasy
    job: dba
   addr: GZ
2 rows in set (0.00 sec)
```

rhel-02 查看复制的数据。可以看到 **MIXED** 模式下，两台服务器的 UUID 相同，亦即主从一致。

``` bash
mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24cd38fe-a29c-11e4-8302-0800276f77f9
en_name: robin
    job: dba
   addr: GZ
*************************** 2. row ***************************
   name: 8dc2c93c-a29c-11e4-b0fc-08002735e4a4
en_name: Wentasy
    job: dba
   addr: GZ
2 rows in set (0.00 sec)
```

### 3.5 基于 ROW 模式###

rhel-01 中做如下设置，设置为 ROW 模式。

``` bash
mysql> SET binlog_format="ROW";
Query OK, 0 rows affected (0.00 sec)
```

rhel-02 也做如下设置，
``` bash
mysql> SET binlog_format="ROW";
Query OK, 0 rows affected (0.00 sec)
```

rhel-01 插入测试数据。

``` bash
mysql> INSERT INTO user(name, en_name, job, addr) \
VALUES(UUID(), "dbarobin", "dba", "GZ");
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24d785a2-a29c-11e4-b0fc-08002735e4a4
en_name: robin
    job: dba
   addr: GZ
*************************** 2. row ***************************
   name: 8dc2c93c-a29c-11e4-b0fc-08002735e4a4
en_name: Wentasy
    job: dba
   addr: GZ
*************************** 3. row ***************************
   name: d8123587-a29c-11e4-b0fc-08002735e4a4
en_name: dbarobin
    job: dba
   addr: GZ
3 rows in set (0.00 sec)
```

rhel-02 查看复制的测试数据。

``` bash
mysql> SELECT * FROM user \G
*************************** 1. row ***************************
   name: 24cd38fe-a29c-11e4-8302-0800276f77f9
en_name: robin
    job: dba
   addr: GZ
*************************** 2. row ***************************
   name: 8dc2c93c-a29c-11e4-b0fc-08002735e4a4
en_name: Wentasy
    job: dba
   addr: GZ
*************************** 3. row ***************************
   name: d8123587-a29c-11e4-b0fc-08002735e4a4
en_name: dbarobin
    job: dba
   addr: GZ
3 rows in set (0.00 sec)
```

可以看到，在 **ROW** 模式下，复制的数据和主服务器相同，亦即主从一致。

## 四 UUID_SHORT() 函数##

在 MySQL 5.1 之后的版本，提供 UUID_SHORT() 函数，生成一个 64 位无符号整数。另外，需要注意的是，`server_id` 的范围必须为 0-255，并且不支持 STATEMENT 模式复制。

``` bash
mysql> SELECT UUID_SHORT();
+-------------------+
| UUID_SHORT()      |
+-------------------+
| 95914352036544514 |
+-------------------+
1 row in set (0.00 sec)
```

## 五 小结##

* 同一个 SQL 语句中，多处调用 UUID() 函数得到的值不相同，多次调用或执行得到的后两组值相同。
* 同一台服务器，重启 MySQL 前后的 UUID() 第四组值发生变化，第五组值不变；
* MySQL 中，utf8 字符集下，生成的 UUID 长度为 36 位；
* 不同机器生成的 UUID 不同，包括第五组值；
* 在复制环境中，使用到 UUID() 函数，则一定要使用基于行或者基于混合模式复制方式。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 六 Ref ##

* <a href="http://dev.mysql.com/doc/refman/5.5/en/miscellaneous-functions.html#function_uuid" target="_blank">UUID()</a><br/>
* <a href="http://en.wikipedia.org/wiki/Universally_unique_identifier" target="_blank">Universally unique identifier</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
