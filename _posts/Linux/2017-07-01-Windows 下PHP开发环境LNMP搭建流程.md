---
layout: post
categories: Linux
title: Windows 下PHP开发环境LNMP搭建流程
meta: Windows 下PHP开发环境LNMP搭建流程
---
* content
{:toc}

## 正文

### 安装Linux

在windows下，我们开发php应用时，一般会选择WAMP，但部署到服务器时，我们又选择的是LNMP，
所以偶尔会导致应用运行环境不兼容的问题。所以最好我们开发环境也是LNMP的。下面说一下windows下LNMP环境搭建的流程：

首先，我们要安装VirtualBox和vagrant，这两个软件：

![]({{site.baseurl}}/images/20200329/20200329191533.jpg)

VirtualBox 是一款开源虚拟机软件；Vagrant是一个基于Ruby的工具，用于创建和部署虚拟化开发环境。 

我们要选择我们的虚拟化开发环境位置，因为已经在本地安装过一个虚拟开发环境了，所以这次安装第二个虚拟化开发环境。
我们选择在D:/vagrant2下。我们要把将要安装的Linux包放在这个目录下，我这次用的box包是vagrant-centos-6.7.box，
把它copy到D:/vagrant2/box下并重命名一下，如图：
    
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

切换到root用户：

> sudo su

用source命令，让/etc/profile文件立刻生效，而不用重新登录：

> source /etc/profile 

如图：

![]({{site.baseurl}}/images/20200329/20200329191547.jpg)

### 安装PHP：

到软件资源目录下： 
> cd /usr/local/src/

切换yum源：
> rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm

如果失败，换成国内Yum源试一下。这里可能会碰到选哪个源、证书过期等问题，一个一个解决吧。

安装php及其拓展： 
> yum install php70w php70w-common php70w-fpm php70w-opcache php70w-cli php70w-devel php70w-mysql php70w-gd php70w-mbstring php70w-mcrypt php70w-pear php70w-bcmath php70w-pecl-imagick php70w-pecl-redis php70w-pecl-memcached

输出：
```
[root@localhost src]# yum install php70w php70w-common php70w-fpm php70w-opcache php70w-cli php70w-devel php70w-mysql php70w-gd php70w-mbstring php70w-mcrypt php70w-pear php70w-bcmath php70w-pecl-imagick php70w-pecl-redis php70w-pecl-memcached
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
Could not get metalink https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=x86_64 error was
14: problem making ssl connection
 * base: mirrors.aliyun.com
 * epel: ftp.lysator.liu.se
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
 * webtatic: uk.repo.webtatic.com
http://mirrors.aliyun.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 22 - "The requested URL returned error: 404 Not Found"
Trying other mirror.
http://mirrors.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 12] Timeout on http://mirrors.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: (28, 'connect() timed out!')
Trying other mirror.
http://mirrors.cloud.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'mirrors.cloud.aliyuncs.com'"
Trying other mirror.
http://mirrors.aliyun.com/centos/6/extras/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 22 - "The requested URL returned error: 404 Not Found"
Trying other mirror.
http://mirrors.aliyuncs.com/centos/6/extras/x86_64/repodata/repomd.xml: [Errno 12] Timeout on http://mirrors.aliyuncs.com/centos/6/extras/x86_64/repodata/repomd.xml: (28, 'connect() timed out!')
Trying other mirror.
http://mirrors.cloud.aliyuncs.com/centos/6/extras/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'mirrors.cloud.aliyuncs.com'"
Trying other mirror.
puppetlabs-pc1                                           | 2.5 kB     00:00
puppetlabs-pc1/primary_db                                | 236 kB     00:00
http://mirrors.aliyun.com/centos/6/updates/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 22 - "The requested URL returned error: 404 Not Found"
Trying other mirror.
http://mirrors.aliyuncs.com/centos/6/updates/x86_64/repodata/repomd.xml: [Errno 12] Timeout on http://mirrors.aliyuncs.com/centos/6/updates/x86_64/repodata/repomd.xml: (28, 'connect() timed out!')
Trying other mirror.
http://mirrors.cloud.aliyuncs.com/centos/6/updates/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'mirrors.cloud.aliyuncs.com'"
Trying other mirror.
webtatic                                                 | 3.6 kB     00:00
webtatic/primary_db                                      | 229 kB     00:00
Resolving Dependencies
--> Running transaction check
---> Package php70w.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: httpd-mmn = 20051115 for package: php70w-7.0.33-1.w6.x86_64
--> Processing Dependency: httpd for package: php70w-7.0.33-1.w6.x86_64
---> Package php70w-bcmath.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-cli.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-common.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-devel.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: pcre-devel(x86-64) for package: php70w-devel-7.0.33-1.w6.x86_64
--> Processing Dependency: automake for package: php70w-devel-7.0.33-1.w6.x86_64
--> Processing Dependency: autoconf for package: php70w-devel-7.0.33-1.w6.x86_64
---> Package php70w-fpm.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-gd.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: libpng12.so.0(PNG12_0)(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libjpeg.so.62(LIBJPEG_6.2)(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libpng12.so.0()(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libjpeg.so.62()(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libfreetype.so.6()(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libXpm.so.4()(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
--> Processing Dependency: libX11.so.6()(64bit) for package: php70w-gd-7.0.33-1.w6.x86_64
---> Package php70w-mbstring.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-mcrypt.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: libmcrypt.so.4()(64bit) for package: php70w-mcrypt-7.0.33-1.w6.x86_64
--> Processing Dependency: libltdl.so.7()(64bit) for package: php70w-mcrypt-7.0.33-1.w6.x86_64
---> Package php70w-mysql.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: php70w-pdo(x86-64) for package: php70w-mysql-7.0.33-1.w6.x86_64
---> Package php70w-opcache.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-pear.noarch 1:1.10.4-1.w6 will be installed
--> Processing Dependency: php70w-xml for package: 1:php70w-pear-1.10.4-1.w6.noarch
--> Processing Dependency: php70w-posix for package: 1:php70w-pear-1.10.4-1.w6.noarch
---> Package php70w-pecl-imagick.x86_64 0:3.4.3-1.w6 will be installed
--> Processing Dependency: libMagickWand.so.5()(64bit) for package: php70w-pecl-imagick-3.4.3-1.w6.x86_64
--> Processing Dependency: libMagickCore.so.5()(64bit) for package: php70w-pecl-imagick-3.4.3-1.w6.x86_64
---> Package php70w-pecl-memcached.x86_64 0:3.0.4-1.w6 will be installed
--> Processing Dependency: php-pecl-igbinary(x86-64) for package: php70w-pecl-memcached-3.0.4-1.w6.x86_64
--> Processing Dependency: libmemcachedutil.so.2()(64bit) for package: php70w-pecl-memcached-3.0.4-1.w6.x86_64
--> Processing Dependency: libmemcached.so.11()(64bit) for package: php70w-pecl-memcached-3.0.4-1.w6.x86_64
---> Package php70w-pecl-redis.x86_64 0:3.1.6-1.w6 will be installed
--> Running transaction check
---> Package ImageMagick.x86_64 0:6.7.2.7-2.el6 will be installed
--> Processing Dependency: libwmflite-0.2.so.7()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libwmf-0.2.so.7()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libtiff.so.3()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: librsvg-2.so.2()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: liblcms.so.1()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libjasper.so.1()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libgs.so.8()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libgdk_pixbuf-2.0.so.0()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libfontconfig.so.1()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libcairo.so.2()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libXt.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libXext.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libSM.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libImath.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libIlmThread.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libIlmImf.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libIex.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libICE.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
--> Processing Dependency: libHalf.so.6()(64bit) for package: ImageMagick-6.7.2.7-2.el6.x86_64
---> Package autoconf.noarch 0:2.63-5.1.el6 will be installed
---> Package automake.noarch 0:1.11.1-4.el6 will be installed
---> Package freetype.x86_64 0:2.3.11-15.el6_6.1 will be installed
---> Package httpd.x86_64 0:2.2.15-47.el6.centos will be installed
--> Processing Dependency: httpd-tools = 2.2.15-47.el6.centos for package: httpd-2.2.15-47.el6.centos.x86_64
--> Processing Dependency: apr-util-ldap for package: httpd-2.2.15-47.el6.centos.x86_64
--> Processing Dependency: /etc/mime.types for package: httpd-2.2.15-47.el6.centos.x86_64
--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.2.15-47.el6.centos.x86_64
--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.2.15-47.el6.centos.x86_64
---> Package libX11.x86_64 0:1.6.0-6.el6 will be installed
--> Processing Dependency: libX11-common = 1.6.0-6.el6 for package: libX11-1.6.0-6.el6.x86_64
--> Processing Dependency: libxcb.so.1()(64bit) for package: libX11-1.6.0-6.el6.x86_64
---> Package libXpm.x86_64 0:3.5.10-2.el6 will be installed
---> Package libjpeg-turbo.x86_64 0:1.2.1-3.el6_5 will be installed
---> Package libmcrypt.x86_64 0:2.5.8-9.el6 will be installed
---> Package libmemcached10.x86_64 0:1.0.16-1.ius.el6 will be installed
---> Package libpng.x86_64 2:1.2.49-1.el6_2 will be installed
---> Package libtool-ltdl.x86_64 0:2.2.6-15.5.el6 will be installed
---> Package pcre-devel.x86_64 0:7.8-7.el6 will be installed
---> Package php70w-pdo.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-pecl-igbinary.x86_64 0:2.0.5-1.w6 will be installed
---> Package php70w-process.x86_64 0:7.0.33-1.w6 will be installed
---> Package php70w-xml.x86_64 0:7.0.33-1.w6 will be installed
--> Processing Dependency: libxslt.so.1(LIBXML2_1.0.24)(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libxslt.so.1(LIBXML2_1.0.22)(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libxslt.so.1(LIBXML2_1.0.18)(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libxslt.so.1(LIBXML2_1.0.13)(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libxslt.so.1(LIBXML2_1.0.11)(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libxslt.so.1()(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Processing Dependency: libexslt.so.0()(64bit) for package: php70w-xml-7.0.33-1.w6.x86_64
--> Running transaction check
---> Package OpenEXR-libs.x86_64 0:1.6.1-8.1.el6 will be installed
---> Package apr.x86_64 0:1.3.9-5.el6_2 will be installed
---> Package apr-util.x86_64 0:1.3.9-3.el6_0.1 will be installed
---> Package apr-util-ldap.x86_64 0:1.3.9-3.el6_0.1 will be installed
---> Package cairo.x86_64 0:1.8.8-6.el6_6 will be installed
--> Processing Dependency: libpixman-1.so.0()(64bit) for package: cairo-1.8.8-6.el6_6.x86_64
--> Processing Dependency: libXrender.so.1()(64bit) for package: cairo-1.8.8-6.el6_6.x86_64
---> Package fontconfig.x86_64 0:2.8.0-5.el6 will be installed
---> Package gdk-pixbuf2.x86_64 0:2.24.1-6.el6_7 will be installed
---> Package ghostscript.x86_64 0:8.70-21.el6 will be installed
--> Processing Dependency: urw-fonts >= 1.1 for package: ghostscript-8.70-21.el6.x86_64
--> Processing Dependency: ghostscript-fonts for package: ghostscript-8.70-21.el6.x86_64
--> Processing Dependency: libcupsimage.so.2()(64bit) for package: ghostscript-8.70-21.el6.x86_64
--> Processing Dependency: libcups.so.2()(64bit) for package: ghostscript-8.70-21.el6.x86_64
---> Package httpd-tools.x86_64 0:2.2.15-47.el6.centos will be installed
---> Package ilmbase.x86_64 0:1.0.1-6.1.el6 will be installed
---> Package jasper-libs.x86_64 0:1.900.1-16.el6_6.3 will be installed
---> Package lcms-libs.x86_64 0:1.19-1.el6 will be installed
---> Package libICE.x86_64 0:1.0.6-1.el6 will be installed
---> Package libSM.x86_64 0:1.2.1-2.el6 will be installed
---> Package libX11-common.noarch 0:1.6.0-6.el6 will be installed
---> Package libXext.x86_64 0:1.3.2-2.1.el6 will be installed
---> Package libXt.x86_64 0:1.1.4-6.1.el6 will be installed
---> Package librsvg2.x86_64 0:2.26.0-14.el6 will be installed
--> Processing Dependency: libgsf >= 1.6.0 for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: gtk2 >= 2.9.0 for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libpangoft2-1.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libpangocairo-1.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libpango-1.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libgtk-x11-2.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libgsf-1.so.114()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libgdk-x11-2.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libcroco-0.6.so.3()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
--> Processing Dependency: libatk-1.0.so.0()(64bit) for package: librsvg2-2.26.0-14.el6.x86_64
---> Package libtiff.x86_64 0:3.9.4-10.el6_5 will be installed
---> Package libwmf.x86_64 0:0.2.8.4-25.el6_7 will be installed
---> Package libwmf-lite.x86_64 0:0.2.8.4-25.el6_7 will be installed
---> Package libxcb.x86_64 0:1.9.1-3.el6 will be installed
--> Processing Dependency: libXau.so.6()(64bit) for package: libxcb-1.9.1-3.el6.x86_64
---> Package libxslt.x86_64 0:1.1.26-2.el6_3.1 will be installed
---> Package mailcap.noarch 0:2.1.31-2.el6 will be installed
--> Running transaction check
---> Package atk.x86_64 0:1.30.0-1.el6 will be installed
---> Package cups-libs.x86_64 1:1.4.2-72.el6 will be installed
--> Processing Dependency: libgnutls.so.26(GNUTLS_1_4)(64bit) for package: 1:cups-libs-1.4.2-72.el6.x86_64
--> Processing Dependency: libgnutls.so.26()(64bit) for package: 1:cups-libs-1.4.2-72.el6.x86_64
--> Processing Dependency: libavahi-common.so.3()(64bit) for package: 1:cups-libs-1.4.2-72.el6.x86_64
--> Processing Dependency: libavahi-client.so.3()(64bit) for package: 1:cups-libs-1.4.2-72.el6.x86_64
---> Package ghostscript-fonts.noarch 0:5.50-23.2.el6 will be installed
--> Processing Dependency: xorg-x11-font-utils for package: ghostscript-fonts-5.50-23.2.el6.noarch
---> Package gtk2.x86_64 0:2.24.23-6.el6 will be installed
--> Processing Dependency: libXrandr >= 1.2.99.4-2 for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: hicolor-icon-theme for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXrandr.so.2()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXinerama.so.1()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXi.so.6()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXfixes.so.3()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXdamage.so.1()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXcursor.so.1()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
--> Processing Dependency: libXcomposite.so.1()(64bit) for package: gtk2-2.24.23-6.el6.x86_64
---> Package libXau.x86_64 0:1.0.6-4.el6 will be installed
---> Package libXrender.x86_64 0:0.9.8-2.1.el6 will be installed
---> Package libcroco.x86_64 0:0.6.2-5.el6 will be installed
---> Package libgsf.x86_64 0:1.14.15-5.el6 will be installed
--> Processing Dependency: GConf2 for package: libgsf-1.14.15-5.el6.x86_64
--> Processing Dependency: GConf2 for package: libgsf-1.14.15-5.el6.x86_64
---> Package pango.x86_64 0:1.28.1-10.el6 will be installed
--> Processing Dependency: libthai >= 0.1.9 for package: pango-1.28.1-10.el6.x86_64
--> Processing Dependency: libthai.so.0(LIBTHAI_0.1)(64bit) for package: pango-1.28.1-10.el6.x86_64
--> Processing Dependency: libthai.so.0()(64bit) for package: pango-1.28.1-10.el6.x86_64
--> Processing Dependency: libXft.so.2()(64bit) for package: pango-1.28.1-10.el6.x86_64
---> Package pixman.x86_64 0:0.32.4-4.el6 will be installed
---> Package urw-fonts.noarch 0:2.4-10.el6 will be installed
--> Running transaction check
---> Package GConf2.x86_64 0:2.28.0-6.el6 will be installed
--> Processing Dependency: sgml-common for package: GConf2-2.28.0-6.el6.x86_64
--> Processing Dependency: dbus for package: GConf2-2.28.0-6.el6.x86_64
--> Processing Dependency: libpolkit-gobject-1.so.0()(64bit) for package: GConf2-2.28.0-6.el6.x86_64
--> Processing Dependency: libORBit-2.so.0()(64bit) for package: GConf2-2.28.0-6.el6.x86_64
---> Package avahi-libs.x86_64 0:0.6.25-15.el6 will be installed
---> Package gnutls.x86_64 0:2.8.5-18.el6 will be installed
---> Package hicolor-icon-theme.noarch 0:0.11-1.1.el6 will be installed
---> Package libXcomposite.x86_64 0:0.4.3-4.el6 will be installed
---> Package libXcursor.x86_64 0:1.1.14-2.1.el6 will be installed
---> Package libXdamage.x86_64 0:1.1.3-4.el6 will be installed
---> Package libXfixes.x86_64 0:5.0.1-2.1.el6 will be installed
---> Package libXft.x86_64 0:2.3.1-2.el6 will be installed
---> Package libXi.x86_64 0:1.7.2-2.2.el6 will be installed
---> Package libXinerama.x86_64 0:1.1.3-2.1.el6 will be installed
---> Package libXrandr.x86_64 0:1.4.1-2.1.el6 will be installed
---> Package libthai.x86_64 0:0.1.12-3.el6 will be installed
---> Package xorg-x11-font-utils.x86_64 1:7.2-11.el6 will be installed
--> Processing Dependency: libfontenc.so.1()(64bit) for package: 1:xorg-x11-font-utils-7.2-11.el6.x86_64
--> Processing Dependency: libXfont.so.1()(64bit) for package: 1:xorg-x11-font-utils-7.2-11.el6.x86_64
--> Running transaction check
---> Package ORBit2.x86_64 0:2.14.17-5.el6 will be installed
--> Processing Dependency: libIDL-2.so.0()(64bit) for package: ORBit2-2.14.17-5.el6.x86_64
---> Package dbus.x86_64 1:1.2.24-8.el6_6 will be installed
---> Package libXfont.x86_64 0:1.4.5-5.el6_7 will be installed
---> Package libfontenc.x86_64 0:1.0.5-2.el6 will be installed
---> Package polkit.x86_64 0:0.96-11.el6 will be installed
--> Processing Dependency: ConsoleKit for package: polkit-0.96-11.el6.x86_64
--> Processing Dependency: libeggdbus-1.so.0()(64bit) for package: polkit-0.96-11.el6.x86_64
---> Package sgml-common.noarch 0:0.6.3-33.el6 will be installed
--> Running transaction check
---> Package ConsoleKit.x86_64 0:0.4.1-3.el6 will be installed
--> Processing Dependency: libck-connector.so.0()(64bit) for package: ConsoleKit-0.4.1-3.el6.x86_64
---> Package eggdbus.x86_64 0:0.6-3.el6 will be installed
---> Package libIDL.x86_64 0:0.8.13-2.1.el6 will be installed
--> Running transaction check
---> Package ConsoleKit-libs.x86_64 0:0.4.1-3.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                  Arch      Version                   Repository   Size
================================================================================
Installing:
 php70w                   x86_64    7.0.33-1.w6               webtatic    2.7 M
 php70w-bcmath            x86_64    7.0.33-1.w6               webtatic     34 k
 php70w-cli               x86_64    7.0.33-1.w6               webtatic    2.6 M
 php70w-common            x86_64    7.0.33-1.w6               webtatic    1.2 M
 php70w-devel             x86_64    7.0.33-1.w6               webtatic    2.7 M
 php70w-fpm               x86_64    7.0.33-1.w6               webtatic    1.3 M
 php70w-gd                x86_64    7.0.33-1.w6               webtatic    125 k
 php70w-mbstring          x86_64    7.0.33-1.w6               webtatic    950 k
 php70w-mcrypt            x86_64    7.0.33-1.w6               webtatic     24 k
 php70w-mysql             x86_64    7.0.33-1.w6               webtatic     74 k
 php70w-opcache           x86_64    7.0.33-1.w6               webtatic    120 k
 php70w-pear              noarch    1:1.10.4-1.w6             webtatic    353 k
 php70w-pecl-imagick      x86_64    3.4.3-1.w6                webtatic    127 k
 php70w-pecl-memcached    x86_64    3.0.4-1.w6                webtatic     91 k
 php70w-pecl-redis        x86_64    3.1.6-1.w6                webtatic    169 k
Installing for dependencies:
 ConsoleKit               x86_64    0.4.1-3.el6               base         82 k
 ConsoleKit-libs          x86_64    0.4.1-3.el6               base         17 k
 GConf2                   x86_64    2.28.0-6.el6              base        964 k
 ImageMagick              x86_64    6.7.2.7-2.el6             base        1.9 M
 ORBit2                   x86_64    2.14.17-5.el6             base        168 k
 OpenEXR-libs             x86_64    1.6.1-8.1.el6             base        197 k
 apr                      x86_64    1.3.9-5.el6_2             base        123 k
 apr-util                 x86_64    1.3.9-3.el6_0.1           base         87 k
 apr-util-ldap            x86_64    1.3.9-3.el6_0.1           base         15 k
 atk                      x86_64    1.30.0-1.el6              base        195 k
 autoconf                 noarch    2.63-5.1.el6              base        781 k
 automake                 noarch    1.11.1-4.el6              base        550 k
 avahi-libs               x86_64    0.6.25-15.el6             base         55 k
 cairo                    x86_64    1.8.8-6.el6_6             base        309 k
 cups-libs                x86_64    1:1.4.2-72.el6            base        321 k
 dbus                     x86_64    1:1.2.24-8.el6_6          base        207 k
 eggdbus                  x86_64    0.6-3.el6                 base         91 k
 fontconfig               x86_64    2.8.0-5.el6               base        186 k
 freetype                 x86_64    2.3.11-15.el6_6.1         base        361 k
 gdk-pixbuf2              x86_64    2.24.1-6.el6_7            updates     501 k
 ghostscript              x86_64    8.70-21.el6               base        4.4 M
 ghostscript-fonts        noarch    5.50-23.2.el6             base        590 k
 gnutls                   x86_64    2.8.5-18.el6              base        347 k
 gtk2                     x86_64    2.24.23-6.el6             base        3.2 M
 hicolor-icon-theme       noarch    0.11-1.1.el6              base         40 k
 httpd                    x86_64    2.2.15-47.el6.centos      updates     830 k
 httpd-tools              x86_64    2.2.15-47.el6.centos      updates      77 k
 ilmbase                  x86_64    1.0.1-6.1.el6             base         72 k
 jasper-libs              x86_64    1.900.1-16.el6_6.3        base        137 k
 lcms-libs                x86_64    1.19-1.el6                base        100 k
 libICE                   x86_64    1.0.6-1.el6               base         53 k
 libIDL                   x86_64    0.8.13-2.1.el6            base         83 k
 libSM                    x86_64    1.2.1-2.el6               base         37 k
 libX11                   x86_64    1.6.0-6.el6               base        586 k
 libX11-common            noarch    1.6.0-6.el6               base        192 k
 libXau                   x86_64    1.0.6-4.el6               base         24 k
 libXcomposite            x86_64    0.4.3-4.el6               base         20 k
 libXcursor               x86_64    1.1.14-2.1.el6            base         28 k
 libXdamage               x86_64    1.1.3-4.el6               base         18 k
 libXext                  x86_64    1.3.2-2.1.el6             base         35 k
 libXfixes                x86_64    5.0.1-2.1.el6             base         17 k
 libXfont                 x86_64    1.4.5-5.el6_7             updates     137 k
 libXft                   x86_64    2.3.1-2.el6               base         55 k
 libXi                    x86_64    1.7.2-2.2.el6             base         37 k
 libXinerama              x86_64    1.1.3-2.1.el6             base         13 k
 libXpm                   x86_64    3.5.10-2.el6              base         51 k
 libXrandr                x86_64    1.4.1-2.1.el6             base         23 k
 libXrender               x86_64    0.9.8-2.1.el6             base         24 k
 libXt                    x86_64    1.1.4-6.1.el6             base        165 k
 libcroco                 x86_64    0.6.2-5.el6               base        100 k
 libfontenc               x86_64    1.0.5-2.el6               base         24 k
 libgsf                   x86_64    1.14.15-5.el6             base        116 k
 libjpeg-turbo            x86_64    1.2.1-3.el6_5             base        174 k
 libmcrypt                x86_64    2.5.8-9.el6               epel         96 k
 libmemcached10           x86_64    1.0.16-1.ius.el6          webtatic    154 k
 libpng                   x86_64    2:1.2.49-1.el6_2          base        182 k
 librsvg2                 x86_64    2.26.0-14.el6             base        140 k
 libthai                  x86_64    0.1.12-3.el6              base        183 k
 libtiff                  x86_64    3.9.4-10.el6_5            base        343 k
 libtool-ltdl             x86_64    2.2.6-15.5.el6            base         44 k
 libwmf                   x86_64    0.2.8.4-25.el6_7          updates     132 k
 libwmf-lite              x86_64    0.2.8.4-25.el6_7          updates      51 k
 libxcb                   x86_64    1.9.1-3.el6               base        110 k
 libxslt                  x86_64    1.1.26-2.el6_3.1          base        452 k
 mailcap                  noarch    2.1.31-2.el6              base         27 k
 pango                    x86_64    1.28.1-10.el6             base        351 k
 pcre-devel               x86_64    7.8-7.el6                 base        320 k
 php70w-pdo               x86_64    7.0.33-1.w6               webtatic     81 k
 php70w-pecl-igbinary     x86_64    2.0.5-1.w6                webtatic     38 k
 php70w-process           x86_64    7.0.33-1.w6               webtatic     34 k
 php70w-xml               x86_64    7.0.33-1.w6               webtatic    112 k
 pixman                   x86_64    0.32.4-4.el6              base        243 k
 polkit                   x86_64    0.96-11.el6               base        162 k
 sgml-common              noarch    0.6.3-33.el6              base         43 k
 urw-fonts                noarch    2.4-10.el6                base        3.1 M
 xorg-x11-font-utils      x86_64    1:7.2-11.el6              base         75 k

Transaction Summary
================================================================================
Install      91 Package(s)

Total download size: 38 M
Installed size: 135 M
Is this ok [y/N]: 
```

