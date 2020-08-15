---
published: true
author: Robin Wen
layout: post
title: "MySQL 集群"
category: 数据库
summary: "上一篇文章我们提到MySQL 读写分离，这篇文章我们讲解MySQL集群。我们提到的集群，是指多台机器完成一个工作，最主要的场景是数据库服务器和Web服务器，但是集群环境不适合大规模计算。前面我们有提到MySQL AB复制，因为MySQL AB复制不适合大规模运用，要解决这个问题，我们可以使用MySQL集群。"
tags:
- 数据库
- Database
- MySQL
- 集群
- Cluster
- 高可用
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

## 一 MySQL集群简介 ##

上一篇文章我们提到<a href="https://dbarobin.com/2013/12/15/mysql-proxy/" target="_blank">MySQL 读写分离</a>，这篇文章我们讲解MySQL集群。我们提到的集群，是指多台机器完成一个工作，最主要的场景是数据库服务器和Web服务器，但是集群环境不适合大规模计算。前面我们有提到<a href="https://dbarobin.com/2013/10/27/mysql-replication/" target="_blank">MySQL AB复制</a>，因为MySQL AB复制不适合大规模运用，要解决这个问题，我们可以使用MySQL集群。

MySQL集群分为三类节点：管理节点、SQL节点、存储节点。管理节点的功能是管理其他节点，负责调度不同的SQL节点和存储节点。SQL节点作用是用户和该节点进行交互，用户发送SQL语句到该节点，进行读写请求。存储节点负责到磁盘中读数据和写数据。MySQL集群中采用一种特殊存储引擎，名叫NDB。NDB负责对数据进行读写，并保证节点之间的数据一致性，存储节点没有必要使用共享存储，因为第一存储节点本身的数据互为镜像，本身已经对数据做了备份。其中，管理节点只需要一个，SQL节点根据业务需要可以有多个，存储节点同理。

## 二 MySQL集群示意图 ##

