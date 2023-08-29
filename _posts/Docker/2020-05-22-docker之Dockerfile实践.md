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

## Docker的网络配置

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


## Nginx镜像管理

### 新建Dockerfile

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

### 生成镜像

然后生成镜像：
```
docker build --network host -t centos7_nginx1.18:v1.0 .
```

注意，最后有个点。`centos7_nginx1.18` 是镜像名称，`v1.0` 是打的标签，跟版本号一样的意思。
`-t` 或者 `--tag`： 镜像的名字及标签，通常 `name:tag` 或者 `name` 格式；可以在一次构建中为一个镜像设置多个标签。
`--network`: 默认 default，这里可以去掉，在RUN指令构建期间再设置网络模式。

生成过程中，可能会报错，按照提示，进行修改。具体生成细节查看 附录 部分。

查看生成的镜像：
> docker images

输出：
```
[root@localhost ~]# docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
centos7_nginx1.18            v1.0                60f1a3c49c5a        6 minutes ago       994 MB
[root@localhost ~]#
```

### 启动容器

启动一个容器：
```
docker run --name=nginx --privileged=true -p 80:80 -d centos7_nginx1.18:v1.0
```

`--name` 容器名称；`--privileged` 特权模式；`-p`  映射端口号，宿主机端口:容器端口；
`-d`  是守护进程运行的意思，即容器后台运行不会退出；`centos7_nginx1.18:v1.0` 是使用的 镜像:版本号 。

输出：
```
[root@localhost ~]# docker run --name=nginx --privileged=true -p 80:80 -d centos7_nginx1.18:v1.0
9b7d9b130a221950c2fdb93e1ef72a5b6927e75105483e7a0fdcf1cd1a69ea15
[root@localhost ~]#
```

查看容器列表：
> docker ps

输出：
```
[root@localhost ~]# docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED        STATUS       PORTS                NAMES
9b7d9b130a22  centos7_nginx1.18:v1.0   "nginx -g 'daemon ..."   6 minutes ago  Up 6 minutes  0.0.0.0:80->80/tcp   nginx
[root@localhost ~]#
```

命令行执行`curl 127.0.0.1:80` 命令，能看到  Welcome to nginx  等英文提示，即说明一切OK。
```
[root@localhost ~]# curl 127.0.0.1:80
\<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
[root@localhost ~]#
```

也可以打开浏览器，访问虚拟机IP，查看页面内容。

### 提交仓库

我们可以把上面生成的镜像，提交到Docker官方的个人仓库中，以备日后直接使用。

首先在Docker仓库官网 <https://hub.docker.com/> 申请账号，如用户名是 ibaiyang 。

然后本地命令行登录：
```
[root@localhost ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: ibaiyang
Password:
Login Succeeded
[root@localhost ~]#
```

给镜像打标签(60f1a3c49c5a是镜像的ID)：
```
[root@localhost ~]# docker tag 60f1a3c49c5a ibaiyang/centos7_nginx1.18:v1.0
[root@localhost ~]#
[root@localhost ~]# docker images
REPOSITORY                      TAG          IMAGE ID            CREATED             SIZE
centos7_nginx1.18               v1.0         60f1a3c49c5a        6 minutes ago       994 MB
ibaiyang/centos7_nginx1.18      v1.0         60f1a3c49c5a        6 minutes ago       994 MB
[root@localhost ~]#
```

推送镜像到Docker个人仓库：
```
[root@localhost ~]# docker push ibaiyang/centos7_nginx1.18:v1.0
The push refers to a repository [docker.io/ibaiyang/centos7_nginx1.18]
cf609eac522b: Pushed
ff0de8b371e9: Mounted from library/nginx
37ec37fdbd74: Mounted from library/nginx
d491a3adbb37: Mounted from library/nginx
fdcab9036a54: Mounted from library/nginx
cb66017db9e6: Mounted from library/nginx
v1.0: digest: sha256:992901a9aea825b81a0bac0878f42b49ba8821968efdfede687d54b3c63f1fe9 size: 2622
[root@localhost ~]#
```

在Docker仓库官网的个人中心中就可以看到自己提交的镜像包了。

下次下载时，直接使用：
> docker pull ibaiyang/centos7_nginx1.18:v1.0

还有一点需要说明，docker 也有类似 git 的版本管理功能，每次在运行的容器内修改后，确认没有问题时，
可以用`docker commit` 命令打个新的Tag标签，生成一个新的镜像，然后再推送到个人仓库。

```
docker commit <容器ID或名称> <新镜像名称:标签>
```

### 移除容器

先停止容器运行：
> docker stop nginx

移除容器：
> docker rm nginx

输出：
```
[root@localhost ~]# docker ps
CONTAINER ID    IMAGE                    COMMAND                 CREATED        STATUS        PORTS               NAMES
9b7d9b130a22    centos7_nginx1.18:v1.0   "nginx -g 'daemon ..."  6 minutes ago  Up 6 minutes  0.0.0.0:80->80/tcp  nginx
[root@localhost ~]#
[root@localhost ~]# docker stop nginx
nginx
[root@localhost ~]#
[root@localhost ~]# docker rm nginx
nginx
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE          COMMAND       CREATED     STATUS      PORTS        NAMES
[root@localhost ~]# 
```

### 删除镜像

删除镜像，执行命令：
> docker rmi centos7_nginx1.18:v1.0

输出：
```
[root@localhost ~]# docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
centos7_nginx1.18            v1.0                60f1a3c49c5a        45 minutes ago      994 MB
[root@localhost ~]# 
[root@localhost ~]# docker rmi centos7_nginx1.18:v1.0
Untagged: centos7_nginx1.18:v1.0
Deleted: sha256:60f1a3c49c5a335acd26453e6cd5080d7fce17239feac021eaa23c7ad13d5c4c
Deleted: sha256:3198d7526ec47498fa3718d5d4f459406735f7077212f420f84bbc982d0a4227
Deleted: sha256:fc067d5345908eb0c740cc5fcc077fd3e72fd667f65e5c3138334569be2ad61f
Deleted: sha256:87a613c393ca7ee61b8cea04683a6eefb263f16ff83d575cfd40ea231f840e3b
Deleted: sha256:eefe3d1c82543d1b68269008e3e1367cf381e8dc821661851d1f3e366343df4c
Deleted: sha256:a1ce4ccb9cec82096b1b9b64d82dd4d5096668381a2664cc1a880913bbe497a1
Deleted: sha256:9208332d147d78b4f8942c84108c92bd4404bfc971d9a6ea21366dcf20681bf2
Deleted: sha256:ed2ce01cc48d08cdca39803069c6ffd6384d518e4159840a0e568aea3e736f45
Deleted: sha256:9f7c960c54767cf10d166cd7af17b7e376245ebdfc7bd24043b8dafd76a61f37
Deleted: sha256:405bc90fe433803fe837027c7d2ac7bf27cb361a37d055325f4c35e97eb2e631
Deleted: sha256:d4e35c129f8a36bb5acc39a5d17ed8b4db02f819bf4d228c3d0f209f993a6fca
Deleted: sha256:f599c045c59aa6b345d2fd5216cd189196eda28162bb66c87dd16f5f660772fa
Deleted: sha256:be6c7ec3db808bf30d96d9b4608a36c82ed30b87c733ae40f4510e450bd0ed16
Deleted: sha256:3d80052b52b395c45db63f5549f91cbb65657a9b30a4d323e2e088e21402b89f
Deleted: sha256:5dbb31fad90ad196b971e3faba7c1a0bc4717c8ac0be74ddd95f7c147115ed32
Deleted: sha256:94f187424ceafc73a0f8b28a62a1f2f51d8be3d6188b1f74a1a24e89ec07efb0
Deleted: sha256:38078780973dc33788eebd2bfb95ff5f1bd87d118c58c6e10844eae7769da8be
Deleted: sha256:d04b4c94a49f871ad0f0630158a55d38d92e2d75c572b4aeb07e8c21d742c35d
Deleted: sha256:7e830c3d01313ac3f47693cc4c4d2898a14fd013447e8830acb96fd344fa497d
Deleted: sha256:abf4295f1f6df399e278814226dc1624741065c5129d6b757482fd7d06ca5f21
Deleted: sha256:3ad085e709cc8d273013d55b914578053321bdf71396c5bd69d90e7a37e07d17
Deleted: sha256:89e8587c436bd4b675b7b56ab2fac41fc7e2e871a6f6d09b2957d12c2e496ccc
[root@localhost ~]#
[root@localhost ~]# docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
[root@localhost ~]# 
```

### 清理镜像

在使用 Docker 一段时间后，系统一般都会残存一些临时的、没有被使用的镜像文件，可以通过以下命令进行清理：
> docker image prune

输出：
```
[root@localhost ~]# docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y
Total reclaimed space: 0 B
[root@localhost ~]#
```

### 总结

Dockerfile用于本地生成镜像，像Nginx这类软件，定制化程度很低，不需要自己生成，直接用docker官方库的就可以：
> docker pull nginx

PHP等软件可能牵扯到各类拓展，如 bcmath、gd 等，倒是可以在本地生成镜像，打上版本号，然后推送到docker官方库，
下次直接从官方库`docker pull 镜像`下载。




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
1. `CMD ["executable","param1","param2"]` (exec form, this is the preferred form)
2. `CMD ["param1","param2"]` (as default parameters to ENTRYPOINT)
3. `CMD command param1 param2` (shell form)

因为还没有讲 ENTRYPOINT，所以先不看用法2。 

用法3：shell form，即没有中括号的形式。那么命令command默认是在 `/bin/sh -c`下执行的。

比如下面的dockerfile：
```
# vi Dockerfile
FROM ubuntu:18.04
CMD echo "hello cmd!"
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
CMD ["/bin/bash", "-c", "echo ‘hello2 cmd!’"]
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
1. `ENTRYPOINT ["executable", "param1", "param2"]` (exec form, preferred)
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
CMD ["p in cmd"]
ENTRYPOINT ["echo"]

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
CMD ["p in cmd"]
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
CMD [ "top" ]
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
CMD [ "echo", "$HOME" ]
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
CMD [ "sh", "-c", "echo $HOME" ]
```

把上面的代码保存到 test1 目录的 Dockerfile 中，然后进入 test1 目录构建镜像并启动一个容器：
```
$ docker build --no-cache -t test1 .
$ docker run --rm test1
/root
```

这次正确取到了 $HOME 环境变量的值。

**shell 模式**

使用 shell 模式时，docker 会以 `/bin/sh -c "task command"` 的方式执行任务命令。也就是说容器中的 1 号进程不是任务进程而是 bash 进程，看下面的例子：
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

`CMD ["param1","param2"]`

另外两种使用方式分别是 exec 模式和 shell 模式：
* `CMD ["executable","param1","param2"]` // 这是 exec 模式的写法，注意需要使用双引号。
* `CMD command param1 param2` // 这是 shell 模式的写法。

注意命令行参数可以覆盖 CMD 指令的设置，但是只能是重写，却不能给 CMD 中的命令通过命令行传递参数。
一般的镜像都会提供容器启动时的默认命令，但是有些场景中用户并不想执行默认的命令。用户可以通过命令行参数的方式覆盖 CMD 指令提供的默认命令。比如通过下面命令创建的镜像：
```
FROM ubuntu:18.04
CMD [ "top" ]
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
* `ENTRYPOINT ["executable", "param1", "param2"]` // 这是 exec 模式的写法，注意需要使用双引号。
* `ENTRYPOINT command param1 param2` // 这是 shell 模式的写法。

exec 模式和 shell 模式的基本用法和 CMD 指令是一样的，下面我们介绍一些比较特殊的用法。

指定 ENTRYPOINT 指令为 exec 模式时，命令行上指定的参数会作为参数添加到 ENTRYPOINT 指定命令的参数列表中。用下面的代码构建镜像 test1：
```
FROM ubuntu:18.04
ENTRYPOINT [ "top", "-b" ]
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
ENTRYPOINT [ "top", "-b" ]
CMD [ "-c" ]
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





## 附录

### Nginx镜像生成细节

看一下Nginx镜像生成细节：
> docker build -t centos7_nginx1.18:v1.0 .

