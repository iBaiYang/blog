---
layout: post
categories: Linux
title: 阿里云ECS之Docker使用V2
meta: 阿里云ECS之Docker使用第2版
---
* content
{:toc}

## 正文

在阿里云上购买ECS后，命令行root连接登录实例。

### docker安装

ECS实例是centos 7.6 64位系统。 centos 8.*版本安装会有问题。

移除老版本：
```
yum remove docker \
          docker-client \
          docker-client-latest \
          docker-common \
          docker-latest \
          docker-latest-logrotate \
          docker-logrotate \
          docker-selinux \
          docker-engine-selinux \
          docker-engine
```

输出：
```
Loaded plugins: fastestmirror
No Match for argument: docker

No Match for argument: docker-client
No Match for argument: docker-client-latest
No Match for argument: docker-common
No Match for argument: docker-latest
No Match for argument: docker-latest-logrotate
No Match for argument: docker-logrotate
No Match for argument: docker-selinux
No Match for argument: docker-engine-selinux
No Match for argument: docker-engine
No Packages marked for removal
```

安装docker:
> yum install docker

输出（是否下载时输入y）:
```
Loaded plugins: fastestmirror
Determining fastest mirrors
base                                                     | 3.6 kB     00:00
epel                                                     | 4.7 kB     00:00
extras                                                   | 2.9 kB     00:00
updates                                                  | 2.9 kB     00:00
(1/7): base/7/x86_64/group_gz                              | 153 kB   00:00
(2/7): epel/x86_64/group_gz                                |  96 kB   00:00
(3/7): epel/x86_64/updateinfo                              | 1.0 MB   00:00
(4/7): extras/7/x86_64/primary_db                          | 242 kB   00:00
(5/7): epel/x86_64/primary_db                              | 6.9 MB   00:00
(6/7): base/7/x86_64/primary_db                            | 6.1 MB   00:00
(7/7): updates/7/x86_64/primary_db                         | 9.6 MB   00:00
Resolving Dependencies
--> Running transaction check
---> Package docker.x86_64 2:1.13.1-208.git7d71120.el7_9 will be installed
--> Processing Dependency: docker-common = 2:1.13.1-208.git7d71120.el7_9 for pac kage: 2:docker-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: docker-client = 2:1.13.1-208.git7d71120.el7_9 for pac kage: 2:docker-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: subscription-manager-rhsm-certificates for package: 2 :docker-1.13.1-208.git7d71120.el7_9.x86_64
--> Running transaction check
---> Package docker-client.x86_64 2:1.13.1-208.git7d71120.el7_9 will be installe d
---> Package docker-common.x86_64 2:1.13.1-208.git7d71120.el7_9 will be installe d
--> Processing Dependency: skopeo-containers >= 1:0.1.26-2 for package: 2:docker -common-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: oci-umount >= 2:2.3.3-3 for package: 2:docker-common- 1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: oci-systemd-hook >= 1:0.1.4-9 for package: 2:docker-c ommon-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: oci-register-machine >= 1:0-5.13 for package: 2:docke r-common-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: lvm2 >= 2.02.112 for package: 2:docker-common-1.13.1- 208.git7d71120.el7_9.x86_64
--> Processing Dependency: container-storage-setup >= 0.9.0-1 for package: 2:doc ker-common-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: container-selinux >= 2:2.51-1 for package: 2:docker-c ommon-1.13.1-208.git7d71120.el7_9.x86_64
--> Processing Dependency: atomic-registries for package: 2:docker-common-1.13.1 -208.git7d71120.el7_9.x86_64
---> Package subscription-manager-rhsm-certificates.x86_64 0:1.24.48-1.el7.cento s will be installed
--> Running transaction check
---> Package atomic-registries.x86_64 1:1.22.1-33.gitb507039.el7_8 will be insta lled
--> Processing Dependency: python-yaml for package: 1:atomic-registries-1.22.1-3 3.gitb507039.el7_8.x86_64
--> Processing Dependency: python-setuptools for package: 1:atomic-registries-1. 22.1-33.gitb507039.el7_8.x86_64
--> Processing Dependency: python-pytoml for package: 1:atomic-registries-1.22.1 -33.gitb507039.el7_8.x86_64
---> Package container-selinux.noarch 2:2.119.2-1.911c772.el7_8 will be installe d
--> Processing Dependency: policycoreutils-python for package: 2:container-selin ux-2.119.2-1.911c772.el7_8.noarch
---> Package container-storage-setup.noarch 0:0.11.0-2.git5eaf76c.el7 will be in stalled
---> Package containers-common.x86_64 1:0.1.40-11.el7_8 will be installed
--> Processing Dependency: subscription-manager for package: 1:containers-common -0.1.40-11.el7_8.x86_64
--> Processing Dependency: slirp4netns for package: 1:containers-common-0.1.40-1 1.el7_8.x86_64
--> Processing Dependency: fuse-overlayfs for package: 1:containers-common-0.1.4 0-11.el7_8.x86_64
---> Package lvm2.x86_64 7:2.02.187-6.el7_9.5 will be installed
--> Processing Dependency: lvm2-libs = 7:2.02.187-6.el7_9.5 for package: 7:lvm2- 2.02.187-6.el7_9.5.x86_64
--> Processing Dependency: device-mapper-persistent-data >= 0.7.0-0.1.rc6 for pa ckage: 7:lvm2-2.02.187-6.el7_9.5.x86_64
--> Processing Dependency: liblvm2app.so.2.2(Base)(64bit) for package: 7:lvm2-2. 02.187-6.el7_9.5.x86_64
--> Processing Dependency: libdevmapper-event.so.1.02(Base)(64bit) for package:  7:lvm2-2.02.187-6.el7_9.5.x86_64
--> Processing Dependency: libaio.so.1(LIBAIO_0.4)(64bit) for package: 7:lvm2-2. 02.187-6.el7_9.5.x86_64
--> Processing Dependency: libaio.so.1(LIBAIO_0.1)(64bit) for package: 7:lvm2-2. 02.187-6.el7_9.5.x86_64
--> Processing Dependency: liblvm2app.so.2.2()(64bit) for package: 7:lvm2-2.02.1 87-6.el7_9.5.x86_64
--> Processing Dependency: libdevmapper-event.so.1.02()(64bit) for package: 7:lv m2-2.02.187-6.el7_9.5.x86_64
--> Processing Dependency: libaio.so.1()(64bit) for package: 7:lvm2-2.02.187-6.e l7_9.5.x86_64
---> Package oci-register-machine.x86_64 1:0-6.git2b44233.el7 will be installed
---> Package oci-systemd-hook.x86_64 1:0.2.0-1.git05e6923.el7_6 will be installe d
--> Processing Dependency: libyajl.so.2()(64bit) for package: 1:oci-systemd-hook -0.2.0-1.git05e6923.el7_6.x86_64
---> Package oci-umount.x86_64 2:2.5-3.el7 will be installed
--> Running transaction check
---> Package PyYAML.x86_64 0:3.10-11.el7 will be installed
--> Processing Dependency: libyaml-0.so.2()(64bit) for package: PyYAML-3.10-11.e l7.x86_64
---> Package device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5 will be instal led
---> Package device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2 will be inst alled
---> Package fuse-overlayfs.x86_64 0:0.7.2-6.el7_8 will be installed
--> Processing Dependency: libfuse3.so.3(FUSE_3.2)(64bit) for package: fuse-over layfs-0.7.2-6.el7_8.x86_64
--> Processing Dependency: libfuse3.so.3(FUSE_3.0)(64bit) for package: fuse-over layfs-0.7.2-6.el7_8.x86_64
--> Processing Dependency: libfuse3.so.3()(64bit) for package: fuse-overlayfs-0. 7.2-6.el7_8.x86_64
---> Package libaio.x86_64 0:0.3.109-13.el7 will be installed
---> Package lvm2-libs.x86_64 7:2.02.187-6.el7_9.5 will be installed
--> Processing Dependency: device-mapper-event = 7:1.02.170-6.el7_9.5 for packag e: 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64
---> Package policycoreutils-python.x86_64 0:2.5-34.el7 will be installed
--> Processing Dependency: setools-libs >= 3.3.8-4 for package: policycoreutils- python-2.5-34.el7.x86_64
--> Processing Dependency: libsemanage-python >= 2.5-14 for package: policycoreu tils-python-2.5-34.el7.x86_64
--> Processing Dependency: audit-libs-python >= 2.1.3-4 for package: policycoreu tils-python-2.5-34.el7.x86_64
--> Processing Dependency: python-IPy for package: policycoreutils-python-2.5-34 .el7.x86_64
--> Processing Dependency: libqpol.so.1(VERS_1.4)(64bit) for package: policycore utils-python-2.5-34.el7.x86_64
--> Processing Dependency: libqpol.so.1(VERS_1.2)(64bit) for package: policycore utils-python-2.5-34.el7.x86_64
--> Processing Dependency: libcgroup for package: policycoreutils-python-2.5-34. el7.x86_64
--> Processing Dependency: libapol.so.4(VERS_4.0)(64bit) for package: policycore utils-python-2.5-34.el7.x86_64
--> Processing Dependency: checkpolicy for package: policycoreutils-python-2.5-3 4.el7.x86_64
--> Processing Dependency: libqpol.so.1()(64bit) for package: policycoreutils-py thon-2.5-34.el7.x86_64
--> Processing Dependency: libapol.so.4()(64bit) for package: policycoreutils-py thon-2.5-34.el7.x86_64
---> Package python-pytoml.noarch 0:0.1.14-1.git7dea353.el7 will be installed
---> Package python-setuptools.noarch 0:0.9.8-7.el7 will be installed
--> Processing Dependency: python-backports-ssl_match_hostname for package: pyth on-setuptools-0.9.8-7.el7.noarch
---> Package slirp4netns.x86_64 0:0.4.3-4.el7_8 will be installed
---> Package subscription-manager.x86_64 0:1.24.48-1.el7.centos will be installe d
--> Processing Dependency: subscription-manager-rhsm = 1.24.48 for package: subs cription-manager-1.24.48-1.el7.centos.x86_64
--> Processing Dependency: python-dmidecode >= 3.12.2-2 for package: subscriptio n-manager-1.24.48-1.el7.centos.x86_64
--> Processing Dependency: usermode for package: subscription-manager-1.24.48-1. el7.centos.x86_64
--> Processing Dependency: python-syspurpose for package: subscription-manager-1 .24.48-1.el7.centos.x86_64
--> Processing Dependency: python-six for package: subscription-manager-1.24.48- 1.el7.centos.x86_64
--> Processing Dependency: python-inotify for package: subscription-manager-1.24 .48-1.el7.centos.x86_64
--> Processing Dependency: python-ethtool for package: subscription-manager-1.24 .48-1.el7.centos.x86_64
--> Processing Dependency: python-dateutil for package: subscription-manager-1.2 4.48-1.el7.centos.x86_64
---> Package yajl.x86_64 0:2.0.4-4.el7 will be installed
--> Running transaction check
---> Package audit-libs-python.x86_64 0:2.8.5-4.el7 will be installed
---> Package checkpolicy.x86_64 0:2.5-8.el7 will be installed
---> Package device-mapper-event.x86_64 7:1.02.170-6.el7_9.5 will be installed
--> Processing Dependency: device-mapper = 7:1.02.170-6.el7_9.5 for package: 7:d evice-mapper-event-1.02.170-6.el7_9.5.x86_64
---> Package fuse3-libs.x86_64 0:3.6.1-4.el7 will be installed
---> Package libcgroup.x86_64 0:0.41-21.el7 will be installed
---> Package libsemanage-python.x86_64 0:2.5-14.el7 will be installed
---> Package libyaml.x86_64 0:0.1.4-11.el7_0 will be installed
---> Package python-IPy.noarch 0:0.75-6.el7 will be installed
---> Package python-backports-ssl_match_hostname.noarch 0:3.5.0.1-1.el7 will be  installed
--> Processing Dependency: python-ipaddress for package: python-backports-ssl_ma tch_hostname-3.5.0.1-1.el7.noarch
--> Processing Dependency: python-backports for package: python-backports-ssl_ma tch_hostname-3.5.0.1-1.el7.noarch
---> Package python-dateutil.noarch 0:1.5-7.el7 will be installed
---> Package python-dmidecode.x86_64 0:3.12.2-4.el7 will be installed
--> Processing Dependency: libxml2-python for package: python-dmidecode-3.12.2-4 .el7.x86_64
--> Processing Dependency: libxml2mod.so()(64bit) for package: python-dmidecode- 3.12.2-4.el7.x86_64
---> Package python-ethtool.x86_64 0:0.8-8.el7 will be installed
--> Processing Dependency: libnl.so.1()(64bit) for package: python-ethtool-0.8-8 .el7.x86_64
---> Package python-inotify.noarch 0:0.9.4-4.el7 will be installed
---> Package python-six.noarch 0:1.9.0-2.el7 will be installed
---> Package python-syspurpose.x86_64 0:1.24.48-1.el7.centos will be installed
---> Package setools-libs.x86_64 0:3.3.8-4.el7 will be installed
---> Package subscription-manager-rhsm.x86_64 0:1.24.48-1.el7.centos will be ins talled
---> Package usermode.x86_64 0:1.111-6.el7 will be installed
--> Running transaction check
---> Package device-mapper.x86_64 7:1.02.164-7.el7_8.2 will be updated
--> Processing Dependency: device-mapper = 7:1.02.164-7.el7_8.2 for package: 7:d evice-mapper-libs-1.02.164-7.el7_8.2.x86_64
---> Package device-mapper.x86_64 7:1.02.170-6.el7_9.5 will be an update
---> Package libnl.x86_64 0:1.1.4-3.el7 will be installed
---> Package libxml2-python.x86_64 0:2.9.1-6.el7.5 will be installed
--> Processing Dependency: libxml2 = 2.9.1-6.el7.5 for package: libxml2-python-2 .9.1-6.el7.5.x86_64
---> Package python-backports.x86_64 0:1.0-8.el7 will be installed
---> Package python-ipaddress.noarch 0:1.0.16-2.el7 will be installed
--> Running transaction check
---> Package device-mapper-libs.x86_64 7:1.02.164-7.el7_8.2 will be updated
---> Package device-mapper-libs.x86_64 7:1.02.170-6.el7_9.5 will be an update
---> Package libxml2.x86_64 0:2.9.1-6.el7.4 will be updated
---> Package libxml2.x86_64 0:2.9.1-6.el7.5 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                     Arch   Version                       Repository
                                                                           Size
================================================================================
Installing:
 docker                      x86_64 2:1.13.1-208.git7d71120.el7_9 extras   17 M
Installing for dependencies:
 PyYAML                      x86_64 3.10-11.el7                   base    153 k
 atomic-registries           x86_64 1:1.22.1-33.gitb507039.el7_8  extras   36 k
 audit-libs-python           x86_64 2.8.5-4.el7                   base     76 k
 checkpolicy                 x86_64 2.5-8.el7                     base    295 k
 container-selinux           noarch 2:2.119.2-1.911c772.el7_8     extras   40 k
 container-storage-setup     noarch 0.11.0-2.git5eaf76c.el7       extras   35 k
 containers-common           x86_64 1:0.1.40-11.el7_8             extras   43 k
 device-mapper-event         x86_64 7:1.02.170-6.el7_9.5          updates 192 k
 device-mapper-event-libs    x86_64 7:1.02.170-6.el7_9.5          updates 192 k
 device-mapper-persistent-data
                             x86_64 0.8.5-3.el7_9.2               updates 423 k
 docker-client               x86_64 2:1.13.1-208.git7d71120.el7_9 extras  3.9 M
 docker-common               x86_64 2:1.13.1-208.git7d71120.el7_9 extras  101 k
 fuse-overlayfs              x86_64 0.7.2-6.el7_8                 extras   54 k
 fuse3-libs                  x86_64 3.6.1-4.el7                   extras   82 k
 libaio                      x86_64 0.3.109-13.el7                base     24 k
 libcgroup                   x86_64 0.41-21.el7                   base     66 k
 libnl                       x86_64 1.1.4-3.el7                   base    128 k
 libsemanage-python          x86_64 2.5-14.el7                    base    113 k
 libxml2-python              x86_64 2.9.1-6.el7.5                 base    247 k
 libyaml                     x86_64 0.1.4-11.el7_0                base     55 k
 lvm2                        x86_64 7:2.02.187-6.el7_9.5          updates 1.3 M
 lvm2-libs                   x86_64 7:2.02.187-6.el7_9.5          updates 1.1 M
 oci-register-machine        x86_64 1:0-6.git2b44233.el7          extras  1.1 M
 oci-systemd-hook            x86_64 1:0.2.0-1.git05e6923.el7_6    extras   34 k
 oci-umount                  x86_64 2:2.5-3.el7                   extras   33 k
 policycoreutils-python      x86_64 2.5-34.el7                    base    457 k
 python-IPy                  noarch 0.75-6.el7                    base     32 k
 python-backports            x86_64 1.0-8.el7                     base    5.8 k
 python-backports-ssl_match_hostname
                             noarch 3.5.0.1-1.el7                 base     13 k
 python-dateutil             noarch 1.5-7.el7                     base     85 k
 python-dmidecode            x86_64 3.12.2-4.el7                  base     83 k
 python-ethtool              x86_64 0.8-8.el7                     base     34 k
 python-inotify              noarch 0.9.4-4.el7                   base     49 k
 python-ipaddress            noarch 1.0.16-2.el7                  base     34 k
 python-pytoml               noarch 0.1.14-1.git7dea353.el7       extras   18 k
 python-setuptools           noarch 0.9.8-7.el7                   base    397 k
 python-six                  noarch 1.9.0-2.el7                   base     29 k
 python-syspurpose           x86_64 1.24.48-1.el7.centos          updates 274 k
 setools-libs                x86_64 3.3.8-4.el7                   base    620 k
 slirp4netns                 x86_64 0.4.3-4.el7_8                 extras   81 k
 subscription-manager        x86_64 1.24.48-1.el7.centos          updates 1.1 M
 subscription-manager-rhsm   x86_64 1.24.48-1.el7.centos          updates 333 k
 subscription-manager-rhsm-certificates
                             x86_64 1.24.48-1.el7.centos          updates 237 k
 usermode                    x86_64 1.111-6.el7                   base    193 k
 yajl                        x86_64 2.0.4-4.el7                   base     39 k
Updating for dependencies:
 device-mapper               x86_64 7:1.02.170-6.el7_9.5          updates 297 k
 device-mapper-libs          x86_64 7:1.02.170-6.el7_9.5          updates 325 k
 libxml2                     x86_64 2.9.1-6.el7.5                 base    668 k

Transaction Summary
================================================================================
Install  1 Package  (+45 Dependent packages)
Upgrade             (  3 Dependent packages)

Total download size: 32 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/49): audit-libs-python-2.8.5-4.el7.x86_64.rpm           |  76 kB   00:00
(2/49): atomic-registries-1.22.1-33.gitb507039.el7_8.x86_6 |  36 kB   00:00
(3/49): PyYAML-3.10-11.el7.x86_64.rpm                      | 153 kB   00:00
(4/49): container-storage-setup-0.11.0-2.git5eaf76c.el7.no |  35 kB   00:00
(5/49): containers-common-0.1.40-11.el7_8.x86_64.rpm       |  43 kB   00:00
(6/49): checkpolicy-2.5-8.el7.x86_64.rpm                   | 295 kB   00:00
(7/49): container-selinux-2.119.2-1.911c772.el7_8.noarch.r |  40 kB   00:00
(8/49): device-mapper-event-1.02.170-6.el7_9.5.x86_64.rpm  | 192 kB   00:00
(9/49): device-mapper-1.02.170-6.el7_9.5.x86_64.rpm        | 297 kB   00:00
(10/49): device-mapper-event-libs-1.02.170-6.el7_9.5.x86_6 | 192 kB   00:00
(11/49): device-mapper-libs-1.02.170-6.el7_9.5.x86_64.rpm  | 325 kB   00:00
(12/49): device-mapper-persistent-data-0.8.5-3.el7_9.2.x86 | 423 kB   00:00
(13/49): docker-client-1.13.1-208.git7d71120.el7_9.x86_64. | 3.9 MB   00:00
(14/49): docker-common-1.13.1-208.git7d71120.el7_9.x86_64. | 101 kB   00:00
(15/49): fuse-overlayfs-0.7.2-6.el7_8.x86_64.rpm           |  54 kB   00:00
(16/49): fuse3-libs-3.6.1-4.el7.x86_64.rpm                 |  82 kB   00:00
(17/49): libaio-0.3.109-13.el7.x86_64.rpm                  |  24 kB   00:00
(18/49): libcgroup-0.41-21.el7.x86_64.rpm                  |  66 kB   00:00
(19/49): libsemanage-python-2.5-14.el7.x86_64.rpm          | 113 kB   00:00
(20/49): libnl-1.1.4-3.el7.x86_64.rpm                      | 128 kB   00:00
(21/49): libxml2-python-2.9.1-6.el7.5.x86_64.rpm           | 247 kB   00:00
(22/49): docker-1.13.1-208.git7d71120.el7_9.x86_64.rpm     |  17 MB   00:00
(23/49): libyaml-0.1.4-11.el7_0.x86_64.rpm                 |  55 kB   00:00
(24/49): libxml2-2.9.1-6.el7.5.x86_64.rpm                  | 668 kB   00:00
(25/49): oci-systemd-hook-0.2.0-1.git05e6923.el7_6.x86_64. |  34 kB   00:00
(26/49): oci-umount-2.5-3.el7.x86_64.rpm                   |  33 kB   00:00
(27/49): oci-register-machine-0-6.git2b44233.el7.x86_64.rp | 1.1 MB   00:00
(28/49): python-IPy-0.75-6.el7.noarch.rpm                  |  32 kB   00:00
(29/49): python-backports-1.0-8.el7.x86_64.rpm             | 5.8 kB   00:00
(30/49): python-backports-ssl_match_hostname-3.5.0.1-1.el7 |  13 kB   00:00
(31/49): lvm2-2.02.187-6.el7_9.5.x86_64.rpm                | 1.3 MB   00:00
(32/49): lvm2-libs-2.02.187-6.el7_9.5.x86_64.rpm           | 1.1 MB   00:00
(33/49): python-dateutil-1.5-7.el7.noarch.rpm              |  85 kB   00:00
(34/49): python-dmidecode-3.12.2-4.el7.x86_64.rpm          |  83 kB   00:00
(35/49): python-ethtool-0.8-8.el7.x86_64.rpm               |  34 kB   00:00
(36/49): policycoreutils-python-2.5-34.el7.x86_64.rpm      | 457 kB   00:00
(37/49): python-ipaddress-1.0.16-2.el7.noarch.rpm          |  34 kB   00:00
(38/49): python-inotify-0.9.4-4.el7.noarch.rpm             |  49 kB   00:00
(39/49): python-setuptools-0.9.8-7.el7.noarch.rpm          | 397 kB   00:00
(40/49): setools-libs-3.3.8-4.el7.x86_64.rpm               | 620 kB   00:00
(41/49): subscription-manager-1.24.48-1.el7.centos.x86_64. | 1.1 MB   00:00
(42/49): python-pytoml-0.1.14-1.git7dea353.el7.noarch.rpm  |  18 kB   00:00
(43/49): subscription-manager-rhsm-1.24.48-1.el7.centos.x8 | 333 kB   00:00
(44/49): python-six-1.9.0-2.el7.noarch.rpm                 |  29 kB   00:00
(45/49): slirp4netns-0.4.3-4.el7_8.x86_64.rpm              |  81 kB   00:00
(46/49): subscription-manager-rhsm-certificates-1.24.48-1. | 237 kB   00:00
(47/49): python-syspurpose-1.24.48-1.el7.centos.x86_64.rpm | 274 kB   00:00
(48/49): yajl-2.0.4-4.el7.x86_64.rpm                       |  39 kB   00:00
(49/49): usermode-1.111-6.el7.x86_64.rpm                   | 193 kB   00:00
--------------------------------------------------------------------------------
Total                                               20 MB/s |  32 MB  00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Updating   : 7:device-mapper-1.02.170-6.el7_9.5.x86_64                   1/52
  Updating   : 7:device-mapper-libs-1.02.170-6.el7_9.5.x86_64              2/52
  Installing : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64        3/52
  Installing : libaio-0.3.109-13.el7.x86_64                                4/52
  Updating   : libxml2-2.9.1-6.el7.5.x86_64                                5/52
  Installing : yajl-2.0.4-4.el7.x86_64                                     6/52
  Installing : python-six-1.9.0-2.el7.noarch                               7/52
  Installing : python-dateutil-1.5-7.el7.noarch                            8/52
  Installing : subscription-manager-rhsm-certificates-1.24.48-1.el7.cen    9/52
  Installing : subscription-manager-rhsm-1.24.48-1.el7.centos.x86_64      10/52
  Installing : 2:oci-umount-2.5-3.el7.x86_64                              11/52
  Installing : 1:oci-systemd-hook-0.2.0-1.git05e6923.el7_6.x86_64         12/52
  Installing : setools-libs-3.3.8-4.el7.x86_64                            13/52
  Installing : libxml2-python-2.9.1-6.el7.5.x86_64                        14/52
  Installing : python-dmidecode-3.12.2-4.el7.x86_64                       15/52
  Installing : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64       16/52
  Installing : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64            17/52
  Installing : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                      18/52
  Installing : 7:lvm2-2.02.187-6.el7_9.5.x86_64                           19/52
  Installing : container-storage-setup-0.11.0-2.git5eaf76c.el7.noarch     20/52
  Installing : python-syspurpose-1.24.48-1.el7.centos.x86_64              21/52
  Installing : libcgroup-0.41-21.el7.x86_64                               22/52
  Installing : python-ipaddress-1.0.16-2.el7.noarch                       23/52
  Installing : libnl-1.1.4-3.el7.x86_64                                   24/52
  Installing : python-ethtool-0.8-8.el7.x86_64                            25/52
  Installing : audit-libs-python-2.8.5-4.el7.x86_64                       26/52
  Installing : python-backports-1.0-8.el7.x86_64                          27/52
  Installing : python-backports-ssl_match_hostname-3.5.0.1-1.el7.noarch   28/52
  Installing : python-setuptools-0.9.8-7.el7.noarch                       29/52
  Installing : slirp4netns-0.4.3-4.el7_8.x86_64                           30/52
  Installing : checkpolicy-2.5-8.el7.x86_64                               31/52
  Installing : 1:oci-register-machine-0-6.git2b44233.el7.x86_64           32/52
  Installing : libsemanage-python-2.5-14.el7.x86_64                       33/52
  Installing : libyaml-0.1.4-11.el7_0.x86_64                              34/52
  Installing : PyYAML-3.10-11.el7.x86_64                                  35/52
  Installing : fuse3-libs-3.6.1-4.el7.x86_64                              36/52
  Installing : fuse-overlayfs-0.7.2-6.el7_8.x86_64                        37/52
  Installing : usermode-1.111-6.el7.x86_64                                38/52
  Installing : python-pytoml-0.1.14-1.git7dea353.el7.noarch               39/52
  Installing : 1:atomic-registries-1.22.1-33.gitb507039.el7_8.x86_64      40/52
  Installing : python-IPy-0.75-6.el7.noarch                               41/52
  Installing : policycoreutils-python-2.5-34.el7.x86_64                   42/52
  Installing : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch         43/52
setsebool:  SELinux is disabled.
  Installing : python-inotify-0.9.4-4.el7.noarch                          44/52
  Installing : subscription-manager-1.24.48-1.el7.centos.x86_64           45/52
  Installing : 1:containers-common-0.1.40-11.el7_8.x86_64                 46/52
  Installing : 2:docker-common-1.13.1-208.git7d71120.el7_9.x86_64         47/52
  Installing : 2:docker-client-1.13.1-208.git7d71120.el7_9.x86_64         48/52
  Installing : 2:docker-1.13.1-208.git7d71120.el7_9.x86_64                49/52
  Cleanup    : 7:device-mapper-1.02.164-7.el7_8.2.x86_64                  50/52
  Cleanup    : 7:device-mapper-libs-1.02.164-7.el7_8.2.x86_64             51/52
  Cleanup    : libxml2-2.9.1-6.el7.4.x86_64                               52/52
  Verifying  : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64             1/52
  Verifying  : python-backports-ssl_match_hostname-3.5.0.1-1.el7.noarch    2/52
  Verifying  : 1:atomic-registries-1.22.1-33.gitb507039.el7_8.x86_64       3/52
  Verifying  : python-ethtool-0.8-8.el7.x86_64                             4/52
  Verifying  : fuse-overlayfs-0.7.2-6.el7_8.x86_64                         5/52
  Verifying  : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch          6/52
  Verifying  : container-storage-setup-0.11.0-2.git5eaf76c.el7.noarch      7/52
  Verifying  : python-inotify-0.9.4-4.el7.noarch                           8/52
  Verifying  : 1:containers-common-0.1.40-11.el7_8.x86_64                  9/52
  Verifying  : python-setuptools-0.9.8-7.el7.noarch                       10/52
  Verifying  : 7:device-mapper-libs-1.02.170-6.el7_9.5.x86_64             11/52
  Verifying  : subscription-manager-rhsm-certificates-1.24.48-1.el7.cen   12/52
  Verifying  : libxml2-2.9.1-6.el7.5.x86_64                               13/52
  Verifying  : python-IPy-0.75-6.el7.noarch                               14/52
  Verifying  : python-dateutil-1.5-7.el7.noarch                           15/52
  Verifying  : python-dmidecode-3.12.2-4.el7.x86_64                       16/52
  Verifying  : 2:oci-umount-2.5-3.el7.x86_64                              17/52
  Verifying  : policycoreutils-python-2.5-34.el7.x86_64                   18/52
  Verifying  : python-pytoml-0.1.14-1.git7dea353.el7.noarch               19/52
  Verifying  : python-six-1.9.0-2.el7.noarch                              20/52
  Verifying  : 7:lvm2-2.02.187-6.el7_9.5.x86_64                           21/52
  Verifying  : usermode-1.111-6.el7.x86_64                                22/52
  Verifying  : setools-libs-3.3.8-4.el7.x86_64                            23/52
  Verifying  : libxml2-python-2.9.1-6.el7.5.x86_64                        24/52
  Verifying  : fuse3-libs-3.6.1-4.el7.x86_64                              25/52
  Verifying  : libyaml-0.1.4-11.el7_0.x86_64                              26/52
  Verifying  : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                      27/52
  Verifying  : libsemanage-python-2.5-14.el7.x86_64                       28/52
  Verifying  : 1:oci-systemd-hook-0.2.0-1.git05e6923.el7_6.x86_64         29/52
  Verifying  : 1:oci-register-machine-0-6.git2b44233.el7.x86_64           30/52
  Verifying  : checkpolicy-2.5-8.el7.x86_64                               31/52
  Verifying  : slirp4netns-0.4.3-4.el7_8.x86_64                           32/52
  Verifying  : python-backports-1.0-8.el7.x86_64                          33/52
  Verifying  : yajl-2.0.4-4.el7.x86_64                                    34/52
  Verifying  : audit-libs-python-2.8.5-4.el7.x86_64                       35/52
  Verifying  : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64       36/52
  Verifying  : subscription-manager-rhsm-1.24.48-1.el7.centos.x86_64      37/52
  Verifying  : 2:docker-common-1.13.1-208.git7d71120.el7_9.x86_64         38/52
  Verifying  : 2:docker-1.13.1-208.git7d71120.el7_9.x86_64                39/52
  Verifying  : libaio-0.3.109-13.el7.x86_64                               40/52
  Verifying  : libnl-1.1.4-3.el7.x86_64                                   41/52
  Verifying  : 2:docker-client-1.13.1-208.git7d71120.el7_9.x86_64         42/52
  Verifying  : python-ipaddress-1.0.16-2.el7.noarch                       43/52
  Verifying  : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64       44/52
  Verifying  : 7:device-mapper-1.02.170-6.el7_9.5.x86_64                  45/52
  Verifying  : PyYAML-3.10-11.el7.x86_64                                  46/52
  Verifying  : libcgroup-0.41-21.el7.x86_64                               47/52
  Verifying  : python-syspurpose-1.24.48-1.el7.centos.x86_64              48/52
  Verifying  : subscription-manager-1.24.48-1.el7.centos.x86_64           49/52
  Verifying  : 7:device-mapper-1.02.164-7.el7_8.2.x86_64                  50/52
  Verifying  : 7:device-mapper-libs-1.02.164-7.el7_8.2.x86_64             51/52
  Verifying  : libxml2-2.9.1-6.el7.4.x86_64                               52/52

Installed:
  docker.x86_64 2:1.13.1-208.git7d71120.el7_9

Dependency Installed:
  PyYAML.x86_64 0:3.10-11.el7
  atomic-registries.x86_64 1:1.22.1-33.gitb507039.el7_8
  audit-libs-python.x86_64 0:2.8.5-4.el7
  checkpolicy.x86_64 0:2.5-8.el7
  container-selinux.noarch 2:2.119.2-1.911c772.el7_8
  container-storage-setup.noarch 0:0.11.0-2.git5eaf76c.el7
  containers-common.x86_64 1:0.1.40-11.el7_8
  device-mapper-event.x86_64 7:1.02.170-6.el7_9.5
  device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5
  device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2
  docker-client.x86_64 2:1.13.1-208.git7d71120.el7_9
  docker-common.x86_64 2:1.13.1-208.git7d71120.el7_9
  fuse-overlayfs.x86_64 0:0.7.2-6.el7_8
  fuse3-libs.x86_64 0:3.6.1-4.el7
  libaio.x86_64 0:0.3.109-13.el7
  libcgroup.x86_64 0:0.41-21.el7
  libnl.x86_64 0:1.1.4-3.el7
  libsemanage-python.x86_64 0:2.5-14.el7
  libxml2-python.x86_64 0:2.9.1-6.el7.5
  libyaml.x86_64 0:0.1.4-11.el7_0
  lvm2.x86_64 7:2.02.187-6.el7_9.5
  lvm2-libs.x86_64 7:2.02.187-6.el7_9.5
  oci-register-machine.x86_64 1:0-6.git2b44233.el7
  oci-systemd-hook.x86_64 1:0.2.0-1.git05e6923.el7_6
  oci-umount.x86_64 2:2.5-3.el7
  policycoreutils-python.x86_64 0:2.5-34.el7
  python-IPy.noarch 0:0.75-6.el7
  python-backports.x86_64 0:1.0-8.el7
  python-backports-ssl_match_hostname.noarch 0:3.5.0.1-1.el7
  python-dateutil.noarch 0:1.5-7.el7
  python-dmidecode.x86_64 0:3.12.2-4.el7
  python-ethtool.x86_64 0:0.8-8.el7
  python-inotify.noarch 0:0.9.4-4.el7
  python-ipaddress.noarch 0:1.0.16-2.el7
  python-pytoml.noarch 0:0.1.14-1.git7dea353.el7
  python-setuptools.noarch 0:0.9.8-7.el7
  python-six.noarch 0:1.9.0-2.el7
  python-syspurpose.x86_64 0:1.24.48-1.el7.centos
  setools-libs.x86_64 0:3.3.8-4.el7
  slirp4netns.x86_64 0:0.4.3-4.el7_8
  subscription-manager.x86_64 0:1.24.48-1.el7.centos
  subscription-manager-rhsm.x86_64 0:1.24.48-1.el7.centos
  subscription-manager-rhsm-certificates.x86_64 0:1.24.48-1.el7.centos
  usermode.x86_64 0:1.111-6.el7
  yajl.x86_64 0:2.0.4-4.el7

Dependency Updated:
  device-mapper.x86_64 7:1.02.170-6.el7_9.5
  device-mapper-libs.x86_64 7:1.02.170-6.el7_9.5
  libxml2.x86_64 0:2.9.1-6.el7.5

Complete!
```

