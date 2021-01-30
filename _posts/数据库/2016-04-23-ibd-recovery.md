---
published: true
author: Robin Wen
layout: post
title: "MySQL 数据恢复案例"
category: 数据库
summary: "某项目研发 A 删除压测环境大表，等待时间较长，于是直接将 MySQL 数据目录中对应数据库文件删除。于此同时，误删 ibdata 和 MySQL 配置文件。此时 MySQL 已经崩溃，研发从其他机器拷贝误删的数据文件以及配置文件，重启 MySQL，出现 Unknown/unsupported storage engine: InnoDB 错误，于是有了接下来的数据恢复。本文会从几个方面讲解这个案例，方案确定、方案实施、原理探讨和案例小结，期间会交代诸多细节，以及使用到的技巧。相信读者读完之后，会对以后的数据恢复有所启发。接下来，做如下总结：第一，备份重于一切。第二，遇到问题，恐惧问题比问题本身可怕。第三，解决问题的同时，做好素材收集很重要。第四，官方文档是一手好资料，应该好好利用。"
tags:
- 数据库
- Database
- 安全
- Security
- MySQL
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x00 文章目录
***

* Table of Contents
{:toc}

## 0x01 背景介绍
***

某项目研发 A 删除压测环境大表，等待时间较长，于是直接将 MySQL 数据目录中对应数据库文件删除。于此同时，误删 ibdata 和 MySQL 配置文件。此时 MySQL 已经崩溃，研发从其他机器拷贝误删的数据文件以及配置文件，重启 MySQL，出现 `Unknown/unsupported storage engine: InnoDB` 错误，于是有了接下来的数据恢复。

本文会从几个方面讲解这个案例，方案确定、方案实施、原理探讨和案例小结，期间会交代诸多细节，以及使用到的技巧。相信读者读完之后，会对以后的数据恢复有所启发。

## 0x02 方案确定
***

从背景介绍所知，研发遇到的问题是 MySQL 不支持 InnoDB 存储引擎，MySQL 错误日志详细信息如下：

``` bash
[ERROR] Plugin 'InnoDB' init function returned error.
[ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
[ERROR] Unknown/unsupported storage engine: InnoDB
[ERROR] Aborting
```

遇到此类问题，我们通常的做法是将 ib_logfile0 和 ib_logfile1 删除，然后重启 MySQL。

首先交代下，MySQL 实例大版本是 5.5，使用独立表空间。此次案例，ibdata 已经不存在，这样会导致数据表不能正常加载。MySQL 5.5 版本，不管使用独立表空间还是共享表空间，ibdata（系统表空间）都会存储 InnoDB 数据表的元数据信息，也就是数据字典，还会存储 undo log、change buffer 和 doublewrite buffer。区别在于，当启用 `innodb_file_per_table`，也就是使用了独立表空间，数据和索引会存储在独立的 ibd 文件中；如果禁用 `innodb_file_per_table`，也就是使用了共享表空间，数据和索引会存储在 ibdata 中。

那么问题来了，接下来怎么做数据恢复。

可以这样理解，这些存在的 ibd 文件，都是孤立的。也就是说，在没有备份的前提下，怎么从这些孤立的文件中恢复数据。

我们可以按照如下步骤进行恢复：

1. 获得整个库所有表的表结构；
2. 新建 MySQL 实例，导入表结构；
3. 使用 `ALTER TABLE dbName.tableName DISCARD TABLESPACE` 删除新建的 ibd 文件；
4. 拷贝对应库对应表的 ibd 文件到对应目录，并更改权限；
5. 使用 `ALTER TABLE dbName.tableName IMPORT TABLESPACE` 导入拷贝的 ibd 文件。

导入拷贝的 ibd 文件，会遇到如下错误：

``` bash
ERROR 1030 (HY000): Got error -1 from storage engine
```

对应 MySQL 错误日志如下：

``` bash
160419 16:06:08  InnoDB: Error: tablespace id and flags in file './dbName/tableName.ibd' \
are 243 and 0, but in the InnoDB
InnoDB: data dictionary they are 247 and 0.
InnoDB: Have you moved InnoDB .ibd files around without using the
InnoDB: commands DISCARD TABLESPACE and IMPORT TABLESPACE?
InnoDB: Please refer to
InnoDB: http://dev.mysql.com/doc/refman/5.5/en/innodb-troubleshooting-datadict.html
InnoDB: for how to resolve the issue.
160419 16:06:08  InnoDB: cannot find or open in the database directory the .ibd file of
InnoDB: table `dbName`.`tableName`
InnoDB: in ALTER TABLE ... IMPORT TABLESPACE
```

