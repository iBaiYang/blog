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

### 解说

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


<br/><br/><br/><br/><br/>
## 参考资料

VirtualBox中Centos7.9搭建及Docker安装各服务 <https://ibaiyang.github.io/blog/linux/2023/10/27/VirtualBox中Centos7.9搭建及Docker安装各服务.html>

Centos7.8 安装PHP7.4 <https://ibaiyang.github.io/blog/linux/2021/08/29/Centos7.8-安装PHP7.4.html>

Centos8（Liunx） 中安装PHP7.4 的三种方法和删除它的三种方法 <https://www.kancloud.cn/lk_super/mysql/2311557>

