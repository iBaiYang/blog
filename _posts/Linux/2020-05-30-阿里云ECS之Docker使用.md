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
允许	自定义 TCP	
22/22
	IPv4地址段访问	

0.0.0.0/0
	
System created rule.
	100	
允许	全部 ICMP(IPv4)	
-1/-1
	IPv4地址段访问	

0.0.0.0/0
	
System created rule.
	100	
允许	自定义 TCP	
3389/3389
	IPv4地址段访问	

0.0.0.0/0
	
System created rule.
	100	
```

<br/><br/><br/><br/><br/>
### 参考资料

阿里云ECS服务器安装docker <https://www.cnblogs.com/one-reader/p/11406047.html>

阿里云ECS-yum 安装docker <https://blog.csdn.net/wxb880114/article/details/82219701>
