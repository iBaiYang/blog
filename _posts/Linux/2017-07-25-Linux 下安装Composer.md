---
layout: post
categories: Linux
title: Linux 下安装Composer
meta: 记录一下 Linux 下安装安装Composer流程
---
* content
{:toc}

### 正文

按照下面的参考地址一步一步安装就好，顺便附一下自己安装的流程图：

到/usr/local/src下操作：

```
下载composer安装包：
> php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

校验下载的包，可以不执行：
> php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

安装：
> php composer-setup.php

移除安装包：
> php -r "unlink('composer-setup.php');" 
```

（捎带一提：php -r 意思是  Run PHP code without using script tags，<?...?> 也就是不必使用前后闭合标签）

![]({{site.baseurl}}/images/20191114/20191114105445.jpeg)

（从这里可以看到，我把composer安装到了/home目录下。里面有多次错误示范，可以注意辨别学习。）

现在安装好了，不过使用时必须如下命令：
> php /usr/local/src/composer.phar install ***

install、update 等等，不方便，我们要把composer命令加入环境变量：
> vim /etc/profile

vim 修改/etc/profile文件的内容，以设置环境变量（在文件最后加上下面这句，这样就可以用shell调用composer了）：
```
export PATH="$PATH:/usr/local/src"
```

也可以直接移动到环境搜索目录下，重命名为composer：
> mv composer.phar /usr/local/bin/composer

设置包源：
> composer config -g repo.packagist composer https://packagist.phpcomposer.com

这个包源虽然是中国区的，但服务可能不稳定，可以换成阿里云的：
> composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

查看版本：
> composer --version

![]({{site.baseurl}}/images/20191114/20191114105457.jpeg)

#### 其他问题

在安装过程中，可能会碰到这个错误提示：

```
The phar extension is missing.
Install it or recompile php without --disable-phar
```

phar拓展缺失。我们要给php安装这个拓展：

> sudo apt install php7.1-phar

修改php.ini文件：

> sudo find / -name php.ini

> sudo vim /etc/php/7.1/cli/php.ini 

加入一行：

```
extension=phar.so 
```

然后就可以了。

<br/><br/><br/><br/><br/>
### 参考资料

<https://getcomposer.org/download/>

<http://blog.csdn.net/AnglePYou/article/details/69568478>

<http://jingyan.baidu.com/article/a65957f4d23de824e77f9b70.html>

<http://blog.csdn.net/yangbbenyang/article/details/52457666>

<https://pkg.phpcomposer.com/>

<https://pkg.phpcomposer.com/>

Composer简介 <https://docs.phpcomposer.com/00-intro.html>

PHP 的命令行模式  <https://www.cnblogs.com/myjavawork/articles/1869205.html>

Linux /etc/profile文件详解及修改后如何立即生效（使用source命令） <https://blog.csdn.net/weixin_38233274/article/details/80092837>
	
Linux下配置环境变量最常用的两种方法—— .bashrc 和 /etc/profile <https://blog.csdn.net/sun8112133/article/details/79901527>

