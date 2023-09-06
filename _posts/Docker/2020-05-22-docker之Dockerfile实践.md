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
<!DOCTYPE html>
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

### Compose使用

在Dockerfile同一文件夹下新建 docker-compose.yml 文件，写入：
```
version: '3'                             # yml 依从的 compose 版本
services:
  nginx2:                                # 服务名为'nginx2'
    image: registry.cn-hangzhou.aliyuncs.com/zhengqing/nginx:1.21.1       # 镜像`nginx:1.21.1`
    container_name: nginx2               # 容器名为'nginx2'
    restart: unless-stopped              # 指定容器退出后的重启策略为始终重启，但是不考虑在Docker守护进程启动时就已经停止了的容器
    volumes:                            # 数据卷挂载路径设置,将本机目录映射到容器目录
      - "./nginx/conf/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/conf/conf.d/default.conf:/etc/nginx/conf.d/default.conf"
      - "./nginx/html:/usr/share/nginx/html"
      - "./nginx/log:/var/log/nginx"
    environment:                        # 设置环境变量,相当于docker run命令中的-e
      TZ: Asia/Shanghai
      LANG: en_US.UTF-8
    ports:                              # 映射端口
      - "80:80"
```

`./nginx/conf/nginx.conf` 等文件及文件夹是Dockerfile同一目录下的文件夹nginx中的内容。

启动：
> docker-compose up

查看 `docker images` 多了一个镜像。

查看 `docker ps` 运行的容器，多了一个 nginx2 的容器。

容器停止运行后，查看镜像和容器，上面生成的两个都在。

再次`docker-compose up`启动时少了好多初始化操作。

### 总结

Dockerfile用于本地生成镜像，像Nginx这类软件，定制化程度很低，不需要自己生成，直接用docker官方库的就可以：
> docker pull nginx

PHP等软件可能牵扯到各类拓展，如 bcmath、gd 等，倒是可以在本地生成镜像，打上版本号，然后推送到docker官方库，
下次直接从官方库`docker pull 镜像`下载。




## 集合

### jianyan74

具体项目 <https://github.com/jianyan74/dockerfiles> ，这是一个收集了很多服务的 Dockerfile 的仓库，
可以看一下这里面相关Dockerfile的使用。里面的涉及到的应用有：Elasticsearch、filebeat、Kafka、Kibana、Logstash、
metricbeat、mongo、Mysql、Nginx、PHP、RabbitMQ、Redis、zabbix 等。
另外这里面的各个应用的说明文档也整理的挺好。

#### Nginx

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

docker-compose.yml 文件 `version: '3.0'` 是指定本 yml 依从的 compose 哪个版本制定的；
services 下的 nginx 是自定义服务名；
container_name 是指定自定义容器名称，而不是生成的默认名称；
`context: .` 是 Dockerfile 上下文路径；
args 中的 NGINX_VER 是 Dockerfile 文件的 `${NGINX_VER}` 变量；
ports 是容器和主机绑定的端口；
volumes 是将主机的数据卷或着文件挂载到容器里；
`restart: always` 容器总是重新启动；
`command: nginx -g 'daemon off;'` 覆盖容器启动的默认命令。

`../../app/php` 下放具体的PHP项目；`../../logs/nginx`下是nginx的日志；

`./nginx.conf` 是nginx的配置文件，可以修改：
```
user www-data;
pid /run/nginx.pid;

# nginx进程数，建议设置为等于CPU总核心数
worker_processes auto;
# worker_cpu_affinity 01 10 01 10;
# 指定进程可以打开的最大描述符：数目
# 工作模式与连接数上限
# 这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。
# 现在在linux 2.6内核下开启文件打开数为65535，worker_rlimit_nofile就相应应该填写65535。
# 这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。
worker_rlimit_nofile 51200;

events {
    worker_connections 51200;
    multi_accept on; 
}

http {
	include /etc/nginx/mime.types;
	default_type application/octet-stream;
	charset UTF-8; 

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    server_tokens off;
    # 长连接超时时间，单位是秒
    keepalive_timeout 60;

    send_timeout 10; 
    server_name_in_redirect off;
    server_names_hash_bucket_size 64;
    types_hash_max_size 2048;
	client_header_timeout 10; 
	client_header_buffer_size 32k;
	large_client_header_buffers 4 32k;
	client_max_body_size 100m;
	client_body_timeout 10; 
	client_body_buffer_size 10m;
	reset_timedout_connection on; 

    # log setting
    log_format main  '{"@timestamp":"$time_local",'
                                  '"http_host":"$http_host",'
                                  '"clinetip":"$remote_addr",'
                                  '"request":"$request",'
                                  '"status":"$status",'
                                  '"size":"$body_bytes_sent",'
                                  '"upstream_addr":"$upstream_addr",'
                                  '"upstream_status":"$upstream_status",'
                                  '"upstream_response_time":"$upstream_response_time",'
                                  '"request_time":"$request_time",'
                                  '"http_referer":"$http_referer",'
                                  '"http_user_agent":"$http_user_agent",'
                                  '"http_x_forwarded_for":"$http_x_forwarded_for"}';

    # access_log /var/log/nginx/access.log main;
    access_log off;
    # 全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
    error_log /var/log/nginx/error.log warn;

    # fast cgi 相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
	fastcgi_buffers 256 16k; 
	fastcgi_buffer_size 128k; 
	fastcgi_connect_timeout 3s; 
	fastcgi_send_timeout 120s; 
	fastcgi_read_timeout 120s; 
	fastcgi_busy_buffers_size 256k; 
	fastcgi_temp_file_write_size 256k; 
	fastcgi_hide_header X-Powered-By;

    # Gzip Compression
    gzip on;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
    gzip_proxied any;
    gzip_min_length 1000;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.0;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_vary on;


	open_file_cache max=10000 inactive=20s;
	open_file_cache_valid 30s;
	open_file_cache_min_uses 2;
	open_file_cache_errors on;

    #负载均衡配置 TODO

    # 虚拟主机的配置
    include /etc/nginx/conf.d/*.conf;
}
```

