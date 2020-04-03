---
layout: post
categories: Linux
title: linux 安装redis服务
meta: linux 安装redis服务
---
* content
{:toc}

### 正文

安装：

> sudo apt-get update

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

<br/><br/><br/><br/><br/>
### 参考资料

runoob Redis 安装 <https://www.runoob.com/redis/redis-install.html>

Ubuntu下用apt-get安装最新版本的Redis <https://blog.csdn.net/txj236/article/details/81413407>