输出：
```
[root@localhost nginx]# docker build -t centos7_nginx1.18:v1.0 .
Sending build context to Docker daemon 4.096 kB
Step 1/16 : FROM centos:7
Trying to pull repository docker.io/library/centos ...
7: Pulling from docker.io/library/centos
2d473b07cdd5: Pull complete
Digest: sha256:be65f488b7764ad3638f236b7b515b3678369a5124c47b8d32916d6487418ea4
Status: Downloaded newer image for docker.io/centos:7
 ---> eeb6ee3f44bd
Step 2/16 : MAINTAINER zhuyingu@126.com
 ---> Running in 73b6ebcb557e
 ---> 89e8587c436b
Removing intermediate container 73b6ebcb557e
Step 3/16 : RUN yum install -y wget
 ---> Running in 5dadced1c673

Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.14-18.el7_6.1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package        Arch             Version                   Repository      Size
================================================================================
Installing:
 wget           x86_64           1.14-18.el7_6.1           base           547 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 547 k
Installed size: 2.0 M
Downloading packages:
warning: /var/cache/yum/x86_64/7/base/packages/wget-1.14-18.el7_6.1.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for wget-1.14-18.el7_6.1.x86_64.rpm is not installed
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-9.2009.0.el7.centos.x86_64 (@CentOS)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : wget-1.14-18.el7_6.1.x86_64                                  1/1
install-info: No such file or directory for /usr/share/info/wget.info.gz
  Verifying  : wget-1.14-18.el7_6.1.x86_64                                  1/1

Installed:
  wget.x86_64 0:1.14-18.el7_6.1

Complete!
 ---> abf4295f1f6d
Removing intermediate container 5dadced1c673
Step 4/16 : WORKDIR /usr/local/src
 ---> 7e830c3d0131
Removing intermediate container b8ab495762db
Step 5/16 : ADD http://nginx.org/download/nginx-1.18.0.tar.gz .
Downloading [==================================================>]  1.04 MB/1.04 MB
 ---> 38078780973d
Removing intermediate container 9a5870f78095
Step 6/16 : RUN tar zxvf nginx-1.18.0.tar.gz
 ---> Running in f63a421abaec

nginx-1.18.0/
nginx-1.18.0/auto/
nginx-1.18.0/conf/
nginx-1.18.0/contrib/
nginx-1.18.0/src/
nginx-1.18.0/configure
nginx-1.18.0/LICENSE
nginx-1.18.0/README
nginx-1.18.0/html/
nginx-1.18.0/man/
nginx-1.18.0/CHANGES.ru
nginx-1.18.0/CHANGES
nginx-1.18.0/man/nginx.8
nginx-1.18.0/html/50x.html
nginx-1.18.0/html/index.html
nginx-1.18.0/src/core/
nginx-1.18.0/src/event/
nginx-1.18.0/src/http/
nginx-1.18.0/src/mail/
nginx-1.18.0/src/misc/
nginx-1.18.0/src/os/
nginx-1.18.0/src/stream/
nginx-1.18.0/src/stream/ngx_stream.c
nginx-1.18.0/src/stream/ngx_stream.h
nginx-1.18.0/src/stream/ngx_stream_access_module.c
nginx-1.18.0/src/stream/ngx_stream_core_module.c
nginx-1.18.0/src/stream/ngx_stream_geo_module.c
nginx-1.18.0/src/stream/ngx_stream_geoip_module.c
nginx-1.18.0/src/stream/ngx_stream_handler.c
nginx-1.18.0/src/stream/ngx_stream_limit_conn_module.c
nginx-1.18.0/src/stream/ngx_stream_log_module.c
nginx-1.18.0/src/stream/ngx_stream_map_module.c
nginx-1.18.0/src/stream/ngx_stream_proxy_module.c
nginx-1.18.0/src/stream/ngx_stream_realip_module.c
nginx-1.18.0/src/stream/ngx_stream_return_module.c
nginx-1.18.0/src/stream/ngx_stream_script.c
nginx-1.18.0/src/stream/ngx_stream_script.h
nginx-1.18.0/src/stream/ngx_stream_split_clients_module.c
nginx-1.18.0/src/stream/ngx_stream_ssl_module.c
nginx-1.18.0/src/stream/ngx_stream_ssl_module.h
nginx-1.18.0/src/stream/ngx_stream_ssl_preread_module.c
nginx-1.18.0/src/stream/ngx_stream_upstream.c
nginx-1.18.0/src/stream/ngx_stream_upstream.h
nginx-1.18.0/src/stream/ngx_stream_upstream_hash_module.c
nginx-1.18.0/src/stream/ngx_stream_upstream_least_conn_module.c
nginx-1.18.0/src/stream/ngx_stream_upstream_random_module.c
nginx-1.18.0/src/stream/ngx_stream_upstream_round_robin.c
nginx-1.18.0/src/stream/ngx_stream_upstream_round_robin.h
nginx-1.18.0/src/stream/ngx_stream_upstream_zone_module.c
nginx-1.18.0/src/stream/ngx_stream_variables.c
nginx-1.18.0/src/stream/ngx_stream_variables.h
nginx-1.18.0/src/stream/ngx_stream_write_filter_module.c
nginx-1.18.0/src/os/unix/
nginx-1.18.0/src/os/unix/ngx_alloc.c
nginx-1.18.0/src/os/unix/ngx_alloc.h
nginx-1.18.0/src/os/unix/ngx_atomic.h
nginx-1.18.0/src/os/unix/ngx_channel.c
nginx-1.18.0/src/os/unix/ngx_channel.h
nginx-1.18.0/src/os/unix/ngx_daemon.c
nginx-1.18.0/src/os/unix/ngx_darwin.h
nginx-1.18.0/src/os/unix/ngx_darwin_config.h
nginx-1.18.0/src/os/unix/ngx_darwin_init.c
nginx-1.18.0/src/os/unix/ngx_darwin_sendfile_chain.c
nginx-1.18.0/src/os/unix/ngx_dlopen.c
nginx-1.18.0/src/os/unix/ngx_dlopen.h
nginx-1.18.0/src/os/unix/ngx_errno.c
nginx-1.18.0/src/os/unix/ngx_errno.h
nginx-1.18.0/src/os/unix/ngx_file_aio_read.c
nginx-1.18.0/src/os/unix/ngx_files.c
nginx-1.18.0/src/os/unix/ngx_files.h
nginx-1.18.0/src/os/unix/ngx_freebsd.h
nginx-1.18.0/src/os/unix/ngx_freebsd_config.h
nginx-1.18.0/src/os/unix/ngx_linux.h
nginx-1.18.0/src/os/unix/ngx_freebsd_init.c
nginx-1.18.0/src/os/unix/ngx_freebsd_sendfile_chain.c
nginx-1.18.0/src/os/unix/ngx_gcc_atomic_amd64.h
nginx-1.18.0/src/os/unix/ngx_gcc_atomic_ppc.h
nginx-1.18.0/src/os/unix/ngx_gcc_atomic_sparc64.h
nginx-1.18.0/src/os/unix/ngx_gcc_atomic_x86.h
nginx-1.18.0/src/os/unix/ngx_linux_aio_read.c
nginx-1.18.0/src/os/unix/ngx_linux_config.h
nginx-1.18.0/src/os/unix/ngx_linux_init.c
nginx-1.18.0/src/os/unix/ngx_linux_sendfile_chain.c
nginx-1.18.0/src/os/unix/ngx_os.h
nginx-1.18.0/src/os/unix/ngx_posix_config.h
nginx-1.18.0/src/os/unix/ngx_posix_init.c
nginx-1.18.0/src/os/unix/ngx_process.c
nginx-1.18.0/src/os/unix/ngx_process.h
nginx-1.18.0/src/os/unix/ngx_process_cycle.c
nginx-1.18.0/src/os/unix/ngx_process_cycle.h
nginx-1.18.0/src/os/unix/ngx_readv_chain.c
nginx-1.18.0/src/os/unix/ngx_recv.c
nginx-1.18.0/src/os/unix/ngx_send.c
nginx-1.18.0/src/os/unix/ngx_setaffinity.c
nginx-1.18.0/src/os/unix/ngx_setaffinity.h
nginx-1.18.0/src/os/unix/ngx_setproctitle.c
nginx-1.18.0/src/os/unix/ngx_setproctitle.h
nginx-1.18.0/src/os/unix/ngx_shmem.c
nginx-1.18.0/src/os/unix/ngx_shmem.h
nginx-1.18.0/src/os/unix/ngx_socket.c
nginx-1.18.0/src/os/unix/ngx_socket.h
nginx-1.18.0/src/os/unix/ngx_solaris.h
nginx-1.18.0/src/os/unix/ngx_solaris_config.h
nginx-1.18.0/src/os/unix/ngx_solaris_init.c
nginx-1.18.0/src/os/unix/ngx_solaris_sendfilev_chain.c
nginx-1.18.0/src/os/unix/ngx_sunpro_amd64.il
nginx-1.18.0/src/os/unix/ngx_sunpro_atomic_sparc64.h
nginx-1.18.0/src/os/unix/ngx_sunpro_sparc64.il
nginx-1.18.0/src/os/unix/ngx_thread.h
nginx-1.18.0/src/os/unix/ngx_sunpro_x86.il
nginx-1.18.0/src/os/unix/ngx_thread_cond.c
nginx-1.18.0/src/os/unix/ngx_thread_id.c
nginx-1.18.0/src/os/unix/ngx_thread_mutex.c
nginx-1.18.0/src/os/unix/ngx_time.c
nginx-1.18.0/src/os/unix/ngx_time.h
nginx-1.18.0/src/os/unix/ngx_udp_recv.c
nginx-1.18.0/src/os/unix/ngx_udp_send.c
nginx-1.18.0/src/os/unix/ngx_udp_sendmsg_chain.c
nginx-1.18.0/src/os/unix/ngx_user.c
nginx-1.18.0/src/os/unix/ngx_user.h
nginx-1.18.0/src/os/unix/ngx_writev_chain.c
nginx-1.18.0/src/misc/ngx_cpp_test_module.cpp
nginx-1.18.0/src/misc/ngx_google_perftools_module.c
nginx-1.18.0/src/mail/ngx_mail.c
nginx-1.18.0/src/mail/ngx_mail.h
nginx-1.18.0/src/mail/ngx_mail_auth_http_module.c
nginx-1.18.0/src/mail/ngx_mail_core_module.c
nginx-1.18.0/src/mail/ngx_mail_handler.c
nginx-1.18.0/src/mail/ngx_mail_imap_handler.c
nginx-1.18.0/src/mail/ngx_mail_imap_module.c
nginx-1.18.0/src/mail/ngx_mail_imap_module.h
nginx-1.18.0/src/mail/ngx_mail_parse.c
nginx-1.18.0/src/mail/ngx_mail_pop3_handler.c
nginx-1.18.0/src/mail/ngx_mail_pop3_module.c
nginx-1.18.0/src/mail/ngx_mail_pop3_module.h
nginx-1.18.0/src/mail/ngx_mail_proxy_module.c
nginx-1.18.0/src/mail/ngx_mail_smtp_handler.c
nginx-1.18.0/src/mail/ngx_mail_smtp_module.c
nginx-1.18.0/src/mail/ngx_mail_smtp_module.h
nginx-1.18.0/src/mail/ngx_mail_ssl_module.c
nginx-1.18.0/src/mail/ngx_mail_ssl_module.h
nginx-1.18.0/src/http/modules/
nginx-1.18.0/src/http/ngx_http.c
nginx-1.18.0/src/http/ngx_http.h
nginx-1.18.0/src/http/ngx_http_cache.h
nginx-1.18.0/src/http/ngx_http_config.h
nginx-1.18.0/src/http/ngx_http_copy_filter_module.c
nginx-1.18.0/src/http/ngx_http_core_module.c
nginx-1.18.0/src/http/ngx_http_core_module.h
nginx-1.18.0/src/http/ngx_http_file_cache.c
nginx-1.18.0/src/http/ngx_http_header_filter_module.c
nginx-1.18.0/src/http/ngx_http_parse.c
nginx-1.18.0/src/http/ngx_http_postpone_filter_module.c
nginx-1.18.0/src/http/ngx_http_request.c
nginx-1.18.0/src/http/ngx_http_request.h
nginx-1.18.0/src/http/ngx_http_request_body.c
nginx-1.18.0/src/http/ngx_http_script.c
nginx-1.18.0/src/http/v2/
nginx-1.18.0/src/http/ngx_http_script.h
nginx-1.18.0/src/http/ngx_http_special_response.c
nginx-1.18.0/src/http/ngx_http_upstream.c
nginx-1.18.0/src/http/ngx_http_upstream.h
nginx-1.18.0/src/http/ngx_http_upstream_round_robin.c
nginx-1.18.0/src/http/ngx_http_upstream_round_robin.h
nginx-1.18.0/src/http/ngx_http_variables.c
nginx-1.18.0/src/http/ngx_http_variables.h
nginx-1.18.0/src/http/ngx_http_write_filter_module.c
nginx-1.18.0/src/http/v2/ngx_http_v2.c
nginx-1.18.0/src/http/v2/ngx_http_v2.h
nginx-1.18.0/src/http/v2/ngx_http_v2_encode.c
nginx-1.18.0/src/http/v2/ngx_http_v2_filter_module.c
nginx-1.18.0/src/http/v2/ngx_http_v2_huff_decode.c
nginx-1.18.0/src/http/v2/ngx_http_v2_huff_encode.c
nginx-1.18.0/src/http/v2/ngx_http_v2_module.c
nginx-1.18.0/src/http/v2/ngx_http_v2_module.h
nginx-1.18.0/src/http/v2/ngx_http_v2_table.c
nginx-1.18.0/src/http/modules/ngx_http_access_module.c
nginx-1.18.0/src/http/modules/ngx_http_addition_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_auth_basic_module.c
nginx-1.18.0/src/http/modules/ngx_http_auth_request_module.c
nginx-1.18.0/src/http/modules/ngx_http_autoindex_module.c
nginx-1.18.0/src/http/modules/ngx_http_browser_module.c
nginx-1.18.0/src/http/modules/ngx_http_charset_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_chunked_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_dav_module.c
nginx-1.18.0/src/http/modules/ngx_http_degradation_module.c
nginx-1.18.0/src/http/modules/ngx_http_empty_gif_module.c
nginx-1.18.0/src/http/modules/ngx_http_fastcgi_module.c
nginx-1.18.0/src/http/modules/perl/
nginx-1.18.0/src/http/modules/ngx_http_flv_module.c
nginx-1.18.0/src/http/modules/ngx_http_geo_module.c
nginx-1.18.0/src/http/modules/ngx_http_geoip_module.c
nginx-1.18.0/src/http/modules/ngx_http_grpc_module.c
nginx-1.18.0/src/http/modules/ngx_http_gunzip_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_gzip_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_gzip_static_module.c
nginx-1.18.0/src/http/modules/ngx_http_headers_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_image_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_index_module.c
nginx-1.18.0/src/http/modules/ngx_http_limit_conn_module.c
nginx-1.18.0/src/http/modules/ngx_http_limit_req_module.c
nginx-1.18.0/src/http/modules/ngx_http_log_module.c
nginx-1.18.0/src/http/modules/ngx_http_map_module.c
nginx-1.18.0/src/http/modules/ngx_http_memcached_module.c
nginx-1.18.0/src/http/modules/ngx_http_mirror_module.c
nginx-1.18.0/src/http/modules/ngx_http_mp4_module.c
nginx-1.18.0/src/http/modules/ngx_http_not_modified_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_proxy_module.c
nginx-1.18.0/src/http/modules/ngx_http_random_index_module.c
nginx-1.18.0/src/http/modules/ngx_http_range_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_realip_module.c
nginx-1.18.0/src/http/modules/ngx_http_referer_module.c
nginx-1.18.0/src/http/modules/ngx_http_rewrite_module.c
nginx-1.18.0/src/http/modules/ngx_http_scgi_module.c
nginx-1.18.0/src/http/modules/ngx_http_secure_link_module.c
nginx-1.18.0/src/http/modules/ngx_http_slice_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_split_clients_module.c
nginx-1.18.0/src/http/modules/ngx_http_ssi_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_ssi_filter_module.h
nginx-1.18.0/src/http/modules/ngx_http_ssl_module.c
nginx-1.18.0/src/http/modules/ngx_http_ssl_module.h
nginx-1.18.0/src/http/modules/ngx_http_static_module.c
nginx-1.18.0/src/http/modules/ngx_http_stub_status_module.c
nginx-1.18.0/src/http/modules/ngx_http_sub_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_try_files_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_hash_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_ip_hash_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_keepalive_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_random_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_least_conn_module.c
nginx-1.18.0/src/http/modules/ngx_http_upstream_zone_module.c
nginx-1.18.0/src/http/modules/ngx_http_userid_filter_module.c
nginx-1.18.0/src/http/modules/ngx_http_uwsgi_module.c
nginx-1.18.0/src/http/modules/ngx_http_xslt_filter_module.c
nginx-1.18.0/src/http/modules/perl/Makefile.PL
nginx-1.18.0/src/http/modules/perl/nginx.pm
nginx-1.18.0/src/http/modules/perl/nginx.xs
nginx-1.18.0/src/http/modules/perl/ngx_http_perl_module.c
nginx-1.18.0/src/http/modules/perl/ngx_http_perl_module.h
nginx-1.18.0/src/http/modules/perl/typemap
nginx-1.18.0/src/event/modules/
nginx-1.18.0/src/event/ngx_event.c
nginx-1.18.0/src/event/ngx_event.h
nginx-1.18.0/src/event/ngx_event_accept.c
nginx-1.18.0/src/event/ngx_event_connect.c
nginx-1.18.0/src/event/ngx_event_connect.h
nginx-1.18.0/src/event/ngx_event_openssl.c
nginx-1.18.0/src/event/ngx_event_openssl.h
nginx-1.18.0/src/event/ngx_event_openssl_stapling.c
nginx-1.18.0/src/event/ngx_event_pipe.c
nginx-1.18.0/src/event/ngx_event_pipe.h
nginx-1.18.0/src/event/ngx_event_posted.c
nginx-1.18.0/src/event/ngx_event_posted.h
nginx-1.18.0/src/event/ngx_event_timer.c
nginx-1.18.0/src/event/ngx_event_timer.h
nginx-1.18.0/src/event/ngx_event_udp.c
nginx-1.18.0/src/event/modules/ngx_devpoll_module.c
nginx-1.18.0/src/event/modules/ngx_epoll_module.c
nginx-1.18.0/src/event/modules/ngx_eventport_module.c
nginx-1.18.0/src/event/modules/ngx_kqueue_module.c
nginx-1.18.0/src/event/modules/ngx_poll_module.c
nginx-1.18.0/src/event/modules/ngx_select_module.c
nginx-1.18.0/src/event/modules/ngx_win32_poll_module.c
nginx-1.18.0/src/event/modules/ngx_win32_select_module.c
nginx-1.18.0/src/core/nginx.c
nginx-1.18.0/src/core/nginx.h
nginx-1.18.0/src/core/ngx_array.c
nginx-1.18.0/src/core/ngx_array.h
nginx-1.18.0/src/core/ngx_buf.c
nginx-1.18.0/src/core/ngx_buf.h
nginx-1.18.0/src/core/ngx_conf_file.c
nginx-1.18.0/src/core/ngx_conf_file.h
nginx-1.18.0/src/core/ngx_config.h
nginx-1.18.0/src/core/ngx_connection.c
nginx-1.18.0/src/core/ngx_connection.h
nginx-1.18.0/src/core/ngx_core.h
nginx-1.18.0/src/core/ngx_cpuinfo.c
nginx-1.18.0/src/core/ngx_crc.h
nginx-1.18.0/src/core/ngx_crc32.c
nginx-1.18.0/src/core/ngx_crc32.h
nginx-1.18.0/src/core/ngx_crypt.c
nginx-1.18.0/src/core/ngx_crypt.h
nginx-1.18.0/src/core/ngx_cycle.c
nginx-1.18.0/src/core/ngx_cycle.h
nginx-1.18.0/src/core/ngx_file.c
nginx-1.18.0/src/core/ngx_file.h
nginx-1.18.0/src/core/ngx_hash.c
nginx-1.18.0/src/core/ngx_hash.h
nginx-1.18.0/src/core/ngx_inet.c
nginx-1.18.0/src/core/ngx_inet.h
nginx-1.18.0/src/core/ngx_list.c
nginx-1.18.0/src/core/ngx_list.h
nginx-1.18.0/src/core/ngx_log.c
nginx-1.18.0/src/core/ngx_log.h
nginx-1.18.0/src/core/ngx_md5.c
nginx-1.18.0/src/core/ngx_md5.h
nginx-1.18.0/src/core/ngx_module.c
nginx-1.18.0/src/core/ngx_module.h
nginx-1.18.0/src/core/ngx_murmurhash.c
nginx-1.18.0/src/core/ngx_murmurhash.h
nginx-1.18.0/src/core/ngx_open_file_cache.c
nginx-1.18.0/src/core/ngx_open_file_cache.h
nginx-1.18.0/src/core/ngx_output_chain.c
nginx-1.18.0/src/core/ngx_palloc.c
nginx-1.18.0/src/core/ngx_palloc.h
nginx-1.18.0/src/core/ngx_parse.c
nginx-1.18.0/src/core/ngx_parse.h
nginx-1.18.0/src/core/ngx_parse_time.c
nginx-1.18.0/src/core/ngx_queue.c
nginx-1.18.0/src/core/ngx_parse_time.h
nginx-1.18.0/src/core/ngx_proxy_protocol.c
nginx-1.18.0/src/core/ngx_proxy_protocol.h
nginx-1.18.0/src/core/ngx_queue.h
nginx-1.18.0/src/core/ngx_radix_tree.c
nginx-1.18.0/src/core/ngx_radix_tree.h
nginx-1.18.0/src/core/ngx_rbtree.c
nginx-1.18.0/src/core/ngx_rbtree.h
nginx-1.18.0/src/core/ngx_regex.c
nginx-1.18.0/src/core/ngx_regex.h
nginx-1.18.0/src/core/ngx_resolver.c
nginx-1.18.0/src/core/ngx_resolver.h
nginx-1.18.0/src/core/ngx_rwlock.c
nginx-1.18.0/src/core/ngx_rwlock.h
nginx-1.18.0/src/core/ngx_sha1.c
nginx-1.18.0/src/core/ngx_sha1.h
nginx-1.18.0/src/core/ngx_shmtx.c
nginx-1.18.0/src/core/ngx_shmtx.h
nginx-1.18.0/src/core/ngx_slab.c
nginx-1.18.0/src/core/ngx_slab.h
nginx-1.18.0/src/core/ngx_spinlock.c
nginx-1.18.0/src/core/ngx_string.c
nginx-1.18.0/src/core/ngx_string.h
nginx-1.18.0/src/core/ngx_syslog.c
nginx-1.18.0/src/core/ngx_syslog.h
nginx-1.18.0/src/core/ngx_thread_pool.c
nginx-1.18.0/src/core/ngx_thread_pool.h
nginx-1.18.0/src/core/ngx_times.c
nginx-1.18.0/src/core/ngx_times.h
nginx-1.18.0/contrib/README
nginx-1.18.0/contrib/geo2nginx.pl
nginx-1.18.0/contrib/unicode2nginx/
nginx-1.18.0/contrib/vim/
nginx-1.18.0/contrib/vim/ftdetect/
nginx-1.18.0/contrib/vim/ftplugin/
nginx-1.18.0/contrib/vim/indent/
nginx-1.18.0/contrib/vim/syntax/
nginx-1.18.0/contrib/vim/syntax/nginx.vim
nginx-1.18.0/contrib/vim/indent/nginx.vim
nginx-1.18.0/contrib/vim/ftplugin/nginx.vim
nginx-1.18.0/contrib/vim/ftdetect/nginx.vim
nginx-1.18.0/contrib/unicode2nginx/koi-utf
nginx-1.18.0/contrib/unicode2nginx/unicode-to-nginx.pl
nginx-1.18.0/contrib/unicode2nginx/win-utf
nginx-1.18.0/conf/fastcgi.conf
nginx-1.18.0/conf/fastcgi_params
nginx-1.18.0/conf/koi-utf
nginx-1.18.0/conf/koi-win
nginx-1.18.0/conf/mime.types
nginx-1.18.0/conf/nginx.conf
nginx-1.18.0/conf/scgi_params
nginx-1.18.0/conf/uwsgi_params
nginx-1.18.0/conf/win-utf
nginx-1.18.0/auto/cc/
nginx-1.18.0/auto/define
nginx-1.18.0/auto/endianness
nginx-1.18.0/auto/feature
nginx-1.18.0/auto/have
nginx-1.18.0/auto/have_headers
nginx-1.18.0/auto/headers
nginx-1.18.0/auto/include
nginx-1.18.0/auto/init
nginx-1.18.0/auto/install
nginx-1.18.0/auto/lib/
nginx-1.18.0/auto/make
nginx-1.18.0/auto/module
nginx-1.18.0/auto/modules
nginx-1.18.0/auto/nohave
nginx-1.18.0/auto/options
nginx-1.18.0/auto/os/
nginx-1.18.0/auto/sources
nginx-1.18.0/auto/stubs
nginx-1.18.0/auto/summary
nginx-1.18.0/auto/threads
nginx-1.18.0/auto/types/
nginx-1.18.0/auto/unix
nginx-1.18.0/auto/types/sizeof
nginx-1.18.0/auto/types/typedef
nginx-1.18.0/auto/types/uintptr_t
nginx-1.18.0/auto/types/value
nginx-1.18.0/auto/os/conf
nginx-1.18.0/auto/os/darwin
nginx-1.18.0/auto/os/freebsd
nginx-1.18.0/auto/os/linux
nginx-1.18.0/auto/os/solaris
nginx-1.18.0/auto/os/win32
nginx-1.18.0/auto/lib/conf
nginx-1.18.0/auto/lib/geoip/
nginx-1.18.0/auto/lib/google-perftools/
nginx-1.18.0/auto/lib/libatomic/
nginx-1.18.0/auto/lib/libgd/
nginx-1.18.0/auto/lib/libxslt/
nginx-1.18.0/auto/lib/make
nginx-1.18.0/auto/lib/openssl/
nginx-1.18.0/auto/lib/pcre/
nginx-1.18.0/auto/lib/perl/
nginx-1.18.0/auto/lib/zlib/
nginx-1.18.0/auto/lib/zlib/conf
nginx-1.18.0/auto/lib/zlib/make
nginx-1.18.0/auto/lib/zlib/makefile.bcc
nginx-1.18.0/auto/lib/zlib/makefile.msvc
nginx-1.18.0/auto/lib/zlib/makefile.owc
nginx-1.18.0/auto/lib/perl/conf
nginx-1.18.0/auto/lib/perl/make
nginx-1.18.0/auto/lib/pcre/conf
nginx-1.18.0/auto/lib/pcre/make
nginx-1.18.0/auto/lib/pcre/makefile.bcc
nginx-1.18.0/auto/lib/pcre/makefile.msvc
nginx-1.18.0/auto/lib/pcre/makefile.owc
nginx-1.18.0/auto/lib/openssl/conf
nginx-1.18.0/auto/lib/openssl/make
nginx-1.18.0/auto/lib/openssl/makefile.bcc
nginx-1.18.0/auto/lib/openssl/makefile.msvc
nginx-1.18.0/auto/lib/libxslt/conf
nginx-1.18.0/auto/lib/libgd/conf
nginx-1.18.0/auto/lib/libatomic/conf
nginx-1.18.0/auto/lib/libatomic/make
nginx-1.18.0/auto/lib/google-perftools/conf
nginx-1.18.0/auto/lib/geoip/conf
nginx-1.18.0/auto/cc/acc
nginx-1.18.0/auto/cc/bcc
nginx-1.18.0/auto/cc/ccc
nginx-1.18.0/auto/cc/clang
nginx-1.18.0/auto/cc/conf
nginx-1.18.0/auto/cc/gcc
nginx-1.18.0/auto/cc/icc
nginx-1.18.0/auto/cc/msvc
nginx-1.18.0/auto/cc/name
nginx-1.18.0/auto/cc/owc
nginx-1.18.0/auto/cc/sunc
 ---> 5dbb31fad90a
Removing intermediate container f63a421abaec
Step 7/16 : WORKDIR /usr/local/src/nginx-1.18.0
 ---> 3d80052b52b3
Removing intermediate container a8cac90da9b4
Step 8/16 : RUN yum install -y gcc gcc-c++ glibc make openssl-devel
 ---> Running in d2c1f3c2f029

Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package gcc.x86_64 0:4.8.5-44.el7 will be installed
--> Processing Dependency: libgomp = 4.8.5-44.el7 for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: cpp = 4.8.5-44.el7 for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: glibc-devel >= 2.2.90-12 for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: libmpfr.so.4()(64bit) for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: libmpc.so.3()(64bit) for package: gcc-4.8.5-44.el7.x86_64
--> Processing Dependency: libgomp.so.1()(64bit) for package: gcc-4.8.5-44.el7.x86_64
---> Package gcc-c++.x86_64 0:4.8.5-44.el7 will be installed
--> Processing Dependency: libstdc++-devel = 4.8.5-44.el7 for package: gcc-c++-4.8.5-44.el7.x86_64
---> Package glibc.x86_64 0:2.17-317.el7 will be updated
--> Processing Dependency: glibc = 2.17-317.el7 for package: glibc-common-2.17-317.el7.x86_64
---> Package glibc.x86_64 0:2.17-326.el7_9 will be an update
---> Package make.x86_64 1:3.82-24.el7 will be installed
---> Package openssl-devel.x86_64 1:1.0.2k-26.el7_9 will be installed
--> Processing Dependency: openssl-libs(x86-64) = 1:1.0.2k-26.el7_9 for package: 1:openssl-devel-1.0.2k-26.el7_9.x86_64
--> Processing Dependency: zlib-devel(x86-64) for package: 1:openssl-devel-1.0.2k-26.el7_9.x86_64
--> Processing Dependency: krb5-devel(x86-64) for package: 1:openssl-devel-1.0.2k-26.el7_9.x86_64
--> Running transaction check
---> Package cpp.x86_64 0:4.8.5-44.el7 will be installed
---> Package glibc-common.x86_64 0:2.17-317.el7 will be updated
---> Package glibc-common.x86_64 0:2.17-326.el7_9 will be an update
---> Package glibc-devel.x86_64 0:2.17-326.el7_9 will be installed
--> Processing Dependency: glibc-headers = 2.17-326.el7_9 for package: glibc-devel-2.17-326.el7_9.x86_64
--> Processing Dependency: glibc-headers for package: glibc-devel-2.17-326.el7_9.x86_64
---> Package krb5-devel.x86_64 0:1.15.1-55.el7_9 will be installed
--> Processing Dependency: libkadm5(x86-64) = 1.15.1-55.el7_9 for package: krb5-devel-1.15.1-55.el7_9.x86_64
--> Processing Dependency: krb5-libs(x86-64) = 1.15.1-55.el7_9 for package: krb5-devel-1.15.1-55.el7_9.x86_64
--> Processing Dependency: libverto-devel for package: krb5-devel-1.15.1-55.el7_9.x86_64
--> Processing Dependency: libselinux-devel for package: krb5-devel-1.15.1-55.el7_9.x86_64
--> Processing Dependency: libcom_err-devel for package: krb5-devel-1.15.1-55.el7_9.x86_64
--> Processing Dependency: keyutils-libs-devel for package: krb5-devel-1.15.1-55.el7_9.x86_64
---> Package libgomp.x86_64 0:4.8.5-44.el7 will be installed
---> Package libmpc.x86_64 0:1.0.1-3.el7 will be installed
---> Package libstdc++-devel.x86_64 0:4.8.5-44.el7 will be installed
---> Package mpfr.x86_64 0:3.1.1-4.el7 will be installed
---> Package openssl-libs.x86_64 1:1.0.2k-19.el7 will be updated
---> Package openssl-libs.x86_64 1:1.0.2k-26.el7_9 will be an update
---> Package zlib-devel.x86_64 0:1.2.7-21.el7_9 will be installed
--> Processing Dependency: zlib = 1.2.7-21.el7_9 for package: zlib-devel-1.2.7-21.el7_9.x86_64
--> Running transaction check
---> Package glibc-headers.x86_64 0:2.17-326.el7_9 will be installed
--> Processing Dependency: kernel-headers >= 2.2.1 for package: glibc-headers-2.17-326.el7_9.x86_64
--> Processing Dependency: kernel-headers for package: glibc-headers-2.17-326.el7_9.x86_64
---> Package keyutils-libs-devel.x86_64 0:1.5.8-3.el7 will be installed
---> Package krb5-libs.x86_64 0:1.15.1-50.el7 will be updated
---> Package krb5-libs.x86_64 0:1.15.1-55.el7_9 will be an update
---> Package libcom_err-devel.x86_64 0:1.42.9-19.el7 will be installed
---> Package libkadm5.x86_64 0:1.15.1-55.el7_9 will be installed
---> Package libselinux-devel.x86_64 0:2.5-15.el7 will be installed
--> Processing Dependency: libsepol-devel(x86-64) >= 2.5-10 for package: libselinux-devel-2.5-15.el7.x86_64
--> Processing Dependency: pkgconfig(libsepol) for package: libselinux-devel-2.5-15.el7.x86_64
--> Processing Dependency: pkgconfig(libpcre) for package: libselinux-devel-2.5-15.el7.x86_64
---> Package libverto-devel.x86_64 0:0.2.5-4.el7 will be installed
---> Package zlib.x86_64 0:1.2.7-18.el7 will be updated
---> Package zlib.x86_64 0:1.2.7-21.el7_9 will be an update
--> Running transaction check
---> Package kernel-headers.x86_64 0:3.10.0-1160.88.1.el7 will be installed
---> Package libsepol-devel.x86_64 0:2.5-10.el7 will be installed
---> Package pcre-devel.x86_64 0:8.32-17.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                 Arch       Version                   Repository   Size
================================================================================
Installing:
 gcc                     x86_64     4.8.5-44.el7              base         16 M
 gcc-c++                 x86_64     4.8.5-44.el7              base        7.2 M
 make                    x86_64     1:3.82-24.el7             base        421 k
 openssl-devel           x86_64     1:1.0.2k-26.el7_9         updates     1.5 M
Updating:
 glibc                   x86_64     2.17-326.el7_9            updates     3.6 M
Installing for dependencies:
 cpp                     x86_64     4.8.5-44.el7              base        5.9 M
 glibc-devel             x86_64     2.17-326.el7_9            updates     1.1 M
 glibc-headers           x86_64     2.17-326.el7_9            updates     691 k
 kernel-headers          x86_64     3.10.0-1160.88.1.el7      updates     9.1 M
 keyutils-libs-devel     x86_64     1.5.8-3.el7               base         37 k
 krb5-devel              x86_64     1.15.1-55.el7_9           updates     273 k
 libcom_err-devel        x86_64     1.42.9-19.el7             base         32 k
 libgomp                 x86_64     4.8.5-44.el7              base        159 k
 libkadm5                x86_64     1.15.1-55.el7_9           updates     180 k
 libmpc                  x86_64     1.0.1-3.el7               base         51 k
 libselinux-devel        x86_64     2.5-15.el7                base        187 k
 libsepol-devel          x86_64     2.5-10.el7                base         77 k
 libstdc++-devel         x86_64     4.8.5-44.el7              base        1.5 M
 libverto-devel          x86_64     0.2.5-4.el7               base         12 k
 mpfr                    x86_64     3.1.1-4.el7               base        203 k
 pcre-devel              x86_64     8.32-17.el7               base        480 k
 zlib-devel              x86_64     1.2.7-21.el7_9            updates      50 k
Updating for dependencies:
 glibc-common            x86_64     2.17-326.el7_9            updates      12 M
 krb5-libs               x86_64     1.15.1-55.el7_9           updates     810 k
 openssl-libs            x86_64     1:1.0.2k-26.el7_9         updates     1.2 M
 zlib                    x86_64     1.2.7-21.el7_9            updates      90 k

Transaction Summary
================================================================================
Install  4 Packages (+17 Dependent packages)
Upgrade  1 Package  (+ 4 Dependent packages)

Total download size: 62 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
--------------------------------------------------------------------------------
Total                                               14 MB/s |  62 MB  00:04
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : glibc-common-2.17-326.el7_9.x86_64                          1/31
  Updating   : glibc-2.17-326.el7_9.x86_64                                 2/31
  Updating   : zlib-1.2.7-21.el7_9.x86_64                                  3/31
  Installing : mpfr-3.1.1-4.el7.x86_64                                     4/31
  Installing : libmpc-1.0.1-3.el7.x86_64                                   5/31
  Updating   : 1:openssl-libs-1.0.2k-26.el7_9.x86_64                       6/31
  Updating   : krb5-libs-1.15.1-55.el7_9.x86_64                            7/31
  Installing : libkadm5-1.15.1-55.el7_9.x86_64                             8/31
  Installing : cpp-4.8.5-44.el7.x86_64                                     9/31
  Installing : zlib-devel-1.2.7-21.el7_9.x86_64                           10/31
  Installing : libgomp-4.8.5-44.el7.x86_64                                11/31
  Installing : libcom_err-devel-1.42.9-19.el7.x86_64                      12/31
  Installing : libstdc++-devel-4.8.5-44.el7.x86_64                        13/31
  Installing : kernel-headers-3.10.0-1160.88.1.el7.x86_64                 14/31
  Installing : glibc-headers-2.17-326.el7_9.x86_64                        15/31
  Installing : glibc-devel-2.17-326.el7_9.x86_64                          16/31
  Installing : gcc-4.8.5-44.el7.x86_64                                    17/31
  Installing : libverto-devel-0.2.5-4.el7.x86_64                          18/31
  Installing : libsepol-devel-2.5-10.el7.x86_64                           19/31
  Installing : pcre-devel-8.32-17.el7.x86_64                              20/31
  Installing : libselinux-devel-2.5-15.el7.x86_64                         21/31
  Installing : keyutils-libs-devel-1.5.8-3.el7.x86_64                     22/31
  Installing : krb5-devel-1.15.1-55.el7_9.x86_64                          23/31
  Installing : 1:openssl-devel-1.0.2k-26.el7_9.x86_64                     24/31
  Installing : gcc-c++-4.8.5-44.el7.x86_64                                25/31
  Installing : 1:make-3.82-24.el7.x86_64                                  26/31
  Cleanup    : krb5-libs-1.15.1-50.el7.x86_64                             27/31
  Cleanup    : 1:openssl-libs-1.0.2k-19.el7.x86_64                        28/31
  Cleanup    : zlib-1.2.7-18.el7.x86_64                                   29/31
  Cleanup    : glibc-common-2.17-317.el7.x86_64                           30/31
  Cleanup    : glibc-2.17-317.el7.x86_64                                  31/31
  Verifying  : gcc-c++-4.8.5-44.el7.x86_64                                 1/31
  Verifying  : keyutils-libs-devel-1.5.8-3.el7.x86_64                      2/31
  Verifying  : glibc-2.17-326.el7_9.x86_64                                 3/31
  Verifying  : krb5-devel-1.15.1-55.el7_9.x86_64                           4/31
  Verifying  : pcre-devel-8.32-17.el7.x86_64                               5/31
  Verifying  : libselinux-devel-2.5-15.el7.x86_64                          6/31
  Verifying  : zlib-1.2.7-21.el7_9.x86_64                                  7/31
  Verifying  : 1:openssl-devel-1.0.2k-26.el7_9.x86_64                      8/31
  Verifying  : libsepol-devel-2.5-10.el7.x86_64                            9/31
  Verifying  : libverto-devel-0.2.5-4.el7.x86_64                          10/31
  Verifying  : 1:openssl-libs-1.0.2k-26.el7_9.x86_64                      11/31
  Verifying  : cpp-4.8.5-44.el7.x86_64                                    12/31
  Verifying  : glibc-headers-2.17-326.el7_9.x86_64                        13/31
  Verifying  : 1:make-3.82-24.el7.x86_64                                  14/31
  Verifying  : kernel-headers-3.10.0-1160.88.1.el7.x86_64                 15/31
  Verifying  : zlib-devel-1.2.7-21.el7_9.x86_64                           16/31
  Verifying  : krb5-libs-1.15.1-55.el7_9.x86_64                           17/31
  Verifying  : gcc-4.8.5-44.el7.x86_64                                    18/31
  Verifying  : libmpc-1.0.1-3.el7.x86_64                                  19/31
  Verifying  : glibc-common-2.17-326.el7_9.x86_64                         20/31
  Verifying  : libkadm5-1.15.1-55.el7_9.x86_64                            21/31
  Verifying  : mpfr-3.1.1-4.el7.x86_64                                    22/31
  Verifying  : glibc-devel-2.17-326.el7_9.x86_64                          23/31
  Verifying  : libstdc++-devel-4.8.5-44.el7.x86_64                        24/31
  Verifying  : libcom_err-devel-1.42.9-19.el7.x86_64                      25/31
  Verifying  : libgomp-4.8.5-44.el7.x86_64                                26/31
  Verifying  : glibc-2.17-317.el7.x86_64                                  27/31
  Verifying  : glibc-common-2.17-317.el7.x86_64                           28/31
  Verifying  : krb5-libs-1.15.1-50.el7.x86_64                             29/31
  Verifying  : zlib-1.2.7-18.el7.x86_64                                   30/31
  Verifying  : 1:openssl-libs-1.0.2k-19.el7.x86_64                        31/31

Installed:
  gcc.x86_64 0:4.8.5-44.el7        gcc-c++.x86_64 0:4.8.5-44.el7
  make.x86_64 1:3.82-24.el7        openssl-devel.x86_64 1:1.0.2k-26.el7_9

Dependency Installed:
  cpp.x86_64 0:4.8.5-44.el7
  glibc-devel.x86_64 0:2.17-326.el7_9
  glibc-headers.x86_64 0:2.17-326.el7_9
  kernel-headers.x86_64 0:3.10.0-1160.88.1.el7
  keyutils-libs-devel.x86_64 0:1.5.8-3.el7
  krb5-devel.x86_64 0:1.15.1-55.el7_9
  libcom_err-devel.x86_64 0:1.42.9-19.el7
  libgomp.x86_64 0:4.8.5-44.el7
  libkadm5.x86_64 0:1.15.1-55.el7_9
  libmpc.x86_64 0:1.0.1-3.el7
  libselinux-devel.x86_64 0:2.5-15.el7
  libsepol-devel.x86_64 0:2.5-10.el7
  libstdc++-devel.x86_64 0:4.8.5-44.el7
  libverto-devel.x86_64 0:0.2.5-4.el7
  mpfr.x86_64 0:3.1.1-4.el7
  pcre-devel.x86_64 0:8.32-17.el7
  zlib-devel.x86_64 0:1.2.7-21.el7_9

Updated:
  glibc.x86_64 0:2.17-326.el7_9

Dependency Updated:
  glibc-common.x86_64 0:2.17-326.el7_9     krb5-libs.x86_64 0:1.15.1-55.el7_9
  openssl-libs.x86_64 1:1.0.2k-26.el7_9    zlib.x86_64 0:1.2.7-21.el7_9

Complete!
 ---> f599c045c59a
Removing intermediate container d2c1f3c2f029
Step 9/16 : RUN yum install -y libxslt-devel -y gd-devel GeoIP GeoIP-devel pcre pcre-devel
 ---> Running in f95136a43bf0

Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
 * base: mirrors.ustc.edu.cn
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
Package pcre-8.32-17.el7.x86_64 already installed and latest version
Package pcre-devel-8.32-17.el7.x86_64 already installed and latest version
Resolving Dependencies
--> Running transaction check
---> Package GeoIP.x86_64 0:1.5.0-14.el7 will be installed
---> Package GeoIP-devel.x86_64 0:1.5.0-14.el7 will be installed
---> Package gd-devel.x86_64 0:2.0.35-27.el7_9 will be installed
--> Processing Dependency: gd = 2.0.35-27.el7_9 for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libpng-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libjpeg-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libXpm-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libX11-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: freetype-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: fontconfig-devel for package: gd-devel-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libgd.so.2()(64bit) for package: gd-devel-2.0.35-27.el7_9.x86_64
---> Package libxslt-devel.x86_64 0:1.1.28-6.el7 will be installed
--> Processing Dependency: libxslt = 1.1.28-6.el7 for package: libxslt-devel-1.1.28-6.el7.x86_64
--> Processing Dependency: pkgconfig(libxml-2.0) for package: libxslt-devel-1.1.28-6.el7.x86_64
--> Processing Dependency: libgcrypt-devel for package: libxslt-devel-1.1.28-6.el7.x86_64
--> Processing Dependency: libxslt.so.1()(64bit) for package: libxslt-devel-1.1.28-6.el7.x86_64
--> Processing Dependency: libexslt.so.0()(64bit) for package: libxslt-devel-1.1.28-6.el7.x86_64
--> Running transaction check
---> Package fontconfig-devel.x86_64 0:2.13.0-4.3.el7 will be installed
--> Processing Dependency: fontconfig(x86-64) = 2.13.0-4.3.el7 for package: fontconfig-devel-2.13.0-4.3.el7.x86_64
--> Processing Dependency: pkgconfig(uuid) for package: fontconfig-devel-2.13.0-4.3.el7.x86_64
--> Processing Dependency: pkgconfig(expat) for package: fontconfig-devel-2.13.0-4.3.el7.x86_64
--> Processing Dependency: gettext for package: fontconfig-devel-2.13.0-4.3.el7.x86_64
--> Processing Dependency: libfontconfig.so.1()(64bit) for package: fontconfig-devel-2.13.0-4.3.el7.x86_64
---> Package freetype-devel.x86_64 0:2.8-14.el7_9.1 will be installed
--> Processing Dependency: freetype = 2.8-14.el7_9.1 for package: freetype-devel-2.8-14.el7_9.1.x86_64
--> Processing Dependency: libfreetype.so.6()(64bit) for package: freetype-devel-2.8-14.el7_9.1.x86_64
---> Package gd.x86_64 0:2.0.35-27.el7_9 will be installed
--> Processing Dependency: libpng15.so.15(PNG15_0)(64bit) for package: gd-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libjpeg.so.62(LIBJPEG_6.2)(64bit) for package: gd-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libpng15.so.15()(64bit) for package: gd-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libjpeg.so.62()(64bit) for package: gd-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libXpm.so.4()(64bit) for package: gd-2.0.35-27.el7_9.x86_64
--> Processing Dependency: libX11.so.6()(64bit) for package: gd-2.0.35-27.el7_9.x86_64
---> Package libX11-devel.x86_64 0:1.6.7-4.el7_9 will be installed
--> Processing Dependency: pkgconfig(xcb) >= 1.11.1 for package: libX11-devel-1.6.7-4.el7_9.x86_64
--> Processing Dependency: pkgconfig(xproto) for package: libX11-devel-1.6.7-4.el7_9.x86_64
--> Processing Dependency: pkgconfig(xcb) for package: libX11-devel-1.6.7-4.el7_9.x86_64
--> Processing Dependency: pkgconfig(kbproto) for package: libX11-devel-1.6.7-4.el7_9.x86_64
---> Package libXpm-devel.x86_64 0:3.5.12-2.el7_9 will be installed
--> Processing Dependency: libXt.so.6()(64bit) for package: libXpm-devel-3.5.12-2.el7_9.x86_64
--> Processing Dependency: libXext.so.6()(64bit) for package: libXpm-devel-3.5.12-2.el7_9.x86_64
---> Package libgcrypt-devel.x86_64 0:1.5.3-14.el7 will be installed
--> Processing Dependency: libgpg-error-devel for package: libgcrypt-devel-1.5.3-14.el7.x86_64
---> Package libjpeg-turbo-devel.x86_64 0:1.2.90-8.el7 will be installed
---> Package libpng-devel.x86_64 2:1.5.13-8.el7 will be installed
---> Package libxml2-devel.x86_64 0:2.9.1-6.el7_9.6 will be installed
--> Processing Dependency: libxml2 = 2.9.1-6.el7_9.6 for package: libxml2-devel-2.9.1-6.el7_9.6.x86_64
--> Processing Dependency: xz-devel for package: libxml2-devel-2.9.1-6.el7_9.6.x86_64
---> Package libxslt.x86_64 0:1.1.28-6.el7 will be installed
--> Running transaction check
---> Package expat-devel.x86_64 0:2.1.0-15.el7_9 will be installed
--> Processing Dependency: expat = 2.1.0-15.el7_9 for package: expat-devel-2.1.0-15.el7_9.x86_64
---> Package fontconfig.x86_64 0:2.13.0-4.3.el7 will be installed
--> Processing Dependency: fontpackages-filesystem for package: fontconfig-2.13.0-4.3.el7.x86_64
--> Processing Dependency: dejavu-sans-fonts for package: fontconfig-2.13.0-4.3.el7.x86_64
---> Package freetype.x86_64 0:2.8-14.el7_9.1 will be installed
---> Package gettext.x86_64 0:0.19.8.1-3.el7 will be installed
--> Processing Dependency: gettext-libs(x86-64) = 0.19.8.1-3.el7 for package: gettext-0.19.8.1-3.el7.x86_64
--> Processing Dependency: libunistring.so.0()(64bit) for package: gettext-0.19.8.1-3.el7.x86_64
--> Processing Dependency: libgettextsrc-0.19.8.1.so()(64bit) for package: gettext-0.19.8.1-3.el7.x86_64
--> Processing Dependency: libgettextlib-0.19.8.1.so()(64bit) for package: gettext-0.19.8.1-3.el7.x86_64
--> Processing Dependency: libcroco-0.6.so.3()(64bit) for package: gettext-0.19.8.1-3.el7.x86_64
---> Package libX11.x86_64 0:1.6.7-4.el7_9 will be installed
--> Processing Dependency: libX11-common >= 1.6.7-4.el7_9 for package: libX11-1.6.7-4.el7_9.x86_64
--> Processing Dependency: libxcb.so.1()(64bit) for package: libX11-1.6.7-4.el7_9.x86_64
---> Package libXext.x86_64 0:1.3.3-3.el7 will be installed
---> Package libXpm.x86_64 0:3.5.12-2.el7_9 will be installed
---> Package libXt.x86_64 0:1.1.5-3.el7 will be installed
--> Processing Dependency: libSM.so.6()(64bit) for package: libXt-1.1.5-3.el7.x86_64
--> Processing Dependency: libICE.so.6()(64bit) for package: libXt-1.1.5-3.el7.x86_64
---> Package libgpg-error-devel.x86_64 0:1.12-3.el7 will be installed
---> Package libjpeg-turbo.x86_64 0:1.2.90-8.el7 will be installed
---> Package libpng.x86_64 2:1.5.13-8.el7 will be installed
---> Package libuuid-devel.x86_64 0:2.23.2-65.el7_9.1 will be installed
--> Processing Dependency: libuuid = 2.23.2-65.el7_9.1 for package: libuuid-devel-2.23.2-65.el7_9.1.x86_64
---> Package libxcb-devel.x86_64 0:1.13-1.el7 will be installed
--> Processing Dependency: pkgconfig(xau) >= 0.99.2 for package: libxcb-devel-1.13-1.el7.x86_64
---> Package libxml2.x86_64 0:2.9.1-6.el7.5 will be updated
--> Processing Dependency: libxml2 = 2.9.1-6.el7.5 for package: libxml2-python-2.9.1-6.el7.5.x86_64
---> Package libxml2.x86_64 0:2.9.1-6.el7_9.6 will be an update
---> Package xorg-x11-proto-devel.noarch 0:2018.4-1.el7 will be installed
---> Package xz-devel.x86_64 0:5.2.2-2.el7_9 will be installed
--> Processing Dependency: xz-libs = 5.2.2-2.el7_9 for package: xz-devel-5.2.2-2.el7_9.x86_64
--> Running transaction check
---> Package dejavu-sans-fonts.noarch 0:2.33-6.el7 will be installed
--> Processing Dependency: dejavu-fonts-common = 2.33-6.el7 for package: dejavu-sans-fonts-2.33-6.el7.noarch
---> Package expat.x86_64 0:2.1.0-12.el7 will be updated
---> Package expat.x86_64 0:2.1.0-15.el7_9 will be an update
---> Package fontpackages-filesystem.noarch 0:1.44-8.el7 will be installed
---> Package gettext-libs.x86_64 0:0.19.8.1-3.el7 will be installed
---> Package libICE.x86_64 0:1.0.9-9.el7 will be installed
---> Package libSM.x86_64 0:1.2.2-2.el7 will be installed
---> Package libX11-common.noarch 0:1.6.7-4.el7_9 will be installed
---> Package libXau-devel.x86_64 0:1.0.8-2.1.el7 will be installed
--> Processing Dependency: libXau = 1.0.8-2.1.el7 for package: libXau-devel-1.0.8-2.1.el7.x86_64
--> Processing Dependency: libXau.so.6()(64bit) for package: libXau-devel-1.0.8-2.1.el7.x86_64
---> Package libcroco.x86_64 0:0.6.12-6.el7_9 will be installed
---> Package libunistring.x86_64 0:0.9.3-9.el7 will be installed
---> Package libuuid.x86_64 0:2.23.2-65.el7 will be updated
--> Processing Dependency: libuuid = 2.23.2-65.el7 for package: libmount-2.23.2-65.el7.x86_64
--> Processing Dependency: libuuid = 2.23.2-65.el7 for package: util-linux-2.23.2-65.el7.x86_64
--> Processing Dependency: libuuid = 2.23.2-65.el7 for package: libblkid-2.23.2-65.el7.x86_64
---> Package libuuid.x86_64 0:2.23.2-65.el7_9.1 will be an update
---> Package libxcb.x86_64 0:1.13-1.el7 will be installed
---> Package libxml2-python.x86_64 0:2.9.1-6.el7.5 will be updated
---> Package libxml2-python.x86_64 0:2.9.1-6.el7_9.6 will be an update
---> Package xz-libs.x86_64 0:5.2.2-1.el7 will be updated
--> Processing Dependency: xz-libs = 5.2.2-1.el7 for package: xz-5.2.2-1.el7.x86_64
---> Package xz-libs.x86_64 0:5.2.2-2.el7_9 will be an update
--> Running transaction check
---> Package dejavu-fonts-common.noarch 0:2.33-6.el7 will be installed
---> Package libXau.x86_64 0:1.0.8-2.1.el7 will be installed
---> Package libblkid.x86_64 0:2.23.2-65.el7 will be updated
---> Package libblkid.x86_64 0:2.23.2-65.el7_9.1 will be an update
---> Package libmount.x86_64 0:2.23.2-65.el7 will be updated
---> Package libmount.x86_64 0:2.23.2-65.el7_9.1 will be an update
---> Package util-linux.x86_64 0:2.23.2-65.el7 will be updated
---> Package util-linux.x86_64 0:2.23.2-65.el7_9.1 will be an update
--> Processing Dependency: libsmartcols = 2.23.2-65.el7_9.1 for package: util-linux-2.23.2-65.el7_9.1.x86_64
---> Package xz.x86_64 0:5.2.2-1.el7 will be updated
---> Package xz.x86_64 0:5.2.2-2.el7_9 will be an update
--> Running transaction check
---> Package libsmartcols.x86_64 0:2.23.2-65.el7 will be updated
---> Package libsmartcols.x86_64 0:2.23.2-65.el7_9.1 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                     Arch       Version               Repository   Size
================================================================================
Installing:
 GeoIP                       x86_64     1.5.0-14.el7          base        1.5 M
 GeoIP-devel                 x86_64     1.5.0-14.el7          base         14 k
 gd-devel                    x86_64     2.0.35-27.el7_9       updates      79 k
 libxslt-devel               x86_64     1.1.28-6.el7          base        309 k
Installing for dependencies:
 dejavu-fonts-common         noarch     2.33-6.el7            base         64 k
 dejavu-sans-fonts           noarch     2.33-6.el7            base        1.4 M
 expat-devel                 x86_64     2.1.0-15.el7_9        updates      58 k
 fontconfig                  x86_64     2.13.0-4.3.el7        base        254 k
 fontconfig-devel            x86_64     2.13.0-4.3.el7        base        138 k
 fontpackages-filesystem     noarch     1.44-8.el7            base        9.9 k
 freetype                    x86_64     2.8-14.el7_9.1        updates     380 k
 freetype-devel              x86_64     2.8-14.el7_9.1        updates     447 k
 gd                          x86_64     2.0.35-27.el7_9       updates     146 k
 gettext                     x86_64     0.19.8.1-3.el7        base        1.0 M
 gettext-libs                x86_64     0.19.8.1-3.el7        base        502 k
 libICE                      x86_64     1.0.9-9.el7           base         66 k
 libSM                       x86_64     1.2.2-2.el7           base         39 k
 libX11                      x86_64     1.6.7-4.el7_9         updates     607 k
 libX11-common               noarch     1.6.7-4.el7_9         updates     164 k
 libX11-devel                x86_64     1.6.7-4.el7_9         updates     981 k
 libXau                      x86_64     1.0.8-2.1.el7         base         29 k
 libXau-devel                x86_64     1.0.8-2.1.el7         base         14 k
 libXext                     x86_64     1.3.3-3.el7           base         39 k
 libXpm                      x86_64     3.5.12-2.el7_9        updates      56 k
 libXpm-devel                x86_64     3.5.12-2.el7_9        updates      37 k
 libXt                       x86_64     1.1.5-3.el7           base        173 k
 libcroco                    x86_64     0.6.12-6.el7_9        updates     105 k
 libgcrypt-devel             x86_64     1.5.3-14.el7          base        129 k
 libgpg-error-devel          x86_64     1.12-3.el7            base         16 k
 libjpeg-turbo               x86_64     1.2.90-8.el7          base        135 k
 libjpeg-turbo-devel         x86_64     1.2.90-8.el7          base         99 k
 libpng                      x86_64     2:1.5.13-8.el7        base        213 k
 libpng-devel                x86_64     2:1.5.13-8.el7        base        122 k
 libunistring                x86_64     0.9.3-9.el7           base        293 k
 libuuid-devel               x86_64     2.23.2-65.el7_9.1     updates      93 k
 libxcb                      x86_64     1.13-1.el7            base        214 k
 libxcb-devel                x86_64     1.13-1.el7            base        1.1 M
 libxml2-devel               x86_64     2.9.1-6.el7_9.6       updates     1.1 M
 libxslt                     x86_64     1.1.28-6.el7          base        242 k
 xorg-x11-proto-devel        noarch     2018.4-1.el7          base        280 k
 xz-devel                    x86_64     5.2.2-2.el7_9         updates      46 k
Updating for dependencies:
 expat                       x86_64     2.1.0-15.el7_9        updates      83 k
 libblkid                    x86_64     2.23.2-65.el7_9.1     updates     183 k
 libmount                    x86_64     2.23.2-65.el7_9.1     updates     185 k
 libsmartcols                x86_64     2.23.2-65.el7_9.1     updates     143 k
 libuuid                     x86_64     2.23.2-65.el7_9.1     updates      84 k
 libxml2                     x86_64     2.9.1-6.el7_9.6       updates     668 k
 libxml2-python              x86_64     2.9.1-6.el7_9.6       updates     247 k
 util-linux                  x86_64     2.23.2-65.el7_9.1     updates     2.0 M
 xz                          x86_64     5.2.2-2.el7_9         updates     229 k
 xz-libs                     x86_64     5.2.2-2.el7_9         updates     103 k

Transaction Summary
================================================================================
Install  4 Packages (+37 Dependent packages)
Upgrade             ( 10 Dependent packages)

Total download size: 16 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
--------------------------------------------------------------------------------
Total                                              9.6 MB/s |  16 MB  00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : libuuid-2.23.2-65.el7_9.1.x86_64                            1/61
  Updating   : xz-libs-5.2.2-2.el7_9.x86_64                                2/61
  Updating   : libxml2-2.9.1-6.el7_9.6.x86_64                              3/61
  Installing : 2:libpng-1.5.13-8.el7.x86_64                                4/61
  Installing : freetype-2.8-14.el7_9.1.x86_64                              5/61
  Installing : 2:libpng-devel-1.5.13-8.el7.x86_64                          6/61
  Installing : freetype-devel-2.8-14.el7_9.1.x86_64                        7/61
  Installing : libcroco-0.6.12-6.el7_9.x86_64                              8/61
  Updating   : libblkid-2.23.2-65.el7_9.1.x86_64                           9/61
  Installing : libXau-1.0.8-2.1.el7.x86_64                                10/61
  Installing : libxcb-1.13-1.el7.x86_64                                   11/61
  Updating   : expat-2.1.0-15.el7_9.x86_64                                12/61
  Installing : libjpeg-turbo-1.2.90-8.el7.x86_64                          13/61
  Installing : xorg-x11-proto-devel-2018.4-1.el7.noarch                   14/61
  Installing : fontpackages-filesystem-1.44-8.el7.noarch                  15/61
  Installing : libICE-1.0.9-9.el7.x86_64                                  16/61
  Installing : libunistring-0.9.3-9.el7.x86_64                            17/61
  Installing : gettext-libs-0.19.8.1-3.el7.x86_64                         18/61
  Installing : gettext-0.19.8.1-3.el7.x86_64                              19/61
install-info: No such file or directory for /usr/share/info/gettext.info.gz
  Installing : libSM-1.2.2-2.el7.x86_64                                   20/61
  Installing : dejavu-fonts-common-2.33-6.el7.noarch                      21/61
  Installing : dejavu-sans-fonts-2.33-6.el7.noarch                        22/61
  Installing : fontconfig-2.13.0-4.3.el7.x86_64                           23/61
  Installing : libXau-devel-1.0.8-2.1.el7.x86_64                          24/61
  Installing : libxcb-devel-1.13-1.el7.x86_64                             25/61
  Installing : libjpeg-turbo-devel-1.2.90-8.el7.x86_64                    26/61
  Installing : expat-devel-2.1.0-15.el7_9.x86_64                          27/61
  Updating   : libmount-2.23.2-65.el7_9.1.x86_64                          28/61
  Installing : libxslt-1.1.28-6.el7.x86_64                                29/61
  Installing : xz-devel-5.2.2-2.el7_9.x86_64                              30/61
  Installing : libxml2-devel-2.9.1-6.el7_9.6.x86_64                       31/61
  Installing : libuuid-devel-2.23.2-65.el7_9.1.x86_64                     32/61
  Installing : fontconfig-devel-2.13.0-4.3.el7.x86_64                     33/61
  Installing : libX11-common-1.6.7-4.el7_9.noarch                         34/61
  Installing : libX11-1.6.7-4.el7_9.x86_64                                35/61
  Installing : libXpm-3.5.12-2.el7_9.x86_64                               36/61
  Installing : libX11-devel-1.6.7-4.el7_9.x86_64                          37/61
  Installing : gd-2.0.35-27.el7_9.x86_64                                  38/61
  Installing : libXext-1.3.3-3.el7.x86_64                                 39/61
  Installing : libXt-1.1.5-3.el7.x86_64                                   40/61
  Installing : libXpm-devel-3.5.12-2.el7_9.x86_64                         41/61
  Installing : libgpg-error-devel-1.12-3.el7.x86_64                       42/61
  Installing : libgcrypt-devel-1.5.3-14.el7.x86_64                        43/61
  Updating   : libsmartcols-2.23.2-65.el7_9.1.x86_64                      44/61
  Installing : GeoIP-1.5.0-14.el7.x86_64                                  45/61
  Installing : GeoIP-devel-1.5.0-14.el7.x86_64                            46/61
  Updating   : util-linux-2.23.2-65.el7_9.1.x86_64                        47/61
  Installing : libxslt-devel-1.1.28-6.el7.x86_64                          48/61
  Installing : gd-devel-2.0.35-27.el7_9.x86_64                            49/61
  Updating   : libxml2-python-2.9.1-6.el7_9.6.x86_64                      50/61
  Updating   : xz-5.2.2-2.el7_9.x86_64                                    51/61
  Cleanup    : util-linux-2.23.2-65.el7.x86_64                            52/61
  Cleanup    : libmount-2.23.2-65.el7.x86_64                              53/61
  Cleanup    : libxml2-python-2.9.1-6.el7.5.x86_64                        54/61
  Cleanup    : libxml2-2.9.1-6.el7.5.x86_64                               55/61
  Cleanup    : libblkid-2.23.2-65.el7.x86_64                              56/61
  Cleanup    : xz-5.2.2-1.el7.x86_64                                      57/61
  Cleanup    : xz-libs-5.2.2-1.el7.x86_64                                 58/61
  Cleanup    : libuuid-2.23.2-65.el7.x86_64                               59/61
  Cleanup    : libsmartcols-2.23.2-65.el7.x86_64                          60/61
  Cleanup    : expat-2.1.0-12.el7.x86_64                                  61/61
  Verifying  : libXext-1.3.3-3.el7.x86_64                                  1/61
  Verifying  : 2:libpng-devel-1.5.13-8.el7.x86_64                          2/61
  Verifying  : fontconfig-2.13.0-4.3.el7.x86_64                            3/61
  Verifying  : libxml2-devel-2.9.1-6.el7_9.6.x86_64                        4/61
  Verifying  : libxml2-2.9.1-6.el7_9.6.x86_64                              5/61
  Verifying  : libblkid-2.23.2-65.el7_9.1.x86_64                           6/61
  Verifying  : expat-devel-2.1.0-15.el7_9.x86_64                           7/61
  Verifying  : libjpeg-turbo-devel-1.2.90-8.el7.x86_64                     8/61
  Verifying  : libunistring-0.9.3-9.el7.x86_64                             9/61
  Verifying  : libmount-2.23.2-65.el7_9.1.x86_64                          10/61
  Verifying  : libXpm-3.5.12-2.el7_9.x86_64                               11/61
  Verifying  : libXt-1.1.5-3.el7.x86_64                                   12/61
  Verifying  : gettext-libs-0.19.8.1-3.el7.x86_64                         13/61
  Verifying  : libICE-1.0.9-9.el7.x86_64                                  14/61
  Verifying  : fontpackages-filesystem-1.44-8.el7.noarch                  15/61
  Verifying  : GeoIP-1.5.0-14.el7.x86_64                                  16/61
  Verifying  : fontconfig-devel-2.13.0-4.3.el7.x86_64                     17/61
  Verifying  : libuuid-devel-2.23.2-65.el7_9.1.x86_64                     18/61
  Verifying  : libcroco-0.6.12-6.el7_9.x86_64                             19/61
  Verifying  : libxslt-devel-1.1.28-6.el7.x86_64                          20/61
  Verifying  : xorg-x11-proto-devel-2018.4-1.el7.noarch                   21/61
  Verifying  : dejavu-fonts-common-2.33-6.el7.noarch                      22/61
  Verifying  : libX11-devel-1.6.7-4.el7_9.x86_64                          23/61
  Verifying  : libsmartcols-2.23.2-65.el7_9.1.x86_64                      24/61
  Verifying  : libxcb-1.13-1.el7.x86_64                                   25/61
  Verifying  : GeoIP-devel-1.5.0-14.el7.x86_64                            26/61
  Verifying  : libXpm-devel-3.5.12-2.el7_9.x86_64                         27/61
  Verifying  : xz-libs-5.2.2-2.el7_9.x86_64                               28/61
  Verifying  : libjpeg-turbo-1.2.90-8.el7.x86_64                          29/61
  Verifying  : util-linux-2.23.2-65.el7_9.1.x86_64                        30/61
  Verifying  : gd-devel-2.0.35-27.el7_9.x86_64                            31/61
  Verifying  : dejavu-sans-fonts-2.33-6.el7.noarch                        32/61
  Verifying  : 2:libpng-1.5.13-8.el7.x86_64                               33/61
  Verifying  : expat-2.1.0-15.el7_9.x86_64                                34/61
  Verifying  : xz-devel-5.2.2-2.el7_9.x86_64                              35/61
  Verifying  : freetype-devel-2.8-14.el7_9.1.x86_64                       36/61
  Verifying  : gd-2.0.35-27.el7_9.x86_64                                  37/61
  Verifying  : libgcrypt-devel-1.5.3-14.el7.x86_64                        38/61
  Verifying  : libxcb-devel-1.13-1.el7.x86_64                             39/61
  Verifying  : libuuid-2.23.2-65.el7_9.1.x86_64                           40/61
  Verifying  : gettext-0.19.8.1-3.el7.x86_64                              41/61
  Verifying  : libSM-1.2.2-2.el7.x86_64                                   42/61
  Verifying  : libxslt-1.1.28-6.el7.x86_64                                43/61
  Verifying  : libX11-1.6.7-4.el7_9.x86_64                                44/61
  Verifying  : libxml2-python-2.9.1-6.el7_9.6.x86_64                      45/61
  Verifying  : libXau-1.0.8-2.1.el7.x86_64                                46/61
  Verifying  : libgpg-error-devel-1.12-3.el7.x86_64                       47/61
  Verifying  : xz-5.2.2-2.el7_9.x86_64                                    48/61
  Verifying  : freetype-2.8-14.el7_9.1.x86_64                             49/61
  Verifying  : libX11-common-1.6.7-4.el7_9.noarch                         50/61
  Verifying  : libXau-devel-1.0.8-2.1.el7.x86_64                          51/61
  Verifying  : libuuid-2.23.2-65.el7.x86_64                               52/61
  Verifying  : expat-2.1.0-12.el7.x86_64                                  53/61
  Verifying  : libxml2-2.9.1-6.el7.5.x86_64                               54/61
  Verifying  : xz-libs-5.2.2-1.el7.x86_64                                 55/61
  Verifying  : libblkid-2.23.2-65.el7.x86_64                              56/61
  Verifying  : xz-5.2.2-1.el7.x86_64                                      57/61
  Verifying  : libxml2-python-2.9.1-6.el7.5.x86_64                        58/61
  Verifying  : libsmartcols-2.23.2-65.el7.x86_64                          59/61
  Verifying  : util-linux-2.23.2-65.el7.x86_64                            60/61
  Verifying  : libmount-2.23.2-65.el7.x86_64                              61/61

Installed:
  GeoIP.x86_64 0:1.5.0-14.el7           GeoIP-devel.x86_64 0:1.5.0-14.el7
  gd-devel.x86_64 0:2.0.35-27.el7_9     libxslt-devel.x86_64 0:1.1.28-6.el7

Dependency Installed:
  dejavu-fonts-common.noarch 0:2.33-6.el7
  dejavu-sans-fonts.noarch 0:2.33-6.el7
  expat-devel.x86_64 0:2.1.0-15.el7_9
  fontconfig.x86_64 0:2.13.0-4.3.el7
  fontconfig-devel.x86_64 0:2.13.0-4.3.el7
  fontpackages-filesystem.noarch 0:1.44-8.el7
  freetype.x86_64 0:2.8-14.el7_9.1
  freetype-devel.x86_64 0:2.8-14.el7_9.1
  gd.x86_64 0:2.0.35-27.el7_9
  gettext.x86_64 0:0.19.8.1-3.el7
  gettext-libs.x86_64 0:0.19.8.1-3.el7
  libICE.x86_64 0:1.0.9-9.el7
  libSM.x86_64 0:1.2.2-2.el7
  libX11.x86_64 0:1.6.7-4.el7_9
  libX11-common.noarch 0:1.6.7-4.el7_9
  libX11-devel.x86_64 0:1.6.7-4.el7_9
  libXau.x86_64 0:1.0.8-2.1.el7
  libXau-devel.x86_64 0:1.0.8-2.1.el7
  libXext.x86_64 0:1.3.3-3.el7
  libXpm.x86_64 0:3.5.12-2.el7_9
  libXpm-devel.x86_64 0:3.5.12-2.el7_9
  libXt.x86_64 0:1.1.5-3.el7
  libcroco.x86_64 0:0.6.12-6.el7_9
  libgcrypt-devel.x86_64 0:1.5.3-14.el7
  libgpg-error-devel.x86_64 0:1.12-3.el7
  libjpeg-turbo.x86_64 0:1.2.90-8.el7
  libjpeg-turbo-devel.x86_64 0:1.2.90-8.el7
  libpng.x86_64 2:1.5.13-8.el7
  libpng-devel.x86_64 2:1.5.13-8.el7
  libunistring.x86_64 0:0.9.3-9.el7
  libuuid-devel.x86_64 0:2.23.2-65.el7_9.1
  libxcb.x86_64 0:1.13-1.el7
  libxcb-devel.x86_64 0:1.13-1.el7
  libxml2-devel.x86_64 0:2.9.1-6.el7_9.6
  libxslt.x86_64 0:1.1.28-6.el7
  xorg-x11-proto-devel.noarch 0:2018.4-1.el7
  xz-devel.x86_64 0:5.2.2-2.el7_9

Dependency Updated:
  expat.x86_64 0:2.1.0-15.el7_9
  libblkid.x86_64 0:2.23.2-65.el7_9.1
  libmount.x86_64 0:2.23.2-65.el7_9.1
  libsmartcols.x86_64 0:2.23.2-65.el7_9.1
  libuuid.x86_64 0:2.23.2-65.el7_9.1
  libxml2.x86_64 0:2.9.1-6.el7_9.6
  libxml2-python.x86_64 0:2.9.1-6.el7_9.6
  util-linux.x86_64 0:2.23.2-65.el7_9.1
  xz.x86_64 0:5.2.2-2.el7_9
  xz-libs.x86_64 0:5.2.2-2.el7_9

Complete!
 ---> 405bc90fe433
Removing intermediate container f95136a43bf0
Step 10/16 : RUN useradd -M -s /sbin/nologin nginx
 ---> Running in 13531f2b5b8c

 ---> ed2ce01cc48d
Removing intermediate container 13531f2b5b8c
Step 11/16 : RUN ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-file-aio --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_degradation_module --with-http_stub_status_module && make && make install
 ---> Running in 6de06ff5648a

checking for OS
 + Linux 3.10.0-1160.53.1.el7.x86_64 x86_64
checking for C compiler ... found
 + using GNU C compiler
 + gcc version: 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC)
checking for gcc -pipe switch ... found
checking for -Wl,-E switch ... found
checking for gcc builtin atomic operations ... found
checking for C99 variadic macros ... found
checking for gcc variadic macros ... found
checking for gcc builtin 64 bit byteswap ... found
checking for unistd.h ... found
checking for inttypes.h ... found
checking for limits.h ... found
checking for sys/filio.h ... not found
checking for sys/param.h ... found
checking for sys/mount.h ... found
checking for sys/statvfs.h ... found
checking for crypt.h ... found
checking for Linux specific features
checking for epoll ... found
checking for EPOLLRDHUP ... found
checking for EPOLLEXCLUSIVE ... not found
checking for O_PATH ... found
checking for sendfile() ... found
checking for sendfile64() ... found
checking for sys/prctl.h ... found
checking for prctl(PR_SET_DUMPABLE) ... found
checking for prctl(PR_SET_KEEPCAPS) ... found
checking for capabilities ... found
checking for crypt_r() ... found
checking for sys/vfs.h ... found
checking for poll() ... found
checking for /dev/poll ... not found
checking for kqueue ... not found
checking for crypt() ... not found
checking for crypt() in libcrypt ... found
checking for F_READAHEAD ... not found
checking for posix_fadvise() ... found
checking for O_DIRECT ... found
checking for F_NOCACHE ... not found
checking for directio() ... not found
checking for statfs() ... found
checking for statvfs() ... found
checking for dlopen() ... not found
checking for dlopen() in libdl ... found
checking for sched_yield() ... found
checking for sched_setaffinity() ... found
checking for SO_SETFIB ... not found
checking for SO_REUSEPORT ... found
checking for SO_ACCEPTFILTER ... not found
checking for SO_BINDANY ... not found
checking for IP_TRANSPARENT ... found
checking for IP_BINDANY ... not found
checking for IP_BIND_ADDRESS_NO_PORT ... found
checking for IP_RECVDSTADDR ... not found
checking for IP_SENDSRCADDR ... not found
checking for IP_PKTINFO ... found
checking for IPV6_RECVPKTINFO ... found
checking for TCP_DEFER_ACCEPT ... found
checking for TCP_KEEPIDLE ... found
checking for TCP_FASTOPEN ... found
checking for TCP_INFO ... found
checking for accept4() ... found
checking for kqueue AIO support ... not found
checking for Linux AIO support ... found
checking for int size ... 4 bytes
checking for long size ... 8 bytes
checking for long long size ... 8 bytes
checking for void * size ... 8 bytes
checking for uint32_t ... found
checking for uint64_t ... found
checking for sig_atomic_t ... found
checking for sig_atomic_t size ... 4 bytes
checking for socklen_t ... found
checking for in_addr_t ... found
checking for in_port_t ... found
checking for rlim_t ... found
checking for uintptr_t ... uintptr_t found
checking for system byte ordering ... little endian
checking for size_t size ... 8 bytes
checking for off_t size ... 8 bytes
checking for time_t size ... 8 bytes
checking for AF_INET6 ... found
checking for setproctitle() ... not found
checking for pread() ... found
checking for pwrite() ... found
checking for pwritev() ... found
checking for sys_nerr ... found
checking for localtime_r() ... found
checking for clock_gettime(CLOCK_MONOTONIC) ... found
checking for posix_memalign() ... found
checking for memalign() ... found
checking for mmap(MAP_ANON|MAP_SHARED) ... found
checking for mmap("/dev/zero", MAP_SHARED) ... found
checking for System V shared memory ... found
checking for POSIX semaphores ... not found
checking for POSIX semaphores in libpthread ... found
checking for struct msghdr.msg_control ... found
checking for ioctl(FIONBIO) ... found
checking for ioctl(FIONREAD) ... found
checking for struct tm.tm_gmtoff ... found
checking for struct dirent.d_namlen ... not found
checking for struct dirent.d_type ... found
checking for sysconf(_SC_NPROCESSORS_ONLN) ... found
checking for sysconf(_SC_LEVEL1_DCACHE_LINESIZE) ... found
checking for openat(), fstatat() ... found
checking for getaddrinfo() ... found
checking for PCRE library ... found
checking for PCRE JIT support ... found
checking for OpenSSL library ... found
checking for zlib library ... found
checking for libxslt ... found
checking for libexslt ... found
checking for GD library ... found
checking for GD WebP support ... not found
checking for GeoIP library ... found
checking for GeoIP IPv6 support ... found
creating objs/Makefile

Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"

make -f objs/Makefile
make[1]: Entering directory `/usr/local/src/nginx-1.18.0'
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/nginx.o \
        src/core/nginx.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_log.o \
        src/core/ngx_log.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_palloc.o \
        src/core/ngx_palloc.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_array.o \
        src/core/ngx_array.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_list.o \
        src/core/ngx_list.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_hash.o \
        src/core/ngx_hash.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_buf.o \
        src/core/ngx_buf.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_queue.o \
        src/core/ngx_queue.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_output_chain.o \
        src/core/ngx_output_chain.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_string.o \
        src/core/ngx_string.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_parse.o \
        src/core/ngx_parse.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_parse_time.o \
        src/core/ngx_parse_time.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_inet.o \
        src/core/ngx_inet.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_file.o \
        src/core/ngx_file.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_crc32.o \
        src/core/ngx_crc32.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_murmurhash.o \
        src/core/ngx_murmurhash.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_md5.o \
        src/core/ngx_md5.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_sha1.o \
        src/core/ngx_sha1.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_rbtree.o \
        src/core/ngx_rbtree.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_radix_tree.o \
        src/core/ngx_radix_tree.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_slab.o \
        src/core/ngx_slab.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_times.o \
        src/core/ngx_times.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_shmtx.o \
        src/core/ngx_shmtx.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_connection.o \
        src/core/ngx_connection.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_cycle.o \
        src/core/ngx_cycle.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_spinlock.o \
        src/core/ngx_spinlock.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_rwlock.o \
        src/core/ngx_rwlock.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_cpuinfo.o \
        src/core/ngx_cpuinfo.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_conf_file.o \
        src/core/ngx_conf_file.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_module.o \
        src/core/ngx_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_resolver.o \
        src/core/ngx_resolver.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_open_file_cache.o \
        src/core/ngx_open_file_cache.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_crypt.o \
        src/core/ngx_crypt.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_proxy_protocol.o \
        src/core/ngx_proxy_protocol.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_syslog.o \
        src/core/ngx_syslog.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event.o \
        src/event/ngx_event.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_timer.o \
        src/event/ngx_event_timer.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_posted.o \
        src/event/ngx_event_posted.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_accept.o \
        src/event/ngx_event_accept.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_udp.o \
        src/event/ngx_event_udp.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_connect.o \
        src/event/ngx_event_connect.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_pipe.o \
        src/event/ngx_event_pipe.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_time.o \
        src/os/unix/ngx_time.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_errno.o \
        src/os/unix/ngx_errno.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_alloc.o \
        src/os/unix/ngx_alloc.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_files.o \
        src/os/unix/ngx_files.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_socket.o \
        src/os/unix/ngx_socket.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_recv.o \
        src/os/unix/ngx_recv.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_readv_chain.o \
        src/os/unix/ngx_readv_chain.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_udp_recv.o \
        src/os/unix/ngx_udp_recv.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_send.o \
        src/os/unix/ngx_send.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_writev_chain.o \
        src/os/unix/ngx_writev_chain.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_udp_send.o \
        src/os/unix/ngx_udp_send.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_udp_sendmsg_chain.o \
        src/os/unix/ngx_udp_sendmsg_chain.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_channel.o \
        src/os/unix/ngx_channel.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_shmem.o \
        src/os/unix/ngx_shmem.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_process.o \
        src/os/unix/ngx_process.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_daemon.o \
        src/os/unix/ngx_daemon.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_setaffinity.o \
        src/os/unix/ngx_setaffinity.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_setproctitle.o \
        src/os/unix/ngx_setproctitle.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_posix_init.o \
        src/os/unix/ngx_posix_init.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_user.o \
        src/os/unix/ngx_user.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_dlopen.o \
        src/os/unix/ngx_dlopen.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_process_cycle.o \
        src/os/unix/ngx_process_cycle.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_linux_init.o \
        src/os/unix/ngx_linux_init.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/modules/ngx_epoll_module.o \
        src/event/modules/ngx_epoll_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_linux_sendfile_chain.o \
        src/os/unix/ngx_linux_sendfile_chain.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/os/unix/ngx_linux_aio_read.o \
        src/os/unix/ngx_linux_aio_read.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_openssl.o \
        src/event/ngx_event_openssl.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/event/ngx_event_openssl_stapling.o \
        src/event/ngx_event_openssl_stapling.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/src/core/ngx_regex.o \
        src/core/ngx_regex.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http.o \
        src/http/ngx_http.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_core_module.o \
        src/http/ngx_http_core_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_special_response.o \
        src/http/ngx_http_special_response.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_request.o \
        src/http/ngx_http_request.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_parse.o \
        src/http/ngx_http_parse.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_log_module.o \
        src/http/modules/ngx_http_log_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_request_body.o \
        src/http/ngx_http_request_body.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_variables.o \
        src/http/ngx_http_variables.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_script.o \
        src/http/ngx_http_script.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_upstream.o \
        src/http/ngx_http_upstream.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_upstream_round_robin.o \
        src/http/ngx_http_upstream_round_robin.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_file_cache.o \
        src/http/ngx_http_file_cache.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_write_filter_module.o \
        src/http/ngx_http_write_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_header_filter_module.o \
        src/http/ngx_http_header_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_chunked_filter_module.o \
        src/http/modules/ngx_http_chunked_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_range_filter_module.o \
        src/http/modules/ngx_http_range_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_gzip_filter_module.o \
        src/http/modules/ngx_http_gzip_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_postpone_filter_module.o \
        src/http/ngx_http_postpone_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_ssi_filter_module.o \
        src/http/modules/ngx_http_ssi_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_charset_filter_module.o \
        src/http/modules/ngx_http_charset_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_xslt_filter_module.o \
        src/http/modules/ngx_http_xslt_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_image_filter_module.o \
        src/http/modules/ngx_http_image_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_sub_filter_module.o \
        src/http/modules/ngx_http_sub_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_addition_filter_module.o \
        src/http/modules/ngx_http_addition_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_userid_filter_module.o \
        src/http/modules/ngx_http_userid_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_headers_filter_module.o \
        src/http/modules/ngx_http_headers_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/ngx_http_copy_filter_module.o \
        src/http/ngx_http_copy_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_not_modified_filter_module.o \
        src/http/modules/ngx_http_not_modified_filter_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_static_module.o \
        src/http/modules/ngx_http_static_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_dav_module.o \
        src/http/modules/ngx_http_dav_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_autoindex_module.o \
        src/http/modules/ngx_http_autoindex_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_index_module.o \
        src/http/modules/ngx_http_index_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_mirror_module.o \
        src/http/modules/ngx_http_mirror_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_try_files_module.o \
        src/http/modules/ngx_http_try_files_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_auth_basic_module.o \
        src/http/modules/ngx_http_auth_basic_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_access_module.o \
        src/http/modules/ngx_http_access_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_limit_conn_module.o \
        src/http/modules/ngx_http_limit_conn_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_limit_req_module.o \
        src/http/modules/ngx_http_limit_req_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_realip_module.o \
        src/http/modules/ngx_http_realip_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_geo_module.o \
        src/http/modules/ngx_http_geo_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_geoip_module.o \
        src/http/modules/ngx_http_geoip_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_map_module.o \
        src/http/modules/ngx_http_map_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_split_clients_module.o \
        src/http/modules/ngx_http_split_clients_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_referer_module.o \
        src/http/modules/ngx_http_referer_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_rewrite_module.o \
        src/http/modules/ngx_http_rewrite_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_ssl_module.o \
        src/http/modules/ngx_http_ssl_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_proxy_module.o \
        src/http/modules/ngx_http_proxy_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_fastcgi_module.o \
        src/http/modules/ngx_http_fastcgi_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_uwsgi_module.o \
        src/http/modules/ngx_http_uwsgi_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_scgi_module.o \
        src/http/modules/ngx_http_scgi_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_memcached_module.o \
        src/http/modules/ngx_http_memcached_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_empty_gif_module.o \
        src/http/modules/ngx_http_empty_gif_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_browser_module.o \
        src/http/modules/ngx_http_browser_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_degradation_module.o \
        src/http/modules/ngx_http_degradation_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_flv_module.o \
        src/http/modules/ngx_http_flv_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_mp4_module.o \
        src/http/modules/ngx_http_mp4_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_hash_module.o \
        src/http/modules/ngx_http_upstream_hash_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_ip_hash_module.o \
        src/http/modules/ngx_http_upstream_ip_hash_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_least_conn_module.o \
        src/http/modules/ngx_http_upstream_least_conn_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_random_module.o \
        src/http/modules/ngx_http_upstream_random_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_keepalive_module.o \
        src/http/modules/ngx_http_upstream_keepalive_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_upstream_zone_module.o \
        src/http/modules/ngx_http_upstream_zone_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs -I src/http -I src/http/modules \
        -o objs/src/http/modules/ngx_http_stub_status_module.o \
        src/http/modules/ngx_http_stub_status_module.c
