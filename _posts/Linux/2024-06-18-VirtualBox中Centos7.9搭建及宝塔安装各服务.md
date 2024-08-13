---
layout: post
categories: Linux
title: VirtualBox中Centos7.9搭建及宝塔安装各服务
meta: VirtualBox中Centos7.9搭建及宝塔安装各服务
---
* content
{:toc}

## 引言

VirtualBox中搭建Centos7.9并设置共享文件夹，
参考博文 <https://ibaiyang.github.io/blog/linux/2023/10/27/VirtualBox中Centos7.9搭建及Docker安装各服务.html> 下面这部分：

Centos7.9环境搭建
* 虚拟机安装
* CPU开启虚拟机支持功能
* Centos7.9安装
* SSH客户端连接
* 网络配置
* 防火墙配置
* 更换yum源

共享文件夹
* 磁盘相关命令

这里使用的win11系统，未分区，所有软件和文档都在C盘，共享文件夹为 C:\www 指向 /media/sf_www 。

## Git安装

使用命令：
> yum install git

明细：
```
[root@localhost ~]# yum install git
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
* base: mirrors.aliyun.com
* extras: mirrors.aliyun.com
* updates: mirrors.aliyun.com
  正在解决依赖关系
  --> 正在检查事务
  ---> 软件包 git.x86_64.0.1.8.3.1-25.el7_9 将被 安装
  --> 正在处理依赖关系 perl-Git = 1.8.3.1-25.el7_9，它被软件包 git-1.8.3.1-25.el7_            9.x86_64 需要
  --> 正在处理依赖关系 rsync，它被软件包 git-1.8.3.1-25.el7_9.x86_64 需要
  --> 正在处理依赖关系 perl(Term::ReadKey)，它被软件包 git-1.8.3.1-25.el7_9.x86_64             需要
  --> 正在处理依赖关系 perl(Git)，它被软件包 git-1.8.3.1-25.el7_9.x86_64 需要
  --> 正在处理依赖关系 perl(Error)，它被软件包 git-1.8.3.1-25.el7_9.x86_64 需要
  --> 正在检查事务
  ---> 软件包 perl-Error.noarch.1.0.17020-2.el7 将被 安装
  ---> 软件包 perl-Git.noarch.0.1.8.3.1-25.el7_9 将被 安装
  ---> 软件包 perl-TermReadKey.x86_64.0.2.30-20.el7 将被 安装
  ---> 软件包 rsync.x86_64.0.3.1.2-12.el7_9 将被 安装
  --> 解决依赖关系完成

依赖关系解决

================================================================================
Package                架构         版本                   源             大小
================================================================================
正在安装:
git                    x86_64       1.8.3.1-25.el7_9       updates       4.4 M
为依赖而安装:
perl-Error             noarch       1:0.17020-2.el7        base           32 k
perl-Git               noarch       1.8.3.1-25.el7_9       updates        56 k
perl-TermReadKey       x86_64       2.30-20.el7            base           31 k
rsync                  x86_64       3.1.2-12.el7_9         updates       408 k

事务概要
================================================================================
安装  1 软件包 (+4 依赖软件包)

总下载量：4.9 M
安装大小：23 M
Is this ok [y/d/N]: y
Downloading packages:
(1/5): perl-Error-0.17020-2.el7.noarch.rpm                 |  32 kB   00:00
(2/5): perl-Git-1.8.3.1-25.el7_9.noarch.rpm                |  56 kB   00:00
(3/5): perl-TermReadKey-2.30-20.el7.x86_64.rpm             |  31 kB   00:00
(4/5): rsync-3.1.2-12.el7_9.x86_64.rpm                     | 408 kB   00:01
(5/5): git-1.8.3.1-25.el7_9.x86_64.rpm                     | 4.4 MB   00:12
--------------------------------------------------------------------------------
总计                                               414 kB/s | 4.9 MB  00:12
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
正在安装    : 1:perl-Error-0.17020-2.el7.noarch                           1/5
正在安装    : rsync-3.1.2-12.el7_9.x86_64                                 2/5
正在安装    : perl-TermReadKey-2.30-20.el7.x86_64                         3/5
正在安装    : perl-Git-1.8.3.1-25.el7_9.noarch                            4/5
正在安装    : git-1.8.3.1-25.el7_9.x86_64                                 5/5
验证中      : perl-TermReadKey-2.30-20.el7.x86_64                         1/5
验证中      : 1:perl-Error-0.17020-2.el7.noarch                           2/5
验证中      : git-1.8.3.1-25.el7_9.x86_64                                 3/5
验证中      : perl-Git-1.8.3.1-25.el7_9.noarch                            4/5
验证中      : rsync-3.1.2-12.el7_9.x86_64                                 5/5

已安装:
git.x86_64 0:1.8.3.1-25.el7_9

作为依赖被安装:
perl-Error.noarch 1:0.17020-2.el7        perl-Git.noarch 0:1.8.3.1-25.el7_9
perl-TermReadKey.x86_64 0:2.30-20.el7    rsync.x86_64 0:3.1.2-12.el7_9

完毕！
[root@localhost ~]#
[root@localhost ~]# git --version
git version 1.8.3.1
[root@localhost ~]#
```

