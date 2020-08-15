---
published: true
author: Robin Wen
layout: post
title: "MySQL binlog 物理回溯最佳实战"
category: 数据库
summary: "在平时的迁移或者其他维护场景中，我们需要利用备份的物理 binlog 做回溯。本篇文章根据真实案例进行讲解，如何优雅地利用 binlog 进行物理回溯。结论如下：拷贝回去的 binlog，需要将属性改为 mysql；mysql-bin.index 这个文件不管加一行减一行，在触发 FLUSH LOGS 的时候原有的复制会被中断。如果旧 Master 有从库，恢复其他从库数据的时候，需要暂时将已有的从库同步停掉（执行 STOP SLAVE），就能避免中断；任何线上操作，都要在完备的测试前提下再操作；迁移过程中，重要的一点是做好数据校验，不管是用 pt，还是手动写脚本，这个过程不能缺失。"
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

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

| 日期 | 作者 |  文档概要 | 版本 | 更新历史 |
|:------------|:---------------|:-----|:-----|:-----|:-----|
| 2017/03/01 | robin |  MySQL binlog 物理回溯最佳实战 | v1.0 | 文档初稿 |
| 2017/03/02 | robin |  MySQL binlog 物理回溯最佳实战 | v1.1 | 修改结论 |

## 0x00 目录
***

* Table of Contents
{:toc}

## 0x01 前言
***

在平时的迁移或者其他维护场景中，我们需要利用备份的物理 binlog 做回溯。本篇文章根据真实案例进行讲解，如何优雅地利用 binlog 进行物理回溯。

## 0x02 测试
***

测试环境 IP 如下：

* 192.168.1.101（主）
* 192.168.1.102（从）
* 192.168.1.103（从）

测试步骤如下：

1、192.168.1.101 和 192.168.1.102 建立主从，192.168.1.101 创建 sbtest 库，然后使用 sysbench 插入 100 万测试数据，相关命令如下：

``` bash
sysbench --test=oltp --oltp-table-size=1000000 --oltp-read-only=off --init-rng=on \
--num-threads=16 --max-requests=0 --oltp-dist-type=uniform --max-time=1800 \
--mysql-user=root --mysql-socket=/tmp/mysql.sock \
--mysql-password=xxxx --db-driver=mysql --mysql-table-engine=innodb \
--oltp-test-mode=complex prepare
```

2、在 192.168.1.102 导出数据，然后拷贝到 192.168.1.103，在 192.168.1.103 导入数据。备份文件的 MASTER_LOG_FILE 和 MASTER_LOG_POS 信息如下：

``` sql
-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000104', \
-- MASTER_LOG_POS=3661463;
```

3、在 192.168.1.101 使用如下脚本再次导入 10 万数据。

``` bash
#!/bin/bash

for i in $(seq 1 100000)
do
    mysql -uroot -p'xxxx' --socket=/tmp/mysql.sock -e \
    "INSERT INTO sbtest.sbtest(k, c, pad) VALUES(0, '1', \
    'qqqqqqqqqqwwwwwwwwwweeeeeeeeeerrrrrrrrrrtttttttttt');"
```

4、192.168.1.101 模拟 binlog 被清除。先备份 binlog，然后再 PURGE。

``` sql
mysql> SELECT COUNT(*) FROM sbtest;
+----------+
| COUNT(*) |
+----------+
|  1100000 |
+----------+
1 row in set (0.19 sec)

mysql> PURGE BINARY LOGS TO 'mysql-bin.000106';
Query OK, 0 rows affected (0.03 sec)
```

5、192.168.1.102 停掉同步。

6、192.168.1.101 修改 mysql-bin.index 文件，把备份的 binlog 文件拷贝到 binlog 目录，然后手动执行 FLUSH LOGS。

``` bash
pwd
/data/mysql/binlog
cp -v /data/backup/mysql-bin.00010{4,5} .

cat mysql-bin.index
/data/mysql/binlog/mysql-bin.000104
/data/mysql/binlog/mysql-bin.000105
/data/mysql/binlog/mysql-bin.000106
/data/mysql/binlog/mysql-bin.000107

# 注意修改权限
chown mysql:mysql -R mysql-bin.*
```

