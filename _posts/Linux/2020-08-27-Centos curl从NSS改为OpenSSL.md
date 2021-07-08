---
layout: post
categories: Linux
title: Centos curl从NSS改为OpenSSL
meta: Centos curl从NSS改为OpenSSL
---
* content
{:toc}

### 正文

Centos中curl默认使用的NSS，而php中访问http时curl需要用OpenSSl，所以我们需要把curl从NSS改为OpenSSL。

看一下curl具体内容：
```
curl -V
```

输出：
```
curl 7.29.0 (x86_64-redhat-linux-gnu) libcurl/7.29.0 NSS/3.44 zlib/1.2.7 libidn/1.28 libssh2/1.8.0
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp scp sftp smtp smtps telnet tftp 
Features: AsynchDNS GSS-Negotiate IDN IPv6 Largefile NTLM NTLM_WB SSL libz unix-sockets
```

我们需要安装OpenSSL，Centos中安装软件用yum比较方便，不过国内需要换一下yum源，不然yum找不到软件包。
yum源换为国内源后，安装OpenSSL，再重新编译安装Curl，然后就可以使用了。

#### 更换yum源

看一下Centos版本：

```
lsb_release -a
```

输出：
```
LSB Version:    :core-4.1-amd64:core-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.8.2003 (Core)
Release:        7.8.2003
Codename:       Core
```

看到Centos是7.8版。

如果是阿里云的机子，那使用的是阿里云的源，不需要换了，不然要换一下源，可以考虑清华的源。

我们在清华源官网查看一下yum源具体地址，里面也是使用指导。

先备份：
```
sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

然后编辑 /etc/yum.repos.d/CentOS-Base.repo 文件，在 mirrorlist= 开头行前面加 # 注释掉；
并将 baseurl= 开头行取消注释（如果被注释的话），把该行内的域名（例如mirror.centos.org）替换为 mirrors.tuna.tsinghua.edu.cn。

具体内容：
```
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
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7

#released updates
[updates]
name=CentOS-$releasever - Updates
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/updates/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7



#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/extras/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7



#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos/$releasever/centosplus/$basearch/
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-7
```

#### 安装OpenSSL

看一下OpenSSL有没有安装：
```
openssl version -a
```

如果输出下面内容，说明安装了：
```
OpenSSL 1.0.2k-fips  26 Jan 2017
built on: reproducible build, date unspecified
platform: linux-x86_64
options:  bn(64,64) md2(int) rc4(16x,int) des(idx,cisc,16,int) idea(int) blowfish(idx) 
compiler: gcc -I. -I.. -I../include  -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -DKRB5_MIT -m64 -DL_ENDIAN -Wall -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -Wa,--noexecstack -DPURIFY -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM
OPENSSLDIR: "/etc/pki/tls"
engines:  rdrand dynamic
```

否则需要手动安装：
```
yum install openssl openssl-devel
```

确认一下安装：
```
OpenSSL 1.0.2k-fips  26 Jan 2017
built on: reproducible build, date unspecified
platform: linux-x86_64
options:  bn(64,64) md2(int) rc4(16x,int) des(idx,cisc,16,int) idea(int) blowfish(idx) 
compiler: gcc -I. -I.. -I../include  -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -DKRB5_MIT -m64 -DL_ENDIAN -Wall -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -Wa,--noexecstack -DPURIFY -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM
OPENSSLDIR: "/etc/pki/tls"
engines:  rdrand dynamic 
```

#### curl更换openssl

下载curl包：

> cd /usr/loacl/src

> wget https://curl.haxx.se/download/curl-7.29.0.tar.gz     

解压：

> tar xf curl-7.29.0.tar.gz 

进入解压包：

> cd curl-7.29.0

指定参数，编译安装：
```
./configure --without-nss --with-ssl && make &&make install
```

发现报错了：
```
checking whether to enable maintainer-specific portions of Makefiles... no
checking whether to enable debug build options... no
checking whether to enable compiler optimizer... (assumed) yes
checking whether to enable strict compiler warnings... no
checking whether to enable compiler warnings as errors... no
checking whether to enable curl debug memory tracking... no
checking whether to enable hiding of library internal symbols... yes
checking whether to enable c-ares for DNS lookups... no
checking for path separator... :
checking for sed... /usr/bin/sed
checking for grep... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for ar... /usr/bin/ar
checking for a BSD-compatible install... /usr/bin/install -c
checking for gcc... no
checking for cc... no
checking for cl.exe... no
configure: error: in `/usr/local/src/curl-7.29.0':
configure: error: no acceptable C compiler found in $PATH
See `config.log' for more details
```
configure: error: no acceptable C compiler found in $PATH

需要安装GCC软件套件：

> yum install gcc

然后再编译执行：
```
./configure --without-nss --with-ssl && make &&make install
```

输出大段内容后成功。

动态链接库管理命令：

```
> echo "/usr/local/lib" >>/etc/ld.so.conf
```

> ldconfig

现在再看一下curl版本详情，就是openssl了：

> curl -V

输出：
```
curl 7.29.0 (x86_64-redhat-linux-gnu) libcurl/7.29.0 OpenSSL/1.0.2k zlib/1.2.7
Protocols: dict file ftp ftps gopher http https imap imaps pop3 pop3s rtsp smtp smtps telnet tftp 
Features: IPv6 Largefile NTLM NTLM_WB SSL libz 
```


<br/><br/><br/><br/><br/>
### 参考资料

清华大学开源软件镜像站 <https://mirror.tuna.tsinghua.edu.cn/help/centos/>

curl官网 <https://curl.haxx.se/>

CentOS 6.8 curl支持的NSS修改为OpenSSL的方法 <https://www.cnblogs.com/cyleon/p/10038229.html>

linux中ldconfig的使用介绍 <https://www.cnblogs.com/schips/p/10183111.html>