## 宝塔安装

查看宝塔官网 <https://www.bt.cn> 的 下载安装教程。

这里使用命令：
> yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_6.0.sh && sh install.sh ed8484bec

不用切换文件夹，安装过程中会询问是否安装到 /www 目录下，默认就可以。

明细：
```
[root@localhost ~]# cd /usr/local
[root@localhost local]#
[root@localhost local]# yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_6.0.sh && sh install.sh ed8484bec
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
软件包 wget-1.14-18.el7_6.1.x86_64 已安装并且是最新版本
无须任何处理
--2024-06-17 19:31:36--  https://download.bt.cn/install/install_6.0.sh
正在解析主机 download.bt.cn (download.bt.cn)... 42.157.129.47, 240e:97e:4000:33f:103:212:48:148
正在连接 download.bt.cn (download.bt.cn)|42.157.129.47|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：37929 (37K) [application/octet-stream]
正在保存至: “install.sh”

100%[==================================================>] 37,929      20.4KB/s 用时 1.8s

2024-06-17 19:31:43 (20.4 KB/s) - 已保存 “install.sh” [37929/37929])


+----------------------------------------------------------------------
| Bt-WebPanel FOR CentOS/Ubuntu/Debian
+----------------------------------------------------------------------
| Copyright © 2015-2099 BT-SOFT(http://www.bt.cn) All rights reserved.
+----------------------------------------------------------------------
| The WebPanel URL will be http://SERVER_IP:32283 when installed.
+----------------------------------------------------------------------
| 为了您的正常使用，请确保使用全新或纯净的系统安装宝塔面板，不支持已部署项目/环境的系统安装
+----------------------------------------------------------------------

Do you want to install Bt-Panel to the /www directory now?(y/n): y
---------------------------------------------
Selected download node...
Download node: https://download.bt.cn
---------------------------------------------
Synchronizing system time...
curl: (28) Resolving timed out after 3167 milliseconds
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package ntp-4.2.6p5-29.el7.centos.2.x86_64 already installed and latest version
Nothing to do
17 Jun 19:34:53 ntpdate[3259]: adjust time server 45.125.1.20 offset -0.031641 sec
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package libcurl-devel-7.29.0-59.el7_9.2.x86_64 already installed and latest version
Package wget-1.14-18.el7_6.1.x86_64 already installed and latest version
Package 2:tar-1.26-35.el7.x86_64 already installed and latest version
Package gcc-4.8.5-44.el7.x86_64 already installed and latest version
Package 1:make-3.82-24.el7.x86_64 already installed and latest version
Package zip-3.0-11.el7.x86_64 already installed and latest version
Package unzip-6.0-24.el7_9.x86_64 already installed and latest version
Package 1:openssl-1.0.2k-26.el7_9.x86_64 already installed and latest version
Package 1:openssl-devel-1.0.2k-26.el7_9.x86_64 already installed and latest version
Package gcc-4.8.5-44.el7.x86_64 already installed and latest version
Package libxml2-2.9.1-6.el7_9.6.x86_64 already installed and latest version
Package libxml2-devel-2.9.1-6.el7_9.6.x86_64 already installed and latest version
Package libxslt-1.1.28-6.el7.x86_64 already installed and latest version
Package libxslt-devel-1.1.28-6.el7.x86_64 already installed and latest version
Package libxslt-python-1.1.28-6.el7.x86_64 already installed and latest version
Package zlib-1.2.7-21.el7_9.x86_64 already installed and latest version
Package zlib-devel-1.2.7-21.el7_9.x86_64 already installed and latest version
Package libjpeg-turbo-devel-1.2.90-8.el7.x86_64 already installed and latest version
Package 2:libpng-devel-1.5.13-8.el7.x86_64 already installed and latest version
Package libwebp-0.3.0-11.el7.x86_64 already installed and latest version
Package libwebp-devel-0.3.0-11.el7.x86_64 already installed and latest version
Package freetype-2.8-14.el7_9.1.x86_64 already installed and latest version
Package freetype-devel-2.8-14.el7_9.1.x86_64 already installed and latest version
Package lsof-4.87-6.el7.x86_64 already installed and latest version
Package pcre-8.32-17.el7.x86_64 already installed and latest version
Package pcre-devel-8.32-17.el7.x86_64 already installed and latest version
No package vixie-cron available.
Package crontabs-1.11-6.20121102git.el7.noarch already installed and latest version
Package icu-50.2-4.el7_7.x86_64 already installed and latest version
Package libicu-devel-50.2-4.el7_7.x86_64 already installed and latest version
Package c-ares-1.10.0-3.el7_9.1.x86_64 already installed and latest version
Package libffi-devel-3.0.13-19.el7.x86_64 already installed and latest version
Package bzip2-devel-1.0.6-13.el7.x86_64 already installed and latest version
Package ncurses-devel-5.9-14.20130511.el7_4.x86_64 already installed and latest version
Package sqlite-devel-3.7.17-8.el7_7.1.x86_64 already installed and latest version
Package readline-devel-6.2-11.el7.x86_64 already installed and latest version
Package 1:tk-devel-8.5.13-6.el7.x86_64 already installed and latest version
Package gdbm-devel-1.10-8.el7.x86_64 already installed and latest version
No package db4-devel available.
Package 14:libpcap-devel-1.5.3-13.el7_9.x86_64 already installed and latest version
Package xz-devel-5.2.2-2.el7_9.x86_64 already installed and latest version
Package qrencode-3.4.1-3.el7.x86_64 already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package libxslt-1.1.28-6.el7.x86_64 already installed and latest version
Package libxslt-devel-1.1.28-6.el7.x86_64 already installed and latest version
Package libxslt-python-1.1.28-6.el7.x86_64 already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package libjpeg-turbo-devel-1.2.90-8.el7.x86_64 already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package vixie-cron available.
Error: Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package db4-devel available.
Error: Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package epel-release.noarch 0:7-11 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

============================================================================================
 Package                   Arch                Version            Repository           Size
============================================================================================
Installing:
 epel-release              noarch              7-11               extras               15 k

Transaction Summary
============================================================================================
Install  1 Package

Total download size: 15 k
Installed size: 24 k
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
epel-release-7-11.noarch.rpm   FAILED
http://mirrors.aliyun.com/centos/7/extras/x86_64/Packages/epel-release-7-11.noarch.rpm: [Errno 14] curl#6 - "Could not resolve host: mirrors.aliyun.com; Unknown error"
Trying other mirror.
epel-release-7-11.noarch.rpm   FAILED
http://mirrors.cloud.aliyuncs.com/centos/7/extras/x86_64/Packages/epel-release-7-11.noarch.rpm: [Errno 14] curl#6 - "Could not resolve host: mirrors.cloud.aliyuncs.com; Unknown error"
Trying other mirror.
epel-release-7-11.noarch.rpm   FAILED
http://mirrors.aliyuncs.com/centos/7/extras/x86_64/Packages/epel-release-7-11.noarch.rpm: [Errno 12] Timeout on http://mirrors.aliyuncs.com/centos/7/extras/x86_64/Packages/epel-release-7-11.noarch.rpm: (28, 'Resolving timed out after 30891 milliseconds')
Trying other mirror.
epel-release-7-11.noarch.rpm   FAILED
http://mirrors.aliyuncs.com/centos/7/extras/x86_64/Packages/epel-release-7-11.noarch.rpm: [Errno 14] curl#6 - "Could not resolve host: mirrors.aliyuncs.com; Unknown error"
Trying other mirror.


Error downloading packages:
  epel-release-7-11.noarch: [Errno 256] No more mirrors to try.

curl: (28) Resolving timed out after 3518 milliseconds
OS: el - 7
===============================================
正在下载面板运行环境，请稍等...............
===============================================
--2024-06-17 19:36:53--  https://download.bt.cn/install/pyenv/pyenv-el7-x64.tar.gz
Resolving download.bt.cn (download.bt.cn)... failed: Connection timed out.
wget: unable to resolve host address ‘download.bt.cn’
---------------------------------------------
Selected download node...
Download node: https://ctcc2-node.bt.cn
---------------------------------------------
--2024-06-17 19:37:39--  https://ctcc2-node.bt.cn/install/pyenv/pyenv-el7-x64.tar.gz
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 105412602 (101M) [application/octet-stream]
Saving to: ‘/www/pyenv.tar.gz’

100%[==================================================>] 105,412,602  248KB/s   in 5m 48s

2024-06-17 19:43:28 (295 KB/s) - ‘/www/pyenv.tar.gz’ saved [105412602/105412602]

Install python env...
Python 3.7.9
--2024-06-17 19:43:32--  https://ctcc2-node.bt.cn/install/src/bt6.init
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 14613 (14K) [application/octet-stream]
Saving to: ‘/etc/init.d/bt’

100%[==================================================>] 14,613      --.-K/s   in 0s

2024-06-17 19:43:33 (205 MB/s) - ‘/etc/init.d/bt’ saved [14613/14613]

--2024-06-17 19:43:33--  https://ctcc2-node.bt.cn/install/public.sh
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4560 (4.5K) [application/octet-stream]
Saving to: ‘/www/server/panel/install/public.sh’

100%[==================================================>] 4,560       --.-K/s   in 0s

2024-06-17 19:43:33 (813 MB/s) - ‘/www/server/panel/install/public.sh’ saved [4560/4560]

==============================================
正在下载面板文件,请稍等...................
==============================================
--2024-06-17 19:43:33--  https://ctcc2-node.bt.cn/install/src/panel6.zip
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 35928415 (34M) [application/zip]
Saving to: ‘panel.zip’

100%[==================================================>] 35,928,415  2.90MB/s   in 12s

2024-06-17 19:43:45 (2.92 MB/s) - ‘panel.zip’ saved [35928415/35928415]

--2024-06-17 19:43:46--  https://ctcc2-node.bt.cn/install/src/bt7.init
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 15900 (16K) [application/octet-stream]
Saving to: ‘/etc/init.d/bt’

100%[==================================================>] 15,900      --.-K/s   in 0s

2024-06-17 19:43:46 (365 MB/s) - ‘/etc/init.d/bt’ saved [15900/15900]

--2024-06-17 19:43:46--  https://ctcc2-node.bt.cn/install/src/bt7.init
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 15900 (16K) [application/octet-stream]
Saving to: ‘/www/server/panel/init.sh’

100%[==================================================>] 15,900      --.-K/s   in 0s

2024-06-17 19:43:47 (453 MB/s) - ‘/www/server/panel/init.sh’ saved [15900/15900]

--2024-06-17 19:43:47--  https://ctcc2-node.bt.cn/install/conf/softList.conf
Resolving ctcc2-node.bt.cn (ctcc2-node.bt.cn)... 103.212.48.148
Connecting to ctcc2-node.bt.cn (ctcc2-node.bt.cn)|103.212.48.148|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2216 (2.2K) [application/octet-stream]
Saving to: ‘/www/server/panel/data/softList.conf’

100%[==================================================>] 2,216       --.-K/s   in 0s

2024-06-17 19:43:47 (377 MB/s) - ‘/www/server/panel/data/softList.conf’ saved [2216/2216]

Starting Bt-Panel....   done
Starting Bt-Tasks...    done
username: z7khri3w
========================================
正在开启面板SSL，请稍等............
========================================
证书开启成功！
========================================

清理面板所有host
检测节点(0.5s)：download.bt.cn
当前可用下载节点：download.bt.cn

检测www节点(0.5S)：{'name': '主节点', 'url': 'www-node1.bt.cn'}
检测api节点(0.5S)：{'name': '主节点', 'url': 'api-node1.bt.cn'}
Stopping Bt-Tasks...    done
Stopping Bt-Panel...    done
Starting Bt-Panel....   done
Starting Bt-Tasks...    done
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package firewalld-0.6.3-13.el7_9.noarch already installed and latest version
Nothing to do
success
True
==================================================================
Congratulations! Installed successfully!
=============注意：首次打开面板浏览器将提示不安全=================

 请选择以下其中一种方式解决不安全提醒
 1、下载证书，地址：https://dg2.bt.cn/ssl/baota_root.pfx，双击安装,密码【www.bt.cn】
 2、点击【高级】-【继续访问】或【接受风险并继续】访问
 教程：https://www.bt.cn/bbs/thread-117246-1-1.html

========================面板账户登录信息==========================

 【云服务器】请在安全组放行 32283 端口
 外网面板地址: https://117.71.110.127:32283/94693025
 内网面板地址: https://10.0.2.15:32283/94693025
 username: z7khri3w
 password: 6aa9da0c

 浏览器访问以下链接，添加宝塔客服
 https://www.bt.cn/new/wechat_customer
==================================================================
Time consumed: 9 Minute!
[root@localhost local]#
```

