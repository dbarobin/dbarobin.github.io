---
published: true
author: Robin Wen
layout: post
title: "Python 统计个人加密货币资产"
category: 区块链
summary: "对个人每一笔投资进行复盘是非常有必要的。投资过后，复盘看到的那些贪婪、恐惧、紧张、心动、烦躁、欢喜、得瑟、满意……无数种情绪一览无遗。只有通过复盘，才能知道哪笔投资有问题，哪笔投资还有改善的空间。秉承这种原则，笔者有一份区块链资产表格，里面详细记录了各种币种的分布，包含币种、全称、存储位置、数量、购买渠道、购买时间、操作记录、备注等等。另外，同一份表格其他 Sheet 记录了不同交易平台的订单记录，交易时间、类型、交易对、单价、数量、总价、手续费等等。一份表格，就可以窥探所有的投资逻辑。笔者建议进行区块链投资的读者也进行这样的梳理，绝对有价值。摆在笔者面前的一大难题，区块链资产比较分散，各类钱包、各大交易所，同一币种在不同交易所价格还不一样。怎么样精准快速统计个人资产，这还是个问题。"
tags:
- 区块链
- Blockchain
- Crypto
---

`文/Robin`

> 本文由币乎社区（bihu.com）内容支持计划奖励。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 7 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

### 背景
***

对个人每一笔投资进行复盘是非常有必要的。投资过后，复盘看到的那些贪婪、恐惧、紧张、心动、烦躁、欢喜、得瑟、满意……无数种情绪一览无遗。只有通过复盘，才能知道哪笔投资有问题，哪笔投资还有改善的空间。秉承这种原则，笔者有一份区块链资产表格，里面详细记录了各种币种的分布，包含币种、全称、存储位置、数量、购买渠道、购买时间、操作记录、备注等等。另外，同一份表格其他 Sheet 记录了不同交易平台的订单记录，交易时间、类型、交易对、单价、数量、总价、手续费等等。一份表格，就可以窥探所有的投资逻辑。笔者建议进行区块链投资的读者也进行这样的梳理，绝对有价值。

摆在笔者面前的一大难题，区块链资产比较分散，各类钱包、各大交易所，同一币种在不同交易所价格还不一样。怎么样精准快速统计个人资产，这还是个问题。

