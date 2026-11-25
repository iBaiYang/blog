---
layout: post
categories: Docker
title: WSL2中Docker安装LNMP
meta: WSL2中Docker安装LNMP
---
* content
{:toc}

## 正文

WSL2的安装可以看一下 <https://ibaiyang.github.io/blog/linux/2021/08/28/Win10家庭版WSL2安装Centos7.8.html>。
作为对照，可以看一下 <https://ibaiyang.github.io/blog/docker/2026/06/20/Docker桌面版安装LNMP.html> 和 <https://ibaiyang.github.io/blog/docker/2026/08/10/Docker桌面版Compose安装LNMP.html>。

可以在 微软商店中搜索 Ubuntu 进行安装。安装后系统的根目录在 `\\wsl.localhost\Ubuntu` 下，可以在 `\\wsl$` 下看到入口。

`wsl -d Ubuntu` 进入 Ubuntu 环境，初始需要设置用户名和密码，密码可以简单一些，比如 123456。

`hostnamectl` 查看版本。

过程记录：
```
C:\Users\Administrator>wsl -l -v
  NAME              STATE           VERSION
* docker-desktop    Running         2
  Ubuntu            Stopped         2

C:\Users\Administrator>wsl -d Ubuntu
Provisioning the new WSL instance Ubuntu
This might take a while...
Create a default Unix user account: admin
fatal: The group `admin' already exists.
Failed to create user 'admin'. Please choose a different name.
Create a default Unix user account: administrator
New password:
Retype new password:
passwd: password updated successfully
usermod: no changes
Help improve Ubuntu!

Help us improve Ubuntu features and compatibility by sharing system reports with Canonical.
Reports are sent anonymously and do not contain any personal data.
For legal details, please visit: https://ubuntu.com/legal/systems-information-notice

We will save your answer to Windows and will only ask you once.

Would you like to opt-in to platform metrics collection (Y/n)? To see an example of the data collected, enter 'e'.
[Y/n/e]: n
Welcome to Ubuntu 26.04.1 LTS (GNU/Linux 6.18.33.2-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://docs.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed Nov 25 13:48:25 CST 2026

  System load:  0.05                Processes:             40
  Usage of /:   0.1% of 1006.85GB   Users logged in:       0
  Memory usage: 6%                  IPv4 address for eth0: 172.21.61.8
  Swap usage:   0%


This message is shown once a day. To disable it please create the
/home/administrator/.hushlogin file.
administrator@PC-20260803TFLF:/mnt/c/Users/Administrator$
administrator@PC-20260803TFLF:/mnt/c/Users/Administrator$ hostnamectl
 Static hostname: PC-20260803TFLF
       Icon name: computer-container
         Chassis: container ☐
      Machine ID: a283140410e4402687e3f7c7bf74cc00
         Boot ID: 71024d0d89fc4543a7fc51aae47ea502
  Virtualization: wsl
Operating System: Ubuntu 26.04.1 LTS
          Kernel: Linux 6.18.33.2-microsoft-standard-WSL2
    Architecture: x86-64
administrator@PC-20260803TFLF:/mnt/c/Users/Administrator$
administrator@PC-20260803TFLF:/mnt/c/Users/Administrator$ cd ~
administrator@PC-20260803TFLF:~$
administrator@PC-20260803TFLF:~$ pwd
/home/administrator
administrator@PC-20260803TFLF:~$
administrator@PC-20260803TFLF:~$ ping www.baidu.com
PING www.a.shifen.com (36.152.44.132) 56(84) bytes of data.
64 bytes from 36.152.44.132: icmp_seq=1 ttl=50 time=11.5 ms
64 bytes from 36.152.44.132: icmp_seq=2 ttl=50 time=13.4 ms
64 bytes from 36.152.44.132: icmp_seq=3 ttl=50 time=11.3 ms
64 bytes from 36.152.44.132: icmp_seq=4 ttl=50 time=10.3 ms
64 bytes from 36.152.44.132: icmp_seq=5 ttl=50 time=11.2 ms
^C
--- www.a.shifen.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4044ms
rtt min/avg/max/mdev = 10.349/11.546/13.416/1.012 ms
administrator@PC-20260803TFLF:~$
administrator@PC-20260803TFLF:~$
```





<br/><br/><br/><br/><br/>
## 参考资料