cc -c -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g  -I src/core -I src/event -I src/event/modules -I src/os/unix -I /usr/include/libxml2 -I objs \
        -o objs/ngx_modules.o \
        objs/ngx_modules.c
cc -o objs/nginx \
objs/src/core/nginx.o \
objs/src/core/ngx_log.o \
objs/src/core/ngx_palloc.o \
objs/src/core/ngx_array.o \
objs/src/core/ngx_list.o \
objs/src/core/ngx_hash.o \
objs/src/core/ngx_buf.o \
objs/src/core/ngx_queue.o \
objs/src/core/ngx_output_chain.o \
objs/src/core/ngx_string.o \
objs/src/core/ngx_parse.o \
objs/src/core/ngx_parse_time.o \
objs/src/core/ngx_inet.o \
objs/src/core/ngx_file.o \
objs/src/core/ngx_crc32.o \
objs/src/core/ngx_murmurhash.o \
objs/src/core/ngx_md5.o \
objs/src/core/ngx_sha1.o \
objs/src/core/ngx_rbtree.o \
objs/src/core/ngx_radix_tree.o \
objs/src/core/ngx_slab.o \
objs/src/core/ngx_times.o \
objs/src/core/ngx_shmtx.o \
objs/src/core/ngx_connection.o \
objs/src/core/ngx_cycle.o \
objs/src/core/ngx_spinlock.o \
objs/src/core/ngx_rwlock.o \
objs/src/core/ngx_cpuinfo.o \
objs/src/core/ngx_conf_file.o \
objs/src/core/ngx_module.o \
objs/src/core/ngx_resolver.o \
objs/src/core/ngx_open_file_cache.o \
objs/src/core/ngx_crypt.o \
objs/src/core/ngx_proxy_protocol.o \
objs/src/core/ngx_syslog.o \
objs/src/event/ngx_event.o \
objs/src/event/ngx_event_timer.o \
objs/src/event/ngx_event_posted.o \
objs/src/event/ngx_event_accept.o \
objs/src/event/ngx_event_udp.o \
objs/src/event/ngx_event_connect.o \
objs/src/event/ngx_event_pipe.o \
objs/src/os/unix/ngx_time.o \
objs/src/os/unix/ngx_errno.o \
objs/src/os/unix/ngx_alloc.o \
objs/src/os/unix/ngx_files.o \
objs/src/os/unix/ngx_socket.o \
objs/src/os/unix/ngx_recv.o \
objs/src/os/unix/ngx_readv_chain.o \
objs/src/os/unix/ngx_udp_recv.o \
objs/src/os/unix/ngx_send.o \
objs/src/os/unix/ngx_writev_chain.o \
objs/src/os/unix/ngx_udp_send.o \
objs/src/os/unix/ngx_udp_sendmsg_chain.o \
objs/src/os/unix/ngx_channel.o \
objs/src/os/unix/ngx_shmem.o \
objs/src/os/unix/ngx_process.o \
objs/src/os/unix/ngx_daemon.o \
objs/src/os/unix/ngx_setaffinity.o \
objs/src/os/unix/ngx_setproctitle.o \
objs/src/os/unix/ngx_posix_init.o \
objs/src/os/unix/ngx_user.o \
objs/src/os/unix/ngx_dlopen.o \
objs/src/os/unix/ngx_process_cycle.o \
objs/src/os/unix/ngx_linux_init.o \
objs/src/event/modules/ngx_epoll_module.o \
objs/src/os/unix/ngx_linux_sendfile_chain.o \
objs/src/os/unix/ngx_linux_aio_read.o \
objs/src/event/ngx_event_openssl.o \
objs/src/event/ngx_event_openssl_stapling.o \
objs/src/core/ngx_regex.o \
objs/src/http/ngx_http.o \
objs/src/http/ngx_http_core_module.o \
objs/src/http/ngx_http_special_response.o \
objs/src/http/ngx_http_request.o \
objs/src/http/ngx_http_parse.o \
objs/src/http/modules/ngx_http_log_module.o \
objs/src/http/ngx_http_request_body.o \
objs/src/http/ngx_http_variables.o \
objs/src/http/ngx_http_script.o \
objs/src/http/ngx_http_upstream.o \
objs/src/http/ngx_http_upstream_round_robin.o \
objs/src/http/ngx_http_file_cache.o \
objs/src/http/ngx_http_write_filter_module.o \
objs/src/http/ngx_http_header_filter_module.o \
objs/src/http/modules/ngx_http_chunked_filter_module.o \
objs/src/http/modules/ngx_http_range_filter_module.o \
objs/src/http/modules/ngx_http_gzip_filter_module.o \
objs/src/http/ngx_http_postpone_filter_module.o \
objs/src/http/modules/ngx_http_ssi_filter_module.o \
objs/src/http/modules/ngx_http_charset_filter_module.o \
objs/src/http/modules/ngx_http_xslt_filter_module.o \
objs/src/http/modules/ngx_http_image_filter_module.o \
objs/src/http/modules/ngx_http_sub_filter_module.o \
objs/src/http/modules/ngx_http_addition_filter_module.o \
objs/src/http/modules/ngx_http_userid_filter_module.o \
objs/src/http/modules/ngx_http_headers_filter_module.o \
objs/src/http/ngx_http_copy_filter_module.o \
objs/src/http/modules/ngx_http_not_modified_filter_module.o \
objs/src/http/modules/ngx_http_static_module.o \
objs/src/http/modules/ngx_http_dav_module.o \
objs/src/http/modules/ngx_http_autoindex_module.o \
objs/src/http/modules/ngx_http_index_module.o \
objs/src/http/modules/ngx_http_mirror_module.o \
objs/src/http/modules/ngx_http_try_files_module.o \
objs/src/http/modules/ngx_http_auth_basic_module.o \
objs/src/http/modules/ngx_http_access_module.o \
objs/src/http/modules/ngx_http_limit_conn_module.o \
objs/src/http/modules/ngx_http_limit_req_module.o \
objs/src/http/modules/ngx_http_realip_module.o \
objs/src/http/modules/ngx_http_geo_module.o \
objs/src/http/modules/ngx_http_geoip_module.o \
objs/src/http/modules/ngx_http_map_module.o \
objs/src/http/modules/ngx_http_split_clients_module.o \
objs/src/http/modules/ngx_http_referer_module.o \
objs/src/http/modules/ngx_http_rewrite_module.o \
objs/src/http/modules/ngx_http_ssl_module.o \
objs/src/http/modules/ngx_http_proxy_module.o \
objs/src/http/modules/ngx_http_fastcgi_module.o \
objs/src/http/modules/ngx_http_uwsgi_module.o \
objs/src/http/modules/ngx_http_scgi_module.o \
objs/src/http/modules/ngx_http_memcached_module.o \
objs/src/http/modules/ngx_http_empty_gif_module.o \
objs/src/http/modules/ngx_http_browser_module.o \
objs/src/http/modules/ngx_http_degradation_module.o \
objs/src/http/modules/ngx_http_flv_module.o \
objs/src/http/modules/ngx_http_mp4_module.o \
objs/src/http/modules/ngx_http_upstream_hash_module.o \
objs/src/http/modules/ngx_http_upstream_ip_hash_module.o \
objs/src/http/modules/ngx_http_upstream_least_conn_module.o \
objs/src/http/modules/ngx_http_upstream_random_module.o \
objs/src/http/modules/ngx_http_upstream_keepalive_module.o \
objs/src/http/modules/ngx_http_upstream_zone_module.o \
objs/src/http/modules/ngx_http_stub_status_module.o \
objs/ngx_modules.o \
-ldl -lpthread -lcrypt -lpcre -lssl -lcrypto -ldl -lpthread -lz -lxml2 -lxslt -lexslt -lgd -lGeoIP \
-Wl,-E
sed -e "s|%%PREFIX%%|/usr/local/nginx|" \
        -e "s|%%PID_PATH%%|/usr/local/nginx/logs/nginx.pid|" \
        -e "s|%%CONF_PATH%%|/usr/local/nginx/conf/nginx.conf|" \
        -e "s|%%ERROR_LOG_PATH%%|/usr/local/nginx/logs/error.log|" \
        < man/nginx.8 > objs/nginx.8
