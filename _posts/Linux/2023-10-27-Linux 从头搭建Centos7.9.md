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

在VirtualBox官网下载最新版 [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) 安装，我这里是VirtualBox-7.0.12版。

## Centos7.9环境搭建

### 虚拟机安装

这里的虚拟机只是类似于刚出厂未安装系统（如Win10）的裸机，只是提供硬件上的支持，这也是下面可能会报错的来源。
在安装Centos时才是安装运行的软件系统。有了这个认识后，下面安装应该会好理解一些。

在Centos官网下载iso安装包 [Centos download](http://isoredirect.centos.org/centos/7/isos/x86_64/) ，
选择一个下载速度比较快的源，下载后保存到指定位置，我这里下载的是 CentOS-7-x86_64-Minimal-2009.iso 包。

![]({{site.baseurl}}/images/Linux/20231027150306.png)

启动 VirtualBox，点击新建：

![]({{site.baseurl}}/images/Linux/20231027181544.png)

名称是虚拟机的名称，文件夹是把虚拟机安装在哪个文件夹下，虚拟光盘是上面下载的Centos的iso安装包。

点击下一步，修改下密码：

![]({{site.baseurl}}/images/Linux/20231027181613.png)

点击下一步，配置内存大小和处理器数目：

![]({{site.baseurl}}/images/Linux/20231027181641.png)

点击下一步，配置虚拟硬盘大小：

![]({{site.baseurl}}/images/Linux/20231027181723.png)

点击下一步，看一下上面概括起来的配置：

![]({{site.baseurl}}/images/Linux/20231027181748.png)

点击“完成”按钮，等待安装完成，不过这里安装好的是 虚拟机，不是 Centos7.9 系统环境，所以右侧提示“不能安装系统”：

![]({{site.baseurl}}/images/Linux/20231027181904.png)

### CPU未开启虚拟机支持功能

![]({{site.baseurl}}/images/Linux/20231027160948.png)

期间虚拟机安装后或启动时报错：
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

查询到原因：你正在尝试在没有开启硬件虚拟化的情况下运行虚拟机。

第一个错误 "Not in a hypervisor partition (HVP=0)" 错误提示你可能没有在支持硬件虚拟化的环境下运行你的程序。
这种情况下，你需要启动一个虚拟机并在这个虚拟机中运行你的程序。

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

我们再看一下虚拟机安装的Centos的具体参数（现在 存储 部分中已经没有 光驱 了）：

![]({{site.baseurl}}/images/Linux/20231027183534.png)

在命令行窗口中登录 root 账户：

![]({{site.baseurl}}/images/Linux/20231027183819.png)

至此，Centos7.9安装完成。

接下来是服务器配置部分。

### SSH客户端连接

看一下现在服务器的网络信息：

![]({{site.baseurl}}/images/Linux/20231027224336.png)

主机无法访问服务器，需要为服务器配置 Host-Only模式。

关闭服务器运行，点击服务器的设置，查看“网络”，切换到“网卡2”，选择 “仅主机(Host-0nly)网络”，确定保存。

重新启动服务器，再次看一下服务器的网卡详情：

![]({{site.baseurl}}/images/Linux/20231028172221.png)

可以看到服务器的IP地址为：192.168.56.102。

在Win10的命令行中 ping 一下该地址，确认下主机是否可以访问服务器：

![]({{site.baseurl}}/images/Linux/20231028173128.png)

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

![]({{site.baseurl}}/images/Linux/20231028173033.png)

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

## PHP安装









<br/><br/><br/><br/><br/>
## 参考资料

什么是 SSH 访问？你需要知道的一切 <https://baijiahao.baidu.com/s?id=1734399066815684825>

