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

### swoole安装

搜索对应的swoole包：
```bash
> yum search pecl|grep swoole
> 
> yum list php74* | grep swoole
```

安装：
```bash
yum --enablerepo=remi-php74 install -y php74-php-pecl-swoole4
```

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
[root@localhost ~]# yum --enablerepo=remi-php74 install -y php74-php-pecl-swoole4
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
---> 软件包 php74-php-pecl-swoole4.x86_64.0.4.8.13-1.el7.remi 将被 安装
--> 正在处理依赖关系 php74-php(api) = 20190902-64，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php(zend-abi) = 20190902-64，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-curl(x86-64)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-json(x86-64)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-mysqlnd(x86-64)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-php-sockets(x86-64)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-runtime，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 php74-runtime(remi)(x86-64)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
--> 正在处理依赖关系 libcares.so.2()(64bit)，它被软件包 php74-php-pecl-swoole4-4.8.13-1.el7.remi.x86_64 需要
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

### 解说

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

在 C:\develop\vhost\virtualbox 下新建 test.local.conf 文件，写入内容：
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

在 C:\develop\www\test 下新建 index.html 文件，写入内容：
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

在 C:\develop\vhost\virtualbox 下新建 phpinfo.local.conf 文件，写入内容：
```
server {
    listen       80;
    server_name  phpinfo.local;

    location / {
        root   /media/sf_develop/www/phpinfo;
        index  index.html index.htm;
    }

    location ~ \.php$ {  
        root   /media/sf_develop/www/phpinfo;
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
        #fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
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

在 C:\develop\www\phpinfo 下新建 index.php 文件，写入内容：
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







