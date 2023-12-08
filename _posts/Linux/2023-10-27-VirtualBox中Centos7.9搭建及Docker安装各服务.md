---
layout: post
categories: Linux
title: VirtualBox中Centos7.9搭建及Docker安装各服务
meta: VirtualBox中Centos7.9搭建及Docker安装各服务
---
* content
{:toc}

## 引言

这个是使用VirtualBox重新从头再次搭建一次Centos7.9，可以先参看一下上次的博文 <https://ibaiyang.github.io/blog/linux/2022/02/16/VirtualBox-搭建Centos7.9.html> 。

在VirtualBox官网下载最新版 <https://www.virtualbox.org/wiki/Downloads> 安装，我这里是VirtualBox-7.0.12版。

## Centos7.9环境搭建

### 虚拟机安装

这里的虚拟机只是类似于刚出厂未安装系统（如Win10）的裸机，只是提供硬件上的支持，这也是下面可能会报错的来源。
在安装Centos时才是安装运行在该虚拟机上的系统软件。有了这个认识后，下面安装应该会好理解一些。

在Centos官网下载iso安装包 <http://isoredirect.centos.org/centos/7/isos/x86_64/>，
选择一个下载速度比较快的源，下载后保存到指定位置，我这里下载的是 CentOS-7-x86_64-Minimal-2009.iso 包。

![]({{site.baseurl}}/images/Linux/20231027150306.png)

打开 VirtualBox，点击新建：

![]({{site.baseurl}}/images/Linux/20231027181544.png)

名称是虚拟机的名称，文件夹是把虚拟机安装在哪个文件夹下，虚拟光盘是上面下载的Centos的iso安装包。

点击下一步，修改下密码：

![]({{site.baseurl}}/images/Linux/20231027181613.png)

（下面的“增强功能”可以选择上，可以减少一步操作，后面设置“共享文件夹”时会用到，用到时详说。）

点击下一步，配置内存大小和处理器数目：

![]({{site.baseurl}}/images/Linux/20231027181641.png)

点击下一步，配置虚拟硬盘大小：

![]({{site.baseurl}}/images/Linux/20231027181723.png)

点击下一步，看一下上面概括起来的配置：

![]({{site.baseurl}}/images/Linux/20231027181748.png)

点击“完成”按钮，等待安装完成，不过这里安装好的是 虚拟机，不是 Centos7.9 系统环境，所以右侧提示“不能安装系统”：

![]({{site.baseurl}}/images/Linux/20231027181904.png)

### CPU开启虚拟机支持功能

有些电脑在虚拟机安装后或启动时会报错：

![]({{site.baseurl}}/images/Linux/20231027160948.png)

```
Not in a hypervisor partition (HVP=0) (VERR_NEM_NOT_AVAILABLE).
VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED).
返回 代码:
E_FAIL (0X80004005)
组件:
ConsoleWrap
界面:
IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}
```

查询原因后是CPU未开启或不支持虚拟机功能，我们可以在开机阶段进入BIOS开启虚拟机功能。

CPU的硬件虚拟化主要包括Intel的VT-x和AMD的AMD-V技术。它通过提供基于芯片的功能，借助兼容VMM软件能够改进纯软件解决方案。
由于虚拟化硬件可提供全新的架构，支持操作系统直接在上面运行，从而无需进行二进制转换，减少了相关的性能开销，
极大简化了VMM设计，进而使VMM能够按通用标准进行编写，性能更加强大。
说到底就是在CPU芯片上支持虚拟机功能，不过从i5开始才开始支持。

报错原因：正在尝试在没有开启硬件虚拟化的情况下运行虚拟机。

第一个错误 "Not in a hypervisor partition (HVP=0)" 错误提示你可能没有在支持硬件虚拟化的环境下运行你的程序。

第二个错误 "VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED)" 
说明你的电脑BIOS设置中禁用了VT-x。VT-x 或 VMX 是Intel的一款虚拟化技术，它允许一台机器运行多个操作系统，
每个操作系统都有自己的隔离环境，这种技术需要在 BIOS 中开启。
你的程序可能正在尝试使用这个技术来运行一个虚拟机，但是因为VT-x被禁用，所以无法运行。

为了解决这个问题，你需要进入 BIOS 设置，然后开启你的 CPU 的虚拟化支持。

这个步骤根据电脑品牌和型号的不同可能会有所不同，通常在开机时按下 F2、F10、Del 等键可以进入 BIOS 设置。
在 BIOS 设置中找到类似于 "Intel Virtualization Technology" 或 "Virtualization Technology" 的选项，将其设置为 "Enabled"。

保存你的设置并重启电脑后，你应该就可以在你的虚拟机中运行你的程序了。

例如在装了i5系列CPU的华硕电脑：
1. 开机时按 Del 键 进入BIOS，按 F7 进入 “Advanced Mode”（高级模式）
2. 在“Advanced Mode”（高级模式）下，按以下路径找到“Advanced”（高级）—>“CPU Configuration”（CPU设置）
3. 下滑页面到底部，“Intel (VMX) Virtual Technology”（或者叫虚拟化技术），然后把“Disabled”（禁用）修改为“Enabled”（启用）。
4. 按“F10”键保存并退出，就成功开启VT了。

现在再重新启动一下这个虚拟机就应该没有问题了。

### Centos7.9安装

选中左侧的 虚拟机，点击“启动”，弹出窗口中会提示你选择光驱，在这里选择我们上面下载好的 CentOS-7-x86_64-Minimal-2009.iso 包：

![]({{site.baseurl}}/images/Linux/20231027182012.png)

点击“挂载并尝试启动”，选择 “Install Centos 7”，回车安装：

![]({{site.baseurl}}/images/Linux/20231027182045.png)

选择语言：

![]({{site.baseurl}}/images/Linux/20231027182335.png)

提示安装位置有误：

![]({{site.baseurl}}/images/Linux/20231027182435.png)

点击，选择上面配置的 虚拟硬盘，然后点击左上角的 安装目标位置“完成”按钮：

![]({{site.baseurl}}/images/Linux/20231027182537.png)

点击开始安装，进入安装进度页：

![]({{site.baseurl}}/images/Linux/20231027182649.png)

设置 ROOT 账户密码：

![]({{site.baseurl}}/images/Linux/20231027182723.png)

创建用户：

![]({{site.baseurl}}/images/Linux/20231027182824.png)

现在我们可以看一下虚拟机安装Centos的具体参数（现在 存储 部分中还有 光驱）：

![]({{site.baseurl}}/images/Linux/20231027183053.png)

安装进度提示完成，点击“重启”按钮：

![]({{site.baseurl}}/images/Linux/20231027183229.png)

Centos7.9服务器启动成功，进入命令行模式：

![]({{site.baseurl}}/images/Linux/20231027183505.png)

我们再看一下虚拟机安装的Centos的具体参数（现在 存储 部分中 光驱 已经没有 盘片 了）：

![]({{site.baseurl}}/images/Linux/20231027183534.png)

在命令行窗口中登录 root 账户：

![]({{site.baseurl}}/images/Linux/20231027183819.png)

至此，Centos7.9安装完成。

接下来是服务器配置部分。

### SSH客户端连接

一、开启(Host-0nly)网络

看一下现在服务器的网络信息（虚拟机无法访问外网，下面网络配置部分）：

![]({{site.baseurl}}/images/Linux/20231027224336.png)

虚拟机没有IP，宿主主机无法访问虚拟机服务器，需要为服务器配置IP，我们选择 Host-Only模式。

关闭服务器运行，点击服务器的设置，查看“网络”，切换到“网卡2”，选择 “仅主机(Host-0nly)网络”，确定保存。

![]({{site.baseurl}}/images/Linux/20231028171849.png)

重新启动服务器，再次看一下服务器的网卡详情：

![]({{site.baseurl}}/images/Linux/20231028172221.png)

可以看到服务器的IP地址为：192.168.56.102。

在Win10的命令行中 ping 一下该地址，确认下主机是否可以访问服务器：

```
C:\Users>ping 192.168.56.102

正在 Ping 192.168.56.102 具有 32 字节的数据:
来自 192.168.56.102 的回复: 字节=32 时间=203ms TTL=64
来自 192.168.56.102 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.56.102 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.56.102 的回复: 字节=32 时间<1ms TTL=64

192.168.56.102 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 0ms，最长 = 203ms，平均 = 50ms

C:\Users>
```

访问正常。

在主机中通过SSH协议管理服务器会方便很多。SSH (Secure Shell) 是一种网络协议，可实现两个设备之间的安全通信，
通常用于访问远程服务器以及传输文件或执行命令。

一般每台Linux服务器都会安装OpenSSH，OpenSSH 是 SSH （Secure SHell） 协议的免费开源实现，是SSH协议的服务端实现，
客户端通过SSH协议访问服务器的软件有PuTTY、Xshell等。

在服务器中看一下SSH服务端是否启动：

![]({{site.baseurl}}/images/Linux/20231028171601.png)

正常运行中。如果服务不存在，我们需要安装下 SSH协议服务软件。

我们通过PuTTY连接下服务器：

![]({{site.baseurl}}/images/Linux/20231028172947.png)

“Open”运行，输入服务器用户账号登录服务器成功：

```
login as: root
root@192.168.56.102's password:
Last login: Sat Oct 28 23:05:32 2023 from 192.168.56.1
[root@localhost ~]#
[root@localhost ~]# pwd
/root
[root@localhost ~]#
```

接下来就可以在主机本地操作服务器了，比原来在虚拟机打开的服务器窗口中操作时总是提示“捕获鼠标”方便了不少。
下次服务器启动时，选择“无界面启动”。

二、Win10系统更新后错误修复

记一个发生过的错误。Host-0nly已经正常使用了一段时间，在一次Win10系统更新后，再次启动虚拟机时报错：
```
虚拟电脑名称: Centos7.9

Failed to open/create the internal network 'HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter' (VERR_INTNET_FLT_IF_NOT_FOUND).
Failed to attach the network LUN (VERR_INTNET_FLT_IF_NOT_FOUND).
返回 代码:
E_FAIL (0X80004005)
组件:
ConsoleWrap
界面:
IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}
```

可以看出是 Host-Only 相关方面的错误，与本次Win10系统更新有关。

查询后，解决方案：在Win10中打开 “打开网络和Internet设置”，点击左侧的 “更改适配器设置”，
选中 “VirtualBox Host-Only Ethernet Adapter”,右击选中“属性”，勾选中“VitrualBox NDIS6 Bridged Networking Driver”，
点击“确定”保存，再次启动虚拟机就可以了。

### 网络配置

看一下服务器访问外网的情况：
```
[root@localhost ~]# ping www.baidu.com
ping: www.baidu.com: 未知的名称或服务
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# ping 36.155.132.55
connect: 网络不可达
[root@localhost ~]#
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 351sec preferred_lft 351sec
    inet6 fe80::90bb:7c4e:f558:7881/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]#
[root@localhost ~]# ls /etc/sysconfig/network-scripts/
ifcfg-enp0s3  ifdown-ipv6    ifdown-Team      ifup-eth    ifup-post      ifup-tunnel
ifcfg-lo      ifdown-isdn    ifdown-TeamPort  ifup-ippp   ifup-ppp       ifup-wireless
ifdown        ifdown-post    ifdown-tunnel    ifup-ipv6   ifup-routes    init.ipv6-global
ifdown-bnep   ifdown-ppp     ifup             ifup-isdn   ifup-sit       network-functions
ifdown-eth    ifdown-routes  ifup-aliases     ifup-plip   ifup-Team      network-functions-ipv6
ifdown-ippp   ifdown-sit     ifup-bnep        ifup-plusb  ifup-TeamPort
[root@localhost ~]#
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=c7c6c14c-c2c1-424f-9367-621a3c0a36c7
DEVICE=enp0s3
ONBOOT=no
[root@localhost ~]#
```

访问失败，看到 `ONBOOT=no` ，需要修改为 `ONBOOT=yes` 。在网卡配置中，`ONBOOT="yes"` 表示在系统启动时激活网卡。
只有处于激活状态的网卡才能去连接网络，进行网络通讯。

修改后重启网卡，然后再跨网段访问试一下：
```
[root@localhost ~]# vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
[root@localhost ~]#
[root@localhost ~]# systemctl restart network
[root@localhost ~]#
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 85874sec preferred_lft 85874sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 351sec preferred_lft 351sec
    inet6 fe80::90bb:7c4e:f558:7881/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]#
[root@localhost ~]# ping www.baidu.com
PING www.a.shifen.com (14.119.104.254) 56(84) bytes of data.
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=1 ttl=53 time=27.1 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=2 ttl=53 time=32.1 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=3 ttl=53 time=26.6 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=4 ttl=53 time=26.6 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=5 ttl=53 time=26.9 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=6 ttl=53 time=26.7 ms
64 bytes from 14.119.104.254 (14.119.104.254): icmp_seq=7 ttl=53 time=26.8 ms
^C
--- www.a.shifen.com ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 7741ms
rtt min/avg/max/mdev = 26.647/27.578/32.105/1.858 ms
[root@localhost ~]#
[root@localhost ~]# ping 14.119.104.254
PING 14.119.104.254 (14.119.104.254) 56(84) bytes of data.
64 bytes from 14.119.104.254: icmp_seq=1 ttl=53 time=27.3 ms
64 bytes from 14.119.104.254: icmp_seq=2 ttl=53 time=26.7 ms
64 bytes from 14.119.104.254: icmp_seq=3 ttl=53 time=26.7 ms
64 bytes from 14.119.104.254: icmp_seq=4 ttl=53 time=26.5 ms
64 bytes from 14.119.104.254: icmp_seq=5 ttl=53 time=26.7 ms
^C
--- 14.119.104.254 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4010ms
rtt min/avg/max/mdev = 26.578/26.845/27.328/0.292 ms
[root@localhost ~]#
```

访问顺利，网络配置成功。

### 防火墙配置

一、关闭防火墙

在本地主机使用虚拟机服务器服务，我们可以直接选择关闭防火墙：

> systemctl stop firewalld

明细：
```
[root@10 ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since 一 2023-10-30 10:10:57 CST; 7s ago
     Docs: man:firewalld(1)
 Main PID: 7777 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─7777 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

10月 30 10:10:57 10.0.2.15 systemd[1]: Starting firewalld - dynamic firewal....
10月 30 10:10:57 10.0.2.15 systemd[1]: Started firewalld - dynamic firewall....
10月 30 10:10:57 10.0.2.15 firewalld[7777]: WARNING: AllowZoneDrifting is e....
Hint: Some lines were ellipsized, use -l to show in full.
[root@10 ~]#
[root@10 ~]# systemctl stop firewalld
[root@10 ~]#
[root@10 ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since 一 2023-10-30 10:11:19 CST; 2s ago
     Docs: man:firewalld(1)
  Process: 7777 ExecStart=/usr/sbin/firewalld --nofork --nopid $FIREWALLD_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 7777 (code=exited, status=0/SUCCESS)

10月 30 10:10:57 10.0.2.15 systemd[1]: Starting firewalld - dynamic firewal....
10月 30 10:10:57 10.0.2.15 systemd[1]: Started firewalld - dynamic firewall....
10月 30 10:10:57 10.0.2.15 firewalld[7777]: WARNING: AllowZoneDrifting is e....
10月 30 10:11:14 10.0.2.15 systemd[1]: Stopping firewalld - dynamic firewal....
10月 30 10:11:19 10.0.2.15 systemd[1]: Stopped firewalld - dynamic firewall....
Hint: Some lines were ellipsized, use -l to show in full.
[root@10 ~]#
```

二、开放指定端口

现在我们对外开放了ssh服务的20端口，但没有对外开放80端口，我们在Win10的命令行中`telnet`一下服务器的80端口看一下：
```
C:\Users>telnet 192.168.56.102 22
SSH-2.0-OpenSSH_7.4

Protocol mismatch.

遗失对主机的连接。

C:\Users>telnet 192.168.56.102 80
正在连接192.168.56.102...无法打开到主机的连接。 在端口 80: 连接失败

C:\Users>
``` 

我们需要开放80端口，在服务器命令行中看一下防火墙的内容：
```
[root@10 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@10 ~]#
```

开放80端口，然后重启防火墙：
```
> firewall-cmd --zone=public --add-port=80/tcp --permanent
>
> firewall-cmd --zone=public --add-service=http --permanent
>
> firewall-cmd --reload
```

明细：
```
[root@10 ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent
success
[root@10 ~]#
[root@10 ~]# firewall-cmd --zone=public --add-service=http --permanent
success
[root@10 ~]#
[root@10 ~]# firewall-cmd --reload
success
[root@10 ~]#
[root@10 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client http ssh
  ports: 80/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@10 ~]#
```

查看正在运行监听的端口，netstat需要安装：
```
[root@10 ~]# yum install -y net-tools
[root@10 ~]#
[root@10 ~]# netstat -lnpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1254/sshd
tcp6       0      0 :::22                   :::*                    LISTEN      1254/sshd
[root@10 ~]#
```

### 更换yum源

为了提高下载速度，把yum源更换为国内源。

1、备份

> mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

2、下载新的 CentOS-Base.repo 到 /etc/yum.repos.d/

> curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

3、运行 yum makecache 生成缓存

> yum clean all && yum makecache

明细：
```
[root@10 ~]# ls /etc/yum.repos.d/
CentOS-Base.repo       CentOS-fasttrack.repo  CentOS-Vault.repo
CentOS-CR.repo         CentOS-Media.repo      CentOS-x86_64-kernel.repo
CentOS-Debuginfo.repo  CentOS-Sources.repo
[root@10 ~]#
[root@10 ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.r epo.backup
[root@10 ~]# 
[root@10 ~]# curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.co m/repo/Centos-7.repo
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2523  100  2523    0     0   7122      0 --:--:-- --:--:-- --:--:--  7127
[root@10 ~]#
[root@10 ~]# yum clean all && yum makecache
已加载插件：fastestmirror
正在清理软件源： base extras updates
Cleaning up list of fastest mirrors
已加载插件：fastestmirror
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
base                                                      | 3.6 kB  00:00:00
extras                                                    | 2.9 kB  00:00:00
updates                                                   | 2.9 kB  00:00:00
(1/10): base/7/x86_64/group_gz                            | 153 kB  00:00:00
(2/10): base/7/x86_64/primary_db                          | 6.1 MB  00:00:01
(3/10): extras/7/x86_64/primary_db                        | 250 kB  00:00:00
(4/10): extras/7/x86_64/filelists_db                      | 303 kB  00:00:00
(5/10): base/7/x86_64/other_db                            | 2.6 MB  00:00:00
(6/10): extras/7/x86_64/other_db                          | 150 kB  00:00:00
(7/10): base/7/x86_64/filelists_db                        | 7.2 MB  00:00:02
(8/10): updates/7/x86_64/filelists_db                     |  13 MB  00:00:10
(9/10): updates/7/x86_64/other_db                         | 1.4 MB  00:00:00
(10/10): updates/7/x86_64/primary_db                      |  24 MB  00:00:11
元数据缓存已建立
[root@10 ~]#
[root@10 ~]# ls /etc/yum.repos.d/     
CentOS-Base.repo         CentOS-Debuginfo.repo  CentOS-Sources.repo
CentOS-Base.repo.backup  CentOS-fasttrack.repo  CentOS-Vault.repo
CentOS-CR.repo           CentOS-Media.repo      CentOS-x86_64-kernel.repo
[root@10 ~]#
```

## 共享文件夹

设置共享文件夹，让虚拟机服务器和宿主主机共同操作同一个文件夹。

一、设置共享文件夹

![]({{site.baseurl}}/images/Linux/20231029201505.png)

`D:\develop` 文件夹是我们以后进行开发的根文件夹。

二、加载增强软件镜像

（一）在VirtualBox软件安装目录里找到下面的`VBoxGuestAdditions.ios`文件

（二）在centos虚拟机界面加载`VBoxGuestAdditions.iso`镜像文件

![]({{site.baseurl}}/images/Linux/20231029201605.png)

`lsscsi` 命令查看SATA/SCSI设备(或主机)及它们的属性：
```
[root@10 ~]# lsscsi
[1:0:0:0]    cd/dvd  VBOX     CD-ROM           1.0   /dev/sr0
[2:0:0:0]    disk    ATA      VBOX HARDDISK    1.0   /dev/sda
[root@10 ~]#
```

`/dev/sr0` 就是光驱，这个位置上是用来插入并读取光盘内容的。

把`/dev/sr0`挂载到`/media/cdrom`文件夹里：
```
[root@10 ~]# mkdir -p /media/cdrom
[root@10 ~]#
[root@10 ~]# mount /dev/sr0 /media/cdrom
mount: /dev/sr0 写保护，将以只读方式挂载
[root@10 ~]#
[root@10 ~]# ll /media/cdrom
总用量 42137
-r--r--r--. 1 root root     1048 7月  27 00:44 AUTORUN.INF
-r-xr-xr-x. 1 root root     6848 10月 13 00:56 autorun.sh
dr-xr-xr-x. 2 root root     1468 10月 13 01:41 cert
dr-xr-xr-x. 2 root root     1252 10月 13 01:41 NT3x
dr-xr-xr-x. 2 root root     2828 10月 13 01:41 OS2
-r-xr-xr-x. 1 root root     5096 10月 13 00:56 runasroot.sh
-r--r--r--. 1 root root      592 10月 13 01:41 TRANS.TBL
-r--r--r--. 1 root root  2203724 10月 13 01:00 VBoxDarwinAdditions.pkg
-r-xr-xr-x. 1 root root     4224 10月 13 01:00 VBoxDarwinAdditionsUninstall.tool
-r-xr-xr-x. 1 root root  6306248 10月 13 00:56 VBoxLinuxAdditions.run
-r--r--r--. 1 root root  9405440 10月 13 00:56 VBoxSolarisAdditions.pkg
-r-xr-xr-x. 1 root root 15729536 10月 13 01:41 VBoxWindowsAdditions-amd64.exe
-r-xr-xr-x. 1 root root   242272 10月 13 00:57 VBoxWindowsAdditions.exe
-r-xr-xr-x. 1 root root  9232920 10月 13 01:19 VBoxWindowsAdditions-x86.exe
-r--r--r--. 1 root root      261 7月  27 00:44 windows11-bypass.reg
[root@10 ~]#
```

三、安装增强软件

（一）通过下面命令进行安装`VBoxLinuxAdditons.run`依赖功能，以及安装增强软件：

> yum update
> 
> yum install gcc kernel-devel kernel-headers dkms make bzip2 libXmu
> 
> sh /media/cdrom/VBoxLinuxAdditions.run

明细：
```
[root@10 ~]# sh /media/cdrom/VBoxLinuxAdditions.run
Verifying archive integrity...  100%   MD5 checksums are OK. All good.
Uncompressing VirtualBox 7.0.12 Guest Additions for Linux  100%
VirtualBox Guest Additions installer
Copying additional installer modules ...
Installing additional modules ...
VirtualBox Guest Additions: Starting.
VirtualBox Guest Additions: Setting up modules
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel
modules.  This may take a while.
VirtualBox Guest Additions: To build modules for other installed kernels, run
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>
VirtualBox Guest Additions: or
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all
VirtualBox Guest Additions: Kernel headers not found for target kernel
3.10.0-1160.el7.x86_64. Please install them and execute
  /sbin/rcvboxadd setup
VirtualBox Guest Additions: reloading kernel modules and services
VirtualBox Guest Additions: unable to load vboxguest kernel module, see dmesg
VirtualBox Guest Additions: kernel modules and services were not reloaded
The log file /var/log/vboxadd-setup.log may contain further information.
[root@10 ~]#
```

最后查看 `/media` 文件夹，多出的 `sf_develop` 文件夹对应我们设置的共享文件夹（develop 是上面设置的共享文件夹名称）：
```
[root@10 ~]# ll /media
总用量 0
drwxr-xr-x. 2 root root   6 10月 29 20:38 cdrom
drwxrwx---. 1 root vboxsf 0 10月 29 15:05 sf_develop
[root@10 ~]#
[root@10 ~]# ll /media/sf_develop/
总用量 0
drwxrwx---. 1 root vboxsf 0 10月 29 21:59 test
[root@10 ~]#
```

test文件夹 是在主机的 develop文件夹 下新建的文件夹。

（二）如果安装`VBoxLinuxAdditons.run`依赖功能时报错

如下报错：
```
[root@10 ~]# sh /media/cdrom/VBoxLinuxAdditions.run
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
[root@10 ~]#
```

执行以下命令来运行 VirtualBox Guest Additions 的设置脚本：

> /sbin/rcvboxadd setup 

明细：
```
[root@10 ~]# /sbin/rcvboxadd setup
VirtualBox Guest Additions: Starting.
VirtualBox Guest Additions: Setting up modules
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel
modules.  This may take a while.
VirtualBox Guest Additions: To build modules for other installed kernels, run
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>
VirtualBox Guest Additions: or
VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all
VirtualBox Guest Additions: Kernel headers not found for target kernel
3.10.0-1160.el7.x86_64. Please install them and execute
  /sbin/rcvboxadd setup
VirtualBox Guest Additions: reloading kernel modules and services
VirtualBox Guest Additions: unable to load vboxguest kernel module, see dmesg
VirtualBox Guest Additions: kernel modules and services were not reloaded
The log file /var/log/vboxadd-setup.log may contain further information.
[root@10 ~]#
```

然后重新安装增强软件：

> sh /media/cdrom/VBoxLinuxAdditions.run

### 磁盘相关命令

这部分内容，作为拓展了解即可。

`df` 查看当前机器的磁盘使用情况：
```
[root@10 ~]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)
[root@10 ~]#
[root@10 ~]# df
文件系统                   1K-块    已用     可用 已用% 挂载点
devtmpfs                 1927988       0  1927988    0% /dev
tmpfs                    1939972       0  1939972    0% /devm
tmpfs                    1939972    8756  1931216    1% /run
tmpfs                    1939972       0  1939972    0% /sys/fsroup
/dev/mapper/centos-root 17811456 1288624 16522832    8% /
/dev/sda1                1038336  ******   **4748   15% /boot
tmpfs                     387996       0   387996    0% /run/user/0
[root@10 ~]#
[root@10 ~]# df -h
文件系统                 容量  已用  可用 已用% 挂载点
devtmpfs                 1.9G     0  1.9G    0% /dev
tmpfs                    1.9G     0  1.9G    0% /dev/shm
tmpfs                    1.9G  8.6M  1.9G    1% /run
tmpfs                    1.9G     0  1.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root   17G  1.3G   16G    8% /
/dev/sda1               1014M  150M  865M   15% /boot
tmpfs                    379M     0  379M    0% /run/user/0
[root@10 ~]#
```

`lsscsi` 命令列出SATA/SCSI设备(或主机)及它们的属性：
```
[root@10 ~]# lsscsi
[0:0:0:0]    disk    ATA      VBOX HARDDISK    1.0   /dev/sda
[2:0:0:0]    cd/dvd  VBOX     CD-ROM           1.0   /dev/sr0
[root@10 ~]#
```

`fdisk -l` 命令可以查找硬盘和分区：
```
[root@10 ~]# fdisk -l

磁盘 /dev/sda：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000b260a

   设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

磁盘 /dev/mapper/centos-root：18.2 GB, 18249416704 字节，35643392 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/centos-swap：2147 MB, 2147483648 字节，4194304 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节

[root@10 ~]#
```

`lsblk` 命令显示磁盘相关分区、所属关系以及lvm信息：
```
[root@10 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sr0              11:0    1 1024M  0 rom
[root@10 ~]#
```

`pvs` 查看当期所有物理卷（或使用 `pvdisplay` 命令）：
```
[root@10 ~]# pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  centos lvm2 a--  <19.00g    0
[root@10 ~]#
[root@10 ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               centos
  PV Size               <19.00 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              4863
  Free PE               0
  Allocated PE          4863
  PV UUID               k9YCxy-F1PF-lntv-Cno1-A0JF-mTwh-2BP8fD

[root@10 ~]#
```

`vgs` 查看卷组（或使用 `vgdisplay` 命令）：
```
[root@10 ~]# vgs
  VG     #PV #LV #SN Attr   VSize   VFree
  centos   1   2   0 wz--n- <19.00g    0
[root@10 ~]#
[root@10 ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <19.00 GiB
  PE Size               4.00 MiB
  Total PE              4863
  Alloc PE / Size       4863 / <19.00 GiB
  Free  PE / Size       0 / 0
  VG UUID               suncF0-Ocef-aqc5-9KMX-JxmK-4XSO-1B1H3I

[root@10 ~]#
```

`lvs` 查看当前所有逻辑卷（或使用 lvdisplay 命令）：
```
[root@10 ~]# lvs
  LV   VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  root centos -wi-ao---- <17.00g
  swap centos -wi-ao----   2.00g
[root@10 ~]#
[root@10 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/centos/swap
  LV Name                swap
  VG Name                centos
  LV UUID                6eUSpC-Btof-EE9S-v8De-qOb8-dAEP-MPWY0l
  LV Write Access        read/write
  LV Creation host, time localhost, 2023-10-27 18:25:59 +0800
  LV Status              available
  # open                 2
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:1

  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                rn50GF-Z9f6-A9tK-EAOB-rd4o-1FYE-UAIHml
  LV Write Access        read/write
  LV Creation host, time localhost, 2023-10-27 18:26:00 +0800
  LV Status              available
  # open                 1
  LV Size                <17.00 GiB
  Current LE             4351
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0

[root@10 ~]#
```

