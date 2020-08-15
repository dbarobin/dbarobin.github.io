---
published: true
author: Robin Wen
layout: post
title: MySQL Drop 大表解决方案
category: 数据库
summary: 本文介绍一个快速 `DROP TABLE` 的方法。使用本文提供的方法，不管该表数据量、占用空间有多大，都可以快速的删除。
tags:
  - 数据库
  - Database
  - MySQL
comments:
  - author:
      type: twitter
      displayName: RobberPhex
      url: 'https://twitter.com/RobberPhex'
      picture: >-
        https://pbs.twimg.com/profile_images/3580866058/1bb53ec18c97f6a3598af06c6c41224b_bigger.jpeg
    content: >-
      &#x672C;&#x6765;&#x5220;&#x9664;&#x6587;&#x4EF6;&#x9700;&#x8981;&#x64CD;&#x4F5C;&#x7CFB;&#x7EDF;&#x5728;&#x786C;&#x76D8;&#x5199;&#x591A;&#x6B21;&#xFF0C;&#x6807;&#x8BB0;&#x591A;&#x4E2A;&#x5757;&#x88AB;&#x5220;&#x9664;&#xFF0C;&#x7C7B;&#x4F3C;gc&#x3002;


      &#x521B;&#x5EFA;&#x4E86;&#x786C;&#x94FE;&#x63A5;&#x4E4B;&#x540E;&#xFF0C;&#x5220;&#x9664;&#x64CD;&#x4F5C;&#x4EC5;&#x4EC5;&#x662F;&#x5F15;&#x7528;&#x51CF;&#x4E00;&#xFF0C;&#x4F46;&#x662F;&#x90A3;&#x4E2A;&#x786C;&#x94FE;&#x63A5;&#x5220;&#x9664;&#x7684;&#x65F6;&#x5019;&#x8FD8;&#x662F;&#x8981;gc&#x3002;&#x53EF;&#x4EE5;&#x8C03;&#x5230;&#x4E1A;&#x52A1;&#x4F4E;&#x5CF0;&#x671F;&#x6765;&#x505A;&#x3002;
    date: 2017-02-12T03:06:17.831Z
  - author:
      type: twitter
      displayName: RobberPhex
      url: 'https://twitter.com/RobberPhex'
      picture: >-
        https://pbs.twimg.com/profile_images/3580866058/1bb53ec18c97f6a3598af06c6c41224b_bigger.jpeg
    content: >-
      &#x672C;&#x6765;&#x5220;&#x9664;&#x6587;&#x4EF6;&#x9700;&#x8981;&#x64CD;&#x4F5C;&#x7CFB;&#x7EDF;&#x5728;&#x786C;&#x76D8;&#x5199;&#x591A;&#x6B21;&#xFF0C;&#x6807;&#x8BB0;&#x591A;&#x4E2A;&#x5757;&#x88AB;&#x5220;&#x9664;&#xFF0C;&#x7C7B;&#x4F3C;gc&#x3002;


      &#x521B;&#x5EFA;&#x4E86;&#x786C;&#x94FE;&#x63A5;&#x4E4B;&#x540E;&#xFF0C;&#x5220;&#x9664;&#x64CD;&#x4F5C;&#x4EC5;&#x4EC5;&#x662F;&#x5F15;&#x7528;&#x51CF;&#x4E00;&#xFF0C;&#x4F46;&#x662F;&#x90A3;&#x4E2A;&#x786C;&#x94FE;&#x63A5;&#x5220;&#x9664;&#x7684;&#x65F6;&#x5019;&#x8FD8;&#x662F;&#x8981;gc&#x3002;&#x53EF;&#x4EE5;&#x8C03;&#x5230;&#x4E1A;&#x52A1;&#x4F4E;&#x5CF0;&#x671F;&#x6765;&#x505A;&#x3002;
    date: 2017-02-12T03:06:37.055Z

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

## 一 引子 ##

在生产环境中，删除一个大文件，比如一个数十 G 或者上百 G 的文件是很耗时的。

本文介绍一个快速 `DROP TABLE` 的方法。使用本文提供的方法，不管该表数据量、占用空间有多大，都可以快速的删除。

## 二 演示 ##

下面做一个演示。

### 2.1 环境 ###

首先说明环境：

**环境**

