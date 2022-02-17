---
layout: post
categories: Linux
title: VirtualBox 搭建Centos7.9
meta: VirtualBox 搭建Centos7.9
---
* content
{:toc}

## 正文

在VirtualBox官网下载最新版 [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) 安装。

在Centos官网下载iso安装包 [Centos download](http://isoredirect.centos.org/centos/7/isos/x86_64/) ，保存到指定位置。

### 安装

启动 VirtualBox，点击新建：

![]({{site.baseurl}}/images/20220216/20220216100706.png)

下一步，选择内存大小，如为 4096 MB。

下一步，虚拟硬盘，选择 现在创建虚拟硬盘；下一步，虚拟硬盘，选择 VDI（VirtualBox 磁盘映像）；
下一步，存储在物理硬盘上，选择 动态分配(D)；下一步，文件位置和大小，如配置为 20.00 GB。

点击创建，完成容器创建。

点击该容器，点击 设置，点击 存储：

![]({{site.baseurl}}/images/20220216/20220216101847.png)

选中自己保存的 iso安装包。点击 OK 返回。

点击该容器，点击 设置，点击 系统，在 主板 配置启动顺序：

![]({{site.baseurl}}/images/20220216/20220216102817.png)

看一下详细信息：

![]({{site.baseurl}}/images/20220216/20220216103124.png)

点击 启动，进行安装，选择 中文（中国）：

![]({{site.baseurl}}/images/20220216/20220216103517.png)

系统 安装位置(D) ，选择我们分配的硬盘：

![]({{site.baseurl}}/images/20220216/20220216103740.png)

系统 网络和主机名(N) ，选中网卡，切换到 打开 状态：

![]({{site.baseurl}}/images/20220216/20220216103821.png)

开始安装。

安装过程中，设置 ROOT密码：

![]({{site.baseurl}}/images/20220216/20220216104206.png)

等待安装完成，点击 重启。

输入ROOT账号密码登录。

安装 wget：
> yum install -y wget

安装 vim：
> yum install -y vim

安装 gcc：
> yum install -y gcc gcc-devel gcc-c++ gcc-c++-devel make kernel kernel-devel bzip2

### 网络配置

查看网卡：
> ip addr

```
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:43:ae:ba brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 81574sec preferred_lft 81574sec
    inet6 fe80::b3c6:7975:e3ea:3dc9/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]# 
```

我们可以访问外网：
> ping www.baidu.com

但主机无法访问虚拟机，需要配置。

关机，一般是关闭虚拟机窗口，选择 正常关闭。

点击该容器，点击 设置，点击 网络，启用 网卡2，选择 Host-Only 网络：

![]({{site.baseurl}}/images/20220216/20220216111554.png)

配置网卡：
> cd /etc/sysconfig/network-scripts
> 
> ls

```
[root@localhost ~]# cd /etc/sysconfig/network-scripts/
[root@localhost network-scripts]#
[root@localhost network-scripts]# ls
ifcfg-enp0s3  ifdown-bnep  ifdown-isdn    ifdown-sit       ifup          ifup-ippp  ifup-plusb   ifup-sit       ifup-wireless
ifcfg-lo      ifdown-eth   ifdown-post    ifdown-Team      ifup-aliases  ifup-ipv6  ifup-post    ifup-Team      init.ipv6-global
ifdown        ifdown-ippp  ifdown-ppp     ifdown-TeamPort  ifup-bnep     ifup-isdn  ifup-ppp     ifup-TeamPort  network-functions
              ifdown-ipv6  ifdown-routes  ifdown-tunnel    ifup-eth      ifup-plip  ifup-routes  ifup-tunnel    network-functions-ipv6
[root@localhost network-scripts]#
```

复制网卡配置文件：
> cp ifcfg-enp0s3 ifcfg-enp0s8

编辑网卡配置文件：
> vi ifcfg-enp0s8

如配置IP为 192.168.56.108：
```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp0s8"
UUID="2c874efd8-469f-4991-bfe0-6dd215c8de7d"
DEVICE="enp0s8"
ONBOOT="yes"
IPADDR=192.168.56.108
GATEWAY=192.168.56.1
NETMASK=255.255.255.0
```

重载网卡配置：
> nmcli c reload

重启网络服务或重启系统：
```
> systemctl restart netwoek
>
> reboot
```

查看网卡：
> ip addr

```
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:9a:f5:c4 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 83148sec preferred_lft 83148sec
    inet6 fe80::c8da:b480:5500:c6e/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:20:68:ab brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.108/24 brd 192.168.56.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::f458:be7a:2271:ab78/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]# 
```

### SSH 连接

查看 SSH 服务状态：
> systemctl status sshd

```
[root@localhost ~]# systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since 三 2022-02-16 09:35:05 CST; 1h 14min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 1130 (sshd)
   CGroup: /system.slice/sshd.service
           └─1130 /usr/sbin/sshd -D

2月 16 09:35:05 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
2月 16 09:35:05 localhost.localdomain sshd[1130]: Server listening on 0.0.0.0 port 22.
2月 16 09:35:05 localhost.localdomain sshd[1130]: Server listening on :: port 22.
2月 16 09:35:05 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
[root@localhost ~]#
```

如果未安装，安装：
> yum install -y openssh-server

如果未启动，设置开机自启动：
> systemctl enable sshd

也可以直接启动：
> systemctl start sshd

我们可以看一下SSH的服务配置：
> cat /etc/ssh/sshd_config

```
[root@localhost ~]# cat /etc/ssh/sshd_config
#       $OpenBSD: sshd_config,v 1.100 2016/08/15 12:32:04 naddy Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/local/bin:/usr/bin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
#Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
SyslogFacility AUTHPRIV
#LogLevel INFO

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin yes
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile      .ssh/authorized_keys

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication yes

# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no

# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes

# GSSAPI options
GSSAPIAuthentication yes
GSSAPICleanupCredentials no
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no
#GSSAPIEnablek5users no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
# WARNING: 'UsePAM no' is not supported in Red Hat Enterprise Linux and may cause several
# problems.
UsePAM yes

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding yes
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
#PrintMotd yes
#PrintLastLog yes
#TCPKeepAlive yes
#UseLogin no
#UsePrivilegeSeparation sandbox
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0
#ClientAliveCountMax 3
#ShowPatchLevel no
#UseDNS yes
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# Accept locale-related environment variables
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS

# override default of no subsystems
Subsystem       sftp    /usr/libexec/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
[root@localhost ~]#
```

在主机用 putty 连接虚拟机。

### 共享文件夹

![]({{site.baseurl}}/images/20220216/20220216170702.png)

```
[root@localhost ~]# df
文件系统                   1K-块    已用     可用 已用% 挂载点
devtmpfs                 2960176       0  2960176    0% /dev
tmpfs                    2972164       0  2972164    0% /dev/shm
tmpfs                    2972164    8812  2963352    1% /run
tmpfs                    2972164       0  2972164    0% /sys/fs/cgroup
/dev/mapper/centos-root 17811456 2004672 15806784   12% /
/dev/sda1                1038336  184876   853460   18% /boot
tmpfs                     594436       0   594436    0% /run/user/0
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# lsscsi
[0:0:0:0]    cd/dvd  VBOX     CD-ROM           1.0   /dev/sr0
[2:0:0:0]    disk    ATA      VBOX HARDDISK    1.0   /dev/sda
[root@localhost ~]#
[root@localhost ~]# mkdir -p /var/www
[root@localhost ~]#
[root@localhost ~]# ll /var/www
总用量 0
[root@localhost ~]#
[root@localhost ~]# mount /dev/sr0 /var/www
mount: /dev/sr0 写保护，将以只读方式挂载
[root@localhost ~]#
[root@localhost ~]# ll /var/www
总用量 46893
-r--r--r--. 1 root root      763 2月  20 2020 AUTORUN.INF
-r-xr-xr-x. 1 root root     6384 7月  29 2021 autorun.sh
dr-xr-xr-x. 2 root root      792 7月  29 2021 cert
dr-xr-xr-x. 2 root root     1824 7月  29 2021 NT3x
dr-xr-xr-x. 2 root root     2652 7月  29 2021 OS2
-r-xr-xr-x. 1 root root     4821 7月  29 2021 runasroot.sh
-r--r--r--. 1 root root      547 7月  29 2021 TRANS.TBL
-r--r--r--. 1 root root  3992706 7月  29 2021 VBoxDarwinAdditions.pkg
-r-xr-xr-x. 1 root root     3949 7月  29 2021 VBoxDarwinAdditionsUninstall.tool
-r-xr-xr-x. 1 root root  7433652 7月  29 2021 VBoxLinuxAdditions.run
-r--r--r--. 1 root root  9411584 7月  29 2021 VBoxSolarisAdditions.pkg
-r-xr-xr-x. 1 root root 16889104 7月  29 2021 VBoxWindowsAdditions-amd64.exe
-r-xr-xr-x. 1 root root   270840 7月  29 2021 VBoxWindowsAdditions.exe
-r-xr-xr-x. 1 root root  9995064 7月  29 2021 VBoxWindowsAdditions-x86.exe
[root@localhost ~]#
[root@localhost ~]# unmount /dev/sr0
-bash: unmount: 未找到命令
[root@localhost ~]#
[root@localhost ~]# umount /dev/sr0
[root@localhost ~]#
[root@localhost ~]# ll /var/www
总用量 0
[root@localhost ~]#
[root@localhost ~]# ll /media
总用量 0
[root@localhost ~]#
[root@localhost ~]# mkdir -p /media/cdrom
[root@localhost ~]#
[root@localhost ~]# ll /media
总用量 0
drwxr-xr-x. 2 root root 6 2月  16 21:39 cdrom
[root@localhost ~]#
[root@localhost ~]# mount /dev/sr0 /media/cdrom
mount: /dev/sr0 写保护，将以只读方式挂载
[root@localhost ~]#
[root@localhost ~]# umount /dev/sr0
[root@localhost ~]#
[root@localhost ~]# chmod 0777 /media/cdrom/
[root@localhost ~]#
[root@localhost ~]# ll /media
总用量 0
drwxrwxrwx. 2 root root 6 2月  16 21:39 cdrom
[root@localhost ~]#
[root@localhost ~]# mount /dev/sr0 /media/cdrom
mount: /dev/sr0 写保护，将以只读方式挂载
[root@localhost ~]#
[root@localhost ~]# ll /media/cdrom/
总用量 46893
-r--r--r--. 1 root root      763 2月  20 2020 AUTORUN.INF
-r-xr-xr-x. 1 root root     6384 7月  29 2021 autorun.sh
dr-xr-xr-x. 2 root root      792 7月  29 2021 cert
dr-xr-xr-x. 2 root root     1824 7月  29 2021 NT3x
dr-xr-xr-x. 2 root root     2652 7月  29 2021 OS2
-r-xr-xr-x. 1 root root     4821 7月  29 2021 runasroot.sh
-r--r--r--. 1 root root      547 7月  29 2021 TRANS.TBL
-r--r--r--. 1 root root  3992706 7月  29 2021 VBoxDarwinAdditions.pkg
-r-xr-xr-x. 1 root root     3949 7月  29 2021 VBoxDarwinAdditionsUninstall.tool
-r-xr-xr-x. 1 root root  7433652 7月  29 2021 VBoxLinuxAdditions.run
-r--r--r--. 1 root root  9411584 7月  29 2021 VBoxSolarisAdditions.pkg
-r-xr-xr-x. 1 root root 16889104 7月  29 2021 VBoxWindowsAdditions-amd64.exe
-r-xr-xr-x. 1 root root   270840 7月  29 2021 VBoxWindowsAdditions.exe
-r-xr-xr-x. 1 root root  9995064 7月  29 2021 VBoxWindowsAdditions-x86.exe
[root@localhost ~]#
[root@localhost ~]# cd /media/cdrom/
[root@localhost cdrom]#
[root@localhost cdrom]# ll
总用量 46893
-r--r--r--. 1 root root      763 2月  20 2020 AUTORUN.INF
-r-xr-xr-x. 1 root root     6384 7月  29 2021 autorun.sh
dr-xr-xr-x. 2 root root      792 7月  29 2021 cert
dr-xr-xr-x. 2 root root     1824 7月  29 2021 NT3x
dr-xr-xr-x. 2 root root     2652 7月  29 2021 OS2
-r-xr-xr-x. 1 root root     4821 7月  29 2021 runasroot.sh
-r--r--r--. 1 root root      547 7月  29 2021 TRANS.TBL
-r--r--r--. 1 root root  3992706 7月  29 2021 VBoxDarwinAdditions.pkg
-r-xr-xr-x. 1 root root     3949 7月  29 2021 VBoxDarwinAdditionsUninstall.tool
-r-xr-xr-x. 1 root root  7433652 7月  29 2021 VBoxLinuxAdditions.run
-r--r--r--. 1 root root  9411584 7月  29 2021 VBoxSolarisAdditions.pkg
-r-xr-xr-x. 1 root root 16889104 7月  29 2021 VBoxWindowsAdditions-amd64.exe
-r-xr-xr-x. 1 root root   270840 7月  29 2021 VBoxWindowsAdditions.exe
-r-xr-xr-x. 1 root root  9995064 7月  29 2021 VBoxWindowsAdditions-x86.exe
[root@localhost cdrom]#
[root@localhost cdrom]# sh ./VBoxLinuxAdditions.run
Verifying archive integrity... All good.
Uncompressing VirtualBox 6.1.26 Guest Additions for Linux........
VirtualBox Guest Additions installer
Copying additional installer modules ...
Installing additional modules ...
VirtualBox Guest Additions: Starting.
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel
modules.  This may take a while.
VirtualBox Guest Additions: To build modules for other installed kernels, run
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>
VirtualBox Guest Additions: or
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all
VirtualBox Guest Additions: Kernel headers not found for target kernel
3.10.0-1160.el7.x86_64. Please install them and execute
  /sbin/rcvboxadd setup
modprobe vboxguest failed
The log file /var/log/vboxadd-setup.log may contain further information.
[root@localhost cdrom]#
[root@localhost cdrom]# ll ../
总用量 3
dr-xr-xr-x. 5 root root 2408 7月  29 2021 cdrom
[root@localhost cdrom]#
[root@localhost cdrom]# Kernel headers not found for target kernel
-bash: Kernel: 未找到命令
[root@localhost cdrom]# 3.10.0-1160.el7.x86_64. Please install them and execute
-bash: 3.10.0-1160.el7.x86_64.: 未找到命令
[root@localhost cdrom]#
[root@localhost cdrom]# yum update -y kernel
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
No packages marked for update
[root@localhost cdrom]#
[root@localhost cdrom]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)
[root@localhost cdrom]#
[root@localhost cdrom]# yum install -y kernel-headers perl kernel-devel
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
软件包 kernel-headers-3.10.0-1160.53.1.el7.x86_64 已安装并且是最新版本
软件包 4:perl-5.16.3-299.el7_9.x86_64 已安装并且是最新版本
软件包 kernel-devel-3.10.0-1160.53.1.el7.x86_64 已安装并且是最新版本
无须任何处理
[root@localhost cdrom]#
[root@localhost cdrom]# /sbin/rcvboxadd setup
VirtualBox Guest Additions: Starting.
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel
modules.  This may take a while.
VirtualBox Guest Additions: To build modules for other installed kernels, run
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>
VirtualBox Guest Additions: or
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all
VirtualBox Guest Additions: Kernel headers not found for target kernel
3.10.0-1160.el7.x86_64. Please install them and execute
  /sbin/rcvboxadd setup
modprobe vboxguest failed
The log file /var/log/vboxadd-setup.log may contain further information.
[root@localhost cdrom]#
```

重启之后：
```
[root@localhost ~]# cd /media/cdrom/
[root@localhost cdrom]#
[root@localhost cdrom]# ll
总用量 0
[root@localhost cdrom]#
[root@localhost cdrom]# df
文件系统                    1K-块     已用     可用 已用% 挂载点
devtmpfs                  2960384        0  2960384    0% /dev
tmpfs                     2972160        0  2972160    0% /dev/shm
tmpfs                     2972160     8760  2963400    1% /run
tmpfs                     2972160        0  2972160    0% /sys/fs/cgroup
/dev/mapper/centos-root  17811456  1923608 15887848   11% /
/dev/sda1                 1038336   198192   840144   20% /boot
tmpfs                      594436        0   594436    0% /run/user/0
www                     146799612 62141008 84658604   43% /media/sf_www
[root@localhost cdrom]#
[root@localhost cdrom]# ll /media/sf_www
总用量 3
-rwxrwx---. 1 root vboxsf  1589 11月 28 2020 说明.txt
drwxrwx---. 1 root vboxsf  4096 8月  20 2020 资料
[root@localhost cdrom]#
[root@localhost cdrom]#
```

### docker安装

移除老版本：
```
yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine
```

安装docker:
> yum install -y docker

设置开机自动启动:
> systemctl enable docker

```
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@localhost ~]#
```

启动：
> systemctl start docker

```
[root@localhost ~]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: http://docs.docker.com
[root@localhost ~]#
[root@localhost ~]# systemctl start docker
[root@localhost ~]#
[root@localhost ~]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since 四 2022-02-17 09:46:58 CST; 15s ago
     Docs: http://docs.docker.com
 Main PID: 2093 (dockerd-current)
   CGroup: /system.slice/docker.service
           ├─2093 /usr/bin/dockerd-current --add-runtime docker-runc=/usr/libexec/docker/d...
           └─2101 /usr/bin/docker-containerd-current -l unix:///var/run/docker/libcontaine...

2月 17 09:46:56 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:56.3..."
2月 17 09:46:57 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:57.6..."
2月 17 09:46:57 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:57.6..."
2月 17 09:46:57 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:57.6..."
2月 17 09:46:58 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:58.1..."
2月 17 09:46:58 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:58.5..."
2月 17 09:46:58 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:58.6..."
2月 17 09:46:58 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:58.6...1
2月 17 09:46:58 localhost.localdomain dockerd-current[2093]: time="2022-02-17T09:46:58.6..."
2月 17 09:46:58 localhost.localdomain systemd[1]: Started Docker Application Container ...e.
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]#
```

查看版本：
> docker version

```
[root@localhost ~]# docker version
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-208.git7d71120.el7_9.x86_64
 Go version:      go1.10.3
 Git commit:      7d71120/1.13.1
 Built:           Mon Jun  7 15:36:09 2021
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-208.git7d71120.el7_9.x86_64
 Go version:      go1.10.3
 Git commit:      7d71120/1.13.1
 Built:           Mon Jun  7 15:36:09 2021
 OS/Arch:         linux/amd64
 Experimental:    false
[root@localhost ~]#
```

docker配置：
```
[root@localhost ~]# ls -l /etc/docker
总用量 20
drwxr-xr-x. 5 root root    75 2月  17 09:44 certs.d
-rw-r--r--. 1 root root     3 12月  4 2019 daemon.json
-rw-------. 1 root root   244 2月  17 09:46 key.json
-rw-r--r--. 1 root root 10850 4月  28 2020 seccomp.json
[root@localhost ~]#
```

修改docker仓库地址：
> vi /etc/docker/daemon.json 

写入内容，保存并退出：
```
{"registry-mirrors": ["https://registry.docker-cn.com"], "live-restore": true}
```

### php安装

下载镜像：
> docker pull php:7.1.30-fpm

```
[root@localhost ~]# docker pull php:7.1.30-fpm
Trying to pull repository docker.io/library/php ...
7.1.30-fpm: Pulling from docker.io/library/php
f5d23c7fed46: Pull complete
4f36b8588ea0: Pull complete
6f4f95ddefa8: Pull complete
187af28c9b1d: Pull complete
7ba9cd8f12bd: Pull complete
19ce450f6a80: Pull complete
6a0aa94e79c7: Pull complete
3097ec58d870: Pull complete
05ecbde01690: Pull complete
ab28ea58dda0: Pull complete
Digest: sha256:a0f0773dc2f92ca8f4dab7c7c525574d467d3aa4bb27424bb7f0540a7c9efcd0
Status: Downloaded newer image for docker.io/php:7.1.30-fpm
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/php       7.1.30-fpm          0b13895891aa        2 years ago         391 MB
[root@localhost ~]#
```

把-v与容器挂载的目录准备好：
```
mkdir -p /etc/php
```

创建容器并运行(映射共享文件夹 `/media/sf_www` `)：
```
docker run --name server-php -p 9000:9000 -v /etc/php:/usr/local/etc/php -v /media/sf_www:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php:7.1.30-fpm 
```

容器中 root用户并不是真正的root用户，权限受到了限制，上面运行时可以加上参数 `--privileged=true`

```
[root@localhost ~]# docker run --name server-php -p 9000:9000 -v /etc/php:/usr/local/etc/php -v /media/sf_www:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php:7.1.30-fpm
83199b3ed9ba650e4db7d6aa4b10f6414ac045d79fd7ff7f3203141ca3dc8344
[root@localhost ~]#
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
83199b3ed9ba        php:7.1.30-fpm      "docker-php-entryp..."   4 seconds ago       Up 2 seconds        0.0.0.0:9000->9000/tcp   server-php
[root@localhost ~]#
```

进入容器：
> docker exec -it server-php /bin/bash

查看版本：
> php -v

查看拓展：
> php -m

```
[root@localhost ~]# docker exec -it server-php /bin/bash
root@c1576b02b628:/var/www/html#
root@c1576b02b628:/var/www/html# php -v
PHP 7.1.30 (cli) (built: Jul 13 2019 00:37:03) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2018 Zend Technologies
root@c1576b02b628:/var/www/html#
root@c1576b02b628:/var/www/html# php -m
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@c1576b02b628:/var/www/html#
```

停止容器运行：
```
[root@localhost ~]# docker stop server-php
server-php
[root@localhost ~]#
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
c1576b02b628        php:7.1.30-fpm      "docker-php-entryp..."   18 minutes ago      Exited (0) 3 seconds ago                       server-php
[root@localhost ~]#
```

删除容器：
```
[root@localhost ~]# docker rm server-php
server-php
[root@localhost ~]#
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@localhost ~]#
```

#### mysqli拓展安装

上面没有 mysqli 模块，安装mysqli拓展。

配置目录准备好：
> mkdir -p /usr/local/etc/php/conf.d

输入：
> docker-php-ext-install mysqli

```
root@83199b3ed9ba:~# docker-php-ext-install mysqli
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking for MySQLi support... yes, shared
checking whether to enable embedded MySQLi support... no
checking for specified location of the MySQL UNIX socket... no
checking for MySQL UNIX socket location... no
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli.c -o mysqli.lo
mkdir .libs
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli.c  -fPIC -DPIC -o .libs/mysqli.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_api.c -o mysqli_api.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_api.c  -fPIC -DPIC -o .libs/mysqli_api.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_prop.c -o mysqli_prop.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_prop.c  -fPIC -DPIC -o .libs/mysqli_prop.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_nonapi.c -o mysqli_nonapi.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_nonapi.c  -fPIC -DPIC -o .libs/mysqli_nonapi.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_fe.c -o mysqli_fe.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_fe.c  -fPIC -DPIC -o .libs/mysqli_fe.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_report.c -o mysqli_report.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_report.c  -fPIC -DPIC -o .libs/mysqli_report.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_driver.c -o mysqli_driver.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_driver.c  -fPIC -DPIC -o .libs/mysqli_driver.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_warning.c -o mysqli_warning.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_warning.c  -fPIC -DPIC -o .libs/mysqli_warning.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_exception.c -o mysqli_exception.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_exception.c  -fPIC -DPIC -o .libs/mysqli_exception.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c -o mysqli_result_iterator.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c  -fPIC -DPIC -o .libs/mysqli_result_iterator.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o mysqli.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/mysqli/modules  mysqli.lo mysqli_api.lo mysqli_prop.lo mysqli_nonapi.lo mysqli_fe.lo mysqli_report.lo mysqli_driver.lo mysqli_warning.lo mysqli_exception.lo mysqli_result_iterator.lo
cc -shared  .libs/mysqli.o .libs/mysqli_api.o .libs/mysqli_prop.o .libs/mysqli_nonapi.o .libs/mysqli_fe.o .libs/mysqli_report.o .libs/mysqli_driver.o .libs/mysqli_warning.o .libs/mysqli_exception.o .libs/mysqli_result_iterator.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,mysqli.so -o .libs/mysqli.so
creating mysqli.la
(cd .libs && rm -f mysqli.la && ln -s ../mysqli.la mysqli.la)
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=install cp ./mysqli.la /usr/src/php/ext/mysqli/modules
cp ./.libs/mysqli.so /usr/src/php/ext/mysqli/modules/mysqli.so
cp ./.libs/mysqli.lai /usr/src/php/ext/mysqli/modules/mysqli.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/mysqli/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/mysqli/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/
/usr/local/bin/docker-php-ext-enable: 108: /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-mysqli.ini: Directory nonexistent
root@83199b3ed9ba:~#
root@83199b3ed9ba:~#
root@83199b3ed9ba:~# mkdir -p /usr/local/etc/php/conf.d
root@83199b3ed9ba:~#
root@83199b3ed9ba:~#
root@83199b3ed9ba:~# docker-php-ext-install mysqli
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=install cp ./mysqli.la /usr/src/php/ext/mysqli/modules
cp ./.libs/mysqli.so /usr/src/php/ext/mysqli/modules/mysqli.so
cp ./.libs/mysqli.lai /usr/src/php/ext/mysqli/modules/mysqli.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/mysqli/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/mysqli/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/
find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la       modules/* libs/*
root@83199b3ed9ba:~#
root@83199b3ed9ba:~#
```

#### pdo_mysql拓展安装

安装pdo_mysql拓展：
> docker-php-ext-install pdo_mysql

```
root@83199b3ed9ba:~# docker-php-ext-install pdo_mysql
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking for MySQL support for PDO... yes, shared
checking for the location of libz... no
checking for MySQL UNIX socket location...
checking for PDO includes... checking for PDO includes... /usr/local/include/php/ext
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/pdo_mysql/libtool --mode=compile cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/pdo_mysql/pdo_mysql.c -o pdo_mysql.lo
mkdir .libs
 cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/pdo_mysql/pdo_mysql.c  -fPIC -DPIC -o .libs/pdo_mysql.o
/bin/bash /usr/src/php/ext/pdo_mysql/libtool --mode=compile cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/pdo_mysql/mysql_driver.c -o mysql_driver.lo
 cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/pdo_mysql/mysql_driver.c  -fPIC -DPIC -o .libs/mysql_driver.o
/bin/bash /usr/src/php/ext/pdo_mysql/libtool --mode=compile cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/pdo_mysql/mysql_statement.c -o mysql_statement.lo
 cc -I/usr/local/include/php/ext -I -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/pdo_mysql -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/pdo_mysql/mysql_statement.c  -fPIC -DPIC -o .libs/mysql_statement.o
/bin/bash /usr/src/php/ext/pdo_mysql/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/pdo_mysql/include -I/usr/src/php/ext/pdo_mysql/main -I/usr/src/php/ext/pdo_mysql -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o pdo_mysql.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/pdo_mysql/modules  pdo_mysql.lo mysql_driver.lo mysql_statement.lo
cc -shared  .libs/pdo_mysql.o .libs/mysql_driver.o .libs/mysql_statement.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,pdo_mysql.so -o .libs/pdo_mysql.so
creating pdo_mysql.la
(cd .libs && rm -f pdo_mysql.la && ln -s ../pdo_mysql.la pdo_mysql.la)
/bin/bash /usr/src/php/ext/pdo_mysql/libtool --mode=install cp ./pdo_mysql.la /usr/src/php/ext/pdo_mysql/modules
cp ./.libs/pdo_mysql.so /usr/src/php/ext/pdo_mysql/modules/pdo_mysql.so
cp ./.libs/pdo_mysql.lai /usr/src/php/ext/pdo_mysql/modules/pdo_mysql.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/pdo_mysql/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/pdo_mysql/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la       modules/* libs/*
root@83199b3ed9ba:~#
root@83199b3ed9ba:~#
root@83199b3ed9ba:~# php -m
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mysqli
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@83199b3ed9ba:~#
```

#### bcmath拓展安装

bcmath拓展可进行一些精度数学运行，需要安装好。

安装bcmath拓展：
> docker-php-ext-install bcmath

```

root@83199b3ed9ba:~# docker-php-ext-install bcmath
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking whether to enable bc style precision math functions... yes, shared
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/bcmath.c -o bcmath.lo
mkdir .libs
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/bcmath.c  -fPIC -DPIC -o .libs/bcmath.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/add.c -o libbcmath/src/add.lo
mkdir libbcmath/src/.libs
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/add.c  -fPIC -DPIC -o libbcmath/src/.libs/add.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/div.c -o libbcmath/src/div.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/div.c  -fPIC -DPIC -o libbcmath/src/.libs/div.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/init.c -o libbcmath/src/init.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/init.c  -fPIC -DPIC -o libbcmath/src/.libs/init.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/neg.c -o libbcmath/src/neg.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/neg.c  -fPIC -DPIC -o libbcmath/src/.libs/neg.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/outofmem.c -o libbcmath/src/outofmem.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/outofmem.c  -fPIC -DPIC -o libbcmath/src/.libs/outofmem.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/raisemod.c -o libbcmath/src/raisemod.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/raisemod.c  -fPIC -DPIC -o libbcmath/src/.libs/raisemod.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/rt.c -o libbcmath/src/rt.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/rt.c  -fPIC -DPIC -o libbcmath/src/.libs/rt.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/sub.c -o libbcmath/src/sub.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/sub.c  -fPIC -DPIC -o libbcmath/src/.libs/sub.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/compare.c -o libbcmath/src/compare.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/compare.c  -fPIC -DPIC -o libbcmath/src/.libs/compare.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/divmod.c -o libbcmath/src/divmod.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/divmod.c  -fPIC -DPIC -o libbcmath/src/.libs/divmod.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/int2num.c -o libbcmath/src/int2num.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/int2num.c  -fPIC -DPIC -o libbcmath/src/.libs/int2num.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/num2long.c -o libbcmath/src/num2long.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/num2long.c  -fPIC -DPIC -o libbcmath/src/.libs/num2long.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/output.c -o libbcmath/src/output.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/output.c  -fPIC -DPIC -o libbcmath/src/.libs/output.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/recmul.c -o libbcmath/src/recmul.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/recmul.c  -fPIC -DPIC -o libbcmath/src/.libs/recmul.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/sqrt.c -o libbcmath/src/sqrt.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/sqrt.c  -fPIC -DPIC -o libbcmath/src/.libs/sqrt.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/zero.c -o libbcmath/src/zero.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/zero.c  -fPIC -DPIC -o libbcmath/src/.libs/zero.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/debug.c -o libbcmath/src/debug.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/debug.c  -fPIC -DPIC -o libbcmath/src/.libs/debug.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/doaddsub.c -o libbcmath/src/doaddsub.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/doaddsub.c  -fPIC -DPIC -o libbcmath/src/.libs/doaddsub.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/nearzero.c -o libbcmath/src/nearzero.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/nearzero.c  -fPIC -DPIC -o libbcmath/src/.libs/nearzero.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/num2str.c -o libbcmath/src/num2str.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/num2str.c  -fPIC -DPIC -o libbcmath/src/.libs/num2str.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/raise.c -o libbcmath/src/raise.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/raise.c  -fPIC -DPIC -o libbcmath/src/.libs/raise.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/rmzero.c -o libbcmath/src/rmzero.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/rmzero.c  -fPIC -DPIC -o libbcmath/src/.libs/rmzero.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=compile cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/bcmath/libbcmath/src/str2num.c -o libbcmath/src/str2num.lo
 cc -I/usr/src/php/ext/bcmath/libbcmath/src -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/bcmath -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/bcmath/libbcmath/src/str2num.c  -fPIC -DPIC -o libbcmath/src/.libs/str2num.o
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/bcmath/include -I/usr/src/php/ext/bcmath/main -I/usr/src/php/ext/bcmath -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o bcmath.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/bcmath/modules  bcmath.lo libbcmath/src/add.lo libbcmath/src/div.lo libbcmath/src/init.lo libbcmath/src/neg.lo libbcmath/src/outofmem.lo libbcmath/src/raisemod.lo libbcmath/src/rt.lo libbcmath/src/sub.lo libbcmath/src/compare.lo libbcmath/src/divmod.lo libbcmath/src/int2num.lo libbcmath/src/num2long.lo libbcmath/src/output.lo libbcmath/src/recmul.lo libbcmath/src/sqrt.lo libbcmath/src/zero.lo libbcmath/src/debug.lo libbcmath/src/doaddsub.lo libbcmath/src/nearzero.lo libbcmath/src/num2str.lo libbcmath/src/raise.lo libbcmath/src/rmzero.lo libbcmath/src/str2num.lo
cc -shared  .libs/bcmath.o libbcmath/src/.libs/add.o libbcmath/src/.libs/div.o libbcmath/src/.libs/init.o libbcmath/src/.libs/neg.o libbcmath/src/.libs/outofmem.o libbcmath/src/.libs/raisemod.o libbcmath/src/.libs/rt.o libbcmath/src/.libs/sub.o libbcmath/src/.libs/compare.o libbcmath/src/.libs/divmod.o libbcmath/src/.libs/int2num.o libbcmath/src/.libs/num2long.o libbcmath/src/.libs/output.o libbcmath/src/.libs/recmul.o libbcmath/src/.libs/sqrt.o libbcmath/src/.libs/zero.o libbcmath/src/.libs/debug.o libbcmath/src/.libs/doaddsub.o libbcmath/src/.libs/nearzero.o libbcmath/src/.libs/num2str.o libbcmath/src/.libs/raise.o libbcmath/src/.libs/rmzero.o libbcmath/src/.libs/str2num.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,bcmath.so -o .libs/bcmath.so
creating bcmath.la
(cd .libs && rm -f bcmath.la && ln -s ../bcmath.la bcmath.la)
/bin/bash /usr/src/php/ext/bcmath/libtool --mode=install cp ./bcmath.la /usr/src/php/ext/bcmath/modules
cp ./.libs/bcmath.so /usr/src/php/ext/bcmath/modules/bcmath.so
cp ./.libs/bcmath.lai /usr/src/php/ext/bcmath/modules/bcmath.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/bcmath/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/bcmath/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la       modules/* libs/*
root@83199b3ed9ba:~#
```

### nginx安装

> docker pull nginx

```
[root@localhost ~]# docker pull nginx
Using default tag: latest
Trying to pull repository docker.io/library/nginx ...
latest: Pulling from docker.io/library/nginx
5eb5b503b376: Pull complete
1ae07ab881bd: Pull complete
78091884b7be: Pull complete
091c283c6a66: Pull complete
55de5851019b: Pull complete
b559bad762be: Pull complete
Digest: sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
Status: Downloaded newer image for docker.io/nginx:latest
[root@localhost ~]#
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/nginx     latest              c316d5a335a5        3 weeks ago         142 MB
docker.io/php       7.1.30-fpm          0b13895891aa        2 years ago         391 MB
[root@localhost ~]#
[root@localhost ~]#
```

创建容器并运行：
```
docker run --name server-nginx -p 80:80 -v /media/sf_www:/usr/share/nginx/html -v /etc/localtime:/etc/localtime:ro --link server-php:php --privileged=true -d nginx
```

```
[root@localhost ~]# docker run --name server-nginx -p 80:80 -v /media/sf_www:/usr/share/nginx/html -v /etc/localtime:/etc/localtime:ro --link server-php:php --privileged=true -d nginx
813daeef096da35779aad49277aec10ee9cfff59c48bf230680595c6e6a6445d
[root@localhost ~]#
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
813daeef096d        nginx               "/docker-entrypoin..."   6 seconds ago       Up 4 seconds        0.0.0.0:80->80/tcp       server-nginx
83199b3ed9ba        php:7.1.30-fpm      "docker-php-entryp..."   31 minutes ago      Up 31 minutes       0.0.0.0:9000->9000/tcp   server-php
[root@localhost ~]#
```

进入server-nginx容器：
> docker exec -it server-nginx /bin/bash

```
[root@localhost ~]# docker exec -it server-nginx /bin/bash
root@813daeef096d:/#
root@813daeef096d:/# cat /etc/nginx/nginx.conf

user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
root@813daeef096d:/#
root@813daeef096d:/# ls -l /etc/nginx/conf.d/
total 4
-rw-r--r--. 1 root root 1093 Feb 17 12:07 default.conf
root@813daeef096d:/#
root@813daeef096d:/# cat /etc/nginx/conf.d/default.conf
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

root@813daeef096d:/#
root@813daeef096d:/#
```











## 附录

### 桥接、NAT、Host-only的区别

**桥接**

简而言之桥接就是指：就是通过一台设备（可能不止一个）把几个网络串起来形成的连接。这边主要介绍虚拟机所采用的桥接方式，
虚拟出来的操作系统就像是局域网中的一独立的主机，它可以通过路由器网线访问网内任何一台机器。
不过虚拟机需要占用你同一个网段的一个 IP 地址，当且仅当虚拟机和实体机在同一个网段，两者才可以进行通信，
如：虚拟机 IP：192.168.1.101，实体机 IP：192.168.1.100。

虚拟机采用桥接方式时，是不需要虚拟网卡的，这时候虚拟机操作系统和实体机操作系统同时使用真实网卡工作，
然后真是网卡发送数据出去，而如果虚拟机发送数据给真实机，则数据流向是：虚拟机系统->真实网卡->路由器->真实网卡->实体机系统，
不难看出同一个数据经过网卡在路由器上面绕了一圈又回到了网卡。

通过上述内容发现，桥接必须经过真实网卡和路由器，因此当你的电脑没有链接有线网或者无线网的话，
你的实体机是无法和虚拟机进行交互的，即便看起来两者很近也不行。而选用桥接还是 NAT 等方式是在虚拟机软件中选定，
进入虚拟的操作系统后，所有的设置和真实电脑没有本质区别，如果路由器允许自动分配 IP 地址，
则操作系统设置为自动获取 IP 即可，如果不能自动分配，也可以在操作系统中手动分配 IP 地址。
另外值得注意的是某些公司路由器绑定了电脑 mac 地址和 IP 地址，这时候虚拟机中的桥接也相当于一个真实电脑，
也需要完成相关的绑定才可以访问网络。

可以看出：如果你想利用VMware在局域网内新建一个虚拟服务器，为局域网用户提供网络服务，就应该选择桥接模式。下面举个栗子：

```
例如大学宿舍里有一个路由器，宿舍里四个人连接这个路由器，路由器的 wanip 就不理会了，这个 ip 是动态获取的，而一般lanip默认是192.168.1.1,子网掩码是255.255.255.0。而其他四个人是自动获取ip，假设四个人的 ip 是:

A: 192.168.1.100/255.255.255.0,  B: 192.168.1.101/255.255.255.0,  C: 192.168.1.102/255.255.255.0,  D: 192.168.1.103/255.255.255.0

那么虚拟机的 ip 可以设置的 ip 地址是 192.168.1.2-192.168.1.99, 192.168.1.104-192.168.1.254 (网络地址 0 和 1 的除外，再除去 ABCD 四个人的 ip 地址)

那么虚拟机的 ip 地址可以设置为 192.168.1.98/255.255.255.0,设置了这个 ip 地址，ABCD 这四个人就可以通过 192.168.1.98 访问虚拟机了，如果虚拟机需要上外网，那么还需要配置虚拟机的路由地址，就是 192.168.1.1 了，这样，虚拟机就可以上外网了，但是，上网我们一般是通过域名去访问外网的，所以我们还需要为虚拟机配置一个 dns 服务器，我们可以简单点，把 dns 服务器地址配置为 google 的 dns 服务器 :8.8.8.8，到此，虚拟机就可以上网了。
```

综上所述，同一个数据经过网卡在路由器上面绕了一圈又回到了网卡。而虚拟机的桥接也只在其他电脑需要访问你电脑的虚拟机时，
或者你虚拟机需要访问其他外部设备时才有相关需求*，一般情况还是建议配置成操作方便使用简单的 NAT 方式。

**NAT**

NAT（Network Address Translation）模式实际是虚拟了一个网卡出来，虚拟机直接连接这个虚拟网卡，
每次访问和交互通过这个虚拟网卡交换数据。虚拟机发送数据给实体机：虚拟机系统->虚拟网卡->实体机系统；
虚拟机访问外网：虚拟机系统->虚拟网卡->实体机系统->真实网卡->路由器->外网。

虚拟机借助 NAT (网络地址转换)功能，通过宿主机器所在的网络来访问公网。 NAT 模式中，虚拟机的网卡和真实网卡的网络，
不需要在同一个网络，虚拟机的网卡的网段可以理解：路由器所在网段之下的一个子网段。

采用NAT模式最大的优势是虚拟系统接入互联网非常简单，你不需要进行任何其他的配置，只需要宿主机器能访问互联网即可。
如果你想利用VMWare安装一个新的虚拟系统，在虚拟系统中不用进行任何手工配置就能直接访问互联网，建议你采用NAT模式。
而缺点是： NAT 模式下，虚拟机仅仅可以同路由器下网段中的一台真实机通讯，而这台真实机就是安装虚拟机的这台电脑，
之所以可以通讯是因为这台电脑本身充当了虚拟机的路由器，相当于路由器的 192.168.1.1 这个地址，
而你在 vmware 下的网卡管理中是可以看到这个地址的。这边再补充一点：采用NAT模式时，虚拟机和实体机交互并不是直接进行的，
首先虚拟机操作系统访问虚拟网卡 VMnet8 （地址如： 192.188.1.33），
然后这个虚拟网卡会再将数据提交给真实机操作系统VMware Network Adapter VMnet8（地址： 192.188.1.1），
而地址（192.188.1.33 和 192.188.1.1）实际都是由虚拟机软件 vmware 提供的，具体的交互细节不作研究。

**Host-only**

在 Host-Only 模式下，虚拟网络是一个全封闭的网络，它唯一能够访问的就是主机。其实 Host-Only 网络和 NAT 网络很相似，
不同的地方就是 Host-Only 网络没有 NAT 服务，所以虚拟网络不能连接到 Internet 。
主机和虚拟机之间的通信是通过 VMware Network Adepter VMnet1 虚拟网卡来实现的。

Host-Only 的宗旨就是建立一个与外界隔绝的内部网络，来提高内网的安全性。这个功能或许对普通用户来说没有多大意义，
但大型服务商会常常利用这个功能。如果你想为 VMnet1 网段提供路由功能，那就需要使用RRAS，
而不能使用 XP 或 2000 的 ICS，因为 ICS 会把内网的 IP 地址改为 192.168.0.1，
但虚拟机是不会给 VMnet1 虚拟网卡分配这个地址的，那么主机和虚拟机之间就不能通信了。


<br/><br/><br/><br/><br/>
## 参考资料

小白速进，五小时linux速成班 centos8安装 <https://www.bilibili.com/video/BV1ey4y1S7Q6?p=6>

Oracle VM VirtualBox 安装 Centos7 并配置静态IP <https://blog.csdn.net/qq_23033339/article/details/80867195>

如何安装Virtual Box的VBox Guest Additions扩展程序 <https://blog.csdn.net/i042416/article/details/82735161>

21/70 桥接、NAT、Host-only上网方式的区别 <https://www.jianshu.com/p/b496f9cae1a9>

VirtualBox的网络接入模式解释 Net Host-Only Bridged... <https://blog.csdn.net/y1534414425/article/details/122354784>



