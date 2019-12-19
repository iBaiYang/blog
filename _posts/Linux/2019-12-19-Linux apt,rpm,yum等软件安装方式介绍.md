---
layout: post
categories: Linux
title: Linux apt,rpm,yum等软件安装方式介绍
meta: Linux apt,rpm,yum等软件安装方式介绍
---
* content
{:toc}

### 正文

Linux环境下，软件安装方式有多种：源代码安装、rpm、apt、yum等

#### 源代码安装

源代码安装就是tar包安装,源代码就是别人开发好的软件程序,没有加密,直接公布出来了。
源代码不能直接运行，必须将源代码编译成可执行的二进制文件才可以运行，所以源代码安装比较麻烦，需要编译。

使用源代码安装软件的优点：
获得最新的软件版本，及时修复bug；
根据用户需要，灵活定制软件功能；

源码安装的缺点：
编译麻烦；
缺乏自动依赖管理，软件升级麻烦；


几乎所有的开源软件都支持在Linux下运行，而这些软件一般都以源码形式发放，只需要Linux安装了gcc、make、automake、autoconf都支持源码安装。

源代码安装4步骤：

第1步：tar 解包，解压并释放源代码包到指定目录；

第2步：./configure 配置生成Makefile文件，目的是设定安装目录、安装模块等选项；

第3步：make 编译，将源代码文件编译成可执行的二进制文件；

第4步：make install 安装，目的是复制二进制文件到系统相应的目录。

安装的软件需要卸载时，可以直接删除安装文件夹，少数软件支持执行make uninstall。

##### wget

下载源代码包，我们可以用 wget 方式。

wget是一个下载文件的工具，它用在命令行下。 wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。所谓的“自动下载”是指，
wget可以在用户退出系统的之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成。
wget 可以跟踪HTML页面上的链接依次下载来创建远程服务器的本地版本，完全重建原始站点的目录结构。这又常被称作”递归下载”。

wget 非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性.如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。
如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

案例：
1.使用wget下载单个文件
```
wget http://cn2.php.net/distributions/php-5.6.13.tar.gz
```

2.下载并以不同的文件名保存　
```
wget -o php5.6.tar.gz http://cn2.php.net/distributions/php-5.6.13.tar.gz
```

3.使用wget断点续传
```
wget -c http://cn2.php.net/distributions/php-5.6.13.tar.gz
```

4.使用wget后台下载
```
wget -b  http://cn2.php.net/distributions/php-5.6.13.tar.gz

tail -f wget-log   #查看文件下载进度
```

5.使用wget下载到指定目录
```
wget http://cn2.php.net/distributions/php-5.6.13.tar.gz -P Download/
```

6.使用wget用户名和密码认证下载
```
wget --ftp-user=FTP_usser_name  --ftp-password=User_password  FTP_ADDRESS
```

##### tar

源代码压缩包有好多种格式，我们解压可以用：
```
tar –xvf file.tar        解压 *.tar包
tar -xzvf file.tar.gz    解压 *.tar.gz
tar -xzvf file.tar.gz    解压 *.tgz
tar -xjvf file.tar.bz2   解压 *.tar.bz2
tar –xZvf file.tar.Z     解压 *.tar.Z
unrar e file.rar         解压 *.rar
unzip file.zip           解压 *.zip
```

另外总结下：
```
*.tar            用 tar –xvf 解压
*.tar.gz         用 tar –xzf 解压
*.tgz            用 tar –xzf 解压
*.tar.bz2        用tar –xjf 解压
*.tar.Z          用tar –xZf 解压
*.gz             用 gzip -d或者gunzip 解压
*.bz2            用 bzip2 -d或者用bunzip2 解压
*.Z              用 uncompress 解压
*.rar            用 unrar e解压
*.zip            用 unzip 解压
```

压缩：
```
tar –cvf jpg.tar *.jpg  将目录里所有jpg文件打包成tar.jpg
tar –czf jpg.tar.gz *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg 将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg   zip格式的压缩，需要先下载zip for linux 
```

#### rpm

RPM包管理工具（RPM Package Manager），由Red Hat公司提出，被众多Linux发行版所采用，以前的R代表红帽公司，现在不只是代表红帽了。

Rpm包不是源代码,它是将源代码编译完成后（二进制包）,再去做成rpm包发布出来。

一般命名格式：
```
bash-3.2-24.el5.i386.rpm
```

bash：软件名称，3.2：版本号，24：发布次数（修正版），i386：硬件平台，rpm：拓展名。

我们可以在这里查询需要的软件包： http://rpmfind.net

Rpm包需要通过rpm命令安装，卸载，升级。

rpm常用参数：
```
-i：安装一个新的rpm软件包；
-U：升级某个rpm软件，若原本未装，则进行安装；
-F：更新某个rpm软件，若原本未装，则放弃安装(更新推荐使用)；
-e：卸载一个rpm安装的软件包；
-v：显示安装过程中的详细信息；
-h：以“#”号显示安装的进度
```

安装：
```
rpm -ivh bash-3.2-24.el5.i386.rpm
```

升级：
```
rpm -Uvh bash-3.2-24.el5.i386.rpm
```

卸载：
```
rpm -evh bash
```

#### SRPM

SRPM 文件里面含有源代码( Source Code )。
SRPM 的文件名是以 ***.src.rpm 这种格式来命名。
需要编译生成RPM包后才能进行安装。

使用 rpmbuild 命令安装SRPM包

选项： 

–rebuild 

进行‘编译’与‘打包’的动作，最后会产生 RPM 的软件包，但是产生的 RPM 软件包并没有安装到系统上。

–recompile

rebuild 仅‘编译并打包’而已，而 recompile 不但进行编译跟打包，还同时 进行‘安装’了！

命令范例：
```
rpmbuild --rebuild rp-pppoe-3.5-32.1.src.rpm
```

#### apt

#### yum




<br/><br/><br/><br/><br/>
### 参考资料

Linux中常见软件安装方法及常见管理方法(重点讲了源代码安装和rpm安装)  <https://blog.csdn.net/weixin_42373127/article/details/88605601> 

RPM软件包软件素材参考 <http://rpmfind.net/>

Linux wget命令详解 <https://www.cnblogs.com/ftl1012/p/9265699.html>

Linux之wget下载 <https://www.cnblogs.com/pingzhe/p/8134984.html>

Linux tar命令 <https://www.runoob.com/linux/linux-comm-tar.html>

linux中tar命令用法 <https://www.cnblogs.com/newcaoguo/p/5896975.html>

