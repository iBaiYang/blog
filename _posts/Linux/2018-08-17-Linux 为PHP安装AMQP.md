---
layout: post
categories: Linux
title: Linux 为PHP安装AMQP
meta: AMQP，即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。
---
* content
{:toc}

### 正文

AMQP，即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件设计。
基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。

我们这里环境是CentOS，看一下安装过程。

两个包：

1.rabbitmq-c的包
 
2.amqp的包

#### 安装rabbitmq-c

php的amqp依赖rabbitmq-c，所以需要先安装rabbitmq-c。

我们可以去访问[https://github.com/alanxz/rabbitmq-c](https://github.com/alanxz/rabbitmq-c)查看最新稳定版是哪一版，
然后点击下载或wget下载。

点击[稳点版链接](https://github.com/alanxz/rabbitmq-c/releases/latest)，看到是rabbitmq-c v0.9.0版本，
我这里选择wget下载，所以需要知道该版本的网络地址：https://github.com/alanxz/rabbitmq-c/archive/v0.9.0.tar.gz

putty登录后，切换成超级管理员：

> sudo su

cd到用户目录下：

> cd /usr/local/src/

下载rabbitmq-c v0.9.0：

> wget https://github.com/alanxz/rabbitmq-c/archive/v0.9.0.tar.gz

然后解压：

> tar zxvf v0.9.0.tar.gz

如果没有安装cmake，要安装cmake：

> yum install cmake

看一下解压后文件夹的名字：

> ll

发现是rabbitmq-c-0.9.0，进入该文件夹：

> cd rabbitmq-c-0.9.0

生成build文件夹并进入：

> mkdir build && cd build

在当前目录下生成安装库：

> cmake -DCMAKE_INSTALL_PREFIX=/usr/local/src/rabbitmq-c-0.9.0/ ..

cmake编译：

> cmake ..

安装：

```
cmake --build . --target install
```

成功。

后面安装amqp时会到/rabbitmq-c-0.9.0/lib下找搜索依赖库，然而我们这里是lib64，所以我们需要建立链接：

> cd /usr/local/src/rabbitmq-c-0.9.0/

> ln -s lib64 lib


#### 安装amqp

我们先要访问[http://pecl.php.net/package/amqp](http://pecl.php.net/package/amqp)，看看最新的稳定版本是哪一个。
记下来。比如：amqp-1.9.3.tgz。

然后下载，记得回到/usr/local/src/目录下：

> cd /usr/local/src/

> wget pecl.php.net/get/amqp-1.9.3.tgz

解压：

> tar zxvf amqp-1.9.3.tgz

先安装m4：

> yum install -y m4 autoconf

进入amqp-1.9.3目录：

> cd amqp-1.9.3

phpize化：

> phpize

用find / -name phpize查找命令路径：

> find / -name phpize

配置：

```
./configure --with-php-config=/usr/bin/php-config --with-amqp --with-librabbitmq-dir=/usr/local/src/rabbitmq-c-0.9.0/
```

编译安装：

> make && make install

![](http://s7.sinaimg.cn/mw690/001XbchKzy7nzANESTsd6&690)

查看一下php.ini的位置：
 
> find / -name php.ini

修改php.ini文件：

> vi /etc/php.ini

添加 extension=amqp.so：

> extension=amqp.so

完成。

### 截图

rabbitmq-c：

![](http://s2.sinaimg.cn/mw690/001XbchKzy7mU02WAG511&690)

![](http://s3.sinaimg.cn/mw690/001XbchKzy7mU031rvI42&690)

![](http://s15.sinaimg.cn/mw690/001XbchKzy7mU03lP7E2e&690)

![](http://s9.sinaimg.cn/mw690/001XbchKzy7mU03bDAs48&690)

![](http://s16.sinaimg.cn/mw690/001XbchKzy7mU03JdDN9f&690)

![](http://s1.sinaimg.cn/mw690/001XbchKzy7mU03MXrW60&690)

![](http://s8.sinaimg.cn/mw690/001XbchKzy7nzoBcpkr87&690)

![](http://s14.sinaimg.cn/mw690/001XbchKzy7nzuI38UR0d&690)

![](http://s6.sinaimg.cn/mw690/001XbchKzy7nzuJby4Ja5&690)

![](http://s6.sinaimg.cn/mw690/001XbchKzy7nzuJBdXfb5&690)

amqp：

![](http://s11.sinaimg.cn/mw690/001XbchKzy7mU03fmZkea&690)

![](http://s6.sinaimg.cn/mw690/001XbchKzy7mU03zpRP85&690)

![](http://s16.sinaimg.cn/mw690/001XbchKzy7mU03tBzp6f&690)

![](http://s1.sinaimg.cn/mw690/001XbchKzy7mU03wmycd0&690)

![](http://s6.sinaimg.cn/mw690/001XbchKzy7mU03PpoV05&690)

![](http://s1.sinaimg.cn/mw690/001XbchKzy7nzxCDirC40&690)

![](http://s16.sinaimg.cn/mw690/001XbchKzy7nzxD0Vbh2f&690)

![](http://s5.sinaimg.cn/mw690/001XbchKzy7nzxDzUj2f4&690)

![](http://s11.sinaimg.cn/mw690/001XbchKzy7nzxEIPfsca&690)

![](http://s12.sinaimg.cn/mw690/001XbchKzy7nzyV9FBF0b&690)

![](http://s10.sinaimg.cn/mw690/001XbchKzy7nzyVNV5T59&690)

<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ C AMQP client library <https://github.com/alanxz/rabbitmq-c#rabbitmq-c-amqp-client-library>

<https://www.cnblogs.com/jie-fang/p/7885924.html>

<http://www.mamicode.com/info-detail-2277517.html>

configure配置安装详解 <https://blog.csdn.net/u010977122/article/details/52959098>

<https://segmentfault.com/a/1190000012348103>