## Git安装

> apt-get update  
>
> apt-get install git

明细：
```
root@a0c75b4db3a6:/var/www/html# apt-get update
...............
root@a0c75b4db3a6:/var/www/html# 
root@a0c75b4db3a6:/var/www/html# apt-get install git
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
...............
root@a0c75b4db3a6:/var/www/html# 
root@a0c75b4db3a6:/var/www/html# git --version
git version 2.30.2
root@a0c75b4db3a6:/var/www/html#
```

## Docker安装

一、安装

安装yum管理工具：

> yum install -y yum-utils

yum添加软件源：
```
> yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

如果不添加docker的软件源，会报错：
```
[root@10 ~]# yum install -y docker-ce
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
没有可用软件包 docker-ce。
错误：无须任何处理
[root@10 ~]#
```

安装docker-ce（官方维护的社区版）：

> yum install -y docker-ce

运行docker：

> systemctl start docker

明细：
```
[root@10 ~]# yum install -y yum-utils
已加载插件：fastestmirror
............
[root@10 ~]#
[root@10 ~]# yum-config-manager --add-repo https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
已加载插件：fastestmirror
adding repo from: https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
grabbing file https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
[root@10 ~]#
[root@10 ~]# yum install -y docker-ce
已加载插件：fastestmirror
............
完毕！
[root@10 ~]#
[root@10 ~]# systemctl start docker
[root@10 ~]#
[root@10 ~]# docker version
Client: Docker Engine - Community
 Version:           24.0.7
 API version:       1.43
 Go version:        go1.20.10
 Git commit:        afdd53b
 Built:             Thu Oct 26 09:11:35 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          24.0.7
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.10
  Git commit:       311b9ff
  Built:            Thu Oct 26 09:10:36 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.24
  GitCommit:        61f9fd88f79f081d64d6fa3bb1a0dc71ec870523
 runc:
  Version:          1.1.9
  GitCommit:        v1.1.9-0-gccaecfc
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
[root@10 ~]#
```

验证Docker引擎是否正确安装：

> docker run hello-world

明细：
```
[root@10 ~]# docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[root@10 ~]#
[root@10 ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
719385e32844: Pull complete
Digest: sha256:88ec0acaa3ec199d3b7eaf73588f4518c25f9d34f58ce9a0df68429c5af48e8d
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    9c7a54a9a43c   5 months ago   13.3kB
[root@10 ~]#
```

二、切换国内源

配置镜像加速：
```
[root@10 ~]# touch /etc/docker/daemon.json
[root@10 ~]# 
[root@10 ~]# vi /etc/docker/daemon.json
```

写入：
```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com", "https://docker.mirrors.ustc.edu.cn"]
}
```

重载 systemd 的脚本配置文件内容：
> systemctl daemon-reload

重启docker：
> systemctl restart docker

明细：
```
[root@10 ~]# systemctl daemon-reload
[root@10 ~]#
[root@10 ~]# systemctl restart docker
[root@10 ~]#
[root@10 ~]# docker info|grep "Registry Mirrors" -A 1
 Registry Mirrors:
  http://hub-mirror.c.163.com/
[root@10 ~]#
```

三、浏览器中查看 docker 相关信息

开启远程访问，编辑docker服务器上对应的配置文件：

> vi /usr/lib/systemd/system/docker.service

在 `ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock` 后追加 `-H tcp://0.0.0.0:2375` ：

明细：
```
[root@10 ~]# vi /usr/lib/systemd/system/docker.service
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service containerd.service time-set.target
Wants=network-online.target containerd.service
Requires=docker.socket

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutStartSec=0
RestartSec=2
Restart=always

# Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
# Both the old, and new location are accepted by systemd 229 and up, so using the old location
# to make them work for either version of systemd.
StartLimitBurst=3
```

重载 systemd 的脚本配置文件内容：

> systemctl daemon-reload

重启docker：

> systemctl restart docker

浏览器访问地址 http://192.168.56.102:2375/version ，返回（注意防火墙的配置）：
```
{
  "Platform": {
    "Name": "Docker Engine - Community"
  },
  "Components": [
    {
      "Name": "Engine",
      "Version": "24.0.7",
      "Details": {
        "ApiVersion": "1.43",
        "Arch": "amd64",
        "BuildTime": "2023-10-26T09:10:36.000000000+00:00",
        "Experimental": "false",
        "GitCommit": "311b9ff",
        "GoVersion": "go1.20.10",
        "KernelVersion": "3.10.0-1160.102.1.el7.x86_64",
        "MinAPIVersion": "1.12",
        "Os": "linux"
      }
    },
    {
      "Name": "containerd",
      "Version": "1.6.24",
      "Details": {
        "GitCommit": "61f9fd88f79f081d64d6fa3bb1a0dc71ec870523"
      }
    },
    {
      "Name": "runc",
      "Version": "1.1.9",
      "Details": {
        "GitCommit": "v1.1.9-0-gccaecfc"
      }
    },
    {
      "Name": "docker-init",
      "Version": "0.19.0",
      "Details": {
        "GitCommit": "de40ad0"
      }
    }
  ],
  "Version": "24.0.7",
  "ApiVersion": "1.43",
  "MinAPIVersion": "1.12",
  "GitCommit": "311b9ff",
  "GoVersion": "go1.20.10",
  "Os": "linux",
  "Arch": "amd64",
  "KernelVersion": "3.10.0-1160.102.1.el7.x86_64",
  "BuildTime": "2023-10-26T09:10:36.000000000+00:00"
}
```

看一下监听中的端口：
```
[root@10 ~]# netstat -lnpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1262/sshd
tcp6       0      0 :::22                   :::*                    LISTEN      1262/sshd
tcp6       0      0 :::2375                 :::*                    LISTEN      3128/dockerd
[root@10 ~]#
```

## PHP安装

### 基础安装

一、下载PHP的docker包

从官方基础版本构建。

下载7.4-fpm版：
> docker pull php:7.4-fpm

明细：
```
[root@10 ~]# docker pull php:7.4-fpm
7.4-fpm: Pulling from library/php
7.4-fpm: Pulling from library/php
a603fa5e3b41: Pull complete
c428f1a49423: Pull complete
156740b07ef8: Pull complete
fb5a4c8af82f: Pull complete
972155ae644b: Pull complete
a8e3b94fe6c1: Pull complete
93346a3f46bc: Pull complete
b922b67ca46b: Pull complete
6137f893bda6: Pull complete
79b1a1b78461: Pull complete
Digest: sha256:3ac7c8c74b2b047c7cb273469d74fc0d59b857aa44043e6ea6a0084372811d5b
Status: Downloaded newer image for php:7.4-fpm
docker.io/library/php:7.4-fpm
[root@10 ~]#
[root@10 ~]# ll
总用量 4
-rw-------. 1 root root 1437 10月 27 18:31 anaconda-ks.cfg
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    9c7a54a9a43c   5 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   11 months ago   443MB
[root@10 ~]#
```

二、创建容器并运行

命令：
```
docker run --name php_7.4-fpm -p 9000:9000 -v /media/sf_develop:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php:7.4-fpm
```

明细：
```
[root@10 ~]# systemctl restart docker
[root@10 ~]# 
[root@10 ~]# docker run --name php_7.4-fpm -p 9000:9000 -v /media/sf_develop:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php:7.4-fpm
a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND                   CREATED          STATUS                   PORTS                                       NAMES
a0c75b4db3a6   php:7.4-fpm   "docker-php-entrypoi…"   11 minutes ago   Up 4 minutes             0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   php_7.4-fpm
e386a696ef90   hello-world   "/hello"                  3 hours ago      Exited (0) 3 hours ago                                               confident_pike
[root@10 ~]#
```

进入容器，查看基本信息：
> docker exec -it php_7.4-fpm /bin/bash
>
> php -v 
>
> php -m

明细：
```
[root@10 ~]# docker exec -it php_7.4-fpm /bin/bash
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# php -v
PHP 7.4.33 (cli) (built: Nov 15 2022 06:05:55) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# php -m
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
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@a0c75b4db3a6:/var/www/html# pwd
/var/www/html
root@a0c75b4db3a6:/var/www/html# ls -l .
total 0
drwxrwx---. 1 root 995 0 Oct 29 21:59 test
root@a0c75b4db3a6:/var/www/html#
```

在test文件夹下写个PHP文件测试下。

退出PHP的Docker容器后，看一下现在端口的监听情况：
```
[root@10 ~]# netstat -nlpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1262/sshd
tcp        0      0 0.0.0.0:9000            0.0.0.0:*               LISTEN      4310/docker-proxy
tcp6       0      0 :::22                   :::*                    LISTEN      1262/sshd
tcp6       0      0 :::2375                 :::*                    LISTEN      4135/dockerd
tcp6       0      0 :::9000                 :::*                    LISTEN      4315/docker-proxy
[root@10 ~]#
```

### 用户准备

我们到连接的主机目录下查看文件属性，发现文件所属组为995：

```
[root@10 ~]# docker exec -it php_7.4-fpm /bin/bash
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# ls -l /var/www/html/test/
total 1
-rwxrwx---. 1 root 995 23 Oct 30 14:35 test.php
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
ssh:x:101:
root@a0c75b4db3a6:/var/www/html#
```

995这个组是不存在的，php的用户 www-data 用户ID是 33，组ID是 33，所以对主机文件没有操作权限，我们需要把 www-data 加到 995组下：
> groupadd -g 995 docker
>
> usermod -aG docker www-data

明细：
```
root@a0c75b4db3a6:/var/www/html# groupadd -g 995 docker
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
ssh:x:101:
docker:x:995:
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# usermod -aG docker www-data
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
ssh:x:101:
docker:x:995:www-data
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
root@a0c75b4db3a6:/var/www/html#
```

现在PHP就可以操作主机目录下的文件了。

### 拓展安装

一、拓展安装准备

一部分PHP拓展在源码目录下，首先提取php源码：
> docker-php-source extract

明细：
```
root@a0c75b4db3a6:/var/www/html# cd /usr/src
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls -l .
total 10180
-rw-r--r--. 1 root root 10420144 Nov 15  2022 php.tar.xz
-rw-r--r--. 1 root root      833 Nov 15  2022 php.tar.xz.asc
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# docker-php-source extract
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls -l /usr/src
total 10184
drwxr-xr-x. 16 root root     4096 Oct 30 14:40 php
-rw-r--r--.  1 root root 10420144 Nov 15  2022 php.tar.xz
-rw-r--r--.  1 root root      833 Nov 15  2022 php.tar.xz.asc
root@a0c75b4db3a6:/usr/src#
```

看一下放在`/usr/src/php/ext`目录下的拓展包有哪些：
```
root@a0c75b4db3a6:/usr/src# ls /usr/src/php
CODING_STANDARDS.md  buildconf.bat
CONTRIBUTING.md      configure
EXTENSIONS           configure.ac
LICENSE              docs
NEWS                 ext
README.REDIST.BINS   main
README.md            pear
TSRM                 php.ini-development
UPGRADING            php.ini-production
UPGRADING.INTERNALS  run-tests.php
Zend                 sapi
appveyor             scripts
azure                tests
azure-pipelines.yml  travis
build                win32
buildconf
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls /usr/src/php/ext
bcmath        fileinfo  mbstring      pdo_oci     skeleton   xml
bz2           filter    mysqli        pdo_odbc    snmp       xmlreader
calendar      ftp       mysqlnd       pdo_pgsql   soap       xmlrpc
com_dotnet    gd        oci8          pdo_sqlite  sockets    xmlwriter
ctype         gettext   odbc          pgsql       sodium     xsl
curl          gmp       opcache       phar        spl        zend_test
date          hash      openssl       posix       sqlite3    zip
dba           iconv     pcntl         pspell      standard   zlib
dom           imap      pcre          readline    sysvmsg
enchant       intl      pdo           reflection  sysvsem
exif          json      pdo_dblib     session     sysvshm
ext_skel.php  ldap      pdo_firebird  shmop       tidy
ffi           libxml    pdo_mysql     simplexml   tokenizer
root@a0c75b4db3a6:/usr/src#
```

这里可以看到 bcmath、pdo_mysql 等常用拓展包。

二、bcmath 拓展安装

安装命令：
> docker-php-ext-install bcmath

明细：
```
root@a0c75b4db3a6:/usr/src# ls /usr/local/etc/php/conf.d/
docker-php-ext-sodium.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# docker-php-ext-install bcmath
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
............
root@a0c75b4db3a6:/usr/src# php -m
[PHP Modules]
bcmath
Core
............

[Zend Modules]

root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls /usr/local/etc/php/conf.d/
docker-php-ext-bcmath.ini  docker-php-ext-sodium.ini
root@a0c75b4db3a6:/usr/src#
```

二、pdo_mysql 拓展安装

安装命令：
> docker-php-ext-install pdo_mysql

三、其他常用包

其他在 `/usr/src/php/ext` 下的常用包，安装方法也与上面一样，只是把包名换一下。

### Swoole安装

我们选择PECL方式来安装。

PECL，The PHP Extension Community Library，PHP拓展社区库（PEAR拓展版本），
是一个开放的并通过 PEAR打包格式(PHP Extension and Application Repository，PHP 扩展和应用仓库)来打包安装的 PHP 扩展库仓库。
与以往的多数 PEAR 包不同的是，PECL 扩展包含的是可以编译进 PHP Core 的 C 语言代码，
因此可以将 PECL 扩展库编译成为可动态加载的 `.so` 共享库，或者采用静态编译方式与 PHP 源代码编译为一体的方法进行扩展。
同时 pecl 也是基于 PECL仓库 的一个拓展安装管理工具。

先把 openssl/ssl.h 头文件安装好，不然安装过程中会报错
> apt-get update  
>
> apt-get install libssl-dev

然后下载和安装：
> pecl install swoole-4.8.13
>
> docker-php-ext-enable swoole

明细：
```
root@a0c75b4db3a6:/usr/src# ls /usr/src/php/ext
bcmath      ext_skel.php  intl      pcntl         phar        sockets    xmlreader
bz2         ffi           json      pcre          posix       sodium     xmlrpc
calendar    fileinfo      ldap      pdo           pspell      spl        xmlwriter
com_dotnet  filter        libxml    pdo_dblib     readline    sqlite3    xsl
ctype       ftp           mbstring  pdo_firebird  reflection  standard   zend_test
curl        gd            mysqli    pdo_mysql     session     sysvmsg    zip
date        gettext       mysqlnd   pdo_oci       shmop       sysvsem    zlib
dba         gmp           oci8      pdo_odbc      simplexml   sysvshm
dom         hash          odbc      pdo_pgsql     skeleton    tidy
enchant     iconv         opcache   pdo_sqlite    snmp        tokenizer
exif        imap          openssl   pgsql         soap        xml
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# pecl version
PEAR Version: 1.10.13
PHP Version: 7.4.33
Zend Engine Version: 3.4.0
Running on: Linux a0c75b4db3a6 3.10.0-1160.102.1.el7.x86_64 #1 SMP Tue Oct 17 15:42:21 UTC 2023 x86_64
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# pecl search swoole
Retrieving data...0%
..Matched packages, channel pecl.php.net:
=======================================
Package          Stable/(Latest) Local
openswoole       22.0.0 (stable)        High Performance Programmatic Server for PHP with Async IO, Coroutines and Fibers
swoole           5.1.0 (stable)         Event-driven asynchronous and concurrent networking engine with high performance for PHP.
swoole_serialize 0.1.1 (beta)           the fastest and smallest serialize fucntion bound for php7
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# pecl install swoole-4.8.13
downloading swoole-4.8.13.tgz ...
Starting to download swoole-4.8.13.tgz (2,106,874 bytes)
............
.....期间有几个可选拓展是否启用.......
checking enable sockets support... y
checking enable openssl support... y
checking enable http2.0 support... y
checking enable mysqlnd support... y
checking enable c-ares support... y
............
Build process completed successfully
Installing '/usr/local/lib/php/extensions/no-debug-non-zts-20190902/swoole.so'
Installing '/usr/local/include/php/ext/swoole/config.h'
Installing '/usr/local/include/php/ext/swoole/php_swoole.h'
install ok: channel://pecl.php.net/swoole-4.8.13
configuration option "php_ini" is not set to php.ini location
You should add "extension=swoole.so" to php.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls /usr/src/php/ext
bcmath      ext_skel.php  intl      pcntl         phar        sockets    xmlreader
bz2         ffi           json      pcre          posix       sodium     xmlrpc
calendar    fileinfo      ldap      pdo           pspell      spl        xmlwriter
com_dotnet  filter        libxml    pdo_dblib     readline    sqlite3    xsl
ctype       ftp           mbstring  pdo_firebird  reflection  standard   zend_test
curl        gd            mysqli    pdo_mysql     session     sysvmsg    zip
date        gettext       mysqlnd   pdo_oci       shmop       sysvsem    zlib
dba         gmp           oci8      pdo_odbc      simplexml   sysvshm
dom         hash          odbc      pdo_pgsql     skeleton    tidy
enchant     iconv         opcache   pdo_sqlite    snmp        tokenizer
exif        imap          openssl   pgsql         soap        xml
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# php -m
[PHP Modules]
bcmath
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
pdo_mysql
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# docker-php-ext-enable swoole
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# php -m
[PHP Modules]
bcmath
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
pdo_mysql
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# php --ri swoole

swoole

Swoole => enabled
Author => Swoole Team <team@swoole.com>
Version => 4.8.13
Built => Oct 30 2023 15:45:37
coroutine => enabled with boost asm context
epoll => enabled
eventfd => enabled
signalfd => enabled
cpu_affinity => enabled
spinlock => enabled
rwlock => enabled
openssl => OpenSSL 1.1.1w  11 Sep 2023
dtls => enabled
mutex_timedlock => enabled
pthread_barrier => enabled
futex => enabled
async_redis => enabled

Directive => Local Value => Master Value
swoole.enable_coroutine => On => On
swoole.enable_library => On => On
swoole.enable_preemptive_scheduler => Off => Off
swoole.display_errors => On => On
swoole.use_shortname => On => On
swoole.unixsock_buffer_size => 8388608 => 8388608
root@a0c75b4db3a6:/usr/src#
```

### Redis拓展安装

Redis拓展安装时，原想的是和上面Swoole安装一样，但 pecl 安装redis时要用到libigbinary-dev，
libigbinary-dev安装时在apt源中又找不到包，换了apt源后还是不行，操作下来不太顺利：
```
> apt-get update  
>
> apt-get install vim
>
> apt-get install libigbinary-dev 
>
> pecl install redis-6.0.2
>
> docker-php-ext-enable redis
```

看来要直接使用Redis拓展包源码进行安装了。

在容器中的 `/tmp` 目录下下载 redis-6.0.2.tgz 包，解压缩然后进入解压包，phpize 生成 configure文件：

> cd /tmp
>
> curl https://pecl.php.net/get/redis-6.0.2.tgz -O
>
> tar -zxf redis-6.0.2.tgz
>
> cd redis-6.0.2
> 
> /usr/local/bin/phpize

明细：
```
root@a0c75b4db3a6:/usr/src# cd /tmp
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# curl https://pecl.php.net/get/redis-6.0.2.tgz -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  357k  100  357k    0     0   100k      0  0:00:03  0:00:03 --:--:--  100k
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
redis-6.0.2.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# tar -zxf redis-6.0.2.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
package.xml  redis-6.0.2  redis-6.0.2.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# cd redis-6.0.2
root@a0c75b4db3a6:/tmp/redis-6.0.2#
root@a0c75b4db3a6:/tmp/redis-6.0.2# /usr/local/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
root@a0c75b4db3a6:/tmp/redis-6.0.2#
```

查看 php-config 地址，编译redis扩展：
```
> whereis php-config
>
> ./configure --with-php-config=/usr/local/bin/php-config
```

明细：
```
root@a0c75b4db3a6:/tmp/redis-6.0.2# whereis php-config
php-config: /usr/local/bin/php-config
root@a0c75b4db3a6:/tmp/redis-6.0.2#
root@a0c75b4db3a6:/tmp/redis-6.0.2# ./configure --with-php-config=/usr/local/bin/php-config
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
.....................
creating libtool
appending configuration tag "CXX" to libtool
configure: patching config.h.in
configure: creating ./config.status
config.status: creating config.h
root@a0c75b4db3a6:/tmp/redis-6.0.2#
```

编译并安装源代码：

> make && make install

明细：
```
root@a0c75b4db3a6:/tmp/redis-6.0.2# make
/bin/bash /tmp/redis-6.0.2/libtool --mode=compile cc 
..................
Build complete.
Don't forget to run 'make test'.

root@a0c75b4db3a6:/tmp/redis-6.0.2#
root@a0c75b4db3a6:/tmp/redis-6.0.2# make install
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
root@a0c75b4db3a6:/tmp/redis-6.0.2#
root@a0c75b4db3a6:/tmp/redis-6.0.2# ls /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
bcmath.so  opcache.so  pdo_mysql.so  redis.so  sodium.so  swoole.so
root@a0c75b4db3a6:/tmp/redis-6.0.2#
```

开启redis拓展（新建文件并写入内容）：
```
> cd /usr/local/etc/php/conf.d
>
> touch docker-php-ext-redis.ini
> 
> vim docker-php-ext-redis.ini
>
> echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/redis.so" >> docker-php-ext-redis.ini
```

明细：
```
root@a0c75b4db3a6:/tmp/redis-6.0.2# cd /usr/local/etc/php/conf.d
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# ls
docker-php-ext-bcmath.ini     docker-php-ext-sodium.ini
docker-php-ext-pdo_mysql.ini  docker-php-ext-swoole.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# touch docker-php-ext-redis.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# vim docker-php-ext-redis.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/redis.so" >> docker-php-ext-redis.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
```

退出容器，重新启动，再查看一下：

> exit
> 
> docker restart php_7.4-fpm

明细：
```
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# exit
exit
[root@10 ~]#
[root@10 ~]# docker restart php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
[root@10 ~]# docker exec -it php_7.4-fpm /bin/bash
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# php -m | grep redis
redis
root@a0c75b4db3a6:/var/www/html#
```

### Composer安装

下载并安装：
```
> php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
>
> php composer-setup.php
```

安装完成后移除安装文件：
```
> php -r "unlink('composer-setup.php');"
```

明细：
```
root@a0c75b4db3a6:/usr/src# cd /usr/local/src
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# ls
composer-setup.php
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# php composer-setup.php
All settings correct for using Composer
Downloading...

Composer (version 2.6.5) successfully installed to: /usr/local/src/composer.phar
Use it: php composer.phar

root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# php -r "unlink('composer-setup.php');"
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# ls
composer.phar
root@a0c75b4db3a6:/usr/local/src#
```

移动composer文件到 bin 目录：

> mv composer.phar /usr/local/bin/composer

明细：
```
root@a0c75b4db3a6:/usr/local/src# mv composer.phar /usr/local/bin/composer
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# composer -v
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 2.6.5 2023-10-06 10:11:52
............
root@a0c75b4db3a6:/usr/local/src#
```

设置国内包源：

> composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

明细：
```
root@a0c75b4db3a6:/usr/local/src# composer config -g -l repo.packagist
[repositories.packagist.org.type] composer
[repositories.packagist.org.url] https://repo.packagist.org
............
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src# composer config -g -l repo.packagist             
[repositories.packagist.org.type] composer
[repositories.packagist.org.url] https://mirrors.aliyun.com/composer/
............
root@a0c75b4db3a6:/usr/local/src#
root@a0c75b4db3a6:/usr/local/src#
```

composer使用过程中会用到 php-zip 和 unzip，需要提前下载好：

> apt-get install php-zip
>
> apt-get install unzip

明细：
```
root@a0c75b4db3a6:/var/www/html# apt-get install php-zip
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package php-zip is a virtual package provided by:
  php7.4-zip 7.4.33-1+deb11u4 [Not candidate version]

E: Package 'php-zip' has no installation candidate
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# apt-get install unzip
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  zip
The following NEW packages will be installed:
  unzip
0 upgraded, 1 newly installed, 0 to remove and 31 not upgraded.
Need to get 172 kB of archives.
After this operation, 393 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian bullseye/main amd64 unzip amd64 6.0-26+deb11u1 [172 kB]
Fetched 172 kB in 45s (3825 B/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package unzip.
(Reading database ... 14745 files and directories currently installed.)
Preparing to unpack .../unzip_6.0-26+deb11u1_amd64.deb ...
Unpacking unzip (6.0-26+deb11u1) ...
Setting up unzip (6.0-26+deb11u1) ...
root@a0c75b4db3a6:/var/www/html#
```

### 容器新增开放端口

随着容器中功能不多的增多，免不了需要开放更多的端口，可以按照下面步骤操作。

查看当前端口映射：

> docker port php_7.4-fpm

明细：
```
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND                   CREATED       STATUS                   PORTS                                       NAMES
a0c75b4db3a6   php:7.4-fpm   "docker-php-entrypoi…"   3 hours ago   Up 3 hours               0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   php_7.4-fpm
e386a696ef90   hello-world   "/hello"                  6 hours ago   Exited (0) 6 hours ago                                               confident_pike
[root@10 ~]#
[root@10 ~]# docker port php_7.4-fpm
9000/tcp -> 0.0.0.0:9000
9000/tcp -> [::]:9000
[root@10 ~]#
```

关闭php容器，和docker服务：

> docker stop php_7.4-fpm
>
> systemctl stop docker

明细：
```
[root@10 ~]# docker stop php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
[root@10 ~]# systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
[root@10 ~]#
```

查看容器对应的ID，为接下来修改 `/var/lib/docker/containers/` 下配置做好准备：
```
> docker inspect php_7.4-fpm | grep Id
```

明细：
```
[root@10 ~]# docker inspect php_7.4-fpm | grep Id
        "Id": "a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059",
[root@10 ~]#
[root@10 ~]# ls -l /var/lib/docker/containers/
总用量 0
drwx--x---. 4 root root 237 10月 30 14:19 a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059
drwx--x---. 4 root root 237 10月 30 14:19 e386a696ef901bfa7d1b1409fa059be484061631bb4d69ee3e9e3ab698f928cf
[root@10 ~]#
[root@10 ~]# ls -l /var/lib/docker/containers/a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059/
总用量 28
-rw-r-----. 1 root root  252 10月 30 14:19 a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059-json.log
drwx------. 2 root root    6 10月 30 14:12 checkpoints
-rw-------. 1 root root 4006 10月 30 14:19 config.v2.json
-rw-------. 1 root root 1324 10月 30 14:19 hostconfig.json
-rw-r--r--. 1 root root   13 10月 30 14:19 hostname
-rw-r--r--. 1 root root  174 10月 30 14:19 hosts
drwx--x---. 2 root root    6 10月 30 14:19 mounts
-rw-r--r--. 1 root root   53 10月 30 14:19 resolv.conf
-rw-r--r--. 1 root root   71 10月 30 14:19 resolv.conf.hash
[root@10 ~]#
```

修改 hostconfig.json 文件：
```
[root@10 ~]# vi /var/lib/docker/containers/a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059/hostconfig.json
[root@10 ~]#
```

如增加9501端口，写入配置：`"9501/tcp":[{"HostIp":"","HostPort":"9501"}]`

![]({{site.baseurl}}/images/Linux/20231030173858.png)

```
[root@10 ~]# vi /var/lib/docker/containers/a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059/config.v2.json
[root@10 ~]#
```

增加端口配置：`"9501/tcp":{}`

![]({{site.baseurl}}/images/Linux/20231030175129.png)

重启docker服务，和php容器，查看端口映射：
```
[root@10 ~]# systemctl start docker
[root@10 ~]#
[root@10 ~]# docker start php_7.4-fpm
php_7.4-fpm
[root@10 ~]# 
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND                   CREATED       STATUS                   PORTS                                                                                  NAMES
a0c75b4db3a6   php:7.4-fpm   "docker-php-entrypoi…"   4 hours ago   Up 13 seconds            0.0.0.0:9000->9000/tcp, :::9000->9000/tcp, 0.0.0.0:9501->9501/tcp, :::9501->9501/tcp   php_7.4-fpm
e386a696ef90   hello-world   "/hello"                  7 hours ago   Exited (0) 7 hours ago                                                                                          confident_pike
[root@10 ~]#
[root@10 ~]# docker port php_7.4-fpm
9000/tcp -> 0.0.0.0:9000
9000/tcp -> [::]:9000
9501/tcp -> 0.0.0.0:9501
9501/tcp -> [::]:9501
[root@10 ~]#
```

### 提交仓库

我们可以把上面配置好的容器做成一个包发布到Docker仓库中，下次直接从仓库中拉取就可以了，不用再一步步敲击配置了。

一、生成镜像

从容器Container生成image镜像。

