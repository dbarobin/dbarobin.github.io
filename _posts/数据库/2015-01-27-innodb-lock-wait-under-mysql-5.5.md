---
published: true
author: Robin Wen
layout: post
title: "MySQL 5.5 InnoDB 锁等待"
category: 数据库
summary: "MySQL 5.5 中，information_schema 库中新增了三个关于锁的表，亦即 innodb_trx、innodb_locks 和 innodb_lock_waits。
其中 innodb_trx 表记录当前运行的所有事务，innodb_locks 表记录当前出现的锁，innodb_lock_waits 表记录锁等待的对应关系。"
tags:
- 数据库
- Database
- MySQL
- 故障排查
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

## 一  引子 ##

MySQL 5.5 中，information_schema 库中新增了三个关于锁的表，亦即 **innodb_trx**、**innodb_locks** 和 **innodb_lock_waits**。

其中 `innodb_trx` 表记录当前运行的所有事务，`innodb_locks` 表记录当前出现的锁，`innodb_lock_waits` 表记录锁等待的对应关系。

## 二 表结构说明 ##

登录 MySQL 5.5。

``` bash
mysql -S /tmp/mysql_5540.sock -uroot -proot
```

这是我的 MySQL 版本信息。

``` bash
mysql> SHOW VARIABLES LIKE '%version%';
+-------------------------+------------------------------+
| Variable_name           | Value                        |
+-------------------------+------------------------------+
| innodb_version          | 5.5.40                       |
| protocol_version        | 10                           |
| slave_type_conversions  |                              |
| version                 | 5.5.40                       |
| version_comment         | MySQL Community Server (GPL) |
| version_compile_machine | i386                         |
| version_compile_os      | osx10.6                      |
+-------------------------+------------------------------+
7 rows in set (0.00 sec)
```

查看 innodb_trx 表结构。

``` bash
mysql> USE information_schema;
mysql> DESC innodb_trx;
```

下面对 innodb_trx 表的每个字段进行解释：

> trx_id：事务ID。
> trx_state：事务状态，有以下几种状态：RUNNING、LOCK WAIT、ROLLING BACK 和 COMMITTING。
> trx_started：事务开始时间。
> trx_requested_lock_id：事务当前正在等待锁的标识，可以和 INNODB_LOCKS 表 JOIN 以得到更多详细信息。
> trx_wait_started：事务开始等待的时间。
> trx_weight：事务的权重。
> trx_mysql_thread_id：事务线程 ID，可以和 PROCESSLIST 表 JOIN。
> trx_query：事务正在执行的 SQL 语句。
> trx_operation_state：事务当前操作状态。
> trx_tables_in_use：当前事务执行的 SQL 中使用的表的个数。
> trx_tables_locked：当前执行 SQL 的行锁数量。
> trx_lock_structs：事务保留的锁数量。
> trx_lock_memory_bytes：事务锁住的内存大小，单位为 BYTES。
> trx_rows_locked：事务锁住的记录数。包含标记为 DELETED，并且已经保存到磁盘但对事务不可见的行。
> trx_rows_modified：事务更改的行数。
> trx_concurrency_tickets：事务并发票数。
> trx_isolation_level：当前事务的隔离级别。
> trx_unique_checks：是否打开唯一性检查的标识。
> trx_foreign_key_checks：是否打开外键检查的标识。
> trx_last_foreign_key_error：最后一次的外键错误信息。
> trx_adaptive_hash_latched：自适应散列索引是否被当前事务锁住的标识。
> trx_adaptive_hash_timeout：是否立刻放弃为自适应散列索引搜索 LATCH 的标识。

查看 innodb_locks 表结构。

``` bash
mysql> DESC innodb_locks;
```

下面对 innodb_locks 表的每个字段进行解释：

> lock_id：锁 ID。
> lock_trx_id：拥有锁的事务 ID。可以和 INNODB_TRX 表 JOIN 得到事务的详细信息。
> lock_mode：锁的模式。有如下锁类型：行级锁包括：S、X、IS、IX，分别代表：共享锁、排它锁、意向共享锁、意向排它锁。表级锁包括：S_GAP、X_GAP、IS_GAP、IX_GAP 和 AUTO_INC，分别代表共享间隙锁、排它间隙锁、意向共享间隙锁、意向排它间隙锁和自动递增锁。
> lock_type：锁的类型。RECORD 代表行级锁，TABLE 代表表级锁。
> lock_table：被锁定的或者包含锁定记录的表的名称。
> lock_index：当 LOCK_TYPE='RECORD' 时，表示索引的名称；否则为  NULL。
> lock_space：当 LOCK_TYPE='RECORD' 时，表示锁定行的表空间 ID；否则为  NULL。
> lock_page：当 LOCK_TYPE='RECORD' 时，表示锁定行的页号；否则为  NULL。
> lock_rec：当 LOCK_TYPE='RECORD' 时，表示一堆页面中锁定行的数量，亦即被锁定的记录号；否则为  NULL。
> lock_data：当 LOCK_TYPE='RECORD' 时，表示锁定行的主键；否则为NULL。