一路确定，等待安装完成：
> y

如图：

![]({{site.baseurl}}/images/20200329/20200329191548.jpg)

![]({{site.baseurl}}/images/20200329/20200329191549.jpg)

![]({{site.baseurl}}/images/20200329/20200329191550.jpg)

![]({{site.baseurl}}/images/20200329/20200329191551.jpg)

![]({{site.baseurl}}/images/20200329/20200329191552.jpg)

安装包与拓展关系：
```
安装包			提供的拓展
php70w			mod_php	, php70w-zts
php70w-bcmath		
php70w-cli		php-cgi, php-pcntl, php-readline
php70w-common	php-api, php-bz2, php-calendar, php-ctype, php-curl, php-date, php-exif, php-fileinfo, php-filter, php-ftp, php-gettext, php-gmp, php-hash, php-iconv, php-json, php-libxml, php-openssl, php-pcre, php-pecl-Fileinfo, php-pecl-phar, php-pecl-zip, php-reflection, php-session, php-shmop, php-simplexml	, php-sockets, php-spl, php-tokenizer, php-zend-abi, php-zip, php-zlib
php70w-dba		
php70w-devel		
php70w-embedded		php-embedded-devel
php70w-enchant		
php70w-fpm		
php70w-gd		
php70w-imap		
php70w-interbase		php_database, php-firebird
php70w-intl		
php70w-ldap		
php70w-mbstring		
php70w-mcrypt		
php70w-mysql		php-mysqli, php_database
php70w-mysqlnd		php-mysqli, php_database
php70w-odbc		php-pdo_odbc, php_database
php70w-opcache		php70w-pecl-zendopcache
php70w-pdo		php70w-pdo_sqlite, php70w-sqlite3
php70w-pdo_dblib		php70w-mssql
php70w-pear		
php70w-pecl-apcu	
php70w-pecl-imagick	
php70w-pecl-memcached	
php70w-pecl-mongodb	
php70w-pecl-redis	
php70w-pecl-xdebug	
php70w-pgsql		php-pdo_pgsql, php_database
php70w-phpdbg		
php70w-process		php-posix, php-sysvmsg, php-sysvsem, php-sysvshm
php70w-pspell		
php70w-recode		
php70w-snmp		
php70w-soap		
php70w-tidy		
php70w-xml		php-dom, php-domxml, php-wddx, php-xsl
php70w-xmlrpc	
```

启动php服务：

> /etc/init.d/php-fpm start
 
![]({{site.baseurl}}/images/20200329/20200329191553.jpg)

至此，php安装完成。

查看安装的php：
```
[root@localhost ~]# yum list installed | grep php
php70w.x86_64          7.0.33-1.w6      @webtatic
php70w-bcmath.x86_64   7.0.33-1.w6      @webtatic
php70w-cli.x86_64      7.0.33-1.w6      @webtatic
php70w-common.x86_64   7.0.33-1.w6      @webtatic
php70w-devel.x86_64    7.0.33-1.w6      @webtatic
php70w-fpm.x86_64      7.0.33-1.w6      @webtatic
php70w-gd.x86_64       7.0.33-1.w6      @webtatic
php70w-mbstring.x86_64 7.0.33-1.w6      @webtatic
php70w-mcrypt.x86_64   7.0.33-1.w6      @webtatic
php70w-mysql.x86_64    7.0.33-1.w6      @webtatic
php70w-opcache.x86_64  7.0.33-1.w6      @webtatic
php70w-pdo.x86_64      7.0.33-1.w6      @webtatic
php70w-pear.noarch     1:1.10.4-1.w6    @webtatic
php70w-process.x86_64  7.0.33-1.w6      @webtatic
php70w-xml.x86_64      7.0.33-1.w6      @webtatic
[root@localhost ~]#
```

### 安装zip

给linux安装zip： 
> yum install zip

![]({{site.baseurl}}/images/20200329/20200329191554.jpg)

### 安装unzip

给linux安装unzip： 

> yum install unzip

![]({{site.baseurl}}/images/20200329/20200329191555.jpg)

### 安装vim

给linux安装vim： 

> yum install vim

![]({{site.baseurl}}/images/20200329/20200329191556.jpg)

![]({{site.baseurl}}/images/20200329/20200329191557.jpg)

### 安装Swoole拓展：

下载swoole：swoole-src-1.9.9.zip

copy到D:/www/下

move到当前目录前： 

> mv /var/www/swoole-src-1.9.9.zip ./

解压swoole压缩包：

> unzip swoole-src-1.9.9.zip

如图：
    
![]({{site.baseurl}}/images/20200329/20200329191558.jpg)

进入swoole目录：cd swoole-src-1.9.9

给php添加动态拓展：phpize
    
![]({{site.baseurl}}/images/20200329/20200329191559.jpg)

源码安装前，配置： 

> ./configure
 
![]({{site.baseurl}}/images/20200329/20200329191560.jpg)

编译，并安装：

> make && make install  

![]({{site.baseurl}}/images/20200329/20200329191561.jpg)

到/etc/php.d/下： 

> cd /etc/php.d/

新建文件：

> touch swoole.ini

并写入： 

> echo 'extension=swoole.so' > swoole.ini
    
![]({{site.baseurl}}/images/20200329/20200329191562.jpg)

至此：swoole安装完成。
    
### 安装Nginx：

到上层目录下（即/etc）： 

> cd ../

vim编辑配置文件： 

> vim /etc/yum.repos.d/nginx.repo
            
![]({{site.baseurl}}/images/20200329/20200329191563.jpg)

写入并保存：

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

![]({{site.baseurl}}/images/20200329/20200329191564.jpg)

安装操作： 

> yum -y install nginx

![]({{site.baseurl}}/images/20200329/20200329191565.jpg)

启动nginx服务器： 

> /etc/init.d/nginx start
      
![]({{site.baseurl}}/images/20200329/20200329191566.jpg)

至此，nginx安装完成。
 
### 安装MySQL

到用户目录下： 

> cd /usr/local/src/

下载mysql的包： 

> wget https://dev.mysql.com/get/mysql57-community-release-el6-11.noarch.rpm

升级软件包：

> rpm -Uvh mysql57-community-release-el6-11.noarch.rpm

![]({{site.baseurl}}/images/20200329/20200329191567.jpg)

创建配置文件： 

> touch /etc/yum.repos.d/mysql-community.repo

清空配置文件： 

> echo '' > /etc/yum.repos.d/mysql-community.repo

编辑配置文件： 

> vi /etc/yum.repos.d/mysql-community.repo

![]({{site.baseurl}}/images/20200329/20200329191568.jpg)

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

![]({{site.baseurl}}/images/20200329/20200329191569.jpg)

查看仓库列表中的mysql： 

> yum repolist enabled \| grep mysql

安装mysql： 

> yum install mysql-community-server 
            
![]({{site.baseurl}}/images/20200329/20200329191570.jpg)

一路确定：y
 
![]({{site.baseurl}}/images/20200329/20200329191571.jpg)

mysql安装完成，我们启动它：

> /etc/init.d/mysqld start
 
![]({{site.baseurl}}/images/20200329/20200329191572.jpg)

MySQL安全配置向导：  

> mysql_secure_installation

![]({{site.baseurl}}/images/20200329/20200329191573.jpg)

![]({{site.baseurl}}/images/20200329/20200329191574.jpg)

进入MySQL管理界面： 

> mysql -uroot -p

![]({{site.baseurl}}/images/20200329/20200329191575.jpg)

选定mysql表：

> use mysql;

更新root权限：

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
```

退出mysql管理：

> quit;
            
![]({{site.baseurl}}/images/20200329/20200329191576.jpg)

至此，MySQL的所有安装和配置都完成了。

### 站点配置

在D:/www目录下建设站点文件，如test项目

到nginx配置目录下： 

> cd /etc/nginx/conf.d/

编辑default.conf： 

> vi default.conf
            
![]({{site.baseurl}}/images/20200329/20200329191577.jpg)

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

![]({{site.baseurl}}/images/20200329/20200329191578.jpg)

或者，只在default.conf中最下面加一行：include /var/www/vhost/*.conf;

![]({{site.baseurl}}/images/20200329/20200329191579.jpg)

然后，在D:\www\vhost目录下新建后缀名为conf的文件，这样以后就可以在window窗口中管理站点配置文件了：
 
![]({{site.baseurl}}/images/20200329/20200329191580.jpg)

在相应文件中写入配置：
        
![]({{site.baseurl}}/images/20200329/20200329191581.jpg)

为了对比学习，可以看下yii2的配置：

![]({{site.baseurl}}/images/20200329/20200329191582.jpg)

生成该站点操作日志目录：

> mkdir /var/www/test/logs

![]({{site.baseurl}}/images/20200329/20200329191583.jpg)

到windows的安装目录下的修改hosts文件，如C:\Windows\System32\drivers\etc下

![]({{site.baseurl}}/images/20200329/20200329191584.jpg)

点击打开后添加一行：
 
![]({{site.baseurl}}/images/20200329/20200329191585.jpg)

重启nginx服务器： 

> /etc/init.d/nginx restart

![]({{site.baseurl}}/images/20200329/20200329191586.jpg)

至此，站点配置完成。
  
下面，我们查看下LNMP服务是否全部已启动：

> ps aux

![]({{site.baseurl}}/images/20200329/20200329191587.jpg)

![]({{site.baseurl}}/images/20200329/20200329191588.jpg)

全部启动了。

如果D:\www\test\index.php中写入的内容如下：
 
![]({{site.baseurl}}/images/20200329/20200329191589.jpg)

我们访问一下test.host，访问内容如图：
 
![]({{site.baseurl}}/images/20200329/20200329191590.jpg)

说明，该虚拟开发环境正常。接下来就是我们大展身手进行项目开发的操作了。

### 附言

1、 exit退出root账户：

![]({{site.baseurl}}/images/20200329/20200329191591.jpg)

windows命令行中

> vagrant halt

停止vagrant服务：
 
![]({{site.baseurl}}/images/20200329/20200329191592.jpg)

2、每日启动vagrant服务用vagrant up，不过要进入到相应的虚拟开发环境目录下：
  
![]({{site.baseurl}}/images/20200329/20200329191593.jpg)

别忘了在putty中用ps aux查看LNMP服务是否全部启动（请用root账户——sudo su），如果有未启动的，请手动启动，如：

![]({{site.baseurl}}/images/20200329/20200329191594.jpg)

3、另外，如果发现页面样式修改后，预览时总是没有效果，这是nginx配置导致的。我们要修改nginx.conf的sendfile on为off，修改后别忘了nginx的restart：
  
![]({{site.baseurl}}/images/20200329/20200329191595.jpg)

![]({{site.baseurl}}/images/20200329/20200329191596.jpg)

![]({{site.baseurl}}/images/20200329/20200329191597.jpg)

### 每日启动

win + R 输入cmd调出命令行工具，启动VirtualBox虚拟机中的Vagrant服务：

> d:
>
> cd vagrant
>
> vagrant halt
>
> vagrant up

点击PuTTY.exe连接vagrant，重启nginx和php：

> sudo /etc/init.d/nginx restart
> 
> sudo /etc/init.d/php-fpm restart

然后浏览器就可以访问站点服务了。

### 安装docker

想把PHP升级到7.4版，想到了docker。

先卸载老版本docker：
> yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine

再安装docker：
> yum install -y docker

过程：
```
[root@localhost ~]# yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine
Loaded plugins: fastestmirror
Setting up Remove Process
No Match for argument: docker
Determining fastest mirrors
https://mirrors.tuna.tsinghua.edu.cn/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] Peer cert cannot be verified or peer cert invalid
Trying other mirror.
Error: Cannot retrieve repository metadata (repomd.xml) for repository: base. Please verify its path and try again
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# yum install -y docker
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
https://mirrors.tuna.tsinghua.edu.cn/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] Peer cert cannot be verified or peer cert invalid
Trying other mirror.
Error: Cannot retrieve repository metadata (repomd.xml) for repository: base. Please verify its path and try again
[root@localhost ~]#
```

发现报错了，yum源的问题。

#### yum源配置

查看Linux版本：
> cat /etc/issue

过程：
```
[root@localhost ~]# uname
Linux
[root@localhost ~]# uname -a
Linux localhost.localdomain 2.6.32-573.7.1.el6.x86_64 #1 SMP Tue Sep 22 22:00:00 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@localhost ~]#
[root@localhost ~]# cat /etc/issue
CentOS release 6.7 (Final)
Kernel \r on an \m

