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

在nginx配置目录下加入一个文件`/web/nginx/conf/vhost/blog.com.conf`, 
```
server {
    listen   80;
    server_name   blog.com;

    location ^~ /.well-known/acme-challenge/ {
       default_type "text/plain";
       root     /var/www/html/blog;
    }

    location = /.well-known/acme-challenge/ {
       return 404;
    }
}
```

这两个location配置是为了通过 Let’s Encrypt 的验证，验证域名归属。你也可以不加这个文件。

第三点:使用certbot申请证书

使用方法：`certbot certonly --webroot -w [Web站点目录] -d [站点域名] -m [联系人email地址] --agree-tosde`

邮箱最好是真的 因为证书过期人家好通知你

如：
```
sudo certbot certonly --webroot -w /var/www/html/blog -d blog.com -m lili@google.com --agree-tos
```

输出：
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for blog.com
Using the webroot path /var/www/html/blog for all unmatched domains.
Waiting for verification...
Cleaning up challenges
Subscribe to the EFF mailing list (email: lili@google.com).
Starting new HTTPS connection (1): supporters.eff.org

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/blog.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/blog.com/privkey.pem
   Your cert will expire on 2020-11-27. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

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

命令执行过程中，中间会出现选项，让选择，用`--agree-tosde` 参数可以跳过这些选项，一步到位。可以不输入，一步一步选择。

**证书位置**
证书的保存位置在：
```
ll /etc/letsencrypt/live/blog.com/

#用户证书
lrwxrwxrwx 1 root root  44 Aug 29 23:34 cert.pem -> ../../archive/blog.com/cert1.pem
#中间证书  
lrwxrwxrwx 1 root root  45 Aug 29 23:34 chain.pem -> ../../archive/blog.com/chain1.pem
#证书链chain.pem + cert.pem  
lrwxrwxrwx 1 root root  49 Aug 29 23:34 fullchain.pem -> ../../archive/blog.com/fullchain1.pem
#证书私钥
lrwxrwxrwx 1 root root  47 Aug 29 23:34 privkey.pem -> ../../archive/blog.com/privkey1.pem
```

**证书有效期**
```
openssl x509 -noout -dates -in /etc/letsencrypt/live/blog.com/cert.pem

输出：
notBefore=Aug 29 14:34:18 2020 GMT
notAfter=Nov 27 14:34:18 2020 GMT
```

#### 生成Perfect Forward Security（PFS）键值

PFS（perfect forward secrecy），中文可叫做完全前向保密。要求一个密钥只能访问由它所保护的数据；
用来产生密钥的元素一次一换，不能再产生其他的密钥；一个密钥被破解，并不影响其他密钥的安全性。

```
#创建目录
mkdir /etc/ssl/private/ -p

#执行命令
cd /etc/ssl/private/
openssl dhparam 2048 -out dhparam.pem
```

需要等待一段时间。

#### 将证书目录挂载到nginx容器中

我们nginx一般运行在docker容器中，可以用docker命令挂载到容器：
```
docker run \
  --name server-nginx \
  -p 80:80 \
  -p 443:443 \
  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/ssl:/etc/ssl \
  -v /etc/letsencrypt:/etc/letsencrypt \
  --link server-phpfpm:php \
  -d nginx
```

如果报oci runtime error，可以把`-v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \`去掉后再执行。

如果你的nginx容器在运行中，可以先停止删除后重新创建：
```
docker ps -a

docker stop server-nginx

docker rm server-nginx
```

#### 配置nginx

我们配置项目的nginx配置文件，如 `/web/nginx/conf/vhost/blog.com.conf` :
```
server {
    listen 443 ssl;
    server_name  blog.com www.blog.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    
    # letsencrypt生成的文件
    ssl_certificate      /etc/letsencrypt/live/blog.com/fullchain.pem;
    ssl_certificate_key  /etc/letsencrypt/live/blog.com/privkey.pem;
    ssl_dhparam /etc/ssl/private/dhparam.pem;
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_prefer_server_ciphers  on;
    
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

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
        root           /var/www/html/blog;
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

server {
    listen   80;
    server_name  blog.com www.blog.com;
    return 301 https://$server_name$request_uri;            
}
```

说明：
```
listen 443 ssl：开启443端口监听
ssl_certificate：包含服务器证书的全部证书链文件
ssl_certificate_key：私钥的位置
ssl_dhparam：上面生成的2048位 DH parameters位置
ssl_ciphers：2048位 DH parameters里的值
ssl_prefer_server_ciphers为on表示开启
ssl_protocols 允许使用的协议
ssl_session_cache 所有工作进程之间共享缓存。缓存大小以字节为单位指定；一兆字节可以存储大约4000个session。
ssl_session_timeout  过期时间，10m表示10分钟
```

下面那个server配置是 http 的自动访问跳转，将http的访问都自动重定向到https。

重启nginx容器。

补充说一下。

关于ssl_protocols：SSL v2 及以下已经不安全了，请使用 TLS v1 以上版本（TLS 协议是在 SSL v3 协议基础上设计的）。
之所以把 SSL v3 也关了是因为 TLS 1.0 会被降级攻击，降级到 SSL v3 之后，我们之前提到的 Forward Secrecy（前向加密）就失效了。
另外， SSL v3 存在 POODLE 漏洞，这是关闭它的另一个主要理由。
`ssl_protocols TLSv1 TLSv1.1 TLSv1.2;`，只允许使用 TLS 协议。

关于ssl_session_cache：网站启用https后，会加剧服务器的负担。每次新的TLS连续都需要握手，以便创建共享的加密密钥，
在TCP三次握手之上还需要两个来回。
传统的http使用TCP三次握手建立连接，而SSL和TLS在这个基础上还需要9个握手包，所以这个负担显而易见。
不过，通过重用Session提高https的性能，TLS有几个特点可以抵消额外的握手：重用一个Session。
有两个标准会话重用机制：session IDs (RFC 5246) 和 session tickets (RFC 5077)，使用其中一个技术，
一个客户端可以重用之前创建的会话，这个会话是之前和服务器进行握手成功的，这样可以减少一次来回过程。

#### 设置定时任务自动更新证书 （待完成）

第五点自动更新

可以使用crontab定时更新，例如：

每月1号5时执行执行一次更新，并重启nginx服务器

```
00 05 01 * * /usr/bin/certbot renew --quiet && /bin/systemctl restart nginx
```

```
00 05 01 * * sudo /usr/bin/certbot renew --quiet && sudo docker restart nginx
```


<br/><br/><br/><br/><br/>
### 参考资料

Certbot官网 <https://certbot.eff.org/>

HTTPS 簡介及使用官方工具 Certbot 配置 Let’s Encrypt SSL 安全證書詳細教程 <https://linuxstory.org/deploy-lets-encrypt-ssl-certificate-with-certbot/zh-tw/>

centos7下docker部署nginx使用let's encrypt免费证书 <https://blog.csdn.net/li_fighting/article/details/78679743>

HTTPs setup - Certbot + Docker + Nginx <https://www.jianshu.com/p/a4692f1e3208>

CentOS7 通过certbot脚本安装使用 Let’ s Encrypt <https://www.jianshu.com/p/43e74cddba45>

实战申请Let's Encrypt永久免费SSL证书过程教程及常见问题 <https://www.laozuo.org/7676.html>

使用ssl_session_cache优化https下Nginx的性能 <http://www.361way.com/nginx-ssl-session-cache/6306.html>
