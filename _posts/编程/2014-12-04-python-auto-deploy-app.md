---
published: true
author: Robin Wen
layout: post
title: "Python自动化打包业务和认证平台 V2.0-Release"
category: 编程
summary: "Python 自动化打包业务和认证平台，本机只需执行脚本，远程即可自动部署。脚本采用Python编写，远程调用使用Fabric实现。"
tags:
- 编程
- 运维
- DevOps
- Python
---

相关代码：[@GitHub](https://github.com/dbarobin/python-auto-deploy)

目录

* Table of Contents
{:toc}

`文/Robin`

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

## 1.文档摘要 ##

> Python 自动化打包业务和认证平台，本机只需执行脚本，远程即可自动部署。脚本采用Python编写，远程调用使用Fabric实现。

## 2.更新日志 ##

2014-11-28

> 文档版本为「1.0」，文档名为「Python自动化打包业务和认证平台 V1.0」，备注为「文档正式版，已测试通过」，By Robin。

2014-12-04

> 文档版本为「2.0」，文档名为「Python自动化打包业务和认证平台 V2.0-Release」，备注为「文档正式版第二版，修复若干Bug」，By Robin。

## 3.版本信息 ##

本机 XXX：

> 系统版本：
> 主机名：XXX
> IP：xxx.xxx.xxx.xxx
> Python：2.6.6

远程机 XXX：

> 系统版本：XXX
> 主机名：XXX
> IP：xxx.xxx.xxx.xxx
> Python：2.7.3
> JDK：1.8.25
> Maven：3.2.3
> SVN：1.6.17

## 4.先决条件 ##

本机安装软件：

> Python 2.7.5

安装包如下：

> apt-get: python python-pip python-dev subversion subversion-tools
> yum: python python-pip python-devel subversion
> pip: fabric

远程服务器安装软件：

> JDK：1.8.25
> Maven：3.2.3
> SVN：1.6.17

安装包如下：

> dos2unix subversion subversion-tools

## 5.脚本详解 ##

### 5.1 软件概要 ###

本软件包括两个目录，其中auto_deploy_app_v2为Linux版本。auto_deploy_app_windows为Windows版本。Linux版本包括两个Python脚本以及一个配置文件。Windows版本包括两个Python脚本以及两个配置文件。

Linux目录结构如下：

``` bash
	tree auto_deploy_app_v2
```

> auto_deploy_app_v2
> |\-\- auto_deploy_app_remote.py
> |\-\- auto_deploy_app_v_final.py <br/>
> '\-\- config.conf
>
> 0 directories, 3 files

其中，「auto_deploy_app_remote.py」是主执行脚本，用于显示帮助以及调用相应函数。「auto_deploy_app_v_final.py」是核心执行脚本，实现所有的相关功能。「config.conf」是脚本的配置文件。

Windows版本目录结构如下：

``` bash
	tree auto_deploy_app_windows
```

> auto_deploy_app_windows
> |\-\- auto_deploy_app_remote.py
> |\-\- auto_deploy_app_v_final.py <br/>
> '\-\- config.conf <br/>
> '\-\- logging.conf
>
> 0 directories, 4 files

其中，「auto_deploy_app_remote.py」是主执行脚本，用于显示帮助以及调用相应函数。「auto_deploy_app_v_final.py」是核心执行脚本，实现所有的相关功能。「config.conf」是脚本的配置文件。「logging.conf」是日志配置文件。

该脚本实现的功能如下：

* 打印帮助
* 部署准备
* 检出项目
* 更新项目
* 部署业务平台
* 部署认证平台
* 启动、关闭、重启业务平台
* 启动、关闭、重启认证平台
* 修改数据库配置

### 5.2 脚本帮助 ###

我们通过如下命令可以获得该脚本的帮助。

``` bash
	./auto_deploy_app_remote.py -h
```

``` bash
Auto deploy application to the remote web server. Write in Python.
 Version 1.0. By Robin Wen. Email:blockxyz@gmail.com

 Usage auto_deploy_app.py [-hcustrakgdwp]
   [-h | --help] Prints this help and usage message
   [-p | --deploy-prepare] Deploy prepared. Run as root
   [-c | --svn-co] Checkout the newarkstg repo via svn
   [-u | --svn-update] Update the newarkstg repo via svn
   [-s | --shutdown-core] Shutdown the core platform via the stop.sh scripts
   [-t | --startup-core] Startup the core platform via the startup.sh scripts
   [-r | --restart-core] Restart the core platform via the restart.sh scripts
   [-a | --shutdown-auth] Shutdown the auth platform via the stop.sh scripts
   [-k | --startup-auth] Startup the auth platform via the startup.sh scripts
   [-g | --restart-auth] Restart the auth platform via the restart.sh scripts
   [-d | --deploy-core-platform] Deploy core platform via mvn
   [-w | --deploy-auth-platform] Deploy auth platform via mvn
   [-x | --update-database-setting] Update the database setting
```

在脚本名后加上「-h 或者 --help」表示打印帮助。
同理，加上「-p | --deploy-prepare」表示部署准备，加上「-c | --svn-co」表示检出项目，加上「-u | --svn-update」表示更新项目，加上「-s | --shutdown-core」表示关闭业务平台，加上「-t | --startup-core」表示启动业务平台，加上「-r | --restart-core」表示重启业务平台，加上「-a | --shutdown-auth」表示关闭认证平台，加上「--startup-auth」表示启动认证平台，加上「-g | --restart-auth」表示重启认证平台，加上「-d | --deploy-core-platform」表示部署业务平台，加上「-w | --deploy-auth-platform」表示部署认证平台，加上[-x | --update-database-setting]表示修改数据库配置。

### 5.3 脚本概述 ###

如前所述，「auto_deploy_app_remote.py」是主执行脚本，用于显示帮助以及调用相应函数。「auto_deploy_app_v_final.py」是核心执行脚本，实现所有的相关功能。核心执行脚本采用Fabric实现远程执行命令，主执行脚本再通过`fab -f 脚本名 任务名`调用相应方法。

主执行脚本和核心执行脚本的方法名基本一致，主执行脚本包括如下方法：main(argv)、usage()、svn_co()、svn_update()、shutdown_core()、startup_core()、restart_core()、shutdown_auth()、startup_auth()、restart_auth()、deploy_core_platform()、deploy_auth_plaform()、deploy_prepare()和updata_database_setting()。

核心执行脚本包括如下方法：main(argv)、usage()、svn_co()、svn_update()、shutdown_core()、startup_core()、restart_core()、shutdown_auth()、startup_auth()、restart_auth()、deploy_core_platform()、deploy_auth_platform()、deploy_prepare()、updata_database_setting()和getConfig()。


**主执行脚本：**

* main(argv) 主函数
* usage() 使用说明函数
* svn_co() 检出项目函数
* svn_update() 更新项目函数
* shutdown_core() 关闭业务平台方法
* startup_core() 启动业务平台方法
* restart_core() 重启业务平台方法
* shutdown_auth() 关闭认证平台方法
* startup_auth() 启动认证平台方法
* restart_auth() 重启认证平台方法
* deploy_core_platform() 部署业务平台方法
* deploy_auth_platform() 部署认证平台方法
* deploy_prepare() 部署准备方法
* updata_database_setting() 修改数据库配置方法。

**主执行脚本**

主执行脚本内容如下：

``` python
#!/usr/bin/env python
#encoding:utf-8
# Author: Robin Wen
# Date: 11/25/2014 10:51:54
# Desc: Auto deploy core-platform and auth to remote sever.

# Import necessary packages.
import os
import sys, getopt
import socket
import string
import shutil
import getopt
import syslog
import errno
import logging
import tempfile
import datetime
import subprocess
import json
import ConfigParser

from operator import itemgetter
from functools import wraps
from getpass import getpass, getuser
from glob import glob
from contextlib import contextmanager

from fabric.api import env, cd, prefix, sudo, run, hide, local, put, get, settings
from fabric.contrib.files import exists, upload_template
from fabric.colors import yellow, green, blue, red

try:
    import json
except importError:
    import simplejson as json

script_name='auto_deploy_app_v_final.py'
log_path='/var/logs'

"""
-----------------------------------------------------------------------------
Auto deploy core-platform and auth to tomcat.

Use the -h or the --help flag to get a listing of options.

Program: Deploy application
Author: Robin Wen
Date: November 25, 2014
Revision: 1.0
"""

# Main function.
def main(argv):
    try:
        # If no arguments print usage
        if len(argv) == 0:
            usage()
            sys.exit()

        # Receive the command line arguments. The execute the corresponding function.
        if sys.argv[1] == "-h" or sys.argv[1] == "--help":
            usage()
            sys.exit()
        elif sys.argv[1] == "-p" or sys.argv[1] == "--deploy-prepare":
            deploy_prepare()
        elif sys.argv[1] == "-c" or sys.argv[1] == "--svn-co":
            svn_co()
        elif sys.argv[1] == "-u" or sys.argv[1] == "--svn-update":
            svn_update()
        elif sys.argv[1] == "-s" or sys.argv[1] == "--shutdown-core":
            shutdown_core()
        elif sys.argv[1] == "-t" or sys.argv[1] == "--startup-core":
            startup_core()
        elif sys.argv[1] == "-r" or sys.argv[1] == "--restart-core":
            restart_core()
        elif sys.argv[1] == "-a" or sys.argv[1] == "--shutdown-auth":
            shutdown_auth()
        elif sys.argv[1] == "-k" or sys.argv[1] == "--startup-auth":
            startup_auth()
        elif sys.argv[1] == "-g" or sys.argv[1] == "--restart-auth":
            restart_auth()
        elif sys.argv[1] == "-d" or sys.argv[1] == "--deploy-core-platform":
            deploy_core_platform()
        elif sys.argv[1] == "-w" or sys.argv[1] == "--deploy-auth-platform":
            deploy_auth_platform()
        elif sys.argv[1] == "-x" or sys.argv[1] == "--update-database-setting":
            update_database_setting()
        else:
            print red('Unsupported option! Please refer the help.')
            print ''
            usage()
    except getopt.GetoptError, msg:
        # If an error happens print the usage and exit with an error
        usage()
        sys.exit(errno.EIO)

"""
Prints out the usage for the command line.
"""
# Usage funtion.
def usage():
    usage = [" Auto deploy application to the remote web server. Write in Python.\n"]
    usage.append("Version 1.0. By Robin Wen. Email:blockxyz@gmail.com\n")
    usage.append("\n")
    usage.append("Usage auto_deploy_app.py [-hcustrakgdwp]\n")
    usage.append("  [-h | --help] Prints this help and usage message\n")
    usage.append("  [-p | --deploy-prepare] Deploy prepared. Run as root\n")
    usage.append("  [-c | --svn-co] Checkout the newarkstg repo via svn\n")
    usage.append("  [-u | --svn-update] Update the newarkstg repo via svn\n")
    usage.append("  [-s | --shutdown-core] Shutdown the core platform via the stop.sh scripts\n")
    usage.append("  [-t | --startup-core] Startup the core platform via the startup.sh scripts\n")
    usage.append("  [-r | --restart-core] Restart the core platform via the restart.sh scripts\n")
    usage.append("  [-a | --shutdown-auth] Shutdown the auth platform via the stop.sh scripts\n")
    usage.append("  [-k | --startup-auth] Startup the auth platform via the startup.sh scripts\n")
    usage.append("  [-g | --restart-auth] Restart the auth platform via the restart.sh scripts\n")
    usage.append("  [-d | --deploy-core-platform] Deploy core platform via mvn\n")
    usage.append("  [-w | --deploy-auth-platform] Deploy auth platform via mvn\n")
    usage.append("  [-x | --update-database-setting] Update the database setting\n")
    message = string.join(usage)
    print message

# Checkout the newarkstg repo via svn function.
def svn_co():

    print green('Checkout the newarkstg repo via svn.')
    print 'Logs output to the '+log_path+'/svn_co.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/svn_co.log")
    os.system("fab -f "+script_name+" svn_co > "+log_path+"/svn_co.log")

    print green('Checkout finished!')

# Update the newarkstg repo via svn function.
def svn_update():

    print green('Update the newarkstg repo via svn.')
    print 'Logs output to the '+log_path+'/svn_update.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/svn_update.log")
    os.system("fab -f "+script_name+" svn_update > "+log_path+"/svn_update.log")

    print green('Update finished!')

# Shutdown the core platform via the stop.sh scripts function.
def shutdown_core():

    print green('Shutdown the core platform via the stop.sh scripts.')
    print 'Logs output to the '+log_path+'/shutdown_core.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/shutdown_core.log")
    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" shutdown_core > "+log_path+"/shutdown_core.log 2>/dev/null >/dev/null")

    print green('Shutdown the core platform finished!')

# Startup the core platform via the startup.sh scripts function.
def startup_core():

    print green('Startup the core platform via the startup.sh scripts.')
    print 'Logs output to the '+log_path+'/startup_core.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/startup_core.log")
    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" startup_core > "+log_path+"/startup_core.log & 2>/dev/null >/dev/null")

    print green('Startup the core platform finished!')

# Restart the core platform via the restart.sh scripts function.
def restart_core():
    print green('Restart the core platform via the restart.sh scripts.')
    print 'Logs output to the '+log_path+'/restart_core.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/restart_core.log")
    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" restart_core > "+log_path+"/restart_core.log & 2>/dev/null >/dev/null")

    print green('Restart the core platform finished!')

# Shutdown the auth platform via the stop.sh scripts function.
def shutdown_auth():

    print green('Shutdown the auth platform via the stop.sh scripts.')
    print 'Logs output to the '+log_path+'/shutdown_auth.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/shutdown_auth.log")
    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" shutdown_auth > "+log_path+"/shutdown_auth.log 2>/dev/null >/dev/null")

    print green('Shutdown the auth platform finished!')

# Startup the auth platform via the startup.sh scripts function.
def startup_auth():

    print green('Startup the auth platform via the startup.sh scripts.')
    print 'Logs output to the '+log_path+'/startup_auth.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/startup_auth.log")

    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" startup_auth > "+log_path+"/startup_auth.log & 2>/dev/null >/dev/null")

    print green('Startup the authplatform finished!')

# Restart the auth platform via the restart.sh scripts function.
def restart_auth():
    print green('Restart the core platform via the restart.sh scripts.')
    print 'Logs output to the '+log_path+'/restart_auth.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/restart_auth.log")
    with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
        os.system("fab -f "+script_name+" restart_auth> "+log_path+"/restart_auth.log & 2>/dev/null >/dev/null")

    print green('Restart the core platform finished!')

# Deploy core platform via mvn function.
def deploy_core_platform():

    print green('Deploy core platform via mvn.')
    print 'Logs output to the '+log_path+'/deploy_core_platform.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/deploy_core_platform.log")
    os.system("fab -f "+script_name+" deploy_core_platform > "+log_path+"/deploy_core_platform.log")

    print green('Congratulations! Deploy core platform finished!')

# Deploy auth platform via mvn.
def deploy_auth_platform():

    print green('Deploy auth platform via mvn.')
    print 'Logs output to the '+log_path+'/deploy_auth_platform.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/deploy_auth_platform.log")
    os.system("fab -f "+script_name+" deploy_auth_platform > "+log_path+"/deploy_auth_platform.log")

    print green('Congratulations! Deploy auth platform finished!')
    print red('Attention! If you want take a glance of the deploy log, contact the system administrator.')

# Deploy prepared.
def deploy_prepare():

    print green('Deploy prepared. Run as root.')
    # Install jdk 1.8.25.
    print red('This program require jdk 1.8.25. Make sure jdk and tomcat work out before all of your operations.')
    #Install Maven
    print green('Install maven.')
    print 'Logs output to the '+log_path+'/deploy_prepare.log'

    os.system('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    os.system("echo '' > "+log_path+"/deploy_prepare.log")
    os.system("fab -f "+script_name+" deploy_prepare > "+log_path+"/deploy_prepare.log")

    print green('Deploy prepared finished.')

# Update the databae setting.
def update_database_setting():

    print green('Update the database setting.')

    print 'Logs output to the auto_deploy_app.log'

    os.system("fab -f "+script_name+" update_database_setting > auto_deploy_app.log")

    print green('Update the database setting finished.')

# The entrance of program.
if __name__=='__main__':
    main(sys.argv[1:])
```

**核心执行脚本**

方法和主执行脚本基本一致，相同的不赘述。核心执行脚本还提供getConfig()方法，用于读取配置文件。

核心执行脚本内容如下：

``` python
#!/usr/bin/env python
#encoding:utf-8
# Author: Robin Wen
# Date: 11/25/2014 10:51:54
# Desc: Auto deploy core and auth platform to remote server.

# Import necessary packages.
import os
import sys, getopt
import socket
import string
import shutil
import getopt
import syslog
import errno
import logging
import tempfile
import datetime
import subprocess
import json
import ConfigParser

from operator import itemgetter
from functools import wraps
from getpass import getpass, getuser
from glob import glob
from contextlib import contextmanager

from fabric.api import env, cd, prefix, sudo, run, hide, local, put, get, settings
from fabric.contrib.files import exists, upload_template
from fabric.colors import yellow, green, blue, red

try:
    import json
except importError:
    import simplejson as json

# Configuration file name.
config_file='config.conf'

# Get configuration from the Config
def getConfig(section, key):
    config = ConfigParser.ConfigParser()
    path = os.path.split(os.path.realpath(__file__))[0] + '/'+config_file
    config.read(path)
    return config.get(section, key)

# Log path
log_path=getConfig("other", "remote_log_path")

# Remote server hosts.
hosts=getConfig("remote", "remote_usr")+"@"+getConfig("remote", "remote_ip")+":"+getConfig("remote", "remote_port")

# Remote server password.
password=getConfig("remote", "remote_pwd")

env.hosts=[hosts,]
env.password = password

# Remote server ip.
remote_ip=getConfig("remote", "remote_ip")

# Remote server username.
remote_usr=getConfig("remote", "remote_usr")

# Remote server password.
remote_pwd=getConfig("remote", "remote_pwd")

# Declare multiple variables.

# Core platform path.
core_platform_path=getConfig("core_path", "core_platform_path")

# Core platform configuration file path.
core_platform_config_path=getConfig("core_path", "core_platform_config_path")

# Auth platform path.
auth_path=getConfig("auth_path", "auth_path")

# Auth platform configuration path.
auth_platform_config_path=getConfig("auth_path", "auth_platform_config_path")

# Core platform config api path
core_platform_config_api_path=getConfig("core_path", "core_platform_config_api_path")

# Core platform config auth path
core_platform_config_auth_path=getConfig("core_path", "core_platform_config_auth_path")

# Auth platform configuration api path.
auth_platform_config_api_path=getConfig("auth_path", "auth_platform_config_api_path")

# Auth platform configuration auth path.
auth_platform_config_auth_path=getConfig("auth_path", "auth_platform_config_auth_path")

# Svn main directory of newarkstg repo.
svn_ns_dir=getConfig("svn_path", "svn_ns_dir")

# Svn core platform path.
svn_core_platform_path=getConfig("svn_path", "svn_core_platform_path")

# Svn core platform target path.
svn_core_platform_target_path=getConfig("svn_path", "svn_core_platform_target_path")

# Svn core platform path config path
svn_core_platform_config_path=getConfig("svn_path", "svn_core_platform_config_path")

# Svn core platform path config auth path
svn_core_platform_config_auth_path=getConfig("svn_path", "svn_core_platform_config_auth_path")

# Svn core platform path config api path
svn_core_platform_config_api_path=getConfig("svn_path", "svn_core_platform_config_api_path")

# Svn core platform dao path
svn_core_platform_dao_path=getConfig("svn_path", "svn_core_platform_dao_path")

# Database address.
db_addr=getConfig("database", "db_addr")

# Database username.
db_usr=getConfig("database", "db_usr")

# Datbase password.
db_pwd=getConfig("database", "db_pwd")

# Database port.
db_port=getConfig("database", "db_port")

# SVN username.
svn_username=getConfig("svn", "svn_username")

# SVN password.
svn_password=getConfig("svn", "svn_password")

# SVN url.
svn_url=getConfig("svn", "svn_url")

# Memcached server ip.
memcached_ip=getConfig("memcached", "memcached_ip")

# Memcached server port.
memcached_port=getConfig("memcached", "memcached_port")

# Local ip address. Deploy the application on the localhost by default.
ip_addr=getConfig("remote", "remote_ip")

# Core platform version.
core_version=getConfig("other", "core_version")

# Api port
api_port=getConfig("other", "api_port")

# Core platform bundles path
core_platform_bundles_path=getConfig("core_path", "core_platform_bundles_path")

# Auth platform bundles path
auth_platform_bundles_path=getConfig("auth_path", "auth_platform_bundles_path")

# Core platform jar name
core_platform_jar=getConfig("other", "core_platform_jar")

# Auth platform jar name
auth_platform_jar=getConfig("other", "auth_platform_jar")

# Core jar
core_jar=getConfig("other", "core_jar")

# Auth jar
auth_jar=getConfig("other", "auth_jar")

"""
-----------------------------------------------------------------------------
Auto deploy core-platform and auth to tomcat.

Use the -h or the --help flag to get a listing of options.

Program: Deploy application
Author: Robin Wen
Date: November 25, 2014
Revision: 1.0
"""
# Checkout the newarkstg repo via svn function.
def svn_co():
    print green('Checkout the newarkstg repo via svn.')

    # Create necessary directory
    run('mkdir -p '+svn_ns_dir+' 2>/dev/null >/dev/null')

    #run('ls -l '+path+'')
    with cd(svn_ns_dir):
        run('svn co --username '+svn_username+' --password '+svn_password+' '+svn_url+' '+svn_ns_dir+'')

    print green('Checkout finished!')

# Update the newarkstg repo via svn function.
def svn_update():
    print green('Update the newarkstg repo via svn.')

    # Create necessary directory
    run('mkdir -p '+svn_ns_dir+' 2>/dev/null >/dev/null')

    with cd(svn_ns_dir):
        run('svn update --username '+svn_username+' --password '+svn_password+' '+svn_ns_dir+'')

    print green('Update finished!')

# Shutdown the core platform via the stop.sh scripts function.
def shutdown_core():
    print green('Shutdown the core platform via the stop.sh scripts.')

    ret = run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15")

    if ret:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15|xargs kill -9 2>/dev/null >/dev/null")
    else:
        print red("No such progress!")

    print green('Shutdown the core platform finished!')

# Startup the core platform via the startup.sh scripts function.
def startup_core():
    print green('Startup the core platform via the startup.sh scripts.')

    ret = run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15")

    if ret:
        print red("Startup already!")
    else:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run('cd '+core_platform_path+' && java -jar newark-core-platform-'+core_version+'.jar 2>/dev/null >/dev/null')

    print green('Startup the core platform finished!')

# Restart the core platform via the startup.sh scripts function.
def restart_core():
    print green('Restart the core platform via the restart.sh scripts.')

    ret = run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15")

    if ret:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15|xargs kill -9 2>/dev/null >/dev/null")
    else:
        print red("No such progress!")

    ret_new = run("ps -ef|grep newark-core-platform|grep -v grep|cut -c 9-15")

    if ret_new:
        print red("Startup already!")
    else:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run('cd '+core_platform_path+' && java -jar newark-core-platform-'+core_version+'.jar 2>/dev/null >/dev/null')

    print green('Restart the core platform finished!')

# Shutdown the auth platform via the stop.sh scripts function.

def shutdown_auth():
    print green('Shutdown the auth platform via the stop.sh scripts.')

    ret = run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15")

    if ret:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15|xargs kill -9 2>/dev/null >/dev/null")
    else:
        print red("No such progress!")

    print green('Shutdown the auth platform finished!')

# Startup the auth platform via the startup.sh scripts function.
def startup_auth():
    print green('Startup the auth platform via the startup.sh scripts.')

    ret = run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15")

    if ret:
        print red('Startup already!')
    else:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run('cd '+auth_path+' && java -jar cmms-auth.jar 2>/dev/null >/dev/null')

    print green('Startup the authplatform finished!')

# Restart the auth platform via the startup.sh scripts function.
def restart_auth():
    print green('Restart the core platform via the restart.sh scripts.')

    ret = run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15")

    if ret:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15|xargs kill -9 2>/dev/null >/dev/null")
    else:
        print red("No such progress!")

    ret_new = run("ps -ef|grep cmms-auth|grep -v grep|cut -c 9-15")

    if ret_new:
        print red('Startup already!')
    else:
        with settings(hide('warnings', 'running', 'stdout', 'stderr'),warn_only=True):
            run('cd '+auth_path+' && java -jar cmms-auth.jar 2>/dev/null >/dev/null')

    print green('Restart the core platform finished!')

# Deploy core platform via mvn function.
def deploy_core_platform():
    print green('Deploy core platform via mvn.')

    # Remove the old deploy dir
    run('rm -rfv '+core_platform_path+'')
    run('rm -rfv '+auth_path+'')

    # Create necessary directory
    run('mkdir -p '+log_path+' 2>/dev/null >/dev/null')
    run('mkdir -p '+svn_core_platform_path+' 2>/dev/null >/dev/null')
    run('mkdir -p '+svn_core_platform_target_path+' 2>/dev/null >/dev/null')
    run('mkdir -p '+core_platform_path+' 2>/dev/null >/dev/null')

    # Update the service address. Use the local ip address by default.
    run("sed -i 's/^service_addr=.*$/service_addr=http:\/\/"+ip_addr+":8789\/auth\//g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the database url.
    run("sed -i 's/^url=.*$/url=jdbc:mysql:\/\/"+db_addr+":"+db_port+"\/cmms_auth/g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the database username.
    run("sed -i 's/^username=.*$/username="+db_usr+"/g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the database password.
    run("sed -i 's/^password=.*$/password="+db_pwd+"/g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the authentication server host.
    run("sed -i 's/^authentication_server_host_name=.*$/authentication_server_host_name="+ip_addr+"/g' "+svn_core_platform_config_api_path+"/osgi-util-config.properties")

    # Update the memcached server ip.
    run("sed -i 's/^memcached_server_name=.*$/memcached_server_name="+memcached_ip+"/g' "+svn_core_platform_config_api_path+"/osgi-util-config.properties")

    # Update the memcached server port.
    run("sed -i 's/^memcached_server_port=.*$/memcached_server_port="+memcached_port+"/g' "+svn_core_platform_config_api_path+"/osgi-util-config.properties")

    # Update the memcached server ip.
    run("sed -i 's/^memcached_server_name=.*$/memcached_server_name="+memcached_ip+"/g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the memcached server port.
    run("sed -i 's/^memcached_server_port=.*$/memcached_server_port="+memcached_port+"/g' "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")

    # Update the bundles directory.
    run("sed -i 's/^platform\.bundles\.root\.dir=.*$/platform\.bundles\.root\.dir="+core_platform_bundles_path+"/g' "+svn_core_platform_config_path+"/osgi-container.properties")

    # Update the api service configuration.
    run("sed -i 's/address=.*$/address=\"http:\/\/"+ip_addr+":"+api_port+"\/\" \>/g' "+svn_core_platform_config_api_path+"/api-service.xml")

    # Update the database configuration in the hibernate.cfg.xml.
    #run("sed -i 's/jdbc:mysql:\/\/.*$/jdbc:mysql:\/\/"+db_addr+":"+db_port+"\/cmms\<\/property\>/g' "+svn_core_platform_dao_path+"/hibernate.cfg.xml")

    # Update the database configuration in the persistence.xml.
    #run("sed -i 's/jdbc:mysql:\/\/.*$/jdbc:mysql:\/\/"+db_addr+":"+db_port+"\/cmms\"\/\>/g' "+svn_core_platform_dao_path+"/META-INF/persistence.xml")

    # Remove the end of configuration file. ^M mark.
    run("dos2unix "+svn_core_platform_config_auth_path+"/osgi-auth-config.properties")
    run("dos2unix "+svn_core_platform_config_api_path+"/osgi-util-config.properties")
    run("dos2unix "+svn_core_platform_config_path+"/osgi-container.properties")

    # Convert the configuration file.
    #run("dos2unix "+svn_core_platform_dao_path+"/hibernate.cfg.xml")
    #run("dos2unix "+svn_core_platform_dao_path+"/META-INF/persistence.xml")

    with cd(svn_core_platform_path):
        # Print waiting info.
        print ''
        print red('Please wait the deploy process until it automatically exit...')

        # Install the necessary jar.

        # Clear the core platform deploy log.
        run('echo "" > '+log_path+'/core_deploy.log')
        run('mvn install:install-file -Dfile='+svn_core_platform_path+'/lib/org.eclipse.osgi_3.10.0.v20140606-1445.jar -DgroupId=org.eclipse.osgi -DartifactId=org.eclipse.osgi -Dversion=3.10.0.v20140606 -Dclassifier=1445 -Dpackaging=jar > '+log_path+'/core_deploy.log')

        run('mvn install:install-file -Dfile='+svn_core_platform_path+'/lib/org.eclipse.osgi.services_3.4.0.v20140312-2051.jar -DgroupId=org.eclipse.osgi -DartifactId=org.eclipse.osgi.services -Dversion=3.4.0.v20140312 -Dclassifier=2051 -Dpackaging=jar >> '+log_path+'/core_deploy.log')

        # Pack the core platform use mvn command.
        run('mvn clean install >> '+log_path+'/core_deploy.log')

        # Remove the useless directory.
        run('rm -rf '+svn_core_platform_target_path+'/'+'classes')
        run('rm -rf '+svn_core_platform_target_path+'/'+'maven-archiver')
        run('rm -rf '+svn_core_platform_target_path+'/'+'maven-status')

    # Copy the packed core platform to the deploy directory.
    run('cp -rv '+svn_core_platform_target_path+'/'+'* '+core_platform_path+' 2>/dev/null >/dev/null')

    # Change the privileges of scripts. Make it executable.
    run('chmod +x '+core_platform_path+'/'+'*.sh')

    # Remove the auth directory.
    run("rm -rfv "+core_platform_config_path+"/auth")

    print green('Congratulations! Deploy core platform finished!')

# Deploy auth platform via mvn.
def deploy_auth_platform():
    print green('Deploy auth platform via mvn.')

    # Create necessary directory
    run('mkdir -p '+svn_core_platform_target_path+' 2>/dev/null >/dev/null')
    run('mkdir -p '+auth_path+' 2>/dev/null >/dev/null')

    # Copy the packed core platform to the deploy directory.
    run("cp -r "+svn_core_platform_target_path+"/"+"* "+auth_path)

    # Change the privileges of scripts. Make it executable.
    run("chmod +x "+auth_path+"/"+"*.sh")

    # Update the bundels dir
    run("sed -i 's/^platform\.bundles\.root\.dir=.*$/platform\.bundles\.root\.dir="+auth_platform_bundles_path+"/g' "+auth_platform_config_path+"/osgi-container.properties")

    # Remove the busi directory.
    run("rm -rf "+auth_path+"/bundles/busi")

    # Rename the jar.
    with cd(auth_path):
        sudo('./rename.sh '+core_platform_jar+' '+auth_platform_jar+'')

    # Optimize the stop scripts
    run("sed -i 's/"+core_jar+"/"+auth_jar+"/g' "+auth_path+"/stop.sh")

    # Remove the end of configuration file. ^M mark.
    run("dos2unix "+auth_platform_config_auth_path+"/osgi-auth-config.properties")
    run("dos2unix "+auth_platform_config_api_path+"/osgi-util-config.properties")
    run("dos2unix "+auth_platform_config_path+"/osgi-container.properties")

    # Remove the api directory.
    run("rm -rf "+auth_platform_config_path+"/api")

    # Remove the newarkstg-osgi-auth_version.jar.
    run('rm -rf '+core_platform_path+'/bundles/platform/newarkstg-osgi-auth_'+core_version+'.jar')

    # Remove the newarkstg-osgi-util_version.jar.
    run('rm -rf '+auth_path+'/bundles/platform/newark-osgi-util_'+core_version+'.jar')

    print green('Congratulations! Deploy auth platform finished!')

# Update the database setting function.
def update_database_setting():

    print('Update the database setting.')
    # Create the temp dir.
    run('mkdir -p ~/temp 2>/dev/null >/dev/null')

    # Copy the dao jar.
    run("cp -v "+core_platform_path+"/bundles/busi/service_impl/com.newarkstg.cmms.dao_"+core_version+".jar ~/temp")

    # Unzip the dao jar.
    run("cd ~/temp && jar xvf ~/temp/com.newarkstg.cmms.dao_"+core_version+".jar")

    # Update the database configuration in the hibernate.cfg.xml.
    run("sed -i 's/jdbc:mysql:\/\/.*$/jdbc:mysql:\/\/"+db_addr+":"+db_port+"\/cmms\<\/property\>/g' ~/temp/hibernate.cfg.xml")

    # Update the database configuration in the persistence.xml.
    run("sed -i 's/jdbc:mysql:\/\/.*$/jdbc:mysql:\/\/"+db_addr+":"+db_port+"\/cmms\"\/\>/g' ~/temp/META-INF/persistence.xml")

    # Remove the old jar.
    run("rm -rf ~/temp/com.newarkstg.cmms.dao_"+core_version+".jar")

    # Convert the configuration file.
    run("dos2unix ~/temp/hibernate.cfg.xml")
    run("dos2unix ~/temp/META-INF/persistence.xml")

    # Create the dao jar.
    run("cd ~/temp && jar cvfM com.newarkstg.cmms.dao_"+core_version+".jar com hibernate.cfg.xml META-INF")

    # Copy the jar to the core platform.
    run("cp -v ~/temp/com.newarkstg.cmms.dao_"+core_version+".jar"+" "+core_platform_path+"/bundles/busi/service_impl")

    # Remove the temp directory.
    run("rm -rfv ~/temp")

    print('Update the database setting finished!')

def deploy_prepare():
    print green('Deploy prepared. Run as root.')

    # Install jdk 1.8.25.
    print red('This program require jdk 1.8.25. Make sure jdk and tomcat work out before all of your operations.')

    # Install maven.
    print green('Insall maven.')
    run("wget http://apache.fayea.com/apache-mirror/maven/maven-3/3.2.3/binaries/apache-maven-3.2.3-bin.zip")
    run("unzip -q apache-maven-3.2.3-bin.zip")
    run("mv apache-maven-3.2.3 /usr/local/maven")
    run("echo 'export M2_HOME=/usr/local/maven' >> /etc/profile")
    run("echo 'export PATH=$PATH:$M2_HOME/bin' >> /etc/profile")
    run("source /etc/profile")
    run("rm -rf apache-maven-3.2.3-bin.zip apache-maven-3.2.3")
    run("mvn -version")

    log_path='~/logs'

    run('mkdir -p '+log_path+' 2>/dev/null >/dev/null')

    # Clear the install_requirement.log
    run('echo "" > '+log_path+'/install_requirement.log')

    # Install Python and fabric on the remote server.
    run("apt-get install dos2unix python python-pip python-dev subversion subversion-tools -y > "+log_path+"/install_requirement.log")
    run("pip install fabric >> "+log_path+"/install_requirement.log")

    print green('Deploy prepared finished.')

```

### 5.4 配置文件概述 ###

完整配置文件内容如下：

``` bash
# Database config section.
[database]
# Database address.
db_addr=
# Database port.
db_port=
# Database username.
db_usr=
# Datbase password.
db_pwd=

# Remote server section.
[remote]
# Remote server ip.
remote_ip=
# Remote server port.
remote_port=
# Remote server username.
remote_usr=
# Remote server password.
remote_pwd=

# SVN path section.
[svn_path]
# Svn main directory of newarkstg repo.
svn_ns_dir=
# Svn core platform path.
svn_core_platform_path=
# Svn core platform path config path
svn_core_platform_config_path=
# Svn core platform path config auth path
svn_core_platform_config_auth_path=
# Svn core platform path config api path
svn_core_platform_config_api_path=
# Svn core platform dao path
svn_core_platform_dao_path=
# Svn core platform target path.
svn_core_platform_target_path=

# SVN configuration section.
[svn]
svn_username=
svn_password=
svn_url=

# Core platform path config section.
[core_path]
# Core platform path.
core_platform_path=
# Core platform config path.
core_platform_config_path=
# Core platform config api path
core_platform_config_api_path=
# Core platform config auth path
core_platform_config_auth_path=
# Core platform bundles path
core_platform_bundles_path=

# Auth platform path config section.
[auth_path]
# Auth platform path.
auth_path=
# Auth platform configuration path.
auth_platform_config_path=
# Auth platform configuration api path.
auth_platform_config_api_path=
# Auth platform configuration auth path.
auth_platform_config_auth_path=
# Authplatform bundles path
auth_platform_bundles_path=

# Memcached configuration section.
[memcached]
# Memcached server ip.
memcached_ip=
# Memcached server port.
memcached_port=

# Other configuration section
[other]
# Core platform version.
core_version=
# Remote log path
remote_log_path=
# Api port
api_port=
# Core platform jar name
core_platform_jar=
# Auth platform jar name
auth_platform_jar=
# Core jar
core_jar=
# Auth jar
auth_jar=
```

接下来，我逐一进行讲解。

配置文件包括以下段：database、remote、svn_path、svn、core_path、auth_path、memcached和other。

每个段的说明如下：

* database 该段定义数据库配置。
	* db_addr MySQL数据库地址。
	* db_usr MySQL数据库用户名。
	* db_pwd MySQL数据库密码。
	* db_port MySQL数据库端口，默认为3306。
* remote 该段定义远程服务器登录信息。
	* remote_ip 部署远程服务器IP。
	* remote_port 部署远程服务器端口。
	* remote_usr 部署远程服务器用户名。
	* remote_pwd 部署远程服务器密码。
* svn_path 该段定义远程服务器SVN目录。
	* svn_ns_dir 项目主SVN目录。
	* svn_core_platform_path 业务平台SVN目录。
	* svn_core_platform_config_path 业务平台主配置文件目录。
	* svn_core_platform_config_auth_path 业务平台AUTH配置文件目录。
	* svn_core_platform_config_api_path 业务平台API配置文件目录。
	* svn_core_platform_dao_path 业务平台DAO目录。
	* svn_core_platform_target_path 业务平台Target目录，用于存放打包后的文件。
* svn 该段定义SVN的账户信息。
	* svn_username SVN用户名。
	* svn_password SVN密码。
	* svn_url SVN地址。
* core_path 该段定义部署后的业务平台目录。
	* core_platform_path 业务平台主目录。
	* core_platform_config_path 业务平台配置文件目录。
	* core_platform_config_api_path 业务平台API配置文件目录。
	* core_platform_config_auth_path 业务平台AUTH配置文件目录。
	* core_platform_bundles_path 业务平台Bundles目录。
* auth_path 该段定义部署后的认证平台目录。
	* auth_path 认证平台主目录。
	* auth_platform_config_path 认证平台配置文件目录。
	* auth_platform_config_api_path 认证平台API配置文件目录。
	* auth_platform_config_auth_path 认证平台AUTH配置文件目录。
	* auth_platform_bundles_path 认证平台Bundles目录。
* memcached 该段定义Memcached相关信息。
	* memcached_ip Memcached服务器IP。
	* memcached_port Memcached服务器端口。
* other 该段定义其他配置信息。
	* core_version 业务平台版本号。
	* remote_log_path 远程服务器日志文件目录，用于存放部署业务平台产生的日志。
	* api_port 业务平台的API端口。
	* core_platform_jar 打包生成的业务平台jar包，完整文件名。
	* auth_platform_jar 认证平台jar包，完整文件名。
	* core_jar 业务平台jar包，不带后缀。
	* auth_jar 认证平台jar包，不带后缀。

**注：以上是所有的配置项，请酌情修改。**

## 6.脚本使用 ##

如果您是第一次使用该脚本打包，请依次执行如下命令：

``` bash
# 第一步，编辑配置文件；
vim config.conf

# 第二步，显示帮助；
./auto_deploy_app_remote.py -h

# 第三步，准备部署（此步可以略过，因为环境已经搭建好）；
./auto_deploy_app_remote.py -p

# 第四步，检出项目；
./auto_deploy_app_remote.py -c

# 第五步，部署业务平台；
./auto_deploy_app_remote.py -d

# 第六步，部署认证平台；
./auto_deploy_app_remote.py -w

# 第七步，修改数据库配置；
./auto_deploy_app_remote.py -x

# 第八步，启动认证平台
./auto_deploy_app_remote.py -k

# 第九布，启动业务平台
./auto_deploy_app_remote.py -t
```


**注：第八步可以使用「./auto_deploy_app_remote.py -g」代替，第九步可以使用「./auto_deploy_app_remote.py -r」代替。**

如果您是使用该脚本更新项目，请依次执行如下命令：

``` bash
# 第一步，如有需要，编辑配置文件；
vim config.conf

# 第二步，显示帮助；
./auto_deploy_app_remote.py -h

# 第三步，更新项目；
./auto_deploy_app_remote.py -u

# 第四步，关闭认证平台；
./auto_deploy_app_remote.py -a

# 第五步，关闭业务平台
./auto_deploy_app_remote.py -s

# 第六步，部署业务平台；
./auto_deploy_app_remote.py -d

# 第七步，部署认证平台；
./auto_deploy_app_remote.py -w

# 第八步，修改数据库配置；
./auto_deploy_app_remote.py -x

# 第九步，启动认证平台
./auto_deploy_app_remote.py -k

# 第十布，启动业务平台
./auto_deploy_app_remote.py -t
```

**注：第九步可以使用「./auto_deploy_app_remote.py -g」代替，第十步可以使用「./auto_deploy_app_remote.py -r」代替。**

Enjoy！

## 7.GitHub地址 ##

python-auto-deploy：<a href="https://github.com/dbarobin/python-auto-deploy" target="_blank">https://github.com/dbarobin/python-auto-deploy</a>

## 8.项目说明 ##
auto_deploy_app_v2: 适用于Linux。
auto_deploy_app_windows: 适用于Windows。

***

**本站推广**

币安是全球领先的数字货币交易平台，提供比特币、以太坊、BNB 以及 USDT 交易。

> 币安注册: [https://accounts.binancezh.co/cn/register/?ref=11190872](https://accounts.binancezh.co/cn/register/?ref=11190872)
> 邀请码: **11190872**

***

–EOF–

版权声明：自由转载-非商用-非衍生-保持署名<a href="http://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh" target="_blank">（创意共享4.0许可证）</a>

## 9.作者信息 ##

robin

* Robin Wen
* <a href="mailto:blockxyz@gmail.com"><img src="https://cdn.dbarobin.com/7yOaC7C.png" title="Robin's Gmail" border="0" height="16px" width="16px" alt="Robin's Gmail" /></a>
* <a href="https://github.com/dbarobin" target="_blank"><i class="fa fa-github"></i></a>
* <a href="https://dbarobin.com/" target="_blank"><img src="https://cdn.dbarobin.com/dEfMkyt.jpg" title="Robin's Blog" border="0" alt="Robin's Blog" height="16px" width="16px" /></a>
* <a href="http://blog.csdn.net/justdb" target="_blank"><img src="https://cdn.dbarobin.com/BROigUO.jpg" title="DBA@Robin's CSDN" height="16px" width="16px" border="0" alt="DBA@Robin's CSDN" /></a>
