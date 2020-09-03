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

ECS实例是centos 7.6 64位系统。 centos 8.*版本安装会有问题。

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
docker run \
  --name server-mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=abc$123* \
  -v /web/mysql/data:/var/lib/mysql \
  -v /web/mysql/conf.d:/etc/mysql/conf.d \
  -v /web/mysql/logs:/logs \
  -v /etc/localtime:/etc/localtime:ro \
  -d mysql:5.7
```

MYSQL_ROOT_PASSWORD 是 root 用户连接数据库服务的密码。 

/web/mysql/data 目录将映射为mysql容器配置的数据文件存放路径

/web/mysql/conf.d 目录里的配置文件将映射为mysql容器的配置文件

/web/mysql/logs 目录将映射为mysql容器的日志目录

`-v /etc/localtime:/etc/localtime:ro` 因为容器内的时间会跟宿主机相差 8 个小时，加载这个目录是为了校正时间跟宿主机时间一致。

查看容器运行状态：
> docker ps -a

输出：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
2bfff24639a3        mysql:5.7           "docker-entrypoint..."   13 seconds ago      Up 12 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

##### 新建用户和数据库

接下来可以新建用户和新建库：

进入mysql容器：

> docker exec -it server-mysql /bin/bash

首先用root登录mysql

> mysql -u root -p

输入密码，进入。

**创建用户**，语法：
```
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

user_name：要创建用户的名字。

host：表示要这个新创建的用户允许从哪台机登陆，如果只允许从本机登陆，则 填　‘localhost’ ，如果允许从远程登陆，则填 ‘%’

password：新创建用户的登陆数据库密码，如果没密码可以不写。

如：
```
CREATE USER 'dog'@'%' IDENTIFIED BY '123456';
```

**创建数据库**：

> CREATE DATABASE IF NOT EXISTS dog DEFAULT CHARSET utf8mb4 COLLATE utf8mb4_general_ci;

**用户数据库赋权**，语法：

> GRANT privileges ON databasename.tablename TO ‘username’@’host’

privileges：表示要授予什么权力，例如可以有 select ， insert ，delete，update等，如果要授予全部权力，则填 ALL

databasename.tablename：表示用户的权限能用在哪个库的哪个表中，如果想要用户的权限很作用于所有的数据库所有的表，则填 *.*，*是一个通配符，表示全部。

’username‘@‘host’：表示授权给哪个用户。

如：
```
GRANT  ALL  ON  dog.*  TO  ‘dog’@‘%’；
```

注意：

用以上命令授权的用户不能给其他用户授权，如果想这个用户能够给其他用户授权，就要在后面加上  WITH GRANT OPTION

如： 
```
GRANT  ALL  ON   *.*   TO  ’aaa‘@'%'  WITH GRANT OPTION； 
```

#### php安装

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

```
mkdir -p /web/php-fpm/etc /var/www/html 
```

创建容器并运行：
```
docker run \
  --name server-phpfpm \ 
  -p 9000:9000 \
  -v /web/php-fpm/etc:/usr/local/etc/php \
  -v /var/www/html:/var/www/html \
  -v /etc/localtime:/etc/localtime:ro \
  -d php:7.1.30-fpm
```

查看容器运行状态：
> docker ps -a

输出：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
3a03eba032c0        php:7.1.30-fpm      "docker-php-entryp..."   11 seconds ago      Up 10 seconds       0.0.0.0:9000->9000/tcp              server-phpfpm
2bfff24639a3        mysql:5.7           "docker-entrypoint..."   28 minutes ago      Up 28 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

默认的 php 镜像中不带有 mysqli 模块，我们需要给容器内的 php 安装 mysqli 模块：

> docker exec -it server-phpfpm /bin/bash

进入后输入：

> docker-php-ext-install mysqli

