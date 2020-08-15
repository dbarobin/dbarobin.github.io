---
published: true
author: Robin Wen
layout: post
title: "快速构建 EOS 主网 Fullnode"
category: 区块链
summary: "截止目前，EOS 主网区块数量达到 1093 万，区块数据达到数十 G，备份数据也达到了 16G。如果从头开始同步区块数据，需要数天的时间。随着区块不断累加，同步的时间越来越长。如何快速构建 Fullnode，这成为每一个 BP、DApp 开发者等用户需要考量的问题。本文给出根据区块备份数据快速构建 Fullnode 的方法。"
tags:
- 区块链
- Blockchain
- EOS
---

`文/Robin`

这是「区块链技术指北」的第 37 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 快速构建 Fullnode
***

截止目前，EOS 主网区块数量达到 1093 万，区块数据达到数十 G，备份数据也达到了 16G。如果从头开始同步区块数据，需要数天的时间。随着区块不断累加，同步的时间越来越长。如何快速构建 Fullnode，这成为每一个 BP、DApp 开发者等用户需要考量的问题。

**本文给出根据区块备份数据快速构建 Fullnode 的方法。**

## 备份服务
***

这里给出两个 EOS 区块数据备份服务，一是由 **EOS Store** 提供，二是由 **EOS Cleaner** 提供。

* [eosstore eos mainnet backup](phttps://s3-ap-northeast-1.amazonaws.com/eosstorebp)
* [eoscleaner eos mainnet backup](http://eoscleaner.com/Project%20Detail.html)

以 EOS Store 提供的服务为例，访问 [https://s3-ap-northeast-1.amazonaws.com/eosstorebp](https://s3-ap-northeast-1.amazonaws.com/eosstorebp) 会获得 XML 格式的数据，Contents 中的 Key 表示文件名，LastModified 表示修改时间，Size 表示备份文件大小。EOS Store 每天备份两次，分别是 00:30 和 12:30，如果我们获得最新的一次备份，可以执行以下命令提供：

``` bash
$ wget https://s3-ap-northeast-1.amazonaws.com/eosstorebp/2018-08-13-12_30_20.tar.gz
```

如果自己的服务器是国内云服务商提供，会发现无法访问，不着急，可以用一台国外 VPS 做中转。具体中转方法笔者可以后续提供。

EOS Cleaner 提供的服务比较友好，访问 [https://s3-ap-northeast-1.amazonaws.com/data.eos/index.html](https://s3-ap-northeast-1.amazonaws.com/data.eos/index.html) 可以看到一个文件下载链接，如果不想本地下载，浏览器右键拷贝下载链接即可。

## 编译
***

本文以 `EOS-Mainnet` 为例。

``` bash
$ cd /data
$ git clone https://github.com/EOS-Mainnet/eos --recursive
$ cd eos
$ git pull
$ git checkout mainnet-1.1.4
$ git submodule update --recursive
$ sed -i.bak '16i set( CORE_SYMBOL_NAME "EOS" )' CMakeLists.txt
$ ./eosio_build.sh
```

关于如何加速 GitHub，可以参考 [GitHub 加速最佳实践](https://dbarobin.com/2017/01/24/github-acceleration-best-practices)。

## 启动
***

接着我们启动 nodeos，主网 Fullnode 相关的配置文件可以访问如下链接下载：

> https://github.com/superoneio/eos-mainnet

启动 Fullnode 相关命令如下：

``` bash
$ mkdir /data/eos/build/programs/nodeos
$ git clone https://github.com/superoneio/eos-mainnet
$ mkdir -p data logs config tmp
$ cp eos-mainnet/config.ini config/config
$ cp eos-mainnet/genesis.json .
$ cp eos-mainnet/*.sh .
$ chmod +x *.sh
```

解压 nodeos 备份数据，启动 nodeos 进程，建议以普通用户启动，例如本文的 eosio。另外，注意此处的 `--hard-replay-blockchain` 参数。

``` bash
$ su - eosio -s /bin/bash
$ cd /data/eos/build/programs/nodeos/tmp
$ tar -zxvf 2018-08-13-12_30_20.tar.gz
$ mv state blocks ../data
$ chown eosio:eosio -R /data/eos
$ su - eosio -s /bin/bash
$ cd /data/eos/build/programs/nodeos
$ echo "nohup /data/eos/build/programs/nodeos/nodeos \
--hard-replay-blockchain --data-dir ./data \
--config-dir ./config > ./logs/eos.log 2>&1 &
echo $! > eos.pid" > start.sh
$ bash start.sh
```

## 测试
***

使用如下命令查看同步进度。

``` bash
$ /data/eos/build/programs/cleos/cleos get info
```

## 小结
***

快速构建 Fullnode 对 BP、DApp 开发者等都非常有帮助，希望本文能够给读者一个指引。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

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