``` bash
mysql> SHOW VARIABLES LIKE '%version%';
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

### 2.2 添加 innodb_file_per_table 参数 ###

由于我使用 mysql_multi  的形式启动 MySQL。所以我们需要在 MySQL 的配置文件 my.cnf 中加入 `innodb_file_per_table` 参数。

我的 my.cnf  配置如下：

> [mysqld_multi]
> mysqld     = /usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/bin/mysqld_safe
> mysqladmin = /usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/bin/mysqladmin
> log = /var/log/mysqld_mutil.err
> user       = root
>
> [mysqld5173]
> port=5173
> socket=/tmp/mysql_5173.sock
> basedir=/usr/local/mysql/mysql-5.1.73-osx10.6-x86_64
> datadir=/usr/local/mysql/data/5.1
> user=_mysql
> log-error=/var/log/mysqld_5173.log
> pid-file=/tmp/mysqld_5173.pid
> innodb_file_per_table
>
> [mysqld5540]
> port=5540
> socket=/tmp/mysql_5540.sock
> basedir=/usr/local/mysql/mysql-5.5.40-osx10.6-x86_64
> datadir=/usr/local/mysql/data/5.5
> user=_mysql
> log-error=/var/log/mysqld_5540.log
> pid-file=/tmp/mysqld_5540.pid
> innodb_file_per_table
>
> [mysqld5612]
> port=5612
> socket=/tmp/mysql_5612.sock
> basedir=/usr/local/mysql/mysql-5.6.21-osx10.8-x86_64
> datadir=/usr/local/mysql/data/5.6
> user=_mysql
> log-error=/var/log/mysqld_5612.log
> pid-file=/tmp/mysqld_5612.pid
> innodb_file_per_table

### 2.3 导入数据 ###

接着登录到 MySQL。

``` bash
mysql --socket=/tmp/mysql_5173.sock -uroot -proot
```

创建测试表。

``` bash
mysql> SET storage_engine=INNODB;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| test               |
+--------------------+
3 rows in set (0.01 sec)

mysql> USE test;
Database changed
mysql> CREATE TABLE user
       -> (name VARCHAR(20),
       -> age int,
       -> sex CHAR(2),
       -> city VARCHAR(20),
       -> work VARCHAR(10)
       -> ) DEFAULT CHARSET utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.17 sec)

mysql> CREATE TABLE city
       -> (name VARCHAR(20),
       -> province VARCHAR(20),
       -> shortname VARCHAR(4),
       -> coma VARCHAR(10),
       -> comb VARCHAR(10)
       -> ) DEFAULT CHARSET utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.08 sec)
```

**说明：**实验主要使用 city 表。user 表只是用于测试 `LOAD DATA INFILE` 的速度。

创建数据文本。

``` bash
vim /tmp/user.txt
cat -n /tmp/user.txt
```

该文件包括 100W 行数据。内容如下：

> 1              "robin",19,"M","GuangZhou","DBA"
> ......
> 1000000  "robin",19,"M","GuangZhou","DBA"

``` bash
vim /tmp/city.txt
cat -n /tmp/city.txt
```

该文件包括 1000W 行数据。内容如下：

> 1              "GuangZhou","GuangDong","GZ","Wechat","Netease"
> ......
> 10000000 "GuangZhou","GuangDong","GZ","Wechat","Netease"

编辑导入数据脚本。

``` bash
vim /tmp/load_to_user.sql
cat -n /tmp/load_to_user.sql
```

该文件包括 10 行相同的导入数据命令。成功导入到 user 表后，会有 1000W 的数据。内容如下：

> 1   LOAD DATA INFILE '/tmp/user.txt' \
> INTO TABLE user \
> FIELDS TERMINATED BY ',' \
> LINES TERMINATED BY '\n';
> ......
> 10  LOAD DATA INFILE '/tmp/user.txt' \
> INTO TABLE user \
> FIELDS TERMINATED BY ',' \
> LINES TERMINATED BY '\n';

导入到 city 表的操作类似。

``` bash
vim /tmp/load_to_city.sql
cat -n /tmp/load_to_city.sql
```

该文件包括 20 行相同的导入数据命令。成功导入到 city 表后，会有两亿条数据。内容如下：

> 1  LOAD DATA INFILE '/tmp/city.txt' \
> INTO TABLE city FIELDS \
> TERMINATED BY ',' \
> LINES TERMINATED BY '\n';
> ......
> 20  LOAD DATA INFILE '/tmp/city.txt' \
> INTO TABLE city FIELDS \
> TERMINATED BY ',' \
> LINES TERMINATED BY '\n';

导入数据到 MySQL。

``` bash
mysql> source /tmp/load_to_user.sql
```

其中导入到 user 表共耗时 84.63 秒。

``` bash
mysql> SHOW TABLE STATUS LIKE 'user' \G;
*************************** 1. row ***************************
           Name: user
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 10000389
 Avg_row_length: 63
    Data_length: 632291328
Max_data_length: 0
   Index_length: 0
      Data_free: 179306496
 Auto_increment: NULL
    Create_time: 2015-01-15 14:38:05
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.22 sec)