重启可以使用：
> service bt status

```
[root@localhost local]# service bt status
Bt-Panel (pid 1784) already running
Bt-Task (pid 1817) already running
[root@localhost local]#
[root@localhost local]# systemctl status bt
● bt.service - LSB: starts bt
   Loaded: loaded (/etc/rc.d/init.d/bt; bad; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:systemd-sysv-generator(8)
[root@localhost local]#
[root@localhost local]# systemctl restart bt
[root@localhost local]#
[root@localhost local]# systemctl status bt
● bt.service - LSB: starts bt
   Loaded: loaded (/etc/rc.d/init.d/bt; bad; vendor preset: disabled)
   Active: active (exited) since 二 2024-06-18 10:12:00 CST; 2s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 3402 ExecStart=/etc/rc.d/init.d/bt start (code=exited, status=0/SUCCESS)

6月 18 10:11:59 localhost.localdomain systemd[1]: Starting LSB: starts bt...
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Panel... Bt-Panel (pid 178...ng
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Tasks... Bt-Tasks (pid 181...ng
6月 18 10:12:00 localhost.localdomain systemd[1]: Started LSB: starts bt.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost local]#
[root@localhost local]# systemctl stop bt
[root@localhost local]#
[root@localhost local]# systemctl status bt
● bt.service - LSB: starts bt
   Loaded: loaded (/etc/rc.d/init.d/bt; bad; vendor preset: disabled)
   Active: inactive (dead) since 二 2024-06-18 10:13:21 CST; 5s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 3461 ExecStop=/etc/rc.d/init.d/bt stop (code=exited, status=0/SUCCESS)
  Process: 3402 ExecStart=/etc/rc.d/init.d/bt start (code=exited, status=0/SUCCESS)

6月 18 10:11:59 localhost.localdomain systemd[1]: Starting LSB: starts bt...
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Panel... Bt-Panel (pid 178...ng
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Tasks... Bt-Tasks (pid 181...ng
6月 18 10:12:00 localhost.localdomain systemd[1]: Started LSB: starts bt.
6月 18 10:13:21 localhost.localdomain systemd[1]: Stopping LSB: starts bt...
6月 18 10:13:21 localhost.localdomain bt[3461]: Stopping Bt-Tasks...        done
6月 18 10:13:21 localhost.localdomain bt[3461]: Stopping Bt-Panel...        done
6月 18 10:13:21 localhost.localdomain systemd[1]: Stopped LSB: starts bt.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost local]#
[root@localhost local]# service bt status
Bt-Panel not running
Bt-Task not running
[root@localhost local]# service bt restart

清理面板所有host
当前[api-node]节点连接正常,{'name': '主节点', 'url': 'api-node1.bt.cn', 'ip': '42.157.129.47'}
检测节点(0.5s)：download.bt.cn
当前可用下载节点：download.bt.cn

检测www节点(0.5S)：{'name': '主节点', 'url': 'www-node1.bt.cn'}
检测www节点(0.5S)：{'name': '移动节点', 'url': 'www-node2.bt.cn'}
检测api节点(0.5S)：{'name': '主节点', 'url': 'api-node1.bt.cn'}
Stopping Bt-Tasks...    done
Stopping Bt-Panel...    done
Starting Bt-Panel....   done
Starting Bt-Tasks...    done
[root@localhost local]#
[root@localhost local]# service bt status
Bt-Panel (pid 3609) already running
Bt-Task (pid 3649) already running
[root@localhost local]#
[root@localhost local]# systemctl status bt
● bt.service - LSB: starts bt
   Loaded: loaded (/etc/rc.d/init.d/bt; bad; vendor preset: disabled)
   Active: inactive (dead) since 二 2024-06-18 10:13:21 CST; 1min 9s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 3461 ExecStop=/etc/rc.d/init.d/bt stop (code=exited, status=0/SUCCESS)
  Process: 3402 ExecStart=/etc/rc.d/init.d/bt start (code=exited, status=0/SUCCESS)

6月 18 10:11:59 localhost.localdomain systemd[1]: Starting LSB: starts bt...
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Panel... Bt-Panel (pid 178...ng
6月 18 10:12:00 localhost.localdomain bt[3402]: Starting Bt-Tasks... Bt-Tasks (pid 181...ng
6月 18 10:12:00 localhost.localdomain systemd[1]: Started LSB: starts bt.
6月 18 10:13:21 localhost.localdomain systemd[1]: Stopping LSB: starts bt...
6月 18 10:13:21 localhost.localdomain bt[3461]: Stopping Bt-Tasks...        done
6月 18 10:13:21 localhost.localdomain bt[3461]: Stopping Bt-Panel...        done
6月 18 10:13:21 localhost.localdomain systemd[1]: Stopped LSB: starts bt.
Warning: bt.service changed on disk. Run 'systemctl daemon-reload' to reload units.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost local]#
[root@localhost local]#
[root@localhost local]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since 一 2024-06-17 19:51:21 CST; 14h ago
     Docs: man:firewalld(1)
  Process: 859 ExecStart=/usr/sbin/firewalld --nofork --nopid $FIREWALLD_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 859 (code=exited, status=0/SUCCESS)

6月 17 18:30:05 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewal.....
6月 17 18:30:06 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall...n.
6月 17 18:30:06 localhost.localdomain firewalld[859]: WARNING: AllowZoneDrifting is ena....
6月 17 19:44:07 localhost.localdomain firewalld[859]: WARNING: ZONE_ALREADY_SET: public
6月 17 19:44:08 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 80:tcp
6月 17 19:44:09 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 22:tcp
6月 17 19:44:10 localhost.localdomain firewalld[859]: WARNING: AllowZoneDrifting is ena....
6月 17 19:49:31 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 32283:tcp
6月 17 19:50:57 localhost.localdomain systemd[1]: Stopping firewalld - dynamic firewal.....
6月 17 19:51:21 localhost.localdomain systemd[1]: Stopped firewalld - dynamic firewall...n.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost local]#
[root@localhost local]# systemctl stop firewalld
[root@localhost local]#
[root@localhost local]#
[root@localhost local]#
[root@localhost local]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since 一 2024-06-17 19:51:21 CST; 14h ago
     Docs: man:firewalld(1)
  Process: 859 ExecStart=/usr/sbin/firewalld --nofork --nopid $FIREWALLD_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 859 (code=exited, status=0/SUCCESS)

6月 17 18:30:05 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewal.....
6月 17 18:30:06 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall...n.
6月 17 18:30:06 localhost.localdomain firewalld[859]: WARNING: AllowZoneDrifting is ena....
6月 17 19:44:07 localhost.localdomain firewalld[859]: WARNING: ZONE_ALREADY_SET: public
6月 17 19:44:08 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 80:tcp
6月 17 19:44:09 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 22:tcp
6月 17 19:44:10 localhost.localdomain firewalld[859]: WARNING: AllowZoneDrifting is ena....
6月 17 19:49:31 localhost.localdomain firewalld[859]: WARNING: ALREADY_ENABLED: 32283:tcp
6月 17 19:50:57 localhost.localdomain systemd[1]: Stopping firewalld - dynamic firewal.....
6月 17 19:51:21 localhost.localdomain systemd[1]: Stopped firewalld - dynamic firewall...n.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost local]#
```

