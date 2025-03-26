---
layout: post
categories: Linux
title: VirtualBox中Centos7.9搭建及各服务实现
meta: VirtualBox中Centos7.9搭建及各服务实现
---
* content
{:toc}

## Centos7.9搭建

VirtualBox中Centos7.9安装及共享文件夹搭建，参考 <https://ibaiyang.github.io/blog/linux/2023/10/27/VirtualBox中Centos7.9搭建及Docker安装各服务.html>。

## 环境工具准备

在Linux环境中，需要用到一些工具，如解压等。

**unzip安装**

```
[root@localhost ~]# yum list unzip
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
可安装的软件包
unzip.x86_64                        6.0-24.el7_9                         updates
[root@localhost ~]#
[root@localhost ~]# yum install -y unzip
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                     | 5.1 kB     00:00
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
base                                                     | 3.6 kB     00:00
docker-ce-stable                                         | 3.5 kB     00:00
extras                                                   | 2.9 kB     00:00
mysql-8.4-lts-community                                  | 2.6 kB     00:00
mysql-connectors-community                               | 2.6 kB     00:00
mysql-tools-8.4-lts-community                            | 2.6 kB     00:00
nux-dextop                                               | 2.9 kB     00:00
remi-safe                                                | 3.0 kB     00:00
updates                                                  | 2.9 kB     00:00
正在解决依赖关系
--> 正在检查事务
---> 软件包 unzip.x86_64.0.6.0-24.el7_9 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package        架构            版本                     源                大小
================================================================================
正在安装:
 unzip          x86_64          6.0-24.el7_9             updates          172 k

事务概要
================================================================================
安装  1 软件包

总下载量：172 k
安装大小：369 k
Downloading packages:
unzip-6.0-24.el7_9.x86_64.rpm                              | 172 kB   00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : unzip-6.0-24.el7_9.x86_64                                   1/1
  验证中      : unzip-6.0-24.el7_9.x86_64                                   1/1

已安装:
  unzip.x86_64 0:6.0-24.el7_9

完毕！
[root@localhost ~]#
```

## PHP7.4安装

安装 EPEL 存储库
> yum install epel-release -y

安装Remi存储库
> rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

搜索及查看PHP包：
> yum search php74
> 
> yum list php74

详细：
```
[root@localhost ~]# ll /etc/yum.repos.d/
总用量 48
-rw-r--r--. 1 root root 2523 9月   3 18:00 CentOS-Base.repo
-rw-r--r--. 1 root root 1664 10月 23 2020 CentOS-Base.repo.backup
-rw-r--r--. 1 root root 1309 5月  21 22:48 CentOS-CR.repo
-rw-r--r--. 1 root root  649 5月  21 22:48 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 5月  21 22:48 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 5月  21 22:48 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 5月  21 22:48 CentOS-Sources.repo
-rw-r--r--. 1 root root 9454 5月  21 22:48 CentOS-Vault.repo
-rw-r--r--. 1 root root  616 5月  21 22:48 CentOS-x86_64-kernel.repo
-rw-r--r--. 1 root root 2081 9月  13 14:14 docker-ce.repo
[root@localhost ~]#
[root@localhost ~]# yum list php*
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
可安装的软件包
php.x86_64                              5.4.16-48.el7                    base
php-bcmath.x86_64                       5.4.16-48.el7                    base
php-cli.x86_64                          5.4.16-48.el7                    base
php-common.x86_64                       5.4.16-48.el7                    base
php-dba.x86_64                          5.4.16-48.el7                    base
php-devel.x86_64                        5.4.16-48.el7                    base
php-embedded.x86_64                     5.4.16-48.el7                    base
php-enchant.x86_64                      5.4.16-48.el7                    base
php-fpm.x86_64                          5.4.16-48.el7                    base
php-gd.x86_64                           5.4.16-48.el7                    base
php-intl.x86_64                         5.4.16-48.el7                    base
php-ldap.x86_64                         5.4.16-48.el7                    base
php-mbstring.x86_64                     5.4.16-48.el7                    base
php-mysql.x86_64                        5.4.16-48.el7                    base
php-mysqlnd.x86_64                      5.4.16-48.el7                    base
php-odbc.x86_64                         5.4.16-48.el7                    base
php-pdo.x86_64                          5.4.16-48.el7                    base
php-pear.noarch                         1:1.9.4-23.el7_9                 updates
php-pecl-memcache.x86_64                3.0.8-4.el7                      base
php-pgsql.x86_64                        5.4.16-48.el7                    base
php-process.x86_64                      5.4.16-48.el7                    base
php-pspell.x86_64                       5.4.16-48.el7                    base
php-recode.x86_64                       5.4.16-48.el7                    base
php-snmp.x86_64                         5.4.16-48.el7                    base
php-soap.x86_64                         5.4.16-48.el7                    base
php-xml.x86_64                          5.4.16-48.el7                    base
php-xmlrpc.x86_64                       5.4.16-48.el7                    base
[root@localhost ~]#
[root@localhost ~]# yum repolist
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
源标识                        源名称                                      状态
!base/7/x86_64                CentOS-7 - Base - mirrors.aliyun.com        10,072
!docker-ce-stable/7/x86_64    Docker CE Stable - x86_64                      337
!extras/7/x86_64              CentOS-7 - Extras - mirrors.aliyun.com         526
!updates/7/x86_64             CentOS-7 - Updates - mirrors.aliyun.com      6,173
repolist: 17,108
[root@localhost ~]#
[root@localhost ~]# yum install epel-release -y
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
base                                                              | 3.6 kB  00:00:00
docker-ce-stable                                                  | 3.5 kB  00:00:00
extras                                                            | 2.9 kB  00:00:00
updates                                                           | 2.9 kB  00:00:00
正在解决依赖关系
--> 正在检查事务
---> 软件包 epel-release.noarch.0.7-11 将被 安装
--> 解决依赖关系完成

依赖关系解决

=========================================================================================
 Package                  架构               版本               源                  大小
=========================================================================================
正在安装:
 epel-release             noarch             7-11               extras              15 k

事务概要
=========================================================================================
安装  1 软件包

总下载量：15 k
安装大小：24 k
Downloading packages:
epel-release-7-11.noarch.rpm                                      |  15 kB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : epel-release-7-11.noarch                                             1/1
  验证中      : epel-release-7-11.noarch                                             1/1

已安装:
  epel-release.noarch 0:7-11

完毕！
[root@localhost ~]#
[root@localhost ~]# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
获取http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
警告：/var/tmp/rpm-tmp.BFmRj7: 头V4 DSA/SHA1 Signature, 密钥 ID 00f97f56: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:remi-release-7.9-6.el7.remi      ################################# [100%]
[root@localhost ~]#
[root@localhost ~]# ls -l /etc/yum.repos.d/
总用量 108
-rw-r--r--. 1 root root 2523 9月   3 18:00 CentOS-Base.repo
-rw-r--r--. 1 root root 1664 10月 23 2020 CentOS-Base.repo.backup
-rw-r--r--. 1 root root 1309 5月  21 22:48 CentOS-CR.repo
-rw-r--r--. 1 root root  649 5月  21 22:48 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 5月  21 22:48 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 5月  21 22:48 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 5月  21 22:48 CentOS-Sources.repo
-rw-r--r--. 1 root root 9454 5月  21 22:48 CentOS-Vault.repo
-rw-r--r--. 1 root root  616 5月  21 22:48 CentOS-x86_64-kernel.repo
-rw-r--r--. 1 root root  951 10月  3 2017 epel.repo
-rw-r--r--. 1 root root 1050 10月  3 2017 epel-testing.repo
-rw-r--r--. 1 root root  855 11月 28 2023 remi-modular.repo
-rw-r--r--. 1 root root  456 11月 28 2023 remi-php54.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php70.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php71.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php72.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php73.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php74.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php80.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php81.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php82.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php83.repo
-rw-r--r--. 1 root root 2605 11月 28 2023 remi.repo
-rw-r--r--. 1 root root  750 11月 28 2023 remi-safe.repo
[root@localhost ~]#
[root@localhost ~]# yum search php74
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
================================== N/S matched: php74 ===================================
php74-php-pecl-http-message-devel.x86_64 : php74-php-pecl-http-message developer files
                                         : (headers)
php74-php-pecl-pcsc-devel.x86_64 : php74-php-pecl-pcsc developer files (header)
php74-php-pecl-propro-devel.x86_64 : php74-php-pecl-propro developer files (header)
php74-php-pecl-psr-devel.x86_64 : php74-php-pecl-psr developer files (header)
php74-php-pecl-raphf-devel.x86_64 : php74-php-pecl-raphf developer files (header)
php74-php-pecl-xmldiff-devel.x86_64 : php74-php-pecl-xmldiff developer files (header)
php74-php-pecl-yaconf-devel.x86_64 : php74-php-pecl-yaconf developer files (header)
php74-php-zephir-parser-devel.x86_64 : php74-php-zephir-parser developer files (headers)
php74-php-zstd-devel.x86_64 : php74-php-zstd developer files (header)
php74-runtime.x86_64 : Package that handles php74 Software Collection.
php74-scldevel.x86_64 : Package shipping development files for php74
php74-syspaths.x86_64 : System-wide wrappers for the php74 package
php74.x86_64 : Package that installs PHP 7.4
php74-build.x86_64 : Package shipping basic build configuration
php74-php.x86_64 : PHP scripting language for creating dynamic web sites
php74-php-ast.x86_64 : Abstract Syntax Tree
php74-php-bcmath.x86_64 : A module for PHP applications for using the bcmath library
php74-php-bolt.x86_64 : Protect PHP Source Code
php74-php-brotli.x86_64 : Brotli Extension for PHP
php74-php-cli.x86_64 : Command-line interface for PHP
php74-php-common.x86_64 : Common files for PHP
php74-php-componere.x86_64 : Composing PHP classes at runtime
php74-php-dba.x86_64 : A database abstraction layer module for PHP applications
php74-php-dbg.x86_64 : The interactive PHP debugger
php74-php-devel.x86_64 : Files needed for building PHP extensions
php74-php-embedded.x86_64 : PHP library for embedding in applications
php74-php-enchant.x86_64 : Enchant spelling extension for PHP applications
php74-php-ffi.x86_64 : Foreign Function Interface
php74-php-fpm.x86_64 : PHP FastCGI Process Manager
php74-php-gd.x86_64 : A module for PHP applications for using the gd graphics library
php74-php-geos.x86_64 : PHP module for GEOS
php74-php-gmp.x86_64 : A module for PHP applications for using the GNU MP library
php74-php-horde-horde-lz4.x86_64 : Horde LZ4 Compression Extension
php74-php-imap.x86_64 : A module for PHP applications that use IMAP
php74-php-intl.x86_64 : Internationalization extension for PHP applications
php74-php-ioncube-loader.x86_64 : Loader for ionCube Encoded Files with ionCube 24
                                : support
php74-php-json.x86_64 : JavaScript Object Notation extension for PHP
php74-php-ldap.x86_64 : A module for PHP applications that use LDAP
php74-php-libvirt.x86_64 : PHP language binding for Libvirt
php74-php-libvirt-doc.noarch : Document of php-libvirt
php74-php-litespeed.x86_64 : LiteSpeed Web Server PHP support
php74-php-lz4.x86_64 : LZ4 Extension for PHP
php74-php-maxminddb.x86_64 : MaxMind DB Reader extension
php74-php-mbstring.x86_64 : A module for PHP applications which need multi-byte string
                          : handling
php74-php-mysqlnd.x86_64 : A module for PHP applications that use MySQL databases
php74-php-oci8.x86_64 : A module for PHP applications that use OCI8 databases
php74-php-odbc.x86_64 : A module for PHP applications that use ODBC databases
php74-php-opcache.x86_64 : The Zend OPcache
php74-php-pdlib.x86_64 : A PHP extension for Dlib
php74-php-pdo.x86_64 : A database access abstraction module for PHP applications
php74-php-pdo-dblib.x86_64 : PDO driver for Microsoft SQL Server and Sybase databases
php74-php-pdo-firebird.x86_64 : PDO driver for Interbase/Firebird databases
php74-php-pear.noarch : PHP Extension and Application Repository framework
php74-php-pecl-ahocorasick.x86_64 : Effective Aho-Corasick string pattern matching
                                  : algorithm
php74-php-pecl-amqp.x86_64 : Communicate with any AMQP compliant server
php74-php-pecl-apcu.x86_64 : APC User Cache
php74-php-pecl-apcu-bc.x86_64 : APCu Backwards Compatibility Module
php74-php-pecl-apcu-devel.x86_64 : APCu developer files (header)
php74-php-pecl-apfd.x86_64 : Always Populate Form Data
php74-php-pecl-awscrt.x86_64 : AWS Common Runtime PHP bindings
php74-php-pecl-base58.x86_64 : Encode and decode data with base58
php74-php-pecl-bitset.x86_64 : BITSET library
php74-php-pecl-bsdiff.x86_64 : The bsdiff extension for PHP
php74-php-pecl-cassandra.x86_64 : DataStax PHP Driver for Apache Cassandra
php74-php-pecl-cmark.x86_64 : CommonMark extension
php74-php-pecl-couchbase2.x86_64 : Couchbase Server PHP extension
php74-php-pecl-couchbase3.x86_64 : Couchbase Server PHP extension
php74-php-pecl-couchbase4.x86_64 : Couchbase Server PHP extension
php74-php-pecl-crypto.x86_64 : Wrapper for OpenSSL Crypto Library
php74-php-pecl-csv.x86_64 : CSV PHP extension
php74-php-pecl-datadog-trace.x86_64 : APM and distributed tracing for PHP
php74-php-pecl-dbase.x86_64 : dBase database file access functions
php74-php-pecl-decimal.x86_64 : Arbitrary-precision floating-point decimal
php74-php-pecl-dio.x86_64 : Direct I/O functions
php74-php-pecl-druid.x86_64 : A Druid driver for PHP
php74-php-pecl-ds.x86_64 : Data Structures for PHP
php74-php-pecl-eio.x86_64 : Provides interface to the libeio library
php74-php-pecl-env.x86_64 : Load environment variables
php74-php-pecl-ev.x86_64 : Provides interface to libev library
php74-php-pecl-event.x86_64 : Provides interface to libevent library
php74-php-pecl-excimer.x86_64 : Interrupting timer and low-overhead sampling profiler
php74-php-pecl-fann.x86_64 : Wrapper for FANN Library
php74-php-pecl-gearman.x86_64 : PHP wrapper to libgearman
php74-php-pecl-gender.x86_64 : Gender Extension
php74-php-pecl-geoip.x86_64 : Extension to map IP addresses to geographic places
php74-php-pecl-geospatial.x86_64 : PHP Extension to handle common geospatial functions
php74-php-pecl-gmagick.x86_64 : Provides a wrapper to the GraphicsMagick library
php74-php-pecl-gnupg.x86_64 : Wrapper around the gpgme library
php74-php-pecl-grpc.x86_64 : General RPC framework
php74-php-pecl-handlebars.x86_64 : Handlebars templating language
php74-php-pecl-hdr-histogram.x86_64 : PHP extension wrapper for the C hdrhistogram API
php74-php-pecl-hprose.x86_64 : Hprose for PHP
php74-php-pecl-hrtime.x86_64 : High resolution timing
php74-php-pecl-http.x86_64 : Extended HTTP support
php74-php-pecl-http-devel.x86_64 : Extended HTTP support developer files (header)
php74-php-pecl-http-message.x86_64 : PSR-7 HTTP Message implementation
php74-php-pecl-ice.x86_64 : Simple and fast PHP framework
php74-php-pecl-igbinary.x86_64 : Replacement for the standard PHP serializer
php74-php-pecl-igbinary-devel.x86_64 : Igbinary developer files (header)
php74-php-pecl-imagick.x86_64 : Extension to create and modify images using ImageMagick
php74-php-pecl-imagick-devel.x86_64 : imagick extension developer files (header)
php74-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php74-php-pecl-imagick-im6-devel.x86_64 : imagick extension developer files (header)
php74-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php74-php-pecl-imagick-im7-devel.x86_64 : imagick extension developer files (header)
php74-php-pecl-immutable-cache.x86_64 : An shared memory cache of immutable values
php74-php-pecl-inotify.x86_64 : Inotify
php74-php-pecl-interbase.x86_64 : InterBase/FireBird extension
php74-php-pecl-ip2location.x86_64 : Get geo location information of an IP address
php74-php-pecl-ip2proxy.x86_64 : Get proxy information of an IP address
php74-php-pecl-json-post.x86_64 : JSON POST handler
php74-php-pecl-jsonpath.x86_64 : Extract data using JSONPath notation
php74-php-pecl-krb5.x86_64 : Kerberos authentification extension
php74-php-pecl-krb5-devel.x86_64 : Kerberos extension developer files (header)
php74-php-pecl-leveldb.x86_64 : LevelDB PHP bindings
php74-php-pecl-lua.x86_64 : Embedded lua interpreter
php74-php-pecl-luasandbox.x86_64 : Lua interpreter with limits and safe environment
php74-php-pecl-lzf.x86_64 : Extension to handle LZF de/compression
php74-php-pecl-mailparse.x86_64 : PHP PECL package for parsing and working with email
                                : messages
php74-php-pecl-mcrypt.x86_64 : Bindings for the libmcrypt library
php74-php-pecl-memcache.x86_64 : Extension to work with the Memcached caching daemon
php74-php-pecl-memcached.x86_64 : Extension to work with the Memcached caching daemon
php74-php-pecl-memprof.x86_64 : Memory usage profiler
php74-php-pecl-mogilefs.x86_64 : PHP client library to communicate with the MogileFS
                               : storage
php74-php-pecl-mongodb.x86_64 : MongoDB driver for PHP
php74-php-pecl-mosquitto.x86_64 : Extension for libmosquitto
php74-php-pecl-msgpack.x86_64 : API for communicating with MessagePack serialization
php74-php-pecl-msgpack-devel.x86_64 : MessagePack developer files (header)
php74-php-pecl-mustache.x86_64 : Mustache templating language
php74-php-pecl-mysql.x86_64 : MySQL database access functions
php74-php-pecl-mysql-xdevapi.x86_64 : MySQL database access functions
php74-php-pecl-mysqlnd-azure.x86_64 : Redirection plugin for mysqlnd
php74-php-pecl-mysqlnd-krb.x86_64 : Kerberos authentication plugin for mysqlnd
php74-php-pecl-nsq.x86_64 : PHP extension for NSQ client
php74-php-pecl-oauth.x86_64 : PHP OAuth consumer extension
php74-php-pecl-opencensus.x86_64 : A stats collection and distributed tracing framework
php74-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed networking
                                 : framework
php74-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for PHP with
                                   : Async IO, Coroutines and Fibers
php74-php-pecl-orng.x86_64 : Object scoped PRNG Extension
php74-php-pecl-pam.x86_64 : PAM integration
php74-php-pecl-parle.x86_64 : Parsing and lexing
php74-php-pecl-pcov.x86_64 : Code coverage driver
php74-php-pecl-pcs.x86_64 : PHP Code Service
php74-php-pecl-pcs-devel.x86_64 : PHP Code Service (header)
php74-php-pecl-pcsc.x86_64 : An extension for PHP using the winscard PC/SC API
php74-php-pecl-pdflib.x86_64 : Package for generating PDF files
php74-php-pecl-pkcs11.x86_64 : PHP Bindings for PKCS11 modules
php74-php-pecl-pq.x86_64 : PostgreSQL client library (libpq) binding
php74-php-pecl-propro.x86_64 : Property proxy
php74-php-pecl-protobuf.x86_64 : Mechanism for serializing structured data
php74-php-pecl-ps.x86_64 : An extension to create PostScript files
php74-php-pecl-psr.x86_64 : PSR interfaces
php74-php-pecl-quickhash.x86_64 : Set of specific strongly-typed classes for sets and
                                : hashing
php74-php-pecl-radius.x86_64 : Radius client library
php74-php-pecl-raphf.x86_64 : Resource and persistent handles factory
php74-php-pecl-rar.x86_64 : PHP extension for reading RAR archives
php74-php-pecl-rdkafka.x86_64 : Kafka client based on librdkafka
php74-php-pecl-rdkafka4.x86_64 : Kafka client based on librdkafka
php74-php-pecl-rdkafka5.x86_64 : Kafka client based on librdkafka
php74-php-pecl-rdkafka6.x86_64 : Kafka client based on librdkafka
php74-php-pecl-recode.x86_64 : A module for PHP applications for using the recode library
php74-php-pecl-redis4.x86_64 : Extension for communicating with the Redis key-value store
php74-php-pecl-redis5.x86_64 : Extension for communicating with the Redis key-value store
php74-php-pecl-redis6.x86_64 : Extension for communicating with the Redis key-value store
php74-php-pecl-request.x86_64 : Server-side request and response objects
php74-php-pecl-rpminfo.x86_64 : RPM information
php74-php-pecl-rrd.x86_64 : PHP Bindings for rrdtool
php74-php-pecl-runkit7.x86_64 : For all those things you... shouldn't have been doing
                              : anyway... but surely do!
php74-php-pecl-scoutapm.x86_64 : Native Extension Component for ScoutAPM's PHP Agent
php74-php-pecl-scrypt.x86_64 : Scrypt hashing function
php74-php-pecl-sdl.x86_64 : Simple DirectMedia Layer for PHP
php74-php-pecl-sdl-ttf.x86_64 : SDL_ttf bindings for PHP
php74-php-pecl-seasclick.x86_64 : An Yandex ClickHouse client driven extension for PHP
php74-php-pecl-seaslog.x86_64 : An effective, fast, stable log extension for PHP
php74-php-pecl-seassnowflake.x86_64 : PHP Extension for Distributed unique ID generator
php74-php-pecl-selinux.x86_64 : SELinux binding for PHP scripting language
php74-php-pecl-simdjson.x86_64 : Provides faster json decoding through simdjson bindings
                               : for PHP
php74-php-pecl-simple-kafka-client.x86_64 : Kafka client based on librdkafka
php74-php-pecl-skywalking.x86_64 : The PHP instrument agent for Apache SkyWalking
php74-php-pecl-solr2.x86_64 : API orientée objet pour Apache Solr
php74-php-pecl-ssdeep.x86_64 : Wrapper for libfuzzy library
php74-php-pecl-ssh2.x86_64 : Bindings for the libssh2 library
php74-php-pecl-stats.x86_64 : Routines for statistical computation
php74-php-pecl-stomp.x86_64 : Stomp client extension
php74-php-pecl-svm.x86_64 : Support Vector Machine Library
php74-php-pecl-svn.x86_64 : PHP Bindings for the Subversion Revision control system
php74-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed networking
                              : framework
php74-php-pecl-sync.x86_64 : Named and unnamed synchronization objects
php74-php-pecl-taint.x86_64 : XSS code sniffer
php74-php-pecl-tcpwrap.x86_64 : Tcpwrappers binding
php74-php-pecl-tensor.x86_64 : Objects for scientific computing in PHP
php74-php-pecl-termbox.x86_64 : A termbox wrapper for PHP
php74-php-pecl-timecop.x86_64 : Time travel and freezing extension
php74-php-pecl-trader.x86_64 : Technical Analysis for traders
php74-php-pecl-translit.x86_64 : Transliterates non-latin character sets to latin
php74-php-pecl-trie.x86_64 : PHP Trie extension
php74-php-pecl-uopz.x86_64 : User Operations for Zend
php74-php-pecl-uploadprogress.x86_64 : An extension to track progress of a file upload
php74-php-pecl-uuid.x86_64 : Universally Unique Identifier extension for PHP
php74-php-pecl-uv.x86_64 : Libuv wrapper
php74-php-pecl-var-representation.x86_64 : A compact, more readable alternative to
                                         : var_export
php74-php-pecl-varnish.x86_64 : Varnish Cache bindings
php74-php-pecl-vips.x86_64 : PHP extension for interfacing with libvips
php74-php-pecl-vld.x86_64 : Dump the internal representation of PHP scripts
php74-php-pecl-wddx.x86_64 : Web Distributed Data Exchange
php74-php-pecl-weakreference-bc.x86_64 : Polyfill of WeakMap/WeakReference for older PHP
                                       : versions
php74-php-pecl-xattr.x86_64 : Extended attributes
php74-php-pecl-xdebug.x86_64 : PECL package for debugging PHP scripts
php74-php-pecl-xdebug3.x86_64 : Provides functions for function traces and profiling
php74-php-pecl-xdiff.x86_64 : File differences/patches
php74-php-pecl-xhprof.x86_64 : PHP extension for XHProf, a Hierarchical Profiler
php74-php-pecl-xlswriter.x86_64 : An efficient and fast xlsx file extension
php74-php-pecl-xmldiff.x86_64 : XML diff and merge
php74-php-pecl-xxtea.x86_64 : XXTEA encryption algorithm extension for PHP
php74-php-pecl-yac.x86_64 : Lockless user data cache
php74-php-pecl-yaconf.x86_64 : Yet Another Configurations Container
php74-php-pecl-yaf.x86_64 : Yet Another Framework
php74-php-pecl-yaml.x86_64 : PHP Bindings for yaml
php74-php-pecl-yar.x86_64 : Light, concurrent RPC framework
php74-php-pecl-yaz.x86_64 : Z39.50/SRU client
php74-php-pecl-zip.x86_64 : A ZIP archive management extension
php74-php-pecl-zmq.x86_64 : ZeroMQ messaging
php74-php-pggi.x86_64 : GTK bindings
php74-php-pgsql.x86_64 : A PostgreSQL database module for PHP
php74-php-phalcon4.x86_64 : Phalcon Framework
php74-php-phalcon5.x86_64 : Phalcon Framework
php74-php-phpiredis.x86_64 : Client extension for Redis
php74-php-pinba.x86_64 : Client extension for Pinba statistics server
php74-php-process.x86_64 : Modules for PHP script using system process interfaces
php74-php-pspell.x86_64 : A module for PHP applications for using pspell interfaces
php74-php-realpath-turbo.x86_64 : Use realpath cache despite open_basedir restriction
php74-php-smbclient.x86_64 : PHP wrapper for libsmbclient
php74-php-snappy.x86_64 : Snappy Extension for PHP
php74-php-snmp.x86_64 : A module for PHP applications that query SNMP-managed devices
php74-php-snuffleupagus.x86_64 : Security module for PHP
php74-php-soap.x86_64 : A module for PHP applications that use the SOAP protocol
php74-php-sodium.x86_64 : Wrapper for the Sodium cryptographic library
php74-php-sqlsrv.x86_64 : Microsoft Drivers for PHP for SQL Server
php74-php-tidy.x86_64 : Standard PHP module provides tidy library support
php74-php-wkhtmltox.x86_64 : HTML Converter
php74-php-xml.x86_64 : A module for PHP applications which use XML
php74-php-xmlrpc.x86_64 : A module for PHP applications which use the XML-RPC protocol
php74-php-xz.x86_64 : XZ (LZMA2) compression/decompression
php74-php-zephir-parser.x86_64 : Zephir code parser
php74-php-zstd.x86_64 : Zstandard extension
php74-unit-php.x86_64 : PHP module for NGINX Unit
php74-uwsgi-plugin-php.x86_64 : uWSGI - Plugin for PHP support
php74-xhprof.noarch : A Hierarchical Profiler for PHP - Web interface
php74-zephir.noarch : Zephir language for creation of extensions for PHP.

  名称和简介匹配 only，使用“search all”试试。
[root@localhost ~]#
[root@localhost ~]# yum list php74
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                              | 4.7 kB  00:00:00
 * base: mirrors.aliyun.com
 * epel: mirror.math.princeton.edu
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
epel                                                              | 4.3 kB  00:00:00
remi-safe                                                         | 3.0 kB  00:00:00
(1/4): remi-safe/primary_db                                       | 2.6 MB  00:00:03
(2/4): epel/x86_64/group                                          | 399 kB  00:00:08
(3/4): epel/x86_64/primary_db                                     | 8.7 MB  00:00:19
(4/4): epel/x86_64/updateinfo                                     | 1.0 MB  00:00:19
可安装的软件包
php74.x86_64                           7.4-3.el7.remi                           remi-safe
[root@localhost ~]#
[root@localhost ~]# yum list php74*
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
可安装的软件包
php74.x86_64                              7.4-3.el7.remi                        remi-safe
php74-build.x86_64                        7.4-3.el7.remi                        remi-safe
php74-php.x86_64                          7.4.33-15.el7.remi                    remi-safe
php74-php-ast.x86_64                      1.1.1-1.el7.remi                      remi-safe
php74-php-bcmath.x86_64                   7.4.33-15.el7.remi                    remi-safe
php74-php-bolt.x86_64                     1.0.5-1.el7.remi                      remi-safe
php74-php-brotli.x86_64                   0.15.0-1.el7.remi                     remi-safe
php74-php-cli.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-common.x86_64                   7.4.33-15.el7.remi                    remi-safe
php74-php-componere.x86_64                3.1.2-1.el7.remi                      remi-safe
php74-php-dba.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-dbg.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-devel.x86_64                    7.4.33-15.el7.remi                    remi-safe
php74-php-embedded.x86_64                 7.4.33-15.el7.remi                    remi-safe
php74-php-enchant.x86_64                  7.4.33-15.el7.remi                    remi-safe
php74-php-ffi.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-fpm.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-gd.x86_64                       7.4.33-15.el7.remi                    remi-safe
php74-php-geos.x86_64                     1.0.0-11.el7.remi                     remi-safe
php74-php-gmp.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-horde-horde-lz4.x86_64          1.0.10-15.el7.remi                    remi-safe
php74-php-imap.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-intl.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-ioncube-loader.x86_64           13.0.4-1.el7.remi                     remi-safe
php74-php-json.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-ldap.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-libvirt.x86_64                  0.5.7-1.el7.remi                      remi-safe
php74-php-libvirt-doc.noarch              0.5.7-1.el7.remi                      remi-safe
php74-php-litespeed.x86_64                7.4.33-15.el7.remi                    remi-safe
php74-php-lz4.x86_64                      0.4.3-1.el7.remi                      remi-safe
php74-php-maxminddb.x86_64                1.11.1-1.el7.remi                     remi-safe
php74-php-mbstring.x86_64                 7.4.33-15.el7.remi                    remi-safe
php74-php-mysqlnd.x86_64                  7.4.33-15.el7.remi                    remi-safe
php74-php-oci8.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-odbc.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-opcache.x86_64                  7.4.33-15.el7.remi                    remi-safe
php74-php-pdlib.x86_64                    1.1.0-2.el7.remi                      remi-safe
php74-php-pdo.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-pdo-dblib.x86_64                7.4.33-15.el7.remi                    remi-safe
php74-php-pdo-firebird.x86_64             7.4.33-15.el7.remi                    remi-safe
php74-php-pear.noarch                     1:1.10.15-3.el7.remi                  remi-safe
php74-php-pecl-ahocorasick.x86_64         0.0.7-3.el7.remi                      remi-safe
php74-php-pecl-amqp.x86_64                2.1.2-1.el7.remi                      remi-safe
php74-php-pecl-apcu.x86_64                5.1.23-1.el7.remi                     remi-safe
php74-php-pecl-apcu-bc.x86_64             1.0.5-3.el7.remi                      remi-safe
php74-php-pecl-apcu-devel.x86_64          5.1.23-1.el7.remi                     remi-safe
php74-php-pecl-apfd.x86_64                1.0.3-1.el7.remi                      remi-safe
php74-php-pecl-awscrt.x86_64              1.2.6-1.el7.remi                      remi-safe
php74-php-pecl-base58.x86_64              1.0.2-5.el7.remi                      remi-safe
php74-php-pecl-bitset.x86_64              3.2.0-1.el7.remi                      remi-safe
php74-php-pecl-bsdiff.x86_64              0.1.2-1.el7.remi                      remi-safe
php74-php-pecl-cassandra.x86_64           1.3.2-8.el7.remi                      remi-safe
php74-php-pecl-cmark.x86_64               1.2.0-3.el7.remi                      remi-safe
php74-php-pecl-couchbase2.x86_64          2.6.2-2.el7.remi                      remi-safe
php74-php-pecl-couchbase3.x86_64          3.2.2-1.el7.remi                      remi-safe
php74-php-pecl-couchbase4.x86_64          4.1.0-1.el7.remi                      remi-safe
php74-php-pecl-crypto.x86_64              0.3.2-1.el7.remi                      remi-safe
php74-php-pecl-csv.x86_64                 0.3.2-1.el7.remi                      remi-safe
php74-php-pecl-datadog-trace.x86_64       1.1.0-1.el7.remi                      remi-safe
php74-php-pecl-dbase.x86_64               7.1.1-1.el7.remi                      remi-safe
php74-php-pecl-decimal.x86_64             1.5.0-1.el7.remi                      remi-safe
php74-php-pecl-dio.x86_64                 0.2.2-1.el7.remi                      remi-safe
php74-php-pecl-druid.x86_64               1.0.0-5.el7.remi                      remi-safe
php74-php-pecl-ds.x86_64                  1.5.0-1.el7.remi                      remi-safe
php74-php-pecl-eio.x86_64                 3.1.3-1.el7.remi                      remi-safe
php74-php-pecl-env.x86_64                 0.2.1-8.el7.remi                      remi-safe
php74-php-pecl-ev.x86_64                  1.1.5-1.el7.remi                      remi-safe
php74-php-pecl-event.x86_64               3.1.3-1.el7.remi                      remi-safe
php74-php-pecl-excimer.x86_64             1.2.1-1.el7.remi                      remi-safe
php74-php-pecl-fann.x86_64                1.2.0-1.el7.remi                      remi-safe
php74-php-pecl-gearman.x86_64             2.1.2-1.el7.remi                      remi-safe
php74-php-pecl-gender.x86_64              1.1.0-14.el7.remi                     remi-safe
php74-php-pecl-geoip.x86_64               1.1.1-11.el7.remi                     remi-safe
php74-php-pecl-geospatial.x86_64          0.3.2-1.el7.remi                      remi-safe
php74-php-pecl-gmagick.x86_64             2.0.6~RC1-1.el7.remi                  remi-safe
php74-php-pecl-gnupg.x86_64               1.5.1-1.el7.remi                      remi-safe
php74-php-pecl-grpc.x86_64                1.63.0-1.el7.remi                     remi-safe
php74-php-pecl-handlebars.x86_64          1.0.0-1.el7.remi                      remi-safe
php74-php-pecl-hdr-histogram.x86_64       0.5.0-1.el7.remi                      remi-safe
php74-php-pecl-hprose.x86_64              1.8.0-1.el7.remi                      remi-safe
php74-php-pecl-hrtime.x86_64              0.6.0-7.el7.remi                      remi-safe
php74-php-pecl-http.x86_64                3.3.0-2.el7.remi                      remi-safe
php74-php-pecl-http-devel.x86_64          3.3.0-2.el7.remi                      remi-safe
php74-php-pecl-http-message.x86_64        1.0.0-1.el7.remi                      remi-safe
php74-php-pecl-http-message-devel.x86_64  1.0.0-1.el7.remi                      remi-safe
php74-php-pecl-ice.x86_64                 1.10.1-1.el7.remi                     remi-safe
php74-php-pecl-igbinary.x86_64            3.2.15-1.el7.remi                     remi-safe
php74-php-pecl-igbinary-devel.x86_64      3.2.15-1.el7.remi                     remi-safe
php74-php-pecl-imagick.x86_64             3.4.4-17.el7.remi                     remi-safe
php74-php-pecl-imagick-devel.x86_64       3.4.4-17.el7.remi                     remi-safe
php74-php-pecl-imagick-im6.x86_64         3.7.0-7.el7.remi                      remi-safe
php74-php-pecl-imagick-im6-devel.x86_64   3.7.0-7.el7.remi                      remi-safe
php74-php-pecl-imagick-im7.x86_64         3.7.0-7.el7.remi                      remi-safe
php74-php-pecl-imagick-im7-devel.x86_64   3.7.0-7.el7.remi                      remi-safe
php74-php-pecl-immutable-cache.x86_64     6.1.0-1.el7.remi                      remi-safe
php74-php-pecl-inotify.x86_64             3.0.0-1.el7.remi                      remi-safe
php74-php-pecl-interbase.x86_64           1.0.0~DEV.20190523-5.el7.remi         remi-safe
php74-php-pecl-ip2location.x86_64         8.2.0-2.el7.remi                      remi-safe
php74-php-pecl-ip2proxy.x86_64            3.0.3-3.el7.remi                      remi-safe
php74-php-pecl-json-post.x86_64           1.1.0-1.el7.remi                      remi-safe
php74-php-pecl-jsonpath.x86_64            1.0.1-1.el7.remi                      remi-safe
php74-php-pecl-krb5.x86_64                1.2.2-1.el7.remi                      remi-safe
php74-php-pecl-krb5-devel.x86_64          1.2.2-1.el7.remi                      remi-safe
php74-php-pecl-leveldb.x86_64             0.3.0-1.el7.remi                      remi-safe
php74-php-pecl-lua.x86_64                 2.0.7-1.el7.remi                      remi-safe
php74-php-pecl-luasandbox.x86_64          4.1.2-1.el7.remi                      remi-safe
php74-php-pecl-lzf.x86_64                 1.7.0-1.el7.remi                      remi-safe
php74-php-pecl-mailparse.x86_64           3.1.6-1.el7.remi                      remi-safe
php74-php-pecl-mcrypt.x86_64              1.0.7-1.el7.remi                      remi-safe
php74-php-pecl-memcache.x86_64            4.0.5.2-1.el7.remi                    remi-safe
php74-php-pecl-memcached.x86_64           3.2.0-1.el7.remi                      remi-safe
php74-php-pecl-memprof.x86_64             3.0.2-1.el7.remi                      remi-safe
php74-php-pecl-mogilefs.x86_64            0.9.3.1-10.el7.remi                   remi-safe
php74-php-pecl-mongodb.x86_64             1.19.2-1.el7.remi                     remi-safe
php74-php-pecl-mosquitto.x86_64           0.4.0-7.el7.remi                      remi-safe
php74-php-pecl-msgpack.x86_64             2.2.0-1.el7.remi                      remi-safe
php74-php-pecl-msgpack-devel.x86_64       2.2.0-1.el7.remi                      remi-safe
php74-php-pecl-mustache.x86_64            0.9.3-1.el7.remi                      remi-safe
php74-php-pecl-mysql.x86_64               1.0.0-0.23.20190415.d7643af.el7.remi  remi-safe
php74-php-pecl-mysql-xdevapi.x86_64       8.0.20-1.el7.remi                     remi-safe
php74-php-pecl-mysqlnd-azure.x86_64       1.1.1-1.el7.remi                      remi-safe
php74-php-pecl-mysqlnd-krb.x86_64         1.0.0-1.el7.remi                      remi-safe
php74-php-pecl-nsq.x86_64                 3.5.1-1.el7.remi                      remi-safe
php74-php-pecl-oauth.x86_64               2.0.7-1.el7.remi                      remi-safe
php74-php-pecl-opencensus.x86_64          0.3.0-1.el7.remi                      remi-safe
php74-php-pecl-openswoole.x86_64          4.12.1-1.el7.remi                     remi-safe
php74-php-pecl-openswoole22.x86_64        22.0.0-1.el7.remi                     remi-safe
php74-php-pecl-orng.x86_64                0.0.3-1.el7.remi                      remi-safe
php74-php-pecl-pam.x86_64                 2.2.5-1.el7.remi                      remi-safe
php74-php-pecl-parle.x86_64               0.8.5-1.el7.remi                      remi-safe
php74-php-pecl-pcov.x86_64                1.0.11-1.el7.remi                     remi-safe
php74-php-pecl-pcs.x86_64                 1.3.7-1.el7.remi                      remi-safe
php74-php-pecl-pcs-devel.x86_64           1.3.7-1.el7.remi                      remi-safe
php74-php-pecl-pcsc.x86_64                0.5.1-1.el7.remi                      remi-safe
php74-php-pecl-pcsc-devel.x86_64          0.5.1-1.el7.remi                      remi-safe
php74-php-pecl-pdflib.x86_64              4.1.3-1.el7.remi                      remi-safe
php74-php-pecl-pkcs11.x86_64              1.1.2-1.el7.remi                      remi-safe
php74-php-pecl-pq.x86_64                  2.2.3-1.el7.remi                      remi-safe
php74-php-pecl-propro.x86_64              2.1.0-5.el7.remi                      remi-safe
php74-php-pecl-propro-devel.x86_64        2.1.0-5.el7.remi                      remi-safe
php74-php-pecl-protobuf.x86_64            3.24.4-1.el7.remi                     remi-safe
php74-php-pecl-ps.x86_64                  1.4.4-1.el7.remi                      remi-safe
php74-php-pecl-psr.x86_64                 1.2.0-1.el7.remi                      remi-safe
php74-php-pecl-psr-devel.x86_64           1.2.0-1.el7.remi                      remi-safe
php74-php-pecl-quickhash.x86_64           1.1.1-1.el7.remi                      remi-safe
php74-php-pecl-radius.x86_64              1.4.0-0.7.b1.el7.remi                 remi-safe
php74-php-pecl-raphf.x86_64               2.0.1-1.el7.remi                      remi-safe
php74-php-pecl-raphf-devel.x86_64         2.0.1-1.el7.remi                      remi-safe
php74-php-pecl-rar.x86_64                 4.2.0-1.el7.remi                      remi-safe
php74-php-pecl-rdkafka.x86_64             3.1.2-2.el7.remi                      remi-safe
php74-php-pecl-rdkafka4.x86_64            4.1.2-1.el7.remi                      remi-safe
php74-php-pecl-rdkafka5.x86_64            5.0.2-1.el7.remi                      remi-safe
php74-php-pecl-rdkafka6.x86_64            6.0.3-2.el7.remi                      remi-safe
php74-php-pecl-recode.x86_64              1.0.0~DEV.20190723-6.el7.remi         remi-safe
php74-php-pecl-redis4.x86_64              4.3.0-4.el7.remi                      remi-safe
php74-php-pecl-redis5.x86_64              5.3.7-1.el7.remi                      remi-safe
php74-php-pecl-redis6.x86_64              6.0.2-1.el7.remi                      remi-safe
php74-php-pecl-request.x86_64             2.0.0-1.el7.remi                      remi-safe
php74-php-pecl-rpminfo.x86_64             0.6.0-1.el7.remi                      remi-safe
php74-php-pecl-rrd.x86_64                 2.0.3-1.el7.remi                      remi-safe
php74-php-pecl-runkit7.x86_64             4.0.0~a6-1.el7.remi                   remi-safe
php74-php-pecl-scoutapm.x86_64            1.10.0-1.el7.remi                     remi-safe
php74-php-pecl-scrypt.x86_64              2.0.1-1.el7.remi                      remi-safe
php74-php-pecl-sdl.x86_64                 2.4.0-1.el7.remi                      remi-safe
php74-php-pecl-sdl-ttf.x86_64             0.3.0-1.el7.remi                      remi-safe
php74-php-pecl-seasclick.x86_64           0.1.1-1.el7.remi                      remi-safe
php74-php-pecl-seaslog.x86_64             2.2.0-1.el7.remi                      remi-safe
php74-php-pecl-seassnowflake.x86_64       1.0.0-1.el7.remi                      remi-safe
php74-php-pecl-selinux.x86_64             0.6.0-1.el7.remi                      remi-safe
php74-php-pecl-simdjson.x86_64            4.0.0-1.el7.remi                      remi-safe
php74-php-pecl-simple-kafka-client.x86_64 0.1.4-4.el7.remi                      remi-safe
php74-php-pecl-skywalking.x86_64          5.0.1-1.el7.remi                      remi-safe
php74-php-pecl-solr2.x86_64               2.7.0-1.el7.remi                      remi-safe
php74-php-pecl-ssdeep.x86_64              1.1.0-5.el7.remi                      remi-safe
php74-php-pecl-ssh2.x86_64                1.4.1-1.el7.remi                      remi-safe
php74-php-pecl-stats.x86_64               2.0.3-10.el7.remi                     remi-safe
php74-php-pecl-stomp.x86_64               2.0.3-1.el7.remi                      remi-safe
php74-php-pecl-svm.x86_64                 0.2.3-2.el7.remi                      remi-safe
php74-php-pecl-svn.x86_64                 2.0.3-1.el7.remi                      remi-safe
php74-php-pecl-swoole4.x86_64             4.8.13-1.el7.remi                     remi-safe
php74-php-pecl-sync.x86_64                1.1.3-1.el7.remi                      remi-safe
php74-php-pecl-taint.x86_64               2.1.0-1.el7.remi                      remi-safe
php74-php-pecl-tcpwrap.x86_64             1.2.0-5.el7.remi                      remi-safe
php74-php-pecl-tensor.x86_64              3.0.5-2.el7.remi                      remi-safe
php74-php-pecl-termbox.x86_64             0.1.3-8.el7.remi                      remi-safe
php74-php-pecl-timecop.x86_64             1.2.10-9.el7.remi                     remi-safe
php74-php-pecl-trader.x86_64              0.5.1-1.el7.remi                      remi-safe
php74-php-pecl-translit.x86_64            0.7.1-1.el7.remi                      remi-safe
php74-php-pecl-trie.x86_64                0.1.2-1.el7.remi                      remi-safe
php74-php-pecl-uopz.x86_64                6.1.2-1.el7.remi                      remi-safe
php74-php-pecl-uploadprogress.x86_64      2.0.2-1.el7.remi                      remi-safe
php74-php-pecl-uuid.x86_64                1.2.0-1.el7.remi                      remi-safe
php74-php-pecl-uv.x86_64                  0.2.4-3.el7.remi                      remi-safe
php74-php-pecl-var-representation.x86_64  0.1.4-1.el7.remi                      remi-safe
php74-php-pecl-varnish.x86_64             1.2.6-1.el7.remi                      remi-safe
php74-php-pecl-vips.x86_64                1.0.13-2.el7.remi                     remi-safe
php74-php-pecl-vld.x86_64                 0.18.0-1.el7.remi                     remi-safe
php74-php-pecl-wddx.x86_64                1.0.0~DEV.20190320-6.el7.remi         remi-safe
php74-php-pecl-weakreference-bc.x86_64    0.4.1-1.el7.remi                      remi-safe
php74-php-pecl-xattr.x86_64               1.4.0-1.el7.remi                      remi-safe
php74-php-pecl-xdebug.x86_64              2.9.8-1.el7.remi                      remi-safe
php74-php-pecl-xdebug3.x86_64             3.1.6-1.el7.remi                      remi-safe
php74-php-pecl-xdiff.x86_64               2.1.1-1.el7.remi                      remi-safe
php74-php-pecl-xhprof.x86_64              2.3.9-1.el7.remi                      remi-safe
php74-php-pecl-xlswriter.x86_64           1.5.5-1.el7.remi                      remi-safe
php74-php-pecl-xmldiff.x86_64             1.1.3-1.el7.remi                      remi-safe
php74-php-pecl-xmldiff-devel.x86_64       1.1.3-1.el7.remi                      remi-safe
php74-php-pecl-xxtea.x86_64               1.0.11-8.el7.remi                     remi-safe
php74-php-pecl-yac.x86_64                 2.3.1-5.el7.remi                      remi-safe
php74-php-pecl-yaconf.x86_64              1.1.2-1.el7.remi                      remi-safe
php74-php-pecl-yaconf-devel.x86_64        1.1.2-1.el7.remi                      remi-safe
php74-php-pecl-yaf.x86_64                 3.3.5-1.el7.remi                      remi-safe
php74-php-pecl-yaml.x86_64                2.2.3-1.el7.remi                      remi-safe
php74-php-pecl-yar.x86_64                 2.3.2-1.el7.remi                      remi-safe
php74-php-pecl-yaz.x86_64                 1.2.4-1.el7.remi                      remi-safe
php74-php-pecl-zip.x86_64                 1.22.3-1.el7.remi                     remi-safe
php74-php-pecl-zmq.x86_64                 1.1.3-10.el7.remi                     remi-safe
php74-php-pggi.x86_64                     0.4.1-1.el7.remi                      remi-safe
php74-php-pgsql.x86_64                    7.4.33-15.el7.remi                    remi-safe
php74-php-phalcon4.x86_64                 4.1.2-1.el7.remi                      remi-safe
php74-php-phalcon5.x86_64                 5.4.0-1.el7.remi                      remi-safe
php74-php-phpiredis.x86_64                1.0.1-1.el7.remi                      remi-safe
php74-php-pinba.x86_64                    1.1.2-1.el7.remi                      remi-safe
php74-php-process.x86_64                  7.4.33-15.el7.remi                    remi-safe
php74-php-pspell.x86_64                   7.4.33-15.el7.remi                    remi-safe
php74-php-realpath-turbo.x86_64           2.0.0-1.el7.remi                      remi-safe
php74-php-smbclient.x86_64                1.1.1-1.el7.remi                      remi-safe
php74-php-snappy.x86_64                   0.2.1-1.el7.remi                      remi-safe
php74-php-snmp.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-snuffleupagus.x86_64            0.10.0-1.el7.remi                     remi-safe
php74-php-soap.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-sodium.x86_64                   7.4.33-15.el7.remi                    remi-safe
php74-php-sqlsrv.x86_64                   5.10.1-1.el7.remi                     remi-safe
php74-php-tidy.x86_64                     7.4.33-15.el7.remi                    remi-safe
php74-php-wkhtmltox.x86_64                0.3.2-0.5.20171112.fecc9af.el7.remi   remi-safe
php74-php-xml.x86_64                      7.4.33-15.el7.remi                    remi-safe
php74-php-xmlrpc.x86_64                   7.4.33-15.el7.remi                    remi-safe
php74-php-xz.x86_64                       1.1.2-1.el7.remi                      remi-safe
php74-php-zephir-parser.x86_64            1.6.1-1.el7.remi                      remi-safe
php74-php-zephir-parser-devel.x86_64      1.6.1-1.el7.remi                      remi-safe
php74-php-zstd.x86_64                     0.13.3-1.el7.remi                     remi-safe
php74-php-zstd-devel.x86_64               0.13.3-1.el7.remi                     remi-safe
php74-runtime.x86_64                      7.4-3.el7.remi                        remi-safe
php74-scldevel.x86_64                     7.4-3.el7.remi                        remi-safe
php74-syspaths.x86_64                     7.4-3.el7.remi                        remi-safe
php74-unit-php.x86_64                     1.32.1-1.el7.remi                     remi-safe
php74-uwsgi-plugin-php.x86_64             2.0.18-8.el7.remi                     remi-safe
php74-xhprof.noarch                       2.3.9-1.el7.remi                      remi-safe
php74-zephir.noarch                       0.10.16-1.el7.remi                    remi-safe
[root@localhost ~]#
```

