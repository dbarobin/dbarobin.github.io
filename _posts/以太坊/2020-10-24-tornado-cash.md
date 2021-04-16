---
published: true
author: Robin Wen
layout: post
title: 以太坊隐私协议 Tornado Cash
category: 以太坊
summary: "据 Dune Analytics 数据显示，以太坊隐私协议 Tornado Cash 美元存款量已超 1 亿美金。当前 Tornado Cash 内 ETH 存款量近 32 万枚，约 31.82 万枚。DAI、cDAI、USDC、USDT 等美元稳定币存款总量约为 1.03 亿美金。当然了，使用 Tornado 还有一些额外的隐私辅助技巧，感兴趣可以阅读 Tornado 的 Medium。Tornado 给那些需要隐私保护的用户提供了一个选择。假如前段时间 KuCoin 被盗的币一方面通过 DEX，另一方面通过 Tornado 洗币，这样的追踪难度就瞬间提高了，隐私确实是把双刃剑。"
tags:
  - 以太坊
  - Ethereum
  - Tornado
---

`文/Robin`

***

![](https://cdn.dbarobin.com/8576t8a.png)

据 Dune Analytics 数据显示，以太坊隐私协议 Tornado Cash 美元存款量已超 1 亿美金。当前 Tornado Cash 内 ETH 存款量近 32 万枚，约 31.82 万枚。DAI、cDAI、USDC、USDT 等美元稳定币存款总量约为 1.03 亿美金。

默认情况下，你在以太坊上所有的交易历史和余额都是公开的。通过诸如 Etherscan 这样的区块浏览器可以查看所有的交易，某人但凡知道了你的地址，就可以轻轻松松获取你的支付记录、跟踪你的资金来源、计算你的持仓、甚至分析你的链上活动。

Tornado.cash 是如何在以太坊上实现隐私的？主要思路就是打破收款人和资金目标地址之间在链上的关联。你可以将以太币存入 Tornado.cash 的智能合约中，然后使用另一个不同的地址提款。无论你何时将以太币提现到新地址，其他人都无法将存钱地址和提款地址挂钩，从而确保了绝对的隐私。在这种办法中，Tornado.cash 充当着一个代理，使用 zkSnark 证明来确保交易的 100% 匿名。

此外，Tornado Cash 是 100% 无需托管的，这意味着你拥有对存款的完全控制权。用户在存款时需要生成一个秘密（secret），并将其哈希值（也叫做承诺 commitment）连同存款的数额发送到 Tornado 的智能合约中。合约接收存款后，将其承诺也添加到合约的存款列表中。日后，当用户想要提款时，TA 需要向合约提供一个证明，证明 TA 拥有一个秘密，能和智能合约中的存款列表中未被花费的承诺对应起来。得益于 zkSnark 技术的加持，Tornado 可以在不公开这个秘密与哪笔资金相关的条件下完成匹配。Tornado 智能合约将检查这个证明，然后将存款金额转移到指定的提款地址。而对于外部的观察者来说，无从得知这笔提款来自哪笔存款。

当然了，使用 Tornado 还有一些额外的隐私辅助技巧，感兴趣可以阅读 Tornado 的 Medium。

Tornado 给那些需要隐私保护的用户提供了一个选择。假如前段时间 KuCoin 被盗的币一方面通过 DEX，另一方面通过 Tornado 洗币，这样的追踪难度就瞬间提高了，隐私确实是把双刃剑。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)。如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

本博客开通了 Donate Cafe 打赏，支持 Mixin Messenger、Blockin Wallet、imToken、Blockchain Wallet、Ownbit、Cobo Wallet、bitpie、DropBit、BRD、Pine、Secrypto 等任意钱包扫码转账。

<center>
    <div class="--donate-button"
         data-button-id="f8b9df0d-af9a-460d-8258-d3f435445075"
    ></div>
</center>

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)