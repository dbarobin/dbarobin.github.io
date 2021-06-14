---
published: true
author: Robin Wen
layout: post
title: 比特币 Taproot 升级
category: 比特币
summary: "2021 年 6 月 13 日，根据 taproot.watch 数据，比特币全网支持 Taproot 的矿池算力已超过 90%，为 98.95 %，已达到锁定升级的最低要求，将于 11 月激活。当前支持 Taproot 的矿池包括 AntPool、F2Pool、Poolin、BTC.com、ViaBTC 等。Taproot 为比特币网络带来的好处是巨大的。它带来的更高效、更快速的交易将使网络更易于接入和运维，更多的节点将使网络更加安全和稳健，同时修补了手续费漏洞并增强隐私保护。对于 Bitcoin Hodler 来说，无疑是个利好。"
tags:
  - 比特币
  - Bitcoin
  - Taproot
---

`文/Robin`

***

![](https://cdn.dbarobin.com/eie0plc.png)

2021 年 6 月 13 日，根据 taproot.watch 数据，比特币全网支持 Taproot 的矿池算力已超过 90%，为 98.95 %，已达到锁定升级的最低要求，将于 11 月激活。当前支持 Taproot 的矿池包括 AntPool、F2Pool、Poolin、BTC.com、ViaBTC 等。

Taproot 将改善比特币的运作方式，它改进了交易签名方式，从中删除了例如交易中是否调用了智能合约这类不必要的信息，并降低了发送交易所需的总体数据量。这对隐私保护具有积极意义，并能将更多的复杂交易打包进一个区块里，从而提高比特币网络的整体效率。

Taproot 版本更新是向下兼容的，这意味着普通用户无需做任何事情就可以直接使用新版本，但矿工则需要对软件进行升级。

Taproot 引入了一种新的签名方案 Schnorr 签名，相比起 SegWit 版本 0 中的交易管理方式，这种签名方式减小了交易的数据量，从而使每个区块能包含更多的交易。 这相当于增加了区块的容量，却不会增加同步网络数据所需的带宽或增加存储成本，确保普通用户仍然可以负担得起参与比特币网络治理和运行的成本。

Taproot 对于硬件钱包来说也是利好，Taproot 在交易发送中会更高效，因为在不使用 Taproot 的交易中，平均交易输入和交易输出的数量都很高，硬件钱包要针对每一个交易输入输出做很多数据处理和计算。而使用 Taproot 后，硬件钱包只需处理聚合后的 1 个公钥和 1 个签名就能完成交易了。

随着比特币用户逐渐在钱包中使用更多样化的地址类型，或者使用例如闪电网络及其他类型的智能合约等二层网络方案，识别出某个地址进行过某种特定的交易操作就变得更加容易了，这显然对用户隐私造成威胁。Schnorr 签名技术则不再需要于交易中展示智能合约的脚本，这使所有的交易看起来都长得一模一样，也就提高了交易的隐私性。此外，也会让多签交易手续费更低。

Taproot 为比特币网络带来的好处是巨大的。它带来的更高效、更快速的交易将使网络更易于接入和运维，更多的节点将使网络更加安全和稳健，同时修补了手续费漏洞并增强隐私保护。对于 Bitcoin Hodler 来说，无疑是个利好。

***

我是区块链罗宾，博客 [dbarobin.com](https://dbarobin.com/)。如果您想和我交流，我的微信: **Wentasy**

![](https://cdn.dbarobin.com/v4yywe2.png)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

* 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
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