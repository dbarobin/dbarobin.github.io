---
published: true
author: Robin Wen
layout: post
title: SSD 下的 MySQL IO 优化
category: 数据库
summary: >-
  A 项目，因 I/O 出现规律性地剧烈波动，调整了 MySQL InnoDB 参数。为了保险起见，A 项目方面决定采用配有 SSD
  的机型，对主库进行迁移，同时对 24 的从库 27 进行迁移。待迁移完成后，在新的主库 39 上，针对 SSD 以及 MySQL InnoDB
  参数进行优化。待程序切换完成后，再次对针对 SSD 以及 MySQL InnoDB 参数进行优化。也就是说在上线前后进行优化，观察 I/O
  状态。网络文章介绍的方法有局限性和场景性，不能亲信，不能盲从，做任何调整都要以业务优先。保证业务的平稳运行才是最重要的，性能都是其次；任何一个调整，都要建立在数据的支撑和严谨的分析基础上，否则都是空谈；这类调优是非常有意义的，是真正能带来价值的，所以需要多下功夫，并且尽可能地搞明白为什么要这么调整。
tags:
  - 数据库
  - Database
  - MySQL
  - 性能优化
comments:
  - author:
      type: full
      displayName: leijian
      url: 'https://github.com/leijian'
      picture: 'https://avatars.githubusercontent.com/u/2032390?v=3&s=73'
    content: >-
      &#x5927;&#x795E;&#xFF0C;&#x57FA;&#x4E8E;mysql&#x54EA;&#x4E2A;&#x7248;&#x672C;&#x505A;&#x7684;&#x5440;
    date: 2015-12-23T06:19:30.042Z
  - author:
      type: full
      displayName: leijian
      url: 'https://github.com/leijian'
      picture: 'https://avatars.githubusercontent.com/u/2032390?v=3&s=73'
    content: >-
      &#x5927;&#x795E;&#xFF0C;&#x6587;&#x7AE0;&#x662F;&#x57FA;&#x4E8E;mysql&#x54EA;&#x4E2A;&#x7248;&#x672C;&#x5440;
    date: 2015-12-23T06:20:01.199Z
  - author:
      type: full
      displayName: tenyks
      url: 'https://github.com/tenyks'
      picture: 'https://avatars.githubusercontent.com/u/3964982?v=3&s=73'
    content: >-
      LZ&#xFF0C;&#x56FE;&#x4E8C;&#x7684;&#x6570;&#x636E;&#x662F;&#x7528;&#x4EC0;&#x4E48;&#x5DE5;&#x5177;&#x6536;&#x96C6;&#x7684;&#xFF1F;
    date: 2016-07-18T11:39:10.179Z
  - author:
      type: github
      displayName: toontong
      url: 'https://github.com/toontong'
      picture: 'https://avatars0.githubusercontent.com/u/416141?v=3&s=73'
    content: >-
      LevelDB &#x7684;&#x8BBE;&#x8BA1;&#x5B8C;&#x5168;&#x662F;&#x8D34;&#x5408;
      SSD &#x7684;&#x8BBE;&#x8BA1;&#x601D;&#x60F3;&#x7684;&#x3002;----
      &#x8FD9;&#x53E5;&#x8BDD;&#xFF0C;&#x4F60;&#x786E;&#x5B9A;&#x662F;&#x6B63;&#x786E;&#xFF1F;
      level&#x662F;&#x9760;&#x987A;&#x5E8F;&#x5199;&#xFF0C;&#x5229;&#x7528;&#x7684;&#x673A;&#x68B0;&#x786C;&#x76D8;&#x987A;&#x5E8F;IO&#x9AD8;&#x6548;&#x4E8E;&#x79BB;&#x6563;IO&#x7684;&#x7279;&#x70B9;&#xFF0C;&#x800C;SSD&#x79BB;&#x6563;IO&#x4E0E;&#x987A;&#x5E8F;IO&#x662F;&#x6CA1;&#x591A;&#x5927;&#x5DEE;&#x522B;&#x7684;&#x3002;
    date: 2017-06-15T03:49:06.175Z

---

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 一 目录
***

* Table of Contents
{:toc}

## 二 背景
***

