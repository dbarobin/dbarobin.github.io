---
published: true
author: Robin Wen
layout: post
title: Mixin Webhook 第二篇，mixin-log-cli
category: 区块链
summary: 上一篇文章讲解了通过 Mixin Webhook 实现零代码实现接收 GitHub Release 推送，今天给大家讲解一个工具，mixin-log-cli，Webhook 实现监控日志。想象一种场景，同步区块链节点数据，一般日志都会有同步进度，假如读者不想一直盯着屏幕，那这个工具就可以帮您做重复检查同步日志的工作。当然了，这个工具能用的场景不仅于此，读者可以自行挖掘。
tags:
  - 区块链
  - Blockchain
  - Mixin
  - Mixin Network
  - Webhook
---

`文/robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 **71** 篇文章。

## 一 前言
***

上一篇文章讲解了通过 Mixin Webhook 实现零代码实现接收 GitHub Release 推送，今天给大家讲解一个工具，[mixin-log-cli](https://github.com/liuzemei/mixin-log-cli)，Webhook 实现监控日志。

![](https://cdn.dbarobin.com/dkugonh.png)

> 题图来自：© Helton Simões Gomes / Fim da Franquia de dados / uol.com.br

## 二 实践
***

### 2.1 安装
***

mixin-log-cli 的项目地址：[https://github.com/liuzemei/mixin-log-cli](https://github.com/liuzemei/mixin-log-cli)，作者是 [liuzemei](https://github.com/liuzemei/)。

使用这个工具也很简单，使用 npm 安装即可。

``` bash
$ sudo apt-get install curl
$ curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
$ sudo apt-get install nodejs
$ node -v
$ npm -v
$ npm install -g mixin-log-cli
```

### 2.2 使用示例
***

**参数介绍**

| 参数 | 是否必须 | 含义 |
| - | ---- | ----------------- |
| -f | 是 | `filepath`:要监听的文件路径 |
| -t | 否 | `token`: webhook 的 token |
| -k | 否 | `keystore`:keystore的json格式的文件路径 |
| -r | 否 | `recipient_id`:获取 log 的 user_id |
| -w | 否 | `keyword`:要监听的关键词，以 `,` 隔开 |
| -s | 否 | `filesize`:监听文件大小（单位为 KB） |
| -i | 否 | `interval`:log的时间间隔只对监听文件大小生效（单位为分钟，默认60） |

注意：

* `-t` 和 `-k` 至少得指定一个。
* 如果指定 `-k` ，则必须指定 `-r`。
* `-w` 和 `-s` 至少得指定一个。

示例一，通过 Webhook 发送消息给个人或者 Mixin Messenger 小群群主。`YOUR_WEBBOOK_TOKEN` 需要读者从 Webhook 机器人 [7000000012](https://mixin.one/codes/4d792128-1db8-4baf-8d90-d0d8189a4a7e) 获取。

``` bash
$ mixin-log-cli -f ./log/err.log -t YOUR_WEBBOOK_TOKEN -w error

# 检测到 err.log 有相关日志，如果发送给个人，Webhook 机器人会给你发送消息。
error was found 23095 times. Please check.
```

示例二，通过任意机器人发送消息给个人。`keystore.json ` 需要读者从 [https://developers.mixin.one/dashboard](https://developers.mixin.one/dashboard) 新建机器人后获取。`YOUR_RECEIPIENT_ID` 读者可以从机器人 mixin info bot（ID：7000101942）获取，关键词为 search:mixin_id，比如 search:26930。

``` bash
$ mixin-log-cli -f ./log/err.log -k ./config/keystore.json -r YOUR_RECEIPIENT_ID  -w error

# 检测到 err.log 有相关日志，keystore.json 配置的机器人会给你发送消息。
error was found 23095 times. Please check.
```

此外，您还可以同时指定以上两种方式，会分别发送。

## 三 小结
***

想象一种场景，同步区块链节点数据，一般日志都会有同步进度，假如读者不想一直盯着屏幕，那这个工具就可以帮您做重复检查同步日志的工作。当然了，这个工具能用的场景不仅于此，读者可以自行挖掘。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

![区块链技术指北](https://cdn.dbarobin.com/3YzonTR.png)

「区块链技术指北」相关资讯渠道：

> 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
> 官网，[https://chainon.io](https://chainon.io)
> 官方博客，[https://blog.chainon.io](https://blog.chainon.io)
> 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
> Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
> Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
> Facebook，[https://www.facebook.com/chainone.org](https://www.facebook.com/chainone.org)
> 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

> 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
> 个人博客，[https://dbarobin.com](https://dbarobin.com)
> 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
> 简书，[https://www.jianshu.com/c/a37698a12ba9](https://www.jianshu.com/c/a37698a12ba9)
> Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
> Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
> 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
> EOS LIVE，[https://eos.live/user/robin](https://eos.live/user/robin)
> 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

> BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
> ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
> PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
> 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