关闭防火墙：
> systemctl stop firewalld

查看ip：
```
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:9c:f7:dd brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 86268sec preferred_lft 86268sec
    inet6 fe80::ae91:c0f8:8fa4:a7c/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:50:24:27 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.101/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 468sec preferred_lft 468sec
    inet6 fe80::f1c0:b3cd:6a0:93aa/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]#
```

安装完成后，浏览器访问 192.168.56.101:32283/94693025 ，发现无法访问，折腾了好长时间，无奈放弃。

## phpStudy

最后放弃虚拟机和宝塔，使用了phpStudy。在Windows环境使用phpStudy用来快速搭建开发环境是个不错的选择。
不过只有Nginx、MySQL、PHP、Composer，对于一般开发来说够用了。



## 阿里云ECS安装宝塔

下面记录下阿里云ECS安装宝塔，作为日后参考。

翻阅宝塔安装说明 <https://www.bt.cn/new/download.html>

```
[root@iZuf63na909xjyb4nymwotZ ~]# yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_lts.sh && bash install.sh ed8484bec
Loaded plugins: fastestmirror
Determining fastest mirrors
base                                                                                                                             | 3.6 kB  00:00:00
epel                                                                                                                             | 4.3 kB  00:00:00
extras                                                                                                                           | 2.9 kB  00:00:00
updates                                                                                                                          | 2.9 kB  00:00:00
(1/7): base/7/x86_64/group_gz                                                                                                    | 153 kB  00:00:00
(2/7): epel/x86_64/group                                                                                                         | 399 kB  00:00:00
(3/7): epel/x86_64/updateinfo                                                                                                    | 1.0 MB  00:00:00
(4/7): extras/7/x86_64/primary_db                                                                                                | 253 kB  00:00:00
(5/7): epel/x86_64/primary_db                                                                                                    | 8.7 MB  00:00:00
(6/7): base/7/x86_64/primary_db                                                                                                  | 6.1 MB  00:00:00
(7/7): updates/7/x86_64/primary_db                                                                                               |  27 MB  00:00:00
Package wget-1.14-18.el7_6.1.x86_64 already installed and latest version
Nothing to do
--2024-08-13 23:03:48--  https://download.bt.cn/install/install_lts.sh
Resolving download.bt.cn (download.bt.cn)... 42.157.128.146, 240e:97e:4000:33f:103:212:48:148
Connecting to download.bt.cn (download.bt.cn)|42.157.128.146|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 51960 (51K) [application/octet-stream]
Saving to: ‘install.sh’

100%[==============================================================================================================>] 51,960      --.-K/s   in 0.03s

2024-08-13 23:03:48 (1.94 MB/s) - ‘install.sh’ saved [51960/51960]


+----------------------------------------------------------------------
| Bt-WebPanel FOR CentOS/Ubuntu/Debian
+----------------------------------------------------------------------
| Copyright © 2015-2099 BT-SOFT(http://www.bt.cn) All rights reserved.
+----------------------------------------------------------------------
| The WebPanel URL will be http://SERVER_IP:39625 when installed.
+----------------------------------------------------------------------
| 为了您的正常使用，请确保使用全新或纯净的系统安装宝塔面板，不支持已部署项目/环境的系统安装
+----------------------------------------------------------------------
| 当前您正在安装的是宝塔面板稳定版-9.0-lts
+----------------------------------------------------------------------

Do you want to install Bt-Panel to the /www directory now?(y/n): y
Loading mirror speeds from cached hostfile
Package wget-1.14-18.el7_6.1.x86_64 already installed and latest version
Nothing to do
---------------------------------------------
Selected download node...
Download node: https://download.bt.cn
---------------------------------------------
正在设置虚拟内存，请稍等..........
---------------------------------------------
1025+0 records in
1025+0 records out
1074790400 bytes (1.1 GB) copied, 5.39333 s, 199 MB/s

+++++++++++++++++++++++++++++++++++省略若干+++++++++++++++++++++++++++++++++++++++++

 39350K .......... .......... .......... ....                 100% 74.7M=2.6s

2024-08-13 23:07:20 (14.6 MB/s) - ‘panel.zip’ saved [40330045/40330045]

--2024-08-13 23:07:21--  https://download.bt.cn/install/src/bt7.init
Resolving download.bt.cn (download.bt.cn)... 42.157.128.146, 240e:97e:4000:33f:103:212:48:148
Connecting to download.bt.cn (download.bt.cn)|42.157.128.146|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 16511 (16K) [application/octet-stream]
Saving to: ‘/etc/init.d/bt’

     0K .......... ......                                     100%  148M=0s

2024-08-13 23:07:21 (148 MB/s) - ‘/etc/init.d/bt’ saved [16511/16511]

--2024-08-13 23:07:21--  https://download.bt.cn/install/src/bt7.init
Resolving download.bt.cn (download.bt.cn)... 42.157.128.146, 240e:97e:4000:33f:103:212:48:148
Connecting to download.bt.cn (download.bt.cn)|42.157.128.146|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 16511 (16K) [application/octet-stream]
Saving to: ‘/www/server/panel/init.sh’

     0K .......... ......                                     100%  132M=0s

2024-08-13 23:07:21 (132 MB/s) - ‘/www/server/panel/init.sh’ saved [16511/16511]

--2024-08-13 23:07:21--  https://download.bt.cn/install/conf/softList.conf
Resolving download.bt.cn (download.bt.cn)... 42.157.128.146, 240e:97e:4000:33f:103:212:48:148
Connecting to download.bt.cn (download.bt.cn)|42.157.128.146|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2216 (2.2K) [application/octet-stream]
Saving to: ‘/www/server/panel/data/softList.conf’

     0K ..                                                    100%  300M=0s

2024-08-13 23:07:22 (300 MB/s) - ‘/www/server/panel/data/softList.conf’ saved [2216/2216]

Starting Bt-Panel....   done
Starting Bt-Tasks...    done
username: w6xdsagd
========================================
正在开启面板SSL，请稍等............
========================================
证书开启成功！
========================================
Stopping Bt-Tasks...    done
Stopping Bt-Panel...    done
Starting Bt-Panel....   done
Starting Bt-Tasks...    done
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Package firewalld-0.6.3-13.el7_9.noarch already installed and latest version
Nothing to do
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.
success
True
==================================================================
Congratulations! Installed successfully!
=============注意：首次打开面板浏览器将提示不安全=================

 请选择以下其中一种方式解决不安全提醒
 1、下载证书，地址：https://dg2.bt.cn/ssl/baota_root.pfx，双击安装,密码【www.bt.cn】
 2、点击【高级】-【继续访问】或【接受风险并继续】访问
 教程：https://www.bt.cn/bbs/thread-117246-1-1.html
 mac用户请下载使用此证书：https://dg2.bt.cn/ssl/mac.crt

========================面板账户登录信息==========================

 【云服务器】请在安全组放行 23412 端口
 外网面板地址: https://139.121.001.102:23412/e412f75g
 内网面板地址: https://172.13.151.231:23412/e412f75g
 username: sd3j3jsdd
 password: 234234354

 浏览器访问以下链接，添加宝塔客服
 https://www.bt.cn/new/wechat_customer
==================================================================
Time consumed: 1 Minute!
[root@iZuf63na909xjyb4nymwotZ ~]#
```

ECS安全组 放开 23412 端口入方向。

然后浏览器访问：https://139.121.001.102:23412/e412f75g



## 参考资料


