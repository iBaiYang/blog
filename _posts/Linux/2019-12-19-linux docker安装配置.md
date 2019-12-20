---
layout: post
categories: Linux
title: Linux docker安装配置
meta: Linux docker安装配置
---
* content
{:toc}

### 正文

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

<https://docs.docker.com/install/linux/docker-ce/debian/>

<https://wiki.deepin.org/wiki/Docker>

干货满满！10分钟看懂Docker和K8S <https://my.oschina.net/jamesview/blog/2994112>