如果现在查看docker版本：
```
Client:
 Version:         1.13.1
 API version:     1.26
 Package version:
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docke r daemon running?
```

启动docker服务:
> service docker start

输出：
```
Redirecting to /bin/systemctl start docker.service
```

查看版本：
> docker version

输出：
```
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-208.git7d71120.el7_9.x86_64
 Go version:      go1.10.3
 Git commit:      7d71120/1.13.1
 Built:           Mon Jun  7 15:36:09 2021
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-208.git7d71120.el7_9.x86_64
 Go version:      go1.10.3
 Git commit:      7d71120/1.13.1
 Built:           Mon Jun  7 15:36:09 2021
 OS/Arch:         linux/amd64
 Experimental:    false
```

修改docker仓库地址：
> vi /etc/docker/daemon.json 

写入内容，保存并退出：
```
{"registry-mirrors": ["https://registry.docker-cn.com"], "live-restore": true}
```

### mysql安装

下载mysql镜像：
> docker pull mysql:5.7

输出：
```
Trying to pull repository docker.io/library/mysql ...
5.7: Pulling from docker.io/library/mysql
33847f680f63: Pull complete
5cb67864e624: Pull complete
1a2b594783f5: Pull complete
b30e406dd925: Pull complete
48901e306e4c: Pull complete
603d2b7147fd: Pull complete
802aa684c1c4: Pull complete
5b5a19178915: Pull complete
f9ce7411c6e4: Pull complete
f51f6977d9b2: Pull complete
aeb6b16ce012: Pull complete
Digest: sha256:be70d18aedc37927293e7947c8de41ae6490ecd4c79df1db40d1b5b5af7d9596
Status: Downloaded newer image for docker.io/mysql:5.7
```

