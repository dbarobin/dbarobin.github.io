---
published: true
author: Robin Wen
layout: post
title: EOS 区块链数据实时异构到 MySQL
category: 区块链
summary: >-
  早在 6 月 7 日，kesar 提交了一个编号为 #3882 的 PR，但直至目前仍处于未合并状态。团队做一些开发需要取 EOS 链上数据，但无法直接从
  EOS 区块链中获取。#3882 提供 EOS 链上数据导入 MySQL，可以很方便的读取历史数据。但问题来了，该 PR 未合并，EOSIO 也没有一个
  sql_db_plugin 相关的 branch 或 tag，所以无法使用 git 自动合并。目前 #3882 暂未合并，具体原因未从得知。EOS
  链上数据和传统的关系型数据库结合，将会大大降低开发者成本。待 #3882 merge 之后，读者将会更便利的使用到这个功能。
tags:
  - 区块链
  - Blockchain
  - EOS
  - MySQL
  - 数据库
comments:
  - author:
      type: github
      displayName: keithyau
      url: 'https://github.com/keithyau'
      picture: 'https://avatars0.githubusercontent.com/u/659311?v=4&s=73'
    content: |-
      https://github.com/superoneio/eos_sql_db_plugin

      &#x597D;&#x50CF;&#x6C92;&#x4E86;
    date: 2018-11-15T08:31:38.890Z

---

`文/Robin`

这是「区块链技术指北」的第 33 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 0x00 背景
***