[root@localhost ~]#
```

确认版本是：CentOS 6.7 。

旧yum源备份：
```
[root@localhost ~]# ls -l /etc/yum.repos.d/
total 76
-rw-r--r--  1 root root 1986 Aug 26  2020 CentOS-Base.repo
-rw-r--r--  1 root root 1991 Aug 22  2020 CentOS-Base.repo.bak
-rw-r--r--  1 root root 2051 Aug 22  2020 CentOS-Base.repo.bak.tsinghua
-rw-r--r--. 1 root root  647 Aug  3  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  289 Aug  3  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Aug  3  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 6259 Aug  3  2015 CentOS-Vault.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak
-rw-r--r--  1 root root 1056 Nov  5  2012 epel-testing.repo
-rw-r--r--  1 root root  133 Feb 15 02:44 gitlab-ce.repo
-rw-r--r--  1 root root  218 Aug 26  2020 mysql-community.repo.bak
-rw-r--r--  1 root root 1885 Apr 27  2017 mysql-community-source.repo
-rw-r--r--  1 root root  111 Aug 20  2020 nginx.repo
-rw-r--r--  1 root root  422 Oct  1  2015 puppetlabs-pc1.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-archive.repo
-rw-r--r--  1 root root  865 Aug 22  2020 webtatic.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-testing.repo
[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/CentOS-Base.repo
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#


[base]
name=CentOS-$releasever - Base
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/os/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-6

#released updates
[updates]
name=CentOS-$releasever - Updates
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/updates/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-6



#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/extras/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-6



#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/centosplus/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-6


#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/contrib/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-6

[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/epel.repo
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
#mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

[epel-debuginfo]
name=Extra Packages for Enterprise Linux 6 - $basearch - Debug
baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch/debug
#mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-debug-6&arch=$basearch
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=1

[epel-source]
name=Extra Packages for Enterprise Linux 6 - $basearch - Source
baseurl=http://download.fedoraproject.org/pub/epel/6/SRPMS
#mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-source-6&arch=$basearch
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=1
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak.20220507        
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# ls -l /etc/yum.repos.d/
total 76
-rw-r--r--  1 root root 1991 Aug 22  2020 CentOS-Base.repo.bak
-rw-r--r--  1 root root 1986 Aug 26  2020 CentOS-Base.repo.bak.20220507
-rw-r--r--  1 root root 2051 Aug 22  2020 CentOS-Base.repo.bak.tsinghua
-rw-r--r--. 1 root root  647 Aug  3  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  289 Aug  3  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Aug  3  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 6259 Aug  3  2015 CentOS-Vault.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak.20220507
-rw-r--r--  1 root root 1056 Nov  5  2012 epel-testing.repo
-rw-r--r--  1 root root  133 Feb 15 02:44 gitlab-ce.repo
-rw-r--r--  1 root root  218 Aug 26  2020 mysql-community.repo.bak
-rw-r--r--  1 root root 1885 Apr 27  2017 mysql-community-source.repo
-rw-r--r--  1 root root  111 Aug 20  2020 nginx.repo
-rw-r--r--  1 root root  422 Oct  1  2015 puppetlabs-pc1.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-archive.repo
-rw-r--r--  1 root root  865 Aug 22  2020 webtatic.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-testing.repo
[root@localhost ~]#
```

这里选用 阿里开源镜像，访问 <https://developer.aliyun.com/mirror>，查看相应命令。

下载新的 CentOS-Base.repo：
> wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo

下载新的 epel扩展源。EPEL (Extra Packages for Enterprise Linux), 
是由 Fedora Special Interest Group 维护的 Enterprise Linux（RHEL、CentOS）中经常用到的包。
阿里云提示（epel6官方源已下线，建议切换epel-archive源）：
> wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-archive-6.repo

如果 wget 命令不可用，使用 curl 命令：
> curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo
>
> curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-archive-6.repo

过程：
```
[root@localhost ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo
--2022-05-07 03:43:49--  https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo
Resolving mirrors.aliyun.com... 112.132.37.238, 112.132.37.240, 112.132.37.237, ...
Connecting to mirrors.aliyun.com|112.132.37.238|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2533 (2.5K) [application/octet-stream]
Saving to: `/etc/yum.repos.d/CentOS-Base.repo'

100%[=====================================================================>] 2,533       --.-K/s   in 0s

2022-05-07 03:43:49 (7.69 MB/s) - `/etc/yum.repos.d/CentOS-Base.repo' saved [2533/2533]

[root@localhost ~]#
[root@localhost ~]# wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-archive-6.repo
--2022-05-07 03:44:33--  http://mirrors.aliyun.com/repo/epel-archive-6.repo
Resolving mirrors.aliyun.com... 112.132.37.238, 112.132.37.240, 112.132.37.237, ...
Connecting to mirrors.aliyun.com|112.132.37.238|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 712 [application/octet-stream]
Saving to: `/etc/yum.repos.d/epel.repo'

100%[=====================================================================>] 712         --.-K/s   in 0.006s

2022-05-07 03:44:33 (119 KB/s) - `/etc/yum.repos.d/epel.repo' saved [712/712]

[root@localhost ~]#
[root@localhost ~]# ls -l /etc/yum.repos.d/
total 84
-rw-r--r--  1 root root 2533 Mar  2 09:33 CentOS-Base.repo
-rw-r--r--  1 root root 1991 Aug 22  2020 CentOS-Base.repo.bak
-rw-r--r--  1 root root 1986 Aug 26  2020 CentOS-Base.repo.bak.20220507
-rw-r--r--  1 root root 2051 Aug 22  2020 CentOS-Base.repo.bak.tsinghua
-rw-r--r--. 1 root root  647 Aug  3  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  289 Aug  3  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Aug  3  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 6259 Aug  3  2015 CentOS-Vault.repo
-rw-r--r--  1 root root  712 Mar  2 09:37 epel.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak.20220507
-rw-r--r--  1 root root 1056 Nov  5  2012 epel-testing.repo
-rw-r--r--  1 root root  133 Feb 15 02:44 gitlab-ce.repo
-rw-r--r--  1 root root  218 Aug 26  2020 mysql-community.repo.bak
-rw-r--r--  1 root root 1885 Apr 27  2017 mysql-community-source.repo
-rw-r--r--  1 root root  111 Aug 20  2020 nginx.repo
-rw-r--r--  1 root root  422 Oct  1  2015 puppetlabs-pc1.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-archive.repo
-rw-r--r--  1 root root  865 Aug 22  2020 webtatic.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-testing.repo
[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/CentOS-Base.repo
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-vault-6.10 - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/os/$basearch/
        http://mirrors.aliyuncs.com/centos-vault/6.10/os/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos-vault/6.10/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#released updates
[updates]
name=CentOS-vault-6.10 - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/updates/$basearch/
        http://mirrors.aliyuncs.com/centos-vault/6.10/updates/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos-vault/6.10/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful
[extras]
name=CentOS-vault-6.10 - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/extras/$basearch/
        http://mirrors.aliyuncs.com/centos-vault/6.10/extras/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos-vault/6.10/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-vault-6.10 - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/centosplus/$basearch/
        http://mirrors.aliyuncs.com/centos-vault/6.10/centosplus/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos-vault/6.10/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#contrib - packages by Centos Users
[contrib]
name=CentOS-vault-6.10 - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos-vault/6.10/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos-vault/6.10/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/epel.repo
[epel-archive]
name=Extra Packages for Enterprise Linux 6 - $basearch
baseurl=http://mirrors.aliyun.com/epel-archive/6/$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

[epel-archive-debuginfo]
name=Extra Packages for Enterprise Linux 6 - $basearch - Debug
baseurl=http://mirrors.aliyun.com/epel-archive/6/$basearch/debug
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=0

[epel-archive-source]
name=Extra Packages for Enterprise Linux 6 - $basearch - Source
baseurl=http://mirrors.aliyun.com/epel-archive/6/SRPMS
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
gpgcheck=0
[root@localhost ~]#
```

清理缓存：
> yum clean all

生成缓存：
> yum makecache

过程：
```
[root@localhost ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base epel-archive extras gitlab-ce nginx puppetlabs-pc1 updates webtatic
Cleaning up Everything
Cleaning up list of fastest mirrors
[root@localhost ~]#
[root@localhost ~]# yum makecache
Loaded plugins: fastestmirror
Determining fastest mirrors
Could not retrieve mirrorlist https://mirror.webtatic.com/yum/el6/x86_64/mirrorlist error was
14: problem making ssl connection
Error: Cannot find a valid baseurl for repo: webtatic
[root@localhost ~]#
```

发现报错了：`Could not retrieve mirrorlist https://mirror.webtatic.com/yum/el6/x86_64/mirrorlist error was 14:problem making ssl connection`

查看本地已有的repo地址：
> yum repolist

```
[root@localhost ~]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Could not retrieve mirrorlist https://mirror.webtatic.com/yum/el6/x86_64/mirrorlist error was
14: problem making ssl connection
base                                                                                    | 3.7 kB     00:00
base/primary_db                                                                         | 4.7 MB     00:08
epel-archive                                                                            | 4.7 kB     00:00
epel-archive/primary_db                                                                 | 6.1 MB     00:05
extras                                                                                  | 3.4 kB     00:00
extras/primary_db                                                                       |  29 kB     00:00
https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6/repodata/repomd.xml: [Errno 14] Peer cert cannot be verified or peer cert invalid
Trying other mirror.
nginx                                                                                   | 2.9 kB     00:00
nginx/primary_db                                                                        |  64 kB     00:00
puppetlabs-pc1                                                                          | 2.5 kB     00:00
puppetlabs-pc1/primary_db                                                               | 236 kB     00:00
updates                                                                                 | 3.4 kB     00:00
updates/primary_db                                                                      |  12 MB     00:09
repo id                            repo name                                                             status
base                               CentOS-vault-6.10 - Base - mirrors.aliyun.com                          6,713
epel-archive                       Extra Packages for Enterprise Linux 6 - x86_64                        12,581
extras                             CentOS-vault-6.10 - Extras - mirrors.aliyun.com                           47
gitlab-ce                          Gitlab CE Repository                                                       0
nginx                              nginx repo                                                               208
puppetlabs-pc1                     Puppet Labs PC1 Repository el 6 - x86_64                                 192
updates                            CentOS-vault-6.10 - Updates - mirrors.aliyun.com                       1,193
webtatic                           Webtatic Repository EL6 - x86_64                                           0
repolist: 20,934
[root@localhost ~]#
```

看到了webtatic源，曾经安装PHP时用到的源，查看内容：
```
[root@localhost ~]# cat /etc/yum.repos.d/webtatic-archive.repo
[webtatic-archive]
name=Webtatic Repository EL6 - $basearch - Archive
#baseurl=https://repo.webtatic.com/yum/el6-archive/$basearch/
mirrorlist=https://mirror.webtatic.com/yum/el6-archive/$basearch/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-archive-debuginfo]
name=Webtatic Repository EL6 - $basearch - Archive Debug
#baseurl=https://repo.webtatic.com/yum/el6-archive/$basearch/debug/
mirrorlist=https://mirror.webtatic.com/yum/el6-archive/$basearch/debug/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-archive-source]
name=Webtatic Repository EL6 - $basearch - Archive Source
#baseurl=https://repo.webtatic.com/yum/el6-archive/SRPMS/
mirrorlist=https://mirror.webtatic.com/yum/el6-archive/SRPMS/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6
[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/webtatic.repo
[webtatic]
name=Webtatic Repository EL6 - $basearch
#baseurl=https://repo.webtatic.com/yum/el6/$basearch/
mirrorlist=https://mirror.webtatic.com/yum/el6/$basearch/mirrorlist
failovermethod=priority
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-debuginfo]
name=Webtatic Repository EL6 - $basearch - Debug
#baseurl=https://repo.webtatic.com/yum/el6/$basearch/debug/
mirrorlist=https://mirror.webtatic.com/yum/el6/$basearch/debug/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-source]
name=Webtatic Repository EL6 - $basearch - Source
#baseurl=https://repo.webtatic.com/yum/el6/SRPMS/
mirrorlist=https://mirror.webtatic.com/yum/el6/SRPMS/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6
[root@localhost ~]#
[root@localhost ~]# cat /etc/yum.repos.d/webtatic-testing.repo
[webtatic-testing]
name=Webtatic Repository EL6 - $basearch - Testing
#baseurl=https://repo.webtatic.com/yum/el6-testing/$basearch/
mirrorlist=https://mirror.webtatic.com/yum/el6-testing/$basearch/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-testing-debuginfo]
name=Webtatic Repository EL6 - $basearch - Testing Debug
#baseurl=https://repo.webtatic.com/yum/el6-testing/$basearch/debug/
mirrorlist=https://mirror.webtatic.com/yum/el6-testing/$basearch/debug/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6

[webtatic-testing-source]
name=Webtatic Repository EL6 - $basearch - Testing Source
#baseurl=https://repo.webtatic.com/yum/el6-testing/SRPMS/
mirrorlist=https://mirror.webtatic.com/yum/el6-testing/SRPMS/mirrorlist
failovermethod=priority
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-webtatic-el6
[root@localhost ~]#
[root@localhost ~]#
```

把webtatic这3个源移除掉：
```
[root@localhost ~]# mv /etc/yum.repos.d/webtatic-archive.repo /etc/yum.repos.d/webtatic-archive.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/webtatic.repo /etc/yum.repos.d/webtatic.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/webtatic-testing.repo /etc/yum.repos.d/webtatic-testing.repo.bak.20220507          
[root@localhost ~]#
[root@localhost ~]# ls -l /etc/yum.repos.d
total 84
-rw-r--r--  1 root root 2533 Mar  2 09:33 CentOS-Base.repo
-rw-r--r--  1 root root 1991 Aug 22  2020 CentOS-Base.repo.bak
-rw-r--r--  1 root root 1986 Aug 26  2020 CentOS-Base.repo.bak.20220507
-rw-r--r--  1 root root 2051 Aug 22  2020 CentOS-Base.repo.bak.tsinghua
-rw-r--r--. 1 root root  647 Aug  3  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  289 Aug  3  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Aug  3  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 6259 Aug  3  2015 CentOS-Vault.repo
-rw-r--r--  1 root root  712 Mar  2 09:37 epel.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak.20220507
-rw-r--r--  1 root root 1056 Nov  5  2012 epel-testing.repo
-rw-r--r--  1 root root  133 Feb 15 02:44 gitlab-ce.repo
-rw-r--r--  1 root root  218 Aug 26  2020 mysql-community.repo.bak
-rw-r--r--  1 root root 1885 Apr 27  2017 mysql-community-source.repo
-rw-r--r--  1 root root  111 Aug 20  2020 nginx.repo
-rw-r--r--  1 root root  422 Oct  1  2015 puppetlabs-pc1.repo
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-archive.repo.bak.20220507
-rw-r--r--  1 root root  865 Aug 22  2020 webtatic.repo.bak.20220507
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-testing.repo.bak.20220507
[root@localhost ~]#
```

保险期间，只保留上面下载的两个源，其他的都移除掉：
```
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Debuginfo.repo /etc/yum.repos.d/CentOS-Debuginfo.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-fasttrack.repo /etc/yum.repos.d/CentOS-fasttrack.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Media.repo /etc/yum.repos.d/CentOS-Media.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/CentOS-Vault.repo /etc/yum.repos.d/CentOS-Vault.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/epel-testing.repo /etc/yum.repos.d/epel-testing.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/gitlab-ce.repo /etc/yum.repos.d/gitlab-ce.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/mysql-community-source.repo /etc/yum.repos.d/mysql-community-source.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/nginx.repo /etc/yum.repos.d/nginx.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# mv /etc/yum.repos.d/puppetlabs-pc1.repo /etc/yum.repos.d/puppetlabs-pc1.repo.bak.20220507
[root@localhost ~]#
[root@localhost ~]# ls -l /etc/yum.repos.d
total 84
-rw-r--r--  1 root root 2533 Mar  2 09:33 CentOS-Base.repo
-rw-r--r--  1 root root 1991 Aug 22  2020 CentOS-Base.repo.bak
-rw-r--r--  1 root root 1986 Aug 26  2020 CentOS-Base.repo.bak.20220507
-rw-r--r--  1 root root 2051 Aug 22  2020 CentOS-Base.repo.bak.tsinghua
-rw-r--r--. 1 root root  647 Aug  3  2015 CentOS-Debuginfo.repo.bak.20220507
-rw-r--r--. 1 root root  289 Aug  3  2015 CentOS-fasttrack.repo.bak.20220507
-rw-r--r--. 1 root root  630 Aug  3  2015 CentOS-Media.repo.bak.20220507
-rw-r--r--. 1 root root 6259 Aug  3  2015 CentOS-Vault.repo.bak.20220507
-rw-r--r--  1 root root  712 Mar  2 09:37 epel.repo
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak
-rw-r--r--  1 root root  957 Aug 26  2020 epel.repo.bak.20220507
-rw-r--r--  1 root root 1056 Nov  5  2012 epel-testing.repo.bak.20220507
-rw-r--r--  1 root root  133 Feb 15 02:44 gitlab-ce.repo.bak.20220507
-rw-r--r--  1 root root  218 Aug 26  2020 mysql-community.repo.bak
-rw-r--r--  1 root root 1885 Apr 27  2017 mysql-community-source.repo.bak.20220507
-rw-r--r--  1 root root  111 Aug 20  2020 nginx.repo.bak.20220507
-rw-r--r--  1 root root  422 Oct  1  2015 puppetlabs-pc1.repo.bak.20220507
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-archive.repo.bak.20220507
-rw-r--r--  1 root root  865 Aug 22  2020 webtatic.repo.bak.20220507
-rw-r--r--  1 root root  963 Nov 13  2016 webtatic-testing.repo.bak.20220507
[root@localhost ~]#
```

重新清理缓存，生成缓存：
> yum clean all
> 
> yum makecache

过程：
```
[root@localhost ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base epel-archive extras updates
Cleaning up Everything
Cleaning up list of fastest mirrors
[root@localhost ~]#
[root@localhost ~]# yum makecache
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
base                                                         | 3.7 kB     00:00
base/group_gz                                                | 242 kB     00:00
base/filelists_db                                            | 6.4 MB     00:05
base/primary_db                                              | 4.7 MB     00:03
base/other_db                                                | 2.8 MB     00:02
epel-archive                                                 | 4.7 kB     00:00
epel-archive/group_gz                                        |  74 kB     00:00
epel-archive/filelists_db                                    | 7.9 MB     00:06
epel-archive/prestodelta                                     |  574 B     00:00
epel-archive/primary_db                                      | 6.1 MB     00:07
epel-archive/other_db                                        | 3.0 MB     00:04
extras                                                       | 3.4 kB     00:00
extras/filelists_db                                          |  24 kB     00:00
extras/prestodelta                                           | 2.2 kB     00:00
extras/primary_db                                            |  29 kB     00:00
extras/other_db                                              |  14 kB     00:00
updates                                                      | 3.4 kB     00:00
updates/filelists_db                                         | 8.4 MB     00:06
updates/prestodelta                                          | 357 kB     00:00
updates/primary_db                                           |  12 MB     00:11
updates/other_db                                             | 479 kB     00:00
Metadata Cache Created
[root@localhost ~]#
```

#### 安装docker

安装docker：
> yum install -y docker

```
[root@localhost ~]# yum install -y docker
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package docker.x86_64 0:1.5-5.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================
 Package               Arch             Version            Repository            Size
======================================================================================
Installing:
 docker                x86_64           1.5-5.el6          epel-archive          19 k

Transaction Summary
======================================================================================
Install       1 Package(s)

Total download size: 19 k
Installed size: 37 k
Downloading Packages:
docker-1.5-5.el6.x86_64.rpm                                      |  19 kB     00:00
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : docker-1.5-5.el6.x86_64                           1/1
  Verifying  : docker-1.5-5.el6.x86_64                           1/1

Installed:
  docker.x86_64 0:1.5-5.el6

Complete!
[root@localhost ~]#
```

查看版本：
```
[root@localhost ~]# docker -v
docker - version 1.5
Copyright 2003, Ben Jansens <ben@orodu.net>

Usage: docker [OPTIONS]

Options:
  -help             Show this help.
  -display DISLPAY  The X display to connect to.
  -border           The width of the border to put around the
                    system tray icons. Defaults to 1.
  -vertical         Line up the icons vertically. Defaults to
                    horizontally.
  -wmaker           WindowMaker mode. This makes docker a
                    fixed size (64x64) to appear nicely in
                    in WindowMaker.
                    Note: In this mode, you have a fixed
                    number of icons that docker can hold.
  -iconsize SIZE    The size (width and height) to display
                    icons as in the system tray. Defaults to
                    24.
[root@localhost ~]#
[root@localhost ~]# docker version
Segmentation Fault or Critical Error encountered. Dumping core and aborting.
Aborted
[root@localhost ~]#
```

报错了`Segmentation Fault or Critical Error encountered. Dumping core and aborting. Aborted`。

查询后，应该安装 docker-io，而不是 docker。需要卸载 docker，然后安装 docker-io。

##### 方法一 

查询之前是否已经安装docker，及docker的版本：
> yum list installed | grep docker

卸载安装的docker：
> yum remove -y docker.x86_64

```
[root@localhost ~]# yum list installed | grep docker
docker.x86_64          1.5-5.el6        @epel-archive
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# yum remove -y docker
Loaded plugins: fastestmirror
Setting up Remove Process
Resolving Dependencies
--> Running transaction check
---> Package docker.x86_64 0:1.5-5.el6 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================================================
 Package               Arch                  Version                   Repository                    Size
==========================================================================================================
Removing:
 docker                x86_64                1.5-5.el6                 @epel-archive                 37 k

Transaction Summary
==========================================================================================================
Remove        1 Package(s)

Installed size: 37 k
Downloading Packages:
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Erasing    : docker-1.5-5.el6.x86_64                                                                1/1
  Verifying  : docker-1.5-5.el6.x86_64                                                                1/1

Removed:
  docker.x86_64 0:1.5-5.el6

Complete!
[root@localhost ~]#
```

重新安装docker-io：
> yum install -y docker-io

```
[root@localhost ~]# yum install -y docker-io
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package docker-io available.
Error: Nothing to do
[root@localhost ~]#
```

提示未找到包。

升级yum：
> yum update

```
[root@localhost ~]# yum update
Loaded plugins: fastestmirror
Setting up Update Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Resolving Dependencies
--> Running transaction check
---> Package acl.x86_64 0:2.2.49-6.el6 will be updated
---> Package acl.x86_64 0:2.2.49-7.el6_9.1 will be an update
---> Package audit.x86_64 0:2.3.7-5.el6 will be updated
---> Package audit.x86_64 0:2.4.5-6.el6 will be an update
---> Package audit-libs.x86_64 0:2.3.7-5.el6 will be updated
---> Package audit-libs.x86_64 0:2.4.5-6.el6 will be an update
---> Package bash.x86_64 0:4.1.2-33.el6_7.1 will be updated
---> Package bash.x86_64 0:4.1.2-48.el6 will be an update
---> Package binutils.x86_64 0:2.20.51.0.2-5.43.el6 will be updated
---> Package binutils.x86_64 0:2.20.51.0.2-5.48.el6_10.1 will be an update
---> Package ca-certificates.noarch 0:2015.2.4-65.0.1.el6_6 will be updated
---> Package ca-certificates.noarch 0:2020.2.41-65.1.el6_10 will be an update
---> Package centos-release.x86_64 0:6-7.el6.centos.12.3 will be updated
---> Package centos-release.x86_64 0:6-10.el6.centos.12.3 will be an update
---> Package chkconfig.x86_64 0:1.3.49.3-5.el6 will be updated
---> Package chkconfig.x86_64 0:1.3.49.5-1.el6 will be an update
---> Package coreutils.x86_64 0:8.4-37.el6 will be updated
---> Package coreutils.x86_64 0:8.4-47.el6 will be an update
---> Package coreutils-libs.x86_64 0:8.4-37.el6 will be updated
---> Package coreutils-libs.x86_64 0:8.4-47.el6 will be an update
---> Package cpio.x86_64 0:2.10-12.el6_5 will be updated
---> Package cpio.x86_64 0:2.10-13.el6 will be an update
---> Package cpp.x86_64 0:4.4.7-16.el6 will be updated
---> Package cpp.x86_64 0:4.4.7-23.el6 will be an update
---> Package cronie.x86_64 0:1.4.4-15.el6 will be updated
---> Package cronie.x86_64 0:1.4.4-16.el6_8.2 will be an update
---> Package cronie-anacron.x86_64 0:1.4.4-15.el6 will be updated
---> Package cronie-anacron.x86_64 0:1.4.4-16.el6_8.2 will be an update
---> Package curl.x86_64 0:7.19.7-46.el6 will be updated
---> Package curl.x86_64 0:7.19.7-54.el6_10 will be an update
---> Package db4.x86_64 0:4.7.25-20.el6_7 will be updated
---> Package db4.x86_64 0:4.7.25-22.el6 will be an update
---> Package db4-utils.x86_64 0:4.7.25-20.el6_7 will be updated
---> Package db4-utils.x86_64 0:4.7.25-22.el6 will be an update
---> Package dbus-libs.x86_64 1:1.2.24-8.el6_6 will be updated
---> Package dbus-libs.x86_64 1:1.2.24-11.el6_10 will be an update
---> Package device-mapper.x86_64 0:1.02.95-3.el6_7.3 will be updated
---> Package device-mapper.x86_64 0:1.02.117-12.el6_9.1 will be an update
---> Package device-mapper-event.x86_64 0:1.02.95-3.el6_7.3 will be updated
---> Package device-mapper-event.x86_64 0:1.02.117-12.el6_9.1 will be an update
---> Package device-mapper-event-libs.x86_64 0:1.02.95-3.el6_7.3 will be updated
---> Package device-mapper-event-libs.x86_64 0:1.02.117-12.el6_9.1 will be an update
---> Package device-mapper-libs.x86_64 0:1.02.95-3.el6_7.3 will be updated
---> Package device-mapper-libs.x86_64 0:1.02.117-12.el6_9.1 will be an update
---> Package device-mapper-multipath.x86_64 0:0.4.9-87.el6 will be updated
---> Package device-mapper-multipath.x86_64 0:0.4.9-106.el6_10.1 will be an update
---> Package device-mapper-multipath-libs.x86_64 0:0.4.9-87.el6 will be updated
---> Package device-mapper-multipath-libs.x86_64 0:0.4.9-106.el6_10.1 will be an update
---> Package device-mapper-persistent-data.x86_64 0:0.3.2-1.el6 will be updated
---> Package device-mapper-persistent-data.x86_64 0:0.6.2-0.2.rc7.el6 will be an update
---> Package dhclient.x86_64 12:4.1.1-49.P1.el6.centos will be updated
---> Package dhclient.x86_64 12:4.1.1-63.P1.el6.centos will be an update
---> Package dhcp-common.x86_64 12:4.1.1-49.P1.el6.centos will be updated
---> Package dhcp-common.x86_64 12:4.1.1-63.P1.el6.centos will be an update
---> Package dmidecode.x86_64 1:2.12-6.el6 will be updated
---> Package dmidecode.x86_64 1:2.12-7.el6 will be an update
---> Package dracut.noarch 0:004-388.el6 will be updated
---> Package dracut.noarch 0:004-411.el6 will be an update
---> Package dracut-kernel.noarch 0:004-388.el6 will be updated
---> Package dracut-kernel.noarch 0:004-411.el6 will be an update
---> Package efibootmgr.x86_64 0:0.5.4-13.el6 will be updated
---> Package efibootmgr.x86_64 0:0.5.4-15.el6 will be an update
---> Package elfutils-libelf.x86_64 0:0.161-3.el6 will be updated
---> Package elfutils-libelf.x86_64 0:0.164-2.el6 will be an update
---> Package expat.x86_64 0:2.0.1-11.el6_2 will be updated
---> Package expat.x86_64 0:2.0.1-13.el6_8 will be an update
---> Package file.x86_64 0:5.04-21.el6 will be updated
---> Package file.x86_64 0:5.04-30.el6 will be an update
---> Package file-libs.x86_64 0:5.04-21.el6 will be updated
---> Package file-libs.x86_64 0:5.04-30.el6 will be an update
---> Package findutils.x86_64 1:4.4.2-6.el6 will be updated
---> Package findutils.x86_64 1:4.4.2-9.el6 will be an update
---> Package fuse.x86_64 0:2.8.3-4.el6 will be updated
---> Package fuse.x86_64 0:2.8.3-5.el6 will be an update
---> Package gawk.x86_64 0:3.1.7-10.el6 will be updated
---> Package gawk.x86_64 0:3.1.7-10.el6_7.3 will be an update
---> Package gcc.x86_64 0:4.4.7-16.el6 will be updated
---> Package gcc.x86_64 0:4.4.7-23.el6 will be an update
---> Package gcc-c++.x86_64 0:4.4.7-16.el6 will be updated
---> Package gcc-c++.x86_64 0:4.4.7-23.el6 will be an update
---> Package gdbm.x86_64 0:1.8.0-38.el6 will be updated
---> Package gdbm.x86_64 0:1.8.0-39.el6 will be an update
---> Package glib2.x86_64 0:2.28.8-4.el6 will be updated
---> Package glib2.x86_64 0:2.28.8-10.el6 will be an update
---> Package glibc.x86_64 0:2.12-1.166.el6_7.3 will be updated
---> Package glibc.x86_64 0:2.12-1.212.el6_10.3 will be an update
---> Package glibc-common.x86_64 0:2.12-1.166.el6_7.3 will be updated
---> Package glibc-common.x86_64 0:2.12-1.212.el6_10.3 will be an update
---> Package glibc-devel.x86_64 0:2.12-1.166.el6_7.3 will be updated
---> Package glibc-devel.x86_64 0:2.12-1.212.el6_10.3 will be an update
---> Package glibc-headers.x86_64 0:2.12-1.166.el6_7.3 will be updated
---> Package glibc-headers.x86_64 0:2.12-1.212.el6_10.3 will be an update
---> Package gmp.x86_64 0:4.3.1-7.el6_2.2 will be updated
---> Package gmp.x86_64 0:4.3.1-13.el6 will be an update
---> Package gnupg2.x86_64 0:2.0.14-8.el6 will be updated
---> Package gnupg2.x86_64 0:2.0.14-9.el6_10 will be an update
---> Package grep.x86_64 0:2.20-3.el6 will be updated
---> Package grep.x86_64 0:2.20-6.el6 will be an update
---> Package grub.x86_64 1:0.97-94.el6 will be updated
---> Package grub.x86_64 1:0.97-99.el6 will be an update
---> Package gzip.x86_64 0:1.3.12-22.el6 will be updated
---> Package gzip.x86_64 0:1.3.12-24.el6 will be an update
---> Package hwdata.noarch 0:0.233-14.1.el6 will be updated
---> Package hwdata.noarch 0:0.233-20.1.el6 will be an update
---> Package initscripts.x86_64 0:9.03.49-1.el6.centos.1 will be updated
---> Package initscripts.x86_64 0:9.03.61-1.el6.centos will be an update
---> Package iproute.x86_64 0:2.6.32-45.el6 will be updated
---> Package iproute.x86_64 0:2.6.32-57.el6 will be an update
---> Package iptables.x86_64 0:1.4.7-16.el6 will be updated
---> Package iptables.x86_64 0:1.4.7-19.el6 will be an update
---> Package iptables-ipv6.x86_64 0:1.4.7-16.el6 will be updated
---> Package iptables-ipv6.x86_64 0:1.4.7-19.el6 will be an update
---> Package iputils.x86_64 0:20071127-20.el6 will be updated
---> Package iputils.x86_64 0:20071127-24.el6 will be an update
---> Package iscsi-initiator-utils.x86_64 0:6.2.0.873-14.el6 will be updated
---> Package iscsi-initiator-utils.x86_64 0:6.2.0.873-27.el6_9 will be an update
---> Package kernel.x86_64 0:2.6.32-754.35.1.el6 will be installed
---> Package kernel-devel.x86_64 0:2.6.32-754.35.1.el6 will be installed
---> Package kernel-firmware.noarch 0:2.6.32-573.7.1.el6 will be updated
---> Package kernel-firmware.noarch 0:2.6.32-754.35.1.el6 will be an update
---> Package kernel-headers.x86_64 0:2.6.32-573.7.1.el6 will be updated
---> Package kernel-headers.x86_64 0:2.6.32-754.35.1.el6 will be an update
---> Package kpartx.x86_64 0:0.4.9-87.el6 will be updated
---> Package kpartx.x86_64 0:0.4.9-106.el6_10.1 will be an update
---> Package libX11.x86_64 0:1.6.4-3.el6 will be updated
---> Package libX11.x86_64 0:1.6.4-4.el6_10 will be an update
---> Package libX11-common.noarch 0:1.6.4-3.el6 will be updated
---> Package libX11-common.noarch 0:1.6.4-4.el6_10 will be an update
---> Package libacl.x86_64 0:2.2.49-6.el6 will be updated
---> Package libacl.x86_64 0:2.2.49-7.el6_9.1 will be an update
---> Package libblkid.x86_64 0:2.17.2-12.18.el6 will be updated
---> Package libblkid.x86_64 0:2.17.2-12.28.el6_9.2 will be an update
---> Package libcurl.x86_64 0:7.19.7-46.el6 will be updated
---> Package libcurl.x86_64 0:7.19.7-54.el6_10 will be an update
---> Package libdrm.x86_64 0:2.4.59-2.el6 will be updated
---> Package libdrm.x86_64 0:2.4.65-2.el6 will be an update
---> Package libgcc.x86_64 0:4.4.7-16.el6 will be updated
---> Package libgcc.x86_64 0:4.4.7-23.el6 will be an update
---> Package libgcrypt.x86_64 0:1.4.5-11.el6_4 will be updated
---> Package libgcrypt.x86_64 0:1.4.5-12.el6_8 will be an update
---> Package libgomp.x86_64 0:4.4.7-16.el6 will be updated
---> Package libgomp.x86_64 0:4.4.7-23.el6 will be an update
---> Package libnih.x86_64 0:1.0.1-7.el6 will be updated
---> Package libnih.x86_64 0:1.0.1-8.el6 will be an update
---> Package libpciaccess.x86_64 0:0.13.3-0.1.el6 will be updated
---> Package libpciaccess.x86_64 0:0.13.4-1.el6 will be an update
---> Package libssh2.x86_64 0:1.4.2-1.el6_6.1 will be updated
---> Package libssh2.x86_64 0:1.4.2-3.el6_10.1 will be an update
---> Package libstdc++.x86_64 0:4.4.7-16.el6 will be updated
---> Package libstdc++.x86_64 0:4.4.7-23.el6 will be an update
---> Package libstdc++-devel.x86_64 0:4.4.7-16.el6 will be updated
---> Package libstdc++-devel.x86_64 0:4.4.7-23.el6 will be an update
---> Package libudev.x86_64 0:147-2.63.el6 will be updated
---> Package libudev.x86_64 0:147-2.74.el6_10 will be an update
---> Package libuuid.x86_64 0:2.17.2-12.18.el6 will be updated
---> Package libuuid.x86_64 0:2.17.2-12.28.el6_9.2 will be an update
---> Package libxml2.x86_64 0:2.7.6-20.el6 will be updated
---> Package libxml2.x86_64 0:2.7.6-21.el6_8.1 will be an update
---> Package logrotate.x86_64 0:3.7.8-23.el6 will be updated
---> Package logrotate.x86_64 0:3.7.8-28.el6 will be an update
---> Package lvm2.x86_64 0:2.02.118-3.el6_7.3 will be updated
---> Package lvm2.x86_64 0:2.02.143-12.el6_9.1 will be an update
---> Package lvm2-libs.x86_64 0:2.02.118-3.el6_7.3 will be updated
---> Package lvm2-libs.x86_64 0:2.02.143-12.el6_9.1 will be an update
---> Package make.x86_64 1:3.81-20.el6 will be updated
---> Package make.x86_64 1:3.81-23.el6 will be an update
---> Package mdadm.x86_64 0:3.3.2-5.el6 will be updated
---> Package mdadm.x86_64 0:3.3.4-8.el6 will be an update
---> Package module-init-tools.x86_64 0:3.9-25.el6 will be updated
---> Package module-init-tools.x86_64 0:3.9-26.el6 will be an update
---> Package net-tools.x86_64 0:1.60-110.el6_2 will be updated
---> Package net-tools.x86_64 0:1.60-114.el6 will be an update
---> Package newt.x86_64 0:0.52.11-3.el6 will be updated
---> Package newt.x86_64 0:0.52.11-4.el6 will be an update
---> Package newt-python.x86_64 0:0.52.11-3.el6 will be updated
---> Package newt-python.x86_64 0:0.52.11-4.el6 will be an update
---> Package nspr.x86_64 0:4.10.8-1.el6_6 will be updated
---> Package nspr.x86_64 0:4.21.0-1.el6_10 will be an update
---> Package nss.x86_64 0:3.19.1-3.el6_6 will be updated
---> Package nss.x86_64 0:3.44.0-7.el6_10 will be an update
---> Package nss-softokn.x86_64 0:3.14.3-23.el6_7 will be updated
---> Package nss-softokn.x86_64 0:3.44.0-6.el6_10 will be an update
---> Package nss-softokn-freebl.x86_64 0:3.14.3-23.el6_7 will be updated
---> Package nss-softokn-freebl.x86_64 0:3.44.0-6.el6_10 will be an update
---> Package nss-sysinit.x86_64 0:3.19.1-3.el6_6 will be updated
---> Package nss-sysinit.x86_64 0:3.44.0-7.el6_10 will be an update
---> Package nss-tools.x86_64 0:3.19.1-3.el6_6 will be updated
---> Package nss-tools.x86_64 0:3.44.0-7.el6_10 will be an update
---> Package nss-util.x86_64 0:3.19.1-1.el6_6 will be updated
---> Package nss-util.x86_64 0:3.44.0-1.el6_10 will be an update
---> Package openldap.x86_64 0:2.4.40-6.el6_7 will be updated
---> Package openldap.x86_64 0:2.4.40-16.el6 will be an update
---> Package openssh.x86_64 0:5.3p1-112.el6_7 will be updated
---> Package openssh.x86_64 0:5.3p1-124.el6_10 will be an update
---> Package openssh-clients.x86_64 0:5.3p1-112.el6_7 will be updated
---> Package openssh-clients.x86_64 0:5.3p1-124.el6_10 will be an update
---> Package openssh-server.x86_64 0:5.3p1-112.el6_7 will be updated
---> Package openssh-server.x86_64 0:5.3p1-124.el6_10 will be an update
---> Package pam.x86_64 0:1.1.1-20.el6_7.1 will be updated
---> Package pam.x86_64 0:1.1.1-24.el6 will be an update
---> Package passwd.x86_64 0:0.77-4.el6_2.2 will be updated
---> Package passwd.x86_64 0:0.77-7.el6 will be an update
---> Package perl.x86_64 4:5.10.1-141.el6 will be updated
---> Package perl.x86_64 4:5.10.1-144.el6 will be an update
---> Package perl-Module-Pluggable.x86_64 1:3.90-141.el6 will be updated
---> Package perl-Module-Pluggable.x86_64 1:3.90-144.el6 will be an update
---> Package perl-Pod-Escapes.x86_64 1:1.04-141.el6 will be updated
---> Package perl-Pod-Escapes.x86_64 1:1.04-144.el6 will be an update
---> Package perl-Pod-Simple.x86_64 1:3.13-141.el6 will be updated
---> Package perl-Pod-Simple.x86_64 1:3.13-144.el6 will be an update
---> Package perl-libs.x86_64 4:5.10.1-141.el6 will be updated
---> Package perl-libs.x86_64 4:5.10.1-144.el6 will be an update
---> Package perl-version.x86_64 3:0.77-141.el6 will be updated
---> Package perl-version.x86_64 3:0.77-144.el6 will be an update
---> Package plymouth.x86_64 0:0.8.3-27.el6.centos.1 will be updated
---> Package plymouth.x86_64 0:0.8.3-29.el6.centos will be an update
---> Package plymouth-core-libs.x86_64 0:0.8.3-27.el6.centos.1 will be updated
---> Package plymouth-core-libs.x86_64 0:0.8.3-29.el6.centos will be an update
---> Package plymouth-scripts.x86_64 0:0.8.3-27.el6.centos.1 will be updated
---> Package plymouth-scripts.x86_64 0:0.8.3-29.el6.centos will be an update
---> Package policycoreutils.x86_64 0:2.0.83-24.el6 will be updated
---> Package policycoreutils.x86_64 0:2.0.83-30.1.el6_8 will be an update
---> Package procps.x86_64 0:3.2.8-34.el6_7 will be updated
---> Package procps.x86_64 0:3.2.8-45.el6_9.3 will be an update
---> Package psmisc.x86_64 0:22.6-19.el6_5 will be updated
---> Package psmisc.x86_64 0:22.6-24.el6 will be an update
---> Package python.x86_64 0:2.6.6-64.el6 will be updated
---> Package python.x86_64 0:2.6.6-68.el6_10 will be an update
---> Package python-libs.x86_64 0:2.6.6-64.el6 will be updated
---> Package python-libs.x86_64 0:2.6.6-68.el6_10 will be an update
---> Package python-pycurl.x86_64 0:7.19.0-8.el6 will be updated
---> Package python-pycurl.x86_64 0:7.19.0-9.el6 will be an update
---> Package python-urlgrabber.noarch 0:3.9.1-9.el6 will be updated
---> Package python-urlgrabber.noarch 0:3.9.1-11.el6 will be an update
---> Package rpm.x86_64 0:4.8.0-47.el6 will be updated
---> Package rpm.x86_64 0:4.8.0-59.el6 will be an update
---> Package rpm-libs.x86_64 0:4.8.0-47.el6 will be updated
---> Package rpm-libs.x86_64 0:4.8.0-59.el6 will be an update
---> Package rpm-python.x86_64 0:4.8.0-47.el6 will be updated
---> Package rpm-python.x86_64 0:4.8.0-59.el6 will be an update
---> Package rsyslog.x86_64 0:5.8.10-10.el6_6 will be updated
---> Package rsyslog.x86_64 0:5.8.10-12.el6 will be an update
---> Package selinux-policy.noarch 0:3.7.19-279.el6_7.6 will be updated
---> Package selinux-policy.noarch 0:3.7.19-312.el6 will be an update
---> Package selinux-policy-targeted.noarch 0:3.7.19-279.el6_7.6 will be updated
---> Package selinux-policy-targeted.noarch 0:3.7.19-312.el6 will be an update
---> Package setup.noarch 0:2.8.14-20.el6_4.1 will be updated
---> Package setup.noarch 0:2.8.14-23.el6 will be an update
---> Package shadow-utils.x86_64 2:4.1.4.2-19.el6_6.1 will be updated
---> Package shadow-utils.x86_64 2:4.1.5.1-5.el6 will be an update
---> Package sudo.x86_64 0:1.8.6p3-20.el6_7 will be updated
---> Package sudo.x86_64 0:1.8.6p3-29.el6_10.3 will be an update
---> Package tar.x86_64 2:1.23-13.el6 will be updated
---> Package tar.x86_64 2:1.23-15.el6_8 will be an update
---> Package tcp_wrappers-libs.x86_64 0:7.6-57.el6 will be updated
---> Package tcp_wrappers-libs.x86_64 0:7.6-58.el6 will be an update
---> Package tzdata.noarch 0:2015g-2.el6 will be updated
---> Package tzdata.noarch 0:2020d-1.el6 will be an update
---> Package udev.x86_64 0:147-2.63.el6 will be updated
---> Package udev.x86_64 0:147-2.74.el6_10 will be an update
---> Package upstart.x86_64 0:0.6.5-13.el6_5.3 will be updated
---> Package upstart.x86_64 0:0.6.5-17.el6 will be an update
---> Package util-linux-ng.x86_64 0:2.17.2-12.18.el6 will be updated
---> Package util-linux-ng.x86_64 0:2.17.2-12.28.el6_9.2 will be an update
---> Package vim-minimal.x86_64 2:7.4.629-5.el6 will be updated
---> Package vim-minimal.x86_64 2:7.4.629-5.el6_10.2 will be an update
---> Package wget.x86_64 0:1.12-5.el6_6.1 will be updated
---> Package wget.x86_64 0:1.12-10.el6 will be an update
---> Package xfsprogs.x86_64 0:3.1.1-16.el6 will be updated
---> Package xfsprogs.x86_64 0:3.1.1-20.el6 will be an update
---> Package yum.noarch 0:3.2.29-69.el6.centos will be updated
---> Package yum.noarch 0:3.2.29-81.el6.centos.0.1 will be an update
---> Package yum-plugin-fastestmirror.noarch 0:1.1.30-30.el6 will be updated
---> Package yum-plugin-fastestmirror.noarch 0:1.1.30-42.el6_10 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================================================
 Package                              Arch          Version                          Repository      Size
==========================================================================================================
Installing:
 kernel                               x86_64        2.6.32-754.35.1.el6              updates         32 M
 kernel-devel                         x86_64        2.6.32-754.35.1.el6              updates         11 M
Updating:
 acl                                  x86_64        2.2.49-7.el6_9.1                 base            76 k
 audit                                x86_64        2.4.5-6.el6                      base           204 k
 audit-libs                           x86_64        2.4.5-6.el6                      base            74 k
 bash                                 x86_64        4.1.2-48.el6                     base           910 k
 binutils                             x86_64        2.20.51.0.2-5.48.el6_10.1        updates        2.8 M
 ca-certificates                      noarch        2020.2.41-65.1.el6_10            updates        908 k
 centos-release                       x86_64        6-10.el6.centos.12.3             base            22 k
 chkconfig                            x86_64        1.3.49.5-1.el6                   base           160 k
 coreutils                            x86_64        8.4-47.el6                       base           3.0 M
 coreutils-libs                       x86_64        8.4-47.el6                       base            52 k
 cpio                                 x86_64        2.10-13.el6                      base           192 k
 cpp                                  x86_64        4.4.7-23.el6                     base           3.7 M
 cronie                               x86_64        1.4.4-16.el6_8.2                 base            75 k
 cronie-anacron                       x86_64        1.4.4-16.el6_8.2                 base            31 k
 curl                                 x86_64        7.19.7-54.el6_10                 updates        198 k
 db4                                  x86_64        4.7.25-22.el6                    base           564 k
 db4-utils                            x86_64        4.7.25-22.el6                    base           131 k
 dbus-libs                            x86_64        1:1.2.24-11.el6_10               updates        127 k
 device-mapper                        x86_64        1.02.117-12.el6_9.1              base           218 k
 device-mapper-event                  x86_64        1.02.117-12.el6_9.1              base           134 k
 device-mapper-event-libs             x86_64        1.02.117-12.el6_9.1              base           126 k
 device-mapper-libs                   x86_64        1.02.117-12.el6_9.1              base           257 k
 device-mapper-multipath              x86_64        0.4.9-106.el6_10.1               updates        133 k
 device-mapper-multipath-libs         x86_64        0.4.9-106.el6_10.1               updates        204 k
 device-mapper-persistent-data        x86_64        0.6.2-0.2.rc7.el6                base           463 k
 dhclient                             x86_64        12:4.1.1-63.P1.el6.centos        updates        323 k
 dhcp-common                          x86_64        12:4.1.1-63.P1.el6.centos        updates        145 k
 dmidecode                            x86_64        1:2.12-7.el6                     base            74 k
 dracut                               noarch        004-411.el6                      base           127 k
 dracut-kernel                        noarch        004-411.el6                      base            28 k
 efibootmgr                           x86_64        0.5.4-15.el6                     base            38 k
 elfutils-libelf                      x86_64        0.164-2.el6                      base           197 k
 expat                                x86_64        2.0.1-13.el6_8                   base            77 k
 file                                 x86_64        5.04-30.el6                      base            49 k
 file-libs                            x86_64        5.04-30.el6                      base           317 k
 findutils                            x86_64        1:4.4.2-9.el6                    base           447 k
 fuse                                 x86_64        2.8.3-5.el6                      base            77 k
 gawk                                 x86_64        3.1.7-10.el6_7.3                 base           780 k
 gcc                                  x86_64        4.4.7-23.el6                     base            10 M
 gcc-c++                              x86_64        4.4.7-23.el6                     base           4.7 M
 gdbm                                 x86_64        1.8.0-39.el6                     base            29 k
 glib2                                x86_64        2.28.8-10.el6                    base           1.7 M
 glibc                                x86_64        2.12-1.212.el6_10.3              updates        3.8 M
 glibc-common                         x86_64        2.12-1.212.el6_10.3              updates         14 M
 glibc-devel                          x86_64        2.12-1.212.el6_10.3              updates        991 k
 glibc-headers                        x86_64        2.12-1.212.el6_10.3              updates        620 k
 gmp                                  x86_64        4.3.1-13.el6                     base           207 k
 gnupg2                               x86_64        2.0.14-9.el6_10                  updates        1.6 M
 grep                                 x86_64        2.20-6.el6                       base           345 k
 grub                                 x86_64        1:0.97-99.el6                    base           939 k
 gzip                                 x86_64        1.3.12-24.el6                    base           116 k
 hwdata                               noarch        0.233-20.1.el6                   base           1.4 M
 initscripts                          x86_64        9.03.61-1.el6.centos             base           949 k
 iproute                              x86_64        2.6.32-57.el6                    base           386 k
 iptables                             x86_64        1.4.7-19.el6                     base           255 k
 iptables-ipv6                        x86_64        1.4.7-19.el6                     base           103 k
 iputils                              x86_64        20071127-24.el6                  base           121 k
 iscsi-initiator-utils                x86_64        6.2.0.873-27.el6_9               base           732 k
 kernel-firmware                      noarch        2.6.32-754.35.1.el6              updates         29 M
 kernel-headers                       x86_64        2.6.32-754.35.1.el6              updates        4.6 M
 kpartx                               x86_64        0.4.9-106.el6_10.1               updates         72 k
 libX11                               x86_64        1.6.4-4.el6_10                   updates        587 k
 libX11-common                        noarch        1.6.4-4.el6_10                   updates        171 k
 libacl                               x86_64        2.2.49-7.el6_9.1                 base            24 k
 libblkid                             x86_64        2.17.2-12.28.el6_9.2             base           119 k
 libcurl                              x86_64        7.19.7-54.el6_10                 updates        170 k
 libdrm                               x86_64        2.4.65-2.el6                     base           136 k
 libgcc                               x86_64        4.4.7-23.el6                     base           104 k
 libgcrypt                            x86_64        1.4.5-12.el6_8                   base           229 k
 libgomp                              x86_64        4.4.7-23.el6                     base           135 k
 libnih                               x86_64        1.0.1-8.el6                      base           138 k
 libpciaccess                         x86_64        0.13.4-1.el6                     base            24 k
 libssh2                              x86_64        1.4.2-3.el6_10.1                 updates        123 k
 libstdc++                            x86_64        4.4.7-23.el6                     base           296 k
 libstdc++-devel                      x86_64        4.4.7-23.el6                     base           1.6 M
 libudev                              x86_64        147-2.74.el6_10                  updates         78 k
 libuuid                              x86_64        2.17.2-12.28.el6_9.2             base            72 k
 libxml2                              x86_64        2.7.6-21.el6_8.1                 base           805 k
 logrotate                            x86_64        3.7.8-28.el6                     base            59 k
 lvm2                                 x86_64        2.02.143-12.el6_9.1              base           941 k
 lvm2-libs                            x86_64        2.02.143-12.el6_9.1              base           1.0 M
 make                                 x86_64        1:3.81-23.el6                    base           389 k
 mdadm                                x86_64        3.3.4-8.el6                      base           348 k
 module-init-tools                    x86_64        3.9-26.el6                       base           467 k
 net-tools                            x86_64        1.60-114.el6                     base           269 k
 newt                                 x86_64        0.52.11-4.el6                    base            97 k
 newt-python                          x86_64        0.52.11-4.el6                    base            48 k
 nspr                                 x86_64        4.21.0-1.el6_10                  updates        114 k
 nss                                  x86_64        3.44.0-7.el6_10                  updates        883 k
 nss-softokn                          x86_64        3.44.0-6.el6_10                  updates        288 k
 nss-softokn-freebl                   x86_64        3.44.0-6.el6_10                  updates        202 k
 nss-sysinit                          x86_64        3.44.0-7.el6_10                  updates         54 k
 nss-tools                            x86_64        3.44.0-7.el6_10                  updates        472 k
 nss-util                             x86_64        3.44.0-1.el6_10                  updates         73 k
 openldap                             x86_64        2.4.40-16.el6                    base           285 k
 openssh                              x86_64        5.3p1-124.el6_10                 updates        278 k
 openssh-clients                      x86_64        5.3p1-124.el6_10                 updates        444 k
 openssh-server                       x86_64        5.3p1-124.el6_10                 updates        330 k
 pam                                  x86_64        1.1.1-24.el6                     base           659 k
 passwd                               x86_64        0.77-7.el6                       base            89 k
 perl                                 x86_64        4:5.10.1-144.el6                 base            10 M
 perl-Module-Pluggable                x86_64        1:3.90-144.el6                   base            41 k
 perl-Pod-Escapes                     x86_64        1:1.04-144.el6                   base            33 k
 perl-Pod-Simple                      x86_64        1:3.13-144.el6                   base           213 k
 perl-libs                            x86_64        4:5.10.1-144.el6                 base           579 k
 perl-version                         x86_64        3:0.77-144.el6                   base            52 k
 plymouth                             x86_64        0.8.3-29.el6.centos              base            89 k
 plymouth-core-libs                   x86_64        0.8.3-29.el6.centos              base            88 k
 plymouth-scripts                     x86_64        0.8.3-29.el6.centos              base            31 k
 policycoreutils                      x86_64        2.0.83-30.1.el6_8                base           663 k
 procps                               x86_64        3.2.8-45.el6_9.3                 updates        220 k
 psmisc                               x86_64        22.6-24.el6                      base            82 k
 python                               x86_64        2.6.6-68.el6_10                  updates         76 k
 python-libs                          x86_64        2.6.6-68.el6_10                  updates        5.3 M
 python-pycurl                        x86_64        7.19.0-9.el6                     base            77 k
 python-urlgrabber                    noarch        3.9.1-11.el6                     base            86 k
 rpm                                  x86_64        4.8.0-59.el6                     base           906 k
 rpm-libs                             x86_64        4.8.0-59.el6                     base           318 k
 rpm-python                           x86_64        4.8.0-59.el6                     base            61 k
 rsyslog                              x86_64        5.8.10-12.el6                    base           650 k
 selinux-policy                       noarch        3.7.19-312.el6                   base           895 k
 selinux-policy-targeted              noarch        3.7.19-312.el6                   base           3.1 M
 setup                                noarch        2.8.14-23.el6                    base           151 k
 shadow-utils                         x86_64        2:4.1.5.1-5.el6                  base           1.0 M
 sudo                                 x86_64        1.8.6p3-29.el6_10.3              updates        712 k
 tar                                  x86_64        2:1.23-15.el6_8                  base           810 k
 tcp_wrappers-libs                    x86_64        7.6-58.el6                       base            62 k
 tzdata                               noarch        2020d-1.el6                      updates        514 k
 udev                                 x86_64        147-2.74.el6_10                  updates        358 k
 upstart                              x86_64        0.6.5-17.el6                     base           177 k
 util-linux-ng                        x86_64        2.17.2-12.28.el6_9.2             base           1.6 M
 vim-minimal                          x86_64        2:7.4.629-5.el6_10.2             updates        422 k
 wget                                 x86_64        1.12-10.el6                      base           484 k
 xfsprogs                             x86_64        3.1.1-20.el6                     base           725 k
 yum                                  noarch        3.2.29-81.el6.centos.0.1         updates        1.0 M
 yum-plugin-fastestmirror             noarch        1.1.30-42.el6_10                 updates         33 k

Transaction Summary
==========================================================================================================
Install       2 Package(s)
Upgrade     136 Package(s)

Total download size: 181 M
Is this ok [y/N]: y
Downloading Packages:
(1/138): acl-2.2.49-7.el6_9.1.x86_64.rpm                                           |  76 kB     00:00
(2/138): audit-2.4.5-6.el6.x86_64.rpm                                              | 204 kB     00:00
(3/138): audit-libs-2.4.5-6.el6.x86_64.rpm                                         |  74 kB     00:00
(4/138): bash-4.1.2-48.el6.x86_64.rpm                                              | 910 kB     00:00
(5/138): binutils-2.20.51.0.2-5.48.el6_10.1.x86_64.rpm                             | 2.8 MB     00:01
(6/138): ca-certificates-2020.2.41-65.1.el6_10.noarch.rpm                          | 908 kB     00:00
(7/138): centos-release-6-10.el6.centos.12.3.x86_64.rpm                            |  22 kB     00:00
(8/138): chkconfig-1.3.49.5-1.el6.x86_64.rpm                                       | 160 kB     00:00
(9/138): coreutils-8.4-47.el6.x86_64.rpm                                           | 3.0 MB     00:01
(10/138): coreutils-libs-8.4-47.el6.x86_64.rpm                                     |  52 kB     00:00
(11/138): cpio-2.10-13.el6.x86_64.rpm                                              | 192 kB     00:00
(12/138): cpp-4.4.7-23.el6.x86_64.rpm                                              | 3.7 MB     00:03
(13/138): cronie-1.4.4-16.el6_8.2.x86_64.rpm                                       |  75 kB     00:00
(14/138): cronie-anacron-1.4.4-16.el6_8.2.x86_64.rpm                               |  31 kB     00:00
(15/138): curl-7.19.7-54.el6_10.x86_64.rpm                                         | 198 kB     00:00
(16/138): db4-4.7.25-22.el6.x86_64.rpm                                             | 564 kB     00:00
(17/138): db4-utils-4.7.25-22.el6.x86_64.rpm                                       | 131 kB     00:00
(18/138): dbus-libs-1.2.24-11.el6_10.x86_64.rpm                                    | 127 kB     00:00
(19/138): device-mapper-1.02.117-12.el6_9.1.x86_64.rpm                             | 218 kB     00:00
(20/138): device-mapper-event-1.02.117-12.el6_9.1.x86_64.rpm                       | 134 kB     00:00
(21/138): device-mapper-event-libs-1.02.117-12.el6_9.1.x86_64.rpm                  | 126 kB     00:00
(22/138): device-mapper-libs-1.02.117-12.el6_9.1.x86_64.rpm                        | 257 kB     00:00
(23/138): device-mapper-multipath-0.4.9-106.el6_10.1.x86_64.rpm                    | 133 kB     00:00
(24/138): device-mapper-multipath-libs-0.4.9-106.el6_10.1.x86_64.rpm               | 204 kB     00:00
(25/138): device-mapper-persistent-data-0.6.2-0.2.rc7.el6.x86_64.rpm               | 463 kB     00:00
(26/138): dhclient-4.1.1-63.P1.el6.centos.x86_64.rpm                               | 323 kB     00:00
(27/138): dhcp-common-4.1.1-63.P1.el6.centos.x86_64.rpm                            | 145 kB     00:00
(28/138): dmidecode-2.12-7.el6.x86_64.rpm                                          |  74 kB     00:00
(29/138): dracut-004-411.el6.noarch.rpm                                            | 127 kB     00:00
(30/138): dracut-kernel-004-411.el6.noarch.rpm                                     |  28 kB     00:00
(31/138): efibootmgr-0.5.4-15.el6.x86_64.rpm                                       |  38 kB     00:00
(32/138): elfutils-libelf-0.164-2.el6.x86_64.rpm                                   | 197 kB     00:00
(33/138): expat-2.0.1-13.el6_8.x86_64.rpm                                          |  77 kB     00:00
(34/138): file-5.04-30.el6.x86_64.rpm                                              |  49 kB     00:00
(35/138): file-libs-5.04-30.el6.x86_64.rpm                                         | 317 kB     00:00
(36/138): findutils-4.4.2-9.el6.x86_64.rpm                                         | 447 kB     00:00
(37/138): fuse-2.8.3-5.el6.x86_64.rpm                                              |  77 kB     00:00
(38/138): gawk-3.1.7-10.el6_7.3.x86_64.rpm                                         | 780 kB     00:00
(39/138): gcc-4.4.7-23.el6.x86_64.rpm                                              |  10 MB     00:08
(40/138): gcc-c++-4.4.7-23.el6.x86_64.rpm                                          | 4.7 MB     00:07
(41/138): gdbm-1.8.0-39.el6.x86_64.rpm                                             |  29 kB     00:00
(42/138): glib2-2.28.8-10.el6.x86_64.rpm                                           | 1.7 MB     00:01
(43/138): glibc-2.12-1.212.el6_10.3.x86_64.rpm                                     | 3.8 MB     00:03
(44/138): glibc-common-2.12-1.212.el6_10.3.x86_64.rpm                              |  14 MB     00:12
(45/138): glibc-devel-2.12-1.212.el6_10.3.x86_64.rpm                               | 991 kB     00:00
(46/138): glibc-headers-2.12-1.212.el6_10.3.x86_64.rpm                             | 620 kB     00:00
(47/138): gmp-4.3.1-13.el6.x86_64.rpm                                              | 207 kB     00:00
(48/138): gnupg2-2.0.14-9.el6_10.x86_64.rpm                                        | 1.6 MB     00:00
(49/138): grep-2.20-6.el6.x86_64.rpm                                               | 345 kB     00:00
(50/138): grub-0.97-99.el6.x86_64.rpm                                              | 939 kB     00:00
(51/138): gzip-1.3.12-24.el6.x86_64.rpm                                            | 116 kB     00:00
(52/138): hwdata-0.233-20.1.el6.noarch.rpm                                         | 1.4 MB     00:00
(53/138): initscripts-9.03.61-1.el6.centos.x86_64.rpm                              | 949 kB     00:00
(54/138): iproute-2.6.32-57.el6.x86_64.rpm                                         | 386 kB     00:00
(55/138): iptables-1.4.7-19.el6.x86_64.rpm                                         | 255 kB     00:00
(56/138): iptables-ipv6-1.4.7-19.el6.x86_64.rpm                                    | 103 kB     00:00
(57/138): iputils-20071127-24.el6.x86_64.rpm                                       | 121 kB     00:00
(58/138): iscsi-initiator-utils-6.2.0.873-27.el6_9.x86_64.rpm                      | 732 kB     00:00
(59/138): kernel-2.6.32-754.35.1.el6.x86_64.rpm                                    |  32 MB     00:32
(60/138): kernel-devel-2.6.32-754.35.1.el6.x86_64.rpm                              |  11 MB     00:14
(61/138): kernel-firmware-2.6.32-754.35.1.el6.noarch.rpm                           |  29 MB     00:54
(62/138): kernel-headers-2.6.32-754.35.1.el6.x86_64.rpm                            | 4.6 MB     00:11
(63/138): kpartx-0.4.9-106.el6_10.1.x86_64.rpm                                     |  72 kB     00:00
(64/138): libX11-1.6.4-4.el6_10.x86_64.rpm                                         | 587 kB     00:01
(65/138): libX11-common-1.6.4-4.el6_10.noarch.rpm                                  | 171 kB     00:00
(66/138): libacl-2.2.49-7.el6_9.1.x86_64.rpm                                       |  24 kB     00:00
(67/138): libblkid-2.17.2-12.28.el6_9.2.x86_64.rpm                                 | 119 kB     00:00
(68/138): libcurl-7.19.7-54.el6_10.x86_64.rpm                                      | 170 kB     00:00
(69/138): libdrm-2.4.65-2.el6.x86_64.rpm                                           | 136 kB     00:00
(70/138): libgcc-4.4.7-23.el6.x86_64.rpm                                           | 104 kB     00:00
(71/138): libgcrypt-1.4.5-12.el6_8.x86_64.rpm                                      | 229 kB     00:00
(72/138): libgomp-4.4.7-23.el6.x86_64.rpm                                          | 135 kB     00:00
(73/138): libnih-1.0.1-8.el6.x86_64.rpm                                            | 138 kB     00:00
(74/138): libpciaccess-0.13.4-1.el6.x86_64.rpm                                     |  24 kB     00:00
(75/138): libssh2-1.4.2-3.el6_10.1.x86_64.rpm                                      | 123 kB     00:00
(76/138): libstdc++-4.4.7-23.el6.x86_64.rpm                                        | 296 kB     00:00
(77/138): libstdc++-devel-4.4.7-23.el6.x86_64.rpm                                  | 1.6 MB     00:03
(78/138): libudev-147-2.74.el6_10.x86_64.rpm                                       |  78 kB     00:00
(79/138): libuuid-2.17.2-12.28.el6_9.2.x86_64.rpm                                  |  72 kB     00:00
(80/138): libxml2-2.7.6-21.el6_8.1.x86_64.rpm                                      | 805 kB     00:01
(81/138): logrotate-3.7.8-28.el6.x86_64.rpm                                        |  59 kB     00:00
(82/138): lvm2-2.02.143-12.el6_9.1.x86_64.rpm                                      | 941 kB     00:02
(83/138): lvm2-libs-2.02.143-12.el6_9.1.x86_64.rpm                                 | 1.0 MB     00:02
(84/138): make-3.81-23.el6.x86_64.rpm                                              | 389 kB     00:01
(85/138): mdadm-3.3.4-8.el6.x86_64.rpm                                             | 348 kB     00:01
(86/138): module-init-tools-3.9-26.el6.x86_64.rpm                                  | 467 kB     00:01
(87/138): net-tools-1.60-114.el6.x86_64.rpm                                        | 269 kB     00:00
(88/138): newt-0.52.11-4.el6.x86_64.rpm                                            |  97 kB     00:00
(89/138): newt-python-0.52.11-4.el6.x86_64.rpm                                     |  48 kB     00:00
(90/138): nspr-4.21.0-1.el6_10.x86_64.rpm                                          | 114 kB     00:00
(91/138): nss-3.44.0-7.el6_10.x86_64.rpm                                           | 883 kB     00:02
(92/138): nss-softokn-3.44.0-6.el6_10.x86_64.rpm                                   | 288 kB     00:00
(93/138): nss-softokn-freebl-3.44.0-6.el6_10.x86_64.rpm                            | 202 kB     00:00
(94/138): nss-sysinit-3.44.0-7.el6_10.x86_64.rpm                                   |  54 kB     00:00
(95/138): nss-tools-3.44.0-7.el6_10.x86_64.rpm                                     | 472 kB     00:00
(96/138): nss-util-3.44.0-1.el6_10.x86_64.rpm                                      |  73 kB     00:00
(97/138): openldap-2.4.40-16.el6.x86_64.rpm                                        | 285 kB     00:00
(98/138): openssh-5.3p1-124.el6_10.x86_64.rpm                                      | 278 kB     00:00
(99/138): openssh-clients-5.3p1-124.el6_10.x86_64.rpm                              | 444 kB     00:00
(100/138): openssh-server-5.3p1-124.el6_10.x86_64.rpm                              | 330 kB     00:00
(101/138): pam-1.1.1-24.el6.x86_64.rpm                                             | 659 kB     00:01
(102/138): passwd-0.77-7.el6.x86_64.rpm                                            |  89 kB     00:00
(103/138): perl-5.10.1-144.el6.x86_64.rpm                                          |  10 MB     00:18
(104/138): perl-Module-Pluggable-3.90-144.el6.x86_64.rpm                           |  41 kB     00:00
(105/138): perl-Pod-Escapes-1.04-144.el6.x86_64.rpm                                |  33 kB     00:00
(106/138): perl-Pod-Simple-3.13-144.el6.x86_64.rpm                                 | 213 kB     00:00
(107/138): perl-libs-5.10.1-144.el6.x86_64.rpm                                     | 579 kB     00:01
(108/138): perl-version-0.77-144.el6.x86_64.rpm                                    |  52 kB     00:00
(109/138): plymouth-0.8.3-29.el6.centos.x86_64.rpm                                 |  89 kB     00:00
(110/138): plymouth-core-libs-0.8.3-29.el6.centos.x86_64.rpm                       |  88 kB     00:00
(111/138): plymouth-scripts-0.8.3-29.el6.centos.x86_64.rpm                         |  31 kB     00:00
(112/138): policycoreutils-2.0.83-30.1.el6_8.x86_64.rpm                            | 663 kB     00:02
(113/138): procps-3.2.8-45.el6_9.3.x86_64.rpm                                      | 220 kB     00:00
(114/138): psmisc-22.6-24.el6.x86_64.rpm                                           |  82 kB     00:00
(115/138): python-2.6.6-68.el6_10.x86_64.rpm                                       |  76 kB     00:00
(116/138): python-libs-2.6.6-68.el6_10.x86_64.rpm                                  | 5.3 MB     00:07
(117/138): python-pycurl-7.19.0-9.el6.x86_64.rpm                                   |  77 kB     00:00
(118/138): python-urlgrabber-3.9.1-11.el6.noarch.rpm                               |  86 kB     00:00
(119/138): rpm-4.8.0-59.el6.x86_64.rpm                                             | 906 kB     00:00
(120/138): rpm-libs-4.8.0-59.el6.x86_64.rpm                                        | 318 kB     00:00
(121/138): rpm-python-4.8.0-59.el6.x86_64.rpm                                      |  61 kB     00:00
(122/138): rsyslog-5.8.10-12.el6.x86_64.rpm                                        | 650 kB     00:00
(123/138): selinux-policy-3.7.19-312.el6.noarch.rpm                                | 895 kB     00:00
(124/138): selinux-policy-targeted-3.7.19-312.el6.noarch.rpm                       | 3.1 MB     00:02
(125/138): setup-2.8.14-23.el6.noarch.rpm                                          | 151 kB     00:00
(126/138): shadow-utils-4.1.5.1-5.el6.x86_64.rpm                                   | 1.0 MB     00:00
(127/138): sudo-1.8.6p3-29.el6_10.3.x86_64.rpm                                     | 712 kB     00:00
(128/138): tar-1.23-15.el6_8.x86_64.rpm                                            | 810 kB     00:00
(129/138): tcp_wrappers-libs-7.6-58.el6.x86_64.rpm                                 |  62 kB     00:00
(130/138): tzdata-2020d-1.el6.noarch.rpm                                           | 514 kB     00:00
(131/138): udev-147-2.74.el6_10.x86_64.rpm                                         | 358 kB     00:00
(132/138): upstart-0.6.5-17.el6.x86_64.rpm                                         | 177 kB     00:00
(133/138): util-linux-ng-2.17.2-12.28.el6_9.2.x86_64.rpm                           | 1.6 MB     00:01
(134/138): vim-minimal-7.4.629-5.el6_10.2.x86_64.rpm                               | 422 kB     00:00
(135/138): wget-1.12-10.el6.x86_64.rpm                                             | 484 kB     00:00
(136/138): xfsprogs-3.1.1-20.el6.x86_64.rpm                                        | 725 kB     00:00
(137/138): yum-3.2.29-81.el6.centos.0.1.noarch.rpm                                 | 1.0 MB     00:00
(138/138): yum-plugin-fastestmirror-1.1.30-42.el6_10.noarch.rpm                    |  33 kB     00:00
----------------------------------------------------------------------------------------------------------
Total                                                                     724 kB/s | 181 MB     04:16
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Updating   : libgcc-4.4.7-23.el6.x86_64                                                           1/274
  Updating   : setup-2.8.14-23.el6.noarch                                                           2/274
warning: /etc/profile created as /etc/profile.rpmnew
warning: /etc/shadow created as /etc/shadow.rpmnew
  Updating   : kernel-firmware-2.6.32-754.35.1.el6.noarch                                           3/274
  Updating   : centos-release-6-10.el6.centos.12.3.x86_64                                           4/274
  Updating   : 12:dhcp-common-4.1.1-63.P1.el6.centos.x86_64                                         5/274
  Updating   : libX11-common-1.6.4-4.el6_10.noarch                                                  6/274
  Updating   : kernel-headers-2.6.32-754.35.1.el6.x86_64                                            7/274
  Updating   : tzdata-2020d-1.el6.noarch                                                            8/274
  Updating   : bash-4.1.2-48.el6.x86_64                                                             9/274
  Updating   : nss-softokn-freebl-3.44.0-6.el6_10.x86_64                                           10/274
  Updating   : glibc-common-2.12-1.212.el6_10.3.x86_64                                             11/274
  Updating   : glibc-2.12-1.212.el6_10.3.x86_64                                                    12/274
  Updating   : nspr-4.21.0-1.el6_10.x86_64                                                         13/274
  Updating   : nss-util-3.44.0-1.el6_10.x86_64                                                     14/274
  Updating   : chkconfig-1.3.49.5-1.el6.x86_64                                                     15/274
  Updating   : audit-libs-2.4.5-6.el6.x86_64                                                       16/274
  Updating   : libacl-2.2.49-7.el6_9.1.x86_64                                                      17/274
  Updating   : db4-4.7.25-22.el6.x86_64                                                            18/274
  Updating   : grep-2.20-6.el6.x86_64                                                              19/274
  Updating   : libudev-147-2.74.el6_10.x86_64                                                      20/274
  Updating   : gawk-3.1.7-10.el6_7.3.x86_64                                                        21/274
  Updating   : glib2-2.28.8-10.el6.x86_64                                                          22/274
  Updating   : file-libs-5.04-30.el6.x86_64                                                        23/274
  Updating   : 1:dbus-libs-1.2.24-11.el6_10.x86_64                                                 24/274
  Updating   : 1:findutils-4.4.2-9.el6.x86_64                                                      25/274
  Updating   : libstdc++-4.4.7-23.el6.x86_64                                                       26/274
  Updating   : gmp-4.3.1-13.el6.x86_64                                                             27/274
  Updating   : coreutils-libs-8.4-47.el6.x86_64                                                    28/274
  Updating   : coreutils-8.4-47.el6.x86_64                                                         29/274
  Updating   : pam-1.1.1-24.el6.x86_64                                                             30/274
  Updating   : 2:shadow-utils-4.1.5.1-5.el6.x86_64                                                 31/274
  Updating   : elfutils-libelf-0.164-2.el6.x86_64                                                  32/274
  Updating   : libuuid-2.17.2-12.28.el6_9.2.x86_64                                                 33/274
  Updating   : libblkid-2.17.2-12.28.el6_9.2.x86_64                                                34/274
  Updating   : plymouth-scripts-0.8.3-29.el6.centos.x86_64                                         35/274
  Updating   : nss-softokn-3.44.0-6.el6_10.x86_64                                                  36/274
  Updating   : nss-3.44.0-7.el6_10.x86_64                                                          37/274
  Updating   : nss-sysinit-3.44.0-7.el6_10.x86_64                                                  38/274
  Updating   : libssh2-1.4.2-3.el6_10.1.x86_64                                                     39/274
  Updating   : tcp_wrappers-libs-7.6-58.el6.x86_64                                                 40/274
  Updating   : expat-2.0.1-13.el6_8.x86_64                                                         41/274
  Updating   : binutils-2.20.51.0.2-5.48.el6_10.1.x86_64                                           42/274
  Updating   : module-init-tools-3.9-26.el6.x86_64                                                 43/274
  Updating   : hwdata-0.233-20.1.el6.noarch                                                        44/274
  Updating   : gdbm-1.8.0-39.el6.x86_64                                                            45/274
  Updating   : 1:perl-Pod-Escapes-1.04-144.el6.x86_64                                              46/274
  Updating   : 3:perl-version-0.77-144.el6.x86_64                                                  47/274
  Updating   : 4:perl-libs-5.10.1-144.el6.x86_64                                                   48/274
  Updating   : 1:perl-Pod-Simple-3.13-144.el6.x86_64                                               49/274
  Updating   : 1:perl-Module-Pluggable-3.90-144.el6.x86_64                                         50/274
  Updating   : 4:perl-5.10.1-144.el6.x86_64                                                        51/274
  Updating   : python-libs-2.6.6-68.el6_10.x86_64                                                  52/274
  Updating   : python-2.6.6-68.el6_10.x86_64                                                       53/274
  Updating   : cpio-2.10-13.el6.x86_64                                                             54/274
  Updating   : libpciaccess-0.13.4-1.el6.x86_64                                                    55/274
  Updating   : device-mapper-persistent-data-0.6.2-0.2.rc7.el6.x86_64                              56/274
  Updating   : nss-tools-3.44.0-7.el6_10.x86_64                                                    57/274
  Updating   : gzip-1.3.12-24.el6.x86_64                                                           58/274
  Updating   : logrotate-3.7.8-28.el6.x86_64                                                       59/274
  Updating   : cpp-4.4.7-23.el6.x86_64                                                             60/274
  Updating   : libstdc++-devel-4.4.7-23.el6.x86_64                                                 61/274
  Updating   : libnih-1.0.1-8.el6.x86_64                                                           62/274
  Updating   : upstart-0.6.5-17.el6.x86_64                                                         63/274
  Updating   : file-5.04-30.el6.x86_64                                                             64/274
  Updating   : db4-utils-4.7.25-22.el6.x86_64                                                      65/274
  Updating   : libcurl-7.19.7-54.el6_10.x86_64                                                     66/274
  Updating   : rpm-libs-4.8.0-59.el6.x86_64                                                        67/274
  Updating   : curl-7.19.7-54.el6_10.x86_64                                                        68/274
  Updating   : rpm-4.8.0-59.el6.x86_64                                                             69/274
  Updating   : openldap-2.4.40-16.el6.x86_64                                                       70/274
  Updating   : rpm-python-4.8.0-59.el6.x86_64                                                      71/274
  Updating   : python-pycurl-7.19.0-9.el6.x86_64                                                   72/274
  Updating   : python-urlgrabber-3.9.1-11.el6.noarch                                               73/274
  Updating   : yum-plugin-fastestmirror-1.1.30-42.el6_10.noarch                                    74/274
  Updating   : yum-3.2.29-81.el6.centos.0.1.noarch                                                 75/274
  Updating   : 2:tar-1.23-15.el6_8.x86_64                                                          76/274
  Updating   : 2:vim-minimal-7.4.629-5.el6_10.2.x86_64                                             77/274
  Updating   : newt-0.52.11-4.el6.x86_64                                                           78/274
  Updating   : glibc-headers-2.12-1.212.el6_10.3.x86_64                                            79/274
  Updating   : glibc-devel-2.12-1.212.el6_10.3.x86_64                                              80/274
  Updating   : psmisc-22.6-24.el6.x86_64                                                           81/274
  Updating   : net-tools-1.60-114.el6.x86_64                                                       82/274
  Updating   : procps-3.2.8-45.el6_9.3.x86_64                                                      83/274
  Updating   : plymouth-core-libs-0.8.3-29.el6.centos.x86_64                                       84/274
  Updating   : libdrm-2.4.65-2.el6.x86_64                                                          85/274
  Updating   : iptables-1.4.7-19.el6.x86_64                                                        86/274
  Updating   : iproute-2.6.32-57.el6.x86_64                                                        87/274
  Updating   : iputils-20071127-24.el6.x86_64                                                      88/274
  Updating   : plymouth-0.8.3-29.el6.centos.x86_64                                                 89/274
  Updating   : util-linux-ng-2.17.2-12.28.el6_9.2.x86_64                                           90/274
  Updating   : initscripts-9.03.61-1.el6.centos.x86_64                                             91/274
  Updating   : udev-147-2.74.el6_10.x86_64                                                         92/274
  Updating   : policycoreutils-2.0.83-30.1.el6_8.x86_64                                            93/274
  Updating   : device-mapper-libs-1.02.117-12.el6_9.1.x86_64                                       94/274
  Updating   : device-mapper-1.02.117-12.el6_9.1.x86_64                                            95/274
  Updating   : device-mapper-event-libs-1.02.117-12.el6_9.1.x86_64                                 96/274
  Updating   : openssh-5.3p1-124.el6_10.x86_64                                                     97/274
  Updating   : device-mapper-event-1.02.117-12.el6_9.1.x86_64                                      98/274
  Updating   : lvm2-libs-2.02.143-12.el6_9.1.x86_64                                                99/274
  Updating   : kpartx-0.4.9-106.el6_10.1.x86_64                                                   100/274
  Updating   : device-mapper-multipath-libs-0.4.9-106.el6_10.1.x86_64                             101/274
  Updating   : selinux-policy-3.7.19-312.el6.noarch                                               102/274
  Updating   : dracut-004-411.el6.noarch                                                          103/274
  Updating   : dracut-kernel-004-411.el6.noarch                                                   104/274
  Installing : kernel-2.6.32-754.35.1.el6.x86_64                                                  105/274
  Updating   : rsyslog-5.8.10-12.el6.x86_64                                                       106/274
  Updating   : cronie-anacron-1.4.4-16.el6_8.2.x86_64                                             107/274
  Updating   : cronie-1.4.4-16.el6_8.2.x86_64                                                     108/274
  Updating   : libgcrypt-1.4.5-12.el6_8.x86_64                                                    109/274
  Updating   : libgomp-4.4.7-23.el6.x86_64                                                        110/274
  Updating   : gcc-4.4.7-23.el6.x86_64                                                            111/274
  Updating   : gcc-c++-4.4.7-23.el6.x86_64                                                        112/274
  Updating   : gnupg2-2.0.14-9.el6_10.x86_64                                                      113/274
  Updating   : fuse-2.8.3-5.el6.x86_64                                                            114/274
  Updating   : selinux-policy-targeted-3.7.19-312.el6.noarch                                      115/274
  Updating   : device-mapper-multipath-0.4.9-106.el6_10.1.x86_64                                  116/274
  Updating   : lvm2-2.02.143-12.el6_9.1.x86_64                                                    117/274
  Updating   : openssh-clients-5.3p1-124.el6_10.x86_64                                            118/274
  Updating   : openssh-server-5.3p1-124.el6_10.x86_64                                             119/274
  Updating   : mdadm-3.3.4-8.el6.x86_64                                                           120/274
  Updating   : 12:dhclient-4.1.1-63.P1.el6.centos.x86_64                                          121/274
  Updating   : iscsi-initiator-utils-6.2.0.873-27.el6_9.x86_64                                    122/274
  Updating   : iptables-ipv6-1.4.7-19.el6.x86_64                                                  123/274
  Updating   : newt-python-0.52.11-4.el6.x86_64                                                   124/274
  Updating   : sudo-1.8.6p3-29.el6_10.3.x86_64                                                    125/274
warning: /etc/sudoers created as /etc/sudoers.rpmnew
  Updating   : 1:grub-0.97-99.el6.x86_64                                                          126/274
  Updating   : audit-2.4.5-6.el6.x86_64                                                           127/274
  Updating   : xfsprogs-3.1.1-20.el6.x86_64                                                       128/274
  Updating   : passwd-0.77-7.el6.x86_64                                                           129/274
  Updating   : ca-certificates-2020.2.41-65.1.el6_10.noarch                                       130/274
  Installing : kernel-devel-2.6.32-754.35.1.el6.x86_64                                            131/274
  Updating   : acl-2.2.49-7.el6_9.1.x86_64                                                        132/274
  Updating   : efibootmgr-0.5.4-15.el6.x86_64                                                     133/274
  Updating   : 1:dmidecode-2.12-7.el6.x86_64                                                      134/274
  Updating   : 1:make-3.81-23.el6.x86_64                                                          135/274
  Updating   : wget-1.12-10.el6.x86_64                                                            136/274
  Updating   : libxml2-2.7.6-21.el6_8.1.x86_64                                                    137/274
  Updating   : libX11-1.6.4-4.el6_10.x86_64                                                       138/274
  Cleanup    : openssh-server-5.3p1-112.el6_7.x86_64                                              139/274
  Cleanup    : device-mapper-multipath-0.4.9-87.el6.x86_64                                        140/274
  Cleanup    : cronie-anacron-1.4.4-15.el6.x86_64                                                 141/274
  Cleanup    : cronie-1.4.4-15.el6.x86_64                                                         142/274
  Cleanup    : lvm2-2.02.118-3.el6_7.3.x86_64                                                     143/274
  Cleanup    : rsyslog-5.8.10-10.el6_6.x86_64                                                     144/274
  Cleanup    : lvm2-libs-2.02.118-3.el6_7.3.x86_64                                                145/274
  Cleanup    : device-mapper-event-1.02.95-3.el6_7.3.x86_64                                       146/274
  Cleanup    : iscsi-initiator-utils-6.2.0.873-14.el6.x86_64                                      147/274
  Cleanup    : audit-2.3.7-5.el6.x86_64                                                           148/274
  Cleanup    : sudo-1.8.6p3-20.el6_7.x86_64                                                       149/274
  Cleanup    : iptables-ipv6-1.4.7-16.el6.x86_64                                                  150/274
  Cleanup    : device-mapper-persistent-data-0.3.2-1.el6.x86_64                                   151/274
  Cleanup    : gnupg2-2.0.14-8.el6.x86_64                                                         152/274
  Cleanup    : gcc-c++-4.4.7-16.el6.x86_64                                                        153/274
  Cleanup    : gcc-4.4.7-16.el6.x86_64                                                            154/274
  Cleanup    : openssh-clients-5.3p1-112.el6_7.x86_64                                             155/274
  Cleanup    : openssh-5.3p1-112.el6_7.x86_64                                                     156/274
  Cleanup    : xfsprogs-3.1.1-16.el6.x86_64                                                       157/274
  Cleanup    : passwd-0.77-4.el6_2.2.x86_64                                                       158/274
  Cleanup    : 12:dhclient-4.1.1-49.P1.el6.centos.x86_64                                          159/274
  Cleanup    : yum-plugin-fastestmirror-1.1.30-30.el6.noarch                                      160/274
  Cleanup    : yum-3.2.29-69.el6.centos.noarch                                                    161/274
  Cleanup    : glibc-devel-2.12-1.166.el6_7.3.x86_64                                              162/274
  Cleanup    : selinux-policy-targeted-3.7.19-279.el6_7.6.noarch                                  163/274
  Cleanup    : selinux-policy-3.7.19-279.el6_7.6.noarch                                           164/274
  Cleanup    : glibc-headers-2.12-1.166.el6_7.3.x86_64                                            165/274
  Cleanup    : 1:grub-0.97-94.el6.x86_64                                                          166/274
  Cleanup    : rpm-python-4.8.0-47.el6.x86_64                                                     167/274
  Cleanup    : device-mapper-multipath-libs-0.4.9-87.el6.x86_64                                   168/274
  Cleanup    : fuse-2.8.3-4.el6.x86_64                                                            169/274
  Cleanup    : mdadm-3.3.2-5.el6.x86_64                                                           170/274
  Cleanup    : cpp-4.4.7-16.el6.x86_64                                                            171/274
  Cleanup    : logrotate-3.7.8-23.el6.x86_64                                                      172/274
  Cleanup    : newt-python-0.52.11-3.el6.x86_64                                                   173/274
  Cleanup    : python-urlgrabber-3.9.1-9.el6.noarch                                               174/274
  Cleanup    : ca-certificates-2015.2.4-65.0.1.el6_6.noarch                                       175/274
  Cleanup    : python-pycurl-7.19.0-8.el6.x86_64                                                  176/274
  Cleanup    : python-libs-2.6.6-64.el6.x86_64                                                    177/274
  Cleanup    : python-2.6.6-64.el6.x86_64                                                         178/274
  Cleanup    : device-mapper-event-libs-1.02.95-3.el6_7.3.x86_64                                  179/274
  Cleanup    : kpartx-0.4.9-87.el6.x86_64                                                         180/274
  Cleanup    : device-mapper-1.02.95-3.el6_7.3.x86_64                                             181/274
  Cleanup    : device-mapper-libs-1.02.95-3.el6_7.3.x86_64                                        182/274
  Cleanup    : libX11-1.6.4-3.el6.x86_64                                                          183/274
  Cleanup    : libgomp-4.4.7-16.el6.x86_64                                                        184/274
  Cleanup    : 2:vim-minimal-7.4.629-5.el6.x86_64                                                 185/274
  Cleanup    : acl-2.2.49-6.el6.x86_64                                                            186/274
  Cleanup    : wget-1.12-5.el6_6.1.x86_64                                                         187/274
  Cleanup    : 1:make-3.81-20.el6.x86_64                                                          188/274
  Cleanup    : libstdc++-devel-4.4.7-16.el6.x86_64                                                189/274
  Cleanup    : 1:perl-Module-Pluggable-3.90-141.el6.x86_64                                        190/274
  Cleanup    : 1:perl-Pod-Simple-3.13-141.el6.x86_64                                              191/274
  Cleanup    : 1:perl-Pod-Escapes-1.04-141.el6.x86_64                                             192/274
  Cleanup    : 3:perl-version-0.77-141.el6.x86_64                                                 193/274
  Cleanup    : 4:perl-libs-5.10.1-141.el6.x86_64                                                  194/274
  Cleanup    : 4:perl-5.10.1-141.el6.x86_64                                                       195/274
  Cleanup    : dracut-kernel-004-388.el6.noarch                                                   196/274
  Cleanup    : dracut-004-388.el6.noarch                                                          197/274
  Cleanup    : plymouth-0.8.3-27.el6.centos.1.x86_64                                              198/274
  Cleanup    : plymouth-scripts-0.8.3-27.el6.centos.1.x86_64                                      199/274
  Cleanup    : libdrm-2.4.59-2.el6.x86_64                                                         200/274
  Cleanup    : iproute-2.6.32-45.el6.x86_64                                                       201/274
  Cleanup    : iptables-1.4.7-16.el6.x86_64                                                       202/274
  Cleanup    : policycoreutils-2.0.83-24.el6.x86_64                                               203/274
  Cleanup    : util-linux-ng-2.17.2-12.18.el6.x86_64                                              204/274
  Cleanup    : iputils-20071127-20.el6.x86_64                                                     205/274
  Cleanup    : udev-147-2.63.el6.x86_64                                                           206/274
  Cleanup    : initscripts-9.03.49-1.el6.centos.1.x86_64                                          207/274
  Cleanup    : libcurl-7.19.7-46.el6.x86_64                                                       208/274
  Cleanup    : openldap-2.4.40-6.el6_7.x86_64                                                     209/274
  Cleanup    : curl-7.19.7-46.el6.x86_64                                                          210/274
  Cleanup    : rpm-libs-4.8.0-47.el6.x86_64                                                       211/274
  Cleanup    : rpm-4.8.0-47.el6.x86_64                                                            212/274
  Cleanup    : nss-tools-3.19.1-3.el6_6.x86_64                                                    213/274
  Cleanup    : nss-sysinit-3.19.1-3.el6_6.x86_64                                                  214/274
  Cleanup    : nss-3.19.1-3.el6_6.x86_64                                                          215/274
  Cleanup    : nss-softokn-3.14.3-23.el6_7.x86_64                                                 216/274
  Cleanup    : upstart-0.6.5-13.el6_5.3.x86_64                                                    217/274
  Cleanup    : nss-util-3.19.1-1.el6_6.x86_64                                                     218/274
  Cleanup    : 2:shadow-utils-4.1.4.2-19.el6_6.1.x86_64                                           219/274
  Cleanup    : libnih-1.0.1-7.el6.x86_64                                                          220/274
  Cleanup    : db4-utils-4.7.25-20.el6_7.x86_64                                                   221/274
  Cleanup    : libblkid-2.17.2-12.18.el6.x86_64                                                   222/274
  Cleanup    : file-5.04-21.el6.x86_64                                                            223/274
  Cleanup    : gzip-1.3.12-22.el6.x86_64                                                          224/274
  Cleanup    : libpciaccess-0.13.3-0.1.el6.x86_64                                                 225/274
  Cleanup    : 2:tar-1.23-13.el6.x86_64                                                           226/274
  Cleanup    : gawk-3.1.7-10.el6.x86_64                                                           227/274
  Cleanup    : 1:findutils-4.4.2-6.el6.x86_64                                                     228/274
  Cleanup    : cpio-2.10-12.el6_5.x86_64                                                          229/274
  Cleanup    : glib2-2.28.8-4.el6.x86_64                                                          230/274
  Cleanup    : hwdata-0.233-14.1.el6.noarch                                                       231/274
  Cleanup    : module-init-tools-3.9-25.el6.x86_64                                                232/274
  Cleanup    : coreutils-libs-8.4-37.el6.x86_64                                                   233/274
  Cleanup    : pam-1.1.1-20.el6_7.1.x86_64                                                        234/274
  Cleanup    : coreutils-8.4-37.el6.x86_64                                                        235/274
  Cleanup    : gmp-4.3.1-7.el6_2.2.x86_64                                                         236/274
  Cleanup    : libstdc++-4.4.7-16.el6.x86_64                                                      237/274
  Cleanup    : grep-2.20-3.el6.x86_64                                                             238/274
  Cleanup    : binutils-2.20.51.0.2-5.43.el6.x86_64                                               239/274
  Cleanup    : libacl-2.2.49-6.el6.x86_64                                                         240/274
  Cleanup    : audit-libs-2.3.7-5.el6.x86_64                                                      241/274
  Cleanup    : db4-4.7.25-20.el6_7.x86_64                                                         242/274
  Cleanup    : file-libs-5.04-21.el6.x86_64                                                       243/274
  Cleanup    : libuuid-2.17.2-12.18.el6.x86_64                                                    244/274
  Cleanup    : 1:dbus-libs-1.2.24-8.el6_6.x86_64                                                  245/274
  Cleanup    : nspr-4.10.8-1.el6_6.x86_64                                                         246/274
  Cleanup    : chkconfig-1.3.49.3-5.el6.x86_64                                                    247/274
  Cleanup    : elfutils-libelf-0.161-3.el6.x86_64                                                 248/274
  Cleanup    : libssh2-1.4.2-1.el6_6.1.x86_64                                                     249/274
  Cleanup    : psmisc-22.6-19.el6_5.x86_64                                                        250/274
  Cleanup    : procps-3.2.8-34.el6_7.x86_64                                                       251/274
  Cleanup    : net-tools-1.60-110.el6_2.x86_64                                                    252/274
  Cleanup    : plymouth-core-libs-0.8.3-27.el6.centos.1.x86_64                                    253/274
  Cleanup    : gdbm-1.8.0-38.el6.x86_64                                                           254/274
  Cleanup    : libudev-147-2.63.el6.x86_64                                                        255/274
  Cleanup    : expat-2.0.1-11.el6_2.x86_64                                                        256/274
  Cleanup    : newt-0.52.11-3.el6.x86_64                                                          257/274
  Cleanup    : libgcrypt-1.4.5-11.el6_4.x86_64                                                    258/274
  Cleanup    : tcp_wrappers-libs-7.6-57.el6.x86_64                                                259/274
  Cleanup    : libxml2-2.7.6-20.el6.x86_64                                                        260/274
  Cleanup    : 1:dmidecode-2.12-6.el6.x86_64                                                      261/274
  Cleanup    : efibootmgr-0.5.4-13.el6.x86_64                                                     262/274
  Cleanup    : setup-2.8.14-20.el6_4.1.noarch                                                     263/274
  Cleanup    : centos-release-6-7.el6.centos.12.3.x86_64                                          264/274
  Cleanup    : libX11-common-1.6.4-3.el6.noarch                                                   265/274
  Cleanup    : kernel-headers-2.6.32-573.7.1.el6.x86_64                                           266/274
  Cleanup    : 12:dhcp-common-4.1.1-49.P1.el6.centos.x86_64                                       267/274
  Cleanup    : kernel-firmware-2.6.32-573.7.1.el6.noarch                                          268/274
  Cleanup    : glibc-common-2.12-1.166.el6_7.3.x86_64                                             269/274
  Cleanup    : bash-4.1.2-33.el6_7.1.x86_64                                                       270/274
  Cleanup    : nss-softokn-freebl-3.14.3-23.el6_7.x86_64                                          271/274
  Cleanup    : glibc-2.12-1.166.el6_7.3.x86_64                                                    272/274
  Cleanup    : tzdata-2015g-2.el6.noarch                                                          273/274
  Cleanup    : libgcc-4.4.7-16.el6.x86_64                                                         274/274
  Verifying  : iptables-ipv6-1.4.7-19.el6.x86_64                                                    1/274
  Verifying  : libssh2-1.4.2-3.el6_10.1.x86_64                                                      2/274
  Verifying  : 2:tar-1.23-15.el6_8.x86_64                                                           3/274
  Verifying  : 1:dbus-libs-1.2.24-11.el6_10.x86_64                                                  4/274
  Verifying  : efibootmgr-0.5.4-15.el6.x86_64                                                       5/274
  Verifying  : glib2-2.28.8-10.el6.x86_64                                                           6/274
  Verifying  : newt-0.52.11-4.el6.x86_64                                                            7/274
  Verifying  : file-libs-5.04-30.el6.x86_64                                                         8/274
  Verifying  : python-2.6.6-68.el6_10.x86_64                                                        9/274
  Verifying  : tzdata-2020d-1.el6.noarch                                                           10/274
  Verifying  : lvm2-2.02.143-12.el6_9.1.x86_64                                                     11/274
  Verifying  : 1:dmidecode-2.12-7.el6.x86_64                                                       12/274
  Verifying  : device-mapper-persistent-data-0.6.2-0.2.rc7.el6.x86_64                              13/274
  Verifying  : device-mapper-multipath-0.4.9-106.el6_10.1.x86_64                                   14/274
  Verifying  : 12:dhclient-4.1.1-63.P1.el6.centos.x86_64                                           15/274
  Verifying  : cronie-1.4.4-16.el6_8.2.x86_64                                                      16/274
  Verifying  : 1:findutils-4.4.2-9.el6.x86_64                                                      17/274
  Verifying  : python-urlgrabber-3.9.1-11.el6.noarch                                               18/274
  Verifying  : rpm-4.8.0-59.el6.x86_64                                                             19/274
  Verifying  : kernel-2.6.32-754.35.1.el6.x86_64                                                   20/274
  Verifying  : iputils-20071127-24.el6.x86_64                                                      21/274
  Verifying  : pam-1.1.1-24.el6.x86_64                                                             22/274
  Verifying  : 2:shadow-utils-4.1.5.1-5.el6.x86_64                                                 23/274
  Verifying  : openssh-5.3p1-124.el6_10.x86_64                                                     24/274
  Verifying  : 1:perl-Pod-Escapes-1.04-144.el6.x86_64                                              25/274
  Verifying  : initscripts-9.03.61-1.el6.centos.x86_64                                             26/274
  Verifying  : grep-2.20-6.el6.x86_64                                                              27/274
  Verifying  : kpartx-0.4.9-106.el6_10.1.x86_64                                                    28/274
  Verifying  : openldap-2.4.40-16.el6.x86_64                                                       29/274
  Verifying  : device-mapper-1.02.117-12.el6_9.1.x86_64                                            30/274
  Verifying  : db4-utils-4.7.25-22.el6.x86_64                                                      31/274
  Verifying  : 3:perl-version-0.77-144.el6.x86_64                                                  32/274
  Verifying  : passwd-0.77-7.el6.x86_64                                                            33/274
  Verifying  : kernel-devel-2.6.32-754.35.1.el6.x86_64                                             34/274
  Verifying  : 1:make-3.81-23.el6.x86_64                                                           35/274
  Verifying  : selinux-policy-3.7.19-312.el6.noarch                                                36/274
  Verifying  : glibc-devel-2.12-1.212.el6_10.3.x86_64                                              37/274
  Verifying  : nss-softokn-3.44.0-6.el6_10.x86_64                                                  38/274
  Verifying  : hwdata-0.233-20.1.el6.noarch                                                        39/274
  Verifying  : kernel-headers-2.6.32-754.35.1.el6.x86_64                                           40/274
  Verifying  : libblkid-2.17.2-12.28.el6_9.2.x86_64                                                41/274
  Verifying  : xfsprogs-3.1.1-20.el6.x86_64                                                        42/274
  Verifying  : 4:perl-libs-5.10.1-144.el6.x86_64                                                   43/274
  Verifying  : glibc-headers-2.12-1.212.el6_10.3.x86_64                                            44/274
  Verifying  : python-pycurl-7.19.0-9.el6.x86_64                                                   45/274
  Verifying  : openssh-clients-5.3p1-124.el6_10.x86_64                                             46/274
  Verifying  : psmisc-22.6-24.el6.x86_64                                                           47/274
  Verifying  : lvm2-libs-2.02.143-12.el6_9.1.x86_64                                                48/274
  Verifying  : glibc-common-2.12-1.212.el6_10.3.x86_64                                             49/274
  Verifying  : gzip-1.3.12-24.el6.x86_64                                                           50/274
  Verifying  : plymouth-scripts-0.8.3-29.el6.centos.x86_64                                         51/274
  Verifying  : rpm-python-4.8.0-59.el6.x86_64                                                      52/274
  Verifying  : coreutils-8.4-47.el6.x86_64                                                         53/274
  Verifying  : nss-softokn-freebl-3.44.0-6.el6_10.x86_64                                           54/274
  Verifying  : device-mapper-libs-1.02.117-12.el6_9.1.x86_64                                       55/274
  Verifying  : cpp-4.4.7-23.el6.x86_64                                                             56/274
  Verifying  : iproute-2.6.32-57.el6.x86_64                                                        57/274
  Verifying  : tcp_wrappers-libs-7.6-58.el6.x86_64                                                 58/274
  Verifying  : upstart-0.6.5-17.el6.x86_64                                                         59/274
  Verifying  : policycoreutils-2.0.83-30.1.el6_8.x86_64                                            60/274
  Verifying  : glibc-2.12-1.212.el6_10.3.x86_64                                                    61/274
  Verifying  : module-init-tools-3.9-26.el6.x86_64                                                 62/274
  Verifying  : libstdc++-4.4.7-23.el6.x86_64                                                       63/274
  Verifying  : libX11-common-1.6.4-4.el6_10.noarch                                                 64/274
  Verifying  : net-tools-1.60-114.el6.x86_64                                                       65/274
  Verifying  : 1:grub-0.97-99.el6.x86_64                                                           66/274
  Verifying  : libpciaccess-0.13.4-1.el6.x86_64                                                    67/274
  Verifying  : expat-2.0.1-13.el6_8.x86_64                                                         68/274
  Verifying  : sudo-1.8.6p3-29.el6_10.3.x86_64                                                     69/274
  Verifying  : libnih-1.0.1-8.el6.x86_64                                                           70/274
  Verifying  : audit-libs-2.4.5-6.el6.x86_64                                                       71/274
  Verifying  : wget-1.12-10.el6.x86_64                                                             72/274
  Verifying  : procps-3.2.8-45.el6_9.3.x86_64                                                      73/274
  Verifying  : curl-7.19.7-54.el6_10.x86_64                                                        74/274
  Verifying  : audit-2.4.5-6.el6.x86_64                                                            75/274
  Verifying  : elfutils-libelf-0.164-2.el6.x86_64                                                  76/274
  Verifying  : dracut-004-411.el6.noarch                                                           77/274
  Verifying  : gcc-c++-4.4.7-23.el6.x86_64                                                         78/274
  Verifying  : libuuid-2.17.2-12.28.el6_9.2.x86_64                                                 79/274
  Verifying  : libxml2-2.7.6-21.el6_8.1.x86_64                                                     80/274
  Verifying  : gcc-4.4.7-23.el6.x86_64                                                             81/274
  Verifying  : plymouth-core-libs-0.8.3-29.el6.centos.x86_64                                       82/274
  Verifying  : newt-python-0.52.11-4.el6.x86_64                                                    83/274
  Verifying  : util-linux-ng-2.17.2-12.28.el6_9.2.x86_64                                           84/274
  Verifying  : device-mapper-event-1.02.117-12.el6_9.1.x86_64                                      85/274
  Verifying  : nss-tools-3.44.0-7.el6_10.x86_64                                                    86/274
  Verifying  : acl-2.2.49-7.el6_9.1.x86_64                                                         87/274
  Verifying  : 2:vim-minimal-7.4.629-5.el6_10.2.x86_64                                             88/274
  Verifying  : libdrm-2.4.65-2.el6.x86_64                                                          89/274
  Verifying  : iptables-1.4.7-19.el6.x86_64                                                        90/274
  Verifying  : file-5.04-30.el6.x86_64                                                             91/274
  Verifying  : udev-147-2.74.el6_10.x86_64                                                         92/274
  Verifying  : binutils-2.20.51.0.2-5.48.el6_10.1.x86_64                                           93/274
  Verifying  : yum-3.2.29-81.el6.centos.0.1.noarch                                                 94/274
  Verifying  : 12:dhcp-common-4.1.1-63.P1.el6.centos.x86_64                                        95/274
  Verifying  : logrotate-3.7.8-28.el6.x86_64                                                       96/274
  Verifying  : nss-3.44.0-7.el6_10.x86_64                                                          97/274
  Verifying  : cronie-anacron-1.4.4-16.el6_8.2.x86_64                                              98/274
  Verifying  : 4:perl-5.10.1-144.el6.x86_64                                                        99/274
  Verifying  : libudev-147-2.74.el6_10.x86_64                                                     100/274
  Verifying  : libcurl-7.19.7-54.el6_10.x86_64                                                    101/274
  Verifying  : gdbm-1.8.0-39.el6.x86_64                                                           102/274
  Verifying  : libstdc++-devel-4.4.7-23.el6.x86_64                                                103/274
  Verifying  : ca-certificates-2020.2.41-65.1.el6_10.noarch                                       104/274
  Verifying  : plymouth-0.8.3-29.el6.centos.x86_64                                                105/274
  Verifying  : python-libs-2.6.6-68.el6_10.x86_64                                                 106/274
  Verifying  : gawk-3.1.7-10.el6_7.3.x86_64                                                       107/274
  Verifying  : rsyslog-5.8.10-12.el6.x86_64                                                       108/274
  Verifying  : 1:perl-Pod-Simple-3.13-144.el6.x86_64                                              109/274
  Verifying  : device-mapper-event-libs-1.02.117-12.el6_9.1.x86_64                                110/274
  Verifying  : nspr-4.21.0-1.el6_10.x86_64                                                        111/274
  Verifying  : libgcrypt-1.4.5-12.el6_8.x86_64                                                    112/274
  Verifying  : libgomp-4.4.7-23.el6.x86_64                                                        113/274
  Verifying  : yum-plugin-fastestmirror-1.1.30-42.el6_10.noarch                                   114/274
  Verifying  : chkconfig-1.3.49.5-1.el6.x86_64                                                    115/274
  Verifying  : cpio-2.10-13.el6.x86_64                                                            116/274
  Verifying  : coreutils-libs-8.4-47.el6.x86_64                                                   117/274
  Verifying  : iscsi-initiator-utils-6.2.0.873-27.el6_9.x86_64                                    118/274
  Verifying  : gnupg2-2.0.14-9.el6_10.x86_64                                                      119/274
  Verifying  : rpm-libs-4.8.0-59.el6.x86_64                                                       120/274
  Verifying  : db4-4.7.25-22.el6.x86_64                                                           121/274
  Verifying  : dracut-kernel-004-411.el6.noarch                                                   122/274
  Verifying  : 1:perl-Module-Pluggable-3.90-144.el6.x86_64                                        123/274
  Verifying  : libacl-2.2.49-7.el6_9.1.x86_64                                                     124/274
  Verifying  : mdadm-3.3.4-8.el6.x86_64                                                           125/274
  Verifying  : openssh-server-5.3p1-124.el6_10.x86_64                                             126/274
  Verifying  : centos-release-6-10.el6.centos.12.3.x86_64                                         127/274
  Verifying  : fuse-2.8.3-5.el6.x86_64                                                            128/274
  Verifying  : selinux-policy-targeted-3.7.19-312.el6.noarch                                      129/274
  Verifying  : kernel-firmware-2.6.32-754.35.1.el6.noarch                                         130/274
  Verifying  : bash-4.1.2-48.el6.x86_64                                                           131/274
  Verifying  : device-mapper-multipath-libs-0.4.9-106.el6_10.1.x86_64                             132/274
  Verifying  : setup-2.8.14-23.el6.noarch                                                         133/274
  Verifying  : nss-sysinit-3.44.0-7.el6_10.x86_64                                                 134/274
  Verifying  : libX11-1.6.4-4.el6_10.x86_64                                                       135/274
  Verifying  : libgcc-4.4.7-23.el6.x86_64                                                         136/274
  Verifying  : gmp-4.3.1-13.el6.x86_64                                                            137/274
  Verifying  : nss-util-3.44.0-1.el6_10.x86_64                                                    138/274
  Verifying  : 1:perl-Pod-Simple-3.13-141.el6.x86_64                                              139/274
  Verifying  : acl-2.2.49-6.el6.x86_64                                                            140/274
  Verifying  : 12:dhcp-common-4.1.1-49.P1.el6.centos.x86_64                                       141/274
  Verifying  : libxml2-2.7.6-20.el6.x86_64                                                        142/274
  Verifying  : libX11-1.6.4-3.el6.x86_64                                                          143/274
  Verifying  : libpciaccess-0.13.3-0.1.el6.x86_64                                                 144/274
  Verifying  : pam-1.1.1-20.el6_7.1.x86_64                                                        145/274
  Verifying  : xfsprogs-3.1.1-16.el6.x86_64                                                       146/274
  Verifying  : python-2.6.6-64.el6.x86_64                                                         147/274
  Verifying  : newt-0.52.11-3.el6.x86_64                                                          148/274
  Verifying  : cpio-2.10-12.el6_5.x86_64                                                          149/274
  Verifying  : 4:perl-libs-5.10.1-141.el6.x86_64                                                  150/274
  Verifying  : iptables-ipv6-1.4.7-16.el6.x86_64                                                  151/274
  Verifying  : libblkid-2.17.2-12.18.el6.x86_64                                                   152/274
  Verifying  : net-tools-1.60-110.el6_2.x86_64                                                    153/274
  Verifying  : 1:dmidecode-2.12-6.el6.x86_64                                                      154/274
  Verifying  : python-libs-2.6.6-64.el6.x86_64                                                    155/274
  Verifying  : gawk-3.1.7-10.el6.x86_64                                                           156/274
  Verifying  : nss-tools-3.19.1-3.el6_6.x86_64                                                    157/274
  Verifying  : cpp-4.4.7-16.el6.x86_64                                                            158/274
  Verifying  : module-init-tools-3.9-25.el6.x86_64                                                159/274
  Verifying  : lvm2-2.02.118-3.el6_7.3.x86_64                                                     160/274
  Verifying  : kernel-headers-2.6.32-573.7.1.el6.x86_64                                           161/274
  Verifying  : libnih-1.0.1-7.el6.x86_64                                                          162/274
  Verifying  : hwdata-0.233-14.1.el6.noarch                                                       163/274
  Verifying  : python-urlgrabber-3.9.1-9.el6.noarch                                               164/274
  Verifying  : dracut-004-388.el6.noarch                                                          165/274
  Verifying  : nspr-4.10.8-1.el6_6.x86_64                                                         166/274
  Verifying  : 4:perl-5.10.1-141.el6.x86_64                                                       167/274
  Verifying  : udev-147-2.63.el6.x86_64                                                           168/274
  Verifying  : glibc-headers-2.12-1.166.el6_7.3.x86_64                                            169/274
  Verifying  : util-linux-ng-2.17.2-12.18.el6.x86_64                                              170/274
  Verifying  : libacl-2.2.49-6.el6.x86_64                                                         171/274
  Verifying  : gmp-4.3.1-7.el6_2.2.x86_64                                                         172/274
  Verifying  : fuse-2.8.3-4.el6.x86_64                                                            173/274
  Verifying  : iptables-1.4.7-16.el6.x86_64                                                       174/274
  Verifying  : mdadm-3.3.2-5.el6.x86_64                                                           175/274
  Verifying  : python-pycurl-7.19.0-8.el6.x86_64                                                  176/274
  Verifying  : plymouth-core-libs-0.8.3-27.el6.centos.1.x86_64                                    177/274
  Verifying  : passwd-0.77-4.el6_2.2.x86_64                                                       178/274
  Verifying  : rpm-python-4.8.0-47.el6.x86_64                                                     179/274
  Verifying  : upstart-0.6.5-13.el6_5.3.x86_64                                                    180/274
  Verifying  : openldap-2.4.40-6.el6_7.x86_64                                                     181/274
  Verifying  : gcc-c++-4.4.7-16.el6.x86_64                                                        182/274
  Verifying  : psmisc-22.6-19.el6_5.x86_64                                                        183/274
  Verifying  : 3:perl-version-0.77-141.el6.x86_64                                                 184/274
  Verifying  : openssh-5.3p1-112.el6_7.x86_64                                                     185/274
  Verifying  : db4-utils-4.7.25-20.el6_7.x86_64                                                   186/274
  Verifying  : chkconfig-1.3.49.3-5.el6.x86_64                                                    187/274
  Verifying  : libgcrypt-1.4.5-11.el6_4.x86_64                                                    188/274
  Verifying  : device-mapper-1.02.95-3.el6_7.3.x86_64                                             189/274
  Verifying  : yum-plugin-fastestmirror-1.1.30-30.el6.noarch                                      190/274
  Verifying  : 2:vim-minimal-7.4.629-5.el6.x86_64                                                 191/274
  Verifying  : 12:dhclient-4.1.1-49.P1.el6.centos.x86_64                                          192/274
  Verifying  : coreutils-8.4-37.el6.x86_64                                                        193/274
  Verifying  : file-libs-5.04-21.el6.x86_64                                                       194/274
  Verifying  : nss-util-3.19.1-1.el6_6.x86_64                                                     195/274
  Verifying  : libstdc++-4.4.7-16.el6.x86_64                                                      196/274
  Verifying  : iscsi-initiator-utils-6.2.0.873-14.el6.x86_64                                      197/274
  Verifying  : curl-7.19.7-46.el6.x86_64                                                          198/274
  Verifying  : sudo-1.8.6p3-20.el6_7.x86_64                                                       199/274
  Verifying  : dracut-kernel-004-388.el6.noarch                                                   200/274
  Verifying  : nss-softokn-3.14.3-23.el6_7.x86_64                                                 201/274
  Verifying  : nss-softokn-freebl-3.14.3-23.el6_7.x86_64                                          202/274
  Verifying  : ca-certificates-2015.2.4-65.0.1.el6_6.noarch                                       203/274
  Verifying  : db4-4.7.25-20.el6_7.x86_64                                                         204/274
  Verifying  : kernel-firmware-2.6.32-573.7.1.el6.noarch                                          205/274
  Verifying  : setup-2.8.14-20.el6_4.1.noarch                                                     206/274
  Verifying  : device-mapper-event-1.02.95-3.el6_7.3.x86_64                                       207/274
  Verifying  : 2:shadow-utils-4.1.4.2-19.el6_6.1.x86_64                                           208/274
  Verifying  : glibc-devel-2.12-1.166.el6_7.3.x86_64                                              209/274
  Verifying  : gnupg2-2.0.14-8.el6.x86_64                                                         210/274
  Verifying  : libssh2-1.4.2-1.el6_6.1.x86_64                                                     211/274
  Verifying  : 2:tar-1.23-13.el6.x86_64                                                           212/274
  Verifying  : newt-python-0.52.11-3.el6.x86_64                                                   213/274
  Verifying  : grep-2.20-3.el6.x86_64                                                             214/274
  Verifying  : cronie-anacron-1.4.4-15.el6.x86_64                                                 215/274
  Verifying  : elfutils-libelf-0.161-3.el6.x86_64                                                 216/274
  Verifying  : centos-release-6-7.el6.centos.12.3.x86_64                                          217/274
  Verifying  : libdrm-2.4.59-2.el6.x86_64                                                         218/274
  Verifying  : gdbm-1.8.0-38.el6.x86_64                                                           219/274
  Verifying  : iputils-20071127-20.el6.x86_64                                                     220/274
  Verifying  : expat-2.0.1-11.el6_2.x86_64                                                        221/274
  Verifying  : glibc-common-2.12-1.166.el6_7.3.x86_64                                             222/274
  Verifying  : libcurl-7.19.7-46.el6.x86_64                                                       223/274
  Verifying  : openssh-clients-5.3p1-112.el6_7.x86_64                                             224/274
  Verifying  : device-mapper-libs-1.02.95-3.el6_7.3.x86_64                                        225/274
  Verifying  : libudev-147-2.63.el6.x86_64                                                        226/274
  Verifying  : libuuid-2.17.2-12.18.el6.x86_64                                                    227/274
  Verifying  : 1:make-3.81-20.el6.x86_64                                                          228/274
  Verifying  : libgcc-4.4.7-16.el6.x86_64                                                         229/274
  Verifying  : libX11-common-1.6.4-3.el6.noarch                                                   230/274
  Verifying  : nss-3.19.1-3.el6_6.x86_64                                                          231/274
  Verifying  : wget-1.12-5.el6_6.1.x86_64                                                         232/274
  Verifying  : 1:dbus-libs-1.2.24-8.el6_6.x86_64                                                  233/274
  Verifying  : 1:grub-0.97-94.el6.x86_64                                                          234/274
  Verifying  : cronie-1.4.4-15.el6.x86_64                                                         235/274
  Verifying  : yum-3.2.29-69.el6.centos.noarch                                                    236/274
  Verifying  : glib2-2.28.8-4.el6.x86_64                                                          237/274
  Verifying  : 1:perl-Module-Pluggable-3.90-141.el6.x86_64                                        238/274
  Verifying  : file-5.04-21.el6.x86_64                                                            239/274
  Verifying  : efibootmgr-0.5.4-13.el6.x86_64                                                     240/274
  Verifying  : libstdc++-devel-4.4.7-16.el6.x86_64                                                241/274
  Verifying  : openssh-server-5.3p1-112.el6_7.x86_64                                              242/274
  Verifying  : selinux-policy-targeted-3.7.19-279.el6_7.6.noarch                                  243/274
  Verifying  : plymouth-0.8.3-27.el6.centos.1.x86_64                                              244/274
  Verifying  : device-mapper-multipath-libs-0.4.9-87.el6.x86_64                                   245/274
  Verifying  : nss-sysinit-3.19.1-3.el6_6.x86_64                                                  246/274
  Verifying  : 1:perl-Pod-Escapes-1.04-141.el6.x86_64                                             247/274
  Verifying  : libgomp-4.4.7-16.el6.x86_64                                                        248/274
  Verifying  : coreutils-libs-8.4-37.el6.x86_64                                                   249/274
  Verifying  : rpm-libs-4.8.0-47.el6.x86_64                                                       250/274
  Verifying  : audit-2.3.7-5.el6.x86_64                                                           251/274
  Verifying  : device-mapper-event-libs-1.02.95-3.el6_7.3.x86_64                                  252/274
  Verifying  : initscripts-9.03.49-1.el6.centos.1.x86_64                                          253/274
  Verifying  : selinux-policy-3.7.19-279.el6_7.6.noarch                                           254/274
  Verifying  : device-mapper-multipath-0.4.9-87.el6.x86_64                                        255/274
  Verifying  : iproute-2.6.32-45.el6.x86_64                                                       256/274
  Verifying  : logrotate-3.7.8-23.el6.x86_64                                                      257/274
  Verifying  : binutils-2.20.51.0.2-5.43.el6.x86_64                                               258/274
  Verifying  : tzdata-2015g-2.el6.noarch                                                          259/274
  Verifying  : bash-4.1.2-33.el6_7.1.x86_64                                                       260/274
  Verifying  : audit-libs-2.3.7-5.el6.x86_64                                                      261/274
  Verifying  : rpm-4.8.0-47.el6.x86_64                                                            262/274
  Verifying  : device-mapper-persistent-data-0.3.2-1.el6.x86_64                                   263/274
  Verifying  : policycoreutils-2.0.83-24.el6.x86_64                                               264/274
  Verifying  : glibc-2.12-1.166.el6_7.3.x86_64                                                    265/274
  Verifying  : lvm2-libs-2.02.118-3.el6_7.3.x86_64                                                266/274
  Verifying  : kpartx-0.4.9-87.el6.x86_64                                                         267/274
  Verifying  : procps-3.2.8-34.el6_7.x86_64                                                       268/274
  Verifying  : rsyslog-5.8.10-10.el6_6.x86_64                                                     269/274
  Verifying  : tcp_wrappers-libs-7.6-57.el6.x86_64                                                270/274
  Verifying  : gzip-1.3.12-22.el6.x86_64                                                          271/274
  Verifying  : gcc-4.4.7-16.el6.x86_64                                                            272/274
  Verifying  : plymouth-scripts-0.8.3-27.el6.centos.1.x86_64                                      273/274
  Verifying  : 1:findutils-4.4.2-6.el6.x86_64                                                     274/274

Installed:
  kernel.x86_64 0:2.6.32-754.35.1.el6              kernel-devel.x86_64 0:2.6.32-754.35.1.el6

Updated:
  acl.x86_64 0:2.2.49-7.el6_9.1
  audit.x86_64 0:2.4.5-6.el6
  audit-libs.x86_64 0:2.4.5-6.el6
  bash.x86_64 0:4.1.2-48.el6
  binutils.x86_64 0:2.20.51.0.2-5.48.el6_10.1
  ca-certificates.noarch 0:2020.2.41-65.1.el6_10
  centos-release.x86_64 0:6-10.el6.centos.12.3
  chkconfig.x86_64 0:1.3.49.5-1.el6
  coreutils.x86_64 0:8.4-47.el6
  coreutils-libs.x86_64 0:8.4-47.el6
  cpio.x86_64 0:2.10-13.el6
  cpp.x86_64 0:4.4.7-23.el6
  cronie.x86_64 0:1.4.4-16.el6_8.2
  cronie-anacron.x86_64 0:1.4.4-16.el6_8.2
  curl.x86_64 0:7.19.7-54.el6_10
  db4.x86_64 0:4.7.25-22.el6
  db4-utils.x86_64 0:4.7.25-22.el6
  dbus-libs.x86_64 1:1.2.24-11.el6_10
  device-mapper.x86_64 0:1.02.117-12.el6_9.1
  device-mapper-event.x86_64 0:1.02.117-12.el6_9.1
  device-mapper-event-libs.x86_64 0:1.02.117-12.el6_9.1
  device-mapper-libs.x86_64 0:1.02.117-12.el6_9.1
  device-mapper-multipath.x86_64 0:0.4.9-106.el6_10.1
  device-mapper-multipath-libs.x86_64 0:0.4.9-106.el6_10.1
  device-mapper-persistent-data.x86_64 0:0.6.2-0.2.rc7.el6
  dhclient.x86_64 12:4.1.1-63.P1.el6.centos
  dhcp-common.x86_64 12:4.1.1-63.P1.el6.centos
  dmidecode.x86_64 1:2.12-7.el6
  dracut.noarch 0:004-411.el6
  dracut-kernel.noarch 0:004-411.el6
  efibootmgr.x86_64 0:0.5.4-15.el6
  elfutils-libelf.x86_64 0:0.164-2.el6
  expat.x86_64 0:2.0.1-13.el6_8
  file.x86_64 0:5.04-30.el6
  file-libs.x86_64 0:5.04-30.el6
  findutils.x86_64 1:4.4.2-9.el6
  fuse.x86_64 0:2.8.3-5.el6
  gawk.x86_64 0:3.1.7-10.el6_7.3
  gcc.x86_64 0:4.4.7-23.el6
  gcc-c++.x86_64 0:4.4.7-23.el6
  gdbm.x86_64 0:1.8.0-39.el6
  glib2.x86_64 0:2.28.8-10.el6
  glibc.x86_64 0:2.12-1.212.el6_10.3
  glibc-common.x86_64 0:2.12-1.212.el6_10.3
  glibc-devel.x86_64 0:2.12-1.212.el6_10.3
  glibc-headers.x86_64 0:2.12-1.212.el6_10.3
  gmp.x86_64 0:4.3.1-13.el6
  gnupg2.x86_64 0:2.0.14-9.el6_10
  grep.x86_64 0:2.20-6.el6
  grub.x86_64 1:0.97-99.el6
  gzip.x86_64 0:1.3.12-24.el6
  hwdata.noarch 0:0.233-20.1.el6
  initscripts.x86_64 0:9.03.61-1.el6.centos
  iproute.x86_64 0:2.6.32-57.el6
  iptables.x86_64 0:1.4.7-19.el6
  iptables-ipv6.x86_64 0:1.4.7-19.el6
  iputils.x86_64 0:20071127-24.el6
  iscsi-initiator-utils.x86_64 0:6.2.0.873-27.el6_9
  kernel-firmware.noarch 0:2.6.32-754.35.1.el6
  kernel-headers.x86_64 0:2.6.32-754.35.1.el6
  kpartx.x86_64 0:0.4.9-106.el6_10.1
  libX11.x86_64 0:1.6.4-4.el6_10
  libX11-common.noarch 0:1.6.4-4.el6_10
  libacl.x86_64 0:2.2.49-7.el6_9.1
  libblkid.x86_64 0:2.17.2-12.28.el6_9.2
  libcurl.x86_64 0:7.19.7-54.el6_10
  libdrm.x86_64 0:2.4.65-2.el6
  libgcc.x86_64 0:4.4.7-23.el6
  libgcrypt.x86_64 0:1.4.5-12.el6_8
  libgomp.x86_64 0:4.4.7-23.el6
  libnih.x86_64 0:1.0.1-8.el6
  libpciaccess.x86_64 0:0.13.4-1.el6
  libssh2.x86_64 0:1.4.2-3.el6_10.1
  libstdc++.x86_64 0:4.4.7-23.el6
  libstdc++-devel.x86_64 0:4.4.7-23.el6
  libudev.x86_64 0:147-2.74.el6_10
  libuuid.x86_64 0:2.17.2-12.28.el6_9.2
  libxml2.x86_64 0:2.7.6-21.el6_8.1
  logrotate.x86_64 0:3.7.8-28.el6
  lvm2.x86_64 0:2.02.143-12.el6_9.1
  lvm2-libs.x86_64 0:2.02.143-12.el6_9.1
  make.x86_64 1:3.81-23.el6
  mdadm.x86_64 0:3.3.4-8.el6
  module-init-tools.x86_64 0:3.9-26.el6
  net-tools.x86_64 0:1.60-114.el6
  newt.x86_64 0:0.52.11-4.el6
  newt-python.x86_64 0:0.52.11-4.el6
  nspr.x86_64 0:4.21.0-1.el6_10
  nss.x86_64 0:3.44.0-7.el6_10
  nss-softokn.x86_64 0:3.44.0-6.el6_10
  nss-softokn-freebl.x86_64 0:3.44.0-6.el6_10
  nss-sysinit.x86_64 0:3.44.0-7.el6_10
  nss-tools.x86_64 0:3.44.0-7.el6_10
  nss-util.x86_64 0:3.44.0-1.el6_10
  openldap.x86_64 0:2.4.40-16.el6
  openssh.x86_64 0:5.3p1-124.el6_10
  openssh-clients.x86_64 0:5.3p1-124.el6_10
  openssh-server.x86_64 0:5.3p1-124.el6_10
  pam.x86_64 0:1.1.1-24.el6
  passwd.x86_64 0:0.77-7.el6
  perl.x86_64 4:5.10.1-144.el6
  perl-Module-Pluggable.x86_64 1:3.90-144.el6
  perl-Pod-Escapes.x86_64 1:1.04-144.el6
  perl-Pod-Simple.x86_64 1:3.13-144.el6
  perl-libs.x86_64 4:5.10.1-144.el6
  perl-version.x86_64 3:0.77-144.el6
  plymouth.x86_64 0:0.8.3-29.el6.centos
  plymouth-core-libs.x86_64 0:0.8.3-29.el6.centos
  plymouth-scripts.x86_64 0:0.8.3-29.el6.centos
  policycoreutils.x86_64 0:2.0.83-30.1.el6_8
  procps.x86_64 0:3.2.8-45.el6_9.3
  psmisc.x86_64 0:22.6-24.el6
  python.x86_64 0:2.6.6-68.el6_10
  python-libs.x86_64 0:2.6.6-68.el6_10
  python-pycurl.x86_64 0:7.19.0-9.el6
  python-urlgrabber.noarch 0:3.9.1-11.el6
  rpm.x86_64 0:4.8.0-59.el6
  rpm-libs.x86_64 0:4.8.0-59.el6
  rpm-python.x86_64 0:4.8.0-59.el6
  rsyslog.x86_64 0:5.8.10-12.el6
  selinux-policy.noarch 0:3.7.19-312.el6
  selinux-policy-targeted.noarch 0:3.7.19-312.el6
  setup.noarch 0:2.8.14-23.el6
  shadow-utils.x86_64 2:4.1.5.1-5.el6
  sudo.x86_64 0:1.8.6p3-29.el6_10.3
  tar.x86_64 2:1.23-15.el6_8
  tcp_wrappers-libs.x86_64 0:7.6-58.el6
  tzdata.noarch 0:2020d-1.el6
  udev.x86_64 0:147-2.74.el6_10
  upstart.x86_64 0:0.6.5-17.el6
  util-linux-ng.x86_64 0:2.17.2-12.28.el6_9.2
  vim-minimal.x86_64 2:7.4.629-5.el6_10.2
  wget.x86_64 0:1.12-10.el6
  xfsprogs.x86_64 0:3.1.1-20.el6
  yum.noarch 0:3.2.29-81.el6.centos.0.1
  yum-plugin-fastestmirror.noarch 0:1.1.30-42.el6_10

Complete!
[root@localhost ~]#
```

更新epel第三方软件库：
> yum install -y epel-release

```
[root@localhost ~]# yum install -y epel-release
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package epel-release-6-8.noarch already installed and latest version
Nothing to do
[root@localhost ~]#
[root@localhost ~]# yum install -y docker-io
Loaded plugins: fastestmirror
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package docker-io available.
Error: Nothing to do
[root@localhost ~]#
[root@localhost ~]# yum search docker-io
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Warning: No matches found for: docker-io
No Matches found
[root@localhost ~]#
[root@localhost ~]# yum search docker
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
========================================== N/S Matched: docker ===========================================
fedora-dockerfiles.x86_64 : Example dockerfiles to assist standing up containers quickly
golang-github-docker-libtrust-unit-test.x86_64 : Unit tests for golang-github-docker-libtrust package
golang-github-docker-spdystream-unit-test.x86_64 : Unit tests for golang-github-docker-spdystream package
golang-github-fsouza-go-dockerclient-devel.noarch : Client for the Docker remote API
golang-github-fsouza-go-dockerclient-unit-test.x86_64 : Unit tests for
                                                      : golang-github-fsouza-go-dockerclient package
imagefactory-plugins-Docker.noarch : Cloud plugin for Docker
python-docker-py.x86_64 : An API client for docker written in Python
python-docker-registry-core.noarch : Core package for docker-registry (drivers) developers
python-dockerfile-parse.noarch : Python library for Dockerfile manipulation
docker.x86_64 : KDE and GNOME2 system tray replacement docking application
golang-github-docker-libcontainer.x86_64 : Configuration options for containers
golang-github-docker-libcontainer-devel.x86_64 : Configuration options for containers
golang-github-docker-libtrust-devel.noarch : Library for managing authentication and authorization
golang-github-docker-spdystream-devel.noarch : A multiplexed stream library using spdy

  Name and summary matches only, use "search all" for everything.
[root@localhost ~]#
```

没实现。

##### 方法二

####


####




<br/><br/><br/><br/><br/>
## 参考资料

<https://stackoverflow.com/questions/9479117/vagrant-virtualbox-apache2-strange-cache-behaviour#>

Nginx学习（一）之安装、控制、配置 <https://www.cnblogs.com/qiye5757/p/9614571.html>

yum安装最新版php7 <https://blog.csdn.net/zhezhebie/article/details/73325663>

linux使用国内镜像源 <https://blog.csdn.net/zhezhebie/article/details/73484695>

清华大学开源软件镜像站 CentOS 镜像使用帮助 <https://mirrors.tuna.tsinghua.edu.cn/help/centos/>

超详细的 Vagrant 上手指南 <https://zhuanlan.zhihu.com/p/259833884>

Centos7环境下更新PHP7.2到PHP7.4（WordPress） <https://chenyan98.cn/391.html>

CentOS6 yum源设置为国内aliyun yum源 <https://blog.csdn.net/zhuifeng2014/article/details/108019120>

阿里开源镜像站 <https://developer.aliyun.com/mirror>

yum安装的repo源地址都有哪些？都保存在什么地方？ <https://newsn.net/say/yum-repo-list.html>

运行docker出错 <https://blog.csdn.net/a33130317/article/details/81123562>

玩转 docker 入门(1) docker 在 CentOS 7.4 的安装部署 <https://blog.csdn.net/a33130317/article/details/80882225>