把-v与容器挂载的目录准备好：
> mkdir -p /web/mysql /web/mysql/data /web/mysql/conf.d /web/mysql/logs 

创建容器并运行：
```
docker run \
  --name server-mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=abc$123* \
  -v /web/mysql/data:/var/lib/mysql \
  -v /web/mysql/conf.d:/etc/mysql/conf.d \
  -v /etc/localtime:/etc/localtime:ro \
  -d mysql:5.7
```

`MYSQL_ROOT_PASSWORD` 是 root 用户连接数据库服务的密码。 

`/web/mysql/data` 目录将映射为mysql容器配置的数据文件存放路径

`/web/mysql/conf.d` 目录里的配置文件将映射为mysql容器的配置文件

`-v /etc/localtime:/etc/localtime:ro` 因为容器内的时间会跟宿主机相差 8 个小时，加载这个目录是为了校正时间跟宿主机时间一致。

查看容器运行状态：
> docker ps -a

输出：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
c6b99eed3bc6        mysql:5.7           "docker-entrypoint..."   34 seconds ago      Up 33 seconds       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

#### 新建用户和数据库

接下来可以新建用户和新建库：

进入mysql容器：
> docker exec -it server-mysql /bin/bash

首先用root登录mysql
> mysql -u root -p

输入密码，进入。



