---
published: true
author: Robin Wen
layout: post
title: "MySQL 每次查询一条数据查询十次与一次查询十条数据之间的区别"
category: 数据库
summary: "有个知友邀请我回答问题，问道：「MySQL 每次查询一条数据查 10 次和一次查询 10 条数据效率有多少差距？」总体上来说，一次查询 10 条数据效率是高于每次查询一条数据查 10 次的。但究竟差距多少，具体的数据很难说。这本来是一个很简单的问题，但我还是想亲身实践下，给以后碰到这个问题的朋友一点参考。我先做一个模拟，然后在文末给出一个分析。"
tags:
- 数据库
- Database
- MySQL
- 性能优化
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 引子 ##

有个知友邀请我回答问题，问道：<a href="http://www.zhihu.com/question/28422374" target="_blank">「MySQL 每次查询一条数据查 10 次和一次查询 10 条数据效率有多少差距？」</a>

总体上来说，一次查询 10 条数据效率是高于每次查询一条数据查 10 次的。但究竟差距多少，具体的数据很难说。这本来是一个很简单的问题，但我还是想亲身实践下，给以后碰到这个问题的朋友一点参考。我先做一个模拟，然后在文末给出一个分析。

**说明：本文中的模拟只能提供参考。实际情况跟硬件配置、系统负载等因素相关。**

## 二 模拟 ##

在做模拟之前，得有数据。所以我创建了一组测试数据，如下：

``` bash
mysql> USE test;
Database changed

mysql> CREATE TABLE user
    -> (id INT PRIMARY KEY AUTO_INCREMENT,
    -> name VARCHAR(20),
    -> age INT,
    -> sex CHAR(2),
    -> city VARCHAR(20),
    -> work VARCHAR(10)
    -> ) DEFAULT CHARSET utf8 ENGINE = INNODB;
Query OK, 0 rows affected (0.10 sec)

mysql> INSERT INTO user(name, age, sex, city, work) \
    -> VALUES("robin01",19,"M","GuangZhou","DBA"),\
    -> ("robin02",19,"M","GuangZhou","DBA"),\
    -> ("robin03",19,"M","GuangZhou","DBA"),\
    -> ("robin04",19,"M","GuangZhou","DBA"),\
    -> ("robin05",19,"M","GuangZhou","DBA"),\
    -> ("robin06",19,"M","GuangZhou","DBA"),\
    -> ("robin07",19,"M","GuangZhou","DBA"),\
    -> ("robin08",19,"M","GuangZhou","DBA"),\
    -> ("robin09",19,"M","GuangZhou","DBA"),\
    -> ("robin10",19,"M","GuangZhou","DBA"),\
    -> ("robin11",19,"M","GuangZhou","DBA"),\
    -> ("robin12",19,"M","GuangZhou","DBA"),\
    -> ("robin13",19,"M","GuangZhou","DBA"),\
    -> ("robin14",19,"M","GuangZhou","DBA"),\
    -> ("robin15",19,"M","GuangZhou","DBA");
Query OK, 15 rows affected (0.03 sec)
Records: 15  Duplicates: 0  Warnings: 0
```

接着，为了模拟一条数据查询十次，我写了一个存储过程。这个存储过程也很简单，如下：

**说明：这里的模拟如果这样会更好：不用循环，写十条 SQL，ID 不同。查询相同的数据会受查询缓存的影响，多少有些偏差。数据少，差别不是太大，所以这里还是这样模拟了。**

``` bash
vim /tmp/proc_loop.sql
```

``` bash
delimiter //
DROP PROCEDURE IF EXISTS proc_loop_test;
CREATE PROCEDURE proc_loop_test()
BEGIN
   DECLARE int_val INT DEFAULT 0;
   test_loop : LOOP
      IF (int_val = 10) THEN
         LEAVE test_loop;
      END IF;

   SELECT * FROM user WHERE id = 7;

   SET int_val = int_val + 1;
   END LOOP;
END //

delimiter ;
```

然后，执行此外部 SQL。在调用此存储过程之前，我设置了 profiling ＝ 1，目的是统计 SQL 执行时间（只截取了需要的数据）。数据量比较少，耗费时间都是毫秒级，甚至更少。所以采用了这个笨办法。如下：

