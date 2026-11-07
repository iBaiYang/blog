---
layout: post
categories: Docker
title: Docker桌面版Compose安装LNMP
meta: Docker桌面版Compose安装LNMP
---
* content
{:toc}

## 引言

前面Docker桌面版安装过一次LNMP环境，但 PHP 和 Nginx 是放在一个容器中的，这里分成两个容器进行安装和运行。而且原来项目目录是映射到 Windows 的 C 盘挂载目录 `/mnt/c/xxx`，现在为了提速，要把文件挂载在 WSL 内部路径。前文地址 <https://ibaiyang.github.io/blog/docker/2026/06/20/Docker桌面版安装LNMP.html/>

## 正文

这里使用 Docker Compose 安装 LNMP 环境。PHP 需要编译安装，速度会很慢。

## Compose 编排

前面安装好了 MySQL 和 Redis，下面按照：官方镜像 + 职责分离 + Compose 编排 + 复用已有网络， 来安装 php:8.5-fpm 和 nginx 两个独立容器，不自己拼 LNMP 大镜像。PHP 升级只换 tag，Nginx 调参只动配置，互不影响。

先看一下环境：
```
C:\Users\Administrator>wsl -l -v
NAME              STATE           VERSION

- docker-desktop    Running         2

C:\Users\Administrator>
C:\Users\Administrator>docker ps
CONTAINER ID   IMAGE                 COMMAND                   CREATED        STATUS      PORTS                                         NAMES
a30b1a6ccf85   centos9-nginx-php74   "sh -c 'mkdir -p /ru…"   2 months ago   Up 9 days   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp       centos9-nginx-php74
d5046d1e795e   redis:7.4             "docker-entrypoint.s…"   2 months ago   Up 9 days   0.0.0.0:6379->6379/tcp, [::]:6379->6379/tcp   redis74
2009b3504497   mysql:8.0             "docker-entrypoint.s…"   2 months ago   Up 9 days   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp   mysql8

C:\Users\Administrator>
C:\Users\Administrator>docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
835013813fdf   bridge    bridge    local
32c352e4a5ca   host      host      local
0671617bb89a   none      null      local
e9ba8523abf9   web-net   bridge    local

C:\Users\Administrator>
C:\Users\Administrator>docker network inspect web-net
[
    {
        "Name": "web-net",
        "Id": "e9ba8523abf9e6dabf39a46e3eef7505af94ce5b74f45cd01fb8db12c9e6dd3c",
        "Created": "2026-08-08T16:17:21.675273113Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Options": {
            "com.docker.network.enable_ipv4": "true",
            "com.docker.network.enable_ipv6": "false"
        },
        "Labels": {},
        "Containers": {
            "2009b35044971280f3944e05dd912fa404f21a469163511fc8b4ddc48cb20fa1": {
                "Name": "mysql8",
                "EndpointID": "faedafb85b2938b8334c182c053290b5c1e2eb2be2da6ccfc777b021be38c71c",
                "MacAddress": "22:10:d7:e4:0b:6b",
                "IPv4Address": "172.18.0.4/16",
                "IPv6Address": ""
            },
            "a30b1a6ccf85b51acb6dc0cf3e82c69d406b0bff15edede48cee4b153a3565ec": {
                "Name": "centos9-nginx-php74",
                "EndpointID": "3c57656f1de8fb77de983a492be80382c898c51dc3dc8dcf979ddc4deccaa57a",
                "MacAddress": "f2:34:9a:d6:7e:c7",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "d5046d1e795e62948c2c13b215925b6a530ad373f7f4a2501dc5c814064e099f": {
                "Name": "redis74",
                "EndpointID": "0b36d099a93cc71ab4b0caa8c0b53418b2f819ab14a20b690573be9c3e32a9de",
                "MacAddress": "8a:db:e3:94:65:2c",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
        "Status": {
            "IPAM": {
                "Subnets": {
                    "172.18.0.0/16": {
                        "IPsInUse": 6,
                        "DynamicIPsAvailable": 65530
                    }
                }
            }
        }
    }
]

C:\Users\Administrator>
C:\Users\Administrator>docker network inspect web-net --format "{{range .Containers}}{{.Name}} {{.IPv4Address}}{{println}}{{end}}"
mysql8 172.18.0.4/16
centos9-nginx-php74 172.18.0.2/16
redis74 172.18.0.3/16


C:\Users\Administrator>
C:\Users\Administrator>wsl -d docker-desktop
docker-desktop:/tmp/docker-desktop-root/run/desktop/mnt/host/c/Users/Administrator#
docker-desktop:/tmp/docker-desktop-root/run/desktop/mnt/host/c/Users/Administrator# pwd
/tmp/docker-desktop-root/run/desktop/mnt/host/c/Users/Administrator
docker-desktop:/tmp/docker-desktop-root/run/desktop/mnt/host/c/Users/Administrator#
docker-desktop:/tmp/docker-desktop-root/run/desktop/mnt/host/c/Users/Administrator# cd ~
docker-desktop:~#
docker-desktop:~# pwd
/root
docker-desktop:~#
docker-desktop:~# ll /home
-sh: ll: not found
docker-desktop:~#
docker-desktop:~# ls /home
docker-desktop:~#
docker-desktop:~# cat /etc/passwd
root:x:0:0:root:/root:/bin/sh
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
docker-desktop:~#
```