### php安装

下载镜像：

> docker pull php:7.1.30-fpm

输出：
```
Trying to pull repository docker.io/library/php ...
7.1.30-fpm: Pulling from docker.io/library/php
f5d23c7fed46: Pull complete
4f36b8588ea0: Pull complete
6f4f95ddefa8: Pull complete
187af28c9b1d: Pull complete
7ba9cd8f12bd: Pull complete
19ce450f6a80: Pull complete
6a0aa94e79c7: Pull complete
3097ec58d870: Pull complete
05ecbde01690: Pull complete
ab28ea58dda0: Pull complete
Digest: sha256:a0f0773dc2f92ca8f4dab7c7c525574d467d3aa4bb27424bb7f0540a7c9efcd0
Status: Downloaded newer image for docker.io/php:7.1.30-fpm
```

把-v与容器挂载的目录准备好：

```
mkdir -p /web/php-fpm/etc /var/www/html 
```

创建容器并运行：
```
docker run \
  --name server-phpfpm \ 
  -p 9000:9000 \
  -v /web/php-fpm/etc:/usr/local/etc/php \
  -v /var/www/html:/var/www/html \
  -v /etc/localtime:/etc/localtime:ro \
  -d php:7.1.30-fpm
```

查看容器运行状态：
> docker ps -a