查看 innodb_lock_waits 表结构。

``` bash
mysql> DESC innodb_lock_waits;
```

下面对 innodb_lock_waits 表的每个字段进行解释：

> requesting_trx_id：请求事务的 ID。
> requested_lock_id：事务所等待的锁定的 ID。可以和 INNODB_LOCKS 表 JOIN。
> blocking_trx_id：阻塞事务的 ID。
> blocking_lock_id：某一事务的锁的 ID，该事务阻塞了另一事务的运行。可以和 INNODB_LOCKS 表 JOIN。

## 三 INNODB 锁等待模拟 ##

### 3.1 创建测试表，录入测试数据 ###

创建测试表，录入测试数据。

``` bash
mysql> USE test;

mysql> CREATE TABLE user
    -> (id INT PRIMARY KEY,
    -> name VARCHAR(20),
    -> age INT,
    -> sex CHAR(2),
    -> city VARCHAR(20),
    -> job VARCHAR(10)
    -> ) DEFAULT CHARSET utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.15 sec)

mysql> INSERT INTO user(id, name, age, sex, city, job)  \
       -> VALUES(1, 'robin', 19, 'M', 'GZ', 'DBA');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO user(id, name, age, sex, city, job)  \
       -> VALUES(2, 'Wentasy', 19, 'M', 'GZ', 'DBA');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO user(id, name, age, sex, city, job)  \
       -> VALUES(3, 'dbarobin', 19, 'M', 'GZ', 'DBA');
Query OK, 1 row affected (0.00 sec)

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)
```

### 3.2 模拟锁等待 ###

Session 1 开始事务。

``` bash
mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> UPDATE user SET name='wentasy' WHERE id = 2;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

-- 此时已经开始事务，所以 innodb_trx 表会有记录。
mysql> SELECT * FROM information_schema.innodb_trx \G
*************************** 1. row ***************************
                    trx_id: 360E
                 trx_state: RUNNING
               trx_started: 2015-01-27 15:23:49
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 3
       trx_mysql_thread_id: 1
                 trx_query: SELECT * FROM information_schema.innodb_trx
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 0
          trx_lock_structs: 2
     trx_lock_memory_bytes: 376
           trx_rows_locked: 1
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 10000
1 row in set (0.00 sec)

-- 此时没有发生锁等待，故 innodb_locks表 和 innodb_lock_waits 表都没有数据。
mysql> SELECT * FROM information_schema.innodb_locks \G
Empty set (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_lock_waits \G
Empty set (0.00 sec)
```

Session 2 更新数据。

``` bash
mysql -S /tmp/mysql_5540.sock -uroot -proot
```

``` bash
mysql> USE test;
mysql> UPDATE user SET name="lock_waits" WHERE ID = 2;
```

Session 1 查看 innodb_trx 表、innodb_locks 表和 innodb_lock_waits 表，可以查看到数据。

在 innodb_trx 表的第一行，trx_id 为 360F 表示第二个事务，状态为等待状态，请求的锁 ID 为 360F:243:3:3，线程 ID 为 2，事务用到的表为 1，有 1 个表被锁。第二行中，trx_id 为 360E 表示第一个事务。

``` bash
mysql> SELECT * FROM information_schema.innodb_trx \G
*************************** 1. row ***************************
                    trx_id: 360F
                 trx_state: LOCK WAIT
               trx_started: 2015-01-27 15:28:48
     trx_requested_lock_id: 360F:243:3:3
          trx_wait_started: 2015-01-27 15:28:48
                trx_weight: 2
       trx_mysql_thread_id: 2
                 trx_query: UPDATE user SET name="lock_waits" WHERE ID = 2
       trx_operation_state: starting index read
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 376
           trx_rows_locked: 1
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 10000
*************************** 2. row ***************************
                    trx_id: 360E
                 trx_state: RUNNING
               trx_started: 2015-01-27 15:23:49
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 3
       trx_mysql_thread_id: 1
                 trx_query: SELECT * FROM information_schema.innodb_trx
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 0
          trx_lock_structs: 2
     trx_lock_memory_bytes: 376
           trx_rows_locked: 1
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 10000
2 rows in set (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_locks \G
*************************** 1. row ***************************
    lock_id: 360F:243:3:3
lock_trx_id: 360F
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
*************************** 2. row ***************************
    lock_id: 360E:243:3:3
lock_trx_id: 360E
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
2 rows in set (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_lock_waits \G
*************************** 1. row ***************************
requesting_trx_id: 360F
requested_lock_id: 360F:243:3:3
  blocking_trx_id: 360E
 blocking_lock_id: 360E:243:3:3
1 row in set (0.00 sec)
```

