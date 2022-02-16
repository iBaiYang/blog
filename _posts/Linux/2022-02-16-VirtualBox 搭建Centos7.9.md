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

如配置IP为 192.168.56.103：
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
IPADDR=192.168.56.103
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
    link/ether 08:00:27:43:ae:ba brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 81574sec preferred_lft 81574sec
    inet6 fe80::b3c6:7975:e3ea:3dc9/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:55:2b:e2 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.101/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 526sec preferred_lft 526sec
    inet 192.168.56.103/24 brd 192.168.56.255 scope global secondary enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::4206:fbf3:7c07:f2e/64 scope link noprefixroute
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



