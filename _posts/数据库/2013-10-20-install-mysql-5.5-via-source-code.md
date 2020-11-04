---
published: true
author: Robin Wen
layout: post
title: "MySQL 5.5源码安装"
category: 数据库
summary: "MySQL 5.5的安装方法和5.1略有 不同，主要区别在配置环境，MySQL 5.1的安装方法，参考搭建LAMP环境(源码方式)。本文讲解怎样在RedHat 6.1系统上安装MySQL 5.5。"
tags:
- 数据库
- Database
- MySQL
- 运维
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

MySQL 5.5的安装方法和5.1略有 不同，主要区别在配置环境，MySQL 5.1的安装方法，参考搭建LAMP环境(源码方式)。本文讲解怎样在RedHat 6.1系统上安装MySQL 5.5。

首先，我们要准备MySQL，至于在什么地方下载，想必不用多说，这个可难不倒聪明的小伙伴们。本文使用的MySQL版本是5.5.29，假设读者已经把该版本或者5.5的其他版本准备好了，下面正式讲解怎样安装MySQL 5.5。本文的操作均在虚拟机下完成，并且均以root用户运行。

第一步，真实机拷贝MySQL 5.5源码包到虚拟机下。

``` bash
yum install /usr/bin/scp -y
scp mysql-5.5.29.tar.gz 192.168.1.11:/opt
```

第二步，对源码进行编译需要make等命令，所以我们安装开发工具包。

``` bash
yum grouplist | grep Devel
yum groupinstall "Development tools" -y
```

第三步，解压源码包到/usr/src目录，/usr/src是建议路径。

``` bash
tar -xvf mysql-5.5.29.tar.gz -C /usr/src/
```

第四步，进入MySQL的解压目录。

``` bash
cd /usr/src/mysql-5.5.29/
```

INSTALL-SOURCE是安装帮助文档，可以参考这个文件进行安装。

> shell> tar zxvf mysql-VERSION.tar.gz
> shell> cd mysql-VERSION
> shell> cmake .
> shell> make
> shell> make install
> # End of source-build specific instructions
> # Postinstallation setup
> shell> cd /usr/local/mysql
> shell> chown -R mysql .
> shell> chgrp -R mysql .
> shell> scripts/mysql_install_db--user=mysql
> shell> chown -R root .
> shell> chown -R mysql data
> # Next command is optional
> shell> cp support-files/my-medium.cnf \
/etc/my.cnf
> shell> bin/mysqld_safe --user=mysql &
> # Next command is optional
> shell> cp support-files/mysql.server \
> /etc/init.d/mysql.server

第五步，因为配置环境需要使用到cmake，且MySQL依赖ncurses-devel包，所以我们安装cmake和ncurses-devel。

``` bash
yum install cmake-y
yum install ncurses-devel -y
```

第六步，关键步骤，这一步也是和MySQL 5.1的不同之处，使用cmake命令配置环境，如下

``` bash
cmake .  \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DWITH_INNOBASE_STORAGE_ENGINE=1  \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_MEMORY_STORAGE_ENGINE=1 \
-DDEFAULT_CHARSET=utf8  \
-DDEFAULT_COLLATION=utf8_general_ci  \
-DWITH_EXTRA_CHARSETS=all \
-DMYSQL_TCP_PORT=3306  \
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock  \
-DMYSQL_DATADIR=/usr/local/mysql/data
```

**解释：**
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql：MySQL安装目录，推荐安装到此目录
-DWITH_INNOBASE_STORAGE_ENGINE=1：安装InnoDB存储引擎
-DWITH_MYISAM_STORAGE_ENGINE=1：安装MyISAM存储引擎
-DWITH_MEMORY_STORAGE_ENGINE=1：安装内存存储引擎
-DDEFAULT_CHARSET=utf8：默认编码设置成utf8
-DDEFAULT_COLLATION=utf8_general_ci：默然校验规则是utf8_general_ci
-DWITH_EXTRA_CHARSETS=all：支持其他所有的编码
-DMYSQL_TCP_PORT=3306：MySQL端口指定为3306
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock：指定SOCK文件路径
-DMYSQL_DATADIR=/usr/local/mysql/data：MySQL数据目录

第七步，编译安装。

``` bash
make && make install
```

安装完成后，确定MySQL目录存在。

``` bash
ls /usr/local/mysql/
```

第八步，添加mysql组和用户。

``` bash
groupadd -g 500 mysql
useradd -u 500 -g 500 -r -M -s /sbin/nologin mysql
```

第九步，拷贝配置文件和启动脚本，并修改启动脚本的执行权限。

``` bash
cp support-files/my-medium.cnf /etc/my.cnf
cp support-files/mysql.server /etc/init.d/mysqld
chmod a+x /etc/init.d/mysqld
ls /usr/local/mysql/data/
```

第十步，改变mysql目录的拥有者和所属组，并修改my.cnf文件，添加data目录。

``` bash
chown mysql.mysql/usr/local/mysql/ -R
vim /etc/my.cnf
cat /etc/my.cnf |grep datadir
datadir         =/usr/local/mysql/data
```

第十一步，修改mysql_install_db的权限，使其可执行，并进行初始化操作。

``` bash
chmod a+x scripts/mysql_install_db
./scripts/mysql_install_db --user=mysql \
--datadir=/usr/local/mysql/data/ \
--basedir=/usr/local/mysql/
```

第十二步，启动MySQL，如果出现SUCCESS，恭喜您，MySQL启动成功；如果出错，不要着急，根据日志排查错误。

``` bash
/etc/init.d/mysqld start
Starting MySQL.. SUCCESS!
ll /usr/local/mysql/data/ -d
```
[root@serv01 mysql-5.5.29]#

第十三步，添加环境变量，并使其生效。
``` bash
vim~/.bash_profile
cat ~/.bash_profile| grep PATH
PATH=/usr/local/mysql/bin/:$PATH:$HOME/bin
export PATH
. !$
```

第十四步，登录mysql，查看版本，如果出现版本号，则证明安装成功。

``` bash
mysql
```

``` bash
mysql> select version();
+------------+
| version() |
+------------+
| 5.5.29-log |
+------------+
1 row in set (0.00 sec)

mysql> exit
Bye
```

如果需要安装多个MySQL，需要修改端口和修改sock文件。

``` bash
cat /etc/my.cnf |grep -e sock -e port
port              =3306
socket           =/tmp/mysql.sock
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/12881957" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="MySQL 5.5源码安装" height="16px" width="16px" border="0" alt="MySQL 5.5源码安装" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
