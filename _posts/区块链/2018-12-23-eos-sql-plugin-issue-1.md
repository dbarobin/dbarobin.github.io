---
published: true
author: Robin Wen
layout: post
title: EOSIO MySQL 插件 issue 1
category: 区块链
summary: 链上数据如何转化为 API 友好的数据，这是一个很重要的话题。因为能够转化为 API 友好的数据，对于开发者而言，可以极大地提高效率。目前 EOS 生态有很多插件，比如 MySQL 插件、ES 插件、Kafka 插件等等。
tags:
  - 区块链
  - Blockchain
  - EOS
---

`文/Robin`

这是「区块链技术指北」的第 40 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 0x00 背景
***

链上数据如何转化为 API 友好的数据，这是一个很重要的话题。因为能够转化为 API 友好的数据，对于开发者而言，可以极大地提高效率。目前 EOS 生态有很多插件，比如 MySQL 插件、ES 插件、Kafka 插件等等。

笔者在测试环境部署了基于 [kesar](https://github.com/EOSIO/eos/pull/3882) 写的 MySQL 插件，结果同步数据时发现有如下的异常。

<!--more-->

![](https://cdn.dbarobin.com/O0w2yeu.jpg)

## 0x01 解决
***

查看 nodeos 日志，可以发现如下异常。

``` bash
When sync to zhengjinhua1, and nodoes exit. The log as follows:

terminate called after throwing an instance of 'soci::mysql_soci_error'
  what():  Out of range value for column 'amount' at row 1 while executing "INSERT INTO tokens(account, amount, symbol) VALUES (:ac, :am, :as) " with :ac="zhengjinhua1", :am=1e+10, :as="UXB".
```

我们从日志中分析得出如下结论：

> The amount filed of tokens table defines double(14,4), but 1e+10 means 10 billion, it exceed the maximum. It should change to double(64,4), the command as follows:

``` sql
ALTER TABLE tokens CHANGE amount amount double(64,4) DEFAULT NULL;
```

In addition, I sync the data of maninnet using this sql_db_plugin.

Submit a pr, see at: https://github.com/NebulaProtocol/eos/pull/2

## 0x02 小结
***

MySQL 插件目前不太成熟，还有很多坑，谨慎入坑。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

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
