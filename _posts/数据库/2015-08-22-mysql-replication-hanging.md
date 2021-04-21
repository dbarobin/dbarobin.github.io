---
published: true
author: Robin Wen
layout: post
title: MySQL 复制夯住排查以及原理探讨
category: 数据库
summary: >-
  研发反应，有台从库和主库不同步。由于业务读操作是针对从库的，数据不同步必定会带来数据的不一致，业务获取的结果会受影响，所以这个问题必须尽快解决。登上服务器，查看
  MySQL 的从库状态，并没有任何报错信息。刷新从库状态，发现状态没有任何变化，Exec_Master_Log_Pos
  卡住不动。这样的故障，归根结底还是研发写的程序还有优化的余地。大批量的数据插入，这在 MySQL 中是不推荐使用的。我们可以这样：第一，一条 SQL
  语句插入多条数据；第二，在事务中进行插入处理；第三，分批插入，在程序中设置 auto_commit 为 0，分批插入完成后，手动
  COMMIT；第四，需要使用 LOAD DATA LOCAL INFILE 时，设置 sync_binlog 为 1。
tags:
  - 数据库
  - Database
  - MySQL
  - 复制
  - 故障排查
comments:
  - author:
      type: github
      displayName: RorschachChan
      url: 'https://github.com/RorschachChan'
      picture: 'https://avatars0.githubusercontent.com/u/31002078?v=4&s=73'
    content: >-
      &#x535A;&#x4E3B;&#x4F60;&#x597D;&#xFF01;


      &#x770B;&#x4E86; &#x6545;&#x969C;&#x89E3;&#x51B3;
      &#x90A3;&#x8282;&#x6CA1;&#x6709;&#x5F88;&#x61C2;&#xFF1A;

      1&#xFF09;&#x6545;&#x969C;&#x53D1;&#x751F;&#x5728;&#x4ECE;&#x5E93;&#xFF0C;&#x4E3A;&#x5565;&#x8FD8;&#x8981;&#x91CD;&#x542F;&#x4E3B;&#x5E93;&#xFF1F;

      2&#xFF09;&#x4E3B;&#x5E93;&#x91CD;&#x542F;&#x5B8C;&#x6BD5;&#x4E4B;&#x540E;&#xFF0C;&#x5C31;&#x9A6C;&#x4E0A;&#x628A;&#x6D41;&#x91CF;&#x5207;&#x56DE;&#x53BB;&#x4E86;&#x5417;&#xFF1F;&#x8FD8;&#x662F;&#x5728;&#x4ECE;&#x5E93;&#x7684;my.cnf&#x6DFB;&#x52A0;replicate-ignore-table
      &#x5E76;&#x91CD;&#x542F;&#x540E;&#xFF0C;&#x518D;&#x5207;&#x56DE;&#x53BB;&#x7684;&#x5462;&#xFF1F;


      &#x5148;&#x611F;&#x8C22;&#x60A8;&#x7684;&#x89E3;&#x60D1;&#x4E86;&#x3002;
    date: 2019-07-08T11:27:26.570Z

---

## 目录 ##
***

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 引子 ##
***

研发反应，有台从库和主库不同步。由于业务读操作是针对从库的，数据不同步必定会带来数据的不一致，业务获取的结果会受影响，所以这个问题必须尽快解决。

登上服务器，查看 MySQL 的从库状态，并没有任何报错信息。刷新从库状态，发现状态没有任何变化，**Exec_Master_Log_Pos** 卡住不动。

## 二 故障分析 ##
***

为了安全起见，此文略去 MySQL 版本以及其他可能会带来安全问题的信息。

接下来逐步分析问题。

首先查看从库状态：

``` bash
mysql> SHOW SLAVE STATUS \G
*************************** 1. row ***************************
               Slave_IO_State: Queueing master event to the relay log
                  Master_Host: masterIP
                  Master_User: replUser
                  Master_Port: masterPort
                Connect_Retry: 60
              Master_Log_File: binlog.000296
          Read_Master_Log_Pos: 364027786
               Relay_Log_File: relaylog.000002
                Relay_Log_Pos: 250
        Relay_Master_Log_File: binlog.000283
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 594374863
              Relay_Log_Space: 13803486573
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 256219
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: masterID
1 row in set (0.00 sec)
```

此时的 Slave_IO_State 为 Queueing master event to the relay log，而不是正常状态下的 Waiting for master to send event。刷新多次，状态没有任何变化，**Exec_Master_Log_Pos** 不变，从而导致 **Seconds_Behind_Master** 一直不变。

接下来查看 PROCESSLIST 状态：