输出：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
3a03eba032c0        php:7.1.30-fpm      "docker-php-entryp..."   11 seconds ago      Up 10 seconds       0.0.0.0:9000->9000/tcp              server-phpfpm
2bfff24639a3        mysql:5.7           "docker-entrypoint..."   28 minutes ago      Up 28 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

#### mysqli拓展安装

默认的 php 镜像中不带有 mysqli 模块，我们需要给容器内的 php 安装 mysqli 模块：
> docker exec -it server-phpfpm /bin/bash

进入后输入，我们看一下容器内php的拓展：
> php -m

如果没有 mysqli 模块，输入：
> docker-php-ext-install mysqli

输出的内容比较多：
```
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking for MySQLi support... yes, shared
checking whether to enable embedded MySQLi support... no
checking for specified location of the MySQL UNIX socket... no
checking for MySQL UNIX socket location... no
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli.c -o mysqli.lo 
mkdir .libs
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli.c  -fPIC -DPIC -o .libs/mysqli.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_api.c -o mysqli_api.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_api.c  -fPIC -DPIC -o .libs/mysqli_api.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_prop.c -o mysqli_prop.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_prop.c  -fPIC -DPIC -o .libs/mysqli_prop.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_nonapi.c -o mysqli_nonapi.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_nonapi.c  -fPIC -DPIC -o .libs/mysqli_nonapi.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_fe.c -o mysqli_fe.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_fe.c  -fPIC -DPIC -o .libs/mysqli_fe.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_report.c -o mysqli_report.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_report.c  -fPIC -DPIC -o .libs/mysqli_report.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_driver.c -o mysqli_driver.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_driver.c  -fPIC -DPIC -o .libs/mysqli_driver.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_warning.c -o mysqli_warning.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_warning.c  -fPIC -DPIC -o .libs/mysqli_warning.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_exception.c -o mysqli_exception.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_exception.c  -fPIC -DPIC -o .libs/mysqli_exception.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c -o mysqli_result_iterator.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c  -fPIC -DPIC -o .libs/mysqli_result_iterator.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o mysqli.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/mysqli/modules  mysqli.lo mysqli_api.lo mysqli_prop.lo mysqli_nonapi.lo mysqli_fe.lo mysqli_report.lo mysqli_driver.lo mysqli_warning.lo mysqli_exception.lo mysqli_result_iterator.lo 
cc -shared  .libs/mysqli.o .libs/mysqli_api.o .libs/mysqli_prop.o .libs/mysqli_nonapi.o .libs/mysqli_fe.o .libs/mysqli_report.o .libs/mysqli_driver.o .libs/mysqli_warning.o .libs/mysqli_exception.o .libs/mysqli_result_iterator.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,mysqli.so -o .libs/mysqli.so
creating mysqli.la
(cd .libs && rm -f mysqli.la && ln -s ../mysqli.la mysqli.la)
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=install cp ./mysqli.la /usr/src/php/ext/mysqli/modules
cp ./.libs/mysqli.so /usr/src/php/ext/mysqli/modules/mysqli.so
cp ./.libs/mysqli.lai /usr/src/php/ext/mysqli/modules/mysqli.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/mysqli/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/mysqli/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/
/usr/local/bin/docker-php-ext-enable: 108: /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-mysqli.ini: Directory nonexistent
```