在阅读这篇文章之前，读者需要注意的是，为了维护隐私，用 MySQL 服务器的 D 段代替完整 IP，并且略去一些私密信息。

A 项目，因 I/O 出现规律性地剧烈波动。每 15 分钟落地一次，innodbBuffPoolPagesFlushed 参数监控波峰和波谷交替出现，磁盘 I/O 同样如此，并且 until 达到 100%。经过排查，排除了触发器、事件、存储过程、前端程序定时器、系统 crontab 的可能性。最终定位为 InnoDB 日志切换，但是否完全是日志造成的影响，还有待进一步跟踪和分析。

找到问题的可能所在，试图在 24 主库上做了如下调整：

* 关闭 Query Cache；
* 设置 InnoDB Log 大小为 1280M；
* 设置 innodb_max_dirty_pages_pct 为 30，innodb_io_capacity 保持 200 不变。

做了如上调整以后，I/O 趋于平稳，没有再出现大的波动。

为了保险起见，A 项目方面决定采用配有 SSD 的机型，对主库进行迁移，同时对 24 的从库 27 进行迁移。待迁移完成后，在新的主库 39 上，针对 SSD 以及 MySQL InnoDB 参数进行优化。待程序切换完成后，再次对针对 SSD 以及 MySQL InnoDB 参数进行优化。也就是说在上线前后进行优化，观察 I/O 状态。

## 三 SSD 特性
***

众所周知，SSD 的平均性能是优于 SAS 的。SSD 能解决 I/O 瓶颈，但互联网行业总要权衡收益与成本的。目前内存数据库是这个领域的一大趋势，一方面，越来越多的应用会往 NoSQL 迁移。另一方面，重要数据总要落地，传统的机械硬盘已经不能满足目前高并发、大规模数据的要求。总的来说，一方面，为了提高性能，尽可能把数据内存化，这也是 InnoDB 存储引擎不断改进的核心原则。后续的 MySQL 版本已经对 SSD 做了优化。另一方面，尽可能上 SSD。

SSD 这么神秘，接下来我们看看它有哪些特性：

* 随机读能力非常好，连续读性能一般，但比普通 SAS 磁盘好；
* 不存在磁盘寻道的延迟时间，随机写和连续写的响应延迟差异不大。
* erase-before-write 特性，造成写入放大，影响写入的性能；
* 写磨损特性，采用 Wear Leveling 算法延长寿命，但同时会影响读的性能；
* 读和写的 I/O 响应延迟不对等（读要大大好于写），而普通磁盘读和写的 I/O 响应延迟差异很小；
* 连续写比随机写性能好，比如 1M 顺序写比 128 个 8K 的随即写要好很多，因为随即写会带来大量的擦除。

总结起来，**也就是随机读性能较连续读性能好，连续写性能较随机写性能好，会有写入放大的问题，同一位置插入次数过多容易导致损坏。**

## 四 基于 SSD 的数据库优化
***

基于 SSD 的数据库优化，我们可以做如下事情：

* 减少对同一位置的反复擦写，也就是针对 InnoDB 的 Redo Log。因为 Redo Log 保存在 ib_logfile0/1/2，这几个日志文件是复写，来回切换，必定会带来同一位置的反复擦写；
* 减少离散写入，转化为 Append 或者批量写入，也就是针对数据文件；
* 提高顺序写入的量。

具体来说，我们可以做如下调整：

* 修改系统 I/O 调度算法为 NOOP；
* 提高每个日志文件大小为 1280M（调整 innodb_log_file_size）；
* 通过不断调整 innodb_io_capacity 和 innodb_max_dirty_pages_pct 让落地以及 I/O 水平达到均衡；
* 关闭 innodb_adaptive_flushing，查看效果；
* 修改 innodb_write_io_threads 和 innodb_read_io_threads。

针对系统 I/O 调度算法，做如下解释。系统 I/O 调度算法有四种，CFQ(Complete Fairness Queueing，完全公平排队 I/O 调度程序)、NOOP(No Operation，电梯式调度程序)、Deadline（截止时间调度程序）、AS(Anticipatory，预料 I/O 调度程序)。

下面对上述几种调度算法做简单地介绍。