``` bash
mysql> source /tmp/proc_loop.sql
Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql> SET profiling = 1;
Query OK, 0 rows affected (0.00 sec)

mysql> call proc_loop_test();

mysql> SHOW PROFILES;
+----------+------------+---------------------------------+
| Query_ID | Duration   | Query                           |
+----------+------------+---------------------------------+
|       13 | 0.00019700 | SELECT * FROM user WHERE id = 7 |
|       14 | 0.00009800 | SELECT * FROM user WHERE id = 7 |
|       15 | 0.00016200 | SELECT * FROM user WHERE id = 7 |
|       16 | 0.00016100 | SELECT * FROM user WHERE id = 7 |
|       17 | 0.00012100 | SELECT * FROM user WHERE id = 7 |
|       18 | 0.00014500 | SELECT * FROM user WHERE id = 7 |
|       19 | 0.00010000 | SELECT * FROM user WHERE id = 7 |
|       20 | 0.00010300 | SELECT * FROM user WHERE id = 7 |
|       21 | 0.00009300 | SELECT * FROM user WHERE id = 7 |
|       22 | 0.00009300 | SELECT * FROM user WHERE id = 7 |
+----------+------------+---------------------------------+
15 rows in set (0.00 sec)
```

再接着，利用分页一条 SQL 查询 10 条数据，并且查询所用时间（只截取了需要的数据）。

``` bash
mysql> SELECT * FROM user LIMIT 0,10;
+----+---------+------+------+-----------+------+
| id | name    | age  | sex  | city      | work |
+----+---------+------+------+-----------+------+
|  1 | robin01 |   19 | M    | GuangZhou | DBA  |
|  2 | robin02 |   19 | M    | GuangZhou | DBA  |
|  3 | robin03 |   19 | M    | GuangZhou | DBA  |
|  4 | robin04 |   19 | M    | GuangZhou | DBA  |
|  5 | robin05 |   19 | M    | GuangZhou | DBA  |
|  6 | robin06 |   19 | M    | GuangZhou | DBA  |
|  7 | robin07 |   19 | M    | GuangZhou | DBA  |
|  8 | robin08 |   19 | M    | GuangZhou | DBA  |
|  9 | robin09 |   19 | M    | GuangZhou | DBA  |
| 10 | robin10 |   19 | M    | GuangZhou | DBA  |
+----+---------+------+------+-----------+------+
10 rows in set (0.00 sec)

mysql> SHOW PROFILES;
+----------+------------+-------------------------------+
| Query_ID | Duration   | Query                         |
+----------+------------+-------------------------------+
|        1 | 0.00030400 | SELECT * FROM user LIMIT 0,10 |
+----------+------------+-------------------------------+
1 row in set (0.00 sec)
```

最后，统计每次查询一条数据查询 10 此所需时间，完成后，计算和一次查询 10 条数据耗费时间的比值，可以看到，每次查询一条数据，查询 10 次耗费时间为 0.00127300 秒，一次查询 10 条数据耗费时间为 0.00030400 秒，他们之间的比值为 4.1875。如果数据量够大，数据够复杂，这个比值会更大的。

``` bash
mysql> SELECT 0.00019700 + 0.00009800 + 0.00016200 + \
       -> 0.00016100 + 0.00012100 + 0.00014500 + 0.00010000 \
       -> + 0.00010300 + 0.00009300 + 0.00009300 \
       -> AS mutiple_select;
+----------------+
| mutiple_select |
+----------------+
|     0.00127300 |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT 0.00127300/0.00030400 AS times;
+----------------+
| times          |
+----------------+
| 4.187500000000 |
+----------------+
1 row in set (0.00 sec)
```

## 三 分析 ##

MySQL 中，每一次查询要经过如下过程：

* SQL 接口（SQL Interface）接受用户输入的 SQL 命令，此时会建立 Socket 连接；
* SQL 命令传递到解析器（Parser）的时候会被解析器验证和解析，将 SQL 语句分解成数据结构，并将这个结构传递到后续步骤，以后 SQL 语句的传递和处理就是基于这个结构；如果在分解构成中遇到错误，那么就说明这个 SQL 语句是不合理的。
* SQL 语句在查询之前会使用查询优化器（Optimizer）对查询进行优化，构建查询计划；
* 如果查询缓存有命中的查询结果，查询语句就可以直接去查询缓存中取数据。这一部分是通过查询缓存（Cache 和 Buffer）实现。
* 利用存储引擎（Engine）和磁盘进行交互，从硬盘读取数据；
* SQL 接口（SQL Interface）返回用户需要查询的结果。此时 SQL 执行已经完成，关闭 Socket 连接。

读者可以参考 MySQL Architecture：

![MySQL Architecture](https://cdn.dbarobin.com/xCOBqKX.jpg)

读者请看，一个正确的 SQL 查询需要经历以上步骤，稍显复杂。获得 10 条数据，查询十次，每一次都要经历上述过程，耗费的时间不用说，也比查询 1 次要多。所以，推荐的是 1 次就获取 10 条数据，而不是执行 10 次。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

题图来自：<a href="http://www.oracle.com/technetwork/articles/javase/figure2-large-145676.jpg" target="_blank"><img src="https://cdn.dbarobin.com/mvKAMvm.png" title="MySQL Architecture" height="16px" width="16px" border="0" alt="MySQL Architecture" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