最后可能会报错：
```
Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/
/usr/local/bin/docker-php-ext-enable: 108: /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-mysqli.ini: Directory nonexistent
```

是说`/usr/local/etc/php/conf.d/docker-php-ext-mysqli.ini`创建失败，查询原因后发现`/usr/local/etc/php/conf.d/`目录不存在导致的，
我们需要创建这个目录：
> mkdir -p /usr/local/etc/php/conf.d

然后再运行上面的命令。输出：
```
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking target system type... x86_64-pc-linux-gnu
checking for PHP prefix... /usr/local
checking for PHP includes... -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... re2c
checking for re2c version... 1.1.1 (ok)
checking for gawk... no
checking for nawk... nawk
checking if nawk is broken... no
checking for MySQLi support... yes, shared
checking whether to enable embedded MySQLi support... no
checking for specified location of the MySQL UNIX socket... no
checking for MySQL UNIX socket location... no
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... yes
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
configure: creating ./config.status
config.status: creating config.h
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli.c -o mysqli.lo 
mkdir .libs
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli.c  -fPIC -DPIC -o .libs/mysqli.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_api.c -o mysqli_api.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_api.c  -fPIC -DPIC -o .libs/mysqli_api.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_prop.c -o mysqli_prop.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_prop.c  -fPIC -DPIC -o .libs/mysqli_prop.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_nonapi.c -o mysqli_nonapi.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_nonapi.c  -fPIC -DPIC -o .libs/mysqli_nonapi.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_fe.c -o mysqli_fe.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_fe.c  -fPIC -DPIC -o .libs/mysqli_fe.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_report.c -o mysqli_report.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_report.c  -fPIC -DPIC -o .libs/mysqli_report.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_driver.c -o mysqli_driver.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_driver.c  -fPIC -DPIC -o .libs/mysqli_driver.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_warning.c -o mysqli_warning.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_warning.c  -fPIC -DPIC -o .libs/mysqli_warning.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_exception.c -o mysqli_exception.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_exception.c  -fPIC -DPIC -o .libs/mysqli_exception.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2   -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c -o mysqli_result_iterator.lo 
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/usr/src/php/ext/mysqli -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H -fstack-protector-strong -fpic -fpie -O2 -c /usr/src/php/ext/mysqli/mysqli_result_iterator.c  -fPIC -DPIC -o .libs/mysqli_result_iterator.o
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=link cc -DPHP_ATOM_INC -I/usr/src/php/ext/mysqli/include -I/usr/src/php/ext/mysqli/main -I/usr/src/php/ext/mysqli -I/usr/local/include/php -I/usr/local/include/php/main -I/usr/local/include/php/TSRM -I/usr/local/include/php/Zend -I/usr/local/include/php/ext -I/usr/local/include/php/ext/date/lib  -fstack-protector-strong -fpic -fpie -O2 -DHAVE_CONFIG_H  -fstack-protector-strong -fpic -fpie -O2  -Wl,-O1 -Wl,--hash-style=both -pie -o mysqli.la -export-dynamic -avoid-version -prefer-pic -module -rpath /usr/src/php/ext/mysqli/modules  mysqli.lo mysqli_api.lo mysqli_prop.lo mysqli_nonapi.lo mysqli_fe.lo mysqli_report.lo mysqli_driver.lo mysqli_warning.lo mysqli_exception.lo mysqli_result_iterator.lo 
cc -shared  .libs/mysqli.o .libs/mysqli_api.o .libs/mysqli_prop.o .libs/mysqli_nonapi.o .libs/mysqli_fe.o .libs/mysqli_report.o .libs/mysqli_driver.o .libs/mysqli_warning.o .libs/mysqli_exception.o .libs/mysqli_result_iterator.o   -Wl,-O1 -Wl,--hash-style=both -Wl,-soname -Wl,mysqli.so -o .libs/mysqli.so
creating mysqli.la
(cd .libs && rm -f mysqli.la && ln -s ../mysqli.la mysqli.la)
/bin/bash /usr/src/php/ext/mysqli/libtool --mode=install cp ./mysqli.la /usr/src/php/ext/mysqli/modules
cp ./.libs/mysqli.so /usr/src/php/ext/mysqli/modules/mysqli.so
cp ./.libs/mysqli.lai /usr/src/php/ext/mysqli/modules/mysqli.la
PATH="$PATH:/sbin" ldconfig -n /usr/src/php/ext/mysqli/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/src/php/ext/mysqli/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/lib/php/extensions/no-debug-non-zts-20160303/
Installing header files:          /usr/local/include/php/

warning: mysqli (mysqli.so) is already loaded!

find . -name \*.gcno -o -name \*.gcda | xargs rm -f
find . -name \*.lo -o -name \*.o | xargs rm -f
find . -name \*.la -o -name \*.a | xargs rm -f
find . -name \*.so | xargs rm -f
find . -name .libs -a -type d|xargs rm -rf
rm -f libphp.la       modules/* libs/*
```

