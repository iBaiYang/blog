---
layout: post
categories: Linux
title: 兄弟连Linux教程(李明,沈超) 第2章 Linux系统安装
meta: 兄弟连Linux教程(李明,沈超) 第2章 Linux系统安装
---
* content
{:toc}

### 正文

#### 2.1 VMware虚拟机安装与使用

1、VMware 简介

VMware是一个虚拟PC的软件，可以在现有的操 作系统上虚拟出一个新的硬件环境，相当于模拟 出一台新的PC，
以此来实现在一台机器上真正 同时运行两个独立的操作系统 。

VMware官方网站  http://www.vmware.com

VMware主要特点：
* 不需要分区或重新开机就能在同一台 PC上使用两种以上的操作系统
* 本机系统可以与虚拟机系统网络通信
* 可以设定并且随时修改虚拟机操作系统的硬件环境

2、建议的VMware配置

CPU：建议主频为1GHz以上

内存：建议1GB以上

硬盘：建议分区空闲空间8GB以上

3、安装VMware

![]({{site.baseurl}}/images/20210331/20210331170819.png)

4、新建虚拟机

![]({{site.baseurl}}/images/20210331/20210331170821.png)

5、虚拟机硬件设置

![]({{site.baseurl}}/images/20210331/20210331170823.png)

6、虚拟机网络设置

![]({{site.baseurl}}/images/20210331/20210331170825.png)

7、虚拟机使用技巧

![]({{site.baseurl}}/images/20210331/20210331170827.png)

#### 2.2 系统分区

1、磁盘分区

磁盘分区是使用分区编辑器（partition editor）在磁盘上划分几个逻辑部分。
碟片一旦划分成数个分区（Partition），不同类的目录与文件可以存储进不同的分区。

![]({{site.baseurl}}/images/20210331/20210331170831.png)

分区类型

主分区：最多只能有4个。

扩展分区：

    最多只能有1个。
    主分区加扩展分区最多有4个。
    不能写入数据，只能包含逻辑分区

逻辑分区

![]({{site.baseurl}}/images/20210331/20210331170833.png)

2、格式化

格式化（高级格式化）又称逻辑格式化，它是指根据用户选定的文件系统（如FAT16、FAT32、NTFS、EXT2、EXT3、EXT4等），
在磁盘的特定区域写入特定数据，在分区中划出一片用于存放文件分配表、目录表等用于文件管理的磁盘空间。

![]({{site.baseurl}}/images/20210331/20210331170835.png)

3、硬件设备文件名

| 硬件 | 设备文件名 |
| :--------   | :-----  |
|IDE硬盘 | /dev/hd[a-d] |
|SCSI/SATA/USB硬盘 | /dev/sd[a-p] |
|光驱 | /dev/cdrom或/dev/hdc |
|软盘 | /dev/fd[0-1] |
|打印机（25针） | /dev/lp[0-2] |
|打印机（USB） | /dev/usb/lp[0-15] |
|鼠标 | /dev/mouse |

分区设备文件名

分区表示

![]({{site.baseurl}}/images/20210331/20210331170837.png)

![]({{site.baseurl}}/images/20210331/20210331170839.png)

4、挂载

必须分区

    / （根分区）
    swap分区 （交换分区，内存2倍，不超过2GB）

推荐分区

    /boot （启动分区，200MB）

文件系统结构：

![]({{site.baseurl}}/images/20210331/20210331170841.png)

![]({{site.baseurl}}/images/20210331/20210331170843.png)

**总结**

分区：把大硬盘分为小的逻辑分区

格式化：写入文件系统

分区设备文件名：给每个分区定义设备文件名

挂载：给每个分区分配挂载点

#### 2.3 Linux系统安装

1、安装欢迎界面

“Install or upgrade an existing system”：安装或升级现有系统

“Install system with basic video driver”：安装过程采用基本的显卡驱动

“Rescue installed system”：进入系统修复模式

“Boot from local drive”：退出安装从硬盘启动

“Memory test”：存储介质检测

2、密码原则

复杂性

    八位字符以上、大小写字母、数字、符号
    不能是英文单词
    不能是和用户相关的内容

易记忆性

时效性

3、软件包选择

Desktop（桌面）

Minimal Desktop（最小化桌面）

Minimal（最小化）

Basic Server（基本服务器）

Database Server（数据库服务器）

Web Server（网页服务器）

Virtual Host（虚拟主机）

software development workstation（软件开发工作站）

4、安装日志

/root/install.log：存储了安装在系统中的软件包及其版本信息

/root/install.log.syslog：存储了安装过程中留下的事件记录

/root/anaconda-ks.cfg：以Kickstart配置文件的格式记录安装过程中设置的选项信息

#### 2.4 远程登录管理工具

1、虚拟机网络配置

![]({{site.baseurl}}/images/20210331/20210331211749.png)

![]({{site.baseurl}}/images/20210331/20210331211857.png)

桥接     与主机共用同一个网卡，占用网段IP，可访问外网

NAT     使用虚拟网卡，不占用网段IP，与主机通信基础上，可以访问外网

Host only     使用虚拟网卡，不占用网段IP，只与主机通信，不可访问外网

查看网卡配置，配置网卡IP
> ifconfig
> 
> ifconfig eth0 192.168.118.2

> ping 192.168.118.2

2、SecureCRT 远程连接管理工具

![]({{site.baseurl}}/images/20210331/20210331212006.png)

![]({{site.baseurl}}/images/20210331/20210331212036.png)

3、Winscp 文件拷贝工具

在 windows 与 linux 之间copy文件

![]({{site.baseurl}}/images/20210331/20210331212111.png)















磁盘分区

分区类型

主分区：最多4个

扩展分区：最多1个，不能写入数据，只能挂载逻辑分区

主分区+扩展分区 最多4个

逻辑分区

格式化（高级格式化）又称为逻辑格式化，是要在硬盘中写入文件系统

ext4  4k  数据块 block

i节点 inode列表

低级格式化是硬盘操作不是操作系统操作


ide 133MB  最古老， scsi都淘汰了 200MB贵服务器

SATA3 串口硬盘 500MB/s  现在一般都是这个

逻辑分区不能占用1 2 3 4编号   5一定是第一个逻辑分区

挂载：

/ 根分区

swap分区 交换分区  内存1.5-2倍  不超过2G

推荐分区/boot  启动分区 200m即可 太大无用，系统启动都要有空闲空间，防止系统写满导致无法启动，写完之后永远不会往这个分区再写入数据，他永远有空闲空间  boot分区一定是sda1第一个分区

挂载点必须是空目录





<br/><br/><br/><br/><br/>
### 参考资料




