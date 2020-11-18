---
layout: post
categories: Linux
title: linux 安装webbench及服务压测
meta: linux 安装webbench及服务压测
---
* content
{:toc}

### 正文

性能测试工具目前最常见的有以下几种：ab、http_load、webbench、siege。下面讲一下webbench。

webbench最多可以模拟3万个并发连接去测试网站的负载能力，我们可以安装使用。

webbench依赖于ctags，在shell中只需输入ctags即知有没有安装，如果没有要先安装ctags。

#### ctags安装

安装比较简单：
> sudo apt-get install ctags  

#### webbench安装

进入到用户主目录下：
> cd ~

下载压缩包：
> wget http://www.ha97.com/code/webbench-1.5.tar.gz

解压缩：
> tar zxvf webbench-1.5.tar.gz

进入解压缩后的包：
> cd webbench-1.5

根据 Makefile文件 编译：
> sudo make

看一下Makefile文件内容：
```
CFLAGS?=	-Wall -ggdb -W -O
CC?=		gcc
LIBS?=
LDFLAGS?=
PREFIX?=	/usr/local
VERSION=1.5
TMPDIR=/tmp/webbench-$(VERSION)

all:   webbench tags

tags:  *.c
	-ctags *.c

install: webbench
	install -s webbench $(DESTDIR)$(PREFIX)/bin	
	install -m 644 webbench.1 $(DESTDIR)$(PREFIX)/man/man1	
	install -d $(DESTDIR)$(PREFIX)/share/doc/webbench
	install -m 644 debian/copyright $(DESTDIR)$(PREFIX)/share/doc/webbench
	install -m 644 debian/changelog $(DESTDIR)$(PREFIX)/share/doc/webbench

webbench: webbench.o Makefile
	$(CC) $(CFLAGS) $(LDFLAGS) -o webbench webbench.o $(LIBS) 

clean:
	-rm -f *.o webbench *~ core *.core tags
	
tar:   clean
	-debian/rules clean
	rm -rf $(TMPDIR)
	install -d $(TMPDIR)
	cp -p Makefile webbench.c socket.c webbench.1 $(TMPDIR)
	install -d $(TMPDIR)/debian
	-cp -p debian/* $(TMPDIR)/debian
	ln -sf debian/copyright $(TMPDIR)/COPYRIGHT
	ln -sf debian/changelog $(TMPDIR)/ChangeLog
	-cd $(TMPDIR) && cd .. && tar cozf webbench-$(VERSION).tar.gz webbench-$(VERSION)

webbench.o:	webbench.c socket.c Makefile

.PHONY: clean install all tar
```

安装：
> sudo make install

![]({{site.baseurl}}/images/20200609/20200609111105.png)

![]({{site.baseurl}}/images/20200609/20200609111156.png)

#### 压测

查看一下帮助：
> webbench -help

输出：

```
webbench [option]... URL
  -f|--force               Don't wait for reply from server.
  -r|--reload              Send reload request - Pragma: no-cache.
  -t|--time <sec>          Run benchmark for <sec> seconds. Default 30.
  -p|--proxy <server:port> Use proxy server for request.
  -c|--clients <n>         Run <n> HTTP clients at once. Default one.
  -9|--http09              Use HTTP/0.9 style requests.
  -1|--http10              Use HTTP/1.0 protocol.
  -2|--http11              Use HTTP/1.1 protocol.
  --get                    Use GET request method.
  --head                   Use HEAD request method.
  --options                Use OPTIONS request method.
  --trace                  Use TRACE request method.
  -?|-h|--help             This information.
  -V|--version             Display program version.
```

一般常用的是：
> webbench -c 500 -t 30 http://127.0.0.1/test.php

```
-c 500  并发数
-t 30  时间(秒)
```

![]({{site.baseurl}}/images/20200609/20200609111332.png)

Speed 为每分钟多少个请求，是根据Requests计算出来的

Requests 指定时间内成功多少个请求，失败多少个请求


<br/><br/><br/><br/><br/>
### 参考资料

网站性能Web压力测试工具webbench <http://www.ha97.com/533.html>

webbench 压力测试软件 <https://www.jianshu.com/p/dc1032b19c8d>

webbench进行web服务器压力测试思路 <https://blog.csdn.net/yanggd1987/article/details/40147985>

linux下Webbench的安装及使用 <https://blog.csdn.net/pamelay/article/details/77401374>

configure、make 和 make install 的区别 <https://www.cnblogs.com/pjl1119/p/9875507.html>

php方法压力测试 <https://cloud.tencent.com/developer/information/php%E6%96%B9%E6%B3%95%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95>

WebBench压力测试工具（详细源码注释+分析） <https://www.bbsmax.com/A/QV5ZDRn2zy/>

大家须知简单的php性能优化注意点 <https://www.jb51.net/article/77446.htm>