安装PHP7.4
```
yum --enablerepo=remi-php74 install -y php
```

详细：
```
[root@localhost ~]# yum --enablerepo=remi-php74 install -y php
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-php74: mirrors.tuna.tsinghua.edu.cn
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
remi-php74                                                        | 3.0 kB  00:00:00
remi-php74/primary_db                                             | 268 kB  00:00:00
正在解决依赖关系
--> 正在检查事务
---> 软件包 php.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 httpd-mmn = 20120211x8664，它被软件包 php-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 php-cli(x86-64) = 7.4.33-15.el7.remi，它被软件包 php-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 php-common(x86-64) = 7.4.33-15.el7.remi，它被软件包 php-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 php-sodium(x86-64) = 7.4.33-15.el7.remi，它被软件包 php-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 httpd，它被软件包 php-7.4.33-15.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 httpd.x86_64.0.2.4.6-99.el7.centos.1 将被 安装
--> 正在处理依赖关系 httpd-tools = 2.4.6-99.el7.centos.1，它被软件包 httpd-2.4.6-99.el7.centos.1.x86_64 需要
--> 正在处理依赖关系 /etc/mime.types，它被软件包 httpd-2.4.6-99.el7.centos.1.x86_64 需要
--> 正在处理依赖关系 libaprutil-1.so.0()(64bit)，它被软件包 httpd-2.4.6-99.el7.centos.1.x86_64 需要
--> 正在处理依赖关系 libapr-1.so.0()(64bit)，它被软件包 httpd-2.4.6-99.el7.centos.1.x86_64 需要
---> 软件包 php-cli.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-common.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 php-json(x86-64) = 7.4.33-15.el7.remi，它被软件包 php-common-7.4.33-15.el7.remi.x86_64 需要
---> 软件包 php-sodium.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 libsodium.so.23()(64bit)，它被软件包 php-sodium-7.4.33-15.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 apr.x86_64.0.1.4.8-7.el7 将被 安装
---> 软件包 apr-util.x86_64.0.1.5.2-6.el7_9.1 将被 安装
---> 软件包 httpd-tools.x86_64.0.2.4.6-99.el7.centos.1 将被 安装
---> 软件包 libsodium.x86_64.0.1.0.18-1.el7 将被 安装
---> 软件包 mailcap.noarch.0.2.1.41-2.el7 将被 安装
---> 软件包 php-json.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 解决依赖关系完成

依赖关系解决

=========================================================================================
 Package            架构          版本                           源                 大小
=========================================================================================
正在安装:
 php                x86_64        7.4.33-15.el7.remi             remi-php74        3.4 M
为依赖而安装:
 apr                x86_64        1.4.8-7.el7                    base              104 k
 apr-util           x86_64        1.5.2-6.el7_9.1                updates            92 k
 httpd              x86_64        2.4.6-99.el7.centos.1          updates           2.7 M
 httpd-tools        x86_64        2.4.6-99.el7.centos.1          updates            94 k
 libsodium          x86_64        1.0.18-1.el7                   epel              147 k
 mailcap            noarch        2.1.41-2.el7                   base               31 k
 php-cli            x86_64        7.4.33-15.el7.remi             remi-php74        5.2 M
 php-common         x86_64        7.4.33-15.el7.remi             remi-php74        1.2 M
 php-json           x86_64        7.4.33-15.el7.remi             remi-php74         77 k
 php-sodium         x86_64        7.4.33-15.el7.remi             remi-php74         88 k

事务概要
=========================================================================================
安装  1 软件包 (+10 依赖软件包)

总下载量：13 M
安装大小：52 M
Downloading packages:
(1/11): apr-1.4.8-7.el7.x86_64.rpm                                | 104 kB  00:00:00
(2/11): apr-util-1.5.2-6.el7_9.1.x86_64.rpm                       |  92 kB  00:00:00
(3/11): httpd-tools-2.4.6-99.el7.centos.1.x86_64.rpm              |  94 kB  00:00:00
(4/11): mailcap-2.1.41-2.el7.noarch.rpm                           |  31 kB  00:00:00
warning: /var/cache/yum/x86_64/7/epel/packages/libsodium-1.0.18-1.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 352c64e5: NOKEY
libsodium-1.0.18-1.el7.x86_64.rpm 的公钥尚未安装
(5/11): libsodium-1.0.18-1.el7.x86_64.rpm                         | 147 kB  00:00:02
(6/11): httpd-2.4.6-99.el7.centos.1.x86_64.rpm                    | 2.7 MB  00:00:03
warning: /var/cache/yum/x86_64/7/remi-php74/packages/php-sodium-7.4.33-15.el7.remi.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 00f97f56: NOKEY
php-sodium-7.4.33-15.el7.remi.x86_64.rpm 的公钥尚未安装
(7/11): php-sodium-7.4.33-15.el7.remi.x86_64.rpm                  |  88 kB  00:00:00
(8/11): php-json-7.4.33-15.el7.remi.x86_64.rpm                    |  77 kB  00:00:01
(9/11): php-common-7.4.33-15.el7.remi.x86_64.rpm                  | 1.2 MB  00:00:10
(10/11): php-cli-7.4.33-15.el7.remi.x86_64.rpm                    | 5.2 MB  00:00:14
(11/11): php-7.4.33-15.el7.remi.x86_64.rpm                        | 3.4 MB  00:00:16
-----------------------------------------------------------------------------------------
总计                                                        786 kB/s |  13 MB  00:17
从 file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 检索密钥
导入 GPG key 0x352C64E5:
 用户ID     : "Fedora EPEL (7) <epel@fedoraproject.org>"
 指纹       : 91e9 7d7c 4a5e 96f1 7f3e 888f 6a2f aea2 352c 64e5
 软件包     : epel-release-7-11.noarch (@extras)
 来自       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
从 file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi 检索密钥
导入 GPG key 0x00F97F56:
 用户ID     : "Remi Collet <RPMS@FamilleCollet.com>"
 指纹       : 1ee0 4cce 88a4 ae4a a29a 5df5 004e 6f47 00f9 7f56
 软件包     : remi-release-7.9-6.el7.remi.noarch (installed)
 来自       : /etc/pki/rpm-gpg/RPM-GPG-KEY-remi
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告：RPM 数据库已被非 yum 程序修改。
  正在安装    : php-json-7.4.33-15.el7.remi.x86_64                                  1/11
  正在安装    : php-common-7.4.33-15.el7.remi.x86_64                                2/11
  正在安装    : apr-1.4.8-7.el7.x86_64                                              3/11
  正在安装    : apr-util-1.5.2-6.el7_9.1.x86_64                                     4/11
  正在安装    : httpd-tools-2.4.6-99.el7.centos.1.x86_64                            5/11
  正在安装    : php-cli-7.4.33-15.el7.remi.x86_64                                   6/11
  正在安装    : mailcap-2.1.41-2.el7.noarch                                         7/11
  正在安装    : httpd-2.4.6-99.el7.centos.1.x86_64                                  8/11
  正在安装    : libsodium-1.0.18-1.el7.x86_64                                       9/11
  正在安装    : php-sodium-7.4.33-15.el7.remi.x86_64                               10/11
  正在安装    : php-7.4.33-15.el7.remi.x86_64                                      11/11
=====================================================================

  WARNING : PHP 7.4 have reached its "End of Life" in
  November 2022. Even, if this package includes some of
  the important security fixes, backported from 8.1, the
  UPGRADE to a maintained version is very strongly RECOMMENDED.

=====================================================================
  验证中      : libsodium-1.0.18-1.el7.x86_64                                       1/11
  验证中      : php-cli-7.4.33-15.el7.remi.x86_64                                   2/11
  验证中      : httpd-tools-2.4.6-99.el7.centos.1.x86_64                            3/11
  验证中      : php-7.4.33-15.el7.remi.x86_64                                       4/11
  验证中      : mailcap-2.1.41-2.el7.noarch                                         5/11
  验证中      : apr-1.4.8-7.el7.x86_64                                              6/11
  验证中      : php-common-7.4.33-15.el7.remi.x86_64                                7/11
  验证中      : php-json-7.4.33-15.el7.remi.x86_64                                  8/11
  验证中      : httpd-2.4.6-99.el7.centos.1.x86_64                                  9/11
  验证中      : php-sodium-7.4.33-15.el7.remi.x86_64                               10/11
  验证中      : apr-util-1.5.2-6.el7_9.1.x86_64                                    11/11

已安装:
  php.x86_64 0:7.4.33-15.el7.remi

作为依赖被安装:
  apr.x86_64 0:1.4.8-7.el7                 apr-util.x86_64 0:1.5.2-6.el7_9.1
  httpd.x86_64 0:2.4.6-99.el7.centos.1     httpd-tools.x86_64 0:2.4.6-99.el7.centos.1
  libsodium.x86_64 0:1.0.18-1.el7          mailcap.noarch 0:2.1.41-2.el7
  php-cli.x86_64 0:7.4.33-15.el7.remi      php-common.x86_64 0:7.4.33-15.el7.remi
  php-json.x86_64 0:7.4.33-15.el7.remi     php-sodium.x86_64 0:7.4.33-15.el7.remi

完毕！
[root@localhost ~]#
[root@localhost ~]# php -v
PHP 7.4.33 (cli) (built: Jun  5 2024 05:05:14) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
[root@localhost ~]#
[root@localhost ~]# php -m
[PHP Modules]
bz2
calendar
Core
ctype
curl
date
exif
fileinfo
filter
ftp
gettext
hash
iconv
json
libxml
openssl
pcntl
pcre
Phar
readline
Reflection
session
sockets
sodium
SPL
standard
tokenizer
zlib

[Zend Modules]

[root@localhost ~]#
```

### PHP拓展安装

安装拓展
```
yum --enablerepo=remi-php74 install -y php-fpm php-cli php-bcmath php-gd php-json php-mbstring php-mcrypt php-opcache php-mysqlnd php-pdo php-pecl-mcrypt php-xml php-zip
```

详细：
```
[root@localhost ~]# yum --enablerepo=remi-php74 install -y php-fpm php-cli php-bcmath php-gd php-json php-mbstring php-mcrypt php-opcache php-mysqlnd php-pdo php-pecl-mcrypt php-xml php-zip
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-php74: ftp.riken.jp
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
软件包 php-cli-7.4.33-15.el7.remi.x86_64 已安装并且是最新版本
软件包 php-json-7.4.33-15.el7.remi.x86_64 已安装并且是最新版本
软件包 php-mcrypt 已经被 php-pecl-mcrypt 取代，改为尝试安装 php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64
正在解决依赖关系
--> 正在检查事务
---> 软件包 php-bcmath.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-fpm.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-gd.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 libgd.so.103()(64bit)，它被软件包 php-gd-7.4.33-15.el7.remi.x86_64 需要
---> 软件包 php-mbstring.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 libonig.so.105()(64bit)，它被软件包 php-mbstring-7.4.33-15.el7.remi.x86_64 需要
---> 软件包 php-mysqlnd.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-opcache.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-pdo.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php-pecl-mcrypt.x86_64.0.1.0.7-1.el7.remi.7.4 将被 安装
--> 正在处理依赖关系 libmcrypt.so.4()(64bit)，它被软件包 php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64 需要
---> 软件包 php-pecl-zip.x86_64.0.1.22.3-1.el7.remi.7.4 将被 安装
--> 正在处理依赖关系 libzip5(x86-64) >= 1.10.1，它被软件包 php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64 需要
--> 正在处理依赖关系 libzip.so.5()(64bit)，它被软件包 php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64 需要
---> 软件包 php-xml.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 libxslt.so.1(LIBXML2_1.0.11)(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libxslt.so.1(LIBXML2_1.0.13)(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libxslt.so.1(LIBXML2_1.0.18)(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libxslt.so.1(LIBXML2_1.0.22)(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libxslt.so.1(LIBXML2_1.0.24)(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libexslt.so.0()(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在处理依赖关系 libxslt.so.1()(64bit)，它被软件包 php-xml-7.4.33-15.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 gd3php.x86_64.0.2.3.3-7.el7.remi 将被 安装
--> 正在处理依赖关系 libjpeg.so.62(LIBJPEG_6.2)(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libtiff.so.5(LIBTIFF_4.0)(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libXpm.so.4()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libfontconfig.so.1()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libjpeg.so.62()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libraqm.so.0()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libtiff.so.5()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
--> 正在处理依赖关系 libwebp.so.7()(64bit)，它被软件包 gd3php-2.3.3-7.el7.remi.x86_64 需要
---> 软件包 libmcrypt.x86_64.0.2.5.8-13.el7 将被 安装
---> 软件包 libxslt.x86_64.0.1.1.28-6.el7 将被 安装
---> 软件包 libzip5.x86_64.0.1.10.1-1.el7.remi 将被 安装
--> 正在处理依赖关系 libzstd(x86-64) >= 1.3.6，它被软件包 libzip5-1.10.1-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libzstd.so.1()(64bit)，它被软件包 libzip5-1.10.1-1.el7.remi.x86_64 需要
---> 软件包 oniguruma5php.x86_64.0.6.9.9-1.el7.remi 将被 安装
--> 正在检查事务
---> 软件包 fontconfig.x86_64.0.2.13.0-4.3.el7 将被 安装
--> 正在处理依赖关系 fontpackages-filesystem，它被软件包 fontconfig-2.13.0-4.3.el7.x86_64 需要
--> 正在处理依赖关系 dejavu-sans-fonts，它被软件包 fontconfig-2.13.0-4.3.el7.x86_64 需要
---> 软件包 libXpm.x86_64.0.3.5.12-2.el7_9 将被 安装
---> 软件包 libjpeg-turbo.x86_64.0.1.2.90-8.el7 将被 安装
---> 软件包 libraqm.x86_64.0.0.7.0-4.el7 将被 安装
--> 正在处理依赖关系 libfribidi.so.0()(64bit)，它被软件包 libraqm-0.7.0-4.el7.x86_64 需要
--> 正在处理依赖关系 libharfbuzz.so.0()(64bit)，它被软件包 libraqm-0.7.0-4.el7.x86_64 需要
---> 软件包 libtiff.x86_64.0.4.0.3-35.el7 将被 安装
--> 正在处理依赖关系 libjbig.so.2.0()(64bit)，它被软件包 libtiff-4.0.3-35.el7.x86_64 需要
---> 软件包 libwebp7.x86_64.0.1.0.3-2.el7.remi 将被 安装
---> 软件包 libzstd.x86_64.0.1.5.5-1.el7 将被 安装
--> 正在检查事务
---> 软件包 dejavu-sans-fonts.noarch.0.2.33-6.el7 将被 安装
--> 正在处理依赖关系 dejavu-fonts-common = 2.33-6.el7，它被软件包 dejavu-sans-fonts-2.33-6.el7.noarch 需要
---> 软件包 fontpackages-filesystem.noarch.0.1.44-8.el7 将被 安装
---> 软件包 fribidi.x86_64.0.1.0.2-1.el7_7.1 将被 安装
---> 软件包 harfbuzz.x86_64.0.1.7.5-2.el7 将被 安装
--> 正在处理依赖关系 libgraphite2.so.3()(64bit)，它被软件包 harfbuzz-1.7.5-2.el7.x86_64 需要
---> 软件包 jbigkit-libs.x86_64.0.2.0-11.el7 将被 安装
--> 正在检查事务
---> 软件包 dejavu-fonts-common.noarch.0.2.33-6.el7 将被 安装
---> 软件包 graphite2.x86_64.0.1.3.10-1.el7_3 将被 安装
--> 解决依赖关系完成

依赖关系解决

=========================================================================================
 Package                     架构       版本                        源              大小
=========================================================================================
正在安装:
 php-bcmath                  x86_64     7.4.33-15.el7.remi          remi-php74      83 k
 php-fpm                     x86_64     7.4.33-15.el7.remi          remi-php74     1.8 M
 php-gd                      x86_64     7.4.33-15.el7.remi          remi-php74      92 k
 php-mbstring                x86_64     7.4.33-15.el7.remi          remi-php74     528 k
 php-mysqlnd                 x86_64     7.4.33-15.el7.remi          remi-php74     258 k
 php-opcache                 x86_64     7.4.33-15.el7.remi          remi-php74     335 k
 php-pdo                     x86_64     7.4.33-15.el7.remi          remi-php74     142 k
 php-pecl-mcrypt             x86_64     1.0.7-1.el7.remi.7.4        remi-php74      31 k
 php-pecl-zip                x86_64     1.22.3-1.el7.remi.7.4       remi-php74      67 k
 php-xml                     x86_64     7.4.33-15.el7.remi          remi-php74     214 k
为依赖而安装:
 dejavu-fonts-common         noarch     2.33-6.el7                  base            64 k
 dejavu-sans-fonts           noarch     2.33-6.el7                  base           1.4 M
 fontconfig                  x86_64     2.13.0-4.3.el7              base           254 k
 fontpackages-filesystem     noarch     1.44-8.el7                  base           9.9 k
 fribidi                     x86_64     1.0.2-1.el7_7.1             base            79 k
 gd3php                      x86_64     2.3.3-7.el7.remi            remi-safe      138 k
 graphite2                   x86_64     1.3.10-1.el7_3              base           115 k
 harfbuzz                    x86_64     1.7.5-2.el7                 base           267 k
 jbigkit-libs                x86_64     2.0-11.el7                  base            46 k
 libXpm                      x86_64     3.5.12-2.el7_9              updates         56 k
 libjpeg-turbo               x86_64     1.2.90-8.el7                base           135 k
 libmcrypt                   x86_64     2.5.8-13.el7                epel            99 k
 libraqm                     x86_64     0.7.0-4.el7                 epel            15 k
 libtiff                     x86_64     4.0.3-35.el7                base           172 k
 libwebp7                    x86_64     1.0.3-2.el7.remi            remi-safe      266 k
 libxslt                     x86_64     1.1.28-6.el7                base           242 k
 libzip5                     x86_64     1.10.1-1.el7.remi           remi-safe       67 k
 libzstd                     x86_64     1.5.5-1.el7                 epel           292 k
 oniguruma5php               x86_64     6.9.9-1.el7.remi            remi-safe      209 k

事务概要
=========================================================================================
安装  10 软件包 (+19 依赖软件包)

总下载量：7.4 M
安装大小：24 M
Downloading packages:
(1/29): dejavu-fonts-common-2.33-6.el7.noarch.rpm                 |  64 kB  00:00:00
(2/29): fontconfig-2.13.0-4.3.el7.x86_64.rpm                      | 254 kB  00:00:00
(3/29): fontpackages-filesystem-1.44-8.el7.noarch.rpm             | 9.9 kB  00:00:00
(4/29): fribidi-1.0.2-1.el7_7.1.x86_64.rpm                        |  79 kB  00:00:00
(5/29): graphite2-1.3.10-1.el7_3.x86_64.rpm                       | 115 kB  00:00:00
(6/29): harfbuzz-1.7.5-2.el7.x86_64.rpm                           | 267 kB  00:00:00
(7/29): jbigkit-libs-2.0-11.el7.x86_64.rpm                        |  46 kB  00:00:00
(8/29): gd3php-2.3.3-7.el7.remi.x86_64.rpm                        | 138 kB  00:00:01
(9/29): libXpm-3.5.12-2.el7_9.x86_64.rpm                          |  56 kB  00:00:00
(10/29): libjpeg-turbo-1.2.90-8.el7.x86_64.rpm                    | 135 kB  00:00:00
(11/29): dejavu-sans-fonts-2.33-6.el7.noarch.rpm                  | 1.4 MB  00:00:02
(12/29): libtiff-4.0.3-35.el7.x86_64.rpm                          | 172 kB  00:00:00
(13/29): libxslt-1.1.28-6.el7.x86_64.rpm                          | 242 kB  00:00:00
(14/29): libraqm-0.7.0-4.el7.x86_64.rpm                           |  15 kB  00:00:01
(15/29): libwebp7-1.0.3-2.el7.remi.x86_64.rpm                     | 266 kB  00:00:01
(16/29): php-bcmath-7.4.33-15.el7.remi.x86_64.rpm                 |  83 kB  00:00:00
(17/29): libmcrypt-2.5.8-13.el7.x86_64.rpm                        |  99 kB  00:00:04
(18/29): oniguruma5php-6.9.9-1.el7.remi.x86_64.rpm                | 209 kB  00:00:03
(19/29): php-mbstring-7.4.33-15.el7.remi.x86_64.rpm               | 528 kB  00:00:01
(20/29): php-mysqlnd-7.4.33-15.el7.remi.x86_64.rpm                | 258 kB  00:00:00
(21/29): php-opcache-7.4.33-15.el7.remi.x86_64.rpm                | 335 kB  00:00:00
(22/29): php-pdo-7.4.33-15.el7.remi.x86_64.rpm                    | 142 kB  00:00:00
(23/29): php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64.rpm          |  31 kB  00:00:00
(24/29): php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64.rpm            |  67 kB  00:00:00
(25/29): php-xml-7.4.33-15.el7.remi.x86_64.rpm                    | 214 kB  00:00:00
(26/29): libzstd-1.5.5-1.el7.x86_64.rpm                           | 292 kB  00:00:14
(27/29): php-fpm-7.4.33-15.el7.remi.x86_64.rpm                    | 1.8 MB  00:00:13
php-gd-7.4.33-15.el7.remi.x86_ FAILED                                          00:47 ETA
https://cdn.centos.no/remi/enterprise/7/php74/x86_64/php-gd-7.4.33-15.el7.remi.x86_64.rpm: [Errno 12] Timeout on https://cdn.centos.no/remi/enterprise/7/php74/x86_64/php-gd-7.4.33-15.el7.remi.x86_64.rpm: (28, 'Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds')
正在尝试其它镜像。
(28/29): php-gd-7.4.33-15.el7.remi.x86_64.rpm                     |  92 kB  00:00:01
libzip5-1.10.1-1.el7.remi.x86_ FAILED                                          01:52 ETA
https://cdn.centos.no/remi/enterprise/7/safe/x86_64/libzip5-1.10.1-1.el7.remi.x86_64.rpm: [Errno 12] Timeout on https://cdn.centos.no/remi/enterprise/7/safe/x86_64/libzip5-1.10.1-1.el7.remi.x86_64.rpm: (28, 'Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds')
正在尝试其它镜像。
(29/29): libzip5-1.10.1-1.el7.remi.x86_64.rpm                     |  67 kB  00:00:01
-----------------------------------------------------------------------------------------
总计                                                        121 kB/s | 7.4 MB  01:02
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : fontpackages-filesystem-1.44-8.el7.noarch                           1/29
  正在安装    : libjpeg-turbo-1.2.90-8.el7.x86_64                                   2/29
  正在安装    : dejavu-fonts-common-2.33-6.el7.noarch                               3/29
  正在安装    : dejavu-sans-fonts-2.33-6.el7.noarch                                 4/29
  正在安装    : fontconfig-2.13.0-4.3.el7.x86_64                                    5/29
  正在安装    : oniguruma5php-6.9.9-1.el7.remi.x86_64                               6/29
  正在安装    : libwebp7-1.0.3-2.el7.remi.x86_64                                    7/29
  正在安装    : graphite2-1.3.10-1.el7_3.x86_64                                     8/29
  正在安装    : harfbuzz-1.7.5-2.el7.x86_64                                         9/29
  正在安装    : libxslt-1.1.28-6.el7.x86_64                                        10/29
  正在安装    : jbigkit-libs-2.0-11.el7.x86_64                                     11/29
  正在安装    : libtiff-4.0.3-35.el7.x86_64                                        12/29
  正在安装    : php-pdo-7.4.33-15.el7.remi.x86_64                                  13/29
  正在安装    : fribidi-1.0.2-1.el7_7.1.x86_64                                     14/29
  正在安装    : libraqm-0.7.0-4.el7.x86_64                                         15/29
  正在安装    : libzstd-1.5.5-1.el7.x86_64                                         16/29
  正在安装    : libzip5-1.10.1-1.el7.remi.x86_64                                   17/29
  正在安装    : libmcrypt-2.5.8-13.el7.x86_64                                      18/29
  正在安装    : libXpm-3.5.12-2.el7_9.x86_64                                       19/29
  正在安装    : gd3php-2.3.3-7.el7.remi.x86_64                                     20/29
  正在安装    : php-gd-7.4.33-15.el7.remi.x86_64                                   21/29
  正在安装    : php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64                        22/29
  正在安装    : php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64                          23/29
  正在安装    : php-mysqlnd-7.4.33-15.el7.remi.x86_64                              24/29
  正在安装    : php-xml-7.4.33-15.el7.remi.x86_64                                  25/29
  正在安装    : php-mbstring-7.4.33-15.el7.remi.x86_64                             26/29
  正在安装    : php-bcmath-7.4.33-15.el7.remi.x86_64                               27/29
  正在安装    : php-fpm-7.4.33-15.el7.remi.x86_64                                  28/29
  正在安装    : php-opcache-7.4.33-15.el7.remi.x86_64                              29/29
  验证中      : php-opcache-7.4.33-15.el7.remi.x86_64                               1/29
  验证中      : fontconfig-2.13.0-4.3.el7.x86_64                                    2/29
  验证中      : php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64                         3/29
  验证中      : libjpeg-turbo-1.2.90-8.el7.x86_64                                   4/29
  验证中      : libzip5-1.10.1-1.el7.remi.x86_64                                    5/29
  验证中      : libXpm-3.5.12-2.el7_9.x86_64                                        6/29
  验证中      : libmcrypt-2.5.8-13.el7.x86_64                                       7/29
  验证中      : fontpackages-filesystem-1.44-8.el7.noarch                           8/29
  验证中      : libzstd-1.5.5-1.el7.x86_64                                          9/29
  验证中      : dejavu-fonts-common-2.33-6.el7.noarch                              10/29
  验证中      : php-xml-7.4.33-15.el7.remi.x86_64                                  11/29
  验证中      : fribidi-1.0.2-1.el7_7.1.x86_64                                     12/29
  验证中      : php-fpm-7.4.33-15.el7.remi.x86_64                                  13/29
  验证中      : php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64                          14/29
  验证中      : php-bcmath-7.4.33-15.el7.remi.x86_64                               15/29
  验证中      : gd3php-2.3.3-7.el7.remi.x86_64                                     16/29
  验证中      : php-pdo-7.4.33-15.el7.remi.x86_64                                  17/29
  验证中      : harfbuzz-1.7.5-2.el7.x86_64                                        18/29
  验证中      : dejavu-sans-fonts-2.33-6.el7.noarch                                19/29
  验证中      : jbigkit-libs-2.0-11.el7.x86_64                                     20/29
  验证中      : libraqm-0.7.0-4.el7.x86_64                                         21/29
  验证中      : php-gd-7.4.33-15.el7.remi.x86_64                                   22/29
  验证中      : php-mbstring-7.4.33-15.el7.remi.x86_64                             23/29
  验证中      : libxslt-1.1.28-6.el7.x86_64                                        24/29
  验证中      : graphite2-1.3.10-1.el7_3.x86_64                                    25/29
  验证中      : php-mysqlnd-7.4.33-15.el7.remi.x86_64                              26/29
  验证中      : libwebp7-1.0.3-2.el7.remi.x86_64                                   27/29
  验证中      : libtiff-4.0.3-35.el7.x86_64                                        28/29
  验证中      : oniguruma5php-6.9.9-1.el7.remi.x86_64                              29/29

已安装:
  php-bcmath.x86_64 0:7.4.33-15.el7.remi
  php-fpm.x86_64 0:7.4.33-15.el7.remi
  php-gd.x86_64 0:7.4.33-15.el7.remi
  php-mbstring.x86_64 0:7.4.33-15.el7.remi
  php-mysqlnd.x86_64 0:7.4.33-15.el7.remi
  php-opcache.x86_64 0:7.4.33-15.el7.remi
  php-pdo.x86_64 0:7.4.33-15.el7.remi
  php-pecl-mcrypt.x86_64 0:1.0.7-1.el7.remi.7.4
  php-pecl-zip.x86_64 0:1.22.3-1.el7.remi.7.4
  php-xml.x86_64 0:7.4.33-15.el7.remi

作为依赖被安装:
  dejavu-fonts-common.noarch 0:2.33-6.el7   dejavu-sans-fonts.noarch 0:2.33-6.el7
  fontconfig.x86_64 0:2.13.0-4.3.el7        fontpackages-filesystem.noarch 0:1.44-8.el7
  fribidi.x86_64 0:1.0.2-1.el7_7.1          gd3php.x86_64 0:2.3.3-7.el7.remi
  graphite2.x86_64 0:1.3.10-1.el7_3         harfbuzz.x86_64 0:1.7.5-2.el7
  jbigkit-libs.x86_64 0:2.0-11.el7          libXpm.x86_64 0:3.5.12-2.el7_9
  libjpeg-turbo.x86_64 0:1.2.90-8.el7       libmcrypt.x86_64 0:2.5.8-13.el7
  libraqm.x86_64 0:0.7.0-4.el7              libtiff.x86_64 0:4.0.3-35.el7
  libwebp7.x86_64 0:1.0.3-2.el7.remi        libxslt.x86_64 0:1.1.28-6.el7
  libzip5.x86_64 0:1.10.1-1.el7.remi        libzstd.x86_64 0:1.5.5-1.el7
  oniguruma5php.x86_64 0:6.9.9-1.el7.remi

完毕！
[root@localhost ~]#
[root@localhost ~]# php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dom
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
json
libxml
mbstring
mcrypt
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
readline
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache

[root@localhost ~]#
```