```
> docker commit -a "baiyang" -m "php:7.4-fpm add vi\ext_swoole-4.8.13\ext_redis-6.0.2" php_7.4-fpm php-7.4:1.0
```

-a :镜像作者；   -m :备注文字；      容器名；       镜像名:标签。

明细：
```
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    9c7a54a9a43c   6 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
[root@10 ~]#
[root@10 ~]# docker ps
CONTAINER ID   IMAGE         COMMAND                   CREATED       STATUS        PORTS                                                                                 NAMES
a0c75b4db3a6   php:7.4-fpm   "docker-php-entrypoi…"   2 weeks ago   Up 21 hours   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp,  0.0.0.0:9501->9501/tcp, :::9501->9501/tcp php_7.4-fpm
[root@10 ~]#
[root@10 ~]# docker commit -a "baiyang" -m "php:7.4-fpm add vi\ext_swoole-4.8.13\ext_redis-6.0.2" php_7.4-fpm php-7.4:1.0
sha256:2a7836ecd1a300b1fb14bd2483f082272f625e0dec5d656aace1c4477ac7f2e8
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
php-7.4       1.0       2a7836ecd1a3   44 seconds ago   885MB
hello-world   latest    9c7a54a9a43c   6 months ago     13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago    443MB
[root@10 ~]#
[root@10 ~]# docker inspect --format='{{.Comment}}' php-7.4:1.0
php's 7.4-fpm add vi\ext_swoole-4.8.13\ext_redis-6.0.2
[root@10 ~]#
[root@10 ~]# docker inspect php-7.4:1.0
[
    {
        "Id": "sha256:2a7836ecd1a300b1fb14bd2483f082272f625e0dec5d656aace1c4477ac7f2e8",
        "RepoTags": [
            "php-7.4:1.0"
        ],
        "RepoDigests": [],
        "Parent": "sha256:38f2b691dcb8c6d4630caa2999173e35be341f2f1264164ae045d9bfb3906c28",
        "Comment": "php:7.4-fpm add vi\\ext_swoole-4.8.13\\ext_redis-6.0.2",
        "Created": "2023-11-14T03:38:26.271139554Z",
        "Container": "a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059",
        "ContainerConfig": {
            "Hostname": "a0c75b4db3a6",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9000/tcp": {},
                "9501/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "PHPIZE_DEPS=autoconf \t\tdpkg-dev \t\tfile \t\tg++ \t\tgcc \t\tlibc-dev \t\tmake \t\tpkg-config \t\tre2c",
                "PHP_INI_DIR=/usr/local/etc/php",
                "PHP_CFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_CPPFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_LDFLAGS=-Wl,-O1 -pie",
                "GPG_KEYS=42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312",
                "PHP_VERSION=7.4.33",
                "PHP_URL=https://www.php.net/distributions/php-7.4.33.tar.xz",
                "PHP_ASC_URL=https://www.php.net/distributions/php-7.4.33.tar.xz.asc",
                "PHP_SHA256=924846abf93bc613815c55dd3f5809377813ac62a9ec4eb3778675b82a27b927"
            ],
            "Cmd": [
                "php-fpm"
            ],
            "Image": "php:7.4-fpm",
            "Volumes": null,
            "WorkingDir": "/var/www/html",
            "Entrypoint": [
                "docker-php-entrypoint"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGQUIT"
        },
        "DockerVersion": "24.0.7",
        "Author": "baiyang",
        "Config": {
            "Hostname": "a0c75b4db3a6",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9000/tcp": {},
                "9501/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "PHPIZE_DEPS=autoconf \t\tdpkg-dev \t\tfile \t\tg++ \t\tgcc \t\tlibc-dev \t\tmake \t\tpkg-config \t\tre2c",
                "PHP_INI_DIR=/usr/local/etc/php",
                "PHP_CFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_CPPFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_LDFLAGS=-Wl,-O1 -pie",
                "GPG_KEYS=42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312",
                "PHP_VERSION=7.4.33",
                "PHP_URL=https://www.php.net/distributions/php-7.4.33.tar.xz",
                "PHP_ASC_URL=https://www.php.net/distributions/php-7.4.33.tar.xz.asc",
                "PHP_SHA256=924846abf93bc613815c55dd3f5809377813ac62a9ec4eb3778675b82a27b927"
            ],
            "Cmd": [
                "php-fpm"
            ],
            "Image": "php:7.4-fpm",
            "Volumes": null,
            "WorkingDir": "/var/www/html",
            "Entrypoint": [
                "docker-php-entrypoint"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 885362596,
        "VirtualSize": 885362596,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/fc5c96e6b9dbf58328d71feeefa75870a9838477e980de58cd4fbe9d3214ff44/diff:/var/lib/docker/overlay2/b6e3fd48125609ecea4a80b45a82a76c4b68be0cbd2b33d713fbe088322f3122/diff:/var/lib/docker/overlay2/740e2302d083a923d551f5a3cba278f60475c29cf321071b5fe99edda7009b20/diff:/var/lib/docker/overlay2/16d688c5615942a2d0d0ddb63418957a7c3fe49195b237a46e09e167292ef1eb/diff:/var/lib/docker/overlay2/79eda0d06bb282f6f9c844379059b98da6bf3460f26510464935a8c70cea3cf9/diff:/var/lib/docker/overlay2/9f26f4db2b79c55dabe01d8a5a2d73c9e3a55e0ef3016a09245c3d08ea678690/diff:/var/lib/docker/overlay2/43cf2bfacd4fd1a2e183d138b73586de49f3f1bf8a3da988a3048af9a1eb4ad2/diff:/var/lib/docker/overlay2/f2b5e9dd2deec3febc49b305e6dfcf3f8349bdf066c2448068f1b3d57df62594/diff:/var/lib/docker/overlay2/071f0d1ae9a19d24101d691e1fc694745e69c908dfe99ebc781b436cca1de318/diff:/var/lib/docker/overlay2/7dc269bd6d2a5787850d8578223347320775916c8073e4af7b9be7d6afadd647/diff",
                "MergedDir": "/var/lib/docker/overlay2/80bac22e14a498192b3ba1ef85aba9fc2a902c61815c98a6d06b9f038d5d7c15/merged",
                "UpperDir": "/var/lib/docker/overlay2/80bac22e14a498192b3ba1ef85aba9fc2a902c61815c98a6d06b9f038d5d7c15/diff",
                "WorkDir": "/var/lib/docker/overlay2/80bac22e14a498192b3ba1ef85aba9fc2a902c61815c98a6d06b9f038d5d7c15/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:ec4a38999118b78eab6899b913a548cb0b2c9b68fd05aff846a56b628b597f38",
                "sha256:f6011769641091803058f3d7f21ae44fe06946be14914ff729844e7ef94ab15c",
                "sha256:797a7c0590e0dd31ea2f40f5139ced40ad710a2db563718e77595c615ca0ae1a",
                "sha256:44148371c697ea6420ffeca0559033fbfe1a0e59e4b83edba4768342238b2782",
                "sha256:08cc615b0242bb13dd9b7f009b58207bd1503abdab48e4331502aff96e68e05a",
                "sha256:91fd2792fa749b55021a242357fb011906acc3a1700f0c3a69aaa3528a28ed2a",
                "sha256:89982c6135ad7dc8c363ff2ca816ef1f164e790fa8203de928ccb6acd01eeeb1",
                "sha256:7c314756ee72cc25be6bed25e57bb53379a304d1540a4119ae72f916e951ef91",
                "sha256:d78098596d788ff699ab6c79ecec1f1778b3deb1f217e5538c02d860ce4dd2be",
                "sha256:5e65a6c61859cb1afa8653262ac22b702f88d061d2aa5a6f9d6d4d767871d31b",
                "sha256:1b4e8aaa034fd93cadc90918af1ef7629dcafeaaf6bc14d45b0a362846134ca1"
            ]
        },
        "Metadata": {
            "LastTagTime": "2023-11-14T11:50:33.050616374+08:00"
        }
    }
]
[root@10 ~]#
```

镜像 php-7.4:1.0 生成成功。

当我们把本地的容器弄得很乱时，我们可以删除该容器，

> docker stop php_7.4-fpm
> 
> docker rm php_7.4-fpm

也可以删除原来的镜像：

> docker rmi php:7.4-fpm

然后直接从该镜像重新生成一个新的容器，生成时指定映射端口和映射目录：

```
> docker run --name php-7.4 -p 9000:9000 -p 9501:9501 -v /media/sf_develop:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php-7.4:1.0
```

明细：
```
[root@10 ~]# docker run --name php-7.4 -p 9000:9000 -p 9501:9501 -v /media/sf_develop:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php-7.4:1.0
ddc612af48f88c8ee6a6bf07e9e69274b88a8e2da1eef615779f05b427da444a
[root@10 ~]#
[root@10 ~]# docker ps
CONTAINER ID   IMAGE         COMMAND                   CREATED          STATUS         PORTS                                                                                                 NAMES
ddc612af48f8   php-7.4:1.0   "docker-php-entrypoi…"   10 seconds ago   Up 9 seconds   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp, 0.0.0.0:9501->9501/tcp, :::9501->9501/tcp, 9250-9269/tcp   php-7.4
[root@10 ~]#
```

二、提交仓库

我们可以把生成的镜像提交到Docker仓库中，以备日后直接使用。

首先在Docker仓库官网 <https://hub.docker.com> 申请账号，如用户名是 ibaiyang 。

然后本地命令行登录：
```
[root@10 ~]# docker login --username ibaiyang
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@10 ~]#
```

给镜像打标签(2a7836ecd1a3是镜像的ID)，然后推送到远程Docker仓库：

> docker tag 2a7836ecd1a3 ibaiyang/php-7.4:1.0
>
> docker push ibaiyang/php-7.4:1.0

```
[root@10 ~]# docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
php-7.4            1.0       2a7836ecd1a3   12 minutes ago   885MB
hello-world        latest    9c7a54a9a43c   6 months ago     13.3kB
php                7.4-fpm   38f2b691dcb8   12 months ago    443MB
[root@10 ~]#
[root@10 ~]# docker tag 2a7836ecd1a3 ibaiyang/php-7.4:1.0
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
ibaiyang/php-7.4   1.0       2a7836ecd1a3   12 minutes ago   885MB
php-7.4            1.0       2a7836ecd1a3   12 minutes ago   885MB
hello-world        latest    9c7a54a9a43c   6 months ago     13.3kB
php                7.4-fpm   38f2b691dcb8   12 months ago    443MB
[root@10 ~]#
[root@10 ~]# docker push ibaiyang/php-7.4:1.0
The push refers to repository [docker.io/ibaiyang/php-7.4]
1b4e8aaa034f: Pushed
5e65a6c61859: Pushed
d78098596d78: Pushed
7c314756ee72: Pushed
89982c6135ad: Pushed
91fd2792fa74: Pushed
08cc615b0242: Pushed
44148371c697: Pushed
797a7c0590e0: Pushed
f60117696410: Pushed
ec4a38999118: Pushed
1.0: digest: sha256:73635d95e50c44d50d0bb11cca5f4fa9a4454622bf63d1ed8965e13715939355 size: 2623
[root@10 ~]#
```

在Docker仓库官网的个人中心中就可以看到自己提交的镜像包了。

下次使用时，直接从仓库用Docker下载该imgae镜像：

> docker pull ibaiyang/php-7.4:1.0

### gd拓展安装

gd拓展安装依赖于zlib库、libpng库。我们需要先zlib库、libpng库，再安装gd拓展：

> apt-get install zlib1g-dev
>
> apt-get install libpng-dev
> 
> docker-php-ext-install gd

明细：
```
root@a0c75b4db3a6:/usr/src# apt-get install zlib1g-dev
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  zlib1g-dev
0 upgraded, 1 newly installed, 0 to remove and 31 not upgraded.
Need to get 191 kB of archives.
After this operation, 592 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian bullseye/main amd64 zlib1g-dev amd64 1:1.2.11.dfsg-2+deb11u2 [191 kB]
Fetched 191 kB in 19s (10.2 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package zlib1g-dev:amd64.
(Reading database ... 16744 files and directories currently installed.)
Preparing to unpack .../zlib1g-dev_1%3a1.2.11.dfsg-2+deb11u2_amd64.deb ...
Unpacking zlib1g-dev:amd64 (1:1.2.11.dfsg-2+deb11u2) ...
Setting up zlib1g-dev:amd64 (1:1.2.11.dfsg-2+deb11u2) ...
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# apt-get install libpng-dev
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libpng-tools libpng16-16
..............
Setting up libpng-dev:amd64 (1.6.37-3) ...
Processing triggers for libc-bin (2.31-13+deb11u5) ...
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# docker-php-ext-install gd
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
......
See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

+ strip --strip-all modules/gd.so
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
Installing header files:          /usr/local/include/php/
find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la      modules/* libs/*
root@a0c75b4db3a6:/usr/src#
```

查看拓展：
> php -m

```
root@a0c75b4db3a6:/usr/src# php -m | grep gd
gd
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# php -m
[PHP Modules]
bcmath
Core
ctype
curl
date
dom
fileinfo
filter
ftp
gd
hash
iconv
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]

root@a0c75b4db3a6:/usr/src#
```

但上面gd拓展支持的格式比较少：
```
GD Support 	enabled
GD Version 	bundled (2.1.0 compatible)
GIF Read Support 	enabled
GIF Create Support 	enabled
PNG Support 	enabled
libPNG Version 	1.6.37
WBMP Support 	enabled
XBM Support 	enabled
BMP Support 	enabled
TGA Read Support 	enabled 
```

开发过程中碰到其他格式需求时会报错，我们这里把常用的webp、jpeg、freetype也安装一下。

先卸载gd拓展，再安装webp、jpeg、freetype包，最后重新安装gd拓展，安装完成后退出并重启容器：

```
> rm -f /usr/local/etc/php/conf.d/docker-php-ext-gd.ini
>
> apt-get install -y libwebp-dev libjpeg-dev libfreetype6-dev
>
> docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2
>
> docker-php-ext-install gd
>
> exit
>
> docker restart php_7.4-fpm
```

明细：
```
root@a0c75b4db3a6:/usr/src# ls /usr/local/etc/php/conf.d
docker-php-ext-bcmath.ini     docker-php-ext-redis.ini   docker-php-ext-zip.ini
docker-php-ext-gd.ini         docker-php-ext-sodium.ini
docker-php-ext-pdo_mysql.ini  docker-php-ext-swoole.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# rm -f /usr/local/etc/php/conf.d/docker-php-ext-gd.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# apt-get install -y libwebp-dev libjpeg-dev libfreetype6-dev 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libbrotli-dev libfreetype-dev libfreetype6 libjpeg62-turbo libjpeg62-turbo-dev
  libwebp6 libwebpdemux2 libwebpmux3
...........
Setting up libjpeg62-turbo:amd64 (1:2.0.6-4) ...
Setting up libfreetype6:amd64 (2.10.4+dfsg-1+deb11u1) ...
Setting up libjpeg62-turbo-dev:amd64 (1:2.0.6-4) ...
Setting up libwebp6:amd64 (0.6.1-2.1+deb11u2) ...
Setting up libwebpmux3:amd64 (0.6.1-2.1+deb11u2) ...
Setting up libbrotli-dev:amd64 (1.0.9-2+b2) ...
Setting up libwebpdemux2:amd64 (0.6.1-2.1+deb11u2) ...
Setting up libjpeg-dev:amd64 (1:2.0.6-4) ...
Setting up libfreetype-dev:amd64 (2.10.4+dfsg-1+deb11u1) ...
Setting up libwebp-dev:amd64 (0.6.1-2.1+deb11u2) ...
Setting up libfreetype6-dev:amd64 (2.10.4+dfsg-1+deb11u1) ...
Processing triggers for libc-bin (2.31-13+deb11u5) ...
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
...........
creating libtool
appending configuration tag "CXX" to libtool
configure: patching config.h.in
configure: creating ./config.status
config.status: creating config.h
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# 
root@a0c75b4db3a6:/usr/src# docker-php-ext-install gd
/bin/bash /usr/src/php/ext/gd/libtool --mode=compile cc 
-I/usr/src/php/ext/gd/libgd -I. 
-I/usr/src/php/ext/gd -DPHP_ATOM_INC
...........
Build complete.
Don't forget to run 'make test'.

+ strip --strip-all modules/gd.so
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
Installing header files:          /usr/local/include/php/
find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la      modules/* libs/*
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# php -m | grep gd
gd
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# exit
exit
[root@10 ~]#
[root@10 ~]# docker restart php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
```

输出`phpinfo()`可以看到gd拓展相关的包：
```
GD Support 	enabled
GD Version 	bundled (2.1.0 compatible)
FreeType Support 	enabled
FreeType Linkage 	with freetype
FreeType Version 	2.10.4
GIF Read Support 	enabled
GIF Create Support 	enabled
JPEG Support 	enabled
libJPEG Version 	6b
PNG Support 	enabled
libPNG Version 	1.6.37
WBMP Support 	enabled
XBM Support 	enabled
WebP Support 	enabled
BMP Support 	enabled
TGA Read Support 	enabled 
```

### zib拓展安装

`composer install` 安装过程中报错：
```
error: /usr/src/php/ext/ext-zip does not exist

usage: /usr/local/bin/docker-php-ext-install [-jN] [--ini-name file.ini] ext-name [ext-name ...]
   ie: /usr/local/bin/docker-php-ext-install gd mysqli
       /usr/local/bin/docker-php-ext-install pdo pdo_mysql
       /usr/local/bin/docker-php-ext-install -j5 gd mbstring mysqli pdo pdo_mysql shmop

if custom ./configure arguments are necessary, see docker-php-ext-configure

Possible values for ext-name:
bcmath bz2 calendar ctype curl dba dom enchant exif ffi fileinfo filter ftp gd gettext gmp hash iconv imap 
intl json ldap mbstring mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci 
pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline reflection session shmop simplexml snmp 
soap sockets sodium spl standard sysvmsg sysvsem sysvshm tidy tokenizer xml xmlreader xmlrpc xmlwriter 
xsl zend_test zip

Some of the above modules are already compiled into PHP; please check
the output of "php -i" to see which modules are already loaded.
```

提示 ext-zip 不存在，我们需要安装 zip 拓展。

推荐使用拓展包源码进行编译安装。

做好准备工作，先给容器安装好cmake：
> apt-get update
>
> apt-get install cmake

明细：
```
root@a0c75b4db3a6:/usr/src# apt-get update
Hit:1 http://deb.debian.org/debian bullseye InRelease
Get:2 http://deb.debian.org/debian-security bullseye-security InRelease [48.4 kB]
Get:3 http://deb.debian.org/debian bullseye-updates InRelease [44.1 kB]
Get:4 http://deb.debian.org/debian-security bullseye-security/main amd64 Packages [260 kB]
Get:5 http://deb.debian.org/debian bullseye-updates/main amd64 Packages.diff/Index [22.9 kB]
Get:6 http://deb.debian.org/debian bullseye-updates/main amd64 Packages T-2023-11-13-2005.21-F-2023-11-13-2005.21.pdiff [474 B]
Get:6 http://deb.debian.org/debian bullseye-updates/main amd64 Packages T-2023-11-13-2005.21-F-2023-11-13-2005.21.pdiff [474 B]
Fetched 376 kB in 37s (10.1 kB/s)
Reading package lists... Done
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# apt-get install cmake
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  cmake-data libarchive13 libjsoncpp24 libncurses6 libncursesw6 libprocps8 librhash0 libtinfo6 libuv1
  procps psmisc
Suggested packages:
  cmake-doc ninja-build lrzip
The following NEW packages will be installed:
  cmake cmake-data libarchive13 libjsoncpp24 libncurses6 libncursesw6 libprocps8 librhash0 libuv1
  procps psmisc
The following packages will be upgraded:
  libtinfo6
1 upgraded, 11 newly installed, 0 to remove and 31 not upgraded.
Need to get 9340 kB of archives.
After this operation, 37.7 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://deb.debian.org/debian bullseye/main amd64 libtinfo6 amd64 6.2+20201114-2+deb11u2 [342 kB]
..........
Setting up procps (2:3.3.17-5) ...
Setting up cmake (3.18.4-2+deb11u1) ...
Processing triggers for libc-bin (2.31-13+deb11u5) ...
root@a0c75b4db3a6:/usr/src#
```

再安装好zip拓展依赖的libzip包：

> cd /tmp
>
> curl https://libzip.org/download/libzip-1.10.1.tar.gz -O
>
> tar -zxf libzip-1.10.1.tar.gz
>
> cd libzip-1.10.1
>
> mkdir build
>
> cd build
>
> cmake ..
>
> make && make install

明细：
```
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# cd /tmp
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# curl https://libzip.org/download/libzip-1.10.1.tar.gz -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1242k  100 1242k    0     0  87313      0  0:00:14  0:00:14 --:--:-- 40669
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
libzip-1.10.1.tar.gz  package.xml
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# tar -zxf libzip-1.10.1.tar.gz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
libzip-1.10.1  libzip-1.10.1.tar.gz  package.xml
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# cd libzip-1.10.1
root@a0c75b4db3a6:/tmp/libzip-1.10.1#
root@a0c75b4db3a6:/tmp/libzip-1.10.1# ls
API-CHANGES.md  LICENSE      THANKS        cmake               examples                man
AUTHORS         NEWS.md      TODO.md       cmake-compat        lib                     regress
CMakeLists.txt  README.md    android       cmake-config.h.in   libzip-config.cmake.in  src
INSTALL.md      SECURITY.md  appveyor.yml  cmake-zipconf.h.in  libzip.pc.in
root@a0c75b4db3a6:/tmp/libzip-1.10.1#
root@a0c75b4db3a6:/tmp/libzip-1.10.1# mkdir build
root@a0c75b4db3a6:/tmp/libzip-1.10.1#
root@a0c75b4db3a6:/tmp/libzip-1.10.1# cd build
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build#
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build# cmake ..
-- The C compiler identification is GNU 10.2.1
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
..........
-- Configuring done
-- Generating done
-- Build files have been written to: /tmp/libzip-1.10.1/build
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build#
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build# make && make install
[  0%] Generating zip_err_str.c
Scanning dependencies of target zip
[  0%] Building C object lib/CMakeFiles/zip.dir/zip_add.c.o
..........
-- Installing: /usr/local/share/doc/libzip/libzip/zip_stat_index.html
-- Installing: /usr/local/bin/zipcmp
-- Set runtime path of "/usr/local/bin/zipcmp" to ""
-- Installing: /usr/local/bin/zipmerge
-- Set runtime path of "/usr/local/bin/zipmerge" to ""
-- Installing: /usr/local/bin/ziptool
-- Set runtime path of "/usr/local/bin/ziptool" to ""
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build#
```

现在开始安装PHP的zip拓展。在容器中的 /tmp 目录下下载 zip-1.22.3.tgz 包，解压缩然后进入解压包，phpize 生成 configure文件：

> cd /tmp
>
> curl https://pecl.php.net/get/zip-1.22.3.tgz -O
>
> tar -zxf redis-6.0.2.tgz
>
> cd redis-6.0.2
>
> /usr/local/bin/phpize

明细：
```
root@a0c75b4db3a6:/tmp/libzip-1.10.1/build# cd /tmp
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# curl https://pecl.php.net/get/zip-1.22.3.tgz -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  355k  100  355k    0     0   224k      0  0:00:01  0:00:01 --:--:--  224k
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# tar -zxf zip-1.22.3.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
libzip-1.10.1  libzip-1.10.1.tar.gz  package.xml    zip-1.22.3    zip-1.22.3.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# cd zip-1.22.3
root@a0c75b4db3a6:/tmp/zip-1.22.3#
root@a0c75b4db3a6:/tmp/zip-1.22.3# /usr/local/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
root@a0c75b4db3a6:/tmp/zip-1.22.3#
```

查看 php-config 地址，编译redis扩展：
```
> whereis php-config
>
> ./configure --with-php-config=/usr/local/bin/php-config
```

明细：
```
root@a0c75b4db3a6:/tmp/zip-1.22.3# whereis php-config
php-config: /usr/local/bin/php-config
root@a0c75b4db3a6:/tmp/zip-1.22.3#
root@a0c75b4db3a6:/tmp/zip-1.22.3# ./configure --with-php-config=/usr/local/bin/php-config
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for pkg-config... /usr/bin/pkg-config
checking pkg-config is at least version 0.9.0... yes
.........
creating libtool
appending configuration tag "CXX" to libtool
configure: patching config.h.in
configure: creating ./config.status
config.status: creating config.h
root@a0c75b4db3a6:/tmp/zip-1.22.3#
```

编译并安装源代码：
> make && make install

明细：
```
root@a0c75b4db3a6:/tmp/zip-1.22.3# make && make install
/bin/bash /tmp/zip-1.22.3/libtool --mode=compile cc -I/usr/local/include -I. -I/tmp/zip-1.22.3 -DPHP_ATOM_INC 
-I/tmp/zip-1.22.3/include -I/tmp/zip-1.22.3/main -I/tmp/zip-1.22.3 -I/usr/local/include/php 
-I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend 
-I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -I/tmp/zip-1.22.3/php74  
-DHAVE_CONFIG_H  -g -O2   -c /tmp/zip-1.22.3/php74/php_zip.c -o php74/php_zip.lo
mkdir php74/.libs
.........
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
root@a0c75b4db3a6:/tmp/zip-1.22.3#
root@a0c75b4db3a6:/tmp/zip-1.22.3# ls /usr/local/lib/php/extensions/no-debug-non-zts-20190902
bcmath.so  gd.so  opcache.so  pdo_mysql.so  redis.so  sodium.so  swoole.so  zip.so
root@a0c75b4db3a6:/tmp/zip-1.22.3#
```

开启zip拓展（新建文件并写入内容）：
```
> cd /usr/local/etc/php/conf.d
>
> touch docker-php-ext-zip.ini
>
> echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/zip.so" >> docker-php-ext-zip.ini
```

明细：
```
root@a0c75b4db3a6:/var/www/html# cd /usr/local/etc/php/conf.d
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# ls
docker-php-ext-bcmath.ini     docker-php-ext-redis.ini  
docker-php-ext-gd.ini         docker-php-ext-sodium.ini
docker-php-ext-pdo_mysql.ini  docker-php-ext-swoole.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# touch docker-php-ext-zip.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/zip.so" >> docker-php-ext-zip.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
```

退出容器，重新启动，再查看一下：
> exit
>
> docker restart php_7.4-fpm

明细：
```
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# exit
exit
[root@10 ~]#
[root@10 ~]# docker restart php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
[root@10 ~]# docker exec -it php_7.4-fpm /bin/bash
root@a0c75b4db3a6:/var/www/html#
root@a0c75b4db3a6:/var/www/html# php -m | grep zip
zip
root@a0c75b4db3a6:/var/www/html#
```

### msgpack拓展安装

使用msgpack拓展包源码进行安装。在容器中的 /tmp 目录下下载 msgpack-2.2.0.tgz 包，解压缩然后进入解压包，phpize 生成 configure文件：

> cd /tmp
> 
> curl https://pecl.php.net/get/redis-6.0.2.tgz -O
> 
> tar -zxf redis-6.0.2.tgz
> 
> cd redis-6.0.2
> 
> /usr/local/bin/phpize

明细：
```
root@a0c75b4db3a6:/usr/src# cd /tmp
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# curl https://pecl.php.net/get/msgpack-2.2.0.tgz -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  124k  100  124k    0     0  88524      0  0:00:01  0:00:01 --:--:-- 88463
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
msgpack-2.2.0.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# tar -zxf msgpack-2.2.0.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# ls
package.xml  msgpack-2.2.0  msgpack-2.2.0.tgz
root@a0c75b4db3a6:/tmp#
root@a0c75b4db3a6:/tmp# cd msgpack-2.2.0
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# /usr/local/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
```

查看 php-config 地址，编译redis扩展：
```
> whereis php-config
>
> ./configure --with-php-config=/usr/local/bin/php-config
```

明细：
```
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# ls
CREDITS         config.m4        msgpack_class.h    msgpack_unpack.h
EXPERIMENTAL    config.w32       msgpack_convert.c  php_msgpack.h
LICENSE         configure        msgpack_convert.h  run-tests.php
README.md       configure.ac     msgpack_errors.h   tests
autom4te.cache  msgpack          msgpack_pack.c
build           msgpack.c        msgpack_pack.h
config.h.in     msgpack_class.c  msgpack_unpack.c
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# whereis php-config
php-config: /usr/local/bin/php-config
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# ./configure --with-php-config=/usr/local/bin/php-config
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for pkg-config... /usr/bin/pkg-config
checking pkg-config is at least version 0.9.0... yes
..........................
creating libtool
appending configuration tag "CXX" to libtool
configure: patching config.h.in
configure: creating ./config.status
config.status: creating config.h
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
```

编译并安装源代码：
> make && make install

