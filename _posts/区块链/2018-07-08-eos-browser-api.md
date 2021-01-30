---
published: true
author: Robin Wen
layout: post
title: 构建 EOS 区块链浏览器 API
category: 区块链
summary: "在上一篇文章中笔者讲解了如何将 EOS 链上数据实时异构到 MySQL 数据库。数据既然有了，就要有用武之地。传统互联网获取数据的方式，最底层是数据层，然后有各种各种的 API，API 访问数据，再供前端展示。本文讲解如何使用开源的 EOSTracke-API 构建区块链浏览器 API。目前有较多的区块链浏览器，具体可以访问由 SuperONE 团队整理的 awesome-eos。EOSTracker 是较早开源的区块链浏览器，后端使用 PHP，前端使用 AngularJS，读者感兴趣可以阅读源码。"
tags:
  - 区块链
  - Blockchain
  - EOS
---

`文/Robin`

这是「区块链技术指北」的第 34 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 0x00 背景
***

在上一篇 [文章](https://dbarobin.com/2018/07/06/eos-sql-db-plugin) 中笔者讲解了如何将 EOS 链上数据实时异构到 MySQL 数据库。数据既然有了，就要有用武之地。传统互联网获取数据的方式，最底层是数据层，然后有各种各种的 API，API 访问数据，再供前端展示。

本文讲解如何使用开源的 [EOSTracke-API](https://github.com/EOSEssentials/EOSTracker-API.git) 构建区块链浏览器 API。

## 0x01 部署
***

EOSTracke-API 使用 PHP 编写，为了能让它正确地运行，我们需要安装 PHP 相关依赖，命令如下：

``` bash
$ apt-get -y install php7.1 php7.1-common
$ apt-get -y install php7.1-curl php7.1-xml php7.1-zip php7.1-gd php7.1-mysql php7.1-mbstring
$ apt-get -y install php7.1-apcu
$ apt-get -y install php7.1-intl
$ apt-get -y install php7.1-redis
```

需要注意的是，PHP 需要 7.1 版本，所以按照如上的命令执行即可。

接着安装 PHP composer，命令如下：

``` bash
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
$ php composer-setup.php
$ php -r "unlink('composer-setup.php');"
```

然后安装 EOSTracker-API。

``` bash
$ git clone https://github.com/EOSEssentials/EOSTracker-API.git
$ cd EOSTracker-API
$ ./composer.phar install
```

安装过程中会输入如下的几个参数：

* secret，建议随机生成
* env(DB_URL) 输入 'mysql://YOURUSER:YOURPASS@127.0.0.1/YOURDB'，其中 YOURUSER 表示连接 EOS 区块链数据库的用户名，YOURPASS 表示连接 EOS 区块链数据库的用户密码，YOURDB 表示 EOS 区块链数据库名字
* env(REDIS_URL)，目前暂未实现，推测作者想解决直接访问 MySQL 速度过慢问题

如果命令行交互输入错误，我们可以编辑 `app/config/parameters.yml` 文件手动修改。

完成之后，运行如下命令启动 Server：

``` bash
$ nohup php bin/console server:run 0.0.0.0:8000 > api.log 2>&1 &
```

## 0x02 测试
***

浏览器访问 http://xxx.xxx.xxx.xxx:8000 即可访问 API，在 http://xxx.xxx.xxx.xxx:8000/_profiler/ac3944?panel=router 即可看到所有的 router，读者可以访问如下的连接测试 API 是否能够正常读取 MySQL 数据。

> http://xxx.xxx.xxx.xxx:8000/accounts/eosio.token

当然，可以给 API 配置 HTTPS 域名，参考如下的文章：

* [How To Install Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)
* [Configuring a Web Server](https://symfony.com/doc/current/setup/web_server_configuration.html)
* [How To Secure Nginx with Let's Encrypt on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

## 0x03 小结
***

目前有较多的区块链浏览器，具体可以访问由 SuperONE 团队整理的 [awesome-eos](https://github.com/superoneio/awesome-eos)。EOSTracker 是较早开源的区块链浏览器，后端使用 PHP，前端使用 AngularJS，读者感兴趣可以阅读源码。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
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