安装完成。

设置开机自启动：
> systemctl enable php-fpm

启动fpm服务：
> systemctl start php-fpm

停止fpm服务：
> systemctl stop php-fpm

详细：
```
[root@localhost ~]# systemctl enable php-fpm
Created symlink from /etc/systemd/system/multi-user.target.wants/php-fpm.service to /usr/lib/systemd/system/php-fpm.service.
[root@localhost ~]#
[root@localhost ~]# systemctl status php-fpm
● php-fpm.service - The PHP FastCGI Process Manager
   Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
[root@localhost ~]#
[root@localhost ~]# systemctl start php-fpm
[root@localhost ~]#
[root@localhost ~]# systemctl status php-fpm
● php-fpm.service - The PHP FastCGI Process Manager
   Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; disabled; vendor preset: disabled)
   Active: active (running) since 三 2024-09-18 19:26:23 CST; 3s ago
 Main PID: 2654 (php-fpm)
   Status: "Ready to handle connections"
   CGroup: /system.slice/php-fpm.service
           ├─2654 php-fpm: master process (/etc/php-fpm.conf)
           ├─2655 php-fpm: pool www
           ├─2656 php-fpm: pool www
           ├─2657 php-fpm: pool www
           ├─2658 php-fpm: pool www
           └─2659 php-fpm: pool www

9月 18 19:26:23 localhost.localdomain systemd[1]: Starting The PHP FastCGI Process M....
9月 18 19:26:23 localhost.localdomain systemd[1]: Started The PHP FastCGI Process Ma....
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]#
[root@localhost ~]# systemctl stop php-fpm
[root@localhost ~]#
[root@localhost ~]# systemctl status php-fpm
● php-fpm.service - The PHP FastCGI Process Manager
   Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; disabled; vendor preset: disabled)
   Active: inactive (dead)

9月 18 19:26:23 localhost.localdomain systemd[1]: Starting The PHP FastCGI Process M....
9月 18 19:26:23 localhost.localdomain systemd[1]: Started The PHP FastCGI Process Ma....
9月 18 19:27:20 localhost.localdomain systemd[1]: Stopping The PHP FastCGI Process M....
9月 18 19:27:20 localhost.localdomain systemd[1]: Stopped The PHP FastCGI Process Ma....
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]#
```

### Swoolet拓展安装

搜索对应的swoole包：
```bash
> yum search pecl|grep swoole
> 
> yum list php74* | grep swoole
```

安装：
```bash
yum --enablerepo=remi-php74 install -y php-pecl-swoole4
```

这里有一个注意点，别输入成```yum --enablerepo=remi-php74 install -y php74-php-pecl-swoole4```，不然拓展会安装失败，安装后找不到。

详细：
```bash
[root@localhost ~]# yum search pecl|grep swoole
php72-php-pecl-swoole-devel.x86_64 : php72-php-pecl-swoole developer files
php72-php-pecl-swoole2-devel.x86_64 : php72-php-pecl-swoole2 developer files
php80-php-pecl-swoole5-devel.x86_64 : php80-php-pecl-swoole5 developer files
php81-php-pecl-openswoole22-devel.x86_64 : php81-php-pecl-openswoole22 developer
php81-php-pecl-swoole5-devel.x86_64 : php81-php-pecl-swoole5 developer files
php82-php-pecl-openswoole22-devel.x86_64 : php82-php-pecl-openswoole22 developer
php82-php-pecl-swoole5-devel.x86_64 : php82-php-pecl-swoole5 developer files
php83-php-pecl-openswoole22-devel.x86_64 : php83-php-pecl-openswoole22 developer
php83-php-pecl-swoole5-devel.x86_64 : php83-php-pecl-swoole5 developer files
php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed networking
php54-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php55-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php55-php-pecl-swoole2.x86_64 : PHP's asynchronous concurrent distributed
php56-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php56-php-pecl-swoole2.x86_64 : PHP's asynchronous concurrent distributed
php70-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php70-php-pecl-swoole-serialize.x86_64 : Fast and Small serialize
php70-php-pecl-swoole2.x86_64 : PHP's asynchronous concurrent distributed
php70-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php71-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php71-php-pecl-swoole-serialize.x86_64 : Fast and Small serialize
php71-php-pecl-swoole2.x86_64 : PHP's asynchronous concurrent distributed
php71-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php72-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php72-php-pecl-swoole.x86_64 : PHP's asynchronous concurrent distributed
php72-php-pecl-swoole-serialize.x86_64 : Fast and Small serialize
php72-php-pecl-swoole2.x86_64 : PHP's asynchronous concurrent distributed
php72-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php73-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php73-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php74-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php74-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for
php74-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php80-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php80-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for
php80-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php80-php-pecl-swoole5.x86_64 : PHP's asynchronous concurrent distributed
php81-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php81-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for
php81-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php81-php-pecl-swoole5.x86_64 : PHP's asynchronous concurrent distributed
php82-php-pecl-openswoole.x86_64 : PHP's asynchronous concurrent distributed
php82-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for
php82-php-pecl-swoole4.x86_64 : PHP's asynchronous concurrent distributed
php82-php-pecl-swoole5.x86_64 : PHP's asynchronous concurrent distributed
php83-php-pecl-openswoole22.x86_64 : High Performance Programmatic Server for
php83-php-pecl-swoole5.x86_64 : PHP's asynchronous concurrent distributed
[root@localhost ~]#
[root@localhost ~]# yum list php74* | grep swoole
php74-php-pecl-openswoole.x86_64         4.12.1-1.el7.remi             remi-safe
php74-php-pecl-openswoole22.x86_64       22.0.0-1.el7.remi             remi-safe
php74-php-pecl-swoole4.x86_64            4.8.13-1.el7.remi             remi-safe
[root@localhost ~]#
[root@localhost ~]# yum --enablerepo=remi-php74 install -y php-pecl-swoole4
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                              | 4.7 kB  00:00:00
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-php74: mirrors.tuna.tsinghua.edu.cn
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
base                                                              | 3.6 kB  00:00:00
docker-ce-stable                                                  | 3.5 kB  00:00:00
extras                                                            | 2.9 kB  00:00:00
remi-php74                                                        | 3.0 kB  00:00:00
remi-safe                                                         | 3.0 kB  00:00:00
updates                                                           | 2.9 kB  00:00:00
正在解决依赖关系
--> 正在检查事务
---> 软件包 php-pecl-swoole4.x86_64.0.4.8.13-1.el7.remi 将被 安装
--> 正在处理依赖关系 php74-php(api) = 20190902-64，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php(zend-abi) = 20190902-64，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-curl(x86-64)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-json(x86-64)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-mysqlnd(x86-64)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-sockets(x86-64)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-runtime，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-runtime(remi)(x86-64)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libcares.so.2()(64bit)，它被软件包 php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 c-ares.x86_64.0.1.10.0-3.el7_9.1 将被 安装
---> 软件包 php74-php-common.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php74-php-json.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 php74-php-mysqlnd.x86_64.0.7.4.33-15.el7.remi 将被 安装
--> 正在处理依赖关系 php74-php-pdo(x86-64) = 7.4.33-15.el7.remi，它被软件包 php74-php-mysqlnd-7.4.33-15.el7.remi.x86_64 需要
---> 软件包 php74-runtime.x86_64.0.7.4-3.el7.remi 将被 安装
--> 正在处理依赖关系 environment-modules，它被软件包 php74-runtime-7.4-3.el7.remi.x86_64 需要
--> 正在处理依赖关系 scl-utils，它被软件包 php74-runtime-7.4-3.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 environment-modules.x86_64.0.3.2.10-10.el7 将被 安装
--> 正在处理依赖关系 libtcl8.5.so()(64bit)，它被软件包 environment-modules-3.2.10-10.el7.x86_64 需要
---> 软件包 php74-php-pdo.x86_64.0.7.4.33-15.el7.remi 将被 安装
---> 软件包 scl-utils.x86_64.0.20130529-19.el7 将被 安装
--> 正在检查事务
---> 软件包 tcl.x86_64.1.8.5.13-8.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

=========================================================================================
 Package                     架构        版本                       源              大小
=========================================================================================
正在安装:
 php74-php-pecl-swoole4      x86_64      4.8.13-1.el7.remi          remi-safe      1.0 M
为依赖而安装:
 c-ares                      x86_64      1.10.0-3.el7_9.1           updates         78 k
 environment-modules         x86_64      3.2.10-10.el7              base           107 k
 php74-php-common            x86_64      7.4.33-15.el7.remi         remi-safe      702 k
 php74-php-json              x86_64      7.4.33-15.el7.remi         remi-safe       80 k
 php74-php-mysqlnd           x86_64      7.4.33-15.el7.remi         remi-safe      198 k
 php74-php-pdo               x86_64      7.4.33-15.el7.remi         remi-safe      128 k
 php74-runtime               x86_64      7.4-3.el7.remi             remi-safe      1.1 M
 scl-utils                   x86_64      20130529-19.el7            base            24 k
 tcl                         x86_64      1:8.5.13-8.el7             base           1.9 M

事务概要
=========================================================================================
安装  1 软件包 (+9 依赖软件包)

总下载量：5.3 M
安装大小：15 M
Downloading packages:
(1/10): environment-modules-3.2.10-10.el7.x86_64.rpm              | 107 kB  00:00:00
(2/10): c-ares-1.10.0-3.el7_9.1.x86_64.rpm                        |  78 kB  00:00:00
(3/10): php74-php-mysqlnd-7.4.33-15.el7.remi.x86_64.rpm           | 198 kB  00:00:01
(4/10): php74-runtime-7.4-3.el7.remi.x86_64.rpm                   | 1.1 MB  00:00:04
(5/10): scl-utils-20130529-19.el7.x86_64.rpm                      |  24 kB  00:00:00
(6/10): tcl-8.5.13-8.el7.x86_64.rpm                               | 1.9 MB  00:00:02
(7/10): php74-php-json-7.4.33-15.el7.remi.x86_64.rpm              |  80 kB  00:00:18
(8/10): php74-php-common-7.4.33-15.el7.remi.x86_64.rpm            | 702 kB  00:00:47
(9/10): php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64.rpm       | 1.0 MB  00:00:56
php74-php-pdo-7.4.33-15.el7.re FAILED                                          00:04 ETA
http://mirrors.thzhost.com/remi/enterprise/7/safe/x86_64/php74-php-pdo-7.4.33-15.el7.remi.x86_64.rpm: [Errno 12] Timeout on http://mirrors.thzhost.com/remi/enterprise/7/safe/x86_64/php74-php-pdo-7.4.33-15.el7.remi.x86_64.rpm: (28, 'Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds')
正在尝试其它镜像。
(10/10): php74-php-pdo-7.4.33-15.el7.remi.x86_64.rpm              | 128 kB  00:00:01
-----------------------------------------------------------------------------------------
总计                                                         91 kB/s | 5.3 MB  01:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : c-ares-1.10.0-3.el7_9.1.x86_64                                      1/10
  正在安装    : scl-utils-20130529-19.el7.x86_64                                    2/10
  正在安装    : 1:tcl-8.5.13-8.el7.x86_64                                           3/10
  正在安装    : environment-modules-3.2.10-10.el7.x86_64                            4/10
  正在安装    : php74-runtime-7.4-3.el7.remi.x86_64                                 5/10
  正在安装    : php74-php-json-7.4.33-15.el7.remi.x86_64                            6/10
  正在安装    : php74-php-common-7.4.33-15.el7.remi.x86_64                          7/10
  正在安装    : php74-php-pdo-7.4.33-15.el7.remi.x86_64                             8/10
  正在安装    : php74-php-mysqlnd-7.4.33-15.el7.remi.x86_64                         9/10
  正在安装    : php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64                    10/10
=====================================================================

  WARNING : PHP 7.4 have reached its "End of Life" in
  November 2022. Even, if this package includes some of
  the important security fixes, backported from 8.1, the
  UPGRADE to a maintained version is very strongly RECOMMENDED.

=====================================================================
  验证中      : 1:tcl-8.5.13-8.el7.x86_64                                           1/10
  验证中      : scl-utils-20130529-19.el7.x86_64                                    2/10
  验证中      : php74-php-mysqlnd-7.4.33-15.el7.remi.x86_64                         3/10
  验证中      : c-ares-1.10.0-3.el7_9.1.x86_64                                      4/10
  验证中      : environment-modules-3.2.10-10.el7.x86_64                            5/10
  验证中      : php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64                     6/10
  验证中      : php74-php-json-7.4.33-15.el7.remi.x86_64                            7/10
  验证中      : php74-runtime-7.4-3.el7.remi.x86_64                                 8/10
  验证中      : php74-php-pdo-7.4.33-15.el7.remi.x86_64                             9/10
  验证中      : php74-php-common-7.4.33-15.el7.remi.x86_64                         10/10

已安装:
  php74-php-pecl-swoole4.x86_64 0:4.8.13-1.el7.remi

作为依赖被安装:
  c-ares.x86_64 0:1.10.0-3.el7_9.1
  environment-modules.x86_64 0:3.2.10-10.el7
  php74-php-common.x86_64 0:7.4.33-15.el7.remi
  php74-php-json.x86_64 0:7.4.33-15.el7.remi
  php74-php-mysqlnd.x86_64 0:7.4.33-15.el7.remi
  php74-php-pdo.x86_64 0:7.4.33-15.el7.remi
  php74-runtime.x86_64 0:7.4-3.el7.remi
  scl-utils.x86_64 0:20130529-19.el7
  tcl.x86_64 1:8.5.13-8.el7

完毕！
[root@localhost ~]#
[root@localhost ~]# php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dom
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
json
libxml
mbstring
mcrypt
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
readline
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache

[root@localhost ~]#
```

安装成功。

重启 php-fpm 服务：
> systemctl restart php-fpm

### Composer安装

下载安装包：
>  php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"

安装：
> php composer-setup.php

移到bin目录：
> mv composer.phar /usr/local/bin/composer

删除安装包：
> php -r "unlink('composer-setup.php');"

指定国内源：
> composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

查看当前的全局配置：
> composer config -g -l

详细：
```bash
[root@localhost ~]# php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
[root@localhost ~]#
[root@localhost ~]# ls
anaconda-ks.cfg  composer-setup.php
[root@localhost ~]#
[root@localhost ~]# php composer-setup.php
All settings correct for using Composer
Downloading...

Composer (version 2.7.9) successfully installed to: /root/composer.phar
Use it: php composer.phar

[root@localhost ~]#
[root@localhost ~]# ll
总用量 3012
-rw-------. 1 root root    1412 9月   3 17:28 anaconda-ks.cfg
-rwxr-xr-x  1 root root 3018138 9月  20 14:25 composer.phar
-rw-r--r--  1 root root   58452 9月  20 14:24 composer-setup.php
[root@localhost ~]#
[root@localhost ~]# mv composer.phar /usr/local/bin/composer
[root@localhost ~]#
[root@localhost ~]# ls
anaconda-ks.cfg  composer-setup.php
[root@localhost ~]#
[root@localhost ~]# composer --version
Composer version 2.7.9 2024-09-04 14:43:28
PHP version 7.4.33 (/usr/bin/php)
Run the "diagnose" command to get more detailed diagnostics output.
[root@localhost ~]#
[root@localhost ~]# php -r "unlink('composer-setup.php');"
[root@localhost ~]#
[root@localhost ~]# ls
anaconda-ks.cfg
[root@localhost ~]#
```

### 拓展解说

PECL 的全称是 The PHP Extension Community Library ，是一个开放的并通过 PEAR(PHP Extension and Application Repository，
PHP 扩展和应用仓库)打包格式来打包安装的 PHP 扩展库仓库。通过 PEAR 的 Package Manager 的安装管理方式，
可以对 PECL 模块进行下载和安装。与以往的多数 PEAR 包不同的是，PECL 扩展包含的是可以编译进 PHP Core 的 C 语言代码，
因此可以将 PECL 扩展库编译成为可动态加载的 .so 共享库，或者采用静态编译方式与 PHP 源代码编译为一体的方法进行扩展。
PECL 扩展库包含了对于 XML 解析，数据库访问，邮件解析，嵌入式的 Perl 以及 Python 脚本解释器等诸多的 PHP 扩展模块，
因此从某种意义上来说，在运行效率上 PECL 要高于以往诸多的 PEAR 扩展库。

```
php74：安装PHP 7.4的包
php74-php：用于创建动态web的PHP脚本语言网站
php74-build：基本的构建配置
php74-php-gd：用于PHP应用程序使用gd的模块图形库
php74-xhprof：一个用于PHP - Web接口的层次分析器
php74-zephir：用于创建扩展的Zephir语言PHP：
php74-php-ast：抽象语法树
php74-php-cli：用于PHP的命令行接口
php74-php-dba：一个用于PHP的数据库抽象层模块应用程序
php74-php-dbg：交互式PHP调试器
php74-php-ffi：外部函数接口
php74-php-fpm：PHP FastCGI进程管理器
php74-php-gmp：一个用于使用GNU的PHP应用程序模块议员库
php74-php-lz4：x86_64: PHP的LZ4扩展
php74-php-pdo：一个用于PHP的数据库访问抽象模块应用程序
php74-php-xml：x86_64:用于使用XML的PHP应用程序的模块
php74-runtime：处理php74软件集合的包：
php74-php-imap：x86_64:用于使用IMAP的PHP应用程序的模块
php74-php-intl：x86_64: PHP国际化扩展应用程序
php74-php-json：x86_64: PHP的JavaScript对象符号扩展
php74-php-ldap：x86_64:用于使用LDAP的PHP应用程序的模块
php74-php-oci8：x86_64:用于使用OCI8的PHP应用程序的模块数据库
php74-php-odbc：x86_64:用于使用ODBC的PHP应用程序的模块数据库
php74-php-pear：noarch: PHP扩展和应用程序库框架
php74-php-pggi：GTK绑定
php74-php-snmp：一个用于PHP应用程序查询的模块SNMP-managed设备
php74-php-soap：用于使用SOAP的PHP应用程序的模块协议
php74-php-tidy：标准PHP模块提供了tidy库支持
php74-php-zstd：x86_64: Zstandard扩展名
php74-scldevel：x86_64:打包php74的开发文件
php74-unit-php：用于NGINX单元的PHP模块
php74-php-devel：构建PHP扩展所需的文件
php74-php-pgsql：一个用于PHP的PostgreSQL数据库模块
php74-php-pinba：x86_64: Pinba统计服务器的客户端扩展
php74-php-bcmath：用于PHP应用程序的模块bcmath库
php74-php-brotli：用于PHP的Brotli扩展
php74-php-common：PHP的公共文件
php74-php-pspell：x86_64:用于PHP应用程序的模块中接口
php74-php-recode：用于PHP应用程序的模块重新编码库
php74-php-snappy：PHP的Snappy扩展
php74-php-sodium：x86_64:钠密码库的包装
php74-php-sqlsrv：x86_64:用于SQL Server的PHP Microsoft驱动程序
php74-php-xmlrpc：的PHP应用程序模块xml - rpc协议
php74-php-enchant：增强PHP的拼写扩展应用程序
php74-php-libvirt：用于Libvirt的PHP语言绑定
php74-php-mysqlnd：x86_64:用于使用MySQL的PHP应用程序的模块数据库
php74-php-opcache：x86_64: Zend OPcache
php74-php-pecl-ds：x86_64: PHP的数据结构
php74-php-pecl-ev：提供libev库的接口
php74-php-pecl-pq：PostgreSQL客户端库(libpq)绑定
php74-php-pecl-uv：Libuv包装器
php74-php-process：用于PHP脚本的模块使用系统进程接口
php74-php-embedded：用于嵌入应用程序的PHP库
php74-php-mbstring：一个用于PHP应用程序的模块多字节字符串处理
php74-php-pecl-dio：直接I/O函数
php74-php-pecl-eio：提供libeio库的接口
php74-php-pecl-env：加载环境变量
php74-php-pecl-lua：嵌入式lua解释器
php74-php-pecl-lzf：x86_64:处理LZF de/压缩的扩展
php74-php-pecl-nsq：NSQ客户端的PHP扩展
php74-php-pecl-psr：PSR接口
php74-php-pecl-rar：用于读取RAR存档的PHP扩展
php74-php-pecl-rrd：x86_64: rrdtool的PHP绑定
php74-php-pecl-sdl：简单的PHP DirectMedia层
php74-php-pecl-svn：x86_64: Subversion版本的PHP绑定控制系统
php74-php-pecl-vld：x86_64:转储PHP的内部表示脚本
php74-php-pecl-yac：无锁用户数据缓存
php74-php-pecl-yaf：x86_64:又一个框架
php74-php-pecl-yar：轻量级并发RPC框架
php74-php-pecl-yaz：x86_64: Z39.50/SRU客户端
php74-php-pecl-zip：一个ZIP文件的扩展名
php74-php-pecl-zmq：ZeroMQ消息传递
php74-php-phalcon4：Phalcon框架
php74-php-componere：在运行时编写PHP类
php74-php-litespeed：LiteSpeed Web服务器PHP支持
php74-php-maxminddb：MaxMind DB Reader扩展
php74-php-pdo-dblib：x86_64: PDO驱动程序，用于Microsoft SQL Server和Sybase数据库
php74-php-pecl-amqp：与任何AMQP兼容的服务器通信
php74-php-pecl-apcu：APC用户缓存
php74-php-pecl-apfd：总是填充表单数据
php74-php-pecl-fann：用于FANN库的包装器
php74-php-pecl-grpc：x86_64:通用RPC框架
php74-php-pecl-http：扩展的HTTP支持
php74-php-pecl-krb5：Kerberos验证扩展
php74-php-pecl-pcov：x86_64:代码覆盖驱动程序
php74-php-pecl-ssh2：x86_64: libssh2库的绑定
php74-php-pecl-sync：已命名和未命名同步对象
php74-php-pecl-uopz：x86_64: Zend的用户操作
php74-php-pecl-uuid：通用唯一标识符扩展对于PHP
php74-php-pecl-vips：x86_64:与libvip接口的PHP扩展
php74-php-pecl-wddx：Web分布式数据交换
php74-php-pecl-yaml：x86_64: yaml的PHP绑定
php74-php-phpiredis：x86_64: Redis的客户端扩展名
php74-php-smbclient：libsmbclient的PHP包装
php74-php-wkhtmltox：HTML转换器
php74-php-pecl-cmark：CommonMark扩展名
php74-php-pecl-dbase：dBase数据库文件访问函数
php74-php-pecl-druid：一个PHP的德鲁伊驱动
php74-php-pecl-event：提供libevent库的接口
php74-php-pecl-geoip：将IP地址映射到的扩展名地理位置
php74-php-pecl-gnupg：x86_64: gpgme库的包装
php74-php-pecl-mysql：MySQL数据库访问函数
php74-php-pecl-oauth：PHP OAuth消费者扩展
php74-php-pecl-parle：x86_64:解析和词法分析
php74-php-pecl-raphf：资源和持久句柄工厂
php74-php-pecl-solr2：Apache Solr的API定向对象
php74-php-pecl-stats：用于统计计算的例程
php74-php-pecl-stomp：Stomp客户端扩展
php74-php-pecl-taint：x86_64: XSS代码嗅探器
php74-php-pecl-xattr：扩展属性
php74-php-pecl-xdiff：文件差异/补丁
php74-php-pecl-xxtea：XXTEA加密算法扩展PHP
php74-php-libvirt-doc：noarch: php-libvirt文件
php74-php-pecl-base58：用base58编码和解码数据
php74-php-pecl-bitset：位集库
php74-php-pecl-crypto：OpenSSL密码库的包装器
php74-php-pecl-gender：x86_64:性别扩展
php74-php-pecl-hprose：用于PHP的h散文
php74-php-pecl-hrtime：x86_64:高分辨率定时
php74-php-pecl-mcrypt：x86_64: libmcrypt库的绑定
php74-php-pecl-pdflib：生成PDF文件的包
php74-php-pecl-propro：属性代理
php74-php-pecl-radius：Radius客户端库
php74-php-pecl-redis4：的扩展名 Redis键值存储
php74-php-pecl-redis5：的扩展名 Redis键值存储
php74-php-pecl-scrypt：Scrypt散列函数
php74-php-pecl-sphinx：Sphinx SQL全文的PECL扩展搜索引擎
php74-php-pecl-ssdeep：libfuzzy库的包装器
php74-php-pecl-trader：为贸易商提供技术分析
php74-php-pecl-xdebug：用于调试PHP脚本的PECL包
php74-php-pecl-xhprof：x86_64: XHProf的PHP扩展，层次结构分析器
php74-php-pecl-yaconf：x86_64:另一个配置容器
php74-php-pecl-apcu-bc：APCu向后兼容模块
php74-php-pecl-decimal：任意精度浮点小数
php74-php-pecl-gearman：PHP包装器到libgearman
php74-php-pecl-gmagick：的包装器GraphicsMagick库
php74-php-pecl-imagick：x86_64:用于创建和修改映像的扩展使用ImageMagick
php74-php-pecl-inotify：x86_64: Inotify
php74-php-pecl-leveldb：LevelDB PHP绑定
php74-php-pecl-memprof：x86_64:内存使用分析器
php74-php-pecl-mongodb：x86_64:用于PHP的MongoDB驱动程序
php74-php-pecl-msgpack：用于与MessagePack通信的API序列化
php74-php-pecl-rdkafka：基于librdkafka的Kafka客户端
php74-php-pecl-rpminfo：x86_64: RPM信息
php74-php-pecl-runkit7：为了这些你…不应该…无论如何我一直在做……但是肯定做的!
php74-php-pecl-seaslog：一个有效、快速、稳定的日志 PHP扩展
php74-php-pecl-selinux：用于PHP脚本的SELinux绑定语言
php74-php-pecl-swoole4：PHP的异步并发分布式网络框架
php74-php-pecl-timecop：x86_64:时间旅行和冻结扩展
php74-php-pecl-varnish：Varnish缓存绑定
php74-php-pecl-xmldiff：x86_64: XML差异和合并
php74-php-pecl-igbinary：x86_64:替代标准PHP序列化器
php74-php-pecl-memcache：x86_64:使用Memcached的扩展缓存守护进程
php74-php-pecl-mogilefs：要与之通信的PHP客户端库 MogileFS存储
php74-php-pecl-mustache：Mustache模板语言
php74-php-pecl-protobuf：序列化结构化的机制数据
php74-php-pecl-rdkafka4：基于librdkafka的Kafka客户端
php74-php-pecl-scoutapm：本地扩展组件 ScoutAPM的PHP代理
php74-php-pecl-translit：将非拉丁字符音译设置为拉丁文
php74-php-snuffleupagus：x86_64: PHP的安全模块
php74-php-zephir-parser：x86_64: Zephir解析器扩展
php74-php-pecl-cassandra：用于Apache的DataStax PHP驱动程序卡桑德拉
php74-php-pecl-json-post：JSON POST处理程序
php74-php-pecl-mailparse：用于解析和的PHP PECL包处理电子邮件消息
php74-php-pecl-memcached：x86_64:使用Memcached的扩展缓存守护进程
php74-php-pecl-mosquitto：libmosquito - to的扩展
php74-php-pecl-seasclick：一个Yandex ClickHouse客户端驱动 PHP扩展
php74-php-pecl-xlswriter：一个高效、快速的xlsx文件出口扩展
php74-php-horde-horde-lz4：Horde LZ4压缩扩展
php74-php-pecl-apcu-devel：APCu开发人员文件(头文件)
php74-php-pecl-couchbase2：Couchbase服务器PHP扩展
php74-php-pecl-geospatial：PHP扩展来处理公共地理空间功能
php74-php-pecl-handlebars：Handlebars模板语言
php74-php-pecl-http-devel：x86_64:扩展HTTP支持开发人员文件(头)
php74-php-pecl-krb5-devel：Kerberos扩展开发人员文件(头)
php74-php-pecl-luasandbox：带有限制和安全的Lua解释器环境
php74-php-pecl-opencensus：一个stats集合和分布式跟踪框架
php74-php-pecl-skywalking：x86_64: Apache的PHP工具代理人行天桥
php74-php-pecl-ahocorasick：有效的Aho-Corasick字符串模式匹配算法
php74-php-pecl-ip2location：获取an的地理位置信息IP地址
php74-php-pecl-http-message：x86_64: PSR-7 HTTP消息实现
php74-php-pecl-datadog-trace：APM和分布式跟踪PHP
php74-php-pecl-hdr-histogram：x86_64: C语言的PHP扩展包装hdrhistogram API
php74-php-pecl-imagick-devel：imagick扩展开发人员文件(头)
php74-php-pecl-msgpack-devel：MessagePack开发人员文件(头)
php74-php-pecl-mysql-xdevapi：MySQL数据库访问函数
php74-php-pecl-mysqlnd-azure：x86_64: mysqlnd的重定向插件
php74-php-pecl-igbinary-devel：Igbinary开发人员文件(头)
php74-php-pecl-uploadprogress：x86_64:跟踪进度的扩展文件上传
```

### rpm和yum-utils相关

rpm命令是RPM软件包的管理工具。RPM套件管理方式的出现，让Linux易于安装，升级，间接提升了Linux的适用度。

rpm常见命令参数：
```
用法: rpm [选项...]
-a：查询所有套件；
-b<完成阶段><套件档>+或-t <完成阶段><套件档>+：设置包装套件的完成阶段，并指定套件档的文件名称；
-c：只列出组态配置文件，本参数需配合"-l"参数使用；
-d：只列出文本文件，本参数需配合"-l"参数使用；
-e<套件档>或--erase<套件档>：删除指定的套件；
-f<文件>+：查询拥有指定文件的套件；
-h或--hash：套件安装时列出标记；
-i：显示套件的相关信息；
-i<套件档>或--install<套件档>：安装指定的套件档；
-l：显示套件的文件列表；
-p<套件档>+：查询指定的RPM套件档；
-q：使用询问模式，当遇到任何问题时，rpm指令会先询问用户；
-R：显示套件的关联性信息；
-s：显示文件状态，本参数需配合"-l"参数使用；
-U<套件档>或--upgrade<套件档>：升级指定的套件档；
-v：显示指令执行过程；
-vv：详细显示指令执行过程，便于排错。
```

```
# rpm更新软件包
rpm -Uvh your.rpm

# 直接安装
rpm -ivh your-package
# 忽略报错，强制安装  
rpmrpm --force -ivh your-package.rpm 

# 查询
rpm -ql 程序名

# 列出所有安装过的包
rpm -qa

# 获得某个软件包的文件全名
rpm -q 程序名

# rpm包中的文件安装的位置
rpm -ql 程序名

# 返回软件包的全名
rpm -qf `which 程序名`    
# 返回软件包的有关信息
rpm -qif `which 程序名`   
# 返回软件包的文件列表
rpm -qlf `which 程序名`   

# 卸载
rpm -e 程序名 
```

