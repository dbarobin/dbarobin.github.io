---
published: true
author: Robin Wen
layout: post
title: 区块链钱包开发
category: 区块链
summary: >-
  本文讲解了开发钱包的预备知识，包括第一是什么是钱包，以及相关的分类，第二是 RPC、JSON-RPC 以及
  JSON，第三是了解区块链相关的基础知识，第四是掌握一门开发语言。接着浅谈了怎么样开发，最后列出了主流项目相关的 RPC
  接口以及开源钱包项目。如果读者对钱包开发感兴趣，希望本文能够给读者一个指引。笔者之前写了数篇关于钱包的文章，但都是做了个简单的介绍。本文站在开发者的角度，给读者讲解下怎么样和钱包进行交互，以及如何开发一个钱包。我们怎么样理解钱包呢？简单讲它是连接区块链的一个入口。目前比较成熟的公链，如比特币、以太坊都有很多钱包可以选择。一般钱包需要完全访问你的用户资产，也就是会要求你输入私钥。钱包的作恶成本极低，这也是笔者建议选择开源钱包的原因之一。
tags:
  - 区块链
  - Blockchain
  - 钱包
  - Wallet
comments:
  - author:
      type: github
      displayName: vat2018
      url: 'https://github.com/vat2018'
      picture: 'https://avatars3.githubusercontent.com/u/37202732?v=4&s=73'
    content: >
      &#x8C22;&#x8C22;&#x5206;&#x4EAB;&#x3002;&#x8865;&#x5145;&#x4E00;&#x4E2A;&#x9002;&#x5408;&#x65B0;&#x624B;&#x7684;&#x4EE5;&#x592A;&#x574A;DApp&#x5F00;&#x53D1;&#x6559;&#x7A0B;&#xFF1A;http://xc.hubwiz.com/course/5a952991adb3847553d205d1?affid=dbarobin7878
    date: 2018-03-12T02:39:31.864Z

---

`文/Robin`

> 本文由币乎(bihu.com)优质内容计划支持。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 21 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

## 0x00 预备知识
***

开发钱包之前，我们需要有以下的预备知识。

**第一，什么是钱包，以及相关的分类。**

