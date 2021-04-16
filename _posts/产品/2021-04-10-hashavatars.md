---
published: true
author: Robin Wen
layout: post
title: 推荐 HashAvatars
category: 产品
summary: "无意中发现一个很有趣的产品，叫做 HashAvatars。HashAvatars 是在以太坊的 Layer 2 侧链 xDai 中运行的，生成每一个 HashAvatar 需要 1 xDai，也就是 1 USD。但是生成之后，你可以用任意的价格进行售卖。你生成的头像或者你购买的头像，不会被复制或者销毁，将会永久存储在区块链网络上。HashAvatars 的网站是 thehashavatars.com，感兴趣可以去体验下。"
tags:
  - 产品
  - DeFi
  - xDai
  - NFT
  - HashAvatars
---

`文/Robin`

***

![](https://cdn.dbarobin.com/b1nfgue.png)

无意中发现一个很有趣的产品，叫做 HashAvatars。这是一个什么样的产品呢？

> The HashAvatars are Avatars waiting to be claimed by anyone on xDai Chain. Once you select the avatar's name a specific avatar figure will be generated and you can mint single or multiple copies of it. Choose your preferred HashAvatar and start your collection now!

笔者的头像用了很多年了，头像是在一个叫做 Face Your Manga 的网站生成的。当时制作太过神似，于是花了重金买了高清版。HashAvatars 的玩法也是生成头像，不过结合当下流行的 NFT 玩法，生成的头像还可以交易。

HashAvatars 是在以太坊的 Layer 2 侧链 xDai 中运行的，生成每一个 HashAvatar 需要 1 xDai，也就是 1 USD。但是生成之后，你可以用任意的价格进行售卖。你生成的头像或者你购买的头像，不会被复制或者销毁，将会永久存储在区块链网络上。

提到 NFT，这里再解释下。NFT 是一种非同质化代币，该类代币的智能合约具有独特的属性和数据。NFT 的标准通常有 ERC-721 和 ERC-1155。ERC-721 是非同质化代币的标准之一。一个 ERC-721 代币代表了一份完整且不可分割的资产，有着独一无二的价值，如证书和代币化商品。ERC-1155 同样支持创建 NFT 代币，且允许一次批量传输多个代币，速度比 ERC-721 快得多。ERC-1155 代币通常被描述为 「下一代多代币标准」。

阅读 HashAvatars 的源码后发现，在 contracts 目录同时有 ERC-721 和 ERC-1155 两个标准的合约代码。HashAvatars 生成的头像，是通过一个叫做 Eporio 的网站交易的。

每次访问 HashAvatars 网站的 Generate Avatar 页面，都会自动生成一个新的 Avatar，如果你要认领，只需要取一个名字，然后设定副本数量，连接钱包交互即可。不过 HashAvatars 的问题在于，没有给创作者发挥的空间。

HashAvatars 的网站是 thehashavatars.com，感兴趣可以去体验下。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)。如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

* 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
* 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

***

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)