明细：
```
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# make && make install
/bin/bash /tmp/msgpack-2.2.0/libtool --mode=compile cc  -I. -I/tmp/msgpack-2.2.0 -DPHP_ATOM_INC
..............................
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
Installing header files:          /usr/local/include/php/
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# ls /usr/local/lib/php/extensions/no-debug-non-zts-20190902
bcmath.so  msgpack.so  pdo_mysql.so  sodium.so  zip.so
gd.so      opcache.so  redis.so      swoole.so
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# ls /usr/local/include/php
TSRM  Zend  ext  include  main  sapi
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# ls /usr/local/include/php/ext
date    gd     json      msgpack  pdo      simplexml  swoole
dom     hash   libxml    mysqlnd  phar     spl        xml
filter  iconv  mbstring  pcre     session  standard
root@a0c75b4db3a6:/tmp/msgpack-2.2.0#
```

开启msgpack拓展（新建文件并写入内容）：

```
> cd /usr/local/etc/php/conf.d
>
> touch docker-php-ext-msgpack.ini
>
> echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/msgpack.so" >> docker-php-ext-msgpack.ini
```

明细：
```
root@a0c75b4db3a6:/tmp/msgpack-2.2.0# cd /usr/local/etc/php/conf.d
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# touch docker-php-ext-msgpack.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/msgpack.so" >> docker-php-ext-msgpack.ini
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
root@a0c75b4db3a6:/usr/local/etc/php/conf.d# php -m | grep msgpack
msgpack
root@a0c75b4db3a6:/usr/local/etc/php/conf.d#
```

退出容器，重新启动：

> exit
>
> docker restart php_7.4-fpm

### 打新镜像

上面安装gd拓展和zip拓展的过程很复杂，最好能打个包出来，下面具体操作下。

一、生成镜像

从容器Container生成image镜像：
```
> docker commit -a "baiyang" -m "php:7.4-fpm add new ext: gd,zip-1.22.3 on base of vi,ext_swoole-4.8.13,ext_redis-6.0.2" php_7.4-fpm php-7.4:1.1
```

明细：
```
[root@10 ~]# docker images宿主机中配置Redis容器的服务参
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
php-7.4       1.0       2a7836ecd1a3   3 weeks ago     885MB
hello-world   latest    9c7a54a9a43c   7 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
[root@10 ~]#
[root@10 ~]#
[root@10 ~]#
[root@10 ~]# docker commit -a "baiyang" -m "php:7.4-fpm add new ext: gd,zip-1.22.3 on base of vi,ext_swoole-4.8.13,ext_redis-6.0.2" php_7.4-fpm php-7.4:1.1
sha256:fea7b20a4a27568b1772b87d48d4033a13907b47776edda757becdc8a884010b
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
php-7.4       1.1       fea7b20a4a27   3 seconds ago   802MB
php-7.4       1.0       2a7836ecd1a3   3 weeks ago     885MB
hello-world   latest    9c7a54a9a43c   7 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
[root@10 ~]#
[root@10 ~]# docker inspect --format='{{.Comment}}' php-7.4:1.1
php:7.4-fpm add new ext: gd, zip-1.22.3 on base of vi,ext_swoole-4.8.13,ext_redis-6.0.2
[root@10 ~]#
[root@10 ~]# docker inspect php-7.4:1.1
[
    {
        "Id": "sha256:fea7b20a4a27568b1772b87d48d4033a13907b47776edda757becdc8a884010b",
        "RepoTags": [
            "php-7.4:1.1"
        ],
        "RepoDigests": [],
        "Parent": "sha256:38f2b691dcb8c6d4630caa2999173e35be341f2f1264164ae045d9bfb3906c28",
        "Comment": "php:7.4-fpm add new ext: gd, zip-1.22.3 on base of vi,ext_swoole-4.8.13,ext_redis-6.0.2",
        "Created": "2023-12-05T06:29:17.071771107Z",
        "Container": "a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059",
        "ContainerConfig": {
            "Hostname": "a0c75b4db3a6",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9000/tcp": {},
                "9501/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "PHPIZE_DEPS=autoconf \t\tdpkg-dev \t\tfile \t\tg++ \t\tgcc \t\tlibc-dev \t\tmake \t\tpkg-config \t\tre2c",
                "PHP_INI_DIR=/usr/local/etc/php",
                "PHP_CFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_CPPFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_LDFLAGS=-Wl,-O1 -pie",
                "GPG_KEYS=42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312",
                "PHP_VERSION=7.4.33",
                "PHP_URL=https://www.php.net/distributions/php-7.4.33.tar.xz",
                "PHP_ASC_URL=https://www.php.net/distributions/php-7.4.33.tar.xz.asc",
                "PHP_SHA256=924846abf93bc613815c55dd3f5809377813ac62a9ec4eb3778675b82a27b927"
            ],
            "Cmd": [
                "php-fpm"
            ],
            "Image": "php:7.4-fpm",
            "Volumes": null,
            "WorkingDir": "/var/www/html",
            "Entrypoint": [
                "docker-php-entrypoint"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGQUIT"
        },
        "DockerVersion": "24.0.7",
        "Author": "baiyang",
        "Config": {
            "Hostname": "a0c75b4db3a6",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9000/tcp": {},
                "9501/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "PHPIZE_DEPS=autoconf \t\tdpkg-dev \t\tfile \t\tg++ \t\tgcc \t\tlibc-dev \t\tmake \t\tpkg-config \t\tre2c",
                "PHP_INI_DIR=/usr/local/etc/php",
                "PHP_CFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_CPPFLAGS=-fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64",
                "PHP_LDFLAGS=-Wl,-O1 -pie",
                "GPG_KEYS=42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312",
                "PHP_VERSION=7.4.33",
                "PHP_URL=https://www.php.net/distributions/php-7.4.33.tar.xz",
                "PHP_ASC_URL=https://www.php.net/distributions/php-7.4.33.tar.xz.asc",
                "PHP_SHA256=924846abf93bc613815c55dd3f5809377813ac62a9ec4eb3778675b82a27b927"
            ],
            "Cmd": [
                "php-fpm"
            ],
            "Image": "php:7.4-fpm",
            "Volumes": null,
            "WorkingDir": "/var/www/html",
            "Entrypoint": [
                "docker-php-entrypoint"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 801912394,
        "VirtualSize": 801912394,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/fc5c96e6b9dbf58328d71feeefa75870a9838477e980de58cd4fbe9d3214ff44/diff:/var/lib/docker/overlay2/b6e3fd48125609ecea4a80b45a82a76c4b68be0cbd2b33d713fbe088322f3122/diff:/var/lib/docker/overlay2/740e2302d083a923d551f5a3cba278f60475c29cf321071b5fe99edda7009b20/diff:/var/lib/docker/overlay2/16d688c5615942a2d0d0ddb63418957a7c3fe49195b237a46e09e167292ef1eb/diff:/var/lib/docker/overlay2/79eda0d06bb282f6f9c844379059b98da6bf3460f26510464935a8c70cea3cf9/diff:/var/lib/docker/overlay2/9f26f4db2b79c55dabe01d8a5a2d73c9e3a55e0ef3016a09245c3d08ea678690/diff:/var/lib/docker/overlay2/43cf2bfacd4fd1a2e183d138b73586de49f3f1bf8a3da988a3048af9a1eb4ad2/diff:/var/lib/docker/overlay2/f2b5e9dd2deec3febc49b305e6dfcf3f8349bdf066c2448068f1b3d57df62594/diff:/var/lib/docker/overlay2/071f0d1ae9a19d24101d691e1fc694745e69c908dfe99ebc781b436cca1de318/diff:/var/lib/docker/overlay2/7dc269bd6d2a5787850d8578223347320775916c8073e4af7b9be7d6afadd647/diff",
                "MergedDir": "/var/lib/docker/overlay2/835377fa0cf2a443174ae0187bd90996bb9b35612d5809e0084d1f605c97615e/merged",
                "UpperDir": "/var/lib/docker/overlay2/835377fa0cf2a443174ae0187bd90996bb9b35612d5809e0084d1f605c97615e/diff",
                "WorkDir": "/var/lib/docker/overlay2/835377fa0cf2a443174ae0187bd90996bb9b35612d5809e0084d1f605c97615e/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:ec4a38999118b78eab6899b913a548cb0b2c9b68fd05aff846a56b628b597f38",
                "sha256:f6011769641091803058f3d7f21ae44fe06946be14914ff729844e7ef94ab15c",
                "sha256:797a7c0590e0dd31ea2f40f5139ced40ad710a2db563718e77595c615ca0ae1a",
                "sha256:44148371c697ea6420ffeca0559033fbfe1a0e59e4b83edba4768342238b2782",
                "sha256:08cc615b0242bb13dd9b7f009b58207bd1503abdab48e4331502aff96e68e05a",
                "sha256:91fd2792fa749b55021a242357fb011906acc3a1700f0c3a69aaa3528a28ed2a",
                "sha256:89982c6135ad7dc8c363ff2ca816ef1f164e790fa8203de928ccb6acd01eeeb1",
                "sha256:7c314756ee72cc25be6bed25e57bb53379a304d1540a4119ae72f916e951ef91",
                "sha256:d78098596d788ff699ab6c79ecec1f1778b3deb1f217e5538c02d860ce4dd2be",
                "sha256:5e65a6c61859cb1afa8653262ac22b702f88d061d2aa5a6f9d6d4d767871d31b",
                "sha256:96ae5e3fd0b9d2d61e6768bedcf8b8e0173c19705614151931039233ddeaa5c1"
            ]
        },
        "Metadata": {
            "LastTagTime": "2023-12-05T14:29:17.084946899+08:00"
        }
    }
]
[root@10 ~]#
```

二、提交仓库

我们把生成的镜像提交到Docker仓库中，以备日后直接使用。

本地命令行登录：
```
[root@10 ~]# docker login --username ibaiyang
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@10 ~]#
```

给镜像打标签(2a7836ecd1a3是镜像的ID)，然后推送到远程Docker仓库：

> docker tag fea7b20a4a27 ibaiyang/php-7.4:1.1
>
> docker push ibaiyang/php-7.4:1.1

```
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
php-7.4       1.1       fea7b20a4a27   17 minutes ago   802MB
php-7.4       1.0       2a7836ecd1a3   3 weeks ago      885MB
hello-world   latest    9c7a54a9a43c   7 months ago     13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago    443MB
[root@10 ~]#
[root@10 ~]# docker tag fea7b20a4a27 ibaiyang/php-7.4:1.1
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
php-7.4            1.1       fea7b20a4a27   19 minutes ago   802MB
ibaiyang/php-7.4   1.1       fea7b20a4a27   19 minutes ago   802MB
php-7.4            1.0       2a7836ecd1a3   3 weeks ago      885MB
hello-world        latest    9c7a54a9a43c   7 months ago     13.3kB
php                7.4-fpm   38f2b691dcb8   12 months ago    443MB
[root@10 ~]#
[root@10 ~]# docker push ibaiyang/php-7.4:1.1
The push refers to repository [docker.io/ibaiyang/php-7.4]
96ae5e3fd0b9: Pushed
5e65a6c61859: Layer already exists
d78098596d78: Layer already exists
7c314756ee72: Layer already exists
89982c6135ad: Layer already exists
91fd2792fa74: Layer already exists
08cc615b0242: Layer already exists
44148371c697: Layer already exists
797a7c0590e0: Layer already exists
f60117696410: Layer already exists
ec4a38999118: Layer already exists
1.1: digest: sha256:f653fb2a52cdb0ba6166d17a1356047ef4a025e442ac5d23c0e0df546e4b3bd0 size: 2623
[root@10 ~]#
```

## Nginx安装

### 基础安装

下载nginx包：
> docker pull nginx

明细：
```
[root@10 ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
............
[root@10 ~]# 
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         latest    593aee2afb64   5 days ago      187MB
hello-world   latest    9c7a54a9a43c   5 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   11 months ago   443MB
[root@10 ~]#
```

运行：
```
docker run --name nginx_php_7.4-fpm -p 80:80 -v /media/sf_develop/virtualbox/docker/nginx:/etc/nginx/conf.d -v /media/sf_develop:/usr/share/nginx/www -v /etc/localtime:/etc/localtime:ro --link php_7.4-fpm:php --privileged=true -d nginx
```

`D:\develop\virtualbox\docker\nginx` 文件夹用来保存我们所有项目的nginx配置。

`--link php_7.4-fpm:php` 可以不写，这个是docker内跨容器访问的配置。如果不写，Nginx容器 可以通过 宿主主机 中转访问 PHP容器，
在下面项目的conf配置中把 `fastcgi_pass   php:9000;` 改为 `fastcgi_pass   192.168.56.102:9000;` 。
这里需要注意一个问题，容器使用的是虚拟ip，每次启动，ip都会变化，只有不变化时，这里才方便使用，不然每次启动都要修改conf配置。

明细：
```
[root@10 ~]# docker run --name nginx_php_7.4-fpm -p 80:80 -v /media/sf_develop/virtualbox/docker/nginx:/etc/nginx/conf.d -v /media/sf_develop:/usr/share/nginx/www -v /etc/localtime:/etc/localtime:ro --link php_7.4-fpm:php --privileged=true -d nginx
f2d344418b9fb0dfcc75da53a6bfdaf5b7d722dab1c2edc0f06ad770c2143571
[root@10 ~]#
[root@10 ~]# docker exec -it nginx_php_7.4-fpm /bin/bash
root@f2d344418b9f:/#
root@f2d344418b9f:/# nginx -v
nginx version: nginx/1.25.3
root@f2d344418b9f:/#
```

### 用户准备

我们到连接的主机目录下查看文件属性，发现文件所属组为995：

```
root@f2d344418b9f:/# ls -l /usr/share/nginx/www/test
total 1
-rwxrwx---. 1 root 995 23 Oct 30 14:35 test.php
root@f2d344418b9f:/#
root@f2d344418b9f:/# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
nginx:x:101:101:nginx user:/nonexistent:/bin/false
root@f2d344418b9f:/#
root@f2d344418b9f:/# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
nginx:x:101:
root@f2d344418b9f:/#
```

995这个组是不存在的，nginx 的用户ID是 101，组ID是 101，所以对主机文件没有操作权限，我们需要把 nginx 加到 995组下：

> groupadd -g 995 docker
>
> usermod -aG docker nginx

明细：
```
root@f2d344418b9f:/# groupadd -g 995 docker
root@f2d344418b9f:/#
root@f2d344418b9f:/# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
nginx:x:101:
docker:x:995:
root@f2d344418b9f:/#
root@f2d344418b9f:/# usermod -aG docker nginx
root@f2d344418b9f:/#
root@f2d344418b9f:/# cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
nginx:x:101:
docker:x:995:nginx
root@f2d344418b9f:/#
root@f2d344418b9f:/# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
nginx:x:101:101:nginx user:/nonexistent:/bin/false
root@f2d344418b9f:/#
```

### 项目配置

下面举一个示例配置，域名 test.test 。

修改主机的hosts文件，Win10中位置：`C:\Windows\System32\drivers\etc\hosts`

文件中新增一行：`192.168.56.102      test.test`

在 `D:\develop\virtualbox\docker\nginx` 文件夹下 新增项目配置文件：`test.test.conf`

写入配置内容：
```
server {
    listen       80;
    server_name test.test;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/test;
        index  index.php;
        if (!-e $request_filename) {
               rewrite  ^(.*)$  /index.php?s=/$1  last;
               break;
         }
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
    location ~ \.php$ {
        root   /var/www/html/test;
        fastcgi_pass   php:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

查看nginx的相关配置有没有问题：
> nginx -t

重启nginx服务：
> nginx -s reload

明细：
```
root@f2d344418b9f:/# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@f2d344418b9f:/#
root@f2d344418b9f:/# nginx -s reload
2023/10/31 13:46:44 [notice] 55#55: signal process started
root@f2d344418b9f:/#
```

浏览器访问地址 `http://test.test/test.php`，得到想要输出。

最后我们可以看一下nginx原来在`/etc/nginx/conf.d`文件夹下的配置文件`default.conf`的内容，可以参考下：
```
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
```

## MySQL安装

### 基础安装

下载现在最新的8.0.35版本：
> docker pull mysql:8.0.35

明细：
```
[root@10 ~]# docker pull mysql:8.0.35
8.0.35: Pulling from library/mysql
8e0176adc18c: Pull complete
a6b6bf6e5d0f: Pull complete
c17b83f8620f: Pull complete
b2e259cd9b6c: Pull complete
366131ab00d1: Pull complete
2f99ba83a3cb: Pull complete
f7c88955f01f: Pull complete
577fb415d7f8: Pull complete
29160ed46eb1: Pull complete
69ce9884ce5d: Pull complete
848f0dceb14c: Pull complete
Digest: sha256:974cac08fff819ea2dfeb83fed4d2eb5100bb79603aff6148bdc53d8be4895f3
Status: Downloaded newer image for mysql:8.0.35
docker.io/library/mysql:8.0.35
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
php-7.4       1.0       2a7836ecd1a3   2 weeks ago     885MB
nginx-vim     latest    19c8bf579203   4 weeks ago     248MB
nginx         latest    593aee2afb64   5 weeks ago     187MB
mysql         8.0.35    96bc8cf3633b   5 weeks ago     582MB
hello-world   latest    9c7a54a9a43c   6 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
[root@10 ~]#
```

创建容器：
```
docker run \
  --name mysql_8.0.35 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v /user/local/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d mysql:8.0.35
```

说明：
```
--name：容器名，此处命名为mysql_8.0.35
-p：端口映射，此处映射 主机3306端口 到 容器的3306端口
-e：配置信息，此处配置mysql的root用户的登陆密码
-v /user/local/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d：服务配置目录
-v /etc/localtime:/etc/localtime:ro：映射这个目录是为了校正容器内的时间跟宿主机时间一致
--privileged=true：表示容器内的root用户拥有真正的root权限，而不是外部普通用户的权限
-d：后台运行容器，保证在退出终端后容器继续运行
```

明细：
```
[root@10 ~]# docker run \
>   --name mysql_8.0.35 \
>   -p 3306:3306 \
>   -e MYSQL_ROOT_PASSWORD=123456 \
>   -v /user/local/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
>   -v /etc/localtime:/etc/localtime:ro \
>   --privileged=true \
>   -d mysql:8.0.35
a75c23af4150953c59afcba8f5f19b351b82054493caed49b56cd385260b1346
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                    PORTS                                                  NAMES
a75c23af4150   mysql:8.0.35   "docker-entrypoint.s…"   8 seconds ago   Up 6 seconds              0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_8.0.35
f2d344418b9f   nginx          "/docker-entrypoint.…"   4 weeks ago     Exited (0) 25 hours ago                                                          nginx_php_7.4-fpm
a0c75b4db3a6   php:7.4-fpm    "docker-php-entrypoi…"   4 weeks ago     Exited (0) 25 hours ago                                                          php_7.4-fpm
e386a696ef90   hello-world    "/hello"                  4 weeks ago     Exited (0) 4 weeks ago                                                           confident_pike
[root@10 ~]#
```

进入容器，输入账户密码进人管理，远程登录授权：
> docker exec -it mysql_8.0.35 /bin/bash
> 
> mysql -u root -p
>
> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

`%` 表示可以远程登录，`localhost` 表示只允许本地登录，也可以是具体IP地址。

明细：
```
[root@10 ~]# docker exec -it mysql_8.0.35 /bin/bash
bash-4.4#
bash-4.4# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.35 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
Query OK, 0 rows affected (0.01 sec)

mysql>
```

然后在MySQL管理客户端就可以连接了，如 Navicat for MySQL。

### 新建数据库和用户

新建数据库：
> CREATE DATABASE IF NOT EXISTS test DEFAULT CHARSET utf8mb4 COLLATE utf8mb4_general_ci;

新建用户：
> CREATE USER 'test'@'%' IDENTIFIED BY '123456';

用户数据库赋权：
> GRANT ALL ON test.* TO 'test'@'%';
>
> flush privileges;

远程登录授权：
> ALTER USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

明细：
```
mysql> CREATE DATABASE IF NOT EXISTS test DEFAULT CHARSET utf8mb4 COLLATE utf8mb4_general_ci;
Query OK, 1 row affected (0.01 sec)

mysql>
mysql> CREATE USER 'test'@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> GRANT ALL ON test.* TO 'test'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> ALTER USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
Query OK, 0 rows affected (0.00 sec)

mysql>
```

## Redis安装

### 基础安装

下载现在最新的7.2.3版本：
> docker pull redis:7.2.3

明细：
```
root@10 ~]# docker pull redis:7.2.3
7.2.3: Pulling from library/redis
1f7ce2fa46ab: Pull complete
3c6368585bf1: Pull complete
3911d271d7d8: Pull complete
ac88aa9d4021: Pull complete
127cd75a68a2: Pull complete
4f4fb700ef54: Pull complete
f3993c1104fc: Pull complete
Digest: sha256:2976bc0437deff693af2dcf081a1d1758de8b413e6de861151a5a136c25eb9e4
Status: Downloaded newer image for redis:7.2.3
docker.io/library/redis:7.2.3
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
php-7.4       1.0       2a7836ecd1a3   2 weeks ago     885MB
redis         7.2.3     961dda256baa   2 weeks ago     138MB
nginx-vim     latest    19c8bf579203   4 weeks ago     248MB
nginx         latest    593aee2afb64   5 weeks ago     187MB
mysql         8.0.35    96bc8cf3633b   5 weeks ago     582MB
hello-world   latest    9c7a54a9a43c   7 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
mysql         8.0.28    f2ad9f23df82   19 months ago   521MB
[root@10 ~]#
```

创建容器：
```
docker run \
  --name redis_7.2.3 \
  -p 6379:6379 \
  -v /user/local/docker/redis_7.2.3/conf:/usr/local/etc/redis \
  -v /user/local/docker/redis_7.2.3/data:/data \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d redis:7.2.3 \
  redis-server \
  /usr/local/etc/redis/redis.conf
```

```
--name：容器名，此处命名为redis_7.2.3
-p：端口映射，此处映射 主机6379端口 到 容器的6379端口
-v /user/local/docker/redis_7.2.3/conf:/usr/local/etc/redis：服务配置目录，里面文件为 redis.conf
-v /user/local/docker/redis_7.2.3/data:/data：持久化的数据映射目录，可以不添加
-v /etc/localtime:/etc/localtime:ro：映射这个目录是为了校正容器内的时间跟宿主机时间一致
--privileged=true：表示容器内的root用户拥有真正的root权限，而不是外部普通用户的权限
-d：后台运行容器，保证在退出终端后容器继续运行
redis-server /usr/local/etc/redis/redis.conf：加载配置文件
```

配置文件/user/local/docker/redis_7.2.3/conf/redis.conf：
```
# 编辑redis.c0nf文件

# 数据库个数，默认16
databases 8

# 配置密码，默认无密码
requirepass 123456

# 数据持久化，默认no
# appendonly no
```

配置文件其他用户要有读的权限（至少要是004），不然会启动失败：
```
[root@10 ~]# ls -l /user/local/docker/redis_7.2.3/conf
总用量 4
-rwxr-x---. 1 root root 113 12月  4 12:08 redis.conf
[root@10 ~]#
[root@10 ~]# chmod 777 /user/local/docker/redis_7.2.3/conf/redis.conf
[root@10 ~]# 
[root@10 ~]# ls -l /user/local/docker/redis_7.2.3/conf
总用量 4
-rwxrwxrwx. 1 root root 113 12月  4 12:08 redis.conf
[root@10 ~]#
```

明细：
```
[root@10 ~]# docker run \
>   --name redis_7.2.3 \
>   -p 6379:6379 \
>   -v /user/local/docker/redis_7.2.3/conf:/usr/local/etc/redis \
>   -v /user/local/docker/redis_7.2.3/data:/data \
>   -v /etc/localtime:/etc/localtime:ro \
>   --privileged=true \
>   -d redis:7.2.3 \
>   redis-server \
>   /usr/local/etc/redis/redis.conf
89492b2b64eb830032dae75a09ee62be3a085a86cdd39c6c70f10c4aa3832a22
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                      PORTS                                       NAMES
89492b2b64eb   redis:7.2.3    "docker-entrypoint.s…"   44 seconds ago   Up 43 seconds               0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   redis_7.2.3
[root@10 ~]#
[root@10 ~]# docker logs redis_7.2.3
1:C 04 Dec 2023 12:20:16.724 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:C 04 Dec 2023 12:20:16.724 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 04 Dec 2023 12:20:16.724 * Redis version=7.2.3, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 04 Dec 2023 12:20:16.724 * Configuration loaded
1:M 04 Dec 2023 12:20:16.724 * monotonic clock: POSIX clock_gettime
1:M 04 Dec 2023 12:20:16.725 * Running mode=standalone, port=6379.
1:M 04 Dec 2023 12:20:16.725 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 04 Dec 2023 12:20:16.725 * Server initialized
1:M 04 Dec 2023 12:20:16.726 * Ready to accept connections tcp
[root@10 ~]#
```

现在就可以在客户端使用Redis了，如在命令行客户端：
```
[root@10 ~]# docker exec -it redis_7.2.3 redis-cli
127.0.0.1:6379>
127.0.0.1:6379> get name
(error) NOAUTH Authentication required.
127.0.0.1:6379>
127.0.0.1:6379> auth 123
(error) WRONGPASS invalid username-password pair or user is disabled.
127.0.0.1:6379>
127.0.0.1:6379> auth 123456
OK
127.0.0.1:6379>
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379>
127.0.0.1:6379> set name a1
OK
127.0.0.1:6379>
127.0.0.1:6379> get name
"a1"
127.0.0.1:6379>
127.0.0.1:6379>
```

### 参数配置

