---
published: true
author: Robin Wen
layout: post
title: DeFi 与授权
category: DeFi
summary: "这两天有社群用户表示，其参与 EOS 钻石（dmd.finance）流动性挖矿导致 10 万 USDT 被盗。经 TokenPocket 查证，被盗原因为用户在 2018 年 9 月将账户 owner 权限提交给「exchangename」账号导致的，与参与 DMD 挖矿无直接联系。波场、EOS 上的 DeFi 项目，不少人不敢入场，也是对这两条公链的鲁棒性存疑。特别是 EOS，公钥、私钥（还分 Owner Key 和 Active Key）的基础上，还有账号的概念，而且还可以更改一个账号的公钥（公私钥成对出现，相对应的私钥也修改了）。所以如果 EOS 的私钥泄漏，那别人可以很方便地修改公钥，然后账户你的资产就灰飞烟灭了。相比而言，以太坊、波场是不可能更改私钥的。DeFi 世界，赚多少钱都是其次的，核心是资产安全，希望你不要忽视这个根本的问题。"
tags:
  - DeFi
  - 以太坊
  - Ethereum
  - 波场
  - Tron
  - EOS
  - 区块链
---

`文/Robin`

***

![](https://cdn.dbarobin.com/el95w6s.png)

这两天有社群用户表示，其参与 EOS 钻石（dmd.finance）流动性挖矿导致 10 万 USDT 被盗。经 TokenPocket 查证，被盗原因为用户在 2018 年 9 月将账户 owner 权限提交给「exchangename」账号导致的，与参与 DMD 挖矿无直接联系。

以上的案例是发生在 EOS 网络上的。在「[DeFi 的风险](https://dbarobin.com/2020/09/06/defi-risk/)」一文中，讲到了一个因为授权导致资产被盗的案例。DeFi 离钱太近了，相信这样的事情会越来越多。相比中心化金融的银行，有非常多的安全措施保障资金的安全，抢劫银行、攻击银行系统，这些都被纳入刑法，一般人可不敢轻举妄动。但去中心化网络上，你甚至都不知道这个地址背后的主人是谁，在「私钥即正义」的信条下，只有掌握私钥，才真正掌握资产。

以太坊的 DeFi 应用实在是太多，通过审计的只有一部分。DeFi 应用向用户获取授权，很有可能存在过度授权的风险。授权是什么呢？授权本质上也是一笔链上交易，需要用户支付矿工费（Gas 费）。Dapp 开发者为了避免用户反复授权，一般会默认设置授权最大数量的代币给智能合约。但这样的处理也明显暴露风险，如果智能合约出现漏洞或合约管理员作恶，那么用户的代币将存在丢失的风险，这就是「Dapp 过度授权」带来的问题。

检查你的以太坊账号的授权情况，可以使用一个名叫「Token Allowance Checker」的工具，用了 dfuse 的 API 进行检测，网址是 tac.dappstar.io。

波场、EOS 上的 DeFi 项目，不少人不敢入场，也是对这两条公链的鲁棒性存疑。特别是 EOS，公钥、私钥（还分 Owner Key 和 Active Key）的基础上，还有账号的概念，而且还可以更改一个账号的公钥（公私钥成对出现，相对应的私钥也修改了）。所以如果 EOS 的私钥泄漏，那别人可以很方便地修改公钥，然后账户里的资产就灰飞烟灭了。相比而言，以太坊、波场是不可能更改私钥的。

在授权这个事，笔者有如下的建议：

* 体验 DeFi 项目，授权时建议认真看下都拿了什么权限；
* 体验 DeFi 项目，优先选择经过审计的项目；
* 建议选择知名度高的钱包，比如 MetaMask、imToken、TokenPocket；
* 体验 DeFi 项目的地址，最好是全新的，大额资产还是放硬件钱包；
* 如果有条件，可以使用 Trezor 或者 Ledger 之类的硬件钱包，再结合 MetaMask 使用，高阶用户都是这样玩的；
* 如果是 EOS 账号，定期检查自己的权限是否有问题；
* 有条件的情况下，可以考虑使用多签钱包管理资产。以太坊、波场、EOS 都支持多签，而且都可以找到对应的客户端。

DeFi 世界，赚多少钱都是其次的，核心是资产安全，希望你不要忽视这个根本的问题。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)
如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
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