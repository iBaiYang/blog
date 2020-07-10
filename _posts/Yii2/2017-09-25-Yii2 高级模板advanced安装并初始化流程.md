---
layout: post
categories: Yii2
title: Yii2 高级模板advanced安装并初始化流程
meta: Yii2 高级模板advanced安装并初始化流程
---
* content
{:toc}

### 正文

<http://blog.sina.com.cn/s/blog_6aba78b40102x55u.html>

下面流程：

先安装yii2包，有多种方式，或composer、或归档文件等。这里举一下composer的例子：

> composer global require "fxp/composer-asset-plugin:~1.1"

![]({{site.baseurl}}/images/20200417/20200417132730.jpeg)

这一步命令安装 Composer asset plugin， 它是通过 Composer 管理 bower 和 npm 包所必须的，此命令全局生效，一劳永逸。

composer create-project --prefer-dist yiisoft/yii2-app-advanced yii2_test

第一个箭头所指token那里，需要在github生成：

setting->Developer settings->Personal access tokens->Generate new token按钮，全勾生成，复制生成的字符串后，在箭头那里粘贴，回车。

![]({{site.baseurl}}/images/20200417/20200417132731.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132732.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132733.jpeg)

运行途中遇到bug类问题，可以去这看一下：

<http://blog.csdn.net/u014175572/article/details/55510825>

<http://blog.csdn.net/zqtsx/article/details/41517965>

接着域名相关：

本地开发环境域名配置：

windows下  C:\Windows\System32\drivers\etc\hosts

linux下  /etc/hosts

重定向配置：

127.0.0.1 yii2_advanced_backend.host

127.0.0.1 yii2_advanced_frontend.host

vhost配置：

/etc/nginx/nginx.conf

![]({{site.baseurl}}/images/20200417/20200417132734jpeg)

```php
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name yii2_advanced_backend.host;
    root        /var/www/yii2_advanced/backend/web;
    index       index.php;

    access_log  /var/www/yii2_advanced/backend/web/log/access.log;
    error_log   /var/www/yii2_advanced/backend/web/log/error.log;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # uncomment to avoid processing of calls to non-existing static files by Yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

![]({{site.baseurl}}/images/20200417/20200417132735.jpeg)

开始初始化：

运行项目根目录下的init文件

> php init

![]({{site.baseurl}}/images/20200417/20200417132736.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132737.jpeg)

配置数据库：

![]({{site.baseurl}}/images/20200417/20200417132738.jpeg)

数据库迁移：

> php yii migrate

![]({{site.baseurl}}/images/20200417/20200417132739.jpeg)




<br/><br/><br/><br/><br/>
### 参考资料 

<http://www.weixistyle.com/yii2-video-play.php?chapter=3.1&title=博客系统需求>

