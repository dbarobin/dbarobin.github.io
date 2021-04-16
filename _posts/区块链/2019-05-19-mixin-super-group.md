---
published: true
author: Robin Wen
layout: post
title: Mixin 大群部署完全教程
category: 区块链
summary: >-
  群聊是社交软件非常重要的功能之一，Mixin Messenger 当然也提供了群聊，不过美中不足的是，通过 Mixin Messenger 创建的群最多支持
  256 人。Mixin 大群是基于 Mixin Bot 实现的。笔者简单画了个架构图。域名解析使用 Nginx，其中有大群主域名和 API
  域名，主域名指向前端代码，前端右 Node.js 实现。API 域名指向后端，后端由 Go 实现，有两个进程，一个是处理 HTTP 请求的，一个是处理
  Message 请求的。其中 Go 的进程还会和 Mixin 的 API 交互。此外，数据库采用 PostgreSQL。Mixin
  大群最初的版本，数据库采用的是 Google 的 Spanner，但 Spanner 只有选择 Google Cloud，而且价格偏高。
tags:
  - 区块链
  - Blockchain
  - Mixin
comments:
  - author:
      type: github
      displayName: Kuri-su
      url: 'https://github.com/Kuri-su'
      picture: 'https://avatars3.githubusercontent.com/u/22676438?v=4&s=73'
    content: >-
      &#x4F60;&#x597D;,&#x6BD4;&#x8F83;&#x597D;&#x5947;,
      &#x67B6;&#x6784;&#x56FE;&#x662F;&#x7528;&#x4EC0;&#x4E48;&#x8F6F;&#x4EF6;&#x753B;&#x7684;&#x4E2B;
      :&gt;
    date: 2019-05-25T04:35:29.163Z
  - author:
      type: github
      displayName: dbarobin
      url: 'https://github.com/dbarobin'
      picture: 'https://avatars1.githubusercontent.com/u/3949252?v=4&s=73'
    content: >-
      @Kuri-su &#x7528; OmniGraffle &#x753B;&#x7684;&#xFF0C;Omni
      &#x5168;&#x5BB6;&#x6876;&#xFF0C;&#x1F602;
    date: 2019-05-26T07:49:36.276Z

---

`文/Robin`

这是「区块链技术指北」的第 **63** 篇文章。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

| 版本 | 更新历史 | 更新时间 | 备注 |
| :------: | :------: | :------: | :------: |
| v1.0 | 文档初稿 | 2019/05/19 12:49:38 | 全网公开 |
| v2.0 | 更改后端启动方式 | 2019/06/06 17:27:57 | 重构后的代码，大群维护更友好 |
| v2.1 | 增加机器人后台配置 | 2019/06/10 12:10:17 | home uri 和 OAuth redirect uri |
| v2.2 | 增加修改时区 | 2019/06/11 17:43:29 | 部署在国内服务器的读者尤其注意 |
| v2.3 | 修复创建数据库无法找到 schema.sql | 2019/06/27 10:51:29 | 对内容也做了小幅调整 |
| v3.0 | 前端更换为 vue 版本 | 2019/08/01 19:52:18 | 大版本更新 |
| v3.1 | 更改为 go module 方式编译 | 2019/08/05 17:38:20 | go module 将成为标准 |
| v3.2 | 去除 GOPATH 路径 | 2019/08/06 16:38:30 | 同时做小幅修正 |
| v3.3 | 添加打赏功能 | 2019/08/28 12:25:19 | 注意修改 config.yaml |
| v3.4 | 更改限制发送消息频率 | 2019/09/19 17:51:29 | limit_message_duration 参数 |
| v3.5 | 增加 mixin info bot | 2019/11/19 16:12:18 | 便捷获取 UUID |
| v3.6 | 更新 Go 版本 | 2019/12/05 11:29:19 | 更新到 1.13.5 |
| v3.7 | 更新 nodejs 版本 | 2019/12/18 14:34:19 | 更新到 node v13 |
| v3.8 | 更新 schema.sql 位置 | 2019/12/18 15:349:38 | 解决权限问题 |
| v3.9 | 更新 Go 版本 | 2020/01/17 12:33:16 | 更新到 1.13.6 |
| v4.0 | 更新 service 启动方式 | 2020/07/30 16:24:34 | 其他小问题修复 |
| v4.1 | 更新 daemon-reload | 2020/08/05 16:32:11 | 其他小问题修复 |

