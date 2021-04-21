---
published: true
author: Robin Wen
layout: post
title: "搭建 LAMP 环境(源码方式)"
category: 运维
summary: "LAMP：Linux、Apache、MySQL、PHP 的组合。目前企业使用较多。本文提供采用源码搭建 LAMP 环境的方法。"
tags:
- 运维
- MySQL
- Linux
- Apache
- PHP
---

## 目录 ##

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 关于LAMP ##

> LAMP：Linux、Apache、MySQL、PHP的组合。目前企业使用较多。

除了LAMP，LNMP使用也很频繁。LNMP只是把Apache换成了Nginx。

另外，还有一个组合叫做WAMP。如下：

> WAMP：Windows、 Apache、MySQL、PHP的组合。

安装顺序：首先MySQL，然后Apache，最后PHP。**注意：本文所有操作以root用户运行。**

版本说明：

> RHEL: 6.1 x86_64
> MySQL: 5.1.58
> PHP: 5.3.6
> Apache: 2.2.21

## 二 搭建LAMP ##

第一步，安装MySQL。

1.安装Development tools和ncurses-devel。

``` bash
yum grouplist | grep Devel
yum groupinstall "Development tools" -y
yum install ncurses-devel -y
```

2.解压。

``` bash
tar -xf mysql-5.1.58.tar.gz -C /usr/src/
cd /usr/src/mysql-5.1.58/
```

3.配置。

``` bash
./configure --prefix=/usr/local/mysql \
--with-extra-charsets=gbk,gb2312 \
—with-plugins=partition,innobase,innodb_plugin,myisam
```

4.编译。

``` bash
make
```

5.安装。
``` bash
make install
```

6.拷贝配置文件和执行脚本。

``` bash
cp support-files/my-medium.cnf /etc/my.cnf
cp support-files/mysql.server /etc/init.d/mysqld
chmod a+x/etc/init.d/mysqld
```

7.创建数据文件的存放路径，并修改my.cnf和mysqld文件。

``` bash
mkdir /usr/local/mysql/data
vim /etc/my.cnf
grep "^datadir" /etc/my.cnf -n
27:datadir           =/usr/local/mysql/data
vim /etc/init.d/mysqld
sed "46,47p" /etc/init.d/mysqld -n
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
```

8.新增用户，并加入MySQL的用户组。然后执行mysql_install_db脚本。

``` bash
groupadd mysql
useradd -g mysql mysql
./scripts/mysql_install_db --user=mysql
```

9.启动MySQL，进入/usr/local/mysql/bin/，执行mysql，查询MySQL版本。

``` bash
/etc/init.d/mysqld start
cd /usr/local/mysql/bin/
./mysql
```

``` bash
mysql> select version();
+------------+
| version() |
+------------+
| 5.1.58-log |
+------------+
1 row in set (0.00 sec)

mysql> create database larry defaultcharset utf8;
Query OK, 1 row affected (0.00 sec)

mysql> use larry;
Database changed

mysql> show tables;
Empty set (0.00 sec)

mysql> create table t_user(id int(11) \
primary key auto_increment, name varchar(20));
Query OK, 0 rows affected (0.01 sec)

mysql> insert into t_user(name)values('larrywen');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t_user(name)values('justdb');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t_user(name)values('wgb');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t_user;
+----+----------+
| id | name     |
+----+----------+
|  1 |larrywen |
|  2 |justdb   |
|  3 |wgb      |
+----+----------+
3 rows in set (0.00 sec)

mysql> create table t_log(id int(11)primary key auto_increment,\
content varchar(20), t_user_id int(11), \
constraintfk_larry_t_log_usr_id foreign key(t_user_id) references t_user(id));
Query OK, 0 rows affected (0.01 sec)

mysql> insert into t_log(content,t_user_id) values('Logining System', 1);
Query OK, 1 row affected (0.01 sec)

mysql> select * from t_log;
+----+-----------------+-----------+
| id | content         | t_user_id |
+----+-----------------+-----------+
|  1 |Logining System |         1 |
+----+-----------------+-----------+
1 row in set (0.00 sec)
mysql> exit
Bye
```

10.修改mysql目录的所有者和组拥有者。

``` bash
cd /usr/local/mysql
chown -R mysql .
chgrp -R mysql .
```

第二步，安装Apache。

1.解压。

``` bash
tar -xf httpd-2.2.21.tar.gz -C /usr/src
```

2.进入安装目录，检查配置。

