---
layout: post
categories: Docker
title: 浅述 Docker 使用 Compose 容器编排
meta: 浅述 Docker 使用 Compose 容器编排
---
* content
{:toc}

## 正文

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。
然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

Compose 使用的三个步骤：
1. 使用 Dockerfile 定义应用程序的环境。
2. 使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。
3. 最后，执行 `docker-compose up` 命令来启动并运行整个应用程序。

### 安装

Linux 上我们可以从 Github 上下载它的二进制包来使用，最新发行的版本地址：<https://github.com/docker/compose/releases>。

运行以下命令以下载 Docker Compose 的当前稳定版本：
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

要安装其他版本的 Compose，请替换 v2.2.2。

Docker Compose 存放在 GitHub，不太稳定。你可以也通过执行下面的命令，高速安装 Docker Compose。
```
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.4.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

将可执行权限应用于二进制文件：
> sudo chmod +x /usr/local/bin/docker-compose

创建软链：
> sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

测试是否安装成功：
```
$ docker-compose version
cker-compose version 1.24.1, build 4667896b
```

### 使用




### 示例

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

在目录中，执行以下命令来启动应用程序：
> docker-compose up

如果你想在后台执行该服务可以加上 `-d` 参数：
> docker-compose up -d

### yml 配置指令参考



## 集合

更多 Compose 实例，参见 <https://github.com/jianyan74/dockerfiles> ，看一下这里面相关Compose的使用。
里面的涉及到的应用有：Elasticsearch、filebeat、Kafka、Kibana、Logstash、metricbeat、mongo、Mysql、Nginx、PHP、RabbitMQ、Redis、zabbix 等。
另外这里面的各个应用的说明文档也整理的挺好。

<br/><br/><br/><br/><br/>
## 参考资料

Docker Compose Runoob <https://www.runoob.com/docker/docker-compose.html>

docker之Dockerfile实践 集合一 <https://ibaiyang.github.io/blog/docker/2020/05/22/docker之Dockerfile实践.html#集合一>

YAML 入门教程 <https://www.runoob.com/w3cnote/yaml-intro.html>

