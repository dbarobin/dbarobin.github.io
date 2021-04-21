---
published: true
author: Robin Wen
layout: post
title: "区块链资产安全攻略"
category: 区块链
summary: "在上一篇译文「译」保护你的个人资产中，有多达 20 条保护资产的建议。本文从个人经验出发，再为读者总结下「区块链资产安全攻略」。本文从钱包、密码、软件、备份、交易所、习惯几个方面给出一些指引。本篇文章写了比较长时间，想到哪里写到哪里，希望对读者有所帮助。安全是每个人都应该关心的话题，不管是否有技术背景。关心是意识问题，关键是时刻反思自己的安全策略是否有漏洞，如果有，需要及时 Fix。并且经常 Review，再 upgrade。"
tags:
- 区块链
- Blockchain
- 安全
- Security
- Crypto
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 8 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

在上一篇译文 [「译」保护你的个人资产](https://dbarobin.com/2017/12/13/blockchain-protect-your-funds) 中，有多达 20 条保护资产的建议。本文从个人经验出发，再为读者总结下「区块链资产安全攻略」。

本文从钱包、密码、软件、备份、交易所、习惯几个方面给出一些指引。

![hackernoon](https://cdn.dbarobin.com/O07H43e.jpg)

> 题图来自: © Preethi Kasireddy / Bitcoin, Ethereum, Blockchain, Tokens, ICOs / hackernoon.com

### 钱包
***

* 每个钱包在熟练使用之前，请用小额测试。
* 有条件购买硬件钱包。
* 在线钱包如果有 Google 二次验证的，一定要启用，此条规则适用于各交易所，比如币安、OKEx、火币、BigOne、OTCBTC、LocalBitcoins 等等。
* 尽量将自己的资产保留在钱包，交易所只是做一个暂存地。
* 私钥就是资产，切记不要在微信群、电报群、GitHub、博客等一切公共的地方泄漏。
* 尽可能使用离线版 MyEtherWallet，离线版的意思是从官方下载一个 zip 压缩包，可以在不联网的情况下操作钱包。
* 开启 Google 二次验证务必备份二维码以及识别码（密文），更换手机之后，可以手动在另一台手机使用二维码或者识别码恢复。
* 发起交易一定要再三确认好转账地址是否完全正确。
* 确定好 Token 应该存放在什么样的钱包。
* 选择钱包尽量选择开源的国外产品。
* 将资产分散到不同的钱包，不要把所有的 Token 都放入一个钱包，如果非要放在一个钱包，本文所有的安全策略请做到位。这里还有一个建议，如果是数额比较大的区块链资产，连地址（也就是公钥）都不在互联网暴露，一是个人资产保密，二是防止被盯上。

### 密码
***

* 需要设置访问密码的钱包，建议设置至少 64 位的密码，笔者设置了 128 位，并且包含大写字母、小写字母、数字和特殊符号。笔者使用 Python 编写了一个生成随机密码的脚本，本地运行，生成密码的数据字典是 abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 \
!#$%&'()*+,-./:;<=>?@[\]^_\`{|}~。
* 不要使用任何网站生成随机密码。不太确定这些没有开源的网站会怎么样玩弄你生成的密码。对于开发者而言，这只是简单的获取数据再入库的操作罢了。
* 区块链资产网站（比如各交易所）相关的密码，个人建议不要使用诸如 1Password、LastPass 的密码管理工具。

### 软件
***

* 不要下载来历不明的软件，不要访问诈骗或者钓鱼网站，特别是加密货币资产相关的，假如你在这些途径输入了私钥，从技术上来说，很容易就能获取。
* 推荐 Chrome 插件如下：[EtherAddressLookup](https://chrome.google.com/webstore/detail/etheraddresslookup/pdknmigbbbhmllnmgdfalmedcmcefdfn)、[MetaMask](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)、[MyEtherWallet](https://chrome.google.com/webstore/detail/myetherwallet/nlbmnnijcnlegkjjpcfjclmcfggfefdm)、[AdBlock](https://chrome.google.com/webstore/detail/adblock/gighmmpiobklfepjocnamgkkbiglidom)、[Adblock Plus](https://chrome.google.com/webstore/detail/adblock-plus/cfhdojbkjhnklbpkdaibdccddilifddb) 和 [ForceSSL](https://chrome.google.com/webstore/detail/forcessl/bljkjfinmjgppmnmbemglboechkjbhka)。
* 注意网站的 HTTPS 以及证书标识。
* 不要使用盗版软件，盗版软件被添加木马的概率太高。
* 链接不要乱点，乱点的下场就是电脑有可能被注入挖矿脚本，CPU 飙到 100%，还有可能电脑被控制，不开玩笑。
* Mac 或者 PC 安装一个具有流量监控的功能，若上传流量异常，需要及时关注，并且搞清楚是什么软件产生的，避免恶意软件上传本地文件至云端。
* 使用 Gmail 而不是国内邮箱，有条件可以使用 ProtonMail。
* 很多网站都需要 FQ 访问，确认自由上网的软件是不是可靠的，不要用免费的，切记。从 0 到 1 搭建这种服务也不是什么难事。
* 不要使用 Wi-Fi 万能钥匙，不要乱连 Wi-Fi。
* 使用 Mac 的同学可以看下 [macOS Security and Privacy Guide](https://github.com/drduh/macOS-Security-and-Privacy-Guide)。
* 所有软件（包括操作系统）第一时间更新到最新版，最新版可能会有各种问题，但软件没有 100% 完美的，更新做的事情如下：修复漏洞、添加 Feature、性能优化、架构调整等。如果硬件因为软件的更新变慢了，这是提醒你更新硬件了。
* iOS 千万不要越狱，有条件选择原生 Android 手机。
* 关于更多安全知识，可以在 [SecWiki](https://www.sec-wiki.com) 查看。
* PGP 是个好工具，建议合理利用，关于 PGP，可以阅读笔者的文章：[隐私极客 Privacy Geek 0x01 PGP](https://dbarobin.com/2019/05/02/privacy-geek-pgp)

### 备份
***

* 备份很关键，而且最好是冷备份，不要备份到任何云盘。
* 保护好个人手机电脑，谨防丢失，一旦丢失，iPhone 可以提前开启 Find My iPhone，远程抹掉数据。
* 个人 Mac Time Machine 备份开启加密备份，谨防个人移动硬盘丢失。更多关于备份的建议，可以参考 [备份与恢复的一点思考](https://dbarobin.com/2017/11/17/thoughts-on-backup-and-recovery)。

### 交易所
***

* 如果不是 100% 确认可靠，尽量不要找人担保交易，走交易所最好。目前大多数交易所都是币币交易，但也有场外交易平台可以选择。
* 尽量选择 Top 20 交易所，交易所名称可以在 **[这里](https://coinmarketcap.com/exchanges/volume/24-hour)** 查到。
* 通过 [CoinMarketCap](https://coinmarketcap.com) 查看对标现值，不要轻信某个 Token 的价格和市值。

### 习惯
***

* 人是贪婪的，合理把控自己的欲望，同时理解什么是风险。
* 需要对自己的每一个投资行为负责，不追涨杀跌，不懂不投，价值投资，长线投资。另外，场外能力赚钱更重要。
* 通过 Google 搜索区块链相关网站或者资讯，千万不要用某度。任何信息需要自我验证真实性，不要听风是风，听雨是雨。
* 交易所相关网站注册邮箱最好使用一个新的 Gmail，已经对外公布的 Gmail 尽量不用。根据网站的重要程度使用不一样的邮箱，并设置难度不同的密码，这也是一个好习惯。

### 附言
***

本篇文章写了比较长时间，想到哪里写到哪里，希望对读者有所帮助。安全是每个人都应该关心的话题，不管是否有技术背景。关心是意识问题，关键是时刻反思自己的安全策略是否有漏洞，如果有，需要及时 Fix。并且经常 Review，再 upgrade。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

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
