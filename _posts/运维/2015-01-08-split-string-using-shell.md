---
published: true
layout: post
title: "Shell截取字符串"
category: 运维
author: Robin Wen
summary: "两种方法获取 MySQL 数据库表的字符集。"
tags:
- 运维
- Linux
- Shell
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

最近遇到个问题，需要获取表的字符集。

下面做一个演示。

环境：
Linux：RHEL 6.1
MySQL：5.1

首先创建测试库，测试表：

``` bash
CREATE DATABASE TEST DEFAULT CHARACTER SET UTF8;

USE test;

CREATE TABLE t(id INT, name VARCHAR(20)) CHARSET UTF8;
```

接着实现功能：

**第一种方法：**

``` bash
mysql -uroot -proot -Ne 'show create table test.t' | grep CHARSET | awk -F' ' '{print $16}'
```

这种方法的缺陷是：每张表的大小不一样，这样 `$16` 获得的数据不一定是 CHARSET 了。

**第二种方法：**

``` bash
mysql -uroot -proot -Ne 'show create table test.t' > file; \
sed 's/$/ROBIN/g' -i file; awk '{sub(/^.*DEFAULT /, ""); \
sub(/ROBIN.*/, ""); print}' file
CHARSET=utf8

mysql -uroot -proot -Ne 'show create table test.t' > file; \
sed 's/$ROBIN/g' -i file; \
awk '{sub(/^.*DEFAULT CHARSET=/, ""); sub(/ROBIN.*/, ""); print}' file > newfile; \
echo CHARSET=\`cat newfile\`
CHARSET=utf8
```

这种方法的基本思路是：保存文件，追加内容，再截取字符串之间的东西，也就是那个CHARSET。

缺点是麻烦，复杂。

**最简单的方法：**

``` bash
mysql -uroot -proot -Ne 'show create table test.t' | awk -F 'CHARSET=' '{print $2}'
```

这种方法结合上述两种方法的优点。赞。

很简单的一个分享，记录下来。

Enjoy!

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
