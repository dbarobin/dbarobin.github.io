---
published: true
author: Robin Wen
layout: post
title: "美团点评开源 SQL 优化工具 SQLAdvisor 测试报告"
category: 数据库
summary: "SQLAdvisor 是由美团点评公司北京 DBA 团队开发维护的 SQL 优化工具：输入 SQL，输出索引优化建议。它基于 MySQL 原生词法解析，再结合 SQL 中的 where 条件以及字段选择度、聚合条件、多表 Join 关系等最终输出最优的索引优化建议。目前 SQLAdvisor 在美团公司内部大量使用，较为成熟、稳定。美团点评开源 SQL 优化工具 SQLAdvisor 得到的优化建议比较满意，建议在线上试用一段时间。这个工具的成本在于需要在线上 DB 安装相关依赖，如果确认采用，可以考虑在初始化 DB 服务器时部署此工具。"
tags:
- 数据库
- Database
- MySQL
- 测试报告
- 工具
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x00 目录
***

* Table of Contents
{:toc}

| 日期 | 作者 |  文档概要 | 版本 | 更新历史 |
|:------------|:---------------|:-----|:-----|:-----|:-----|
| 2017/03/14 | robin |  美团点评开源 SQL 优化工具 SQLAdvisor 测试报告 | v1.0 | 文档初稿 |

## 0x01 SQLAdvisor 介绍
***

SQLAdvisor 是由美团点评公司北京 DBA 团队开发维护的 SQL 优化工具：输入 SQL，输出索引优化建议。它基于 MySQL 原生词法解析，再结合 SQL 中的 where 条件以及字段选择度、聚合条件、多表 Join 关系等最终输出最优的索引优化建议。目前 SQLAdvisor 在美团公司内部大量使用，较为成熟、稳定。[^1]

SQLAdvisor 的优点

* 基于 MySQL 原生词法解析，充分保证词法解析的性能、准确定以及稳定性；
* 支持常见的 SQL(Insert/Delete/Update/Select)；
* 支持多表 Join 并自动逻辑选定驱动表；
* 支持聚合条件 Order by 和 Group by；
* 过滤表中已存在的索引。

## 0x02 SQLAdvisor 原理
***

SQLAdvisor 架构流程图：