由于默认的 `innodb_lock_wait_timeout` 是 50 秒，所以 50 秒过后，Session 2 出现如下提示：

> ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
>  ">

### 3.3 再次模拟锁等待 ###

再次模拟锁等待之前，把 `innodb_lock_wait_timeout` 设置为 10 分钟，方便后面的演示。

``` bash
mysql> SHOW VARIABLES LIKE '%innodb_lock_wait%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 50    |
+--------------------------+-------+
1 row in set (0.00 sec)

mysql> SET innodb_lock_wait_timeout=600;
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL innodb_lock_wait_timeout=600;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE '%innodb_lock_wait%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 600   |
+--------------------------+-------+
1 row in set (0.00 sec)
```

再次开启一个 Session，此时的 Session 姑且命名为 Session 3。然后再次更新数据，由于 Session 1 未提交，所以会发生锁等待。

``` bash
mysql> USE test;
mysql> UPDATE user SET name="lock_waits" WHERE ID = 2;
```

### 3.4 查询锁等待 ###

此时再次发生锁等待。我们在 Session 1 中使用不同的查询方法查看锁等待。

### 3.4.1 直接查看 innodb_lock_waits 表 ###

``` bash
mysql> SELECT * FROM innodb_lock_waits \G
*************************** 1. row ***************************
requesting_trx_id: 3612
requested_lock_id: 3612:243:3:3
  blocking_trx_id: 360E
 blocking_lock_id: 360E:243:3:3
1 row in set (0.00 sec)
```

### 3.4.2 innodb_locks 表和 innodb_lock_waits 表结合  ###

``` bash
mysql> SELECT * \
        >  FROM innodb_locks \
        >  WHERE lock_trx_id \
        >  IN (SELECT blocking_trx_id FROM innodb_lock_waits) \G
*************************** 1. row ***************************
    lock_id: 360E:243:3:3
lock_trx_id: 360E
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
1 row in set (0.00 sec)
```

### 3.4.3 innodb_locks 表 JOIN innodb_lock_waits 表  ###

``` bash
mysql> SELECT innodb_locks.* \
        >  FROM innodb_locks \
        >  JOIN innodb_lock_waits \
        >  ON (innodb_locks.lock_trx_id = innodb_lock_waits.blocking_trx_id) \G
*************************** 1. row ***************************
    lock_id: 360E:243:3:3
lock_trx_id: 360E
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
1 row in set (0.01 sec)
```

### 3.4.4 指定 innodb_locks 表的 lock_table 属性  ###

需要注意 `lock_table` 值的写法。

``` bash
mysql> SELECT * FROM innodb_locks \
        >  WHERE lock_table = '`test`.`user`' \G
*************************** 1. row ***************************
    lock_id: 3612:243:3:3
lock_trx_id: 3612
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
*************************** 2. row ***************************
    lock_id: 360E:243:3:3
lock_trx_id: 360E
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`user`
 lock_index: `PRIMARY`
 lock_space: 243
  lock_page: 3
   lock_rec: 3
  lock_data: 2
2 rows in set (0.00 sec)
```

### 3.4.5 查询 innodb_trx 表  ###

``` bash
mysql> SELECT trx_id, trx_requested_lock_id, trx_mysql_thread_id, trx_query \
        >  FROM innodb_trx \
        >  WHERE trx_state = 'LOCK WAIT' \G
*************************** 1. row ***************************
               trx_id: 3612
trx_requested_lock_id: 3612:243:3:3
  trx_mysql_thread_id: 9
            trx_query: UPDATE user SET name="lock_waits" WHERE ID = 2
1 row in set (0.00 sec)
```

### 3.4.6 SHOW ENGINE INNODB STATUS  ###

``` bash
mysql> SHOW ENGINE INNODB STATUS \G
```

在输出结果的最后，我们看到如下信息：

``` bash
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
1 read views open inside InnoDB
Main thread id 4525080576, state: waiting for server activity
Number of rows inserted 6, updated 1, deleted 0, read 2
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)
```

### 3.4.7 SHOW PROCESSLIST ###

