---
published: true
author: Robin Wen
layout: post
title: Mixin 大群运维完全教程
category: 区块链
summary: 笔者之前发表了 Mixin 大群部署完全教程，相信读者对如何部署大群有一定的了解。然而让一个大群运转起来只是万里长征第一步，还有诸如安全、运维、备份、监控等方面需要考虑。本文从这些方面讲解如何让大群稳定运行。服务运行和服务稳定运行，这两者之间差了很远。安全、运维、监控等，是为了让服务能有全方位的保障。
tags:
  - 区块链
  - Blockchain
  - Mixin

---

`文/robin`

这是「区块链技术指北」的第 **64** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

| 版本 | 更新历史 | 更新时间 | 备注 |
| :------: | :------: | :------: | :------: |
| v1.0 | 文档初稿 | 2019/06/11 12:32:56 | 全网公开 |
| v1.1 | 修改备份脚本 | 2019/06/27 10:37:56 | crontab 不能调用 pg_dumpall |

## 一 前言
***

笔者之前发表了 [Mixin 大群部署完全教程](https://dbarobin.com/2019/05/19/mixin-super-group)，相信读者对如何部署大群有一定的了解。

然而让一个大群运转起来只是万里长征第一步，还有诸如安全、运维、备份、监控等方面需要考虑。本文从这些方面讲解如何让大群稳定运行。

![](https://cdn.dbarobin.com/fIWTrII.png)

> 题图来自: © Mixin Network / All Cryptocurrencies Accepted Here / bitcointalk.org

## 二 安全
***

因为大群服务器对外开放，如果没有使用负载均衡或者内网隔离之类的技术，服务器是暴露在公网的。这样的服务器，有无数的黑客盯着，他们可能会尝试暴力破解服务器 root 密码，或者挖掘各种各样的漏洞尝试攻击。保障大群稳定运行，安全就是非常重要的考虑因素。

### 2.1 SSH 配置
***

默认的 SSH 端口是 22，黑客在尝试暴力破解 root 密码时优先会选择这个端口。于是，SSH 的配置也是很重要的一点。如果我们使用 SSH 公钥 + 密码的形式进行验证，也就从源头避免了暴力破解。

那如何配置呢？

首先我们要本地（可以是 Mac，也可以是 PC）终端生成一个公私钥对：

``` bash
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa_mixin -C "Mixin"
```

需要注意的是，我们需要为公私钥对生成一个复杂的密码（Enter passphrase 交互输入）。复杂的密码可以保存在如 1Password 之类的软件。这样做的好处是，即使公钥泄漏 + 服务器 IP 泄漏的情况下，服务器也不会被操控。

生成之后，在 ~/.ssh 目录 有两个文件，一个是 `id_rsa_mixin`，另一个是 `id_rsa_mixin.pub`。`id_rsa_mixin` 存储的是私钥，`id_rsa_mixin.pub` 存储的是公钥。

本地生成完成之后，需要在远程服务器做相应的配置，配置如下（假设远程登录的用户是 robin）：

``` bash
# 创建相关目录和文件
$ useradd robin
$ mkdir -p /home/robin/.ssh
$ touch /home/robin/.ssh/authorized_keys

# 修改权限
$ chown robin:robin -R /home/robin/.ssh
$ chmod 600 /home/robin/.ssh/authorized_keys

# 写入个人 Pub Key，也就是本地 id_rsa_mixin.pub 内容
$ vim /home/robin/.ssh/authorized_keys

$ vim /etc/ssh/sshd_config

# SSH 默认端口改成非标准非常用端口，建议 10000 以上
Port xxxxx
# 禁止密码登录
PasswordAuthentication no
# 禁止空密码登录
PermitEmptyPasswords no
# 禁止 root 用户登录
PermitRootLogin no
# 开启 RSA 验证
RSAAuthentication yes
PubkeyAuthentication yes

# 确认配置
$ grep -e Port -e PasswordAuthentication \
-e PermitEmptyPasswords -e PermitRootLogin \
-e RSAAuthentication -e PubkeyAuthentication \
/etc/ssh/sshd_config | grep -v "^#" \
| grep -v "RhostsRSAAuthentication"
```

修改完成之后，保留当前会话。然后到云服务器后台安全组放行自定义的 SSH 端口。

本地 SSH 客户端新建一个 Tab，重新连接服务器，然后再重启 SSH 服务，这样做的目的是，避免因 SSH 配置错误，导致 SSH 服务重启后无法登录服务器。

``` bash
# 重启 SSH 服务
$ systemctl restart sshd.service
```

### 2.2 放行指定端口
***

放行指定端口，也就是传统意义的防火墙。如果服务器部署在云服务商，后台已经有比较完善的防火墙管理规则，无需使用 `iptables` 配置。

按照之前 [Mixin 大群部署完全教程](https://dbarobin.com/2019/05/19/mixin-super-group) 部署的大群，只需要开放 SSH 端口、80、443 即可。

### 2.3 定期更换机器人配置信息
***

机器人配置包含 client_id、client_secret、session_asset_pin、pin_token、session_id 和 session_key。除了 client_id 没法修改之外，其余均可重新生成。可以制定相应的策略，每一个月或者每季度修改一次，避免因误操作导致机器人配置泄漏，造成一些不必要的困扰。

重构的机器人代码，修改配置无需重新编译，重启 Service 服务即可。

### 2.4 清除 history 信息
***

Linux 是多任务多用户系统，每一个用户登录进去后，终端执行的命令，服务器都有记录，用户执行 history 即可查看。如果服务器被攻破，黑客执行 history 可以查看所有历史命令，如果 history 暴露了一些密钥、密码或者敏感信息，可能其他服务会被二次攻破。所以稳妥的办法是，做到无痕登录服务器。

配置方法也很简单，vim 编辑 `/etc/profile`，加入如下内容：

``` bash
$ vim /etc/profile

history -c
rm -f ~/.bash_history
```

### 2.5 其他

本文指给出必要的安全配置，其他更细的安全配置，可以参考：

* [加强 Linux 服务器安全的 20 项建议](https://www.sysgeek.cn/linux-server-security-tips/)
* [Linux 服务器安全简明指南](http://os.51cto.com/art/201712/559821.htm)
* [An Introduction to Securing your Linux VPS](https://www.digitalocean.com/community/tutorials/an-introduction-to-securing-your-linux-vps)

## 三 备份
***

### 3.1 数据库备份
***

大群采用 PostgreSQL 数据库，用户、历史消息、红包等信息都存在这里。云服务器可能因为一些不可控因素，导致服务器异常。为了维持大群的稳定运行，数据备份必不可少。

PostgreSQL 备份教程如下：

首先，安装备份相关依赖（pigz 用于压缩备份文件，亲测可以减少 70%～80% 的备份存储空间）：

``` bash
$ apt-get -y install pigz
```

接着，切换到 postgres 用户，创建备份目录：

``` bash
$ sudo -i -u postgres
$ mkdir -p ~/postgres/backups
```

然后，编写备份脚本，内容如下：

``` bash
$ vim ~/postgres/backups/backup.sh
```

``` bash
#!/bin/bash
#
# Copyright © 2019 ExinPool <robin@exin.one>
#
# Distributed under terms of the MIT license.
#
# Desc: Mixin super group PostgreSQL backup script.
# User: Robin@ExinPool
# Date: 2019-6-27
# Time: 10:15:18

BACKUP_DIR="/var/lib/postgresql/postgres/backups"
BACKUP_TIME=`date '+%Y%m%d%H%M%S'`

# pg_dumpall 表示备份所有数据库，通过这个备份就可以完整地恢复
pg_dumpall | pigz > ${BACKUP_DIR}/${BACKUP_TIME}-postgres-backup.sql.gz

if [ $? -eq 0 ]
then
    echo "`date '+%Y-%m-%d %H:%M:%S'` `whoami` INFO Mixin super group PostgreSQL backup successfully."
else
    echo "`date '+%Y-%m-%d %H:%M:%S'` `whoami` ERROR Mixin super group PostgreSQL backup failed."
fi
```

接着，创建定时任务：

``` bash
crontab -e

# 0 3 * * * 表示每天凌晨 3 点备份，这个频率可以自定义
0 3 * * * nohup bash /var/lib/postgresql/postgres/backups/backup.sh >> /var/lib/postgresql/postgres/backups/backup.log 2>&1 &

crontab -l
```

数据备份完成之后，需要定期检查备份情况以及做备份恢复测试。考虑单节点云服务器服务可能中断，读者可以将数据库备份推送到另外一台服务器或者存储到云服务商的对象存储。

### 3.2 配置备份
***

大群相关配置文件包含用于后端的 `config.yaml`、前端的 `env.prod.sh`、Nginx 域名。读者可以每个月备份一次。

### 3.3 系统备份
***

读者可以使用云服务商提供的功能，定期对系统做镜像（也就是快照），方便服务器异常时可以快速恢复服务。

## 四 监控
***

### 4.1 服务器监控
***

如果使用了云服务商，后台集成了服务器的监控，比如 CPU、内存、硬盘、内网带宽、外网带宽等，读者可以定期登录后台观察分析，也可以设置告警阈值，及时知晓服务器情况。如果 CPU 负载过高、内存一直飙升、流量异常、硬盘空间占用过高等，需要保持关注并及时处理。

### 4.2 日志监控
***

日志监控主要有两方面，一是执行 `journalctl -f ` 查看 systemd 的日志，二是服务器系统日志（/var/log/kern.log、/var/log/syslog 等）。读者可以定期查看或者写解析脚本分析异常，笔者在此不再赘述。

## 五 小结
***

服务运行和服务稳定运行，这两者之间差了很远。安全、运维、监控等，是为了让服务能有全方位的保障。

如果读者遇到任何问题，欢迎交流，[关于](https://dbarobin.com/about) 页面有我的联系方式。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://www.binancezh.com/cn/register/?ref=11190872](https://www.binancezh.com/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

「区块链技术指北」同名 **知识星球**，二维码如下，欢迎加入。

![区块链技术指北](https://cdn.dbarobin.com/3YzonTR.png)

「区块链技术指北」相关资讯渠道：

> 「区块链技术指北」同名知识星球，[https://t.xiaomiquan.com/ZRbmaU3](https://t.xiaomiquan.com/ZRbmaU3)
> 官网，[https://chainon.io](https://chainon.io)
> 官方博客，[https://blog.chainon.io](https://blog.chainon.io)
> 官方社区，[https://bbs.chainon.io](https://bbs.chainon.io)
> Telegram Channel，[https://t.me/chainone](https://t.me/chainone)
> Twitter，[https://twitter.com/bcageone](https://twitter.com/bcageone)
> Facebook，[https://www.facebook.com/chainone.org](https://www.facebook.com/chainone.org)
> 新浪微博，[https://weibo.com/BlockchainAge](https://weibo.com/BlockchainAge)

同时，本系列文章会在以下渠道同步更新，欢迎关注：

> 「区块链技术指北」同名微信公众号（微信号：BlockchainAge）
> 个人博客，[https://dbarobin.com](https://dbarobin.com)
> 知乎，[https://zhuanlan.zhihu.com/robinwen](https://zhuanlan.zhihu.com/robinwen)
> 简书，[https://www.jianshu.com/c/a37698a12ba9](https://www.jianshu.com/c/a37698a12ba9)
> Steemit，[https://steemit.com/@robinwen](https://steemit.com/@robinwen)
> Medium，[https://medium.com/@robinwan](https://medium.com/@robinwan)
> 掘金，[robinwen@juejin.im](https://juejin.im/user/5673ccae60b2260ee435f89a/posts)
> EOS LIVE，[https://eos.live/user/robin](https://eos.live/user/robin)
> 币乎，[https://bihu.com/people/22207](https://bihu.com/people/22207)

原创不易，读者可以通过如下途径打赏，虚拟货币、美元、法币均支持。

> BTC: 3QboL2k5HfKjKDrEYtQAKubWCjx9CX7i8f
> ERC20 Token: 0x8907B2ed72A1E2D283c04613536Fac4270C9F0b3
> PayPal: [https://www.paypal.me/robinwen](https://www.paypal.me/robinwen)
> 微信打赏二维码

![Wechat](https://cdn.dbarobin.com/SzoNl5b.jpg)

–EOF–

版权声明：[自由转载-非商用-非衍生-保持署名（创意共享4.0许可证）](http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)