mysql> SELECT count(*) FROM user;
+----------+
| count(*) |
+----------+
| 10000000 |
+----------+
1 row in set (7.06 sec)
```

接着导入数据到 city 表。

``` bash
mysql> source /tmp/load_to_city.sql
Query OK, 10000000 rows affected (1 min 45.95 sec)
Records: 10000000  Deleted: 0  Skipped: 0  Warnings: 0
......
```

总共耗时：

``` bash
bc <<< 105.95+113.84+114.89+111.83+\
116.20+128.12+131.41+118.94+115.5+\
122.63+116.12+119.87+140.83+148.78+\
126.61+129.62+116.2+103.37+108.52+105.07
```

共计 2394.30 秒，亦即 39.905 分钟。

### 2.4 第一次删除表 ###

我们查看数据目录，可以看到该表占用空间为 15G。

``` bash
sudo ls -FGlAhp test
```

> total 15699980
> -rw-rw----  1 _mysql  _mysql   8.5K Jan 15 16:46 city.frm
> -rw-rw----  1 _mysql  _mysql    15G Jan 15 17:33 city.ibd

删除表，耗时 1.08 秒。当然，这里数据量还不够大，所以速度还是挺快。

``` bash
mysql> DROP TABLE city;
Query OK, 0 rows affected (1.08 sec)
```

接下来，我们重新创建表，导入数据。

``` bash
mysql> CREATE TABLE city
       -> (name VARCHAR(20),
       -> province VARCHAR(20),
       -> shortname VARCHAR(4),
       -> coma VARCHAR(10),
       -> comb VARCHAR(10)
       -> ) DEFAULT CHARSET utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.06 sec)

mysql> source /tmp/load_to_city.sql
```

导入数据耗时跟之前相差不多，不做计算。

### 2.5 第二次删除表，使用硬链接 ###

创建硬链接。
``` bash
sudo ls -FGlAhp test
```

> total 15699980
> -rw-rw----  1 _mysql  _mysql   8.5K Jan 15 17:35 city.frm
> -rw-rw----  1 _mysql  _mysql    15G Jan 15 18:13 city.ibd

``` bash
sudo ln test/city.ibd test/city.ibd.hl
sudo ls -FGlAhp test
```

> total 31399948
> -rw-rw----  1 _mysql  _mysql   8.5K Jan 15 17:35 city.frm
> -rw-rw----  2 _mysql  _mysql    15G Jan 15 18:13 city.ibd
> -rw-rw----  2 _mysql  _mysql    15G Jan 15 18:13 city.ibd.hl

可以看到，iNode 由 1 变为 2。

再次删除。

``` bash
mysql> SHOW TABLE STATUS LIKE 'city' \G;
*************************** 1. row ***************************
           Name: city
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 200000304
 Avg_row_length: 79
    Data_length: 15847129088
Max_data_length: 0
   Index_length: 0
      Data_free: 4194304
 Auto_increment: NULL
    Create_time: 2015-01-15 17:35:14
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.38 sec)

mysql> SELECT count(*) FROM city;
+-----------+
| count(*)  |
+-----------+
| 200000000 |
+-----------+
1 row in set (3 min 11.39 sec)

mysql> DROP TABLE city;
Query OK, 0 rows affected (0.90 sec)
```

最后，把硬链接文件删除。

``` bash
sudo ls -FGlAhp test
```

> total 15699968
> -rw-rw----  1 _mysql  _mysql    15G Jan 15 18:13 city.ibd.hl

``` bash
sudo rm -rf test/city.ibd.hl
```

## 三 实验结果 ##

第一次删除，耗时 1.08 秒。第二次，建立硬链接后，删除表耗时 0.90  秒。两次删除表耗时差异不是太明显，那是因为我的数据只有 15 G。如果在生产环境中，数据量达到数十 G、上百 G、甚至 T 级，就会显示这种方法的威力了。本来打算模拟出 100 G 的数据，但由于机器配置和时间关系，就没有做了。

PS：两次插入数据，每次 两亿，已经耗去我 1 个多小时的时间。时间宝贵啊，不在这里浪费了。

## 四 原理分析 ##

本文中快速 `DROP TABLE` 利用了操作系统的 `Hard Link(硬链接)` 的原理。当多个文件名同时指向同一个 iNode 时，这个 iNode 的引用数 N > 1，删除其中任何一个文件名都会很快。因为其直接的物理文件块没有被删除，只是删除了一个指针而已；当 iNode 的引用数 N = 1 时，删除文件需要去把这个文件相关的所有数据块清除，所以会比较耗时。

最后，吐槽下 Windows。这次测试环境为 Mac OS X 10.9.5，i5，8G 内存。vim 打开一个 458 M 的文本，只需要数秒（N <= 5）。 插入数据时内存几乎用完，使用率达到 98%，但 Mac 相当给力，没有丝毫卡顿。如果这个实验是在 同样配置的 Windows 下做，不知要折腾到什么时候，或许，根本跑不动。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

截个图给读者欣赏欣赏。

![2015-01-15-solution-of-droping-large-table-under-mysql](https://cdn.dbarobin.com/63SkdXF.png)

Enjoy!

## 五 Ref ##

* <a href="http://dev.mysql.com/doc/refman/5.1/en/load-data.html" target="_blank">13.2.6 LOAD DATA INFILE Syntax</a>
* <a href="http://pangge.blog.51cto.com/6013757/1303893" target="_blank">MySQL 数据库存储引擎</a>
* <a href="http://dev.mysql.com/doc/refman/5.1/en/innodb-multiple-tablespaces.html" target="_blank">14.6.4.2 Using Per-Table Tablespaces</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