CFQ 为每个进程/线程，单独创建一个队列来管理该进程所产生的请求，也就是说每个进程一个队列，各队列之间的调度使用时间片来调度，以此来保证每个进程都能被很好的分配到 I/O 带宽，I/O 调度器每次执行一个进程的 4 次请求。

NOOP 实现了一个简单的 FIFO 队列，它像电梯的工作主法一样对 I/O 请求进行组织，当有一个新的请求到来时，它将请求合并到最近的请求之后，以此来保证请求同一介质。

Deadline 确保了在一个截止时间内服务请求，这个截止时间是可调整的，而默认读期限短于写期限，这样就防止了写操作因为不能被读取而饿死的现象。

AS 本质上与 Deadline 一样，但在最后一次读操作后，要等待 6ms，才能继续进行对其它 I/O 请求进行调度。可以从应用程序中预订一个新的读请求，改进读操作的执行，但以一些写操作为代价。它会在每个 6ms 中插入新的 I/O 操作，而会将一些小写入流合并成一个大写入流，用写入延时换取最大的写入吞吐量。

在 SSD 或者 Fusion IO，最简单的 NOOP 反而可能是最好的算法，因为其他三个算法的优化是基于缩短寻道时间的，而固态硬盘没有所谓的寻道时间且 I/O 响应时间非常短。

还是用数据说话吧，以下是 SSD 下针对不同 I/O 调度算法所做的 I/O 性能测试，均为 IOPS。

> 注：以下数据来自于陈广钊，他是我的师兄，在此致谢。

<p></p>

|  I/O Type  | NOOP  | Anticipatory  | Deadline  | CFQ |
| ------------- | ----- | -------- | ------- | ----- |
| Sequential  Read | 22256 |  7955 | 22467 |    8652 |
| Sequential Write    | 4090    | 2560    | 1370    | 1996 |
| Sequential RW Read  | 6355    | 760 | 567 | 1149 |
| Sequential RW Write | 6360    | 760 | 565 | 1149 |
| Random Read | 17905   | 20847   | 20930   | 20671 |
| Random  Write    | 7423    | 8086    | 8113    | 8072 |
| Random  RW Read  | 4994    | 5221    | 5316    | 5275 |
| Random  RW Write | 4991    | 5222    | 5321    | 5278 |

<p></p>

可以看到，整体来说，**NOOP 算法略胜于其他算法。**

接下来讲解需要调整的 InnoDB 参数的含义：

* innodb_log_file_size：InnoDB 日志文件的大小；
* innodb_io_capacity：缓冲区刷新到磁盘时，刷新脏页数量；
* innodb_max_dirty_pages_pct：控制了 Dirty Page 在 Buffer Pool 中所占的比率；
* innodb_adaptive_flushing：自适应刷新脏页；
* innodb_write_io_threads：InnoDB 使用后台线程处理数据页上写 I/O（输入）请求的数量；
* innodb_read_io_threads：InnoDB 使用后台线程处理数据页上读 I/O（输出）请求的数量。

## 五 A 项目 MySQL 主从关系图
***

A 项目 MySQL 主从关系如图一：