#### pdo_mysql拓展安装

一般我们Yii用到的Pdo拓展也是缺失的，我们需要把Pdo拓展打开。不然会报错：
```
Fatal error: Uncaught PDOException: could not find driver in ...
```

先看一下phpinfo()输出的配置地址：
```
Configure Command 	'./configure' '--build=x86_64-linux-gnu' '--with-config-file-path=/usr/local/etc/php' '--with-config-file-scan-dir=/usr/local/etc/php/conf.d' '--enable-option-checking=fatal' '--with-mhash' '--enable-ftp' '--enable-mbstring' '--enable-mysqlnd' '--with-curl' '--with-libedit' '--with-openssl' '--with-zlib' '--with-libdir=lib/x86_64-linux-gnu' '--enable-fpm' '--with-fpm-user=www-data' '--with-fpm-group=www-data' '--disable-cgi' 'build_alias=x86_64-linux-gnu'
Server API 	FPM/FastCGI
Virtual Directory Support 	disabled
Configuration File (php.ini) Path 	/usr/local/etc/php
Loaded Configuration File 	(none)
Scan this dir for additional .ini files 	/usr/local/etc/php/conf.d
Additional .ini files parsed 	(none)
PHP API 	20160303
PHP Extension 	20160303
Zend Extension 	320160303
Zend Extension Build 	API320160303,NTS
PHP Extension Build 	API20160303,NTS
Debug Build 	no
Thread Safety 	disabled
Zend Signal Handling 	enabled
Zend Memory Manager 	enabled
Zend Multibyte Support 	provided by mbstring
IPv6 Support 	enabled
DTrace Support 	disabled
Registered PHP Streams	https, ftps, compress.zlib, php, file, glob, data, http, ftp, phar
Registered Stream Socket Transports	tcp, udp, unix, udg, ssl, tls, tlsv1.0, tlsv1.1, tlsv1.2
Registered Stream Filters	zlib.*, convert.iconv.*, string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, dechunk
```

