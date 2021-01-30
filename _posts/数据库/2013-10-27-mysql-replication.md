---
published: true
author: Robin Wen
layout: post
title: "MySQL AB复制"
category: 数据库
summary: "本文讲解如何快速打包和安装MySQL， MySQL AB复制，MySQL AB双向复制，MySQL多级主从复制，解决AB双向复制主键冲突。"
tags:
- 数据库
- Database
- MySQL
- 复制
- Replication
- 高可用
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 关于MySQL AB复制 ##

本文讲解如何快速打包和安装MySQL， MySQL AB复制，MySQL AB双向复制，MySQL多级主从复制，解决AB双向复制主键冲突。

首先我们先介绍什么是MySQL AB复制。

AB复制又称主从复制，实现的是数据同步。如果要做MySQL AB复制，数据库版本尽量保持一致。如果版本不一致，从服务器版本高于主服务器，但是版本不一致不能做双向复制。MySQL AB复制有什么好处呢？有两点，第一是解决宕机带来的数据不一致，因为MySQL AB复制可以实时备份数据；第二点是减轻数据库服务器压力，这点很容易想到，多台服务器的性能一般比单台要好。但是MySQL AB复制不适用于大数据量，如果是大数据环境，推荐使用集群。

然后我们来看看MySQL复制的 3 个主要步骤：

> 1)主服务器把数据更改记录到二进制日志中，这个操作叫做二进制日志事件；
> 2)从服务器把主服务器的二进制日志事件拷贝到自己的中继日志（relay log）中；
> 3)从服务器执行中继日志中的事件，把更改应用到自己的数据上。

## 快速打包和安装MySQL ##

在正式介绍MySQL AB复制之前，介绍怎样打包MySQL和快速安装MySQL。

第一步，制作文件。
``` bash
find /usr/local/mysql/ /etc/my.cnf /etc/init.d/mysqld > mysql
```

第二步，打包。

``` bash
tar -cPvzf mysql-5.5.29-linux2.6-x86_64.tar.gz -T mysql
ll -h
```

第三步，拷贝文件到实体机。

``` bash
scp mysql-5.5.29-linux2.6-x86_64.tar.gz 192.168.1.1:/home/Wentasy/software/
```

第四步，拷贝文件到serv01。

``` bash
yum install /usr/bin/scp -y
scp /home/Wentasy/software/mysql-5.5.29-linux2.6-x86_64.tar.gz 192.168.1.11:/opt
```

第五步，解压。

``` bash
tar -xPvf mysql-5.5.29-linux2.6-x86_64.tar.gz
```

第六步，创建组和用户，注意编号和安装好数据库的机器上的用户一致。

``` bash
groupadd -g 500 mysql
useradd -u 500 -g 500 -r -M -s /sbin/nologin mysql
id mysql
```

第七步，改变MySQL安装目录的拥有者和所属组。

``` bash
chown mysql.mysql /usr/local/mysql/ -R
```

第八步，启动MySQL，做测试。

