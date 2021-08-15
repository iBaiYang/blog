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

单行写法：
```
docker run --name server-phpfpm  -p 9000:9000 -v /web/php-fpm/etc:/usr/local/etc/php -v /var/www/html:/var/www/html -v /etc/localtime:/etc/localtime:ro -d php:7.1.30-fpm
```

查看容器运行状态：
> docker ps -a

输出：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
9a480218544e        php:7.1.30-fpm      "docker-php-entryp..."   54 seconds ago      Up 54 seconds       0.0.0.0:9000->9000/tcp              server-phpfpm
c6b99eed3bc6        mysql:5.7           "docker-entrypoint..."   25 minutes ago      Up 25 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

#### mysqli拓展安装



#### pdo_mysql拓展安装



#### bcmath拓展安装




### nginx安装

下载镜像：
> docker pull nginx

输出：
```
Using default tag: latest
Trying to pull repository docker.io/library/nginx ...
latest: Pulling from docker.io/library/nginx
33847f680f63: Already exists
dbb907d5159d: Pull complete
8a268f30c42a: Pull complete
b10cf527a02d: Pull complete
c90b090c213b: Pull complete
1f41b2f2bf94: Pull complete
Digest: sha256:8f335768880da6baf72b70c701002b45f4932acae8d574dedfddaf967fc3ac90
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
  -v /web/nginx/conf/vhost:/etc/nginx/conf.d \
  -v /web/nginx/logs:/var/log/nginx \
  -v /var/www/html:/usr/share/nginx/html \
  -v /etc/localtime:/etc/localtime:ro \
  --link server-phpfpm:php \
  -d nginx
```

单行格式：
```
docker run --name server-nginx -p 80:80 -v /web/nginx/conf/vhost:/etc/nginx/conf.d -v /web/nginx/logs:/var/log/nginx -v /var/www/html:/usr/share/nginx/html -v /etc/localtime:/etc/localtime:ro --link server-phpfpm:php -d nginx
```

查看容器：
> docker ps -a

可以看到nginx正在Up运行状态中：
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
06a47e065c86        nginx               "/docker-entrypoin..."   15 seconds ago      Up 14 seconds       0.0.0.0:80->80/tcp                  server-nginx
9a480218544e        php:7.1.30-fpm      "docker-php-entryp..."   12 minutes ago      Up 12 minutes       0.0.0.0:9000->9000/tcp              server-phpfpm
c6b99eed3bc6        mysql:5.7           "docker-entrypoint..."   36 minutes ago      Up 36 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   server-mysql
```

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

我们需要在 安全组 入方向 再加一条入方向 http 80端口的安全规则，加入后用浏览器访问我们实例的公网ip就可以看到nginx欢迎的界面了。

### git安装

安装git后，我们项目代码就可以直接用git实现更新了，而不是用繁杂的ftp，总言之，就是方便：
```
yum install -y git
```

输出：
```
Loaded plugins: fastestmirror, product-id, search-disabled-repos, subscription-manager

This system is not registered with an entitlement server. You can use subscription-manager to register.

Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package git.x86_64 0:1.8.3.1-23.el7_8 will be installed
--> Processing Dependency: perl-Git = 1.8.3.1-23.el7_8 for package: git-1.8.3.1-23.el7_8.x86_64
--> Processing Dependency: perl(Term::ReadKey) for package: git-1.8.3.1-23.el7_8.x86_64
--> Processing Dependency: perl(Git) for package: git-1.8.3.1-23.el7_8.x86_64
--> Processing Dependency: perl(Error) for package: git-1.8.3.1-23.el7_8.x86_64
--> Running transaction check
---> Package perl-Error.noarch 1:0.17020-2.el7 will be installed
---> Package perl-Git.noarch 0:1.8.3.1-23.el7_8 will be installed
---> Package perl-TermReadKey.x86_64 0:2.30-20.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================
 Package                    Arch             Version                     Repository      Size
==============================================================================================
Installing:
 git                        x86_64           1.8.3.1-23.el7_8            base           4.4 M
Installing for dependencies:
 perl-Error                 noarch           1:0.17020-2.el7             base            32 k
 perl-Git                   noarch           1.8.3.1-23.el7_8            base            56 k
 perl-TermReadKey           x86_64           2.30-20.el7                 base            31 k

Transaction Summary
==============================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 4.5 M
Installed size: 22 M
Downloading packages:
(1/4): perl-Error-0.17020-2.el7.noarch.rpm                             |  32 kB  00:00:00
(2/4): perl-Git-1.8.3.1-23.el7_8.noarch.rpm                            |  56 kB  00:00:00
(3/4): perl-TermReadKey-2.30-20.el7.x86_64.rpm                         |  31 kB  00:00:00
(4/4): git-1.8.3.1-23.el7_8.x86_64.rpm                                 | 4.4 MB  00:00:00
----------------------------------------------------------------------------------------------
Total                                                          20 MB/s | 4.5 MB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 1:perl-Error-0.17020-2.el7.noarch                                          1/4
  Installing : perl-TermReadKey-2.30-20.el7.x86_64                                        2/4
  Installing : perl-Git-1.8.3.1-23.el7_8.noarch                                           3/4
  Installing : git-1.8.3.1-23.el7_8.x86_64                                                4/4
  Verifying  : git-1.8.3.1-23.el7_8.x86_64                                                1/4
  Verifying  : 1:perl-Error-0.17020-2.el7.noarch                                          2/4
  Verifying  : perl-Git-1.8.3.1-23.el7_8.noarch                                           3/4
  Verifying  : perl-TermReadKey-2.30-20.el7.x86_64                                        4/4

Installed:
  git.x86_64 0:1.8.3.1-23.el7_8

Dependency Installed:
  perl-Error.noarch 1:0.17020-2.el7               perl-Git.noarch 0:1.8.3.1-23.el7_8
  perl-TermReadKey.x86_64 0:2.30-20.el7

Complete!
```

然后cd切换到目的目录下：
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



<br/><br/><br/><br/><br/>
## 参考资料

阿里云ECS之Docker使用 <https://ibaiyang.github.io/blog/linux/2020/05/30/阿里云ECS之Docker使用.html>