![blockchain](https://cdn.dbarobin.com/44SYx6s.jpg)

> 题图来自: © JASON / Hoard Joins INV Fintech Accelerator / hoardinvest.com

### 调研
***

笔者进行了一番调研，可以通过 [MyToken App](https://www.mytoken.io) 进行汇总统计。但一看 MyToken 的设计，需要注册账户，也就是说你添加的所有资产，从他们的后台都可以看到，对于隐私比较看重的笔者自然是无法忍受。况且 MyToken 还存在价格更新频率不及时的问题，总之使用一段时间，体验不是特别友好。另外有一个网站叫做 [COUNT MY CRYPTO](http://www.countmycrypto.com) 也可以统计，不过需要逐个添加，效率太慢。对于一个 Geek 来说，有没有更快更好的方法呢？答案是有，自己造轮子呗。

去 GitHub 找了一遍，有一个叫做 [coinmarketcap](https://github.com/mrsmn/coinmarketcap) 的 Python 库可以实现以 CNY 形式查询币种的现值。不过试用了下，还是决定自己调用 CoinMarketCap 的原生 API 实现。

我们来看下 CoinMarketCap 是什么网站。

> CoinMarketCap 是一个网站，它追踪大多数已经触及市场的山寨币，以及比特币，并向用户展示每枚币的美元和比特币的现值。

大多数查看行情的网站都是对标 CoinMarketCap 的数据，所以 CoinMarketCap 提供的数据极具参考价值。查看了 CoinMarketCap 的 API，简洁明了，实现思路也已经形成，于是撸起袖子就是干。

### 解决
***

查看 [CoinMarketCap API](https://coinmarketcap.com/api/)，我们可以看到提供的 API 是 Public API，也即是不需要私钥认证即可请求。主要提供三类方法：Ticker、Ticker (Specific Currency)、Global Data。我们需要调用的是 Ticker 和 Ticker (Specific Currency)。

Ticker (Specific Currency) 的调用方法如下：

> https://api.coinmarketcap.com/v1/ticker/bitcoin/?convert=CNY

我们尝试浏览器访问，得到如下 JSON 格式的数据：

``` json
[
    {
        "id": "bitcoin",
        "name": "Bitcoin",
        "symbol": "BTC",
        "rank": "1",
        "price_usd": "16808.0",
        "price_btc": "1.0",
        "24h_volume_usd": "14098700000.0",
        "market_cap_usd": "281370122000",
        "available_supply": "16740250.0",
        "total_supply": "16740250.0",
        "max_supply": "21000000.0",
        "percent_change_1h": "2.35",
        "percent_change_24h": "-1.07",
        "percent_change_7d": "12.19",
        "last_updated": "1513241052",
        "price_cny": "111100.88",
        "24h_volume_cny": "93192407000.0",
        "market_cap_cny": "1859856506420"
    }
]
```

> 如果读者对以上 JSON 格式数据感兴趣，可以自行琢磨下。

也就是说，bitcoin 表示 `ID`，`convert=CNY` 表示转换为人民币。问题是这个 ID 怎么获取呢。

笔者查看 Ticker 方法，发现如下的调用可以获取所有的 Ticker（limit=0 表示没有限制）。

> curl -G https://api.coinmarketcap.com/v1/ticker/\?limit\=0 > ~/Downloads/ticker

通过 Terminal 终端请求之后，得到了一个叫做 ticker 的文件，使用 sed + vim 进行文本处理后，可以得到 1355 个 Ticker 的 ID 的 `ticker_id.txt ` 文本文件，也即是说目前 CoinMarketCap 收录了 1355 种 Token。笔者对 ticker 文件进行二次处理，得到了一个叫做 `ticker_id_full.txt` 的文件，除了 Ticker ID 之外，还有 name 和 symbol。

> 笔者注：1355 种 Ticker 是当前的数据，Ticker 数量每天都在增加。后续笔者也会去维护 GitHub repo 总的 ticker_id.txt 和 ticker_id_full.txt 文本文件。

好了，至此就是使用 Python 实现统计区块链资产了。

笔者建立了一个叫做 `cryptocurrency.txt` 的文本，第一列是 Token 的 ID，第二列是 Token 的数量，列之间以空格进行分隔，文件示例如下：

``` python
bitcoin 10000
ethereum 10000
eos 10000
zcash 10000
```

接下来就是循环读取，requests 请求获取 JSON 中的 `price_cny` 属性，然后累加，核心代码如下：

``` python
'''
Coinmarketcap requests.

Args:
    ticker: cryptocurrency symbol.
    convert: which currency to convert.

Returns:
    req: coinmarketcap requests.
'''
def Coinmarketcap(ticker, convert):
    url = ('https://api.coinmarketcap.com/v1/ticker/%s/?convert=%s') \
    % (ticker, convert)
    req = requests.get(url)
    return req

'''
Count my cryptocurrency.

Args:
    file: cryptocurrency file.
    cryptocurrency.txt includes my cryptocurrency detail.
    This file seperated by one space.
    First column: tikcker id.
    Second column: ticker nums.

Returns:
    price_all: All cryptocurrency convert to CNY.
'''
def CountCryptoCurrency(file):
    # counter.
    price_all = 0
    for line in open(file):
        columns = line.split(' ')
        ticker = columns[0]
        nums = columns[1]
        req = Coinmarketcap(ticker, 'CNY')
        for item in req.json():
            price_cny = item['price_cny']
            price_total = float(price_cny) * float(nums)
            price_all = float(price_all) + float(price_total)

    return price_all
```

脚本编写完成后，执行 `python cryptocurrency.py`，就会得到如下输出：

![cryptocurrency](https://cdn.dbarobin.com/3xz1ahK.png)

看到这么多现值，是不是很酷啊，有时产生下错觉也挺好的。

### 资源
***

相关代码已提交到 GitHub，读者可以自行 clone 玩下，链接：

* [mycrypto](https://github.com/dbarobin/mycrypto)

### 小结
***

通过本文提供的方法得到区块链资产现值，然后和投入资产进行对比，就能计算出盈利率，每个人都应该对自己的投资行为负责。

解决问题需要执行力的，如果觉得有价值，思考成熟之后就去做。在解决问题的过程中，就会发现自己的能力就这样慢慢提升了，希望本文对读者有所帮助。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
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
