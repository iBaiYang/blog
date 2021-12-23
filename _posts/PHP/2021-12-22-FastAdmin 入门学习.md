---
layout: post
categories: PHP
title: FastAdmin 入门学习
meta: FastAdmin 入门学习
---
* content
{:toc}

## 正文

大致先翻一遍文档，对项目框架有个印象 <https://doc.fastadmin.net/doc>

然后看一下视频，找找感觉 <https://www.bilibili.com/video/BV1Ji4y1V7ZV>

再就是项目实战，慢慢上手。

### 完整包安装

* 前往官网下载页面 <https://www.fastadmin.net/download.html> 下载完整包解压到你的项目目录
* 添加站点并绑定到项目中的public目录为运行目录，参考下面 nginx配置
* 访问 http://www.yoursite.com ，会自动重定向到 install.php 进行安装，填写信息中 站点名称 不能出现 admin 字样
* 为了安全，安装完成后会在public目录生成随机后台入口，请通过随机后台入口登录管理后台 （也可以不执行）

现在访问 admin 后台，无法进入，需要修改配置文件 `application/config.php` 中内容：
```
    // 默认模块名
    'default_module'         => 'admin',
    // 禁止访问模块
    'deny_module_list'       => ['common'],
```

### nginx配置

root指向项目的public目录。

`location /` 要重写：
```
    location / {
        if (!-e $request_filename){
            rewrite  ^(.*)$  /index.php?s=$1  last;  
            break;
        }
    }
```

页面刷新出问题时，是缓存的原因，把 禁用缓存 开启。

参考laragon实现的例子：
```
server {
    listen 80;
    server_name fastadmin_test1.test *.fastadmin_test1.test;
    root "G:/www/fastadmin_test1/public";
    
    index index.html index.htm index.php;
 
    location / {
        if (!-e $request_filename){
            rewrite  ^(.*)$  /index.php?s=$1  last;  
            break;
        }
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass php_upstream;		
        #fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
	
	
    charset utf-8;
	
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }
    location ~ /\.ht {
        deny all;
    }
}

# This file is auto-generated.
# If you want Laragon to respect your changes, just remove the [auto.] prefix
# If you want to use SSL, enable it at: Menu > Nginx > SSL > Enabled
```

## 参考资料

FastAdmin 官方文档 <https://doc.fastadmin.net/doc>

写代码的猫叔：fastadmin系列教程:小白学习之路 <https://www.bilibili.com/video/BV1Ji4y1V7ZV>

FastAdmin开发系列之视频教程 <https://www.fastadmin.net/video.html>



