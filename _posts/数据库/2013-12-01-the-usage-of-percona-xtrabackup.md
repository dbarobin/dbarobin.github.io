---
published: true
author: Robin Wen
layout: post
title: "MySQL备份与恢复之percona-xtrabackup软件的使用"
category: 数据库
summary: "在前面，我们讲到MySQL冷备、热备、mysqldump、热拷贝、保证数据的一致性。因为mysql冷备、mysqldump、mysql热拷贝均不能实现增量备份，在实际环境中增量备份是使用较多的，percona-xtrabackup就是为实现增量备份而生，因此我们需要使用percona-xtrabackup。在前面，我们讲到MySQL冷备、热备、mysqldump、热拷贝、保证数据的一致性。因为mysql冷备、mysqldump、mysql热拷贝均不能实现增量备份，在实际环境中增量备份是使用较多的，percona-xtrabackup就是为实现增量备份而生，因此我们需要使用percona-xtrabackup。"
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

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 使用percona-xtrabackup的原因 ##

在前面，我们讲到MySQL冷备、热备、mysqldump、热拷贝、保证数据的一致性。因为mysql冷备、mysqldump、mysql热拷贝均不能实现增量备份，在实际环境中增量备份是使用较多的，percona-xtrabackup就是为实现增量备份而生，因此我们需要使用percona-xtrabackup。

本文讲解percona-xtrabackup软件的使用，下一篇文章讲解percona-xtrabackup实现增量备份及恢复。

## 二 什么是percona-xtrabackup ##

> Percona XtraBackup is an open-source hot backup utility for MySQL -based servers that doesn’t lock your database during the backup.
>
> It can back up data from <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-innodb" target="_blank">InnoDB</a>, <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-xtradb" target="_blank">XtraDB</a>,and <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-myisam" target="_blank">MyISAM</a> tableson MySQL 5.1 <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/#n-1" target="_blank">[1]</a>, 5.5 and5.6 servers, as well as Percona Server with <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-xtradb" target="_blank">XtraDB</a>. For a high-level overview of many of its advanced features, including a featurecomparison, please see <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/intro.html" target="_blank">About Percona Xtrabackup</a>.
>
> Whether it is a 24x7 highly loaded server or alow-transaction-volume environment, Percona XtraBackup isdesigned to make backups a seamless procedure without disrupting theperformance of the server in a production environment.<a href="http://www.percona.com/services/mysql-support" target="_blank">Commercial support contracts areavailable</a>.
>
> Percona XtraBackup is a combination of the **xtrabackup** C program,and the **innobackupex** Perl script. The **xtrabackup** programcopies and manipulates <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-innodb" target="_blank">InnoDB</a> and <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-xtradb" target="_blank">XtraDB</a> datafiles, and the Perl script enables enhanced functionality,such as interacting with a running MySQL server and backing up <a href="http://www.percona.com/doc/percona-xtrabackup/2.1/glossary.html#term-myisam" target="_blank">MyISAM</a> tables.

## 三 软件及文档获取 ##

**软件获取**

<a href="http://www.percona.com/software/percona-xtrabackup/downloads" target="_blank"><img src="https://cdn.dbarobin.com/18VTVkQ.jpg" title="percona-xtrabackup" height="16px" width="16px" border="0" alt="percona-xtrabackup" /></a> <br/>
<a href="http://download.csdn.net/detail/wentasy/6638171" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="percona-xtrabackup" height="16px" width="16px" border="0" alt="percona-xtrabackup" /></a>

**文档获取**

<a href="http://www.percona.com/doc/percona-xtrabackup/2.1/" target="_blank"><img src="https://cdn.dbarobin.com/18VTVkQ.jpg" title="percona-xtrabackup" height="16px" width="16px" border="0" alt="percona-xtrabackup" /></a> <br/>
<a href="http://download.csdn.net/detail/wentasy/6638029" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="percona-xtrabackup" height="16px" width="16px" border="0" alt="percona-xtrabackup" /></a>

## 四 软件使用讲解 ##

**注：本文采用的percona-xtrabackup版本为2.0.2，操作系统版本为RHEL 6.1 Server，MySQL版本为5.1**

第一步，准备文件并拷贝文件

