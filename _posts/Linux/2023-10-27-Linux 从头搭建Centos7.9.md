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

这里的虚拟机只是类似于刚出场未安装系统（如Win10）的裸机，只是提供硬件上的支持，这也是下面可能会报错的来源。
在安装Centos时才是安装运行的软件系统。有了这个认识后，下面安装应该会好理解一些。

在Centos官网下载iso安装包 [Centos download](http://isoredirect.centos.org/centos/7/isos/x86_64/) ，
选择一个下载速度比较快的源，下载后保存到指定位置，我这里下载的是 CentOS-7-x86_64-Minimal-2009.iso 包。

![]({{site.baseurl}}/images/Linux/20231027150306.png)

启动 VirtualBox，点击新建：





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

例如装了i5CPU的华硕电脑。
1. 开机时按 Del 键 进入BIOS，按 F7 进入 Advanced Mode（高级模式）
2. 在“Advanced Mode”（高级模式）下，按以下路径找到“Advanced”（高级）——“CPU Configuration”（CPU设置）
3. 下滑页面到底部，“Intel (VMX) Virtual Technology”（或者叫虚拟化技术），然后把“Disabled”（禁用）修改为“Enabled”（启用）。
4. 按“F10”键保存并退出，就成功开启VT了。

现在再重新启动一下这个虚拟机就应该没有问题了。

### Centos7.9安装













<br/><br/><br/><br/><br/>
## 参考资料