make[1]: Leaving directory `/usr/local/src/nginx-1.18.0'
make -f objs/Makefile install
make[1]: Entering directory `/usr/local/src/nginx-1.18.0'
test -d '/usr/local/nginx' || mkdir -p '/usr/local/nginx'
test -d '/usr/local/nginx/sbin' \
        || mkdir -p '/usr/local/nginx/sbin'
test ! -f '/usr/local/nginx/sbin/nginx' \
        || mv '/usr/local/nginx/sbin/nginx' \
                '/usr/local/nginx/sbin/nginx.old'
cp objs/nginx '/usr/local/nginx/sbin/nginx'
test -d '/usr/local/nginx/conf' \
        || mkdir -p '/usr/local/nginx/conf'
cp conf/koi-win '/usr/local/nginx/conf'
cp conf/koi-utf '/usr/local/nginx/conf'
cp conf/win-utf '/usr/local/nginx/conf'
test -f '/usr/local/nginx/conf/mime.types' \
        || cp conf/mime.types '/usr/local/nginx/conf'
cp conf/mime.types '/usr/local/nginx/conf/mime.types.default'
test -f '/usr/local/nginx/conf/fastcgi_params' \
        || cp conf/fastcgi_params '/usr/local/nginx/conf'
cp conf/fastcgi_params \
        '/usr/local/nginx/conf/fastcgi_params.default'
