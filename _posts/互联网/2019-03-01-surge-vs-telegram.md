---
published: true
author: Robin Wen
layout: post
title: Surge Telegram 优化
category: 互联网
summary: Telegram 是一款著名的 IM，笔者使用频率相当高。Telegram 相比其他 IM 有什么优势，读者可以点击此处阅读。怎么样优化 Telegram 消息加载速度？方法很简单，编辑正在使用的 Surge 配置文件，添加如下的代码，然后 Reload 配置文件即可。根据实测，优化前后，某个未读消息超过上千条的群，点击未读消息秒级极速加载，图片、视频、音频加载速度也得到显著提升。当然，笔者懒，没法给出具体数据。读者不妨试试这个技巧，感受下比微信还流畅的聊天是什么个体验。
tags:
  - 互联网
  - Surge
  - Telegram
---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

Telegram 是一款著名的 IM，笔者使用频率相当高。Telegram 相比其他 IM 有什么优势，读者可以点击 [此处](https://telegram.org/faq) 阅读。

![](https://cdn.dbarobin.com/2efMSBS.png)

> 题图来自: © IFTTT / Telegram is on IFTTT / ifttt.com

然而因为众所周知的原因，Telegram 在某国无法正常使用，这就需要一种特别的上网方式。服务有了，还需要工具，Surge 无疑是众多工具中的佼佼者（**特此声明：Surge 是一款网络调试工具**）。

Surge 支持自定义配置，默认情况下，即使用了特殊的上网方式，Telegram 流畅度也没有想象中乐观，特别是对于笔者这种加了无数群、订阅了无数 Channel 的典型用户来说。

怎么样优化 Telegram 消息加载速度？方法很简单，编辑正在使用的 Surge 配置文件，添加如下的代码，然后 Reload 配置文件即可。

``` bash
# Telegram
DOMAIN-SUFFIX,telegra.ph,Proxy,force-remote-dns
DOMAIN-SUFFIX,telegram.org,Proxy,force-remote-dns
DOMAIN-KEYWORD,telegram,Proxy,force-remote-dns
PROCESS-NAME,Telegram,Proxy,force-remote-dns
USER-AGENT,Telegram*,Proxy,force-remote-dns
USER-AGENT,ph.telegra.Telegraph*,Proxy,force-remote-dns
IP-CIDR,43.249.72.0/24,Proxy,no-resolve
IP-CIDR,64.30.224.0/24,Proxy,no-resolve
IP-CIDR,91.108.4.0/22,Proxy,no-resolve
IP-CIDR,91.108.56.0/22,Proxy,no-resolve
IP-CIDR,109.239.140.0/24,Proxy,no-resolve
IP-CIDR,117.18.232.0/21,Proxy,no-resolve
IP-CIDR,149.154.160.0/20,Proxy,no-resolve
IP-CIDR,194.14.177.0/24,Proxy,no-resolve
IP-CIDR,194.68.28.0/23,Proxy,no-resolve
IP-CIDR,194.68.30.0/24,Proxy,no-resolve
IP-CIDR,194.132.172.0/24,Proxy,no-resolve
IP-CIDR,194.132.162.0/24,Proxy,no-resolve
IP-CIDR,193.182.7.0/24,Proxy,no-resolve
IP-CIDR,216.58.197.100/32,Proxy,no-resolve
```

根据实测，优化前后，某个未读消息超过上千条的群，点击未读消息秒级极速加载，图片、视频、音频加载速度也得到显著提升。当然，笔者懒，没法给出具体数据。读者不妨试试这个技巧，感受下比微信还流畅的聊天是什么个体验。

–EOF–

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
