---
layout: post
categories: Linux
title: Linux docker安装配置
meta: Linux docker安装配置
---
* content
{:toc}

### 正文

在未使用虚拟化技术的机子上，长期使用后，上面的软件和版本就变得乱七八器，很混乱和庞杂，比如一个机子上可能既有PHP7.1、又有PHP7.0、还有PHP5.6，
其他软件也一样，而且由于Linux上安装方式的多样性，既有源码编译安装，又有apt、rpm、yum、dpkg等包管理工具安装，
导致PHP7.1是编译安装的、PHP7.0是apt方式安装的、PHP5.6又是dpkg方式安装的等等，最后自己也记不清哪个软件的哪个版本是通过哪种方式安装的了，
卸载都不知道通过什么方式卸载，看着这一摊子不头大才怪：如果一个机子上面每个软件版本都有自己的一个容器，不想要这个版本了，直接把容器删了就好了。

这就是虚拟化技术。有一种传统的方式是虚拟机。虚拟机，类似于“子电脑”，占用空间大、启动慢，一般要几GB到几十GB的空间。
还有一种新的虚拟化技术就是Docker这样的容器技术，属于轻量级的虚拟化，只需要MB级甚至KB级的空间，启动时间很快，几秒钟就能完成。

所有我们选中的就是Docker技术。

#### Docker信息查看

我们在《趣谈网络协议》的TCP/IP实验环境的搭建中安装过Docker，另外有些Linux发行版本可能集成了Docker，我们可以查看下Docker的基本信息。

我们电脑上可能已经安装好了Docker，我们查看一下版本：
> docker -v

输出：
```
Docker version 19.03.5, build 633a0ea838
```

也可以通过下面这条命令查看详情：
> docker version

输出：
```
Client: Docker Engine - Community
 Version:           19.03.5
 API version:       1.40
 Go version:        go1.12.12
 Git commit:        633a0ea838
 Built:             Wed Nov 13 07:25:58 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.5
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.12
  Git commit:       633a0ea838
  Built:            Wed Nov 13 07:24:29 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.10
  GitCommit:        b34a5c8af56e510852c35414db4c1f4fa6172339
 runc:
  Version:          1.0.0-rc8+dev
  GitCommit:        3e425f80a8c931f88e6d94a8c831b9d5aa481657
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

查看镜像有哪些：
> docker images

输出：
```
REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
hub.c.163.com/liuchao110119163/ubuntu   tcpip               396485da9bdd        21 months ago       345MB
```

#### 安装Docker引擎

Docker 的旧版本被称为 docker，docker.io 或 docker-engine 。如果已安装，请卸载它们:
> sudo apt-get remove docker docker-engine docker.io containerd runc

更新 apt 包索引:
> sudo apt-get update

安装 apt 依赖包，用于通过HTTPS来获取仓库:
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

添加 Docker 的官方 GPG 密钥:
```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

可以看出我们是在debian系上安装，如果要在ubuntu上安装，可用：
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

通过搜索指纹的最后8个字符，验证您现在拥有指纹9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88的密钥:
> sudo apt-key fingerprint 0EBFCD88

输出：
```
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

我们设置docker稳定版储存库：
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
```

上面把软件储存库设置好了，下面我们进行安装。

更新 apt 包索引：
> sudo apt-get update

列出可用版本：
> apt-cache madison docker-ce

输出：
```
 docker-ce | 5:19.03.8~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.7~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.6~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.5~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.4~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.3~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.2~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.1~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:19.03.0~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.9~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.8~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.7~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.6~3-0~debian-stretch | http://packages.deepin.com/deepin lion/non-free amd64 Packages
 docker-ce | 5:18.09.6~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.5~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.4~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.3~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.2~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.1~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 5:18.09.0~3-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.06.3~ce~3-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.06.2~ce~3-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.06.1~ce~3-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.06.0~ce~3-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.03.1~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 18.03.0~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.12.1~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.12.0~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.09.1~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.09.0~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.06.2~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.06.1~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.06.0~ce-0~debian | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.03.3~ce-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.03.2~ce-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.03.1~ce-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
 docker-ce | 17.03.0~ce-0~debian-stretch | https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch/stable amd64 Packages
```

我们可以安装上面看到的指定版本， 例如 5:19.03.8~3-0~debian-stretch 版：
> sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

`<VERSION_STRING>`, 替换为 5:19.03.8~3-0~debian-stretch 。

或者我们可以通过下面的命令直接安装最新版，不过有可能不是稳定版：
> sudo apt-get install docker-ce docker-ce-cli containerd.io

通过以上步骤，Docker就安装好了。

Docker是虚拟化容器引擎，我们可以运行hello world映像，验证Docker引擎是否正确安装：
> sudo docker run hello-world

输出：
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:6a65f928fb91fcfbc963f7aa6d57c8eeb426ad9a20c7ee045538ef34847f44f1
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

此命令下载测试映像并在容器中运行它。当容器运行时，它会打印一条信息性消息并退出。

Docker引擎已安装并运行。docker组已创建，但未添加任何用户。你需要用sudo来运行Docker命令。
继续Linux postinstall以允许非特权用户运行Docker命令和其他可选配置步骤。

#### 示例配置

