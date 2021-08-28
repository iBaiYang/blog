---
layout: post
categories: Linux
title: Win10家庭版WSL2安装Centos
meta: Win10家庭版WSL2安装Centos
---
* content
{:toc}

## 正文

```
Windows PowerShell
版权所有 (C) Microsoft Corporation。保留所有权利。

尝试新的跨平台 PowerShell https://aka.ms/pscore6

PS C:\Windows\system32> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

部署映像服务和管理工具
版本: 10.0.19041.844

映像版本: 10.0.19043.1165

启用一个或多个功能
[==========================100.0%==========================]
操作成功完成。
PS C:\Windows\system32>
PS C:\Windows\system32>
PS C:\Windows\system32> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

部署映像服务和管理工具
版本: 10.0.19041.844

映像版本: 10.0.19043.1165

启用一个或多个功能
[==========================100.0%==========================]
操作成功完成。
PS C:\Windows\system32>
PS C:\Windows\system32>
PS C:\Windows\system32> systeminfo | find "System Type"
PS C:\Windows\system32>
PS C:\Windows\system32> systeminfo | find "系统类型"
FIND: 参数格式不正确
PS C:\Windows\system32> systeminfo

主机名:           BAIYANG-PC
OS 名称:          Microsoft Windows 10 家庭中文版
OS 版本:          10.0.19043 暂缺 Build 19043
OS 制造商:        Microsoft Corporation
OS 配置:          独立工作站
OS 构建类型:      Multiprocessor Free
注册的所有人:     zhuyingu@126.com
注册的组织:
产品 ID:          00342-33297-06537-AAOEM
初始安装日期:     2020/8/19, 21:57:21
系统启动时间:     2021/8/24, 20:46:12
系统制造商:       LENOVO
系统型号:         20FWA020CD
系统类型:         x64-based PC
处理器:           安装了 1 个处理器。
                  [01]: Intel64 Family 6 Model 94 Stepping 3 GenuineIntel ~2300 Mhz
BIOS 版本:        LENOVO R07ET72W (2.12 ), 2016/10/28
Windows 目录:     C:\Windows
系统目录:         C:\Windows\system32
启动设备:         \Device\HarddiskVolume1
系统区域设置:     zh-cn;中文(中国)
输入法区域设置:   zh-cn;中文(中国)
时区:             (UTC+08:00) 北京，重庆，香港特别行政区，乌鲁木齐
物理内存总量:     24,441 MB
可用的物理内存:   19,202 MB
虚拟内存: 最大值: 28,025 MB
虚拟内存: 可用:   22,608 MB
虚拟内存: 使用中: 5,417 MB
页面文件位置:     C:\pagefile.sys
域:               WORKGROUP
登录服务器:       \\BAIYANG-PC
修补程序:         安装了 14 个修补程序。
                  [01]: KB5004331
                  [02]: KB4561600
                  [03]: KB4570334
                  [04]: KB4576754
                  [05]: KB4577266
                  [06]: KB4577586
                  [07]: KB4580325
                  [08]: KB4586864
                  [09]: KB4589212
                  [10]: KB4593175
                  [11]: KB4598481
                  [12]: KB5000736
                  [13]: KB5005033
                  [14]: KB5005260
网卡:             安装了 4 个 NIC。
                  [01]: Intel(R) Ethernet Connection (2) I219-LM
                      连接名:      以太网
                      状态:        媒体连接已中断
                  [02]: Intel(R) Dual Band Wireless-AC 8260
                      连接名:      WLAN
                      启用 DHCP:   是
                      DHCP 服务器: 192.168.123.1
                      IP 地址
                        [01]: 192.168.123.163
                        [02]: fe80::b42d:38e0:7af1:eb6f
                  [03]: VirtualBox Host-Only Ethernet Adapter
                      连接名:      VirtualBox Host-Only Network
                      启用 DHCP:   否
                      IP 地址
                        [01]: 192.168.56.1
                        [02]: fe80::350e:3a2e:501b:4a41
                  [04]: TAP-Windows Adapter V9
                      连接名:      以太网 3
                      状态:        媒体连接已中断
Hyper-V 要求:     虚拟机监视器模式扩展: 是
                  固件中已启用虚拟化: 是
                  二级地址转换: 是
                  数据执行保护可用: 是
PS C:\Windows\system32>
```




<br/><br/><br/><br/><br/>
## 参考资料

适用于 Linux 的 Windows 子系统安装指南 (Windows 10) <https://docs.microsoft.com/zh-cn/windows/wsl/install-win10>

Windows Terminal + WSL2 + CENTOS 配置Windows命令终端 <https://zhuanlan.zhihu.com/p/347461016>