可以发现：
1. docker-desktop 是 **Alpine 迷你容器式 WSL 发行版**
2. 用户是 `root`，家目录：`/root`
3. shell 是 sh
4. 已有 docker 网络：`web-net`，我们继续复用它

另外：Windows 资源管理器 `\\wsl.localhost\docker-desktop` ，对应 WSL 内 `/` 根目录。

### 安装 PHP8.5 与 Ngnix

我们在 `root` 目录下创建 `lnmp-web` 目录，用于存放 LNMP 相关文件。下面是具体的目录结构：
```
/root/lnmp-web/
├── docker-compose.yml
├── php
│   ├── Dockerfile
│   └── conf.d
│       └── 99-custom.ini
├── nginx
│   ├── conf.d
│   │   └── catch.conf
│   └── logs
└── www
    └── catchadmin  # 示例：CatchAdmin项目代码
```

如果用户不是 root，可以在家目录下创建目录，如 `/home/sam/lnmp-web/`。

catchadmin 是一个基于 Laravel-12 的PHP项目，不要把代码放 Windows 的 C 盘挂载目录 `/mnt/c/xxx`，不然 IO 会很慢，全部放在 WSL 内部路径。

先把各文件内容写好，再进行安装。

/root/lnmp-web/php/Dockerfile 文件内容：
```
FROM php:8.5-fpm

RUN apt-get update && apt-get install -y --no-install-recommends \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    tzdata \
    git \
    unzip \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-freetype --with-jpeg

# 安装 PHP 扩展，安装后默认是开启的
RUN docker-php-ext-install -j$(nproc) \
    gd \
    intl pdo_mysql \
    zip

# bcmath、ctype、fileinfo、mbstring 原生自带并默认开启

# 开启内置扩展（opcache 原生自带，是Zend 内核的内置优化模块）（不推荐 enable，因为内置扩展用 enable 会报错）
# RUN docker-php-ext-enable opcache

# 也可以在 PHP 配置文件中开启，直接在99-custom.ini中写入配置也可以，不过这里需要注释掉
# RUN echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/99-custom.ini
#     && echo "opcache.memory_consumption=128" >> /usr/local/etc/php/conf.d/99-custom.ini \
#     && echo "opcache.max_accelerated_files=10000" >> /usr/local/etc/php/conf.d/99-custom.ini \
#     && echo "opcache.revalidate_freq=60" >> /usr/local/etc/php/conf.d/99-custom.ini

# 安装 Redis 扩展（pecl 是 PHP 的扩展包管理器，选固定版本，太新PHP可能会不支持）
RUN pecl install redis-7.4.10

# 启用 Redis 扩展
RUN docker-php-ext-enable redis

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY conf.d/*.ini /usr/local/etc/php/conf.d/

WORKDIR /var/www/html
```

/root/lnmp-web/php/conf.d/99-custom.ini 文件内容：
```
date.timezone = Asia/Shanghai
memory_limit = 256M
upload_max_filesize = 20M
post_max_size = 20M
max_execution_time = 120

opcache.enable=1
opcache.enable_cli=0
opcache.memory_consumption=128
opcache.max_accelerated_files=15000
opcache.max_wasted_percentage=10
opcache.revalidate_freq=60
opcache.validate_timestamps=1
```

这里已经有了 opcache 的配置，重复加载，后面的覆盖前面的，属于冗余，可以把上面 `RUN echo "opcache.enable=1"` 这一大段注释掉。

