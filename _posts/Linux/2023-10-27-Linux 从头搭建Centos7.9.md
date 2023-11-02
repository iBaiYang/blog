---
layout: post
categories: Linux
title: VirtualBox 从头搭建Centos7.9
meta: VirtualBox 从头搭建Centos7.9
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

看一下现在服务器的网络信息（虚拟机无法访问外网，下面网络配置部分解决）：

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
docker run --name php_7.4-fpm -p 9000:9000 -v /media/sf_develop:/var/www/html -v /etc/localtime:/etc/localtime:ro --privileged=true -d php_7.4-fpm
```

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

PHP拓展需要安装在源码目录下，初始化php源码目录：
> docker-php-source extract

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

拓展包放在`/usr/src/php/ext`目录下：
```
root@a0c75b4db3a6:/usr/src# ls /usr/src/php
CODING_STANDARDS.md  README.md            azure                configure.ac         php.ini-production  win32
CONTRIBUTING.md      TSRM                 azure-pipelines.yml  docs                 run-tests.php
EXTENSIONS           UPGRADING            build                ext                  sapi
LICENSE              UPGRADING.INTERNALS  buildconf            main                 scripts
NEWS                 Zend                 buildconf.bat        pear                 tests
README.REDIST.BINS   appveyor             configure            php.ini-development  travis
root@a0c75b4db3a6:/usr/src#
root@a0c75b4db3a6:/usr/src# ls /usr/src/php/ext
bcmath      dom           gd       ldap      openssl       pdo_odbc    reflection  sodium    tokenizer  zlib
bz2         enchant       gettext  libxml    pcntl         pdo_pgsql   session     spl       xml
calendar    exif          gmp      mbstring  pcre          pdo_sqlite  shmop       sqlite3   xmlreader
com_dotnet  ext_skel.php  hash     mysqli    pdo           pgsql       simplexml   standard  xmlrpc
ctype       ffi           iconv    mysqlnd   pdo_dblib     phar        skeleton    sysvmsg   xmlwriter
curl        fileinfo      imap     oci8      pdo_firebird  posix       snmp        sysvsem   xsl
date        filter        intl     odbc      pdo_mysql     pspell      soap        sysvshm   zend_test
dba         ftp           json     opcache   pdo_oci       readline    sockets     tidy      zip
root@a0c75b4db3a6:/usr/src#
```

这里可以看到 bcmath、pdo_mysql 等常用拓展包。

二、bcmath 拓展安装

安装命令：
> docker-php-ext-install bcmath

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

先把 openssl/ssl.h 头文件安装好，不然安装过程中会报错
> apt-get update  
>
> apt-get install libssl-dev

然后下载和安装：
> pecl install swoole-4.8.13
>
> docker-php-ext-enable swoole

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

看来要直接使用Redis拓展包源码安装了。

在容器中的 tmp 目录下下载 redis-6.0.2.tgz 包，解压缩然后进入解压包，phpize生成configure文件：

> cd /tmp
>
> curl https://pecl.php.net/get/redis-6.0.2.tgz -O
>
> tar -zxf redis-6.0.2.tgz
>
> cd redis-6.0.2
> 
> /usr/local/bin/phpize

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

> whereis php-config
>
> ./configure --with-php-config=/usr/local/bin/php-config

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

开启redis拓展：

> cd /usr/local/etc/php/conf.d
>
> touch docker-php-ext-redis.ini
> 
> vim docker-php-ext-redis.ini
>
> echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/redis.so" >> docker-php-ext-redis.ini

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
> php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
>
> php composer-setup.php

安装完成后移除安装文件：
> php -r "unlink('composer-setup.php');"

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

## Nginx安装

### 基础安装

下载nginx包：
> ocker pull nginx

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

```
[root@10 ~]# docker run --name nginx_php_7.4-fpm -p 80:80 -v /media/sf_develop/virtualbox/docker/nginx:/etc/nginx/conf.d -v /media/sf_develop:/usr/share/nginx/www -v /etc/localtime:/etc/localtime:ro --link php_7.4-fpm:php --privileged=true -d nginx
f2d344418b9fb0dfcc75da53a6bfdaf5b7d722dab1c2edc0f06ad770c2143571
[root@10 ~]#
[root@10 ~]# docker exec -it nginx_php_7.4-fpm /bin/bash
root@f2d344418b9f:/#
root@f2d344418b9f:/# nginx 0v
nginx: invalid option: "0v"
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






<br/><br/><br/><br/><br/>
## 参考资料

什么是 SSH 访问？你需要知道的一切 <https://baijiahao.baidu.com/s?id=1734399066815684825>

Centos7 系列：磁盘挂载和磁盘扩容(新加硬盘方式) <https://blog.csdn.net/qq_42449106/article/details/130140345>

VirtualBox虚拟机中Centos7系统如何设置共享文件夹 <https://zhuanlan.zhihu.com/p/633877183>

docker php安装redis扩展 <https://blog.csdn.net/longfeng995/article/details/130557612>
