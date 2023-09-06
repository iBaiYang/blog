---
layout: post
categories: Composer
title: Linux 下安装Composer
meta: 记录一下 Linux 下安装安装Composer流程
---
* content
{:toc}

## 正文

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

捎带一提：`php -r` 意思是  Run PHP code without using script tags，<?...?> 也就是不必使用前后闭合标签

安装指定版本composer `php composer-setup.php --version=1.6.5`

过程图：

![]({{site.baseurl}}/images/20191114/20191114105445.jpeg)

（从这里可以看到，我把composer安装到了/home目录下。里面有多次错误示范，可以注意辨别学习。）

现在安装好了，不过使用时必须如下命令：
> php /usr/local/src/composer.phar install ***

install、update 等等，不方便，我们要把composer命令加入环境变量。

查看PATH路径：
> cat /etc/profile

输出：
```
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "`id -u`" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin"
fi
export PATH

if [ "$PS1" ]; then
  if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
tty | egrep -q tty[1-6] && export LC_ALL=C
```

也可以直接移动到环境搜索目录下，重命名为composer (这样就可以用shell调用composer了)：
> sudo mv composer.phar /usr/bin/composer

查看版本：
> composer -v

输出：
```
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 2.0.8 2020-12-03 17:20:38
...
```

设置包源：
> composer config -g repo.packagist composer https://packagist.phpcomposer.com

这个包源虽然是中国区的，但服务可能不稳定，可以换成阿里云的（但不支持Composer2）：
> composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

查看当前源地址：
> composer config -g -l repo.packagist


### 其他问题

在安装过程中，可能会碰到这个错误提示：
```
The phar extension is missing.
Install it or recompile php without --disable-phar
```

phar拓展缺失。我们要给php安装这个拓展：
> sudo apt install php7.1-phar

定位 php.ini 文件位置：
> sudo find / -name php.ini

修改php.ini文件：
> sudo vim /etc/php/7.1/cli/php.ini 

加入一行：
```
extension=phar.so 
```

然后就可以了。











<br/><br/><br/><br/><br/>
## 参考资料

Composer 官网 <https://www.phpcomposer.com/>

Composer 文档 <https://docs.phpcomposer.com/>

镜像官网 <https://packagist.org/>

中国镜像官网 <https://pkg.phpcomposer.com/>

中国全量镜像 <https://pkg.xyz/>

fxp/composer-asset-plugin <https://github.com/fxpio/composer-asset-plugin>

Asset Packagist 官网 <https://asset-packagist.org/>

Asset Packagist 中文站 <https://asset-packagist.cn/>

<https://getcomposer.org/download/>

<http://blog.csdn.net/AnglePYou/article/details/69568478>

<http://jingyan.baidu.com/article/a65957f4d23de824e77f9b70.html>

<http://blog.csdn.net/yangbbenyang/article/details/52457666>

Composer简介 <https://docs.phpcomposer.com/00-intro.html>

PHP 的命令行模式  <https://www.cnblogs.com/myjavawork/articles/1869205.html>

Linux /etc/profile文件详解及修改后如何立即生效（使用source命令） <https://blog.csdn.net/weixin_38233274/article/details/80092837>
    
Linux下配置环境变量最常用的两种方法—— .bashrc 和 /etc/profile <https://blog.csdn.net/sun8112133/article/details/79901527>

阿里云 Composer 全量镜像 <https://developer.aliyun.com/composer>

国内的Composer全量镜像汇总 <https://www.cnblogs.com/huliuwa/p/12028878.html>