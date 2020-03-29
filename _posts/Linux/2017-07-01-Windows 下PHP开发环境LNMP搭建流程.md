---
layout: post
categories: Linux
title: Windows 下PHP开发环境LNMP搭建流程
meta: Windows 下PHP开发环境LNMP搭建流程
---
* content
{:toc}

### 正文

#### 安装Linux

在windows下，我们开发php应用时，一般会选择WAMP，但部署到服务器时，我们又选择的是LNMP，所以偶尔会导致应用运行环境不兼容的问题。所以最好我们开发环境也是LNMP的。下面说一下windows下LNMP环境搭建的流程：

首先，我们要安装VirtualBox和vagrant，这两个软件：

![]({{site.baseurl}}/images/20200329/20200329191533.jpg)

VirtualBox 是一款开源虚拟机软件；Vagrant是一个基于Ruby的工具，用于创建和部署虚拟化开发环境。 

我们要选择我们的虚拟化开发环境位置，因为已经在本地安装过一个虚拟开发环境了，所以这次安装第二个虚拟化开发环境。我们选择在D:/vagrant2下。我们要把将要安装的Linux包放在这个目录下，我这次用的box包是vagrant-centos-6.7.box，把它copy到D:/vagrant2/box下并重命名一下，如图：
    
![]({{site.baseurl}}/images/20200329/20200329191534.jpg)

接下来，我们按下win+R打开运行窗口，输入cmd调出命令行窗口：

![]({{site.baseurl}}/images/20200329/20200329191535.jpg)

我们cd到D:/vagrant2下：

![]({{site.baseurl}}/images/20200329/20200329191536.jpg)

用box add命令添加运行环境：

> vagrant box add centos67 box/centos67.box

![]({{site.baseurl}}/images/20200329/20200329191537.jpg)

用init初始化：

> vagrant init

![]({{site.baseurl}}/images/20200329/20200329191538.jpg)

初始化后，我们会在D:/vagrant2下看到新增了一个文件Vagrantfile：

![]({{site.baseurl}}/images/20200329/20200329191539.jpg)

我们需要修改这个配置文件，像这样：

```
Vagrant.configure("2") do |config|
config.vm.box = "centos67"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 3306, host: 3306
  config.vm.network "forwarded_port", guest: 9801, host: 9801
  config.vm.network "forwarded_port", guest: 9805, host: 9805
  config.vm.network "forwarded_port", guest: 9901, host: 9901
  config.vm.network "forwarded_port", guest: 9905, host: 9905
  config.vm.synced_folder "D:/www", "/var/www", create:true, owner:"vagrant", group:"vagrant"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "2048"
  vb.cpus = "2"
  end
```

![]({{site.baseurl}}/images/20200329/20200329191540.jpg)

![]({{site.baseurl}}/images/20200329/20200329191541.jpg)

接着用up启动：

> vagrant up

![]({{site.baseurl}}/images/20200329/20200329191543.jpg)

我们会在D:/vagrant2下看到新增了文件：

![]({{site.baseurl}}/images/20200329/20200329191544.jpg)

现在，我们的Linux已经安装好了，并且已经成功启动。

附带一句，我们可以用-help查看可用命令，用 box list命令可以查看有几个虚拟运行环境：
    
![]({{site.baseurl}}/images/20200329/20200329191542.jpg)

与Linux交流信息，比如安装php、nginx、mysql等，我们需要通过shell工具，我这里用的是putty：
   
![]({{site.baseurl}}/images/20200329/20200329191545.jpg)

双击运行，进行连接配置，ip为127.0.0.1，端口是2222，用户名为vagrant。保存配置为vagrant2：

![]({{site.baseurl}}/images/20200329/20200329191546.jpg)

点击open连接，输入密码：

> vagrant

；切换到root用户：

> sudo su

；用source命令，让/etc/profile文件立刻生效，而不用重新登录：

> source /etc/profile 

；如图：

![]({{site.baseurl}}/images/20200329/20200329191547.jpg)

#### 安装PHP：

到用户目录下： 

> cd /usr/local/src/

升级软件包： 

> rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm

安装php： 

> yum install php70w php70w-opcache php70w-cli php70w-devel php70w-fpm php70w-gd php70w-mbstring php70w-mcrypt php70w-mysql php70w-pear php70w-bcmath

一路确定：

> y

如图：

![]({{site.baseurl}}/images/20200329/20200329191548.jpg)

![]({{site.baseurl}}/images/20200329/20200329191549.jpg)

![]({{site.baseurl}}/images/20200329/20200329191550.jpg)

![]({{site.baseurl}}/images/20200329/20200329191551.jpg)

![]({{site.baseurl}}/images/20200329/20200329191552.jpg)

启动php服务：

> /etc/init.d/php-fpm start
 
