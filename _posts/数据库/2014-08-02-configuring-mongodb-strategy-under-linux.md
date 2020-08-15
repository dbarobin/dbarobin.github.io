---
published: true
author: Robin Wen
layout: post
title: "深入理解MongoDB（一）Linux下配置MongoDB全攻略"
category: 数据库
summary: "这是深入理解MongoDB的第一篇文章，本篇文章简要地介绍了MongoDB，并把Linux下完整的配置过程呈现给读者。"
tags:
- 数据库
- Database
- MongoDB
- 运维
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

## 一 MongoDB简介 ##

> MongoDB是一个高性能，开源，无模式的文档型数据库，是当前NoSql数据库中比较热门的一种。它在许多场景下可用于替代传统的关系型数据库或键/值存储方式，Mongo使用C++开发。Mongo的官方网站地址是：<a href="http://www.mongodb.org/"><img src="https://cdn.dbarobin.com/16LI7Gj.png" title="MongoDB" border="0" alt="MongoDB" height="16px" width="16px" /></a>，读者可以在此获得更详细的信息。

**特点：**
它的特点是高性能、易部署、易使用，存储数据非常方便。主要功能特性有：

* 面向集合存储，易存储对象类型的数据。
* 模式自由。
* 支持动态查询。
* 支持完全索引，包含内部对象。
* 支持查询。
* 支持复制和故障恢复。
* 使用高效的二进制数据存储，包括大型对象（如视频等）。
* 自动处理碎片，以支持云计算层次的扩展性。
* 支持RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
* 文件存储格式为BSON（一种JSON的扩展）。
* 可通过网络访问。

**功能:**

* 面向集合的存储：适合存储对象及JSON形式的数据。
* 动态查询：Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
* 完整的索引支持：包括文档内嵌对象及数组。Mongo的查询优化器会分析查询表达式，并生成一个高效的查询计划。
* 查询监视：Mongo包含一个监视工具用于分析数据库操作的性能。
* 复制及自动故障转移：Mongo数据库支持服务器之间的数据复制，支持主-从模式及服务器之间的相互复制。复制的主要目标是提供冗余及自动故障转移。
* 高效的传统存储方式：支持二进制数据及大型对象（如照片或图片）
* 自动分片以支持云级别的伸缩性：自动分片功能支持水平的数据库集群，可动态添加额外的机器。

**适用场景：**

* 网站实时数据处理。它非常适合实时的插入、更新与查询，并具备网站实时数据存储所需的复制及高度伸缩性。
* 缓存。由于性能很高，它适合作为信息基础设施的缓存层。在系统重启之后，由它搭建的持久化缓存层可以避免下层的数据源过载。
* 高伸缩性的场景。非常适合由数十或数百台服务器组成的数据库，它的路线图中已经包含对MapReduce引擎的内置支持。

**不适用场景：**

* 要求高度事务性的系统。
* 传统的商业智能应用。
* 复杂的跨文档（表）级联查询。

## 二 MongoDB配置全攻略 ##

版本说明：

> RedHat：6.1 x86_64
> MongoDB：2.6.3

首先，我们到官网：<a href="http://www.mongodb.org/downloads"><img src="https://cdn.dbarobin.com/16LI7Gj.png" title="MongoDB" border="0" alt="MongoDB" height="16px" width="16px" /></a>，然后下载64位Linux 版的MongoDB；

然后，做配置MongoDB之前的准备工作；

``` bash
# 创建MongoDB主目录
mkdir /usr/local/mongodb/
# 解压MongoDB包到MongoDB主目录
tar -xvf mongodb-linux-x86_64-2.6.3.tgz -C /usr/local/mongodb/
# 创建MongoDB数据目录，可以存放到其他位置，比如RAID、LVM上
mkdir /usr/local/mongodb/data/
# 创建MongoDB日志目录，建议放到var目录下
mkdir /usr/local/mongodb/log/
```

接着，我们使用mongod命令启动MongoDB，再打开另一个终端，使用mongo命令连接到MongoDB；
``` bash
# 进入MongoDB的bin目录，启动之
cd /usr/local/mongodb/bin/
./mongod --dbpath=/usr/local/mongodb/data/ --logpath=/usr/local/mongodb/log/mongo.log

# 登录到MongoDB
./mongo
MongoDB shell version: 2.6.3
connecting to: test
>
```

接着，配置环境变量；

``` bash
# 查看当前路径
pwd
/usr/local/mongodb/bin
# 编辑bash_profile，内容如下
vim ~/.bash_profile
tail -n3 !$
tail -n3 ~/.bash_profile
PATH=$PATH:$HOME/bin:/usr/local/mongodb/bin

export PATH
# 使配置生效
source !$
```

为了更方便的启动和关闭MongoDB，我们可以使用Shell写脚本，当然也可以加入到service中；

``` bash
cp ssh mongodb
vim mongodb
cat mongodb

# 脚本内容如下：

#!/bin/bash
#
# mongod        Start up the MongoDB server daemon
#

# source function library
. /etc/rc.d/init.d/functions

#定义命令
CMD=/usr/local/mongodb/bin/mongod
#定义数据目录
DBPATH=/usr/local/mongodb/data
#定义日志目录
LOGPATH=/usr/local/mongodb/log/mongo.log

start()
{
    #fork表示后台运行
    $CMD --dbpath=$DBPATH --logpath=$LOGPATH --fork
    echo "MongoDB is running background..."
}

stop()
{
    pkill mongod
    echo "MongoDB is stopped."
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    *)
        echo $"Usage: $0 {start|stop}"
esac
```

