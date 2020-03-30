---
layout: post
categories: Linux
title: VirtualBox ln无法创建软连接解决方案
meta: 有需求要用到 ln -sf 源地址 目标地址，但是尝试后总是报错，最后追溯的原因VirtualBox不支持，这里写一下解决方案。
---
* content
{:toc}

### 正文

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

![](http://s4.sinaimg.cn/mw690/001XbchKzy7oZdhpqnx03&690)

然后 “以管理者身份运行” VirtualBox。

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20181218/20181218131035.jpg)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20181218/20181218131522.jpg)


再去ln试一下：

![](http://s9.sinaimg.cn/orignal/001XbchKzy7oZdhM2eca8&690)

<br/><br/><br/><br/><br/>
### 参考资料

<https://www.jianshu.com/p/9ef5ef856d54>

<https://blog.csdn.net/ganshuyu/article/details/44565157>

<https://blog.csdn.net/qq_24898865/article/details/81096418>

<https://www.cnblogs.com/davenkin/p/vagrant-virtualbox.html>

<https://jingyan.baidu.com/article/e73e26c0f87c2424adb6a7f1.html>

[Linux ln命令](http://www.runoob.com/linux/linux-comm-ln.html)
