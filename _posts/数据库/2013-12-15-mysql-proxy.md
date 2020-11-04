---
published: true
author: Robin Wen
layout: post
title: "MySQL 读写分离"
category: 数据库
summary: "MySQL Proxy最强大的一项功能是实现“读写分离(Read/Write Splitting)”。基本的原理是让主数据库处理事务性查询，而从数据库处理SELECT查询。数据库复制被用来把事务性查询导致的变更同步到集群中的从数据库。 当然，主服务器也可以提供查询服务。使用读写分离最大的作用无非是环境服务器压力。"
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

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 什么是读写分离 ##

MySQL Proxy最强大的一项功能是实现“读写分离(Read/Write Splitting)”。基本的原理是让主数据库处理事务性查询，而从数据库处理SELECT查询。数据库复制被用来把事务性查询导致的变更同步到集群中的从数据库。 当然，主服务器也可以提供查询服务。使用读写分离最大的作用无非是环境服务器压力。可以看下这张图：

![MySQL 读写分离示意图](https://cdn.dbarobin.com/wySnl1E.jpg)

## 二 读写分离的好处 ##

* 增加冗余
* 增加了机器的处理能力
* 对于读操作为主的应用，使用读写分离是最好的场景，因为可以确保写的服务器压力更小，而读又可以接受点时间上的延迟。

## 三 读写分离提高性能之原因 ##

* 物理服务器增加，负荷增加
* 主从只负责各自的写和读，极大程度的缓解X锁和S锁争用
* 从库可配置myisam引擎，提升查询性能以及节约系统开销
* 从库同步主库的数据和主库直接写还是有区别的，通过主库发送来的binlog恢复数据，但是，最重要区别在于主库向从库发送binlog是异步的，从库恢复数据也是异步的
* 读写分离适用与读远大于写的场景，如果只有一台服务器，当select很多时，update和delete会被这些select访问中的数据堵塞，等待select结束，并发性能不高。 对于写和读比例相近的应用，应该部署双主相互复制
* 可以在从库启动是增加一些参数来提高其读的性能，例如--skip-innodb、--skip-bdb、--low-priority-updates以及--delay-key-write=ALL。当然这些设置也是需要根据具体业务需求来定得，不一定能用上
* 分摊读取。假如我们有1主3从，不考虑上述1中提到的从库单方面设置，假设现在1分钟内有10条写入，150条读取。那么，1主3从相当于共计40条写入，而读取总数没变，因此平均下来每台服务器承担了10条写入和50条读取（主库不承担读取操作）。因此，虽然写入没变，但是读取大大分摊了，提高了系统性能。另外，当读取被分摊后，又间接提高了写入的性能。所以，总体性能提高了，说白了就是拿机器和带宽换性能。MySQL官方文档中有相关演算公式：<a href="http://dev.mysql.com/doc/refman/5.1/en/replication.html" target="_blank">官方文档</a> 见6.9FAQ之“MySQL复制能够何时和多大程度提高系统性能”
* MySQL复制另外一大功能是增加冗余，提高可用性，当一台数据库服务器宕机后能通过调整另外一台从库来以最快的速度恢复服务，因此不能光看性能，也就是说1主1从也是可以的。

## 四 读写分离示意图 ##

![读写分离示意图](https://cdn.dbarobin.com/wytqs5M.jpg)

## 五 读写分离模拟 ##

实验环境简介

|----------+------------+-----------------+-----------|
| 主机           | IP地址             |主机名                      | 备注              |
|:----------:|:------------|:-----------------|:-----------|
|serv01   |192.168.1.11     | serv01.host.com  | 代理服务器            |
|serv08   |192.168.1.18     | serv08.host.com  | 主服务器（主要写数据，可读可写）    |
|serv09   |192.168.1.19     | serv09.host.com  | 从服务器（主要读数据）    |
|----------+------------+-----------------+-----------|

**操作系统版本**

RHEL Server6.1 64位系统

**使用到的软件包版本**

* mysql-5.5.29-linux2.6-x86_64.tar.gz
* mysql-proxy-0.8.2-linux-glibc2.3-x86-64bit.tar.gz

第一步，搭建MySQL服务器，清空日志。**注意：代理服务器中不需要装MySQL。**

第二步，拷贝mysql-proxy-0.8.2-linux-glibc2.3-x86-64bit.tar.gz文件，解压文件。

``` bash
scp mysql-proxy-0.8.2-linux-glibc2.3-x86-64bit.tar.gz 192.168.1.11:/opt
tar -xvf mysql-proxy-0.8.2-linux-glibc2.3-x86-64bit.tar.gz -C /usr/local/
cd /usr/local/
mv mysql-proxy-0.8.2-linux-glibc2.3-x86-64bit/ mysql-proxy
ll mysql-proxy/

# 可以查看帮助
./mysql-proxy --help-all
```

第三步，serv08主服务器创建用户，serv09从服务器创建用户，注意用户名和密码一致。

``` bash
--serv08
mysql> grant all on *.* to 'larry'@'192.168.1.%' identified by 'larry';
Query OK, 0 rows affected (0.00 sec)

--serv09
mysql> grant all on *.* to 'larry'@'192.168.1.%' identified by 'larry';
Query OK, 0 rows affected (0.00 sec)
```

第四步，serv09从服务器更改设置，开启slave，查看slave状态。创建测试数据库，插入测试数据。

``` bash
--serv09
mysql> change master to  \
master_host='192.168.1.18',  \
master_user='larry', \
master_password='larry', \
master_port=3306, \
master_log_file='mysql-bin.000001', \
master_log_pos=107;
Query OK, 0 rows affected (0.01 sec)

mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.1.18
                  Master_User: larry
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 107
               Relay_Log_File: serv09-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000001
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

mysql> select user,password,host from mysql.user;

mysql> create database larrydb;
Query OK, 1 row affected (0.00 sec)

mysql> use larrydb;
Database changed
mysql> create table user(id int, name varchar(30));
Query OK, 0 rows affected (0.01 sec)
mysql> insert into user values(1,'larrywen');
Query OK, 1 row affected (0.01 sec)

mysql> insert into user values(2,'wentasy');
Query OK, 1 row affected (0.00 sec)

mysql> select * from user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
+------+----------+
2 rows in set (0.00 sec)


serv09
mysql> select * from larrydb.user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
+------+----------+
2 rows in set (0.00 sec)
```

第五步，为了查看现象，serv09从服务器关闭slave。

``` bash
mysql> stop slave;
Query OK, 0 rows affected (0.01 sec)
```

第六步，serv 01查看是否有MySQL用户，修改rw-splitting.lua文件，修改如下几个参数。

``` bash
id mysql
vim rw-splitting.lua
cat rw-splitting.lua | grep -e min_idle_connections -e max_idle_connections -e is_debug
    min_idle_connections = 1,--最小空闲连接数，为了测试，这里设置为1
    max_idle_connections = 1,--最大空闲连接数，为了测试，这里设置为1
    is_debug = true--是否打开Debug调试，为了查看调试信息，这里设置为true
```

第七步，启动mysql-proxy。

``` bash
/etc/init.d/mysql-proxy start
Starting mysql-proxy:
```

先确定是否可以连接。

``` bash
mysql -ularry -plarry -h 192.168.1.18
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.5.29-log Source distribution
mysql> exit
Bye

mysql -ularry -plarry -h 192.168.1.19
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 5.5.29-log Source distribution
mysql> exit
Bye
```

第八步，查看现象。

``` bash
/etc/init.d/mysql-proxy start
Starting mysql-proxy:

mysql -ularry -plarry -h 192.168.1.11
[connect_server] 192.168.1.11:51054
  [1].connected_clients = 0
  [1].pool.cur_idle     = 0
  [1].pool.max_idle     = 1
  [1].pool.min_idle     = 1
  [1].type = 1
  [1].state = 0
  [1] idle-conns below min-idle
Welcome to the MySQL monitor.  Commands end with ; or \g.
[read_query] 192.168.1.11:51054
  current backend   = 0
  client default db =
  client username   = larry
  query             = select @@version_comment limit 1
  sending to backend : 192.168.1.19:3306
    is_slave         : false
    server default db:
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
Your MySQL connection id is 10

mysql> use larrydb;
[read_query] 192.168.1.11:51054
  current backend   = 0
  client default db =
  client username   = larry
  query             = SELECT DATABASE()
  sending to backend : 192.168.1.19:3306
    is_slave         : false
    server default db:
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
[read_query] 192.168.1.11:51054
  current backend   = 0
  client default db =
  client username   = larry
  sending to backend : 192.168.1.19:3306
    is_slave         : false
    server default db:
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : false
Database changed
mysql> select * from user;
[read_query] 192.168.1.11:51054
  current backend   = 0
  client default db = larrydb
  client username   = larry
  query             = select * from user
  sending to backend : 192.168.1.19:3306
    is_slave         : false
    server default db: larrydb
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
+------+----------+
2 rows in set (0.00 sec)

mysql> insert into user values(3,'jsutdb');
[read_query] 192.168.1.11:51644
  current backend   = 0
  client default db = larrydb
  client username   = larry
  query             = insert into user values(3,'jsutdb')
  sending to backend : 192.168.1.19:3306
    is_slave         : false
    server default db: larrydb
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
Query OK, 1 row affected (0.00 sec)
```

查看数据。

``` bash
--serv08
mysql> select * from user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
+------+----------+
2 rows in set (0.00 sec)

--serv09
mysql> select * from larrydb.user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
|    3 | jsutdb   |
+------+----------+
3 rows in set (0.00 sec)
```

第九步，以上的测试虽有效果，但不是预期。排查原因，重新配置。发现proxy-read-only-backend-addresses和proxy-backend-addresses参数配置出错，proxy-read-only-backend-addresses应该配置成从服务器的IP地址，proxy-backend-addresses应该配置成主服务器的IP地址。

``` bash
vim /etc/init.d/mysql-proxy
cat /etc/init.d/mysql-proxy
```

脚本内容如下：

``` bash
#!/bin/sh
#
# mysql-proxy This script starts and stops the mysql-proxy daemon
#
# chkconfig: - 78 30
# processname: mysql-proxy
# description: mysql-proxy is a proxy daemon to mysql

# Source function library.
. /etc/rc.d/init.d/functions

#PROXY_PATH=/usr/local/bin
PROXY_PATH=/usr/local/mysql-proxy/bin

prog="mysql-proxy"

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0

# Set default mysql-proxy configuration.
#PROXY_OPTIONS="--daemon"
PROXY_OPTIONS="--proxy-read-only-backend-addresses=192.168.1.19:3306 \
--proxy-backend-addresses=192.168.1.18:3306 \
--proxy-lua-script=/usr/local/mysql-proxy/share/doc/mysql-proxy/rw-splitting.lua"

#PROXY_PID=/usr/local/mysql-proxy/run/mysql-proxy.pid
PROXY_PID=/var/run/mysql-proxy.pid

# Source mysql-proxy configuration.
if [ -f /etc/sysconfig/mysql-proxy ]; then
        . /etc/sysconfig/mysql-proxy
fi

PATH=$PATH:/usr/bin:/usr/local/bin:$PROXY_PATH

# By default it's all good
RETVAL=0

# See how we were called.
case "$1" in
  start)
        # Start daemon.
        echo -n $"Starting $prog: "
        $NICELEVEL $PROXY_PATH/mysql-proxy $PROXY_OPTIONS \
        --daemon \
        --pid-file=$PROXY_PID \
        --user=mysql \
        --log-level=debug \
        --log-file=/var/log/mysql-proxy.log \
        --proxy-address=192.168.1.11:3306
        RETVAL=$?
        echo
        if [ $RETVAL = 0 ]; then
                touch /var/lock/subsys/mysql-proxy
        fi
       ;;
  stop)
        # Stop daemons.
        echo -n $"Stopping $prog: "
        killproc $prog
        RETVAL=$?
        echo
        if [ $RETVAL = 0 ]; then
                rm -f /var/lock/subsys/mysql-proxy
                rm -f $PROXY_PID
        fi
       ;;
  restart)
        $0 stop
        sleep 3
        $0 start
       ;;
  condrestart)
       [ -e /var/lock/subsys/mysql-proxy ] && $0 restart
      ;;
  status)
        status mysql-proxy
        RETVAL=$?
       ;;
  *)
        echo "Usage: $0 {start|stop|restart|status|condrestart}"
        RETVAL=1
       ;;
esac

exit $RETVAL
```

第十步，测试。插入数据，可以发现连接的是主服务器，查询的时候也是主服务器。说明主服务器和从服务器均有读的的功能。

``` bash
mysql -ularry -plarry -h 192.168.1.11

[connect_server] 192.168.1.11:57891
  [1].connected_clients = 0
  [1].pool.cur_idle     = 0
  [1].pool.max_idle     = 1
  [1].pool.min_idle     = 1
  [1].type = 1
  [1].state = 1
  [1] idle-conns below min-idle
[read_query] 192.168.1.11:57891
  current backend   = 0
  client default db =
  client username   = larry
  query             = select @@version_comment limit 1
  sending to backend : 192.168.1.18:3306
    is_slave         : false
    server default db:
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true


mysql> insert into user values(5,'test');
Query OK, 1 row affected (0.01 sec)
[read_query] 192.168.1.11:57893
  current backend   = 0
  client default db = larrydb
  client username   = larry
  query             = insert into user values(5,'test')
  sending to backend : 192.168.1.18:3306
    is_slave         : false
    server default db: larrydb
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
mysql> select * from user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
|    5 | test     |
+------+----------+
3 rows in set (0.00 sec)
[read_query] 192.168.1.11:57893
  current backend   = 0
  client default db = larrydb
  client username   = larry
  query             = select * from user
  sending to backend : 192.168.1.18:3306
    is_slave         : false
    server default db: larrydb
    server username  : larry
    in_trans        : false
    in_calc_found   : false
    COM_QUERY       : true
```

serv08主服务器查看数据，可以查询到，说明主服务器可以写。

``` bash
mysql> select * from larrydb.user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
|    5 | test     |
+------+----------+
3 rows in set (0.00 sec)
```

serv09从服务器查询数据，发现不可查询到，说明从服务器只读。

``` bash
mysql> mysql> select * from larrydb.user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
|    3 | jsutdb   |
|    4 | db       |
+------+----------+
4 rows in set (0.00 sec)
```


第十一步，开启slave。发现数据同步成功。

``` bash
mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from larrydb.user;
+------+----------+
| id   | name     |
+------+----------+
|    1 | larrywen |
|    2 | wentasy  |
|    3 | jsutdb   |
|    4 | db       |
|    5 | test     |
+------+----------+
5 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 六 参考资料 ##
<a href="http://www.itpub.net/thread-1184103-1-1.html" target="_blank"><img src="https://cdn.dbarobin.com/luz6LB6.png" title="ITPUB" height="16px" width="16px" border="0" alt="ITPUB" /></a> <br/>
<a href="http://segmentfault.com/q/1010000000304576" target="_blank"><img src="https://cdn.dbarobin.com/mf0AZ2m.png" title="segmentfault" height="16px" width="16px" border="0" alt="segmentfault" /></a>

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/17331569" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL 读写分离" height="16px" width="16px" border="0" alt="MySQL 读写分离" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
