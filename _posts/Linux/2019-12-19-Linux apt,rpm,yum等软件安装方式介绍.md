---
layout: post
categories: Linux
title: Linux apt,rpm,yum等软件安装方式介绍
meta: Linux apt,rpm,yum等软件安装方式介绍
---
* content
{:toc}

### 正文

Linux环境下，软件安装方式有多种：源代码安装、rpm、apt、yum等。

虽然软件安装方式有多种，但其实Linux中软件包只有源码包和二进制包两种软件安装包（二进制包是源码包编译后的文件），
这些方式只是执行并优化了这两种软件安装包的过程。

这些安装方式与Linux的发展历史有关。Linux，最早由Linus Benedict Torvalds在1991年开始编写。
在这之前，Richard Stallman创建了Free Software Foundation（FSF）组织以及GNU项目，
并不断的编写创建GNU程序（此类程序的许可方式均为GPL: General Public License）。
在不断的有杰出的程序员和开发者加入到GNU组织中后，便造就了今天我们所看到的Linux，或称GNU/Linux。

Linux的发行版本可以大体分为两类，一类是商业公司维护的发行版本，一类是社区组织维护的发行版本。
前者以著名的Redhat（RHEL）为代表，后者以Debian为代表。

Redhat基础上出现了 Fedora、 CentOS 等，这些发行版采用了.rpm格式的软件包，后来的SUSE也选用了.rpm格式的软件包。
安装rpm包的命令是“rpm -参数”，解决包依赖关系使用yum命令。

Debian基础上出现了 Ubuntu、 Deepin 等，这些发行版采用了.deb格式的软件包。
安装deb包的命令是“dpkg -参数”，解决包依赖关系使用apt命令。

详细的Linux家族树：

![]({{site.baseurl}}/images/20201115/20201115191117650.png)

GNU/Linux OS关系图：

![]({{site.baseurl}}/images/20201115/20201115112430625.png)

#### 源码包安装

源码包安装就是源代码包安装,源代码就是别人开发好的软件程序,没有编译,直接公布出来了。
源代码不能直接运行，必须将源代码编译成可执行的二进制文件才可以运行，所以源代码安装比较麻烦，需要编译。

使用源代码安装软件的优点：
获得最新的软件版本，及时修复bug；
根据用户需要，灵活定制软件功能；

源码安装的缺点：
编译麻烦；
缺乏自动依赖管理，软件升级麻烦；

几乎所有的开源软件都支持在Linux下运行，而这些软件一般都以源码形式发放（源码都是c语言写的），
只需要Linux安装了gcc、make、automake、autoconf都支持源码安装。

源代码安装4步骤：

第1步：tar 解包，解压并释放源代码包到指定目录；

第2步：./configure 配置生成 Makefile 文件，目的是设定安装目录、安装模块等选项；

第3步：make 编译，将源代码文件编译成可执行的二进制文件；

第4步：make install 安装，目的是复制二进制文件到系统相应的目录。

安装的软件需要卸载时，可以直接删除安装文件夹，少数软件支持执行make uninstall。

##### wget

下载源代码包，我们可以用 wget 方式。

wget是一个下载文件的工具，它用在命令行下。 wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。所谓的“自动下载”是指，
wget可以在用户退出系统之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成。
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

##### gzip

gzip命令是.gz文件的压缩程序。

gzip是个使用广泛的压缩程序，文件经它压缩过后，其名称后面会多出`.gz`的扩展名。

如： `gzip book.tar` 后， book.tar 文件就变成了 book.tar.gz 。

解压缩可以用 gunzip 或 `gzip -d`， 如：
`gunzip book.tar.gz` 
或 
`gzip -d book.tar.gz` 
后， book.tar.gz 文件就变成了 book.tar 。

##### bzip2

bzip2命令是.bz2文件的压缩程序。

bzip2采用新的压缩演算法，压缩效果比传统的LZ77/LZ78压缩演算法来得好。若没有加上任何参数，bzip2压缩完文件后会产生`.bz2`的压缩文件，并删除原始的文件。

如： `bzip2 book.tar` 后， book.tar 文件就变成了 book.tar.bz2 。