/root/lnmp-web/docker-compose.yml 文件内容（核心，包含 nginx 服务定义）：
```
services:
  php85:
    build: ./php
    container_name: php85
    restart: unless-stopped
    networks:
      - web-net
    volumes:
      - ./www:/var/www/html
      - ./php/conf.d:/usr/local/etc/php/conf.d
    environment:
      TZ: Asia/Shanghai

    # 如果有别的服务器需要外网/局域网直连这个php-fpm才加下面这行，否则不要加
    # ports:
    #   - "9001:9000"

  nginx:
    image: nginx:stable
    container_name: nginx
    restart: unless-stopped
    ports:
      # - "80:80"
      # - "443:443"
      # 如果上面端口已被占用，这里直接写 8081:80，代表监听宿主机所有网卡（0.0.0.0），局域网、外网都能访问
      - "8081:80"
      - "8444:443"
    networks:
      - web-net
    volumes:
      - ./www:/var/www/html
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/logs:/var/log/nginx
    depends_on:
      - php85

networks:
  web-net:
    external: true
```

这个架构里 nginx 和 php 在同一个 docker 网桥 web-net，`fastcgi_pass php85:9000` 容器内部 DNS 互通，不需要映射 9000 端口。
本机：http://127.0.0.1:8081 可以访问 nginx 项目。

如果允许同一局域网内的其他机器访问，需要Windows 防火墙放行 8081、8444 端口：防火墙 → 高级设置 → 入站规则 → 新建规则，放行 TCP 8081、8444。

/root/lnmp-web/nginx/conf.d/catch.conf 文件内容：
```
server {
    listen 80;
    server_name catch.test;

    root /var/www/html/catchadmin/public;
    index index.php;

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 7d;
        try_files $uri =404;
    }

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass php85:9000;  # 容器内部同一net访问
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 4 128k;
    }

    location ~ /\.env { deny all; }
    location ~ composer\.json { deny all; }
}
```

在 Windows CMD / PowerShell 中执行 compose：
```
docker compose -f \\wsl$\docker-desktop\root\lnmp-web\docker-compose.yml up -d --build
```

不要在 WSL 中执行 `docker` / `docker compose` 命令，否则会报错。所有 `docker compose`、`docker ps`、`docker network` 全部在 Windows CMD / PowerShell 执行。

如果 mysql、redis 未接入 web-net 网络，需要把 mysql、redis 接入 web-net 网络：
```
docker network connect web-net mysql
docker network connect web-net redis
```

然后再执行上面的 `docker compose` 命令。



### 部署 CatchAdmin

在 Windows 资源管理器往 `\\wsl.localhost\docker-desktop\root\lnmp-web\www\` 上传代码包，然后再进容器 `composer install` 安装依赖。

```
docker exec -it php85 bash
cd /var/www/html/catchadmin
composer install
cp .env.example .env
php artisan key:generate
```

然后配置.env，权限，迁移：
```
chown -R www-data:www-data /var/www/html/catchadmin/storage /var/www/html/catchadmin/bootstrap/cache
php artisan migrate
php artisan db:seed
```

修改 `C:\Windows\System32\drivers\etc\hosts` 文件，添加 catch.test 记录：
```
127.0.0.1 catch.test
```

浏览器访问 http://catch.test:8081

后续修改 nginx 配置后的重载命令（CMD）：
```
docker exec nginx nginx -t
docker exec nginx nginx -s reload
```


## 附言

```
# 看有没有指定的拓展
php -m | grep -E "bcmath|ctype|fileinfo|mbstring|Zend OPcache|gd|intl|pdo_mysql|zip"

php -i | grep opcache.enable

# 看有没有redis
php -m | grep redis

# 看加载的ini文件
php --ini
```

```
# 查看80、443、9000占用
netstat -ano | findstr ":80"
netstat -ano | findstr ":443"
netstat -ano | findstr ":9000"
```

```
# 查看容器
docker ps

# 查看日志
docker logs php85
docker logs nginx

# 重载nginx配置（改完catch.conf之后）
docker exec nginx nginx -s reload

# 停止服务
docker compose -f \\wsl$\docker-desktop\root\lnmp-web\docker-compose.yml down

# 停止整套服务
docker compose down

# 只启动，不重新构建镜像
docker compose up -d

# 构建镜像 + 启动 php85 + nginx
docker compose up -d --build
```

```
# 校验 PHP
docker exec php85 php -v
docker exec php85 php -m

# 查看nginx版本
docker exec nginx nginx -v
# 测试配置文件语法
docker exec nginx nginx -t

# 查看日志
docker logs php85
docker logs nginx

# 修改nginx配置后重载
docker exec nginx nginx -s reload

# 停止
docker compose down

# 重新启动（不重新构建镜像）
docker compose up -d
```

权限坑：
```
docker exec php85 chown -R www-data:www-data /var/www/html/catchadmin/storage /var/www/html/catchadmin/bootstrap/cache
```