```
# Redis configuration file example.
#
# Note that in order to read the configuration file, Redis must be
# started with the file path as first argument:
#
# ./redis-server /path/to/redis.conf

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
#
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.

################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Note that option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#
# Included paths may contain wildcards. All files matching the wildcards will
# be included in alphabetical order.
# Note that if an include path contains a wildcards but no files match it when
# the server is started, the include statement will be ignored and no error will
# be emitted.  It is safe, therefore, to include wildcard files from empty
# directories.
#
# include /path/to/local.conf
# include /path/to/other.conf
# include /path/to/fragments/*.conf
#

################################## MODULES #####################################

# Load modules at startup. If the server is not able to load modules
# it will abort. It is possible to use multiple loadmodule directives.
#
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so

################################## NETWORK #####################################

# By default, if no "bind" configuration directive is specified, Redis listens
# for connections from all available network interfaces on the host machine.
# It is possible to listen to just one or multiple selected interfaces using
# the "bind" configuration directive, followed by one or more IP addresses.
# Each address can be prefixed by "-", which means that redis will not fail to
# start if the address is not available. Being not available only refers to
# addresses that does not correspond to any network interface. Addresses that
# are already in use will always fail, and unsupported protocols will always BE
# silently skipped.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1     # listens on two specific IPv4 addresses
# bind 127.0.0.1 ::1              # listens on loopback IPv4 and IPv6
# bind * -::*                     # like the default, all available interfaces
#
# ~~~ WARNING ~~~ If the computer running Redis is directly exposed to the
# internet, binding to all the interfaces is dangerous and will expose the
# instance to everybody on the internet. So by default we uncomment the
# following bind directive, that will force Redis to listen only on the
# IPv4 and IPv6 (if available) loopback interface addresses (this means Redis
# will only be able to accept client connections from the same host that it is
# running on).
#
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# COMMENT OUT THE FOLLOWING LINE.
#
# You will also need to set a password unless you explicitly disable protected
# mode.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
bind 127.0.0.1 -::1

# By default, outgoing connections (from replica to master, from Sentinel to
# instances, cluster bus, etc.) are not bound to a specific local address. In
# most cases, this means the operating system will handle that based on routing
# and the interface through which the connection goes out.
#
# Using bind-source-addr it is possible to configure a specific address to bind
# to, which may also affect how the connection gets routed.
#
# Example:
#
# bind-source-addr 10.0.0.1

# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and the default user has no password, the server
# only accepts local connections from the IPv4 address (127.0.0.1), IPv6 address
# (::1) or Unix domain sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured.
protected-mode yes

# Redis uses default hardened security configuration directives to reduce the
# attack surface on innocent users. Therefore, several sensitive configuration
# directives are immutable, and some potentially-dangerous commands are blocked.
#
# Configuration directives that control files that Redis writes to (e.g., 'dir'
# and 'dbfilename') and that aren't usually modified during runtime
# are protected by making them immutable.
#
# Commands that can increase the attack surface of Redis and that aren't usually
# called by users are blocked by default.
#
# These can be exposed to either all connections or just local ones by setting
# each of the configs listed below to either of these values:
#
# no    - Block for any connection (remain immutable)
# yes   - Allow for any connection (no protection)
# local - Allow only for local connections. Ones originating from the
#         IPv4 address (127.0.0.1), IPv6 address (::1) or Unix domain sockets.
#
# enable-protected-configs no
# enable-debug-command no
# enable-module-command no

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379

# TCP listen() backlog.
#
# In high requests-per-second environments you need a high backlog in order
# to avoid slow clients connection issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511

# Unix socket.
#
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /run/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Force network equipment in the middle to consider the connection to be
#    alive.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
tcp-keepalive 300

# Apply OS-specific mechanism to mark the listening socket with the specified
# ID, to support advanced routing and filtering capabilities.
#
# On Linux, the ID represents a connection mark.
# On FreeBSD, the ID represents a socket cookie ID.
# On OpenBSD, the ID represents a route table ID.
#
# The default value is 0, which implies no marking is required.
# socket-mark-id 0

################################# TLS/SSL #####################################

# By default, TLS/SSL is disabled. To enable it, the "tls-port" configuration
# directive can be used to define TLS-listening ports. To enable TLS on the
# default port, use:
#
# port 0
# tls-port 6379

# Configure a X.509 certificate and private key to use for authenticating the
# server to connected clients, masters or cluster peers.  These files should be
# PEM formatted.
#
# tls-cert-file redis.crt
# tls-key-file redis.key
#
# If the key file is encrypted using a passphrase, it can be included here
# as well.
#
# tls-key-file-pass secret

# Normally Redis uses the same certificate for both server functions (accepting
# connections) and client functions (replicating from a master, establishing
# cluster bus connections, etc.).
#
# Sometimes certificates are issued with attributes that designate them as
# client-only or server-only certificates. In that case it may be desired to use
# different certificates for incoming (server) and outgoing (client)
# connections. To do that, use the following directives:
#
# tls-client-cert-file client.crt
# tls-client-key-file client.key
#
# If the key file is encrypted using a passphrase, it can be included here
# as well.
#
# tls-client-key-file-pass secret

# Configure a DH parameters file to enable Diffie-Hellman (DH) key exchange,
# required by older versions of OpenSSL (<3.0). Newer versions do not require
# this configuration and recommend against it.
#
# tls-dh-params-file redis.dh

# Configure a CA certificate(s) bundle or directory to authenticate TLS/SSL
# clients and peers.  Redis requires an explicit configuration of at least one
# of these, and will not implicitly use the system wide configuration.
#
# tls-ca-cert-file ca.crt
# tls-ca-cert-dir /etc/ssl/certs

# By default, clients (including replica servers) on a TLS port are required
# to authenticate using valid client side certificates.
#
# If "no" is specified, client certificates are not required and not accepted.
# If "optional" is specified, client certificates are accepted and must be
# valid if provided, but are not required.
#
# tls-auth-clients no
# tls-auth-clients optional

# By default, a Redis replica does not attempt to establish a TLS connection
# with its master.
#
# Use the following directive to enable TLS on replication links.
#
# tls-replication yes

# By default, the Redis Cluster bus uses a plain TCP connection. To enable
# TLS for the bus protocol, use the following directive:
#
# tls-cluster yes

# By default, only TLSv1.2 and TLSv1.3 are enabled and it is highly recommended
# that older formally deprecated versions are kept disabled to reduce the attack surface.
# You can explicitly specify TLS versions to support.
# Allowed values are case insensitive and include "TLSv1", "TLSv1.1", "TLSv1.2",
# "TLSv1.3" (OpenSSL >= 1.1.1) or any combination.
# To enable only TLSv1.2 and TLSv1.3, use:
#
# tls-protocols "TLSv1.2 TLSv1.3"

# Configure allowed ciphers.  See the ciphers(1ssl) manpage for more information
# about the syntax of this string.
#
# Note: this configuration applies only to <= TLSv1.2.
#
# tls-ciphers DEFAULT:!MEDIUM

# Configure allowed TLSv1.3 ciphersuites.  See the ciphers(1ssl) manpage for more
# information about the syntax of this string, and specifically for TLSv1.3
# ciphersuites.
#
# tls-ciphersuites TLS_CHACHA20_POLY1305_SHA256

# When choosing a cipher, use the server's preference instead of the client
# preference. By default, the server follows the client's preference.
#
# tls-prefer-server-ciphers yes

# By default, TLS session caching is enabled to allow faster and less expensive
# reconnections by clients that support it. Use the following directive to disable
# caching.
#
# tls-session-caching no

# Change the default number of TLS sessions cached. A zero value sets the cache
# to unlimited size. The default size is 20480.
#
# tls-session-cache-size 5000

# Change the default timeout of cached TLS sessions. The default timeout is 300
# seconds.
#
# tls-session-cache-timeout 60

################################# GENERAL #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# When Redis is supervised by upstart or systemd, this parameter has no impact.
daemonize no

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#                        requires "expect stop" in your upstart job config
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#                        on startup, and updating Redis status on a regular
#                        basis.
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous pings back to your supervisor.
#
# The default is "no". To run under upstart/systemd, you can simply uncomment
# the line below:
#
# supervised auto

# If a pid file is specified, Redis writes it where specified at startup
# and removes it at exit.
#
# When the server runs non daemonized, no pid file is created if none is
# specified in the configuration. When the server is daemonized, the pid file
# is used even if not specified, defaulting to "/var/run/redis.pid".
#
# Creating a pid file is best effort: if Redis is not able to create it
# nothing bad happens, the server will start and run normally.
#
# Note that on modern Linux systems "/run/redis.pid" is more conforming
# and should be used instead.
pidfile /var/run/redis_6379.pid

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
# nothing (nothing is logged)
loglevel notice

# Specify the log file name. Also the empty string can be used to force
# Redis to log on the standard output. Note that if you use standard
# output for logging but daemonize, logs will be sent to /dev/null
logfile ""

# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no

# Specify the syslog identity.
# syslog-ident redis

# Specify the syslog facility. Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0

# To disable the built in crash log, which will possibly produce cleaner core
# dumps when they are needed, uncomment the following:
#
# crash-log-enabled no

# To disable the fast memory check that's run as part of the crash log, which
# will possibly let redis terminate sooner, uncomment the following:
#
# crash-memcheck-enabled no

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16

# By default Redis shows an ASCII art logo only when started to log to the
# standard output and if the standard output is a TTY and syslog logging is
# disabled. Basically this means that normally a logo is displayed only in
# interactive sessions.
#
# However it is possible to force the pre-4.0 behavior and always show a
# ASCII art logo in startup logs by setting the following option to yes.
always-show-logo no

# By default, Redis modifies the process title (as seen in 'top' and 'ps') to
# provide some runtime information. It is possible to disable this and leave
# the process name as executed by setting the following to no.
set-proc-title yes

# When changing the process title, Redis uses the following template to construct
# the modified title.
#
# Template variables are specified in curly brackets. The following variables are
# supported:
#
# {title}           Name of process as executed if parent, or type of child process.
# {listen-addr}     Bind address or '*' followed by TCP or TLS port listening on, or
#                   Unix socket if only that's available.
# {server-mode}     Special mode, i.e. "[sentinel]" or "[cluster]".
# {port}            TCP port listening on, or 0.
# {tls-port}        TLS port listening on, or 0.
# {unixsocket}      Unix domain socket listening on, or "".
# {config-file}     Name of configuration file used.
#
proc-title-template "{title} {listen-addr} {server-mode}"

# Set the local environment which is used for string comparison operations, and 
# also affect the performance of Lua scripts. Empty String indicates the locale 
# is derived from the environment variables.
locale-collate ""

################################ SNAPSHOTTING  ################################

# Save the DB to disk.
#
# save <seconds> <changes> [<seconds> <changes> ...]
#
# Redis will save the DB if the given number of seconds elapsed and it
# surpassed the given number of write operations against the DB.
#
# Snapshotting can be completely disabled with a single empty string argument
# as in following example:
#
# save ""
#
# Unless specified otherwise, by default Redis will save the DB:
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed
#
# You can set these explicitly by uncommenting the following line.
#
# save 3600 1 300 100 60 10000

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.
stop-writes-on-bgsave-error yes

# Compress string objects using LZF when dump .rdb databases?
# By default compression is enabled as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
rdbcompression yes

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
rdbchecksum yes

# Enables or disables full sanitization checks for ziplist and listpack etc when
# loading an RDB or RESTORE payload. This reduces the chances of a assertion or
# crash later on while processing commands.
# Options:
#   no         - Never perform full sanitization
#   yes        - Always perform full sanitization
#   clients    - Perform full sanitization only for user connections.
#                Excludes: RDB files, RESTORE commands received from the master
#                connection, and client connections which have the
#                skip-sanitize-payload ACL flag.
# The default should be 'clients' but since it currently affects cluster
# resharding via MIGRATE, it is temporarily set to 'no' by default.
#
# sanitize-dump-payload no

# The filename where to dump the DB
dbfilename dump.rdb

# Remove RDB files used by replication in instances without persistence
# enabled. By default this option is disabled, however there are environments
# where for regulations or other security concerns, RDB files persisted on
# disk by masters in order to feed replicas, or stored on disk by replicas
# in order to load them for the initial synchronization, should be deleted
# ASAP. Note that this option ONLY WORKS in instances that have both AOF
# and RDB persistence disabled, otherwise is completely ignored.
#
# An alternative (and sometimes better) way to obtain the same effect is
# to use diskless replication on both master and replicas instances. However
# in the case of replicas, diskless is not always an option.
rdb-del-sync-files no

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir ./

################################# REPLICATION #################################

# Master-Replica replication. Use replicaof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
#   +------------------+      +---------------+
#   |      Master      | ---> |    Replica    |
#   | (receive writes) |      |  (exact copy) |
#   +------------------+      +---------------+
#
# 1) Redis replication is asynchronous, but you can configure a master to
#    stop accepting writes if it appears to be not connected with at least
#    a given number of replicas.
# 2) Redis replicas are able to perform a partial resynchronization with the
#    master if the replication link is lost for a relatively small amount of
#    time. You may want to configure the replication backlog size (see the next
#    sections of this file) with a sensible value depending on your needs.
# 3) Replication is automatic and does not need user intervention. After a
#    network partition replicas automatically try to reconnect to masters
#    and resynchronize with them.
#
# replicaof <masterip> <masterport>

# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the replica to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the replica request.
#
# masterauth <master-password>
#
# However this is not enough if you are using Redis ACLs (for Redis version
# 6 or greater), and the default user is not capable of running the PSYNC
# command and/or other commands needed for replication. In this case it's
# better to configure a special user to use with replication, and specify the
# masteruser configuration as such:
#
# masteruser <username>
#
# When masteruser is specified, the replica will authenticate against its
# master using the new AUTH form: AUTH <username> <password>.

# When a replica loses its connection with the master, or when the replication
# is still in progress, the replica can act in two different ways:
#
# 1) if replica-serve-stale-data is set to 'yes' (the default) the replica will
#    still reply to client requests, possibly with out of date data, or the
#    data set may just be empty if this is the first synchronization.
#
# 2) If replica-serve-stale-data is set to 'no' the replica will reply with error
#    "MASTERDOWN Link with MASTER is down and replica-serve-stale-data is set to 'no'"
#    to all data access commands, excluding commands such as:
#    INFO, REPLICAOF, AUTH, SHUTDOWN, REPLCONF, ROLE, CONFIG, SUBSCRIBE,
#    UNSUBSCRIBE, PSUBSCRIBE, PUNSUBSCRIBE, PUBLISH, PUBSUB, COMMAND, POST,
#    HOST and LATENCY.
#
replica-serve-stale-data yes

# You can configure a replica instance to accept writes or not. Writing against
# a replica instance may be useful to store some ephemeral data (because data
# written on a replica will be easily deleted after resync with the master) but
# may also cause problems if clients are writing to it because of a
# misconfiguration.
#
# Since Redis 2.6 by default replicas are read-only.
#
# Note: read only replicas are not designed to be exposed to untrusted clients
# on the internet. It's just a protection layer against misuse of the instance.
# Still a read only replica exports by default all the administrative commands
# such as CONFIG, DEBUG, and so forth. To a limited extent you can improve
# security of read only replicas using 'rename-command' to shadow all the
# administrative / dangerous commands.
replica-read-only yes

# Replication SYNC strategy: disk or socket.
#
# New replicas and reconnecting replicas that are not able to continue the
# replication process just receiving differences, need to do what is called a
# "full synchronization". An RDB file is transmitted from the master to the
# replicas.
#
# The transmission can happen in two different ways:
#
# 1) Disk-backed: The Redis master creates a new process that writes the RDB
#                 file on disk. Later the file is transferred by the parent
#                 process to the replicas incrementally.
# 2) Diskless: The Redis master creates a new process that directly writes the
#              RDB file to replica sockets, without touching the disk at all.
#
# With disk-backed replication, while the RDB file is generated, more replicas
# can be queued and served with the RDB file as soon as the current child
# producing the RDB file finishes its work. With diskless replication instead
# once the transfer starts, new replicas arriving will be queued and a new
# transfer will start when the current one terminates.
#
# When diskless replication is used, the master waits a configurable amount of
# time (in seconds) before starting the transfer in the hope that multiple
# replicas will arrive and the transfer can be parallelized.
#
# With slow disks and fast (large bandwidth) networks, diskless replication
# works better.
repl-diskless-sync yes

# When diskless replication is enabled, it is possible to configure the delay
# the server waits in order to spawn the child that transfers the RDB via socket
# to the replicas.
#
# This is important since once the transfer starts, it is not possible to serve
# new replicas arriving, that will be queued for the next RDB transfer, so the
# server waits a delay in order to let more replicas arrive.
#
# The delay is specified in seconds, and by default is 5 seconds. To disable
# it entirely just set it to 0 seconds and the transfer will start ASAP.
repl-diskless-sync-delay 5

# When diskless replication is enabled with a delay, it is possible to let
# the replication start before the maximum delay is reached if the maximum
# number of replicas expected have connected. Default of 0 means that the
# maximum is not defined and Redis will wait the full delay.
repl-diskless-sync-max-replicas 0

# -----------------------------------------------------------------------------
# WARNING: Since in this setup the replica does not immediately store an RDB on
# disk, it may cause data loss during failovers. RDB diskless load + Redis
# modules not handling I/O reads may cause Redis to abort in case of I/O errors
# during the initial synchronization stage with the master.
# -----------------------------------------------------------------------------
#
# Replica can load the RDB it reads from the replication link directly from the
# socket, or store the RDB to a file and read that file after it was completely
# received from the master.
#
# In many cases the disk is slower than the network, and storing and loading
# the RDB file may increase replication time (and even increase the master's
# Copy on Write memory and replica buffers).
# However, when parsing the RDB file directly from the socket, in order to avoid
# data loss it's only safe to flush the current dataset when the new dataset is
# fully loaded in memory, resulting in higher memory usage.
# For this reason we have the following options:
#
# "disabled"    - Don't use diskless load (store the rdb file to the disk first)
# "swapdb"      - Keep current db contents in RAM while parsing the data directly
#                 from the socket. Replicas in this mode can keep serving current
#                 dataset while replication is in progress, except for cases where
#                 they can't recognize master as having a data set from same
#                 replication history.
#                 Note that this requires sufficient memory, if you don't have it,
#                 you risk an OOM kill.
# "on-empty-db" - Use diskless load only when current dataset is empty. This is 
#                 safer and avoid having old and new dataset loaded side by side
#                 during replication.
repl-diskless-load disabled

# Master send PINGs to its replicas in a predefined interval. It's possible to
# change this interval with the repl_ping_replica_period option. The default
# value is 10 seconds.
#
# repl-ping-replica-period 10

# The following option sets the replication timeout for:
#
# 1) Bulk transfer I/O during SYNC, from the point of view of replica.
# 2) Master timeout from the point of view of replicas (data, pings).
# 3) Replica timeout from the point of view of masters (REPLCONF ACK pings).
#
# It is important to make sure that this value is greater than the value
# specified for repl-ping-replica-period otherwise a timeout will be detected
# every time there is low traffic between the master and the replica. The default
# value is 60 seconds.
#
# repl-timeout 60

# Disable TCP_NODELAY on the replica socket after SYNC?
#
# If you select "yes" Redis will use a smaller number of TCP packets and
# less bandwidth to send data to replicas. But this can add a delay for
# the data to appear on the replica side, up to 40 milliseconds with
# Linux kernels using a default configuration.
#
# If you select "no" the delay for data to appear on the replica side will
# be reduced but more bandwidth will be used for replication.
#
# By default we optimize for low latency, but in very high traffic conditions
# or when the master and replicas are many hops away, turning this to "yes" may
# be a good idea.
repl-disable-tcp-nodelay no

# Set the replication backlog size. The backlog is a buffer that accumulates
# replica data when replicas are disconnected for some time, so that when a
# replica wants to reconnect again, often a full resync is not needed, but a
# partial resync is enough, just passing the portion of data the replica
# missed while disconnected.
#
# The bigger the replication backlog, the longer the replica can endure the
# disconnect and later be able to perform a partial resynchronization.
#
# The backlog is only allocated if there is at least one replica connected.
#
# repl-backlog-size 1mb

# After a master has no connected replicas for some time, the backlog will be
# freed. The following option configures the amount of seconds that need to
# elapse, starting from the time the last replica disconnected, for the backlog
# buffer to be freed.
#
# Note that replicas never free the backlog for timeout, since they may be
# promoted to masters later, and should be able to correctly "partially
# resynchronize" with other replicas: hence they should always accumulate backlog.
#
# A value of 0 means to never release the backlog.
#
# repl-backlog-ttl 3600

# The replica priority is an integer number published by Redis in the INFO
# output. It is used by Redis Sentinel in order to select a replica to promote
# into a master if the master is no longer working correctly.
#
# A replica with a low priority number is considered better for promotion, so
# for instance if there are three replicas with priority 10, 100, 25 Sentinel
# will pick the one with priority 10, that is the lowest.
#
# However a special priority of 0 marks the replica as not able to perform the
# role of master, so a replica with priority of 0 will never be selected by
# Redis Sentinel for promotion.
#
# By default the priority is 100.
replica-priority 100

# The propagation error behavior controls how Redis will behave when it is
# unable to handle a command being processed in the replication stream from a master
# or processed while reading from an AOF file. Errors that occur during propagation
# are unexpected, and can cause data inconsistency. However, there are edge cases
# in earlier versions of Redis where it was possible for the server to replicate or persist
# commands that would fail on future versions. For this reason the default behavior
# is to ignore such errors and continue processing commands.
#
# If an application wants to ensure there is no data divergence, this configuration
# should be set to 'panic' instead. The value can also be set to 'panic-on-replicas'
# to only panic when a replica encounters an error on the replication stream. One of
# these two panic values will become the default value in the future once there are
# sufficient safety mechanisms in place to prevent false positive crashes.
#
# propagation-error-behavior ignore

# Replica ignore disk write errors controls the behavior of a replica when it is
# unable to persist a write command received from its master to disk. By default,
# this configuration is set to 'no' and will crash the replica in this condition.
# It is not recommended to change this default, however in order to be compatible
# with older versions of Redis this config can be toggled to 'yes' which will just
# log a warning and execute the write command it got from the master.
#
# replica-ignore-disk-write-errors no

# -----------------------------------------------------------------------------
# By default, Redis Sentinel includes all replicas in its reports. A replica
# can be excluded from Redis Sentinel's announcements. An unannounced replica
# will be ignored by the 'sentinel replicas <master>' command and won't be
# exposed to Redis Sentinel's clients.
#
# This option does not change the behavior of replica-priority. Even with
# replica-announced set to 'no', the replica can be promoted to master. To
# prevent this behavior, set replica-priority to 0.
#
# replica-announced yes

# It is possible for a master to stop accepting writes if there are less than
# N replicas connected, having a lag less or equal than M seconds.
#
# The N replicas need to be in "online" state.
#
# The lag in seconds, that must be <= the specified value, is calculated from
# the last ping received from the replica, that is usually sent every second.
#
# This option does not GUARANTEE that N replicas will accept the write, but
# will limit the window of exposure for lost writes in case not enough replicas
# are available, to the specified number of seconds.
#
# For example to require at least 3 replicas with a lag <= 10 seconds use:
#
# min-replicas-to-write 3
# min-replicas-max-lag 10
#
# Setting one or the other to 0 disables the feature.
#
# By default min-replicas-to-write is set to 0 (feature disabled) and
# min-replicas-max-lag is set to 10.

# A Redis master is able to list the address and port of the attached
# replicas in different ways. For example the "INFO replication" section
# offers this information, which is used, among other tools, by
# Redis Sentinel in order to discover replica instances.
# Another place where this info is available is in the output of the
# "ROLE" command of a master.
#
# The listed IP address and port normally reported by a replica is
# obtained in the following way:
#
#   IP: The address is auto detected by checking the peer address
#   of the socket used by the replica to connect with the master.
#
#   Port: The port is communicated by the replica during the replication
#   handshake, and is normally the port that the replica is using to
#   listen for connections.
#
# However when port forwarding or Network Address Translation (NAT) is
# used, the replica may actually be reachable via different IP and port
# pairs. The following two options can be used by a replica in order to
# report to its master a specific set of IP and port, so that both INFO
# and ROLE will report those values.
#
# There is no need to use both the options if you need to override just
# the port or the IP address.
#
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234

############################### KEYS TRACKING #################################

# Redis implements server assisted support for client side caching of values.
# This is implemented using an invalidation table that remembers, using
# a radix key indexed by key name, what clients have which keys. In turn
# this is used in order to send invalidation messages to clients. Please
# check this page to understand more about the feature:
#
#   https://redis.io/topics/client-side-caching
#
# When tracking is enabled for a client, all the read only queries are assumed
# to be cached: this will force Redis to store information in the invalidation
# table. When keys are modified, such information is flushed away, and
# invalidation messages are sent to the clients. However if the workload is
# heavily dominated by reads, Redis could use more and more memory in order
# to track the keys fetched by many clients.
#
# For this reason it is possible to configure a maximum fill value for the
# invalidation table. By default it is set to 1M of keys, and once this limit
# is reached, Redis will start to evict keys in the invalidation table
# even if they were not modified, just to reclaim memory: this will in turn
# force the clients to invalidate the cached values. Basically the table
# maximum size is a trade off between the memory you want to spend server
# side to track information about who cached what, and the ability of clients
# to retain cached objects in memory.
#
# If you set the value to 0, it means there are no limits, and Redis will
# retain as many keys as needed in the invalidation table.
# In the "stats" INFO section, you can find information about the number of
# keys in the invalidation table at every given moment.
#
# Note: when key tracking is used in broadcasting mode, no memory is used
# in the server side so this setting is useless.
#
# tracking-table-max-keys 1000000

################################## SECURITY ###################################

# Warning: since Redis is pretty fast, an outside user can try up to
# 1 million passwords per second against a modern box. This means that you
# should use very strong passwords, otherwise they will be very easy to break.
# Note that because the password is really a shared secret between the client
# and the server, and should not be memorized by any human, the password
# can be easily a long string from /dev/urandom or whatever, so by using a
# long and unguessable password no brute force attack will be possible.

# Redis ACL users are defined in the following format:
#
#   user <username> ... acl rules ...
#
# For example:
#
#   user worker +@list +@connection ~jobs:* on >ffa9203c493aa99
#
# The special username "default" is used for new connections. If this user
# has the "nopass" rule, then new connections will be immediately authenticated
# as the "default" user without the need of any password provided via the
# AUTH command. Otherwise if the "default" user is not flagged with "nopass"
# the connections will start in not authenticated state, and will require
# AUTH (or the HELLO command AUTH option) in order to be authenticated and
# start to work.
#
# The ACL rules that describe what a user can do are the following:
#
#  on           Enable the user: it is possible to authenticate as this user.
#  off          Disable the user: it's no longer possible to authenticate
#               with this user, however the already authenticated connections
#               will still work.
#  skip-sanitize-payload    RESTORE dump-payload sanitization is skipped.
#  sanitize-payload         RESTORE dump-payload is sanitized (default).
#  +<command>   Allow the execution of that command.
#               May be used with `|` for allowing subcommands (e.g "+config|get")
#  -<command>   Disallow the execution of that command.
#               May be used with `|` for blocking subcommands (e.g "-config|set")
#  +@<category> Allow the execution of all the commands in such category
#               with valid categories are like @admin, @set, @sortedset, ...
#               and so forth, see the full list in the server.c file where
#               the Redis command table is described and defined.
#               The special category @all means all the commands, but currently
#               present in the server, and that will be loaded in the future
#               via modules.
#  +<command>|first-arg  Allow a specific first argument of an otherwise
#                        disabled command. It is only supported on commands with
#                        no sub-commands, and is not allowed as negative form
#                        like -SELECT|1, only additive starting with "+". This
#                        feature is deprecated and may be removed in the future.
#  allcommands  Alias for +@all. Note that it implies the ability to execute
#               all the future commands loaded via the modules system.
#  nocommands   Alias for -@all.
#  ~<pattern>   Add a pattern of keys that can be mentioned as part of
#               commands. For instance ~* allows all the keys. The pattern
#               is a glob-style pattern like the one of KEYS.
#               It is possible to specify multiple patterns.
# %R~<pattern>  Add key read pattern that specifies which keys can be read 
#               from.
# %W~<pattern>  Add key write pattern that specifies which keys can be
#               written to. 
#  allkeys      Alias for ~*
#  resetkeys    Flush the list of allowed keys patterns.
#  &<pattern>   Add a glob-style pattern of Pub/Sub channels that can be
#               accessed by the user. It is possible to specify multiple channel
#               patterns.
#  allchannels  Alias for &*
#  resetchannels            Flush the list of allowed channel patterns.
#  ><password>  Add this password to the list of valid password for the user.
#               For example >mypass will add "mypass" to the list.
#               This directive clears the "nopass" flag (see later).
#  <<password>  Remove this password from the list of valid passwords.
#  nopass       All the set passwords of the user are removed, and the user
#               is flagged as requiring no password: it means that every
#               password will work against this user. If this directive is
#               used for the default user, every new connection will be
#               immediately authenticated with the default user without
#               any explicit AUTH command required. Note that the "resetpass"
#               directive will clear this condition.
#  resetpass    Flush the list of allowed passwords. Moreover removes the
#               "nopass" status. After "resetpass" the user has no associated
#               passwords and there is no way to authenticate without adding
#               some password (or setting it as "nopass" later).
#  reset        Performs the following actions: resetpass, resetkeys, resetchannels,
#               allchannels (if acl-pubsub-default is set), off, clearselectors, -@all.
#               The user returns to the same state it has immediately after its creation.
# (<options>)   Create a new selector with the options specified within the
#               parentheses and attach it to the user. Each option should be 
#               space separated. The first character must be ( and the last 
#               character must be ).
# clearselectors            Remove all of the currently attached selectors. 
#                           Note this does not change the "root" user permissions,
#                           which are the permissions directly applied onto the
#                           user (outside the parentheses).
#
# ACL rules can be specified in any order: for instance you can start with
# passwords, then flags, or key patterns. However note that the additive
# and subtractive rules will CHANGE MEANING depending on the ordering.
# For instance see the following example:
#
#   user alice on +@all -DEBUG ~* >somepassword
#
# This will allow "alice" to use all the commands with the exception of the
# DEBUG command, since +@all added all the commands to the set of the commands
# alice can use, and later DEBUG was removed. However if we invert the order
# of two ACL rules the result will be different:
#
#   user alice on -DEBUG +@all ~* >somepassword
#
# Now DEBUG was removed when alice had yet no commands in the set of allowed
# commands, later all the commands are added, so the user will be able to
# execute everything.
#
# Basically ACL rules are processed left-to-right.
#
# The following is a list of command categories and their meanings:
# * keyspace - Writing or reading from keys, databases, or their metadata 
#     in a type agnostic way. Includes DEL, RESTORE, DUMP, RENAME, EXISTS, DBSIZE,
#     KEYS, EXPIRE, TTL, FLUSHALL, etc. Commands that may modify the keyspace,
#     key or metadata will also have `write` category. Commands that only read
#     the keyspace, key or metadata will have the `read` category.
# * read - Reading from keys (values or metadata). Note that commands that don't
#     interact with keys, will not have either `read` or `write`.
# * write - Writing to keys (values or metadata)
# * admin - Administrative commands. Normal applications will never need to use
#     these. Includes REPLICAOF, CONFIG, DEBUG, SAVE, MONITOR, ACL, SHUTDOWN, etc.
# * dangerous - Potentially dangerous (each should be considered with care for
#     various reasons). This includes FLUSHALL, MIGRATE, RESTORE, SORT, KEYS,
#     CLIENT, DEBUG, INFO, CONFIG, SAVE, REPLICAOF, etc.
# * connection - Commands affecting the connection or other connections.
#     This includes AUTH, SELECT, COMMAND, CLIENT, ECHO, PING, etc.
# * blocking - Potentially blocking the connection until released by another
#     command.
# * fast - Fast O(1) commands. May loop on the number of arguments, but not the
#     number of elements in the key.
# * slow - All commands that are not Fast.
# * pubsub - PUBLISH / SUBSCRIBE related
# * transaction - WATCH / MULTI / EXEC related commands.
# * scripting - Scripting related.
# * set - Data type: sets related.
# * sortedset - Data type: zsets related.
# * list - Data type: lists related.
# * hash - Data type: hashes related.
# * string - Data type: strings related.
# * bitmap - Data type: bitmaps related.
# * hyperloglog - Data type: hyperloglog related.
# * geo - Data type: geo related.
# * stream - Data type: streams related.
#
# For more information about ACL configuration please refer to
# the Redis web site at https://redis.io/topics/acl

# ACL LOG
#
# The ACL Log tracks failed commands and authentication events associated
# with ACLs. The ACL Log is useful to troubleshoot failed commands blocked
# by ACLs. The ACL Log is stored in memory. You can reclaim memory with
# ACL LOG RESET. Define the maximum entry length of the ACL Log below.
acllog-max-len 128

# Using an external ACL file
#
# Instead of configuring users here in this file, it is possible to use
# a stand-alone file just listing users. The two methods cannot be mixed:
# if you configure users here and at the same time you activate the external
# ACL file, the server will refuse to start.
#
# The format of the external ACL user file is exactly the same as the
# format that is used inside redis.conf to describe users.
#
# aclfile /etc/redis/users.acl

# IMPORTANT NOTE: starting with Redis 6 "requirepass" is just a compatibility
# layer on top of the new ACL system. The option effect will be just setting
# the password for the default user. Clients will still authenticate using
# AUTH <password> as usually, or more explicitly with AUTH default <password>
# if they follow the new protocol: both will work.
#
# The requirepass is not compatible with aclfile option and the ACL LOAD
# command, these will cause requirepass to be ignored.
#
# requirepass foobared

# New users are initialized with restrictive permissions by default, via the
# equivalent of this ACL rule 'off resetkeys -@all'. Starting with Redis 6.2, it
# is possible to manage access to Pub/Sub channels with ACL rules as well. The
# default Pub/Sub channels permission if new users is controlled by the
# acl-pubsub-default configuration directive, which accepts one of these values:
#
# allchannels: grants access to all Pub/Sub channels
# resetchannels: revokes access to all Pub/Sub channels
#
# From Redis 7.0, acl-pubsub-default defaults to 'resetchannels' permission.
#
# acl-pubsub-default resetchannels

# Command renaming (DEPRECATED).
#
# ------------------------------------------------------------------------
# WARNING: avoid using this option if possible. Instead use ACLs to remove
# commands from the default user, and put them only in some admin user you
# create for administrative purposes.
# ------------------------------------------------------------------------
#
# It is possible to change the name of dangerous commands in a shared
# environment. For instance the CONFIG command may be renamed into something
# hard to guess so that it will still be available for internal-use tools
# but not available for general clients.
#
# Example:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
# rename-command CONFIG ""
#
# Please note that changing the name of commands that are logged into the
# AOF file or transmitted to replicas may cause problems.

################################### CLIENTS ####################################

# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
#
# IMPORTANT: When Redis Cluster is used, the max number of connections is also
# shared with the cluster bus: every node in the cluster will use two
# connections, one incoming and another outgoing. It is important to size the
# limit accordingly in case of very large clusters.
#
# maxclients 10000

############################## MEMORY MANAGEMENT ################################

# Set a memory usage limit to the specified amount of bytes.
# When the memory limit is reached Redis will try to remove keys
# according to the eviction policy selected (see maxmemory-policy).
#
# If Redis can't remove keys according to the policy, or if the policy is
# set to 'noeviction', Redis will start to reply with errors to commands
# that would use more memory, like SET, LPUSH, and so on, and will continue
# to reply to read-only commands like GET.
#
# This option is usually useful when using Redis as an LRU or LFU cache, or to
# set a hard memory limit for an instance (using the 'noeviction' policy).
#
# WARNING: If you have replicas attached to an instance with maxmemory on,
# the size of the output buffers needed to feed the replicas are subtracted
# from the used memory count, so that network problems / resyncs will
# not trigger a loop where keys are evicted, and in turn the output
# buffer of replicas is full with DELs of keys evicted triggering the deletion
# of more keys, and so forth until the database is completely emptied.
#
# In short... if you have replicas attached it is suggested that you set a lower
# limit for maxmemory so that there is some free RAM on the system for replica
# output buffers (but this is not needed if the policy is 'noeviction').
#
# maxmemory <bytes>

# MAXMEMORY POLICY: how Redis will select what to remove when maxmemory
# is reached. You can select one from the following behaviors:
#
# volatile-lru -> Evict using approximated LRU, only keys with an expire set.
# allkeys-lru -> Evict any key using approximated LRU.
# volatile-lfu -> Evict using approximated LFU, only keys with an expire set.
# allkeys-lfu -> Evict any key using approximated LFU.
# volatile-random -> Remove a random key having an expire set.
# allkeys-random -> Remove a random key, any key.
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
# noeviction -> Don't evict anything, just return an error on write operations.
#
# LRU means Least Recently Used
# LFU means Least Frequently Used
#
# Both LRU, LFU and volatile-ttl are implemented using approximated
# randomized algorithms.
#
# Note: with any of the above policies, when there are no suitable keys for
# eviction, Redis will return an error on write operations that require
# more memory. These are usually commands that create new keys, add data or
# modify existing keys. A few examples are: SET, INCR, HSET, LPUSH, SUNIONSTORE,
# SORT (due to the STORE argument), and EXEC (if the transaction includes any
# command that requires memory).
#
# The default is:
#
# maxmemory-policy noeviction

# LRU, LFU and minimal TTL algorithms are not precise algorithms but approximated
# algorithms (in order to save memory), so you can tune it for speed or
# accuracy. By default Redis will check five keys and pick the one that was
# used least recently, you can change the sample size using the following
# configuration directive.
#
# The default of 5 produces good enough results. 10 Approximates very closely
# true LRU but costs more CPU. 3 is faster but not very accurate.
#
# maxmemory-samples 5

# Eviction processing is designed to function well with the default setting.
# If there is an unusually large amount of write traffic, this value may need to
# be increased.  Decreasing this value may reduce latency at the risk of
# eviction processing effectiveness
#   0 = minimum latency, 10 = default, 100 = process without regard to latency
#
# maxmemory-eviction-tenacity 10

# Starting from Redis 5, by default a replica will ignore its maxmemory setting
# (unless it is promoted to master after a failover or manually). It means
# that the eviction of keys will be just handled by the master, sending the
# DEL commands to the replica as keys evict in the master side.
#
# This behavior ensures that masters and replicas stay consistent, and is usually
# what you want, however if your replica is writable, or you want the replica
# to have a different memory setting, and you are sure all the writes performed
# to the replica are idempotent, then you may change this default (but be sure
# to understand what you are doing).
#
# Note that since the replica by default does not evict, it may end using more
# memory than the one set via maxmemory (there are certain buffers that may
# be larger on the replica, or data structures may sometimes take more memory
# and so forth). So make sure you monitor your replicas and make sure they
# have enough memory to never hit a real out-of-memory condition before the
# master hits the configured maxmemory setting.
#
# replica-ignore-maxmemory yes

# Redis reclaims expired keys in two ways: upon access when those keys are
# found to be expired, and also in background, in what is called the
# "active expire key". The key space is slowly and interactively scanned
# looking for expired keys to reclaim, so that it is possible to free memory
# of keys that are expired and will never be accessed again in a short time.
#
# The default effort of the expire cycle will try to avoid having more than
# ten percent of expired keys still in memory, and will try to avoid consuming
# more than 25% of total memory and to add latency to the system. However
# it is possible to increase the expire "effort" that is normally set to
# "1", to a greater value, up to the value "10". At its maximum value the
# system will use more CPU, longer cycles (and technically may introduce
# more latency), and will tolerate less already expired keys still present
# in the system. It's a tradeoff between memory, CPU and latency.
#
# active-expire-effort 1

############################# LAZY FREEING ####################################

# Redis has two primitives to delete keys. One is called DEL and is a blocking
# deletion of the object. It means that the server stops processing new commands
# in order to reclaim all the memory associated with an object in a synchronous
# way. If the key deleted is associated with a small object, the time needed
# in order to execute the DEL command is very small and comparable to most other
# O(1) or O(log_N) commands in Redis. However if the key is associated with an
# aggregated value containing millions of elements, the server can block for
# a long time (even seconds) in order to complete the operation.
#
# For the above reasons Redis also offers non blocking deletion primitives
# such as UNLINK (non blocking DEL) and the ASYNC option of FLUSHALL and
# FLUSHDB commands, in order to reclaim memory in background. Those commands
# are executed in constant time. Another thread will incrementally free the
# object in the background as fast as possible.
#
# DEL, UNLINK and ASYNC option of FLUSHALL and FLUSHDB are user-controlled.
# It's up to the design of the application to understand when it is a good
# idea to use one or the other. However the Redis server sometimes has to
# delete keys or flush the whole database as a side effect of other operations.
# Specifically Redis deletes objects independently of a user call in the
# following scenarios:
#
# 1) On eviction, because of the maxmemory and maxmemory policy configurations,
#    in order to make room for new data, without going over the specified
#    memory limit.
# 2) Because of expire: when a key with an associated time to live (see the
#    EXPIRE command) must be deleted from memory.
# 3) Because of a side effect of a command that stores data on a key that may
#    already exist. For example the RENAME command may delete the old key
#    content when it is replaced with another one. Similarly SUNIONSTORE
#    or SORT with STORE option may delete existing keys. The SET command
#    itself removes any old content of the specified key in order to replace
#    it with the specified string.
# 4) During replication, when a replica performs a full resynchronization with
#    its master, the content of the whole database is removed in order to
#    load the RDB file just transferred.
#
# In all the above cases the default is to delete objects in a blocking way,
# like if DEL was called. However you can configure each case specifically
# in order to instead release memory in a non-blocking way like if UNLINK
# was called, using the following configuration directives.

lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no

# It is also possible, for the case when to replace the user code DEL calls
# with UNLINK calls is not easy, to modify the default behavior of the DEL
# command to act exactly like UNLINK, using the following configuration
# directive:

lazyfree-lazy-user-del no

# FLUSHDB, FLUSHALL, SCRIPT FLUSH and FUNCTION FLUSH support both asynchronous and synchronous
# deletion, which can be controlled by passing the [SYNC|ASYNC] flags into the
# commands. When neither flag is passed, this directive will be used to determine
# if the data should be deleted asynchronously.

lazyfree-lazy-user-flush no

################################ THREADED I/O #################################

# Redis is mostly single threaded, however there are certain threaded
# operations such as UNLINK, slow I/O accesses and other things that are
# performed on side threads.
#
# Now it is also possible to handle Redis clients socket reads and writes
# in different I/O threads. Since especially writing is so slow, normally
# Redis users use pipelining in order to speed up the Redis performances per
# core, and spawn multiple instances in order to scale more. Using I/O
# threads it is possible to easily speedup two times Redis without resorting
# to pipelining nor sharding of the instance.
#
# By default threading is disabled, we suggest enabling it only in machines
# that have at least 4 or more cores, leaving at least one spare core.
# Using more than 8 threads is unlikely to help much. We also recommend using
# threaded I/O only if you actually have performance problems, with Redis
# instances being able to use a quite big percentage of CPU time, otherwise
# there is no point in using this feature.
#
# So for instance if you have a four cores boxes, try to use 2 or 3 I/O
# threads, if you have a 8 cores, try to use 6 threads. In order to
# enable I/O threads use the following configuration directive:
#
# io-threads 4
#
# Setting io-threads to 1 will just use the main thread as usual.
# When I/O threads are enabled, we only use threads for writes, that is
# to thread the write(2) syscall and transfer the client buffers to the
# socket. However it is also possible to enable threading of reads and
# protocol parsing using the following configuration directive, by setting
# it to yes:
#
# io-threads-do-reads no
#
# Usually threading reads doesn't help much.
#
# NOTE 1: This configuration directive cannot be changed at runtime via
# CONFIG SET. Also, this feature currently does not work when SSL is
# enabled.
#
# NOTE 2: If you want to test the Redis speedup using redis-benchmark, make
# sure you also run the benchmark itself in threaded mode, using the
# --threads option to match the number of Redis threads, otherwise you'll not
# be able to notice the improvements.

############################ KERNEL OOM CONTROL ##############################

# On Linux, it is possible to hint the kernel OOM killer on what processes
# should be killed first when out of memory.
#
# Enabling this feature makes Redis actively control the oom_score_adj value
# for all its processes, depending on their role. The default scores will
# attempt to have background child processes killed before all others, and
# replicas killed before masters.
#
# Redis supports these options:
#
# no:       Don't make changes to oom-score-adj (default).
# yes:      Alias to "relative" see below.
# absolute: Values in oom-score-adj-values are written as is to the kernel.
# relative: Values are used relative to the initial value of oom_score_adj when
#           the server starts and are then clamped to a range of -1000 to 1000.
#           Because typically the initial value is 0, they will often match the
#           absolute values.
oom-score-adj no

# When oom-score-adj is used, this directive controls the specific values used
# for master, replica and background child processes. Values range -2000 to
# 2000 (higher means more likely to be killed).
#
# Unprivileged processes (not root, and without CAP_SYS_RESOURCE capabilities)
# can freely increase their value, but not decrease it below its initial
# settings. This means that setting oom-score-adj to "relative" and setting the
# oom-score-adj-values to positive values will always succeed.
oom-score-adj-values 0 200 800


#################### KERNEL transparent hugepage CONTROL ######################

# Usually the kernel Transparent Huge Pages control is set to "madvise" or
# or "never" by default (/sys/kernel/mm/transparent_hugepage/enabled), in which
# case this config has no effect. On systems in which it is set to "always",
# redis will attempt to disable it specifically for the redis process in order
# to avoid latency problems specifically with fork(2) and CoW.
# If for some reason you prefer to keep it enabled, you can set this config to
# "no" and the kernel global to "always".

disable-thp yes

############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Note that changing this value in a config file of an existing database and
# restarting the server can lead to data loss. A conversion needs to be done
# by setting it via CONFIG command on a live server first.
#
# Please check https://redis.io/topics/persistence for more information.

appendonly no

# The base name of the append only file.
#
# Redis 7 and newer use a set of append-only files to persist the dataset
# and changes applied to it. There are two basic types of files in use:
#
# - Base files, which are a snapshot representing the complete state of the
#   dataset at the time the file was created. Base files can be either in
#   the form of RDB (binary serialized) or AOF (textual commands).
# - Incremental files, which contain additional commands that were applied
#   to the dataset following the previous file.
#
# In addition, manifest files are used to track the files and the order in
# which they were created and should be applied.
#
# Append-only file names are created by Redis following a specific pattern.
# The file name's prefix is based on the 'appendfilename' configuration
# parameter, followed by additional information about the sequence and type.
#
# For example, if appendfilename is set to appendonly.aof, the following file
# names could be derived:
#
# - appendonly.aof.1.base.rdb as a base file.
# - appendonly.aof.1.incr.aof, appendonly.aof.2.incr.aof as incremental files.
# - appendonly.aof.manifest as a manifest file.

appendfilename "appendonly.aof"

# For convenience, Redis stores all persistent append-only files in a dedicated
# directory. The name of the directory is determined by the appenddirname
# configuration parameter.

appenddirname "appendonlydir"

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# appendfsync always
appendfsync everysec
# appendfsync no

# When the AOF fsync policy is set to always or everysec, and a background
# saving process (a background save or AOF log background rewriting) is
# performing a lot of I/O against the disk, in some Linux configurations
# Redis may block too long on the fsync() call. Note that there is no fix for
# this currently, as even performing fsync in a different thread will block
# our synchronous write(2) call.
#
# In order to mitigate this problem it's possible to use the following option
# that will prevent fsync() from being called in the main process while a
# BGSAVE or BGREWRITEAOF is in progress.
#
# This means that while another child is saving, the durability of Redis is
# the same as "appendfsync no". In practical terms, this means that it is
# possible to lose up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
#
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.

no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes

# Redis can create append-only base files in either RDB or AOF formats. Using
# the RDB format is always faster and more efficient, and disabling it is only
# supported for backward compatibility purposes.
aof-use-rdb-preamble yes

# Redis supports recording timestamp annotations in the AOF to support restoring
# the data from a specific point-in-time. However, using this capability changes
# the AOF format in a way that may not be compatible with existing AOF parsers.
aof-timestamp-enabled no

################################ SHUTDOWN #####################################

# Maximum time to wait for replicas when shutting down, in seconds.
#
# During shut down, a grace period allows any lagging replicas to catch up with
# the latest replication offset before the master exists. This period can
# prevent data loss, especially for deployments without configured disk backups.
#
# The 'shutdown-timeout' value is the grace period's duration in seconds. It is
# only applicable when the instance has replicas. To disable the feature, set
# the value to 0.
#
# shutdown-timeout 10

# When Redis receives a SIGINT or SIGTERM, shutdown is initiated and by default
# an RDB snapshot is written to disk in a blocking operation if save points are configured.
# The options used on signaled shutdown can include the following values:
# default:  Saves RDB snapshot only if save points are configured.
#           Waits for lagging replicas to catch up.
# save:     Forces a DB saving operation even if no save points are configured.
# nosave:   Prevents DB saving operation even if one or more save points are configured.
# now:      Skips waiting for lagging replicas.
# force:    Ignores any errors that would normally prevent the server from exiting.
#
# Any combination of values is allowed as long as "save" and "nosave" are not set simultaneously.
# Example: "nosave force now"
#
# shutdown-on-sigint default
# shutdown-on-sigterm default

################ NON-DETERMINISTIC LONG BLOCKING COMMANDS #####################

# Maximum time in milliseconds for EVAL scripts, functions and in some cases
# modules' commands before Redis can start processing or rejecting other clients.
#
# If the maximum execution time is reached Redis will start to reply to most
# commands with a BUSY error.
#
# In this state Redis will only allow a handful of commands to be executed.
# For instance, SCRIPT KILL, FUNCTION KILL, SHUTDOWN NOSAVE and possibly some
# module specific 'allow-busy' commands.
#
# SCRIPT KILL and FUNCTION KILL will only be able to stop a script that did not
# yet call any write commands, so SHUTDOWN NOSAVE may be the only way to stop
# the server in the case a write command was already issued by the script when
# the user doesn't want to wait for the natural termination of the script.
#
# The default is 5 seconds. It is possible to set it to 0 or a negative value
# to disable this mechanism (uninterrupted execution). Note that in the past
# this config had a different name, which is now an alias, so both of these do
# the same:
# lua-time-limit 5000
# busy-reply-threshold 5000

################################ REDIS CLUSTER  ###############################

# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are a multiple of the node timeout.
#
# cluster-node-timeout 15000

# The cluster port is the port that the cluster bus will listen for inbound connections on. When set 
# to the default value, 0, it will be bound to the command port + 10000. Setting this value requires 
# you to specify the cluster bus port when executing cluster meet.
# cluster-port 0

# A replica of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a replica to actually have an exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple replicas able to failover, they exchange messages
#    in order to try to give an advantage to the replica with the best
#    replication offset (more data from the master processed).
#    Replicas will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single replica computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the replica will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a replica will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * cluster-replica-validity-factor) + repl-ping-replica-period
#
# So for example if node-timeout is 30 seconds, and the cluster-replica-validity-factor
# is 10, and assuming a default repl-ping-replica-period of 10 seconds, the
# replica will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large cluster-replica-validity-factor may allow replicas with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a replica at all.
#
# For maximum availability, it is possible to set the cluster-replica-validity-factor
# to a value of 0, which means, that replicas will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-replica-validity-factor 10

# Cluster replicas are able to migrate to orphaned masters, that are masters
# that are left without working replicas. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working replicas.
#
# Replicas migrate to orphaned masters only if there are still at least a
# given number of other working replicas for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a replica
# will migrate only if there is at least 1 other working replica for its master
# and so forth. It usually reflects the number of replicas you want for every
# master in your cluster.
#
# Default is 1 (replicas migrate only if their masters remain with at least
# one replica). To disable migration just set it to a very large value or
# set cluster-allow-replica-migration to 'no'.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1

# Turning off this option allows to use less automatic cluster configuration.
# It both disables migration to orphaned masters and migration from masters
# that became empty.
#
# Default is 'yes' (allow automatic migrations).
#
# cluster-allow-replica-migration yes

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least a hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# This option, when set to yes, prevents replicas from trying to failover its
# master during master failures. However the replica can still perform a
# manual failover, if forced to do so.
#
# This is useful in different scenarios, especially in the case of multiple
# data center operations, where we want one side to never be promoted if not
# in the case of a total DC failure.
#
# cluster-replica-no-failover no

# This option, when set to yes, allows nodes to serve read traffic while the
# cluster is in a down state, as long as it believes it owns the slots.
#
# This is useful for two cases.  The first case is for when an application
# doesn't require consistency of data during node failures or network partitions.
# One example of this is a cache, where as long as the node has the data it
# should be able to serve it.
#
# The second use case is for configurations that don't meet the recommended
# three shards but want to enable cluster mode and scale later. A
# master outage in a 1 or 2 shard configuration causes a read/write outage to the
# entire cluster without this option set, with it set there is only a write outage.
# Without a quorum of masters, slot ownership will not change automatically.
#
# cluster-allow-reads-when-down no

# This option, when set to yes, allows nodes to serve pubsub shard traffic while
# the cluster is in a down state, as long as it believes it owns the slots.
#
# This is useful if the application would like to use the pubsub feature even when
# the cluster global stable state is not OK. If the application wants to make sure only
# one shard is serving a given channel, this feature should be kept as yes.
#
# cluster-allow-pubsubshard-when-down yes

# Cluster link send buffer limit is the limit on the memory usage of an individual
# cluster bus link's send buffer in bytes. Cluster links would be freed if they exceed
# this limit. This is to primarily prevent send buffers from growing unbounded on links
# toward slow peers (E.g. PubSub messages being piled up).
# This limit is disabled by default. Enable this limit when 'mem_cluster_links' INFO field
# and/or 'send-buffer-allocated' entries in the 'CLUSTER LINKS` command output continuously increase.
# Minimum limit of 1gb is recommended so that cluster link buffer can fit in at least a single
# PubSub message by default. (client-query-buffer-limit default value is 1gb)
#
# cluster-link-sendbuf-limit 0
 
