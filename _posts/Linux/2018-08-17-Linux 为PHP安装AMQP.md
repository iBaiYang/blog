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

#### apt 安装

> sudo apt-get install php7.1-amqp

然后在php.ini配置文件中加入一行：
```
extension=amqp.so
```

这样就完成了amqp拓展的安装。

下面看下比较复杂的编译安装流程。

#### 编译安装

两个包：

1.rabbitmq-c的包
 
2.amqp的包

##### 安装rabbitmq-c

php的amqp依赖rabbitmq-c，所以需要先安装rabbitmq-c。

我们可以去访问[https://github.com/alanxz/rabbitmq-c](https://github.com/alanxz/rabbitmq-c)查看最新稳定版是哪一版，
然后点击下载或wget下载。

点击[稳点版链接](https://github.com/alanxz/rabbitmq-c/releases/latest)，看到是rabbitmq-c v0.9.0版本，
我这里选择wget下载，所以需要知道该版本的网络地址：https://github.com/alanxz/rabbitmq-c/archive/v0.9.0.tar.gz

putty登录后，切换成超级管理员：

> sudo su

cd到软件资源目录下：

> cd /usr/local/src/

下载rabbitmq-c v0.9.0：

> wget https://github.com/alanxz/rabbitmq-c/archive/v0.9.0.tar.gz

然后解压：

> tar zxvf v0.9.0.tar.gz

如果没有安装cmake，要安装cmake：

> yum install cmake

看一下解压后文件夹的名字：

> ls -l

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


##### 安装amqp

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

用find / -name phpize查找命令路径：

> find / -name phpize

phpize化：

> phpize

配置：

```
./configure --with-php-config=/usr/bin/php-config --with-amqp --with-librabbitmq-dir=/usr/local/src/rabbitmq-c-0.9.0/
```

编译安装：

> make && make install

![]({{site.baseurl}}/images/20200330/20200330191560.jpeg)

查看一下php.ini的位置：
 
> find / -name php.ini

修改php.ini文件：

> vi /etc/php.ini

添加 extension=amqp.so：

> extension=amqp.so

完成。

##### 截图

rabbitmq-c：

![]({{site.baseurl}}/images/20200330/20200330191539.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191540.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191541.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191542.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191548.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191549.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191550.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191551.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191552.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191553.jpeg)

amqp：

![]({{site.baseurl}}/images/20200330/20200330191543.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191544.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191545.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191546.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191547.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191554.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191555.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191556.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191557.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191558.jpeg)

![]({{site.baseurl}}/images/20200330/20200330191559.jpeg)

<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ C AMQP client library <https://github.com/alanxz/rabbitmq-c#rabbitmq-c-amqp-client-library>

<https://www.cnblogs.com/jie-fang/p/7885924.html>

<http://www.mamicode.com/info-detail-2277517.html>

configure配置安装详解 <https://blog.csdn.net/u010977122/article/details/52959098>

<https://segmentfault.com/a/1190000012348103>