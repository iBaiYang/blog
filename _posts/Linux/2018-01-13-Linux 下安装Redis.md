---
layout: post
categories: Linux
title: Linux 下安装Redis
meta: 记录一下 Linux 下安装Redis流程
---
* content
{:toc}

### 正文

#### Linux 源码安装

> cd /usr/local/src/
>
> wget http://download.redis.io/releases/redis-4.0.6.tar.gz
> 
> tar xzf redis-4.0.6.tar.gz
> 
> cd redis-4.0.6
>
> make

安装成功

![]({{site.baseurl}}/images/20210112/20210112125424.jpg)

![]({{site.baseurl}}/images/20210112/20210112125425.jpg)

![]({{site.baseurl}}/images/20210112/20210112125426.jpg)

接下来开启服务：
> src/redis-server

![]({{site.baseurl}}/images/20210112/20210112125427.jpg)

#### apt方式安装

安装：
> sudo apt-get update
>
> sudo apt-get install redis-server

启动：
> redis-server

```
22708:C 03 Apr 11:30:21.826 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
22708:M 03 Apr 11:30:21.827 * Increased maximum number of open files to 10032 (it was originally set to 1024).
22708:M 03 Apr 11:30:21.827 # Creating Server TCP listening socket *:6379: bind: Address already in use
```

查看 redis 是否启动：
> redis-cli

查看版本：
> redis-server -v

```
Redis server v=3.2.6 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=c9ca860b301a190d
```

#### Windows 版

Redis是一个基于内存亦可持久化的日志型、Key-Value的NoSQL数据库。

运行在windows上的Redis可以在这里下载：<https://github.com/ServiceStack/redis-windows>。下载完毕后，加压到你的安装目录下。

本次版本：

解压后如图：

![]({{site.baseurl}}/images/20201127/20201127100846.jpg)

然后设置redis的密码：找到# requirepass foobared 改为 requirepass 我的密码 ，如图：

![]({{site.baseurl}}/images/20201127/20201127100847.jpg)

修改后，保存。

接下来，cmd打开命令行进入安装目录下，输入：

> redis-server.exe redis.windows.conf

如图，回车后可看到运行成功：

![]({{site.baseurl}}/images/20201127/20201127100848.jpg)

这个命令行窗口不能关闭，一关闭，redis就停止服务。

你可以测试一下redis可用不，另外打开一个cmd命令行窗口，如图输入查看：

![]({{site.baseurl}}/images/20201127/20201127100849.jpg)

现在，你可以在代码中使用redis服务了。


#### 拓展

linux下安装教程：

<http://www.runoob.com/redis/redis-install.html>

<http://www.cnblogs.com/silent2012/p/3499654.html>

给php安装redis拓展，可以参考：

<http://www.cnblogs.com/zgaspnet/p/3939198.html>

不过仅是参考而已，不要照着做，应该想LNMP那里安装swoole拓展一样安装：

<http://blog.sina.com.cn/s/blog_6aba78b40102x297.html>

redis拓展包在这里下载：

<https://github.com/nicolasff/phpredis/archive/3.1.4.tar.gz>

最新版在这里看：

<https://github.com/phpredis/phpredis/releases>


    
<br/><br/><br/><br/><br/>
### 参考资料

redis 官网下载 <https://redis.io/download>

Redis安装和自启动配置 <https://www.cnblogs.com/zhxilin/p/5892678.html>

[Redis官网](https://redis.io/)

[Redis中文网](http://www.redis.cn/)

<http://blog.csdn.net/jintiaozhuang/article/details/53150832>

[redis配置文件redis.conf中文版](http://www.jb51.net/article/50605.htm)

runoob Redis 安装 <https://www.runoob.com/redis/redis-install.html>

Ubuntu下用apt-get安装最新版本的Redis <https://blog.csdn.net/txj236/article/details/81413407>
