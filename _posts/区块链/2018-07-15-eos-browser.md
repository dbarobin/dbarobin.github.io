---
published: true
author: Robin Wen
layout: post
title: 构建 EOS 区块链浏览器
category: 区块链
summary: "前面的文章讲解了如何让 nodeos 支持将链上数据实时异构到 MySQL 以及如何部署开源的区块链浏览器 API，但问题来了，没有前端展示，对于用户而言不够直观。EOS 链上数据又了，API 也有了，剩下的就是前端了。EOSTracker 是较早开源的区块链浏览器，后端使用 PHP，前端使用 AngularJS，接下来我们讲解如何部署前端。连续更新 3 篇文章，可以让读者从 0 到 1 构建区块链浏览器。区块链的强大魅力之处在于所有数据公开透明，任何人都可以同步全节点数据，任何人都可以追溯链上数据。希望能给读者帮助。"
tags:
  - 区块链
  - Blockchain
  - EOS
---

`文/Robin`

这是「区块链技术指北」的第 35 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

![](https://cdn.dbarobin.com/smxzEPJ.png)

> 题图来自: © ChainONE / Original / chainon.io

## 0x00 背景
***

前面的文章讲解了如何让 nodeos 支持将链上数据实时异构到 MySQL 以及如何部署开源的区块链浏览器 API，但问题来了，没有前端展示，对于用户而言不够直观。

## 0x01 部署
***

EOS 链上数据有了，API 也有了，剩下的就是前端了。EOSTracker 是较早开源的区块链浏览器，后端使用 PHP，前端使用 AngularJS，接下来我们讲解如何部署前端。

首先我们需要安装 nodejs，命令如下：

``` bash
$ apt-get install curl python-software-properties
$ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
$ apt-get install nodejs
$ node -v
$ npm -v
```

接着 clone 代码，安装相关依赖。

``` bash
$ git clone https://github.com/EOSEssentials/EOSTracker.git
$ cd EOSTracker
$ npm install
$ npm install -g @angular/cli
```

## 0x02 测试
***

接着我们执行如下命令访问 EOSTracker。

``` bash
$ ng serve --host 0.0.0.0
```

此步可能会遇到如下错误：

``` js
ERROR in ./node_modules/cipher-base/index.js
Module not found: Error: Can't resolve 'stream' in '/data/EOSTracker/node_modules/cipher-base'
ERROR in ./node_modules/hash-base/index.js
Module not found: Error: Can't resolve 'stream' in '/data/EOSTracker/node_modules/hash-base'
ℹ ｢wdm｣: Failed to compile.
```

我们可以执行如下命令解决上述问题，详情参考 [此处](https://github.com/EOSEssentials/EOSTracker/issues/62)。

> node patch.js

再次执行 `ng serve --host 0.0.0.0`，浏览器访问 http://xxx.xxx.xxx.xxx:4200 即可访问浏览器。

问题来了，我们没有配置 API，这个数据是怎么读取的呢？原来默认读取的是 EOSTracker 提供的 API。假如我们要修改成自己的 API，怎么处理？很简单，两行代码解决。

``` bash
$ vim src/environments/environment.ts
$ vim src/environments/environment.prod.ts
```

将 `apiUrl: 'https://api.eostracker.io'` 修改成自己的 API 即可。

当然，我们同样可以给区块链添加域名以及配置 HTTPS，参考如下：

* [How To Install Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)
* [Configuring a Web Server](https://symfony.com/doc/current/setup/web_server_configuration.html)
* [How To Secure Nginx with Let's Encrypt on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

## 0x03 小结
***

连续更新 3 篇文章，可以让读者从 0 到 1 构建区块链浏览器。区块链的强大魅力之处在于所有数据公开透明，任何人都可以同步全节点数据，任何人都可以追溯链上数据。希望能给读者带来帮助。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
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
