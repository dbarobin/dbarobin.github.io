---
published: true
author: Robin Wen
layout: post
title: "MySQL 社区版审计方案"
category: 数据库
summary: "为生产环境数据库提供审计功能是非常重要的，一方面，敏感操作和误操作有据可循，简化运维复杂度；另一方面，提高 DBA 以及相关使用方的风险意识。MySQL 提供了 3 种方式实现审计功能：audit_log.so 插件实现审计（企业版提供）、init-connect 参数 + access_log + binlog 实现审计、general log 记录所有操作。general log 会记录详细的 SQL 执行记录，但是生产环境如果业务量大，会产生大量的磁盘 IO 操作，严重降低数据库性能，所以生产环境一般不会开启 general log。除此之外，我们可以通过 init-connect 参数 + access_log + binlog 的方法进行 MySQL 的操作审计。由于 MySQL binlog 记录了所有对数据库产生实际修改的 SQL 语句、执行时间和 connection_id，但是却没有记录 connection_id 对应的详细用户信息。在后期审计进行行为追踪时，根据 binlog 记录的行为及对应的 connection_id，再加上之前连接记录进行分析，可以得出最后的结论。"
tags:
- 数据库
- Database
- 安全
- Security
- MySQL
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 目录
***

* Table of Contents
{:toc}

  日期 | 作者 |  文档概要 | 版本 | 更新历史
  ------- | -------- | -------- | -------- | --------
  2017/03/21 | robin |  MySQL 社区版审计方案 | v1.0 | 文档初稿
  2017/07/13 | robin |  MySQL 社区版审计方案 | v1.1 | 增加测试数据

## 0x00 前言
***

为生产环境数据库提供审计功能是非常重要的，一方面，敏感操作和误操作有据可循，简化运维复杂度；另一方面，提高 DBA 以及相关使用方的风险意识。

## 0x01 审计方案
***

MySQL 提供了 3 种方式实现审计功能：

* audit_log.so 插件实现审计（企业版提供）
* init-connect 参数 + access_log + binlog 实现审计
* general log 记录所有操作

> 由于企业版需要付费购买，不适用于本文，因此略过。

general log 会记录详细的 SQL 执行记录，但是生产环境如果业务量大，会产生大量的磁盘 IO 操作，严重降低数据库性能，所以生产环境一般不会开启 general log。

除此之外，我们可以通过 init-connect 参数 + access_log + binlog 的方法进行 MySQL 的操作审计。由于 MySQL binlog 记录了所有对数据库产生实际修改的 SQL 语句、执行时间和 connection_id，但是却没有记录 connection_id 对应的详细用户信息。在后期审计进行行为追踪时，根据 binlog 记录的行为及对应的 connection_id，再加上之前连接记录进行分析，可以得出最后的结论。[^1]