这是原来默认的：
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 2;
    client_max_body_size 100m;
    types_hash_max_size 2048;
    server_tokens off;
    fastcgi_hide_header X-Powered-By;

    server_names_hash_bucket_size 64;
    server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    log_format main  '$remote_addr - $remote_user [$time_local] "$request" '
             '$status $body_bytes_sent "$http_referer" '
             '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;

    gzip on;
    gzip_disable "MSIE [1-6].(?!.*SV1)";

    gzip_vary on;
    gzip_proxied any;
    gzip_min_length 1000;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    include /etc/nginx/conf.d/*.conf;
}
```

`./conf.d`下放项目的nginx配置，如，demo.conf：
```
server {
    listen        80;
    server_name demo.com;

    ##########################
    # 在生产中需要SSL
    # 监听 443 端口 ssl default_server;

    # ssl on;
    # ssl_session_timeout  5m;
    # ssl_protocols  SSLv2 SSLv3 TLSv1;
    # ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
    # ssl_prefer_server_ciphers   on;

    # cert存储位置
    # ssl_certificate        /etc/nginx/certs/default.cert;
    # ssl_certificate_key    /etc/nginx/certs/default.key;
    ##########################

    root /data/www/rageframe2/web;
    index index.php index.html index.htm;

    charset utf-8;
    client_max_body_size 100M;
    fastcgi_read_timeout 1800;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    location /backend {
        try_files $uri $uri/ /backend/index.php$is_args$args;
    }
    location /api {
        try_files $uri $uri/ /api/index.php$is_args$args;
    }
    location /wechat {
        try_files $uri $uri/ /wechat/index.php$is_args$args;
    }
    location /oauth2 {
            try_files $uri $uri/ /oauth2/index.php$is_args$args;
    }

    # 禁止访问的文件或目录
    location ~* ^/(attachment|assets)/.*\.(php|php5)$
    {
        deny all;
    }

    # 匹配.php后缀的文件
    location ~ \.php$
    {
        try_files $uri =404;

        # fastcgi_pass  unix:/var/run/php/php7.0-fpm.sock;
        # php-fpm:9000 的 php-fpm 指的是容器名直接链接映射，如果是host网络模式请用127.0.0.1
        fastcgi_pass  php-fpm:9000;

        fastcgi_index /index.php;

        include fastcgi_params;
        fastcgi_split_path_info       ^(.+\.php)(/.+)$;
        fastcgi_param PATH_INFO       $fastcgi_path_info;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # 禁止访问的文件或目录
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.project|LICENSE|README.md)
    {
        return 404;
    }

    # 禁止访问以.ht为文件后缀名的文件
    location ~ /\.ht
    {
        deny all;
    }

    # 设置某些类型文件的浏览器缓存时间
    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$
    {
        expires       max;
        log_not_found off;
        access_log    off;

        ##########################
        # 反盗链并设置过期时间
        #
        # valid_referers none blocked *.rageframe.com *.rageframe.net localhost 127.0.0.1;
        # if ($invalid_referer)
        # {
        # 　　rewrite ^/ http://www.rageframe.com/leech.gif; # 显示一张防盗链图片
        # 　　return 412; # 为自定义的http状态码，默认为403
        # 　　break;
        # }
        # break;
        ##########################
    }

    # 日志
    access_log  /var/log/nginx/demo.access.log main;
    error_log  /var/log/nginx/demo.error.log;
}
```

`./conf.d`下另外有一个查看php-fpm运行状态的配置文件，phpfpm_status.conf
```
server {
	listen 80;
	server_name 127.0.0.1;
	allow 127.0.0.1;

	location /server-status {
		stub_status on;
		access_log off;
	}

	location /php-status {
	    fastcgi_pass  php-fpm:9000;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME $fastcgi_script_name;
		access_log off;
	}
}
```

#### PHP

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
  php-fpm:                 # 相关文档 https://hub.docker.com/_/php
    container_name: php-fpm
    build:
      context: .
      args:
        - PHP_VER=7.3-fpm                    # php版本
        - SWOOLE_VER=4.4.5                   # swoole版本
        - COMPOSER_URL=https://mirrors.aliyun.com/composer/            # composer源url
    ports:
      - "9000:9000"
    volumes:
      - ../../app/php:/data/www:rw
      - ../../logs/php:/var/log/php:rw
      - ./php.ini:/usr/local/etc/php/php.ini:ro         # 当前php配置文件；可以拷贝修改php-dev.ini为想要的配置
      - ./php-fpm.conf:/usr/local/etc/php-fpm.conf:ro
    restart: always
    command: php-fpm
```

`../../app/php` 下放具体的PHP项目；`../../logs/php`下是php的日志；

php-fpm.conf 文件内容：
```
[global]
daemonize = no
error_log = /var/log/php/php-fpm.log
log_level = notice

[www]
user = www-data
group = www-data

listen = [::]:9000

pm = dynamic
;pm = static
pm.max_children = 50
pm.start_servers = 15
pm.min_spare_servers = 15
pm.max_spare_servers = 35

pm.status_path = /php-status

rlimit_files = 65535
request_terminate_timeout = 100
request_slowlog_timeout = 30
slowlog = /var/log/php/slow.log
```

php-fpm.conf.default 文件内容：
```
;;;;;;;;;;;;;;;;;;;;;
; FPM Configuration ;
;;;;;;;;;;;;;;;;;;;;;

; All relative paths in this configuration file are relative to PHP's install
; prefix (/usr/local). This prefix can be dynamically changed by using the
; '-p' argument from the command line.

;;;;;;;;;;;;;;;;;;
; Global Options ;
;;;;;;;;;;;;;;;;;;

[global]
; Pid file
; Note: the default prefix is /usr/local/var
; Default Value: none
;pid = run/php-fpm.pid

; Error log file
; If it's set to "syslog", log is sent to syslogd instead of being written
; into a local file.
; Note: the default prefix is /usr/local/var
; Default Value: log/php-fpm.log
;error_log = log/php-fpm.log

; syslog_facility is used to specify what type of program is logging the
; message. This lets syslogd specify that messages from different facilities
; will be handled differently.
; See syslog(3) for possible values (ex daemon equiv LOG_DAEMON)
; Default Value: daemon
;syslog.facility = daemon

; syslog_ident is prepended to every message. If you have multiple FPM
; instances running on the same server, you can change the default value
; which must suit common needs.
; Default Value: php-fpm
;syslog.ident = php-fpm

; Log level
; Possible Values: alert, error, warning, notice, debug
; Default Value: notice
;log_level = notice

; Log limit on number of characters in the single line (log entry). If the
; line is over the limit, it is wrapped on multiple lines. The limit is for
; all logged characters including message prefix and suffix if present. However
; the new line character does not count into it as it is present only when
; logging to a file descriptor. It means the new line character is not present
; when logging to syslog.
; Default Value: 1024
;log_limit = 4096

; Log buffering specifies if the log line is buffered which means that the
; line is written in a single write operation. If the value is false, then the
; data is written directly into the file descriptor. It is an experimental
; option that can potentionaly improve logging performance and memory usage
; for some heavy logging scenarios. This option is ignored if logging to syslog
; as it has to be always buffered.
; Default value: yes
;log_buffering = no

; If this number of child processes exit with SIGSEGV or SIGBUS within the time
; interval set by emergency_restart_interval then FPM will restart. A value
; of '0' means 'Off'.
; Default Value: 0
;emergency_restart_threshold = 0

; Interval of time used by emergency_restart_interval to determine when
; a graceful restart will be initiated.  This can be useful to work around
; accidental corruptions in an accelerator's shared memory.
; Available Units: s(econds), m(inutes), h(ours), or d(ays)
; Default Unit: seconds
; Default Value: 0
;emergency_restart_interval = 0

; Time limit for child processes to wait for a reaction on signals from master.
; Available units: s(econds), m(inutes), h(ours), or d(ays)
; Default Unit: seconds
; Default Value: 0
;process_control_timeout = 0

; The maximum number of processes FPM will fork. This has been designed to control
; the global number of processes when using dynamic PM within a lot of pools.
; Use it with caution.
; Note: A value of 0 indicates no limit
; Default Value: 0
; process.max = 128

; Specify the nice(2) priority to apply to the master process (only if set)
; The value can vary from -19 (highest priority) to 20 (lowest priority)
; Note: - It will only work if the FPM master process is launched as root
;       - The pool process will inherit the master process priority
;         unless specified otherwise
; Default Value: no set
; process.priority = -19

; Send FPM to background. Set to 'no' to keep FPM in foreground for debugging.
; Default Value: yes
;daemonize = yes

; Set open file descriptor rlimit for the master process.
; Default Value: system defined value
;rlimit_files = 1024

; Set max core size rlimit for the master process.
; Possible Values: 'unlimited' or an integer greater or equal to 0
; Default Value: system defined value
;rlimit_core = 0

; Specify the event mechanism FPM will use. The following is available:
; - select     (any POSIX os)
; - poll       (any POSIX os)
; - epoll      (linux >= 2.5.44)
; - kqueue     (FreeBSD >= 4.1, OpenBSD >= 2.9, NetBSD >= 2.0)
; - /dev/poll  (Solaris >= 7)
; - port       (Solaris >= 10)
; Default Value: not set (auto detection)
;events.mechanism = epoll

; When FPM is built with systemd integration, specify the interval,
; in seconds, between health report notification to systemd.
; Set to 0 to disable.
; Available Units: s(econds), m(inutes), h(ours)
; Default Unit: seconds
; Default value: 10
;systemd_interval = 10

;;;;;;;;;;;;;;;;;;;;
; Pool Definitions ;
;;;;;;;;;;;;;;;;;;;;

; Multiple pools of child processes may be started with different listening
; ports and different management options.  The name of the pool will be
; used in logs and stats. There is no limitation on the number of pools which
; FPM can handle. Your system will tell you anyway :)

; Include one or more files. If glob(3) exists, it is used to include a bunch of
; files from a glob(3) pattern. This directive can be used everywhere in the
; file.
; Relative path can also be used. They will be prefixed by:
;  - the global prefix if it's been set (-p argument)
;  - /usr/local otherwise
include=NONE/etc/php-fpm.d/*.conf
```

php.ini 文件内容：
```
[PHP]

;;;;;;;;;;;;;;;;;;;
; About php.ini   ;
;;;;;;;;;;;;;;;;;;;
; PHP's initialization file, generally called php.ini, is responsible for
; configuring many of the aspects of PHP's behavior.

; PHP attempts to find and load this configuration from a number of locations.
; The following is a summary of its search order:
; 1. SAPI module specific location.
; 2. The PHPRC environment variable. (As of PHP 5.2.0)
; 3. A number of predefined registry keys on Windows (As of PHP 5.2.0)
; 4. Current working directory (except CLI)
; 5. The web server's directory (for SAPI modules), or directory of PHP
; (otherwise in Windows)
; 6. The directory from the --with-config-file-path compile time option, or the
; Windows directory (C:\windows or C:\winnt)
; See the PHP docs for more specific information.
; http://php.net/configuration.file

; The syntax of the file is extremely simple.  Whitespace and lines
; beginning with a semicolon are silently ignored (as you probably guessed).
; Section headers (e.g. [Foo]) are also silently ignored, even though
; they might mean something in the future.

; Directives following the section heading [PATH=/www/mysite] only
; apply to PHP files in the /www/mysite directory.  Directives
; following the section heading [HOST=www.example.com] only apply to
; PHP files served from www.example.com.  Directives set in these
; special sections cannot be overridden by user-defined INI files or
; at runtime. Currently, [PATH=] and [HOST=] sections only work under
; CGI/FastCGI.
; http://php.net/ini.sections

; Directives are specified using the following syntax:
; directive = value
; Directive names are *case sensitive* - foo=bar is different from FOO=bar.
; Directives are variables used to configure PHP or PHP extensions.
; There is no name validation.  If PHP can't find an expected
; directive because it is not set or is mistyped, a default value will be used.

; The value can be a string, a number, a PHP constant (e.g. E_ALL or M_PI), one
; of the INI constants (On, Off, True, False, Yes, No and None) or an expression
; (e.g. E_ALL & ~E_NOTICE), a quoted string ("bar"), or a reference to a
; previously set variable or directive (e.g. ${foo})

; Expressions in the INI file are limited to bitwise operators and parentheses:
; |  bitwise OR
; ^  bitwise XOR
; &  bitwise AND
; ~  bitwise NOT
; !  boolean NOT

; Boolean flags can be turned on using the values 1, On, True or Yes.
; They can be turned off using the values 0, Off, False or No.

; An empty string can be denoted by simply not writing anything after the equal
; sign, or by using the None keyword:

;  foo =         ; sets foo to an empty string
;  foo = None    ; sets foo to an empty string
;  foo = "None"  ; sets foo to the string 'None'

; If you use constants in your value, and these constants belong to a
; dynamically loaded extension (either a PHP extension or a Zend extension),
; you may only use these constants *after* the line that loads the extension.

;;;;;;;;;;;;;;;;;;;
; About this file ;
;;;;;;;;;;;;;;;;;;;
; PHP comes packaged with two INI files. One that is recommended to be used
; in production environments and one that is recommended to be used in
; development environments.

; php.ini-production contains settings which hold security, performance and
; best practices at its core. But please be aware, these settings may break
; compatibility with older or less security conscience applications. We
; recommending using the production ini in production and testing environments.

; php.ini-development is very similar to its production variant, except it's
; much more verbose when it comes to errors. We recommending using the
; development version only in development environments as errors shown to
; application users can inadvertently leak otherwise secure information.

; This is php.ini-production INI file.

;;;;;;;;;;;;;;;;;;;
; Quick Reference ;
;;;;;;;;;;;;;;;;;;;
; The following are all the settings which are different in either the production
; or development versions of the INIs with respect to PHP's default behavior.
; Please see the actual settings later in the document for more details as to why
; we recommend these changes in PHP's behavior.

; display_errors
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; display_startup_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: Off

; error_reporting
;   Default Value: E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED
;   Development Value: E_ALL
;   Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT

; html_errors
;   Default Value: On
;   Development Value: On
;   Production value: On

; log_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: On

; max_input_time
;   Default Value: -1 (Unlimited)
;   Development Value: 60 (60 seconds)
;   Production Value: 60 (60 seconds)

; output_buffering
;   Default Value: Off
;   Development Value: 4096
;   Production Value: 4096

; register_argc_argv
;   Default Value: On
;   Development Value: Off
;   Production Value: Off

; request_order
;   Default Value: None
;   Development Value: "GP"
;   Production Value: "GP"

; session.bug_compat_42
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; session.bug_compat_warn
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; session.gc_divisor
;   Default Value: 100
;   Development Value: 1000
;   Production Value: 1000

; session.hash_bits_per_character
;   Default Value: 4
;   Development Value: 5
;   Production Value: 5

; short_open_tag
;   Default Value: On
;   Development Value: Off
;   Production Value: Off

; track_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: Off

; url_rewriter.tags
;   Default Value: "a=href,area=href,frame=src,form=,fieldset="
;   Development Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
;   Production Value: "a=href,area=href,frame=src,input=src,form=fakeentry"

; variables_order
;   Default Value: "EGPCS"
;   Development Value: "GPCS"
;   Production Value: "GPCS"

;;;;;;;;;;;;;;;;;;;;
; php.ini Options  ;
;;;;;;;;;;;;;;;;;;;;
; Name for user-defined php.ini (.htaccess) files. Default is ".user.ini"
;user_ini.filename = ".user.ini"

; To disable this feature set this option to empty value
;user_ini.filename =

; TTL for user-defined php.ini files (time-to-live) in seconds. Default is 300 seconds (5 minutes)
;user_ini.cache_ttl = 300

;;;;;;;;;;;;;;;;;;;;
; Language Options ;
;;;;;;;;;;;;;;;;;;;;

; Enable the PHP scripting language engine under Apache.
; http://php.net/engine
engine = On

; This directive determines whether or not PHP will recognize code between
; <? and ?> tags as PHP source which should be processed as such. It is
; generally recommended that <?php and ?> should be used and that this feature
; should be disabled, as enabling it may result in issues when generating XML
; documents, however this remains supported for backward compatibility reasons.
; Note that this directive does not control the <?= shorthand tag, which can be
; used regardless of this directive.
; Default Value: On
; Development Value: Off
; Production Value: Off
; http://php.net/short-open-tag
short_open_tag = On

; Allow ASP-style <% %> tags.
; http://php.net/asp-tags
asp_tags = Off

; The number of significant digits displayed in floating point numbers.
; http://php.net/precision
precision = 14

; Output buffering is a mechanism for controlling how much output data
; (excluding headers and cookies) PHP should keep internally before pushing that
; data to the client. If your application's output exceeds this setting, PHP
; will send that data in chunks of roughly the size you specify.
; Turning on this setting and managing its maximum buffer size can yield some
; interesting side-effects depending on your application and web server.
; You may be able to send headers and cookies after you've already sent output
; through print or echo. You also may see performance benefits if your server is
; emitting less packets due to buffered output versus PHP streaming the output
; as it gets it. On production servers, 4096 bytes is a good setting for performance
; reasons.
; Note: Output buffering can also be controlled via Output Buffering Control
;   functions.
; Possible Values:
;   On = Enabled and buffer is unlimited. (Use with caution)
;   Off = Disabled
;   Integer = Enables the buffer and sets its maximum size in bytes.
; Note: This directive is hardcoded to Off for the CLI SAPI
; Default Value: Off
; Development Value: 4096
; Production Value: 4096
; http://php.net/output-buffering
output_buffering = 4096

; You can redirect all of the output of your scripts to a function.  For
; example, if you set output_handler to "mb_output_handler", character
; encoding will be transparently converted to the specified encoding.
; Setting any output handler automatically turns on output buffering.
; Note: People who wrote portable scripts should not depend on this ini
;   directive. Instead, explicitly set the output handler using ob_start().
;   Using this ini directive may cause problems unless you know what script
;   is doing.
; Note: You cannot use both "mb_output_handler" with "ob_iconv_handler"
;   and you cannot use both "ob_gzhandler" and "zlib.output_compression".
; Note: output_handler must be empty if this is set 'On' !!!!
;   Instead you must use zlib.output_handler.
; http://php.net/output-handler
;output_handler =

; Transparent output compression using the zlib library
; Valid values for this option are 'off', 'on', or a specific buffer size
; to be used for compression (default is 4KB)
; Note: Resulting chunk size may vary due to nature of compression. PHP
;   outputs chunks that are few hundreds bytes each as a result of
;   compression. If you prefer a larger chunk size for better
;   performance, enable output_buffering in addition.
; Note: You need to use zlib.output_handler instead of the standard
;   output_handler, or otherwise the output will be corrupted.
; http://php.net/zlib.output-compression
zlib.output_compression = Off

; http://php.net/zlib.output-compression-level
;zlib.output_compression_level = -1

; You cannot specify additional output handlers if zlib.output_compression
; is activated here. This setting does the same as output_handler but in
; a different order.
; http://php.net/zlib.output-handler
;zlib.output_handler =

; Implicit flush tells PHP to tell the output layer to flush itself
; automatically after every output block.  This is equivalent to calling the
; PHP function flush() after each and every call to print() or echo() and each
; and every HTML block.  Turning this option on has serious performance
; implications and is generally recommended for debugging purposes only.
; http://php.net/implicit-flush
; Note: This directive is hardcoded to On for the CLI SAPI
implicit_flush = Off

; The unserialize callback function will be called (with the undefined class'
; name as parameter), if the unserializer finds an undefined class
; which should be instantiated. A warning appears if the specified function is
; not defined, or if the function doesn't include/implement the missing class.
; So only set this entry, if you really want to implement such a
; callback-function.
unserialize_callback_func =

; When floats & doubles are serialized store serialize_precision significant
; digits after the floating point. The default value ensures that when floats
; are decoded with unserialize, the data will remain the same.
serialize_precision = 17

; open_basedir, if set, limits all file operations to the defined directory
; and below.  This directive makes most sense if used in a per-directory
; or per-virtualhost web server configuration file. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/open-basedir
;open_basedir =

; This directive allows you to disable certain functions for security reasons.
; It receives a comma-delimited list of function names. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/disable-functions
disable_functions = pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority,

; This directive allows you to disable certain classes for security reasons.
; It receives a comma-delimited list of class names. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/disable-classes
disable_classes =

; Colors for Syntax Highlighting mode.  Anything that's acceptable in
; <span style="color: ???????"> would work.
; http://php.net/syntax-highlighting
;highlight.string  = #DD0000
;highlight.comment = #FF9900
;highlight.keyword = #007700
;highlight.default = #0000BB
;highlight.html    = #000000

; If enabled, the request will be allowed to complete even if the user aborts
; the request. Consider enabling it if executing long requests, which may end up
; being interrupted by the user or a browser timing out. PHP's default behavior
; is to disable this feature.
; http://php.net/ignore-user-abort
;ignore_user_abort = On

; Determines the size of the realpath cache to be used by PHP. This value should
; be increased on systems where PHP opens many files to reflect the quantity of
; the file operations performed.
; http://php.net/realpath-cache-size
;realpath_cache_size = 16k

; Duration of time, in seconds for which to cache realpath information for a given
; file or directory. For systems with rarely changing files, consider increasing this
; value.
; http://php.net/realpath-cache-ttl
;realpath_cache_ttl = 120

; Enables or disables the circular reference collector.
; http://php.net/zend.enable-gc
zend.enable_gc = On

; If enabled, scripts may be written in encodings that are incompatible with
; the scanner.  CP936, Big5, CP949 and Shift_JIS are the examples of such
; encodings.  To use this feature, mbstring extension must be enabled.
; Default: Off
;zend.multibyte = Off

; Allows to set the default encoding for the scripts.  This value will be used
; unless "declare(encoding=...)" directive appears at the top of the script.
; Only affects if zend.multibyte is set.
; Default: ""
;zend.script_encoding =

;;;;;;;;;;;;;;;;;
; Miscellaneous ;
;;;;;;;;;;;;;;;;;

; Decides whether PHP may expose the fact that it is installed on the server
; (e.g. by adding its signature to the Web server header).  It is no security
; threat in any way, but it makes it possible to determine whether you use PHP
; on your server or not.
; http://php.net/expose-php
expose_php = On

;;;;;;;;;;;;;;;;;;;
; Resource Limits ;
;;;;;;;;;;;;;;;;;;;

; Maximum execution time of each script, in seconds
; http://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 30

; Maximum amount of time each script may spend parsing request data. It's a good
; idea to limit this time on productions servers in order to eliminate unexpectedly
; long running scripts.
; Note: This directive is hardcoded to -1 for the CLI SAPI
; Default Value: -1 (Unlimited)
; Development Value: 60 (60 seconds)
; Production Value: 60 (60 seconds)
; http://php.net/max-input-time
max_input_time = 60

; Maximum input variable nesting level
; http://php.net/max-input-nesting-level
;max_input_nesting_level = 64

; How many GET/POST/COOKIE input variables may be accepted
; max_input_vars = 1000

; Maximum amount of memory a script may consume (128MB)
; http://php.net/memory-limit
memory_limit = 128M

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
; Error handling and logging ;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

; This directive informs PHP of which errors, warnings and notices you would like
; it to take action for. The recommended way of setting values for this
; directive is through the use of the error level constants and bitwise
; operators. The error level constants are below here for convenience as well as
; some common settings and their meanings.
; By default, PHP is set to take action on all errors, notices and warnings EXCEPT
; those related to E_NOTICE and E_STRICT, which together cover best practices and
; recommended coding standards in PHP. For performance reasons, this is the
; recommend error reporting setting. Your production server shouldn't be wasting
; resources complaining about best practices and coding standards. That's what
; development servers and development settings are for.
; Note: The php.ini-development file has this setting as E_ALL. This
; means it pretty much reports everything which is exactly what you want during
; development and early testing.
;
; Error Level Constants:
; E_ALL             - All errors and warnings (includes E_STRICT as of PHP 5.4.0)
; E_ERROR           - fatal run-time errors
; E_RECOVERABLE_ERROR  - almost fatal run-time errors
; E_WARNING         - run-time warnings (non-fatal errors)
; E_PARSE           - compile-time parse errors
; E_NOTICE          - run-time notices (these are warnings which often result
;                     from a bug in your code, but it's possible that it was
;                     intentional (e.g., using an uninitialized variable and
;                     relying on the fact it's automatically initialized to an
;                     empty string)
; E_STRICT          - run-time notices, enable to have PHP suggest changes
;                     to your code which will ensure the best interoperability
;                     and forward compatibility of your code
; E_CORE_ERROR      - fatal errors that occur during PHP's initial startup
; E_CORE_WARNING    - warnings (non-fatal errors) that occur during PHP's
;                     initial startup
; E_COMPILE_ERROR   - fatal compile-time errors
; E_COMPILE_WARNING - compile-time warnings (non-fatal errors)
; E_USER_ERROR      - user-generated error message
; E_USER_WARNING    - user-generated warning message
; E_USER_NOTICE     - user-generated notice message
; E_DEPRECATED      - warn about code that will not work in future versions
;                     of PHP
; E_USER_DEPRECATED - user-generated deprecation warnings
;
; Common Values:
;   E_ALL (Show all errors, warnings and notices including coding standards.)
;   E_ALL & ~E_NOTICE  (Show all errors, except for notices)
;   E_ALL & ~E_NOTICE & ~E_STRICT  (Show all errors, except for notices and coding standards warnings.)
;   E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR  (Show only errors)
; Default Value: E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED
; Development Value: E_ALL
; Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT
; http://php.net/error-reporting
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT

; This directive controls whether or not and where PHP will output errors,
; notices and warnings too. Error output is very useful during development, but
; it could be very dangerous in production environments. Depending on the code
; which is triggering the error, sensitive information could potentially leak
; out of your application such as database usernames and passwords or worse.
; It's recommended that errors be logged on production servers rather than
; having the errors sent to STDOUT.
; Possible Values:
;   Off = Do not display any errors
;   stderr = Display errors to STDERR (affects only CGI/CLI binaries!)
;   On or stdout = Display errors to STDOUT
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/display-errors
display_errors = Off

; The display of errors which occur during PHP's startup sequence are handled
; separately from display_errors. PHP's default behavior is to suppress those
; errors from clients. Turning the display of startup errors on can be useful in
; debugging configuration problems. But, it's strongly recommended that you
; leave this setting off on production servers.
; Default Value: Off
; Development Value: On
; Production Value: Off
; http://php.net/display-startup-errors
display_startup_errors = Off

; Besides displaying errors, PHP can also log errors to locations such as a
; server-specific log, STDERR, or a location specified by the error_log
; directive found below. While errors should not be displayed on productions
; servers they should still be monitored and logging is a great way to do that.
; Default Value: Off
; Development Value: On
; Production Value: On
; http://php.net/log-errors
log_errors = On

; Set maximum length of log_errors. In error_log information about the source is
; added. The default is 1024 and 0 allows to not apply any maximum length at all.
; http://php.net/log-errors-max-len
log_errors_max_len = 1024

; Do not log repeated messages. Repeated errors must occur in same file on same
; line unless ignore_repeated_source is set true.
; http://php.net/ignore-repeated-errors
ignore_repeated_errors = Off

; Ignore source of message when ignoring repeated messages. When this setting
; is On you will not log errors with repeated messages from different files or
; source lines.
; http://php.net/ignore-repeated-source
ignore_repeated_source = Off

; If this parameter is set to Off, then memory leaks will not be shown (on
; stdout or in the log). This has only effect in a debug compile, and if
; error reporting includes E_WARNING in the allowed list
; http://php.net/report-memleaks
report_memleaks = On

; This setting is on by default.
;report_zend_debug = 0

; Store the last error/warning message in $php_errormsg (boolean). Setting this value
; to On can assist in debugging and is appropriate for development servers. It should
; however be disabled on production servers.
; Default Value: Off
; Development Value: On
; Production Value: Off
; http://php.net/track-errors
track_errors = Off

; Turn off normal error reporting and emit XML-RPC error XML
; http://php.net/xmlrpc-errors
;xmlrpc_errors = 0

; An XML-RPC faultCode
;xmlrpc_error_number = 0

; When PHP displays or logs an error, it has the capability of formatting the
; error message as HTML for easier reading. This directive controls whether
; the error message is formatted as HTML or not.
; Note: This directive is hardcoded to Off for the CLI SAPI
; Default Value: On
; Development Value: On
; Production value: On
; http://php.net/html-errors
html_errors = On

; If html_errors is set to On *and* docref_root is not empty, then PHP
; produces clickable error messages that direct to a page describing the error
; or function causing the error in detail.
; You can download a copy of the PHP manual from http://php.net/docs
; and change docref_root to the base URL of your local copy including the
; leading '/'. You must also specify the file extension being used including
; the dot. PHP's default behavior is to leave these settings empty, in which
; case no links to documentation are generated.
; Note: Never use this feature for production boxes.
; http://php.net/docref-root
; Examples
;docref_root = "/phpmanual/"

; http://php.net/docref-ext
;docref_ext = .html

; String to output before an error message. PHP's default behavior is to leave
; this setting blank.
; http://php.net/error-prepend-string
; Example:
;error_prepend_string = "<span style='color: #ff0000'>"

; String to output after an error message. PHP's default behavior is to leave
; this setting blank.
; http://php.net/error-append-string
; Example:
;error_append_string = "</span>"

; Log errors to specified file. PHP's default behavior is to leave this value
; empty.
; http://php.net/error-log
; Example:
;error_log = php_errors.log
; Log errors to syslog (Event Log on NT, not valid in Windows 95).
;error_log = syslog

;windows.show_crt_warning
; Default value: 0
; Development value: 0
; Production value: 0

;;;;;;;;;;;;;;;;;
; Data Handling ;
;;;;;;;;;;;;;;;;;

; The separator used in PHP generated URLs to separate arguments.
; PHP's default setting is "&".
; http://php.net/arg-separator.output
; Example:
;arg_separator.output = "&amp;"

; List of separator(s) used by PHP to parse input URLs into variables.
; PHP's default setting is "&".
; NOTE: Every character in this directive is considered as separator!
; http://php.net/arg-separator.input
; Example:
;arg_separator.input = ";&"

; This directive determines which super global arrays are registered when PHP
; starts up. G,P,C,E & S are abbreviations for the following respective super
; globals: GET, POST, COOKIE, ENV and SERVER. There is a performance penalty
; paid for the registration of these arrays and because ENV is not as commonly
; used as the others, ENV is not recommended on productions servers. You
; can still get access to the environment variables through getenv() should you
; need to.
; Default Value: "EGPCS"
; Development Value: "GPCS"
; Production Value: "GPCS";
; http://php.net/variables-order
variables_order = "GPCS"

; This directive determines which super global data (G,P,C,E & S) should
; be registered into the super global array REQUEST. If so, it also determines
; the order in which that data is registered. The values for this directive are
; specified in the same manner as the variables_order directive, EXCEPT one.
; Leaving this value empty will cause PHP to use the value set in the
; variables_order directive. It does not mean it will leave the super globals
; array REQUEST empty.
; Default Value: None
; Development Value: "GP"
; Production Value: "GP"
; http://php.net/request-order
request_order = "GP"

; This directive determines whether PHP registers $argv & $argc each time it
; runs. $argv contains an array of all the arguments passed to PHP when a script
; is invoked. $argc contains an integer representing the number of arguments
; that were passed when the script was invoked. These arrays are extremely
; useful when running scripts from the command line. When this directive is
; enabled, registering these variables consumes CPU cycles and memory each time
; a script is executed. For performance reasons, this feature should be disabled
; on production servers.
; Note: This directive is hardcoded to On for the CLI SAPI
; Default Value: On
; Development Value: Off
; Production Value: Off
; http://php.net/register-argc-argv
register_argc_argv = Off

; When enabled, the ENV, REQUEST and SERVER variables are created when they're
; first used (Just In Time) instead of when the script starts. If these
; variables are not used within a script, having this directive on will result
; in a performance gain. The PHP directive register_argc_argv must be disabled
; for this directive to have any affect.
; http://php.net/auto-globals-jit
auto_globals_jit = On

; Whether PHP will read the POST data.
; This option is enabled by default.
; Most likely, you won't want to disable this option globally. It causes $_POST
; and $_FILES to always be empty; the only way you will be able to read the
; POST data will be through the php://input stream wrapper. This can be useful
; to proxy requests or to process the POST data in a memory efficient fashion.
; http://php.net/enable-post-data-reading
;enable_post_data_reading = Off

; Maximum size of POST data that PHP will accept.
; Its value may be 0 to disable the limit. It is ignored if POST data reading
; is disabled through enable_post_data_reading.
; http://php.net/post-max-size
post_max_size = 50M

; Automatically add files before PHP document.
; http://php.net/auto-prepend-file
auto_prepend_file =

; Automatically add files after PHP document.
; http://php.net/auto-append-file
auto_append_file =

; By default, PHP will output a character encoding using
; the Content-type: header.  To disable sending of the charset, simply
; set it to be empty.
;
; PHP's built-in default is text/html
; http://php.net/default-mimetype
default_mimetype = "text/html"

; PHP's default character set is set to empty.
; http://php.net/default-charset
;default_charset = "UTF-8"

; Always populate the $HTTP_RAW_POST_DATA variable. PHP's default behavior is
; to disable this feature. If post reading is disabled through
; enable_post_data_reading, $HTTP_RAW_POST_DATA is *NOT* populated.
; http://php.net/always-populate-raw-post-data
;always_populate_raw_post_data = On

;;;;;;;;;;;;;;;;;;;;;;;;;
; Paths and Directories ;
;;;;;;;;;;;;;;;;;;;;;;;;;

; UNIX: "/path1:/path2"
;include_path = ".:/usr/share/php"
;
; Windows: "\path1;\path2"
;include_path = ".;c:\php\includes"
;
; PHP's default setting for include_path is ".;/path/to/php/pear"
; http://php.net/include-path

; The root of the PHP pages, used only if nonempty.
; if PHP was not compiled with FORCE_REDIRECT, you SHOULD set doc_root
; if you are running php as a CGI under any web server (other than IIS)
; see documentation for security issues.  The alternate is to use the
; cgi.force_redirect configuration below
; http://php.net/doc-root
doc_root =

; The directory under which PHP opens the script using /~username used only
; if nonempty.
; http://php.net/user-dir
user_dir =

; Directory in which the loadable extensions (modules) reside.
; http://php.net/extension-dir
; extension_dir = "./"
; On windows:
; extension_dir = "ext"

; Directory where the temporary files should be placed.
; Defaults to the system default (see sys_get_temp_dir)
; sys_temp_dir = "/tmp"

; Whether or not to enable the dl() function.  The dl() function does NOT work
; properly in multithreaded servers, such as IIS or Zeus, and is automatically
; disabled on them.
; http://php.net/enable-dl
enable_dl = Off

; cgi.force_redirect is necessary to provide security running PHP as a CGI under
; most web servers.  Left undefined, PHP turns this on by default.  You can
; turn it off here AT YOUR OWN RISK
; **You CAN safely turn this off for IIS, in fact, you MUST.**
; http://php.net/cgi.force-redirect
;cgi.force_redirect = 1

; if cgi.nph is enabled it will force cgi to always sent Status: 200 with
; every request. PHP's default behavior is to disable this feature.
;cgi.nph = 1

; if cgi.force_redirect is turned on, and you are not running under Apache or Netscape
; (iPlanet) web servers, you MAY need to set an environment variable name that PHP
; will look for to know it is OK to continue execution.  Setting this variable MAY
; cause security issues, KNOW WHAT YOU ARE DOING FIRST.
; http://php.net/cgi.redirect-status-env
;cgi.redirect_status_env =

; cgi.fix_pathinfo provides *real* PATH_INFO/PATH_TRANSLATED support for CGI.  PHP's
; previous behaviour was to set PATH_TRANSLATED to SCRIPT_FILENAME, and to not grok
; what PATH_INFO is.  For more information on PATH_INFO, see the cgi specs.  Setting
; this to 1 will cause PHP CGI to fix its paths to conform to the spec.  A setting
; of zero causes PHP to behave as before.  Default is 1.  You should fix your scripts
; to use SCRIPT_FILENAME rather than PATH_TRANSLATED.
; http://php.net/cgi.fix-pathinfo
;cgi.fix_pathinfo=1

; FastCGI under IIS (on WINNT based OS) supports the ability to impersonate
; security tokens of the calling client.  This allows IIS to define the
; security context that the request runs under.  mod_fastcgi under Apache
; does not currently support this feature (03/17/2002)
; Set to 1 if running under IIS.  Default is zero.
; http://php.net/fastcgi.impersonate
;fastcgi.impersonate = 1

; Disable logging through FastCGI connection. PHP's default behavior is to enable
; this feature.
;fastcgi.logging = 0

; cgi.rfc2616_headers configuration option tells PHP what type of headers to
; use when sending HTTP response code. If it's set 0 PHP sends Status: header that
; is supported by Apache. When this option is set to 1 PHP will send
; RFC2616 compliant header.
; Default is zero.
; http://php.net/cgi.rfc2616-headers
;cgi.rfc2616_headers = 0

;;;;;;;;;;;;;;;;
; File Uploads ;
;;;;;;;;;;;;;;;;

; Whether to allow HTTP file uploads.
; http://php.net/file-uploads
file_uploads = On

; Temporary directory for HTTP uploaded files (will use system default if not
; specified).
; http://php.net/upload-tmp-dir
;upload_tmp_dir =

; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
upload_max_filesize = 50M

; Maximum number of files that can be uploaded via a single request
max_file_uploads = 20

;;;;;;;;;;;;;;;;;;
; Fopen wrappers ;
;;;;;;;;;;;;;;;;;;

; Whether to allow the treatment of URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-fopen
allow_url_fopen = On

; Whether to allow include/require to open URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-include
allow_url_include = Off

; Define the anonymous ftp password (your email address). PHP's default setting
; for this is empty.
; http://php.net/from
;from="john@doe.com"

; Define the User-Agent string. PHP's default setting for this is empty.
; http://php.net/user-agent
;user_agent="PHP"

; Default timeout for socket based streams (seconds)
; http://php.net/default-socket-timeout
default_socket_timeout = 60

; If your scripts have to deal with files from Macintosh systems,
; or you are running on a Mac and need to deal with files from
; unix or win32 systems, setting this flag will cause PHP to
; automatically detect the EOL character in those files so that
; fgets() and file() will work regardless of the source of the file.
; http://php.net/auto-detect-line-endings
;auto_detect_line_endings = Off

;;;;;;;;;;;;;;;;;;;;;;
; Dynamic Extensions ;
;;;;;;;;;;;;;;;;;;;;;;

; If you wish to have an extension loaded automatically, use the following
; syntax:
;
;   extension=modulename.extension
;
; For example, on Windows:
;
;   extension=msql.dll
;
; ... or under UNIX:
;
;   extension=msql.so
;
; ... or with a path:
;
;   extension=/path/to/extension/msql.so
;
; If you only provide the name of the extension, PHP will look for it in its
; default extension directory.
;

;;;;;;;;;;;;;;;;;;;
; Module Settings ;
;;;;;;;;;;;;;;;;;;;

[CLI Server]
; Whether the CLI web server uses ANSI color coding in its terminal output.
cli_server.color = On

[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
date.timezone = "Asia/Shanghai"

; http://php.net/date.default-latitude
;date.default_latitude = 31.7667

; http://php.net/date.default-longitude
;date.default_longitude = 35.2333

; http://php.net/date.sunrise-zenith
;date.sunrise_zenith = 90.583333

; http://php.net/date.sunset-zenith
;date.sunset_zenith = 90.583333

[filter]
; http://php.net/filter.default
;filter.default = unsafe_raw

; http://php.net/filter.default-flags
;filter.default_flags =

[iconv]
;iconv.input_encoding = ISO-8859-1
;iconv.internal_encoding = ISO-8859-1
;iconv.output_encoding = ISO-8859-1

[intl]
;intl.default_locale =
; This directive allows you to produce PHP errors when some error
; happens within intl functions. The value is the level of the error produced.
; Default is 0, which does not produce any errors.
;intl.error_level = E_WARNING

[sqlite]
; http://php.net/sqlite.assoc-case
;sqlite.assoc_case = 0

[sqlite3]
;sqlite3.extension_dir =

[Pcre]
;PCRE library backtracking limit.
; http://php.net/pcre.backtrack-limit
;pcre.backtrack_limit=100000

;PCRE library recursion limit.
;Please note that if you set this value to a high number you may consume all
;the available process stack and eventually crash PHP (due to reaching the
;stack size limit imposed by the Operating System).
; http://php.net/pcre.recursion-limit
;pcre.recursion_limit=100000

[Pdo]
; Whether to pool ODBC connections. Can be one of "strict", "relaxed" or "off"
; http://php.net/pdo-odbc.connection-pooling
;pdo_odbc.connection_pooling=strict

;pdo_odbc.db2_instance_name

[Pdo_mysql]
; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/pdo_mysql.cache_size
pdo_mysql.cache_size = 2000

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/pdo_mysql.default-socket
pdo_mysql.default_socket=

[Phar]
; http://php.net/phar.readonly
;phar.readonly = On

; http://php.net/phar.require-hash
;phar.require_hash = On

;phar.cache_list =

[mail function]
; For Win32 only.
; http://php.net/smtp
SMTP = localhost
; http://php.net/smtp-port
smtp_port = 25

; For Win32 only.
; http://php.net/sendmail-from
;sendmail_from = me@example.com

; For Unix only.  You may supply arguments as well (default: "sendmail -t -i").
; http://php.net/sendmail-path
;sendmail_path =

; Force the addition of the specified parameters to be passed as extra parameters
; to the sendmail binary. These parameters will always replace the value of
; the 5th parameter to mail(), even in safe mode.
;mail.force_extra_parameters =

; Add X-PHP-Originating-Script: that will include uid of the script followed by the filename
mail.add_x_header = On

; The path to a log file that will log all mail() calls. Log entries include
; the full path of the script, line number, To address and headers.
;mail.log =
; Log mail to syslog (Event Log on NT, not valid in Windows 95).
;mail.log = syslog

[SQL]
; http://php.net/sql.safe-mode
sql.safe_mode = Off

[ODBC]
; http://php.net/odbc.default-db
;odbc.default_db    =  Not yet implemented

; http://php.net/odbc.default-user
;odbc.default_user  =  Not yet implemented

; http://php.net/odbc.default-pw
;odbc.default_pw    =  Not yet implemented

; Controls the ODBC cursor model.
; Default: SQL_CURSOR_STATIC (default).
;odbc.default_cursortype

; Allow or prevent persistent links.
; http://php.net/odbc.allow-persistent
odbc.allow_persistent = On

; Check that a connection is still valid before reuse.
; http://php.net/odbc.check-persistent
odbc.check_persistent = On

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/odbc.max-persistent
odbc.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/odbc.max-links
odbc.max_links = -1

; Handling of LONG fields.  Returns number of bytes to variables.  0 means
; passthru.
; http://php.net/odbc.defaultlrl
odbc.defaultlrl = 4096

; Handling of binary data.  0 means passthru, 1 return as is, 2 convert to char.
; See the documentation on odbc_binmode and odbc_longreadlen for an explanation
; of odbc.defaultlrl and odbc.defaultbinmode
; http://php.net/odbc.defaultbinmode
odbc.defaultbinmode = 1

;birdstep.max_links = -1

[Interbase]
; Allow or prevent persistent links.
ibase.allow_persistent = 1

; Maximum number of persistent links.  -1 means no limit.
ibase.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
ibase.max_links = -1

; Default database name for ibase_connect().
;ibase.default_db =

; Default username for ibase_connect().
;ibase.default_user =

; Default password for ibase_connect().
;ibase.default_password =

; Default charset for ibase_connect().
;ibase.default_charset =

; Default timestamp format.
ibase.timestampformat = "%Y-%m-%d %H:%M:%S"

; Default date format.
ibase.dateformat = "%Y-%m-%d"

; Default time format.
ibase.timeformat = "%H:%M:%S"

[MySQL]
; Allow accessing, from PHP's perspective, local files with LOAD DATA statements
; http://php.net/mysql.allow_local_infile
mysql.allow_local_infile = On

; Allow or prevent persistent links.
; http://php.net/mysql.allow-persistent
mysql.allow_persistent = On

; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/mysql.cache_size
mysql.cache_size = 2000

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/mysql.max-persistent
mysql.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/mysql.max-links
mysql.max_links = -1

; Default port number for mysql_connect().  If unset, mysql_connect() will use
; the $MYSQL_TCP_PORT or the mysql-tcp entry in /etc/services or the
; compile-time value defined MYSQL_PORT (in that order).  Win32 will only look
; at MYSQL_PORT.
; http://php.net/mysql.default-port
mysql.default_port =

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/mysql.default-socket
mysql.default_socket =

; Default host for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysql.default-host
mysql.default_host =

; Default user for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysql.default-user
mysql.default_user =

; Default password for mysql_connect() (doesn't apply in safe mode).
; Note that this is generally a *bad* idea to store passwords in this file.
; *Any* user with PHP access can run 'echo get_cfg_var("mysql.default_password")
; and reveal this password!  And of course, any users with read access to this
; file will be able to reveal the password as well.
; http://php.net/mysql.default-password
mysql.default_password =

; Maximum time (in seconds) for connect timeout. -1 means no limit
; http://php.net/mysql.connect-timeout
mysql.connect_timeout = 60

; Trace mode. When trace_mode is active (=On), warnings for table/index scans and
; SQL-Errors will be displayed.
; http://php.net/mysql.trace-mode
mysql.trace_mode = Off

[MySQLi]

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/mysqli.max-persistent
mysqli.max_persistent = -1

; Allow accessing, from PHP's perspective, local files with LOAD DATA statements
; http://php.net/mysqli.allow_local_infile
;mysqli.allow_local_infile = On

; Allow or prevent persistent links.
; http://php.net/mysqli.allow-persistent
mysqli.allow_persistent = On

; Maximum number of links.  -1 means no limit.
; http://php.net/mysqli.max-links
mysqli.max_links = -1

; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/mysqli.cache_size
mysqli.cache_size = 2000

; Default port number for mysqli_connect().  If unset, mysqli_connect() will use
; the $MYSQL_TCP_PORT or the mysql-tcp entry in /etc/services or the
; compile-time value defined MYSQL_PORT (in that order).  Win32 will only look
; at MYSQL_PORT.
; http://php.net/mysqli.default-port
mysqli.default_port = 3306

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/mysqli.default-socket
mysqli.default_socket =

; Default host for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysqli.default-host
mysqli.default_host =

; Default user for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysqli.default-user
mysqli.default_user =

; Default password for mysqli_connect() (doesn't apply in safe mode).
; Note that this is generally a *bad* idea to store passwords in this file.
; *Any* user with PHP access can run 'echo get_cfg_var("mysqli.default_pw")
; and reveal this password!  And of course, any users with read access to this
; file will be able to reveal the password as well.
; http://php.net/mysqli.default-pw
mysqli.default_pw =

; Allow or prevent reconnect
mysqli.reconnect = Off

[mysqlnd]
; Enable / Disable collection of general statistics by mysqlnd which can be
; used to tune and monitor MySQL operations.
; http://php.net/mysqlnd.collect_statistics
mysqlnd.collect_statistics = On

; Enable / Disable collection of memory usage statistics by mysqlnd which can be
; used to tune and monitor MySQL operations.
; http://php.net/mysqlnd.collect_memory_statistics
mysqlnd.collect_memory_statistics = Off

; Size of a pre-allocated buffer used when sending commands to MySQL in bytes.
; http://php.net/mysqlnd.net_cmd_buffer_size
;mysqlnd.net_cmd_buffer_size = 2048

; Size of a pre-allocated buffer used for reading data sent by the server in
; bytes.
; http://php.net/mysqlnd.net_read_buffer_size
;mysqlnd.net_read_buffer_size = 32768

[OCI8]

; Connection: Enables privileged connections using external
; credentials (OCI_SYSOPER, OCI_SYSDBA)
; http://php.net/oci8.privileged-connect
;oci8.privileged_connect = Off

; Connection: The maximum number of persistent OCI8 connections per
; process. Using -1 means no limit.
; http://php.net/oci8.max-persistent
;oci8.max_persistent = -1

; Connection: The maximum number of seconds a process is allowed to
; maintain an idle persistent connection. Using -1 means idle
; persistent connections will be maintained forever.
; http://php.net/oci8.persistent-timeout
;oci8.persistent_timeout = -1

; Connection: The number of seconds that must pass before issuing a
; ping during oci_pconnect() to check the connection validity. When
; set to 0, each oci_pconnect() will cause a ping. Using -1 disables
; pings completely.
; http://php.net/oci8.ping-interval
;oci8.ping_interval = 60

; Connection: Set this to a user chosen connection class to be used
; for all pooled server requests with Oracle 11g Database Resident
; Connection Pooling (DRCP).  To use DRCP, this value should be set to
; the same string for all web servers running the same application,
; the database pool must be configured, and the connection string must
; specify to use a pooled server.
;oci8.connection_class =

; High Availability: Using On lets PHP receive Fast Application
; Notification (FAN) events generated when a database node fails. The
; database must also be configured to post FAN events.
;oci8.events = Off

; Tuning: This option enables statement caching, and specifies how
; many statements to cache. Using 0 disables statement caching.
; http://php.net/oci8.statement-cache-size
;oci8.statement_cache_size = 20

; Tuning: Enables statement prefetching and sets the default number of
; rows that will be fetched automatically after statement execution.
; http://php.net/oci8.default-prefetch
;oci8.default_prefetch = 100

; Compatibility. Using On means oci_close() will not close
; oci_connect() and oci_new_connect() connections.
; http://php.net/oci8.old-oci-close-semantics
;oci8.old_oci_close_semantics = Off

[PostgreSQL]
; Allow or prevent persistent links.
; http://php.net/pgsql.allow-persistent
pgsql.allow_persistent = On

; Detect broken persistent links always with pg_pconnect().
; Auto reset feature requires a little overheads.
; http://php.net/pgsql.auto-reset-persistent
pgsql.auto_reset_persistent = Off

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/pgsql.max-persistent
pgsql.max_persistent = -1

; Maximum number of links (persistent+non persistent).  -1 means no limit.
; http://php.net/pgsql.max-links
pgsql.max_links = -1

; Ignore PostgreSQL backends Notice message or not.
; Notice message logging require a little overheads.
; http://php.net/pgsql.ignore-notice
pgsql.ignore_notice = 0

; Log PostgreSQL backends Notice message or not.
; Unless pgsql.ignore_notice=0, module cannot log notice message.
; http://php.net/pgsql.log-notice
pgsql.log_notice = 0

[Sybase-CT]
; Allow or prevent persistent links.
; http://php.net/sybct.allow-persistent
sybct.allow_persistent = On

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/sybct.max-persistent
sybct.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/sybct.max-links
sybct.max_links = -1

; Minimum server message severity to display.
; http://php.net/sybct.min-server-severity
sybct.min_server_severity = 10

; Minimum client message severity to display.
; http://php.net/sybct.min-client-severity
sybct.min_client_severity = 10

; Set per-context timeout
; http://php.net/sybct.timeout
;sybct.timeout=

;sybct.packet_size

; The maximum time in seconds to wait for a connection attempt to succeed before returning failure.
; Default: one minute
;sybct.login_timeout=

; The name of the host you claim to be connecting from, for display by sp_who.
; Default: none
;sybct.hostname=

; Allows you to define how often deadlocks are to be retried. -1 means "forever".
; Default: 0
;sybct.deadlock_retry_count=

[bcmath]
; Number of decimal digits for all bcmath functions.
; http://php.net/bcmath.scale
bcmath.scale = 0

[browscap]
; http://php.net/browscap
;browscap = extra/browscap.ini

[Session]
; Handler used to store/retrieve data.
; http://php.net/session.save-handler
session.save_handler = files

; Argument passed to save_handler.  In the case of files, this is the path
; where data files are stored. Note: Windows users have to change this
; variable in order to use PHP's session functions.
;
; The path can be defined as:
;
;     session.save_path = "N;/path"
;
; where N is an integer.  Instead of storing all the session files in
; /path, what this will do is use subdirectories N-levels deep, and
; store the session data in those directories.  This is useful if you
; or your OS have problems with lots of files in one directory, and is
; a more efficient layout for servers that handle lots of sessions.
;
; NOTE 1: PHP will not create this directory structure automatically.
;         You can use the script in the ext/session dir for that purpose.
; NOTE 2: See the section on garbage collection below if you choose to
;         use subdirectories for session storage
;
; The file storage module creates files using mode 600 by default.
; You can change that by using
;
;     session.save_path = "N;MODE;/path"
;
; where MODE is the octal representation of the mode. Note that this
; does not overwrite the process's umask.
; http://php.net/session.save-path
;session.save_path = "/var/lib/php5"

; Whether to use strict session mode.
; Strict session mode does not accept uninitialized session ID and regenerate
; session ID if browser sends uninitialized session ID. Strict mode protects
; applications from session fixation via session adoption vulnerability. It is
; disabled by default for maximum compatibility, but enabling it is encouraged.
; https://wiki.php.net/rfc/strict_sessions
session.use_strict_mode = 0

; Whether to use cookies.
; http://php.net/session.use-cookies
session.use_cookies = 1

; http://php.net/session.cookie-secure
;session.cookie_secure = 1

; This option forces PHP to fetch and use a cookie for storing and maintaining
; the session id. We encourage this operation as it's very helpful in combating
; session hijacking when not specifying and managing your own session id. It is
; not the end all be all of session hijacking defense, but it's a good start.
; http://php.net/session.use-only-cookies
session.use_only_cookies = 1

; Name of the session (used as cookie name).
; http://php.net/session.name
session.name = PHPSESSID

; Initialize session on request startup.
; http://php.net/session.auto-start
session.auto_start = 0

; Lifetime in seconds of cookie or, if 0, until browser is restarted.
; http://php.net/session.cookie-lifetime
session.cookie_lifetime = 0

; The path for which the cookie is valid.
; http://php.net/session.cookie-path
session.cookie_path = /

; The domain for which the cookie is valid.
; http://php.net/session.cookie-domain
session.cookie_domain =

; Whether or not to add the httpOnly flag to the cookie, which makes it inaccessible to browser scripting languages such as JavaScript.
; http://php.net/session.cookie-httponly
session.cookie_httponly = 1

; Handler used to serialize data.  php is the standard serializer of PHP.
; http://php.net/session.serialize-handler
session.serialize_handler = php

; Defines the probability that the 'garbage collection' process is started
; on every session initialization. The probability is calculated by using
; gc_probability/gc_divisor. Where session.gc_probability is the numerator
; and gc_divisor is the denominator in the equation. Setting this value to 1
; when the session.gc_divisor value is 100 will give you approximately a 1% chance
; the gc will run on any give request.
; Default Value: 1
; Development Value: 1
; Production Value: 1
; http://php.net/session.gc-probability
session.gc_probability = 0

; Defines the probability that the 'garbage collection' process is started on every
; session initialization. The probability is calculated by using the following equation:
; gc_probability/gc_divisor. Where session.gc_probability is the numerator and
; session.gc_divisor is the denominator in the equation. Setting this value to 1
; when the session.gc_divisor value is 100 will give you approximately a 1% chance
; the gc will run on any give request. Increasing this value to 1000 will give you
; a 0.1% chance the gc will run on any give request. For high volume production servers,
; this is a more efficient approach.
; Default Value: 100
; Development Value: 1000
; Production Value: 1000
; http://php.net/session.gc-divisor
session.gc_divisor = 1000

; After this number of seconds, stored data will be seen as 'garbage' and
; cleaned up by the garbage collection process.
; http://php.net/session.gc-maxlifetime
session.gc_maxlifetime = 1440

; NOTE: If you are using the subdirectory option for storing session files
;       (see session.save_path above), then garbage collection does *not*
;       happen automatically.  You will need to do your own garbage
;       collection through a shell script, cron entry, or some other method.
;       For example, the following script would is the equivalent of
;       setting session.gc_maxlifetime to 1440 (1440 seconds = 24 minutes):
;          find /path/to/sessions -cmin +24 -type f | xargs rm

; PHP 4.2 and less have an undocumented feature/bug that allows you to
; to initialize a session variable in the global scope.
; PHP 4.3 and later will warn you, if this feature is used.
; You can disable the feature and the warning separately. At this time,
; the warning is only displayed, if bug_compat_42 is enabled. This feature
; introduces some serious security problems if not handled correctly. It's
; recommended that you do not use this feature on production servers. But you
; should enable this on development servers and enable the warning as well. If you
; do not enable the feature on development servers, you won't be warned when it's
; used and debugging errors caused by this can be difficult to track down.
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/session.bug-compat-42
session.bug_compat_42 = Off

; This setting controls whether or not you are warned by PHP when initializing a
; session value into the global space. session.bug_compat_42 must be enabled before
; these warnings can be issued by PHP. See the directive above for more information.
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/session.bug-compat-warn
session.bug_compat_warn = Off

; Check HTTP Referer to invalidate externally stored URLs containing ids.
; HTTP_REFERER has to contain this substring for the session to be
; considered as valid.
; http://php.net/session.referer-check
session.referer_check =

; How many bytes to read from the file.
; http://php.net/session.entropy-length
;session.entropy_length = 32

; Specified here to create the session id.
; http://php.net/session.entropy-file
; Defaults to /dev/urandom
; On systems that don't have /dev/urandom but do have /dev/arandom, this will default to /dev/arandom
; If neither are found at compile time, the default is no entropy file.
; On windows, setting the entropy_length setting will activate the
; Windows random source (using the CryptoAPI)
;session.entropy_file = /dev/urandom

; Set to {nocache,private,public,} to determine HTTP caching aspects
; or leave this empty to avoid sending anti-caching headers.
; http://php.net/session.cache-limiter
session.cache_limiter = nocache

; Document expires after n minutes.
; http://php.net/session.cache-expire
session.cache_expire = 180

; trans sid support is disabled by default.
; Use of trans sid may risk your users security.
; Use this option with caution.
; - User may send URL contains active session ID
;   to other person via. email/irc/etc.
; - URL that contains active session ID may be stored
;   in publicly accessible computer.
; - User may access your site with the same session ID
;   always using URL stored in browser's history or bookmarks.
; http://php.net/session.use-trans-sid
session.use_trans_sid = 0

; Select a hash function for use in generating session ids.
; Possible Values
;   0  (MD5 128 bits)
;   1  (SHA-1 160 bits)
; This option may also be set to the name of any hash function supported by
; the hash extension. A list of available hashes is returned by the hash_algos()
; function.
; http://php.net/session.hash-function
session.hash_function = 0

; Define how many bits are stored in each character when converting
; the binary hash data to something readable.
; Possible values:
;   4  (4 bits: 0-9, a-f)
;   5  (5 bits: 0-9, a-v)
;   6  (6 bits: 0-9, a-z, A-Z, "-", ",")
; Default Value: 4
; Development Value: 5
; Production Value: 5
; http://php.net/session.hash-bits-per-character
session.hash_bits_per_character = 5

; The URL rewriter will look for URLs in a defined set of HTML tags.
; form/fieldset are special; if you include them here, the rewriter will
; add a hidden <input> field with the info which is otherwise appended
; to URLs.  If you want XHTML conformity, remove the form entry.
; Note that all valid entries require a "=", even if no value follows.
; Default Value: "a=href,area=href,frame=src,form=,fieldset="
; Development Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
; Production Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
; http://php.net/url-rewriter.tags
url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=fakeentry"

; Enable upload progress tracking in $_SESSION
; Default Value: On
; Development Value: On
; Production Value: On
; http://php.net/session.upload-progress.enabled
;session.upload_progress.enabled = On

; Cleanup the progress information as soon as all POST data has been read
; (i.e. upload completed).
; Default Value: On
; Development Value: On
; Production Value: On
; http://php.net/session.upload-progress.cleanup
;session.upload_progress.cleanup = On

; A prefix used for the upload progress key in $_SESSION
; Default Value: "upload_progress_"
; Development Value: "upload_progress_"
; Production Value: "upload_progress_"
; http://php.net/session.upload-progress.prefix
;session.upload_progress.prefix = "upload_progress_"

; The index name (concatenated with the prefix) in $_SESSION
; containing the upload progress information
; Default Value: "PHP_SESSION_UPLOAD_PROGRESS"
; Development Value: "PHP_SESSION_UPLOAD_PROGRESS"
; Production Value: "PHP_SESSION_UPLOAD_PROGRESS"
; http://php.net/session.upload-progress.name
;session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"

; How frequently the upload progress should be updated.
; Given either in percentages (per-file), or in bytes
; Default Value: "1%"
; Development Value: "1%"
; Production Value: "1%"
; http://php.net/session.upload-progress.freq
;session.upload_progress.freq =  "1%"

; The minimum delay between updates, in seconds
; Default Value: 1
; Development Value: 1
; Production Value: 1
; http://php.net/session.upload-progress.min-freq
;session.upload_progress.min_freq = "1"

[MSSQL]
; Allow or prevent persistent links.
mssql.allow_persistent = On

; Maximum number of persistent links.  -1 means no limit.
mssql.max_persistent = -1

; Maximum number of links (persistent+non persistent).  -1 means no limit.
mssql.max_links = -1

; Minimum error severity to display.
mssql.min_error_severity = 10

; Minimum message severity to display.
mssql.min_message_severity = 10

; Compatibility mode with old versions of PHP 3.0.
mssql.compatibility_mode = Off

; Connect timeout
;mssql.connect_timeout = 5

; Query timeout
;mssql.timeout = 60

; Valid range 0 - 2147483647.  Default = 4096.
;mssql.textlimit = 4096

; Valid range 0 - 2147483647.  Default = 4096.
;mssql.textsize = 4096

; Limits the number of records in each batch.  0 = all records in one batch.
;mssql.batchsize = 0

; Specify how datetime and datetim4 columns are returned
; On => Returns data converted to SQL server settings
; Off => Returns values as YYYY-MM-DD hh:mm:ss
;mssql.datetimeconvert = On

; Use NT authentication when connecting to the server
mssql.secure_connection = Off

; Specify max number of processes. -1 = library default
; msdlib defaults to 25
; FreeTDS defaults to 4096
;mssql.max_procs = -1

; Specify client character set.
; If empty or not set the client charset from freetds.conf is used
; This is only used when compiled with FreeTDS
;mssql.charset = "ISO-8859-1"

[Assertion]
; Assert(expr); active by default.
; http://php.net/assert.active
;assert.active = On

; Issue a PHP warning for each failed assertion.
; http://php.net/assert.warning
;assert.warning = On

; Don't bail out by default.
; http://php.net/assert.bail
;assert.bail = Off

; User-function to be called if an assertion fails.
; http://php.net/assert.callback
;assert.callback = 0

; Eval the expression with current error_reporting().  Set to true if you want
; error_reporting(0) around the eval().
; http://php.net/assert.quiet-eval
;assert.quiet_eval = 0

[COM]
; path to a file containing GUIDs, IIDs or filenames of files with TypeLibs
; http://php.net/com.typelib-file
;com.typelib_file =

; allow Distributed-COM calls
; http://php.net/com.allow-dcom
;com.allow_dcom = true

; autoregister constants of a components typlib on com_load()
; http://php.net/com.autoregister-typelib
;com.autoregister_typelib = true

; register constants casesensitive
; http://php.net/com.autoregister-casesensitive
;com.autoregister_casesensitive = false

; show warnings on duplicate constant registrations
; http://php.net/com.autoregister-verbose
;com.autoregister_verbose = true

; The default character set code-page to use when passing strings to and from COM objects.
; Default: system ANSI code page
;com.code_page=

[mbstring]
; language for internal character representation.
; http://php.net/mbstring.language
;mbstring.language = Japanese

; internal/script encoding.
; Some encoding cannot work as internal encoding.
; (e.g. SJIS, BIG5, ISO-2022-*)
; http://php.net/mbstring.internal-encoding
;mbstring.internal_encoding = UTF-8

; http input encoding.
; http://php.net/mbstring.http-input
;mbstring.http_input = UTF-8

; http output encoding. mb_output_handler must be
; registered as output buffer to function
; http://php.net/mbstring.http-output
;mbstring.http_output = pass

; enable automatic encoding translation according to
; mbstring.internal_encoding setting. Input chars are
; converted to internal encoding by setting this to On.
; Note: Do _not_ use automatic encoding translation for
;       portable libs/applications.
; http://php.net/mbstring.encoding-translation
;mbstring.encoding_translation = Off

; automatic encoding detection order.
; auto means
; http://php.net/mbstring.detect-order
;mbstring.detect_order = auto

; substitute_character used when character cannot be converted
; one from another
; http://php.net/mbstring.substitute-character
;mbstring.substitute_character = none

; overload(replace) single byte functions by mbstring functions.
; mail(), ereg(), etc are overloaded by mb_send_mail(), mb_ereg(),
; etc. Possible values are 0,1,2,4 or combination of them.
; For example, 7 for overload everything.
; 0: No overload
; 1: Overload mail() function
; 2: Overload str*() functions
; 4: Overload ereg*() functions
; http://php.net/mbstring.func-overload
;mbstring.func_overload = 0

; enable strict encoding detection.
;mbstring.strict_detection = On

; This directive specifies the regex pattern of content types for which mb_output_handler()
; is activated.
; Default: mbstring.http_output_conv_mimetype=^(text/|application/xhtml\+xml)
;mbstring.http_output_conv_mimetype=

[gd]
; Tell the jpeg decode to ignore warnings and try to create
; a gd image. The warning will then be displayed as notices
; disabled by default
; http://php.net/gd.jpeg-ignore-warning
;gd.jpeg_ignore_warning = 0

[exif]
; Exif UNICODE user comments are handled as UCS-2BE/UCS-2LE and JIS as JIS.
; With mbstring support this will automatically be converted into the encoding
; given by corresponding encode setting. When empty mbstring.internal_encoding
; is used. For the decode settings you can distinguish between motorola and
; intel byte order. A decode setting cannot be empty.
; http://php.net/exif.encode-unicode
;exif.encode_unicode = ISO-8859-15

; http://php.net/exif.decode-unicode-motorola
;exif.decode_unicode_motorola = UCS-2BE

; http://php.net/exif.decode-unicode-intel
;exif.decode_unicode_intel    = UCS-2LE

; http://php.net/exif.encode-jis
;exif.encode_jis =

; http://php.net/exif.decode-jis-motorola
;exif.decode_jis_motorola = JIS

; http://php.net/exif.decode-jis-intel
;exif.decode_jis_intel    = JIS

[Tidy]
; The path to a default tidy configuration file to use when using tidy
; http://php.net/tidy.default-config
;tidy.default_config = /usr/local/lib/php/default.tcfg

; Should tidy clean and repair output automatically?
; WARNING: Do not use this option if you are generating non-html content
; such as dynamic images
; http://php.net/tidy.clean-output
tidy.clean_output = Off

[soap]
; Enables or disables WSDL caching feature.
; http://php.net/soap.wsdl-cache-enabled
soap.wsdl_cache_enabled=1

; Sets the directory name where SOAP extension will put cache files.
; http://php.net/soap.wsdl-cache-dir
soap.wsdl_cache_dir="/tmp"

; (time to live) Sets the number of second while cached file will be used
; instead of original one.
; http://php.net/soap.wsdl-cache-ttl
soap.wsdl_cache_ttl=86400

; Sets the size of the cache limit. (Max. number of WSDL files to cache)
soap.wsdl_cache_limit = 5

[sysvshm]
; A default size of the shared memory segment
;sysvshm.init_mem = 10000

[ldap]
; Sets the maximum number of open links or -1 for unlimited.
ldap.max_links = -1

[mcrypt]
; For more information about mcrypt settings see http://php.net/mcrypt-module-open

; Directory where to load mcrypt algorithms
; Default: Compiled in into libmcrypt (usually /usr/local/lib/libmcrypt)
;mcrypt.algorithms_dir=

; Directory where to load mcrypt modes
; Default: Compiled in into libmcrypt (usually /usr/local/lib/libmcrypt)
;mcrypt.modes_dir=

[dba]
;dba.default_handler=

[opcache]
; Determines if Zend OPCache is enabled
opcache.enable=1

; Determines if Zend OPCache is enabled for the CLI version of PHP
opcache.enable_cli=1

; The OPcache shared memory storage size.
opcache.memory_consumption=512

; The amount of memory for interned strings in Mbytes.
opcache.interned_strings_buffer=8

; The maximum number of keys (scripts) in the OPcache hash table.
; Only numbers between 200 and 100000 are allowed.
opcache.max_accelerated_files=10000

; The maximum percentage of "wasted" memory until a restart is scheduled.
;opcache.max_wasted_percentage=5

; When this directive is enabled, the OPcache appends the current working
; directory to the script key, thus eliminating possible collisions between
; files with the same name (basename). Disabling the directive improves
; performance, but may break existing applications.
;opcache.use_cwd=1

; When disabled, you must reset the OPcache manually or restart the
; webserver for changes to the filesystem to take effect.
;opcache.validate_timestamps=1

; How often (in seconds) to check file timestamps for changes to the shared
; memory storage allocation. ("1" means validate once per second, but only
; once per request. "0" means always validate)
opcache.revalidate_freq=1

; Enables or disables file search in include_path optimization
;opcache.revalidate_path=0

; If disabled, all PHPDoc comments are dropped from the code to reduce the
; size of the optimized code.
;opcache.save_comments=1

; If disabled, PHPDoc comments are not loaded from SHM, so "Doc Comments"
; may be always stored (save_comments=1), but not loaded by applications
; that don't need them anyway.
;opcache.load_comments=1

; If enabled, a fast shutdown sequence is used for the accelerated code
opcache.fast_shutdown=1

; Allow file existence override (file_exists, etc.) performance feature.
;opcache.enable_file_override=0

; A bitmask, where each bit enables or disables the appropriate OPcache
; passes
;opcache.optimization_level=0xffffffff

;opcache.inherited_hack=1
;opcache.dups_fix=0

; The location of the OPcache blacklist file (wildcards allowed).
; Each OPcache blacklist file is a text file that holds the names of files
; that should not be accelerated. The file format is to add each filename
; to a new line. The filename may be a full path or just a file prefix
; (i.e., /var/www/x  blacklists all the files and directories in /var/www
; that start with 'x'). Line starting with a ; are ignored (comments).
;opcache.blacklist_filename=

; Allows exclusion of large files from being cached. By default all files
; are cached.
;opcache.max_file_size=0

; Check the cache checksum each N requests.
; The default value of "0" means that the checks are disabled.
;opcache.consistency_checks=0

; How long to wait (in seconds) for a scheduled restart to begin if the cache
; is not being accessed.
;opcache.force_restart_timeout=180

; OPcache error_log file name. Empty string assumes "stderr".
;opcache.error_log=

; All OPcache errors go to the Web server log.
; By default, only fatal errors (level 0) or errors (level 1) are logged.
; You can also enable warnings (level 2), info messages (level 3) or
; debug messages (level 4).
;opcache.log_verbosity_level=1

; Preferred Shared Memory back-end. Leave empty and let the system decide.
;opcache.preferred_memory_model=

; Protect the shared memory from unexpected writing during script execution.
; Useful for internal debugging only.
;opcache.protect_memory=0

[curl]
; A default value for the CURLOPT_CAINFO option. This is required to be an
; absolute path.
;curl.cainfo =

; Local Variables:
; tab-width: 4
; End:
```

php.ini.default 文件内容：
```
[PHP]

;;;;;;;;;;;;;;;;;;;
; About php.ini   ;
;;;;;;;;;;;;;;;;;;;
; PHP's initialization file, generally called php.ini, is responsible for
; configuring many of the aspects of PHP's behavior.

; PHP attempts to find and load this configuration from a number of locations.
; The following is a summary of its search order:
; 1. SAPI module specific location.
; 2. The PHPRC environment variable. (As of PHP 5.2.0)
; 3. A number of predefined registry keys on Windows (As of PHP 5.2.0)
; 4. Current working directory (except CLI)
; 5. The web server's directory (for SAPI modules), or directory of PHP
; (otherwise in Windows)
; 6. The directory from the --with-config-file-path compile time option, or the
; Windows directory (C:\windows or C:\winnt)
; See the PHP docs for more specific information.
; http://php.net/configuration.file

; The syntax of the file is extremely simple.  Whitespace and lines
; beginning with a semicolon are silently ignored (as you probably guessed).
; Section headers (e.g. [Foo]) are also silently ignored, even though
; they might mean something in the future.

; Directives following the section heading [PATH=/www/mysite] only
; apply to PHP files in the /www/mysite directory.  Directives
; following the section heading [HOST=www.example.com] only apply to
; PHP files served from www.example.com.  Directives set in these
; special sections cannot be overridden by user-defined INI files or
; at runtime. Currently, [PATH=] and [HOST=] sections only work under
; CGI/FastCGI.
; http://php.net/ini.sections

; Directives are specified using the following syntax:
; directive = value
; Directive names are *case sensitive* - foo=bar is different from FOO=bar.
; Directives are variables used to configure PHP or PHP extensions.
; There is no name validation.  If PHP can't find an expected
; directive because it is not set or is mistyped, a default value will be used.

; The value can be a string, a number, a PHP constant (e.g. E_ALL or M_PI), one
; of the INI constants (On, Off, True, False, Yes, No and None) or an expression
; (e.g. E_ALL & ~E_NOTICE), a quoted string ("bar"), or a reference to a
; previously set variable or directive (e.g. ${foo})

; Expressions in the INI file are limited to bitwise operators and parentheses:
; |  bitwise OR
; ^  bitwise XOR
; &  bitwise AND
; ~  bitwise NOT
; !  boolean NOT

; Boolean flags can be turned on using the values 1, On, True or Yes.
; They can be turned off using the values 0, Off, False or No.

; An empty string can be denoted by simply not writing anything after the equal
; sign, or by using the None keyword:

;  foo =         ; sets foo to an empty string
;  foo = None    ; sets foo to an empty string
;  foo = "None"  ; sets foo to the string 'None'

; If you use constants in your value, and these constants belong to a
; dynamically loaded extension (either a PHP extension or a Zend extension),
; you may only use these constants *after* the line that loads the extension.

;;;;;;;;;;;;;;;;;;;
; About this file ;
;;;;;;;;;;;;;;;;;;;
; PHP comes packaged with two INI files. One that is recommended to be used
; in production environments and one that is recommended to be used in
; development environments.

; php.ini-production contains settings which hold security, performance and
; best practices at its core. But please be aware, these settings may break
; compatibility with older or less security conscience applications. We
; recommending using the production ini in production and testing environments.

; php.ini-development is very similar to its production variant, except it's
; much more verbose when it comes to errors. We recommending using the
; development version only in development environments as errors shown to
; application users can inadvertently leak otherwise secure information.

; This is php.ini-production INI file.

;;;;;;;;;;;;;;;;;;;
; Quick Reference ;
;;;;;;;;;;;;;;;;;;;
; The following are all the settings which are different in either the production
; or development versions of the INIs with respect to PHP's default behavior.
; Please see the actual settings later in the document for more details as to why
; we recommend these changes in PHP's behavior.

; display_errors
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; display_startup_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: Off

; error_reporting
;   Default Value: E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED
;   Development Value: E_ALL
;   Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT

; html_errors
;   Default Value: On
;   Development Value: On
;   Production value: On

; log_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: On

; max_input_time
;   Default Value: -1 (Unlimited)
;   Development Value: 60 (60 seconds)
;   Production Value: 60 (60 seconds)

; output_buffering
;   Default Value: Off
;   Development Value: 4096
;   Production Value: 4096

; register_argc_argv
;   Default Value: On
;   Development Value: Off
;   Production Value: Off

; request_order
;   Default Value: None
;   Development Value: "GP"
;   Production Value: "GP"

; session.bug_compat_42
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; session.bug_compat_warn
;   Default Value: On
;   Development Value: On
;   Production Value: Off

; session.gc_divisor
;   Default Value: 100
;   Development Value: 1000
;   Production Value: 1000

; session.hash_bits_per_character
;   Default Value: 4
;   Development Value: 5
;   Production Value: 5

; short_open_tag
;   Default Value: On
;   Development Value: Off
;   Production Value: Off

; track_errors
;   Default Value: Off
;   Development Value: On
;   Production Value: Off

; url_rewriter.tags
;   Default Value: "a=href,area=href,frame=src,form=,fieldset="
;   Development Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
;   Production Value: "a=href,area=href,frame=src,input=src,form=fakeentry"

; variables_order
;   Default Value: "EGPCS"
;   Development Value: "GPCS"
;   Production Value: "GPCS"

;;;;;;;;;;;;;;;;;;;;
; php.ini Options  ;
;;;;;;;;;;;;;;;;;;;;
; Name for user-defined php.ini (.htaccess) files. Default is ".user.ini"
;user_ini.filename = ".user.ini"

; To disable this feature set this option to empty value
;user_ini.filename =

; TTL for user-defined php.ini files (time-to-live) in seconds. Default is 300 seconds (5 minutes)
;user_ini.cache_ttl = 300

;;;;;;;;;;;;;;;;;;;;
; Language Options ;
;;;;;;;;;;;;;;;;;;;;

; Enable the PHP scripting language engine under Apache.
; http://php.net/engine
engine = On

; This directive determines whether or not PHP will recognize code between
; <? and ?> tags as PHP source which should be processed as such. It is
; generally recommended that <?php and ?> should be used and that this feature
; should be disabled, as enabling it may result in issues when generating XML
; documents, however this remains supported for backward compatibility reasons.
; Note that this directive does not control the <?= shorthand tag, which can be
; used regardless of this directive.
; Default Value: On
; Development Value: Off
; Production Value: Off
; http://php.net/short-open-tag
short_open_tag = On

; Allow ASP-style <% %> tags.
; http://php.net/asp-tags
asp_tags = Off

; The number of significant digits displayed in floating point numbers.
; http://php.net/precision
precision = 14

; Output buffering is a mechanism for controlling how much output data
; (excluding headers and cookies) PHP should keep internally before pushing that
; data to the client. If your application's output exceeds this setting, PHP
; will send that data in chunks of roughly the size you specify.
; Turning on this setting and managing its maximum buffer size can yield some
; interesting side-effects depending on your application and web server.
; You may be able to send headers and cookies after you've already sent output
; through print or echo. You also may see performance benefits if your server is
; emitting less packets due to buffered output versus PHP streaming the output
; as it gets it. On production servers, 4096 bytes is a good setting for performance
; reasons.
; Note: Output buffering can also be controlled via Output Buffering Control
;   functions.
; Possible Values:
;   On = Enabled and buffer is unlimited. (Use with caution)
;   Off = Disabled
;   Integer = Enables the buffer and sets its maximum size in bytes.
; Note: This directive is hardcoded to Off for the CLI SAPI
; Default Value: Off
; Development Value: 4096
; Production Value: 4096
; http://php.net/output-buffering
output_buffering = 4096

; You can redirect all of the output of your scripts to a function.  For
; example, if you set output_handler to "mb_output_handler", character
; encoding will be transparently converted to the specified encoding.
; Setting any output handler automatically turns on output buffering.
; Note: People who wrote portable scripts should not depend on this ini
;   directive. Instead, explicitly set the output handler using ob_start().
;   Using this ini directive may cause problems unless you know what script
;   is doing.
; Note: You cannot use both "mb_output_handler" with "ob_iconv_handler"
;   and you cannot use both "ob_gzhandler" and "zlib.output_compression".
; Note: output_handler must be empty if this is set 'On' !!!!
;   Instead you must use zlib.output_handler.
; http://php.net/output-handler
;output_handler =

; Transparent output compression using the zlib library
; Valid values for this option are 'off', 'on', or a specific buffer size
; to be used for compression (default is 4KB)
; Note: Resulting chunk size may vary due to nature of compression. PHP
;   outputs chunks that are few hundreds bytes each as a result of
;   compression. If you prefer a larger chunk size for better
;   performance, enable output_buffering in addition.
; Note: You need to use zlib.output_handler instead of the standard
;   output_handler, or otherwise the output will be corrupted.
; http://php.net/zlib.output-compression
zlib.output_compression = Off

; http://php.net/zlib.output-compression-level
;zlib.output_compression_level = -1

; You cannot specify additional output handlers if zlib.output_compression
; is activated here. This setting does the same as output_handler but in
; a different order.
; http://php.net/zlib.output-handler
;zlib.output_handler =

; Implicit flush tells PHP to tell the output layer to flush itself
; automatically after every output block.  This is equivalent to calling the
; PHP function flush() after each and every call to print() or echo() and each
; and every HTML block.  Turning this option on has serious performance
; implications and is generally recommended for debugging purposes only.
; http://php.net/implicit-flush
; Note: This directive is hardcoded to On for the CLI SAPI
implicit_flush = Off

; The unserialize callback function will be called (with the undefined class'
; name as parameter), if the unserializer finds an undefined class
; which should be instantiated. A warning appears if the specified function is
; not defined, or if the function doesn't include/implement the missing class.
; So only set this entry, if you really want to implement such a
; callback-function.
unserialize_callback_func =

; When floats & doubles are serialized store serialize_precision significant
; digits after the floating point. The default value ensures that when floats
; are decoded with unserialize, the data will remain the same.
serialize_precision = 17

; open_basedir, if set, limits all file operations to the defined directory
; and below.  This directive makes most sense if used in a per-directory
; or per-virtualhost web server configuration file. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/open-basedir
;open_basedir =

; This directive allows you to disable certain functions for security reasons.
; It receives a comma-delimited list of function names. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/disable-functions
disable_functions = pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority,

; This directive allows you to disable certain classes for security reasons.
; It receives a comma-delimited list of class names. This directive is
; *NOT* affected by whether Safe Mode is turned On or Off.
; http://php.net/disable-classes
disable_classes =

; Colors for Syntax Highlighting mode.  Anything that's acceptable in
; <span style="color: ???????"> would work.
; http://php.net/syntax-highlighting
;highlight.string  = #DD0000
;highlight.comment = #FF9900
;highlight.keyword = #007700
;highlight.default = #0000BB
;highlight.html    = #000000

; If enabled, the request will be allowed to complete even if the user aborts
; the request. Consider enabling it if executing long requests, which may end up
; being interrupted by the user or a browser timing out. PHP's default behavior
; is to disable this feature.
; http://php.net/ignore-user-abort
;ignore_user_abort = On

; Determines the size of the realpath cache to be used by PHP. This value should
; be increased on systems where PHP opens many files to reflect the quantity of
; the file operations performed.
; http://php.net/realpath-cache-size
;realpath_cache_size = 16k

; Duration of time, in seconds for which to cache realpath information for a given
; file or directory. For systems with rarely changing files, consider increasing this
; value.
; http://php.net/realpath-cache-ttl
;realpath_cache_ttl = 120

; Enables or disables the circular reference collector.
; http://php.net/zend.enable-gc
zend.enable_gc = On

; If enabled, scripts may be written in encodings that are incompatible with
; the scanner.  CP936, Big5, CP949 and Shift_JIS are the examples of such
; encodings.  To use this feature, mbstring extension must be enabled.
; Default: Off
;zend.multibyte = Off

; Allows to set the default encoding for the scripts.  This value will be used
; unless "declare(encoding=...)" directive appears at the top of the script.
; Only affects if zend.multibyte is set.
; Default: ""
;zend.script_encoding =

;;;;;;;;;;;;;;;;;
; Miscellaneous ;
;;;;;;;;;;;;;;;;;

; Decides whether PHP may expose the fact that it is installed on the server
; (e.g. by adding its signature to the Web server header).  It is no security
; threat in any way, but it makes it possible to determine whether you use PHP
; on your server or not.
; http://php.net/expose-php
expose_php = On

;;;;;;;;;;;;;;;;;;;
; Resource Limits ;
;;;;;;;;;;;;;;;;;;;

; Maximum execution time of each script, in seconds
; http://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 30

; Maximum amount of time each script may spend parsing request data. It's a good
; idea to limit this time on productions servers in order to eliminate unexpectedly
; long running scripts.
; Note: This directive is hardcoded to -1 for the CLI SAPI
; Default Value: -1 (Unlimited)
; Development Value: 60 (60 seconds)
; Production Value: 60 (60 seconds)
; http://php.net/max-input-time
max_input_time = 60

; Maximum input variable nesting level
; http://php.net/max-input-nesting-level
;max_input_nesting_level = 64

; How many GET/POST/COOKIE input variables may be accepted
; max_input_vars = 1000

; Maximum amount of memory a script may consume (128MB)
; http://php.net/memory-limit
memory_limit = 128M

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
; Error handling and logging ;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

; This directive informs PHP of which errors, warnings and notices you would like
; it to take action for. The recommended way of setting values for this
; directive is through the use of the error level constants and bitwise
; operators. The error level constants are below here for convenience as well as
; some common settings and their meanings.
; By default, PHP is set to take action on all errors, notices and warnings EXCEPT
; those related to E_NOTICE and E_STRICT, which together cover best practices and
; recommended coding standards in PHP. For performance reasons, this is the
; recommend error reporting setting. Your production server shouldn't be wasting
; resources complaining about best practices and coding standards. That's what
; development servers and development settings are for.
; Note: The php.ini-development file has this setting as E_ALL. This
; means it pretty much reports everything which is exactly what you want during
; development and early testing.
;
; Error Level Constants:
; E_ALL             - All errors and warnings (includes E_STRICT as of PHP 5.4.0)
; E_ERROR           - fatal run-time errors
; E_RECOVERABLE_ERROR  - almost fatal run-time errors
; E_WARNING         - run-time warnings (non-fatal errors)
; E_PARSE           - compile-time parse errors
; E_NOTICE          - run-time notices (these are warnings which often result
;                     from a bug in your code, but it's possible that it was
;                     intentional (e.g., using an uninitialized variable and
;                     relying on the fact it's automatically initialized to an
;                     empty string)
; E_STRICT          - run-time notices, enable to have PHP suggest changes
;                     to your code which will ensure the best interoperability
;                     and forward compatibility of your code
; E_CORE_ERROR      - fatal errors that occur during PHP's initial startup
; E_CORE_WARNING    - warnings (non-fatal errors) that occur during PHP's
;                     initial startup
; E_COMPILE_ERROR   - fatal compile-time errors
; E_COMPILE_WARNING - compile-time warnings (non-fatal errors)
; E_USER_ERROR      - user-generated error message
; E_USER_WARNING    - user-generated warning message
; E_USER_NOTICE     - user-generated notice message
; E_DEPRECATED      - warn about code that will not work in future versions
;                     of PHP
; E_USER_DEPRECATED - user-generated deprecation warnings
;
; Common Values:
;   E_ALL (Show all errors, warnings and notices including coding standards.)
;   E_ALL & ~E_NOTICE  (Show all errors, except for notices)
;   E_ALL & ~E_NOTICE & ~E_STRICT  (Show all errors, except for notices and coding standards warnings.)
;   E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR  (Show only errors)
; Default Value: E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED
; Development Value: E_ALL
; Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT
; http://php.net/error-reporting
error_reporting = E_ALL

; This directive controls whether or not and where PHP will output errors,
; notices and warnings too. Error output is very useful during development, but
; it could be very dangerous in production environments. Depending on the code
; which is triggering the error, sensitive information could potentially leak
; out of your application such as database usernames and passwords or worse.
; It's recommended that errors be logged on production servers rather than
; having the errors sent to STDOUT.
; Possible Values:
;   Off = Do not display any errors
;   stderr = Display errors to STDERR (affects only CGI/CLI binaries!)
;   On or stdout = Display errors to STDOUT
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/display-errors
display_errors = On

; The display of errors which occur during PHP's startup sequence are handled
; separately from display_errors. PHP's default behavior is to suppress those
; errors from clients. Turning the display of startup errors on can be useful in
; debugging configuration problems. But, it's strongly recommended that you
; leave this setting off on production servers.
; Default Value: Off
; Development Value: On
; Production Value: Off
; http://php.net/display-startup-errors
display_startup_errors = Off

; Besides displaying errors, PHP can also log errors to locations such as a
; server-specific log, STDERR, or a location specified by the error_log
; directive found below. While errors should not be displayed on productions
; servers they should still be monitored and logging is a great way to do that.
; Default Value: Off
; Development Value: On
; Production Value: On
; http://php.net/log-errors
log_errors = On

; Set maximum length of log_errors. In error_log information about the source is
; added. The default is 1024 and 0 allows to not apply any maximum length at all.
; http://php.net/log-errors-max-len
log_errors_max_len = 1024

; Do not log repeated messages. Repeated errors must occur in same file on same
; line unless ignore_repeated_source is set true.
; http://php.net/ignore-repeated-errors
ignore_repeated_errors = Off

; Ignore source of message when ignoring repeated messages. When this setting
; is On you will not log errors with repeated messages from different files or
; source lines.
; http://php.net/ignore-repeated-source
ignore_repeated_source = Off

; If this parameter is set to Off, then memory leaks will not be shown (on
; stdout or in the log). This has only effect in a debug compile, and if
; error reporting includes E_WARNING in the allowed list
; http://php.net/report-memleaks
report_memleaks = On

; This setting is on by default.
;report_zend_debug = 0

; Store the last error/warning message in $php_errormsg (boolean). Setting this value
; to On can assist in debugging and is appropriate for development servers. It should
; however be disabled on production servers.
; Default Value: Off
; Development Value: On
; Production Value: Off
; http://php.net/track-errors
track_errors = Off

; Turn off normal error reporting and emit XML-RPC error XML
; http://php.net/xmlrpc-errors
;xmlrpc_errors = 0

; An XML-RPC faultCode
;xmlrpc_error_number = 0

; When PHP displays or logs an error, it has the capability of formatting the
; error message as HTML for easier reading. This directive controls whether
; the error message is formatted as HTML or not.
; Note: This directive is hardcoded to Off for the CLI SAPI
; Default Value: On
; Development Value: On
; Production value: On
; http://php.net/html-errors
html_errors = On

; If html_errors is set to On *and* docref_root is not empty, then PHP
; produces clickable error messages that direct to a page describing the error
; or function causing the error in detail.
; You can download a copy of the PHP manual from http://php.net/docs
; and change docref_root to the base URL of your local copy including the
; leading '/'. You must also specify the file extension being used including
; the dot. PHP's default behavior is to leave these settings empty, in which
; case no links to documentation are generated.
; Note: Never use this feature for production boxes.
; http://php.net/docref-root
; Examples
;docref_root = "/phpmanual/"

; http://php.net/docref-ext
;docref_ext = .html

; String to output before an error message. PHP's default behavior is to leave
; this setting blank.
; http://php.net/error-prepend-string
; Example:
;error_prepend_string = "<span style='color: #ff0000'>"

; String to output after an error message. PHP's default behavior is to leave
; this setting blank.
; http://php.net/error-append-string
; Example:
;error_append_string = "</span>"

; Log errors to specified file. PHP's default behavior is to leave this value
; empty.
; http://php.net/error-log
; Example:
;error_log = php_errors.log
; Log errors to syslog (Event Log on NT, not valid in Windows 95).
;error_log = syslog

;windows.show_crt_warning
; Default value: 0
; Development value: 0
; Production value: 0

;;;;;;;;;;;;;;;;;
; Data Handling ;
;;;;;;;;;;;;;;;;;

; The separator used in PHP generated URLs to separate arguments.
; PHP's default setting is "&".
; http://php.net/arg-separator.output
; Example:
;arg_separator.output = "&amp;"

; List of separator(s) used by PHP to parse input URLs into variables.
; PHP's default setting is "&".
; NOTE: Every character in this directive is considered as separator!
; http://php.net/arg-separator.input
; Example:
;arg_separator.input = ";&"

; This directive determines which super global arrays are registered when PHP
; starts up. G,P,C,E & S are abbreviations for the following respective super
; globals: GET, POST, COOKIE, ENV and SERVER. There is a performance penalty
; paid for the registration of these arrays and because ENV is not as commonly
; used as the others, ENV is not recommended on productions servers. You
; can still get access to the environment variables through getenv() should you
; need to.
; Default Value: "EGPCS"
; Development Value: "GPCS"
; Production Value: "GPCS";
; http://php.net/variables-order
variables_order = "GPCS"

; This directive determines which super global data (G,P,C,E & S) should
; be registered into the super global array REQUEST. If so, it also determines
; the order in which that data is registered. The values for this directive are
; specified in the same manner as the variables_order directive, EXCEPT one.
; Leaving this value empty will cause PHP to use the value set in the
; variables_order directive. It does not mean it will leave the super globals
; array REQUEST empty.
; Default Value: None
; Development Value: "GP"
; Production Value: "GP"
; http://php.net/request-order
request_order = "GP"

; This directive determines whether PHP registers $argv & $argc each time it
; runs. $argv contains an array of all the arguments passed to PHP when a script
; is invoked. $argc contains an integer representing the number of arguments
; that were passed when the script was invoked. These arrays are extremely
; useful when running scripts from the command line. When this directive is
; enabled, registering these variables consumes CPU cycles and memory each time
; a script is executed. For performance reasons, this feature should be disabled
; on production servers.
; Note: This directive is hardcoded to On for the CLI SAPI
; Default Value: On
; Development Value: Off
; Production Value: Off
; http://php.net/register-argc-argv
register_argc_argv = Off

; When enabled, the ENV, REQUEST and SERVER variables are created when they're
; first used (Just In Time) instead of when the script starts. If these
; variables are not used within a script, having this directive on will result
; in a performance gain. The PHP directive register_argc_argv must be disabled
; for this directive to have any affect.
; http://php.net/auto-globals-jit
auto_globals_jit = On

; Whether PHP will read the POST data.
; This option is enabled by default.
; Most likely, you won't want to disable this option globally. It causes $_POST
; and $_FILES to always be empty; the only way you will be able to read the
; POST data will be through the php://input stream wrapper. This can be useful
; to proxy requests or to process the POST data in a memory efficient fashion.
; http://php.net/enable-post-data-reading
;enable_post_data_reading = Off

; Maximum size of POST data that PHP will accept.
; Its value may be 0 to disable the limit. It is ignored if POST data reading
; is disabled through enable_post_data_reading.
; http://php.net/post-max-size
post_max_size = 10M

; Automatically add files before PHP document.
; http://php.net/auto-prepend-file
auto_prepend_file =

; Automatically add files after PHP document.
; http://php.net/auto-append-file
auto_append_file =

; By default, PHP will output a character encoding using
; the Content-type: header.  To disable sending of the charset, simply
; set it to be empty.
;
; PHP's built-in default is text/html
; http://php.net/default-mimetype
default_mimetype = "text/html"

; PHP's default character set is set to empty.
; http://php.net/default-charset
;default_charset = "UTF-8"

; Always populate the $HTTP_RAW_POST_DATA variable. PHP's default behavior is
; to disable this feature. If post reading is disabled through
; enable_post_data_reading, $HTTP_RAW_POST_DATA is *NOT* populated.
; http://php.net/always-populate-raw-post-data
;always_populate_raw_post_data = On

;;;;;;;;;;;;;;;;;;;;;;;;;
; Paths and Directories ;
;;;;;;;;;;;;;;;;;;;;;;;;;

; UNIX: "/path1:/path2"
;include_path = ".:/usr/share/php"
;
; Windows: "\path1;\path2"
;include_path = ".;c:\php\includes"
;
; PHP's default setting for include_path is ".;/path/to/php/pear"
; http://php.net/include-path

; The root of the PHP pages, used only if nonempty.
; if PHP was not compiled with FORCE_REDIRECT, you SHOULD set doc_root
; if you are running php as a CGI under any web server (other than IIS)
; see documentation for security issues.  The alternate is to use the
; cgi.force_redirect configuration below
; http://php.net/doc-root
doc_root =

; The directory under which PHP opens the script using /~username used only
; if nonempty.
; http://php.net/user-dir
user_dir =

; Directory in which the loadable extensions (modules) reside.
; http://php.net/extension-dir
; extension_dir = "./"
; On windows:
; extension_dir = "ext"

; Directory where the temporary files should be placed.
; Defaults to the system default (see sys_get_temp_dir)
; sys_temp_dir = "/tmp"

; Whether or not to enable the dl() function.  The dl() function does NOT work
; properly in multithreaded servers, such as IIS or Zeus, and is automatically
; disabled on them.
; http://php.net/enable-dl
enable_dl = Off

; cgi.force_redirect is necessary to provide security running PHP as a CGI under
; most web servers.  Left undefined, PHP turns this on by default.  You can
; turn it off here AT YOUR OWN RISK
; **You CAN safely turn this off for IIS, in fact, you MUST.**
; http://php.net/cgi.force-redirect
;cgi.force_redirect = 1

; if cgi.nph is enabled it will force cgi to always sent Status: 200 with
; every request. PHP's default behavior is to disable this feature.
;cgi.nph = 1

; if cgi.force_redirect is turned on, and you are not running under Apache or Netscape
; (iPlanet) web servers, you MAY need to set an environment variable name that PHP
; will look for to know it is OK to continue execution.  Setting this variable MAY
; cause security issues, KNOW WHAT YOU ARE DOING FIRST.
; http://php.net/cgi.redirect-status-env
;cgi.redirect_status_env =

; cgi.fix_pathinfo provides *real* PATH_INFO/PATH_TRANSLATED support for CGI.  PHP's
; previous behaviour was to set PATH_TRANSLATED to SCRIPT_FILENAME, and to not grok
; what PATH_INFO is.  For more information on PATH_INFO, see the cgi specs.  Setting
; this to 1 will cause PHP CGI to fix its paths to conform to the spec.  A setting
; of zero causes PHP to behave as before.  Default is 1.  You should fix your scripts
; to use SCRIPT_FILENAME rather than PATH_TRANSLATED.
; http://php.net/cgi.fix-pathinfo
;cgi.fix_pathinfo=1

; FastCGI under IIS (on WINNT based OS) supports the ability to impersonate
; security tokens of the calling client.  This allows IIS to define the
; security context that the request runs under.  mod_fastcgi under Apache
; does not currently support this feature (03/17/2002)
; Set to 1 if running under IIS.  Default is zero.
; http://php.net/fastcgi.impersonate
;fastcgi.impersonate = 1

; Disable logging through FastCGI connection. PHP's default behavior is to enable
; this feature.
;fastcgi.logging = 0

; cgi.rfc2616_headers configuration option tells PHP what type of headers to
; use when sending HTTP response code. If it's set 0 PHP sends Status: header that
; is supported by Apache. When this option is set to 1 PHP will send
; RFC2616 compliant header.
; Default is zero.
; http://php.net/cgi.rfc2616-headers
;cgi.rfc2616_headers = 0

;;;;;;;;;;;;;;;;
; File Uploads ;
;;;;;;;;;;;;;;;;

; Whether to allow HTTP file uploads.
; http://php.net/file-uploads
file_uploads = On

; Temporary directory for HTTP uploaded files (will use system default if not
; specified).
; http://php.net/upload-tmp-dir
;upload_tmp_dir =

; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
upload_max_filesize = 20M

; Maximum number of files that can be uploaded via a single request
max_file_uploads = 20

;;;;;;;;;;;;;;;;;;
; Fopen wrappers ;
;;;;;;;;;;;;;;;;;;

; Whether to allow the treatment of URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-fopen
allow_url_fopen = On

; Whether to allow include/require to open URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-include
allow_url_include = Off

; Define the anonymous ftp password (your email address). PHP's default setting
; for this is empty.
; http://php.net/from
;from="john@doe.com"

; Define the User-Agent string. PHP's default setting for this is empty.
; http://php.net/user-agent
;user_agent="PHP"

; Default timeout for socket based streams (seconds)
; http://php.net/default-socket-timeout
default_socket_timeout = 60

; If your scripts have to deal with files from Macintosh systems,
; or you are running on a Mac and need to deal with files from
; unix or win32 systems, setting this flag will cause PHP to
; automatically detect the EOL character in those files so that
; fgets() and file() will work regardless of the source of the file.
; http://php.net/auto-detect-line-endings
;auto_detect_line_endings = Off

;;;;;;;;;;;;;;;;;;;;;;
; Dynamic Extensions ;
;;;;;;;;;;;;;;;;;;;;;;

; If you wish to have an extension loaded automatically, use the following
; syntax:
;
;   extension=modulename.extension
;
; For example, on Windows:
;
;   extension=msql.dll
;
; ... or under UNIX:
;
;   extension=msql.so
;
; ... or with a path:
;
;   extension=/path/to/extension/msql.so
;
; If you only provide the name of the extension, PHP will look for it in its
; default extension directory.
;

;;;;;;;;;;;;;;;;;;;
; Module Settings ;
;;;;;;;;;;;;;;;;;;;

[CLI Server]
; Whether the CLI web server uses ANSI color coding in its terminal output.
cli_server.color = On

[Date]
; Defines the default timezone used by the date functions
; http://php.net/date.timezone
date.timezone = "Asia/Shanghai"

; http://php.net/date.default-latitude
;date.default_latitude = 31.7667

; http://php.net/date.default-longitude
;date.default_longitude = 35.2333

; http://php.net/date.sunrise-zenith
;date.sunrise_zenith = 90.583333

; http://php.net/date.sunset-zenith
;date.sunset_zenith = 90.583333

[filter]
; http://php.net/filter.default
;filter.default = unsafe_raw

; http://php.net/filter.default-flags
;filter.default_flags =

[iconv]
;iconv.input_encoding = ISO-8859-1
;iconv.internal_encoding = ISO-8859-1
;iconv.output_encoding = ISO-8859-1

[intl]
;intl.default_locale =
; This directive allows you to produce PHP errors when some error
; happens within intl functions. The value is the level of the error produced.
; Default is 0, which does not produce any errors.
;intl.error_level = E_WARNING

[sqlite]
; http://php.net/sqlite.assoc-case
;sqlite.assoc_case = 0

[sqlite3]
;sqlite3.extension_dir =

[Pcre]
;PCRE library backtracking limit.
; http://php.net/pcre.backtrack-limit
;pcre.backtrack_limit=100000

;PCRE library recursion limit.
;Please note that if you set this value to a high number you may consume all
;the available process stack and eventually crash PHP (due to reaching the
;stack size limit imposed by the Operating System).
; http://php.net/pcre.recursion-limit
;pcre.recursion_limit=100000

[Pdo]
; Whether to pool ODBC connections. Can be one of "strict", "relaxed" or "off"
; http://php.net/pdo-odbc.connection-pooling
;pdo_odbc.connection_pooling=strict

;pdo_odbc.db2_instance_name

[Pdo_mysql]
; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/pdo_mysql.cache_size
pdo_mysql.cache_size = 2000

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/pdo_mysql.default-socket
pdo_mysql.default_socket=

[Phar]
; http://php.net/phar.readonly
;phar.readonly = On

; http://php.net/phar.require-hash
;phar.require_hash = On

;phar.cache_list =

[mail function]
; For Win32 only.
; http://php.net/smtp
SMTP = localhost
; http://php.net/smtp-port
smtp_port = 25

; For Win32 only.
; http://php.net/sendmail-from
;sendmail_from = me@example.com

; For Unix only.  You may supply arguments as well (default: "sendmail -t -i").
; http://php.net/sendmail-path
;sendmail_path =

; Force the addition of the specified parameters to be passed as extra parameters
; to the sendmail binary. These parameters will always replace the value of
; the 5th parameter to mail(), even in safe mode.
;mail.force_extra_parameters =

; Add X-PHP-Originating-Script: that will include uid of the script followed by the filename
mail.add_x_header = On

; The path to a log file that will log all mail() calls. Log entries include
; the full path of the script, line number, To address and headers.
;mail.log =
; Log mail to syslog (Event Log on NT, not valid in Windows 95).
;mail.log = syslog

[SQL]
; http://php.net/sql.safe-mode
sql.safe_mode = Off

[ODBC]
; http://php.net/odbc.default-db
;odbc.default_db    =  Not yet implemented

; http://php.net/odbc.default-user
;odbc.default_user  =  Not yet implemented

; http://php.net/odbc.default-pw
;odbc.default_pw    =  Not yet implemented

; Controls the ODBC cursor model.
; Default: SQL_CURSOR_STATIC (default).
;odbc.default_cursortype

; Allow or prevent persistent links.
; http://php.net/odbc.allow-persistent
odbc.allow_persistent = On

; Check that a connection is still valid before reuse.
; http://php.net/odbc.check-persistent
odbc.check_persistent = On

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/odbc.max-persistent
odbc.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/odbc.max-links
odbc.max_links = -1

; Handling of LONG fields.  Returns number of bytes to variables.  0 means
; passthru.
; http://php.net/odbc.defaultlrl
odbc.defaultlrl = 4096

; Handling of binary data.  0 means passthru, 1 return as is, 2 convert to char.
; See the documentation on odbc_binmode and odbc_longreadlen for an explanation
; of odbc.defaultlrl and odbc.defaultbinmode
; http://php.net/odbc.defaultbinmode
odbc.defaultbinmode = 1

;birdstep.max_links = -1

[Interbase]
; Allow or prevent persistent links.
ibase.allow_persistent = 1

; Maximum number of persistent links.  -1 means no limit.
ibase.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
ibase.max_links = -1

; Default database name for ibase_connect().
;ibase.default_db =

; Default username for ibase_connect().
;ibase.default_user =

; Default password for ibase_connect().
;ibase.default_password =

; Default charset for ibase_connect().
;ibase.default_charset =

; Default timestamp format.
ibase.timestampformat = "%Y-%m-%d %H:%M:%S"

; Default date format.
ibase.dateformat = "%Y-%m-%d"

; Default time format.
ibase.timeformat = "%H:%M:%S"

[MySQL]
; Allow accessing, from PHP's perspective, local files with LOAD DATA statements
; http://php.net/mysql.allow_local_infile
mysql.allow_local_infile = On

; Allow or prevent persistent links.
; http://php.net/mysql.allow-persistent
mysql.allow_persistent = On

; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/mysql.cache_size
mysql.cache_size = 2000

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/mysql.max-persistent
mysql.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/mysql.max-links
mysql.max_links = -1

; Default port number for mysql_connect().  If unset, mysql_connect() will use
; the $MYSQL_TCP_PORT or the mysql-tcp entry in /etc/services or the
; compile-time value defined MYSQL_PORT (in that order).  Win32 will only look
; at MYSQL_PORT.
; http://php.net/mysql.default-port
mysql.default_port =

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/mysql.default-socket
mysql.default_socket =

; Default host for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysql.default-host
mysql.default_host =

; Default user for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysql.default-user
mysql.default_user =

; Default password for mysql_connect() (doesn't apply in safe mode).
; Note that this is generally a *bad* idea to store passwords in this file.
; *Any* user with PHP access can run 'echo get_cfg_var("mysql.default_password")
; and reveal this password!  And of course, any users with read access to this
; file will be able to reveal the password as well.
; http://php.net/mysql.default-password
mysql.default_password =

; Maximum time (in seconds) for connect timeout. -1 means no limit
; http://php.net/mysql.connect-timeout
mysql.connect_timeout = 60

; Trace mode. When trace_mode is active (=On), warnings for table/index scans and
; SQL-Errors will be displayed.
; http://php.net/mysql.trace-mode
mysql.trace_mode = Off

[MySQLi]

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/mysqli.max-persistent
mysqli.max_persistent = -1

; Allow accessing, from PHP's perspective, local files with LOAD DATA statements
; http://php.net/mysqli.allow_local_infile
;mysqli.allow_local_infile = On

; Allow or prevent persistent links.
; http://php.net/mysqli.allow-persistent
mysqli.allow_persistent = On

; Maximum number of links.  -1 means no limit.
; http://php.net/mysqli.max-links
mysqli.max_links = -1

; If mysqlnd is used: Number of cache slots for the internal result set cache
; http://php.net/mysqli.cache_size
mysqli.cache_size = 2000

; Default port number for mysqli_connect().  If unset, mysqli_connect() will use
; the $MYSQL_TCP_PORT or the mysql-tcp entry in /etc/services or the
; compile-time value defined MYSQL_PORT (in that order).  Win32 will only look
; at MYSQL_PORT.
; http://php.net/mysqli.default-port
mysqli.default_port = 3306

; Default socket name for local MySQL connects.  If empty, uses the built-in
; MySQL defaults.
; http://php.net/mysqli.default-socket
mysqli.default_socket =

; Default host for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysqli.default-host
mysqli.default_host =

; Default user for mysql_connect() (doesn't apply in safe mode).
; http://php.net/mysqli.default-user
mysqli.default_user =

; Default password for mysqli_connect() (doesn't apply in safe mode).
; Note that this is generally a *bad* idea to store passwords in this file.
; *Any* user with PHP access can run 'echo get_cfg_var("mysqli.default_pw")
; and reveal this password!  And of course, any users with read access to this
; file will be able to reveal the password as well.
; http://php.net/mysqli.default-pw
mysqli.default_pw =

; Allow or prevent reconnect
mysqli.reconnect = Off

[mysqlnd]
; Enable / Disable collection of general statistics by mysqlnd which can be
; used to tune and monitor MySQL operations.
; http://php.net/mysqlnd.collect_statistics
mysqlnd.collect_statistics = On

; Enable / Disable collection of memory usage statistics by mysqlnd which can be
; used to tune and monitor MySQL operations.
; http://php.net/mysqlnd.collect_memory_statistics
mysqlnd.collect_memory_statistics = Off

; Size of a pre-allocated buffer used when sending commands to MySQL in bytes.
; http://php.net/mysqlnd.net_cmd_buffer_size
;mysqlnd.net_cmd_buffer_size = 2048

; Size of a pre-allocated buffer used for reading data sent by the server in
; bytes.
; http://php.net/mysqlnd.net_read_buffer_size
;mysqlnd.net_read_buffer_size = 32768

[OCI8]

; Connection: Enables privileged connections using external
; credentials (OCI_SYSOPER, OCI_SYSDBA)
; http://php.net/oci8.privileged-connect
;oci8.privileged_connect = Off

; Connection: The maximum number of persistent OCI8 connections per
; process. Using -1 means no limit.
; http://php.net/oci8.max-persistent
;oci8.max_persistent = -1

; Connection: The maximum number of seconds a process is allowed to
; maintain an idle persistent connection. Using -1 means idle
; persistent connections will be maintained forever.
; http://php.net/oci8.persistent-timeout
;oci8.persistent_timeout = -1

; Connection: The number of seconds that must pass before issuing a
; ping during oci_pconnect() to check the connection validity. When
; set to 0, each oci_pconnect() will cause a ping. Using -1 disables
; pings completely.
; http://php.net/oci8.ping-interval
;oci8.ping_interval = 60

; Connection: Set this to a user chosen connection class to be used
; for all pooled server requests with Oracle 11g Database Resident
; Connection Pooling (DRCP).  To use DRCP, this value should be set to
; the same string for all web servers running the same application,
; the database pool must be configured, and the connection string must
; specify to use a pooled server.
;oci8.connection_class =

; High Availability: Using On lets PHP receive Fast Application
; Notification (FAN) events generated when a database node fails. The
; database must also be configured to post FAN events.
;oci8.events = Off

; Tuning: This option enables statement caching, and specifies how
; many statements to cache. Using 0 disables statement caching.
; http://php.net/oci8.statement-cache-size
;oci8.statement_cache_size = 20

; Tuning: Enables statement prefetching and sets the default number of
; rows that will be fetched automatically after statement execution.
; http://php.net/oci8.default-prefetch
;oci8.default_prefetch = 100

; Compatibility. Using On means oci_close() will not close
; oci_connect() and oci_new_connect() connections.
; http://php.net/oci8.old-oci-close-semantics
;oci8.old_oci_close_semantics = Off

[PostgreSQL]
; Allow or prevent persistent links.
; http://php.net/pgsql.allow-persistent
pgsql.allow_persistent = On

; Detect broken persistent links always with pg_pconnect().
; Auto reset feature requires a little overheads.
; http://php.net/pgsql.auto-reset-persistent
pgsql.auto_reset_persistent = Off

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/pgsql.max-persistent
pgsql.max_persistent = -1

; Maximum number of links (persistent+non persistent).  -1 means no limit.
; http://php.net/pgsql.max-links
pgsql.max_links = -1

; Ignore PostgreSQL backends Notice message or not.
; Notice message logging require a little overheads.
; http://php.net/pgsql.ignore-notice
pgsql.ignore_notice = 0

; Log PostgreSQL backends Notice message or not.
; Unless pgsql.ignore_notice=0, module cannot log notice message.
; http://php.net/pgsql.log-notice
pgsql.log_notice = 0

[Sybase-CT]
; Allow or prevent persistent links.
; http://php.net/sybct.allow-persistent
sybct.allow_persistent = On

; Maximum number of persistent links.  -1 means no limit.
; http://php.net/sybct.max-persistent
sybct.max_persistent = -1

; Maximum number of links (persistent + non-persistent).  -1 means no limit.
; http://php.net/sybct.max-links
sybct.max_links = -1

; Minimum server message severity to display.
; http://php.net/sybct.min-server-severity
sybct.min_server_severity = 10

; Minimum client message severity to display.
; http://php.net/sybct.min-client-severity
sybct.min_client_severity = 10

; Set per-context timeout
; http://php.net/sybct.timeout
;sybct.timeout=

;sybct.packet_size

; The maximum time in seconds to wait for a connection attempt to succeed before returning failure.
; Default: one minute
;sybct.login_timeout=

; The name of the host you claim to be connecting from, for display by sp_who.
; Default: none
;sybct.hostname=

; Allows you to define how often deadlocks are to be retried. -1 means "forever".
; Default: 0
;sybct.deadlock_retry_count=

[bcmath]
; Number of decimal digits for all bcmath functions.
; http://php.net/bcmath.scale
bcmath.scale = 0

[browscap]
; http://php.net/browscap
;browscap = extra/browscap.ini

[Session]
; Handler used to store/retrieve data.
; http://php.net/session.save-handler
session.save_handler = files

; Argument passed to save_handler.  In the case of files, this is the path
; where data files are stored. Note: Windows users have to change this
; variable in order to use PHP's session functions.
;
; The path can be defined as:
;
;     session.save_path = "N;/path"
;
; where N is an integer.  Instead of storing all the session files in
; /path, what this will do is use subdirectories N-levels deep, and
; store the session data in those directories.  This is useful if you
; or your OS have problems with lots of files in one directory, and is
; a more efficient layout for servers that handle lots of sessions.
;
; NOTE 1: PHP will not create this directory structure automatically.
;         You can use the script in the ext/session dir for that purpose.
; NOTE 2: See the section on garbage collection below if you choose to
;         use subdirectories for session storage
;
; The file storage module creates files using mode 600 by default.
; You can change that by using
;
;     session.save_path = "N;MODE;/path"
;
; where MODE is the octal representation of the mode. Note that this
; does not overwrite the process's umask.
; http://php.net/session.save-path
;session.save_path = "/var/lib/php5"

; Whether to use strict session mode.
; Strict session mode does not accept uninitialized session ID and regenerate
; session ID if browser sends uninitialized session ID. Strict mode protects
; applications from session fixation via session adoption vulnerability. It is
; disabled by default for maximum compatibility, but enabling it is encouraged.
; https://wiki.php.net/rfc/strict_sessions
session.use_strict_mode = 0

; Whether to use cookies.
; http://php.net/session.use-cookies
session.use_cookies = 1

; http://php.net/session.cookie-secure
;session.cookie_secure =

; This option forces PHP to fetch and use a cookie for storing and maintaining
; the session id. We encourage this operation as it's very helpful in combating
; session hijacking when not specifying and managing your own session id. It is
; not the end all be all of session hijacking defense, but it's a good start.
; http://php.net/session.use-only-cookies
session.use_only_cookies = 1

; Name of the session (used as cookie name).
; http://php.net/session.name
session.name = PHPSESSID

; Initialize session on request startup.
; http://php.net/session.auto-start
session.auto_start = 0

; Lifetime in seconds of cookie or, if 0, until browser is restarted.
; http://php.net/session.cookie-lifetime
session.cookie_lifetime = 0

; The path for which the cookie is valid.
; http://php.net/session.cookie-path
session.cookie_path = /

; The domain for which the cookie is valid.
; http://php.net/session.cookie-domain
session.cookie_domain =

; Whether or not to add the httpOnly flag to the cookie, which makes it inaccessible to browser scripting languages such as JavaScript.
; http://php.net/session.cookie-httponly
session.cookie_httponly =

; Handler used to serialize data.  php is the standard serializer of PHP.
; http://php.net/session.serialize-handler
session.serialize_handler = php

; Defines the probability that the 'garbage collection' process is started
; on every session initialization. The probability is calculated by using
; gc_probability/gc_divisor. Where session.gc_probability is the numerator
; and gc_divisor is the denominator in the equation. Setting this value to 1
; when the session.gc_divisor value is 100 will give you approximately a 1% chance
; the gc will run on any give request.
; Default Value: 1
; Development Value: 1
; Production Value: 1
; http://php.net/session.gc-probability
session.gc_probability = 0

; Defines the probability that the 'garbage collection' process is started on every
; session initialization. The probability is calculated by using the following equation:
; gc_probability/gc_divisor. Where session.gc_probability is the numerator and
; session.gc_divisor is the denominator in the equation. Setting this value to 1
; when the session.gc_divisor value is 100 will give you approximately a 1% chance
; the gc will run on any give request. Increasing this value to 1000 will give you
; a 0.1% chance the gc will run on any give request. For high volume production servers,
; this is a more efficient approach.
; Default Value: 100
; Development Value: 1000
; Production Value: 1000
; http://php.net/session.gc-divisor
session.gc_divisor = 1000

; After this number of seconds, stored data will be seen as 'garbage' and
; cleaned up by the garbage collection process.
; http://php.net/session.gc-maxlifetime
session.gc_maxlifetime = 1440

; NOTE: If you are using the subdirectory option for storing session files
;       (see session.save_path above), then garbage collection does *not*
;       happen automatically.  You will need to do your own garbage
;       collection through a shell script, cron entry, or some other method.
;       For example, the following script would is the equivalent of
;       setting session.gc_maxlifetime to 1440 (1440 seconds = 24 minutes):
;          find /path/to/sessions -cmin +24 -type f | xargs rm

; PHP 4.2 and less have an undocumented feature/bug that allows you to
; to initialize a session variable in the global scope.
; PHP 4.3 and later will warn you, if this feature is used.
; You can disable the feature and the warning separately. At this time,
; the warning is only displayed, if bug_compat_42 is enabled. This feature
; introduces some serious security problems if not handled correctly. It's
; recommended that you do not use this feature on production servers. But you
; should enable this on development servers and enable the warning as well. If you
; do not enable the feature on development servers, you won't be warned when it's
; used and debugging errors caused by this can be difficult to track down.
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/session.bug-compat-42
session.bug_compat_42 = Off

; This setting controls whether or not you are warned by PHP when initializing a
; session value into the global space. session.bug_compat_42 must be enabled before
; these warnings can be issued by PHP. See the directive above for more information.
; Default Value: On
; Development Value: On
; Production Value: Off
; http://php.net/session.bug-compat-warn
session.bug_compat_warn = Off

; Check HTTP Referer to invalidate externally stored URLs containing ids.
; HTTP_REFERER has to contain this substring for the session to be
; considered as valid.
; http://php.net/session.referer-check
session.referer_check =

; How many bytes to read from the file.
; http://php.net/session.entropy-length
;session.entropy_length = 32

; Specified here to create the session id.
; http://php.net/session.entropy-file
; Defaults to /dev/urandom
; On systems that don't have /dev/urandom but do have /dev/arandom, this will default to /dev/arandom
; If neither are found at compile time, the default is no entropy file.
; On windows, setting the entropy_length setting will activate the
; Windows random source (using the CryptoAPI)
;session.entropy_file = /dev/urandom

; Set to {nocache,private,public,} to determine HTTP caching aspects
; or leave this empty to avoid sending anti-caching headers.
; http://php.net/session.cache-limiter
session.cache_limiter = nocache

; Document expires after n minutes.
; http://php.net/session.cache-expire
session.cache_expire = 180

; trans sid support is disabled by default.
; Use of trans sid may risk your users security.
; Use this option with caution.
; - User may send URL contains active session ID
;   to other person via. email/irc/etc.
; - URL that contains active session ID may be stored
;   in publicly accessible computer.
; - User may access your site with the same session ID
;   always using URL stored in browser's history or bookmarks.
; http://php.net/session.use-trans-sid
session.use_trans_sid = 0

; Select a hash function for use in generating session ids.
; Possible Values
;   0  (MD5 128 bits)
;   1  (SHA-1 160 bits)
; This option may also be set to the name of any hash function supported by
; the hash extension. A list of available hashes is returned by the hash_algos()
; function.
; http://php.net/session.hash-function
session.hash_function = 0

; Define how many bits are stored in each character when converting
; the binary hash data to something readable.
; Possible values:
;   4  (4 bits: 0-9, a-f)
;   5  (5 bits: 0-9, a-v)
;   6  (6 bits: 0-9, a-z, A-Z, "-", ",")
; Default Value: 4
; Development Value: 5
; Production Value: 5
; http://php.net/session.hash-bits-per-character
session.hash_bits_per_character = 5

; The URL rewriter will look for URLs in a defined set of HTML tags.
; form/fieldset are special; if you include them here, the rewriter will
; add a hidden <input> field with the info which is otherwise appended
; to URLs.  If you want XHTML conformity, remove the form entry.
; Note that all valid entries require a "=", even if no value follows.
; Default Value: "a=href,area=href,frame=src,form=,fieldset="
; Development Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
; Production Value: "a=href,area=href,frame=src,input=src,form=fakeentry"
; http://php.net/url-rewriter.tags
url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=fakeentry"

; Enable upload progress tracking in $_SESSION
; Default Value: On
; Development Value: On
; Production Value: On
; http://php.net/session.upload-progress.enabled
;session.upload_progress.enabled = On

; Cleanup the progress information as soon as all POST data has been read
; (i.e. upload completed).
; Default Value: On
; Development Value: On
; Production Value: On
; http://php.net/session.upload-progress.cleanup
;session.upload_progress.cleanup = On

; A prefix used for the upload progress key in $_SESSION
; Default Value: "upload_progress_"
; Development Value: "upload_progress_"
; Production Value: "upload_progress_"
; http://php.net/session.upload-progress.prefix
;session.upload_progress.prefix = "upload_progress_"

; The index name (concatenated with the prefix) in $_SESSION
; containing the upload progress information
; Default Value: "PHP_SESSION_UPLOAD_PROGRESS"
; Development Value: "PHP_SESSION_UPLOAD_PROGRESS"
; Production Value: "PHP_SESSION_UPLOAD_PROGRESS"
; http://php.net/session.upload-progress.name
;session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"

; How frequently the upload progress should be updated.
; Given either in percentages (per-file), or in bytes
; Default Value: "1%"
; Development Value: "1%"
; Production Value: "1%"
; http://php.net/session.upload-progress.freq
;session.upload_progress.freq =  "1%"

; The minimum delay between updates, in seconds
; Default Value: 1
; Development Value: 1
; Production Value: 1
; http://php.net/session.upload-progress.min-freq
;session.upload_progress.min_freq = "1"

[MSSQL]
; Allow or prevent persistent links.
mssql.allow_persistent = On

; Maximum number of persistent links.  -1 means no limit.
mssql.max_persistent = -1

; Maximum number of links (persistent+non persistent).  -1 means no limit.
mssql.max_links = -1

; Minimum error severity to display.
mssql.min_error_severity = 10

; Minimum message severity to display.
mssql.min_message_severity = 10

; Compatibility mode with old versions of PHP 3.0.
mssql.compatibility_mode = Off

; Connect timeout
;mssql.connect_timeout = 5

; Query timeout
;mssql.timeout = 60

; Valid range 0 - 2147483647.  Default = 4096.
;mssql.textlimit = 4096

; Valid range 0 - 2147483647.  Default = 4096.
;mssql.textsize = 4096

; Limits the number of records in each batch.  0 = all records in one batch.
;mssql.batchsize = 0

; Specify how datetime and datetim4 columns are returned
; On => Returns data converted to SQL server settings
; Off => Returns values as YYYY-MM-DD hh:mm:ss
;mssql.datetimeconvert = On

; Use NT authentication when connecting to the server
mssql.secure_connection = Off

; Specify max number of processes. -1 = library default
; msdlib defaults to 25
; FreeTDS defaults to 4096
;mssql.max_procs = -1

; Specify client character set.
; If empty or not set the client charset from freetds.conf is used
; This is only used when compiled with FreeTDS
;mssql.charset = "ISO-8859-1"

[Assertion]
; Assert(expr); active by default.
; http://php.net/assert.active
;assert.active = On

; Issue a PHP warning for each failed assertion.
; http://php.net/assert.warning
;assert.warning = On

; Don't bail out by default.
; http://php.net/assert.bail
;assert.bail = Off

; User-function to be called if an assertion fails.
; http://php.net/assert.callback
;assert.callback = 0

; Eval the expression with current error_reporting().  Set to true if you want
; error_reporting(0) around the eval().
; http://php.net/assert.quiet-eval
;assert.quiet_eval = 0

[COM]
; path to a file containing GUIDs, IIDs or filenames of files with TypeLibs
; http://php.net/com.typelib-file
;com.typelib_file =

; allow Distributed-COM calls
; http://php.net/com.allow-dcom
;com.allow_dcom = true

; autoregister constants of a components typlib on com_load()
; http://php.net/com.autoregister-typelib
;com.autoregister_typelib = true

; register constants casesensitive
; http://php.net/com.autoregister-casesensitive
;com.autoregister_casesensitive = false

; show warnings on duplicate constant registrations
; http://php.net/com.autoregister-verbose
;com.autoregister_verbose = true

; The default character set code-page to use when passing strings to and from COM objects.
; Default: system ANSI code page
;com.code_page=

[mbstring]
; language for internal character representation.
; http://php.net/mbstring.language
;mbstring.language = Japanese

; internal/script encoding.
; Some encoding cannot work as internal encoding.
; (e.g. SJIS, BIG5, ISO-2022-*)
; http://php.net/mbstring.internal-encoding
;mbstring.internal_encoding = UTF-8

; http input encoding.
; http://php.net/mbstring.http-input
;mbstring.http_input = UTF-8

; http output encoding. mb_output_handler must be
; registered as output buffer to function
; http://php.net/mbstring.http-output
;mbstring.http_output = pass

; enable automatic encoding translation according to
; mbstring.internal_encoding setting. Input chars are
; converted to internal encoding by setting this to On.
; Note: Do _not_ use automatic encoding translation for
;       portable libs/applications.
; http://php.net/mbstring.encoding-translation
;mbstring.encoding_translation = Off

; automatic encoding detection order.
; auto means
; http://php.net/mbstring.detect-order
;mbstring.detect_order = auto

; substitute_character used when character cannot be converted
; one from another
; http://php.net/mbstring.substitute-character
;mbstring.substitute_character = none

; overload(replace) single byte functions by mbstring functions.
; mail(), ereg(), etc are overloaded by mb_send_mail(), mb_ereg(),
; etc. Possible values are 0,1,2,4 or combination of them.
; For example, 7 for overload everything.
; 0: No overload
; 1: Overload mail() function
; 2: Overload str*() functions
; 4: Overload ereg*() functions
; http://php.net/mbstring.func-overload
;mbstring.func_overload = 0

; enable strict encoding detection.
;mbstring.strict_detection = On

; This directive specifies the regex pattern of content types for which mb_output_handler()
; is activated.
; Default: mbstring.http_output_conv_mimetype=^(text/|application/xhtml\+xml)
;mbstring.http_output_conv_mimetype=

[gd]
; Tell the jpeg decode to ignore warnings and try to create
; a gd image. The warning will then be displayed as notices
; disabled by default
; http://php.net/gd.jpeg-ignore-warning
;gd.jpeg_ignore_warning = 0

[exif]
; Exif UNICODE user comments are handled as UCS-2BE/UCS-2LE and JIS as JIS.
; With mbstring support this will automatically be converted into the encoding
; given by corresponding encode setting. When empty mbstring.internal_encoding
; is used. For the decode settings you can distinguish between motorola and
; intel byte order. A decode setting cannot be empty.
; http://php.net/exif.encode-unicode
;exif.encode_unicode = ISO-8859-15

; http://php.net/exif.decode-unicode-motorola
;exif.decode_unicode_motorola = UCS-2BE

; http://php.net/exif.decode-unicode-intel
;exif.decode_unicode_intel    = UCS-2LE

; http://php.net/exif.encode-jis
;exif.encode_jis =

; http://php.net/exif.decode-jis-motorola
;exif.decode_jis_motorola = JIS

; http://php.net/exif.decode-jis-intel
;exif.decode_jis_intel    = JIS

[Tidy]
; The path to a default tidy configuration file to use when using tidy
; http://php.net/tidy.default-config
;tidy.default_config = /usr/local/lib/php/default.tcfg

; Should tidy clean and repair output automatically?
; WARNING: Do not use this option if you are generating non-html content
; such as dynamic images
; http://php.net/tidy.clean-output
tidy.clean_output = Off

[soap]
; Enables or disables WSDL caching feature.
; http://php.net/soap.wsdl-cache-enabled
soap.wsdl_cache_enabled=1

; Sets the directory name where SOAP extension will put cache files.
; http://php.net/soap.wsdl-cache-dir
soap.wsdl_cache_dir="/tmp"

; (time to live) Sets the number of second while cached file will be used
; instead of original one.
; http://php.net/soap.wsdl-cache-ttl
soap.wsdl_cache_ttl=86400

; Sets the size of the cache limit. (Max. number of WSDL files to cache)
soap.wsdl_cache_limit = 5

[sysvshm]
; A default size of the shared memory segment
;sysvshm.init_mem = 10000

[ldap]
; Sets the maximum number of open links or -1 for unlimited.
ldap.max_links = -1

[mcrypt]
; For more information about mcrypt settings see http://php.net/mcrypt-module-open

; Directory where to load mcrypt algorithms
; Default: Compiled in into libmcrypt (usually /usr/local/lib/libmcrypt)
;mcrypt.algorithms_dir=

; Directory where to load mcrypt modes
; Default: Compiled in into libmcrypt (usually /usr/local/lib/libmcrypt)
;mcrypt.modes_dir=

[dba]
;dba.default_handler=

[opcache]
; Determines if Zend OPCache is enabled
;opcache.enable=0

; Determines if Zend OPCache is enabled for the CLI version of PHP
;opcache.enable_cli=0

; The OPcache shared memory storage size.
;opcache.memory_consumption=64

; The amount of memory for interned strings in Mbytes.
;opcache.interned_strings_buffer=4

; The maximum number of keys (scripts) in the OPcache hash table.
; Only numbers between 200 and 100000 are allowed.
;opcache.max_accelerated_files=2000

; The maximum percentage of "wasted" memory until a restart is scheduled.
;opcache.max_wasted_percentage=5

; When this directive is enabled, the OPcache appends the current working
; directory to the script key, thus eliminating possible collisions between
; files with the same name (basename). Disabling the directive improves
; performance, but may break existing applications.
;opcache.use_cwd=1

; When disabled, you must reset the OPcache manually or restart the
; webserver for changes to the filesystem to take effect.
;opcache.validate_timestamps=1

; How often (in seconds) to check file timestamps for changes to the shared
; memory storage allocation. ("1" means validate once per second, but only
; once per request. "0" means always validate)
;opcache.revalidate_freq=2

; Enables or disables file search in include_path optimization
;opcache.revalidate_path=0

; If disabled, all PHPDoc comments are dropped from the code to reduce the
; size of the optimized code.
;opcache.save_comments=1

; If disabled, PHPDoc comments are not loaded from SHM, so "Doc Comments"
; may be always stored (save_comments=1), but not loaded by applications
; that don't need them anyway.
;opcache.load_comments=1

; If enabled, a fast shutdown sequence is used for the accelerated code
;opcache.fast_shutdown=0

; Allow file existence override (file_exists, etc.) performance feature.
;opcache.enable_file_override=0

; A bitmask, where each bit enables or disables the appropriate OPcache
; passes
;opcache.optimization_level=0xffffffff

;opcache.inherited_hack=1
;opcache.dups_fix=0

; The location of the OPcache blacklist file (wildcards allowed).
; Each OPcache blacklist file is a text file that holds the names of files
; that should not be accelerated. The file format is to add each filename
; to a new line. The filename may be a full path or just a file prefix
; (i.e., /var/www/x  blacklists all the files and directories in /var/www
; that start with 'x'). Line starting with a ; are ignored (comments).
;opcache.blacklist_filename=

; Allows exclusion of large files from being cached. By default all files
; are cached.
;opcache.max_file_size=0

; Check the cache checksum each N requests.
; The default value of "0" means that the checks are disabled.
;opcache.consistency_checks=0

; How long to wait (in seconds) for a scheduled restart to begin if the cache
; is not being accessed.
;opcache.force_restart_timeout=180

; OPcache error_log file name. Empty string assumes "stderr".
;opcache.error_log=

; All OPcache errors go to the Web server log.
; By default, only fatal errors (level 0) or errors (level 1) are logged.
; You can also enable warnings (level 2), info messages (level 3) or
; debug messages (level 4).
;opcache.log_verbosity_level=1

; Preferred Shared Memory back-end. Leave empty and let the system decide.
;opcache.preferred_memory_model=

; Protect the shared memory from unexpected writing during script execution.
; Useful for internal debugging only.
;opcache.protect_memory=0

[curl]
; A default value for the CURLOPT_CAINFO option. This is required to be an
; absolute path.
;curl.cainfo =

; Local Variables:
; tab-width: 4
; End:
```

#### MySQL

Dockerfile 文件内容：
```
ARG MYSQL_VER

FROM mysql:${MYSQL_VER}

# 设置时区为上海
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Ubuntu软件源选择中国的服务器
RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

# 更新安装依赖包
RUN apt-get update && apt-get install -y \
        vim \
        wget \
```

docker-compose.yml 文件内容：
```
version: '3.0'
services:
  mysql-db: # 相关文档 https://hub.docker.com/_/mysql
    container_name: mysql-db
    build:
      context: .
      args:
        - MYSQL_VER=8.0
    ports:
      - "3306:3306"
    volumes:
      - ../../data/mysql:/var/lib/mysql:rw
      - ../../logs/mysql:/var/lib/mysql-logs:rw
      - ./conf.d:/etc/mysql/conf.d:ro
    environment:
      MYSQL_ROOT_PASSWORD: 5eNyj6Nf # root密码 自行修改
      MYSQL_DATABASE: rageframe # 数据库名
      MYSQL_USER: test # 子账号
      MYSQL_PASSWORD: 2589632147 # 子密码
    restart: always
    network_mode: host
    command: "--character-set-server=utf8mb4"
```

`./conf.d` 文件夹下 mysql-file.cnf 文件内容：
```
[client]
#password = your_password
port=3306

[mysql]
no-auto-rehash

[mysqld]
port = 3306

#skip-networking
max_connections = 500 #最大连接数
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
log-bin-index = mysql-bin.index
binlog_format = mixed
expire_logs_days = 10 #binlog过期清理时间
server_id = 1
sync-binlog = 1
character-set-server = utf8mb4

max_allowed_packet = 4M

# 慢日志
long_query_time = 1
log_queries_not_using_indexes = 1
slow_query_log = 1
slow_query_log_file =/var/lib/mysql-logs/slow.log

back_log = 128
wait_timeout = 100
interactive_timeout = 200

skip-host-cache
skip-name-resolve

default-storage-engine=INNODB
default_authentication_plugin=mysql_native_password

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



