---
layout: post
categories: Linux
title: Linux HTTPS实现
meta: Linux HTTPS实现
---
* content
{:toc}

### 正文

#### 配置域名

第一点：你要有一个可以访问的域名 而且能访问到你的服务器

#### 安装certbot

第二点：安装certbot

> yum install -y epel-release

> yum install -y certbot

#### 申请证书

第三点:使用certbot申请证书

使用方法：`certbot certonly --webroot -w [Web站点目录] -d [站点域名] -m [联系人email地址] --agree-tosde`

邮箱最好是真的 因为证书过期人家好通知你

**异常处理**

申请时可能报错：
```
usage: 
  certbot [SUBCOMMAND] [options] [-d DOMAIN] [-d DOMAIN] ...

Certbot can obtain and install HTTPS/TLS/SSL certificates.  By default,
it will attempt to use a webserver both for obtaining and installing the
certificate. 
certbot: error: unrecognized arguments: --agree-tosde
```

#### 配置nginx

第四点 ****配置nginx使用证书开通https站点


#### 自动更新

第五点自动更新

可以使用crontab定时更新，例如：

每月1号5时执行执行一次更新，并重启nginx服务器

```
00 05 01 * * /usr/bin/certbot renew --quiet && /bin/systemctl restart nginx
```



<br/><br/><br/><br/><br/>
### 参考资料

Certbot官网 <https://certbot.eff.org/>

CentOS7 通过certbot脚本安装使用 Let’ s Encrypt <https://www.jianshu.com/p/43e74cddba45>

实战申请Let's Encrypt永久免费SSL证书过程教程及常见问题 <https://www.laozuo.org/7676.html>