# Clusters can configure their announced hostname using this config. This is a common use case for 
# applications that need to use TLS Server Name Indication (SNI) or dealing with DNS based
# routing. By default this value is only shown as additional metadata in the CLUSTER SLOTS
# command, but can be changed using 'cluster-preferred-endpoint-type' config. This value is 
# communicated along the clusterbus to all nodes, setting it to an empty string will remove 
# the hostname and also propagate the removal.
#
# cluster-announce-hostname ""

# Clusters can configure an optional nodename to be used in addition to the node ID for
# debugging and admin information. This name is broadcasted between nodes, so will be used
# in addition to the node ID when reporting cross node events such as node failures.
# cluster-announce-human-nodename ""

# Clusters can advertise how clients should connect to them using either their IP address,
# a user defined hostname, or by declaring they have no endpoint. Which endpoint is
# shown as the preferred endpoint is set by using the cluster-preferred-endpoint-type
# config with values 'ip', 'hostname', or 'unknown-endpoint'. This value controls how
# the endpoint returned for MOVED/ASKING requests as well as the first field of CLUSTER SLOTS. 
# If the preferred endpoint type is set to hostname, but no announced hostname is set, a '?' 
# will be returned instead.
#
# When a cluster advertises itself as having an unknown endpoint, it's indicating that
# the server doesn't know how clients can reach the cluster. This can happen in certain 
# networking situations where there are multiple possible routes to the node, and the 
# server doesn't know which one the client took. In this case, the server is expecting
# the client to reach out on the same endpoint it used for making the last request, but use
# the port provided in the response.
#
# cluster-preferred-endpoint-type ip

# In order to setup your cluster make sure to read the documentation
# available at https://redis.io web site.

########################## CLUSTER DOCKER/NAT support  ########################

# In certain deployments, Redis Cluster nodes address discovery fails, because
# addresses are NAT-ted or because ports are forwarded (the typical case is
# Docker and other containers).
#
# In order to make Redis Cluster working in such environments, a static
# configuration where each node knows its public address is needed. The
# following four options are used for this scope, and are:
#
# * cluster-announce-ip
# * cluster-announce-port
# * cluster-announce-tls-port
# * cluster-announce-bus-port
#
# Each instructs the node about its address, client ports (for connections
# without and with TLS) and cluster message bus port. The information is then
# published in the header of the bus packets so that other nodes will be able to
# correctly map the address of the node publishing the information.
#
# If tls-cluster is set to yes and cluster-announce-tls-port is omitted or set
# to zero, then cluster-announce-port refers to the TLS port. Note also that
# cluster-announce-tls-port has no effect if tls-cluster is set to no.
#
# If the above options are not used, the normal Redis Cluster auto-detection
# will be used instead.
#
# Note that when remapped, the bus port may not be at the fixed offset of
# clients port + 10000, so you can specify any port and bus-port depending
# on how they get remapped. If the bus-port is not set, a fixed offset of
# 10000 will be used as usual.
#
# Example:
#
# cluster-announce-ip 10.1.1.5
# cluster-announce-tls-port 6379
# cluster-announce-port 0
# cluster-announce-bus-port 6380

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

################################ LATENCY TRACKING ##############################

# The Redis extended latency monitoring tracks the per command latencies and enables
# exporting the percentile distribution via the INFO latencystats command,
# and cumulative latency distributions (histograms) via the LATENCY command.
#
# By default, the extended latency monitoring is enabled since the overhead
# of keeping track of the command latency is very small.
# latency-tracking yes

# By default the exported latency percentiles via the INFO latencystats command
# are the p50, p99, and p999.
# latency-tracking-info-percentiles 50 99 99.9

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at https://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  n     New key events (Note: not included in the 'A' class)
#  t     Stream commands
#  d     Module key type events
#  m     Key-miss events (Note: It is not included in the 'A' class)
#  A     Alias for g$lshzxetd, so that the "AKE" string means all the events
#        (Except key-miss events which are excluded from 'A' due to their
#         unique nature).
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
hash-max-listpack-entries 512
hash-max-listpack-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-listpack-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Sets containing non-integer values are also encoded using a memory efficient
# data structure when they have a small number of entries, and the biggest entry
# does not exceed a given threshold. These thresholds can be configured using
# the following directives.
set-max-listpack-entries 128
set-max-listpack-value 64

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-listpack-entries 128
zset-max-listpack-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When a HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Streams macro node max size / items. The stream data structure is a radix
# tree of big nodes that encode multiple items inside. Using this configuration
# it is possible to configure how big a single node can be in bytes, and the
# maximum number of items it may contain before switching to a new node when
# appending new stream entries. If any of the following settings are set to
# zero, the limit is ignored, so for instance it is possible to set just a
# max entries limit by setting max-bytes to 0 and max-entries to the desired
# value.
stream-node-max-bytes 4096
stream-node-max-entries 100

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# replica -> replica clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and replica clients, since
# subscribers and replicas receive data in a push fashion.
#
# Note that it doesn't make sense to set the replica clients output buffer
# limit lower than the repl-backlog-size config (partial sync will succeed
# and then replica will get disconnected).
# Such a configuration is ignored (the size of repl-backlog-size will be used).
# This doesn't have memory consumption implications since the replica client
# will share the backlog buffers memory.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Client query buffers accumulate new commands. They are limited to a fixed
# amount by default in order to avoid that a protocol desynchronization (for
# instance due to a bug in the client) will lead to unbound memory usage in
# the query buffer. However you can configure it here if you have very special
# needs, such us huge multi/exec requests or alike.
#
# client-query-buffer-limit 1gb

# In some scenarios client connections can hog up memory leading to OOM
# errors or data eviction. To avoid this we can cap the accumulated memory
# used by all client connections (all pubsub and normal clients). Once we
# reach that limit connections will be dropped by the server freeing up
# memory. The server will attempt to drop the connections using the most 
# memory first. We call this mechanism "client eviction".
#
# Client eviction is configured using the maxmemory-clients setting as follows:
# 0 - client eviction is disabled (default)
#
# A memory value can be used for the client eviction threshold,
# for example:
# maxmemory-clients 1g
#
# A percentage value (between 1% and 100%) means the client eviction threshold
# is based on a percentage of the maxmemory setting. For example to set client
# eviction at 5% of maxmemory:
# maxmemory-clients 5%

# In the Redis protocol, bulk requests, that are, elements representing single
# strings, are normally limited to 512 mb. However you can change this limit
# here, but must be 1mb or greater
#
# proto-max-bulk-len 512mb

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# Normally it is useful to have an HZ value which is proportional to the
# number of clients connected. This is useful in order, for instance, to
# avoid too many clients are processed for each background task invocation
# in order to avoid latency spikes.
#
# Since the default HZ value by default is conservatively set to 10, Redis
# offers, and enables by default, the ability to use an adaptive HZ value
# which will temporarily raise when there are many connected clients.
#
# When dynamic HZ is enabled, the actual configured HZ will be used
# as a baseline, but multiples of the configured HZ value will be actually
# used as needed once more clients are connected. In this way an idle
# instance will use very little CPU time while a busy instance will be
# more responsive.
dynamic-hz yes

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 4 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

# When redis saves RDB file, if the following option is enabled
# the file will be fsync-ed every 4 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
rdb-save-incremental-fsync yes

