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

重启：
> reboot

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

在主机用 putty 连接虚拟机。

<br/><br/><br/><br/><br/>
## 参考资料

小白速进，五小时linux速成班 centos8安装 <https://www.bilibili.com/video/BV1ey4y1S7Q6?p=6>

