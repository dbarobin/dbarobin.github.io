---
published: true
author: Robin Wen
layout: post
title: "数据库上的试错心理"
category: 数据库
summary: "从心理学上来讲，人类都有规避伤害的本能，埋藏在内心的规避伤害本能让我们错过太多。比如我们习惯了很多东西，一旦尝试其他的，就会遇到各种麻烦，但往往你尝试其他的会得到更好的，很多时候我们应该习惯改变习惯。尤其对做IT的人来说，死守“最好”的城池会让你止步不前，也会错过很多更好的东西。当你摆脱习惯，尝试其他，你的世界可以多一点不一样。在学习数据库时，如果不害怕“伤害”，不麻烦各种问题，你将会得到更广阔的天空。"
tags:
- 数据库
- Database
- MySQL
- 想法
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

如果我们习惯试错，将会收获更多。

最近一位好友问了我一个问题，就是MySQL的Delete语句删除会报ERROR1093 (HY000)错误。SQL语句如下：DELETE FROM test WHEREid=(SELECT max(id) FROM test); 我看了下这个SQL，语法上面没有什么问题啊，这不是标准SQL吗。于是我在Linux环境下测试了MySQL5.1版本和MySQL 5.5版本，均有这样的问题。后来我又到Oracle 11G R2环境下模拟了相同的问题，却可以正确地删除。后来才知道，使用MySQL进行DELETE FROM操作时，若子查询的 FROM 字句和更新或者删除对象使用同一张表，会出现错误。这里有一个变通的解决办法，可以通过多加一层SELECT别名表来变通解决。比如DELETE FROM test WHERE id=(SELECT max(id) FROM (SELECT * FROM test) AS t)，但是这样的效率是极低的。这也算是MySQL的一个坑吧。

以下是操作日志：

``` bash
mysql> SELECT max(id) FROM test;
+---------+
| max(id) |
+---------+
|   49134 |
+---------+
1 row in set (0.00 sec)

mysql> DELETE FROM test WHERE id=(SELECT max(id) FROM test);
ERROR 1093 (HY000): You can'tspecify target table 'test' for update in FROM clause
mysql> DELETE FROM test WHERE id=(SELECT max(id) FROM (SELECT * FROM test) AS t);
Query OK, 1 row affected (0.12sec)

mysql> SELECT max(id) FROM test;
+---------+
| max(id) |
+---------+
|   49133 |
+---------+
1 row in set (0.00 sec)
```

同理，UPDATE类似。

``` bash
mysql> UPDATE test SET name='LARRY' WHERE id=(SELECT max(id) FROM test);
ERROR 1093 (HY000): You can'tspecify target table 'test' for update in FROM clause
mysql> UPDATE test SET name='LARRY' WHERE id=(SELECT max(id) FROM (SELECT * FROM
 test) AS t);
Query OK, 1 row affected (0.16sec)
Rows matched: 1  Changed: 1 Warnings: 0

mysql> SELECT id,name FROM test WHERE id=(SELECT max(id) FROM test);
+-------+-------+
| id    | name |
+-------+-------+
| 49133 | LARRY |
+-------+-------+
1 row in set (0.03 sec)
```

我就是一个在学习过程喜欢尝试，喜欢试错的人，这样带给我的好处就是可以遇到更多的问题，学习到更多知识。同样一个问题，在不同的软件版本，不同的实验环境结果是不一样的。如果你尝试在排列组合允许的范围内模拟各种问题，那样你的成长会相当地快。同理，把试错心理运用在生活中，你可能会遇到更多的问题，这样你会尝试使用更多的办法来解决。年轻人不要怕犯错，害怕犯错的人是很难取得成长的。

![Trial and error psychology on the database](https://cdn.dbarobin.com/qssBzgF.jpg)

从心理学上来讲，人类都有规避伤害的本能，埋藏在内心的规避伤害本能让我们错过太多。比如我们习惯了很多东西，一旦尝试其他的，就会遇到各种麻烦，但往往你尝试其他的会得到更好的，很多时候我们应该习惯改变习惯。尤其对做IT的人来说，死守“最好”的城池会让你止步不前，也会错过很多更好的东西。当你摆脱习惯，尝试其他，你的世界可以多一点不一样。在学习数据库时，如果不害怕“伤害”，不麻烦各种问题，你将会得到更广阔的天空。

**年轻在于折腾。**

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：<a href="http://fineartamerica.com/featured/trial-and-error-larry-mulvehill.html" target="_blank"><img src="https://cdn.dbarobin.com/c9ZcLir.png" title="" height="16px" width="100px" border="0" alt="" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
