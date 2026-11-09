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

## Compose介绍

### Compose是什么

**Docker Compose 通俗介绍**

一句话：**Docker Compose 是 Docker 官方的「多容器编排工具」，用来一次性管理一组关联的容器**。

**背景**

单独用 `docker run` 启动容器：
每一个服务（Nginx、PHP、MySQL、Redis）都要敲很长一串命令，写端口、挂载卷、网络、环境变量；
服务多了，命令很难记、不好复用，还要手动保证网络互通、顺序启动。

比如你的项目：Nginx + PHP85，是两个配合在一起工作的容器，属于**一套业务**。

**Compose 核心作用**

把这一整套服务的配置，全部写到一个 `docker-compose.yml` 文件里：
- 每个服务：镜像/怎么构建、端口映射、挂载目录、网络、依赖、重启策略
- 定义统一网络，让容器之间可以互相访问（`php85:9000` 这种容器名通信）
- 一键启动、停止、重启整套服务，不用一条一条敲 docker run

```yaml
# 就是你一直在写的这种
services:
  php85:
    build: ./php
  nginx:
    image: nginx:stable
```

**几个核心概念**
1. **service（服务）**
   一个 service 对应一个容器（Nginx、PHP 各是一个service），描述这个容器怎么跑。
2. **project（项目）**
   同一个目录下的 `docker-compose.yml` 就是一个项目，compose 会给这套容器统一加上项目前缀，隔离不同项目。
3. **network（网络）**
   默认自动创建独立网桥，同项目容器之间互通；也可以接入外部网络（像你用的 `web-net`）。
4. **volume（数据卷）**
   统一管理持久化数据，比如MySQL数据。

**和原生 docker 的关系**
- `docker`：管理单个容器、镜像（底层）
- `docker compose`：基于docker，**批量管理一组有关系的容器**（上层工具）

> 旧版叫独立工具 `docker-compose`（横杠）；新版是 `docker compose`（空格，作为docker内置子命令）

**它能干什么**
- ✅ 一键启动整套环境：`docker compose up -d`
- ✅ 一键关停整套：`docker compose down`
- ✅ 按依赖顺序启动（比如先启动php，再启动nginx）
- ✅ 统一查看所有服务日志
- ✅ 同一个机器上，多套项目互不干扰（多个yml，分开管理）

**它不能干什么（区分清楚）**
Compose **适合单机**，只是在一台服务器上管理多个容器。
如果是多台服务器集群、负载均衡、自动扩缩容，那是 Kubernetes（K8s）干的，Compose 不适合。

**结合你现在的场景理解**

你这套 LNMP：Nginx容器 + PHP容器，放在同一个 compose yml 里，属于**一个项目**。
- 启动：同时拉起nginx和php
- 网络互通：nginx 可以直接用服务名 `php85` 访问php-fpm
- 改配置：只需要改yml，不用记一长串docker run参数

### Docker / Docker Compose / K8s 简单对比

**Docker / Docker Compose / K8s 简单对比表**
| 项目 | Docker（原生） | Docker Compose | Kubernetes(K8s) |
| ---- | ---- | ---- | ---- |
| **定位** | 容器引擎，底层核心 | 单机多容器编排工具 | 分布式集群编排平台 |
| **运行范围** | 单容器 | **一台机器**，管理一组关联容器 | **多台服务器组成集群** |
| 核心用途 | 拉镜像、启动、停止、删除单个容器 | 把 Nginx/PHP/Redis/MySQL 写在yml，一键启停整套环境 | 集群调度、自动扩缩容、故障自愈、负载均衡、滚动更新 |
| 配置文件 | 没有统一配置文件，靠 `docker run` 长命令 | `docker-compose.yml` | yaml（deployment/service/ingress等多种资源） |
| 启动方式 | `docker run` | `docker compose up -d` | kubectl apply |
| 自动重启 | 容器内配置 `restart` | 每个service配置 `restart` | 节点挂了可以自动把容器调度到别的机器 |
| 扩容 | 手动多run几次 | `--scale`，单机扩容 | 横向扩容，跨机器 |
| 故障自愈 | 容器退出可以重启；**机器挂了就没办法** | 容器退出可以重启；**机器挂了整套直接挂** | 检测节点故障，自动迁移实例到其他正常服务器 |
| 学习难度 | 低 | 低（你现在正在用） | 很高，组件多、概念复杂 |
| 适合场景 | 临时测试、单个容器 | 开发环境、单机部署项目（你的LNMP就是典型） | 生产集群、高可用业务、流量大、需要多服务器 |
| 不适合 | 一次性管理一大堆关联容器 | 多服务器集群、高可用 | 本地简单开发环境，太重了 |

**一句话通俗总结**
1. **Docker**：只是“容器发动机”，负责跑一个容器；
2. **Docker Compose**：发动机的简易遥控器，**一台机器上**同时控制一堆配套的容器（Nginx+PHP+Redis），适合开发、单机部署；
3. **K8s**：大型机房调度系统，管一堆服务器，保证服务不宕机、负载均衡，适合线上集群。

补充：还有一个容易听到的：Docker Swarm
- 也是docker官方的集群工具，介于compose和k8s之间，现在基本被K8s淘汰，很少用。

### Docker Compose 常用命令

> 前提：必须在 `docker-compose.yml` 所在目录执行，或者用 `-f` 指定配置文件

**基础启停**

```
# 启动所有服务（后台守护运行，最常用）
docker compose up -d

# 启动并看实时日志（前台，ctrl+c停止）
docker compose up

# 只启动指定服务
docker compose up -d php85

# 停止服务，容器保留（数据卷还在）
docker compose stop

# 启动已经停止的容器
docker compose start

# 重启全部服务
docker compose restart

# 只重启指定服务
docker compose restart php85

# 停止 + 删除容器（默认不删卷、镜像）【重点！日常重建用这个】
docker compose down

# down 的时候同时删除数据卷（⚠️ 会丢数据库数据，谨慎！）
docker compose down -v
```

**构建镜像（你经常用到）**

```
# 检测yml、构建镜像，不启动
docker compose build

# 构建 + 启动（修改Dockerfile后必用）
docker compose up -d --build

# 只单独构建某一个服务镜像
docker compose build php85

# 不使用缓存构建（解决缓存导致修改不生效）
docker compose build --no-cache
```

