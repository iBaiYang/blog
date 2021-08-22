---
layout: post
categories: Linux
title: Vagrant Centos7.8搭建
meta: Vagrant Centos7.8搭建
---
* content
{:toc}

## 正文

在官网下载最新版[Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) 、 [Vagrant](https://www.vagrantup.com/downloads)，安装。

在Centos官网下载Vagrant支持的[box](https://app.vagrantup.com/centos/boxes/7)。

把box移动到 `G:\vagrant2\box` 下，改名为 `CentOS7.8.box`。

win+R，输入cmd打开命令行，切换到 目标盘位置。

添加vagrant box：
> vagrant box add centos7.8 box/CentOS7.8.box

用init初始化：
> vagrant init

初始化后，我们会在D:/vagrant2下看到新增了一个文件Vagrantfile，修改这个配置文件，像这样：
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos7.8"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 3306, host: 3306
  config.vm.network "forwarded_port", guest: 9801, host: 9801
  config.vm.network "forwarded_port", guest: 9805, host: 9805
  config.vm.network "forwarded_port", guest: 9901, host: 9901
  config.vm.network "forwarded_port", guest: 9905, host: 9905
  config.vm.synced_folder "G:/www", "/var/www", create:true, owner:"vagrant", group:"vagrant"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "2048"
  vb.cpus = "2"
  end
```

接着用up启动：
> vagrant up

过程如下：
```
G:\>cd vagrant2
G:\vagrant2>
G:\vagrant2>vagrant box add centos7.8 box/Centos7.8.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos7.8' (v0) for provider:
    box: Unpacking necessary files from: file://G:/vagrant2/box/Centos7.8.box
    box:
==> box: Successfully added box 'centos7.8' (v0) for 'virtualbox'!

G:\vagrant2>vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

G:\vagrant2>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos7.8'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: vagrant2_default_1629637188999_91511
Vagrant is currently configured to create VirtualBox synced folders with
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant
guest is not trusted, you may want to disable this option. For more
information on this option, please refer to the VirtualBox manual:

  https://www.virtualbox.org/manual/ch04.html#sharedfolders

This option can be disabled globally with an environment variable:

  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

or on a per folder basis within the Vagrantfile:

  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: You are trying to forward to privileged ports (ports <= 1024). Most
==> default: operating systems restrict this to only privileged process (typically
==> default: processes running as an administrative user). This is a warning in case
==> default: the port forwarding doesn't work. If any problems occur, please try a
==> default: port higher than 1024.
==> default: Forwarding ports...
    default: 80 (guest) => 80 (host) (adapter 1)
    default: 3306 (guest) => 3306 (host) (adapter 1)
    default: 9801 (guest) => 9801 (host) (adapter 1)
    default: 9805 (guest) => 9805 (host) (adapter 1)
    default: 9901 (guest) => 9901 (host) (adapter 1)
    default: 9905 (guest) => 9905 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default:
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
==> default: Mounting shared folders...
    default: /var/www => G:/www
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000,_netdev var_www /var/www

The error output from the command was:

mount: unknown filesystem type 'vboxsf'

G:\vagrant2>
```

报共享文件夹无法链接错误，查询后的解决方案：
```
vagrant plugin install vagrant-vbguest
vagrant vbguest
```

详细过程：
```
G:\vagrant2>vagrant plugin install vagrant-vbguest
Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
Fetching micromachine-3.0.0.gem
Fetching vagrant-vbguest-0.30.0.gem
Installed the plugin 'vagrant-vbguest (0.30.0)'!

G:\vagrant2>
G:\vagrant2>vagrant halt
==> default: Attempting graceful shutdown of VM...

G:\vagrant2>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: You are trying to forward to privileged ports (ports <= 1024). Most
==> default: operating systems restrict this to only privileged process (typically
==> default: processes running as an administrative user). This is a warning in case
==> default: the port forwarding doesn't work. If any problems occur, please try a
==> default: port higher than 1024.
==> default: Forwarding ports...
    default: 80 (guest) => 80 (host) (adapter 1)
    default: 3306 (guest) => 3306 (host) (adapter 1)
    default: 9801 (guest) => 9801 (host) (adapter 1)
    default: 9805 (guest) => 9805 (host) (adapter 1)
    default: 9901 (guest) => 9901 (host) (adapter 1)
    default: 9905 (guest) => 9905 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection aborted. Retrying...
==> default: Machine booted and ready!
[default] No Virtualbox Guest Additions installation found.
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package centos-release.x86_64 0:7-8.2003.0.el7.centos will be updated
---> Package centos-release.x86_64 0:7-9.2009.1.el7.centos will be an update
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package             Arch        Version                     Repository    Size
================================================================================
Updating:
 centos-release      x86_64      7-9.2009.1.el7.centos       updates       27 k

Transaction Summary
================================================================================
Upgrade  1 Package

Total download size: 27 k
Downloading packages:
No Presto metadata available for updates
Public key for centos-release-7-9.2009.1.el7.centos.x86_64.rpm is not installed
warning: /var/cache/yum/x86_64/7/updates/packages/centos-release-7-9.2009.1.el7.centos.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-8.2003.0.el7.centos.x86_64 (@anaconda)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : centos-release-7-9.2009.1.el7.centos.x86_64                  1/2
  Cleanup    : centos-release-7-8.2003.0.el7.centos.x86_64                  2/2
  Verifying  : centos-release-7-9.2009.1.el7.centos.x86_64                  1/2
  Verifying  : centos-release-7-8.2003.0.el7.centos.x86_64                  2/2

Updated:
  centos-release.x86_64 0:7-9.2009.1.el7.centos

Complete!
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
No package kernel-devel-3.10.0-1127.el7.x86_64 available.
Error: Nothing to do
Unmounting Virtualbox Guest Additions ISO from: /mnt
umount: /mnt: not mounted
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default:
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

umount /mnt

Stdout from the command:



Stderr from the command:

umount: /mnt: not mounted

G:\vagrant2>vagrant halt
==> default: Attempting graceful shutdown of VM...

G:\vagrant2>vagrant vbguest
VM must be running to open SSH connection. Run `vagrant up`
to start the virtual machine.

G:\vagrant2>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: You are trying to forward to privileged ports (ports <= 1024). Most
==> default: operating systems restrict this to only privileged process (typically
==> default: processes running as an administrative user). This is a warning in case
==> default: the port forwarding doesn't work. If any problems occur, please try a
==> default: port higher than 1024.
==> default: Forwarding ports...
    default: 80 (guest) => 80 (host) (adapter 1)
    default: 3306 (guest) => 3306 (host) (adapter 1)
    default: 9801 (guest) => 9801 (host) (adapter 1)
    default: 9805 (guest) => 9805 (host) (adapter 1)
    default: 9901 (guest) => 9901 (host) (adapter 1)
    default: 9905 (guest) => 9905 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
There was an error while executing `VBoxManage`, a CLI used by Vagrant
for controlling VirtualBox. The command and stderr is shown below.

Command: ["startvm", "192460a4-186a-402d-b3b7-a4ad94eb2e86", "--type", "headless"]

Stderr: VBoxManage.exe: error: The VM session was closed before any attempt to power it on
VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component SessionMachine, interface ISession

G:\vagrant2>
```

又报上面这个错，查询后是说VirtualBox和Vagrant没卸载干净，新的环境被旧的污染了。



<br/><br/><br/><br/><br/>
## 参考资料

centos/7 Vagrant box <https://app.vagrantup.com/centos/boxes/7>

vagrant 无法挂载共享目录 <https://www.cnblogs.com/brady-wang/p/11909769.html>

Vagrant was unable to mount VirtualBox shared folders <https://github.com/scotch-io/scotch-box/issues/296>

vagrant+centos7 安装报错 <https://blog.csdn.net/shilei_zhang/article/details/72811274>

vagrant-安装教程及常见问题 <https://blog.csdn.net/ty_hf/article/details/78314583>

vagrant共享文件夹挂载失败 <https://blog.csdn.net/ifeng6/article/details/76316991>

CentOS 8 成为绝版！到底发生了什么? <https://zhuanlan.zhihu.com/p/341881553>