解压缩可以用 bunzip2 或 `bzip2 -d`， 如：
`bunzip2 book.tar.bz2` 
或 
`bzip2 -d book.tar.bz2` 
后， book.tar.bz2 文件就变成了 book.tar 。

##### tar 文件打包

Linux tar（英文全拼：tape archive ）命令用于打包备份文件。

tar 是用来建立，还原备份文件的工具程序，它可以加入，解开备份文件内的文件。

如将本地目录下books目录连同其下文件一同打包成books.tar：
```
tar -cvf books.tar ./books
```

tar常用参数：
```
-c 或--create    建立新的备份文件。 
-x 或--extract 或--get    从备份文件中还原文件。
-v 或--verbose    显示指令执行过程
-f<备份文件> 或--file=<备份文件>   指定备份文件。
-z 或--gzip 或--gunzip    通过 gzip 指令处理 *.tar.gz 备份文件。
-j 或--bzip2 或--bunzip2    通过 bzip2 指令处理 *.tar.bz2 备份文件。
```

打包及压缩：
```
tar –cvf jpg.tar *.jpg  将目录里所有jpg文件打包成 jpg.tar
tar –czf jpg.tar.gz *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg 将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg   将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg   zip格式的压缩，需要先下载zip for linux 
```

源代压缩包有好多种格式，我们解压可以用：
```
tar –xvf file.tar        解压 *.tar包
tar -xzvf file.tar.gz    解压 *.tar.gz
tar -xzvf file.tgz       解压 *.tgz
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

##### dd 转移文件

dd命令曾经广泛用于复制文件系统，但因为有了更好的dump和restore命令，dd现在已经很少使用了。但在一些追求简便的场合，dd仍然发挥着作用。

dd命令使用if选项指定输入端的文件系统，而of选项则指定其输出端。下面这条命令将一张CD完整地储存为iso镜像文件：
`dd if=/dev/cdrom of=CD.iso`

#### rpm

RPM包管理工具（RPM Package Manager），由Red Hat公司提出，被众多Linux发行版所采用，以前的R代表红帽公司，现在不只是代表红帽了。

Rpm包不是源代码包，它是将源代码编译完成为二进制包后,再做成.rpm包发布出来。

优点：
* 包管理系统简单，只通过几个命令就可以实现包的安装、升级、查询和卸载；
* 安装速度比源码包安装快的多。

缺点：
* 经过编译，不再可以看到源代码；
* 功能选择不如源码包灵活；
* 依赖性需要自己解决。

rpm包一般命名格式：
```
bash-3.2-24.el5.i386.rpm
```

bash：软件名称，3.2：版本号，24：发布次数（修正版），el5：适合的Linux平台，i386：硬件平台，rpm：拓展名。

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

rpm包下载后，安装：
> rpm -ivh bash-3.2-24.el5.i386.rpm

软件包升级：
> rpm -Uvh bash-3.2-24.el5.i386.rpm

软件卸载：
> rpm -evh bash

注：若已安装了二进制包，源码包也是可以继续安装的，因为两者安装目录不一样。但是，并不建议这样做，因为端口会冲突。
 
##### SRPM

SRPM 文件里面含有源代码( Source Code )。
SRPM 的文件名是以 ***.src.rpm 这种格式来命名。
需要编译生成RPM包后才能进行安装。

使用 rpmbuild 命令安装SRPM包

选项： 
`–-rebuild`  进行‘编译’与‘打包’的动作，最后会产生 RPM 的软件包，但是产生的 RPM 软件包并没有安装到系统上。
`–-recompile` ， rebuild 仅‘编译并打包’而已，而 recompile 不但进行编译跟打包，还同时 进行‘安装’了！

使用范例：
```
> rpmbuild --rebuild rp-pppoe-3.5-32.1.src.rpm
>
> rpm -ivh rp-pppoe-3.5-32.1.rpm
```

#### yum

yum在线安装，可以方便的解决RPM安装依赖文件，一条命令就可以帮用户从网上（本地也可以）找到安装包进行安装。

注：RedHat的yum是收费服务，而CentOS的yum是免费服务。

常用命令：
```
yum install <package_name>          // 仅安装指定的软件命令
yum update <package_name>           // 仅更新指定的软件命令
yum update                          // 更新所有软件命令
yum remove <package_name>           // 删除软件包命令
yum list                            // 列出所有可安裝的软件清单命令
yum search <keyword>                // 查找软件包命令
yum clean                           // 清除缓存目录下的软件包及旧的 headers
yum makecache                       // 生成缓存 
```

#### dpkg

dpkg ”是“Debian Packager ”的简写。

Debian包文件（Debian archive file）包含了可执行文件、库文件和相关程序的文档。deb文件是linux发行版debian系统的安装包格式，
还有像基于debian系统的发行版ubuntu等系统就是使用的deb格式安装包，我们可以使用dpkg命令进行安装管理这些deb安装包文件。

Linux继承了很多UNIX系统构建和程序设计的思想：一个应用程序会利用更多现有的工具或应用来实现自己的目的。
这种设计哲学很大程度上使Linux成为了一个有机的、各个部分都充满了活力的操作系统，但是也正是这种高度灵活性使得Linux面临着两个复杂的问题：
程序依赖性问题，以及由依赖性问题衍生出来的程序冲突问题。

为了解决这些问题，Debian软件包管理系统引入了一套软件包“依赖性”定义，用来描述独立运行的程序A与现存系统中程序B之间存在的关联程度。

dpkg本身是一个底层的安装工具，如果需要从远程服务器上获取软件包，或者由系统自己处理复杂的软件包依赖性都需要比dpkg更高层的前段工具，
比如Debian系统使用APT。

dpkg常用参数：
```
-i：安装一个新的deb软件包；
-r：卸载一个安装的软件包；
-P：清除软件包的所有文件，实现指定软件包的完全卸载
-l：用于获取当前系统中所有已安装的deb软件包信息
```

deb包下载后，安装：
> dpkg -i package-name.deb

卸载软件包：
> dpkg -r package-name           

`--remove`， 移除软件包，但保留其配置文件，不是完全意义上的卸载

> dpkg -P package-name      

`--purge`， 清除软件包的所有文件（removes everything,including conffiles），实现指定软件包的完全卸载

使用查询示例：
```
dpkg -l 用于获取当前系统中所有已安装的deb软件包信息
eg: dpkg -l | grep -i vim

