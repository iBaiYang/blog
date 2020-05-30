---
layout: post
categories: Linux
title: 阿里云ECS之Docker使用
meta: 阿里云ECS之Docker使用
---
* content
{:toc}

### 正文

在阿里云上购买ECS后，命令行root连接登录实例。

ECS实例是centos 7.6 64位系统。

移除老版本：
```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

安装docker:

> yum install docker

输出:
```
Loaded plugins: fastestmirror, product-id, search-disabled-repos, subscription-manager

This system is not registered with an entitlement server. You can use subscription-manager to register.

Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package docker.x86_64 2:1.13.1-161.git64e9980.el7_8 will be installed
--> Processing Dependency: docker-common = 2:1.13.1-161.git64e9980.el7_8 for package: 2:docker-1.13.1-161.git64e9980.el7_8.x86_64
--> Processing Dependency: docker-client = 2:1.13.1-161.git64e9980.el7_8 for package: 2:docker-1.13.1-161.git64e9980.el7_8.x86_64
--> Running transaction check
---> Package docker-client.x86_64 2:1.13.1-161.git64e9980.el7_8 will be installed
---> Package docker-common.x86_64 2:1.13.1-161.git64e9980.el7_8 will be installed
--> Processing Dependency: container-selinux >= 2:2.51-1 for package: 2:docker-common-1.13.1-161.git64e9980.el7_8.x86_64
--> Running transaction check
---> Package container-selinux.noarch 2:2.119.1-1.c57a6f9.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================================================================================================================
 Package                                         Arch                                 Version                                                      Repository                            Size
==============================================================================================================================================================================================
Installing:
 docker                                          x86_64                               2:1.13.1-161.git64e9980.el7_8                                extras                                18 M
Installing for dependencies:
 container-selinux                               noarch                               2:2.119.1-1.c57a6f9.el7                                      extras                                40 k
 docker-client                                   x86_64                               2:1.13.1-161.git64e9980.el7_8                                extras                               3.9 M
 docker-common                                   x86_64                               2:1.13.1-161.git64e9980.el7_8                                extras                                99 k

Transaction Summary
==============================================================================================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 22 M
Installed size: 77 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): container-selinux-2.119.1-1.c57a6f9.el7.noarch.rpm                                                                                                              |  40 kB  00:00:00     
(2/4): docker-1.13.1-161.git64e9980.el7_8.x86_64.rpm                                                                                                                   |  18 MB  00:00:00     
(3/4): docker-client-1.13.1-161.git64e9980.el7_8.x86_64.rpm                                                                                                            | 3.9 MB  00:00:00     
(4/4): docker-common-1.13.1-161.git64e9980.el7_8.x86_64.rpm                                                                                                            |  99 kB  00:00:00     
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                                          70 MB/s |  22 MB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 2:container-selinux-2.119.1-1.c57a6f9.el7.noarch                                                                                                                           1/4 
setsebool:  SELinux is disabled.
  Installing : 2:docker-common-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                         2/4 
  Installing : 2:docker-client-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                         3/4 
  Installing : 2:docker-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                                4/4 
  Verifying  : 2:container-selinux-2.119.1-1.c57a6f9.el7.noarch                                                                                                                           1/4 
  Verifying  : 2:docker-client-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                         2/4 
  Verifying  : 2:docker-common-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                         3/4 
  Verifying  : 2:docker-1.13.1-161.git64e9980.el7_8.x86_64                                                                                                                                4/4 

Installed:
  docker.x86_64 2:1.13.1-161.git64e9980.el7_8                                                                                                                                                 

Dependency Installed:
  container-selinux.noarch 2:2.119.1-1.c57a6f9.el7             docker-client.x86_64 2:1.13.1-161.git64e9980.el7_8             docker-common.x86_64 2:1.13.1-161.git64e9980.el7_8            

Complete!
```

设置开机自动启动:

> service docker start

输出：
```
Redirecting to /bin/systemctl start docker.service
```

查看版本：

> docker version

输出：
```
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-161.git64e9980.el7_8.x86_64
 Go version:      go1.10.3
 Git commit:      64e9980/1.13.1
 Built:           Tue Apr 28 14:43:01 2020
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-161.git64e9980.el7_8.x86_64
 Go version:      go1.10.3
 Git commit:      64e9980/1.13.1
 Built:           Tue Apr 28 14:43:01 2020
 OS/Arch:         linux/amd64
 Experimental:    false
```

修改docker仓库地址：

> vi /etc/docker/daemon.json 

写入内容，保存并退出：
```
{"registry-mirrors": ["https://registry.docker-cn.com"], "live-restore": true}
```

#### nginx安装

> docker pull nginx

输出：
```
Using default tag: latest
Trying to pull repository docker.io/library/nginx ... 
latest: Pulling from docker.io/library/nginx
afb6ec6fdc1c: Pull complete 
b90c53a0b692: Pull complete 
11fa52a0fdc0: Pull complete 
Digest: sha256:6fff55753e3b34e36e24e37039ee9eae1fe38a6420d8ae16ef37c92d1eb26699
Status: Downloaded newer image for docker.io/nginx:latest
```

创建容器并运行：
```
docker run -p 80:80 -d nginx
```

查看容器：
> docker ps

可以看到nginx正在Up运行状态中：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
8a81faef1e1d        nginx               "nginx -g 'daemon ..."   9 seconds ago       Up 8 seconds        0.0.0.0:80->80/tcp   unruffled_franklin
```