详细：
```
[root@localhost ~]# rpm -qa
php-7.4.33-15.el7.remi.x86_64
setup-2.8.71-11.el7.noarch
dejavu-sans-fonts-2.33-6.el7.noarch
authconfig-6.2.8-30.el7.x86_64
ncurses-base-5.9-14.20130511.el7_4.noarch
graphite2-1.3.10-1.el7_3.x86_64
libtiff-4.0.3-35.el7.x86_64
biosdevname-0.7.3-2.el7.x86_64
libstdc++-4.8.5-44.el7.x86_64
libzstd-1.5.5-1.el7.x86_64
pcre-8.32-17.el7.x86_64
gd3php-2.3.3-7.el7.remi.x86_64
btrfs-progs-4.9.1-1.el7.x86_64
php-mysqlnd-7.4.33-15.el7.remi.x86_64
libsysfs-2.1.0-16.el7.x86_64
bzip2-libs-1.0.6-13.el7.x86_64
php-fpm-7.4.33-15.el7.remi.x86_64
rootfiles-8.1-11.el7.noarch
readline-6.2-11.el7.x86_64
libattr-2.4.46-13.el7.x86_64
audit-libs-2.8.5-4.el7.x86_64
net-tools-2.0-0.25.20131004git.el7.x86_64
findutils-4.5.11-6.el7.x86_64
kbd-misc-1.15.5-16.el7_9.noarch
sqlite-3.7.17-8.el7_7.1.x86_64
nss-softokn-freebl-3.90.0-6.el7_9.x86_64
libaio-0.3.109-13.el7.x86_64
nss-util-3.90.0-1.el7_9.x86_64
libassuan-2.1.0-3.el7.x86_64
libuuid-2.23.2-65.el7_9.1.x86_64
libgomp-4.8.5-44.el7.x86_64
libxml2-2.9.1-6.el7_9.6.x86_64
jansson-2.10-1.el7.x86_64
vim-minimal-7.4.629-8.el7_9.x86_64
tcp_wrappers-libs-7.6-77.el7.x86_64
ca-certificates-2023.2.60_v7.0.306-72.el7_9.noarch
ethtool-4.8-10.el7.x86_64
libblkid-2.23.2-65.el7_9.1.x86_64
newt-0.52.15-4.el7.x86_64
python-libs-2.7.5-94.el7_9.x86_64
virt-what-1.18-4.el7_9.1.x86_64
ipset-7.1-1.el7.x86_64
nss-pem-1.0.3-7.el7_9.1.x86_64
bc-1.06.95-13.el7.x86_64
nss-tools-3.90.0-2.el7_9.x86_64
kmod-libs-20-28.el7.x86_64
rpm-libs-4.11.3-48.el7_9.x86_64
libselinux-utils-2.5-15.el7.x86_64
initscripts-9.49.53-1.el7_9.1.x86_64
gmp-6.0.0-15.el7.x86_64
device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64
libverto-0.2.5-4.el7.x86_64
dhcp-libs-4.2.5-83.el7.centos.2.x86_64
p11-kit-trust-0.23.5-3.el7.x86_64
device-mapper-event-1.02.170-6.el7_9.5.x86_64
systemd-sysv-219-78.el7_9.9.x86_64
shadow-utils-4.6-5.el7.x86_64
firewalld-filesystem-0.6.3-13.el7_9.noarch
NetworkManager-team-1.18.8-2.el7_9.x86_64
cracklib-dicts-2.9.0-11.el7.x86_64
dhclient-4.2.5-83.el7.centos.2.x86_64
gettext-libs-0.19.8.1-3.el7.x86_64
kpartx-0.4.9-136.el7_9.x86_64
gobject-introspection-1.56.1-1.el7.x86_64
kexec-tools-2.0.15-51.el7_9.3.x86_64
pyliblzma-0.5.3-11.el7.x86_64
libcroco-0.6.12-6.el7_9.x86_64
python-configobj-4.7.2-7.el7.noarch
kernel-tools-3.10.0-1160.119.1.el7.x86_64
pyxattr-0.5.1-5.el7.x86_64
iwl6050-firmware-41.28.5.1-83.el7_9.noarch
iwl2030-firmware-18.168.6.1-83.el7_9.noarch
iwl6000-firmware-9.221.4.1-83.el7_9.noarch
iwl5000-firmware-8.83.5.1_1-83.el7_9.noarch
mpfr-3.1.1-4.el7.x86_64
cpp-4.8.5-44.el7.x86_64
hardlink-1.0-19.el7.x86_64
perl-Pod-Perldoc-3.20-4.el7.noarch
perl-Pod-Usage-1.63-3.el7.noarch
libseccomp-2.3.1-4.el7.x86_64
perl-Exporter-5.68-3.el7.noarch
procps-ng-3.3.10-28.el7.x86_64
perl-Carp-1.26-244.el7.noarch
perl-Scalar-List-Utils-1.27-248.el7.x86_64
elfutils-libs-0.176-5.el7.x86_64
perl-libs-5.16.3-299.el7_9.x86_64
dbus-1.10.24-15.el7.x86_64
kernel-headers-3.10.0-1160.119.1.el7.x86_64
iputils-20160308-10.el7.x86_64
libXau-1.0.8-2.1.el7.x86_64
libXt-1.1.5-3.el7.x86_64
bzip2-1.0.6-13.el7.x86_64
perl-Git-1.8.3.1-25.el7_9.noarch
libxml2-python-2.9.1-6.el7_9.6.x86_64
setools-libs-3.3.8-4.el7.x86_64
ebtables-2.0.10-16.el7.x86_64
python-IPy-0.75-6.el7.noarch
hwdata-0.252-9.7.el7.x86_64
checkpolicy-2.5-8.el7.x86_64
dbus-glib-0.100-7.el7.x86_64
docker-buildx-plugin-0.14.1-1.el7.x86_64
plymouth-core-libs-0.8.9-0.34.20140113.el7.centos.x86_64
docker-ce-cli-26.1.4-1.el7.x86_64
wget-1.14-18.el7_6.1.x86_64
gpg-pubkey-00f97f56-467e318a
yum-3.4.3-168.el7.centos.noarch
apr-util-1.5.2-6.el7_9.1.x86_64
httpd-2.4.6-99.el7.centos.1.x86_64
libgcc-4.8.5-44.el7.x86_64
php-sodium-7.4.33-15.el7.remi.x86_64
fontpackages-filesystem-1.44-8.el7.noarch
filesystem-3.2-25.el7.x86_64
dejavu-fonts-common-2.33-6.el7.noarch
fontconfig-2.13.0-4.3.el7.x86_64
audit-2.8.5-4.el7.x86_64
libwebp7-1.0.3-2.el7.remi.x86_64
kernel-3.10.0-1160.el7.x86_64
harfbuzz-1.7.5-2.el7.x86_64
chrony-3.4-1.el7.x86_64
jbigkit-libs-2.0-11.el7.x86_64
php-pdo-7.4.33-15.el7.remi.x86_64
irqbalance-1.0.7-12.el7.x86_64
ncurses-libs-5.9-14.20130511.el7_4.x86_64
libraqm-0.7.0-4.el7.x86_64
parted-3.1-32.el7.x86_64
libsepol-2.5-10.el7.x86_64
libzip5-1.10.1-1.el7.remi.x86_64
man-db-2.6.3-11.el7.x86_64
libselinux-2.5-15.el7.x86_64
libXpm-3.5.12-2.el7_9.x86_64
info-5.1-5.el7.x86_64
php-gd-7.4.33-15.el7.remi.x86_64
xfsprogs-4.5.0-22.el7.x86_64
libcom_err-1.42.9-19.el7.x86_64
php-pecl-zip-1.22.3-1.el7.remi.7.4.x86_64
sed-4.2.2-7.el7.x86_64
php-xml-7.4.33-15.el7.remi.x86_64
popt-1.13-16.el7.x86_64
php-bcmath-7.4.33-15.el7.remi.x86_64
libdb-5.3.21-25.el7.x86_64
php-opcache-7.4.33-15.el7.remi.x86_64
grep-2.20-3.el7.x86_64
elfutils-libelf-0.176-5.el7.x86_64
ivtv-firmware-20080701-26.el7.noarch
libacl-2.2.51-15.el7.x86_64
libcap-ng-0.7.5-4.el7.x86_64
libgpg-error-1.12-3.el7.x86_64
libgcrypt-1.5.3-14.el7.x86_64
cpio-2.11-28.el7.x86_64
gpg-pubkey-f4a80eb5-53a7ff4b
lua-5.1.4-15.el7.x86_64
grub2-common-2.02-0.87.0.2.el7.centos.14.noarch
which-2.20-7.el7.x86_64
grub2-pc-modules-2.02-0.87.0.2.el7.centos.14.noarch
libnl3-3.2.28-4.el7.x86_64
kbd-legacy-1.15.5-16.el7_9.noarch
file-libs-5.11-37.el7.x86_64
bash-4.2.46-35.el7_9.x86_64
libmnl-1.0.3-7.el7.x86_64
glibc-common-2.17-326.el7_9.3.x86_64
nspr-4.35.0-1.el7_9.x86_64
p11-kit-0.23.5-3.el7.x86_64
zlib-1.2.7-21.el7_9.x86_64
groff-base-1.22.2-8.el7.x86_64
systemd-libs-219-78.el7_9.9.x86_64
expat-2.1.0-15.el7_9.x86_64
libunistring-0.9.3-9.el7.x86_64
device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64
libedit-3.0-12.20121213cvs.el7.x86_64
freetype-2.8-14.el7_9.1.x86_64
libnfnetlink-1.0.1-4.el7.x86_64
dmidecode-3.2-5.el7_9.1.x86_64
hostname-3.13-3.el7_7.1.x86_64
kernel-tools-libs-3.10.0-1160.119.1.el7.x86_64
lzo-2.06-8.el7.x86_64
linux-firmware-20200421-83.git78c0348.el7_9.noarch
lz4-1.8.3-1.el7.x86_64
openssl-libs-1.0.2k-26.el7_9.x86_64
keyutils-libs-1.5.8-3.el7.x86_64
krb5-libs-1.15.1-55.el7_9.x86_64
slang-2.2.4-11.el7.x86_64
libmount-2.23.2-65.el7_9.1.x86_64
libnetfilter_conntrack-1.0.6-1.el7_3.x86_64
util-linux-2.23.2-65.el7_9.1.x86_64
iproute-4.11.0-30.el7.x86_64
python-2.7.5-94.el7_9.x86_64
libteam-1.29-3.el7.x86_64
python-firewall-0.6.3-13.el7_9.noarch
ipset-libs-7.1-1.el7.x86_64
bind-export-libs-9.11.4-26.P2.el7_9.16.x86_64
acl-2.2.51-15.el7.x86_64
selinux-policy-3.13.1-268.el7_9.2.noarch
tar-1.26-35.el7.x86_64
nss-sysinit-3.90.0-2.el7_9.x86_64
libdb-utils-5.3.21-25.el7.x86_64
NetworkManager-libnm-1.18.8-2.el7_9.x86_64
libss-1.42.9-19.el7.x86_64
libssh2-1.8.0-4.el7_9.1.x86_64
make-3.82-24.el7.x86_64
curl-7.29.0-59.el7_9.2.x86_64
rpm-4.11.3-48.el7_9.x86_64
systemd-219-78.el7_9.9.x86_64
mozjs17-17.0.0-20.el7.x86_64
device-mapper-libs-1.02.170-6.el7_9.5.x86_64
snappy-1.1.0-3.el7.x86_64
grub2-tools-minimal-2.02-0.87.0.2.el7.centos.14.x86_64
libndp-1.2-9.el7.x86_64
grub2-tools-2.02-0.87.0.2.el7.centos.14.x86_64
ustr-1.0.4-16.el7.x86_64
openssh-7.4p1-23.el7_9.x86_64
libtasn1-4.10-1.el7.x86_64
dhcp-common-4.2.5-83.el7.centos.2.x86_64
grub2-pc-2.02-0.87.0.2.el7.centos.14.x86_64
lvm2-libs-2.02.187-6.el7_9.5.x86_64
cronie-1.4.11-25.el7_9.x86_64
wpa_supplicant-2.6-12.el7_9.2.x86_64
rpm-build-libs-4.11.3-48.el7_9.x86_64
python-decorator-3.4.0-3.el7.noarch
firewalld-0.6.3-13.el7_9.noarch
NetworkManager-tui-1.18.8-2.el7_9.x86_64
NetworkManager-wifi-1.18.8-2.el7_9.x86_64
pam-1.1.8-23.el7.x86_64
gettext-0.19.8.1-3.el7.x86_64
yum-metadata-parser-1.1.4-10.el7.x86_64
python-gobject-base-3.22.0-1.el7_4.1.x86_64
python-iniparse-0.4-9.el7.noarch
newt-python-0.52.15-4.el7.x86_64
python-linux-procfs-0.4.11-4.el7.noarch
python-slip-0.4.0-4.el7.noarch
grubby-8.28-26.el7.x86_64
fipscheck-lib-1.4.1-6.el7.x86_64
libuser-0.60-9.el7.x86_64
python-urlgrabber-3.10-10.el7.noarch
libmpc-1.0.1-3.el7.x86_64
centos-logos-70.0.6-3.el7.centos.noarch
libSM-1.2.2-2.el7.x86_64
logrotate-3.8.6-19.el7.x86_64
perl-parent-0.225-244.el7.noarch
libfastjson-0.99.4-3.el7.x86_64
perl-podlators-2.5.1-3.el7.noarch
qrencode-libs-3.4.1-3.el7.x86_64
perl-Pod-Escapes-1.04-299.el7_9.noarch
libpipeline-1.2.3-3.el7.x86_64
perl-Text-ParseWords-3.29-4.el7.noarch
lsscsi-0.27-6.el7.x86_64
perl-macros-5.16.3-299.el7_9.x86_64
numactl-libs-2.0.12-5.el7.x86_64
perl-Time-HiRes-1.9725-3.el7.x86_64
perl-constant-1.27-2.el7.noarch
perl-Time-Local-1.2300-2.el7.noarch
perl-Storable-2.45-3.el7.x86_64
cryptsetup-libs-2.0.3-6.el7.x86_64
perl-PathTools-3.40-5.el7.x86_64
kmod-20-28.el7.x86_64
perl-Filter-1.49-3.el7.x86_64
perl-File-Path-2.09-2.el7.noarch
perl-Pod-Simple-3.28-4.el7.noarch
elfutils-default-yama-scope-0.176-5.el7.noarch
perl-5.16.3-299.el7_9.x86_64
polkit-pkla-compat-0.1-4.el7.x86_64
glibc-headers-2.17-326.el7_9.3.x86_64
libX11-common-1.6.7-5.el7_9.noarch
libxcb-1.13-1.el7.x86_64
policycoreutils-2.5-34.el7.x86_64
libXext-1.3.3-3.el7.x86_64
os-prober-1.58-9.el7.x86_64
libXmu-1.1.2-2.el7.x86_64
kernel-devel-3.10.0-1160.119.1.el7.x86_64
crontabs-1.11-6.20121102git.el7.noarch
perl-Error-0.17020-2.el7.noarch
perl-TermReadKey-2.30-20.el7.x86_64
git-1.8.3.1-25.el7_9.x86_64
python-kitchen-1.1.1-5.el7.noarch
yum-utils-1.1.31-54.el7_8.noarch
libcgroup-0.41-21.el7.x86_64
audit-libs-python-2.8.5-4.el7.x86_64
fxload-2002_04_11-16.el7.x86_64
libsemanage-python-2.5-14.el7.x86_64
alsa-tools-firmware-1.1.0-1.el7.x86_64
docker-compose-plugin-2.27.1-1.el7.x86_64
libpciaccess-0.14-1.el7.x86_64
fuse-overlayfs-0.7.2-6.el7_8.x86_64
teamd-1.29-3.el7.x86_64
policycoreutils-python-2.5-34.el7.x86_64
dbus-python-1.1.1-9.el7.x86_64
plymouth-scripts-0.8.9-0.34.20140113.el7.centos.x86_64
python-pyudev-0.15-9.el7.noarch
containerd.io-1.6.33-3.1.el7.x86_64
pth-2.0.7-23.el7.x86_64
docker-ce-rootless-extras-26.1.4-1.el7.x86_64
epel-release-7-11.noarch
gpgme-1.3.2-5.el7.x86_64
gpg-pubkey-352c64e5-52ae6884
yum-plugin-fastestmirror-1.1.31-54.el7_8.noarch
php-json-7.4.33-15.el7.remi.x86_64
apr-1.4.8-7.el7.x86_64
httpd-tools-2.4.6-99.el7.centos.1.x86_64
mailcap-2.1.41-2.el7.noarch
grub2-2.02-0.87.0.2.el7.centos.14.x86_64
openssh-server-7.4p1-23.el7_9.x86_64
tuned-2.11.0-12.el7_9.noarch
kbd-1.15.5-16.el7_9.x86_64
microcode_ctl-2.1-73.20.el7_9.x86_64
rsyslog-8.24.0-57.el7_9.3.x86_64
selinux-policy-targeted-3.13.1-268.el7_9.2.noarch
openssl-1.0.2k-26.el7_9.x86_64
gzip-1.5-11.el7_9.x86_64
xz-5.2.2-2.el7_9.x86_64
less-458-10.el7_9.x86_64
iwl4965-firmware-228.61.2.24-83.el7_9.noarch
iwl6000g2b-firmware-18.168.6.1-83.el7_9.noarch
iwl3945-firmware-15.32.2.9-83.el7_9.noarch
iwl5150-firmware-8.24.2.2-83.el7_9.noarch
iwl3160-firmware-25.30.13.0-83.el7_9.noarch
iwl100-firmware-39.31.5.1-83.el7_9.noarch
iwl7260-firmware-25.30.13.0-83.el7_9.noarch
iwl6000g2a-firmware-18.168.6.1-83.el7_9.noarch
libsodium-1.0.18-1.el7.x86_64
libjpeg-turbo-1.2.90-8.el7.x86_64
basesystem-10.0-7.el7.centos.noarch
oniguruma5php-6.9.9-1.el7.remi.x86_64
postfix-2.10.1-9.el7.x86_64
libxslt-1.1.28-6.el7.x86_64
aic94xx-firmware-30-6.el7.noarch
fribidi-1.0.2-1.el7_7.1.x86_64
dracut-config-rescue-033-572.el7.x86_64
libmcrypt-2.5.8-13.el7.x86_64
passwd-0.79-6.el7.x86_64
php-pecl-mcrypt-1.0.7-1.el7.remi.7.4.x86_64
e2fsprogs-1.42.9-19.el7.x86_64
php-mbstring-7.4.33-15.el7.remi.x86_64
chkconfig-1.7.6-1.el7.x86_64
gawk-4.0.2-4.el7_3.1.x86_64
libcap-2.22-11.el7.x86_64
libffi-3.0.13-19.el7.x86_64
centos-release-7-9.2009.2.el7.centos.x86_64
tzdata-2024a-1.el7.noarch
file-5.11-37.el7.x86_64
glibc-2.17-326.el7_9.3.x86_64
libnl3-cli-3.2.28-4.el7.x86_64
xz-libs-5.2.2-2.el7_9.x86_64
e2fsprogs-libs-1.42.9-19.el7.x86_64
nss-softokn-3.90.0-6.el7_9.x86_64
libidn-1.28-4.el7.x86_64
libsmartcols-2.23.2-65.el7_9.1.x86_64
sysvinit-tools-2.88-14.dsf.el7.x86_64
diffutils-3.3-6.el7_9.x86_64
gdbm-1.10-8.el7.x86_64
coreutils-8.22-24.el7_9.2.x86_64
pciutils-libs-3.5.1-3.el7.x86_64
glib2-2.56.1-9.el7_9.x86_64
iptables-1.4.21-35.el7.x86_64
python-perf-3.10.0-1160.119.1.el7.x86_64
cyrus-sasl-lib-2.1.26-24.el7_9.x86_64
nss-3.90.0-2.el7_9.x86_64
pinentry-0.8.1-17.el7.x86_64
libcurl-7.29.0-59.el7_9.2.x86_64
libpng-1.5.13-8.el7.x86_64
openldap-2.4.44-25.el7_9.x86_64
ncurses-5.9-14.20130511.el7_4.x86_64
device-mapper-1.02.170-6.el7_9.5.x86_64
polkit-0.112-26.el7_9.1.x86_64
libsemanage-2.5-14.el7.x86_64
grub2-tools-extra-2.02-0.87.0.2.el7.centos.14.x86_64
cronie-anacron-1.4.11-25.el7_9.x86_64
NetworkManager-1.18.8-2.el7_9.x86_64
cracklib-2.9.0-11.el7.x86_64
rpm-python-4.11.3-48.el7_9.x86_64
shared-mime-info-1.8-5.el7.x86_64
lvm2-2.02.187-6.el7_9.5.x86_64
libpwquality-1.2.3-5.el7.x86_64
openssh-clients-7.4p1-23.el7_9.x86_64
pkgconfig-0.27.1-4.el7.x86_64
kernel-3.10.0-1160.119.1.el7.x86_64
libutempter-1.1.6-4.el7.x86_64
sudo-1.8.23-10.el7_9.3.x86_64
python-schedutils-0.4-6.el7.x86_64
binutils-2.27-44.base.el7_9.1.x86_64
libselinux-python-2.5-15.el7.x86_64
iprutils-2.4.17.1-3.el7_7.x86_64
mariadb-libs-5.5.68-1.el7.x86_64
iwl135-firmware-18.168.6.1-83.el7_9.noarch
fipscheck-1.4.1-6.el7.x86_64
iwl105-firmware-18.168.6.1-83.el7_9.noarch
iwl2000-firmware-18.168.6.1-83.el7_9.noarch
iwl1000-firmware-39.31.5.1-83.el7_9.noarch
python-pycurl-7.19.0-19.el7.x86_64
libICE-1.0.9-9.el7.x86_64
alsa-lib-1.1.8-1.el7.x86_64
perl-HTTP-Tiny-0.033-3.el7.noarch
libdaemon-0.14-7.el7.x86_64
perl-Encode-2.51-7.el7.x86_64
libestr-0.1.9-2.el7.x86_64
perl-threads-1.87-4.el7.x86_64
json-c-0.11-4.el7_0.x86_64
perl-Socket-2.010-5.el7.x86_64
perl-threads-shared-1.43-6.el7.x86_64
dracut-033-572.el7.x86_64
perl-File-Temp-0.23.01-3.el7.noarch
dbus-libs-1.10.24-15.el7.x86_64
perl-Getopt-Long-2.40-3.el7.noarch
glibc-devel-2.17-326.el7_9.3.x86_64
libX11-1.6.7-5.el7_9.x86_64
gcc-4.8.5-44.el7.x86_64
rsync-3.1.2-12.el7_9.x86_64
python-chardet-2.2.1-3.el7.noarch
dracut-network-033-572.el7.x86_64
gpg-pubkey-621e9f35-58adea78
slirp4netns-0.4.3-4.el7_8.x86_64
alsa-firmware-1.0.28-2.el7.noarch
fuse3-libs-3.6.1-4.el7.x86_64
libdrm-2.4.97-2.el7.x86_64
container-selinux-2.119.2-1.911c772.el7_8.noarch
python-slip-dbus-0.4.0-4.el7.noarch
plymouth-0.8.9-0.34.20140113.el7.centos.x86_64
docker-ce-26.1.4-1.el7.x86_64
gnupg2-2.0.22-5.el7_5.x86_64
remi-release-7.9-6.el7.remi.noarch
pygpgme-0.3-9.el7.x86_64
php-common-7.4.33-15.el7.remi.x86_64
php-cli-7.4.33-15.el7.remi.x86_64
[root@localhost ~]#
[root@localhost ~]# rpm -ql php
/etc/httpd/conf.d/php.conf
/etc/httpd/conf.modules.d/15-php.conf
/usr/lib64/httpd/modules/libphp7-zts.so
/usr/lib64/httpd/modules/libphp7.so
/var/lib/php/opcache
/var/lib/php/session
/var/lib/php/wsdlcache
[root@localhost ~]#
[root@localhost ~]# rpm -qf `which php`
php-cli-7.4.33-15.el7.remi.x86_64
[root@localhost ~]#
[root@localhost ~]# rpm -qif `which php`
Name        : php-cli
Version     : 7.4.33
Release     : 15.el7.remi
Architecture: x86_64
Install Date: 2024年09月18日 星期三 18时08分59秒
Group       : Development/Languages
Size        : 19162432
License     : PHP and Zend and BSD and MIT and ASL 1.0 and NCSA and PostgreSQL
Signature   : DSA/SHA1, 2024年06月05日 星期三 13时15分37秒, Key ID 004e6f4700f97f56
Source RPM  : php-7.4.33-15.el7.remi.src.rpm
Build Date  : 2024年06月05日 星期三 13时11分38秒
Build Host  : builder.remirepo.net
Relocations : (not relocatable)
Packager    : Remi Collet
Vendor      : Remi's RPM repository <https://rpms.remirepo.net/> #StandWithUkraine
URL         : http://www.php.net/
Bug URL     : https://forum.remirepo.net/
Summary     : Command-line interface for PHP
Description :
The php-cli package contains the command-line interface
executing PHP scripts, /usr/bin/php, and the CGI interface.
[root@localhost ~]#
[root@localhost ~]# rpm -qlf `which php`
/usr/bin/phar
/usr/bin/phar.phar
/usr/bin/php
/usr/bin/php-cgi
/usr/bin/phpize
/usr/bin/zts-php
/usr/share/man/man1/phar.1.gz
/usr/share/man/man1/phar.phar.1.gz
/usr/share/man/man1/php-cgi.1.gz
/usr/share/man/man1/php.1.gz
/usr/share/man/man1/phpize.1.gz
/usr/share/man/man1/zts-php.1.gz
/usr/share/man/man1/zts-phpize.1.gz
[root@localhost ~]#
```

yum-utils是yum的工具包集合，由不同的作者开发，包含了额外的一些实用命令和工具，用于增强yum的功能，使yum使用起来更加方便和强大。

find-repos-of-install 列出包是从哪个yum 仓库安装的；
package-cleanup列出本地安装的，重复的或者orphan(找不到对应的仓库源)的包；
repoclosure模块从多个yum仓库读取包的原信息，查询所有的依赖关系，列出无法解决依赖关系的包；
等等：

```
[root@localhost ~]# find-repos-of-install yum-utils
已加载插件：fastestmirror
yum-utils-1.1.31-54.el7_8.noarch from repo base
[root@localhost ~]#
[root@localhost ~]# find-repos-of-install php
已加载插件：fastestmirror
php-7.4.33-15.el7.remi.x86_64 from repo remi-php74
[root@localhost ~]#
[root@localhost ~]# package-cleanup --problems
已加载插件：fastestmirror
No Problems Found
[root@localhost ~]#
```

## Nginx安装

查看nginx并安装：
> yum list nginx*
> 
> yum install -y nginx

详细：
```bash
[root@localhost ~]# yum search nginx
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
================================== N/S matched: nginx ===================================
collectd-nginx.x86_64 : Nginx plugin for collectd
munin-nginx.noarch : NGINX support for Munin resource monitoring
nginx-all-modules.noarch : A meta package that installs all available Nginx modules
nginx-filesystem.noarch : The basic directory layout for the Nginx server
nginx-mod-devel.x86_64 : Nginx module development files
nginx-mod-http-image-filter.x86_64 : Nginx HTTP image filter module
nginx-mod-http-perl.x86_64 : Nginx HTTP perl module
nginx-mod-http-xslt-filter.x86_64 : Nginx XSLT module
nginx-mod-mail.x86_64 : Nginx mail modules
nginx-mod-stream.x86_64 : Nginx stream modules
pagure-web-nginx.noarch : Nginx configuration for Pagure
pcp-pmda-nginx.x86_64 : Performance Co-Pilot (PCP) metrics for the Nginx Webserver
python2-certbot-nginx.noarch : The nginx plugin for certbot
sympa-nginx.x86_64 : Sympa with nginx
nginx.x86_64 : A high performance web server and reverse proxy server
php54-unit-php.x86_64 : PHP module for NGINX Unit
php55-unit-php.x86_64 : PHP module for NGINX Unit
php56-unit-php.x86_64 : PHP module for NGINX Unit
php70-unit-php.x86_64 : PHP module for NGINX Unit
php71-unit-php.x86_64 : PHP module for NGINX Unit
php72-unit-php.x86_64 : PHP module for NGINX Unit
php73-unit-php.x86_64 : PHP module for NGINX Unit
php74-unit-php.x86_64 : PHP module for NGINX Unit
php80-unit-php.x86_64 : PHP module for NGINX Unit
php81-unit-php.x86_64 : PHP module for NGINX Unit
php82-unit-php.x86_64 : PHP module for NGINX Unit
php83-unit-php.x86_64 : PHP module for NGINX Unit
unit.x86_64 : NGINX Unit application server
unit-php.x86_64 : PHP module for NGINX Unit

  名称和简介匹配 only，使用“search all”试试。
[root@localhost ~]#
[root@localhost ~]# yum list nginx
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
可安装的软件包
nginx.x86_64                             1:1.20.1-10.el7                             epel
[root@localhost ~]#
[root@localhost ~]# yum list nginx*
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
可安装的软件包
nginx.x86_64                                        1:1.20.1-10.el7                  epel
nginx-all-modules.noarch                            1:1.20.1-10.el7                  epel
nginx-filesystem.noarch                             1:1.20.1-10.el7                  epel
nginx-mod-devel.x86_64                              1:1.20.1-10.el7                  epel
nginx-mod-http-image-filter.x86_64                  1:1.20.1-10.el7                  epel
nginx-mod-http-perl.x86_64                          1:1.20.1-10.el7                  epel
nginx-mod-http-xslt-filter.x86_64                   1:1.20.1-10.el7                  epel
nginx-mod-mail.x86_64                               1:1.20.1-10.el7                  epel
nginx-mod-stream.x86_64                             1:1.20.1-10.el7                  epel
[root@localhost ~]#
[root@localhost ~]# yum install -y nginx
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: dl.fedoraproject.org
 * extras: mirrors.aliyun.com
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 nginx.x86_64.1.1.20.1-10.el7 将被 安装
--> 正在处理依赖关系 nginx-filesystem = 1:1.20.1-10.el7，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libcrypto.so.1.1(OPENSSL_1_1_0)(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libssl.so.1.1(OPENSSL_1_1_0)(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libssl.so.1.1(OPENSSL_1_1_1)(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 nginx-filesystem，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 redhat-indexhtml，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libcrypto.so.1.1()(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libprofiler.so.0()(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在处理依赖关系 libssl.so.1.1()(64bit)，它被软件包 1:nginx-1.20.1-10.el7.x86_64 需要
--> 正在检查事务
---> 软件包 centos-indexhtml.noarch.0.7-9.el7.centos 将被 安装
---> 软件包 gperftools-libs.x86_64.0.2.6.1-1.el7 将被 安装
---> 软件包 nginx-filesystem.noarch.1.1.20.1-10.el7 将被 安装
---> 软件包 openssl11-libs.x86_64.1.1.1.1k-7.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

=========================================================================================
 Package                   架构            版本                      源             大小
=========================================================================================
正在安装:
 nginx                     x86_64          1:1.20.1-10.el7           epel          588 k
为依赖而安装:
 centos-indexhtml          noarch          7-9.el7.centos            base           92 k
 gperftools-libs           x86_64          2.6.1-1.el7               base          272 k
 nginx-filesystem          noarch          1:1.20.1-10.el7           epel           24 k
 openssl11-libs            x86_64          1:1.1.1k-7.el7            epel          1.5 M

事务概要
=========================================================================================
安装  1 软件包 (+4 依赖软件包)

总下载量：2.4 M
安装大小：6.7 M
Downloading packages:
(1/5): centos-indexhtml-7-9.el7.centos.noarch.rpm                 |  92 kB  00:00:00
(2/5): gperftools-libs-2.6.1-1.el7.x86_64.rpm                     | 272 kB  00:00:00
(3/5): nginx-filesystem-1.20.1-10.el7.noarch.rpm                  |  24 kB  00:00:01
(4/5): openssl11-libs-1.1.1k-7.el7.x86_64.rpm                     | 1.5 MB  00:00:05
(5/5): nginx-1.20.1-10.el7.x86_64.rpm                             | 588 kB  00:00:51
-----------------------------------------------------------------------------------------
总计                                                         48 kB/s | 2.4 MB  00:51
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : 1:openssl11-libs-1.1.1k-7.el7.x86_64                                 1/5
  正在安装    : 1:nginx-filesystem-1.20.1-10.el7.noarch                              2/5
  正在安装    : centos-indexhtml-7-9.el7.centos.noarch                               3/5
  正在安装    : gperftools-libs-2.6.1-1.el7.x86_64                                   4/5
  正在安装    : 1:nginx-1.20.1-10.el7.x86_64                                         5/5
  验证中      : gperftools-libs-2.6.1-1.el7.x86_64                                   1/5
  验证中      : centos-indexhtml-7-9.el7.centos.noarch                               2/5
  验证中      : 1:nginx-filesystem-1.20.1-10.el7.noarch                              3/5
  验证中      : 1:nginx-1.20.1-10.el7.x86_64                                         4/5
  验证中      : 1:openssl11-libs-1.1.1k-7.el7.x86_64                                 5/5

已安装:
  nginx.x86_64 1:1.20.1-10.el7

作为依赖被安装:
  centos-indexhtml.noarch 0:7-9.el7.centos      gperftools-libs.x86_64 0:2.6.1-1.el7
  nginx-filesystem.noarch 1:1.20.1-10.el7       openssl11-libs.x86_64 1:1.1.1k-7.el7

完毕！
[root@localhost ~]#
[root@localhost ~]# nginx -v
nginx version: nginx/1.20.1
[root@localhost ~]#
```

设置开机自启动：
> systemctl enable nginx

启动：
> systemctl start nginx

```bash
[root@localhost ~]# systemctl enable php-fpm
Created symlink from /etc/systemd/system/multi-user.target.wants/php-fpm.service to /usr/lib/systemd/system/php-fpm.service.
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# systemctl enable nginx
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
[root@localhost ~]#
[root@localhost ~]# systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
[root@localhost ~]#
[root@localhost ~]# systemctl start nginx
[root@localhost ~]#
[root@localhost ~]# systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: active (running) since 四 2024-09-19 10:43:15 CST; 2s ago
  Process: 4144 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 4140 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 4139 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 4147 (nginx)
   CGroup: /system.slice/nginx.service
           ├─4147 nginx: master process /usr/sbin/nginx
           ├─4148 nginx: worker process
           ├─4149 nginx: worker process
           ├─4150 nginx: worker process
           ├─4151 nginx: worker process
           ├─4152 nginx: worker process
           └─4153 nginx: worker process

9月 19 10:43:15 localhost.localdomain systemd[1]: Starting The nginx HTTP and revers....
9月 19 10:43:15 localhost.localdomain nginx[4140]: nginx: the configuration file /et...k
9月 19 10:43:15 localhost.localdomain nginx[4140]: nginx: configuration file /etc/ng...l
9月 19 10:43:15 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse....
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]#
[root@localhost ~]# systemctl stop nginx
[root@localhost ~]#
[root@localhost ~]# systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since 四 2024-09-19 10:43:27 CST; 1s ago
  Process: 4144 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 4140 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 4139 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 4147 (code=exited, status=0/SUCCESS)

9月 19 10:43:15 localhost.localdomain systemd[1]: Starting The nginx HTTP and revers....
9月 19 10:43:15 localhost.localdomain nginx[4140]: nginx: the configuration file /et...k
9月 19 10:43:15 localhost.localdomain nginx[4140]: nginx: configuration file /etc/ng...l
9月 19 10:43:15 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse....
9月 19 10:43:27 localhost.localdomain systemd[1]: Stopping The nginx HTTP and revers....
9月 19 10:43:27 localhost.localdomain systemd[1]: Stopped The nginx HTTP and reverse....
Hint: Some lines were ellipsized, use -l to show in full.
[root@localhost ~]#
```

### nginx配置

查看配置：
> cat /etc/nginx/nginx.conf

文件内容：
```bash
[root@localhost ~]# cat /etc/nginx/nginx.conf
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}

[root@localhost ~]#
```

在文件 `/etc/nginx/nginx.conf` 的 `include /etc/nginx/conf.d/*.conf;` 下加一行 `include /media/sf_develop/vhost/virtualbox/*.conf;`

重启nginx：
> systemctl restart nginx

### 开放指定端口

查看监听端口：
> ss -tunlp

```bash
[root@localhost ~]# ss -tunlp
Netid State      Recv-Q Send-Q Local Address:Port               Peer Address:Port        
udp   UNCONN     0      0              *:68                         *:*                   users:(("dhclient",pid=1040,fd=6))
udp   UNCONN     0      0      127.0.0.1:323                        *:*                   users:(("chronyd",pid=723,fd=5))
udp   UNCONN     0      0          [::1]:323                     [::]:*                   users:(("chronyd",pid=723,fd=6))
tcp   LISTEN     0      128    127.0.0.1:9000                       *:*                   users:(("php-fpm",pid=4199,fd=9),("php-fpm",pid=4198,fd=9),("php-fpm",pid=4197,fd=9),("php-fpm",pid=4196,fd=9),("php-fpm",pid=4195,fd=9),("php-fpm",pid=4194,fd=7))
tcp   LISTEN     0      128            *:80                         *:*                   users:(("nginx",pid=4185,fd=6),("nginx",pid=4184,fd=6),("nginx",pid=4183,fd=6),("nginx",pid=4182,fd=6),("nginx",pid=4181,fd=6),("nginx",pid=4180,fd=6),("nginx",pid=4179,fd=6))
tcp   LISTEN     0      128            *:22                         *:*                   users:(("sshd",pid=1218,fd=3))
tcp   LISTEN     0      100    127.0.0.1:25                         *:*                   users:(("master",pid=1472,fd=13))
tcp   LISTEN     0      128         [::]:80                      [::]:*                   users:(("nginx",pid=4185,fd=7),("nginx",pid=4184,fd=7),("nginx",pid=4183,fd=7),("nginx",pid=4182,fd=7),("nginx",pid=4181,fd=7),("nginx",pid=4180,fd=7),("nginx",pid=4179,fd=7))
tcp   LISTEN     0      128         [::]:22                      [::]:*                   users:(("sshd",pid=1218,fd=4))
tcp   LISTEN     0      100        [::1]:25                      [::]:*                   users:(("master",pid=1472,fd=14))
[root@localhost ~]#
```

查看开放端口的情况：
```bash
> firewall-cmd --list-all
```

```bash
[root@localhost ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@localhost ~]#
```

`services: dhcpv6-client ssh` 表示 ssh 服务是放行的，而 `ports:` 这里为空，表示无端口号放行。

开放 http服务 和 80端口：
```bash
> sudo firewall-cmd --add-service=http --permanent
>
> sudo firewall-cmd --add-port=80/tcp --permanent
```

命令末尾的```--permanent```表示永久有效；不加这句，重启后刚才开放的端口就又失效了。

然后重启防火墙：
```bash
> sudo firewall-cmd --reload
```

再次查看端口的开放情况：
```bash
> firewall-cmd --list-all
```

```bash
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost ~]#
[root@localhost ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client http ssh
  ports: 80/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@localhost ~]#
```

### 权限准备

查看php和nginx进程的用户：
```bash
[root@localhost ~]# ps aux | grep php-fpm
root      4194  0.0  0.2 494608 16844 ?        Ss   10:44   0:05 php-fpm: master process (/etc/php-fpm.conf)
apache    4195  0.0  0.1 494608  7576 ?        S    10:44   0:00 php-fpm: pool www
apache    4196  0.0  0.1 494608  7576 ?        S    10:44   0:00 php-fpm: pool www
apache    4197  0.0  0.1 494608  7576 ?        S    10:44   0:00 php-fpm: pool www
apache    4198  0.0  0.1 494608  7576 ?        S    10:44   0:00 php-fpm: pool www
apache    4199  0.0  0.1 494608  7576 ?        S    10:44   0:00 php-fpm: pool www
root      4575  0.0  0.0 112824   992 pts/0    R+   16:32   0:00 grep --color=auto php-fpm
[root@localhost ~]#
[root@localhost ~]# ps aux | grep nginx
root      4526  0.0  0.0  39312  1052 ?        Ss   15:53   0:00 nginx: master process /usr/sbin/nginx
nginx     4527  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
nginx     4528  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
nginx     4529  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
nginx     4530  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
nginx     4531  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
nginx     4532  0.0  0.0  39700  2060 ?        S    15:53   0:00 nginx: worker process
root      4577  0.0  0.0 112828   988 pts/0    S+   16:32   0:00 grep --color=auto ngin
[root@localhost ~]#
```

php-fpm 的用户是 apache，nginx 的用户是 nginx。

查看服务器中的用户：
```bash
[root@localhost ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
admin:x:1000:1000:admin:/home/admin:/bin/bash
vboxadd:x:997:1::/var/run/vboxadd:/bin/false
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
nginx:x:996:991:Nginx web server:/var/lib/nginx:/sbin/nologin
[root@localhost ~]#
```

查看服务器中的用户组：
```bash
[root@localhost ~]# cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mem:x:8:
kmem:x:9:
wheel:x:10:admin
cdrom:x:11:
mail:x:12:postfix
man:x:15:
dialout:x:18:
floppy:x:19:
games:x:20:
tape:x:33:
video:x:39:
ftp:x:50:
lock:x:54:
audio:x:63:
nobody:x:99:
users:x:100:
utmp:x:22:
utempter:x:35:
input:x:999:
systemd-journal:x:190:
systemd-network:x:192:
dbus:x:81:
polkitd:x:998:
ssh_keys:x:997:
sshd:x:74:
postdrop:x:90:
postfix:x:89:
chrony:x:996:
admin:x:1000:admin
vboxsf:x:995:
vboxdrmipc:x:994:
cgred:x:993:
docker:x:992:
apache:x:48:
nginx:x:991:
[root@localhost ~]#
```

查看下面将会配置的项目配置文件所在目录的用户和用户组：
```bash
[root@localhost ~]# ls -l /media/
总用量 0
drwxr-xr-x. 2 root root   6 9月   3 18:13 cdrom
drwxrwx---. 1 root vboxsf 0 9月  19 15:54 sf_develop
[root@localhost ~]#
```

