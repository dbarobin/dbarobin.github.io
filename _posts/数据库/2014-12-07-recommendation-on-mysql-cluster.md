---
published: true
author: Robin Wen
layout: post
title: "MySQL集群建议"
category: 数据库
summary: "基于以上分析，我给你的建议是：主主复制被动模式+Slave+MMM。"
tags:
- 数据库
- Database
- MySQL
- Cluster
- 集群
---

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**问题**

> 现想建立一个数据中心，包括运维采集信息、业务数据、其他业务数据等，建立一个集群搞定，数据量大，写入非常多，查询也非常多。请教。
> 我应该建什么样的集群合适，面对高并发，扩展性等问题。是否有什么建议，谢谢。
> 我考虑cluster，因为可以添加很多节点，这样各种业务的各种数据，我可以分布到节点上去，把他们查询比较多的，做成NDB引擎。

**我的分析**

你好：

看了你的问题，从我的认知角度，做如下分析：

* 基于NDB的MySQL Cluster很不可靠，绝大多数公司都已经弃用。既然Cluster经不起市场检验，我们也没有必要使用它；
* 而目前企业使用较多的是复制，主主复制使用较多。主主复制又有两种模式，一是主动，另一种是被动。主动模式会引起数据冲突和不一致，而被动模式不会，因为只有一台对外提供读写功能，另一台只提供读功能；
* 我们可以在主主复制被动模式的基础上，再添加Slave，这样可以最大化的保证数据不丢失；
* 最后，最好加一个高可用，MMM就是很好的选择，它能完成故障自动转移等等。
我的建议

基于以上分析，我给你的建议是：主主复制被动模式+Slave+MMM。多个业务也可以部署在同一套系统上，如果有条件，业务可以分开部署，但这样成本会比较高。

架构示例图：

![Recommendation on MySQL Cluster](https://cdn.dbarobin.com/4IIe1YH.jpg)

题图来自CNBlog。

供你参考。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.pro/cn/register/?ref=11190872](https://accounts.binancezh.pro/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

原文地址：微信公众号文章

题图来自：<a href="http://cdn.marketplaceimages.windowsphone.com/v8/images/a94f0340-4e3d-428b-ba1d-7d5e21eed186?imageType=ws_icon_large" target="_blank"><img src="https://cdn.dbarobin.com/Tnv4yD7.png" title="CNBLOG" border="0" alt="CNBLOG" height="16px" width="16px" /></a>

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
