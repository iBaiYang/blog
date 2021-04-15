---
layout: post
categories: Linux
title: vagrant js/css文件缓存及乱码解决
meta: 用vagrant搭建虚拟机进行开发，有时在nginx环境下，发现js/css等没有及时刷新，有时又报乱码，这里解决一下这个问题。
---
* content
{:toc}

### 正文

用vagrant搭建虚拟机进行开发，有时在nginx环境下，发现js/css等没有及时刷新，有时又报乱码：

![]({{site.baseurl}}/images/20210415/20210415104061.jpeg)

我们需要修改nginx的配置：

![]({{site.baseurl}}/images/20210415/20210415104063.jpeg)

![]({{site.baseurl}}/images/20210415/20210415104065.jpeg)

然后ok。

#### sendfile

配置示例

```
http ｛
    # other directives
    sendfile    on;
    # other directives
｝
```

指令说明

    语法： sendfile on | off;
    默认值： sendfile off;
    上下文： http，server，location，if in location

指定是否使用sendfile系统调用来传输文件。

sendfile系统调用在两个文件描述符之间直接传递数据(完全在内核中操作)，
从而避免了数据在内核缓冲区和用户缓冲区之间的拷贝，操作效率很高，被称之为零拷贝。

**read/write**

在传统的文件传输方式（read、write/send方式），具体流程细节如下：

* 调用read函数，文件数据拷贝到内核缓冲区
* read函数返回，数据从内核缓冲区拷贝到用户缓冲区
* 调用write/send函数，将数据从用户缓冲区拷贝到内核socket缓冲区
* 数据从内核socket缓冲区拷贝到协议引擎中

在这个过程当中，文件数据实际上是经过了四次拷贝操作：

硬盘—>内核缓冲区—>用户缓冲区—>内核socket缓冲区—>协议引擎

**sendfile**

sendfile系统调用则提供了一种减少拷贝次数，提升文件传输性能的方法。

* sendfile系统调用利用DMA引擎将文件数据拷贝到内核缓冲区，之后数据被拷贝到内核socket缓冲区中
* DMA引擎将数据从内核socket缓冲区拷贝到协议引擎中

这里没有用户态和内核态之间的切换，也没有内核缓冲区和用户缓冲区之间的拷贝，大大提升了传输性能。

这个过程数据经历的拷贝操作如下：

硬盘—>内核缓冲区—>内核socket缓冲区—>协议引擎

**带有DMA收集拷贝功能的sendfile**

对于带有DMA收集拷贝功能的sendfile系统调用，还可以再减少一次内核缓冲区之间的拷贝。具体流程如下：

* sendfile系统调用利用DMA引擎将文件数据拷贝到内核缓冲区，之后，将带有文件位置和长度信息的缓冲区描述符添加到内核socket缓冲区中
* DMA引擎会将数据直接从内核缓冲区拷贝到协议引擎中

这个过程数据经历的拷贝操作如下：

硬盘—>内核缓冲区—>协议引擎



<br/><br/><br/><br/><br/>
### 参考资料 

<https://blog.smdcn.net/article/1325.html>

<http://blog.csdn.net/backnet/article/details/38581813>

nginx中配置sendfile及详细说明 <https://www.jianshu.com/p/70e1c396c320?utm_campaign>
