---
layout: post
categories: Linux
title: linux 服务环境docker实现
meta: linux 服务环境docker实现
---
* content
{:toc}

### 正文

#### 引言

个人使用的是Linux环境做个人主机，也是因为对Linux不熟悉，各种摸索和折腾，光PHP就安装了好多个版本，而且里面的版本还升过版本，
导致服务环境非常乱，给PHP安装个拓展总是报错：拓展编码的phpize版本和PHP的版本不一致，导致拓展使用不了，折腾了好长时间无法解决。
算了，还是把这些很杂乱的版本就卸载了吧，居然卸载也卸载不了，因为PHP有的是apt安装的、有的是yum安装的、有的是编译安装的、有的还用apt升过级，
现在不知道哪个版本是用哪种方式安装的了，也没法卸载了，只能物理删除了。

查看下当前的PHP版本：

> php -v

看一下PHP的命令地址：

> which php

再看下php在哪里：

> whereis php

这里可以看到php散落在各个地方。

还好当时安装的都是php7的版本，我们搜一下php7开头的有哪些：

> find / -name 'php7*'

发现列出了很长的一个列表，既有7.0，又有7.1，还有7.4。既然无法一个一个卸载，那就物理删除吧：

> find / -name 'php7*'|xargs rm -rf

操作完成后，再搜索看一下：

> find / -name 'php7*'

接下来就是一些很琐碎的相关文件删除了，先大致搜索看看：

> find / -name php.ini

> find / -name php

追踪看看，能删的就删了吧，注意别把自己的项目开发文件给删了！

#### 

可以看到，一个系统安装很多版本导致的问题，会影响到系统的正常使用，如果安装时只安装一个版本就好了。
或者现在有一种解决方案，就是用docker，一个container中运行一个服务、一个版本，这样以后想尝试多版本也有办法了。



<br/><br/><br/><br/><br/>
### 参考资料

搭建TCP-IP实验环境 <https://ibaiyang.github.io/blog/it%E6%8A%80%E6%9C%AF/2019/12/03/%E6%90%AD%E5%BB%BATCP-IP%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83.html>

Linux docker安装配置 <https://ibaiyang.github.io/blog/linux/2019/12/19/linux-docker%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE.html>