我们还需要配置实例安全组，外网才能访问服务器80端口。

在阿里云配置中心——云服务器 ECS——点击实例，在实例管理处点击左侧菜单的本实例安全组，可以看到右侧三个栏目：

内网入方向全部规则 | 内网出方向全部规则 | 安全组列表

点击 内网入方向全部规则，看到默认的内容：
```
授权策略	协议类型	端口范围	授权类型(全部)授权对象	描述	优先级
允许	自定义 TCP	22/22	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	全部 ICMP(IPv4)	-1/-1	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	自定义 TCP	3389/3389	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
```

我们需要再加一条入方向80端口的安全规则，加入后用浏览器访问我们实例的公网ip就可以看到nginx欢迎的界面了。

#### mysql安装

下载mysql镜像：

> docker pull mysql:5.7

输出：
```
Trying to pull repository docker.io/library/mysql ... 
5.7: Pulling from docker.io/library/mysql
afb6ec6fdc1c: Already exists 
0bdc5971ba40: Pull complete 
97ae94a2c729: Pull complete 
f777521d340e: Pull complete 
1393ff7fc871: Pull complete 
a499b89994d9: Pull complete 
7ebe8eefbafe: Pull complete 
4eec965ae405: Pull complete 
a531a782d709: Pull complete 
270aeddb45e3: Pull complete 
b25569b61008: Pull complete 
Digest: sha256:d16d9ef7a4ecb29efcd1ba46d5a82bda3c28bd18c0f1e3b86ba54816211e1ac4
Status: Downloaded newer image for docker.io/mysql:5.7
```

把-v与容器挂载的目录准备好：

> mkdir -p /web/mysql /web/mysql/data /web/mysql/conf.d /web/mysql/logs 

创建容器并运行：
```
docker run --name server-mysql -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=few#$dgd1@2dsd%^f \
  -v /web/mysql/data:/var/lib/mysql \
  -v /web/mysql/conf.d:/etc/mysql/conf.d \
  -v /web/mysql/logs:/logs \
  -v /etc/localtime:/etc/localtime:ro \
  -d mysql:5.7
```

data目录将映射为mysql容器配置的数据文件存放路径

logs目录将映射为mysql容器的日志目录

conf.d目录里的配置文件将映射为mysql容器的配置文件

`-v /etc/localtime:/etc/localtime:ro` 因为容器内的时间会跟宿主机相差 8 个小时，加载这个目录是为了校正时间跟宿主机时间一致。

#### php-fpm安装

下载镜像：

> docker pull php:7.1.30-fpm

输出：
```
Trying to pull repository docker.io/library/php ... 
7.1.30-fpm: Pulling from docker.io/library/php
f5d23c7fed46: Pull complete 
4f36b8588ea0: Pull complete 
6f4f95ddefa8: Pull complete 
187af28c9b1d: Pull complete 
7ba9cd8f12bd: Pull complete 
19ce450f6a80: Pull complete 
6a0aa94e79c7: Pull complete 
3097ec58d870: Pull complete 
05ecbde01690: Pull complete 
ab28ea58dda0: Pull complete 
Digest: sha256:a0f0773dc2f92ca8f4dab7c7c525574d467d3aa4bb27424bb7f0540a7c9efcd0
Status: Downloaded newer image for docker.io/php:7.1.30-fpm
```

把-v与容器挂载的目录准备好：

> mkdir -p /web/nginx /web/nginx/conf/vhost /web/nginx/logs \
  /var/www 

创建容器并运行：
```
docker run --name server-phpfpm71 -p 9000:9000 \
  -v /var/www:/www \
  -v /etc/localtime:/etc/localtime:ro \
  -d php:7.1.30-fpm
```

#### web项目搭建

我们上面已经运行了nginx容器，但这个容器并无法调用php脚本服务，我们需要做相应配置。

把容器中的nginx.conf文件复制到服务器目录下：

> docker cp 8a81faef1e1d:/etc/nginx/nginx.conf /web/nginx/conf/nginx.conf

nginx.conf文件内容：
```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

把容器中的conf.d/dafault.conf文件复制到服务器目录下：

> docker cp 8a81faef1e1d:/etc/nginx/conf.d/dafault.conf /web/nginx/conf/vhost/

dafault.conf文件内容：
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

我们可以在 vhost 目录下写域名项目配置文件。

把我们上面运行的nginx容器停止运行并删除

> docker rm -f 8a81faef1e1d

创建新的nginx容器并运行：
```
docker run --name server-nginx -p 80:80 \
  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm71:php \
  -d nginx
```



<br/><br/><br/><br/><br/>
### 参考资料

阿里云ECS服务器安装docker <https://www.cnblogs.com/one-reader/p/11406047.html>

阿里云ECS-yum 安装docker <https://blog.csdn.net/wxb880114/article/details/82219701>

服务器部署docker lnmp环境 <https://www.cnblogs.com/cxscode/p/11070880.html>

linux记录-docker配置mysql <https://www.cnblogs.com/xinfang520/p/11122638.html>

阿里云ECS云服务器详细教程CentOS 7 <https://blog.csdn.net/qq_41399901/article/details/84953155>

Docker 部署lnmp <https://blog.csdn.net/weixin_42890981/article/details/86749240>