# Redis LFU eviction (see maxmemory setting) can be tuned. However it is a good
# idea to start with the default settings and only change them after investigating
# how to improve the performances and how the keys LFU change over time, which
# is possible to inspect via the OBJECT FREQ command.
#
# There are two tunable parameters in the Redis LFU implementation: the
# counter logarithm factor and the counter decay time. It is important to
# understand what the two parameters mean before changing them.
#
# The LFU counter is just 8 bits per key, it's maximum value is 255, so Redis
# uses a probabilistic increment with logarithmic behavior. Given the value
# of the old counter, when a key is accessed, the counter is incremented in
# this way:
#
# 1. A random number R between 0 and 1 is extracted.
# 2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
# 3. The counter is incremented only if R < P.
#
# The default lfu-log-factor is 10. This is a table of how the frequency
# counter changes with a different number of accesses with different
# logarithmic factors:
#
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
#
# NOTE: The above table was obtained by running the following commands:
#
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
#
# NOTE 2: The counter initial value is 5 in order to give new objects a chance
# to accumulate hits.
#
# The counter decay time is the time, in minutes, that must elapse in order
# for the key counter to be decremented.
#
# The default value for the lfu-decay-time is 1. A special value of 0 means we
# will never decay the counter.
#
# lfu-log-factor 10
# lfu-decay-time 1

########################### ACTIVE DEFRAGMENTATION #######################
#
# What is active defragmentation?
# -------------------------------
#
# Active (online) defragmentation allows a Redis server to compact the
# spaces left between small allocations and deallocations of data in memory,
# thus allowing to reclaim back memory.
#
# Fragmentation is a natural process that happens with every allocator (but
# less so with Jemalloc, fortunately) and certain workloads. Normally a server
# restart is needed in order to lower the fragmentation, or at least to flush
# away all the data and create it again. However thanks to this feature
# implemented by Oran Agra for Redis 4.0 this process can happen at runtime
# in a "hot" way, while the server is running.
#
# Basically when the fragmentation is over a certain level (see the
# configuration options below) Redis will start to create new copies of the
# values in contiguous memory regions by exploiting certain specific Jemalloc
# features (in order to understand if an allocation is causing fragmentation
# and to allocate it in a better place), and at the same time, will release the
# old copies of the data. This process, repeated incrementally for all the keys
# will cause the fragmentation to drop back to normal values.
#
# Important things to understand:
#
# 1. This feature is disabled by default, and only works if you compiled Redis
#    to use the copy of Jemalloc we ship with the source code of Redis.
#    This is the default with Linux builds.
#
# 2. You never need to enable this feature if you don't have fragmentation
#    issues.
#
# 3. Once you experience fragmentation, you can enable this feature when
#    needed with the command "CONFIG SET activedefrag yes".
#
# The configuration parameters are able to fine tune the behavior of the
# defragmentation process. If you are not sure about what they mean it is
# a good idea to leave the defaults untouched.

# Active defragmentation is disabled by default
# activedefrag no

# Minimum amount of fragmentation waste to start active defrag
# active-defrag-ignore-bytes 100mb

# Minimum percentage of fragmentation to start active defrag
# active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
# active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage, to be used when the lower
# threshold is reached
# active-defrag-cycle-min 1

# Maximal effort for defrag in CPU percentage, to be used when the upper
# threshold is reached
# active-defrag-cycle-max 25

# Maximum number of set/hash/zset/list fields that will be processed from
# the main dictionary scan
# active-defrag-max-scan-fields 1000

# Jemalloc background thread for purging will be enabled by default
jemalloc-bg-thread yes

# It is possible to pin different threads and processes of Redis to specific
# CPUs in your system, in order to maximize the performances of the server.
# This is useful both in order to pin different Redis threads in different
# CPUs, but also in order to make sure that multiple Redis instances running
# in the same host will be pinned to different CPUs.
#
# Normally you can do this using the "taskset" command, however it is also
# possible to this via Redis configuration directly, both in Linux and FreeBSD.
#
# You can pin the server/IO threads, bio threads, aof rewrite child process, and
# the bgsave child process. The syntax to specify the cpu list is the same as
# the taskset command:
#
# Set redis server/io threads to cpu affinity 0,2,4,6:
# server-cpulist 0-7:2
#
# Set bio threads to cpu affinity 1,3:
# bio-cpulist 1,3
#
# Set aof rewrite child process to cpu affinity 8,9,10,11:
# aof-rewrite-cpulist 8-11
#
# Set bgsave child process to cpu affinity 1,10,11
# bgsave-cpulist 1,10-11

# In some cases redis will emit warnings and even refuse to start if it detects
# that the system is in bad state, it is possible to suppress these warnings
# by setting the following config which takes a space delimited list of warnings
# to suppress
#
# ignore-warnings ARM64-COW-BUG
```

## 小结

### 内容一

通过上面一步步的操作，发现目录结构可以再优化下：
```
/media/sf_develop/www     -作为web项目开发目录，各个web项目放在该目录下
/media/sf_develop/www/html     -作为纯静态HTML项目开发目录，各个HTML项目放在该目录下
/media/sf_develop/www/php     -作为PHP项目开发目录，各个PHP的web项目放在该目录下
/media/sf_develop/www/nodejs     -作为NodeJs项目开发目录，各个NodeJs项目放在该目录下
/media/sf_develop/golang     -作为Go项目开发目录，各个Go项目放在该目录下
/media/sf_develop/golang/www     -作为Go的web项目开发目录，各个Go的web项目在该目录下
/media/sf_develop/virtualbox/docker     -作为docker部署的相关软件目录
/media/sf_develop/virtualbox/docker/nginx_1.25.3    -作为docker部署的nginx:1.25.3容器的相关目录
/media/sf_develop/virtualbox/docker/mysql_8.0.35    -作为docker部署的mysql:8.0.35容器的相关目录
```

### 内容二

上面提到Nginx容器与PHP容器通信的方式，使用了硬连接，这是一种过时的连接方式，最好使用容器内桥接方式实现。

<https://ibaiyang.github.io/blog/docker/2022/10/11/深入理解-Docker-网络原理.html>

### 内容三

docker容器创建时，`-v` 目录映射有两个方向，分为左侧和右侧。
当左侧目录存在时，会覆盖右侧的目录。当左侧目录不存在时，右侧的目录会复制到左侧目录下。
具体使用时，需要分清楚场景。

有时右侧容器中的目录下是有文件的，如 `mysql:8.0.35` 的 `/var/lib/mysql` 目录下：
```
bash-4.4# ls /var/lib/mysql
'#ib_16384_0.dblwr'   binlog.index      ibtmp1               server-cert.pem
'#ib_16384_1.dblwr'   ca-key.pem        mysql                server-key.pem
'#innodb_redo'        ca.pem            mysql.ibd            sys
'#innodb_temp'        client-cert.pem   mysql.sock           undo_001
 auto.cnf             client-key.pem    performance_schema   undo_002
 binlog.000001        ib_buffer_pool    private_key.pem
 binlog.000002        ibdata1           public_key.pem
bash-4.4#
```

当左侧目录存在时，创建容器时用`-v` 映射就会把右侧容器中的文件覆盖掉。
这种情况下左侧目录不应该提前创建好。

## 问题集合

### 网络接口配置文件

虽然我们 SSH 可以连接虚拟机了，但发现在`/etc/sysconfig/network-scripts`下并没有网络接口配置文件 `ifcfg-enp0s8` ：

```
[root@10 ~]# cd /etc/sysconfig/network-scripts/
[root@10 network-scripts]#
[root@10 network-scripts]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 85923sec preferred_lft 85923sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 594sec preferred_lft 594sec
    inet6 fe80::90bb:7c4e:f558:7881/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@10 network-scripts]#
[root@10 network-scripts]# ls
ifcfg-enp0s3  ifdown-isdn      ifdown-tunnel  ifup-isdn    ifup-Team
ifcfg-lo      ifdown-post      ifup           ifup-plip    ifup-TeamPort
ifdown        ifdown-ppp       ifup-aliases   ifup-plusb   ifup-tunnel
ifdown-bnep   ifdown-routes    ifup-bnep      ifup-post    ifup-wireless
ifdown-eth    ifdown-sit       ifup-eth       ifup-ppp     init.ipv6-global
ifdown-ippp   ifdown-Team      ifup-ippp      ifup-routes  network-functions
ifdown-ipv6   ifdown-TeamPort  ifup-ipv6      ifup-sit     network-functions-ipv6
[root@10 network-scripts]#
[root@10 network-scripts]# nmcli connection show
NAME        UUID                                  TYPE      DEVICE
enp0s3      c7c6c14c-c2c1-424f-9367-621a3c0a36c7  ethernet  enp0s3
有线连接 1  67578541-4773-32e0-9b24-baa234af0548  ethernet  enp0s8
[root@10 network-scripts]#
```

我们可以使用 NetworkManager管理工具 的nmcli命令生成该文件（建议最好不要执行，会影响到其他网卡的选择，如桥接的搭建）：

> nmcli connection add type ethernet con-name enp0s8 ifname enp0s8

明细：
```
[root@10 network-scripts]# nmcli connection add type ethernet con-name enp0s8 ifname enp0s8
连接 "enp0s8" (b1687624-6940-41e2-810a-f0de39b6a51b) 已成功添加。
[root@10 network-scripts]#
[root@10 network-scripts]# ls
ifcfg-enp0s3  ifdown-ippp    ifdown-sit       ifup-bnep  ifup-plusb   ifup-TeamPort
ifcfg-enp0s8  ifdown-ipv6    ifdown-Team      ifup-eth   ifup-post    ifup-tunnel
ifcfg-lo      ifdown-isdn    ifdown-TeamPort  ifup-ippp  ifup-ppp     ifup-wireless
ifdown        ifdown-post    ifdown-tunnel    ifup-ipv6  ifup-routes  init.ipv6-global
ifdown-bnep   ifdown-ppp     ifup             ifup-isdn  ifup-sit     network-functions
ifdown-eth    ifdown-routes  ifup-aliases     ifup-plip  ifup-Team    network-functions-ipv6
[root@10 network-scripts]#
[root@10 network-scripts]# cat ifcfg-enp0s8
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s8
UUID=b1687624-6940-41e2-810a-f0de39b6a51b
DEVICE=enp0s8
ONBOOT=yes
[root@10 network-scripts]#
[root@10 network-scripts]# systemctl start network
[root@10 network-scripts]#
[root@10 network-scripts]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 84743sec preferred_lft 84743sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 432sec preferred_lft 432sec
    inet6 fe80::90bb:7c4e:f558:7881/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@10 network-scripts]#