挂载目录 sf_develop 的用户组是 vboxsf，为了让php和nginx可以读取与执行该目录下的文件，我们需要把php和nginx加入 vboxsf 用户组：
> usermod -aG vboxsf nginx
> 
> usermod -aG vboxsf apache

详细：
```bash
[root@localhost ~]# usermod -aG vboxsf nginx
[root@localhost ~]#
[root@localhost ~]# usermod -aG vboxsf apache
[root@localhost ~]#
[root@localhost ~]# cat /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mem:x:8:
kmem:x:9:
wheel:x:10:admin
cdrom:x:11:
mail:x:12:postfix
man:x:15:
dialout:x:18:
floppy:x:19:
games:x:20:
tape:x:33:
video:x:39:
ftp:x:50:
lock:x:54:
audio:x:63:
nobody:x:99:
users:x:100:
utmp:x:22:
utempter:x:35:
input:x:999:
systemd-journal:x:190:
systemd-network:x:192:
dbus:x:81:
polkitd:x:998:
ssh_keys:x:997:
sshd:x:74:
postdrop:x:90:
postfix:x:89:
chrony:x:996:
admin:x:1000:admin
vboxsf:x:995:nginx,apache
vboxdrmipc:x:994:
cgred:x:993:
docker:x:992:
apache:x:48:
nginx:x:991:
[root@localhost ~]#
[root@localhost ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
admin:x:1000:1000:admin:/home/admin:/bin/bash
vboxadd:x:997:1::/var/run/vboxadd:/bin/false
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
nginx:x:996:991:Nginx web server:/var/lib/nginx:/sbin/nologin
[root@localhost ~]#
```

查看SELinux状态：
```bash
[root@localhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
[root@localhost ~]#
[root@localhost ~]# getenforce
Enforcing
[root@localhost ~]#
```

永久关闭SELINUX：
> vi /etc/selinux/config

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

将`SELINUX=enforcing` 改为 `SELINUX=disabled`

设置后需要重启才能生效。

查看SELinux状态：
```bash
[root@localhost ~]# getenforce
Disabled
[root@localhost ~]#
[root@localhost ~]# sestatus
SELinux status:                 disabled
[root@localhost ~]#
```

### web配置

在 `C:\develop\vhost\virtualbox` 下新建 `test.local.conf` 文件，写入内容：
```
server {
    listen       80;
    server_name  test.local;

    root   /media/sf_develop/www/test;
    index  index.html index.htm;	

    location / {
        try_files $uri $uri/ =404; 
    }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

在 `C:\develop\www\test` 下新建 `index.html` 文件，写入内容：
```
hello world!
```

查看服务器ip：
> ip addr

```bash
[root@localhost ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:ad:5d:64 brd ff:ff:ff:ff:ff:ff
    inet 192.168.110.203/24 brd 192.168.110.255 scope global noprefixroute dynamic enp0s8
       valid_lft 15854sec preferred_lft 15854sec
    inet6 fe80::933d:1aa0:72ae:e8a3/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost ~]#
```

在 `C:\Windows\System32\drivers\etc\hosts` 文件中追加一行：
```
192.168.110.203    test.local
```

重启nginx：
> systemctl restart nginx

浏览器访问 `test.local` 查看效果。

### php项目配置

示例输出 `phpinfo()` 内容。

在 `C:\develop\vhost\virtualbox` 下新建 `phpinfo.local.conf` 文件，写入内容：
```
server {
    listen       80;
    server_name  phpinfo.local;

    root   /media/sf_develop/www/phpinfo;
    index  index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404; 
    }

    location ~ \.php$ {  
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
        include fastcgi_params;  
    }  

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

在 `C:\develop\www\phpinfo` 下新建 `index.php` 文件，写入内容：
```
<?php
phpinfo();
```

在 `C:\Windows\System32\drivers\etc\hosts` 文件中追加一行：
```
192.168.110.203    phpinfo.local
```

重启nginx：
> systemctl restart nginx

浏览器访问 `phpinfo.local` 查看效果。

下面看个伪静态配置的例子：
```
server {
    listen       80;
    server_name  phpinfo.local;

    root   /media/sf_develop/www/phpinfo;
    index  index.php index.html index.htm;

    location / {
        if (!-e $request_filename) {   #支持伪静态，省去index.php?
               rewrite  ^(.*)$  /index.php?s=/$1  last;
               break;
         }
    }

    location ~ \.php$ {  
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_index  index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;     #增加这一句，支持pathinfo
        fastcgi_param PATH_INFO $fastcgi_path_info;    #增加这一句，支持pathinfo
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
        include fastcgi_params;  
    }  

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    access_log  /dev/null;
    error_log  /media/sf_develop/wwwlogs/wxworkadmin.local.error.log;
}
```

## MySQL安装

查看Linux版本：
```bash
[root@localhost ~]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)
[root@localhost ~]#
```

首先我们需要获取分发源的地址（<https://dev.mysql.com/downloads/repo/yum/>）， 安装存储库：
> rpm -Uvh https://repo.mysql.com/mysql84-community-release-el7-1.noarch.rpm

> ll /etc/yum.repos.d/

```
> yum repolist enabled | grep "mysql.*-community.*"
>
> yum repolist all | grep mysql
> 
> yum repolist enabled | grep mysql
```

安装：
> yum install -y mysql-community-server

设置开机自启动：
> systemctl enable mysqld

启动mysql服务：
> systemctl start mysqld

详细：
```base
[root@localhost ~]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# rpm -Uvh https://repo.mysql.com/mysql84-community-release-el7-1.noarch.rpm
获取https://repo.mysql.com/mysql84-community-release-el7-1.noarch.rpm
警告：/var/tmp/rpm-tmp.w09DRK: 头V4 RSA/SHA256 Signature, 密钥 ID a8d3785c: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:mysql84-community-release-el7-1  ################################# [100%]
[root@localhost ~]#
[root@localhost ~]# ll /etc/yum.repos.d/
总用量 120
-rw-r--r--. 1 root root 2523 9月   3 18:00 CentOS-Base.repo
-rw-r--r--. 1 root root 1664 10月 23 2020 CentOS-Base.repo.backup
-rw-r--r--. 1 root root 1309 5月  21 22:48 CentOS-CR.repo
-rw-r--r--. 1 root root  649 5月  21 22:48 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 5月  21 22:48 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 5月  21 22:48 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 5月  21 22:48 CentOS-Sources.repo
-rw-r--r--. 1 root root 9454 5月  21 22:48 CentOS-Vault.repo
-rw-r--r--. 1 root root  616 5月  21 22:48 CentOS-x86_64-kernel.repo
-rw-r--r--. 1 root root 2081 9月  13 14:14 docker-ce.repo
-rw-r--r--. 1 root root  951 10月  3 2017 epel.repo
-rw-r--r--. 1 root root 1050 10月  3 2017 epel-testing.repo
-rw-r--r--  1 root root 3070 4月  22 17:33 mysql-community-debuginfo.repo
-rw-r--r--  1 root root 2751 4月  22 17:33 mysql-community.repo
-rw-r--r--  1 root root 2870 4月  22 17:33 mysql-community-source.repo
-rw-r--r--. 1 root root  855 11月 28 2023 remi-modular.repo
-rw-r--r--. 1 root root  456 11月 28 2023 remi-php54.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php70.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php71.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php72.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php73.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php74.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php80.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php81.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php82.repo
-rw-r--r--. 1 root root 1314 11月 28 2023 remi-php83.repo
-rw-r--r--. 1 root root 2605 11月 28 2023 remi.repo
-rw-r--r--. 1 root root  750 11月 28 2023 remi-safe.repo
[root@localhost ~]#
[root@localhost ~]# yum repolist enabled | grep "mysql.*-community.*"
mysql-8.4-lts-community/x86_64       MySQL 8.4 LTS Community Server           23
mysql-connectors-community/x86_64    MySQL Connectors Community              258
mysql-tools-8.4-lts-community/x86_64 MySQL Tools 8.4 LTS Community             4
[root@localhost ~]#
[root@localhost ~]# yum repolist all | grep mysql
mysql-8.4-lts-community/x86_64                      MySQL 8.4 LTS C 启用:     23
mysql-8.4-lts-community-debuginfo/x86_64            MySQL 8.4 LTS C 禁用
mysql-8.4-lts-community-source                      MySQL 8.4 LTS C 禁用
mysql-cluster-8.0-community/x86_64                  MySQL Cluster 8 禁用
mysql-cluster-8.0-community-debuginfo/x86_64        MySQL Cluster 8 禁用
mysql-cluster-8.0-community-source                  MySQL Cluster 8 禁用
mysql-cluster-8.4-lts-community/x86_64              MySQL Cluster 8 禁用
mysql-cluster-8.4-lts-community-debuginfo/x86_64    MySQL Cluster 8 禁用
mysql-cluster-8.4-lts-community-source              MySQL Cluster 8 禁用
mysql-cluster-innovation-community/x86_64           MySQL Cluster I 禁用
mysql-cluster-innovation-community-debuginfo/x86_64 MySQL Cluster I 禁用
mysql-cluster-innovation-community-source           MySQL Cluster I 禁用
mysql-connectors-community/x86_64                   MySQL Connector 启用:    258
mysql-connectors-community-debuginfo/x86_64         MySQL Connector 禁用
mysql-connectors-community-source                   MySQL Connector 禁用
mysql-innovation-community/x86_64                   MySQL Innovatio 禁用
mysql-innovation-community-debuginfo/x86_64         MySQL Innovatio 禁用
mysql-innovation-community-source                   MySQL Innovatio 禁用
mysql-tools-8.4-lts-community/x86_64                MySQL Tools 8.4 启用:      4
mysql-tools-8.4-lts-community-debuginfo/x86_64      MySQL Tools 8.4 禁用
mysql-tools-8.4-lts-community-source                MySQL Tools 8.4 禁用
mysql-tools-community/x86_64                        MySQL Tools Com 禁用
mysql-tools-community-debuginfo/x86_64              MySQL Tools Com 禁用
mysql-tools-community-source                        MySQL Tools Com 禁用
mysql-tools-innovation-community/x86_64             MySQL Tools Inn 禁用
mysql-tools-innovation-community-debuginfo/x86_64   MySQL Tools Inn 禁用
mysql-tools-innovation-community-source             MySQL Tools Inn 禁用
mysql80-community/x86_64                            MySQL 8.0 Commu 禁用
mysql80-community-debuginfo/x86_64                  MySQL 8.0 Commu 禁用
mysql80-community-source                            MySQL 8.0 Commu 禁用
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# yum repolist enabled | grep mysql
mysql-8.4-lts-community/x86_64       MySQL 8.4 LTS Community Server           23
mysql-connectors-community/x86_64    MySQL Connectors Community              258
mysql-tools-8.4-lts-community/x86_64 MySQL Tools 8.4 LTS Community             4
[root@localhost ~]#
[root@localhost ~]# yum install mysql-community-server
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: dl.fedoraproject.org
 * extras: mirrors.aliyun.com
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 mysql-community-server.x86_64.0.8.4.2-1.el7 将被 安装
--> 正在处理依赖关系 mysql-community-common(x86-64) = 8.4.2-1.el7，它被软件包 mysql-community-server-8.4.2-1.el7.x86_64 需要
--> 正在处理依赖关系 mysql-community-icu-data-files = 8.4.2-1.el7，它被软件包 mysql-community-server-8.4.2-1.el7.x86_64 需要
--> 正在处理依赖关系 mysql-community-client(x86-64) >= 8.0.11，它被软件包 mysql-community-server-8.4.2-1.el7.x86_64 需要
--> 正在检查事务
---> 软件包 mysql-community-client.x86_64.0.8.4.2-1.el7 将被 安装
--> 正在处理依赖关系 mysql-community-client-plugins = 8.4.2-1.el7，它被软件包 mysql-community-client-8.4.2-1.el7.x86_64 需要
--> 正在处理依赖关系 mysql-community-libs(x86-64) >= 8.0.11，它被软件包 mysql-community-client-8.4.2-1.el7.x86_64 需要
---> 软件包 mysql-community-common.x86_64.0.8.4.2-1.el7 将被 安装
---> 软件包 mysql-community-icu-data-files.x86_64.0.8.4.2-1.el7 将被 安装
--> 正在检查事务
---> 软件包 mariadb-libs.x86_64.1.5.5.68-1.el7 将被 取代
--> 正在处理依赖关系 libmysqlclient.so.18()(64bit)，它被软件包 2:postfix-2.10.1-9.el7.x86_64 需要
--> 正在处理依赖关系 libmysqlclient.so.18(libmysqlclient_18)(64bit)，它被软件包 2:postfix-2.10.1-9.el7.x86_64 需要
---> 软件包 mysql-community-client-plugins.x86_64.0.8.4.2-1.el7 将被 安装
---> 软件包 mysql-community-libs.x86_64.0.8.4.2-1.el7 将被 舍弃
--> 正在检查事务
---> 软件包 mysql-community-libs-compat.x86_64.0.8.4.2-1.el7 将被 舍弃
--> 解决依赖关系完成

依赖关系解决

==========================================================================================
 Package                          架构     版本           源                         大小
==========================================================================================
正在安装:
 mysql-community-libs             x86_64   8.4.2-1.el7    mysql-8.4-lts-community   1.5 M
      替换  mariadb-libs.x86_64 1:5.5.68-1.el7
 mysql-community-libs-compat      x86_64   8.4.2-1.el7    mysql-8.4-lts-community   671 k
      替换  mariadb-libs.x86_64 1:5.5.68-1.el7
 mysql-community-server           x86_64   8.4.2-1.el7    mysql-8.4-lts-community    61 M
为依赖而安装:
 mysql-community-client           x86_64   8.4.2-1.el7    mysql-8.4-lts-community    15 M
 mysql-community-client-plugins   x86_64   8.4.2-1.el7    mysql-8.4-lts-community   3.5 M
 mysql-community-common           x86_64   8.4.2-1.el7    mysql-8.4-lts-community   690 k
 mysql-community-icu-data-files   x86_64   8.4.2-1.el7    mysql-8.4-lts-community   2.2 M

事务概要
==========================================================================================
安装  3 软件包 (+4 依赖软件包)

总下载量：84 M
Is this ok [y/d/N]: y
Downloading packages:
警告：/var/cache/yum/x86_64/7/mysql-8.4-lts-community/packages/mysql-community-client-plugins-8.4.2-1.el7.x86_64.rpm: 头V4 RSA/SHA256 Signature, 密钥 ID a8d3785c: NOKEY
mysql-community-client-plugins-8.4.2-1.el7.x86_64.rpm 的公钥尚未安装
(1/7): mysql-community-client-plugins-8.4.2-1.el7.x86_64.rpm       | 3.5 MB  00:00:07
(2/7): mysql-community-common-8.4.2-1.el7.x86_64.rpm               | 690 kB  00:00:01
(3/7): mysql-community-icu-data-files-8.4.2-1.el7.x86_64.rpm       | 2.2 MB  00:00:04
(4/7): mysql-community-libs-8.4.2-1.el7.x86_64.rpm                 | 1.5 MB  00:00:02
(5/7): mysql-community-libs-compat-8.4.2-1.el7.x86_64.rpm          | 671 kB  00:00:01
(6/7): mysql-community-client-8.4.2-1.el7.x86_64.rpm               |  15 MB  00:00:28
(7/7): mysql-community-server-8.4.2-1.el7.x86_64.rpm               |  61 MB  00:01:04
------------------------------------------------------------------------------------------
总计                                                      1.0 MB/s |  84 MB  00:01:22
从 file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023 检索密钥
导入 GPG key 0xA8D3785C:
 用户ID     : "MySQL Release Engineering <mysql-build@oss.oracle.com>"
 指纹       : bca4 3417 c3b4 85dd 128e c6d4 b7b3 b788 a8d3 785c
 软件包     : mysql84-community-release-el7-1.noarch (installed)
 来自       : /etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2023
是否继续？[y/N]：y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告：RPM 数据库已被非 yum 程序修改。
  正在安装    : mysql-community-client-plugins-8.4.2-1.el7.x86_64                     1/8
  正在安装    : mysql-community-common-8.4.2-1.el7.x86_64                             2/8
  正在安装    : mysql-community-libs-8.4.2-1.el7.x86_64                               3/8
  正在安装    : mysql-community-client-8.4.2-1.el7.x86_64                             4/8
  正在安装    : mysql-community-icu-data-files-8.4.2-1.el7.x86_64                     5/8
  正在安装    : mysql-community-server-8.4.2-1.el7.x86_64                             6/8
  正在安装    : mysql-community-libs-compat-8.4.2-1.el7.x86_64                        7/8
  正在删除    : 1:mariadb-libs-5.5.68-1.el7.x86_64                                    8/8
  验证中      : mysql-community-common-8.4.2-1.el7.x86_64                             1/8
  验证中      : mysql-community-libs-8.4.2-1.el7.x86_64                               2/8
  验证中      : mysql-community-client-8.4.2-1.el7.x86_64                             3/8
  验证中      : mysql-community-server-8.4.2-1.el7.x86_64                             4/8
  验证中      : mysql-community-libs-compat-8.4.2-1.el7.x86_64                        5/8
  验证中      : mysql-community-client-plugins-8.4.2-1.el7.x86_64                     6/8
  验证中      : mysql-community-icu-data-files-8.4.2-1.el7.x86_64                     7/8
  验证中      : 1:mariadb-libs-5.5.68-1.el7.x86_64                                    8/8

已安装:
  mysql-community-libs.x86_64 0:8.4.2-1.el7
  mysql-community-libs-compat.x86_64 0:8.4.2-1.el7
  mysql-community-server.x86_64 0:8.4.2-1.el7

作为依赖被安装:
  mysql-community-client.x86_64 0:8.4.2-1.el7
  mysql-community-client-plugins.x86_64 0:8.4.2-1.el7
  mysql-community-common.x86_64 0:8.4.2-1.el7
  mysql-community-icu-data-files.x86_64 0:8.4.2-1.el7

替代:
  mariadb-libs.x86_64 1:5.5.68-1.el7

完毕！
[root@localhost ~]#
[root@localhost ~]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
[root@localhost ~]#
[root@localhost ~]# systemctl enable mysqld
[root@localhost ~]#
[root@localhost ~]# systemctl start mysqld
[root@localhost ~]#
[root@localhost ~]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since 五 2024-09-20 18:04:03 CST; 4s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 2330 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 2403 (mysqld)
   Status: "Server is operational"
   CGroup: /system.slice/mysqld.service
           └─2403 /usr/sbin/mysqld

9月 20 18:03:49 localhost.localdomain systemd[1]: Starting MySQL Server...
9月 20 18:04:03 localhost.localdomain systemd[1]: Started MySQL Server.
[root@localhost ~]#
```

### 设置root账户远程登录

查看root账户临时密码（这里是 Gy8,KLM4fpJj）：
> grep 'temporary password' /var/log/mysqld.log

登录mysql：
> mysql -u root -p

查看有哪些表，提示要先修改用户密码（设置为 Root.1234）：
> alter user user() identified by "Root.1234";

查看有哪些表：
> show databases;

查看版本：
> select @@version;

修改用户登录权限（`%` 表示可以远程登录，`localhost` 表示只允许本地登录，也可以是具体IP地址），使用mysql库，授权然后刷新：
> USE mysql;
> 
> update user set host='%' where user='root';
> 
> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
> 
> FLUSH PRIVILEGES;

详细：
```base
[root@localhost ~]# grep 'temporary password' /var/log/mysqld.log
2024-09-20T10:03:55.795569Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: Gy8,KLM4fpJj
[root@localhost ~]#
[root@localhost ~]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.4.2

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
mysql> SHOW DATABASES;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql>
mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql>
mysql> alter user user() identified by "Root.1234";
Query OK, 0 rows affected (0.08 sec)

mysql>
mysql> set password='Root.1234';
Query OK, 0 rows affected (0.03 sec)

mysql>
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.14 sec)

mysql>
mysql> select @@version;
+-----------+
| @@version |
+-----------+
| 8.4.2     |
+-----------+
1 row in set (0.00 sec)

mysql>
mysql> USE mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql>
mysql> update user set host='%' where user='root';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 1  Changed: 0  Warnings: 0

mysql>
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.04 sec)

mysql>
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> exit
Bye
[root@localhost ~]#
```

### 开放指定端口

防火墙添加 mysql服务 和 3306端口，然后重启防火墙：
```
> firewall-cmd --permanent --zone=public --add-service=mysql
> 
> firewall-cmd --permanent --zone=public --add-port=3306/tcp
> 
> firewall-cmd --reload
```

详细：
```base
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-service=mysql
success
[root@localhost ~]#
[root@localhost ~]# firewall-cmd --permanent --zone=public --add-port=3306/tcp
success
[root@localhost ~]#
[root@localhost ~]# firewall-cmd --reload
success
[root@localhost ~]#
[root@localhost ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client http mysql ssh
  ports: 80/tcp 3306/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@localhost ~]#
```

远程使用账号 root 和密码 Root.1234 登录看一下。


## Nodejs和Npm安装

Node.js是javascript的一种运行环境，是对Google V8引擎进行的封装，是一个服务器端的javascript的解释器。
Npm是Node.js的包管理器（package manager），和PHP中的Composer类似，Npm会根据依赖关系，把所有依赖的包都下载下来。
Npm是作为Node.js的一部分被打包发布的。

查看nodejs包：
> yum list nodejs

安装：
> yum install -y nodejs

查看Node.js的版本：
> node -v

查看Npm的版本：
> npm -v

详细：
```
[root@localhost ~]# yum list nodejs
已加载插件：fastestmirror
Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
可安装的软件包
nodejs.x86_64                        1:16.20.2-1.el7                        epel
[root@localhost ~]#
[root@localhost ~]# yum install -y nodejs
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                     | 5.1 kB     00:00
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
base                                                     | 3.6 kB     00:00
docker-ce-stable                                         | 3.5 kB     00:00
extras                                                   | 2.9 kB     00:00
mysql-8.4-lts-community                                  | 2.6 kB     00:00
mysql-connectors-community                               | 2.6 kB     00:00
mysql-tools-8.4-lts-community                            | 2.6 kB     00:00
nux-dextop                                               | 2.9 kB     00:00
remi-safe                                                | 3.0 kB     00:00
updates                                                  | 2.9 kB     00:00
(1/3): mysql-tools-8.4-lts-community/x86_64/primary_db     |  10 kB   00:00
(2/3): mysql-8.4-lts-community/x86_64/primary_db           |  33 kB   00:00
(3/3): mysql-connectors-community/x86_64/primary_db        | 121 kB   00:01
正在解决依赖关系
--> 正在检查事务
---> 软件包 nodejs.x86_64.1.16.20.2-1.el7 将被 安装
--> 正在处理依赖关系 nodejs-libs(x86-64) = 1:16.20.2-1.el7，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 libuv >= 1:1.43.0，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 openssl11 >= 1:1.1.1，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 libbrotlidec.so.1()(64bit)，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 libbrotlienc.so.1()(64bit)，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 libnode.so.93()(64bit)，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在处理依赖关系 libuv.so.1()(64bit)，它被软件包 1:nodejs-16.20.2-1.el7.x86_64 需要
--> 正在检查事务
---> 软件包 libbrotli.x86_64.0.1.0.9-10.el7 将被 安装
---> 软件包 libuv.x86_64.1.1.44.2-1.el7 将被 安装
---> 软件包 nodejs-libs.x86_64.1.16.20.2-1.el7 将被 安装
---> 软件包 openssl11.x86_64.1.1.1.1k-7.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package             架构           版本                     源            大小
================================================================================
正在安装:
 nodejs              x86_64         1:16.20.2-1.el7          epel          96 k
为依赖而安装:
 libbrotli           x86_64         1.0.9-10.el7             epel         308 k
 libuv               x86_64         1:1.44.2-1.el7           epel         145 k
 nodejs-libs         x86_64         1:16.20.2-1.el7          epel          13 M
 openssl11           x86_64         1:1.1.1k-7.el7           epel         694 k

事务概要
================================================================================
安装  1 软件包 (+4 依赖软件包)

总下载量：14 M
安装大小：52 M
Downloading packages:
(1/5): libbrotli-1.0.9-10.el7.x86_64.rpm                   | 308 kB   00:01
(2/5): libuv-1.44.2-1.el7.x86_64.rpm                       | 145 kB   00:05
(3/5): nodejs-16.20.2-1.el7.x86_64.rpm                     |  96 kB   00:09
(4/5): openssl11-1.1.1k-7.el7.x86_64.rpm                   | 694 kB   00:50
(5/5): nodejs-libs-16.20.2-1.el7.x86_64.rpm                |  13 MB   05:26
--------------------------------------------------------------------------------
总计                                                45 kB/s |  14 MB  05:26
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : 1:libuv-1.44.2-1.el7.x86_64                                 1/5
  正在安装    : libbrotli-1.0.9-10.el7.x86_64                               2/5
  正在安装    : 1:nodejs-libs-16.20.2-1.el7.x86_64                          3/5
  正在安装    : 1:openssl11-1.1.1k-7.el7.x86_64                             4/5
  正在安装    : 1:nodejs-16.20.2-1.el7.x86_64                               5/5
  验证中      : 1:nodejs-libs-16.20.2-1.el7.x86_64                          1/5
  验证中      : 1:nodejs-16.20.2-1.el7.x86_64                               2/5
  验证中      : libbrotli-1.0.9-10.el7.x86_64                               3/5
  验证中      : 1:libuv-1.44.2-1.el7.x86_64                                 4/5
  验证中      : 1:openssl11-1.1.1k-7.el7.x86_64                             5/5

已安装:
  nodejs.x86_64 1:16.20.2-1.el7

作为依赖被安装:
  libbrotli.x86_64 0:1.0.9-10.el7          libuv.x86_64 1:1.44.2-1.el7
  nodejs-libs.x86_64 1:16.20.2-1.el7       openssl11.x86_64 1:1.1.1k-7.el7

完毕！
[root@localhost ~]#
[root@localhost ~]# node -v
v16.20.2
[root@localhost ~]#
[root@localhost ~]# npm -v
-bash: npm: 未找到命令
[root@localhost ~]#
```

### npm安装

有些情况下npm未被node内置，需要自己安装：
> yum list npm
> 
> yum install -y npm
>
> npm -v

详细：
```
[root@localhost nodejs]# yum list npm
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
可安装的软件包
npm.x86_64                     1:8.19.4-1.16.20.2.1.el7                     epel
[root@localhost nodejs]#
[root@localhost nodejs]# yum install -y npm
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: mirror.li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 npm.x86_64.1.8.19.4-1.16.20.2.1.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package     架构           版本                             源            大小
================================================================================
正在安装:
 npm         x86_64         1:8.19.4-1.16.20.2.1.el7         epel         2.1 M

事务概要
================================================================================
安装  1 软件包

总下载量：2.1 M
安装大小：8.0 M
Downloading packages:
npm-8.19.4-1.16.20.2.1.el7.x86_64.rpm                      | 2.1 MB   00:09
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : 1:npm-8.19.4-1.16.20.2.1.el7.x86_64                         1/1
  验证中      : 1:npm-8.19.4-1.16.20.2.1.el7.x86_64                         1/1

已安装:
  npm.x86_64 1:8.19.4-1.16.20.2.1.el7

完毕！
[root@localhost nodejs]#
[root@localhost nodejs]# npm -v
8.19.4
[root@localhost nodejs]#
```

npm默认是国外源，速度比较慢，需要切换国内源。国内最常用的npm镜像有淘宝源：
> npm set registry https://registry.npmmirror.com/

详细：
```
[root@localhost nodejs]# npm get registry
https://registry.npmjs.org/
[root@localhost nodejs]#
[root@localhost nodejs]# npm set registry https://registry.npmmirror.com/
[root@localhost nodejs]#
[root@localhost nodejs]# npm get registry
https://registry.npmmirror.com/
[root@localhost nodejs]#
```

在Virtualbox中运行`npm install`或者`npm ci`过程中会报错：`npm ERR! syscall symlink`。
原因是npm默认会使用symbolic links来安装组件，也即我们在linux系统里经常用的软链接（ln -s）或硬链接(ln)，
但是有些文件系统不支持文件链接，比如Windows（或者说出于安全原因禁用了），
有时候linux系统运行在有windows分区的硬盘上也不支持链接，比如virtualbox在windows主机上默认禁止在共享文件夹里使用symbolic links，
当然也是出于安全考虑。

解决办法，就是在`npm install`的时候加上一个参数，就是```--no-bin-links```，也即```npm install --no-bin-links```。

### 提示

这里拿出来重点提示一下，如果是纯前端项目，在Windows系统中进行开发调试会更有效率。

### package.json文件说明

package.json文件中是项目的描述，里面有项目基本信息、执行方式、依赖包等。

在`scripts`中是项目的执行方式，里面会用到一些npm的全局脚本，如 vue-cli、cross-env、Webpack、tailwind等。

这些全局脚本或者依赖包可以在 npm官网 <https://www.npmjs.com/> 搜索，也可以在命令行使用`npm search 包名`命令搜索。

项目中安装包，使用`npm install`命令，全局安装使用`npm install -g 包名`命令。

安装完成后使用```npm list --global```命令查看安装结果。

卸载包使用`npm rm 包名`命令。

### cross-env安装

cross-env是一个在Node.js项目中广泛使用的npm包，它主要用于设置跨平台的环境变量。
cross-env的核心目标是解决在Node.js项目中设置环境变量时遇到的平台兼容性问题。
在Windows系统中，环境变量的设置与Unix/Linux系统有很大区别，这可能导致在不同的操作系统上运行同一脚本时出现问题。
cross-env通过提供一个统一的命令语法，使得开发者无需关心具体的操作系统，就能轻松设置环境变量。
它允许你在命令行中设置环境变量，无论你是在Windows、Linux还是macOS上工作，都能确保环境变量的设定保持一致。

cross-env全局安装：
> npm install -g cross-env

详细：
```
[root@localhost ~]# npm search cross-env
NAME                      | DESCRIPTION          | AUTHOR          | DATE       | VERSION
cross-env                 | Run scripts that…    | =kentcdodds     | 2020-12-01 | 7.0.3
path-key                  | Get the PATH…        | =sindresorhus   | 2021-04-09 | 4.0.0
env-variable              | Cross platform…      | =3rdeden =v1    | 2020-01-29 | 0.0.6
cross-blob                | Cross-platform Blob… | =richienb       | 2022-10-18 | 3.0.2
cross-env-argv            | > More convenient…   | =joenix         | 2023-06-05 | 1.1.9
@flatfile/cross-env-confi | A helper for…        | =mmccooyyy…     | 2024-05-02 | 0.0.6
g                         |                      |                 |            |
path-name                 | Cross-platform PATH… | =zkochan        | 2016-10-20 | 1.0.0
zhi-env                   | a cross-platform…    | =terwer-org     | 2023-06-11 | 1.18.6
cross-env-os              | Run scripts that…    | =chestershen    | 2022-05-26 | 7.1.1
cross-conf-env            | To cross-platform…   | =akabeko        | 2022-07-24 | 1.3.0
cross-os                  | Allow to add…        | =milewski       | 2022-06-17 | 1.5.0
@esm2cjs/path-key         | Get the PATH…        | =alcalzone      | 2022-08-18 | 4.0.0
cross-env-file            | Run scripts that…    | =marcojakob     | 2018-10-24 | 1.0.0
cross-ci                  | `cross-ci`…          | =streamich      | 2023-05-18 | 1.7.0
@w72/cross-env            | cross-env rewrite    | =w72            | 2023-06-14 | 1.0.2
@encoderinc/cross-env     | <div…                | =superv1sion…   | 2022-01-14 | 1.0.11
env-cross                 | security holding…    | =npm…           | 2023-07-04 | 0.0.1-s…
python-env                | Cross platform…      | =codejamninja   | 2019-01-23 | 0.2.8
run-with-node-env         | Set the `NODE_ENV`…  | =magiclen       | 2024-10-22 | 2.0.1
ilib-env                  | Common environment…  | =ehoogerbeets   | 2024-02-11 | 1.4.0
[root@localhost ~]#
[root@localhost ~]# npm install -g cross-env

added 7 packages in 2s
[root@localhost ~]#
[root@localhost ~]# npm list --global
/usr/local/lib
├── cross-env@7.0.3
├── tailwindcss@3.4.14
├── webpack-cli@5.1.4
└── webpack@5.96.0

[root@localhost ~]#
[root@localhost ~]# npm list -g cross-env
/usr/local/lib
└── cross-env@7.0.3

[root@localhost ~]#
```

### Webpack安装

WebPack是一款模块加载器兼打包工具，它能把各种资源，如JS、JSX、ES6、SASS、LESS、图片等都作为模块来处理和使用。

> npm install webpack -g
> 
> npm install webpack-cli -g

## FFmpeg安装

安装Nux Dextop Yum 源：
> rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm

安装：
> yum install -y ffmpeg ffmpeg-devel

查看版本：
> ffmpeg -version