**查看状态、日志**

```
# 查看当前compose管理的容器状态
docker compose ps

# 查看日志（持续输出，实时看）
docker compose logs -f

# 只看某个服务日志（比如php85）
docker compose logs -f php85

# 看最后200行日志
docker compose logs --tail=200 php85
```

**进入容器、执行命令**

```
# 交互式进入容器（你最常用）
docker compose exec php85 bash

# 如果容器里面没有bash，用sh
docker compose exec php85 sh

# 直接在容器里执行一条命令，不进入交互
docker compose exec php85 php -m
```

**镜像、资源清理**

```
# 删掉compose对应的镜像（build出来的）
docker compose rm

# 清理本地无用镜像（悬空镜像<none>）
docker image prune -f

# 清理所有未使用镜像（⚠️ 谨慎）
docker image prune -a
```

**配置校验**

```
# 校验yml语法是否写错（写好先检查，避免报错）
docker compose config

# 打印渲染后的完整配置（看变量替换、网络、挂载是否符合预期）
docker compose config
```

**缩放（很少用，了解）**

```
# 启动多个同类型容器（php-fpm一般不用）
docker compose up -d --scale php85=2
```

**指定别的yml文件（多环境很实用）**

```
# 使用自定义名字的配置文件
docker compose -f docker-compose-dev.yml up -d
```

---

**高频组合（你开发LNMP时最常用）**

1. 修改 Dockerfile 后重建：

```
docker compose down
docker compose up -d --build
```

2. 改配置文件（ini、nginx.conf），不需要重新build，只重启服务：

```
docker compose restart php85
```

3. 看php报错日志：

```
docker compose logs -f php85
```

4. 进php容器调试：

```
docker compose exec php85 bash
```

**容易踩坑提醒**

- `docker compose down`：**删除容器，卷不会删**，MySQL数据还在；加 `-v` 才删卷
- `--build` 只有Dockerfile改动时才需要，单纯改nginx.conf、ini不需要build
- exec 是**进入正在运行的容器**，容器stop状态下exec会报错

---

**速查小卡片版**

Docker Compose 速查卡片（v2，docker compose）

> 提示：在 `docker-compose.yml` 所在目录执行；`-f` 指定其他配置文件

🔹 启停服务

```
# 后台启动全部
docker compose up -d
# 前台启动，看日志
docker compose up
# 只启动指定服务
docker compose up -d php85

# 停止（保留容器）
docker compose stop
# 启动已停止容器
docker compose start
# 重启全部
docker compose restart
# 重启单个服务
docker compose restart php85

# 停止并删除容器（不删数据卷）
docker compose down
# down + 删除数据卷 ⚠️ 会丢数据，谨慎
docker compose down -v
```

🔹 构建镜像

```
# 构建镜像，不启动
docker compose build
# 修改Dockerfile后，构建+启动
docker compose up -d --build
# 单独构建某个服务
docker compose build php85
# 无缓存构建
docker compose build --no-cache
```

🔹 查看状态 & 日志

```
# 查看容器状态
docker compose ps
# 实时看全部日志
docker compose logs -f
# 实时看单个服务日志
docker compose logs -f php85
# 查看最后200行
docker compose logs --tail=200 php85
```

🔹 进入容器、执行命令

```
# 进入交互终端
docker compose exec php85 bash
# 无bash时用sh
docker compose exec php85 sh
# 直接执行一条命令
docker compose exec php85 php -m
```

🔹 配置校验

```
# 校验yml语法
docker compose config
# 使用自定义配置文件
docker compose -f docker-compose-dev.yml up -d
```

🔹 清理

```
# 删除compose管理的容器（容器停止才能执行）
docker compose rm
# 清理悬空镜像 <none>
docker image prune -f
```

✅ 高频组合（开发常用）

1. 改Dockerfile重建：`docker compose down && docker compose up -d --build`
2. 改nginx/ini配置：`docker compose restart php85`
3. 调试进入容器：`docker compose exec php85 bash`

💡 备注

- `docker exec -it php85 bash`：容器固定container_name时可用，等价效果
- `--build`：只改Dockerfile才需要；单纯改配置文件不用build
- exec 只能进入**运行中**的容器


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

## 安装 PHP8.5 与 Ngnix

我们在 `D:\develop` 目录下创建 `lnmp-web` 目录，用于存放 LNMP 相关文件。下面是具体的目录结构：
```
D:\develop\lnmp-web\
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
    nodejs \ 
    npm \
    && rm -rf /var/lib/apt/lists/*

# 配置npm镜像源为npmmirror，并下载yarn的镜像及配置镜像源
RUN npm config set registry https://registry.npmmirror.com \
    && npm config set ignore-engines true \
    && npm install -g yarn \
    && yarn config set registry https://registry.npmmirror.com \
    && yarn config set sass_binary_site https://npmmirror.com/mirrors/node-sass/

RUN docker-php-ext-configure gd --with-freetype --with-jpeg

# 安装 PHP 扩展，安装后默认是开启的
RUN docker-php-ext-install -j$(nproc) \
    bcmath \
    gd \
    intl \
    mysqli \
    pdo_mysql \
    zip

# 手动生成扩展加载ini，解决php8.5下不自动生成ini的bug
RUN echo "extension=gd.so" > /usr/local/etc/php/conf.d/20-gd.ini \
    && echo "extension=intl.so" > /usr/local/etc/php/conf.d/20-intl.ini \
    && echo "extension=pdo_mysql.so" > /usr/local/etc/php/conf.d/20-pdo_mysql.ini \
    && echo "extension=zip.so" > /usr/local/etc/php/conf.d/20-zip.ini

# ctype、fileinfo、mbstring 原生自带并默认开启

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

# 安装 composer （从 Docker 镜像内直接复制二进制）
# COPY --from=composer/composer:2-bin /composer /usr/bin/composer
# RUN chmod +x /usr/bin/composer
# 优先官方源，如果需要可配置国内镜像源（建议在容器内进行修改）
# RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

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

    # 如果有别的服务器需要外网/局域网直连这个php-fpm才加下面90001:9000这行，否则不要加。下面 10086 可以作为 vite dev 热更新开发端口
    # ports:
    #   - "9001:9000"
        - "10086:10086"

  nginx:
    image: nginx:stable
    container_name: nginx
    restart: unless-stopped
    ports:
      # - "80:80"
      # - "443:443"
      # 如果上面端口已被占用，这里直接写 8081:80，代表监听宿主机所有网卡（0.0.0.0），局域网、外网都能访问
      - "8081:80"
      # - "8444:443"
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
    index index.html index.php index.htm default.php default.htm default.html;

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 7d;
        try_files $uri =404;
    }

    location / {
       if (!-e $request_filename) {
        rewrite  ^(.*)$  /index.php?s=/$1  last;
        break;
      }
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php85:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
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

## 部署 CatchAdmin

参考文档：https://doc.catchadmin.com/docs/5.0/start/install

官方推荐的是自己的命令安装：
```
# 进入容器
> docker exec -it php85 bash