从日志中我们可以知道，`dbName`.`tableName` 旧的表空间 id 为 243，而数据字典中新的表空间 id 为 247。也就是说，旧的 ibd 文件和新的 ibd 文件，表空间 id 不一致，导致 InnoDB 存储引擎不能正常加载数据表。

接下来，恢复步骤调整如下：

1. 获得整个库所有表的表结构；
2. 新建 MySQL 实例，导入表结构；
3. 使用 `ALTER TABLE dbName.tableName DISCARD TABLESPACE` 删除新建的 ibd 文件；
4. 拷贝对应库对应表的 ibd 文件到对应目录，并更改权限；
5. 使用 `ALTER TABLE dbName.tableName IMPORT TABLESPACE` 导入拷贝的 ibd 文件；
6. 分析 MySQL 错误日志，获取所有表的新旧表空间 id；
7. 结合 xxd 和 sed 替换 ibd 文件中的表空间 id；
8. 使用 `ALTER TABLE dbName.tableName IMPORT TABLESPACE` 再次导入替换过表空间 id 的 ibd 文件；
9. 修改配置文件，将 `innodb_force_recovery` 设置为 6，并重启 MySQL；
10. 使用 mysqldump 备份数据；
11. 再次新建实例，导入逻辑备份文件。

## 0x03 方案实施
***

方案确定好之后，接下来讲解实施过程，以及期间使用的一些技巧。

### 3.1 获取表结构并导入

> 注：此小节对应恢复步骤的 1 和 2。

压测环境没有备份，但是另一套测试环境的表结构与压测环境一致，只是数据有所差异，所以，获取表结构比较容易。

导入表结构没有什么好说明的地方，注意导入 SQL 的权限和字符集。

### 3.2 重建表空间

> 注：此小节对应恢复步骤的 3~5。

