---
published: true
author: Robin Wen
layout: post
title: "Mac OS X 运行多个版本 MySQL 之解决方案"
category: 数据库
summary: "MySQL 中允许运行多个实例，举一反三，也可以同时运行多个版本的 MySQL。每个版本的 MySQL 占有独立的数据目录、端口、Socket、PID 即可。有了这种思路，就不难实现了。"
tags:
- 数据库
- Database
- MySQL
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**环境**

OS: Mac OS X 10.9.5
MySQL: 5.1.73/5.5.40/5.6.21

**解决思路**

MySQL 中允许运行多个实例，举一反三，也可以同时运行多个版本的 MySQL。每个版本的 MySQL 占有独立的数据目录、端口、Socket、PID 即可。有了这种思路，就不难实现了。

**具体实施步骤**

创建 MySQL 主目录，解压不同版本的 MySQL 到 该目录。

``` bash
sudo mkdir /usr/local/mysql
sudo tar -zxvf ~/Downloads/mysql-5.1.73-osx10.6-x86_64.tar.gz \
-C /usr/local/mysql/
sudo tar -zxvf ~/Downloads/mysql-5.5.40-osx10.6-x86_64.tar.gz \
-C /usr/local/mysql/
sudo tar -zxvf ~/Downloads/mysql-5.6.21-osx10.9-x86_64.tar.gz \
-C /usr/local/mysql/
ll /usr/local/mysql
```

创建多个版本的 MySQL 的数据目录。

``` bash
sudo mkdir /usr/local/mysql/data
sudo mkdir /usr/local/mysql/data/5.1
sudo mkdir /usr/local/mysql/data/5.5
sudo mkdir /usr/local/mysql/data/5.6
```

修改权限。

``` bash
id mysql
sudo chown -R mysql:mysql /usr/local/mysql
```

添加环境变量。

``` bash
vim ~/.bash_profile
grep mysql-5.1.73 ~/.bash_profile
```

内容如下：

> export PATH="/usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/bin:$PATHe

使配置生效。

``` bash
source ~/.bash_profile
```

安装 MySQL 5.1.73。

``` bash
cd /usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/
sudo scripts/mysql_install_db --user=mysql \
--datadir=/usr/local/mysql/data/5.1
```

安装 MySQL 5.5.40。

``` bash
cd /usr/local/mysql/mysql-5.5.40-osx10.6-x86_64/
sudo scripts/mysql_install_db --user=mysql \
--datadir=/usr/local/mysql/data/5.5o
```

安装 MySQL 5.6.21。

``` bash
cd /usr/local/mysql/mysql-5.6.21-osx10.8-x86_64/
sudo scripts/mysql_install_db --user=mysql \
--datadir=/usr/local/mysql/data/5.6
```

配置 MySQL Multi。

``` bash
sudo vim /etc/my.cnf
cat /etc/my.cnf
```

内容如下：

> [mysqld_multi]
> mysqld     = /usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/bin/mysqld_safe
> mysqladmin = /usr/local/mysql/mysql-5.1.73-osx10.6-x86_64/bin/mysqladmin
> log = /var/log/mysqld_mutil.err
> user       = root
>
> [mysqld5173]
> port=5173
> socket=/tmp/mysql_5173.sock
> basedir=/usr/local/mysql/mysql-5.1.73-osx10.6-x86_64
> datadir=/usr/local/mysql/data/5.1
> user=_mysql
> log-error=/var/log/mysqld_5173.log
> pid-file=/tmp/mysqld_5173.pid
>
> [mysqld5540]
> port=5540
> socket=/tmp/mysql_5540.sock
> basedir=/usr/local/mysql/mysql-5.5.40-osx10.6-x86_64
> datadir=/usr/local/mysql/data/5.5
> user=_mysql
> log-error=/var/log/mysqld_5540.log
> pid-file=/tmp/mysqld_5540.pid
>
> [mysqld5612]
> port=5612
> socket=/tmp/mysql_5612.sock
> basedir=/usr/local/mysql/mysql-5.6.21-osx10.8-x86_64
> datadir=/usr/local/mysql/data/5.6
> user=_mysql
> log-error=/var/log/mysqld_5612.log
> pid-file=/tmp/mysqld_5612.pid

再次修改环境变量，设置启动和关闭数据库的别名。

``` bash
vim ~/.bash_profile
```

内容如下：

> \#   ---------------------------------------
> \#   10.  MySQL alias command configuration
> \#   ---------------------------------------
>
> \#   Start MySQL 5.1
> \#   ---------------------------------------
> alias sta-5.1='sudo mysqld_multi start 5173 && sleep 2 && ps -ef | grep mysql'
>
> \#   Stop MySQL 5.1
> \#   ---------------------------------------
> alias sto-5.1="ps -ef | grep mysql_5173 | grep -v grep | awk -F' ' '{print $2}' | xargs sudo kill -9"
>
> \#   Start MySQL 5.5
> \#   ---------------------------------------
> alias sta-5.5='sudo mysqld_multi start 5540 && sleep 2 && ps -ef | grep mysql'
>
> \#   Stop MySQL 5.5
> \#   ---------------------------------------
> alias sto-5.5="ps -ef | grep mysql_5540 | grep -v grep | awk -F' ' '{print $2}' | xargs sudo kill -9"
>
> \#   Start MySQL 5.6
> \#   ---------------------------------------
> alias sta-5.6='sudo mysqld_multi start 5612 && sleep 2 && ps -ef | grep mysql'
>
> \#   Stop MySQL 5.6
> \#   ---------------------------------------
> alias sto-5.6="ps -ef | grep mysql_5612 | grep -v grep | awk -F' ' '{print $2}' | xargs sudo kill -9"

使配置生效。

``` bash
source ~/.bash_profile
```

**测试**

依次启动 MySQL 5.1.73、MySQL 5.5.40、MySQL 5.6.12。

``` bash
sta-5.1
sta-5.5
sta-5.6
```

依次测试连接是否成功。

``` bash
mysql --socket=/tmp/mysql_5173.sock -e "SELECT version();"
```

> +-----------+
> | version() |
> +-----------+
> | 5.1.73    |
> +-----------+

``` bash
mysql --socket=/tmp/mysql_5540.sock -e "SELECT version();"
```

> +-----------+
> | version() |
> +-----------+
> | 5.5.40    |
> +-----------+

``` bash
mysql --socket=/tmp/mysql_5612.sock -e "SELECT version();"
```

> +-----------+
> | version() |
> +-----------+
> | 5.6.21    |
> +-----------+

依次关闭 MySQL 5.1.73、MySQL 5.5.40、MySQL 5.6.12。

``` bash
sto-5.1
sto-5.5
sto-5.6
```

文中涉及的配置文件：<a href="https://github.com/dbarobin/configfiles" target="_blank">configfiles</a>

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

Enjoy!

Reference:
<a href="http://criticallog.thornet.net/2011/05/24/running-multiple-mysql-instances-on-macosx/" target="_blank">Running multiple MySQL instances on MacOsX</a>

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享3.0许>可证）</a>