``` bash
/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!

# 将mysql命令加入到profile。
vim ~/.bash_profile
. !$
mysql
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## MySQL AB单向复制 ##

好了，相信读者已经学会怎样打包MySQL和快速安装MySQL，接下来正式进入主题，我们先来看看一主多从架构的拓扑图：
![图一 一主多从架构](https://cdn.dbarobin.com/twFnSZY.jpg)
图一 一主多从架构

该图展示了一个 master 复制多个 slave 的架构，多个 slave 和单个 slave 的实施并没有实质性的区别，在 master 端并不在乎有多少个 slave 连接自己，只要有 slave 的 IO 线程通过了连接认证，向他请求指定位置之后的 binary log 信息，他就会按照该 IO 线程的要球，读取自己的binary log 信息，返回给 slave的 IO 线程。

既然对拓扑图和原理有所了解，我们做一个实验，介绍如何使用MySQL AB复制：

**实验环境介绍**

|----------+------------+-----------------+-----------|
| 主机           | IP地址             |主机名                      | 备注              |
|:----------:|:------------|:-----------------|:-----------:|
|serv01：   |192.168.1.11     | serv01.host.com  | master            |
|serv08：   |192.168.1.18     | serv08.host.com  | slave01           |
|----------+------------+-----------------+-----------|

**操作系统版本：**rhel server 6.1 <br/>
**所需要的软件包：**mysql-5.5.29-linux2.6-x86_64.tar.gz

第一步，主服务器创建用户并清空日志。

``` bash
mysql> show privileges;
mysql> grant replication client, \
replication slave on *.* to 'larry'@'192.168.1.%' \
identified by 'larry';
Query OK, 0 rows affected (0.00 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     27320 |
| mysql-bin.000002 |   1035309 |
| mysql-bin.000003 |       126 |
| mysql-bin.000004 |       279 |
+------------------+-----------+
4 rows in set (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.01 sec)

mysql> reset master;
Query OK, 0 rows affected (0.02 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       107 |
+------------------+-----------+
1 row in set (0.00 sec)
```

第二步，修改从服务器的server-id。

``` bash
cat /etc/my.cnf | grep server-id
server-id = 1
#server-id       = 2

vim /etc/my.cnf
cat /etc/my.cnf | grep server-id
server-id = 2
#server-id       = 2

/etc/init.d/mysqld restart
Shutting down MySQL... SUCCESS!
Starting MySQL.. SUCCESS!

#可以查看从服务器中的数据文件
cd /usr/local/mysql/data/
ll
```

第三步，从服务器清空日志。

``` bash
mysql> show binary logs;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    1
Current database: *** NONE ***

+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     27320 |
| mysql-bin.000002 |   1035309 |
| mysql-bin.000003 |       126 |
| mysql-bin.000004 |       126 |
| mysql-bin.000005 |       107 |
+------------------+-----------+
5 rows in set (0.00 sec)

mysql> reset master;
Query OK, 0 rows affected (0.02 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       107 |
+------------------+-----------+
1 row in set (0.00 sec)

mysql> show slave status;
Empty set (0.00 sec)
```

第四步，从服务器通过change master to命令修改设置。

``` bash
mysql> change master to
    -> master_host='192.168.1.11',
    -> master_user='larry',
    -> master_password='larry',
    -> master_port=3306,
    -> master_log_file='mysql-bin.000001',
    -> master_log_pos=107;
Query OK, 0 rows affected (0.01 sec)
```

第五步，开启slave。

``` bash
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 192.168.1.11
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 107
               Relay_Log_File: serv08-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000001
             **Slave_IO_Running: No**
            **Slave_SQL_Running: No**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 107
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> start slave;
Query OK, 0 rows affected (0.01 sec)
```

第六步，从服务器查看是否和主服务器通信成功。如果出现 Slave_IO_Running和Slave_SQL_Running都是yes，则证明配置成功。

``` bash
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.11
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 107
               Relay_Log_File: serv08-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000001
             **Slave_IO_Running: Yes**
            **Slave_SQL_Running: Yes**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 410
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)

ERROR:
No query specified
```
mysql> show slave status \G;

第七步，从服务器查看数据文件的更改.

``` bash
ll
total 28724
-rw-rw----. 1 mysql mysql 18874368 Oct  5 19:45 ibdata1
-rw-rw----. 1 mysql mysql  5242880 Oct  5 19:45 ib_logfile0
-rw-rw----. 1 mysql mysql  5242880 Oct  5 18:16 ib_logfile1
-rw-rw----. 1 mysql mysql       78 Oct  5 19:49 master.info
drwxr-xr-x. 2 mysql mysql     4096 Oct  5 18:15 mysql
-rw-rw----. 1 mysql mysql      107 Oct  5 19:45 mysql-bin.000001
-rw-rw----. 1 mysql mysql       19 Oct  5 19:45 mysql-bin.index
drwx------. 2 mysql mysql     4096 Oct  5 18:15 performance_schema
-rw-rw----. 1 mysql mysql       51 Oct  5 19:49 relay-log.info
-rw-r-----. 1 mysql root      5589 Oct  5 19:49 serv08.host.com.err
-rw-rw----. 1 mysql mysql        5 Oct  5 19:45 serv08.host.com.pid
-rw-rw----. 1 mysql mysql      157 Oct  5 19:49 serv08-relay-bin.000001
-rw-rw----. 1 mysql mysql      253 Oct  5 19:49 serv08-relay-bin.000002
-rw-rw----. 1 mysql mysql       52 Oct  5 19:49 serv08-relay-bin.index
drwxr-xr-x. 2 mysql mysql     4096 Oct  5 18:12 test

cat relay-log.info
./serv08-relay-bin.000002
253
mysql-bin.000001
107

cat master.info
18
mysql-bin.000001
107
192.168.1.11
larry
larry
3306
```

第八步，测试。

``` bash
--serv08查看数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.02 sec)

--serv01创建数据库
mysql> create database larrydb;
Query OK, 1 row affected (0.00 sec)
--serv01查看数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| larrydb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.01 sec)

