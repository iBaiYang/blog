---
layout: post
categories: Docker
title: docker之Dockerfile实践
meta: docker之Dockerfile实践
---
* content
{:toc}

## 图解

![]({{site.baseurl}}/images/20221010/20221010215527.png)

### Docker的网络配置

在生成自己的镜像前，需要先把docker的网络配置好，不然到时yum等网络操作不可用。

参见 <https://ibaiyang.github.io/blog/docker/2022/10/11/深入理解-Docker-网络原理.html>

docker安装后会自动创建3种网络:bridge、host、none。

我们查看一下：

> docker network ls

输出：
```
NETWORK ID          NAME                DRIVER              SCOPE
5e4777eb95b9        bridge              bridge              local
00d777100d89        host                host                local
3b27effdbb4f        none                null                local
```

Docker在启动时会开启一个虚拟网桥设备docker0，默认的地址为172.17.0.1/16，容器启动后都会被桥接到docker0上，并自动分配到一个ip地址。

查看docker0网桥：

> ip addr

输出：
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 50:7b:9d:62:fe:7c brd ff:ff:ff:ff:ff:ff
    inet 192.168.44.151/24 brd 192.168.44.255 scope global noprefixroute dynamic enp0s25
       valid_lft 23079sec preferred_lft 23079sec
    inet6 fe80::e8b7:c38e:b752:14ec/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: wlp4s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 30:52:cb:70:9f:23 brd ff:ff:ff:ff:ff:ff
4: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 2e:57:cc:13:5f:bc brd ff:ff:ff:ff:ff:ff
5: net2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 0e:72:c6:ce:fb:4d brd ff:ff:ff:ff:ff:ff
6: net1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 1a:b1:8b:e9:36:4e brd ff:ff:ff:ff:ff:ff
7: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:7b:2f:06:38 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:7bff:fe2f:638/64 scope link 
       valid_lft forever preferred_lft forever
129: veth7c0178d@if128: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether b6:ae:15:be:7d:9b brd ff:ff:ff:ff:ff:ff link-netnsid 2
    inet6 fe80::b4ae:15ff:febe:7d9b/64 scope link 
       valid_lft forever preferred_lft forever
```

可以看到第7条就是docker0。



## nginx镜像生成

在一个文件夹下（如：/home/test/nginx），新建Dockerfile文件：

> touch Dockerfile

写入内容：
```
# 基础镜像
FROM centos:7

# 维护者
MAINTAINER baiyang@email.com

# 安装wget下载工具
RUN yum install -y wget 

# 切换到该镜像的/usr/lcoal/src/目录，相当于cd，并可以用cd 代替， 但docker官方不建议用cd
WORKDIR /usr/local/src

# 添加远程文件到当前文件夹， 注意：后面有个点(.) 代表当前目录。ADD可以添加远程文件到镜像，但COPY仅可以添加本地文件到镜像中。
ADD http://nginx.org/download/nginx-1.18.0.tar.gz .

# RUN，在镜像内运行解压命令
RUN tar zxvf nginx-1.18.0.tar.gz

# 切换目录
WORKDIR /usr/local/src/nginx-1.18.0

# 更新yum，可不执行
# RUN yum -y update 

# 安装必要的软件
RUN yum install -y gcc gcc-c++ glibc make openssl-devel
RUN yum install -y libxslt-devel -y gd-devel GeoIP GeoIP-devel pcre pcre-devel

# 添加nginx用户
RUN useradd -M -s /sbin/nologin nginx

# 挂载卷，测试用例（这里的挂载卷，不可以指定本机的目录，不够灵活，一般会在启动容器时通过 -v 参数指定挂载卷，或在docker-compose.yaml文件中指定，都可以指定本地目录）
# VOLUME ["/data"]

# 编译安装nginx
RUN ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-file-aio --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_degradation_module --with-http_stub_status_module && make && make install

# 切换到Nginx的配置目录
WORKDIR /usr/local/nginx/conf

# 建立子配置文件夹，可以不建，或者叫其它名称都可以，但最好不要带特殊符号
# RUN mkdir vhost

