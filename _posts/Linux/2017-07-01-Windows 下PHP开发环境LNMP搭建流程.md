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

切换到root用户：

> sudo su

用source命令，让/etc/profile文件立刻生效，而不用重新登录：

> source /etc/profile 

如图：

![]({{site.baseurl}}/images/20200329/20200329191547.jpg)

#### 安装PHP：

到软件资源目录下： 
> cd /usr/local/src/

切换yum源：
> rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm

如果失败，换成国内Yum源试一下。

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
    
#### 安装Nginx：

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
 
#### 安装MySQL

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

#### 站点配置

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

#### 附言

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

#### 每日启动

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


<br/><br/><br/><br/><br/>
### 参考资料

<https://stackoverflow.com/questions/9479117/vagrant-virtualbox-apache2-strange-cache-behaviour#>

Nginx学习（一）之安装、控制、配置 <https://www.cnblogs.com/qiye5757/p/9614571.html>

yum安装最新版php7 <https://blog.csdn.net/zhezhebie/article/details/73325663>

linux使用国内镜像源 <https://blog.csdn.net/zhezhebie/article/details/73484695>