``` bash
cd /usr/src/httpd-2.2.21/
./configure--help
./configure--prefix=/usr/local/apache \
--enable-modules=all \
--enable-mods-shared=all--enable-so \
--with-mpm=worker
```

如果出现zlib not found，安装zlib-devel。

``` bash
yum install zlib-devel -y
```

3.编译。

``` bash
make
```

4.安装。

``` bash
make install
```

5.进入/usr/local/apache/bin/目录，启动。

``` bash
cd /usr/local/apache/bin/
./apachectl -k start
```

如果出现如下问题：

> httpd: apr_sockaddr_info_get() failed forserv02.host.com
> httpd: Could not reliably determine theserver's fully qualified domain name, using 127.0.0.1 for ServerName

编辑httpd.conf文件，加上ServerName serv02.host.com。编辑hosts文件，加上192.168.1.12 serv02.host.com。

如下：

``` bash
vim ../conf/httpd.conf
grep "ServerName" /usr/local/apache/conf/httpd.conf
ServerName serv02.host.com
echo "192.168.1.12 serv02.host.com" >> /etc/hosts
tail -n1 /etc/hosts
192.168.1.12 serv02.host.com
```

再次启动，查看端口。

``` bash
./apachectl -k start
netstat -langput | grep httpd
```

6.测试。

浏览器输入http://192.168.1.12/ 如果出现“It works”则成功。

第三步，安装PHP。

1.解压。

``` bash
tar -xf php-5.3.6.tar.bz2 -C /usr/src/
```

2.进入/usr/src/php-5.3.6/目录，配置。

``` bash
cd /usr/src/php-5.3.6/
./configure —help
./configure--prefix=/usr/local/php5 \
--with-apxs2=/usr/local/apache/bin/apxs \
--with-mysql-sock=/tmp/mysql.sock \
--with-mysql=/usr/local/mysql/
```

如果出现如下错误，安装libxml2。

> checking libxml2 install dir... no
> checking for xml2-config path...
> configure: error: xml2-config not found.Please check your libxml2 installation.

``` bash
yum install libxml2*-y
```

如果出现如下文本，则证明配置成功。

> +\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-+
> | License:
> | 省略若干内容。
> +\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-+
>
> Thank you for using PHP.

3.编译。

``` bash
make
```

4.安装。

``` bash
make install
```

5.拷贝php.ini文件，修改httpd.conf文件。

``` bash
cp php.ini-development /usr/local/php5/lib/php.ini
grep -e "AddHandler" -e "AddType" /usr/local/apache/conf/httpd.conf
  AddHandler php5-script .php
  AddType text/html .php
```

## 三 测试 ##

1.修改root用户密码，创建测试数据库和表。

``` bash
./mysql
mysql> set password=password("helloworld");
Query OK, 0 rows affected (0.00 sec)

mysql> exit
Bye
```

修改密码成功后，重新登录。
``` bash
./mysql -uroot -phelloworld
```

``` bash
mysql> use larry
Database changed
mysql> show tables;
+-----------------+
| Tables_in_larry |
+-----------------+
| t_log           |
| t_user          |
+-----------------+
2 rows in set (0.00 sec)

mysql> select * from t_user;
+----+----------+
| id | name     |
+----+----------+
|  1 |larrywen |
|  2 |justdb   |
|  3 |wgb      |
+----+----------+
3 rows in set (0.00 sec)
```

2.新建测试php文件。

``` bash
cd /usr/local/apache/htdocs/
vim index.php
cat index.php
```

脚本内容如下：

``` php
<?php
       phpinfo();
?>
```

新建测试LAMP整合脚本。

``` bash
vim user_list.php
pwd
/usr/local/apache/htdocs
vim user_list.php
cat user_list.php
```

脚本内容如下：

``` php
<?php
       $conn=mysql_connect("localhost","root", "helloworld");
       mysql_select_db("larry",$conn);
       $users=mysql_query("select* from t_user");

       while(!!$user=mysql_fetch_array($users)){
              echo$user["id"]."------->".$user["name"]."<br>";
       }
       mysql_close();
?>
```

3.浏览器输入http://192.168.1.12/index.php，如果出现php相关的配置信息，则证明LAMP环境配置成功。输入http://192.168.1.12/user_list.php，如果能出现以下的信息，则证明PHP、MySQL、Apache整合成功。

> 1------->larrywen
> 2------->justdb
> 3------->wgb

Enjoy!

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/12232889" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="搭建LAMP环境(源码方式)" height="16px" width="16px" border="0" alt="搭建LAMP环境(源码方式)" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
