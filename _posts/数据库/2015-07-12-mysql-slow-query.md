---
published: true
author: Robin Wen
layout: post
title: "一次慢查询导致的故障"
category: 数据库
summary: "周四一整天，研发反应某台数据库僵死，后面的会话要么连接不上，要么要花费大量的时间返回结果，哪怕是一个简单的查询。本文记录周四遇到的故障。最后做如下总结：出现这类问题的排查步骤：第一，查看服务器监控和 MySQL 监控，分析服务器以及 MySQL 性能，找出异常；第二，如果是慢查询导致，查看慢查询日志，找出出现问题的 SQL，试着优化，或者把结果缓存；第三，分清主次，先解决大块问题，后解决细小问题。 把大块的异常解决，小问题就迎刃而解了。比如本文中的例子，把耗费时间长的会话 kill 掉后，后面的连接就正常了；第四，总结分析。"
tags:
- 数据库
- Database
- MySQL
- 故障排查
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

## 一 引子 ##

很久没写技术文章了，打算最近几周把最近遇到的故障总结下。这篇文章分享周四遇到的故障。

另外，最近有创作欲望，只等时间宽裕。

## 二 起因 ##

周四一整天，研发反应某台数据库僵死，后面的会话要么连接不上，要么要花费大量的时间返回结果，哪怕是一个简单的查询。

## 三 处理 ##

首先去监控平台查看服务器以及数据库状态，发现这台数据库有大量的慢查询。继续看服务器监控，CPU 平均使用率较高，IO 读写平均值正常。登录到 MySQL，使用 SHOW PROCESSLIST 查看会话状态，总数居然有 600+，这是很不正常的。查看慢查询日志，发现出问题的 SQL 主要集中在几个，有 SUM、有 COUNT、有等值操作等等。这台 MySQL 服务器的 **long_query_time** 设置为 3秒，而一个简单的查询却要几十秒，这显然是有问题的。写脚本试着 kill 掉相关的会话，发现于事无补，仍然有大量的连接进来。此时使用 top 查看服务器状态，mysqld 进程占用内存和 CPU 居高不下。

故障期间的慢查询数，如图：

![SLow query](https://cdn.dbarobin.com/AF0BGeJ.png)

CPU 平均使用率，如图：

![CPU Usage](https://cdn.dbarobin.com/hsXu70E.png)

接着使用 SHOW FULL PROCESSLIST 查看完整状态，在最上面居然发现几条 SQL。这些 SQL 操作使用子查询实现，TIME 列居然达到了 30000 秒，折算过来差不多 10 小时。EXPLAIN 这些语句，居然出现了 USING TEMPORY 和 USING FILESORT，可以看出这些语句是很糟糕的。于是跟开发确认，紧急把这些会话 kill 掉。稍等片刻，会话数立马降下来，只有 100+，top 查看 mysqld 进程，内存和 CPU 都呈现下降的趋势。接着分析开发说上午 9 时写了这些 SQL，发现有问题，注释掉了。新的代码虽然没有此类 SQL，但之前建立的连接并不会释放。解决问题和出现问题的时间差刚好可以和添加子查询的时间对应，就可以确认子查询是此次故障的罪魁祸首。

## 四  总结 ##

通过这个故障，总结如下几点：

* MySQL 应该尽量避免使用子查询，即使使用，也要搞清楚大表和小表的关系；
* 出现这类问题的排查步骤：
1. 第一，查看服务器监控和 MySQL 监控，分析服务器以及 MySQL 性能，找出异常；
2. 第二，如果是慢查询导致，查看慢查询日志，找出出现问题的 SQL，试着优化，或者把结果缓存；
3. 第三，分清主次，先解决大块问题，后解决细小问题。 把大块的异常解决，小问题就迎刃而解了。比如本文中的例子，把耗费时间长的会话 kill 掉后，后面的连接就正常了；
4. 第四，总结分析。
* 高效的沟通会事半功倍；
* DBA 需要定期给出 Top N SQL（类 Oracle 的说法），提供给开发，并协助优化；
* 查看监控时，不管是服务器监控还是 MySQL 监控，需要做对比，比如和昨天甚至前天的同一时间对比，这会更加快速地定位问题。

## 五 技巧 ##

最后，附上一个快速 kill 掉 MySQL 会话的方法：

首先使用如下语句分析出有问题的 SQL：

``` bash
/usr/local/mysql/bin/mysql -uroot -p'XXX' \
-e "SHOW FULL PROCESSLIST;" | more
```

然后将 SHOW FULL PROCESSLIST 的结果保存到一个文件：

``` bash
/usr/local/mysql/bin/mysql -uroot -p'XXX' \
-e "SHOW FULL PROCESSLIST;" | \
grep "XXX" | awk '{print $1}' > mysql_slow.txt
```

最后使用如下简单的 Shell 脚本 kill 掉相关会话：

``` bash
#!/bin/bash
# Author: Robin Wen
# Date: 2015-07-09 18:37:29
# Desc: Kill slow query session.

for i in `cat mysql_slow.txt`
do
  /usr/local/mysql/bin/mysql -uroot -p'XXX' -e "kill $i"
done
```

当然也可以使用如下 SQL 拼接 kill 语句：

``` bash
SELECT concat('kill ',id,';')
FROM information_schema.processlist
WHERE info LIKE 'XXX';
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

Enjoy!

–EOF–

插图来自：监控系统

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