详细：
```base
[root@localhost ~]# rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
获取http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
警告：/var/tmp/rpm-tmp.exmaQ8: 头V4 RSA/SHA1 Signature, 密钥 ID 85c6cd8a: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:nux-dextop-release-0-5.el7.nux   ################################# [100%]
[root@localhost ~]#
[root@localhost ~]# yum search ffmpeg
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
=================================== N/S matched: ffmpeg ===================================
ffmpeg-compat-debuginfo.x86_64 : Debug information for package ffmpeg-compat
ffmpeg-compat-devel.x86_64 : Development package for ffmpeg-compat
ffmpeg-debuginfo.x86_64 : Debug information for package ffmpeg
ffmpeg-devel.x86_64 : Development package for ffmpeg
ffmpeg-libs.x86_64 : Libraries for ffmpeg
ffmpeg2theora-debuginfo.x86_64 : Debug information for package ffmpeg2theora
ffmpegthumbnailer-debuginfo.x86_64 : Debug information for package ffmpegthumbnailer
ffmpegthumbnailer-devel.x86_64 : Headers and libraries for building apps that use
                               : ffmpegthumbnailer
gstreamer-ffmpeg.x86_64 : GStreamer FFmpeg-based plug-ins
gstreamer-ffmpeg-debuginfo.x86_64 : Debug information for package gstreamer-ffmpeg
kffmpegthumbnailer.x86_64 : A video thumbnailer for kde based on ffmpegthumbnailer
kffmpegthumbnailer-debuginfo.x86_64 : Debug information for package kffmpegthumbnailer
mlt-ffmpeg.x86_64 : FFMpeg package for MLT
mythffmpeg.x86_64 : MythTV build of FFmpeg
audacious-plugins-freeworld-ffaudio.x86_64 : FFMpeg/FAAD2 based input plugin for Audacious
ffmpeg.x86_64 : Digital VCR and streaming server
ffmpeg-compat.x86_64 : Digital VCR and streaming server
ffmpeg2theora.x86_64 : Simple converter to create Ogg Theora files
ffmpegthumbnailer.x86_64 : Lightweight video thumbnailer that can be used by file managers
ffms2.x86_64 : Wrapper library around libffmpeg
nv-codec-headers.noarch : FFmpeg version of Nvidia Codec SDK headers
winff.x86_64 : Graphical video and audio batch converter using ffmpeg

  名称和简介匹配 only，使用“search all”试试。
[root@localhost ~]#
[root@localhost ~]# yum list ffmpeg
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
已安装的软件包
ffmpeg.x86_64                         2.8.15-2.el7.nux                          @nux-dextop
[root@localhost ~]#
[root@localhost ~]# yum install -y ffmpeg ffmpeg-devel
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                | 5.1 kB  00:00:00
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
base                                                                | 3.6 kB  00:00:00
docker-ce-stable                                                    | 3.5 kB  00:00:00
extras                                                              | 2.9 kB  00:00:00
mysql-8.4-lts-community                                             | 2.6 kB  00:00:00
mysql-connectors-community                                          | 2.6 kB  00:00:00
mysql-tools-8.4-lts-community                                       | 2.6 kB  00:00:00
nux-dextop                                                          | 2.9 kB  00:00:00
https://mirror.ihost.md/remi/enterprise/7/safe/x86_64/repodata/repomd.xml: [Errno 14] HTTPS Error 403 - Forbidden
正在尝试其它镜像。
To address this issue please refer to the below wiki article

https://wiki.centos.org/yum-errors

If above article doesn't help to resolve this issue please use https://bugs.centos.org/.

remi-safe                                                           | 3.0 kB  00:00:00
updates                                                             | 2.9 kB  00:00:00
nux-dextop/x86_64/primary_db                                        | 1.8 MB  00:01:53
正在解决依赖关系
--> 正在检查事务
---> 软件包 ffmpeg.x86_64.0.2.8.15-2.el7.nux 将被 安装
--> 正在处理依赖关系 ffmpeg-libs(x86-64) = 2.8.15-2.el7.nux，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libswscale.so.3(LIBSWSCALE_3)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libswresample.so.1(LIBSWRESAMPLE_1)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libpostproc.so.53(LIBPOSTPROC_53)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavutil.so.54(LIBAVUTIL_54)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavresample.so.2(LIBAVRESAMPLE_2)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavformat.so.56(LIBAVFORMAT_56)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavfilter.so.5(LIBAVFILTER_5)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavdevice.so.56(LIBAVDEVICE_56)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavcodec.so.56(LIBAVCODEC_56)(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libvdpau.so.1()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libswscale.so.3()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libswresample.so.1()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libpostproc.so.53()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavutil.so.54()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavresample.so.2()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavformat.so.56()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavfilter.so.5()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavdevice.so.56()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libavcodec.so.56()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libSDL-1.2.so.0()(64bit)，它被软件包 ffmpeg-2.8.15-2.el7.nux.x86_64 需要
---> 软件包 ffmpeg-devel.x86_64.0.2.8.15-2.el7.nux 将被 安装
--> 正在检查事务
---> 软件包 SDL.x86_64.0.1.2.15-17.el7 将被 安装
---> 软件包 ffmpeg-libs.x86_64.0.2.8.15-2.el7.nux 将被 安装
--> 正在处理依赖关系 libtheoraenc.so.1(libtheoraenc_1.0)(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libtheoradec.so.1(libtheoradec_1.0)(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libgnutls.so.28(GNUTLS_3_0_0)(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libgnutls.so.28(GNUTLS_1_4)(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libxvidcore.so.4()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libx265.so.79()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libx264.so.142()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libvorbisenc.so.2()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libvorbis.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libvo-amrwbenc.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libva.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libtheoraenc.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libtheoradec.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libspeex.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libsoxr.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libschroedinger-1.0.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libopus.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libopenjpeg.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libopencore-amrwb.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libopencore-amrnb.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libmp3lame.so.0()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libgsm.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libgnutls.so.28()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libfdk-aac.so.1()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libass.so.5()(64bit)，它被软件包 ffmpeg-libs-2.8.15-2.el7.nux.x86_64 需要
---> 软件包 libavdevice.x86_64.0.2.8.15-2.el7.nux 将被 安装
--> 正在处理依赖关系 libpulse.so.0(PULSE_0)(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libcdio_paranoia.so.1(CDIO_PARANOIA_1)(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libcdio_cdda.so.1(CDIO_CDDA_1)(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libv4l2.so.0()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libpulse.so.0()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libopenal.so.1()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libdc1394.so.22()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libcdio_paranoia.so.1()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libcdio_cdda.so.1()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
--> 正在处理依赖关系 libXv.so.1()(64bit)，它被软件包 libavdevice-2.8.15-2.el7.nux.x86_64 需要
---> 软件包 libvdpau.x86_64.0.1.1.1-3.el7 将被 安装
--> 正在检查事务
---> 软件包 fdk-aac.x86_64.0.0.1.4-1 将被 安装
---> 软件包 gnutls.x86_64.0.3.3.29-9.el7_6 将被 安装
--> 正在处理依赖关系 trousers >= 0.3.11.2，它被软件包 gnutls-3.3.29-9.el7_6.x86_64 需要
--> 正在处理依赖关系 libnettle.so.4()(64bit)，它被软件包 gnutls-3.3.29-9.el7_6.x86_64 需要
--> 正在处理依赖关系 libhogweed.so.2()(64bit)，它被软件包 gnutls-3.3.29-9.el7_6.x86_64 需要
---> 软件包 gsm.x86_64.0.1.0.13-11.el7 将被 安装
---> 软件包 lame-libs.x86_64.0.3.100-1.el7 将被 安装
---> 软件包 libXv.x86_64.0.1.0.11-1.el7 将被 安装
---> 软件包 libass.x86_64.0.0.13.4-6.el7 将被 安装
---> 软件包 libcdio-paranoia.x86_64.0.10.2+0.90-11.el7 将被 安装
--> 正在处理依赖关系 libcdio.so.15(CDIO_15)(64bit)，它被软件包 libcdio-paranoia-10.2+0.90-11.el7.x86_64 需要
--> 正在处理依赖关系 libcdio.so.15()(64bit)，它被软件包 libcdio-paranoia-10.2+0.90-11.el7.x86_64 需要
---> 软件包 libdc1394.x86_64.0.2.2.2-3.el7 将被 安装
--> 正在处理依赖关系 libraw1394.so.11()(64bit)，它被软件包 libdc1394-2.2.2-3.el7.x86_64 需要
--> 正在处理依赖关系 libusb-1.0.so.0()(64bit)，它被软件包 libdc1394-2.2.2-3.el7.x86_64 需要
---> 软件包 libtheora.x86_64.1.1.1.1-8.el7 将被 安装
--> 正在处理依赖关系 libogg.so.0()(64bit)，它被软件包 1:libtheora-1.1.1-8.el7.x86_64 需要
---> 软件包 libv4l.x86_64.0.0.9.5-4.el7 将被 安装
---> 软件包 libva.x86_64.0.1.8.3-1.el7 将被 安装
--> 正在处理依赖关系 libwayland-client.so.0()(64bit)，它被软件包 libva-1.8.3-1.el7.x86_64 需要
--> 正在处理依赖关系 libXfixes.so.3()(64bit)，它被软件包 libva-1.8.3-1.el7.x86_64 需要
--> 正在处理依赖关系 libGL.so.1()(64bit)，它被软件包 libva-1.8.3-1.el7.x86_64 需要
--> 正在处理依赖关系 libEGL.so.1()(64bit)，它被软件包 libva-1.8.3-1.el7.x86_64 需要
---> 软件包 libvorbis.x86_64.1.1.3.3-8.el7.1 将被 安装
---> 软件包 openal-soft.x86_64.0.1.16.0-3.el7 将被 安装
---> 软件包 opencore-amr.x86_64.0.0.1.3-3.el7.nux 将被 安装
---> 软件包 openjpeg-libs.x86_64.0.1.5.1-18.el7 将被 安装
---> 软件包 opus.x86_64.0.1.0.2-6.el7 将被 安装
---> 软件包 pulseaudio-libs.x86_64.0.10.0-6.el7_9 将被 安装
--> 正在处理依赖关系 libsndfile.so.1(libsndfile.so.1.0)(64bit)，它被软件包 pulseaudio-libs-10.0-6.el7_9.x86_64 需要
--> 正在处理依赖关系 libsndfile.so.1()(64bit)，它被软件包 pulseaudio-libs-10.0-6.el7_9.x86_64 需要
--> 正在处理依赖关系 libasyncns.so.0()(64bit)，它被软件包 pulseaudio-libs-10.0-6.el7_9.x86_64 需要
--> 正在处理依赖关系 libXtst.so.6()(64bit)，它被软件包 pulseaudio-libs-10.0-6.el7_9.x86_64 需要
---> 软件包 schroedinger.x86_64.0.1.0.11-4.el7 将被 安装
--> 正在处理依赖关系 liborc-0.4.so.0()(64bit)，它被软件包 schroedinger-1.0.11-4.el7.x86_64 需要
---> 软件包 soxr.x86_64.0.0.1.2-1.el7 将被 安装
---> 软件包 speex.x86_64.0.1.2-0.19.rc1.el7 将被 安装
---> 软件包 vo-amrwbenc.x86_64.0.0.1.2-1.el7.nux 将被 安装
---> 软件包 x264-libs.x86_64.0.0.142-11.20141221git6a301b6.el7.nux 将被 安装
---> 软件包 x265-libs.x86_64.0.1.9-1.el7.nux 将被 安装
---> 软件包 xvidcore.x86_64.0.1.3.2-5.el7.nux 将被 安装
--> 正在检查事务
---> 软件包 libXfixes.x86_64.0.5.0.3-1.el7 将被 安装
---> 软件包 libXtst.x86_64.0.1.2.3-1.el7 将被 安装
--> 正在处理依赖关系 libXi.so.6()(64bit)，它被软件包 libXtst-1.2.3-1.el7.x86_64 需要
---> 软件包 libasyncns.x86_64.0.0.8-7.el7 将被 安装
---> 软件包 libcdio.x86_64.0.0.92-3.el7 将被 安装
---> 软件包 libglvnd-egl.x86_64.1.1.0.1-0.8.git5baa1e5.el7 将被 安装
--> 正在处理依赖关系 libglvnd(x86-64) = 1:1.0.1-0.8.git5baa1e5.el7，它被软件包 1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64 需要
--> 正在处理依赖关系 mesa-libEGL(x86-64) >= 13.0.4-1，它被软件包 1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64 需要
--> 正在处理依赖关系 libGLdispatch.so.0()(64bit)，它被软件包 1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64 需要
---> 软件包 libglvnd-glx.x86_64.1.1.0.1-0.8.git5baa1e5.el7 将被 安装
--> 正在处理依赖关系 mesa-libGL(x86-64) >= 13.0.4-1，它被软件包 1:libglvnd-glx-1.0.1-0.8.git5baa1e5.el7.x86_64 需要
---> 软件包 libogg.x86_64.2.1.3.0-7.el7 将被 安装
---> 软件包 libraw1394.x86_64.0.2.1.0-2.el7 将被 安装
---> 软件包 libsndfile.x86_64.0.1.0.25-12.el7_9.1 将被 安装
--> 正在处理依赖关系 libFLAC.so.8()(64bit)，它被软件包 libsndfile-1.0.25-12.el7_9.1.x86_64 需要
---> 软件包 libusbx.x86_64.0.1.0.21-1.el7 将被 安装
---> 软件包 libwayland-client.x86_64.0.1.15.0-1.el7 将被 安装
---> 软件包 nettle.x86_64.0.2.7.1-9.el7_9 将被 安装
---> 软件包 orc.x86_64.0.0.4.26-1.el7 将被 安装
---> 软件包 trousers.x86_64.0.0.3.14-2.el7 将被 安装
--> 正在检查事务
---> 软件包 flac-libs.x86_64.0.1.3.0-5.el7_1 将被 安装
---> 软件包 libXi.x86_64.0.1.7.9-1.el7 将被 安装
---> 软件包 libglvnd.x86_64.1.1.0.1-0.8.git5baa1e5.el7 将被 安装
---> 软件包 mesa-libEGL.x86_64.0.18.3.4-12.el7_9 将被 安装
--> 正在处理依赖关系 mesa-libgbm = 18.3.4-12.el7_9，它被软件包 mesa-libEGL-18.3.4-12.el7_9.x86_64 需要
--> 正在处理依赖关系 libxshmfence.so.1()(64bit)，它被软件包 mesa-libEGL-18.3.4-12.el7_9.x86_64 需要
--> 正在处理依赖关系 libwayland-server.so.0()(64bit)，它被软件包 mesa-libEGL-18.3.4-12.el7_9.x86_64 需要
--> 正在处理依赖关系 libglapi.so.0()(64bit)，它被软件包 mesa-libEGL-18.3.4-12.el7_9.x86_64 需要
--> 正在处理依赖关系 libgbm.so.1()(64bit)，它被软件包 mesa-libEGL-18.3.4-12.el7_9.x86_64 需要
---> 软件包 mesa-libGL.x86_64.0.18.3.4-12.el7_9 将被 安装
--> 正在处理依赖关系 libXxf86vm.so.1()(64bit)，它被软件包 mesa-libGL-18.3.4-12.el7_9.x86_64 需要
--> 正在处理依赖关系 libXdamage.so.1()(64bit)，它被软件包 mesa-libGL-18.3.4-12.el7_9.x86_64 需要
--> 正在检查事务
---> 软件包 libXdamage.x86_64.0.1.1.4-4.1.el7 将被 安装
---> 软件包 libXxf86vm.x86_64.0.1.1.4-1.el7 将被 安装
---> 软件包 libwayland-server.x86_64.0.1.15.0-1.el7 将被 安装
---> 软件包 libxshmfence.x86_64.0.1.2-1.el7 将被 安装
---> 软件包 mesa-libgbm.x86_64.0.18.3.4-12.el7_9 将被 安装
---> 软件包 mesa-libglapi.x86_64.0.18.3.4-12.el7_9 将被 安装
--> 解决依赖关系完成

依赖关系解决

===========================================================================================
 Package              架构      版本                                   源             大小
===========================================================================================
正在安装:
 ffmpeg               x86_64    2.8.15-2.el7.nux                       nux-dextop    1.3 M
 ffmpeg-devel         x86_64    2.8.15-2.el7.nux                       nux-dextop    678 k
为依赖而安装:
 SDL                  x86_64    1.2.15-17.el7                          base          206 k
 fdk-aac              x86_64    0.1.4-1                                nux-dextop    375 k
 ffmpeg-libs          x86_64    2.8.15-2.el7.nux                       nux-dextop    5.5 M
 flac-libs            x86_64    1.3.0-5.el7_1                          base          169 k
 gnutls               x86_64    3.3.29-9.el7_6                         base          680 k
 gsm                  x86_64    1.0.13-11.el7                          base           30 k
 lame-libs            x86_64    3.100-1.el7                            epel          354 k
 libXdamage           x86_64    1.1.4-4.1.el7                          base           20 k
 libXfixes            x86_64    5.0.3-1.el7                            base           18 k
 libXi                x86_64    1.7.9-1.el7                            base           40 k
 libXtst              x86_64    1.2.3-1.el7                            base           20 k
 libXv                x86_64    1.0.11-1.el7                           base           18 k
 libXxf86vm           x86_64    1.1.4-1.el7                            base           18 k
 libass               x86_64    0.13.4-6.el7                           epel           99 k
 libasyncns           x86_64    0.8-7.el7                              base           26 k
 libavdevice          x86_64    2.8.15-2.el7.nux                       nux-dextop     73 k
 libcdio              x86_64    0.92-3.el7                             base          236 k
 libcdio-paranoia     x86_64    10.2+0.90-11.el7                       base           70 k
 libdc1394            x86_64    2.2.2-3.el7                            epel          121 k
 libglvnd             x86_64    1:1.0.1-0.8.git5baa1e5.el7             base           89 k
 libglvnd-egl         x86_64    1:1.0.1-0.8.git5baa1e5.el7             base           44 k
 libglvnd-glx         x86_64    1:1.0.1-0.8.git5baa1e5.el7             base          125 k
 libogg               x86_64    2:1.3.0-7.el7                          base           24 k
 libraw1394           x86_64    2.1.0-2.el7                            base           63 k
 libsndfile           x86_64    1.0.25-12.el7_9.1                      updates       150 k
 libtheora            x86_64    1:1.1.1-8.el7                          base          136 k
 libusbx              x86_64    1.0.21-1.el7                           base           61 k
 libv4l               x86_64    0.9.5-4.el7                            base          194 k
 libva                x86_64    1.8.3-1.el7                            base           80 k
 libvdpau             x86_64    1.1.1-3.el7                            base           34 k
 libvorbis            x86_64    1:1.3.3-8.el7.1                        base          204 k
 libwayland-client    x86_64    1.15.0-1.el7                           base           33 k
 libwayland-server    x86_64    1.15.0-1.el7                           base           39 k
 libxshmfence         x86_64    1.2-1.el7                              base          7.2 k
 mesa-libEGL          x86_64    18.3.4-12.el7_9                        updates       110 k
 mesa-libGL           x86_64    18.3.4-12.el7_9                        updates       166 k
 mesa-libgbm          x86_64    18.3.4-12.el7_9                        updates        39 k
 mesa-libglapi        x86_64    18.3.4-12.el7_9                        updates        46 k
 nettle               x86_64    2.7.1-9.el7_9                          updates       328 k
 openal-soft          x86_64    1.16.0-3.el7                           epel          282 k
 opencore-amr         x86_64    0.1.3-3.el7.nux                        nux-dextop    172 k
 openjpeg-libs        x86_64    1.5.1-18.el7                           base           86 k
 opus                 x86_64    1.0.2-6.el7                            base          630 k
 orc                  x86_64    0.4.26-1.el7                           base          166 k
 pulseaudio-libs      x86_64    10.0-6.el7_9                           updates       651 k
 schroedinger         x86_64    1.0.11-4.el7                           epel          291 k
 soxr                 x86_64    0.1.2-1.el7                            epel           77 k
 speex                x86_64    1.2-0.19.rc1.el7                       base           98 k
 trousers             x86_64    0.3.14-2.el7                           base          289 k
 vo-amrwbenc          x86_64    0.1.2-1.el7.nux                        nux-dextop     70 k
 x264-libs            x86_64    0.142-11.20141221git6a301b6.el7.nux    nux-dextop    570 k
 x265-libs            x86_64    1.9-1.el7.nux                          nux-dextop    1.5 M
 xvidcore             x86_64    1.3.2-5.el7.nux                        nux-dextop    258 k

事务概要
===========================================================================================
安装  2 软件包 (+53 依赖软件包)

总下载量：17 M
安装大小：59 M
Downloading packages:
(1/55): SDL-1.2.15-17.el7.x86_64.rpm                                | 206 kB  00:00:00
warning: /var/cache/yum/x86_64/7/nux-dextop/packages/fdk-aac-0.1.4-1.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID 85c6cd8a: NOKEY
fdk-aac-0.1.4-1.x86_64.rpm 的公钥尚未安装
(2/55): fdk-aac-0.1.4-1.x86_64.rpm                                  | 375 kB  00:00:13
(3/55): ffmpeg-2.8.15-2.el7.nux.x86_64.rpm                          | 1.3 MB  00:00:24
(4/55): flac-libs-1.3.0-5.el7_1.x86_64.rpm                          | 169 kB  00:00:00
(5/55): gsm-1.0.13-11.el7.x86_64.rpm                                |  30 kB  00:00:00
(6/55): libXdamage-1.1.4-4.1.el7.x86_64.rpm                         |  20 kB  00:00:00
(7/55): libXfixes-5.0.3-1.el7.x86_64.rpm                            |  18 kB  00:00:00
(8/55): libXi-1.7.9-1.el7.x86_64.rpm                                |  40 kB  00:00:00
(9/55): libXtst-1.2.3-1.el7.x86_64.rpm                              |  20 kB  00:00:00
(10/55): gnutls-3.3.29-9.el7_6.x86_64.rpm                           | 680 kB  00:00:00
(11/55): libXv-1.0.11-1.el7.x86_64.rpm                              |  18 kB  00:00:00
(12/55): libXxf86vm-1.1.4-1.el7.x86_64.rpm                          |  18 kB  00:00:00
(13/55): libasyncns-0.8-7.el7.x86_64.rpm                            |  26 kB  00:00:00
(14/55): ffmpeg-devel-2.8.15-2.el7.nux.x86_64.rpm                   | 678 kB  00:00:12
(15/55): libcdio-0.92-3.el7.x86_64.rpm                              | 236 kB  00:00:00
(16/55): libcdio-paranoia-10.2+0.90-11.el7.x86_64.rpm               |  70 kB  00:00:00
(17/55): libass-0.13.4-6.el7.x86_64.rpm                             |  99 kB  00:00:01
(18/55): libglvnd-1.0.1-0.8.git5baa1e5.el7.x86_64.rpm               |  89 kB  00:00:00
(19/55): libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64.rpm           |  44 kB  00:00:00
(20/55): libglvnd-glx-1.0.1-0.8.git5baa1e5.el7.x86_64.rpm           | 125 kB  00:00:00
(21/55): libogg-1.3.0-7.el7.x86_64.rpm                              |  24 kB  00:00:00
(22/55): libraw1394-2.1.0-2.el7.x86_64.rpm                          |  63 kB  00:00:00
(23/55): libavdevice-2.8.15-2.el7.nux.x86_64.rpm                    |  73 kB  00:00:01
(24/55): libsndfile-1.0.25-12.el7_9.1.x86_64.rpm                    | 150 kB  00:00:00
(25/55): libusbx-1.0.21-1.el7.x86_64.rpm                            |  61 kB  00:00:00
(26/55): libtheora-1.1.1-8.el7.x86_64.rpm                           | 136 kB  00:00:00
(27/55): lame-libs-3.100-1.el7.x86_64.rpm                           | 354 kB  00:00:03
(28/55): libva-1.8.3-1.el7.x86_64.rpm                               |  80 kB  00:00:00
(29/55): libv4l-0.9.5-4.el7.x86_64.rpm                              | 194 kB  00:00:00
(30/55): libvdpau-1.1.1-3.el7.x86_64.rpm                            |  34 kB  00:00:00
(31/55): libwayland-client-1.15.0-1.el7.x86_64.rpm                  |  33 kB  00:00:00
(32/55): libwayland-server-1.15.0-1.el7.x86_64.rpm                  |  39 kB  00:00:00
(33/55): libxshmfence-1.2-1.el7.x86_64.rpm                          | 7.2 kB  00:00:00
(34/55): libvorbis-1.3.3-8.el7.1.x86_64.rpm                         | 204 kB  00:00:00
(35/55): mesa-libGL-18.3.4-12.el7_9.x86_64.rpm                      | 166 kB  00:00:00
(36/55): mesa-libgbm-18.3.4-12.el7_9.x86_64.rpm                     |  39 kB  00:00:00
(37/55): mesa-libEGL-18.3.4-12.el7_9.x86_64.rpm                     | 110 kB  00:00:00
(38/55): mesa-libglapi-18.3.4-12.el7_9.x86_64.rpm                   |  46 kB  00:00:00
(39/55): libdc1394-2.2.2-3.el7.x86_64.rpm                           | 121 kB  00:00:03
(40/55): nettle-2.7.1-9.el7_9.x86_64.rpm                            | 328 kB  00:00:00
(41/55): openjpeg-libs-1.5.1-18.el7.x86_64.rpm                      |  86 kB  00:00:00
(42/55): orc-0.4.26-1.el7.x86_64.rpm                                | 166 kB  00:00:00
(43/55): opus-1.0.2-6.el7.x86_64.rpm                                | 630 kB  00:00:01
(44/55): openal-soft-1.16.0-3.el7.x86_64.rpm                        | 282 kB  00:00:02
(45/55): pulseaudio-libs-10.0-6.el7_9.x86_64.rpm                    | 651 kB  00:00:01
(46/55): speex-1.2-0.19.rc1.el7.x86_64.rpm                          |  98 kB  00:00:00
(47/55): trousers-0.3.14-2.el7.x86_64.rpm                           | 289 kB  00:00:00
(48/55): schroedinger-1.0.11-4.el7.x86_64.rpm                       | 291 kB  00:00:01
(49/55): soxr-0.1.2-1.el7.x86_64.rpm                                |  77 kB  00:00:01
(50/55): opencore-amr-0.1.3-3.el7.nux.x86_64.rpm                    | 172 kB  00:00:04
(51/55): vo-amrwbenc-0.1.2-1.el7.nux.x86_64.rpm                     |  70 kB  00:00:02
(52/55): x264-libs-0.142-11.20141221git6a301b6.el7.nux.x86_64.rpm   | 570 kB  00:00:07
(53/55): x265-libs-1.9-1.el7.nux.x86_64.rpm                         | 1.5 MB  00:00:42
(54/55): xvidcore-1.3.2-5.el7.nux.x86_64.rpm                        | 258 kB  00:00:09
(55/55): ffmpeg-libs-2.8.15-2.el7.nux.x86_64.rpm                    | 5.5 MB  00:01:47
-------------------------------------------------------------------------------------------
总计                                                       131 kB/s |  17 MB  00:02:12
从 file:///etc/pki/rpm-gpg/RPM-GPG-KEY-nux.ro 检索密钥
导入 GPG key 0x85C6CD8A:
 用户ID     : "Nux.Ro (rpm builder) <rpm@li.nux.ro>"
 指纹       : 561c 96bd 2f7f dc2a db5a fd46 e98b fbe7 85c6 cd8a
 软件包     : nux-dextop-release-0-5.el7.nux.noarch (installed)
 来自       : /etc/pki/rpm-gpg/RPM-GPG-KEY-nux.ro
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告：RPM 数据库已被非 yum 程序修改。
  正在安装    : 2:libogg-1.3.0-7.el7.x86_64                                           1/55
  正在安装    : libXfixes-5.0.3-1.el7.x86_64                                          2/55
  正在安装    : mesa-libglapi-18.3.4-12.el7_9.x86_64                                  3/55
  正在安装    : 1:libvorbis-1.3.3-8.el7.1.x86_64                                      4/55
  正在安装    : libxshmfence-1.2-1.el7.x86_64                                         5/55
  正在安装    : gsm-1.0.13-11.el7.x86_64                                              6/55
  正在安装    : 1:libglvnd-1.0.1-0.8.git5baa1e5.el7.x86_64                            7/55
  正在安装    : SDL-1.2.15-17.el7.x86_64                                              8/55
  正在安装    : libwayland-server-1.15.0-1.el7.x86_64                                 9/55
  正在安装    : libwayland-client-1.15.0-1.el7.x86_64                                10/55
  正在安装    : mesa-libgbm-18.3.4-12.el7_9.x86_64                                   11/55
  正在安装    : 1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64                       12/55
  正在安装    : mesa-libEGL-18.3.4-12.el7_9.x86_64                                   13/55
  正在安装    : libXdamage-1.1.4-4.1.el7.x86_64                                      14/55
  正在安装    : 1:libtheora-1.1.1-8.el7.x86_64                                       15/55
  正在安装    : flac-libs-1.3.0-5.el7_1.x86_64                                       16/55
  正在安装    : libsndfile-1.0.25-12.el7_9.1.x86_64                                  17/55
  正在安装    : libraw1394-2.1.0-2.el7.x86_64                                        18/55
  正在安装    : vo-amrwbenc-0.1.2-1.el7.nux.x86_64                                   19/55
  正在安装    : libass-0.13.4-6.el7.x86_64                                           20/55
  正在安装    : xvidcore-1.3.2-5.el7.nux.x86_64                                      21/55
  正在安装    : libv4l-0.9.5-4.el7.x86_64                                            22/55
  正在安装    : libXxf86vm-1.1.4-1.el7.x86_64                                        23/55
  正在安装    : 1:libglvnd-glx-1.0.1-0.8.git5baa1e5.el7.x86_64                       24/55
  正在安装    : mesa-libGL-18.3.4-12.el7_9.x86_64                                    25/55
  正在安装    : libva-1.8.3-1.el7.x86_64                                             26/55
  正在安装    : nettle-2.7.1-9.el7_9.x86_64                                          27/55
  正在安装    : libusbx-1.0.21-1.el7.x86_64                                          28/55
  正在安装    : libdc1394-2.2.2-3.el7.x86_64                                         29/55
  正在安装    : orc-0.4.26-1.el7.x86_64                                              30/55
  正在安装    : schroedinger-1.0.11-4.el7.x86_64                                     31/55
  正在安装    : fdk-aac-0.1.4-1.x86_64                                               32/55
  正在安装    : x265-libs-1.9-1.el7.nux.x86_64                                       33/55
  正在安装    : libvdpau-1.1.1-3.el7.x86_64                                          34/55
  正在安装    : openjpeg-libs-1.5.1-18.el7.x86_64                                    35/55
  正在安装    : opencore-amr-0.1.3-3.el7.nux.x86_64                                  36/55
  正在安装    : x264-libs-0.142-11.20141221git6a301b6.el7.nux.x86_64                 37/55
  正在安装    : libasyncns-0.8-7.el7.x86_64                                          38/55
  正在安装    : opus-1.0.2-6.el7.x86_64                                              39/55
  正在安装    : openal-soft-1.16.0-3.el7.x86_64                                      40/55
  正在安装    : speex-1.2-0.19.rc1.el7.x86_64                                        41/55
  正在安装    : libcdio-0.92-3.el7.x86_64                                            42/55
  正在安装    : libcdio-paranoia-10.2+0.90-11.el7.x86_64                             43/55
  正在安装    : trousers-0.3.14-2.el7.x86_64                                         44/55
  正在安装    : gnutls-3.3.29-9.el7_6.x86_64                                         45/55
  正在安装    : libXv-1.0.11-1.el7.x86_64                                            46/55
  正在安装    : soxr-0.1.2-1.el7.x86_64                                              47/55
  正在安装    : libXi-1.7.9-1.el7.x86_64                                             48/55
  正在安装    : libXtst-1.2.3-1.el7.x86_64                                           49/55
  正在安装    : pulseaudio-libs-10.0-6.el7_9.x86_64                                  50/55
  正在安装    : lame-libs-3.100-1.el7.x86_64                                         51/55
  正在安装    : ffmpeg-libs-2.8.15-2.el7.nux.x86_64                                  52/55
  正在安装    : libavdevice-2.8.15-2.el7.nux.x86_64                                  53/55
  正在安装    : ffmpeg-2.8.15-2.el7.nux.x86_64                                       54/55
  正在安装    : ffmpeg-devel-2.8.15-2.el7.nux.x86_64                                 55/55
  验证中      : lame-libs-3.100-1.el7.x86_64                                          1/55
  验证中      : libXi-1.7.9-1.el7.x86_64                                              2/55
  验证中      : libcdio-paranoia-10.2+0.90-11.el7.x86_64                              3/55
  验证中      : libwayland-client-1.15.0-1.el7.x86_64                                 4/55
  验证中      : 1:libvorbis-1.3.3-8.el7.1.x86_64                                      5/55
  验证中      : ffmpeg-2.8.15-2.el7.nux.x86_64                                        6/55
  验证中      : soxr-0.1.2-1.el7.x86_64                                               7/55
  验证中      : mesa-libGL-18.3.4-12.el7_9.x86_64                                     8/55
  验证中      : libXv-1.0.11-1.el7.x86_64                                             9/55
  验证中      : 1:libglvnd-glx-1.0.1-0.8.git5baa1e5.el7.x86_64                       10/55
  验证中      : pulseaudio-libs-10.0-6.el7_9.x86_64                                  11/55
  验证中      : libwayland-server-1.15.0-1.el7.x86_64                                12/55
  验证中      : trousers-0.3.14-2.el7.x86_64                                         13/55
  验证中      : libcdio-0.92-3.el7.x86_64                                            14/55
  验证中      : speex-1.2-0.19.rc1.el7.x86_64                                        15/55
  验证中      : openal-soft-1.16.0-3.el7.x86_64                                      16/55
  验证中      : opus-1.0.2-6.el7.x86_64                                              17/55
  验证中      : libasyncns-0.8-7.el7.x86_64                                          18/55
  验证中      : x264-libs-0.142-11.20141221git6a301b6.el7.nux.x86_64                 19/55
  验证中      : SDL-1.2.15-17.el7.x86_64                                             20/55
  验证中      : opencore-amr-0.1.3-3.el7.nux.x86_64                                  21/55
  验证中      : openjpeg-libs-1.5.1-18.el7.x86_64                                    22/55
  验证中      : libXtst-1.2.3-1.el7.x86_64                                           23/55
  验证中      : libvdpau-1.1.1-3.el7.x86_64                                          24/55
  验证中      : ffmpeg-devel-2.8.15-2.el7.nux.x86_64                                 25/55
  验证中      : x265-libs-1.9-1.el7.nux.x86_64                                       26/55
  验证中      : fdk-aac-0.1.4-1.x86_64                                               27/55
  验证中      : gnutls-3.3.29-9.el7_6.x86_64                                         28/55
  验证中      : 1:libtheora-1.1.1-8.el7.x86_64                                       29/55
  验证中      : 1:libglvnd-1.0.1-0.8.git5baa1e5.el7.x86_64                           30/55
  验证中      : orc-0.4.26-1.el7.x86_64                                              31/55
  验证中      : libusbx-1.0.21-1.el7.x86_64                                          32/55
  验证中      : libva-1.8.3-1.el7.x86_64                                             33/55
  验证中      : flac-libs-1.3.0-5.el7_1.x86_64                                       34/55
  验证中      : libdc1394-2.2.2-3.el7.x86_64                                         35/55
  验证中      : gsm-1.0.13-11.el7.x86_64                                             36/55
  验证中      : nettle-2.7.1-9.el7_9.x86_64                                          37/55
  验证中      : libsndfile-1.0.25-12.el7_9.1.x86_64                                  38/55
  验证中      : 2:libogg-1.3.0-7.el7.x86_64                                          39/55
  验证中      : libXxf86vm-1.1.4-1.el7.x86_64                                        40/55
  验证中      : libv4l-0.9.5-4.el7.x86_64                                            41/55
  验证中      : xvidcore-1.3.2-5.el7.nux.x86_64                                      42/55
  验证中      : mesa-libglapi-18.3.4-12.el7_9.x86_64                                 43/55
  验证中      : libass-0.13.4-6.el7.x86_64                                           44/55
  验证中      : mesa-libgbm-18.3.4-12.el7_9.x86_64                                   45/55
  验证中      : schroedinger-1.0.11-4.el7.x86_64                                     46/55
  验证中      : vo-amrwbenc-0.1.2-1.el7.nux.x86_64                                   47/55
  验证中      : libxshmfence-1.2-1.el7.x86_64                                        48/55
  验证中      : libavdevice-2.8.15-2.el7.nux.x86_64                                  49/55
  验证中      : mesa-libEGL-18.3.4-12.el7_9.x86_64                                   50/55
  验证中      : libXdamage-1.1.4-4.1.el7.x86_64                                      51/55
  验证中      : libXfixes-5.0.3-1.el7.x86_64                                         52/55
  验证中      : libraw1394-2.1.0-2.el7.x86_64                                        53/55
  验证中      : ffmpeg-libs-2.8.15-2.el7.nux.x86_64                                  54/55
  验证中      : 1:libglvnd-egl-1.0.1-0.8.git5baa1e5.el7.x86_64                       55/55

已安装:
  ffmpeg.x86_64 0:2.8.15-2.el7.nux          ffmpeg-devel.x86_64 0:2.8.15-2.el7.nux

作为依赖被安装:
  SDL.x86_64 0:1.2.15-17.el7
  fdk-aac.x86_64 0:0.1.4-1
  ffmpeg-libs.x86_64 0:2.8.15-2.el7.nux
  flac-libs.x86_64 0:1.3.0-5.el7_1
  gnutls.x86_64 0:3.3.29-9.el7_6
  gsm.x86_64 0:1.0.13-11.el7
  lame-libs.x86_64 0:3.100-1.el7
  libXdamage.x86_64 0:1.1.4-4.1.el7
  libXfixes.x86_64 0:5.0.3-1.el7
  libXi.x86_64 0:1.7.9-1.el7
  libXtst.x86_64 0:1.2.3-1.el7
  libXv.x86_64 0:1.0.11-1.el7
  libXxf86vm.x86_64 0:1.1.4-1.el7
  libass.x86_64 0:0.13.4-6.el7
  libasyncns.x86_64 0:0.8-7.el7
  libavdevice.x86_64 0:2.8.15-2.el7.nux
  libcdio.x86_64 0:0.92-3.el7
  libcdio-paranoia.x86_64 0:10.2+0.90-11.el7
  libdc1394.x86_64 0:2.2.2-3.el7
  libglvnd.x86_64 1:1.0.1-0.8.git5baa1e5.el7
  libglvnd-egl.x86_64 1:1.0.1-0.8.git5baa1e5.el7
  libglvnd-glx.x86_64 1:1.0.1-0.8.git5baa1e5.el7
  libogg.x86_64 2:1.3.0-7.el7
  libraw1394.x86_64 0:2.1.0-2.el7
  libsndfile.x86_64 0:1.0.25-12.el7_9.1
  libtheora.x86_64 1:1.1.1-8.el7
  libusbx.x86_64 0:1.0.21-1.el7
  libv4l.x86_64 0:0.9.5-4.el7
  libva.x86_64 0:1.8.3-1.el7
  libvdpau.x86_64 0:1.1.1-3.el7
  libvorbis.x86_64 1:1.3.3-8.el7.1
  libwayland-client.x86_64 0:1.15.0-1.el7
  libwayland-server.x86_64 0:1.15.0-1.el7
  libxshmfence.x86_64 0:1.2-1.el7
  mesa-libEGL.x86_64 0:18.3.4-12.el7_9
  mesa-libGL.x86_64 0:18.3.4-12.el7_9
  mesa-libgbm.x86_64 0:18.3.4-12.el7_9
  mesa-libglapi.x86_64 0:18.3.4-12.el7_9
  nettle.x86_64 0:2.7.1-9.el7_9
  openal-soft.x86_64 0:1.16.0-3.el7
  opencore-amr.x86_64 0:0.1.3-3.el7.nux
  openjpeg-libs.x86_64 0:1.5.1-18.el7
  opus.x86_64 0:1.0.2-6.el7
  orc.x86_64 0:0.4.26-1.el7
  pulseaudio-libs.x86_64 0:10.0-6.el7_9
  schroedinger.x86_64 0:1.0.11-4.el7
  soxr.x86_64 0:0.1.2-1.el7
  speex.x86_64 0:1.2-0.19.rc1.el7
  trousers.x86_64 0:0.3.14-2.el7
  vo-amrwbenc.x86_64 0:0.1.2-1.el7.nux
  x264-libs.x86_64 0:0.142-11.20141221git6a301b6.el7.nux
  x265-libs.x86_64 0:1.9-1.el7.nux
  xvidcore.x86_64 0:1.3.2-5.el7.nux

完毕！
[root@localhost ~]#
[root@localhost ~]# ffmpeg -version
ffmpeg version 2.8.15 Copyright (c) 2000-2018 the FFmpeg developers
built with gcc 4.8.5 (GCC) 20150623 (Red Hat 4.8.5-36)
configuration: --prefix=/usr --bindir=/usr/bin --datadir=/usr/share/ffmpeg --incdir=/usr/include/ffmpeg --libdir=/usr/lib64 --mandir=/usr/share/man --arch=x86_64 --optflags='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic' --extra-ldflags='-Wl,-z,relro ' --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libvo-amrwbenc --enable-version3 --enable-bzlib --disable-crystalhd --enable-gnutls --enable-ladspa --enable-libass --enable-libcdio --enable-libdc1394 --enable-libfdk-aac --enable-nonfree --disable-indev=jack --enable-libfreetype --enable-libgsm --enable-libmp3lame --enable-openal --enable-libopenjpeg --enable-libopus --enable-libpulse --enable-libschroedinger --enable-libsoxr --enable-libspeex --enable-libtheora --enable-libvorbis --enable-libv4l2 --enable-libx264 --enable-libx265 --enable-libxvid --enable-x11grab --enable-avfilter --enable-avresample --enable-postproc --enable-pthreads --disable-static --enable-shared --enable-gpl --disable-debug --disable-stripping --shlibdir=/usr/lib64 --enable-runtime-cpudetect
libavutil      54. 31.100 / 54. 31.100
libavcodec     56. 60.100 / 56. 60.100
libavformat    56. 40.101 / 56. 40.101
libavdevice    56.  4.100 / 56.  4.100
libavfilter     5. 40.101 /  5. 40.101
libavresample   2.  1.  0 /  2.  1.  0
libswscale      3.  1.101 /  3.  1.101
libswresample   1.  2.101 /  1.  2.101
libpostproc    53.  3.100 / 53.  3.100
[root@localhost ~]#
```

### 使用

在 PHP 中，你可以使用 exec(), shell_exec(), passthru(), system() 等函数来执行外部命令。
这些函数允许你在 PHP 脚本中运行 FFmpeg 命令。

示例：转换视频格式。假设你想将一个名为 input.mp4 的视频文件转换为 output.avi 格式，你可以使用以下 PHP 代码：
```php
<?php  
$ffmpegPath = '/usr/local/bin/ffmpeg'; // FFmpeg 的安装路径，根据你的服务器环境修改  
$inputFile = 'path/to/your/input.mp4'; // 输入文件路径  
$outputFile = 'path/to/your/output.avi'; // 输出文件路径  
  
// 构建 FFmpeg 命令  
$cmd = "$ffmpegPath -i \"$inputFile\" -vcodec copy -acodec copy \"$outputFile\"";  
  
// 执行命令  
$output = shell_exec($cmd);  
  
// 输出结果（可选）  
echo $output;  
  
// 检查是否出错  
if (empty($output)) {  
    echo "视频转换成功";  
} else {  
    echo "视频转换出错: $output";  
}  
?>
```