输出的内容比较多：
```
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking for MySQLi support... yes, shared
checking whether to enable embedded MySQLi support... no
checking for specified location of the MySQL UNIX socket... no
checking for MySQL UNIX socket location... no
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli.c -o mysqli.lo 
mkdir .libs
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli.c  -fPIC -DPIC -o .libs/mysqli.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_api.c -o mysqli_api.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_api.c  -fPIC -DPIC -o .libs/mysqli_api.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_prop.c -o mysqli_prop.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_prop.c  -fPIC -DPIC -o .libs/mysqli_prop.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_nonapi.c -o mysqli_nonapi.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_nonapi.c  -fPIC -DPIC -o .libs/mysqli_nonapi.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_fe.c -o mysqli_fe.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_fe.c  -fPIC -DPIC -o .libs/mysqli_fe.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_report.c -o mysqli_report.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_report.c  -fPIC -DPIC -o .libs/mysqli_report.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_driver.c -o mysqli_driver.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_driver.c  -fPIC -DPIC -o .libs/mysqli_driver.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_warning.c -o mysqli_warning.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_warning.c  -fPIC -DPIC -o .libs/mysqli_warning.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_exception.c -o mysqli_exception.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_exception.c  -fPIC -DPIC -o .libs/mysqli_exception.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c -o mysqli_result_iterator.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c  -fPIC -DPIC -o .libs/mysqli_result_iterator.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o mysqli.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/mysqli/modules  mysqli.lo mysqli_api.lo mysqli_prop.lo mysqli_nonapi.lo mysqli_fe.lo mysqli_report.lo mysqli_driver.lo mysqli_warning.lo mysqli_exception.lo mysqli_result_iterator.lo 
cc -shared  .libs/mysqli.o .libs/mysqli_api.o .libs/mysqli_prop.o .libs/mysqli_nonapi.o .libs/mysqli_fe.o .libs/mysqli_report.o .libs/mysqli_driver.o .libs/mysqli_warning.o .libs/mysqli_exception.o .libs/mysqli_result_iterator.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,mysqli.so -o .libs/mysqli.so
creating mysqli.la
(cd .libs && rm -f mysqli.la && ln -s ../mysqli.la mysqli.la)
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=install cp ./mysqli.la /usr/src/php/ext/mysqli/modules
cp ./.libs/mysqli.so /usr/src/php/ext/mysqli/modules/mysqli.so
cp ./.libs/mysqli.lai /usr/src/php/ext/mysqli/modules/mysqli.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/mysqli/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/mysqli/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/
/usr/local/bin/docker-php-ext-enable: 108: /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-mysqli.ini: Directory nonexistent
```

一般我们Yii用到的Pdo拓展也是缺失的，我们需要把Pdo拓展打开。不然会报错：
```
Fatal error: Uncaught PDOException: could not find driver in ...
```

先看一下phpinfo()输出的配置地址：
```
Configure Command 	'./configure' '--build=x86_64-linux-gnu' '--with-config-file-path=/usr/local/etc/php' '--with-config-file-scan-dir=/usr/local/etc/php/conf.d' '--enable-option-checking=fatal' '--with-mhash' '--enable-ftp' '--enable-mbstring' '--enable-mysqlnd' '--with-curl' '--with-libedit' '--with-openssl' '--with-zlib' '--with-libdir=lib/x86_64-linux-gnu' '--enable-fpm' '--with-fpm-user=www-data' '--with-fpm-group=www-data' '--disable-cgi' 'build_alias=x86_64-linux-gnu'
Server API 	FPM/FastCGI
Virtual Directory Support 	disabled
Configuration File (php.ini) Path 	/usr/local/etc/php
Loaded Configuration File 	(none)
Scan this dir for additional .ini files 	/usr/local/etc/php/conf.d
Additional .ini files parsed 	(none)
PHP API 	20160303
PHP Extension 	20160303
Zend Extension 	320160303
Zend Extension Build 	API320160303,NTS
PHP Extension Build 	API20160303,NTS
Debug Build 	no
Thread Safety 	disabled
Zend Signal Handling 	enabled
Zend Memory Manager 	enabled
Zend Multibyte Support 	provided by mbstring
IPv6 Support 	enabled
DTrace Support 	disabled
Registered PHP Streams	https, ftps, compress.zlib, php, file, glob, data, http, ftp, phar
Registered Stream Socket Transports	tcp, udp, unix, udg, ssl, tls, tlsv1.0, tlsv1.1, tlsv1.2
Registered Stream Filters	zlib.*, convert.iconv.*, string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, dechunk
```

看到配置拓展的地址在 `/usr/local/etc/php/conf.d` 下。

安装pdo_mysql拓展：

> docker-php-ext-install pdo_mysql

可以看到 `/usr/local/etc/php/conf.d` 这个目录下多了一个文件：docker-php-ext-pdo_mysql.ini

然后退出容器，回到宿主服务器：

> exit

重启server-phpfpm服务：

> docker stop server-phpfpm

> docker start server-phpfpm

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

把-v与容器挂载的目录和文件准备好：

```
mkdir -p /web/nginx/conf /web/nginx/conf/vhost /web/nginx/logs
```

创建容器并运行：
```
docker run \
  --name server-nginx \
  -p 80:80 \
  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm:php \
  -d nginx
```

说明： /web/nginx/conf/nginx.conf 是nginx配置文件，现在暂时还没有，只是把容器与宿主服务器的连接关系先写好。

有时返回结果可能会看到错误提示：
```
/usr/bin/docker-current: Error response from daemon: oci runtime error: container_linux.go:235: starting container process caused "container init exited prematurely".
```

这是因为`-v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf `这个实现不了，我们可以把这一个绑定取消掉
（`/etc/nginx/nginx.conf`里的加载关系是写好的，可以不用挂载修改），再执行：
```
docker run \
  --name server-nginx \
  -p 80:80 \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm:php \
  -d nginx
```

查看容器：

> docker ps -a

可以看到nginx正在Up运行状态中：

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
4f3bd8406cbc        nginx               "nginx -g 'daemon ..."   14 seconds ago      Up 13 seconds       0.0.0.0:80->80/tcp                  server-nginx
3a03eba032c0        php:7.1.30-fpm      "docker-php-entryp..."   12 minutes ago      Up 12 minutes       0.0.0.0:9000->9000/tcp              server-phpfpm
2bfff24639a3        mysql:5.7           "docker-entrypoint..."   40 minutes ago      Up 40 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