![MySQL集群示意图](https://cdn.dbarobin.com/Eaz0QTm.png)
图片来源：<a href="http://dev.mysql.com/doc/refman/5.1/en/mysql-cluster-overview.html" target="_blank"><img src="https://cdn.dbarobin.com/8dlN6sC.png" title="MySQL" border="0" alt="MySQL" height="16px" width="16px" /></a>

## 三 使用MySQL集群的优劣 ##

### 3.1 优势 ###

* 处理业务能力大幅提高；
* 用户关注的点更集中于业务；
* 数据不易丢失，因为存储节点对数据做备份。当然不要完全依靠MySQL集群，制定合理的备份和恢复策略还是很有必要的；
* 在SQL节点有多台的情况下，一台SQL节点宕机不影响，只需要开发人员手动判断该节点是否在线，不在线切换到另一台SQL节点上，保证了高可用性。

### 3.2 劣势 ###

* 成本提高，因为MySQL集群至少需要三台服务器；
* 运维难度增强，因为服务器数量增加。

## 四 搭建MySQL环境 ##

### 4.1 实验环境简介 ###

|----------+------------+-----------------|
| 属性           | IP地址             |主机名                      |
|:----------|:------------|:-----------------|
|管理节点   |192.168.1.11     | mgmd  |
|存储节点   |192.168.1.14     | ndb01  |
|存储节点   |192.168.1.15     | ndb02  |
|SQL节点   |192.168.1.12     | sql01  |
|SQL节点   |192.168.1.13     | sql02  |
|----------+------------+-----------------|

![MySQL集群网络拓扑图](https://cdn.dbarobin.com/T2vpHZq.jpg)
MySQL集群网络拓扑图

### 4.2 操作系统版本 ###

RHEL Server6.1 64位系统

### 4.3 使用到的软件包版本 ###

mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz

### 4.4 准备工作 ###

第一步，拷贝文件。

``` bash
scp mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz 192.168.1.11:/opt/
scp mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz 192.168.1.12:/opt/
scp mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz 192.168.1.13:/opt/
scp mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz 192.168.1.14:/opt/
scp mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz 192.168.1.15:/opt/
```

第二步，修改主机名。

``` bash
# serv01
hostname mgmd.host.com
vim /etc/sysconfig/network
cat !$
cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=mgmd.host.com
hostname
mgmd.host.com

# serv02
hostname sql01.host.com
vim /etc/sysconfig/network
cat !$
cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=sql01.host.com
hostname
sql01.host.com

# serv03
hostname sql02.host.com
vim /etc/sysconfig/network
cat !$
cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=sql02.host.com
hostname
sql02.host.com

# serv04
hostname ndb01.host.com
vim /etc/sysconfig/network
cat !$
cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=ndb01.host.com
hostname
ndb01.host.com

# serv05
hostname ndb02.host.com
vim /etc/sysconfig/network
cat !$
cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=ndb02.host.com
hostname
ndb02.host.com
```

第三步，确定IP地址。

``` bash
# mgmd
ifconfig | grep eth -A1
eth0      Link encap:Ethernet  HWaddr 00:0C:29:07:DD:3B
          inet addr:192.168.1.11  Bcast:192.168.1.255  Mask:255.255.255.0

# sql01
ifconfig | grep eth -A1
eth0      Link encap:Ethernet  HWaddr 00:0C:29:6A:EC:97
          inet addr:192.168.1.12  Bcast:192.168.1.255  Mask:255.255.255.0

# sql02
ifconfig | grep eth -A1
eth0      Link encap:Ethernet  HWaddr 00:0C:29:BD:08:05
          inet addr:192.168.1.13  Bcast:192.168.1.255  Mask:255.255.255.0

# ndb01
ifconfig | grep eth -A1
eth0      Link encap:Ethernet  HWaddr 00:0C:29:0F:1A:09
          inet addr:192.168.1.14  Bcast:192.168.1.255  Mask:255.255.255.0

# ndb02
ifconfig | grep eth -A1
eth0      Link encap:Ethernet  HWaddr 00:0C:29:77:CB:2F
          inet addr:192.168.1.15  Bcast:192.168.1.255  Mask:255.255.255.0
```

### 4.5 管理节点搭建 ###

第一步，添加mysql组和用户。

``` bash
groupadd -g 27 mysql
useradd -u 27 -g 27 -r -M -s /sbin/nologin mysql
id mysql
```

第二步，解压二进制包。

``` bash
tar -xvf mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz -C /usr/local/
cd /usr/local/
```

第三步，重命名安装目录，修改所有者和所属组。

``` bash
mv mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23/ mysql
chown mysql. mysql/ -R
ll mysql/ -d
ll mysql/
```

第四步，拷贝配置文件，编辑该文件。

``` bash
cp /usr/local/mysql/support-files/ndb-config-2-node.ini /etc/ndb-config.ini
vim /etc/ndb-config.ini
cat /etc/ndb-config.ini
```

脚本内容如下。

``` bash
# Example Ndbcluster storage engine config file.
#
[ndbd default]
NoOfReplicas= 2
MaxNoOfConcurrentOperations= 10000
DataMemory= 80M
IndexMemory= 24M
TimeBetweenWatchDogCheck= 30000
DataDir= /var/lib/mysql-cluster
MaxNoOfOrderedIndexes= 512

[ndb_mgmd default]
# 确定该目录存在
DataDir= /var/lib/mysql-cluster

[ndb_mgmd]
Id=1
HostName= 192.168.1.11

[ndbd]
Id= 2
HostName= 192.168.1.14

[ndbd]
Id= 3
HostName= 192.168.1.15

[mysqld]
Id= 4
HostName= 192.168.1.12

[mysqld]
Id= 5
HostName= 192.168.1.13

# choose an unused port number
# in this configuration 63132, 63133, and 63134
# will be used
[tcp default]
PortNumber= 63132
```

第五步，创建数据目录，修改所有者和所属组。

``` bash
mkdir /var/lib/mysql-cluster
chown mysql. /var/lib/mysql-cluster/ -R
ll -d /var/lib/mysql-cluster/
```

第六步，启动ndb_mgmd。

``` bash
/usr/local/mysql/bin/ndb_mgmd -f /etc/ndb-config.ini
2013-11-04 23:46:12 [MgmtSrvr] INFO     --
NDB Cluster Management Server. mysql-5.1.44 ndb-7.1.4b
2013-11-04 23:46:12 [MgmtSrvr] INFO     --
The default config directory '/usr/local/mysql/mysql-cluster' does not exist.
Trying to create it...
2013-11-04 23:46:12 [MgmtSrvr] INFO     --
Sucessfully created config directory
2013-11-04 23:46:12 [MgmtSrvr] INFO     --
Reading cluster configuration from '/etc/ndb-config.ini'
2013-11-04 23:46:12 [MgmtSrvr] WARNING  --
at line 39: [tcp] PortNumber is depricated, use Port used for this transporter instead
2013-11-04 23:46:13 [MgmtSrvr] INFO     --
Reading cluster configuration from '/etc/ndb-config.ini'
2013-11-04 23:46:13 [MgmtSrvr] WARNING  --
at line 39: [tcp] PortNumber is depricated, use Port used for this transporter instead
```

确定进程和端口号。

``` bash
ps -ef | grep mgm | grep -v grep
netstat -langput | grep mgm
```

查看状态。

``` bash
/usr/local/mysql/bin/ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: localhost:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2 (not connected, accepting connect from 192.168.1.14)
id=3 (not connected, accepting connect from 192.168.1.15)

[ndb_mgmd(MGM)] 1 node(s)
id=1  @192.168.1.11  (mysql-5.1.44 ndb-7.1.4)

[mysqld(API)] 2 node(s)
id=4 (not connected, accepting connect from 192.168.1.12)
id=5 (not connected, accepting connect from 192.168.1.13)
```

### 4.6 存储节点搭建 ###

第一步，ndb01添加mysql组和用户。

``` bash
groupadd -g 27 mysql
useradd -u 27 -g 27 -r -M -s /sbin/nologin mysql
id mysql
cd /opt/
```

第二步，ndb01解压二进制包。

``` bash
 tar -xvf mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz -C /usr/local/
 cd /usr/local/
```

第三步，ndb01重命名安装目录，修改所有者和所属组。

``` bash
mv mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23/ mysql
chown mysql. mysql/ -R
ll -d mysql/
ll mysql/
```

第四步，ndb01进入mysql目录，拷贝模板文件，修改该文件，

``` bash
cd mysql/
cp support-files/my-medium.cnf /etc/my.cnf
cp: overwrite `/etc/my.cnf'? y
vim /etc/my.cnf
```

配置文件内容如下。

``` bash
grep "^#\|^$" /etc/my.cnf -v
[client]
port    = 3306
socket    = /tmp/mysql.sock
[mysqld]
port    = 3306
socket    = /tmp/mysql.sock
skip-locking
key_buffer_size = 16M
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
[mysqldump]
quick
max_allowed_packet = 16M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
[mysql_cluster]
ndb_connectstring=192.168.1.11

# 下划线或者横线都可以
# ndb-connectstring=192.168.1.11
```

第五步，ndb01创建数据目录，修改所有者和所属组。

``` bash
mkdir /var/lib/mysql-cluster
chown mysql.mysql !$ -R
 ll -d /var/lib/mysql-cluster/
```

第六步，ndb01初始化ndbd。

``` bash
/usr/local/mysql/bin/ndbd --initial
2013-11-04 23:57:06 [ndbd] INFO     -- Configuration fetched from
'192.168.1.11:1186', generation: 1
```

查看状态。

``` bash
/usr/local/mysql/bin/ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: 192.168.1.11:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2  @192.168.1.14  (mysql-5.1.44 ndb-7.1.4, starting, Nodegroup: 0)
id=3 (not connected, accepting connect from 192.168.1.15)

[ndb_mgmd(MGM)] 1 node(s)
id=1  @192.168.1.11  (mysql-5.1.44 ndb-7.1.4)

[mysqld(API)] 2 node(s)
id=4 (not connected, accepting connect from 192.168.1.12)
id=5 (not connected, accepting connect from 192.168.1.13)
```

第七步，ndb02和ndb01执行相同的操作，如下：

``` bash
groupadd -g 27 mysql
useradd -u 27 -g 27 -r -M -s /sbin/nologin mysql
id mysql
tar -xvf mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz -C /usr/local/

cd /usr/local/
mv mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23/ mysql
chown mysql. mysql/ -R
ll -d mysql/
ll mysql/

mkdir /var/lib/mysql-cluster
chown mysql. /var/lib/mysql-cluster/ -R
ll -d /var/lib/mysql-cluster/
cd mysql/

cp support-files/my-medium.cnf /etc/my.cnf
cp: overwrite `/etc/my.cnf'? y
vim /etc/my.cnf
grep "^#\|^$" !$ -v
grep "^#\|^$" /etc/my.cnf -v
[client]
port    = 3306
socket    = /tmp/mysql.sock
[mysqld]
port    = 3306
socket    = /tmp/mysql.sock
skip-locking
key_buffer_size = 16M
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
[mysqldump]
quick
max_allowed_packet = 16M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
[mysql_cluster]
ndb_connectstring=192.168.1.11

/usr/local/mysql/bin/ndbd --initial
2013-11-05 00:02:18 [ndbd] INFO     -- Configuration fetched from
'192.168.1.11:1186', generation: 1
[root@ndb02 mysql]# /usr/local/mysql/bin/ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: 192.168.1.11:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2  @192.168.1.14  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0, Master)
id=3  @192.168.1.15  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0)

[ndb_mgmd(MGM)] 1 node(s)
id=1  @192.168.1.11  (mysql-5.1.44 ndb-7.1.4)

[mysqld(API)] 2 node(s)
id=4 (not connected, accepting connect from 192.168.1.12)
id=5 (not connected, accepting connect from 192.168.1.13)
```

### 4.7 SQL节点搭建 ###

第一步，sql01添加mysql组和用户。

``` bash
groupadd -g 27 mysql
useradd -u 27 -g 27 -r -M -s /sbin/nologin mysql
id mysql
cd /opt/
```

第二步，sql01解压二进制包。

``` bash
tar -xvf mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz -C /usr/local/
```

第三步，sql01重命名安装目录，修改所有者和所属组。

``` bash
cd /usr/local/
mv mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23/ mysql
chown mysql. mysql/ -R
ll -d mysql/
ll mysql/
```

第四步，sql01拷贝配置文件，并修改。

``` bash
cd mysql/
cp support-files/my-medium.cnf /etc/my.cnf
cp: overwrite `/etc/my.cnf'? y
vim /etc/my.cnf
```

修改脚本如下。

``` bash
grep "^#\|^$" !$ -v
grep "^#\|^$" /etc/my.cnf -v
[client]
port    = 3306
socket    = /tmp/mysql.sock
[mysqld]
datadir=/var/lib/mysql-cluster
ndbcluster
default-storage-engine=ndbcluster
port    = 3306
socket    = /tmp/mysql.sock
skip-locking
key_buffer_size = 16M
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
[mysqldump]
quick
max_allowed_packet = 16M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
[mysql_cluster]
ndb_connectstring=192.168.1.11
```

第五步，sql01拷贝运行脚本，添加可执行权限。

``` bash
cp support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
```

第六步，sql01创建数据目录，修改所有者和所属组。

``` bash
mkdir /var/lib/mysql-cluster
chown mysql. !$ -R
ll -d /var/lib/mysql-cluster/
```

第七步，sql01初始化数据库。

``` bash
/usr/local/mysql/scripts/mysql_install_db --user=mysql
```
[root@sql01 mysql]#

第八步，sql01启动mysql，并加入ndb

``` bash
/etc/init.d/mysqld start
Starting MySQL. SUCCESS!

/usr/local/mysql/bin/ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: 192.168.1.11:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2  @192.168.1.14  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0, Master)
id=3  @192.168.1.15  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0)