# 切换到 www 目录
> cd /var/www/html

# 拉取 CatchAdmin 代码
> git clone https://gitee.com/catchadmin/catchAdmin.git catchadmin

> cd catchadmin

composer install

// 安装项目, 按照提示输入对应信息，会自动下载前端项目。
php artisan catch:install

// 上传显示图片需要软连接
php artisan storage:link
```

### 后端

在 `D:\develop\lnmp-web\www` 文件夹下安装 CatchAdmin 后端：
```
# 进入容器
> docker exec -it php85 bash

# 切换到 www 目录
> cd /var/www/html

# 拉取 CatchAdmin 代码
> git clone https://gitee.com/catchadmin/catchAdmin.git catchadmin

> cd catchadmin

# 切官方源
> composer config repo.packagist composer https://repo.packagist.org

# 安装依赖
> composer install

# 复制环境变量模板
> cp .env.example .env

# 生成应用密钥
> php artisan key:generate

# 数据库迁移
> php artisan migrate

# 上传显示图片需要软连接
> php artisan storage:link

# 修改权限
> ls -1h
> chmod -R 775 /var/www/html/catchadmin/storage /var/www/html/catchadmin/bootstrap/cache
```

打开 `.env` 文件配置数据库连接等信息。

修改 `C:\Windows\System32\drivers\etc\hosts` 文件，添加 catch.test 记录：
```
127.0.0.1 catch.test
```

在 config 目录下新建文件 catch.php，并写入内容：
```php
<?php

return [
    /*
    |--------------------------------------------------------------------------
    | catch-admin 超级管理员
    |--------------------------------------------------------------------------
    |
    | 可以设置超级管理的用户 ID
    | 支持数组
    |
    |--------------------------------------------------------------------------
    */
    'super_admin' => 1,

    /*
    |--------------------------------------------------------------------------
    | catch-admin 请求允许
    |--------------------------------------------------------------------------
    |
    | 默认允许 GET 请求通过 RBAC 权限
    |
    |--------------------------------------------------------------------------
    */
    'request_allowed' => true,

    /*
    |--------------------------------------------------------------------------
    | catch-admin 模块设置
    |--------------------------------------------------------------------------
    |
    | 设置模块根目录
    | 设置模块的根命名空间
    | 设置模块默认生成的文件夹
    |
    |--------------------------------------------------------------------------
    */
    'module' => [
        'root' => 'modules',

        'namespace' => 'Modules',

        /**
         * 默认启动的模块
         */
        'default' => ['develop', 'user', 'common'],

        'default_dirs' => [
            'Http'.DIRECTORY_SEPARATOR,

            'Http'.DIRECTORY_SEPARATOR.'Requests'.DIRECTORY_SEPARATOR,

            'Http'.DIRECTORY_SEPARATOR.'Controllers'.DIRECTORY_SEPARATOR,

            'Models'.DIRECTORY_SEPARATOR,
        ],

        // 模块存储驱动
        // 默认使用文件驱动
        'driver' => [
            // currently, catchadmin support file and database
            // the default is driver
            'default' => 'file',

            // use database driver
            'table_name' => 'admin_modules',
        ],

        /**
         * 模块路由集合
         */
        'routes' => [],

        /**
         * 模块是否自动加载
         *
         * 如果设置成 true，模块会自动全部加载
         */
        'autoload' => env('CATCH_MODULE_AUTOLOAD', false),
    ],

    /*
    |--------------------------------------------------------------------------
    | catch-admin 响应
    |--------------------------------------------------------------------------
    */
    'response' => [
        // JSON 响应, 保证响应数据都是 json
        'always_json' => \Catch\Middleware\JsonResponseMiddleware::class,

        // 响应监听者
        // 监听[RequestHandled]事件
        'request_handled_listener' => \Catch\Listeners\RequestHandledListener::class,
    ],

    /*
   |--------------------------------------------------------------------------
   | 数据库 SQL 日志
   |--------------------------------------------------------------------------
   */
    'listen_db_log' => env('APP_DEBUG', true),

    /*
   |--------------------------------------------------------------------------
   | 管理员授权认证模型
   |--------------------------------------------------------------------------
   */
    'auth_model' => \Modules\User\Models\User::class,

    /*
   |--------------------------------------------------------------------------
   | 管理员授权 Guard
   |--------------------------------------------------------------------------
   */
    'auth' => 'admin',

    /*
   |--------------------------------------------------------------------------
   | 路由配置
   |--------------------------------------------------------------------------
   */
    'route' => [
        'prefix' => 'api',

        'middlewares' => [
            \Catch\Middleware\AuthMiddleware::class,
            \Catch\Middleware\JsonResponseMiddleware::class,
        ],
    ],

    /*
   |--------------------------------------------------------------------------
   | 前端 Vue 视图文件夹路径
   |
   | 如果不设置，将不会生成相关的 Vue 文件
   |--------------------------------------------------------------------------
   */
    'views_path' => base_path('web'.DIRECTORY_SEPARATOR.'src'.DIRECTORY_SEPARATOR.'views'.DIRECTORY_SEPARATOR),

    /*
   |--------------------------------------------------------------------------
   | 开启系统接口日志分析
   |
   | 接口日志依赖 Redis，提高性能
   |--------------------------------------------------------------------------
   */
    'system_api_log' => env('CATCH_SYSTEM_API_LOG', false),

    /*
   |--------------------------------------------------------------------------
   | 图片处理
   |
   | 默认使用 GD
   |--------------------------------------------------------------------------
   */
    'image' => [
        'driver' => env('CATCH_IMAGE_DRIVER', 'gd'),

        'options' => [

        ],

        /**
         * 默认读取磁盘
         */
        'read_from' => env('CATCH_IMAGE_READ_FROM', 'uploads'),
    ],

    /*
    |--------------------------------------------------------------------------
    | 后台缓存统一管理
    |
    | 配置后台缓存前缀，便于清理后台管理的缓存
    |--------------------------------------------------------------------------
    */
    'admin_cache_key' => env('CATCH_ADMIN_CACHE_KEY', 'admin_dashboard_'),

     /*
    |--------------------------------------------------------------------------
    | 模型相关配置
    |
    | 配置模型的配置
    |--------------------------------------------------------------------------
    */
    'model' => [
        // created_at & updated_at format
        'date_format' => 'Y-m-d H:i:s',

        // update creator id
        'change_creator_id' => (bool) env('CATCH_CHANGE_CREATOR_ID', false),
    ],

    /*
    |--------------------------------------------------------------------------
    | Excel 配置
    |--------------------------------------------------------------------------
    */
    'excel' => [
        /**
         * 导出路径, 相对于 storage 目录得相对路径
         */
        'export_path' => 'excel/export',
    ],
];
```

浏览器访问 http://catch.test:8081

### 前端

安装 CatchAdmin 前端：
```
# 拉取 CatchAdmin 前端代码
git clone -b v5 https://gitee.com/catchadmin/catch-admin-vue.git web

