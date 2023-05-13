---
layout: post
categories: Linux
title: VirtualBox 使用
meta: 
---
* content
{:toc}

## 引言

尽管Windows上有WSL，但对于运行于Linux环境上的项目，体验还是太差了，好多功能无法正常使用，被迫还是要在Linux环境下开发，
现在VirtualBox就派上用场了。

## ln无法创建软连接解决方案

有需求要用到 ln -sf 源地址 目标地址，但是尝试后总是报错，最后追溯的原因VirtualBox不支持，这里写一下解决方案。

自己电脑win10上为了装linux，装了VirtualBox，linux可以用了。

现在碰到一个业务场景需要用ln创建软连接，使用命令：

> ln -sf 源地址 目标地址

然而却报错：

```
ln: creating symbolic link `/var/www/tmk/backend/config/main.php': Protocol error
```

ln语法没有问题，最后追溯发现是VirtualBox为了安全，不让用ln命令，怎么解决呢？

win10下命令行到VirtualBox安装目录下，查看有哪些虚拟机：

> VBoxManage list vms

复制这句话：

> VBoxManage setextradata YOURVMNAME VBoxInternal2/SharedFoldersEnableSymlinksCreate/YOURSHAREFOLDERNAME 1 

把YOURVMNAME替换为你虚拟机的名字；YOURSHAREFOLDERNAME替换为共享文件夹的名字，不是地址。执行一下：

> VBoxManage setextradata vagrant_default_1516794961060_79276 VBoxInternal2/SharedFoldersEnableSymlinksCreate/www 1

再次检验下：

> VBoxManage getextradata vagrant_default_1516794961060_79276 enumerate

过程图：

![]({{site.baseurl}}/images/20200330/20200330191561.jpeg)

然后 “以管理者身份运行” VirtualBox。

![]({{site.baseurl}}/images/20181218/20181218131035.jpg)

![]({{site.baseurl}}/images/20181218/20181218131522.jpg)


再去ln试一下：

![]({{site.baseurl}}/images/20200330/20200330191562.jpeg)

## VirtualBox内访问宿主机

开发中，碰到一个问题，在外部宿主机上安装了Redis，VirtualBox内部Linux上的项目需要访问宿主机的Redis获取数据，怎么连接呢？
连接的IP又是多少呢？

参阅下 <https://zhuanlan.zhihu.com/p/554893004>

默认宿主机的IP地址是 10.0.2.2 。

这里牵扯到多种网络连接方式：NAT、桥接（Bridged Adapter）、Internal、Host-Only Adapter。
具体可以参阅：
 
<http://www.taodudu.cc/news/show-201620.html?action=onClick>

<https://blog.csdn.net/bifengmiaozhuan/article/details/79887692>

<http://www.taodudu.cc/news/show-293394.html?action=onClick>

<https://blog.csdn.net/qq_39889944/article/details/128635229>

![]({{site.baseurl}}/images//20230513/20230513140925.png)

                         桥接	                     NAT	                                        host-only
    虚拟机与宿主机	    彼此互通，处于同一网段	    虚拟机能访问宿主机；宿主机不能访问虚拟机	    虚拟机不能访问宿主机；宿主机能访问虚拟机
    虚拟机与虚拟机	    彼此互通，处于同一网段	    彼此不通	                                    彼此互通，处于同一网段
    虚拟机与其他主机	彼此互通，处于同一网段	    虚拟机能访问其他主机；其他主机不能访问虚拟机	彼此不通；需要设置
    虚拟机与互联网	    虚拟机可以上网	            虚拟机可以上网	                                彼此不通；需要设置





<br/><br/><br/><br/><br/>
## 参考资料

<https://www.jianshu.com/p/9ef5ef856d54>

<https://blog.csdn.net/ganshuyu/article/details/44565157>

<https://blog.csdn.net/qq_24898865/article/details/81096418>

<https://www.cnblogs.com/davenkin/p/vagrant-virtualbox.html>

<https://jingyan.baidu.com/article/e73e26c0f87c2424adb6a7f1.html>

[Linux ln命令](http://www.runoob.com/linux/linux-comm-ln.html)