我们可以测试编写脚本的正确性；

``` bash
/etc/init.d/mongodb start
about to fork child process, waiting until server is ready for connections.
forked process: 1347
child process started successfully, parent exiting
MongoDB is running background...

/etc/init.d/mongodb stop
Terminated
```

当然，更好的方式是采用配置文件，把MongoDB需要的参数写入配置文件，然后在脚本中引用；

``` bash
vim mongodb.conf
cat mongodb.conf
#代表端口号，如果不指定则默认为27017
#port=27027
#MongoDB数据文件目录
dbpath=/usr/local/mongodb/data
#MongoDB日志文件目录
logpath=/usr/local/mongodb/log/mongo.log
#日志文件自动累加
logappend=true
```

编写好配置文件后，我们需要修改启动脚本；

``` bash
vim mongodb
cat mongodb

#!/bin/bash
#
# mongod        Start up the MongoDB server daemon
#

# source function library
. /etc/rc.d/init.d/functions
#定义命令
CMD=/usr/local/mongodb/bin/mongod
#定义配置文件路径
INITFILE=/usr/local/mongodb/mongodb.conf
start()
{
    #&表示后台启动，也可以使用fork参数
    $CMD -f $INITFILE &
    echo "MongoDB is running background..."
}

stop()
{
    pkill mongod
    echo "MongoDB is stopped."
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    *)
        echo $"Usage: $0 {start|stop}"
esac
```

编写完成后，再次测试脚本的正确性。

``` bash
# /etc/init.d/mongodb start
MongoDB is running background...

# /etc/init.d/mongodb stop
Terminated
```

优化过的MongoDB启动脚本，如下：

``` bash
#!/bin/bash
# Author: Robin Wen
# Date: 16:20:50 2014-12-12
# Desc: Mongodb startup and shutdown scripts.

# Mongod command path.
mongod=/usr/local/mongodb/bin/mongod
# MongoDB data path.
mongod_data=/data/db
# MongoDB config path.
mongod_conf=/etc/mongod.conf
# MongoDB log path.
mongod_log=/var/log/mongodb.log
# MongDB program name.
prog=mongod

RETVAL=0

# Handle NUMA access to CPUs (SERVER-3574)
# This verifies the existence of numactl \
#as well as testing that the command works
NUMACTL_ARGS="--interleave=all"
if which numactl >/dev/null 2>/dev/null \
&& numactl $NUMACTL_ARGS ls / >/dev/null 2>/dev/null
then
    NUMACTL="`which numactl` -- $NUMACTL_ARGS"
    DAEMON_OPTS=${DAEMON_OPTS:-"--config $mongod_conf"}
else
    NUMACTL=""
    DAEMON_OPTS="-- "${DAEMON_OPTS:-"--config $mongod_conf"}
fi

# Stop MongoDB function.
stop() {
    grep_mongo=`ps aux | grep -v grep | grep "${mongod}"`
    if [ ${#grep_mongo} -gt 0 ]
    then
	echo "MongoDB Stopped!"
		PID=`ps x | grep -v grep | grep "${mongod}" \
		| awk '{ print $1 }'`
		`kill -9 ${PID}`
		RETVAL=$?
    else
		echo "MongoDB is not running."
    fi
}

# Start MongoDB function.
start() {
    grep_mongo=`ps aux | grep -v grep | grep "${mongod}"`
    if [ -n "${grep_mongo}" ]
    then
		echo "MongoDB is already running."
    else
		start-stop-daemon --background \
		--start --quiet --exec $NUMACTL $mongod $DAEMON_OPTS
		echo "MongoDB Started."
		RETVAL=$?
    fi
}

# MongoDB status funciton.
status() {
    grep_mongo=`ps aux | grep -v grep | grep "${mongod}"`
    if [ -n "${grep_mongo}" ]
    then
		echo "MongoDB is running."
    else
		echo "MongoDB is stopped."
		RETVAL=$?
    fi
}

case "$1" in
    start)
		start
	;;
    stop)
		stop
	;;
    restart)
		stop
		start
	;;
	status)
		status
	;;
    *)
		echo $"Usage: $prog {start|stop|restart|status}"
		exit 1
esac

exit $RETVAL
```

## 三 后记 ##

这是深入理解MongoDB的第一篇文章，本篇文章简要地介绍了MongoDB，并把Linux下完整的配置过程呈现给读者。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 四 参考资料 ##

MongoDB（分布式文档存储数据库） <a href="http://baike.baidu.com/subview/3385614/9338179.htm" target    ="_blank"><img src="https://cdn.dbarobin.com/yvMcMOD.png" title="baike" border="0" alt="baike" height="16px" width="16px" /></a>

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/38345537" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="深入理解MongoDB（一）Linux下配置MongoDB全攻略" height="16px" width="16px" border="0" alt="深入理解MongoDB（一）Linux下配置MongoDB全攻略" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
