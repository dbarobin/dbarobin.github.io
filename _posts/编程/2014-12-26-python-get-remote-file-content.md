---
published: true
author: Robin Wen
layout: post
title: "Python获取远程文件内容"
category: 编程
summary: "最近需要实现一个功能，使用Jmeter自动生成测试报告。Jmeter脚本存放在Git仓库，现在需要实现在脚本发生更新时才自动生成测试报告。
我的思路是这样的：在拉取Git项目之前通过git rev-list --count HEAD命令记录一个版本号，然后在拉取项目完成生成之后生成测试报告之前再通过此命令获取另一个版本号。比较这两个版本号，如果相同，则不自动生成测试报告，如果不同，则自动生成测试报告。"
tags:
- 编程
- 运维
- DevOps
- Python
---

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

**环境**

**本机：**Mac OS X 10.9.5 <br/>
**远程服务器：**Debian 7.6 x86_64

**正文**

最近需要实现一个功能，使用Jmeter自动生成测试报告。Jmeter脚本存放在Git仓库，现在需要实现在脚本发生更新时才自动生成测试报告。

我的思路是这样的：在拉取Git项目之前通过`git rev-list --count HEAD`命令记录一个版本号，然后在拉取项目完成生成之后生成测试报告之前再通过此命令获取另一个版本号。比较这两个版本号，如果相同，则不自动生成测试报告，如果不同，则自动生成测试报告。

在本地测试如下：

首先写一个脚本，获取Git项目的版本号。

``` python
#!/usr/bin/env python
# Author: Robin Wen
# Date: 18:15:25 2014-12-24
# Desc: Get repo version.
# FileName: get_git_version.py

import subprocess, os

os.chdir('YOUR_PATH')
lcmd='git rev-list --count HEAD'
res=subprocess.call(lcmd, shell=True)
```

然后写一个测试脚本，内容如下：

``` python
#!/usr/bin/env python
# Author: Robin Wen
# Date: 18:16:54 2014-12-24
# Desc: Test get repo version.
# FileName: test_result.py

import os

# 仅做测试，这个脚本的执行结果当然是equal。
os.system("python /Users/robin/get_git_version.py > old.log")
os.system("python /Users/robin/get_git_version.py > new.log")

file = open('old.log', 'r')
old=file.read()
file.close()

file = open('new.log', 'r')
new=file.read()
file.close()

print old
print new

if old == new:
    print "equal"
else:
    print "not"
```

脚本使用方法：首先在拉取之前获得一次版本号，然后在拉取之后再获得一次版本号，就可以判断出项目是否发生更新了。

但问题来了，我是想实现远程自动化生成测试报告。现在就轮到`Fabric`上场了。

**Fabric**是一个Python的远程执行和部署工具，使用相当简洁。

GitHub地址：<a href="https://github.com/fabric/fabric" target="_blank"><i class="fa fa-github"></i></a>
官网：<a href="http://www.fabfile.org/"><img src="https://cdn.dbarobin.com/yiMwQRp.png" title="Fabric" border="0" height="16px" width="16px" alt="Fabric" /></a>

其中，远程调用使用Fabric提供的run方法，此方法和Ruby中的run有异曲同工之妙。

下面，谈到本文的核心了。**使用Fabric读取远程文件需要使用StringIO模块和get()方法，通过StringIO模块的getvalue()方法获取文件内容。**

下面是脚本的核心代码：

``` python
#!/usr/bin/env python
#encoding:utf-8
# Author: Robin Wen
# Date: 22/22/2014 15:02:20
# Desc: Auto generate testing reports.

# Import necessary packages.
import os
from StringIO import StringIO

from fabric.api import env, cd, prefix, sudo, run, hide, local, put, get, settings
from fabric.contrib.files import exists, upload_template
from fabric.colors import yellow, green, blue, red

# Update the repo via git.
def git_pull():

    print green('Update the repo via git.')

    # Get current git version.
    run('python '+script_dir+'/get_git_version.py > '+script_dir+'/old.log')

    # Git Pull.
    with cd(git_repo):
         run('git pull')

    print green('Update the repo via git.')

# Auto generate testing reports.
def auto_gen():

    print green('Auto generate testing reports.')

    run('python '+script_dir+'/get_git_version.py > '+script_dir+'/new.log')

    fd = StringIO()
    get(script_dir+'/old.log', fd)
    old=fd.getvalue()

    fd = StringIO()
    get(script_dir+'/new.log', fd)
    new=fd.getvalue()

    if old == new:
        print red('Nothing changed, it won\'t generate testing reports.')
    else:
        run('ant -buildfile '+script_dir+'/build.xml gen-testing-report')
        print green('Auto generate testing reports finished!')
```

在远程服务器调用该脚本即可，具体使用方法可以参考<a href="https://github.com/dbarobin/python-auto-deploy/tree/master/auto_gen_testing_reports" target="_blank">此处</a>，该项目已经托管在GitHub上。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>