[ndb_mgmd(MGM)] 1 node(s)
id=1  @192.168.1.11  (mysql-5.1.44 ndb-7.1.4)

[mysqld(API)] 2 node(s)
id=4  @192.168.1.12  (mysql-5.1.44 ndb-7.1.4)
id=5 (not connected, accepting connect from 192.168.1.13)
```

第九步，sql02执行和sql01相同的操作，如下：

``` bash
groupadd -g 27 mysql
useradd -u 27 -g 27 -r -M -s /sbin/nologin mysql
id mysql
cd /opt/
tar -xvf mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23.tar.gz -C /usr/local/

cd !$
mv mysql-cluster-gpl-7.1.4b-linux-x86_64-glibc23/ mysql
chown mysql. mysql/ -R
ll -d mysql/
ll mysql/

mkdir /var/lib/mysql-cluster
chown mysql. /var/lib/mysql-cluster -R
ll -d /var/lib/mysql-cluster/

cd mysql/
cp support-files/my-medium.cnf /etc/my.cnf
cp: overwrite `/etc/my.cnf'? y
vim /etc/my.cnf
rep "^#\|^$" /etc/my.cnf -v
[client]
port    = 3306
socket    = /tmp/mysql.sock
[mysqld]
datadir=/var/lib/mysql-cluster
ndbcluster
default-storage-engine=ndbcluster
port    = 3306
socket    = /tmp/mysql.sock
skip-locking
key_buffer_size = 16M
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
[mysqldump]
quick
max_allowed_packet = 16M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 20M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
[mysql_cluster]
ndb_connectstring=192.168.1.11

cp support-files/mysql.server /etc/init.d/mysqld
chmod +x !$
/usr/local/mysql/scripts/mysql_install_db --user=mysql

/etc/init.d/mysqld start
Starting MySQL. SUCCESS!

/usr/local/mysql/bin/ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: 192.168.1.11:1186
Cluster Configuration
---------------------
[ndbd(NDB)] 2 node(s)
id=2  @192.168.1.14  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0, Master)
id=3  @192.168.1.15  (mysql-5.1.44 ndb-7.1.4, Nodegroup: 0)

[ndb_mgmd(MGM)] 1 node(s)
id=1  @192.168.1.11  (mysql-5.1.44 ndb-7.1.4)

[mysqld(API)] 2 node(s)
id=4  @192.168.1.12  (mysql-5.1.44 ndb-7.1.4)
id=5  @192.168.1.13  (mysql-5.1.44 ndb-7.1.4)
```