# 切换到 web 目录
> cd web

# 切国内源
> yarn config set registry https://registry.npmmirror.com

# 安装依赖
> yarn install --ignore-engines

# 复制环境变量模板
> cp .env.example .env
```

打开 `.env` 文件配置api连接地址信息：
```
VITE_BASE_URL=http://catch.test:8081/api
```

修改 `vite.config.ts` 文件中的 server 配置：
```
server: {
    hmr: true,
    host: '0.0.0.0',
    port: 10086,
    ...
}
```

启动前端项目
> yarn dev

浏览器访问 http://localhost:10086

### 补充

`php artisan catch:install` 是catchadmin的一键安装命令。

#### 安装命令

看一下 `php artisan catch:install`  做了什么。

一、`php artisan catch:install` 的执行流程

命令定义在 [InstallCommand.php](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/InstallCommand.php#L36-L90)（签名 `catch:install {--prod}`），主流程 `handle()` 如下：

1. **环境检测** `detectionEnvironment()`
   - 检查 `git`、`composer`、`node`、`yarn`、`exec` 函数是否可用
   - PHP 版本必须 ≥ 8.2
   - 检查扩展：`bcmath, ctype, intl, dom, mysqli, fileinfo, json, mbstring, openssl, pcre, pdo, tokenizer, xml, pdo_mysql`

2. **关键判断：只有 `.env` 不存在时才执行安装**（[L58-L88](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/InstallCommand.php#L58-L88)）。如果 `.env` 已存在，会直接跳到 `showInfo()` 打印欢迎信息。所以想重装必须先删 `.env`。

3. 安装步骤（`.env` 不存在时）：
   - `askForCreatingDatabase()`：交互式收集应用名称、URL、数据库驱动（mysql/pgsql）、库名、表前缀、host/port/用户名/密码，写入 `.env`（APP_NAME/APP_ENV/APP_DEBUG/APP_URL/DB_*），并执行 `create database if not exists ... default charset utf8mb4 collate utf8mb4_general_ci`（pgsql 走另一条 SQL）
   - `publishConfig()`：见下一节（数据库迁移的核心）
   - `createStorageLink()`：`storage:link`
   - `installed()`：克隆前端仓库 `catch-admin-vue` 到 `web/`、`yarn config set registry` + `yarn install`、生成 `web/.env`、往根 `composer.json` 写入 `Modules\` 的 PSR-4 自动加载并 `dump-autoload`、执行 `catch:plugin-install`

4. `showInfo()`：打印默认账号 `catch@admin.com / catchadmin`，可选打开文档，并改写 `composer run dev` 脚本

5. 任何异常都会触发 `rollback()`：删除 `.env`、`web/.env`，并 **drop 掉刚创建的数据库**。

---

二、数据库迁移相关的具体动作

`publishConfig()`（[L262-L295](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/InstallCommand.php#L262-L295)）内部依次执行：

| 步骤 | 命令 | 作用 |
|---|---|---|
| 1 | `key:generate` | 生成 APP_KEY |
| 2 | `vendor:publish --tag=catch-config` | 发布 `config/catch.php` |
| 3 | `vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"` | 仅在缺少 `sanctum.php` 或 `personal_access_tokens` 迁移时发布 |
| 4 | `migrate` | 跑根目录 `database/migrations` 的 Laravel 原生迁移 |
| 5 | `catch:migrate user` + `catch:db:seed user` | 用户模块 |
| 6 | `catch:migrate develop` + `catch:db:seed develop` | 开发模块 |
| 7 | `catch:migrate permissions` + `catch:db:seed permissions` | 权限模块 |
| 8 | `catch:migrate system` + `catch:db:seed system` | 系统模块 |

