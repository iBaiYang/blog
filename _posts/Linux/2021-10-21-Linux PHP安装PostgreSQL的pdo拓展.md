---
layout: post
categories: Linux
title: Linux PHP安装PostgreSQL的pdo拓展
meta: Linux PHP安装PostgreSQL的pdo拓展
---
* content
{:toc}

## 正文

一次项目开发中，接触到了Laravel8，PHP版本要求为7.4，数据库是PostgreSQL，头大，Laravel和PostgreSQL属于头次接触。
PHP可以通过pdo拓展像操作MySQL一样操作PostgreSQL，不过PHP要安装pgsql拓展。
服务器的环境他人已经部署好，但你对这个服务器一无所知。下面记录下经历。

项目composer.json文件：
```
{
    "name": "laravel/laravel",
    "type": "project",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "require": {
        "php": "^7.3|^8.0",
        "fruitcake/laravel-cors": "^2.0",
        "guzzlehttp/guzzle": "^7.0.1",
        "laravel/framework": "^8.54",
        "laravel/tinker": "^2.5",
        "maatwebsite/excel": "3.1.*",
        "predis/predis": "^1.1",
        "tymon/jwt-auth": "dev-develop"
    },
    "require-dev": {
        "facade/ignition": "^2.5",
        "fakerphp/faker": "^1.9.1",
        "laravel/sail": "^1.0.1",
        "mockery/mockery": "^1.4.2",
        "nunomaduro/collision": "^5.0",
        "phpunit/phpunit": "^9.3.3"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        },
        "files": [
            "app/Helpers/Helpers.php"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    "scripts": {
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover --ansi"
        ],
        "post-update-cmd": [
            "@php artisan vendor:publish --tag=laravel-assets --ansi"
        ],
        "post-root-package-install": [
            "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "@php artisan key:generate --ansi"
        ]
    },
    "extra": {
        "laravel": {
            "dont-discover": []
        }
    },
    "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

### 项目准备

在服务器`/var/www/html`目录下拉取项目文件
> git clone ***

加载vendor包
> composer install

环境配置文件准备：
> cp .env.example .env

把配置修改好。

修改指定目录权限
> chmod -R 0777 ./storage

### 配置Nginx

查看系统居然是Ubuntu：
```
root@localhost:/usr/local/src/# uname -a
Linux localhost 4.4.0-131-generic #157-Ubuntu SMP Thu Jul 12 15:51:36 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# cat /proc/version
Linux version 4.4.0-131-generic (buildd@lgw01-amd64-015) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.10) ) #157-Ubuntu SMP Thu Jul 12 15:51:36 UTC 2018
root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# cat /etc/issue
Ubuntu 16.04.5 LTS \n \l

root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.5 LTS
Release:	16.04
Codename:	xenial
root@localhost:/usr/local/src/#
```

简化请求，不需要DNS了，直接通过 IP:端口 方式提供服务。Nginx配置监听端口就可以了。

查看哪个端口可用。



查看php信息交互方式



查看配置文件位置。




重启Nginx
> nginx -s reload

### 安装pgsql拓展

#### 源码包方式

> cd /usr/local/src
> 
> wget http://pecl.php.net/get/PDO_PGSQL-1.0.2.tgz
> 
> gunzip PDO_PGSQL-1.0.2.tgz
> 
> tar -xf PDO_PGSQL-1.0.2.tar
> 
> cd PDO_PGSQL-1.0.2


```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis phpize
phpize: /usr/bin/phpize /usr/bin/phpize7.1 /usr/share/man/man1/phpize.1.gz
```

```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis php-config
php-config: /usr/bin/php-config7.1 /usr/bin/php-config /usr/share/man/man1/php-config.1.gz
```

> /usr/bin/phpize
> 
> ./configure --with-php-config=/usr/bin/php-config
>
> make && make install

查找php配置文件地址：
> whereis php.ini

或：
> find / -name php.ini

如：
```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis php.ini
php: /usr/bin/php7.4 /usr/bin/php /usr/bin/php7.1 /usr/lib/php /etc/php /usr/include/php /usr/share/php7.1-opcache 
/usr/share/php7.1-mysql /usr/share/php7.4-gd /usr/share/php7.4-mbstring /usr/share/php7.4-xml /usr/share/php7.4-curl 
/usr/share/php7.4-ldap /usr/share/php7.1-common /usr/share/php7.1-gd /usr/share/php7.1-xml /usr/share/php7.1-curl 
/usr/share/php7.1-imap /usr/share/php7.4-readline /usr/share/php7.1-readline /usr/share/php7.4-zip 
/usr/share/php7.1-mbstring /usr/share/php7.1-zip /usr/share/php7.1-xmlrpc /usr/share/php7.1-pgsql 
/usr/share/php7.1-json /usr/share/php7.4-opcache /usr/share/php7.4-mysql /usr/share/php7.4-common 
/usr/share/php7.1-gmp /usr/share/php /usr/share/php7.4-json /usr/share/man/man1/php.1.gz
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# 
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# find / -name php.ini
/etc/php/7.1/cli/php.ini
/etc/php/7.1/fpm/php.ini
/etc/php/7.1/apache2/php.ini
/etc/php/7.4/cli/php.ini
/etc/php/7.4/fpm/php.ini
/etc/php/7.4/apache2/php.ini
```

编辑
> vim /etc/php/7.4/cli/php.ini

文件最后写入：
```
extension=pgsql.so
extension=pdo_pgsql.so
```

查看拓展是否安装成功：
> php -m


#### apt方式

sudo add-apt-repository ppa:ondrej/php

sudo apt-get update

apt-get update 1>/dev/null 2>&1 &

sudo apt-get install php7.4-pdo_pgsql



### Docker实现

一个陌生的服务器环境，而且软件管理的很混乱，可以通过Docker容器实现一个新的纯净的环境。



<br/><br/><br/><br/><br/>
## 参考资料

add-apt-repository添加源 <https://blog.csdn.net/u012901451/article/details/16923855>

10 分钟上手 Vim，常用命令大盘点 <https://zhuanlan.zhihu.com/p/89042423>

Php7安装pdo_pgsql，pgsql扩展 <https://www.cnblogs.com/feixiablog/p/9249347.html>

PECL PDO_PGSQL <http://pecl.php.net/package/PDO_PGSQL>

tgz文件如何解压 <https://jingyan.baidu.com/article/0aa22375610d1088cd0d646b.html>



php7 安装pg_sql, pdo_pgsql扩展 <https://www.clarkhu.net/?p=9929>

deepin docker 容器安装php7.2扩展报错： Couldn't find any package by glob 'php7.2-*' <https://blog.csdn.net/one312/article/details/104949126>