--serv08查看数据库，发现已经同步
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| larrydb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)

--serv01创建表 插入数据
mysql> use larrydb;
Database changed
mysql> create table test(id int(11));
Query OK, 0 rows affected (0.00 sec)

mysql> insert into test values(1);
Query OK, 1 row affected (0.00 sec)

--serv08查看数据是否同步成功，发现数据已经同步
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| test              |
+-------------------+
1 row in set (0.00 sec)

mysql> select * from test;
+------+
| id   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
```

第九步，查看进程状态。

``` bash
--serv01查看进程状态
mysql> show processlist;
+----+-------+--------------------+---------+-------------+------+-----------------------------------------------------------------------+------------------+
| Id | User  | Host               | db      | Command     | Time | State                                                                 | Info             |
+----+-------+--------------------+---------+-------------+------+-----------------------------------------------------------------------+------------------+
|  1 | root  | localhost          | larrydb | Query       |    0 | NULL                                                                  | show processlist |
|  2 | larry | 192.168.1.18:41393 | NULL    | Binlog Dump |  854 | Master has sent all binlog to slave; waiting for binlog to be updated | NULL             |
+----+-------+--------------------+---------+-------------+------+-----------------------------------------------------------------------+------------------+
2 rows in set (0.00 sec)

--serv08查看进程状态
mysql> show processlist;
+----+-------------+-----------+---------+---------+------+-----------------------------------------------------------------------------+------------------+
| Id | User        | Host      | db      | Command | Time | State                                                                       | Info             |
+----+-------------+-----------+---------+---------+------+-----------------------------------------------------------------------------+------------------+
|  1 | root        | localhost | larrydb | Query   |    0 | NULL                                                                        | show processlist |
|  2 | system user |           | NULL    | Connect |  880 | Waiting for master to send event                                            | NULL             |
|  3 | system user |           | NULL    | Connect |   65 | Slave has read all relay log; waiting for the slave I/O thread to update it | NULL             |
+----+-------------+-----------+---------+---------+------+-----------------------------------------------------------------------------+------------------+
3 rows in set (0.00 sec)
```

## MySQLAB双向复制 ##

好了，MySQL AB单向复制介绍完毕。接下来想想，会有这样的应用场景。比如Master和Slave之间都要进行数据同步，那么单向复制是无法完成的，因为一个是Master，一个是Slave，只能单向操作，这就像网络里的半双工一样。既然一方可以向另一方同步数据，那么两方都做成Master 不就可以实现互相同步数据了。这就是接下来要介绍的MySQL AB双向复制。同样我们来看看MySQL AB双向复制的拓扑图。

![图二 MySQL AB双向复制](https://cdn.dbarobin.com/yGFM5W2.jpg)
图二 MySQL AB双向复制

既然对拓扑图和原理有所了解，我们做一个实验，介绍如何使用MySQL AB双向复制，注意该实验是在MySQL单级复制的基础上做的。

实验环境介绍

|----------+------------+-----------------+-----------|
| 主机           | IP地址             |主机名                      | 备注              |
|:----------:|:------------|:-----------------|:-----------:|
|serv01：   |192.168.1.11     | serv01.host.com  | master            |
|serv08：   |192.168.1.18     | serv08.host.com  | slave01           |
|----------+------------+-----------------+-----------|

**操作系统版本：**rhel server 6.1 <br/>
**所需要的软件包：**mysql-5.5.29-linux2.6-x86_64.tar.gz

第一步，serv08创建授权用户。

``` bash
mysql> grant replication client, \
replication slave on *.* to 'larry'@'192.168.1.%' \
identified by 'larry';
Query OK, 0 rows affected (0.01 sec)
```

第二步，serv08清空日志。

``` bash
mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       286 |
+------------------+-----------+
1 row in set (0.00 sec)

