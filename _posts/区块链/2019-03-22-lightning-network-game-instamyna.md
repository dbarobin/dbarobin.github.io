---
published: true
author: Robin Wen
layout: post
title: 基于闪电网络的比特币挖矿游戏 Instamyna
category: 区块链
summary: 根据 BlueWallet 的应用市场页面，目前有 25 款应用。今天刷 Twitter 看到一款名叫 Instamyna 的游戏。心血来潮，了解后赶紧去体验下。相信读者也看明白了，游戏宣传是比特币挖矿游戏，实际上这就是骰子游戏啊。比特币闪电网络的极速支付，将会诞生越来越多的 Lapp。笔者将会持续关注闪电网络，欢迎通过 RSS 订阅本博客。此外，读者还可以关注笔者的 Twitter。
tags:
  - 区块链
  - Blockchain
  - 闪电网络
---

`文/Robin`

这是「区块链技术指北」的第 **53** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」，同时我会把你拉入微信群。

## 前言
***

根据 BlueWallet 的 [应用市场](https://bluewallet.io/marketplace) 页面，目前有 25 款应用。

今天刷 Twitter 看到一款名叫 [Instamyna](https://ice3x.co.za/ln-game-instamyna) 的游戏。心血来潮，了解后赶紧去体验下。

## Instamyna 是什么
***

首先我们看看 Instamyna 是什么。

Instamyna 是基于比特币闪电网络的比特币挖矿游戏。读者将获得 3 份挖矿合约，出块时间是 30 秒。每轮中的 3 份合约只有一份是赚钱的。读者的目标是选择一个最有可能赚钱的合约来支付。

## 怎么玩 Instamyna
***

这个游戏具体怎么玩呢？

选择 3 份中的一份合约，读者将会看到一个二维码和闪电网络付款文本地址，使用支付闪电网络的比特币钱包来转账指定数量的聪。一旦智能合约收到读者的支付信息，它将会自动开启挖矿，耗时 30 秒。如果读者选择的合约刚好中奖，将会立刻收到奖金并且可以在钱包内提现（当然闪电网络钱包要支持）。

## 实操
***

来吧，开始我们的游戏。游戏地址点击 [此处](https://ice3x.co.za/cryptocurrency-games/instamyna-bitcoin-lightning-game/)。

首先，Chrome 打开游戏链接，读者将会看到如下的页面，可以看到有 Light、Standard、Pro 三个套餐选择，当然需要支付的聪也是不同的。

![](https://cdn.dbarobin.com/qpm59gZ.png)

接着，笔者选择了 1000 Satoshis Instamyna Light，选择了 #3 合约，使用 Hoo 钱包支付了 1000 聪（实际支付了 1001 Satoshi，Hoo [iOS](https://dbarobin.com/2019/03/22/lightning-network-hoo-ios) 和 Android 均支持闪电网络），之后可以看到秒级支付，合约马上开始执行。

![](https://cdn.dbarobin.com/rwpML8n.png)

然后，看到如下图的 30 秒等待页面：

![](https://cdn.dbarobin.com/u3oZJLI.png)

最后，笔者输了，读者看到的图是这个样子的。

![](https://cdn.dbarobin.com/vbwwdTU.png)

## 验证
***

可能读者担心了，这个结果靠谱吗？背后是否有暗箱操作。不用担心，这款游戏提供了验证脚本，点击 [此处](https://repl.it/@citlayik/tcmverify) 访问。完整代码如下：

``` js
//to verify -> copy your values and set seedObject as below:
var seedObject = {
  serverSeed: "1d26867264f68d6d1f503582dd6903ee", clientSeed: "42a00c71f192f75fca40fbf83b0cf8d3"};

verifyServerSeedvsHash(seedObject);

for(var i=1; i < 100; i++) {
  calcSeed(seedObject.serverSeed, seedObject.clientSeed, i);
}


function verifyServerSeedvsHash(seedObject) {
  var server_seed = seedObject.serverSeed;
    let hash = crypto.createHash('SHA256');
      hash.update(server_seed);
      var server_seed_hashed = hash.digest().toString('hex');
      console.log("server_seed: ", server_seed, "\nserver_seed_hashed: ", server_seed_hashed);
}

function calcSeed(server_seed, client_seed, nonce) {

      //var nonce = 1;
      var newclientseed = client_seed + "-" + nonce;
      //console.log("calcSeed newclientseed: ", newclientseed);

      let hmac = crypto.createHmac('SHA512', server_seed);
      hmac.update(newclientseed);
      let buf = hmac.digest();
      var finalOutput = buf.readUInt32BE() / Math.pow(2, 32) * 100;
      var winorlose = "lose";
      if(finalOutput < 45) {
        winorlose = "win";
      } 
      console.log("Bet Result: ",nonce, finalOutput, winorlose);
      return finalOutput;

}
```

笔者使用此次游戏生成的 serverSeed 和 clientSeed 进行替换，执行结果确实是 lose。

> serverSeed: "zdlnv9driigy7lktpbi7cuyzta3ud39q"
> clientSeed: "kfkuvhz0laxi5k79sbhothmgtcoi60jf"

## 小结
***

相信读者也看明白了，游戏宣传是比特币挖矿游戏，实际上这就是骰子游戏啊。比特币闪电网络的极速支付，将会诞生越来越多的 Lapp。笔者将会持续关注闪电网络，欢迎通过 [RSS](https://dbarobin.com/feed.xm) 订阅本博客。区块链技术指北社区也有 [Lightning](https://bbs.chainon.io/t/lightning) 模块，欢迎关注。此外，读者还可以关注笔者的 [Twitter](https://twitter.com/vrwio)。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.cc/cn/register/?ref=11190872](https://accounts.binancezh.cc/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

Enjoy！

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