![Yzone](https://cdn.dbarobin.com/ktLzmgs.png)
图一 A 项目 MySQL 主从关系图

## 六 程序切换之前调优
***

程序切换之前，39 只是 24 的从库，所以 IO 压力不高，以下的调整也不能说明根本性的变化。需要说明一点，以下调整的平均间隔在 30 分钟左右。

### 6.1 修改系统 IO 调度算法

系统默认的  I/O 调度算法 是 CFQ，我们试图先修改之。至于为什么修改，可以查看第四节。

具体的做法如下，需要注意的是，请根据实际情况做调整，比如你的系统中磁盘很可能不是 sda。

``` bash
echo "noop" > /sys/block/sda/queue/scheduler
```

如果想永久生效，需要更改 /etc/grup.conf，添加 elevator，示例如下：

> kernel /vmlinuz-x.x.xx-xxx.el6.x86_64 ro root=UUID=e01d6bb4-bd74-404f-855a-0f700fad4de0 rd_NO_LUKS rd_NO_LVM LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun1
6 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM elevator=noop rhgb quiet

此步调整做完以后，查看 39 I/O 状态，并没有显著的变化。

### 6.2 修改 innodb_io_capacity = 4000

在做这个参数调整之前，我们来看看当前 MySQL 的配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 200
> innodb_max_dirty_pages_pct 30
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

修改方法如下：

``` bash
SET GLOBAL innodb_io_capacity = 4000;
```

网络上的文章，针对 SSD 的优化，MySQL 方面需要把 innodb_io_capacity 设置为 4000，或者更高。然而实际上，此业务 UPDATE 较多，每次的修改量大概有 20K，并且基本上都是离散写。innodb_io_capacity 达到 4000，SSD 并没有给整个系统带来很大的性能提升。相反，反而使  IO 压力过大，until 甚至达到 80% 以上。

### 6.3 修改 innodb_max_dirty_pages_pct = 25

修改方法如下：

``` bash
SET GLOBAL innodb_max_dirty_pages_pct = 25;
```

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 4000
> innodb_max_dirty_pages_pct 25
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

之前已经将 innodb_max_dirty_pages_pct 设置为 30，此处将 innodb_max_dirty_pages_pct 下调为 25%，目的为了查看脏数据对 I/O 的影响。修改的结果是，I/O 出现波动，innodbBuffPoolPagesFlushed 同样出现波动。然而，由于 39 是 24 的从库，暂时还没有切换，所有压力不够大，脏数据也不够多，所以调整此参数看不出效果。

### 6.4 修改 innodb_io_capacity = 2000

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 2000
> innodb_max_dirty_pages_pct 25
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

因为 innodb_io_capacity 为 4000 的情况下，I/O 压力过高，所以将 innodb_io_capacity 调整为 2000。调整后，w/s 最高不过 2000 左右，并且 I/O until 还是偏高，最高的时候有 70%。我们同时可以看到，I/O 波动幅度减小，innodbBuffPoolPagesFlushed 同样如此。

### 6.5 修改 innodb_io_capacity = 1500

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1500
> innodb_max_dirty_pages_pct 25
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

I/O 持续出现波动，我们接着继续下调 innodb_io_capacity，调整为 1500。I/O until 降低，I/O 波动幅度继续减小，innodbBuffPoolPagesFlushed 同样如此。

### 6.6 关闭 innodb_adaptive_flushing

修改方法如下：

``` bash
SET GLOBAL innodb_adaptive_flushing = OFF;
```

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1500
> innodb_max_dirty_pages_pct 25
> innodb_adaptive_flushing OFF
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

既然落地仍然有异常，那我们可以试着关闭 innodb_adaptive_flushing，不让 MySQL 干预落地。调整的结果是，脏数据该落地还是落地，并没有受 I/O 压力的影响，调整此参数无效。

### 6.7 打开 innodb_adaptive_flushing

修改方法如下：

``` bash
SET GLOBAL innodb_adaptive_flushing = ON;
```

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1500
> innodb_max_dirty_pages_pct 25
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

经过以上调整，关闭 innodb_adaptive_flushing 没有效果，还是保持默认打开，让这个功能持续起作用吧。

### 6.8 设置 innodb_max_dirty_pages_pct = 20

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1500
> innodb_max_dirty_pages_pct 20
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

接着我们将 innodb_max_dirty_pages_pct 下调为 20，观察脏数据情况。由于 InnoDB Buffer Pool 设置为 40G，20% 也就是 8G，此时的压力达不到此阀值，所以调整参数是没有效果的。但业务繁忙时，就可以看到效果，落地频率会增高。

### 6.9 设置 innodb_io_capacity = 1000

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1000
> innodb_max_dirty_pages_pct 20
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

经过以上调整，我们需要的是一个均衡的 IO，给其他进程一些余地。于是把 innodb_io_capacity 设置为 1000，此时可以看到 I/O until 维持在 10% 左右，整个系统的参数趋于稳定。

后续还要做进一步的监控、跟踪、分析和优化。

## 七 程序切换之后调优
***

在业务低峰，凌晨 1 点左右，配合研发做了切换。切换之后的主从关系可以查看第五节。

### 7.1 设置 innodb_max_dirty_pages_pct = 30，innodb_io_capacity = 1500

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 1500
> innodb_max_dirty_pages_pct 30
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

在 innodb_io_capacity 为 1000，innodb_max_dirty_pages_pct 为 20 的环境下，I/O until 有小幅波动，而且波峰和波谷持续交替，这种情况是不希望看到的。innodbBuffPoolPagesFlushed 比较稳定，但 innodbBuffPoolPagesDirty 持续上涨，没有下降的趋势。故做了如下调整：innodb_max_dirty_pages_pct = 30，innodb_io_capacity = 1500。调整完成后，innodbBuffPoolPagesDirty 趋于稳定，I/O until 也比较稳定。

### 7.2 设置 innodb_max_dirty_pages_pct = 40，innodb_io_capacity = 2000

修改方法不赘述。

修改之后的 MySQL 配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 2000
> innodb_max_dirty_pages_pct 40
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

针对目前这种 I/O 情况，做了如下调整：innodb_max_dirty_pages_pct = 40，innodb_io_capacity = 2000。

### 7.3 分析

针对以上两个调整，我们通过结合监控数据来分析 I/O 状态。

以下是高速缓冲区的脏页数据情况，如图二：

![InnoDB Dirty pages](https://cdn.dbarobin.com/3elQdCP.png)
图二 主库的脏数据情况

以下是脏数据落地的情况，如图三

![InnoDB Flushed](https://cdn.dbarobin.com/xvXiOAW.png)
图三 主库的脏数据落地情况

28 号早 8 点到下午 7 点，当脏数据上升，也就是在内存中的数据更多，那么落地就会很少，呈现一个平稳的趋势；当脏数据维持不变，也就是脏数据达到了 innodb_max_dirty_pages_pct 的限额（innodb_buffer_pool_size 为 40G，innodb_max_dirty_pages_pct 为 40%，也就是在内存中的脏数据最多为 16G，每个 Page 16K，则 innodbBufferPoolDirtyPages 最大为 1000K），落地就会增多，呈现上升的趋势，所以才会出现上述图片中的曲线。

这是最后的配置：

> innodb_buffer_pool_size 42949672960
> innodb_log_file_size 1342177280
> innodb_io_capacity 2000
> innodb_max_dirty_pages_pct 40
> innodb_adaptive_flushing ON
> innodb_write_io_threads 4
>  innodb_read_io_threads 4

## 八 小结
***

此次针对 SSD 以及 MySQL InnoDB 参数优化，总结起来，也就是以下三条：

* 修改系统 I/O 调度算法；
* 分析 I/O 情况，动态调整 innodb_io_capacity 和 innodb_max_dirty_pages_pct；
* 试图调整 innodb_adaptive_flushing，查看效果。

针对 innodb_write_io_threads 和 innodb_read_io_threads 的调优我们目前没有做，我相信调整为 8 或者 16，系统 I/O 性能会更好。

还有，需要**注意**以下几点：

* 网络文章介绍的方法有局限性和场景性，不能亲信，不能盲从，做任何调整都要以业务优先。保证业务的平稳运行才是最重要的，性能都是其次；
* 任何一个调整，都要建立在数据的支撑和严谨的分析基础上，否则都是空谈；
* 这类调优是非常有意义的，是真正能带来价值的，所以需要多下功夫，并且尽可能地搞明白为什么要这么调整。

文末，说一点比较有意思的。之前有篇文章提到过 SSDB。SSDB 底层采用 Google 的 LevelDB，并支持 Redis 协议。LevelDB 的设计完全是贴合 SSD 的设计思想的。首先，尽可能地转化为连续写；其次，不断新增数据文件，防止同一位置不断擦写。另外，SSDB 的名字取得也很有意思，也很有水平。我猜想作者也是希望用户将 SSDB 应用在 SSD 上吧。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.io/cn/register/?ref=11190872](https://accounts.binancezh.io/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 九 参考
***

[8.5 Optimizing for InnoDB Tables](http://dev.mysql.com/doc/refman/5.5/en/optimizing-innodb.html)

–EOF–

插图来自：图一采用 Xmind Pro for Mac 制作，图二、图三来自监控系统。

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
