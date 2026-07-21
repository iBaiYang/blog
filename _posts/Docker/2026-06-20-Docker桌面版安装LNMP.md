---
layout: post
categories: Docker
title: Docker桌面版安装LNMP
meta: Docker桌面版安装LNMP
---
* content
{:toc}

## 引言

一直在windows中开发，为了便捷开发，不用搭建虚拟机等，可以使用桌面版docker解决这个问题。下面是多项目开发使用的LNMP环境。

## 正文

### 条件准备

正常从 Docker <https://www.docker.com/> 官网下载安装即可。

修改为国内源加速，在docker desktop的设置中setting配置即可，Docker Engine中写入下面内容，然后Apply应用并重启Docker Desktop：
```
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "registry-mirrors": [
    "https://docker.1ms.run",
    "https://dockerproxy.com",
    "https://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

需要说明 `docker config set --global registry-mirrors`命令无效，不可用。

项目的目录结构如下：
```
D:\develop
├── DockerEnv
    ├── docker-multi-web
        ├── dockerfile                # PHP+Nginx基础镜像构建文件
        ├── nginx
            ├── nginx.conf            # Nginx主配置
            └── vhost                 # 多站点虚拟主机配置目录
                ├── site1.conf        # 站点1配置
                └── site2.conf        # 站点2配置
    ├── mysql-server
        ├── mysql-data                # MySQL持久化数据
├── www
    ├── site1                 # 项目1：test1.local
    │   └── index.php
    └── site2                 # 项目2：test2.local
        └── index.php
```

搭建网桥`docker network create web-net`，如：
```
D:\develop\DockerEnv>docker network create web-net
2b27ad41e8489e7765f93e2fec27eb9321cf88e0d65969f438f7d3ea844d8de6

D:\develop\DockerEnv>
```


### 安装MySQL：

MySQL 不要放进自定义 Image。如果把 MySQL 打包进镜像，删除容器会丢失数据库数据，无法持久化；分离容器更灵活，可单独重启、备份、更换 MySQL 版本。

Docker Hub 官方已经提供好现成的 mysql:8.0 官方镜像，直接 docker run 拉取启动即可，不需要自己打包 MySQL。

启动MySQL容器：
```
docker run -d --name mysql8 --network web-net -v D:/develop/DockerEnv/mysql-server/mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=testdb -e MYSQL_ROOT_HOST=% -p 3306:3306 mysql:8.0
```

加上 `MYSQL_ROOT_HOST=%`，否则外部 Windows、PHP 容器无法连接数据库。`-e MYSQL_ROOT_HOST=%` 允许 PHP 容器、本机 Navicat 全部访问，本地开发必备。

运行完成后在Docker Desktop中查看MySQL容器是否启动成功，Container、Image等。

外部连接时，连接地址不要使用 127.0.0.1，而要使用 mysql8 。

### Nginx 配置

nginx.conf 内容：
```
user www-data;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;
    sendfile on;
    keepalive_timeout 65;
    # 加载所有站点虚拟主机配置
    include /etc/nginx/conf.d/*.conf;
}
```

### 项目配置

下面看几个项目的配置。

#### 项目一

site1.conf 内容：
```
server {
    listen 80;
    server_name test1.local www.test1.local;
    root /usr/share/nginx/www/site1;
    index index.html index.htm index.php;

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

www/site1/index.php 内容：
```php
<?php

echo "<h2>站点1 test1.local</h2>";
phpinfo();
```

#### 项目二

site2.conf 内容：
```
server {
    listen 80;
    server_name test2.local www.test2.local;
    root /usr/share/nginx/www/site2;
    index index.html index.htm index.php;

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

www/site2/index.php 内容：
```php
<?php

echo "<h2>站点2 test2.local</h2>";
// 测试mysql连接
$dsn = 'mysql:host=mysql8;dbname=testdb;charset=utf8';
try {
    $db = new PDO($dsn, 'root', '123456');
    echo "<p>MySQL连接成功</p>";
}catch (PDOException $e){
    echo "<p>数据库连接失败：".$e->getMessage()."</p>";
}
```

www/site2/index.php 内容：
```php
<?php
    echo "hello world";
?>
```

#### hosts 配置

在宿主机的 C:\Windows\System32\drivers\etc\hosts 文件中添加：
```
127.0.0.1 test1.local
127.0.0.1 test2.local
```

如果提示无权限修改，可以用管理员权限打开记事本，然后用记事本打开该文件进行修改并保存。

### 安装 Nginx + PHP

接下来安装Nginx+PHP。

dockerfile 内容：
```
FROM quay.io/centos/centos:stream9-minimal

# 1. 替换 CentOS 基础源为阿里云
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos*.repo && \
    microdnf clean all && microdnf makecache

# 2. 强制移除 curl-minimal，再安装 dnf 和 curl
RUN rpm -e --nodeps curl-minimal && \
    microdnf install -y dnf curl && \
    microdnf clean all

# 3. 安装 epel-release 并替换为阿里云 EPEL 镜像
RUN dnf install -y epel-release && \
    sed -e 's|https://download.fedoraproject.org/pub|https://mirrors.aliyun.com/fedora-epel|g' \
        -i /etc/yum.repos.d/epel*.repo && \
    dnf clean all && dnf makecache

# 4. 安装 Remi 源并替换为清华镜像（国内唯一完整同步Remi）
RUN dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm && \
    sed -e 's|https://rpms.remirepo.net|https://mirrors.tuna.tsinghua.edu.cn/remi|g' \
        -i /etc/yum.repos.d/remi*.repo && \
    dnf clean all && dnf makecache

# 5. 安装 Nginx + 启用 php:remi-7.4 模块并安装全部组件 + 系统底层时区库
RUN dnf module enable -y php:remi-7.4 && \
    dnf install -y \
        nginx \
        php-fpm \
        php-cli \
        php-mysqlnd \
        php-pdo \
        php-gd \
        php-zip \
        php-curl \
        php-mbstring \
        php-opcache \
        php-xml \
        php-redis \
        vim unzip && \
    dnf clean all

# 6.安装 tzdata 并重装确保时区数据文件存在
RUN dnf install -y tzdata && \
    dnf reinstall -y tzdata

# 7. 新增创建www-data用户组和用户
RUN groupadd www-data && useradd -g www-data www-data

# 8. 修改 php-fpm 运行用户为 www-data，解决页面403权限
RUN sed -i 's/^user = apache/user = www-data/' /etc/php-fpm.d/www.conf && \
    sed -i 's/^group = apache/group = www-data/' /etc/php-fpm.d/www.conf && \
    sed -i 's/^listen = .*/listen = 127.0.0.1:9000/' /etc/php-fpm.d/www.conf