```
cat > docker-php-entrypoint << EOF
#!/bin/sh
nginx -g 'daemon off;' &
php-fpm
EOF
chmod +x docker-php-entrypoint
cat > Dockerfile << EOF
#FROM vipcloud/vipcloud-php-fpm-v2:7.1.24
FROM vipcloud/vipcloud-php-fpm-apcu:7.1.24
RUN mkdir /var/www/html/ -pv
RUN mkdir /config/dev/ -pv
COPY --chown=nginx:nginx VIPSales.json /config/dev/
COPY --chown=nginx:nginx dist/VIPSales.${BUILD_NUMBER}.${BUILD_ID}.tar.gz /var/www/html/
WORKDIR /var/www/html/
RUN /bin/sh -xe \
&& tar -xf ./VIPSales.${BUILD_NUMBER}.${BUILD_ID}.tar.gz && rm -f ./VIPSales.${BUILD_NUMBER}.${BUILD_ID}.tar.gz \
&& cd /var/www/html/ \
&& mkdir -p runtime \
&& mkdir -p web/assets \
&& mkdir -p web/tmp \
&& chown -R nginx:nginx runtime \
&& chown -R nginx:nginx web/assets \
&& chown -R nginx:nginx web/tmp \
&& chmod -R 0777 runtime \
&& chmod -R 0777 web/assets \
&& chmod -R 0777 web/tmp \

COPY docker-php-entrypoint /usr/local/bin/
EXPOSE 80
ENTRYPOINT ["docker-php-entrypoint"]
EOF
```


```

FROM centos:7

MAINTAINER www  qing@iw16.com

ENV PHPIZE_DEPS \
		autoconf \
		file \
		g++ \
		gcc \


RUN apk add --no-cache --virtual .persistent-deps \
		ca-certificates \
		curl \
		tar \
		xz \
# https://github.com/docker-library/php/issues/494
		libressl
		
ENV PHP_INI_DIR /usr/local/etc/php
RUN mkdir -p $PHP_INI_DIR/conf.d

ENV PHP_EXTRA_CONFIGURE_ARGS --enable-fpm --disable-cgi --enable-bcmath --enable-sockets --enable-exif --enable-zip --with-gd --with-jpeg-dir --with-png-dir --with-freetype-dir --with-webp-dir --with-xpm-dir  --with-gettext --enable-intl --with-mcrypt --with-mysqli --enable-pcntl --with-pdo-mysql --enable-shmop -enable-soap --enable-sysvsem --with-xmlrpc --with-xs

ENV PHP_VERSION 7.1.23
ENV PHP_URL="https://secure.php.net/get/php-7.1.23.tar.xz/from/this/mirror" PHP_ASC_URL="https://secure.php.net/get/php-7.1.23.tar.xz.asc/from/this/mirror"

RUN mkdir -p /usr/src; \
	cd /usr/src; \
	\
	wget -O php.tar.xz "$PHP_URL";
RUN	wget -O /usr/src/php.tar.xz  https://secure.php.net/get/php-7.1.21.tar.xz/from/this/mirror && rm -rf /usr/src/php && mkdir /usr/src/php && tar -Jxf /usr/src/php.tar.xz -C /usr/src/php --strip-components=1 \

RUN  ./configure   --prefix=/usr/local/php --with-pdo-mysql --disable-cgi  -with-mysqli  -enable-ftp  -enable-zip  -with-bz2  -with-jpeg-dir   -with-png-dir    -with-freetype-dir    -with-libxml-dir  -with-xmlrpc    -with-zlib-dir   -with-gd  -with-curl   -with-gettext    -with-pear  -enable-mbstring -enable-bcmath   -enable-sockets   -enable-exif  -enable-fpm  -enable-pcntl   -with-mhash  -with-gmp    -with-openssl   -enable-sysvsem   -enable-sysvshm -enable-mbregex --enable-intl --with-mcrypt --enable-shmop



##########
yum install libxml2-devel openssl-devel bzip2-devel curl-devel libjpeg-devel libpng-devel freetype-devel gmp-devel -y 

freetype             

krb5-devel keyutils-libs-devel libcom_err-devel libselinux-devel  libsepol-devel libverto-devel xz-devel pcre-devel zlib-devel      
   
libjpeg-turbo       
libkadm5            
libpng              
          

yum install gcc-c++ -y
yum install libicu-devel -y

yum install systemtap-sdt-devel enchant-devel xpm-devel libXpm-devel libc-client-devel openldap-devel libmcrypt-devel unixODBC-devel -y

yum install make -y


yum autoremove systemtap-sdt-devel enchant-devel xpm-devel libXpm-devel libc-client-devel openldap-devel libmcrypt-devel unixODBC-devel
```

<br/><br/><br/><br/><br/>
### 参考资料

Install Docker Engine on Debian <https://docs.docker.com/install/linux/docker-ce/debian/>

Deepin 中的 Docker <https://wiki.deepin.org/wiki/Docker>

干货满满！10分钟看懂Docker和K8S <https://my.oschina.net/jamesview/blog/2994112>

搭建TCP-IP实验环境 <https://ibaiyang.github.io/blog/it%E6%8A%80%E6%9C%AF/2019/12/03/%E6%90%AD%E5%BB%BATCP-IP%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83.html>

Linux和Docker常用命令 <https://www.cnblogs.com/mq0036/p/8520605.html>