由于是整库恢复，数据库和表较多，所以使用脚本处理，具体可以参考脚本：「[auto_recovery_data_prefix.sh](https://github.com/dbarobin/ibd-recovery/blob/master/auto_recovery_data_prefix.sh)」。

大概的处理流程是，两层循环，外层循环数据库列表，内层循环对应数据库表列表。然后依次 DISCARD TABLESPACE、拷贝对应库对应表的 ibd 文件到对应目录并更改权限、IMPORT TABLESPACE。

之前分析过，由于新旧的 ibd 文件表空间 id 不一致，导致不能正确导入。在 MySQL 错误日志中记录了表名、新旧表空间 id，接下来我们看看怎么分解。

### 3.3 分析 MySQL 错误日志

> 注：此小节对应恢复步骤的 6 和 7。

这一步很有意思，也是重点讲解的一个小节，具体可以参考脚本：「[auto_update_table_id_via_xxd.sh](https://github.com/dbarobin/ibd-recovery/blob/master/auto_update_table_id_via_xxd.sh)」。

所有的数据库表累计 500+，不可能使用人工处理，我们得想点取巧的办法。

我们发现 MySQL 错误日志记录的表名、新旧表空间 id 很有规律，我们只需要依次取出这些值，问题就解决一大半了。

笔者的思路是这样的，错误日志记录了表名、新旧表空间 id，这不是典型的行列模型吗？于是想到了使用 MySQL 表进行记录。当我们循环数据库列表和数据库表列表的时候，通过数据库名和数据库表名进行筛选，获得新旧的表空间 id，然后再使用 xxd 和 sed 进行替换。

首先，我们需要创建一张表，结构如下：

``` sql
CREATE TABLE `robin.config` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `dbName` varchar(100) DEFAULT NULL,
  `tableName` varchar(100) DEFAULT NULL,
  `old` int(11) DEFAULT NULL,
  `new` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

这张表的字段有 id、dbName、tableName、old、new，分别表示自增 ID（无实际含义）、数据库名、表名、旧表空间 id、新表空间 id。

然后，我们分析 MySQL 错误日志，将数据库名、表名、旧表空间 id、新表空间 id 依次导出。这是笔者使用的方法：

``` bash
grep "InnoDB: Error: tablespace id and flags in file" mysql_error.log -A 1 | \
sed 's#^.*flags in file \x27./\(.*\)\x27 are \(\w\+\).*$#\1 \2#;s/^.*they are \(\w\+\) and .*$/\1/g' | \
sed "s/\//,/g" | \
sed "s/.ibd//g" | \
sed "s/\ /,/g" | \
sed "s/--//g" | \
sed "/^$/d" \
> ${TABLE_DIR}/ibd.txt
```

这里不做过多讲解，读者自行品味 sed 的美妙吧。

我们稍微调整下格式，将会得到一个以逗号分隔列，`\n` 分隔行的文本。

接着，我们使用 LOAD DATA 导入我们创建的表，使用命令如下：

``` bash
LOAD DATA INFILE '${TABLE_DIR}/ibd.txt' INTO TABLE config \
FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n' \
(@col1,@col2,@col3,@col4) \
set dbName=@col1,tableName=@col2,old=@col3,new=@col4;
```

再者，我们使用 vim -b 其中一个较小的 ibd 文件，由于 ibd 文件是二进制文件，我们需要使用 :%! xxd 转换为十六进制文件。我们可以看到，在以 0000020 开头的第三行，第三列和第五列就是表空间 id，我们的目标就是使用新的表空间 id 替换它。

但是问题来了，MySQL 错误日志记录的旧表空间 id、新表空间 id 是十进制，我们需要转换为十六进制。怎样转换呢？接下来告诉你。

我们可以使用 MySQL 的 HEX 函数进行转换。由于我们需要四位的十六进制数，但是十进制数转换为十六进制之后，有可能是两位，也有可能是三位，所以我们需要使用 CASE WHEN 格式化。具体做法是，在转换之后的十六进制数前补 0，两位的十六进制数补两个 0，三位的十六进制数补一个 0。另外，ibd 文件中以小写的十六进制数存储，所以我们需要使用 LOWER 函数转换为小写。

获得旧表空间 id SQL 如下：

``` sql
SELECT CASE LENGTH(LOWER(HEX(old)))
           WHEN 2 THEN LOWER(CONCAT('00',HEX(old)))
           WHEN 3 THEN LOWER(CONCAT('0',HEX(old)))
           ELSE NULL
       END AS 'old'
FROM robin.config
WHERE dbName='$db'
  AND tableName='$table';
```

获得新表空间 ID SQL 如下：

``` sql
SELECT CASE LENGTH(LOWER(HEX(new)))
           WHEN 2 THEN LOWER(CONCAT('00',HEX(new)))
           WHEN 3 THEN LOWER(CONCAT('0',HEX(new)))
           ELSE NULL
       END AS 'new'
FROM robin.config
WHERE dbName='$db'
  AND tableName='$table';
```

接下来，我们就可以使用 xxd 和 sed 愉快地替换 ibd 文件了，命令如下：

``` bash
xxd ${DATA_DIR}/$db/$table.ibd | \
sed "/^0000020/s/$old/$new/g" | \
xxd -r \
> ${DATA_DIR}/$db/${table}_new.ibd
```

### 3.4 导入替换过的 ibd 文件

> 注：此小节对应恢复步骤的 8。

这一步没啥好讲解的，因为在 3.1 节我们就讨论过了。具体可以参考脚本：「[auto_recovery_data_postfix.sh](https://github.com/dbarobin/ibd-recovery/blob/master/auto_recovery_data_postfix.sh)」。

### 3.5 修改 innodb_force_recovery 并重启

> 注：此小节对应恢复步骤的 9。

导入完成之后，接下来，我们修改配置文件，将 `innodb_force_recovery` 修改为 6，然后重启。为什么修改为 6，在原理探讨小节会阐述。

### 3.6 备份数据

> 注：此小节对应恢复步骤的 10。

接下来，我们登录 MySQL，查询某张表的数据，惊奇地发现有结果了，是不是有一种无比开心的感觉，好玩吧。具体可以参考脚本：「[auto_backup_data.sh](https://github.com/dbarobin/ibd-recovery/blob/master/auto_backup_data.sh)」。

### 3.7 导入备份

> 注：此小节对应恢复步骤的 11。

导入备份也没啥好讲的，具体可以参考脚本：「[auto_import_data.sh](https://github.com/dbarobin/ibd-recovery/blob/master/auto_import_data.sh)」。

## 0x04 原理探讨
***

在原理探讨这一小节，做两点探讨，第一个是关于恢复方案，第二个是关于 `innodb_force_recovery` 参数。

恢复方案中，我们使用到了 DISCARD TABLESPACE、IMPORT TABLESPACE 和修改表空间 id。我们先说下 InnoDB 数据页的组成。InnoDB 数据页由 7 个部分组成，分别是 File Header、Page Header、Infimum 和 Supermum Records、User Records、Free Space 和 Page Directory。

接下来看看 ibdata 文件的组织结构，如下图：

![ibdata1_File_Overview](https://cdn.dbarobin.com/7ne7eCG.png)
From [blog.jcole.us](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/), by Jeremy Cole.

然后看看 ibd 文件的组织结构，如下图：

![IBD_File_Overview](https://cdn.dbarobin.com/lToZcaf.png)
From [blog.jcole.us](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/), by Jeremy Cole.

我们要修改的表空间 id，位于 FSP_HEADER。不同的 ibd 文件，表空间 id 是不同的。ibdata 文件中有一个数据字典 data dictionary，记录的是实例中每个表在 ibdata 中的一个逻辑位置，而在 ibd 文件中也存储着同样的一个 tablespace id，两者必须一致，InnoDB 引擎才能正常加载到数据。所以，我们需要修改旧的表空间 id 为新的。

实际上，我们对于 ibdata 文件中的 undo、change buffer、double write buffer 数据可以不用关心。我们只需要利用一个全新的实例，以及一个干净的 ibdata 文件，通过卸载和加载表空间把 ibd 文件与 ibdata 文件关联。笔者使用了这么多脚本，目的就是如此。

接下来，我们谈谈 `innodb_force_recovery` 参数。这个参数是恢复过程中常用的，目的是跳过特定的步骤，让 MySQL 正常启动。它有以下几个级别：

1. (SRV_FORCE_IGNORE_CORRUPT): 忽略检查到的 corrupt 页。
2. (SRV_FORCE_NO_BACKGROUND): 阻止主线程和 puge 线程的运行。
3. (SRV_FORCE_NO_TRX_UNDO): 不执行事务回滚操作。
4. (SRV_FORCE_NO_IBUF_MERGE): 不执行插入缓冲的合并操作。
5. (SRV_FORCE_NO_UNDO_LOG_SCAN): 不查看重做日志。
6. (SRV_FORCE_NO_LOG_REDO): 不执行前滚的操作。

我们设置为 6 之后，就可以使用 mysqldump 导出数据。

**读者福利：**所有的脚本，笔者已经上传到 GitHub，仅供参考。获取链接如下：[https://github.com/dbarobin/ibd-recovery](https://github.com/dbarobin/ibd-recovery)

## 0x05 案例小结
***

此次案例，精华的地方有两点，一是对于获取新旧表空间 id 所使用的技巧，二是对于原理的探讨。之所以花这么多技巧取获取表空间 id，那是因为此次恢复是全库级别，数据容量接近 100G，不允许手工处理。可以这样说，这次恢复，可以做为工程对待。

接下来，做如下总结：

**第一，备份重于一切。**这句话，想必读者非常熟悉。这是前辈 **eygle** 经常提及的一句话。好在此次案例是在压测环境下做的，数据的重要性赶不上线上环境。但这给广大 DBA 提了个醒，制定完善的备份方案，以及做好完善的实施，非常重要。

**第二，遇到问题，恐惧问题比问题本身可怕。**即使问题之前没有遇到过，不要紧，只要你探索、分析问题的思路对了，不愁解决不了。当你的知识积累够多，解决问题就是一个融会贯通的过程，就会有很多 Aha Moment。

第三，解决问题的同时，做好素材收集很重要。如果没有完善的素材收集，读者也就很难看到这篇文章了。

第四，官方文档是一手好资料，应该好好利用。另外，强烈推荐看看姜老师写的这本书：[MySQL 技术内幕 InnoDB 存储引擎](https://book.douban.com/subject/24708143/)。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x06 相关资料
***

* [14.8.4 InnoDB File-Per-Table Tablespaces](https://dev.mysql.com/doc/refman/5.5/en/innodb-multiple-tablespaces.html)
* [Recovering an InnoDB table from only an .ibd file.](http://www.chriscalender.com/recovering-an-innodb-table-from-only-an-ibd-file/)
* [14.21.2 Forcing InnoDB Recovery](http://dev.mysql.com/doc/refman/5.5/en/forcing-innodb-recovery.html)
* [The basics of InnoDB space file layout](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/)

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
