---
published: true
author: Robin Wen
layout: post
title: "缺乏导入数据权限，SQL Server创建测试数据"
category: 数据库
summary: "只具有生产库的登录、查询、创建临时表权限，缺失导入数据（比如Excel文件、txt文档、sql脚本等等）权限，需要创建临时表，插入测试数据。"
tags:
- 数据库
- Database
- MSSQL
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 环境 ##

SQL Server 2012 + CentOS 6.3

## 问题描述 ##

只具有生产库的登录、查询、创建临时表权限，缺失导入数据（比如Excel文件、txt文档、sql脚本等等）权限，需要创建临时表，插入测试数据。

## 问题模拟 ##

由于生产库的数据是敏感数据，并且数据量非常大，当然不能提供出来。这里只是对这个问题进行一个模拟。数据量少和数据量大操作方法是一样的。

## 问题解决 ##

我们可以这样：

这是Excel中的源数据，如图1:
![图1 Excel 源数据](https://cdn.dbarobin.com/vSbj6Ac.jpg)
图1 Excel 源数据

**Step 1**，首先把源数据（Excel中的数据）拷贝出来，或者另存为csv文件（以逗号作为分隔），然后重命名后缀为txt。这里的文件名假设为source.txt，然后把行首标题去掉；

``` bash
6789,Robin,朱二,成都
1234,justdb,张三,泸州
4567,HelloWorld,李四,广州
5678,CSDN Blog,王五,中山
1331,Wen,邓六,深圳
3142,Wentasy,徐七,长沙
4131,Fantasy,燕八,昆明
```

**Step 2**，源数据准备好了，那现在我们切换到Linux环境下开始对数据进行处理。观察源数据中有四列数据，那么我们需要分隔数据。这里采用awk处理。代码如下：

``` bash
# -F表示以逗号作为分隔，把源数据中的每列分别保存为新的四个文件
awk -F","'{print $1}' source.txt > source1.txt
awk -F","'{print $2}' source.txt > source2.txt
awk -F","'{print $3}' source.txt > source3.txt
awk -F","'{print $4}' source.txt > source4.txt
```

源数据如下：

``` bash
cat source.txt
1234,justdb,张三,泸州
4567,HelloWorld,李四,广州
5678,CSDN Blog,王五,中山
1331,Wen,邓六,深圳
3142,Wentasy,徐七,长沙
4131,Fantasy,燕八,昆明
```

操作结果：

``` bash
cat source1.txt
1234
4567
5678
1331
3142
4131
```

效果如图2：
![图2 Step 2 效果图](https://cdn.dbarobin.com/1VKz8ft.png)
图2 Step 2 效果图

**Step 3**，考虑到这些数据都是基于文本存储的，那么INSERT插入时需要在值的首尾加上单引号或者双引号。代码如下：

``` bash
# ^表示行首，此行代码表示在每行的行首加上yy，注意此处添加的内容不要和正文文本相同；
sed 's/^/yy/g'source1.txt –i
# $表示行尾，此行代码表示在每行的行尾加上zz，同理，意此处添加的内容不要和正文文本相同
sed 's/$/zz/g'source1.txt –i
# 把行首的yy替换成单引号
sed"s/yy/\'/g" source1.txt –i
# 把行尾的zz替换成单引号
sed"s/zz/\'/g" source1.txt –i

# 说明：读者也可以把行尾和行首替换为相同的内容
# 那把替换后的内容再替换为单引号就只需要执行一行代码即可。
# 这里只演示一个文本，其余文本操作方法相同。
```

操作结果如下：

``` bash
cat source1.txt
yy1234zz
yy4567zz
yy5678zz
yy1331zz
yy3142zz
yy4131zz

cat source1.txt
'1234'
'4567'
'5678'
'1331'
'3142'
'4131'
```

效果如图3：
![图 3 Step 3效果图](https://cdn.dbarobin.com/3PP3uNb.png)
图 3 Step 3效果图

**Step 4**，我们得到每列带单引号的文本，但是我们需要把这四个文件的每列放到一个文件中，就像炒青椒肉丝，把切好的瘦肉丝、佐料、青椒放到锅里炒一样。我们可以采用如下方法合并文件，使用paste命令，命令如下：

``` bash
# 此命令表示以逗号作为分隔，合并经过上述处理的四个文件，并保存到结果文件
paste -d ","source1.txt source2.txt source3.txt source4.txt > result.txt
```

操作结果如下：

``` bash
cat result.txt
'1234','justdb','张三','泸州'
'4567','HelloWorld','李四','广州'
'5678','CSDN Blog','王五','中山'
'1331','Wen','邓六','深圳'
'3142','Wentasy','徐七','长沙'
'4131','Fantasy','燕八','昆明'
```

效果如图4：
![图4 Step 4效果图](https://cdn.dbarobin.com/qlYzL3U.png)
图4 Step 4效果图

**Step 5**，将得到的结果进行最后的处理。我们在行尾加入INSERT语句，这里假设后面创建的临时表名称为##temp，在行尾加上括号和分号，语句如下：

``` bash
sed 's/^/INSERT INTO ##tempVALUES(/g' result.txt -i
sed 's/$/);/g'result.txt -i
```

操作结果如下：

``` bash
cat result.txt
INSERT INTO ##temp VALUES('1234','justdb','张三','泸州');
INSERT INTO ##temp VALUES('4567','HelloWorld','李四','广州');
INSERT INTO ##temp VALUES('5678','CSDN Blog','王五','中山');
INSERT INTO ##temp VALUES('1331','Wen','邓六','深圳');
INSERT INTO ##temp VALUES('3142','Wentasy','徐七','长沙');
INSERT INTO ##temp VALUES('4131','Fantasy','燕八','昆明');
```

效果如图5：
![图5 Step 5效果图](https://cdn.dbarobin.com/riTuoDD.png)
图5 Step 5效果图

**Step 6**，创建临时表，语句如下：

``` bash
CREATE TABLE ##temp
(
       ID CHAR(16) NOT NULL,
       EName VARCHAR(20),
       CName VARCHAR(40),
       City VARCHAR(20)
);
```

**Step 7**，打开SQLServer的查询分析器，然后执行创建临时表的语句和插入数据的语句。

执行结果如图6：
![图6 插入数据效果](https://cdn.dbarobin.com/fXIYBUq.png)
图6 插入数据效果

**其他说明**

1.如果文件中每行的末尾出现空格，我们可以使用此命令把空格去掉：
sed 's/\ \+$//'source1.txt –i
2.如果文件中出现^M，我们可以使用此命令将^M去掉：
sed 's/^M//g'source_4.txt –i
3.本文只是简单的模拟，数据量小不能体现这种方法的优越性，如果数据量大，那给你带来的是质的飞跃；
4.本文中Step3可以简化，直接在每列的行首和行尾加入INSERT和括号，但是这样只是行首和行尾OK了，每个字符串还是没有用单引号括起来，可以把每行作为一个单元，然后加入单引号，而不是本文的将每个列分隔出来；
5.本文还想告诉读者的是多使用Linux吧，并且学会一门脚本语言，这会让你的工作事半功倍；
6.本文是基于没有导入数据的权限的情况下做的，如果有该权限，自然很简单，如果没有，那本文还是很有参考价值。其实本文提供的就是一种思路，如何把问题拆分、如何巧妙的拼接文本。使用到的核心技术就是Linux的Shell，比如awk、sed的用法。

**最终的一键脚本**

``` bash
#!/bin/bash
#FileName:auto_import_data.sh
#Desc:Auto Import DataTo MS SQL
#Date:2014-3-14 17:53:12
#Author:Robin

#1.分离数据
awk -F","'{print $1}' source.txt > source1.txt
awk -F","'{print $2}' source.txt > source2.txt
awk -F","'{print $3}' source.txt > source3.txt
awk -F","'{print $4}' source.txt > source4.txt

#2.在行首和行尾添加单引号
sed 's/\ \+$//'source1.txt -i
sed 's/^/yy/g'source1.txt -i
sed 's/$/zz/g'source1.txt -i
sed"s/yy/\'/g" source1.txt -i
sed"s/zz/\'/g" source1.txt -i

sed 's/^/yy/g'source2.txt -i
sed 's/$/zz/g'source2.txt -i
sed "s/yy/\'/g"source2.txt -i
sed"s/zz/\'/g" source2.txt -i

sed 's/^/yy/g'source3.txt -i
sed 's/$/zz/g'source3.txt -i
sed"s/yy/\'/g" source3.txt -i
sed"s/zz/\'/g" source3.txt -i

sed 's/^/yy/g'source4.txt -i
sed 's/$/zz/g'source4.txt -i
sed "s/yy/\'/g"source4.txt -i
sed"s/zz/\'/g" source4.txt -i

#3.合并文件
paste -d ","source1.txt source2.txt source3.txt source4.txt > result.txt

#4.拼接为最终的插入语句
sed 's/^/INSERT INTO ##tempVALUES(/g' result.txt -i
sed 's/$/);/g'result.txt -i
```

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：<a href="http://blog.csdn.net/justdb/article/details/21289621" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="缺乏导入数据权限，SQL Server创建测试数据" height="16px" width="16px" border="0" alt="缺乏导入数据权限，SQL Server创建测试数据" /></a>

题图来自：原创，By <a href="https://dbarobin.com/" target="_blank">Robin Wen</a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