test -f '/usr/local/nginx/conf/fastcgi.conf' \
        || cp conf/fastcgi.conf '/usr/local/nginx/conf'
cp conf/fastcgi.conf '/usr/local/nginx/conf/fastcgi.conf.default'
test -f '/usr/local/nginx/conf/uwsgi_params' \
        || cp conf/uwsgi_params '/usr/local/nginx/conf'
cp conf/uwsgi_params \
        '/usr/local/nginx/conf/uwsgi_params.default'
test -f '/usr/local/nginx/conf/scgi_params' \
        || cp conf/scgi_params '/usr/local/nginx/conf'
cp conf/scgi_params \
        '/usr/local/nginx/conf/scgi_params.default'
test -f '/usr/local/nginx/conf/nginx.conf' \
        || cp conf/nginx.conf '/usr/local/nginx/conf/nginx.conf'
cp conf/nginx.conf '/usr/local/nginx/conf/nginx.conf.default'
test -d '/usr/local/nginx/logs' \
        || mkdir -p '/usr/local/nginx/logs'
test -d '/usr/local/nginx/logs' \
        || mkdir -p '/usr/local/nginx/logs'
test -d '/usr/local/nginx/html' \
        || cp -R html '/usr/local/nginx'
test -d '/usr/local/nginx/logs' \
        || mkdir -p '/usr/local/nginx/logs'
make[1]: Leaving directory `/usr/local/src/nginx-1.18.0'
 ---> a1ce4ccb9cec
Removing intermediate container 6de06ff5648a
Step 12/16 : WORKDIR /usr/local/nginx/conf
 ---> eefe3d1c8254
Removing intermediate container d33c2cad77b2
Step 13/16 : ENV PATH /usr/local/nginx/sbin:$PATH
 ---> Running in ad156a6b22f6
 ---> 87a613c393ca
Removing intermediate container ad156a6b22f6
Step 14/16 : EXPOSE 80
 ---> Running in 7b07c6eb8a44
 ---> fc067d534590
Removing intermediate container 7b07c6eb8a44
Step 15/16 : ENTRYPOINT nginx
 ---> Running in 96b0eac019bc
 ---> 3198d7526ec4
Removing intermediate container 96b0eac019bc
Step 16/16 : CMD -g daemon off;
 ---> Running in fe0303170b56
 ---> 60f1a3c49c5a
Removing intermediate container fe0303170b56
Successfully built 60f1a3c49c5a
[root@localhost nginx]#
```


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