**模块迁移机制**（[MigrateRun.php](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/Migrate/MigrateRun.php#L52-L78)）：
- 遍历 `modules/{module}/database/migrations/` 下的**每个文件**，逐个调用 `artisan migrate --path=...`
- 执行前用 `migrations` 表按文件名判断是否已执行过 → **所有模块共用同一张 `migrations` 表**，可重复执行、增量迁移
- 因此每个模块的迁移文件都是"单体迁移"，而不是一次批量执行

**模块填充机制**（[SeedRun.php](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/Migrate/SeedRun.php#L52-L72)）：`require_once` 载入 `modules/{module}/database/seeder/*.php` 中的匿名 Seeder 类并调用 `run()`。

**需要注意的两个点**：
- `catch:install` 直接调用 `catch:migrate` / `catch:db:seed`，**没有走模块的 `Installer::install()`**（[Installer.php](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Support/Module/Installer.php#L75-L105)），所以不会写模块注册表。默认 `catch.module.driver=file` 且 `autoload=true`，`admin_modules` 表根本不会被创建（该表迁移只在 `vendor:publish --tag=catch-module` 时才发布）。
- `modules/Develop` 下**没有 `database/seeder` 目录**，`catch:db:seed develop` 会抛异常并被外层 `catch` 吞掉，可能导致后面的 `permissions`、`system` 的迁移/填充没跑。若发现表缺失，手动补跑：

```bash
php artisan catch:migrate permissions
php artisan catch:db:seed permissions
php artisan catch:migrate system
php artisan catch:db:seed system
```

**字段宏约定**（[Blueprint.php](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Support/Macros/Blueprint.php#L36-L110)）：`createdAt()/updatedAt()/deletedAt()` 都是 `unsignedInteger default 0` 的 **Unix 时间戳**（`deleted_at=0` 表示未删除，即软删除）；`creatorId()` 为 `unsignedInteger`；`status()` 为 `tinyInteger`，1 正常 / 2 禁用。

---

三、迁移会创建的具体表

1. Laravel 基础表（根 `database/migrations`，`migrate` 执行）
- `users`、`password_reset_tokens`、`sessions` — [0001_01_01_000000_create_users_table.php](file:///d:/develop/lnmp-web/www/catchadmin/database/migrations/0001_01_01_000000_create_users_table.php)
- `cache`、`cache_locks` — 0001_01_01_000001
- `jobs`、`job_batches`、`failed_jobs` — 0001_01_01_000002
- `migrations`（Laravel 自动建）
- `personal_access_tokens`（Sanctum 发布后执行）

2. User 模块（`catch:migrate user`）
| 表 | 说明 | 主要字段 |
|---|---|---|
| `admin_users` | 后台用户表 | username, password, email, avatar, remember_token, department_id, creator_id, status, login_ip, login_at, 时间戳/软删 |
| `log_login` | 登录日志 | account, login_ip, browser, platform, login_at, status（后续迁移追加 location、token_id） |
| `log_operate` | 操作日志 | module, action, params, ip, http_method, http_code, start_at, time_taken, creator_id, created_at |

后续迁移对 `admin_users` 追加 `mobile`、改 `password`、追加 `open_id/unionid`。

3. Permissions 模块
| 表 | 说明 |
|---|---|
| `roles` | 角色表：role_name, identify, parent_id, description, data_range |
| `positions` | 岗位表（原 `jobs` 被重命名为 `positions`）：job_name, coding, status, sort, description |
| `departments` | 部门表：parent_id, department_name, principal, mobile, email, status, sort |
| `permissions` | 权限/菜单表：parent_id, permission_name, route, icon, module, permission_mark, component, redirect, keepalive, type(1目录/2菜单/3按钮), hidden, sort |
| `user_has_roles` | 用户-角色关联（user_id, role_id） |
| `role_has_permissions` | 角色-权限关联（role_id, permission_id） |
| `user_has_jobs` | 用户-岗位关联（user_id, job_id） |
| `role_has_departments` | 角色-部门关联（role_id, department_id） |

4. Develop 模块（代码生成器）
| 表 | 说明 |
|---|---|
| `schemas` | 生成方案：module, name, columns, is_soft_delete, generate_params |
| `schema_files` | 生成产物：schema_id + controller/model/request/vue table/vue form 的路径与文件内容 |

5. System 模块
| 表 | 说明 |
|---|---|
| `system_dictionary` | 字典：name, key, status, description |
| `system_dictionary_values` | 字典值：dic_id, label, key, value, sort, status |
| `system_config` | 配置项 key/value |
| `system_sms_template` | 短信模板：channel, template_id, content, variables |
| `system_sms_code` | 短信验证码记录：mobile, code, behavior, channel, status, expired_at |
| `system_attachments` | 附件表：category_id, filename, path, extension, filesize, mimetype, driver |
| `system_attachment_category` | 附件分类：parent_id, title |
| `system_mcp_tokens` | MCP Token：user_id, name, token_hash, status, expires_at, revoked_at, last_used_at, last_used_ip，外键指向 `admin_users` |

6. 可选表
- `admin_modules`（`config('catch.module.table_name')`）：模块注册表，仅在使用 database 驱动时需要，执行 `php artisan vendor:publish --tag=catch-module` 后再 `migrate`。

---

四、种子数据（seeders）

| 模块 | Seeder | 写入内容 |
|---|---|---|
| user | [User.php](file:///d:/develop/lnmp-web/www/catchadmin/modules/User/database/seeder/User.php#L10-L34) | 创建超管 `catchadmin / catch@admin.com / catchadmin`（已存在则跳过） |
| permissions | [PermissionsMenusSeeder.php](file:///d:/develop/lnmp-web/www/catchadmin/modules/Permissions/database/seeder/PermissionsMenusSeeder.php#L14-L25) | 通过 `ImportPermissions::import()` 递归写入"权限管理"菜单树到 `permissions` 表（按 permission_name+module+permission_mark 去重） |
| system | [SystemMenusSeeder.php](file:///d:/develop/lnmp-web/www/catchadmin/modules/System/database/seeder/SystemMenusSeeder.php#L14-L25) | 写入"系统管理"菜单树到 `permissions` 表 |
| 安装收尾 | `catch:plugin-install` | 发布插件视图 + 往 `permissions` 写入"插件管理"菜单（`/plugin`），之后可在后台 `/plugins` 安装插件 |

> 补充：`roles`、`departments` 等表在安装时只有表结构、没有预置数据，需要登录后台自行创建；默认超管 `id=1` 由 `config('catch.super_admin')` 视为超级管理员，绕过 RBAC 校验。

#### 安装步骤

如果 `php artisan catch:install` 执行错误，可以按照下面给命令逐行执行。

一、先说结论：你的 `.env` 已经存在，所以 `catch:install` 本来就会跳过安装步骤

`handle()` 里有个硬判断（[InstallCommand.php:58-88](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/InstallCommand.php#L58-L88)）：**只有 `.env` 不存在时才执行"建库 → 发布配置 → 迁移 → 装前端"**，`.env` 存在就直接跳到打印欢迎信息。

你当前 [.env](file:///d:/develop/lnmp-web/www/catchadmin/.env) 里已经配好了：

```
APP_KEY=base64:...          已有
DB_CONNECTION=mysql
DB_HOST=mysql8
DB_PORT=3306
DB_DATABASE=catchadDb
DB_USERNAME=root / DB_PASSWORD=123456
DB_PREFIX=ca_               ← 注意所有表都会带 ca_ 前缀
APP_URL=http://catch.test:8081
```

同时 `config/catch.php` 已发布、`composer.json` 里 `"Modules\\": "modules/"` 已存在，但 **`web/` 前端目录和 `public/storage` 软链接都不存在**，`database/migrations` 里也没有 `personal_access_tokens` 迁移。

所以**不要重复执行 `catch:install`**，手动按下面顺序补齐即可。

---

二、替代命令（按顺序执行）

阶段 1：确认/创建数据库

```bat
mysql -h mysql8 -P 3306 -u root -p123456 -e "CREATE DATABASE IF NOT EXISTS catchadDb DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;"
```

如果是 Docker 起的 MySQL：

```bat
docker exec -it mysql8 mysql -uroot -p123456 -e "CREATE DATABASE IF NOT EXISTS catchadDb DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;"
```

验证连通性（能连上再往下走）：

```bat
php artisan db:show
```

阶段 2：配置与基础迁移

```bat
php artisan key:generate
php artisan vendor:publish --tag=catch-config
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

对应 `catch:install` 里的 `publishConfig()`。第 2 条会提示 `config/catch.php` 已存在，选 `no` 覆盖即可跳过；第 4 条会创建 `users / password_reset_tokens / sessions / cache / jobs / migrations / personal_access_tokens` 等基础表。

阶段 3：四个业务模块的迁移 + 种子（顺序不能乱）

```bat
php artisan catch:migrate user
php artisan catch:db:seed user

php artisan catch:migrate permissions
php artisan catch:db:seed permissions

php artisan catch:migrate system
php artisan catch:db:seed system

php artisan catch:migrate develop
php artisan catch:db:seed develop
```

顺序说明：`system` 模块的 `system_mcp_tokens` 表带外键指向 `admin_users`，所以 **user 必须排在 system 之前**，否则会报外键错误。

执行完确认表都建好了（会带 `ca_` 前缀）：

```bat
php artisan db:show --counts
```

应能看到：`ca_admin_users`、`ca_log_login`、`ca_log_operate`、`ca_roles`、`ca_positions`、`ca_departments`、`ca_permissions`、`ca_user_has_roles`、`ca_role_has_permissions`、`ca_user_has_jobs`、`ca_role_has_departments`、`ca_schemas`、`ca_schema_files`、`ca_system_dictionary`、`ca_system_dictionary_values`、`ca_system_config`、`ca_system_sms_template`、`ca_system_sms_code`、`ca_system_attachments`、`ca_system_attachment_category`、`ca_system_mcp_tokens`。

阶段 4：收尾（软链接 + 插件 + 前端）

```bat
php artisan storage:link
php artisan catch:plugin-install
```

前端（`catch:install` 里 `installFrontProject()` 做的事，需先装好 git / node / yarn）：

```bat
git clone https://gitee.com/catchadmin/catch-admin-vue.git web
cd web
yarn config set registry https://registry.npmmirror.com
yarn install
cd ..
```

新建 `web\.env`，内容两行：

```ini
VITE_BASE_URL=http://catch.test:8081/api/
VITE_APP_NAME=CatchAdmin后台管理系统
```

最后清缓存：

```bat
php artisan optimize:clear
```

---

三、几个容易踩的坑

| 问题 | 说明 |
|---|---|
| **重复执行是安全的** | `catch:migrate` 会逐个文件比对 `migrations` 表里的文件名（[MigrateRun.php:60-72](file:///d:/develop/lnmp-web/www/catchadmin/vendor/catchadmin/core/src/Commands/Migrate/MigrateRun.php#L60-L72)），已执行过的会跳过，可以放心重跑 |
| **`catch:db:seed` 不是幂等的** | `SeedRun` 直接 `require_once` 所有 seeder 并 `run()`。用户 seeder 内部有 email 去重；菜单 seeder 的 `ImportPermissions` 按 `permission_name + module + permission_mark` 去重，所以也能重复跑，但**自定义 seeder 需自己保证幂等** |
| **生产环境要加 `--force`** | `APP_ENV=production` 时 `migrate` / `catch:migrate` / `catch:db:seed` 都会交互确认，加上 `--force` 才能无人值守，例如 `php artisan catch:migrate system --force` |
| **`develop` 模块没有 seeder 目录** | 执行 `catch:db:seed develop` 会自动创建一个空的 `modules/Develop/database/seeder/` 目录，不报错也无数据，属正常 |
| **`admin_modules` 表不会被创建** | 因为 `catch.module.driver` 默认是 `file` 且 `autoload=true`，模块不落库。只有改用 database 驱动时才需要 `php artisan vendor:publish --tag=catch-module` 再 `migrate` |
| **重装想彻底重来** | 删 `.env` → `php artisan catch:install`；或直接 `php artisan catch:migrate:fresh user --force` 等逐模块重建（会丢数据） |

如果 `catch:install` 失败的具体报错是 **Node / Yarn / Git 未安装**，那前三个阶段（建库、配置、迁移、种子）其实都能正常完成，只是前端没装；把阶段 1~3 + `storage:link` + `catch:plugin-install` 跑完，后端就能用了，前端单独补装即可。

### 其他

后续有其他项目配置后，修改 nginx 配置后的重载命令（CMD）：
```
docker exec nginx nginx -t
docker exec nginx nginx -s reload

docker compose restart nginx

docker compose up -d nginx
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

### 安装PHP拓展

在正式开发中如果发现安装的 php85 容器中缺少php拓展，如 pdo_mysql，可以通过以下命令安装：
```
# 进入容器
docker exec -it php85 bash

# 1. 先装编译依赖
apt-get update && apt-get install -y --no-install-recommends libmysqlclient-dev

# 2. 使用官方脚本编译安装
docker-php-ext-install pdo_mysql
```

如果是 gd 拓展：
```
docker exec -it php85 bash
# 先装gd依赖库
apt-get update && apt-get install -y --no-install-recommends libfreetype6-dev libjpeg62-turbo-dev libpng-dev
# 配置编译参数
docker-php-ext-configure gd --with-freetype --with-jpeg
# 编译安装gd
docker-php-ext-install -j$(nproc) gd
```

如果是 redis 拓展，可以使用 PHP 的扩展包管理器 PECL 安装：
```
docker exec -it php85 bash
# pecl安装指定版本redis扩展
pecl install redis-7.4.10
# 注册ini配置，加载扩展
docker-php-ext-enable redis
```

Swoole（PECL 第三方扩展）:
```
docker exec -it php85 bash

apt-get update && apt-get install -y libssl-dev

pecl install swoole

docker-php-ext-enable swoole
```

#### composer

如果容器中没有 composer，需要安装 composer。可以执行下面的命令：
```
# 进入容器
docker exec -it php85 bash

# 安装依赖
apt-get update && apt-get install -y --no-install-recommends git unzip && rm -rf /var/lib/apt/lists/*

# 安装 composer
curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# 阿里云镜像（推荐用composer命令设置，比直接写json更稳妥）
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

但是要注意：Docker 容器是**分层 + 临时写入**，`docker exec` 进去装的东西，是写在**容器读写层**，**不是镜像里**。
- 只要你执行：`docker compose down`（停止并删除容器）
- 再 `docker compose up -d` 重新拉起
容器会基于原来的镜像重新新建，**composer、git、unzip 全部消失，又要重新装一遍！**
> 简单区分：
> - Dockerfile 构建镜像：写入镜像，容器删了重建也还在（永久固化）
> - exec 进容器手动安装：只在当前这个容器实例生效，容器销毁就没了（临时）

查看执行过程：
```
root@e0650bf43609:/var/www/html# curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
All settings correct for using Composer
Downloading...

Composer (version 2.11.1) successfully installed to: /usr/local/bin/composer
Use it: php /usr/local/bin/composer

root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# composer --version
Composer version 2.11.1 2026-10-30 14:52:10
PHP version 8.5.12 (/usr/local/bin/php)
Run the "diagnose" command to get more detailed diagnostics output.
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
root@e0650bf43609:/var/www/html#
```

#### Xdebug 扩展

Xdebug（PECL 第三方调试扩展）:
```
docker exec -it php85 bash
pecl install xdebug
docker-php-ext-enable xdebug
```

装好后还需要在 ini 里面配置调试参数（端口、idekey 等），不然只是加载了扩展，调试功能不生效：
```
xdebug.mode=debug
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
```

还需要其他一些配合。

### 补充

在容器中安装一些软件：
```
apt-get install -y nodejs

curl -fsSL https://www.npmjs.com/install.sh | sh

npm install -g yarn@1.22.22
```

## 执行查看

```
D:\develop\lnmp-web>docker compose up -d
[+] up 1/2
[+] up 1/2er php85 Starting                                                                      0.2s[+] up 1/2er php85 Starting                                                                                         0.3s[+] up 1/2er php85 Starting                                                                                         0.4s[+] up 2/2ner php85 Started                                                                                          0.4 ✔ Container php85 Started                                                                                          0.4s- Container nginx Starting                                                                                         0.4s ✔ Container nginx Started                                                                                          0.5s

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker ps
CONTAINER ID   IMAGE                 COMMAND                   CREATED         STATUS              PORTS                                                                                NAMES
b7844d81ab25   nginx:stable          "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes        0.0.0.0:8081->80/tcp, [::]:8081->80/tcp, 0.0.0.0:8444->443/tcp, [::]:8444->443/tcp   nginx
e0650bf43609   lnmp-web-php85        "docker-php-entrypoi…"   2 minutes ago   Up 2 minutes        0.0.0.0:9001->9000/tcp, [::]:9001->9000/tcp                                          php85
a30b1a6ccf85   centos9-nginx-php74   "sh -c 'mkdir -p /ru…"   3 months ago    Up About a minute   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp                                              centos9-nginx-php74
d5046d1e795e   redis:7.4             "docker-entrypoint.s…"   3 months ago    Up About a minute   0.0.0.0:6379->6379/tcp, [::]:6379->6379/tcp                                          redis74
2009b3504497   mysql:8.0             "docker-entrypoint.s…"   3 months ago    Up About a minute   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp                                          mysql8

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":80"
  TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       16648
  TCP    0.0.0.0:8081           0.0.0.0:0              LISTENING       16648
  TCP    127.0.0.1:8043         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8051         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8056         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8062         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8068         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8076         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8085         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8090         127.0.0.1:21151        TIME_WAIT       0
  TCP    127.0.0.1:8097         127.0.0.1:21151        TIME_WAIT       0
  TCP    192.168.30.101:2025    117.185.25.42:80       ESTABLISHED     40828
  TCP    192.168.30.101:8001    101.132.144.139:30975  TIME_WAIT       0
  TCP    192.168.30.101:8019    211.141.179.87:443     ESTABLISHED     14320
  TCP    192.168.30.101:8026    112.28.231.77:443      TIME_WAIT       0
  TCP    192.168.30.101:8036    112.28.231.77:443      TIME_WAIT       0
  TCP    192.168.30.101:8041    112.29.220.51:443      TIME_WAIT       0
  TCP    192.168.30.101:8047    112.28.231.77:443      TIME_WAIT       0
  TCP    192.168.30.101:8059    112.28.231.81:443      TIME_WAIT       0
  TCP    192.168.30.101:8064    36.150.160.206:443     ESTABLISHED     16364
  TCP    [::]:8080              [::]:0                 LISTENING       16648
  TCP    [::]:8081              [::]:0                 LISTENING       16648
  TCP    [::1]:8080             [::]:0                 LISTENING       12620
  TCP    [::1]:8081             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":443"
  TCP    192.168.30.101:2181    42.186.118.109:443     ESTABLISHED     8596
  TCP    192.168.30.101:3952    112.28.234.13:443      ESTABLISHED     14272
  TCP    192.168.30.101:5630    20.184.175.22:443      ESTABLISHED     6296
  TCP    192.168.30.101:5751    211.141.179.77:443     ESTABLISHED     16364
  TCP    192.168.30.101:7327    146.75.45.91:443       ESTABLISHED     16364
  TCP    192.168.30.101:7458    34.107.243.93:443      ESTABLISHED     16364
  TCP    192.168.30.101:7498    103.45.131.133:443     ESTABLISHED     30700
  TCP    192.168.30.101:7506    103.45.131.133:443     TIME_WAIT       0
  TCP    192.168.30.101:7623    120.253.253.225:443    TIME_WAIT       0
  TCP    192.168.30.101:7751    112.28.231.88:443      TIME_WAIT       0
  TCP    192.168.30.101:7752    112.28.231.88:443      TIME_WAIT       0
  TCP    192.168.30.101:7754    112.28.231.88:443      TIME_WAIT       0
  TCP    192.168.30.101:7763    39.136.231.205:443     TIME_WAIT       0
  TCP    192.168.30.101:7956    36.151.170.62:443      ESTABLISHED     16364
  TCP    192.168.30.101:8019    211.141.179.87:443     ESTABLISHED     14320
  TCP    192.168.30.101:8047    112.28.231.77:443      TIME_WAIT       0
  TCP    192.168.30.101:8059    112.28.231.81:443      TIME_WAIT       0
  TCP    192.168.30.101:8064    36.150.160.206:443     ESTABLISHED     16364
  TCP    192.168.30.101:8105    112.28.231.77:443      TIME_WAIT       0
  TCP    192.168.30.101:8118    112.29.220.51:443      TIME_WAIT       0
  TCP    192.168.30.101:8141    146.75.45.91:443       ESTABLISHED     16364
  TCP    192.168.30.101:8142    3.175.214.86:443       ESTABLISHED     16364
  TCP    192.168.30.101:8166    112.30.160.179:443     ESTABLISHED     14272
  TCP    192.168.30.101:8178    74.125.203.139:443     SYN_SENT        30700
  TCP    192.168.30.101:8179    74.125.203.139:443     SYN_SENT        30700
  TCP    192.168.30.101:8180    74.125.203.113:443     SYN_SENT        30700
  TCP    192.168.30.101:8181    74.125.203.113:443     SYN_SENT        30700
  TCP    192.168.30.101:8185    112.29.220.51:443      TIME_WAIT       0
  TCP    192.168.30.101:8190    140.82.114.26:443      ESTABLISHED     16364
  TCP    192.168.30.101:8200    104.18.95.41:443       ESTABLISHED     16364
  TCP    192.168.30.101:8201    104.18.95.41:443       TIME_WAIT       0
  TCP    192.168.30.101:8205    112.29.220.51:443      ESTABLISHED     13540
  TCP    192.168.30.101:8608    36.155.197.218:443     ESTABLISHED     17740
  TCP    192.168.30.101:9022    203.107.86.23:443      ESTABLISHED     32752
  TCP    192.168.30.101:11642   36.151.170.62:443      ESTABLISHED     16364
  TCP    192.168.30.101:11645   36.151.170.62:443      ESTABLISHED     16364
  TCP    192.168.30.101:13136   111.62.36.224:443      ESTABLISHED     16364

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":8080"
  TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       16648
  TCP    [::]:8080              [::]:0                 LISTENING       16648
  TCP    [::1]:8080             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":3306"
  TCP    0.0.0.0:3306           0.0.0.0:0              LISTENING       16648
  TCP    [::]:3306              [::]:0                 LISTENING       16648
  TCP    [::1]:3306             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":6379"
  TCP    0.0.0.0:6379           0.0.0.0:0              LISTENING       16648
  TCP    127.0.0.1:6379         127.0.0.1:7881         ESTABLISHED     16648
  TCP    127.0.0.1:7881         127.0.0.1:6379         ESTABLISHED     12516
  TCP    [::]:6379              [::]:0                 LISTENING       16648
  TCP    [::1]:6379             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":9000"

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":8081"
  TCP    0.0.0.0:8081           0.0.0.0:0              LISTENING       16648
  TCP    [::]:8081              [::]:0                 LISTENING       16648
  TCP    [::1]:8081             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":8444"
  TCP    0.0.0.0:8444           0.0.0.0:0              LISTENING       16648
  TCP    [::]:8444              [::]:0                 LISTENING       16648
  TCP    [::1]:8444             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>netstat -ano | findstr ":9001"
  TCP    0.0.0.0:9001           0.0.0.0:0              LISTENING       16648
  TCP    [::]:9001              [::]:0                 LISTENING       16648
  TCP    [::1]:9001             [::]:0                 LISTENING       12620

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker exec php85 php -v
PHP 8.5.12 (cli) (built: Nov  5 2026 07:20:38) (NTS)
Copyright (c) The PHP Group
Built by https://github.com/docker-library/php
Zend Engine v4.5.12, Copyright (c) Zend Technologies
    with Zend OPcache v8.5.12, Copyright (c), by Zend Technologies

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker exec php85 php -m
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
hash
iconv
json
lexbor
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_sqlite
Phar
posix
random
readline
Reflection
session
SimpleXML
SPL
sqlite3
standard
tokenizer
uri
xml
xmlreader
xmlwriter
Zend OPcache
zlib

[Zend Modules]
Zend OPcache


D:\develop\lnmp-web>
D:\develop\lnmp-web>docker logs php85
[08-Nov-2026 10:23:13] NOTICE: fpm is running, pid 1
[08-Nov-2026 10:23:13] NOTICE: ready to handle connections

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker exec nginx nginx -v
nginx version: nginx/1.30.6

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker exec nginx nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

D:\develop\lnmp-web>
D:\develop\lnmp-web>docker logs nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf is not a file or does not exist
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up

D:\develop\lnmp-web>
D:\develop\lnmp-web>
```


```
D:\develop\lnmp-web>docker exec -it php85 bash
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# php -m | grep -E "bcmath|ctype|fileinfo|mbstring|Zend OPcache|gd|intl|pdo_mysql|zip"
ctype
fileinfo
mbstring
Zend OPcache
Zend OPcache
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# php -i | grep opcache.enable
opcache.enable => On => On
opcache.enable_cli => Off => Off
opcache.enable_file_override => Off => Off
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# php -m | grep redis
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# php --ini
Configuration File (php.ini) Path: "/usr/local/etc/php"
Loaded Configuration File:         (none)
Scan for additional .ini files in: "/usr/local/etc/php/conf.d"
Additional .ini files parsed:      /usr/local/etc/php/conf.d/99-custom.ini

root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# node -v
v20.19.2
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# npm -v
9.2.0
root@e0650bf43609:/var/www/html#
root@e0650bf43609:/var/www/html# yarn -v
1.22.22
root@e0650bf43609:/var/www/html#
```