``` bash
mysql> SHOW FULL PROCESSLIST;
+--------+-------------+------------------+--------------------+---------+---------+---------------+------------------+
| Id     | User        | Host             | db                 | Command | Time    | State           | Info                  |
+--------+-------------+------------------+--------------------+---------+---------+---------------+------------------+
|  51378 | system user |   | NULL | Connect | 1121183 | Waiting for master to send event | NULL   |
|  88917 | system user |   | NULL | Connect |  245327 | Reading event from the relay log | NULL    |
| 106029 | userA     | xxx.xxx.xxx.xxx:14057 | NULL               | Sleep   |   14504 |   | NULL          |
| 106109 | userA     | xxx.xxx.xxx.xxx:15077 | databaseA | Sleep   |      79 |     | NULL                  |
| 106110 | userA     | xxx.xxx.xxx.xxx:15081 | databaseA | Sleep   |   13000 |   | NULL                  |
| 106116 | userB    | xxx.xxx.xxx.xxx:15096 | databaseA | Sleep   |     357 |  | NULL                  |
| 106117 | userB    | xxx.xxx.xxx.xxx:15097 | NULL               | Sleep   |   12964 |     | NULL         |
| 106119 | root        | localhost    | NULL   | Query   |    0 | NULL     | SHOW FULL PROCESSLIST |
| 106126 | userB    | xxx.xxx.xxx.xxx:15173 | NULL               | Sleep   |   12856 |      | NULL      |
| 106127 | userB    | xxx.xxx.xxx.xxx:15180 | databaseA | Sleep   |   12849 |    | NULL                  |
| 106766 | userA     | xxx.xxx.xxx.xxx:17960 | databaseA | Sleep   |      64 |       | NULL                |
+--------+-------------+------------------+--------------------+---------+---------+---------------+------------------+
11 rows in set (0.00 sec)
```

从以上的结果来看，没有任何异常。

既然从上述信息中得不到任何对排查问题有帮助的信息，那么我们可以试着分析 MySQL 的 binlog，看 Pos 为 594374863 的点发生了什么操作。

分析日志我们可以使用 mysqlbinlog 命令，指定 start-position 为夯住的那个点，并重定向到文件。

``` bash
/usr/local/mysql/bin/mysqlbinlog --no-defaults -v --start-position="594374863" \
binlog.000283 > /XXX/binlog.sql
```

查看输出结果，发现端倪了，以下是摘抄的部分结果：

``` bash
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#150814 17:43:15 server id 21  end_log_pos 107  Start: binlog v 4, server v x.x.xx-log created 150814 17:43:15
BINLOG '
M7jNVQ8VAAAAZwAAAGsAAAAAAAQANS41LjE5LWxvZwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAEzgNAAgAEgAEBAQEEgAAVAAEGggAAAAICAgCAA==
'/*!*/;
# at 594374863
#150814 18:09:36 server id 21  end_log_pos 594374945    Query   thread_id=210702841 exec_time=43    error_code=0
SET TIMESTAMP=1439546976/*!*/;
SET @@session.pseudo_thread_id=210702841/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=0/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8 *//*!*/;
SET @@session.character_set_client=33,@@session.collation_connection=33,@@session.collation_server=33/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
BEGIN
/*!*/;
# at 594374945
# at 594375036
# at 594376047
# at 594377085
# at 594378123
# at 594379152
# at 594380187
# at 594381165
# at 594382194
# at 594383223
# at 594384252
# at 594385269
# at 594386307
# at 594387282
# at 594388299
# at 594389265
# at 594390270
# at 594391299
# at 594392310
# at 594393327
# at 594394344
# at 594395340
# at 594396336
# at 594397332
```

从以上输出中，我们可以知道，从夯住的那个点开始，binlog 记录的信息就出现了异常，可以推测在主库有大操作。另外，针对出现问题库，查看主库和从库的表数量，发现从库的表数量多于主库，有几个临时表出现。可以推测的，主库有删表的操作，从库同步夯住，导致同步异常，主库删表的操作还没来得及同步到从库。

经过和研发沟通，确认了两点。第一，确实有大操作，程序有大量的批量插入，而且是用的 LOAD DATA LOCAL INFILE；第二，主库确实有删表的操作，这几张表都是临时表。

## 三 故障解决 ##
***

既然问题找到了，那解决办法自然就有了。既然从库的表多于主库，而且这几张表是临时数据，我们可以过滤掉对这几张表的同步操作。具体思路如下：在主库备份临时表（虽然研发说数据不重要，但还是以防万一，DBA 还是谨慎为好），然后通知研发临时切走从库的流量，修改配置文件，指定 **replicate-ignore-table** 参数，重启 MySQL。