早在 6 月 7 日，[kesar](https://github.com/kesar) 在 [EOSIO](https://github.com/EOSIO/eos) 提交了一个编号为 [#3882](https://github.com/EOSIO/eos/pull/3882) 的 PR，但直至目前仍处于未合并状态。团队做一些开发需要取 EOS 链上数据，但无法直接从 EOS 区块链中获取。#3882 提供 EOS 链上数据导入 MySQL，可以很方便的读取历史数据。但问题来了，该 PR 未合并，EOSIO 也没有一个 sql_db_plugin 相关的 branch 或 tag，所以无法使用 git 自动合并。

## 0x01 解决
***

SuperONE 团队将 #3882 提交的内容打包，提交了个一个 repo，链接如下：

> https://github.com/superoneio/eos_sql_db_plugin

接下来本文将讲解如何将 EOS 链上数据异构到 MySQL。

## 0x02 部署
***

部署的思路如下：

* 安装 MySQL 以及 client 库
* 安装 soci
* 修改 eos CMakeLists.txt
* 编译支持 sql_db_plugin 的 nodeos
* 创建数据库，分配用户名
* 导入 eos 表结构
* 启动 nodeos，同步主网数据
* 查看 MySQL，确认数据

接下来我们逐步讲解。

如果使用 Ubuntu，安装 MySQL 可以快速使用 apt-get 安装，笔者建议使用源码编译。

如果使用源码编译，mysql-client 相关库已经存在。如果使用 apt-get 或者 yum 安装 mysql-sever，需要执行如下命令安装 mysql-client，命令如下：

``` bash
$ apt-get -y install mysql-client
```

接下来我们安装 soci。soci 是 C++ 连接 MySQL 的 Library。Ubuntu 可以快速安装，命令如下：

``` bash
$ apt-get -y install libsoci-dev
```

当然，我们还可以源码编译，步骤如下：[1]

``` bash
$ git clone https://github.com/SOCI/soci.git
$ cd soci && mkdir build && cd build
$ cmake -DWITH_BOOST=ON -WITH_MYSQL=ON -MYSQL_INCLUDE_DIR=/usr/local/mysql/include -MYSQL_LIBRARIES=/usr/local/mysql/lib/libmysqlclient.a /data/tmp/robin/soci
$ make
$ make install
```

当然 `-MYSQL_INCLUDE_DIR` 和 `-MYSQL_LIBRARIES` 根据实际路径更改。[2]

接下来我们需要 clone eos 代码，命令如下：

``` bash
$ cd /data
$ git clone https://github.com/EOSIO/eos --recursive
$ cd /data/eos
# 如果不想用 master 分支代码，可以使用 git checkout v1.0.8 使用其他分支
$ git submodule update --init --recursive
```

然后更改 CMakeLists.txt，总共有两个 CMakeLists.txt 需要更改。

* eos/plugins/CMakeLists.txt
* eos/programs/nodeos/CMakeLists.txt

在 eos/plugins/CMakeLists.txt 添加如下行：

> add_subdirectory(sql_db_plugin)

在  eos/programs/nodeos/CMakeLists.txt 添加如下内容：

``` c++
// 如果以下内容已经取消注释，可以忽略。
if(TARGET sql_db_plugin)
    target_link_libraries( nodeos PRIVATE -Wl,${whole_archive_flag} sql_db_plugin -Wl,${no_whole_archive_flag} )
   endif()
```

接着将 SuperONE 团队合并的 `sql_db_plugin` 合并到 EOS 代码中。

``` bash
$ cd /data
$ git clone https://github.com/superoneio/eos_sql_db_plugin
\cp -rv /data/eos_sql_db_plugin/sql_db_plugin/* /data/eos/plugins/sql_db_plugin
```

接下来编译支持 `sql_db_plugin` 的 nodeos。

``` bash
$ cd /data/eos
$ ./eosio_build.sh
```

待 nodeos 编译完成之后，我们执行如下命令确认是否编译成功。

``` bash
$ /data/eos/build/programs/nodeos/nodeos --help | grep "sql_db-uri"
```

如果输出如下内容，证明编译成功。

> --sql_db-uri arg                      Sql DB URI connection string If not

接下来在 MySQL 里创建数据库以及分配账号，SQL 如下：

``` sql
CREATE DATABASE eos DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
GRANT ALL PRIVILEGES ON eos.* TO app_eos@'%' IDENTIFIED BY 'YOURPASS';
GRANT ALL PRIVILEGES ON eos.* TO app_eos@'127.0.0.1' IDENTIFIED BY 'YOURPASS';
```

然后我们导入 EOS 数据库表结构，如不想手动导入，可以在启动 nodeos 的时候添加 `sql_db-block-start=0` 参数，该参数为 0 时会在数据库中创建表。表结构可以在点击 [此处](https://raw.githubusercontent.com/superoneio/eos_sql_db_plugin/master/eos.sql) 下载。

接着我们启动 nodeos，主网 fullnode 相关的配置文件可以访问如下链接下载：

> https://github.com/superoneio/eos-mainnet

启动 fullnode 相关命令如下：

``` bash
$ mkdir /data/mainnet
$ cd /data/mainnet
$ git clone https://github.com/superoneio/eos-mainnet
$ mkdir -p data logs config
$ cp eos-mainnet/config.ini mainnet/config
$ cp eos-mainnet/genesis.json mainnet
$ cp eos-mainnet/*.sh mainnet
$ chmod +x mainnet
```

修改 config.ini 相关配置，添加 `plugin = eosio::sql_db_plugin` 参数。

修改 start.sh 脚本，sql_db-uri 格式如下：

> sql_db-uri="mysql://db=YOURDB user=YOURUSER host=127.0.0.1 password='YOURPASS'

sql_db-uri 参数中的 YOURUSER 表示连接 EOS 区块链数据库的用户名，YOURPASS 表示连接 EOS 区块链数据库的用户密码，YOURDB 表示 EOS 区块链数据库名字。

需要注意的是 sql_db-uri 暂时不能写入 config.ini 配置文件，否则将会遇到 `Failed to find shared library for backend "mysql` 错误。另外，笔者的操作系统是 Ubuntu 16.04.4 LTS。

最后执行 start.sh，登录 MySQL 数据库就可以查看到相关数据。

``` sql
[root@localhost][(none)]> USE eos
Database changed
[root@localhost][eos]> SHOW TABLES;
+------------------+
| Tables_in_eos    |
+------------------+
| accounts         |
| accounts_keys    |
| actions          |
| actions_accounts |
| blocks           |
| stakes           |
| tokens           |
| transactions     |
| votes            |
+------------------+
9 rows in set (0.02 sec)
[root@localhost][eos]> SELECT * FROM tokens LIMIT 1 \G
*************************** 1. row ***************************
account: eosio
 symbol: EOS
 amount: 995314228.4771
1 row in set (0.00 sec)
```

## 0x03 小结
***

目前 #3882 暂未合并，具体原因未从得知。EOS 链上数据和传统的关系型数据库结合，将会大大降低开发者成本。待 #3882 merge 之后，读者将会更便利的使用到这个功能。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x04 参考
***

* [1] SOCI (2018-07-06). SOCI Installation. Retrieved from [http://soci.sourceforge.net/doc/master/installation](http://soci.sourceforge.net/doc/master/installation).
* [2] 太上绝情 (2016-07-14). soci-mysq 编译. Retrieved from [https://blog.csdn.net/bjrxyz/article/details/51912676](https://blog.csdn.net/bjrxyz/article/details/51912676).

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。BTW，**李笑来老师也加入了**。

![区块链技术指北](https://cdn.dbarobin.com/RBmpxTL.jpg)

「区块链技术指北」相关资讯渠道：

* 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
* 官网，[https://chainon.io](https://chainon.io)
* 官方博客，[https://blog.chainon.io](https://blog.chainon.io)
* 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
* Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
* Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
* Facebook，[https://www.facebook.com/chainone.org](https://www.facebook.com/chainone.org)
* 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

* 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
* 个人博客，[https://dbarobin.com](https://dbarobin.com)
* 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
* 简书，[https://www.jianshu.com/c/a37698a12ba9](https://www.jianshu.com/c/a37698a12ba9)
* Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
* Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
* 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
* EOS LIVE，[https://eos.live/user/robin](https://eos.live/user/robin)
* 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

* BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
* ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
* PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
* 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
