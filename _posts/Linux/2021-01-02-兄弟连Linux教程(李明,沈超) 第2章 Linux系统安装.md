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



#### 2.2 系统分区


#### 2.3 Linux系统安装


#### 2.4 远程登录管理工具


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

硬件设备文件名：

IDE硬盘             hd[a-d]

SCSI/SATA/USB硬盘      sd[a-p]

光驱                    cdrom或sr0

鼠标                     mouse

打印机(25针)                 lp[0-2]

打印机（USB）                 /dev/usb/lp[0-15]

ide 133MB  最古老， scsi都淘汰了 200MB贵服务器

SATA3 串口硬盘 500MB/s  现在一般都是这个

逻辑分区不能占用1 2 3 4编号   5一定是第一个逻辑分区

挂载：

/ 根分区

swap分区 交换分区  内存1.5-2倍  不超过2G

推荐分区/boot  启动分区 200m即可 太大无用，系统启动都要有空闲空间，防止系统写满导致无法启动，写完之后永远不会往这个分区再写入数据，他永远有空闲空间  boot分区一定是sda1第一个分区

挂载点必须是空目录

安装日志：

/root/install.log:存储了安装在系统中的软件包及其版本信息

/root/install.log.syslog 存储安装过程中留下的事件记录

/root/anaconda-ks.cfg 已Kickstart配置文件的格式记录安装过程的选项设置  作为无人值守安装的模板

linux 严格区分大小写

命令全是小写

文件名区分大小写

tab键自动补全

linux中一切皆文件  设备文件是特殊的文件

永久生效都要写入文件  写入内存的都是临时生效

linux不通过扩展名区分文件类型

linux存储设备都必须挂载后才能使用

目录作用

/bin  存放系统命令，普通用户和root都可以执行

/sbin 保存和系统环境设置相关的命令，只有root能执行

/usr/bin

/usr/sbin

/boot 系统启动目录

/dev 设备文件

/etc  配置文件

/home 宿主目录

/lib 系统函数库

/lost+found/ 当系统崩溃或意外关机而产生的文件碎片。当系统启动过程fsck工具会检查这里，并修复系统   每个分区都有自己的这个目录

/media 挂载目录   多媒体设备  光盘

/mnt 挂载目录 u盘移动硬盘或其他操作系统分区

/misc 挂载目录 系统建议用来挂载nfs服务的共享目录

/opt 第三方软件保存位置  用处不多，现在外部软件一般放/usr/local目录

/proc 虚拟文件系统  放内存中 当前系统的进程和硬件信息

/sys  虚拟文件系统  放内存中 存放内核相关信息

/root  root家目录

/srv  服务数据目录

/tmp 临时

/usr 系统软件资源目录

/var 动态数据保存位置。保存缓存、日志及软件运行产生的文件

usr  unix system resource

服务器不允许关机，只能重启

重启时应该关闭服务

不要在服务器访问高峰运行高负载命令

远程配置防火墙时不要把自己踢出服务器

合理分配权限

定期备份重要数据和日志



mint 默认不启用root用户,sudo passwd root来启用

mint也默认不启用ssh,sudo apt-get install openssh-server然后修改配置文件vi /etc/ssh/sshd_config 修改PermitRootLogin yes行，来允许root用户ssh远程登陆


<br/><br/><br/><br/><br/>
### 参考资料