# 设置变量，执行命令时，就可以省略前缀目录了	
ENV PATH /usr/local/nginx/sbin:$PATH

# 暴露端口
EXPOSE 80

# the command of entrypoint，容器启动时，执行的命令
ENTRYPOINT ["nginx"]

# 容器启动时，默认的执行命令，数组形式， "-g daemon off;" 使我们运行容器时，容器可以前台运行，不会退出
CMD ["-g", "daemon off;"]
```

然后生成镜像：
```
docker build --network host -t centos7_nginx:1.18.0 .
```

注意，最后有个点，centos7_nginx 是镜像名称，1.18.0 是打的标签，跟版本号一样的意思

`-t` 或者 `--tag`： 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。

`--network`: 默认 default。在构建期间设置RUN指令的网络模式

查看生成的镜像：

> docker images

启动一个容器：
```
docker run --name=test_nginx --privileged=true -p 81:80 -d centos7_nginx:1.18.0
```

`--name` 容器名称；
`--privileged` 特权模式；
`-p`  映射端口号，宿主机端口：容器端口；
`-d`  是守护进程运行的意思，即容器后台运行不会退出；
最后的 `centos7_nginx:1.18.0` 是使用的镜像：版本号。

查看所有容器列表：

> docker ps -a 

命令行执行`curl 127.0.0.1:81` 命令，能看到  Welcome to nginx  等英文提示，即说明一切OK。

## 集合一

更多 Dockerfile 实例，参见 <https://github.com/jianyan74/dockerfiles> ，看一下这里面相关Dockerfile的使用。
里面的涉及到的应用有：Elasticsearch、filebeat、Kafka、Kibana、Logstash、metricbeat、mongo、Mysql、Nginx、PHP、RabbitMQ、Redis、zabbix 等。
另外这里面的各个应用的说明文档也整理的挺好。

### Nginx

Dockerfile 文件内容：
```
ARG NGINX_VER

FROM nginx:${NGINX_VER}

# 设置时区为上海
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Ubuntu软件源选择中国的服务器
RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

# 更新安装依赖包
RUN apt-get update && apt-get install -y \
        vim \
        wget

# 安装模块

# unzip nginx-upstream-fair-master.zip #解压
# mv nginx-upstream-fair-master nginx-upstream-fair #重命名

#配置
#./configure \
#--prefix=/usr/sbin/nginx \
#--with-http_ssl_module \
#--add-module=/root/nginx-upstream-fair
#编译安装
#make && make install
```

docker-compose.yml 文件内容：
```
version: '3.0'
services:
  nginx: # 相关文档 https://hub.docker.com/_/nginx
    container_name: nginx
    build:
      context: .
      args:
        - NGINX_VER=1.17
    ports:
      - "80:80"
      - "8080:8080"
      - "443:443"
    volumes:
      - ../../app/php:/data/www:rw
      - ../../logs/nginx:/var/log/nginx
      - ./conf.d:/etc/nginx/conf.d:ro
      - ./certs/:/etc/nginx/certs
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    restart: always
    command: nginx -g 'daemon off;'
```

### PHP

Dockerfile 文件内容：
```
ARG PHP_VER

# 镜像包
FROM php:${PHP_VER}

ARG SWOOLE_VER
ARG COMPOSER_URL

# 设置时区为上海
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Ubuntu软件源选择中国的服务器
RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

# 更新安装依赖包
RUN apt-get update && apt-get install -y \
        git \
        vim \
        curl \
        wget \
        openssl \
        imagemagick \
        libmagickwand-dev \
        libmagickcore-dev \
        libzip-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libxml2-dev \
		libssl-dev \
		librabbitmq-dev