mysql> reset master;
Query OK, 0 rows affected (0.00 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       107 |
+------------------+-----------+
1 row in set (0.00 sec)
```

第三步，serv01使用change master to命令修改从服务器设置。

``` bash
mysql> show slave status;
Empty set (0.00 sec)

mysql> change master to
    -> master_host='192.168.1.18',
    -> master_user='larry',
    -> master_password='larry',
    -> master_port=3306,
    -> master_log_file='mysql-bin.000001',
    -> master_log_pos=107;
Query OK, 0 rows affected (0.01 sec)
```

第四步，serv01开启slave。

``` bash
mysql> start slave;
Query OK, 0 rows affected (0.01 sec)

mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.18
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 107
               Relay_Log_File: serv01-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000001
             **Slave_IO_Running: Yes**
            **Slave_SQL_Running: Yes**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 410
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 2
1 row in set (0.00 sec)

ERROR:
No query specified
```

第五步，测试。

``` bash
--serv01查看数据
mysql> use larrydb;
Database changed
mysql> select * from test;
+------+
| id   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

--serv08插入数据
mysql> use larrydb;
Database changed
mysql> select * from test;
+------+
| id   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

mysql> insert into test values(2);
Query OK, 1 row affected (0.00 sec)

--serv01查看数据，数据更新
mysql> select * from test;
+------+
| id   |
+------+
|    1 |
|    2 |
+------+
2 rows in set (0.00 sec)

--serv01插入数据
mysql> insert into test values(3);
Query OK, 1 row affected (0.01 sec)

--serv01查询数据
mysql> select * from test;
+------+
| id   |
+------+
|    1 |
|    2 |
|    3 |
+------+
3 rows in set (0.00 sec)

--serv08查询数据，数据已更新
mysql> select * from test;
+------+
| id   |
+------+
|    1 |
|    2 |
|    3 |
+------+
3 rows in set (0.00 sec)
```

## MySQL多级主从复制 ##

好了，MySQL AB双向复制介绍完毕，我们又想了，不管是MySQL AB单向复制，还是MySQL 双向复制，都是双方的关系。MySQL AB单向复制可以是一对一，也就是一个Master对应一个Slave，或者一对多，也就是一个Master对应多个Slave；MySQL双向复制是一对一的关系。我们可不可以这样，实现多级关系，一个Master，接下来Slave，Slave下面还有Slave。这样做有什么好处呢？这样可以缓解数据库压力。这就是接下来要介绍的MySQL多级主从复制。多级也就是A---->B---->C，A作为主服务器，B是从服务器，B跟A建立主从关系；而且B是主服务器，C作为从服务器，B跟C建立主从关系。这样：A是主服务器，B既是主服务器，又是从服务器，C是从服务器。同样，我们来看看MySQL 多级主从复制的拓扑图：

![图三 MySQL 多级主从复制](https://cdn.dbarobin.com/1WNsydu.jpg)
图三 MySQL 多级主从复制

该拓扑图实现 mysql 的 A 到B 的复制，再从 B 到 C 的复制。

既然对拓扑图和原理有所了解，我们做一个实验，介绍如何使用MySQL AB双向复制：

实验环境介绍

|----------+------------+-----------------+-----------|
| 主机           | IP地址             |主机名                      | 备注              |
|:----------:|:------------|:-----------------|:-----------:|
|serv01：   |192.168.1.11     | serv01.host.com  | master            |
|serv08：   |192.168.1.18     | serv08.host.com  | slave01           |
|serv09：   |192.168.1.19     | serv09.host.com  | slave02           |
|----------+------------+-----------------+-----------|

**操作系统版本：**rhel server 6.1 <br/>
**所需要的软件包：**mysql-5.5.29-linux2.6-x86_64.tar.gz

第一步，断开双向关系。A只作为主服务器。

``` bash
--停止slave
mysql> stop slave;
Query OK, 0 rows affected (0.00 sec)
--查看slave状态发现仍然有相关信息，我们要彻底删除，只需要把数据文件中相关文件删除即可。
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 192.168.1.18
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000002
          Read_Master_Log_Pos: 587
               Relay_Log_File: serv01-relay-bin.000006
                Relay_Log_Pos: 733
        Relay_Master_Log_File: mysql-bin.000002
             **Slave_IO_Running: No**
            **Slave_SQL_Running: No**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 587
              Relay_Log_Space: 1036
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 2
1 row in set (0.00 sec)

ERROR:
No query specified
```

进入data目录，删除以下文件：master.info relay-log.info serv01-relay-bin.*。

``` bash
cd /usr/local/mysql/data

ll
total 28736
-rw-rw----. 1 mysql mysql 18874368 Oct  5 22:38 ibdata1
-rw-rw----. 1 mysql mysql  5242880 Oct  5 22:38 ib_logfile0
-rw-rw----. 1 mysql mysql  5242880 Oct  5 18:16 ib_logfile1
drwx------. 2 mysql mysql     4096 Oct  5 22:36 larrydb
-rw-rw----. 1 mysql mysql       79 Oct  5 23:24 master.info
drwxr-xr-x. 2 mysql mysql     4096 Oct  5 18:15 mysql
-rw-rw----. 1 mysql mysql      690 Oct  5 22:34 mysql-bin.000001
-rw-rw----. 1 mysql mysql      970 Oct  5 22:38 mysql-bin.000002
-rw-rw----. 1 mysql mysql       38 Oct  5 22:34 mysql-bin.index
drwx------. 2 mysql mysql     4096 Oct  5 18:15 performance_schema
-rw-rw----. 1 mysql mysql       53 Oct  5 23:24 relay-log.info
-rw-r-----. 1 mysql root      5309 Oct  5 23:24 serv01.host.com.err
-rw-rw----. 1 mysql mysql        5 Oct  5 22:34 serv01.host.com.pid
-rw-rw----. 1 mysql mysql      303 Oct  5 22:35 serv01-relay-bin.000005
-rw-rw----. 1 mysql mysql      733 Oct  5 22:37 serv01-relay-bin.000006
-rw-rw----. 1 mysql mysql       52 Oct  5 22:35 serv01-relay-bin.index
-rw-r-----. 1 mysql mysql     2209 Oct  5 18:16 serv08.host.com.err
drwxr-xr-x. 2 mysql mysql     4096 Oct  5 18:12 test

rm -rf master.info relay-log.info serv01-relay-bin.*
```

第二步，serv01重启服务，再次查看slave信息，发现已经不存在。

``` bash
/etc/init.d/mysqld restart
Shutting down MySQL.... SUCCESS!
Starting MySQL.. SUCCESS!
```

``` bash
mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.5.29-log Source distribution
mysql> show slave status \G;

Empty set (0.00 sec)
ERROR:
No query specified
```

第三步，serv08查看slave状态。

``` bash
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.11
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 107
               Relay_Log_File: serv08-relay-bin.000007
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000003
             **Slave_IO_Running: Yes**
             **Slave_SQL_Running: Yes**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 107
              Relay_Log_Space: 556
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)