![]({{site.baseurl}}/images/20200329/20200329191553.jpg)

至此，php安装为完成。

#### 安装zip

给linux安装zip： 

> yum install zip

![]({{site.baseurl}}/images/20200329/20200329191554.jpg)

#### 安装unzip

给linux安装unzip： 

> yum install unzip

![]({{site.baseurl}}/images/20200329/20200329191555.jpg)

#### 安装vim

给linux安装vim： 

> yum install vim

![]({{site.baseurl}}/images/20200329/20200329191556.jpg)

![]({{site.baseurl}}/images/20200329/20200329191557.jpg)

#### 安装Swoole拓展：

下载swoole：swoole-src-1.9.9.zip

copy到D:/www/下

move到当前目录前： 

> mv /var/www/swoole-src-1.9.9.zip ./

解压swoole压缩包：

> unzip swoole-src-1.9.9.zip

如图：
    
![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciEKvw5J4b)

进入swoole目录：cd swoole-src-1.9.9

给php添加动态拓展：phpize
    
![](http://s15.sinaimg.cn/mw690/001XbchKzy7ciF5hDFcbe)

源码安装前，配置： 

> ./configure
 
![](http://s9.sinaimg.cn/mw690/001XbchKzy7ciFwjVmU38)

编译，并安装：

> make && make install  

![](http://s16.sinaimg.cn/mw690/001XbchKzy7ciFMAfh59f)

到/etc/php.d/下： 

> cd /etc/php.d/

新建文件：

> touch swoole.ini

并写入： 

> echo 'extension=swoole.so' > swoole.ini
    
![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciGA19Gzeb)

至此：swoole安装完成。
    
#### 安装Nginx：

到上层目录下（即/etc）： 

> cd ../

vim编辑配置文件： 

> vim /etc/yum.repos.d/nginx.repo
            
![](http://s15.sinaimg.cn/mw690/001XbchKzy7ciHbQWRM1e)

写入并保存：

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

![](http://s11.sinaimg.cn/mw690/001XbchKzy7ciHkq6gyaa)

安装操作： 

> yum -y install nginx

![](http://s9.sinaimg.cn/mw690/001XbchKzy7ciIdDFwY38)

启动nginx服务器： 

> /etc/init.d/nginx start
      
![](http://s3.sinaimg.cn/mw690/001XbchKzy7ciRhRB2G82)

至此，nginx安装完成。
 
#### 安装MySQL

到用户目录下： 

> cd /usr/local/src/

下载mysql的包： 

> wget https://dev.mysql.com/get/mysql57-community-release-el6-11.noarch.rpm

升级软件包：

> rpm -Uvh mysql57-community-release-el6-11.noarch.rpm

![](http://s15.sinaimg.cn/mw690/001XbchKzy7ciIJFwmy7e)

创建配置文件： 

> touch /etc/yum.repos.d/mysql-community.repo

清空配置文件： 

> echo '' > /etc/yum.repos.d/mysql-community.repo

编辑配置文件： 

> vi /etc/yum.repos.d/mysql-community.repo

![](http://s5.sinaimg.cn/mw690/001XbchKzy7ciJqMG0sc4)

写入下面源码并保存：

```
写入下面源码并保存：
# Enable to use MySQL 5.6
[mysql56-community]
name=MySQL 5.6 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

![](http://s13.sinaimg.cn/mw690/001XbchKzy7ciJuEXko5c)

查看仓库列表中的mysql： 

> yum repolist enabled \| grep mysql

安装mysql： 

> yum install mysql-community-server 
            
![](http://s8.sinaimg.cn/mw690/001XbchKzy7ciK50Sj587)

一路确定：y
 
![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciNNpcN5bb)

mysql安装完成，我们启动它：

> /etc/init.d/mysqld start
 
![](http://s4.sinaimg.cn/mw690/001XbchKzy7ciO2zUjxe3)

MySQL安全配置向导：  

> mysql_secure_installation

![](http://s10.sinaimg.cn/mw690/001XbchKzy7ciOsHN6V19)

![](http://s16.sinaimg.cn/mw690/001XbchKzy7ciOxp2oL8f)

进入MySQL管理界面： 

> mysql -uroot -p

![](http://s6.sinaimg.cn/mw690/001XbchKzy7ciOX9MaN05)

选定mysql表：

> use mysql;

更新root权限：

> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;

退出mysql管理：

> quit;
            
![](http://s11.sinaimg.cn/mw690/001XbchKzy7ciPjkxRw2a)

至此，MySQL的所有安装和配置都完成了。

#### 站点配置

在D:/www目录下建设站点文件，如test项目

到nginx配置目录下： 

> cd /etc/nginx/conf.d/

编辑default.conf： 

> vi default.conf
            
![](http://s2.sinaimg.cn/mw690/001XbchKzy7ciQQe0ff81)

接着写入配置并保存：
 
```
server {
    set $rootDir  /var/www/test;
    listen 80;
    server_name test.host;
    index index.html index.htm index.php default.html default.htm default.php;
    root $rootDir;
    limit_rate_after 5m;
    limit_rate 512k;
    access_log  /var/www/test/logs/yyxt.log;

    location / {
        root   $rootDir;
        index  index.php default.php default.html index.html default.htm index.htm;
        if (!-e $request_filename) {
            rewrite  ^(.*)$  /index.php?s=$1  last;     #重写URL，省去index.php?
            break;
        }
    }

    location ~ \.php {      #去掉$，支持pathinfo
        root           $rootDir;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;     #增加这一句，支持pathinfo
        fastcgi_param PATH_INFO $fastcgi_path_info;    #增加这一句，支持pathinfo
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    location ~ \.(gif|jpg|jpeg|png|bmp|swf|ico)$ {
        root  $rootDir;
        access_log off;
    }
    location ~ \.(js|css)?$ {
        root  $rootDir;
        access_log off;
    }
    location ~ \.mp4$ {
        root  $rootDir;
        access_log off;
    }
}
```

如图：

![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciQMwLKH0b)

或者，只在default.conf中最下面加一行：include /var/www/vhost/*.conf;

![](http://s2.sinaimg.cn/mw690/001XbchKzy7gXn3gDZv11)

然后，在D:\www\vhost目录下新建后缀名为conf的文件，这样以后就可以在window窗口中管理站点配置文件了：
 
![](http://s8.sinaimg.cn/mw690/001XbchKzy7gXnaCbor67)

在相应文件中写入配置：
        
![](http://s13.sinaimg.cn/mw690/001XbchKzy7gXnzfIeMdc)

为了对比学习，可以看下yii2的配置：

![](http://s15.sinaimg.cn/mw690/001XbchKzy7gXnMJ6468e)

生成该站点操作日志目录：

> mkdir /var/www/test/logs

![](http://s10.sinaimg.cn/mw690/001XbchKzy7ciR8OjBT49)

到windows的安装目录下的修改hosts文件，如C:\Windows\System32\drivers\etc下

![](http://s2.sinaimg.cn/mw690/001XbchKzy7ciRL6Mhza1)

点击打开后添加一行：
 
![](http://s10.sinaimg.cn/mw690/001XbchKzy7ciRWG2wx19)

重启nginx服务器： 

> /etc/init.d/nginx restart

![](http://s11.sinaimg.cn/mw690/001XbchKzy7ciRm8dDQda)

至此，站点配置完成。
  
下面，我们查看下LNMP服务是否全部已启动：

> ps aux

![](http://s9.sinaimg.cn/mw690/001XbchKzy7ciS3J1WMc8)

![](http://s9.sinaimg.cn/mw690/001XbchKzy7ciS9eNNCa8)

全部启动了。

如果D:\www\test\index.php中写入的内容如下：
 
![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciSEoO034b)

我们访问一下test.host，访问内容如图：
 
![](http://s4.sinaimg.cn/mw690/001XbchKzy7ciSGYv4L23)

说明，该虚拟开发环境正常。接下来就是我们大展身手进行项目开发的操作了。

#### 附言

1、 exit退出root账户：

![](http://s3.sinaimg.cn/mw690/001XbchKzy7ciT1Zsn8d2)

windows命令行中

> vagrant halt

停止vagrant服务：
 
![](http://s9.sinaimg.cn/mw690/001XbchKzy7ciTg2G9268)

2、每日启动vagrant服务用vagrant up，不过要进入到相应的虚拟开发环境目录下：
  
![](http://s15.sinaimg.cn/mw690/001XbchKzy7ciToO5Yy4e)

别忘了在putty中用ps aux查看LNMP服务是否全部启动（请用root账户——sudo su），如果有未启动的，请手动启动，如：

![](http://s12.sinaimg.cn/mw690/001XbchKzy7ciTFxTAvdb)

3、另外，如果发现页面样式修改后，预览时总是没有效果，这是nginx配置导致的。我们要修改nginx.conf的sendfile on为off，修改后别忘了nginx的restart：
  
![](http://s6.sinaimg.cn/mw690/001XbchKzy7gXpeZtTn75)

![](http://s10.sinaimg.cn/mw690/001XbchKzy7gXpc22Tvc9)

![](http://s6.sinaimg.cn/mw690/001XbchKzy7gXpdlKuhd5)




<br/><br/><br/><br/><br/>
### 参考资料

<https://stackoverflow.com/questions/9479117/vagrant-virtualbox-apache2-strange-cache-behaviour#>

Nginx学习（一）之安装、控制、配置 <https://www.cnblogs.com/qiye5757/p/9614571.html>