## 一 前言
***

群聊是社交软件非常重要的功能之一，Mixin Messenger 当然也提供了群聊，不过美中不足的是，通过 Mixin Messenger 创建的群最多支持 256 人。

如何创建突破 256 人数限制的大群？官方已经有了答案，仓库 [在此](https://github.com/MixinNetwork/supergroup.mixin.one)，后端语言为 Go，前端语言为 Node.js。

据笔者调研，目前 Mixin Messenger 主要有 5 个大群，分别是：Mixin 中文群（7000101317）、PRESS.one 中文群（7000101697）、BigONE 官方中文群（7000101910）、Mixin English（7000101502）和 Exin 中文群（7000000016）。然而对于一款社交软件来说，数量这么少的大群是远远不够的。官方的大群仓库是有了，笔者却发现没有一个详细的帮助文档，这显然是不友好的。

最近 Mixin 大群代码做了重构，修改后端相关的配置无需重新编译代码，所以本教程也做相应的调整。

## 二 架构
***

Mixin 大群是基于 Mixin Bot 实现的。笔者简单画了个架构图。域名解析使用 Nginx，其中有大群主域名和 API 域名，主域名指向前端代码，前端右 Node.js 实现。API 域名指向后端，后端由 Go 实现，有两个进程，一个是处理 HTTP 请求的，一个是处理 Message 请求的。其中 Go 的进程还会和 Mixin 的 API 交互。此外，数据库采用 PostgreSQL。Mixin 大群最初的版本，数据库采用的是 Google 的 Spanner，但 Spanner 只有选择 Google Cloud，而且价格偏高。

架构示意图如下：

![](https://cdn.dbarobin.com/loplv1n.png)

## 三 如何部署
***

介绍完 Mixin 大群的架构，读者可能好奇怎么样部署。根据笔者猜测，Mixin 不支持不限制人数大群，可能是出于成本的考虑。因为笔者部署下来，服务器成本、维护成本还是挺高的。既然 Mixin 大群采用了 PostgreSQL，那么无论国内外云服务器都可以选择。值得注意的是，Mixin API 目前在国外，选择国内服务器部署大群，打开红包体验比较快，但转账没有部署在国外的大群快。而且国内服务器在解决墙的问题上，还得多花功夫。

好了，接下来笔者讲解下详细部署。

## 四 部署大群
***

> 大群部署或者更新之前务必阅读 CHANGELOG.md

为了方便读者理解，笔者设置的大群主域名是：group.test.com，API 域名是 api.test.com，并解决了 SSL 的问题。测试用户为 test，真实运行需要替换用户。

> 如果大群部署在国内服务器，因为众所周知的原因，域名需要备案。如果没有域名，IP + 端口的形式也可以实现，缺陷在于无法使用 SSL。

### 4.1 申请服务器
***

国内外云服务商很多选择，国内的话，阿里云、腾讯云都可以选择，国外的话，AWS、Google Cloud、Azure。

申请服务器并不难，根据预算选择，建议前期选择配置中等的机器，机器配置可以根据情况调整。

至于系统版本，笔者选择的是 Ubuntu 18.04.2 LTS。本文大部分操作都以 root 用户操作。如果是 ubuntu 用户，使用 sudo 执行。

### 4.2 申请 Mixin 机器人
***

参考开发文档，点击 [此处](https://developers.mixin.one/api/alpha-mixin-network/register-app/)。

这里有一点非常重要，机器人后台配置（访问 Mixin Developers 后台，点击申请的 App 的 client id 即可修改配置）：

* The home uri：设置为主域名，这个教程里的示例为 [https://group.test.com](https://group.test.com)
* The OAuth redirect uri：设置为 [https://group.test.com/auth](https://group.test.com/auth)，如果设置为跟 home uri 一致，用户访问机器人请求授权，一直循环，不能校验成功

### 4.3 安装 PostgreSQL
***

命令如下：

``` bash
$ apt -y update
$ apt -y install postgresql postgresql-contrib

$ /usr/lib/postgresql/10/bin/postgres -V
postgres (PostgreSQL) 10.8 (Ubuntu 10.8-0ubuntu0.18.04.1)
```

安装之后，PostgreSQL 服务自动运行。当然读者也可以选择源码安装。

### 4.4 修改时区
***

部署在国内服务器的读者注意了，强烈建议将服务器时间改为 UTC。部署在国外服务器的大群时区默认是 UTC，但国内服务器时区默认是 CST。如果时区为 CST，浏览群组列表「加载更多」将会导致一直重复显示第一页。

修改方法如下：

第一，修改系统时区：

``` bash
$ timedatectl set-timezone UTC
```

第二，修改数据库配置：

``` bash
# 将 CST 修改为为 UTC
$ vim /etc/postgresql/10/main/postgresql.conf

timezone = 'UTC'
log_timezone = 'UTC'

# 重启数据库
$ systemctl restart postgresql
```

由于时间字段使用的格式为 `TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()`，所以即使是先在国内服务器上线大群，后期再修改也不影响服务。

### 4.5 安装 Nginx
***

命令如下：

``` bash
$ apt -y install nginx

$ nginx -v
nginx version: nginx/1.14.0 (Ubuntu)
```

安装之后，Nginx 服务自动运行。当然读者也可以选择源码安装。

### 4.6 安装 Node.js
***

命令如下：

``` bash
$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
$ apt-get -y install nodejs

$ node -v
v12.18.3

$ npm -v
6.14.5
```

### 4.7 安装 Go
***

命令如下：

``` bash
$ mkdir -p /data/tmp
$ cd /data/tmp
$ wget https://dl.google.com/go/go1.14.6.linux-amd64.tar.gz
$ tar -zxvf go1.14.6.linux-amd64.tar.gz
$ mv go /usr/local
```

接着配置环境变量。

``` bash
$ vim /etc/profile

export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

$ source /etc/profile

$ go version
go version go1.14.6 linux/amd64
```

### 4.8 编译后端
***

最新的代码采用了 go module，所以代码无需存放在 `$GOPATH`。

如果依然在 `$GOPATH/src/github.com` 目录下 go build，会出现如下的问题：

``` bash
# github.com/MixinNetwork/supergroup.mixin.one/services
supergroup.mixin.one/services/tasks.go:44:22: xurls.Relaxed.Match undefined (type func() *regexp.Regexp has no field or method Match)
```

编译命令如下：

``` bash
$ mkdir -p /data && cd /data
$ git clone https://github.com/MixinNetwork/supergroup.mixin.one
$ cd supergroup.mixin.one
$ go build
```

如果是国内服务器，估计比较悬。读者可以采用 ss + [cow](https://github.com/cyfdecyf/cow/) 解决这个问题，在此不多说。

如果没有什么问题的话，会在当前目录生成一个 `supergroup.mixin.one` 的可执行文件，当然读者可以重命名为其他名字。

此外，不少读者反馈 go build 遇到各种各样的问题，为了方便读者搭建大群，笔者为大家提前编译好了。如果读者使用笔者编译好的二进制文件，4.7 和 4.8 步骤都可以忽略。

下载地址点击 [此处](https://download.exin.one/mixin/supergroup)。需要说明的是，下载目录以日期为归档，建议下载最新的可执行文件。笔者会跟踪大群代码，保持同步，并且定期上传二进制文件。将 `supergroup.mixin.one` 可执行文件下载下来之后，读者将此文件上传到服务器，需要执行 `chmod +x supergroup.mixin.one` 为二进制文件添加可执行权限，并且可以重命名为其他名字。

### 4.9 数据库配置
***

PostgreSQL 安装好之后，需要建用户、建库、建表、授权。

``` bash
$ sudo -i -u postgres
# 注意：以下命令在切换到 postgres 用户之后的命令行执行
$ createuser --interactive

Enter name of role to add: test
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) y
Shall the new role be allowed to create more new roles? (y/n) n

$ createdb test

$ exit
logout

# adduser 以 root 用户执行
$ adduser test
Adding user `test' ...
Adding new group `test' (1000) ...
Adding new user `test' (1000) with group `test' ...
Creating home directory `/home/test' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for test
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y

$ cp -v /data/supergroup.mixin.one/models/schema.sql /home/test
$ chown test:test /home/test/schema.sql

$ sudo -i -u test
$ psql
psql (10.8 (Ubuntu 10.8-0ubuntu0.18.04.1))
Type "help" for help.
test=> \conninfo
You are connected to database "test" as user "test" via socket in "/var/run/postgresql" at port "5432".
# 这个路径仅供参考，只要能找到 schema.sql 文件即可，新版无需依赖 $GOPATH，放在任何位置即可
test=> \i /home/test/schema.sql
test=> \q

$ exit
logout

# 也可以 sudo -i -u test 修改用户密码
$ sudo -i -u postgres
$ psql
postgres=# ALTER USER test WITH PASSWORD 'xxxxxxxx';
```

### 4.10 修改后端配置
***

重构后的代码，`config.go` 文件无需编辑，只需要修改 config.yaml 文件，步骤如下：

``` bash
# clone 代码后，当前目录拷贝即可
$ cd /data/supergroup.mixin.one
$ cp -v config/config.tpl.yaml config.yaml
$ vim config.yaml
```

修改配置文件：

``` yaml
service:
  name: "Mixin 中文群"
  enviroment: "production"
  port: 7001
  host: "https://you-domain-name"
database:
  username: "postgres"
  password: "YOUR_PASSWORD"
  host: "localhost"
  port: 5432
  database_name: "postgres"
system:
  message_shard_modifier: SHARD
  message_shard_size: 8
  # 只允许发有价格的红包，默认为 true
  price_asset_enable: true
  # 允许发送语音消息
  audio_message_enable: true
  # 允许发送图片消息
  image_message_enable: true
  # 允许发送视频消息
  video_message_enable: true
  # 允许发送名片
  contact_message_enable: true
  # 限制发送频率，单位秒
  limit_message_duration: 0 # seconds: 60, 0 表示不限制
  # 限制发送频率，消息条数
  limit_message_number: 0 # number: 5 60s 5 条
  # 禁止发带二维码的图片
  detect_image: false
  # 禁止发送链接
  detect_link: false
  # 管理员列表
  operator_list:
    - "e9a5b807-fa8b-455a-8dfa-b189d28310ff"
    - "fcc87491-4fa0-4c2f-b387-262b63cbc112"
  # 只能支付加入
  pay_to_join: true
  # 支持支付数字货币列表
  accept_asset_list:
    - symbol: "XIN"
      asset_id: "c94ac88f-4671-3976-b60a-09064f1811e8" # XIN
      amount: "auto" # auto estimate
    - symbol: "CNB"
      asset_id: "965e5c6e-434c-3fa9-b780-c50f43cd955c" # CNB
      amount: "1000.00"
# 主页外观
appearance:
  home_shortcut_groups:
    - label_en: "3-Party Services"
      label_zh: "第三方提供的服务"
      shortcuts:
        # icon URL
        - icon: "..."
          label_en: "Service Name"
          label_zh: "服务名称"
          # 服务链接
          url: "https://exinone.com"
message_template:
  welcome_message        : "欢迎加入 Mixin 中文群"
  group_redpacket        : "中文群红包"
  group_redpacket_short_desc: "来自无名氏的红包"
  group_redpacket_desc    : "来自 %s 的红包"
  group_opened_redpacket  : "%s 打开了你的红包"
  message_prohibit        : "群主开启了禁言，暂时不能发言了。"
  message_allow           : "群主关闭了禁言，你可以发言了。"
  message_tips_guest     : "您需要先点击机器图标授权。"
  message_tips_join      : "%s 加入了群组"
  message_tips_help      : "您需要先支付 0.001 XIN, 加入群组才能发消息。"
  message_tips_help_btn   : "点击加入群组"
  message_tips_unsubscribe: "您已经取消了本群的消息订阅, 无法发送或者接收消息。"
  message_reward_label: "% s 给 % s 转了 % s % s"
  message_reward_memo: " 来自 % s"
  message_tips_too_many   : "发送太频繁"
  message_tips_suspended   : " 由于您长时间未使用，暂停发送消息。"
  message_commands_info   : "/INFO"
  message_commands_info_resp: "当前订阅人数: %d"
mixin:
  client_id        : ""
  client_secret    : ""
  session_asset_pin : ""
  pin_token        : ""
  session_id       : ""
  session_key: |
    -----BEGIN RSA PRIVATE KEY-----
    -----END RSA PRIVATE KEY-----
```

Operators 变量用于配置管理员列表，这里填写的不是管理员的 ID，而是真实 ID，格式为 UUID。笔者根据 [Mixin-SDK-PHP](https://github.com/ExinOne/mixin-sdk-php) 写了个脚本，可以参考下，点击 [此处](https://github.com/dbarobin/mixin/blob/master/mixin-searchuser.php) 阅读。此外，还可以通过「mixin info bot」机器人获取（机器人 ID：**7000101942**），添加机器人之后，回复「search:ID」指令就可以获取到用户的 UUID。

### 4.11 编译前端
***

编译前端的方式也有变化，之前是修改 `webpack.config.js` 文件，重构后的代码修改的是 `env.prod.sh` 脚本，更换为 VUE 修改的是 `.env.local`，步骤如下：

``` bash
$ cd /data/supergroup.mixin.one/web
# 如果只编译线上环境，可以不用修改 env.dev.sh 文件
$ cp -v env.example .env.local
```

修改配置文件：

``` bash
$ vim .env.local

NODE_ENV="production"
VUE_APP_WEB_ROOT="http://group.test.com"
VUE_APP_API_ROOT="http://api.test.com"
VUE_APP_CLIENT_ID="xxxxxxxx"
VUE_APP_ROUTER_MODE="history"
```

安装依赖：

``` bash
$ cd /data/supergroup.mixin.one/web
# 安装依赖
$ npm install
# 编译线上环境
$ npm run build
$ mkdir /var/www/test
$ mv dist /var/www/test
$ chown www-data:www-data -R /var/www/
```

### 4.12 配置 Nginx
***

创建配置文件。

``` bash
$ cd /etc/nginx/conf.d/
$ touch group.test.com.conf
$ touch api.test.com.conf
```

group.test.com.conf 内容如下（参考 [nginx.web.conf](https://raw.githubusercontent.com/ExinOne/supergroup.mixin.one/exin/nginx/nginx.web.conf)）：

``` bash
server {
    listen 443 ssl http2;
    server_name group.test.com;
    ssl on;
    ssl_certificate /etc/nginx/conf.d/cert/group.test.com_bundle.crt;
    ssl_certificate_key /etc/nginx/conf.d/cert/group.test.com.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers xxxxxxxx;
    ssl_prefer_server_ciphers on;

    root /var/www/test/dist;
    index index.html index.htm;
    charset utf-8;

    gzip            on;
    gzip_comp_level 5;
    gzip_min_length 256;
    gzip_proxied    any;
    gzip_types
      application/atom+xml
      application/javascript
      application/json
      application/ld+json
      application/manifest+json
      application/rss+xml
      application/vnd.geo+json
      application/vnd.ms-fontobject
      application/x-font-ttf
      application/x-web-app-manifest+json
      application/xhtml+xml
      application/xml
      font/opentype
      image/bmp
      image/svg+xml
      image/x-icon
      text/cache-manifest
      text/css
      text/plain
      text/vcard
      text/vnd.rim.location.xloc
      text/vtt
      text/x-component
      text/x-cross-domain-policy;

    # 注意，这里有修改，前端跳转路径变了
    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
      expires max;
      try_files $uri =404;
    }

    location / {
      try_files /index.html =404;
    }
}
```

api.test.com.conf 内容如下（参考 [nginx.api.conf](https://raw.githubusercontent.com/ExinOne/supergroup.mixin.one/exin/nginx/nginx.api.conf)）：

``` bash
upstream test {
        server 127.0.0.1:7001 fail_timeout=0;
}

server {
    listen 443 ssl http2;
    server_name api.test.com;
    ssl on;
    ssl_certificate /etc/nginx/conf.d/cert/api.test.com_bundle.crt;
    ssl_certificate_key /etc/nginx/conf.d/cert/api.test.com.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers xxxxxxxx;
    ssl_prefer_server_ciphers on;

    index index.html index.htm;

    charset utf-8;

    gzip            on;
    gzip_comp_level 5;
    gzip_proxied    any;
    gzip_types      *;

    location / {
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header  X_FORWARDED_PROTO $scheme;
        proxy_set_header  Host $http_host;
        proxy_redirect    off;
        client_max_body_size 1M;

        proxy_pass http://test;
        }

 }
```

接下来重启 Nginx：

``` bash
$ /usr/sbin/nginx -t
$ systemctl restart nginx
```

### 4.13 部署服务
***

为了管理方便，为两个 Go 进程部署 Service 服务。因为重构后的代码，编译不依赖配置文件，所以 Service 服务脚本，需要指定 `-dir` 参数。

``` bash
$ cd /etc/systemd/system
$ touch test.api.service
$ touch test.message.service
```

test.api.service 文件内容如下：

``` bash
[Unit]
Description=Group API Daemon
After=network.target

[Service]
# 需要把 test 替换为运行 service 的 linux 用户，比如 ubuntu
User=test
Type=simple
# 新版无需依赖 $GOPATH
# 注意 -dir 参数无效
ExecStart=/data/supergroup.mixin.one/supergroup.mixin.one -config /data/supergroup.mixin.one/config.yaml -service http
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

test.message.service 文件内容如下：

``` bash
[Unit]
Description=Group Message Daemon
After=network.target

[Service]
# 需要把 test 替换为运行 service 的 linux 用户，比如 ubuntu
User=test
Type=simple
# 新版无需依赖 $GOPATH
# 注意 -dir 参数无效
ExecStart=/data/supergroup.mixin.one/supergroup.mixin.one -config /data/supergroup.mixin.one/config.yaml -service message
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

接下来重启服务：

``` bash
$ systemctl restart test.api.service
$ systemctl restart test.message.service
```

### 4.14 检测服务
***

使用 `netstat -langput | grep LISTEN` 检测监听端口，如果正常的话，Mixin 大群相关的，会有 80 和 443（Nginx）、5432（PostgreSQL）、7001 和 9001（Go 后端）。

此外，服务器只需要对外开放 80 和 443 即可。

### 4.15 Mixin 测试
***

打开 Mixin，搜索机器人 ID，添加应用，点击机器人授权，多拉几个人即可进行测试。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 五 小结
***

目前 Mixin 大群部署维护成本还是比较高，然而建大群需求还是有的，建议接下来 Mixin 能提供对用户的大群服务。

后续如果仓库有更新，针对后端，git pull 在 go build 然后重启 service 即可（如果没法编译后端代码，可以下载由本教程提供的二进制文件）；针对前端，git pull 然后 npm run build 编译替换 Web 目录的内容即可。当然读者有开发能力，可以在开源的代码基础上添加自己想要的功能。

如果读者在部署过程中有任何问题，欢迎交流，[关于](https://dbarobin.com/about) 页面有我的联系方式。

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