ERROR:
No query specified
```

如果查看slave状态出错，我们重启服务。

``` bash
/etc/init.d/mysqld restart
Shutting down MySQL.. SUCCESS!
Starting MySQL.. SUCCESS!
```

第四步，serv09搭建相同版本的MySQL，修改server-id，启动服务。

``` bash
vim /etc/my.cnf
cat /etc/my.cnf | grep server-id
server-id = 3
/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!
```

第五步，serv01插入数据。

``` bash
mysql> use larrydb;
Database changed
mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
+----+---------+
5 rows in set (0.00 sec)

mysql> insert into t2(name) values('larry07');
Query OK, 1 row affected (0.01 sec)
```

第六步，serv08查看serv01插入的数据是否记录到日志文件。可以发现，和serv01建立关系的延时日志文件中有相关记录，而主日志文件mysql-bin.000003中没有相关记录。

``` bash
mysqlbinlog serv08-relay-bin.000009 | grep insert -i --color
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
SET INSERT_ID=9/*!*/;
insert into t2(name) values('larry07')

mysqlbinlog mysql-bin.000003 | grep larry07
```

第七步，我们要把serv08的数据同步到serv09，因为serv08中的mysql-bin.000003文件没有相关记录，所以不能通过日志文件同步，我们只有先serv08导出数据，然后serv09导入数据，再把log_slave_updates打开

``` bash
# 导出数据
mysqldump --help --verbose | grep database
mysqldump --databases larrydb > larrydb.sql
# 拷贝数据文件
scp larrydb.sql 192.168.1.19:/opt
```

serv09导入数据。

``` bash
mysql> source /opt/larrydb.sql;
Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 1 row affected (0.01 sec)

