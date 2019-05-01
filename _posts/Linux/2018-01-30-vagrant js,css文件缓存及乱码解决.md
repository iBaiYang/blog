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

![](http://s6.sinaimg.cn/mw690/001XbchKzy7hMgF2mHze5&690)

我们需要修改nginx的配置：

![](http://s1.sinaimg.cn/mw690/001XbchKzy7hMgIbfyM90&690)

![](http://s7.sinaimg.cn/mw690/001XbchKzy7hMgHnm6i56&690)

然后ok。


<br/><br/><br/><br/><br/>
### 参考资料 

<https://blog.smdcn.net/article/1325.html>

<http://blog.csdn.net/backnet/article/details/38581813>