![SQLAdvisor Structure](https://cdn.dbarobin.com/Ndau7CG.png)

SQLAdvisor 包含了如下的处理方式：Join 处理、where 处理、计算区分度、添加备选索引、Group 与 Order 处理、驱动表选择、添加被驱动表备选索引、输出建议，具体的流程图可以参考：[美团点评 SQL 优化工具 SQLAdvisor 开源](http://tech.meituan.com/sqladvisor_pr.html)

## 0x03 SQLAdvisor 测试
***

### 3.1 SQLAdvisor 安装
***

> 参考 [^2].

**3.1.1 拉取最新代码**

``` bash
git clone https://github.com/Meituan-Dianping/SQLAdvisor.git
```

**3.1.2 安装依赖项**

``` bash
yum install -y cmake libaio-devel libffi-devel glib2 glib2-devel bison

# 因 yum 安装 Percona-Server-shared-56 失败，故使用 rpm 包安装，\
# 具体参考 https://github.com/Meituan-Dianping/SQLAdvisor/issues/12
yum install -y --enablerepo=Percona56 Percona-Server-shared-56
yum install -y Percona-Server-server-56 Percona-Server-client-56

rpm -ivh Percona-Server-shared-56-5.6.25-rel73.1.el6.x86_64.rpm

# 设置软链
cd /usr/lib64/
ls -l libperconaserverclient_r.so.18
ln -s libperconaserverclient_r.so.18 libperconaserverclient_r.so
```

**3.1.3 编译依赖项 sqlparser**

``` bash
1. cmake -DBUILD_CONFIG=mysql_release -DCMAKE_BUILD_TYPE=debug \
-DCMAKE_INSTALL_PREFIX=/usr/local/sqlparser ./
2. make && make install
```

**3.1.4 安装 SQLAdvisor 源码**

``` bash
1. cd sqladvisor/
2. cmake -DCMAKE_BUILD_TYPE=debug ./
3. make
4. cp sqladvisor /usr/local/bin
5. sqladvisor --help
Usage:
  sqladvisor [OPTION...] sqladvisor

SQL Advisor Summary

Help Options:
  -?, --help              Show help options

Application Options:
  -f, --defaults-file     sqls file
  -u, --username          username
  -p, --password          password
  -P, --port              port
  -h, --host              host
  -d, --dbname            database name
  -q, --sqls              sqls
  -v, --verbose           1:output logs 0:output nothing
```

### 3.2 导入测试数据
***

> 注：测试环境 MySQL 版本为 5.5.24-log。

为了隐私考虑，线上表名屏蔽，以 tableA 和 tableB 代替。脱敏处理的表结构如下：

``` sql
CREATE TABLE `tableA` ( \
  `ID` int(10) unsigned NOT NULL AUTO_INCREMENT, \
  `CATE` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `E_ID` char(18) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `RD` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `RU` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `MO` int(10) NOT NULL DEFAULT '0' COMMENT 'xxxx', \
  `LID` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `LEVEL` tinyint(1) NOT NULL DEFAULT '0' COMMENT 'xxxx', \
  `GI` varchar(15) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `GT` datetime DEFAULT NULL COMMENT 'xxxx', \
  `CL` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `ST` tinyint(1) NOT NULL DEFAULT '0' COMMENT 'xxxx', \
  `RES` varchar(64) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  PRIMARY KEY (`ID`), \
  UNIQUE KEY `i_e_id` (`E_ID`), \
  KEY `i_lid` (`LID`), \
  KEY `i_rd_ru_mo` (`RD`,`RU`,`MO`) \
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='tableA'

CREATE TABLE `tableB` ( \
  `ID` int(11) NOT NULL AUTO_INCREMENT, \
  `LID` varchar(32) NOT NULL COMMENT 'xxxx', \
  `NAME` varchar(45) NOT NULL COMMENT 'xxxx', \
  `CL` varchar(30) NOT NULL COMMENT 'xxxx', \
  `TIME` datetime DEFAULT NULL COMMENT 'xxxx', \
  `NUM` varchar(64) NOT NULL COMMENT 'xxxx', \
  `SOUR` varchar(32) NOT NULL COMMENT 'xxxx', \
  `GI` varchar(15) NOT NULL COMMENT 'xxxx', \
  `TYPE` tinyint(4) NOT NULL COMMENT 'xxxx', \
  `SID` int(11) NOT NULL COMMENT 'xxxx', \
  `SEID` int(11) NOT NULL COMMENT 'xxxx', \
  `NAME` varchar(20) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `ADD` varchar(255) NOT NULL COMMENT 'xxxx', \
  `PO` varchar(11) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `QQ` varchar(20) NOT NULL COMMENT 'xxxx', \
  `CATE` varchar(20) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `RU` varchar(32) NOT NULL DEFAULT '' COMMENT 'xxxx', \
  `SC` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'xxxx', \
  `LEVEL` varchar(32) DEFAULT '' COMMENT 'xxxx', \
  PRIMARY KEY (`ID`), \
  KEY `i_user` (`LID`,`CATE`), \
  KEY `i_num` (`NUM`), \
  KEY `i_cl` (`CL`) \
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='tableB'
```

导入数据量如下：

``` sql
mysql> SELECT COUNT(*) FROM tableA;
+----------+
| COUNT(*) |
+----------+
|   122658 |
+----------+
1 row in set (0.06 sec)

mysql> SELECT COUNT(*) FROM tableB;
+----------+
| COUNT(*) |
+----------+
|   979525 |
+----------+
1 row in set (0.23 sec)
```

### 3.3 执行测试
***

测试的语句如下：

``` bash
a. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT * FROM tableA WHERE LID = 'xxxx' ORDER BY GT DESC" -v 1

b. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT E_ID, LID, MO, GT FROM tableA WHERE \
ST != '1' OR RES != '1' LIMIT 100" -v 1

c. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT MAX(NUM) FROM tableB WHERE LID = 'xxxx' AND \
CL='xxxx'" -v 1

d. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT * FROM tableB WHERE LID = 'xxxx' AND CL \
LIKE 'xxx_%' LIMIT 1" -v 1

e. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT COUNT(*) FROM tableB WHERE  LID = 'xxxx' AND \
CL = 'xxxx' AND SID = '1' AND TIME >= '2017-03-07 00:07:51'" -v 1

f. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT COUNT(1) AS NUM FROM tableB WHERE \
LEFT(TIME, 10) = '2017-03-07' AND LID    = 'xxxx' AND SOUR = 'xxxx'" -v 1

g. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT COUNT(1) AS NUM FROM tableB WHERE \
LEFT(TIME, 10) = '2017-03-07' AND LID    = 'xxxx' AND \
SOUR = 'xxxx' AND RU = '_xxxx_'" -v 1

h. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT COUNT(1) AS NUM FROM tableB WHERE \
LEFT(TIME, 10) = '2017-03-07' AND LID = 'xxxx' AND \
SOUR = 'xxxx' AND CL = 'xxxx'" -v 1

i. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT TIME FROM tableB WHERE LID = 'xxxx' AND \
SOUR = 'xxxx' AND CL = 'xxxx'" -v 1

j. /usr/local/bin/sqladvisor -u root -p 'xxxx' -P 3306 -h xxx.xxx.xxx.xxx -d databaseA \
-q "SELECT LID, TIME, NUM, CL FROM tableB \
WHERE LID = 'xxxx' AND SOUR = 'xxxx'" -v 1
```

其中 a 语句输出信息如下：

``` bash
2017-03-14 12:30:51 1923 [Note] 第1步: 对SQL解析优化之后得到的SQL:\
select `*` AS `*` from `databaseA`.`tableA` where (`LID` = 'xxxx') \
order by `GT` desc
2017-03-14 12:30:51 1923 [Note] 第2步：开始解析where中的条件:(`LID` = 'xxxx')
2017-03-14 12:30:51 1923 [Note] show index from tableA
2017-03-14 12:30:51 1923 [Note] show table ST like 'tableA'
2017-03-14 12:30:51 1923 [Note] select count(*) from ( select `LID` from `tableA` \
    FORCE INDEX( i_E_ID ) order by E_ID DESC limit 10000) `tableA` \
    where (`LID` = 'xxxx')
2017-03-14 12:30:51 1923 [Note] 第3步：表tableA的行数:122879,limit行数:10000,\
得到where条件中(`LID` = 'xxxx')的选择度:10000
2017-03-14 12:30:51 1923 [Note] 第4步：开始解析order by 条件
2017-03-14 12:30:51 1923 [Note] 第5步：开始验证 字段GT是不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] show index from tableA where Key_name = 'PRIMARY' \
and Column_name ='GT' and Seq_in_index = 1
2017-03-14 12:30:51 1923 [Note] 第6步：字段GT不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] 第7步：开始添加order by 字段
2017-03-14 12:30:51 1923 [Note] 第8步：开始验证 字段GT是不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] show index from tableA where Key_name = 'PRIMARY' \
and Column_name ='GT' and Seq_in_index = 1
2017-03-14 12:30:51 1923 [Note] 第9步：字段GT不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] 第10步：开始验证 字段LID是不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] show index from tableA where Key_name = 'PRIMARY' \
and Column_name ='LID' and Seq_in_index = 1
2017-03-14 12:30:51 1923 [Note] 第11步：字段LID不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] 第12步：开始验证 字段LID是不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] show index from tableA where Key_name = 'PRIMARY' \
and Column_name ='LID' and Seq_in_index = 1
2017-03-14 12:30:51 1923 [Note] 第13步：字段LID不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] 第14步：开始验证表中是否已存在相关索引。表名:tableA, \
字段名:LID, 在索引中的位置:1
2017-03-14 12:30:51 1923 [Note] show index from tableA where Column_name ='LID' \
and Seq_in_index =1
2017-03-14 12:30:51 1923 [Note] 第15步：开始验证 字段GT是不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] show index from tableA where Key_name = 'PRIMARY' \
and Column_name ='GT' and Seq_in_index = 1
2017-03-14 12:30:51 1923 [Note] 第16步：字段GT不是主键。表名:tableA
2017-03-14 12:30:51 1923 [Note] 第17步：开始验证表中是否已存在相关索引。\
表名:tableA, 字段名:GT, 在索引中的位置:2
2017-03-14 12:30:51 1923 [Note] show index from tableA where \
Column_name ='GT' and Seq_in_index =2
2017-03-14 12:30:52 1923 [Note] 第18步：开始输出表tableA索引优化建议:
2017-03-14 12:30:52 1923 [Note] Create_Index_SQL：alter table tableA add index \
idx_LID_GT(LID,GT)
2017-03-14 12:30:52 1923 [Note] 第19步: SQLAdvisor结束!
```

以上 10 个语句，平均 1s 就可以得到优化结果。

其中 alter table tableA add index idx_LID_GT(LID,GT) 就是优化建议，跟人工优化得到的结果一致。

另外，tableB 得到优化建议 alter table gamedaylottery20170210_gift_log add index idx_LID_CL(LID,CL)，也是比较理想的索引建议。

## 0x04 结论
***

美团点评开源 SQL 优化工具 SQLAdvisor 得到的优化建议比较满意，建议在线上试用一段时间。这个工具的成本在于需要在线上 DB 安装相关依赖，如果确认采用，可以考虑在初始化 DB 服务器时部署此工具。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x05 参考
***

* [^1] 美团点评 DBA 团队 (2017-03-09). 美团点评 SQL 优化工具 SQLAdvisor 开源. Retrieved from [http://tech.meituan.com/sqladvisor_pr.html](http://tech.meituan.com/sqladvisor_pr.html).
* [^2] longxuegang (2017-03-10). SQLAdvisor 安装. Retrieved from [https://github.com/Meituan-Dianping/SQLAdvisor/blob/master/doc/QUICK_START.md](https://github.com/Meituan-Dianping/SQLAdvisor/blob/master/doc/QUICK_START.md).