``` bash
ll percona-xtrabackup-2.0.2-461.rhel6.x86_64.rpm
scp percona-xtrabackup-2.0.2-461.rhel6.x86_64.rpm 192.168.1.11:/opt
```

第二步，该软件需要依赖MySQL客户端，所以使用yum安装。注意，此处安装的只是MySQL的客户端，和本身使用源码安装的MySQL不冲突。

``` bash
yum install percona-xtrabackup-2.0.2-461.rhel6.x86_64.rpm -y
Installed:
  percona-xtrabackup.x86_64 0:2.0.2-461.rhel6
Dependency Installed:
  mysql.x86_64 0:5.1.52-1.el6_0.1
```

第三步，初始化备份。

``` bash
innobackupex --user=root --password=123456 /databackup/
InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.

……
innobackupex: Backup created in directory '/databackup/2013-09-10_21-49-44'
innobackupex: MySQL binlog position: filename 'mysql-bin.000001', position 7312
130910 21:50:03  innobackupex: completed OK!
```

第四步，这样的备份文件无法使用，我们需要做统一检查。

``` bash
ls
2013-09-10_21-49-44

# 做统一检查
innobackupex --apply-log /databackup/2013-09-10_21-49-44/

InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.
……
xtrabackup: starting shutdown with innodb_fast_shutdown = 1
130910 21:51:52  InnoDB: Starting shutdown...
130910 21:51:56  InnoDB: Shutdown completed; log sequence number 2098188
130910 21:51:56  innobackupex: completed OK!
```

第五步，模拟数据丢失。

``` bash
rm -rf /usr/local/mysql/data/*
ll /usr/local/mysql/data/
```

第六步，恢复数据。

``` bash
innobackupex --copy-back /databackup/2013-09-10_21-49-44/
InnoDB Backup Utility v1.5.1-xtrabackup; Copyright 2003, 2009 Innobase Oy
and Percona Inc 2009-2012.  All Rights Reserved.
……
innobackupex: Starting to copy InnoDB system tablespace
innobackupex: in '/databackup/2013-09-10_21-49-44'
innobackupex: back to original InnoDB data directory '/usr/local/mysql/data'
innobackupex: Copying file '/databackup/2013-09-10_21-49-44/ibdata1'

innobackupex: Starting to copy InnoDB log files
innobackupex: in '/databackup/2013-09-10_21-49-44'
innobackupex: back to original InnoDB log directory '/usr/local/mysql/data'
innobackupex: Finished copying back files.

130910 22:02:29  innobackupex: completed OK!
```

第七步，重启mysql服务，发现报错，pkill掉，然后启动一切正常。

``` bash
/etc/init.d/mysqld restart
 ERROR! MySQL server PID file could not be found!
Starting MySQL. ERROR! The server quit without updating PID file
(/usr/local/mysql/data/serv01.host.com.pid).
```

查看恢复的数据目录，拥有者和所属组不是mysql用户，我们更改拥有者和所属组。
``` bash
ll /usr/local/mysql/data/
chown mysql.mysql /usr/local/mysql/data/ -R
```

再次启动，仍然失败，我们杀掉进程，再次启动mysql，正常。

``` bash
/etc/init.d/mysqld restart
 ERROR! MySQL server PID file could not be found!
Starting MySQL.. ERROR! The server quit without updating PID file
(/usr/local/mysql/data/serv01.host.com.pid).

ps -ef | grep mysql
pkill -9 mysql

/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!
```

登录到MySQL。

``` bash
mysql -uroot -p123456
Server version: 5.5.29-log Source distribution
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| game               |
| hello              |
| larrydb            |
| mnt                |
| mysql              |
| performance_schema |
| test               |
+--------------------+
8 rows in set (0.00 sec)

```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 五 参考资料 ##

Percona 文档：<a href="http://www.percona.com/doc/percona-xtrabackup/2.1/" target="_blank"><img src="https://cdn.dbarobin.com/18VTVkQ.jpg" title="percona-xtrabackup" height="16px" width="16px" border="0" alt="percona-xtrabackup" /></a>

–EOF–

原文地址：<a href="" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL备份与恢复之percona-xtrabackup软件的使用" height="16px" width="16px" border="0" alt="MySQL备份与恢复之percona-xtrabackup软件的使用" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