接下来就是具体的解决步骤，首先备份数据。备份时不加 --master-data 参数和 --single-transaction。究其原因，--master-data 禁用 --lock-tables 参数，在和 --single-transaction 一起使用时会禁用  --lock-all-tables。在备份开始时，会获取全局 read lock。 --single-transaction 参数设置默认级别为 REPEATABLE READ，并且在开始备份时执行 START TRANSACTION。在备份期间， 其他连接不能执行如下语句：ALTER TABLE、CREATE TABLE、DROP TABLE、RENAME TABLE、TRUNCATE TABLE。MySQL 同步夯住，如果加了上述参数，mysqldump 也会夯住。mysqldump 会 FLUSH TABLES、LOCK TABLES，如果有 --master-data 参数，会导致 Waiting for table flush。同样，有  --single-transaction 参数，仍然会导致 Waiting for table flush。另外，还可以看到 Waiting for table metadata lock，此时做了 DROP TABLE 的操作。此时可以停掉 MySQL 同步来避免这个问题。

为了保险起见，我们在主库加大 expire_logs_days 参数，避免 binlog 丢失。

``` bash
mysql> SHOW VARIABLES LIKE '%expire%';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| expire_logs_days | 3     |
+------------------+-------+
1 row in set (0.00 sec)

mysql> SET GLOBAL expire_logs_days=5;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE '%expire%';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| expire_logs_days | 5     |
+------------------+-------+
1 row in set (0.00 sec)
```

接着修改从库的配置文件：

``` bash
vim /xxx/xxxx/xxx/my.cnf
```

在 mysqld 后，加入如下配置：

> replicate-ignore-table=databaseA.tableA
> replicate-ignore-table=databaseA.tableB
> replicate-ignore-table=databaseA.tableC
> replicate-ignore-table=databaseA.tableD

然后重启 MySQL：

``` bash
/xxx/xxx/xxx/xxx/mysqld restart
```

登录 MySQL 从库，查看从库状态，并定时刷新状态，我们可以看到的是，**Exec_Master_Log_Pos** 在递增，**Seconds_Behind_Master** 在递减，证明问题已经解决了。

``` bash
mysql> SHOW SLAVE STATUS \G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: masterIP
                  Master_User: replUser
                  Master_Port: masterPort
                Connect_Retry: 60
              Master_Log_File: binlog.000319
          Read_Master_Log_Pos: 985656691
               Relay_Log_File: relaylog.000004
                Relay_Log_Pos: 709043542
        Relay_Master_Log_File: binlog.000284
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table: databaseA.tableA,databaseA.tableB,databaseA.tableC,databaseA.tableD
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 709043399
              Relay_Log_Space: 38579192969
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 258490
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: masterID
1 row in set (0.00 sec)
```

需要注意的是，待同步完成后，需要把从库配置文件中增加的 **replicate-ignore-table** 参数注释，并重启 MySQL。

## 四 原理探讨 ##
***

在主库运行 **LOAD DATA LOCAL INFILE**，主库和从库时这样同步的：

* 在主节点：
  1. 执行 LOAD DATA LOCAL INFILE；
  2. 拷贝使用的整个文本文件内容到二进制日志；
  3. 添加 LOAD DATA LOCAL INFILE 到最新的二进制日志。
* 复制所有主库的二进制日志到从库的中继日志；
* 在从节点：
  1. 检查中继日志中的文本文件；
  2. 从多个中继日志文件中读取所有的块；
  3. 文本文件存放在 /tmp 文件夹中；
  4. 从中继日志中读取 LOAD DATA LOCAL INFILE；
  5. 在 SQL 线程中执行 LOAD DATA LOCAL INFILE。

在从节点执行的 1-4 步骤中，IO 线程会呈现 Reading event from the relay log 状态，持续地为下一个  LOAD DATA LOCAL INFILE 命令提取 CSV 行。此时从库会持续落后，一旦从库落后时间较长，会导致 SQL 线程阻塞，呈现 Queueing master event to the relay log 状态，从而复制夯住。

![2015-08-22-mysql-replication-hanging](https://cdn.dbarobin.com/TLCA1lt.jpg)

## 五 小结 ##
***

这样的故障，归根结底还是研发写的程序还有优化的余地。大批量的数据插入，这在 MySQL 中是不推荐使用的。我们可以这样：第一，一条 SQL 语句插入多条数据；第二，在事务中进行插入处理；第三，分批插入，在程序中设置 auto_commit 为 0，分批插入完成后，手动 COMMIT；第四，需要使用 LOAD DATA LOCAL INFILE 时，设置 sync_binlog 为 1。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

题图来自：<a href="http://blog.secaserver.com/2011/06/the-best-way-to-setup-mysq-replication/" target="_blank">secaserver.com</a>, By SecaGuy.

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
