---
published: true
author: Robin Wen
layout: post
title: 优化 EOS 交易记录查询
category: 区块链
summary: 钱包获取交易记录，这是一个非常重要的功能。EOS 链上数据转化为关系型数据，然后再从关系型数据获取交易记录，这是比较普遍的做法。问题在于，EOSIO 并没有提供此插件。所以，本文给出实现思路以及解决方案。SQL 语句优化是一门不断试错，不断否定自己的学问。读者需要对业务逻辑有深刻的认识，才有可能优化出对业务友好的 SQL 语句。
tags:
  - 区块链
  - Blockchain
  - EOS
---

`文/Robin`

这是「区块链技术指北」的第 43 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 0x00 背景
***

钱包获取交易记录，这是一个非常重要的功能。EOS 链上数据转化为关系型数据，然后再从关系型数据获取交易记录，这是比较普遍的做法。

问题在于，EOSIO 并没有提供此插件。所以，本文给出实现思路以及解决方案。

<!--more-->

![](https://cdn.dbarobin.com/O0w2yeu.jpg)

## 0x01 解决方案
***

EOSIO MySQL 插件可以参考 [这里](https://github.com/superoneio/eos_sql_db_plugin)。

笔者在 actions 添加相关字段，然后通过插件把数据解析到对应字段，最后再用 SQL 语句查询即可。

其中 actions 表结构如下：

``` sql
CREATE TABLE `actions` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `account` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '合约拥有者账号',
  `transaction_id` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '交易号',
  `seq` smallint(6) NOT NULL DEFAULT '0' COMMENT '序列号',
  `parent` bigint(20) NOT NULL DEFAULT '0' COMMENT '',
  `name` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT 'action 名称',
  `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `data` json DEFAULT NULL COMMENT 'action 数据',
  `authorization` mediumtext DEFAULT NULL COMMENT '执行权限',
  `eosto` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '提取 data 的 to 字段',
  `eosfrom` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '提取 data 的 from 字段',
  `receiver` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '提取 data 的 receiver 字段',
  `payer` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '提取 data 的 payer 字段',
  `newaccount` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '新建账号名称',
  `sellram_account` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '卖内存的用户名',
  PRIMARY KEY (`id`),
  KEY `idx_actions_account` (`account`),
  KEY `idx_actions_name` (`name`),
  KEY `idx_actions_tx_id` (`transaction_id`),
  KEY `idx_actions_created` (`created_at`),
  KEY `idx_actions_eosto` (`eosto`),
  KEY `idx_actions_eosfrom` (`eosfrom`),
  KEY `idx_actions_receiver` (`receiver`),
  KEY `idx_actions_payer` (`payer`),
  KEY `idx_actions_newaccount` (`newaccount`),
  KEY `idx_actions_sellram_account` (`sellram_account`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 0x02 优化
***

获取交易记录的语句如下：

``` sql
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE (b.actor = ?
    OR a.newaccount = ?
    OR a.eosto = ?
    OR a.eosfrom = ?
    OR a.receiver = ?
    OR a.payer = ?)
ORDER BY a.created_at DESC
LIMIT ?, ?
```

这是初始版本，因为有数个 OR 条件，导致效率极低，查询一个账号的交易记录要数十秒，完全不可用。于是第二版优化如下：

``` sql
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE b.actor = ?
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE a.newaccount = ?
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE a.eosto = ?
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE a.eosfrom = ?
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE a.receiver = ?
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
    LEFT JOIN actions_accounts b ON a.id = b.action_id
WHERE a.payer = ?
ORDER BY created_at DESC
LIMIT ?, ?;
```

第二版仍然使用表关联，不过把 WHERE 语句中的 OR 条件改成了 UNION ALL。因为表里有大量的关联，效率还是低，查询一个账号的交易记录要 10 秒左右。

于是我们对 SQL 插件解析数据也进行改写，有了第三版的优化：

``` sql
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE a.newaccount = ?
    AND a.name = 'newaccount'
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE (a.eosto = ?
        OR a.eosfrom = ?)
    AND a.name = 'transfer'
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE (a.eosfrom = ?
        OR a.receiver = ?)
    AND a.name = 'delegatebw'
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE (a.eosfrom = ?
        OR a.receiver = ?)
    AND a.name = 'undelegatebw'
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE a.sellram_account = ?
    AND a.name = 'sellram'
UNION ALL
SELECT created_at, data, name, transaction_id
FROM actions a
WHERE (a.payer = ?
        OR a.receiver = ?)
    AND (a.name = 'buyrambytes'
        OR a.name = 'buyram')
ORDER BY created_at DESC
LIMIT ?, ?
```

我们可以看到只需要查询 actions 表，name 字段表示动作（目前查询：newaccount、transfer、delegatebw、undelegatebw、sellram 和 buyram）。另外，WHERE 条件全部有索引。查询一个账号的交易记录，1s 以内就可以返回，业务可用性极速提高。

业务运行一段时间后，访问速度又出现了瓶颈，我们做了下一次的优化。从业务入手，既然查询交易记录只查询单表，如果不同的操作只要涉及到查询账号，返回数据不就好了？于是优化结果如下：

``` sql
SELECT id, created_at, data, name, transaction_id
FROM actions a
WHERE (a.eosto = ?
    OR a.eosfrom = ?
    OR a.receiver = ?
    OR a.payer = ?
    OR a.newaccount = ?
    OR a.sellram_account = ?)
ORDER BY created_at DESC
LIMIT ?, ?
```

## 0x03 小结
***

SQL 语句优化是一门不断试错，不断否定自己的学问。读者需要对业务逻辑有深刻的认识，才有可能优化出对业务友好的 SQL 语句。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)

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
