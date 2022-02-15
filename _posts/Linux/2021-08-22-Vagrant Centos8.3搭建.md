---
layout: post
categories: Linux
title: Vagrant Centos8.3搭建
meta: Vagrant Centos8.3搭建
---
* content
{:toc}

## 正文

在Centos官网下载Vagrant支持的[box](https://app.vagrantup.com/centos/boxes/8)。

把box移动到 `G:\vagrant2\box` 下，改名为 `CentOS8.3.2011.box`。

win+R，输入cmd打开命令行，切换到 目标盘位置。

添加vagrant box：
> vagrant box add centos83 box/CentOS8.3.2011.box

用init初始化：
> vagrant init

初始化后，我们会在G:/vagrant2下看到新增了一个文件Vagrantfile，修改这个配置文件，像这样：
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos83"
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
G:\vagrant2>vagrant box add centos83 box/CentOS8.3.2011.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos83' (v0) for provider:
    box: Unpacking necessary files from: file://G:/vagrant2/box/CentOS8.3.2011.box
    box: Progress: 100% (Rate: 421M/s, Estimated time remaining: --:--:--)
==> box: Successfully added box 'centos83' (v0) for 'virtualbox'!

G:\vagrant2>
G:\vagrant2>vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

G:\vagrant2>
G:\vagrant2>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos83'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: vagrant2_default_1629597602883_31769
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
The guest machine entered an invalid state while waiting for it
to boot. Valid states are 'starting, running'. The machine is in the
'poweroff' state. Please verify everything is configured
properly and try again.

If the provider you're using has a GUI that comes with it,
it is often helpful to open that and watch the machine, since the
GUI often has more helpful error messages than Vagrant can retrieve.
For example, if you're using VirtualBox, run `vagrant up` while the
VirtualBox GUI is open.

The primary issue for this error is that the provider you're using
is not properly configured. This is very rarely a Vagrant issue.

G:\vagrant2>
```

如果如上面报了错，先halt停止，再up就好了：
```
G:\vagrant2>vagrant halt

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

mount -t vboxsf -o uid=1000,gid=1000 var_www /var/www

The error output from the command was:

mount: /var/www: unknown filesystem type 'vboxsf'.

G:\vagrant2>
```

报共享文件夹无法链接错误，Centos6.7的可以，Centos8.3的不可以，可能是Centos包的问题，不好解决。

<br/><br/><br/><br/><br/>
## 参考资料

centos/8 Vagrant box <https://app.vagrantup.com/centos/boxes/8>

vagrant+centos7 安装报错 <https://blog.csdn.net/shilei_zhang/article/details/72811274>

vagrant-安装教程及常见问题 <https://blog.csdn.net/ty_hf/article/details/78314583>

vagrant共享文件夹挂载失败 <https://blog.csdn.net/ifeng6/article/details/76316991>

CentOS 8 成为绝版！到底发生了什么? <https://zhuanlan.zhihu.com/p/341881553>