注意：
* 请根据你的服务器环境调整 $ffmpegPath 变量。
* 上面的命令中 `-vcodec copy -acodec copy` 表示视频和音频编解码器都使用复制模式，这意味着不会重新编码音视频流，
只会改变容器格式。 如果你需要进行编码转换（例如，改变分辨率或比特率），你需要相应地修改这些参数。
* 使用 shell_exec() 可能会带来安全风险，特别是当你执行的命令包含来自用户输入的数据时。
确保对任何用户输入进行适当的清理和验证，以避免注入攻击。

## ImageMagick安装

ImageMagick是一个功能强大的图片处理工具集，它可以用来对图片进行编辑、转换、合并等操作。

使用国内EPEL源，上面已经安装过了。

安装ImageMagick：
> yum install -y ImageMagick ImageMagick-devel

验证是否安装成功： 尝试运行ImageMagick提供的一些常用命令，比如：
```
> convert --version
```

详细：
```bash
[root@localhost ~]# yum search ImageMagick
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
=========================== N/S matched: ImageMagick ===========================
ImageMagick-c++.i686 : ImageMagick Magick++ library (C++ bindings)
ImageMagick-c++.x86_64 : ImageMagick Magick++ library (C++ bindings)
ImageMagick-c++-devel.i686 : C++ bindings for the ImageMagick library
ImageMagick-c++-devel.x86_64 : C++ bindings for the ImageMagick library
ImageMagick-devel.i686 : Library links and header files for ImageMagick app
                       : development
ImageMagick-devel.x86_64 : Library links and header files for ImageMagick app
                         : development
ImageMagick-doc.x86_64 : ImageMagick html documentation
ImageMagick-perl.x86_64 : ImageMagick perl bindings
ImageMagick6-c++.x86_64 : ImageMagick Magick++ library (C++ bindings)
ImageMagick6-djvu.x86_64 : DjVu plugin for ImageMagick
ImageMagick6-heic.x86_64 : HEIC plugin for ImageMagick
ImageMagick6-libs.x86_64 : ImageMagick library
ImageMagick7-c++.x86_64 : ImageMagick Magick++ library (C++ bindings)
ImageMagick7-djvu.x86_64 : DjVu plugin for ImageMagick
ImageMagick7-heic.x86_64 : HEIC plugin for ImageMagick
ImageMagick7-libs.x86_64 : ImageMagick library
GraphicsMagick.x86_64 : An ImageMagick fork, offering faster image generation
                      : and better quality
ImageMagick.i686 : An X application for displaying and manipulating images
ImageMagick.x86_64 : An X application for displaying and manipulating images
php-magickwand.x86_64 : PHP API for ImageMagick
php-pecl-imagick.x86_64 : Provides a wrapper to the ImageMagick library
php54-php-magickwand.x86_64 : PHP API for ImageMagick
php54-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php54-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php54-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php55-php-magickwand.x86_64 : PHP API for ImageMagick
php55-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php55-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php55-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php56-php-magickwand.x86_64 : PHP API for ImageMagick
php56-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php56-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php56-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php70-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php70-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php70-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php71-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php71-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php71-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php72-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php72-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php72-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php73-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php73-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php73-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php74-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php74-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php74-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php80-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php80-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php80-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php81-php-pecl-imagick.x86_64 : Extension to create and modify images using
                              : ImageMagick
php81-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php81-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php82-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php82-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
php83-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
                                  : ImageMagick 6
php83-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
                                  : ImageMagick 7
vips-magick-im6.x86_64 : Magick support for vips using ImageMagick6
vips-magick-im7.x86_64 : Magick support for vips using ImageMagick7

  名称和简介匹配 only，使用“search all”试试。
[root@localhost ~]#
[root@localhost ~]# yum list | grep ImageMagick
ImageMagick.x86_64                         6.9.10.68-7.el7_9          updates
ImageMagick-devel.x86_64                   6.9.10.68-7.el7_9          updates
ImageMagick.i686                           6.9.10.68-7.el7_9          updates
ImageMagick-c++.i686                       6.9.10.68-7.el7_9          updates
ImageMagick-c++.x86_64                     6.9.10.68-7.el7_9          updates
ImageMagick-c++-devel.i686                 6.9.10.68-7.el7_9          updates
ImageMagick-c++-devel.x86_64               6.9.10.68-7.el7_9          updates
ImageMagick-devel.i686                     6.9.10.68-7.el7_9          updates
ImageMagick-doc.x86_64                     6.9.10.68-7.el7_9          updates
ImageMagick-perl.x86_64                    6.9.10.68-7.el7_9          updates
ImageMagick6-c++.x86_64                    6.9.13.11-1.el7.remi       remi-safe
ImageMagick6-djvu.x86_64                   6.9.13.11-1.el7.remi       remi-safe
ImageMagick6-heic.x86_64                   6.9.13.11-1.el7.remi       remi-safe
ImageMagick6-libs.x86_64                   6.9.13.11-1.el7.remi       remi-safe
ImageMagick7-c++.x86_64                    1:7.1.1.33-1.el7.remi      remi-safe
ImageMagick7-djvu.x86_64                   1:7.1.1.33-1.el7.remi      remi-safe
ImageMagick7-heic.x86_64                   1:7.1.1.33-1.el7.remi      remi-safe
ImageMagick7-libs.x86_64                   1:7.1.1.33-1.el7.remi      remi-safe
[root@localhost ~]
[root@localhost ~]# yum install -y ImageMagick ImageMagick-devel
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                     | 5.1 kB     00:00
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
base                                                     | 3.6 kB     00:00
docker-ce-stable                                         | 3.5 kB     00:00
extras                                                   | 2.9 kB     00:00
mysql-8.4-lts-community                                  | 2.6 kB     00:00
mysql-connectors-community                               | 2.6 kB     00:00
mysql-tools-8.4-lts-community                            | 2.6 kB     00:00
nux-dextop                                               | 2.9 kB     00:00
remi-safe                                                | 3.0 kB     00:00
updates                                                  | 2.9 kB     00:00
正在解决依赖关系
--> 正在检查事务
---> 软件包 ImageMagick.x86_64.0.6.9.10.68-7.el7_9 将被 安装
--> 正在处理依赖关系 urw-base35-fonts，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libwmflite-0.2.so.7()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 librsvg-2.so.2()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libpangocairo-1.0.so.0()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libpango-1.0.so.0()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libopenjp2.so.7()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libltdl.so.7()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 liblcms2.so.2()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libgs.so.9()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libgdk_pixbuf-2.0.so.0()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libcairo.so.2()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libImath.so.6()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libIlmThread.so.6()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libIlmImf.so.7()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libIexMath.so.6()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libIex.so.6()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libHalf.so.6()(64bit)，它被软件包 ImageMagick-6.9.10.68-7.el7_9.x86_64 需要
---> 软件包 ImageMagick-devel.x86_64.0.6.9.10.68-7.el7_9 将被 安装
--> 正在处理依赖关系 libtiff-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libjpeg-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libXt-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libXext-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 libX11-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 jasper-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 ghostscript-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 freetype-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在处理依赖关系 bzip2-devel，它被软件包 ImageMagick-devel-6.9.10.68-7.el7_9.x86_64 需要
--> 正在检查事务
---> 软件包 OpenEXR-libs.x86_64.0.1.7.1-8.el7 将被 安装
---> 软件包 bzip2-devel.x86_64.0.1.0.6-13.el7 将被 安装
---> 软件包 cairo.x86_64.0.1.15.12-4.el7 将被 安装
--> 正在处理依赖关系 libpixman-1.so.0()(64bit)，它被软件包 cairo-1.15.12-4.el7.x86_64 需要
--> 正在处理依赖关系 libXrender.so.1()(64bit)，它被软件包 cairo-1.15.12-4.el7.x86_64 需要
---> 软件包 freetype-devel.x86_64.0.2.8-14.el7_9.1 将被 安装
--> 正在处理依赖关系 pkgconfig(zlib)，它被软件包 freetype-devel-2.8-14.el7_9.1.x86_64 需要
--> 正在处理依赖关系 pkgconfig(libpng)，它被软件包 freetype-devel-2.8-14.el7_9.1.x86_64 需要
---> 软件包 gdk-pixbuf2.x86_64.0.2.36.12-3.el7 将被 安装
--> 正在处理依赖关系 libjasper.so.1()(64bit)，它被软件包 gdk-pixbuf2-2.36.12-3.el7.x86_64 需要
---> 软件包 ilmbase.x86_64.0.1.0.3-7.el7 将被 安装
---> 软件包 jasper-devel.x86_64.0.1.900.1-33.el7 将被 安装
---> 软件包 lcms2.x86_64.0.2.6-3.el7 将被 安装
---> 软件包 libX11-devel.x86_64.0.1.6.7-5.el7_9 将被 安装
--> 正在处理依赖关系 pkgconfig(xcb) >= 1.11.1，它被软件包 libX11-devel-1.6.7-5.el7_9.x86_64 需要
--> 正在处理依赖关系 pkgconfig(xproto)，它被软件包 libX11-devel-1.6.7-5.el7_9.x86_64 需要
--> 正在处理依赖关系 pkgconfig(xcb)，它被软件包 libX11-devel-1.6.7-5.el7_9.x86_64 需要
--> 正在处理依赖关系 pkgconfig(kbproto)，它被软件包 libX11-devel-1.6.7-5.el7_9.x86_64 需要
---> 软件包 libXext-devel.x86_64.0.1.3.3-3.el7 将被 安装
---> 软件包 libXt-devel.x86_64.0.1.1.5-3.el7 将被 安装
--> 正在处理依赖关系 pkgconfig(sm)，它被软件包 libXt-devel-1.1.5-3.el7.x86_64 需要
--> 正在处理依赖关系 pkgconfig(ice)，它被软件包 libXt-devel-1.1.5-3.el7.x86_64 需要
---> 软件包 libgs.x86_64.0.9.25-5.el7 将被 安装
--> 正在处理依赖关系 adobe-mappings-pdf，它被软件包 libgs-9.25-5.el7.x86_64 需要
--> 正在处理依赖关系 adobe-mappings-cmap-deprecated，它被软件包 libgs-9.25-5.el7.x86_64 需要
--> 正在处理依赖关系 adobe-mappings-cmap，它被软件包 libgs-9.25-5.el7.x86_64 需要
--> 正在处理依赖关系 libpaper.so.1()(64bit)，它被软件包 libgs-9.25-5.el7.x86_64 需要
--> 正在处理依赖关系 libcupsimage.so.2()(64bit)，它被软件包 libgs-9.25-5.el7.x86_64 需要
--> 正在处理依赖关系 libcups.so.2()(64bit)，它被软件包 libgs-9.25-5.el7.x86_64 需要
---> 软件包 libgs-devel.x86_64.0.9.25-5.el7 将被 安装
---> 软件包 libjpeg-turbo-devel.x86_64.0.1.2.90-8.el7 将被 安装
---> 软件包 librsvg2.x86_64.0.2.40.20-1.el7 将被 安装
---> 软件包 libtiff-devel.x86_64.0.4.0.3-35.el7 将被 安装
---> 软件包 libtool-ltdl.x86_64.0.2.4.2-22.el7_3 将被 安装
---> 软件包 libwmf-lite.x86_64.0.0.2.8.4-44.el7 将被 安装
---> 软件包 openjpeg2.x86_64.0.2.3.1-3.el7_7 将被 安装
---> 软件包 pango.x86_64.0.1.42.4-4.el7_7 将被 安装
--> 正在处理依赖关系 libthai(x86-64) >= 0.1.9，它被软件包 pango-1.42.4-4.el7_7.x86_64 需要
--> 正在处理依赖关系 libXft(x86-64) >= 2.0.0，它被软件包 pango-1.42.4-4.el7_7.x86_64 需要
--> 正在处理依赖关系 libthai.so.0(LIBTHAI_0.1)(64bit)，它被软件包 pango-1.42.4-4.el7_7.x86_64 需要
--> 正在处理依赖关系 libthai.so.0()(64bit)，它被软件包 pango-1.42.4-4.el7_7.x86_64 需要
--> 正在处理依赖关系 libXft.so.2()(64bit)，它被软件包 pango-1.42.4-4.el7_7.x86_64 需要
---> 软件包 urw-base35-fonts.noarch.0.20170801-10.el7 将被 安装
--> 正在处理依赖关系 urw-base35-fonts-common = 20170801-10.el7，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-z003-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-standard-symbols-ps-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-p052-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-nimbus-sans-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-nimbus-roman-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-nimbus-mono-ps-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-gothic-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-d050000l-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-c059-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 urw-base35-bookman-fonts，它被软件包 urw-base35-fonts-20170801-10.el7.noarch 需要
--> 正在检查事务
---> 软件包 adobe-mappings-cmap.noarch.0.20171205-3.el7 将被 安装
---> 软件包 adobe-mappings-cmap-deprecated.noarch.0.20171205-3.el7 将被 安装
---> 软件包 adobe-mappings-pdf.noarch.0.20180407-1.el7 将被 安装
---> 软件包 cups-libs.x86_64.1.1.6.3-52.el7_9 将被 安装
--> 正在处理依赖关系 libavahi-common.so.3()(64bit)，它被软件包 1:cups-libs-1.6.3-52.el7_9.x86_64 需要
--> 正在处理依赖关系 libavahi-client.so.3()(64bit)，它被软件包 1:cups-libs-1.6.3-52.el7_9.x86_64 需要
---> 软件包 jasper-libs.x86_64.0.1.900.1-33.el7 将被 安装
---> 软件包 libICE-devel.x86_64.0.1.0.9-9.el7 将被 安装
---> 软件包 libSM-devel.x86_64.0.1.2.2-2.el7 将被 安装
---> 软件包 libXft.x86_64.0.2.3.2-2.el7 将被 安装
---> 软件包 libXrender.x86_64.0.0.9.10-1.el7 将被 安装
---> 软件包 libpaper.x86_64.0.1.1.24-9.el7 将被 安装
---> 软件包 libpng-devel.x86_64.2.1.5.13-8.el7 将被 安装
---> 软件包 libthai.x86_64.0.0.1.14-9.el7 将被 安装
---> 软件包 libxcb-devel.x86_64.0.1.13-1.el7 将被 安装
--> 正在处理依赖关系 pkgconfig(xau) >= 0.99.2，它被软件包 libxcb-devel-1.13-1.el7.x86_64 需要
---> 软件包 pixman.x86_64.0.0.34.0-1.el7 将被 安装
---> 软件包 urw-base35-bookman-fonts.noarch.0.20170801-10.el7 将被 安装
--> 正在处理依赖关系 xorg-x11-server-utils，它被软件包 urw-base35-bookman-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 xorg-x11-server-utils，它被软件包 urw-base35-bookman-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 xorg-x11-font-utils，它被软件包 urw-base35-bookman-fonts-20170801-10.el7.noarch 需要
--> 正在处理依赖关系 xorg-x11-font-utils，它被软件包 urw-base35-bookman-fonts-20170801-10.el7.noarch 需要
---> 软件包 urw-base35-c059-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-d050000l-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-fonts-common.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-gothic-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-nimbus-mono-ps-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-nimbus-roman-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-nimbus-sans-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-p052-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-standard-symbols-ps-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 urw-base35-z003-fonts.noarch.0.20170801-10.el7 将被 安装
---> 软件包 xorg-x11-proto-devel.noarch.0.2018.4-1.el7 将被 安装
---> 软件包 zlib-devel.x86_64.0.1.2.7-21.el7_9 将被 安装
--> 正在检查事务
---> 软件包 avahi-libs.x86_64.0.0.6.31-20.el7 将被 安装
---> 软件包 libXau-devel.x86_64.0.1.0.8-2.1.el7 将被 安装
---> 软件包 xorg-x11-font-utils.x86_64.1.7.5-21.el7 将被 安装
--> 正在处理依赖关系 libfontenc.so.1()(64bit)，它被软件包 1:xorg-x11-font-utils-7.5-21.el7.x86_64 需要
---> 软件包 xorg-x11-server-utils.x86_64.0.7.7-20.el7 将被 安装
--> 正在处理依赖关系 libXxf86misc.so.1()(64bit)，它被软件包 xorg-x11-server-utils-7.7-20.el7.x86_64 需要
--> 正在处理依赖关系 libXrandr.so.2()(64bit)，它被软件包 xorg-x11-server-utils-7.7-20.el7.x86_64 需要
--> 正在处理依赖关系 libXinerama.so.1()(64bit)，它被软件包 xorg-x11-server-utils-7.7-20.el7.x86_64 需要
--> 正在处理依赖关系 libXcursor.so.1()(64bit)，它被软件包 xorg-x11-server-utils-7.7-20.el7.x86_64 需要
--> 正在检查事务
---> 软件包 libXcursor.x86_64.0.1.1.15-1.el7 将被 安装
---> 软件包 libXinerama.x86_64.0.1.1.3-2.1.el7 将被 安装
---> 软件包 libXrandr.x86_64.0.1.5.1-2.el7 将被 安装
---> 软件包 libXxf86misc.x86_64.0.1.0.3-7.1.el7 将被 安装
---> 软件包 libfontenc.x86_64.0.1.1.3-3.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package                              架构   版本                 源       大小
================================================================================
正在安装:
 ImageMagick                          x86_64 6.9.10.68-7.el7_9    updates 2.3 M
 ImageMagick-devel                    x86_64 6.9.10.68-7.el7_9    updates 111 k
为依赖而安装:
 OpenEXR-libs                         x86_64 1.7.1-8.el7          base    217 k
 adobe-mappings-cmap                  noarch 20171205-3.el7       base    2.1 M
 adobe-mappings-cmap-deprecated       noarch 20171205-3.el7       base    114 k
 adobe-mappings-pdf                   noarch 20180407-1.el7       base    703 k
 avahi-libs                           x86_64 0.6.31-20.el7        base     62 k
 bzip2-devel                          x86_64 1.0.6-13.el7         base    218 k
 cairo                                x86_64 1.15.12-4.el7        base    741 k
 cups-libs                            x86_64 1:1.6.3-52.el7_9     updates 359 k
 freetype-devel                       x86_64 2.8-14.el7_9.1       updates 447 k
 gdk-pixbuf2                          x86_64 2.36.12-3.el7        base    570 k
 ilmbase                              x86_64 1.0.3-7.el7          base    100 k
 jasper-devel                         x86_64 1.900.1-33.el7       base    376 k
 jasper-libs                          x86_64 1.900.1-33.el7       base    150 k
 lcms2                                x86_64 2.6-3.el7            base    150 k
 libICE-devel                         x86_64 1.0.9-9.el7          base     50 k
 libSM-devel                          x86_64 1.2.2-2.el7          base     13 k
 libX11-devel                         x86_64 1.6.7-5.el7_9        updates 982 k
 libXau-devel                         x86_64 1.0.8-2.1.el7        base     14 k
 libXcursor                           x86_64 1.1.15-1.el7         base     30 k
 libXext-devel                        x86_64 1.3.3-3.el7          base     75 k
 libXft                               x86_64 2.3.2-2.el7          base     58 k
 libXinerama                          x86_64 1.1.3-2.1.el7        base     14 k
 libXrandr                            x86_64 1.5.1-2.el7          base     27 k
 libXrender                           x86_64 0.9.10-1.el7         base     26 k
 libXt-devel                          x86_64 1.1.5-3.el7          base    446 k
 libXxf86misc                         x86_64 1.0.3-7.1.el7        base     19 k
 libfontenc                           x86_64 1.1.3-3.el7          base     31 k
 libgs                                x86_64 9.25-5.el7           base    4.6 M
 libgs-devel                          x86_64 9.25-5.el7           base     57 k
 libjpeg-turbo-devel                  x86_64 1.2.90-8.el7         base     99 k
 libpaper                             x86_64 1.1.24-9.el7         base     37 k
 libpng-devel                         x86_64 2:1.5.13-8.el7       base    122 k
 librsvg2                             x86_64 2.40.20-1.el7        base    132 k
 libthai                              x86_64 0.1.14-9.el7         base    187 k
 libtiff-devel                        x86_64 4.0.3-35.el7         base    474 k
 libtool-ltdl                         x86_64 2.4.2-22.el7_3       base     49 k
 libwmf-lite                          x86_64 0.2.8.4-44.el7       base     66 k
 libxcb-devel                         x86_64 1.13-1.el7           base    1.1 M
 openjpeg2                            x86_64 2.3.1-3.el7_7        base    153 k
 pango                                x86_64 1.42.4-4.el7_7       base    280 k
 pixman                               x86_64 0.34.0-1.el7         base    248 k
 urw-base35-bookman-fonts             noarch 20170801-10.el7      base    852 k
 urw-base35-c059-fonts                noarch 20170801-10.el7      base    879 k
 urw-base35-d050000l-fonts            noarch 20170801-10.el7      base     75 k
 urw-base35-fonts                     noarch 20170801-10.el7      base    7.6 k
 urw-base35-fonts-common              noarch 20170801-10.el7      base     19 k
 urw-base35-gothic-fonts              noarch 20170801-10.el7      base    650 k
 urw-base35-nimbus-mono-ps-fonts      noarch 20170801-10.el7      base    796 k
 urw-base35-nimbus-roman-fonts        noarch 20170801-10.el7      base    860 k
 urw-base35-nimbus-sans-fonts         noarch 20170801-10.el7      base    1.3 M
 urw-base35-p052-fonts                noarch 20170801-10.el7      base    978 k
 urw-base35-standard-symbols-ps-fonts noarch 20170801-10.el7      base     40 k
 urw-base35-z003-fonts                noarch 20170801-10.el7      base    275 k
 xorg-x11-font-utils                  x86_64 1:7.5-21.el7         base    104 k
 xorg-x11-proto-devel                 noarch 2018.4-1.el7         base    280 k
 xorg-x11-server-utils                x86_64 7.7-20.el7           base    178 k
 zlib-devel                           x86_64 1.2.7-21.el7_9       updates  50 k

事务概要
================================================================================
安装  2 软件包 (+57 依赖软件包)

总下载量：25 M
安装大小：84 M
Downloading packages:
(1/59): OpenEXR-libs-1.7.1-8.el7.x86_64.rpm                | 217 kB   00:00
(2/59): adobe-mappings-pdf-20180407-1.el7.noarch.rpm       | 703 kB   00:00
(3/59): avahi-libs-0.6.31-20.el7.x86_64.rpm                |  62 kB   00:00
(4/59): bzip2-devel-1.0.6-13.el7.x86_64.rpm                | 218 kB   00:00
(5/59): ImageMagick-devel-6.9.10.68-7.el7_9.x86_64.rpm     | 111 kB   00:00
(6/59): cairo-1.15.12-4.el7.x86_64.rpm                     | 741 kB   00:00
(7/59): adobe-mappings-cmap-20171205-3.el7.noarch.rpm      | 2.1 MB   00:01
(8/59): cups-libs-1.6.3-52.el7_9.x86_64.rpm                | 359 kB   00:00
(9/59): gdk-pixbuf2-2.36.12-3.el7.x86_64.rpm               | 570 kB   00:00
(10/59): freetype-devel-2.8-14.el7_9.1.x86_64.rpm          | 447 kB   00:00
(11/59): ilmbase-1.0.3-7.el7.x86_64.rpm                    | 100 kB   00:00
(12/59): jasper-libs-1.900.1-33.el7.x86_64.rpm             | 150 kB   00:00
(13/59): lcms2-2.6-3.el7.x86_64.rpm                        | 150 kB   00:00
(14/59): libICE-devel-1.0.9-9.el7.x86_64.rpm               |  50 kB   00:00
(15/59): jasper-devel-1.900.1-33.el7.x86_64.rpm            | 376 kB   00:00
(16/59): ImageMagick-6.9.10.68-7.el7_9.x86_64.rpm          | 2.3 MB   00:02
(17/59): libSM-devel-1.2.2-2.el7.x86_64.rpm                |  13 kB   00:00
(18/59): libXau-devel-1.0.8-2.1.el7.x86_64.rpm             |  14 kB   00:00
(19/59): libXcursor-1.1.15-1.el7.x86_64.rpm                |  30 kB   00:00
(20/59): libXft-2.3.2-2.el7.x86_64.rpm                     |  58 kB   00:00
(21/59): libXext-devel-1.3.3-3.el7.x86_64.rpm              |  75 kB   00:00
(22/59): libXinerama-1.1.3-2.1.el7.x86_64.rpm              |  14 kB   00:00
(23/59): libXrandr-1.5.1-2.el7.x86_64.rpm                  |  27 kB   00:00
(24/59): libXrender-0.9.10-1.el7.x86_64.rpm                |  26 kB   00:00
(25/59): libXxf86misc-1.0.3-7.1.el7.x86_64.rpm             |  19 kB   00:00
(26/59): libfontenc-1.1.3-3.el7.x86_64.rpm                 |  31 kB   00:00
(27/59): libXt-devel-1.1.5-3.el7.x86_64.rpm                | 446 kB   00:00
(28/59): libX11-devel-1.6.7-5.el7_9.x86_64.rpm             | 982 kB   00:00
(29/59): libgs-devel-9.25-5.el7.x86_64.rpm                 |  57 kB   00:00
(30/59): libgs-9.25-5.el7.x86_64.rpm                       | 4.6 MB   00:00
(31/59): libjpeg-turbo-devel-1.2.90-8.el7.x86_64.rpm       |  99 kB   00:00
(32/59): libpaper-1.1.24-9.el7.x86_64.rpm                  |  37 kB   00:00
(33/59): libpng-devel-1.5.13-8.el7.x86_64.rpm              | 122 kB   00:00
(34/59): librsvg2-2.40.20-1.el7.x86_64.rpm                 | 132 kB   00:00
(35/59): libthai-0.1.14-9.el7.x86_64.rpm                   | 187 kB   00:00
(36/59): libtool-ltdl-2.4.2-22.el7_3.x86_64.rpm            |  49 kB   00:00
(37/59): libtiff-devel-4.0.3-35.el7.x86_64.rpm             | 474 kB   00:00
(38/59): libwmf-lite-0.2.8.4-44.el7.x86_64.rpm             |  66 kB   00:00
(39/59): openjpeg2-2.3.1-3.el7_7.x86_64.rpm                | 153 kB   00:00
(40/59): pango-1.42.4-4.el7_7.x86_64.rpm                   | 280 kB   00:00
(41/59): pixman-0.34.0-1.el7.x86_64.rpm                    | 248 kB   00:00
(42/59): libxcb-devel-1.13-1.el7.x86_64.rpm                | 1.1 MB   00:00
(43/59): urw-base35-c059-fonts-20170801-10.el7.noarch.rpm  | 879 kB   00:00
(44/59): urw-base35-bookman-fonts-20170801-10.el7.noarch.r | 852 kB   00:00
(45/59): urw-base35-d050000l-fonts-20170801-10.el7.noarch. |  75 kB   00:00
(46/59): urw-base35-fonts-20170801-10.el7.noarch.rpm       | 7.6 kB   00:00
(47/59): urw-base35-fonts-common-20170801-10.el7.noarch.rp |  19 kB   00:00
(48/59): urw-base35-gothic-fonts-20170801-10.el7.noarch.rp | 650 kB   00:00
(49/59): urw-base35-nimbus-mono-ps-fonts-20170801-10.el7.n | 796 kB   00:00
(50/59): urw-base35-nimbus-roman-fonts-20170801-10.el7.noa | 860 kB   00:00
(51/59): urw-base35-nimbus-sans-fonts-20170801-10.el7.noar | 1.3 MB   00:00
(52/59): urw-base35-standard-symbols-ps-fonts-20170801-10. |  40 kB   00:00
(53/59): urw-base35-p052-fonts-20170801-10.el7.noarch.rpm  | 978 kB   00:00
(54/59): urw-base35-z003-fonts-20170801-10.el7.noarch.rpm  | 275 kB   00:00
(55/59): xorg-x11-font-utils-7.5-21.el7.x86_64.rpm         | 104 kB   00:00
(56/59): xorg-x11-proto-devel-2018.4-1.el7.noarch.rpm      | 280 kB   00:00
(57/59): xorg-x11-server-utils-7.7-20.el7.x86_64.rpm       | 178 kB   00:00
(58/59): zlib-devel-1.2.7-21.el7_9.x86_64.rpm              |  50 kB   00:00
adobe-mappings-cmap-deprecated FAILED
http://mirrors.cloud.aliyuncs.com/centos/7/os/x86_64/Packages/adobe-mappings-cmap-deprecated-20171205-3.el7.noarch.rpm: [Errno 12] Timeout on http://mirrors.cloud.aliyuncs.com/centos/7/os/x86_64/Packages/adobe-mappings-cmap-deprecated-20171205-3.el7.noarch.rpm: (28, 'Connection timed out after 30001 milliseconds')
正在尝试其它镜像。
(59/59): adobe-mappings-cmap-deprecated-20171205-3.el7.noa | 114 kB   00:00
--------------------------------------------------------------------------------
总计                                               836 kB/s |  25 MB  00:30
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : urw-base35-fonts-common-20170801-10.el7.noarch             1/59
  正在安装    : xorg-x11-proto-devel-2018.4-1.el7.noarch                   2/59
  正在安装    : libXrender-0.9.10-1.el7.x86_64                             3/59
  正在安装    : libICE-devel-1.0.9-9.el7.x86_64                            4/59
  正在安装    : jasper-libs-1.900.1-33.el7.x86_64                          5/59
  正在安装    : gdk-pixbuf2-2.36.12-3.el7.x86_64                           6/59
  正在安装    : lcms2-2.6-3.el7.x86_64                                     7/59
  正在安装    : openjpeg2-2.3.1-3.el7_7.x86_64                             8/59
  正在安装    : zlib-devel-1.2.7-21.el7_9.x86_64                           9/59
  正在安装    : adobe-mappings-cmap-20171205-3.el7.noarch                 10/59
  正在安装    : ilmbase-1.0.3-7.el7.x86_64                                11/59
  正在安装    : libjpeg-turbo-devel-1.2.90-8.el7.x86_64                   12/59
  正在安装    : jasper-devel-1.900.1-33.el7.x86_64                        13/59
  正在安装    : OpenEXR-libs-1.7.1-8.el7.x86_64                           14/59
  正在安装    : adobe-mappings-cmap-deprecated-20171205-3.el7.noarch      15/59
  正在安装    : 2:libpng-devel-1.5.13-8.el7.x86_64                        16/59
  正在安装    : freetype-devel-2.8-14.el7_9.1.x86_64                      17/59
  正在安装    : libSM-devel-1.2.2-2.el7.x86_64                            18/59
  正在安装    : libXcursor-1.1.15-1.el7.x86_64                            19/59
  正在安装    : libXrandr-1.5.1-2.el7.x86_64                              20/59
  正在安装    : libXft-2.3.2-2.el7.x86_64                                 21/59
  正在安装    : libXau-devel-1.0.8-2.1.el7.x86_64                         22/59
  正在安装    : libxcb-devel-1.13-1.el7.x86_64                            23/59
  正在安装    : libX11-devel-1.6.7-5.el7_9.x86_64                         24/59
  正在安装    : libXt-devel-1.1.5-3.el7.x86_64                            25/59
  正在安装    : libXext-devel-1.3.3-3.el7.x86_64                          26/59
  正在安装    : pixman-0.34.0-1.el7.x86_64                                27/59
  正在安装    : cairo-1.15.12-4.el7.x86_64                                28/59
  正在安装    : avahi-libs-0.6.31-20.el7.x86_64                           29/59
  正在安装    : 1:cups-libs-1.6.3-52.el7_9.x86_64                         30/59
  正在安装    : libfontenc-1.1.3-3.el7.x86_64                             31/59
  正在安装    : 1:xorg-x11-font-utils-7.5-21.el7.x86_64                   32/59
  正在安装    : libtiff-devel-4.0.3-35.el7.x86_64                         33/59
  正在安装    : libwmf-lite-0.2.8.4-44.el7.x86_64                         34/59
  正在安装    : libthai-0.1.14-9.el7.x86_64                               35/59
  正在安装    : pango-1.42.4-4.el7_7.x86_64                               36/59
  正在安装    : librsvg2-2.40.20-1.el7.x86_64                             37/59
  正在安装    : libtool-ltdl-2.4.2-22.el7_3.x86_64                        38/59
  正在安装    : bzip2-devel-1.0.6-13.el7.x86_64                           39/59
  正在安装    : adobe-mappings-pdf-20180407-1.el7.noarch                  40/59
  正在安装    : libXxf86misc-1.0.3-7.1.el7.x86_64                         41/59
  正在安装    : libpaper-1.1.24-9.el7.x86_64                              42/59
  正在安装    : libXinerama-1.1.3-2.1.el7.x86_64                          43/59
  正在安装    : xorg-x11-server-utils-7.7-20.el7.x86_64                   44/59
  正在安装    : urw-base35-standard-symbols-ps-fonts-20170801-10.el7.no   45/59
  正在安装    : urw-base35-z003-fonts-20170801-10.el7.noarch              46/59
  正在安装    : urw-base35-p052-fonts-20170801-10.el7.noarch              47/59
  正在安装    : urw-base35-c059-fonts-20170801-10.el7.noarch              48/59
  正在安装    : urw-base35-d050000l-fonts-20170801-10.el7.noarch          49/59
  正在安装    : urw-base35-nimbus-mono-ps-fonts-20170801-10.el7.noarch    50/59
  正在安装    : urw-base35-bookman-fonts-20170801-10.el7.noarch           51/59
  正在安装    : urw-base35-nimbus-sans-fonts-20170801-10.el7.noarch       52/59
  正在安装    : urw-base35-nimbus-roman-fonts-20170801-10.el7.noarch      53/59
  正在安装    : urw-base35-gothic-fonts-20170801-10.el7.noarch            54/59
  正在安装    : urw-base35-fonts-20170801-10.el7.noarch                   55/59
  正在安装    : libgs-9.25-5.el7.x86_64                                   56/59
  正在安装    : ImageMagick-6.9.10.68-7.el7_9.x86_64                      57/59
  正在安装    : libgs-devel-9.25-5.el7.x86_64                             58/59
  正在安装    : ImageMagick-devel-6.9.10.68-7.el7_9.x86_64                59/59
  验证中      : urw-base35-standard-symbols-ps-fonts-20170801-10.el7.no    1/59
  验证中      : 1:xorg-x11-font-utils-7.5-21.el7.x86_64                    2/59
  验证中      : 2:libpng-devel-1.5.13-8.el7.x86_64                         3/59
  验证中      : gdk-pixbuf2-2.36.12-3.el7.x86_64                           4/59
  验证中      : libXinerama-1.1.3-2.1.el7.x86_64                           5/59
  验证中      : libpaper-1.1.24-9.el7.x86_64                               6/59
  验证中      : libXrender-0.9.10-1.el7.x86_64                             7/59
  验证中      : urw-base35-z003-fonts-20170801-10.el7.noarch               8/59
  验证中      : libgs-9.25-5.el7.x86_64                                    9/59
  验证中      : libjpeg-turbo-devel-1.2.90-8.el7.x86_64                   10/59
  验证中      : urw-base35-p052-fonts-20170801-10.el7.noarch              11/59
  验证中      : ilmbase-1.0.3-7.el7.x86_64                                12/59
  验证中      : libXcursor-1.1.15-1.el7.x86_64                            13/59
  验证中      : libICE-devel-1.0.9-9.el7.x86_64                           14/59
  验证中      : adobe-mappings-cmap-deprecated-20171205-3.el7.noarch      15/59
  验证中      : libXxf86misc-1.0.3-7.1.el7.x86_64                         16/59
  验证中      : adobe-mappings-pdf-20180407-1.el7.noarch                  17/59
  验证中      : libSM-devel-1.2.2-2.el7.x86_64                            18/59
  验证中      : libXrandr-1.5.1-2.el7.x86_64                              19/59
  验证中      : bzip2-devel-1.0.6-13.el7.x86_64                           20/59
  验证中      : libXt-devel-1.1.5-3.el7.x86_64                            21/59
  验证中      : adobe-mappings-cmap-20171205-3.el7.noarch                 22/59
  验证中      : ImageMagick-6.9.10.68-7.el7_9.x86_64                      23/59
  验证中      : urw-base35-c059-fonts-20170801-10.el7.noarch              24/59
  验证中      : xorg-x11-proto-devel-2018.4-1.el7.noarch                  25/59
  验证中      : libX11-devel-1.6.7-5.el7_9.x86_64                         26/59
  验证中      : pango-1.42.4-4.el7_7.x86_64                               27/59
  验证中      : libtool-ltdl-2.4.2-22.el7_3.x86_64                        28/59
  验证中      : libthai-0.1.14-9.el7.x86_64                               29/59
  验证中      : urw-base35-d050000l-fonts-20170801-10.el7.noarch          30/59
  验证中      : libXft-2.3.2-2.el7.x86_64                                 31/59
  验证中      : urw-base35-fonts-common-20170801-10.el7.noarch            32/59
  验证中      : zlib-devel-1.2.7-21.el7_9.x86_64                          33/59
  验证中      : libwmf-lite-0.2.8.4-44.el7.x86_64                         34/59
  验证中      : urw-base35-fonts-20170801-10.el7.noarch                   35/59
  验证中      : openjpeg2-2.3.1-3.el7_7.x86_64                            36/59
  验证中      : 1:cups-libs-1.6.3-52.el7_9.x86_64                         37/59
  验证中      : urw-base35-nimbus-mono-ps-fonts-20170801-10.el7.noarch    38/59
  验证中      : ImageMagick-devel-6.9.10.68-7.el7_9.x86_64                39/59
  验证中      : libXext-devel-1.3.3-3.el7.x86_64                          40/59
  验证中      : urw-base35-bookman-fonts-20170801-10.el7.noarch           41/59
  验证中      : jasper-devel-1.900.1-33.el7.x86_64                        42/59
  验证中      : freetype-devel-2.8-14.el7_9.1.x86_64                      43/59
  验证中      : urw-base35-nimbus-sans-fonts-20170801-10.el7.noarch       44/59
  验证中      : cairo-1.15.12-4.el7.x86_64                                45/59
  验证中      : libXau-devel-1.0.8-2.1.el7.x86_64                         46/59
  验证中      : urw-base35-nimbus-roman-fonts-20170801-10.el7.noarch      47/59
  验证中      : lcms2-2.6-3.el7.x86_64                                    48/59
  验证中      : libxcb-devel-1.13-1.el7.x86_64                            49/59
  验证中      : libtiff-devel-4.0.3-35.el7.x86_64                         50/59
  验证中      : jasper-libs-1.900.1-33.el7.x86_64                         51/59
  验证中      : libgs-devel-9.25-5.el7.x86_64                             52/59
  验证中      : librsvg2-2.40.20-1.el7.x86_64                             53/59
  验证中      : xorg-x11-server-utils-7.7-20.el7.x86_64                   54/59
  验证中      : OpenEXR-libs-1.7.1-8.el7.x86_64                           55/59
  验证中      : libfontenc-1.1.3-3.el7.x86_64                             56/59
  验证中      : avahi-libs-0.6.31-20.el7.x86_64                           57/59
  验证中      : urw-base35-gothic-fonts-20170801-10.el7.noarch            58/59
  验证中      : pixman-0.34.0-1.el7.x86_64                                59/59

已安装:
  ImageMagick.x86_64 0:6.9.10.68-7.el7_9
  ImageMagick-devel.x86_64 0:6.9.10.68-7.el7_9

作为依赖被安装:
  OpenEXR-libs.x86_64 0:1.7.1-8.el7
  adobe-mappings-cmap.noarch 0:20171205-3.el7
  adobe-mappings-cmap-deprecated.noarch 0:20171205-3.el7
  adobe-mappings-pdf.noarch 0:20180407-1.el7
  avahi-libs.x86_64 0:0.6.31-20.el7
  bzip2-devel.x86_64 0:1.0.6-13.el7
  cairo.x86_64 0:1.15.12-4.el7
  cups-libs.x86_64 1:1.6.3-52.el7_9
  freetype-devel.x86_64 0:2.8-14.el7_9.1
  gdk-pixbuf2.x86_64 0:2.36.12-3.el7
  ilmbase.x86_64 0:1.0.3-7.el7
  jasper-devel.x86_64 0:1.900.1-33.el7
  jasper-libs.x86_64 0:1.900.1-33.el7
  lcms2.x86_64 0:2.6-3.el7
  libICE-devel.x86_64 0:1.0.9-9.el7
  libSM-devel.x86_64 0:1.2.2-2.el7
  libX11-devel.x86_64 0:1.6.7-5.el7_9
  libXau-devel.x86_64 0:1.0.8-2.1.el7
  libXcursor.x86_64 0:1.1.15-1.el7
  libXext-devel.x86_64 0:1.3.3-3.el7
  libXft.x86_64 0:2.3.2-2.el7
  libXinerama.x86_64 0:1.1.3-2.1.el7
  libXrandr.x86_64 0:1.5.1-2.el7
  libXrender.x86_64 0:0.9.10-1.el7
  libXt-devel.x86_64 0:1.1.5-3.el7
  libXxf86misc.x86_64 0:1.0.3-7.1.el7
  libfontenc.x86_64 0:1.1.3-3.el7
  libgs.x86_64 0:9.25-5.el7
  libgs-devel.x86_64 0:9.25-5.el7
  libjpeg-turbo-devel.x86_64 0:1.2.90-8.el7
  libpaper.x86_64 0:1.1.24-9.el7
  libpng-devel.x86_64 2:1.5.13-8.el7
  librsvg2.x86_64 0:2.40.20-1.el7
  libthai.x86_64 0:0.1.14-9.el7
  libtiff-devel.x86_64 0:4.0.3-35.el7
  libtool-ltdl.x86_64 0:2.4.2-22.el7_3
  libwmf-lite.x86_64 0:0.2.8.4-44.el7
  libxcb-devel.x86_64 0:1.13-1.el7
  openjpeg2.x86_64 0:2.3.1-3.el7_7
  pango.x86_64 0:1.42.4-4.el7_7
  pixman.x86_64 0:0.34.0-1.el7
  urw-base35-bookman-fonts.noarch 0:20170801-10.el7
  urw-base35-c059-fonts.noarch 0:20170801-10.el7
  urw-base35-d050000l-fonts.noarch 0:20170801-10.el7
  urw-base35-fonts.noarch 0:20170801-10.el7
  urw-base35-fonts-common.noarch 0:20170801-10.el7
  urw-base35-gothic-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-mono-ps-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-roman-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-sans-fonts.noarch 0:20170801-10.el7
  urw-base35-p052-fonts.noarch 0:20170801-10.el7
  urw-base35-standard-symbols-ps-fonts.noarch 0:20170801-10.el7
  urw-base35-z003-fonts.noarch 0:20170801-10.el7
  xorg-x11-font-utils.x86_64 1:7.5-21.el7
  xorg-x11-proto-devel.noarch 0:2018.4-1.el7
  xorg-x11-server-utils.x86_64 0:7.7-20.el7
  zlib-devel.x86_64 0:1.2.7-21.el7_9

完毕！
[root@localhost ~]#
[root@localhost ~]# convert --version
Version: ImageMagick 6.9.10-68 Q16 x86_64 2024-01-12 https://imagemagick.org
Copyright: © 1999-2019 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC Modules OpenMP(3.1)
Delegates (built-in): bzlib cairo fontconfig freetype gslib jng jp2 jpeg lcms ltdl lzma openexr pangocairo png ps rsvg tiff wmf x xml zlib
[root@localhost ~]#
```