# 9. 统一设置系统时区+PHP时区配置，彻底消除时区报错
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo 'date.timezone = Asia/Shanghai' > /etc/php.d/99-timezone.ini

# 10. 安装 Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# 11. 手动创建 Composer 全局配置文件（阿里云镜像）
RUN mkdir -p /root/.composer && \
    echo '{"config": {"repositories": {"packagist": {"type": "composer", "url": "https://mirrors.aliyun.com/composer/"}}}}' > /root/.composer/config.json

# 12. 复制本地Nginx主配置 + 多站点虚拟主机目录
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf

# 13. 创建站点根目录并授权www-data读写权限
RUN mkdir -p /usr/share/nginx/www && \
    chown -R www-data:www-data /usr/share/nginx/www


EXPOSE 80

# 启动php-fpm + 前台运行nginx，防止容器自动退出
CMD ["sh", "-c", "mkdir -p /run/php-fpm && chown www-data:www-data /run/php-fpm && php-fpm -F & nginx -g 'daemon off;'"]
```

这一块用了好长时间，说一下里面的注意点。基础镜像用的是centos:stream9精简版，为了国内加速，CentOS 基础源换为了阿里云。里面多处安装用到了dnf，但基础镜像 quay.io/centos/centos:stream9-minimal 默认的仓库配置中，没有可用的 dnf 包（尽管 microdnf 是内置的，但microdnf 本身是轻量级包管理器，好多包的安装还是要用到dnf），有些地方也要用到curl（尽管 curl-minimal 是内置的，但有些下载是要用到curl的，而curl 包与 curl-minimal 冲突），我们需要强制移除 curl-minimal，再安装 dnf 和 curl。还有centos:stream9精简版底层是缺少系统底层时区库，安装Composer会用到。Composer 全局配置文件改为阿里云，命令行总是无法执行，无奈改为手动创建。php-fpm 一般是用socket实现，绑定地址 /run/php-fpm/www.sock，这是一个临时目录，在容器启动时需要创建该目录；但这里改下来有问题，最后改为了 127.0.0.1:9000 端口监听。`php-fpm -F`前台运行会阻塞后面`nginx -g 'daemon off;'`的执行，所以不能用`&&`，而是改为`&`。

centos:stream9-minimal 为精简镜像，有意删除了 /usr/share/zoneinfo，导致tzdata安装不完整，需要重新安装`dnf reinstall -y tzdata`。之后 第10步 可以修改看一下能否正常安装：
```
# 11. 设置 Composer 全局为阿里云镜像
php -d date.timezone=Asia/Shanghai composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