### 4.8 测试 ###

第一步，sql02创建测试数据库，sql01可以发现。

``` bash
# sql02
/usr/local/mysql/bin/mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.1.44-ndb-7.1.4b-cluster-gpl-log MySQL Cluster Server (GPL)
```

``` bash
-- sql02
mysql> create database larrydb;
Query OK, 1 row affected (0.21 sec)

--sql01
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| larrydb            |
| mysql              |
| ndbinfo            |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```

第二步，sql01创建测试表，插入测试数据，sql02可以发现变化。

``` bash
-- sql01
mysql> use larrydb;
Database changed
mysql> create table user(id int,name varchar(30));
Query OK, 0 rows affected (0.62 sec)

mysql> insert into user values(1,'larry');
Query OK, 1 row affected (0.03 sec)

--sql02
mysql> select * from larrydb.user;
+------+-------+
| id   | name  |
+------+-------+
|    1 | larry |
+------+-------+
1 row in set (0.04 sec)
```

如果只能使用三台服务器，可以这样配置：sql节点和ndb节点放在一起。管理节点做如下配置：

``` bash
# 管理节点
[ndb_mgmd]
Id=1
HostName= 192.168.1.11

[ndbd]
Id= 2
HostName= 192.168.1.14

[ndbd]
Id= 3
HostName= 192.168.1.15

[mysqld]
Id= 4

[mysqld]
Id= 5

# 其他节点做sql节点的配置即可
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 五 参考资料 ##

MySQL Cluster：<a href="http://dev.mysql.com/doc/refman/5.1/en/mysql-cluster.html" target="_blank"><img src="https://cdn.dbarobin.com/8dlN6sC.png" title="MySQL Cluster" height="16px" width="16px" border="0" alt="MySQL Cluster" /></a>

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/17481389" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL 集群" height="16px" width="16px" border="0" alt="MySQL 集群" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