dpkg -l package-name-pattern # --list, 查看系统中软件包名符合pattern模式的软件包
eg: dpkg -l \*dpkg*

dpkg -L vim 查询某一软件包所安装的文件

dpkg -L package-name # --listfiles, 查看package-name对应的软件包安装的文件及目录
eg: dpkg -L dpkg

dpkg -s vim 进一步了解某一软件包的详细信息

dpkg -s package-name # --status, 查看package-name（已安装）对应的软件包信息

dpkg -S /bin/ls 查询系统中的某个文件属于哪个软件包

dpkg -S filename-search-pattern # --search,从已经安装的软件包中查找包含filename的软件包名称 （Tip：也可使用子命令dpkg-query来进行查询操作）

dpkg -p package-name # --print-avail, 显示包的具体信息
```

获取软件包的文件信息:
```
dpkg -I 软件包名    查看deb包文件的详细信息

dpkg -c 软件包名    查询deb包文件中所包含的文件
```

重新配置软件包:
```
dpkg-reconfigure 软件包名 # 实现对指定的软件包进行配置
```

dpkg 与 dpkg-deb 的关系：
dpkg可以认为是dpkg-deb的前端，只用在遇到一些特定的参数时才会调用dpkg-deb命令。
用户通常只使用dpkg命令，dpkg命令再自动调用dpkg-deb来实现功能。

#### apt

Advanced Packaging Tool（apt）是Linux下的一款安装包管理工具，是一个客户/服务器系统。

最初只有.tar.gz的打包文件，用户必须编译每个他想在GNU/Linux上运行的软件。用户们普遍认为系统很有必要提供一种方法来管理这些安装在机器上的软件包，
当Debian诞生时，这样一个管理工具也就应运而生，它被命名为dpkg。从而著名的“package”概念第一次出现在GNU/Linux系统中，
稍后Red Hat才决定开发自己的“rpm”包管理系统。
很快一个新的问题难倒了GNU/Linux制作者，他们需要一个快速、实用、高效的方法来安装软件包，当软件包更新时，
这个工具应该能自动管理关联文件和维护已有配置文件。Debian再次率先解决了这个问题，APT(Advanced Packaging Tool）作为dpkg的前端诞生了。
APT后来还被Conectiva改造用来管理rpm，并被其它Linux发行版本采用为它们的软件包管理工具。

虽然我们在使用dpkg时，已经解决掉了软件安装过程中的大量问题，但是当依赖关系不满足时，仍然需要手动解决，
而apt这个工具解决了这样的问题，linux distribution 先将软件放置到对应的服务器中，然后分析软件的依赖关系，并且记录下来，
然后当客户端有安装软件需求时，通过清单列表与本地的dpkg以存在的软件数据相比较，就能从网络端获取所有需要的具有依赖属性的软件了。

APT是一个客户/服务器系统。在服务器上先复制所有DEB包（DEB是Debian软件包格式的文件扩展名），
然后用APT的分析工具（genbasedir）根据每个DEB 包的包头（Header）信息对所有的DEB包进行分析，并将该分析结果记录在一个文件中，
这个文件称为DEB 索引清单，APT服务器的DEB索引清单置于base文件夹内。一旦APT 服务器内的DEB有所变动，
一定要使用genbasedir产生新的DEB索引清单。客户端在进行安装或升级时先要查询DEB索引清单，从而可以获知所有具有依赖关系的软件包，
并一同下载到客户端以便安装。

当客户端需要安装、升级或删除某个软件包时，客户端计算机取得DEB索引清单压缩文件后，会将其解压置放于/var/state/apt/lists/，
而客户端使用apt-get install或apt-get upgrade命令的时候，就会将这个文件夹内的数据和客户端计算机内的DEB数据库比对，
知道哪些DEB已安装、未安装或是可以升级的。

apt-get 常简写为 apt。

常用命令：
```
apt install <package_name>          // 仅安装指定的软件命令
apt update <package_name>           // 仅更新指定的软件命令
apt upgrade                         // 更新所有软件命令
apt remove <package_name>           // 删除软件包命令（保留配置文件）
apt purge <package_name>            // 移除软件包（删除配置信息）
apt search <keyword>                // 查找软件包命令
apt list --installed                // 列出所有已安装的包
```

安装软件包示例：
> apt-get install package_name 

安装指定版本的软件包示例:
> apt-get install package_name=version  

卸载软件包：
```
apt-get remove package_name            # 卸载一个已安装的软件包（保留配置文件）
apt-get purge package_name             # 移除软件包（删除配置信息）
apt-get --purge remove packagename     # 移除软件包（删除配置信息）
```

#### 一键安装

所谓的一键安装包，如：lnmp/lamp LNMP一键安装包，就是把复杂的软件包安装过程写成了程序脚本，使用者可以执行脚本实现一键安装。
但实际安装的还是源码包和二进制包。

优点：安装简单、快捷、方便；

缺点：完全丧失了自定义性；

### 参考资料

Linux各种发行版本概述（Redhat系、Debian系） <https://blog.csdn.net/wangjianno2/article/details/51607847>

Linux中常见软件安装方法及常见管理方法 <https://blog.csdn.net/weixin_42373127/article/details/88605601> (重点讲了源代码安装和rpm安装)

Linux系统中软件的“四”种安装原理详解：源码包安装、RPM二进制安装、YUM在线安装、脚本安装包 <https://segmentfault.com/a/1190000011325357>

Linux软件安装管理之——源码安装详解 <https://segmentfault.com/a/1190000011200004?share_user=1030000007255638>

Linux软件安装管理之——RPM与YUM详解 <https://segmentfault.com/a/1190000011200461?share_user=1030000007255638>

Linux软件安装管理之——dpkg与apt-*详解 <https://segmentfault.com/a/1190000011463440?share_user=1030000007255638>

RPM软件包软件素材参考 <http://rpmfind.net/>

Linux rpm命令详解 <https://www.cnblogs.com/ftl1012/p/rpm.html>

Linux wget命令详解 <https://www.cnblogs.com/ftl1012/p/9265699.html>

Linux之wget下载 <https://www.cnblogs.com/pingzhe/p/8134984.html>

Linux tar命令 <https://www.runoob.com/linux/linux-comm-tar.html>

linux中tar命令用法 <https://www.cnblogs.com/newcaoguo/p/5896975.html>