### 命令行使用

下面是一些常用的ImageMagick命令：

1.裁剪图片：
```
convert input.jpg -crop 100x100+10+10 output.jpg
```
这条命令将从input.jpg中裁剪出一块100x100的区域，起点坐标为（10,10），并保存为output.jpg。

2.缩放图片：
```
convert input.jpg -resize 50% output.jpg
```
这条命令将input.jpg缩小为原来的一半，并保存为output.jpg。

3.调整图片质量：
```
convert input.jpg -quality 80 output.jpg
```
这条命令将input.jpg的质量调整为80%，并保存为output.jpg。

4.图片合并：
```
convert image1.jpg image2.jpg +append output.jpg
```
这条命令将image1.jpg和image2.jpg横向合并，并保存为output.jpg。

5.添加水印：
```
convert input.jpg watermark.png -gravity southeast -composite output.jpg
```
这条命令将watermark.png添加在input.jpg的右下角，并保存为output.jpg。

6.示例，文字从底部向上滚动：
```bash
#!/bin/bash  
# 滚动文本设置  
text="滚动内容小说示例...滚动内容小说示例..."
# 注意：确保这里使用的是支持中文的字体
font="/usr/share/fonts/chinese/simsun.ttc"

// 背景图片
bgimg="../a.jpg"

# 视频分辨率设置
video_width=720
video_height=1280
video_dibu=236
video_dingbu=288
video_box_height=$(( 1280 - video_dibu ))

font_size=36
scroll_speed=60  # 每秒滚动的行数，这里用于计算总帧数的参考，每秒移动一行文字的比例
total_frames=$(( ((video_box_height / font_size) * scroll_speed) - ((video_dingbu/font_size) * scroll_speed) ))  # 总帧数，你可以根据需要调整这个值

# 创建一个临时目录用于存储图像  
frames_dir="frames"  
mkdir -p "$frames_dir"  
  
# 切换到临时目录  
cd "$frames_dir"  
  
# 生成滚动文本的图像  
for(( i=0; i<$total_frames; i++ ))  
do  
    # 这里假设每帧滚动一点点，具体取决于你想要的滚动效果  
    # offset=$(( (i * font_size) / (total_frames / scroll_speed) ))
    # convert ${bgimg} -font "$font" -fill black -pointsize ${font_size} -gravity north -annotate +72+${offset} "${text}" roll-text${i}.png
    offset=$(( video_box_height - ((i * font_size) / (scroll_speed)) ))
    convert ${bgimg} -font "$font" -fill black -pointsize ${font_size} -gravity northwest -draw "text 72,${offset} '${text}'" roll-text${i}.png
done  
  
# 返回到原始目录（可选，但在这里有助于保持脚本的整洁）  
cd ..  
  
# 使用FFmpeg将图像转换成视频  
# ffmpeg -r $(echo "scale=0; $total_frames / ($scroll_speed)" | bc) -i "$frames_dir/roll-text%d.png" -c:v libx264 -pix_fmt yuv420p -s ${video_width}x${video_height} output.mp4
ffmpeg -r $(($scroll_speed * 2)) -i "$frames_dir/roll-text%d.png" -c:v libx264 -pix_fmt yuv420p -s ${video_width}x${video_height} output.mp4

# 清理临时文件和目录  
#rm -rf "$frames_dir"
```

### PHP使用

PHP画图操作除了gd拓展，也可以使用imagick拓展，不过依赖于上面安装的ImageMagick，需要先安装好ImageMagick。

```bash
# 搜索 php 的 ImageMagick
yum search ImageMagick | grep php

# 查看源
yum list | grep imagick

# 安装imagick拓展
yum --enablerepo=remi-php74 install -y php-pecl-imagick
```

详细：
```
[root@localhost ~]# yum search ImageMagick | grep php
php-magickwand.x86_64 : PHP API for ImageMagick
php-pecl-imagick.x86_64 : Provides a wrapper to the ImageMagick library
php54-php-magickwand.x86_64 : PHP API for ImageMagick
php54-php-pecl-imagick.x86_64 : Extension to create and modify images using
php54-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php54-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php55-php-magickwand.x86_64 : PHP API for ImageMagick
php55-php-pecl-imagick.x86_64 : Extension to create and modify images using
php55-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php55-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php56-php-magickwand.x86_64 : PHP API for ImageMagick
php56-php-pecl-imagick.x86_64 : Extension to create and modify images using
php56-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php56-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php70-php-pecl-imagick.x86_64 : Extension to create and modify images using
php70-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php70-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php71-php-pecl-imagick.x86_64 : Extension to create and modify images using
php71-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php71-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php72-php-pecl-imagick.x86_64 : Extension to create and modify images using
php72-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php72-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php73-php-pecl-imagick.x86_64 : Extension to create and modify images using
php73-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php73-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php74-php-pecl-imagick.x86_64 : Extension to create and modify images using
php74-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php74-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php80-php-pecl-imagick.x86_64 : Extension to create and modify images using
php80-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php80-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php81-php-pecl-imagick.x86_64 : Extension to create and modify images using
php81-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php81-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php82-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php82-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
php83-php-pecl-imagick-im6.x86_64 : Extension to create and modify images using
php83-php-pecl-imagick-im7.x86_64 : Extension to create and modify images using
[root@localhost ~]#
[root@localhost ~]# yum list | grep imagick
php-pecl-imagick.x86_64                    3.4.4-1.el7                epel
php54-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php54-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php54-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php54-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php54-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php54-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php55-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php55-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php55-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php55-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php55-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php55-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php56-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php56-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php56-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php56-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php56-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php56-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php70-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php70-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php70-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php70-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php70-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php70-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php71-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php71-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php71-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php71-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php71-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php71-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php72-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php72-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php72-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php72-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php72-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php72-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php73-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php73-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php73-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php73-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php73-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php73-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php74-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php74-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php74-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php74-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php74-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php74-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php80-php-pecl-imagick.x86_64              3.4.4-17.el7.remi          remi-safe
php80-php-pecl-imagick-devel.x86_64        3.4.4-17.el7.remi          remi-safe
php80-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php80-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php80-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php80-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php81-php-pecl-imagick.x86_64              3.4.4-18.el7.remi          remi-safe
php81-php-pecl-imagick-devel.x86_64        3.4.4-18.el7.remi          remi-safe
php81-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php81-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php81-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php81-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php82-php-pecl-imagick-im6.x86_64          3.7.0-7.el7.remi           remi-safe
php82-php-pecl-imagick-im6-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php82-php-pecl-imagick-im7.x86_64          3.7.0-7.el7.remi           remi-safe
php82-php-pecl-imagick-im7-devel.x86_64    3.7.0-7.el7.remi           remi-safe
php83-php-pecl-imagick-im6.x86_64          3.7.0-9.el7.remi           remi-safe
php83-php-pecl-imagick-im6-devel.x86_64    3.7.0-9.el7.remi           remi-safe
php83-php-pecl-imagick-im7.x86_64          3.7.0-9.el7.remi           remi-safe
php83-php-pecl-imagick-im7-devel.x86_64    3.7.0-9.el7.remi           remi-safe
[root@localhost ~]#
[root@localhost ~]# yum --enablerepo=remi-php74 install -y php-pecl-imagick
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                | 5.1 kB  00:00:00
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-php74: ftp.riken.jp
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
base                                                                | 3.6 kB  00:00:00
docker-ce-stable                                                    | 3.5 kB  00:00:00
extras                                                              | 2.9 kB  00:00:00
mysql-8.4-lts-community                                             | 2.6 kB  00:00:00
mysql-connectors-community                                          | 2.6 kB  00:00:00
mysql-tools-8.4-lts-community                                       | 2.6 kB  00:00:00
nux-dextop                                                          | 2.9 kB  00:00:00
remi-php74                                                          | 3.0 kB  00:00:00
remi-safe                                                           | 3.0 kB  00:00:00
updates                                                             | 2.9 kB  00:00:00
软件包 php-pecl-imagick 已经被 php-pecl-imagick-im6 取代，改为尝试安装 php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64
正在解决依赖关系
--> 正在检查事务
---> 软件包 php-pecl-imagick-im6.x86_64.0.3.7.0-7.el7.remi.7.4 将被 安装
--> 正在处理依赖关系 libMagickCore-6.Q16.so.7()(64bit)，它被软件包 php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64 需要
--> 正在处理依赖关系 libMagickWand-6.Q16.so.7()(64bit)，它被软件包 php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64 需要
--> 正在检查事务
---> 软件包 ImageMagick6-libs.x86_64.0.6.9.13.11-1.el7.remi 将被 安装
--> 正在处理依赖关系 open-sans-fonts，它被软件包 ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libcdt.so.5()(64bit)，它被软件包 ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libcgraph.so.6()(64bit)，它被软件包 ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libgvc.so.6()(64bit)，它被软件包 ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libraw_r.so.19()(64bit)，它被软件包 ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64 需要
--> 正在检查事务
---> 软件包 LibRaw.x86_64.0.0.19.4-2.el7_9 将被 安装
---> 软件包 graphviz.x86_64.0.2.30.1-22.el7 将被 安装
--> 正在处理依赖关系 libgtk-x11-2.0.so.0()(64bit)，它被软件包 graphviz-2.30.1-22.el7.x86_64 需要
--> 正在处理依赖关系 libgdk-x11-2.0.so.0()(64bit)，它被软件包 graphviz-2.30.1-22.el7.x86_64 需要
--> 正在处理依赖关系 libgd.so.2()(64bit)，它被软件包 graphviz-2.30.1-22.el7.x86_64 需要
--> 正在处理依赖关系 libatk-1.0.so.0()(64bit)，它被软件包 graphviz-2.30.1-22.el7.x86_64 需要
--> 正在处理依赖关系 libXaw.so.7()(64bit)，它被软件包 graphviz-2.30.1-22.el7.x86_64 需要
---> 软件包 open-sans-fonts.noarch.0.1.10-1.el7 将被 安装
--> 正在检查事务
---> 软件包 atk.x86_64.0.2.28.1-2.el7 将被 安装
---> 软件包 gd.x86_64.0.2.0.35-27.el7_9 将被 安装
---> 软件包 gtk2.x86_64.0.2.24.31-1.el7 将被 安装
--> 正在处理依赖关系 hicolor-icon-theme，它被软件包 gtk2-2.24.31-1.el7.x86_64 需要
--> 正在处理依赖关系 gtk-update-icon-cache，它被软件包 gtk2-2.24.31-1.el7.x86_64 需要
--> 正在处理依赖关系 libXcomposite.so.1()(64bit)，它被软件包 gtk2-2.24.31-1.el7.x86_64 需要
---> 软件包 libXaw.x86_64.0.1.0.13-4.el7 将被 安装
--> 正在检查事务
---> 软件包 gtk-update-icon-cache.x86_64.0.3.22.30-8.el7_9 将被 安装
---> 软件包 hicolor-icon-theme.noarch.0.0.12-7.el7 将被 安装
---> 软件包 libXcomposite.x86_64.0.0.4.4-4.1.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

===========================================================================================
 Package                    架构        版本                         源               大小
===========================================================================================
正在安装:
 php-pecl-imagick-im6       x86_64      3.7.0-7.el7.remi.7.4         remi-php74      185 k
为依赖而安装:
 ImageMagick6-libs          x86_64      6.9.13.11-1.el7.remi         remi-safe       2.4 M
 LibRaw                     x86_64      0.19.4-2.el7_9               updates         309 k
 atk                        x86_64      2.28.1-2.el7                 base            263 k
 gd                         x86_64      2.0.35-27.el7_9              updates         146 k
 graphviz                   x86_64      2.30.1-22.el7                base            1.3 M
 gtk-update-icon-cache      x86_64      3.22.30-8.el7_9              updates          27 k
 gtk2                       x86_64      2.24.31-1.el7                base            3.4 M
 hicolor-icon-theme         noarch      0.12-7.el7                   base             42 k
 libXaw                     x86_64      1.0.13-4.el7                 base            192 k
 libXcomposite              x86_64      0.4.4-4.1.el7                base             22 k
 open-sans-fonts            noarch      1.10-1.el7                   base            475 k

事务概要
===========================================================================================
安装  1 软件包 (+11 依赖软件包)

总下载量：8.7 M
安装大小：32 M
Downloading packages:
(1/12): LibRaw-0.19.4-2.el7_9.x86_64.rpm                            | 309 kB  00:00:00
(2/12): gtk-update-icon-cache-3.22.30-8.el7_9.x86_64.rpm            |  27 kB  00:00:00
(3/12): atk-2.28.1-2.el7.x86_64.rpm                                 | 263 kB  00:00:00
(4/12): graphviz-2.30.1-22.el7.x86_64.rpm                           | 1.3 MB  00:00:00
(5/12): hicolor-icon-theme-0.12-7.el7.noarch.rpm                    |  42 kB  00:00:00
(6/12): libXaw-1.0.13-4.el7.x86_64.rpm                              | 192 kB  00:00:00
(7/12): libXcomposite-0.4.4-4.1.el7.x86_64.rpm                      |  22 kB  00:00:00
(8/12): open-sans-fonts-1.10-1.el7.noarch.rpm                       | 475 kB  00:00:00
(9/12): gtk2-2.24.31-1.el7.x86_64.rpm                               | 3.4 MB  00:00:00
(10/12): gd-2.0.35-27.el7_9.x86_64.rpm                              | 146 kB  00:00:01
(11/12): php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64.rpm       | 185 kB  00:00:22
ImageMagick6-libs-6.9.13.11-1. FAILED                                          1:28:32 ETA
https://mirrors.uni-ruse.bg/remi/enterprise/7/safe/x86_64/ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64.rpm: [Errno 12] Timeout on https://mirrors.uni-ruse.bg/remi/enterprise/7/safe/x86_64/ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64.rpm: (28, 'Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds')
正在尝试其它镜像。
(12/12): ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64.rpm          | 2.4 MB  00:00:13
-------------------------------------------------------------------------------------------
总计                                                       151 kB/s | 8.7 MB  00:00:58
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : atk-2.28.1-2.el7.x86_64                                               1/12
  正在安装    : gd-2.0.35-27.el7_9.x86_64                                             2/12
  正在安装    : LibRaw-0.19.4-2.el7_9.x86_64                                          3/12
  正在安装    : gtk-update-icon-cache-3.22.30-8.el7_9.x86_64                          4/12
  正在安装    : libXcomposite-0.4.4-4.1.el7.x86_64                                    5/12
  正在安装    : libXaw-1.0.13-4.el7.x86_64                                            6/12
  正在安装    : open-sans-fonts-1.10-1.el7.noarch                                     7/12
  正在安装    : hicolor-icon-theme-0.12-7.el7.noarch                                  8/12
  正在安装    : gtk2-2.24.31-1.el7.x86_64                                             9/12
  正在安装    : graphviz-2.30.1-22.el7.x86_64                                        10/12
  正在安装    : ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64                        11/12
  正在安装    : php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64                     12/12
  验证中      : hicolor-icon-theme-0.12-7.el7.noarch                                  1/12
  验证中      : open-sans-fonts-1.10-1.el7.noarch                                     2/12
  验证中      : libXaw-1.0.13-4.el7.x86_64                                            3/12
  验证中      : ImageMagick6-libs-6.9.13.11-1.el7.remi.x86_64                         4/12
  验证中      : php-pecl-imagick-im6-3.7.0-7.el7.remi.7.4.x86_64                      5/12
  验证中      : libXcomposite-0.4.4-4.1.el7.x86_64                                    6/12
  验证中      : gtk-update-icon-cache-3.22.30-8.el7_9.x86_64                          7/12
  验证中      : LibRaw-0.19.4-2.el7_9.x86_64                                          8/12
  验证中      : atk-2.28.1-2.el7.x86_64                                               9/12
  验证中      : gtk2-2.24.31-1.el7.x86_64                                            10/12
  验证中      : gd-2.0.35-27.el7_9.x86_64                                            11/12
  验证中      : graphviz-2.30.1-22.el7.x86_64                                        12/12

已安装:
  php-pecl-imagick-im6.x86_64 0:3.7.0-7.el7.remi.7.4

作为依赖被安装:
  ImageMagick6-libs.x86_64 0:6.9.13.11-1.el7.remi
  LibRaw.x86_64 0:0.19.4-2.el7_9
  atk.x86_64 0:2.28.1-2.el7
  gd.x86_64 0:2.0.35-27.el7_9
  graphviz.x86_64 0:2.30.1-22.el7
  gtk-update-icon-cache.x86_64 0:3.22.30-8.el7_9
  gtk2.x86_64 0:2.24.31-1.el7
  hicolor-icon-theme.noarch 0:0.12-7.el7
  libXaw.x86_64 0:1.0.13-4.el7
  libXcomposite.x86_64 0:0.4.4-4.1.el7
  open-sans-fonts.noarch 0:1.10-1.el7

完毕！
[root@localhost ~]#
[root@localhost ~]# php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dom
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
imagick
json
libxml
mbstring
mcrypt
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
readline
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache

[root@localhost ~]#
```

以下是一个简单的示例，展示了如何使用 Imagick 创建一个新的图像，并绘制一些文本：
```php
<?php  
// 创建一个新的 Imagick 对象，并设置图像大小  
$imagick = new Imagick();  
$imagick->newImage(800, 600, new ImagickPixel('white'));  
  
// 设置字体和大小  
$draw = new ImagickDraw();  
$draw->setFont('path/to/your/font.ttf');  
$draw->setFontSize(36);  
  
// 设置文本颜色  
$draw->setFillColor(new ImagickPixel('black'));  
  
// 绘制文本  
$draw->annotation(10, 50, 'Hello, Imagick!');  
$imagick->drawImage($draw);  
  
// 设置图像格式  
$imagick->setImageFormat('png');  
  
// 输出图像到浏览器  
header("Content-Type: image/png");  
echo $imagick->getImageBlob();  
  
// 或者，将图像保存到文件  
// $imagick->writeImage('hello.png');  
?>
```

请注意，你需要将 'path/to/your/font.ttf' 替换为你系统上有效的字体文件路径。

## Python3安装

使用 yum 安装 Python 3：
> yum install python3

```
[root@localhost dx-txt2video]# yum install python3
已加载插件：fastestmirror
Determining fastest mirrors
epel/x86_64/metalink                                     | 5.1 kB     00:00
 * base: mirrors.aliyun.com
 * epel: dl.fedoraproject.org
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.aliyun.com
base                                                     | 3.6 kB     00:00
docker-ce-stable                                         | 3.5 kB     00:00
extras                                                   | 2.9 kB     00:00
mysql-8.4-lts-community                                  | 2.6 kB     00:00
mysql-connectors-community                               | 2.6 kB     00:00
mysql-tools-8.4-lts-community                            | 2.6 kB     00:00
nux-dextop                                               | 2.9 kB     00:00
remi-safe                                                | 3.0 kB     00:00
updates                                                  | 2.9 kB     00:00
(1/3): mysql-8.4-lts-community/x86_64/primary_db           |  41 kB   00:01
(2/3): mysql-connectors-community/x86_64/primary_db        | 124 kB   00:01
(3/3): mysql-tools-8.4-lts-community/x86_64/primary_db     |  12 kB   00:01
正在解决依赖关系
--> 正在检查事务
---> 软件包 python3.x86_64.0.3.6.8-21.el7_9 将被 安装
--> 正在处理依赖关系 python3-libs(x86-64) = 3.6.8-21.el7_9，它被软件包 python3-3.6.8-21.el7_9.x86_64 需要
--> 正在处理依赖关系 python3-setuptools，它被软件包 python3-3.6.8-21.el7_9.x86_64 需要
--> 正在处理依赖关系 python3-pip，它被软件包 python3-3.6.8-21.el7_9.x86_64 需要
--> 正在处理依赖关系 libpython3.6m.so.1.0()(64bit)，它被软件包 python3-3.6.8-21.el7_9.x86_64 需要
--> 正在检查事务
---> 软件包 python3-libs.x86_64.0.3.6.8-21.el7_9 将被 安装
--> 正在处理依赖关系 libtirpc.so.1()(64bit)，它被软件包 python3-libs-3.6.8-21.el7_9.x86_64 需要
---> 软件包 python3-pip.noarch.0.9.0.3-8.el7 将被 安装
---> 软件包 python3-setuptools.noarch.0.39.2.0-10.el7 将被 安装
--> 正在检查事务
---> 软件包 libtirpc.x86_64.0.0.2.4-0.16.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package                  架构         版本                 源             大小
================================================================================
正在安装:
 python3                  x86_64       3.6.8-21.el7_9       updates        71 k
为依赖而安装:
 libtirpc                 x86_64       0.2.4-0.16.el7       base           89 k
 python3-libs             x86_64       3.6.8-21.el7_9       updates       7.0 M
 python3-pip              noarch       9.0.3-8.el7          base          1.6 M
 python3-setuptools       noarch       39.2.0-10.el7        base          629 k

事务概要
================================================================================
安装  1 软件包 (+4 依赖软件包)

总下载量：9.3 M
安装大小：48 M
Is this ok [y/d/N]: y
Downloading packages:
(1/5): libtirpc-0.2.4-0.16.el7.x86_64.rpm                  |  89 kB   00:00
(2/5): python3-3.6.8-21.el7_9.x86_64.rpm                   |  71 kB   00:00
(3/5): python3-setuptools-39.2.0-10.el7.noarch.rpm         | 629 kB   00:00
(4/5): python3-pip-9.0.3-8.el7.noarch.rpm                  | 1.6 MB   00:01
(5/5): python3-libs-3.6.8-21.el7_9.x86_64.rpm              | 7.0 MB   00:04
--------------------------------------------------------------------------------
总计                                               2.1 MB/s | 9.3 MB  00:04
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : libtirpc-0.2.4-0.16.el7.x86_64                              1/5
  正在安装    : python3-setuptools-39.2.0-10.el7.noarch                     2/5
  正在安装    : python3-pip-9.0.3-8.el7.noarch                              3/5
  正在安装    : python3-3.6.8-21.el7_9.x86_64                               4/5
  正在安装    : python3-libs-3.6.8-21.el7_9.x86_64                          5/5
  验证中      : libtirpc-0.2.4-0.16.el7.x86_64                              1/5
  验证中      : python3-libs-3.6.8-21.el7_9.x86_64                          2/5
  验证中      : python3-3.6.8-21.el7_9.x86_64                               3/5
  验证中      : python3-setuptools-39.2.0-10.el7.noarch                     4/5
  验证中      : python3-pip-9.0.3-8.el7.noarch                              5/5

已安装:
  python3.x86_64 0:3.6.8-21.el7_9

作为依赖被安装:
  libtirpc.x86_64 0:0.2.4-0.16.el7   python3-libs.x86_64 0:3.6.8-21.el7_9
  python3-pip.noarch 0:9.0.3-8.el7   python3-setuptools.noarch 0:39.2.0-10.el7

完毕！
[root@localhost dx-txt2video]#
[root@localhost dx-txt2video]# python3 --version
Python 3.6.8
[root@localhost dx-txt2video]#
```

## 中文字体安装

Centos中默认是没有中文字体的，我们需要安装中文字体。可以直接从Windows系统中拿过来用，在目录 `C:\Windows\Fonts` 下。

准备工作，找到比较常用的 宋体、微软雅黑 放到 `C:\develop\chinese` 下，
然后整体移动到 `/usr/share/fonts/` 目录中，修改权限为755：
> mv /media/sf_develop/chinese /usr/share/fonts/
> 
> chmod -R 755 /usr/share/fonts/chinese

安装字体管理软件fontconfig：
> yum install -y fontconfig

更新字体缓存：
> fc-cache -fv

查看安装的中文字体：
> fc-list :lang=zh

详细：
```bash
[root@localhost ~]# ll /media/sf_develop/chinese/
总用量 65216
-rwxrwx--- 1 root vboxsf 16880832 8月  11 2023 msyhbd.ttc
-rwxrwx--- 1 root vboxsf 12183612 8月  11 2023 msyhl.ttc
-rwxrwx--- 1 root vboxsf 19701556 8月  11 2023 msyh.ttc
-rwxrwx--- 1 root vboxsf 18008680 8月  11 2023 simsun.ttc
[root@localhost ~]# 
[root@localhost ~]# mv /media/sf_develop/chinese /usr/share/fonts/
[root@localhost ~]# 
[root@localhost ~]# ll /usr/share/fonts | grep chinese
drwxrwx---  2 root vboxsf   75 9月  25 19:19 chinese
[root@localhost ~]# 
[root@localhost ~]# chmod -R 755 /usr/share/fonts/chinese
[root@localhost ~]# 
[root@localhost ~]# ll /usr/share/fonts | grep chinese
drwxr-xr-x  2 root vboxsf   75 9月  25 19:19 chinese
[root@localhost ~]# 
[root@localhost ~]# yum search fontconfig
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
========================================= N/S matched: fontconfig =========================================
fontconfig-devel-doc.noarch : Development Documentation files for fontconfig library
fontconfig.i686 : Font configuration and customization library
fontconfig.x86_64 : Font configuration and customization library
fontconfig-devel.i686 : Font configuration and customization library
fontconfig-devel.x86_64 : Font configuration and customization library
libeasyfc.i686 : Easy configuration generator interface for fontconfig
libeasyfc.x86_64 : Easy configuration generator interface for fontconfig
xemacs-xft.x86_64 : Different version of Emacs built with Xft/fontconfig support

  名称和简介匹配 only，使用“search all”试试。
[root@localhost ~]# 
[root@localhost ~]# yum list | grep fontconfig
fontconfig.x86_64                          2.13.0-4.3.el7             @base     
fontconfig.i686                            2.13.0-4.3.el7             base      
fontconfig-devel.i686                      2.13.0-4.3.el7             base      
fontconfig-devel.x86_64                    2.13.0-4.3.el7             base      
fontconfig-devel-doc.noarch                2.13.0-4.3.el7             base      
[root@localhost ~]# 
[root@localhost ~]# yum install -y fontconfig
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                                | 5.1 kB  00:00:00     
 * base: mirrors.aliyun.com
 * epel: repo.jing.rocks
 * extras: mirrors.aliyun.com
 * nux-dextop: li.nux.ro
 * remi-safe: ftp.riken.jp
 * updates: mirrors.aliyun.com
base                                                                                | 3.6 kB  00:00:00     
docker-ce-stable                                                                    | 3.5 kB  00:00:00     
extras                                                                              | 2.9 kB  00:00:00     
mysql-8.4-lts-community                                                             | 2.6 kB  00:00:00     
mysql-connectors-community                                                          | 2.6 kB  00:00:00     
mysql-tools-8.4-lts-community                                                       | 2.6 kB  00:00:00     
nux-dextop                                                                          | 2.9 kB  00:00:00     
remi-safe                                                                           | 3.0 kB  00:00:00     
updates                                                                             | 2.9 kB  00:00:00     
软件包 fontconfig-2.13.0-4.3.el7.x86_64 已安装并且是最新版本
无须任何处理
[root@localhost ~]# 
[root@localhost ~]# fc-cache --version
fontconfig version 2.13.0
[root@localhost ~]#
[root@localhost ~]# fc-cache -fv
/usr/share/fonts: 正在生成缓存，新增缓存内容：0 个字体，4 个目录
/usr/share/fonts/chinese: 正在生成缓存，新增缓存内容：8 个字体，0 个目录
/usr/share/fonts/dejavu: 正在生成缓存，新增缓存内容：9 个字体，0 个目录
/usr/share/fonts/open-sans: 正在生成缓存，新增缓存内容：10 个字体，0 个目录
/usr/share/fonts/urw-base35: 正在生成缓存，新增缓存内容：69 个字体，0 个目录
/usr/share/X11/fonts/Type1: 跳过，无此目录
/usr/share/X11/fonts/TTF: 跳过，无此目录
/usr/local/share/fonts: 跳过，无此目录
/root/.local/share/fonts: 跳过，无此目录
/root/.fonts: 跳过，无此目录
/usr/share/fonts/chinese: 跳过，探测到循环目录
/usr/share/fonts/dejavu: 跳过，探测到循环目录
/usr/share/fonts/open-sans: 跳过，探测到循环目录
/usr/share/fonts/urw-base35: 跳过，探测到循环目录
/usr/lib/fontconfig/cache: cleaning cache directory
/root/.cache/fontconfig: not cleaning non-existent cache directory
/root/.fontconfig: not cleaning non-existent cache directory
/usr/bin/fc-cache-64: 缓存生成成功
[root@localhost ~]# 
[root@localhost test]# fc-list :lang=zh
/usr/share/fonts/chinese/msyh.ttc: Microsoft YaHei:style=Normal
/usr/share/fonts/chinese/simsun.ttc: 宋体,SimSun:style=常规,Regular
/usr/share/fonts/chinese/msyhbd.ttc: Microsoft YaHei:style=Έντονα
/usr/share/fonts/chinese/msyh.ttc: Microsoft YaHei UI:style=Normal
/usr/share/fonts/chinese/msyhbd.ttc: Microsoft YaHei UI:style=Έντονα
/usr/share/fonts/chinese/simsun.ttc: 新宋体,NSimSun:style=常规,Regular
/usr/share/fonts/chinese/msyhl.ttc: Microsoft YaHei UI,Microsoft YaHei UI Light:style=Light,Regular
/usr/share/fonts/chinese/msyhl.ttc: Microsoft YaHei,Microsoft YaHei Light:style=Light,Regular
[root@localhost test]# 
```

## SELinux是什么

SELinux，全称为Security-Enhanced Linux，是一种在Linux操作系统上实施细粒度访问控制的安全机制。
它由美国国家安全局（NSA）联合其他安全机构共同开发，旨在增强传统Linux操作系统的安全性，
解决自主访问控制（DAC）系统中的各种权限问题，如root权限过高等。 

### SELinux的主要特点包括：

1. **强制访问控制（MAC）**：SELinux采用MAC系统，控制一个进程对具体文件系统上的文件或目录是否拥有访问权限。
2. 这种控制机制比传统的基于用户和组的访问控制更为严格和细致。

2. **安全策略**：SELinux使用了一个安全策略，该策略定义了每个进程、文件、目录等在系统中的访问权限。
3. 当进程或用户尝试访问一个对象时，SELinux会根据安全策略中定义的规则，检查请求是否合法，并决定是否允许访问。

3. **上下文（Context）**：在SELinux中，每个对象（如文件、目录、进程等）都有一个上下文，包括对象的类别和安全标签。
4. 这些上下文信息用于在访问控制决策中识别和区分不同的对象。

4. **内核级实现**：SELinux的安全策略是在Linux内核中实现的，它拦截系统调用和文件操作，并根据安全策略进行验证和授权。
5. 这种内核级的实现方式使得SELinux能够提供更强大的安全保护。

5. **可定制性**：SELinux提供了丰富的安全策略选项，用户可以根据自己的需求定制安全策略。
6. 这些策略可以包括不同的安全级别（如disabled、permissive、enforcing等）和不同的策略类型（如targeted、strict等）。

### SELinux的应用场景：

SELinux被广泛应用于对安全性要求较高的系统中，如服务器环境、金融系统、政府机构等。在这些系统中，
SELinux可以有效地防止未经授权的访问、提高系统的安全性和稳定性。

### 总结：

SELinux是一种在Linux操作系统上实施细粒度访问控制的安全机制，它通过强制访问控制和安全策略来增强系统的安全性。
SELinux的内核级实现和可定制性使其成为一种强大的安全工具，被广泛应用于对安全性要求较高的系统中。










<br/><br/><br/><br/><br/>
## 参考资料

VirtualBox中Centos7.9搭建及Docker安装各服务 <https://ibaiyang.github.io/blog/linux/2023/10/27/VirtualBox中Centos7.9搭建及Docker安装各服务.html>

Centos7.8 安装PHP7.4 <https://ibaiyang.github.io/blog/linux/2021/08/29/Centos7.8-安装PHP7.4.html>

Centos8（Liunx） 中安装PHP7.4 的三种方法和删除它的三种方法 <https://www.kancloud.cn/lk_super/mysql/2311557>

VirtualBox-搭建Centos7.9 <https://ibaiyang.github.io/blog/linux/2022/02/16/VirtualBox-搭建Centos7.9.html>

Linux YUM yum-utils 模块详解 <https://www.cnblogs.com/ryanpan/p/16422240.html>

yum 安装 mysql <https://cloud.tencent.com/developer/article/1441098?from=15425>

CentOS7中用yum安装Nginx配置Web服务器 <https://blog.csdn.net/Sunshine________/article/details/106419291>

yum安装ffmpeg <https://www.kancloud.cn/lengyueguang/linux/847013>

lengyueguang博客，上面有各类常用软件的安装及使用方式 <https://www.kancloud.cn/lengyueguang/linux>

详解npm源及其使用方法 <https://blog.csdn.net/kaka_buka/article/details/142379293>

Laravel npm install 报错syscall symlink的原因及解决办法 <https://www.jianshu.com/p/b7a1e6cfb95a>




