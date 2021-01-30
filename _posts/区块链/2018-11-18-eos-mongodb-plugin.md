---
published: true
author: Robin Wen
layout: post
title: EOS 区块链数据实时异构到 MongoDB
category: 区块链
summary: >-
  之前介绍了将 EOS 主网数据同步到 MySQL，本文讲解了另一种同步到文档型数据库的方法。MySQL 和 MongoDB 最大的区别在于，MySQL
  是传统的关系型数据库，支持 SQL 标准，而 MongoDB 是文档型数据库。MySQL 天生适用于结构化数据以及需要使用事务的场景，天生支持 SQL
  标准，对开发者比较友好。
tags:
  - 区块链
  - Blockchain
  - EOS
comments:
  - author:
      type: github
      displayName: joepeak
      url: 'https://github.com/joepeak'
      picture: 'https://avatars3.githubusercontent.com/u/4530403?v=4&s=73'
    content: '&#x8FD9;&#x4E2A;&#x7F51;&#x7AD9;&#x662F;&#x9759;&#x6001;&#x7684;&#x5417;'
    date: 2018-11-25T12:51:35.881Z
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: '@joepeak &#x662F;&#x7684;&#xFF0C;&#x662F;&#x9759;&#x6001;&#x7684;&#x3002;'
    date: 2018-11-26T02:48:39.186Z

---

`文/Robin`

这是「区块链技术指北」的第 38 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

> 更新：从 EOSIO 1.1.0 开始，已经默认支持 MongoDB，所以本文提供的方法仅做参考。

## 0x00 背景
***

执行 `eosio_build.sh` 脚本编译 nodeos 会默认安装 mongodb，但是从 Dawn 4.0 开始，`mongo_db_plugin` 插件不再生效，详情请参考 [#3030](https://github.com/EOSIO/eos/issues/3030)。[#4304](https://github.com/EOSIO/eos/pull/4304) PR 重新支持 MongoDB，本文讲解如何将链上数据实时同步到 MongoDB。

![](https://cdn.dbarobin.com/O0w2yeu.jpg)

## 0x01 部署
***

部署的思路如下：

* 编译支持 mongo_db_plugin 的 nodeos
* 配置 MongoDB
* 启动 MongoDB
* 创建数据库
* 启动 nodeos，同步主网数据
* 查看 MongoDB，确认数据

接下来我们逐步讲解。

由于 EOSIO 代码库有 `gh#3030-enable-mongodb` 分支，所以可以使用 git 自动将支持 mongo_db_plugin 插件的代码合并，详细命令如下：

``` bash
$ mkdir -p /data/mongodb && cd /data/mongodb
$ git clone -b release/1.1 https://github.com/EOSIO/eos.git --recursive
$ cd eos
$ git fetch --all --tags --prune
$ git merge --m "merge" --commit origin/gh#3030-enable-mongodb
$ git submodule update --init --recursive
$ ./eosio_build.sh
```

如果 nodeos 编译成功，MongoDB 自动安装，路径在 **$USER/opt/mongodb**。执行 `/data/mongodb/eos/build/programs/nodeos/nodeos --help | grep mongo`，如果有如下的输出，表示 MongoDB 插件编译成功。

``` bash
Config Options for eosio::mongo_db_plugin:
  -q [ --mongodb-queue-size ] arg (=256)
  --mongodb-wipe                        Required with --replay-blockchain,
                                        --delete-all-blocks to wipe mongo
                                        accidental wipe of mongo db.
  --mongodb-block-start arg (=0)        If specified then only abi data pushed
                                        to mongodb until specified block is
  -m [ --mongodb-uri ] arg              MongoDB URI connection string, see:
                                        https://docs.mongodb.com/master/referen
                                        in URI. Example: mongodb://127.0.0.1:27
```

接下来我们配置 MongoDB。

``` bash
$ mkdir -p /data/mongodb/db /data/mongodb/logs
$ touch /data/mongodb/mongodb.conf
```

mongodb.conf 配置文件内容如下：

``` bash
systemLog:
  destination: file
  path: /data/mongodb/logs/mongo.log
  logAppend: true
storage:
  dbPath: /data/mongodb/db
  journal:
   enabled: true
net:
  bindIp: 127.0.0.1
  port: 27017
```

接着启动 MongoDB，并且创建 eos 数据库。

``` bash
$ /$USER/opt/mongodb/bin/mongod -f /data/mongodb/mongodb.conf --fork
$ /$USER/opt/mongodb/bin/mongo --port 27017
$ use eos
```

然后配置 EOS 主网 fullnode。

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

修改 config.ini 相关配置，添加如下参数：

``` bash
plugin = eosio::mongo_db_plugin
mongodb-uri = mongodb://127.0.0.1:27017/eos
```

最后执行 start.sh，同步主网数据。

## 0x02 测试
***

我们执行 `$ /$USER/opt/mongodb/bin/mongo --port 27017` 登录到 MongoDB，可以对同步的数据进行校验。

``` sql
> use eos
> show dbs
admin   0.000GB
config  0.000GB
eos     0.031GB
local   0.000GB
> use eos
switched to db eos
> show tables;
accounts
actions
block_states
blocks
transaction_traces
transactions
> db.accounts.find({name:{$eq:'eosio.ram'}})
{ "_id" : ObjectId("5b4163f2992ecd51b4277f77"), "name" : "eosio.ram", "createdAt" : ISODate("2018-07-08T01:08:02.071Z") }
```

## 0x03 小结
***

之前介绍了将 EOS 主网数据同步到 MySQL，本文讲解了另一种同步到文档型数据库的方法。MySQL 和 MongoDB 最大的区别在于，MySQL 是传统的关系型数据库，支持 SQL 标准，而 MongoDB 是文档型数据库。MySQL 天生适用于结构化数据以及需要使用事务的场景，天生支持 SQL 标准，对开发者比较友好。

MongoDB 适合如下场景：[1]

* 表结构不明确且数据不断变大，MongoDB 是非结构化文档数据库，扩展字段很容易且不会影响原有数据
* 更高的写入负载，MongoDB 侧重高数据写入的性能，而非事务安全
* 数据量很大或者将来会变得很大，MongoDB 内建了 Sharding、数据分片的特性，容易水平扩展
* 高可用性，MongoDB 自带高可用，自动主从切换（副本集）

而 MongoDB 不支持事务、不支持 JOIN，所以涉及事务和复杂查询的场景不适合 MySQL。

关于 MongoDB 和 MySQL 的对比，可以点击 [此处](https://ruby-china.org/topics/27659) 查看。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x04 参考
***

* [1] 张家江 (2017-09-07). SOCI Installation. Retrieved from [http://tech.lede.com/2017/09/07/rd/server/MongoDBvsMysql](http://tech.lede.com/2017/09/07/rd/server/MongoDBvsMysql).

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

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