``` bash
mysql> SHOW PROCESSLIST \G
*************************** 1. row ***************************
     Id: 1
   User: root
   Host: localhost
     db: information_schema
Command: Query
   Time: 0
  State: NULL
   Info: SHOW PROCESSLIST
*************************** 2. row ***************************
     Id: 9
   User: root
   Host: localhost
     db: test
Command: Query
   Time: 116
  State: Updating
   Info: UPDATE user SET name="lock_waits" WHERE ID = 2
2 rows in set (0.00 sec)
```

## 3.5 解决锁等待 ##

既然我们从上述方法中得到了相关信息，我们可以得到发生锁等待的线程 ID，然后将其 KILL 掉。

Session 1 中 KILL 掉发生锁等待的线程。

``` bash
mysql> kill 9;
Query OK, 0 rows affected (0.00 sec)
```

Session 3 中可以看到锁等待消除。

``` bash
mysql> UPDATE user SET name="lock_waits" WHERE ID = 2;
```

有如下输出：

> ERROR 2013 (HY000): Lost connection to MySQL server during query

Session 1 中再次查看 PROCESSLIST，可以看到没有相关的信息了。

``` bash
mysql> SHOW PROCESSLIST \G
*************************** 1. row ***************************
     Id: 1
   User: root
   Host: localhost
     db: information_schema
Command: Query
   Time: 0
  State: NULL
   Info: SHOW PROCESSLIST
1 row in set (0.00 sec)
```

模拟完成后，我们提交，此时 innodb_trx 表、innodb_locks 表和 innodb_lock_waits 表中都没有数据。

``` bash
mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_trx \G
Empty set (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_locks \G
Empty set (0.00 sec)

mysql> SELECT * FROM information_schema.innodb_lock_waits \G
Empty set (0.00 sec)
```

把 `innodb_lock_wait` 还原为默认值。

``` bash
mysql> SHOW VARIABLES LIKE '%innodb_lock_wait%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 600   |
+--------------------------+-------+
1 row in set (0.00 sec)

mysql> SET GLOBAL innodb_lock_wait_timeout=50;
Query OK, 0 rows affected (0.00 sec)

mysql> SET innodb_lock_wait_timeout=50;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE '%innodb_lock_wait%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 50    |
+--------------------------+-------+
1 row in set (0.00 sec)
```

## 四  小结 ##

* information_schema 库中新增了三个关于锁的表，亦即 innodb_trx、innodb_locks 和 innodb_lock_waits；
* innodb_trx 表记录当前运行的所有事务；
* innodb_locks 表记录当前出现的锁；
* innodb_lock_waits 表记录锁等待的对应关系；
* 获得锁等待的技巧
    * 从 innodb_trx、innodb_locks 和 innodb_lock_waits 表中得到；
    * SHOW ENGINE INNODB STATUS；
    * SHOW FULL PROCESSLIST；
    * 启用 InnoDB Lock Monitor；
    * 运行 mysqladmin debug；
    * MySQK Error Log；
    * SHOW CREATE TABLE 输出；
* 发生锁等待会引起系统资源的大量浪费，合理的监控和处理锁等待很重要。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 五 Ref ##

* <a href="http://dev.mysql.com/doc/refman/5.5/en/innodb-trx-table.html" target="_blank">21.28.3 The INFORMATION_SCHEMA INNODB_TRX Table</a>
* <a href="http://dev.mysql.com/doc/refman/5.5/en/innodb-locks-table.html" target="_blank">21.28.4 The INFORMATION_SCHEMA INNODB_LOCKS Table</a>
* <a href="http://dev.mysql.com/doc/refman/5.5/en/innodb-lock-waits-table.html" target="_blank">21.28.5 The INFORMATION_SCHEMA INNODB_LOCK_WAITS Table</a>
* <a href="http://stackoverflow.com/questions/13148630/how-do-i-find-which-transaction-is-causing-a-waiting-for-table-metadata-lock-s" target="_blank">How do I find which transaction is causing a “Waiting for table metadata lock” state?</a>
* <a href="http://stackoverflow.com/questions/5836623/getting-lock-wait-timeout-exceeded-try-restarting-transaction-even-though-im" target="_blank">Getting “Lock wait timeout exceeded; try restarting transaction” even though I'm not using a transaction</a>
* <a href="http://www.chriscalender.com/tag/information_schema-innodb_lock_waits/" target="_blank">Advanced InnoDB Deadlock Troubleshooting – What SHOW INNODB STATUS Doesn’t Tell You, and What Diagnostics You Should be Looking At</a>
* <a href="http://hedengcheng.com/?p=771" target="_blank">MySQL 加锁处理分析</a>


–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