[root@10 network-scripts]# cat ifcfg-enp0s3
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=c7c6c14c-c2c1-424f-9367-621a3c0a36c7
DEVICE=enp0s3
ONBOOT=yes
[root@10 network-scripts]#
```

### 局域网内其他电脑访问本机虚拟机

一、开启桥接模式

项目开发过程中碰到一个问题，团队没有测试服务器，同事想直接使用我的虚拟机提供的服务。
这里就需要关闭 “仅主机(Host-0nly)网络” 网卡，不然只有本地主机可以访问虚拟机而其他电脑无法访问。
然后 连接方式 选择 “桥接网卡”， 混杂模式 中选择 “全部允许”。

原本想的是同时开启 “仅主机(Host-0nly)网络” 网卡 和 “桥接网卡”，结果各种意外情况发生，调试了好长时间。
这也是为什么在上面说到的 ——网络接口配置文件 中最好不要手动生成 enp0s8 的原因，手动生成意味着配置会写死，
写死后 VirtualBox 选择各类型网卡时的配置灵活性就丧失了。

我们在 VirtualBox 中指定虚拟机的 网络 配置页面中：保留 “网络地址转换(NAT)”，因为该网卡使虚拟机内可以访问外网；
另外取消 “仅主机(Host-0nly)网络” 的选择，该网卡的作用是只允许本地主机可以访问虚拟机；
接下来新建 “桥接网卡” 的选择，混杂模式 中选择 “全部允许”。

现在 VirtualBox 配置部分完成，我们需要知道其他电脑通过哪个IP地址访问本机虚拟机。
我们在VirtualBox中选择 非后台模式 启动虚拟机，登录虚拟机，然后使用下面命令查看虚拟机在局域网内的IP地址：

> ip addr

明细：
```
[root@10 ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 77498sec preferred_lft 77498sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:46:43:7b brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.149/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s9
       valid_lft 34787sec preferred_lft 34787sec
    inet6 fe80::c932:de89:9774:d265/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:5a:e4:7c:6f brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:5aff:fee4:7c6f/64 scope link
       valid_lft forever preferred_lft forever
[root@10 ~]#
```

可以看到该虚拟机在局域网中的IP是 192.168.0.149，局域网内的电脑都可以通过该IP访问虚拟机：

```
C:\Users>ping 192.168.0.149

正在 Ping 192.168.0.149 具有 32 字节的数据:
来自 192.168.0.149 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.0.149 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.0.149 的回复: 字节=32 时间<1ms TTL=64
来自 192.168.0.149 的回复: 字节=32 时间<1ms TTL=64

192.168.0.149 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 0ms，最长 = 0ms，平均 = 0ms

C:\Users>
```

二、修改账号密码

既然局域网内的电脑都可以访问该虚拟机，为了安全，我们就要修改下管理员的账户密码：

```
[root@10 ~]# passwd root
更改用户 root 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[root@10 ~]#
```

其他用户也可以修改下自己的密码：

```
[admin@10 ~]$ pwd
/home/admin
[admin@10 ~]$
[admin@10 ~]$ passwd
更改用户 admin 的密码 。
为 admin 更改 STRESS 密码。
（当前）UNIX 密码：
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[admin@10 ~]$
```

三、虚拟机内桥接网络

这里所说的桥接是虚拟机内的桥接，与上面局域网内与虚拟机的桥接不是一个东西。

这里可以先看下 <https://ibaiyang.github.io/blog/docker/2022/10/11/深入理解-Docker-网络原理.html>

Docker提供了三种网络模式：

```
[root@10 ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
2217f1eafbc0   bridge    bridge    local
707c2e449547   host      host      local
60bec5c05037   none      null      local
[root@10 ~]#
```

看一下虚拟机内的ip地址信息：

```
[root@10 ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 81950sec preferred_lft 81950sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:46:43:7b brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.149/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s9
       valid_lft 38750sec preferred_lft 38750sec
    inet6 fe80::c932:de89:9774:d265/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:c2:41:a8:11 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:c2ff:fe41:a811/64 scope link
       valid_lft forever preferred_lft forever
[root@10 ~]#
```

虚拟机内默认使用的网络模式是bridge，也就是我们的 docker0 网卡，通过inspect查看：

```
[root@10 ~]# docker inspect 2217f1eafbc0
[
    {
        "Name": "bridge",
        "Id": "2217f1eafbc0c986c7d63e19b73a7ae442f53414ec35c7001e7ab05018d752e3",
        "Created": "2023-11-16T09:00:50.601003227+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
[root@10 ~]#
```

docker0 相当于一个路由器的作用，任何一个容器启动默认都是 docker0 网络。 Docker默认会给容器分配一个可用ip，并把它同docke0相连。

看下下面两个：

```
[root@10 ~]# docker start php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
[root@10 ~]# docker start nginx_php_7.4-fpm
nginx_php_7.4-fpm
[root@10 ~]#
[root@10 ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 81869sec preferred_lft 81869sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:46:43:7b brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.149/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s9
       valid_lft 38670sec preferred_lft 38670sec
    inet6 fe80::c932:de89:9774:d265/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:c2:41:a8:11 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:c2ff:fe41:a811/64 scope link
       valid_lft forever preferred_lft forever
10: veth0c9ecab@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 5a:60:41:aa:75:76 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::5860:41ff:feaa:7576/64 scope link
       valid_lft forever preferred_lft forever
12: veth37e3437@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 62:13:be:99:44:61 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::6013:beff:fe99:4461/64 scope link
       valid_lft forever preferred_lft forever
[root@10 ~]#
[root@10 ~]# docker inspect 2217f1eafbc0
[
    {
        "Name": "bridge",
        "Id": "2217f1eafbc0c986c7d63e19b73a7ae442f53414ec35c7001e7ab05018d752e3",
        "Created": "2023-11-16T09:00:50.601003227+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "a0c75b4db3a63ec76e1319333f36ec5865adceaf9048f2714bc1bdb8e67df059": {
                "Name": "php_7.4-fpm",
                "EndpointID": "01ccbf12bed3d6951c6db61a0b89c2abb6133befb0d05c97cf3636ac5bb7a537",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "f2d344418b9fb0dfcc75da53a6bfdaf5b7d722dab1c2edc0f06ad770c2143571": {
                "Name": "nginx_php_7.4-fpm",
                "EndpointID": "42a48cba3f1f0997943d7a4bf958b87d2d197dcfbb40bdce07443fb084a71695",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
[root@10 ~]#
```

这是 host 和 none 网络模式的内容：

```
[root@10 ~]# docker inspect 707c2e449547
[
    {
        "Name": "host",
        "Id": "707c2e449547f569aad79dbbafbfab6f30252f974c34c37b42c2af29de84b5ae",
        "Created": "2023-10-30T11:31:54.973527817+08:00",
        "Scope": "local",
        "Driver": "host",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": []
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
[root@10 ~]#
[root@10 ~]# docker inspect 60bec5c05037
[
    {
        "Name": "none",
        "Id": "60bec5c050379035ba1926ba5ca70f8ea16b682d34aef81d07f28954d07782eb",
        "Created": "2023-10-30T11:31:54.892502216+08:00",
        "Scope": "local",
        "Driver": "null",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": []
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
[root@10 ~]#
```

看一下各容器的 hosts ：
```
[root@10 ~]# docker exec -it php_7.4-fpm cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      a0c75b4db3a6
[root@10 ~]#
[root@10 ~]# docker exec -it nginx_php_7.4-fpm cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      php a0c75b4db3a6 php_7.4-fpm
172.17.0.3      f2d344418b9f
[root@10 ~]#
```

`--link` 实现 nginx_php_7.4-fpm 容器和 php_7.4-fpm 容器之间的连通，`--link`是单向的。

在宿主机上 ping 一下容器：
```
[root@10 ~]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.150 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.031 ms
^C
--- 172.17.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1013ms
rtt min/avg/max/mdev = 0.031/0.090/0.150/0.060 ms
[root@10 ~]#
[root@10 ~]# ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.141 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.038 ms
^C
--- 172.17.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1008ms
rtt min/avg/max/mdev = 0.038/0.089/0.141/0.052 ms
[root@10 ~]#
```

四、可删内容

```
[root@10 ~]# ls
ifcfg-enp0s3  ifdown-ippp    ifdown-sit       ifup-bnep  ifup-plusb   ifup-TeamPort
ifcfg-enp0s8  ifdown-ipv6    ifdown-Team      ifup-eth   ifup-post    ifup-tunnel
ifcfg-lo      ifdown-isdn    ifdown-TeamPort  ifup-ippp  ifup-ppp     ifup-wireless
ifdown        ifdown-post    ifdown-tunnel    ifup-ipv6  ifup-routes  init.ipv6-global
ifdown-bnep   ifdown-ppp     ifup             ifup-isdn  ifup-sit     network-functions
ifdown-eth    ifdown-routes  ifup-aliases     ifup-plip  ifup-Team    network-functions-ipv6
[root@10 ~]#
[root@10 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group defaul       t qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP        group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 86319sec preferred_lft 86319sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP        group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic        enp0s8
       valid_lft 519sec preferred_lft 519sec
    inet6 fe80::855a:f81e:c405:a566/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP        group default qlen 1000
    link/ether 08:00:27:46:43:7b brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.149/24 brd 192.168.0.255 scope global noprefixroute dynamic e       np0s9
       valid_lft 43119sec preferred_lft 43119sec
    inet6 fe80::c932:de89:9774:d265/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@10 ~]#
[root@10 ~]# nmcli connection show
NAME        UUID                                  TYPE      DEVICE
enp0s3      c7c6c14c-c2c1-424f-9367-621a3c0a36c7  ethernet  enp0s3
enp0s8      b1687624-6940-41e2-810a-f0de39b6a51b  ethernet  enp0s8
有线连接 1  daeff1ab-9633-37c4-8b9a-202beb53a70e  ethernet  enp0s9
[root@10 ~]#
[root@10 ~]# nmcli connection add type ethernet con-name enp0s9 ifname enp0s9
连接 "enp0s9" (3190d018-e47b-4371-94f2-cd5fcbc75eac) 已成功添加。
[root@10 ~]#
[root@10 ~]# nmcli connection show
NAME        UUID                                  TYPE      DEVICE
enp0s3      c7c6c14c-c2c1-424f-9367-621a3c0a36c7  ethernet  enp0s3
enp0s8      b1687624-6940-41e2-810a-f0de39b6a51b  ethernet  enp0s8
有线连接 1  daeff1ab-9633-37c4-8b9a-202beb53a70e  ethernet  enp0s9
enp0s9      3190d018-e47b-4371-94f2-cd5fcbc75eac  ethernet  --
[root@10 ~]#
[root@10 ~]# ls /etc/sysconfig/network-scripts/
ifcfg-enp0s3  ifdown-ipv6      ifdown-tunnel  ifup-plip      ifup-tunnel
ifcfg-enp0s8  ifdown-isdn      ifup           ifup-plusb     ifup-wireless
ifcfg-enp0s9  ifdown-post      ifup-aliases   ifup-post      init.ipv6-global
ifcfg-lo      ifdown-ppp       ifup-bnep      ifup-ppp       network-functions
ifdown        ifdown-routes    ifup-eth       ifup-routes    network-functions-ipv6
ifdown-bnep   ifdown-sit       ifup-ippp      ifup-sit
ifdown-eth    ifdown-Team      ifup-ipv6      ifup-Team
ifdown-ippp   ifdown-TeamPort  ifup-isdn      ifup-TeamPort
[root@10 ~]#
[root@10 ~]# ls /etc/sysconfig/network-scripts/
ifcfg-enp0s3  ifdown-isdn      ifup          ifup-plusb     ifup-wireless
ifcfg-enp0s8  ifdown-post      ifup-aliases  ifup-post      init.ipv6-global
ifcfg-lo      ifdown-ppp       ifup-bnep     ifup-ppp       network-functions
ifdown        ifdown-routes    ifup-eth      ifup-routes    network-functions-ipv6
ifdown-bnep   ifdown-sit       ifup-ippp     ifup-sit
ifdown-eth    ifdown-Team      ifup-ipv6     ifup-Team
ifdown-ippp   ifdown-TeamPort  ifup-isdn     ifup-TeamPort
ifdown-ipv6   ifdown-tunnel    ifup-plip     ifup-tunnel
[root@10 ~]#
[root@10 ~]# nmcli connection show
NAME        UUID                                  TYPE      DEVICE
enp0s3      c7c6c14c-c2c1-424f-9367-621a3c0a36c7  ethernet  enp0s3
enp0s8      b1687624-6940-41e2-810a-f0de39b6a51b  ethernet  enp0s8
有线连接 1  daeff1ab-9633-37c4-8b9a-202beb53a70e  ethernet  enp0s9
[root@10 ~]#
[root@10 network-scripts]# cp ifcfg-enp0s8 ifcfg-enp0s9
[root@10 network-scripts]#
[root@10 network-scripts]# ls
ifcfg-enp0s3  ifdown-ipv6      ifdown-tunnel  ifup-plip      ifup-tunnel
ifcfg-enp0s8  ifdown-isdn      ifup           ifup-plusb     ifup-wireless
ifcfg-enp0s9  ifdown-post      ifup-aliases   ifup-post      init.ipv6-global
ifcfg-lo      ifdown-ppp       ifup-bnep      ifup-ppp       network-functions
ifdown        ifdown-routes    ifup-eth       ifup-routes    network-functions-ipv6
ifdown-bnep   ifdown-sit       ifup-ippp      ifup-sit
ifdown-eth    ifdown-Team      ifup-ipv6      ifup-Team
ifdown-ippp   ifdown-TeamPort  ifup-isdn      ifup-TeamPort
[root@10 network-scripts]#
[root@10 network-scripts]# vi ifcfg-enp0s9
```

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s8
UUID=b1687624-6940-41e2-810a-f0de39b6a51b
DEVICE=enp0s8
ONBOOT=yes
```

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s9
UUID=daeff1ab-9633-37c4-8b9a-202beb53a70e
DEVICE=enp09
ONBOOT=yes
IPADDR=192.168.0.149
PREFIX=24
IPV6_PRIVACY=no
```

```
[root@10 network-scripts]# nmcli connection show
NAME        UUID                                  TYPE      DEVICE
enp0s3      c7c6c14c-c2c1-424f-9367-621a3c0a36c7  ethernet  enp0s3
enp0s8      b1687624-6940-41e2-810a-f0de39b6a51b  ethernet  enp0s8
有线连接 1  daeff1ab-9633-37c4-8b9a-202beb53a70e  ethernet  enp0s9
[root@10 network-scripts]#
[root@10 network-scripts]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:2e:7d:8e brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 85217sec preferred_lft 85217sec
    inet6 fe80::ebac:d904:2f88:6288/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:77:ee:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global noprefixroute dynamic enp0s8
       valid_lft 467sec preferred_lft 467sec
    inet6 fe80::855a:f81e:c405:a566/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:46:43:7b brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.149/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s9
       valid_lft 42017sec preferred_lft 42017sec
    inet6 fe80::c932:de89:9774:d265/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@10 network-scripts]#
[root@10 network-scripts]# systemctl restart network
Job for network.service failed because the control process exited with error code. See "systemctl status network.service" and "journalctl -xe" for details.
[root@10 network-scripts]#
[root@10 network-scripts]# systemctl status network
● network.service - LSB: Bring up/down networking
   Loaded: loaded (/etc/rc.d/init.d/network; bad; vendor preset: disabled)
   Active: failed (Result: exit-code) since 四 2023-11-09 14:00:52 CST; 24s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 1819 ExecStop=/etc/rc.d/init.d/network stop (code=exited, status=0/SUCCESS)
  Process: 2022 ExecStart=/etc/rc.d/init.d/network start (code=exited, status=1/FAILURE)

11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: 正在打开接口 enp0s3： 连接已成功激…4）
11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: [  确定  ]
11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: 正在打开接口 enp0s8： 连接已成功激…5）
11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: [  确定  ]
11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: 正在打开接口 enp0s9： 错误：连接激…)).
11月 09 14:00:52 MiWiFi-RA72-srv network[2022]: [失败]
11月 09 14:00:52 MiWiFi-RA72-srv systemd[1]: network.service: control process exi...=1
11月 09 14:00:52 MiWiFi-RA72-srv systemd[1]: Failed to start LSB: Bring up/down n...g.
11月 09 14:00:52 MiWiFi-RA72-srv systemd[1]: Unit network.service entered failed ...e.
11月 09 14:00:52 MiWiFi-RA72-srv systemd[1]: network.service failed.
Hint: Some lines were ellipsized, use -l to show in full.
[root@10 network-scripts]#
```

### 增加PHP运行内存

在有些PHP项目的运行中会报错：Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to 
allocate 20480 bytes) in /var/www/html/*** on line ***。指出PHP默认内存是 134217728 bytes 即128M，
额外需要 20480 bytes 即20KB。我们需要修改PHP的 `php.ini` 配置文件，增加PHP运行内存。

```
root@a0c75b4db3a6:/usr/src# php --ini
Configuration File (php.ini) Path: /usr/local/etc/php
Loaded Configuration File:         (none)
Scan for additional .ini files in: /usr/local/etc/php/conf.d
Additional .ini files parsed:      /usr/local/etc/php/conf.d/docker-php-ext-bcmath.ini,
/usr/local/etc/php/conf.d/docker-php-ext-gd.ini,
/usr/local/etc/php/conf.d/docker-php-ext-msgpack.ini,
/usr/local/etc/php/conf.d/docker-php-ext-pdo_mysql.ini,
/usr/local/etc/php/conf.d/docker-php-ext-redis.ini,
/usr/local/etc/php/conf.d/docker-php-ext-sodium.ini,
/usr/local/etc/php/conf.d/docker-php-ext-swoole.ini,
/usr/local/etc/php/conf.d/docker-php-ext-zip.ini

root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls -l /usr/local/etc/php
total 148
drwxr-xr-x. 1 root root  4096 Dec  8 14:46 conf.d
-rw-r--r--. 1 root root 72554 Nov 15  2022 php.ini-development
-rw-r--r--. 1 root root 72584 Nov 15  2022 php.ini-production
root@a0c75b4db3a6:/usr/src#
```

conf.d 是PHP拓展配置目录。
php.ini-development 是PHP测试环境配置；
php.ini-production 是PHP生产环境配置。

现在 `php.ini` 在这里未启用，为了方便，我们直接新建一个 `php.ini` ，然后写入最大内存配置（这里按照需求设置具体大小），
然后退出容器，重启容器：

> touch /usr/local/etc/php/php.ini
>
> echo "memory_limit = 512M" >> /usr/local/etc/php/php.ini

```
root@a0c75b4db3a6:/usr/src# touch /usr/local/etc/php/php.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# echo "memory_limit = 512M" >> /usr/local/etc/php/php.ini
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# exit
exit
[root@10 ~]#
[root@10 ~]# docker restart php_7.4-fpm
php_7.4-fpm
[root@10 ~]#
```

## MySQL安装过程报错

在虚拟机中使用docker安装MySQL时，想打通宿主机与容器的数据共享，这样在win10宿主机中就可以很方便的操作和查看MySQL了。
但各种思路操作下来都是报错，下面记录下，作为以后的参考。

### 思路一

常见方式，直接把宿主机目录与容器目录进行映射。

下载现在最新的8.0.35版本：
> docker pull mysql:8.0.35

明细：
```
[root@10 ~]# docker pull mysql:8.0.35
8.0.35: Pulling from library/mysql
8e0176adc18c: Pull complete
a6b6bf6e5d0f: Pull complete
c17b83f8620f: Pull complete
b2e259cd9b6c: Pull complete
366131ab00d1: Pull complete
2f99ba83a3cb: Pull complete
f7c88955f01f: Pull complete
577fb415d7f8: Pull complete
29160ed46eb1: Pull complete
69ce9884ce5d: Pull complete
848f0dceb14c: Pull complete
Digest: sha256:974cac08fff819ea2dfeb83fed4d2eb5100bb79603aff6148bdc53d8be4895f3
Status: Downloaded newer image for mysql:8.0.35
docker.io/library/mysql:8.0.35
[root@10 ~]#
[root@10 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
php-7.4       1.0       2a7836ecd1a3   2 weeks ago     885MB
nginx-vim     latest    19c8bf579203   4 weeks ago     248MB
nginx         latest    593aee2afb64   5 weeks ago     187MB
mysql         8.0.35    96bc8cf3633b   5 weeks ago     582MB
hello-world   latest    9c7a54a9a43c   6 months ago    13.3kB
php           7.4-fpm   38f2b691dcb8   12 months ago   443MB
[root@10 ~]#
```

在创建容器前，先把要与容器挂载的目录创建好：
```
mkdir -p /media/sf_develop/virtualbox/docker/mysql_8.0.35
```

下面mysql_8.0.35目录下的conf.d 目录里为配置文件，MySQL运行时 `/etc/my.cnf` 会读取该目录下的所有后缀为 `.cnf` 的文件；
data 目录里为数据文件；log 目录里为日志文件。

创建容器：
```
docker run \
  --name mysql_8.0.35 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
  -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/data:/var/lib/mysql \
  -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/log:/var/log/mysql \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d mysql:8.0.35
```

```
--name：容器名，此处命名为mysql_8.0.35
-p：端口映射，此处映射 主机3306端口 到 容器的3306端口
-e：配置信息，此处配置mysql的root用户的登陆密码
-v：主机和容器的目录映射关系，":"前为主机目录，之后为容器目录
-v /etc/localtime:/etc/localtime:ro：映射这个目录是为了校正容器内的时间跟宿主机时间一致
--privileged=true：表示容器内的root用户拥有真正的root权限，而不是外部普通用户的权限
-d：后台运行容器，保证在退出终端后容器继续运行
```

明细：
```
[root@10 ~]# mkdir -p \
>   /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d \
>   /media/sf_develop/virtualbox/docker/mysql_8.0.35/data \
>   /media/sf_develop/virtualbox/docker/mysql_8.0.35/log
[root@10 ~]#
[root@10 ~]# docker run \
>   --name mysql_8.0.35 \
>   -p 3306:3306 \
>   -e MYSQL_ROOT_PASSWORD=root \
>   -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
>   -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/data:/var/lib/mysql \
>   -v /media/sf_develop/virtualbox/docker/mysql_8.0.35/log:/var/log/mysql \
>   -v /etc/localtime:/etc/localtime:ro \
>   --privileged=true \
>   -d mysql:8.0.35
673c5fcc483c122ac3bb95e6d3be4551bd8cdefe717f8a1eb0e03f0b4abe7a36
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                     PORTS                                      NAMES
673c5fcc483c   mysql:8.0.35   "docker-entrypoint.s…"   5 minutes ago   Exited (1) 5 minutes ago                                              mysql_8.0.35
f2d344418b9f   nginx          "/docker-entrypoint.…"   4 weeks ago     Up 6 hours                 0.0.0.0:80->80/tcp, :::80->80/tcp          nginx_php_7.4-fpm
a0c75b4db3a6   php:7.4-fpm    "docker-php-entrypoi…"   4 weeks ago     Up 6 hours                 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp  php_7.4-fpm
e386a696ef90   hello-world    "/hello"                  4 weeks ago     Exited (0) 4 weeks ago                                                confident_pike
[root@10 ~]#
```

看到上面启动失败，查看原因：
```
[root@10 ~]# docker logs  mysql_8.0.35
2023-11-30 15:56:52+08:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.35-1.el8 started.
2023-11-30 15:56:52+08:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2023-11-30 15:56:52+08:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.35-1.el8 started.
2023-11-30 15:56:52+08:00 [ERROR] [Entrypoint]: mysqld failed while attempting to check config
        command was: mysqld --verbose --help --log-bin-index=/tmp/tmp.YUVn5nAiWS
        mysqld: Can't read dir of '/etc/mysql/conf.d/' (OS errno 13 - Permission denied)
mysqld: [ERROR] Stopped processing the 'includedir' directive in file /etc/my.cnf at line 36.
mysqld: [ERROR] Fatal error in defaults handling. Program aborted!
2023-11-30 16:05:42+08:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.35-1.el8 started.
2023-11-30 16:05:42+08:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2023-11-30 16:05:42+08:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.35-1.el8 started.
2023-11-30 16:05:42+08:00 [ERROR] [Entrypoint]: mysqld failed while attempting to check config
        command was: mysqld --verbose --help --log-bin-index=/tmp/tmp.x32HKKcF9D
        mysqld: Can't read dir of '/etc/mysql/conf.d/' (OS errno 13 - Permission denied)
mysqld: [ERROR] Stopped processing the 'includedir' directive in file /etc/my.cnf at line 36.
mysqld: [ERROR] Fatal error in defaults handling. Program aborted!
[root@10 ~]#
```

`/etc/my.cnf` 读取 `/etc/mysql/conf.d/` 目录时权限不足。

修改权限没有奏效：
```
[root@10 ~]# ll  /media/sf_develop/virtualbox/docker/mysql_8.0.35
总用量 0
drwxrwx---. 1 root vboxsf 0 11月 30 15:53 conf.d
drwxrwx---. 1 root vboxsf 0 11月 30 16:17 data
drwxrwx---. 1 root vboxsf 0 11月 30 15:53 log
[root@10 ~]#
[root@10 ~]# chmod -R 777 /media/sf_develop/virtualbox/docker/mysql_8.0.35
[root@10 ~]#
[root@10 ~]# ll  /media/sf_develop/virtualbox/docker/mysql_8.0.35
总用量 0
drwxrwx---. 1 root vboxsf 0 11月 30 15:53 conf.d
drwxrwx---. 1 root vboxsf 0 11月 30 16:17 data
drwxrwx---. 1 root vboxsf 0 11月 30 15:53 log
[root@10 ~]#
```

这种方式无法进行下去。

原想的是宿主机中先放开权限，这样容器中也可以直接操作宿主机中的文件了；
或者启动后，在容器内修改需要操作的文件的权限，现在是容器都启动不起来。
该思路宣告失败。

### 思路二

先把虚拟机的一个目录映射到MySQL容器中，再在MySQL容器中把虚拟机映射的目录下的各个文件夹配置到MySQL服务中。

下载现在最新的8.0.35版本：
> docker pull mysql:8.0.35

在创建容器前，先把要与容器挂载的目录创建好：
```
mkdir -p  /media/sf_develop/virtualbox/docker/mysql_8.0.35
```

创建并启动容器：
```
[root@10 ~]# docker run \
>   --name mysql_8.0.35 \
>   -p 3306:3306 \
>   -e MYSQL_ROOT_PASSWORD=root \
>   -v /media/sf_develop/virtualbox/docker/mysql_8.0.35:/home/mysql/docker \
>   -v /etc/localtime:/etc/localtime:ro \
>   --privileged=true \
>   -d mysql:8.0.35
327c430e53cbaf34c11e97da999cec8f0e785407f5c01749914314f46089953e
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                    PORTS                                                  NAMES
327c430e53cb   mysql:8.0.35   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds              0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_8.0.35
f2d344418b9f   nginx          "/docker-entrypoint.…"   4 weeks ago     Exited (0) 17 hours ago                                                          nginx_php_7.4-fpm
a0c75b4db3a6   php:7.4-fpm    "docker-php-entrypoi…"   4 weeks ago     Exited (0) 17 hours ago                                                          php_7.4-fpm
e386a696ef90   hello-world    "/hello"                  4 weeks ago     Exited (0) 4 weeks ago                                                           confident_pike
[root@10 ~]#
```

进入容器，想到用链接的方式打通宿主机与虚拟机：
```
[root@10 ~]# docker exec -it mysql_8.0.35 /bin/bash
bash-4.4#
bash-4.4# ls /var/lib/mysql
'#ib_16384_0.dblwr'   binlog.000002     ib_buffer_pool   performance_schema   undo_001
'#ib_16384_1.dblwr'   binlog.index      ibdata1          private_key.pem      undo_002
'#innodb_redo'        ca-key.pem        ibtmp1           public_key.pem
'#innodb_temp'        ca.pem            mysql            server-cert.pem
 auto.cnf             client-cert.pem   mysql.ibd        server-key.pem
 binlog.000001        client-key.pem    mysql.sock       sys
bash-4.4#
bash-4.4# ln -s /var/lib/mysql /home/mysql/docker/data
ln: failed to create symbolic link '/home/mysql/docker/data': Operation not permitted
bash-4.4#
```

没成功，还是按照原思路来吧，给文件夹授权让mysql用户可以操作：
```
bash-4.4# ls -l /home/mysql
total 0
drwxrwx---. 1 root 995 0 Dec  1 10:12 docker
bash-4.4#
bash-4.4# ls /home/mysql/docker/
conf.d  data  log
bash-4.4#
bash-4.4# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
mysql:x:999:999::/var/lib/mysql:/bin/bash
bash-4.4#
bash-4.4# cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mem:x:8:
kmem:x:9:
wheel:x:10:
cdrom:x:11:
mail:x:12:
man:x:15:
dialout:x:18:
floppy:x:19:
games:x:20:
tape:x:33:
video:x:39:
ftp:x:50:
lock:x:54:
audio:x:63:
users:x:100:
nobody:x:65534:
mysql:x:999:
bash-4.4#
bash-4.4# groupadd -g 995 docker
bash-4.4#
bash-4.4# usermod -aG docker mysql
bash-4.4#
bash-4.4# cat /etc/group | grep docker
docker:x:995:mysql
bash-4.4#
bash-4.4# cat /etc/passwd | grep mysql
mysql:x:999:999::/var/lib/mysql:/bin/bash
bash-4.4#
bash-4.4# ls -l /home/mysql/docker
total 0
drwxrwx---. 1 root docker 0 Dec  1 10:21 conf.d
drwxrwx---. 1 root docker 0 Dec  1 10:21 data
drwxrwx---. 1 root docker 0 Dec  1 10:21 log
bash-4.4#
bash-4.4# ln -s /var/lib/mysql /home/mysql/docker/data
ln: failed to create symbolic link '/home/mysql/docker/data': Operation not permitted
bash-4.4#
```

mysql用户可以操作`/home/mysql/docker`文件夹了，不过链接时还是报无权限，链接的方式行不通。

原思路，修改相关配置：
```
bash-4.4# ls -l /etc | grep my
-rw-r--r--.  1 root root   1317 Oct 28 00:51 my.cnf
drwxr-xr-x.  2 root root      6 Oct 12 20:50 my.cnf.d
drwxr-xr-x.  3 root root     20 Oct 28 00:51 mysql
bash-4.4#
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
bash-4.4#
bash-4.4#
bash-4.4# ls -l /etc/my.cnf.d
total 0
bash-4.4#
bash-4.4# ls -l /etc/mysql
total 0
drwxr-xr-x. 2 root root 6 Oct 28 00:51 conf.d
bash-4.4#
bash-4.4# ls -l /etc/mysql/conf.d
total 0
bash-4.4#
bash-4.4# vi -v
bash: vi: command not found
bash-4.4#
bash-4.4# vim -h
bash: vim: command not found
bash-4.4#
```

容器中没有文本编辑器，也下载不了：
```
bash-4.4# yum -v
bash: yum: command not found
bash-4.4#
bash-4.4# apt -h
bash: apt: command not found
bash-4.4#
bash-4.4# dnf -h
bash: dnf: command not found
bash-4.4#
```

想着下载一个现成的AppImage使用，结果依赖fuse库，运行失败：
```
bash-4.4# mv /home/mysql/docker/soft/Vim-v9.0.2138.glibc2.29-x86_64.AppImage /usr/local/bin/vim
bash-4.4#
bash-4.4# ls -l /usr/local/bin
total 21492
-rwxr-xr-x. 1 root root      14189 Oct 28 00:50 docker-entrypoint.sh
-rwxr-xr-x. 1 root root    2355690 Oct 28 00:50 gosu
-rwxrwx---. 1 root docker 19629248 Dec  1 11:54 vim
bash-4.4#
bash-4.4# chmod +x /usr/local/bin/vim
bash-4.4#
bash-4.4# vim -v
dlopen(): error loading libfuse.so.2

AppImages require FUSE to run.
You might still be able to extract the contents of this AppImage
if you run it with the --appimage-extract option.
See https://github.com/AppImage/AppImageKit/wiki/FUSE
for more information
bash-4.4#
```

想着把vim源码git下载后移动到了容器中，编译时发现无法编译：
```
bash-4.4# ls /home/mysql/docker/soft/vim
CONTRIBUTING.md  README.md       SECURITY.md  pixmaps  uninstall.txt
Filelist         README.txt      ci           runtime  vimtutor.bat
LICENSE          README_VIM9.md  configure    src      vimtutor.com
Makefile         READMEdir       nsis         tools
bash-4.4#
bash-4.4# cd /home/mysql/docker/soft/vim/src
bash-4.4#
bash-4.4# make && make install
bash: make: command not found
bash-4.4#
bash-4.4# make -h
bash: make: command not found
bash-4.4#
```

该思路折戟沉沙，宣告失败。

在容器中，没有mysql服务重启的命令，需要退出容器，在虚拟机中重启：
```
bash-4.4# uname
Linux
bash-4.4#
bash-4.4# uname -a
Linux 327c430e53cb 3.10.0-1160.102.1.el7.x86_64 #1 SMP Tue Oct 17 15:42:21 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
bash-4.4#
bash-4.4# cat /etc/issue
\S
Kernel \r on an \m

bash-4.4#
bash-4.4# cat /etc/os-release
NAME="Oracle Linux Server"
VERSION="8.8"
ID="ol"
ID_LIKE="fedora"
VARIANT="Server"
VARIANT_ID="server"
VERSION_ID="8.8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="Oracle Linux Server 8.8"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:oracle:linux:8:8:server"
HOME_URL="https://linux.oracle.com/"
BUG_REPORT_URL="https://github.com/oracle/oracle-linux"

ORACLE_BUGZILLA_PRODUCT="Oracle Linux 8"
ORACLE_BUGZILLA_PRODUCT_VERSION=8.8
ORACLE_SUPPORT_PRODUCT="Oracle Linux"
ORACLE_SUPPORT_PRODUCT_VERSION=8.8
bash-4.4#
bash-4.4# systemctl status mysql
bash: systemctl: command not found
bash-4.4#
```

### 思路三

上面操作下来，MySQL容器启动不了的原因应该是MySQL容器启动时没有读权限加载宿主机中的配置文件导致的，
可以先让MySQL容器启动时不要去加载宿主机中的配置文件，启动成功后再去修改配置文件。

在容器启动时先把虚拟机关联的宿主机的一个目录映射到MySQL容器中，启动成功后进入容器让mysql用户可以操作映射目录下的文件。
接下来再在虚拟机写一个MySQL的配置文件`my.cnf`， 在这个配置文件中把虚拟机映射的目录下的各个文件夹配置到MySQL服务中，
然后`docker cp`把该配置文件复制到容器中，重启容器。

下载现在最新的8.0.35版本：
> docker pull mysql:8.0.35

在创建容器前，先把要与容器挂载的目录创建好：
```
mkdir -p /media/sf_develop/virtualbox/docker/mysql_8.0.35
```

到时mysql_8.0.35目录下的conf.d 目录里为配置文件，MySQL运行时 `/etc/my.cnf` 会读取该目录下的所有后缀为 `.cnf` 的文件；
data 目录里为数据文件；log 目录里为日志文件。

创建容器：
```
docker run \
  --name mysql_8.0.35 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -v /media/sf_develop/virtualbox/docker/mysql_8.0.35:/usr/local/docker/mysql \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d mysql:8.0.35
```

```
--name：容器名，此处命名为mysql_8.0.35
-p：端口映射，此处映射 主机3306端口 到 容器的3306端口
-e：配置信息，此处配置mysql的root用户的登陆密码
-v：主机和容器的目录映射关系，":"前为主机目录，之后为容器目录
-v /etc/localtime:/etc/localtime:ro：映射这个目录是为了校正容器内的时间跟宿主机时间一致
--privileged=true：表示容器内的root用户拥有真正的root权限，而不是外部普通用户的权限
-d：后台运行容器，保证在退出终端后容器继续运行
```

明细：
```
[root@10 ~]# docker run \
>   --name mysql_8.0.35 \
>   -p 3306:3306 \
>   -e MYSQL_ROOT_PASSWORD=root \
>   -v /media/sf_develop/virtualbox/docker/mysql_8.0.35:/usr/local/docker/mysql \
>   -v /etc/localtime:/etc/localtime:ro \
>   --privileged=true \
>   -d mysql:8.0.35
690b39429e8eabb2803184fea34055b904943eff63cc83a24ee3a324e401b79b
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS                    PORTS                                                  NAMES
690b39429e8e   mysql:8.0.35   "docker-entrypoint.s…"   16 seconds ago   Up 14 seconds             0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_8.0.35
f2d344418b9f   nginx          "/docker-entrypoint.…"   4 weeks ago      Exited (0) 21 hours ago                                                          nginx_php_7.4-fpm
a0c75b4db3a6   php:7.4-fpm    "docker-php-entrypoi…"   4 weeks ago      Exited (0) 21 hours ago                                                          php_7.4-fpm
e386a696ef90   hello-world    "/hello"                  4 weeks ago      Exited (0) 4 weeks ago                                                           confident_pike
[root@10 ~]#
```

进入容器，新建组，赋权：
> docker exec -it mysql_8.0.35 /bin/bash
>
> groupadd -g 995 docker
>
> usermod -aG docker mysql

明细：
```
[root@10 ~]# docker exec -it mysql_8.0.35 /bin/bash
bash-4.4#
bash-4.4# ls -l /usr/local
total 0
drwxr-xr-x. 1 root root 34 Oct 28 00:52 bin
drwxr-xr-x. 3 root root 19 Dec  1 14:16 docker
drwxr-xr-x. 2 root root  6 Oct  9  2021 etc
drwxr-xr-x. 2 root root  6 Oct  9  2021 games
drwxr-xr-x. 2 root root  6 Oct  9  2021 include
drwxr-xr-x. 2 root root  6 Oct  9  2021 lib
drwxr-xr-x. 3 root root 17 Oct 20 06:17 lib64
drwxr-xr-x. 2 root root  6 Oct  9  2021 libexec
drwxr-xr-x. 2 root root  6 Oct  9  2021 sbin
drwxr-xr-x. 5 root root 49 Oct 20 06:17 share
drwxr-xr-x. 2 root root  6 Oct  9  2021 src
bash-4.4#
bash-4.4# ls -l /usr/local/docker
total 0
drwxrwx---. 1 root 995 0 Dec  1 14:06 mysql
bash-4.4#
bash-4.4# ls -l /usr/local/docker/mysql
total 0
drwxrwx---. 1 root 995 0 Dec  1 14:01 conf.d
drwxrwx---. 1 root 995 0 Dec  1 10:22 data
drwxrwx---. 1 root 995 0 Dec  1 10:24 log
bash-4.4#
bash-4.4# cat /etc/passwd | grep mysql
mysql:x:999:999::/var/lib/mysql:/bin/bash
bash-4.4#
bash-4.4# cat /etc/group | grep mysql
mysql:x:999:
bash-4.4#
bash-4.4# groupadd -g 995 docker
bash-4.4#
bash-4.4# usermod -aG docker mysql
bash-4.4#
bash-4.4# cat /etc/passwd | grep mysql
mysql:x:999:999::/var/lib/mysql:/bin/bash
bash-4.4#
bash-4.4# cat /etc/group | grep mysql
mysql:x:999:
docker:x:995:mysql
bash-4.4#
bash-4.4# ls -l /usr/local/docker/mysql
total 0
drwxrwx---. 1 root docker 0 Dec  1 14:01 conf.d
drwxrwx---. 1 root docker 0 Dec  1 10:22 data
drwxrwx---. 1 root docker 0 Dec  1 10:24 log
bash-4.4#
```

把数据目录复制过去：
```
bash-4.4# ls /var/lib/mysql
'#ib_16384_0.dblwr'   binlog.index      ibtmp1               server-cert.pem
'#ib_16384_1.dblwr'   ca-key.pem        mysql                server-key.pem
'#innodb_redo'        ca.pem            mysql.ibd            sys
'#innodb_temp'        client-cert.pem   mysql.sock           undo_001
 auto.cnf             client-key.pem    performance_schema   undo_002
 binlog.000001        ib_buffer_pool    private_key.pem
 binlog.000002        ibdata1           public_key.pem
bash-4.4#
bash-4.4# cp -r /var/lib/mysql/* /usr/local/docker/mysql/data
cp: cannot create symbolic link '/usr/local/docker/mysql/data/mysql.sock': Operation not permitted
bash-4.4#
bash-4.4# ls /usr/local/docker/mysql/data
'#ib_16384_0.dblwr'   binlog.index      ibtmp1               server-key.pem
'#ib_16384_1.dblwr'   ca-key.pem        mysql                sys
'#innodb_redo'        ca.pem            mysql.ibd            undo_001
'#innodb_temp'        client-cert.pem   performance_schema   undo_002
 auto.cnf             client-key.pem    private_key.pem
 binlog.000001        ib_buffer_pool    public_key.pem
 binlog.000002        ibdata1           server-cert.pem
bash-4.4#
bash-4.4# ls -l /var/lib/mysql | grep mysql.sock
lrwxrwxrwx. 1 mysql mysql       27 Dec  1 15:02 mysql.sock -> /var/run/mysqld/mysqld.sock
bash-4.4#
bash-4.4# ln -s /var/run/mysqld/mysqld.sock /usr/local/docker/mysql/data/mysql.sock
ln: failed to create symbolic link '/usr/local/docker/mysql/data/mysql.sock': Operation not permitted
bash-4.4#
```

退出容器，新建`/etc/my.cnf`并编辑：
```
bash-4.4#exit
[root@10 ~]#
[root@10 ~]# cd /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d
[root@10 conf.d]#
[root@10 conf.d]# touch my.cnf
[root@10 conf.d]#
[root@10 conf.d]# vi my.cnf
[root@10 conf.d]#
```

写入内容并保存：
```
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/usr/local/docker/mysql/data
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /usr/local/docker/mysql/conf.d/
```

从虚拟机复制到容器中：
> docker cp /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d/my.cnf mysql_8.0.35:/etc/

明细：
```
[root@10 conf.d]# cd ~
[root@10 ~]# 
[root@10 ~]# docker cp /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d/my.cnf mysql_8.0.35:/etc/
Successfully copied 3.07kB to mysql_8.0.35:/etc/
[root@10 ~]#
[root@10 ~]# docker exec -it mysql_8.0.35 /bin/bash
bash-4.4#
bash-4.4# ls -l /etc | grep my
-rwxrwx---.  1 root docker   1380 Dec  1 14:46 my.cnf
drwxr-xr-x.  2 root root        6 Oct 12 20:50 my.cnf.d
drwxr-xr-x.  3 root root       20 Oct 28 00:51 mysql
bash-4.4#
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/usr/local/docker/mysql/data
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /usr/local/docker/mysql/conf.d/
bash-4.4#
```

删除`my.cnf`文件，防止MySQL启动时循环加载：
> rm /media/sf_develop/virtualbox/docker/mysql_8.0.35/conf.d/my.cnf

重新启动，启动失败：
```
[root@10 ~]# docker restart mysql_8.0.35
mysql_8.0.35
[root@10 ~]#
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                     PORTS     NAMES
f0435d049aeb   mysql:8.0.35   "docker-entrypoint.s…"   7 minutes ago   Exited (1) 6 seconds ago             mysql_8.0.35
f2d344418b9f   nginx          "/docker-entrypoint.…"   4 weeks ago     Exited (0) 23 hours ago              nginx_php_7.4-fpm
a0c75b4db3a6   php:7.4-fpm    "docker-php-entrypoi…"   4 weeks ago     Exited (0) 23 hours ago              php_7.4-fpm
e386a696ef90   hello-world    "/hello"                  4 weeks ago     Exited (0) 4 weeks ago               confident_pike
[root@10 ~]#
```

这么多思路操作下来，原因应该是容器可以与虚拟机进行文件映射，但容器无法操作宿主机文件。

### 思路四

避开宿主机目录，用虚拟机中的目录去映射容器的目录。

下载现在最新的8.0.35版本：
> docker pull mysql:8.0.35

为了与虚拟机中直接编译安装的MySQL区分开，在虚拟机中新建目录：
> mkdir -p /user/local/docker/mysql_8.0.35

创建容器：
```
docker run \
  --name mysql_8.0.35 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -v /user/local/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
  -v /user/local/docker/mysql_8.0.35/data:/var/lib/mysql \
  -v /user/local/docker/mysql_8.0.35/log:/var/log/mysql \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d mysql:8.0.35
```

虽然这样容器可以创建和启动成功，而且目录也映射成功，但没有意义，相关的文件还是在虚拟机中，不是在宿主机中，
和去容器中进行配置、查看日志没有什么区别，而且数据文件也还是在虚拟机中，不是在宿主机中，
再者使用 `docker cp` 也可以把容器中的数据文件直接复制到宿主机中，即使容器没用启动，
如`docker cp mysql_8.0.35:/var/lib/mysql  /media/sf_develop/virtualbox/docker/mysql_8.0.35/data`。

后记：

* 容器内是没有文本编辑器的，我们需要借用虚拟机的vi编辑器，所以配置目录需要映射

```
docker run \
  --name mysql_8.0.35 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=root \
  -v /user/local/docker/mysql_8.0.35/conf.d:/etc/mysql/conf.d \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d mysql:8.0.35
```

## Redis安装过程报错

在虚拟机中使用docker安装Redis时，想直接可视化的在宿主机中配置Redis容器的服务参数，
但操作下来和上面MySQL一样，容器可以打通虚拟机，但无法打通宿主机。
下面记录下，作为以后的参考。

```
docker run \
  --name redis_7.2.3 \
  -p 6379:6379 \
  -v /media/sf_develop/virtualbox/docker/redis_7.2.3/conf:/usr/local/etc/redis \
  -v /etc/localtime:/etc/localtime:ro \
  --privileged=true \
  -d redis:7.2.3 \
  redis-server \
  /usr/local/etc/redis/redis.conf
```

查看下状态：
```
[root@10 ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                      PORTS     NAMES
3eb2af5dcedd   redis:7.2.3    "docker-entrypoint.s…"   9 minutes ago   Exited (1) 9 minutes ago              redis_7.2.3
[root@10 ~]#
```

启动失败，查看原因：
```
[root@10 ~]# docker logs redis_7.2.3-2
1:C 04 Dec 2023 10:27:11.077 # Fatal error, can't open config file '/usr/local/etc/redis/redis.conf': Permission denied
[root@10 ~]#
```

没有权限。

结合上面正确安装部分内容，原因应该是容器中其他用户无法操作 配置文件(995用户组) 导致的（虚拟机）修改不了宿主机文件权限：
```
[root@10 ~]# ls -l /media/sf_develop/virtualbox/docker/redis_7.2.3/conf
总用量 1
-rwxrwx---. 1 root vboxsf 113 12月  4 12:29 redis.conf
[root@10 ~]#
[root@10 ~]# chmod 777 /media/sf_develop/virtualbox/docker/redis_7.2.3/conf/redis.conf
[root@10 ~]#
[root@10 ~]# ls -l /media/sf_develop/virtualbox/docker/redis_7.2.3/conf
总用量 1
-rwxrwx---. 1 root vboxsf 113 12月  4 12:29 redis.conf
[root@10 ~]#
```







<br/><br/><br/><br/><br/>
## 参考资料

什么是 SSH 访问？你需要知道的一切 <https://baijiahao.baidu.com/s?id=1734399066815684825>

Centos7 系列：磁盘挂载和磁盘扩容(新加硬盘方式) <https://blog.csdn.net/qq_42449106/article/details/130140345>

VirtualBox虚拟机中Centos7系统如何设置共享文件夹 <https://zhuanlan.zhihu.com/p/633877183>

docker php安装redis扩展 <https://blog.csdn.net/longfeng995/article/details/130557612>

Failed to open/create the internal network 'HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter'
<https://www.jianshu.com/p/4bee5f609d01>