手动 FLUSH LOGS，可以看到前后 BINARY LOGS 列表发生变化。

``` sql
mysql> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000106 |  10485866 |
| mysql-bin.000107 |    504130 |
+------------------+-----------+
2 rows in set (0.00 sec)

mysql> FLUSH LOGS;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000104 |  10486051 |
| mysql-bin.000105 |  10485866 |
| mysql-bin.000106 |  10485866 |
| mysql-bin.000107 |    504173 |
| mysql-bin.000108 |       107 |
+------------------+-----------+
6 rows in set (0.00 sec)
```

7、192.168.1.103 利用备份文件的 MASTER_LOG_FILE 和 MASTER_LOG_POS 信息和 192.168.1.101 建立主从关系。

8、192.168.1.103 和 192.168.1.101 校验数据。192.168.1.101 的增量数据已经完全同步到 192.168.1.103。

9、192.168.1.101 修改 mysql-bin.index 文件，将之前手动加入的 binlog 删除，然后再 FLUSH LOGS。

10、192.168.1.102 打开同步，可以看到同步正常，再确认 192.168.1.103，同步也正常。

![OIl_2](https://cdn.dbarobin.com/c69sDpL.jpg)

© cosgood1969/OIl_2/Pinterest

**我们接下来测试下旧 DB 已有从库不停掉同步的情况。**

测试环境 IP 如下：

* 192.168.1.101（主）
* 192.168.1.102（从）
* 192.168.1.103（从）

测试步骤如下：

1、192.168.1.101 和 192.168.1.102 建立主从，192.168.1.101 创建 sbtest 库，然后使用 sysbench 插入 100 万测试数据，相关命令如下：

``` bash
sysbench --test=oltp --oltp-table-size=1000000 --oltp-read-only=off --init-rng=on \
--num-threads=16 --max-requests=0 --oltp-dist-type=uniform --max-time=1800 \
--mysql-user=root --mysql-socket=/tmp/mysql.sock \
--mysql-password=xxxx --db-driver=mysql --mysql-table-engine=innodb \
--oltp-test-mode=complex prepare
```

2、在 192.168.1.102 导出数据，然后拷贝到 192.168.1.103，在 192.168.1.103 导入数据。备份文件的 MASTER_LOG_FILE 和 MASTER_LOG_POS 信息如下：

``` sql
-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000115', \
-- MASTER_LOG_POS=6102367;
```

3、在 192.168.1.101 使用如下脚本再次导入 10 万数据。

``` bash
#!/bin/bash

for i in $(seq 1 100000)
do
    mysql -uroot -p'xxxx' --socket=/tmp/mysql.sock -e \
    "INSERT INTO sbtest.sbtest(k, c, pad) VALUES(0, '1', \
    'qqqqqqqqqqwwwwwwwwwweeeeeeeeeerrrrrrrrrrtttttttttt');"
```

4、192.168.1.101 模拟 binlog 被清除。先备份 binlog，然后再 PURGE。

``` sql
mysql> SELECT COUNT(*) FROM sbtest;
+----------+
| COUNT(*) |
+----------+
|  1100000 |
+----------+
1 row in set (0.19 sec)

mysql> PURGE BINARY LOGS TO 'mysql-bin.000118';
Query OK, 0 rows affected (0.03 sec)
```

5、192.168.1.102 **不停同步。**

6、192.168.1.101 修改 mysql-bin.index 文件，把备份的 binlog 文件拷贝到 binlog 目录，然后手动执行 FLUSH LOGS。

``` bash
pwd
/data/mysql/binlog
cp -v /data/backup/mysql-bin.00011{5,6,7} .

cat mysql-bin.index
/data/mysql/binlog/mysql-bin.000115
/data/mysql/binlog/mysql-bin.000116
/data/mysql/binlog/mysql-bin.000117
/data/mysql/binlog/mysql-bin.000118

# 注意修改权限
chown mysql:mysql -R mysql-bin.*
```

手动 FLUSH LOGS，可以看到前后 BINARY LOGS 列表发生变化。

``` sql
mysql> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000118 |   2945005 |
+------------------+-----------+
1 row in set (0.00 sec)

mysql> FLUSH LOGS;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000115 |  10486080 |
| mysql-bin.000116 |  10485866 |
| mysql-bin.000117 |  10485866 |
| mysql-bin.000118 |   2945048 |
| mysql-bin.000119 |       107 |
+------------------+-----------+
5 rows in set (0.00 sec)
```

7、观察 192.168.1.102，可以看到此时同步已经出错。

``` sql
Master_Log_File: mysql-bin.000119
Read_Master_Log_Pos: 107
Relay_Log_File: relaylog.000326
Relay_Log_Pos: 253
Relay_Master_Log_File: mysql-bin.000116
Exec_Master_Log_Pos: 107

Last_Errno: 1062
Last_Error: Error 'Duplicate entry '1015491' for key 'PRIMARY'' on query. \
Default database: ''. Query: 'INSERT INTO sbtest.sbtest(k, c, pad) VALUES \
(0, '1', 'qqqqqqqqqqwwwwwwwwwweeeeeeeeeerrrrrrrrrrtttttttttt')'
```

再对比下出错之前的从库状态：

``` sql
Master_Log_File: mysql-bin.000118
Read_Master_Log_Pos: 2945005
Relay_Log_File: relaylog.000324
Relay_Log_Pos: 2945151
Relay_Master_Log_File: mysql-bin.000118
Exec_Master_Log_Pos: 2945005
```

可以看到，192.168.1.102 从 mysql-bin.000116:107 的位置重现同步，就会导致主键冲突的问题。

8、192.168.1.103 利用备份文件的 MASTER_LOG_FILE 和 MASTER_LOG_POS 信息和 192.168.1.101 建立主从关系。

``` sql
CHANGE MASTER TO MASTER_HOST='192.168.1.101', MASTER_PORT=3307, MASTER_USER='slave', \
MASTER_PASSWORD='xxxx', MASTER_LOG_FILE='mysql-bin.000115', MASTER_LOG_POS=6102367;
```

9、192.168.1.103 和 192.168.1.101 校验数据。192.168.1.101 的增量数据已经完全同步到 192.168.1.103。

10、192.168.1.101 修改 mysql-bin.index 文件，将之前手动加入的 binlog 删除，然后再 FLUSH LOGS。

11、再次观察 192.168.1.102

``` sql
Master_Log_File: mysql-bin.000120
Read_Master_Log_Pos: 4
Relay_Log_File: relaylog.000326
Relay_Log_Pos: 253
Relay_Master_Log_File: mysql-bin.000116
Exec_Master_Log_Pos: 107
Last_IO_Errno: 1236
Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: \
'could not find next log; the first event 'mysql-bin.000107' at 504130, \
the last event read from '/data/mysql/binlog/mysql-bin.000119' at 150, \
the last byte read from '/data/mysql/binlog/mysql-bin.000119' at 150.'
Last_SQL_Errno: 1062
Last_SQL_Error: Error 'Duplicate entry '1015491' for key 'PRIMARY'' on query. \
Default database: ''. Query: 'INSERT INTO sbtest.sbtest(k, c, pad) VALUES \
(0, '1', 'qqqqqqqqqqwwwwwwwwwweeeeeeeeeerrrrrrrrrrtttttttttt')'
```

此时 IO 线程和 SQL 线程均异常。

## 0x03 结论
***

结论如下：

* 拷贝回去的 binlog，需要将属性改为 mysql
* mysql-bin.index 这个文件不管加一行减一行，在触发 FLUSH LOGS 的时候原有的复制会被中断。如果旧 Master 有从库，恢复其他从库数据的时候，需要暂时将已有的从库同步停掉（执行 STOP SLAVE），就能避免中断
* 任何线上操作，都要在完备的测试前提下再操作
* 迁移过程中，重要的一点是做好数据校验，不管是用 pt，还是手动写脚本，这个过程不能缺失

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