笔者之前写了数篇关于 [钱包](https://dbarobin.com/blockchain) 的文章，但都是做了个简单的介绍。本文站在开发者的角度，给读者讲解下怎么样和钱包进行交互，以及如何开发一个钱包。

我们怎么样理解钱包呢？**简单讲它是连接区块链的一个入口**。目前比较成熟的公链，如比特币、以太坊都有很多钱包可以选择。一般钱包需要完全访问你的用户资产，也就是会要求你输入私钥。钱包的作恶成本极低，这也是笔者建议选择开源钱包的原因之一。

![2018-01-21-blockchain-wallet-development](https://cdn.dbarobin.com/p1Tu0t4.jpg)

> 题图来自: © admin / Delphi White Paper Interpretation / 8855104.com

第二，需要了解下什么是 **RPC**、**JSON-RPC** 以及 **JSON**。

> 远程过程调用（英语：Remote Procedure Call，缩写为 RPC）是一个计算机通信协议。该协议允许运行于一台计算机的程序调用另一台计算机的子程序，而程序员无需额外地为这个交互作用编程。如果涉及的软件采用面向对象编程，那么远程过程调用亦可称作远程调用或远程方法调用，例：Java RMI。[1]

RPC 的主要功能目标是让构建分布式计算（应用）更容易，在提供强大的远程调用能力时不损失本地调用的语义简洁性。

RPC 调用分为以下两种：

* 同步调用，客户方等待调用执行完成并返回结果。
* 异步调用，客户方调用后不用等待执行结果返回，但依然可以通过回调通知等方式获取返回结果。若客户方不关心调用返回结果，则变成单向异步调用，单向调用不用返回结果。

**异步和同步的区分在于是否等待服务端执行完成并返回结果。**

远程过程调用是一个分布式计算的客户端 - 服务器（Client/Server）的例子，它简单而又广受欢迎。远程过程调用总是由客户端对服务器发出一个执行若干过程请求，并用客户端提供的参数。执行结果将返回给客户端。由于存在各式各样的变体和细节差异，对应地派生了各式远程过程调用协议，而且它们并不互相兼容。其中我们广为使用的是一个叫做 **JSON-RPC** 的协议。

> JSON-RPC，是一个无状态且轻量级的远程过程调用（RPC）传送协议，其传递内容通过 JSON 为主。相较于一般的 REST 通过网址（如 GET /user）调用远程服务器，JSON-RPC 直接在内容中定义了欲调用的函数名称（如 {"method": "getUser"}），这也令开发者不会陷于该使用 PUT 或者 PATCH 的问题之中。 本规范主要定义了一些数据结构及其相关的处理规则。它允许运行在基于 Socket、HTTP 等诸多不同消息传输环境的同一进程中。其使用 JSON（RFC 4627）作为数据格式。[2]

JSON-RPC 使用了 JSON 格式，那 JSON 是什么呢？

**JSON（JavaScript Object Notation）是一种轻量级的数据交换语言。** 使用 JSON 做数据交换的好处在于，一是兼容性高，二是可阅读性高，三是支持较多的数据格式（比如 number, string, booleans, nulls, array, associative array），四是支持的语言较多。

**第三，了解区块链相关的基础知识。**

了解区块链，书籍资料不用，一本 **精通比特币** 足矣。这里就不赘述了。

**第四，掌握一门开发语言。**

如果你是一门经验丰富的程序员，此条预备知识可以忽略。如果你是一名非计算机科班出身的区块链技术爱好者，建议从 Python 入手。同时，多了解下 JavaScript。

## 0x01 怎么样开发
***

假设我们把预备知识都了解了，接下来我们需要阅读比特币和以太坊的 RPC 接口。

我们以比特币为例，根据 [Original Bitcoin client/API calls list](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list) 文档我们可以得到 RPC 接口提供的函数列表。

以 `sendrawtransaction` 为例，这个函数列表提供了四列（Command、Parameters、Description、Requires unlocked wallet? ），分别表示函数名、传入参数、描述、是否需要解锁钱包。

我们把这个函数列表掌握之后，可以选择某一种语言，然后进行区块链钱包相关的开发。

当然，你还需要知道怎么样去部署一个比特币的完全节点和测试网络，这样就可以利用 RPC 进行接口调用，来和区块链网络进行交互。

## 0x02 相关文档以及源码
***

这里列出主流项目相关的 RPC 接口以及开源钱包项目，以供读者参考。

### 2.1 Bitcoin
***

#### 2.1.1 RPC
***

* [Original Bitcoin client/API calls list](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list)
* [API reference (JSON-RPC)](https://en.bitcoin.it/wiki/API_reference_(JSON-RPC))
* [JSON RPC API](https://blockchain.info/el/api/json_rpc_api)

#### 2.1.2 Wallet
***

* [Bitcoin Core](https://github.com/bitcoin/bitcoin)，官方出品
* [bitcoinj](https://github.com/bitcoinj/bitcoinj)，比特币协议 Java 版
* [bither](https://github.com/bither)，简单安全的比特币钱包
* [Electrum](https://github.com/spesmilo/electrum)，全平台轻钱包
* [bread](https://github.com/voisine/breadwallet-ios)，iOS 钱包
* [Mycelium](https://github.com/mycelium-com/wallet-android)，Android 钱包
* [Copay](https://github.com/bitpay/copay)，同时支持 Bitcoin 和 Bitcoin Cash
* [bitcoin-wallet](https://github.com/bitcoin-wallet/bitcoin-wallet)，又一款 Android 钱包
* [DotNetWallet](https://github.com/nopara73/DotNetWallet)，.NET 实现的钱包
* [Coinpunk](https://github.com/kyledrake/coinpunk)，基于浏览器的钱包
* [btcwallet](https://github.com/btcsuite/btcwallet)，Go 实现的钱包

### 2.2 Ethereum/ERC20
***

#### 2.2.1 RPC
***

* [JSON RPC](https://github.com/ethereum/wiki/wiki/JSON-RPC)
* [JSON RPC API](https://ethereum.gitbooks.io/frontier-guide/content/rpc.html)
* [Management APIs](https://github.com/ethereum/go-ethereum/wiki/Management-APIs)
* [ethjsonrpc](https://github.com/ConsenSys/ethjsonrpc)
* [web3.py](https://github.com/ethereum/web3.py)

#### 2.2.2 Wallet
***

* [go-ethereum](https://github.com/ethereum/go-ethereum)，以太坊协议 Go 版
* [Mist](https://github.com/ethereum/mist)，官方出品
* [Parity](https://github.com/paritytech/parity)，支持 Windows、Mac、PC 的钱包
* [MetaMask](https://github.com/MetaMask)
* [MyEtherWallet](https://github.com/kvhnuke/etherwallet)，基于浏览器的钱包
* [eth-lightwallet](https://github.com/ConsenSys/eth-lightwallet)，轻量级 JavasSript 版本钱包
* [ethaddress.org](https://github.com/ryepdx/ethaddress.org)，纸质版钱包生成器
* [Иeureal Wallet](https://github.com/hunterlong/neureal-wallet)，支持 Windows、Mac、PC 的钱包

### 2.3 其他
***

#### 2.3.1 Zcash
***

* [Zcash](https://github.com/zcash/zcash)，官方出品

#### 2.3.2 BitShares
***

* [BitShares](https://github.com/bitshares)，官方出品

#### 2.3.3 Sia
***

* [Sia](https://github.com/NebulousLabs/Sia)，官方出品

#### 2.3.4 Nem
***

* [NanoWallet](https://github.com/NemProject/NanoWallet)，官方出品

#### 2.3.5 Dash
***

* [Dash](https://github.com/dashpay)，官方出品

#### 2.3.6 Qtum
***

* [Qtum Core Wallet](https://github.com/qtumproject/qtum)，官方出品

#### 2.3.7 Litecoin
***

* [Litecoin](https://github.com/litecoin-project)，官方出品

#### 2.3.8 IOTA
***

* [IOTA Wallet](https://github.com/iotaledger/wallet)，官方出品

#### 2.3.9 Monero
***

* [Monero](https://github.com/monero-project/monero)，官方出品

#### 2.3.10 GXS
***

* [GXS Wallet for mobile](https://github.com/gxchain/gxs-wallet)，官方出品

#### 2.3.11 Ethereum Classic
***

* [Ethereum Classic Wallet](https://github.com/ethereumproject/emerald-wallet)，官方出品

### 0x03 小结
***

本文讲解了开发钱包的预备知识，包括第一是什么是钱包，以及相关的分类，第二是 RPC、JSON-RPC 以及 JSON，第三是了解区块链相关的基础知识，第四是掌握一门开发语言。接着浅谈了怎么样开发，最后列出了主流项目相关的 RPC 接口以及开源钱包项目。如果读者对钱包开发感兴趣，希望本文能够给读者一个指引。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

### 0x04 参考
***

* [1] 维基百科 (2006-07-21). 远程过程调用. Retrieved from [https://zh.wikipedia.org/wiki/遠程過程調用](https://zh.wikipedia.org/wiki/遠程過程調用).
* [2] 维基百科 (2017-04-19). JSON-RPC. Retrieved from [https://zh.wikipedia.org/wiki/JSON-RPC](https://zh.wikipedia.org/wiki/JSON-RPC).

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。BTW，**李笑来老师也加入了**。

![区块链技术指北](https://cdn.dbarobin.com/pQxlDqF.jpg)

「区块链技术指北」相关资讯渠道：

* 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
* 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
* Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
* Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
* 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

* 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
* 个人博客，[https://dbarobin.com](https://dbarobin.com)
* 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
* Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
* Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
* 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
* 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

* BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
* ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
* PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
* 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
