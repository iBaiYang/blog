---
layout: post
categories: Linux
title: linux 给php安装redis拓展
meta: linux 给php安装redis拓展
---
* content
{:toc}

## 正文

### apt安装

用apt方式安装：

> sudo apt-get install php7.1-redis 

如果提示：
```
注意，选中 'php-redis' 而非 'php7.1-redis'
php-redis 已经是最新版 (5.2.1+4.3.0-1+0~20200321.20+debian9~1.gbp9fe0ce)。
```

说明本机已安装了redis拓展。我们在php.ini文件中新增一行：

如在7.1命令行版本， `sudo vim /etc/php/7.1/cli/php.ini` ：
```
extension=redis.so
```

然后看一下效果：
```
php -m |grep redis 
```

提示：
```
PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/lib/php/20160303/redis.so' 
- /usr/lib/php/20160303/redis.so: undefined symbol: igbinary_serialize in Unknown on line 0
```

发现未能成功加载redis拓展。卸载了重新安装一次：

> sudo apt-get purge php-redis 

> sudo apt-get install php7.1-redis

发现安装报错：
```
/usr/sbin/phpenmod: 31: .: Can't open /usr/lib/php/php-helper
dpkg: 处理软件包 php-redis (--configure)时出错：
 子进程 已安装 post-installation 脚本 返回错误状态 2
在处理时有错误发生：
 php-redis
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

各种搜索后说是dpkg的问题，需要删除dpkg包信息：

> sudo rm /var/lib/dpkg/info/php-redis.*

再重复一次卸载安装：

> sudo apt-get purge php-redis 

> sudo apt-get install php7.1-redis

现在又报这个错：
```
正在设置 php-redis (5.2.1+4.3.0-1+0~20200321.20+debian9~1.gbp9fe0ce) ...
W: APT had planned for dpkg to do more than it reported back (0 vs 4).
   Affected packages: php-redis:amd64
```

搜索后说是包的问题，因为你的电脑是i386的，包却是amd64的，所以安装不上。

看来需要编译安装了。

上面只是一部分，具体可以参考下 [Linux php7.1 apt安装](https://ibaiyang.github.io/blog/linux/2019/12/19/Linux-php7.1-apt%E5%AE%89%E8%A3%85.html#%E5%AE%89%E8%A3%85redis%E6%8B%93%E5%B1%95) 。

### 编译安装

在/usr/local/src目录下，下载php-redis拓展包：

>sudo wget http://pecl.php.net/get/redis-5.2.1.tgz

解压：

> sudo tar zxvf redis-5.2.1.tgz

> cd redis-5.2.1

看一下phpize和php-config的位置：

> whereis phpize

```
phpize: /usr/bin/phpize /usr/bin/phpize7.1 /usr/share/man/man1/phpize.1.gz
```

> whereis php-config

```
php-config: /usr/bin/php-config /usr/bin/php-config7.1 /usr/share/man/man1/php-config.1.gz
```

编译配置：

```
> sudo phpize
> sudo ./configure --with-php-config=/usr/bin/php-config7.1
```

编译并安装：

> sudo make && make install

在相应php.ini文件中新增一行：
```
extension=redis.so
```

再看一下就有了：

```
php -m | grep redis
```

如果是php-fpm，需要重启服务：

> service php7.1-fpm restart





<br/><br/><br/><br/><br/>
## 参考资料

PECL redis <http://pecl.php.net/package/redis>

github phpredis <https://github.com/phpredis/phpredis/releases>

runoob PHP 使用 Redis <https://www.runoob.com/redis/redis-php.html>

php7.1(linux) redis 扩展安装 <https://blog.csdn.net/b1303110335/article/details/77054054>

Linux php7.1 apt安装 <https://ibaiyang.github.io/blog/linux/2019/12/19/Linux-php7.1-apt安装.html>