# 安装PHP核心拓展
RUN docker-php-ext-install \
        pdo_mysql \
        opcache \
        mysqli \
        intl \
        exif \
        zip \
        bcmath \
	&& docker-php-ext-configure gd \
        --with-freetype-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && rm -r /var/lib/apt/lists/*

# pecl安装amqp、mongodb、redis扩展
RUN pecl install -o -f mongodb amqp redis imagick  \
    && rm -rf /tmp/pear

# 加入php扩展
RUN docker-php-ext-enable mongodb amqp redis imagick

# php swoole扩展
RUN cd /usr/src \
    && pecl download swoole-${SWOOLE_VER} \
    && tar -zxvf swoole-${SWOOLE_VER}.tgz \
    && cd swoole-${SWOOLE_VER} \
    && phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config --enable-openssl \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini \
    && rm -rf swoole-${SWOOLE_VER}.tgz \
    && rm -rf swoole-${SWOOLE_VER}

# 安装 Composer
ENV COMPOSER_HOME /root/composer

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

RUN composer config -g repo.packagist composer ${COMPOSER_URL}

RUN composer global require "fxp/composer-asset-plugin:^1.4.0"

WORKDIR /data

# 写权限
RUN usermod -u 1000 www-data

EXPOSE 9000
```

docker-compose.yml 文件内容：
```
version: '3.0'
services:
  php-fpm: # 相关文档 https://hub.docker.com/_/php
    container_name: php-fpm
    build:
      context: .
      args:
        - PHP_VER=7.3-fpm # php版本
        - SWOOLE_VER=4.4.5 # swoole版本
        - COMPOSER_URL=https://mirrors.aliyun.com/composer/ # composer源url
    ports:
      - "9000:9000"
    volumes:
      - ../../app/php:/data/www:rw
      - ../../logs/php:/var/log/php:rw
      - ./php.ini:/usr/local/etc/php/php.ini:ro # 当前php配置文件；可以拷贝修改php-dev.ini为想要的配置
      - ./php-fpm.conf:/usr/local/etc/php-fpm.conf:ro
    restart: always
    command: php-fpm
```

## ENTRYPOINT 与 CMD 的区别

看完解说一，再看解说二，会有更深的理解。

参阅 <https://blog.csdn.net/weixin_43300291/article/details/103874801>

### 解说一

**CMD**

这个命令是用来做什么的？下面是官网的答案：

The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable, 
or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.

意思是，cmd给出的是一个容器的默认的可执行体。也就是容器启动以后，默认的执行的命令。重点就是这个“默认”。
意味着，如果docker run没有指定任何的执行命令或者dockerfile里面也没有entrypoint，那么，
就会使用cmd指定的默认的执行命令执行。同时也从侧面说明了entrypoint的含义，它才是真正的容器启动以后要执行命令。

所以这句话就给出了cmd命令的一个角色定位，它主要作用是默认的容器启动执行命令。（注意不是“全部”作用）

这也是为什么大多数网上博客论坛说的“cmd会被覆盖”，其实为什么会覆盖？因为cmd的角色定位就是默认，
如果你不额外指定，那么就执行cmd的命令，否则呢？只要你指定了，那么就不会执行cmd，也就是cmd会被覆盖。
　　
明白了CMD命令的主要用途。下面就看看具体用法。

总共有三种用法：
１. `CMD [“executable”,“param1”,“param2”]` (exec form, this is the preferred form)
２. `CMD [“param1”,“param2”]` (as default parameters to ENTRYPOINT)
３. `CMD command param1 param2` (shell form)

因为还没有讲 ENTRYPOINT，所以先不看用法2。 

用法3：shell form，即没有中括号的形式。那么命令command默认是在 `/bin/sh -c`下执行的。

比如下面的dockerfile：
```
# vi Dockerfile
FROM ubuntu:18.04
CMD echo “hello cmd!”
#
# docker build -t hello .
# docker run hello
hello cmd!
```

用法1：带有中括号的形式。这时，命令没有在任何shell终端环境下，如果我们要执行shell，必须把shell加入到中括号的参数中。
这种用法就像一个c语言的exec函数，意思是我们要执行一个进程。如果采用非shell的方法，那么上面的例子要修改为：
```
# vi Dockerfile
FROM ubuntu:18.04
CMD ["/bin/bash", “-c”, “echo ‘hello2 cmd!’”]
#
# docker build -t hello2 .
# docker run hello2
hello2 cmd!
```

需要注意，采用中括号形式，那么第一个参数必须是命令的全路径才行。
而且，一个dockerfile至多只能有一个cmd，如果有多个，只有最后一个生效。官网推荐采用这种方法。

当然，以上都是体现了cmd的“默认”行为。如果我们在run时指定了命令或者有 entrypoint，那么cmd就会被覆盖。
仍然是上面的image。run命令变了：
```
# docker run hello2 echo abcdefg
abcdefg
```

可以看到，最终容器里面执行的是run命令后面的命令，而不是cmd里面定义的。

**ENTRYPOINT**

An ENTRYPOINT allows you to configure a container that will run as an executable.

也就是说entrypoint才是正统地用于定义容器启动以后的执行体的，其实我们从名字也可以理解，这个是容器的“入口”。

有两种用法：
1. `ENTRYPOINT [“executable”, “param1”, “param2”]` (exec form, preferred)
2. `ENTRYPOINT command param1 param2` (shell form)

命令行和shell。

先看命令行模式，也就是带中括号的。和cmd的中括号形式是一致的，但是是在shell的环境下执行的，
与cmd（不在shell的环境下运行）有区别。如果run命令后面有东西，那么后面的全部都会作为entrypoint的参数。
如果run后面没有额外的东西，但是cmd有，那么cmd的全部内容会作为entrypoint的参数，这同时是cmd的第二种用法。
这也是网上说的entrypoint不会被覆盖。当然如果要在run里面覆盖，也是有办法的，使用`--entrypoint`即可。

下面看几个例子。
```
# vi Dockerfile
FROM ubuntu:18.04
CMD [“p in cmd”]
ENTRYPOINT [“echo”]

#
# docker build -t hello3 .
```

如果run不带参数：
```
# docker run hello3
p in cmd
```

如果run带参数：
```
# docker run hello3 abcdefg
abcdefg
```

而且，确实entrypoint的中括号形式下，command是在shell环境下运行的，否则这里的echo是无法被执行的。

第二种是shell模式的。在这种模式下，任何run和cmd的参数都无法被传入到entrypoint里。官网推荐第一种用法。

修改文件：
```
# vi Dockerfile
FROM ubuntu:18.04
CMD [“p in cmd”]
ENTRYPOINT echo

#
# docker build -t hello4 .
```

如果run不带参数：
```
# docker run hello4
空
```

如果run带参数：
```
# docker run hello4 abcdefg
空
```

cmd的参数没有被打印。

总结下一般该怎么使用：一般还是会用entrypoint的中括号形式作为docker 容器启动以后的默认执行命令，
里面放的是不变的部分，可变部分比如命令参数可以使用cmd的形式提供默认版本，也就是run里面没有任何参数时使用的默认参数。
如果我们想用默认参数，就直接run，否则想用其他参数，就run 里面加参数。

### 解说二

CMD 和 ENTRYPOINT 指令都是用来指定容器启动时运行的命令。

单从功能上来看，这两个命令几乎是重复的。单独使用其中的一个就可以实现绝大多数的用例。但是既然 doker 同时提供了它们，
为了在使用中不至于混淆，本文试图把它们的用法理清楚。下面话不多说了，来一起看看详细的介绍吧。

exec 模式和 shell 模式

CMD 和 ENTRYPOINT 指令都支持 exec 模式和 shell 模式的写法，所以要理解 CMD 和 ENTRYPOINT 指令的用法，
就得先区分 exec 模式和 shell 模式。这两种模式主要用来指定容器中的不同进程为 1 号进程。
了解 linux 的朋友应该清楚 1 号进程在系统中的重要地位。1 号进程对容器中信号处理的重要性，感兴趣的朋友可以进行了解。
下面我们通过 CMD 指令来学习 exec 模式和 shell 模式的特点。

**exec 模式**

使用 exec 模式时，容器中的任务进程就是容器内的 1 号进程，看下面的例子：
```
FROM ubuntu:18.04
CMD [ “top” ]
```

把上面的代码保存到 test1 目录的 Dockerfile 中，然后进入 test1 目录构建镜像并启动一个容器：
```
$ docker build -t test1 .
$ docker run -idt --name testcon test1
```

然后查看容器中的进程 ID：
```
$ docker exec testcon ps aux
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.2 0.0 36592 3040 ? Ss+ 02:06 0:00 top
root 7 0.0 0.0 34396 2808 ? Rs 02:06 0:00 ps aux
```

从图中我们看到运行 top 命令的进程 ID 为 1。

exec 模式是建议的使用模式，因为当运行任务的进程作为容器中的 1 号进程时，我们可以通过 docker 的 stop 命令优雅的结束容器(详情请参考《在 docker 容器中捕获信号》)。

exec 模式的特点是不会通过 shell 执行相关的命令，所以像 $HOME 这样的环境变量是取不到的：
```
FROM ubuntu:18.04
CMD [ “echo”, “$HOME” ]
```

把上面的代码保存到 test1 目录的 Dockerfile 中，然后进入 test1 目录构建镜像并启动一个容器：
```
$ docker build --no-cache -t test1 .
$ docker run --rm test1
$HOME
```

通过 exec 模式执行 shell 可以获得环境变量：
```
FROM ubuntu:18.04
CMD [ “sh”, “-c”, “echo $HOME” ]
```

把上面的代码保存到 test1 目录的 Dockerfile 中，然后进入 test1 目录构建镜像并启动一个容器：
```
$ docker build --no-cache -t test1 .
$ docker run --rm test1
/root
```

这次正确取到了 $HOME 环境变量的值。

**shell 模式**

使用 shell 模式时，docker 会以 /bin/sh -c “task command” 的方式执行任务命令。也就是说容器中的 1 号进程不是任务进程而是 bash 进程，看下面的例子：
```
FROM ubuntu:18.04
CMD top
```

把上面的代码保存到 test2 目录的 Dockerfile 中，然后进入 test2 目录构建镜像并启动一个容器：
```
$ docker build -t test2 .
$ docker run -itd --name testcon2 test2
```

然后查看容器中的进程 ID：
```
$ docker exec testcon2 ps aux
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.2 0.0 4624 868 ? Ss+ 02:39 0:00 /bin/sh -c top
root 7 0.0 0.0 36592 3012 ? S+ 02:39 0:00 top
root 8 0.0 0.0 34396 2836 ? Rs 02:39 0:00 ps aux
```

1 号进程执行的命令居然是/bin/sh -c top。而我们指定的 top 命令的进程 ID 为 7。这是由 docker 内部决定的，目的是让我们执行的命令或者脚本可以取到环境变量。

**CMD 指令**

CMD 指令的目的是：为容器提供默认的执行命令。

CMD 指令有三种使用方式，其中的一种是为 ENTRYPOINT 提供默认的参数：

`CMD [“param1”,“param2”]`

另外两种使用方式分别是 exec 模式和 shell 模式：
* `CMD [“executable”,“param1”,“param2”]` // 这是 exec 模式的写法，注意需要使用双引号。
* `CMD command param1 param2` // 这是 shell 模式的写法。

注意命令行参数可以覆盖 CMD 指令的设置，但是只能是重写，却不能给 CMD 中的命令通过命令行传递参数。
一般的镜像都会提供容器启动时的默认命令，但是有些场景中用户并不想执行默认的命令。用户可以通过命令行参数的方式覆盖 CMD 指令提供的默认命令。比如通过下面命令创建的镜像：
```
FROM ubuntu:18.04
CMD [ “top” ]
```

在启动容器时我们通过命令行指定参数 ps aux 覆盖默认的 top 命令：
```
$ docker build --no-cache -t test1 .
$ docker run -idt --name testcon test1
$ docker exec testcon ps aux
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.0 0.0 36592 3196 ? Ss+ 03:13 0:00 top
root 7 0.0 0.0 34396 2812 ? Rs 03:15 0:00 ps aux
```

```
$ docker run --rm test1 ps aux
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.0 0.0 25940 1544 ? Rs 03:21 0:00 ps aux
```

从上面可以看到，命令行上指定的 `ps aux` 命令覆盖了 Dockerfile 中的 `CMD["TOP"]`。实际上，命令行上的命令同样会覆盖 shell 模式的 CMD 指令。

**ENTRYPOINT 指令**

ENTRYPOINT 指令的目的也是为容器指定默认执行的任务。

ENTRYPOINT 指令有两种使用方式，就是我们前面介绍的 exec 模式和 shell 模式：
* `ENTRYPOINT [“executable”, “param1”, “param2”]` // 这是 exec 模式的写法，注意需要使用双引号。
* `ENTRYPOINT command param1 param2` // 这是 shell 模式的写法。

exec 模式和 shell 模式的基本用法和 CMD 指令是一样的，下面我们介绍一些比较特殊的用法。

指定 ENTRYPOINT 指令为 exec 模式时，命令行上指定的参数会作为参数添加到 ENTRYPOINT 指定命令的参数列表中。用下面的代码构建镜像 test1：
```
FROM ubuntu:18.04
ENTRYPOINT [ “top”, “-b” ]
```

运行下面的命令：
```
$ docker build --no-cache -t test1 .
$ docker run --rm test1 -c
top - 03:31:59 up 2:18, 0 users, load average: 0.19, 0.15, 0.10
Tasks: 1 total, 1 running, 0 sleeping, 0 stopped, 0 zombie
%Cpu(s): 0.2 us, 0.2 sy, 0.0 ni, 99.4 id, 0.1 wa, 0.0 hi, 0.0 si, 0.0 st
KiB Mem : 16383348 total, 13349388 free, 1883336 used, 1150624 buff/cache
KiB Swap: 0 total, 0 free, 0 used. 14059300 avail Mem

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
1 root 20 0 36480 3024 2676 R 0.0 0.0 0:00.06 top -b -c
```

我们在命令行上添加的参数被追加到了 top 命令的参数列表中(top -b -c)。

由 CMD 指令指定默认的可选参数：
```
FROM ubuntu
ENTRYPOINT [ “top”, “-b” ]
CMD [ “-c” ]
```

使用这段代码构建镜像 test2 并不带命令行参数启动容器：
```
$ docker build --no-cache -t test2 .
$ docker run --rm test2
top - 03:37:48 up 2:24, 0 users, load average: 0.37, 0.13, 0.10
Tasks: 1 total, 1 running, 0 sleeping, 0 stopped, 0 zombie
%Cpu(s): 5.4 us, 1.8 sy, 0.0 ni, 92.0 id, 0.6 wa, 0.0 hi, 0.3 si, 0.0 st
KiB Mem : 16383348 total, 13342380 free, 1889964 used, 1151004 buff/cache
KiB Swap: 0 total, 0 free, 0 used. 14052664 avail Mem

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
1 root 20 0 36480 3068 2712 R 0.0 0.0 0:00.04 top -b -c
```

这时容器中运行的命令为：`top -b -c`。

如果我们指定命令行参数：
```
$ docker run --rm test2 -n 1
top - 03:38:46 up 2:25, 0 users, load average: 0.28, 0.14, 0.10
Tasks: 1 total, 1 running, 0 sleeping, 0 stopped, 0 zombie
%Cpu(s): 1.0 us, 0.5 sy, 0.0 ni, 97.8 id, 0.7 wa, 0.0 hi, 0.0 si, 0.0 st
KiB Mem : 16383348 total, 13341072 free, 1891212 used, 1151064 buff/cache
KiB Swap: 0 total, 0 free, 0 used. 14051396 avail Mem

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
1 root 20 0 36480 3048 2700 R 0.0 0.0 0:00.04 top
```

`-n 1` 会覆盖 通过 `CMD["-c"]` 指定的参数，容器执行的命令为：`top -b -n 1`
注意上面的输出显示 `-c` 参数被覆盖了。

指定 ENTRYPOINT 指令为 shell 模式时，会完全忽略命令行参数：
```
FROM ubuntu:18.04
ENTRYPOINT echo $HOME
```

把上面的代码编译成镜像 test2，分别不带命令行参数和使用命令行参数 ls 执行命令：
```
$ docker build --no-cache -t test2 .
$ docker run --rm test2
/root
$ docker run --rm test2 ls
/root
```

我们看到 ls 命令没有被执行，这说明命令行参数被 ENTRYPOINT 指令的 shell 模式忽略了。

覆盖默认的 ENTRYPOINT 指令：

ENTRYPOINT 指令也是可以被命令行覆盖的，只不过不是默认的命令行参数，而是需要显式的指定 `--entrypoint` 参数。
比如我们通过下面的方式覆盖上面镜像中的 `echo $HOME` 命令：
```
$ docker run --rm --entrypoint hostname test2
706d6acdc3e4
```

这里我们使用 hostname 命令覆盖了默认的 `echo $HOME` 命令。

**Dockerfile 中至少要有一个**

如果镜像中既没有指定 CMD 也没有指定 ENTRYPOINT 那么在启动容器时会报错。这不算是什么问题，
因为现在能见到的绝大多数镜像都默认添加了 CMD 或 ENTRYPOINT 指令。

指定任意一个，效果差不多

从结果上看，CMD 和 ENTRYPOINT 是一样的，我们可以通过它们实现相同的目的。
下面我们分别用 CMD 和 ENTRYPOINT 设置 `top -b` 命令，然后观察容器运行时的 metadata 信息：

![]({{site.baseurl}}/images/20230414/20230414161844.png)

或者：

![]({{site.baseurl}}/images/20230414/20230414161848.png)

虽然实现方式不同，但最终容器运行的命令是一样的。

**同时使用 CMD 和 ENTRYPOINT 的情况**

对于 CMD 和 ENTRYPOINT 的设计而言，多数情况下它们应该是单独使用的。当然，有一个例外是 CMD 为 ENTRYPOINT 提供默认的可选参数。

我们大概可以总结出下面几条规律：
* 如果 ENTRYPOINT 使用了 shell 模式，CMD 指令会被忽略。
* 如果 ENTRYPOINT 使用了 exec 模式，CMD 指定的内容被追加为 ENTRYPOINT 指定命令的参数。
* 如果 ENTRYPOINT 使用了 exec 模式，CMD 也应该使用 exec 模式。

真实的情况要远比这三条规律复杂，好在 docker 给出了官方的解释，如下图所示：

![]({{site.baseurl}}/images/20230414/20230414161856.png)

当我们无法理解容器中运行命令的行为时，说不定通过这个表格可以解开疑惑！

**总结**

对于 Dockerfile 来说，CMD 和 ENTRYPOINT 是非常重要的指令。它们不是在构建镜像的过程中执行，而是在启动容器时执行，
所以主要用来指定容器默认执行的命令。但是提供两个功能类似的指令，必然会给用户带来理解上的困惑和使用中的混淆。
希望本文能够帮助大家理解二者的区别与联系，并更好的使用二者。







<br/><br/><br/><br/><br/>
## 参考资料

docker之Dockerfile实践 <https://www.cnblogs.com/jsonhc/p/7767669.html>

Dockerfiles 搭建各种环境集合 <https://github.com/jianyan74/dockerfiles>

docker一键搭建Nginx+PHP环境(含自动部署命令) <https://www.cnblogs.com/lz0925/p/10985700.html>

基于Docker搭建LNPM环境 <https://www.jianshu.com/p/beb8cebaafb8>

Docker的网络配置 <https://blog.csdn.net/hetoto/article/details/99892743>

docker容器无法访问外网 <https://www.wencst.com/archives/626>

Docker创建容器无法访问外网问题 <https://www.jianshu.com/p/49f63dcf3813>

docker桥接网络

<https://blog.csdn.net/qq_41772936/article/details/81192369>

<https://www.cnblogs.com/legenidongma/p/10670535.html>

<https://www.cnblogs.com/weifeng1463/p/7468497.html>

“CMD”“ ENTRYPOINT”详解 <https://blog.csdn.net/weixin_43300291/article/details/103874801>



