---
published: true
author: Robin Wen
layout: post
title: "MySQL BIGINT UNSIGNED value is out of range"
category: 数据库
summary: "MySQL中，SMALLINT 有符号数的范围是 -32768 到 32767，无符号数的范围是 0 到 65535。因为创建表时，定义为无符号数，cola - 100 显然超过了它所表示的范围，所以会报错。我们在查询时，把无符号数转换为有符号数，就不会有这个问题了。"
tags:
- 数据库
- Database
- MySQL
- 故障排查
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

环境：
Linux：RHEL 6.1
MySQL：5.5

登录到MySQL。

``` bash
mysql -uroot -proot
```

创建测试表：

``` bash
mysql> USE test;
Database changed
mysql> CREATE TABLE data_type(cola smallint unsigned) DEFAULT CHARACTER SET utf8;

Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO data_type VALUES(27);
Query OK, 1 row affected (0.01 sec)
```

查询数据：

``` bash
mysql> SELECT cola-100 FROM data_type;
ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '(`test`.`data_type`.`cola` - 100)'
```

可以看到，报错：`BIGINT UNSIGNED value is out of range`。

解决方法：

``` bash
mysql> SELECT (CAST(cola AS SIGNED)-CAST(100 AS SIGNED)) AS cola from data_type;
+------+
| cola |
+------+
|  -73 |
+------+
1 row in set (0.00 sec)

```

究其原因：

``` bash
mysql> HELP smallint;

Name: 'SMALLINT'
Description:
SMALLINT[(M)] [UNSIGNED] [ZEROFILL]

A small integer. The signed range is -32768 to 32767. The unsigned
range is 0 to 65535.

URL: http://dev.mysql.com/doc/refman/5.5/en/numeric-type-overview.html
```

可以看到，MySQL中，`SMALLINT` 有符号数的范围是 -32768 到 32767，无符号数的范围是 0 到 65535。因为创建表时，定义为无符号数，cola - 100 显然超过了它所表示的范围，所以会报错。我们在查询时，把无符号数转换为有符号数，就不会有这个问题了。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

Enjoy!

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