看到配置拓展的地址在 `/usr/local/etc/php/conf.d` 下。

安装pdo_mysql拓展：

> docker-php-ext-install pdo_mysql

可以看到 `/usr/local/etc/php/conf.d` 这个目录下多了一个文件：docker-php-ext-pdo_mysql.ini

然后退出容器，回到宿主服务器：

> exit

重启server-phpfpm服务：

> docker stop server-phpfpm

> docker start server-phpfpm

#### bcmath拓展安装

bcmath拓展可进行一些精度数学运行，需要安装好。

安装bcmath拓展：

> docker-php-ext-install bcmath

别忘了重启server-phpfpm服务。

### nginx安装

> docker pull nginx

输出：
```
Using default tag: latest
Trying to pull repository docker.io/library/nginx ... 
latest: Pulling from docker.io/library/nginx
afb6ec6fdc1c: Pull complete 
b90c53a0b692: Pull complete 
11fa52a0fdc0: Pull complete 
Digest: sha256:6fff55753e3b34e36e24e37039ee9eae1fe38a6420d8ae16ef37c92d1eb26699
Status: Downloaded newer image for docker.io/nginx:latest
```

把-v与容器挂载的目录和文件准备好：

```
mkdir -p /web/nginx/conf /web/nginx/conf/vhost /web/nginx/logs
```

创建容器并运行：
```
docker run \
  --name server-nginx \
  -p 80:80 \
  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm:php \
  -d nginx
```

说明： /web/nginx/conf/nginx.conf 是nginx配置文件，现在暂时还没有，只是把容器与宿主服务器的连接关系先写好。

有时返回结果可能会看到错误提示：
```
/usr/bin/docker-current: Error response from daemon: oci runtime error: container_linux.go:235: starting container process caused "container init exited prematurely".
```

这是因为`-v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf `这个实现不了，我们可以把这一个绑定取消掉
（`/etc/nginx/nginx.conf`里的加载关系是写好的，可以不用挂载修改），再执行：
```
docker run \
  --name server-nginx \
  -p 80:80 \
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm:php \
  -d nginx
```

查看容器：

> docker ps -a

可以看到nginx正在Up运行状态中：

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
4f3bd8406cbc        nginx               "nginx -g 'daemon ..."   14 seconds ago      Up 13 seconds       0.0.0.0:80->80/tcp                  server-nginx
3a03eba032c0        php:7.1.30-fpm      "docker-php-entryp..."   12 minutes ago      Up 12 minutes       0.0.0.0:9000->9000/tcp              server-phpfpm
2bfff24639a3        mysql:5.7           "docker-entrypoint..."   40 minutes ago      Up 40 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

进入server-nginx容器，我们看一下`/etc/nginx/nginx.conf`文件内容：
> docker exec -it server-nginx /bin/bash
> cat /etc/nginx/nginx.conf

输出：
```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

看到最下面一行`include /etc/nginx/conf.d/*.conf;`，把挂载目录下的文件`/web/nginx/conf/vhost/*.conf`都include进来。

我们还需要配置实例安全组，外网才能访问服务器80端口。

在阿里云配置中心——云服务器 ECS——点击实例，在实例管理处点击左侧菜单的本实例安全组，可以看到右侧三个栏目：

`内网入方向全部规则 | 内网出方向全部规则 | 安全组列表`

点击 内网入方向全部规则，看到默认的内容：
```
授权策略	协议类型	端口范围	授权类型(全部)授权对象	描述	优先级
允许	自定义 TCP	22/22	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	全部 ICMP(IPv4)	-1/-1	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
允许	自定义 TCP	3389/3389	IPv4地址段访问	0.0.0.0/0	System created rule.	100	
```

我们需要再加一条入方向80端口的安全规则，加入后用浏览器访问我们实例的公网ip就可以看到nginx欢迎的界面了。

### git安装

安装git后，我们项目代码就可以直接用git实现更新了，而不是用繁杂的ftp，总言之，就是方便：

```
yum install -y git
```

然后cd到目的目录下：
```
git clone 项目git地址
```

按照提示输入验证信息。

以后就只是拉取了：
```
git pull
```

### web项目搭建

我们上面已经运行了nginx容器，但这个容器并无法调用php脚本服务，我们需要做相应配置。

把容器中的nginx.conf文件复制到服务器目录下：

> docker cp server-nginx:/etc/nginx/nginx.conf /web/nginx/conf/nginx.conf

nginx.conf 文件内容：
```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

把容器中的 /etc/nginx/conf.d/dafault.conf 文件复制到服务器目录下：

> docker cp server-nginx:/etc/nginx/conf.d/dafault.conf /web/nginx/conf/vhost/

dafault.conf文件内容：
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

我们可以在服务器 /web/nginx/conf/vhost 目录下写域名项目配置文件：

test.com.conf文件内容：
```
server {
    listen       80;
    server_name  test.com www.test.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/test;
        index  index.html index.htm index.php;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root           /var/www/html/test;
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

把我们上面运行的nginx容器停止运行

> docker stop server-nginx

然后重新运行，让我们修改的配置文件生效：

> docker start server-nginx

可以看出我们test项目的代码在宿主服务器文件夹 /var/www/html/test 下，我们把项目内容发布到这个文件夹下就可以了。
然后我们就可以用 test.com 访问项目了（这里省略 域名/服务器配置相关内容）。 

同样，其他项目我们也可以像test项目这样部署，共用nginx和php容器。

### 附录

```
docker run --name server-mysql  -p 3306:3306  -e MYSQL_ROOT_PASSWORD=abc$123* -v /web/mysql/data:/var/lib/mysql -v /web/mysql/conf.d:/etc/mysql/conf.d -v /web/mysql/logs:/logs  -v /etc/localtime:/etc/localtime:ro -d mysql:5.7

docker run --name server-phpfpm -p 9000:9000 -v /web/php-fpm/etc:/usr/local/etc/php -v /var/www/html:/var/www/html -v /etc/localtime:/etc/localtime:ro -d php:7.1.30-fpm

docker run --name server-nginx -p 80:80  -v /web/nginx/conf/nginx.conf:/etc/nginx/nginx.conf  -v /web/nginx/conf/vhost:/etc/nginx/conf.d  -v /web/nginx/logs:/var/log/nginx  -v /var/www/html:/usr/share/nginx/html  -v /etc/localtime:/etc/localtime:ro  --link server-phpfpm:php  -d nginx
```

<br/><br/><br/><br/><br/>
## 参考资料

阿里云ECS之Docker使用 <https://ibaiyang.github.io/blog/linux/2020/05/30/阿里云ECS之Docker使用.html>

