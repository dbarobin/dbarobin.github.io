---
published: true
author: Robin Wen
layout: post
title: Harvest 被攻击
category: DeFi
summary: "Harvest Finance 官方称，像其他套利经济攻击一样，此次攻击起源于一笔巨额闪电贷。攻击者通过多次操纵 Curve Y 池的价格，以耗尽 Harvest 的 fUSDT、fUSDC 池的资金。攻击者随后将资金转换为 renBTC 并套现。像其他闪电贷攻击操作一样，攻击者动作迅速，没有给平台反映的时间，连续 7 分钟端到端地进行攻击。攻击者以 USDT 和 USDC 的形式向开发者退回 2478549.94 美元。Harvest 表示这笔资金将通过快照按比例分配给受影响的储户。DeFi 依赖 TVL，然而每次安全事件，注定会导致用户疯狂的提现。从另一方面讲，黑客也是 DeFi 发展和进步的重要力量啊。"
tags:
  - DeFi
  - 以太坊
  - Ethereum
  - Harvest
---

`文/Robin`

***

![](https://cdn.dbarobin.com/kjnecg3.png)

10 月 26 日，**Harvest Finance** 项目遭受闪电贷攻击，损失超过 400 万美元。

Harvest Finance 官方称，像其他套利经济攻击一样，此次攻击起源于一笔巨额闪电贷。攻击者通过多次操纵 Curve Y 池的价格，以耗尽 Harvest 的 fUSDT、fUSDC 池的资金。攻击者随后将资金转换为 renBTC 并套现。像其他闪电贷攻击操作一样，攻击者动作迅速，没有给平台反映的时间，连续 7 分钟端到端地进行攻击。攻击者以 USDT 和 USDC 的形式向开发者退回 2478549.94 美元。Harvest 表示这笔资金将通过快照按比例分配给受影响的储户。

随后慢雾也给出被黑的具体过程。此次攻击主要是 Harvest Finance 的 fToken(fUSDC、fUSDT...) 在铸币时采用的是 Curve y 池中的报价 (即使用 Curve 作为喂价来源)，导致攻击者可以通过巨额兑换操控预言机的价格来控制 Harvest Finance 中 fToken 的铸币数量，从而使攻击者有利可图。

Twitter [@jiecut42](https://twitter.com/jiecut42/status/1320603188219809793) 总结了黑客的这一系列操作，都对那些平台或个人产生了收入。目前来看黑客获利 2400 万美元左右；通过 Uniswap 进行套现，Uniswap LP 收入 600 万美元；黑客向 Harvest 的开发者返还了 247 万余美元；通过 Curve 的流动性池完成的这一波套利操作，因此 Curve LP 大约可获得 100 万美元收入；调用合约、转账等产生的 ETH Gas 手续费 10 万美元；最后通过 RenBTC 兑现，RenVM 手续费 2 万美元。

受此事件影响，不少用户担心资产安全，纷纷提现转账，加上黑客操作及套现行为，根据 Debank 数据显示，去中心化交易平台（DEX）的单日成交量创历史新高，超过 32.5 亿美元。同时，Harvest 和 Curve 的总锁仓量（TVL）开始下降，Curve 24 小时锁仓量下跌 25.91 %，Harvest 24 小时锁仓量下跌 46.7%。

此次黑客的攻击手法非常高明，使用了 renBTC 进行洗钱，还使用了 Tornado 匿名化自己的攻击地址。前几天笔者才提到 Tornado，这次黑客就完美地用上了。

DeFi 依赖 TVL，然而每次安全事件，注定会导致用户疯狂的提现。从另一方面讲，黑客也是 DeFi 发展和进步的重要力量啊。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

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

***

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)