![Purple Scene](https://cdn.dbarobin.com/blQZ61i.jpg)

© Ivailo Nikolov / Purple Scene / fineartamerica.com

## 0x02 实现步骤
***

### 2.1 init-connect 配置

1、创建用于存放连接信息的表。

> MySQL 用户名最长为 32 个字符，5.7.8 版本之前最长为 16 个字符。IPv4 最长为 15 个字符，IPv6 最长为 39 字符。MySQL 目前版本 5.5.24，为了向后兼容，用户名按照 32 个字符计算。另外，此处只考虑 IPv4 地址。

``` sql
mysql> CREATE DATABASE AuditDB DEFAULT CHARSET utf8;
mysql> USE AuditDB;
mysql> CREATE TABLE access_log (
  ID BIGINT(20) UNSIGNED PRIMARY KEY  NOT NULL AUTO_INCREMENT,
  ConnectionID BIGINT(20) UNSIGNED NOT NULL DEFAULT 0,
  ConnUser VARCHAR(50) NOT NULL DEFAULT '',
  MatchUser VARCHAR(50) NOT NULL DEFAULT '',
  LoginTime DATETIME NOT NULL DEFAULT '0000-00-00 00:00:00'
);
```

2、保证所有连接用户对此表有写入权限。

``` sql
mysql> INSERT INTO mysql.db (Host,Db,User,Insert_priv) VALUES ('%','AuditDB','','Y');
Query OK, 1 row affected (0.03 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

```

3、设置 init-connect 参数。

在 [mysqld] 下添加以下设置：

``` bash
#设置初始化连接操作
init-connect='INSERT INTO AuditDB.access_log(ConnectionID, ConnUser, MatchUser, \
  LoginTime) VALUES(connection_id(), user(), current_user(), now());'
#开启binlog
log-bin=xxx
```

4、创建修改用户和数据库。

``` sql
mysql> CREATE DATABASE 37com;
Query OK, 1 row affected (0.00 sec)

mysql> GRANT SELECT, UPDATE, DELETE, INSERT, EXECUTE ON 37com.* TO \
web_37com@'127.0.0.1' IDENTIFIED BY 'xxx';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT INSERT ON AuditDB.access_log TO web_37com@'127.0.0.1';
Query OK, 0 rows affected (0.00 sec)
```

5、重启数据库生效。

``` bash
shell> cd /data/mysql/3306 && bash stop.sh
shell> cd /data/mysql/3306 && bash start.sh
```

6、连接测试查询

``` bash
#使用web_37com登录
/usr/local/mysql/bin/mysql -uweb_37com -h127.0.0.1 -p -P3306

#使用root登录，可以看到access_log记录了登录信息
/usr/local/mysql/bin/mysql -uroot -p
mysql> SELECT * FROM AuditDB.access_log;
+----+--------------+---------------------+---------------------+---------------------+
| ID | ConnectionID | ConnUser            | MatchUser           | LoginTime           |
+----+--------------+---------------------+---------------------+---------------------+
|  1 |           29 | web_37com@127.0.0.1 | web_37com@127.0.0.1 | 2017-07-13 10:47:43 |
|  2 |           31 | web_37com@127.0.0.1 | web_37com@127.0.0.1 | 2017-07-13 10:49:28 |
+----+--------------+---------------------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

### 2.2 查找操作记录

1、 进行模拟操作，下列操作可由多个连接进行。

``` bash
#root用户登录
/usr/local/mysql/bin/mysql -uroot -p
mysql> USE 37com;
Database changed
mysql> CREATE TABLE t(ID INT, NAME VARCHAR(20));
Query OK, 0 rows affected (0.03 sec)

#web_37com用户登录
/usr/local/mysql/bin/mysql -uweb_37com -h127.0.0.1 -p -P3306

mysql> USE 37com;
Database changed
mysql> INSERT INTO t VALUES(1, 'Robin');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t VALUES(2, 'Marry');
Query OK, 1 row affected (0.00 sec)

mysql> DELETE FROM t;
Query OK, 2 rows affected (0.00 sec)
```

2、根据 binlog，确认操作 delete 的 thread_id。

``` bash
mysqlbinlog --start-datetime='2017-07-13 10:30:00' --stop-datetime='2017-07-13 11:00:00' \
/data/mysql/3306/binlog/mysql-bin.000007 | grep -B 5 DELETE
CREATE DATABASE 37com
/*!*/;
#at 192
#170713 10:35:03 server id 204138  end_log_pos 372      Query   thread_id=1     \
exec_time=0     error_code=0
SET TIMESTAMP=1499913303/*!*/;
GRANT SELECT, UPDATE, DELETE, INSERT ON 37com.* TO web_37com@'127.0.0.1' \
IDENTIFIED BY ''
--
BEGIN
/*!*/;
#at 1944
#170713 10:53:23 server id 204138  end_log_pos 2021     Query   thread_id=38    \
exec_time=0     error_code=0
SET TIMESTAMP=1499914403/*!*/;
DELETE FROM t
```

3、查询 AuditDB.access_log。

``` sql
/usr/local/mysql/bin/mysql -uroot -p
mysql> SELECT * FROM AuditDB.access_log where ConnectionID=38;
+----+--------------+---------------------+---------------------+---------------------+
| ID | ConnectionID | ConnUser            | MatchUser           | LoginTime           |
+----+--------------+---------------------+---------------------+---------------------+
|  3 |           38 | web_37com@127.0.0.1 | web_37com@127.0.0.1 | 2017-07-13 10:52:18 |
+----+--------------+---------------------+---------------------+---------------------+
1 row in set (0.00 sec)
```

## 0x03 性能测试
***

1、添加 init-connect 参数之前的测试。此处采用 sysbench 进行基准测试。 [^2]

``` bash
#准备测试环境
./sysbench ---mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root \
--mysql-password=ROOT_PWD --test=tests/db/oltp.lua --oltp_tables_count=10 \
--oltp-table-size=100000 --rand-init=on prepare

sysbench 0.5:  multi-threaded system evaluation benchmark

Creating table 'sbtest1'...
Inserting 100000 records into 'sbtest1'
Creating table 'sbtest2'...
Inserting 100000 records into 'sbtest2'
Creating table 'sbtest3'...
Inserting 100000 records into 'sbtest3'
Creating table 'sbtest4'...
Inserting 100000 records into 'sbtest4'
Creating table 'sbtest5'...
Inserting 100000 records into 'sbtest5'
Creating table 'sbtest6'...
Inserting 100000 records into 'sbtest6'
Creating table 'sbtest7'...
Inserting 100000 records into 'sbtest7'
Creating table 'sbtest8'...
Inserting 100000 records into 'sbtest8'
Creating table 'sbtest9'...
Inserting 100000 records into 'sbtest9'
Creating table 'sbtest10'...
Inserting 100000 records into 'sbtest10'

#持续10分钟基准测试
./sysbench --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root \
--mysql-password=ROOT_PWD --test=tests/db/oltp.lua --oltp_tables_count=10 \
--oltp-table-size=10000000 --num-threads=8 --oltp-read-only=off \
--report-interval=10 --rand-type=uniform --max-time=600 \
 --max-requests=0 --percentile=99 run

sysbench 0.5:  multi-threaded system evaluation benchmark

Running the test with following options:
Number of threads: 8
Report intermediate results every 10 second(s)
Random number generator seed is 0 and will be ignored


Threads started!

[  10s] threads: 8, tps: 1197.78, reads/s: 16774.66, writes/s: 4791.13, \
response time: 15.87ms (99%)
...
[ 600s] threads: 8, tps: 1182.40, reads/s: 16558.10, writes/s: 4730.50, \
response time: 18.07ms (99%)
OLTP test statistics:
    queries performed:
        read:                            9870476
        write:                           2820134
        other:                           1410066
        total:                           14100676
    transactions:                        705032 (1175.05 per sec.)
    deadlocks:                           2      (0.00 per sec.)
    read/write requests:                 12690610 (21150.91 per sec.)
    other operations:                    1410066 (2350.10 per sec.)

General statistics:
    total time:                          600.0030s
    total number of events:              705032
    total time taken by event execution: 4796.8465s
    response time:
         min:                                  2.98ms
         avg:                                  6.80ms
         max:                                223.59ms
         approx.  99 percentile:              17.50ms

Threads fairness:
    events (avg/stddev):           88129.0000/207.92
    execution time (avg/stddev):   599.6058/0.00
```

添加 init-connect 参数之后测试。

``` bash
./sysbench --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root \
--mysql-password=ROOT_PWD --test=tests/db/oltp.lua --oltp_tables_count=10 \
--oltp-table-size=10000000 --num-threads=8 --oltp-read-only=off \
--report-interval=10 --rand-type=uniform --max-time=600 \
 --max-requests=0 --percentile=99 run
sysbench 0.5:  multi-threaded system evaluation benchmark

Running the test with following options:
Number of threads: 8
Report intermediate results every 10 second(s)
Random number generator seed is 0 and will be ignored


Threads started!

[  10s] threads: 8, tps: 31.20, reads/s: 443.41, writes/s: 125.30, \
response time: 610.04ms (99%)
...
[ 600s] threads: 8, tps: 1166.10, reads/s: 16322.20, writes/s: 4663.50, \
response time: 18.36ms (99%)
OLTP test statistics:
    queries performed:
        read:                            8349768
        write:                           2385646
        other:                           1192822
        total:                           11928236
    transactions:                        596410 (994.01 per sec.)
    deadlocks:                           2      (0.00 per sec.)
    read/write requests:                 10735414 (17892.19 per sec.)
    other operations:                    1192822 (1988.02 per sec.)

General statistics:
    total time:                          600.0056s
    total number of events:              596410
    total time taken by event execution: 4797.4918s
    response time:
         min:                                  3.01ms
         avg:                                  8.04ms
         max:                                691.88ms
         approx.  99 percentile:              42.24ms

Threads fairness:
    events (avg/stddev):           74551.2500/186.24
    execution time (avg/stddev):   599.6865/0.00
```

可以看到，开启 init_connect 参数之后，平均响应时间从之前的 2.98ms 增加到 3.01ms，性能略有影响。

## 0x04 小结
***

1. 理论上，只会在用户每次连接时往数据库里插入一条记录，不会对数据库产生很大影响。除非连接频率非常高
2. 如果数据库连接数量很大的话，建议一定时间做一次数据导出，然后清表。[^3]
3. access_log 表当然不只用于审计，当然也可以用于对于数据库连接的情况进行数据分析，例如每日连接数分布图等等。
4. init-connect 是不会在 super 用户登录时执行的。所以 access_log 里不会有数据库超级用户的记录，因为当 init_connect 设置有误时，root 超级管理员可进行修改。
5. 如多人使用同一用户可能无法区分，最好一个人分配一个数据库操作用户
6. 用户需要有 AuditDB.access_log INSERT 权限，否则登录失败。这也对今后的授权提供了一个要求，任何非 root 用户都需要授予 AuditDB.access_log INSERT 权限。
7. 此方案在新业务中适用，旧业务不做变更。

## 0x05 参考
***

* [^1] svoid (2015-04-27). MySQL 审计功能. Retrieved from [http://blog.itpub.net/29733787/viewspace-1604392](http://blog.itpub.net/29733787/viewspace-1604392).
* [^2] 叶金荣 (2014-10-17). sysbench 安装、使用、结果解读. Retrieved from [http://imysql.com/2014/10/17/sysbench-full-user-manual.shtml](http://imysql.com/2014/10/17/sysbench-full-user-manual.shtml).
* [^3] 卢钧轶 (2012-05-09). 通过 init-connect + binlog 实现 MySQL 审计功能. Retrieved from [http://www.cnblogs.com/cenalulu/archive/2012/05/09/2491736.html](http://www.cnblogs.com/cenalulu/archive/2012/05/09/2491736.html).

## 0x06 其他
***

因不可抗拒因素，我们的网络状况日益紧缩，故建立了一个 Telegram 群和 Telegram Channel。Telegram 群方便交流，Telegram Channel 分享心得以及阅读的好文章。

> Telegram 群：[https://t.me/robinwenio](https://t.me/robinwenio)
> Telegram Channel：[https://t.me/fuckgfwio](https://t.me/fuckgfwio)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
