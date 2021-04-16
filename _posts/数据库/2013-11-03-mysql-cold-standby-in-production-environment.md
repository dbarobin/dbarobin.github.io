---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之真实环境使用冷备"
category: 数据库
summary: "在上一篇文章（MySQL备份与恢复之冷备）中，我们提到了冷备。但是有个问题，我们存储的数据文件是保存在当前本地磁盘的，如果这个磁盘挂掉，那我们存储的数据不就丢失了，这样备份数据不就功亏一篑，劳而无功。所以真实环境中我们多准备几块磁盘，然后再在这些磁盘上搭建LVM，把MySQL的数据目录挂载到LVM上，这样数据就不是存储在当前磁盘上，就可以保证数据的安全性。"
tags:
- 数据库
- Database
- MySQL
- 安全
- Security
- 数据安全
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 真实环境使用冷备 ##

在上一篇文章（<a href="https://dbarobin.com/2013/11/02/mysql-cold-standby/" target="_blank">MySQL备份与恢复之冷备</a>）中，我们提到了冷备。但是有个问题，我们存储的数据文件是保存在当前本地磁盘的，如果这个磁盘挂掉，那我们存储的数据不就丢失了，这样备份数据不就功亏一篑，劳而无功。所以真实环境中我们多准备几块磁盘，然后再在这些磁盘上搭建LVM，把MySQL的数据目录挂载到LVM上，这样数据就不是存储在当前磁盘上，就可以保证数据的安全性。

## 二 示意图 ##

![真实环境使用冷备示意图](https://cdn.dbarobin.com/xdwARPo.jpg)

## 三 真实环境使用冷备模拟 ##

第一步，需要提前规划好磁盘，这里做模拟，添加两磁盘。

第二步，对磁盘进行分区。

``` bash
fdisk /dev/sdb
fdisk /dev/sdc
ll /dev/sd[bc]1
```

第三步，yum安装lvm2。

``` bash
yum install lvm2 -y
```

第四步，创建物理卷。

``` bash
pvcreate /dev/sdb1 /dev/sdc1
  Physical volume "/dev/sdb1" successfully created
  Physical volume "/dev/sdc1" successfully created
```

第五步，创建卷组

``` bash
vgcreate data /dev/sdb1 /dev/sdc1
  Volume group "data" successfully created
```

第六步，创建逻辑卷

``` bash
lvcreate -L 2G -n mydata data
  Logical volume "mydata" created
```

第七步，格式化磁盘。

``` bash
mkfs.ext4 /dev/data/mydata
mke2fs 1.41.12 (17-May-2010)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
131072 inodes, 524288 blocks
26214 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=536870912
16 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
  32768, 98304, 163840, 229376, 294912

Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 28 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```

第八步，冷备

``` bash
ls /usr/local/mysql/data/

tar -cvPzf mysql01.tar.gz /usr/local/mysql/data/
```

第九步，删除数据库文件。

``` bash
rm -rf /usr/local/mysql/data/*
```

第十步，挂载。

``` bash
mount /dev/data/mydata /usr/local/mysql/data/

df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sda2             9.7G  2.4G  6.8G  27% /
tmpfs                 188M     0  188M   0% /dev/shm
/dev/sda1             194M   25M  160M  14% /boot
/dev/sda5             4.0G  160M  3.7G   5% /opt
/dev/sr0              3.4G  3.4G     0 100% /iso
/dev/mapper/data-mydata
                      2.0G   67M  1.9G   4% /usr/local/mysql/data
```
[root@serv01 ~]#

第十一步，将挂载信息写入配置文件。

``` bash
echo "/dev/mapper/data-mydata /usr/local/mysql/data ext4 defaults 1 2" >> /etc/fstab
tail -n1 /etc/fstab
/dev/mapper/data-mydata /usr/local/mysql/data ext4 defaults 1 2
```

第十二步，停掉数据库。

``` bash
/etc/init.d/mysqld stop
 ERROR! MySQL server PID file could not be found!
ps -ef | grep mysqld
pkill -9 mysql
ps -ef | grep mysqld

chown mysql.mysql /usr/local/mysql/data/ -R
ll /usr/local/mysql/data/
ll /usr/local/mysql/data/ -d

```

第十三步，恢复数据。

``` bash
tar -xPvf mysql01.tar.gz
```

第十四步，启动数据库，登录MySQL，然后查看数据是否丢失。

``` bash
/etc/init.d/mysqld start
Starting MySQL SUCCESS!

mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.5.29-log Source distribution
```

查看数据。

``` bash
mysql> use larrydb;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_larrydb |
+-------------------+
| class             |
| stu               |
+-------------------+
2 rows in set (0.00 sec)

mysql> select * from class;
+------+--------+
| cid  | cname  |
+------+--------+
|    1 | linux  |
|    2 | oracle |
+------+--------+
2 rows in set (0.01 sec)

mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)
```


第十五步，使用LVS的快照功能创建快照，快照不需要格式化。

``` bash
lvcreate -L 100M -s -n smydata /dev/data/mydata
  Logical volume "smydata" created
```

第十六步，挂载。

``` bash
mount /dev/data/smydata /mnt

df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sda2             9.7G  2.4G  6.8G  27% /
tmpfs                 188M     0  188M   0% /dev/shm
/dev/sda1             194M   25M  160M  14% /boot
/dev/sda5             4.0G  161M  3.7G   5% /opt
/dev/sr0              3.4G  3.4G     0 100% /iso
/dev/mapper/data-mydata
                      2.0G   98M  1.8G   6% /usr/local/mysql/data
/dev/mapper/data-smydata
                      2.0G   98M  1.8G   6% /mnt
```

第十七步，模拟数据丢失和验证快照的数据不会受本身数据的影响。

``` bash
cd /mnt
ls

# 进入数据目录，创建一个文件
cd /usr/local/mysql/data/
touch aa01.txt

# 进入快照挂载目录，发现没有这个文件
ls aa01.txt
ls: cannot access aa01.txt: No such file or directory
```

第十八步，备份数据。

``` bash
cd /databackup/
ll
total 976
-rw-r--r--. 1 root root 995761 Sep 10 17:47 mysql01.tar.gz

/etc/init.d/mysqld status
 SUCCESS! MySQL running (2198)

tar -cvzf mysql02.tar.gz /mnt
```

模拟数据丢失。

``` bash
rm -rf /usr/local/mysql/data/*

etc/init.d/mysqld stop
 ERROR! MySQL server PID file could not be found!

pkill -9 mysql
ps -ef | grep mysqld | grep grep -v

cd /usr/local/mysql/data/
ll
total 0
```

第十九步，恢复数据，启动数据库，登录MySQL，然后查看数据是否丢失。

``` bash
tar -xvf /databackup/mysql02.tar.gz
ls
mnt

cd mnt/
mv ./* ../

cd ..
ls

/etc/init.d/mysqld start
Starting MySQL SUCCESS!

mysql
```

查看数据。

``` bash
mysql> use larrydb;
Database changed
mysql> select * from class;
+------+--------+
| cid  | cname  |
+------+--------+
|    1 | linux  |
|    2 | oracle |
+------+--------+
2 rows in set (0.00 sec)

mysql> select * from stu;
+------+---------+------+
| sid  | sname   | cid  |
+------+---------+------+
|    1 | larry01 |    1 |
|    2 | larry02 |    2 |
+------+---------+------+
2 rows in set (0.00 sec)
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之真实环境使用冷备" height="16px" width="16px" border="0" alt="MySQL备份与恢复之真实环境使用冷备" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