生成镜像：
> docker build -t centos9-nginx-php74 .

创建容器：
```
docker run -d --name centos9-nginx-php74 --network web-net -v D:/develop/www:/usr/share/nginx/www -v D:/develop/DockerEnv/docker-multi-web/nginx/vhost:/etc/nginx/conf.d -p 8080:80 centos9-nginx-php74
```

浏览器访问，查看效果：
- 站点 1：http://test1.local:8080
- 站点 2：http://test2.local:8080

### 新项目配置

和第二个项目一样，除了项目文件，另外需要写site3.conf配置，修改hosts文件。

重启容器：
> docker restart centos9-nginx-php74


## PHP8.3 安装

dockerfile 内容：
```
FROM quay.io/centos/centos:stream9

# 1. 替换 CentOS 基础源为阿里云
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos*.repo && \
    microdnf clean all && microdnf makecache

# 2. 强制移除 curl-minimal，再安装 dnf 和 curl
RUN rpm -e --nodeps curl-minimal && \
    microdnf install -y dnf curl && \
    microdnf clean all

# 3. 安装 epel-release 并替换为阿里云 EPEL 镜像
RUN dnf install -y epel-release && \
    sed -e 's|https://download.fedoraproject.org/pub|https://mirrors.aliyun.com/fedora-epel|g' \
        -i /etc/yum.repos.d/epel*.repo && \
    dnf clean all && dnf makecache

# 4. 安装 Remi 源并替换为清华镜像（国内唯一完整同步Remi）
RUN dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm && \
    sed -e 's|https://rpms.remirepo.net|https://mirrors.tuna.tsinghua.edu.cn/remi|g' \
        -i /etc/yum.repos.d/remi*.repo && \
    dnf clean all && dnf makecache

# 5. 安装 Nginx + 启用 php:remi-7.4 模块并安装全部组件 + 系统底层时区库
RUN dnf module enable -y php:remi-7.4 && \
    dnf install -y \
        nginx \
        git \
        php-fpm \
        php-cli \
        php-mysqlnd \
        php-pdo \
        php-gd \
        php-zip \
        php-curl \
        php-mbstring \
        php-opcache \
        php-xml \
        vim unzip && \
    dnf clean all

# 6.安装 tzdata 并重装确保时区数据文件存在
RUN dnf install -y tzdata && \
    dnf reinstall -y tzdata

# 7. 新增创建www-data用户组和用户
RUN groupadd www-data && useradd -g www-data www-data

# 8. 修改 php-fpm 运行用户为 www-data，解决页面403权限
RUN sed -i 's/^user = apache/user = www-data/' /etc/php-fpm.d/www.conf && \
    sed -i 's/^group = apache/group = www-data/' /etc/php-fpm.d/www.conf && \
    sed -i 's/^listen = .*/listen = 127.0.0.1:9000/' /etc/php-fpm.d/www.conf

# 9. 统一设置系统时区+PHP时区配置，彻底消除时区报错
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo 'date.timezone = Asia/Shanghai' > /etc/php.d/99-timezone.ini

# 10. 安装 Composer
RUN curl -sS https://mirrors.aliyun.com/composer/composer.phar | php -- --install-dir=/usr/local/bin --filename=composer

# 11. 手动创建 Composer 全局配置文件（阿里云镜像）
RUN mkdir -p /root/.composer && \
    echo '{"config": {"repositories": {"packagist": {"type": "composer", "url": "https://mirrors.aliyun.com/composer/"}}}}' > /root/.composer/config.json

# 12. 复制本地Nginx主配置 + 多站点虚拟主机目录
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf

# 13. 创建站点根目录并授权www-data读写权限
RUN mkdir -p /usr/share/nginx/www && \
    chown -R www-data:www-data /usr/share/nginx/www


EXPOSE 80

# 启动php-fpm + 前台运行nginx，防止容器自动退出
CMD ["sh", "-c", "mkdir -p /run/php-fpm && chown www-data:www-data /run/php-fpm && php-fpm -F & nginx -g 'daemon off;'"]
```

centos:stream9-minimal 为精简镜像，缺少好多东西，我们直接用centos:stream9，需要时我们也可以把git安装上。

生成镜像：
> docker build -t centos9-nginx-php83 .

创建容器：
```
docker run -d --name centos9-nginx-php83 --network web-net -v D:/develop/www:/usr/share/nginx/www -v D:/develop/DockerEnv/docker-multi-web/nginx/vhost:/etc/nginx/conf.d -p 8081:80 centos9-nginx-php83
```