我们还需要配置实例安全组，外网才能访问服务器80端口。

在阿里云配置中心——云服务器 ECS——点击实例，在实例管理处点击左侧菜单的本实例安全组，可以看到右侧三个栏目：

`内网入方向全部规则 | 内网出方向全部规则 | 安全组列表`

点击 内网入方向全部规则，看到默认的内容：
```
授权策略	协议类型	端口范围	授权类型(全部)授权对象	描述	优先级
允许	自定义 TCP	22/22	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	全部 ICMP(IPv4)	-1/-1	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	自定义 TCP	3389/3389	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
```

我们需要再加一条入方向80端口的安全规则，加入后用浏览器访问我们实例的公网ip就可以看到nginx欢迎的界面了。

#### git安装

安装git后，我们项目代码就可以直接用git实现更新了，而不是用繁杂的ftp，总言之，就是方便：

```
yum install -y git
```

然后cd到目的目录下：
```
git clone 项目git地址
```

按照提示输入验证信息。

以后就只是拉取了：
```
git pull
```

#### web项目搭建

我们上面已经运行了nginx容器，但这个容器并无法调用php脚本服务，我们需要做相应配置。

把容器中的nginx.conf文件复制到服务器目录下：

> docker cp server-nginx:/etc/nginx/nginx.conf /web/nginx/conf/nginx.conf

nginx.conf 文件内容：
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

把容器中的 /etc/nginx/conf.d/dafault.conf 文件复制到服务器目录下：

> docker cp server-nginx:/etc/nginx/conf.d/dafault.conf /web/nginx/conf/vhost/

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

我们可以在服务器 /web/nginx/conf/vhost 目录下写域名项目配置文件：

test.com.conf文件内容：
```
server {
    listen       80;
    server_name  test.com www.test.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/test;
        index  index.html index.htm index.php;
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
    location ~ \.php$ {
        root           /var/www/html/test;
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

把我们上面运行的nginx容器停止运行

> docker stop server-nginx

然后重新运行，让我们修改的配置文件生效：

> docker start server-nginx

可以看出我们test项目的代码在宿主服务器文件夹 /var/www/html/test 下，我们把项目内容发布到这个文件夹下就可以了。
然后我们就可以用 test.com 访问项目了（这里省略 域名/服务器配置相关内容）。 

同样，其他项目我们也可以像test项目这样部署，共用nginx和php容器。

#### 附录

我们打印phpinfo信息看一下：

![]({{site.baseurl}}/images/20200602/20200602154735.png)

![]({{site.baseurl}}/images/20200602/20200602154737.png)

![]({{site.baseurl}}/images/20200602/20200602154739.png)

![]({{site.baseurl}}/images/20200602/20200602154741.png)

```
docker run --name server-mysql  -p 3306:3306  -e MYSQL_ROOT_PASSWORD=abc$123* -v /web/mysql/data:/var/lib/mysql -v /web/mysql/conf.d:/etc/mysql/conf.d -v /web/mysql/logs:/logs  -v /etc/localtime:/etc/localtime:ro -d mysql:5.7

docker run --name server-phpfpm -p 9000:9000 -v /web/php-fpm/etc:/usr/local/etc/php -v /var/www/html:/var/www/html -v /etc/localtime:/etc/localtime:ro -d php:7.1.30-fpm

docker run --name server-nginx -p 80:80  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf  -v /web/nginx/conf/vhost:/etc/nginx/conf.d  -v /web/nginx/logs:/var/log/nginx  -v /var/www/html:/usr/share/nginx/html  -v /etc/localtime:/etc/localtime:ro  --link server-phpfpm:php  -d nginx
```

<br/><br/><br/><br/><br/>
### 参考资料

阿里云ECS服务器安装docker <https://www.cnblogs.com/one-reader/p/11406047.html>

阿里云ECS-yum 安装docker <https://blog.csdn.net/wxb880114/article/details/82219701>

服务器部署docker lnmp环境 <https://www.cnblogs.com/cxscode/p/11070880.html>

linux记录-docker配置mysql <https://www.cnblogs.com/xinfang520/p/11122638.html>

阿里云ECS云服务器详细教程CentOS 7 <https://blog.csdn.net/qq_41399901/article/details/84953155>

Docker 部署lnmp <https://blog.csdn.net/weixin_42890981/article/details/86749240>

docker的安装部署及使用docker安装mysql+php+nginx教程 <https://boke112.com/6402.html>

MySQL创建用户与授权 <https://blog.csdn.net/u011120248/article/details/79983250>

centos mysql允许远程root登录 <https://www.cnblogs.com/miaoshiqian/p/3760818.html>

MYSQL的创建用户，授权用户，删除用户，查看用户 <https://blog.csdn.net/u014453898/article/details/55064312>

