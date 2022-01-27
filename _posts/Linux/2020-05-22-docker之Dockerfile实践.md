---
layout: post
categories: Linux
title: docker之Dockerfile实践
meta: docker之Dockerfile实践
---
* content
{:toc}

### 正文

#### Docker的网络配置

在生成自己的镜像前，需要先把docker的网络配置好，不然到时yum等网络操作不可用。

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



#### nginx镜像生成

在一个个人文件夹下新建Dockerfile文件：

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

# 安装必要的软件和添加nginx用户
RUN yum install -y gcc gcc-c++ glibc make openssl-devel
RUN yum install -y libxslt-devel -y gd-devel GeoIP GeoIP-devel pcre pcre-devel
RUN useradd -M -s /sbin/nologin nginx

# 挂载卷，测试用例（这里的挂载卷，不可以指定本机的目录，不够灵活，一般会在 启动容器时通过 -v 参数指定挂载卷，或在docker-compose.yaml文件中指定，都可以指定本地目录）
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

# the command of entrypoint
ENTRYPOINT ["nginx"]

# 执行命令，数组形式， "-g daemon off;" 使我们运行容器时，容器可以前台运行，不会退出
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

curl 127.0.0.1:81 命令，能看到  Welcome to nginx  等英文提示，即说明一切OK。

<br/><br/><br/><br/><br/>
### 参考资料

docker之Dockerfile实践 <https://www.cnblogs.com/jsonhc/p/7767669.html>

docker一键搭建Nginx+PHP环境(含自动部署命令) <https://www.cnblogs.com/lz0925/p/10985700.html>

基于Docker搭建LNPM环境 <https://www.jianshu.com/p/beb8cebaafb8>

Docker的网络配置 <https://blog.csdn.net/hetoto/article/details/99892743>

docker容器无法访问外网 <https://www.wencst.com/archives/626>

Docker创建容器无法访问外网问题 <https://www.jianshu.com/p/49f63dcf3813>


docker桥接网络

<https://blog.csdn.net/qq_41772936/article/details/81192369>

<https://www.cnblogs.com/legenidongma/p/10670535.html>

<https://www.cnblogs.com/weifeng1463/p/7468497.html>