Database changed
Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.02 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| t2                |
| test              |
+-------------------+
2 rows in set (0.01 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
+----+---------+
6 rows in set (0.01 sec)
```

serv08修改配置文件，打开log_slave_updates，重启MySQL服务。

``` bash
mysql> show variables like '%update%';
+-----------------------------------------+-------+
| Variable_name                           | Value |
+-----------------------------------------+-------+
| binlog_direct_non_transactional_updates | OFF   |
| log_slave_updates                       | OFF   |
| low_priority_updates                    | OFF   |
| sql_low_priority_updates                | OFF   |
| sql_safe_updates                        | OFF   |
+-----------------------------------------+-------+
5 rows in set (0.00 sec)
```

增加log_slave_updates参数。

``` bash
vim /etc/my.cnf
cat /etc/my.cnf | grep log_slave_updates
log_slave_updates=1
/etc/init.d/mysqld restart
Shutting down MySQL.... SUCCESS!
Starting MySQL.. SUCCESS!
```

查看参数是否生效。

``` bash
--serv08
mysql> show variables like "%update%";
+-----------------------------------------+-------+
| Variable_name                           | Value |
+-----------------------------------------+-------+
| binlog_direct_non_transactional_updates | OFF   |
| log_slave_updates                       | ON    |
| low_priority_updates                    | OFF   |
| sql_low_priority_updates                | OFF   |
| sql_safe_updates                        | OFF   |
+-----------------------------------------+-------+
5 rows in set (0.00 sec)
```

第八步，serv01插入测试数据，我们看到打开这个参数后mysql-bin.000004和serv08-relay-bin.000011都有相关的插入数据的记录。

``` bash
mysql> insert into t2(name) values('larry08');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
| 11 | larry08 |
+----+---------+
7 rows in set (0.00 sec)
```


``` bash
mysqlbinlog mysql-bin.000004 | grep larry
use `larrydb`/*!*/;
insert into t2(name) values('larry08')

mysqlbinlog serv08-relay-bin.000011 | grep larry
use `larrydb`/*!*/;
insert into t2(name) values('larry08')
```

第九步，serv08创建授权用户。

``` bash
mysql> select user,password,host from mysql.user where user='larry';
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    3
Current database: larrydb

+-------+-------------------------------------------+-------------+
| user  | password                                  | host        |
+-------+-------------------------------------------+-------------+
| larry | *0CDC8D34246E22649D647DB04E7CCCACAB4368B6 | 192.168.1.% |
+-------+-------------------------------------------+-------------+
1 row in set (0.00 sec)

mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |      1046 |
| mysql-bin.000002 |       606 |
| mysql-bin.000003 |       126 |
| mysql-bin.000004 |       335 |
+------------------+-----------+
4 rows in set (0.00 sec)
```

第十步，serv09通过change master to修改slave配置，然后启动slave，查看slave状态，查看数据，发现已经从serv08更新过来。

``` bash
mysql> change master to
    -> master_host='192.168.1.18',
    -> master_user='larry',
    -> master_password='larry',
    -> master_port=3306,
    -> master_log_file='mysql-bin.000003',
    -> master_log_pos=126;
Query OK, 0 rows affected (0.03 sec)

mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 192.168.1.18
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 126
               Relay_Log_File: serv09-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000003
             **Slave_IO_Running: No**
            **Slave_SQL_Running: No**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 126
              Relay_Log_Space: 107
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> start slave;
Query OK, 0 rows affected (0.01 sec)

mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.18
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 335
               Relay_Log_File: serv09-relay-bin.000003
                Relay_Log_Pos: 481
        Relay_Master_Log_File: mysql-bin.000004
             **Slave_IO_Running: Yes**
            **Slave_SQL_Running: Yes**
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 335
              Relay_Log_Space: 784
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 2
1 row in set (0.00 sec)

ERROR:
No query specified


mysql> select * from larrydb.t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
| 11 | larry08 |
+----+---------+
7 rows in set (0.00 sec)
```

第十一步，serv01插入数据，可以看到serv08 serv09都已经同步过去了

``` bash
--serv01
mysql> insert into t2(name) values('larry09');
Query OK, 1 row affected (0.00 sec)
mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
| 11 | larry08 |
| 13 | larry09 |
+----+---------+
8 rows in set (0.00 sec)

--serv08
mysql> select * from larrydb.t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
| 11 | larry08 |
| 13 | larry09 |
+----+---------+
8 rows in set (0.00 sec)

--serv09
mysql> select * from larrydb.t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
|  9 | larry07 |
| 11 | larry08 |
| 13 | larry09 |
+----+---------+
8 rows in set (0.00 sec)
```

## 解决AB双向复制主键冲突 ##

在进行MySQLAB双向复制时，如果一张表的主键是自增的，会出现问题。主服务器和从服务器在插入数据时会发生主键冲突，比如A服务器插入一条数据，id为5，B服务器同步过去，但是B服务器插入数据ID也可能是5，就这会引起主键冲突，导致数据不能插入。因此，我们需要解决这个问题。解决办法是主键间隔设置，通过设置主键步长，比如A（13 5 7），B（2 4 6 8），有几台机器步长就为几。接下来的实验是在MySQLAB双向复制的基础上做的。

第一步，serv08创建测试表，插入数据，查看数据。

``` bash
mysql> create table t2(id int auto_increment primary key,name varchar(30));
Query OK, 0 rows affected (0.00 sec)

mysql> desc t2;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(30) | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> insert into t2(name) values('larry01');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2(name) values('larry02');
Query OK, 1 row affected (0.01 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  2 | larry02 |
+----+---------+
2 rows in set (0.00 sec)
```

第二步，serv01查看数据。

``` bash
mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  2 | larry02 |
+----+---------+
2 rows in set (0.00 sec)

mysql> drop table t2;
Query OK, 0 rows affected (0.01 sec)
```

第二步，serv01和serv08修改配置文件，并重启服务。

``` bash
# serv01操作
vim /etc/my.cnf
cat /etc/my.cnf | grep auto_incre
auto_increment_increment=2
auto_increment_offset=1

/etc/init.d/mysqld restart
Shutting down MySQL.. SUCCESS!
Starting MySQL.. SUCCESS!

# serv08操作
vim /etc/my.cnf
cat /etc/my.cnf | grep auto_incre
auto_increment_increment=2
auto_increment_offset=2
/etc/init.d/mysqld restart
Shutting down MySQL. SUCCESS!
Starting MySQL.. SUCCESS!
```

第三步，serv01再次模拟数据

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| test              |
+-------------------+
1 row in set (0.00 sec)
mysql> create table t2(id int(11) primary key auto_increment, name varchar(30));
Query OK, 0 rows affected (0.02 sec)

mysql> insert into t2(name) values('larry01');
Query OK, 1 row affected (0.01 sec)

mysql> insert into t2(name) values('larry02');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
+----+---------+
2 rows in set (0.00 sec)

--serv08
mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
+----+---------+
2 rows in set (0.00 sec)

mysql> insert into t2(name) values('larry03');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2(name) values('larry04');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
+----+---------+
4 rows in set (0.00 sec)

--serv01
mysql> insert into t2(name) values('larry05');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
+----+---------+
5 rows in set (0.00 sec)

--serv08
mysql> select * from t2;
+----+---------+
| id | name    |
+----+---------+
|  1 | larry01 |
|  3 | larry02 |
|  4 | larry03 |
|  6 | larry04 |
|  7 | larry05 |
+----+---------+
5 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/13168569" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL AB复制" height="16px" width="16px" border="0" alt="MySQL AB复制" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
