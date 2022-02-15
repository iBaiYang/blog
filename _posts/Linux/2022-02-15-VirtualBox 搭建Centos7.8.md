---
layout: post
categories: Linux
title: VirtualBox 搭建Centos7.8
meta: VirtualBox 搭建Centos7.8
---
* content
{:toc}

## 正文

在官网下载最新版[Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) 、 [Vagrant](https://www.vagrantup.com/downloads)，安装。

在官网下载Vagrant支持的[box](https://app.vagrantup.com/centos/boxes/7)。

把box移动到 `G:\virtualbox\centos78\box` 下，改名为 `centos78.box`。

win+R，输入cmd打开命令行，切换到 目标盘位置：
> g:
> cd virtualbox\centos78

添加vagrant box：
> vagrant box add centos78 box/centos78.box

用init初始化：
> vagrant init

输出：
```
G:\virtualbox\centos78>vagrant box add centos78 box/centos78.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos78' (v0) for provider:
    box: Unpacking necessary files from: file://G:/virtualbox/centos78/box/centos78.box
    box:
==> box: Successfully added box 'centos78' (v0) for 'virtualbox'!

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant box list
centos67 (virtualbox, 0)
centos78 (virtualbox, 0)

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

G:\virtualbox\centos78>
```

初始化后，我们会在`G:virtualbox\centos78`下看到新增了一个文件`Vagrantfile`：
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "base"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

修改这个配置文件，像这样：
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos78"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 3306, host: 3306
  config.vm.network "forwarded_port", guest: 9801, host: 9801
  config.vm.network "forwarded_port", guest: 9805, host: 9805
  config.vm.network "forwarded_port", guest: 9901, host: 9901
  config.vm.network "forwarded_port", guest: 9905, host: 9905
  config.vm.synced_folder "G:/www", "/var/www", create:true, owner:"vagrant", group:"vagrant"
  config.vm.synced_folder ".", "/share", disabled: true
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "4096"
  vb.cpus = "2"
end
```

安装 vagrant-vbguest 插件：
> vagrant plugin install vagrant-vbguest

```
G:\virtualbox\centos78>vagrant plugin install vagrant-vbguest
Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
Installed the plugin 'vagrant-vbguest (0.30.0)'!

G:\virtualbox\centos78>
```

接着用up启动：
> vagrant up

输出：


### 一次错误的经历

在官网下载Vagrant支持的[box](https://app.vagrantup.com/centos/boxes/7)。

把box移动到 `G:\virtualbox\centos78\box` 下，改名为 `centos78.box`。

win+R，输入cmd打开命令行，切换到 目标盘位置：
> g:
> cd virtualbox\centos78

添加vagrant box：
> vagrant box add centos78 box/centos78.box

用init初始化：
> vagrant init

输出：
```
G:\virtualbox\centos78>vagrant box add centos78 box/centos78.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos78' (v0) for provider:
    box: Unpacking necessary files from: file://G:/virtualbox/centos78/box/centos78.box
    box:
==> box: Successfully added box 'centos78' (v0) for 'virtualbox'!

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant box list
centos67 (virtualbox, 0)
centos78 (virtualbox, 0)

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

G:\virtualbox\centos78>
```

初始化后，我们会在`G:virtualbox\centos78`下看到新增了一个文件`Vagrantfile`：
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "base"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

修改这个配置文件，像这样：
```
Vagrant.configure("2") do |config|
  config.vm.box = "centos78"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 3306, host: 3306
  config.vm.network "forwarded_port", guest: 9801, host: 9801
  config.vm.network "forwarded_port", guest: 9805, host: 9805
  config.vm.network "forwarded_port", guest: 9901, host: 9901
  config.vm.network "forwarded_port", guest: 9905, host: 9905
  config.vm.synced_folder "G:/www", "/var/www", create:true, owner:"vagrant", group:"vagrant"
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider "virtualbox" do |vb|
  vb.memory = "4096"
  vb.cpus = "2"
end
```

接着用up启动：
> vagrant up

输出：
```
G:\virtualbox\centos78>vagrant up
Vagrant failed to initialize at a very early stage:

There is a syntax error in the following Vagrantfile. The syntax error
message is reproduced below for convenience:

G:/virtualbox/centos78/Vagrantfile:81: syntax error, unexpected end-of-input, expecting `end'

G:\virtualbox\centos78>
G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'centos78'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: centos78_default_1644905263730_19816
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

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant plugin install vagrant-vbguest
Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
Fetching micromachine-3.0.0.gem
Fetching vagrant-vbguest-0.30.0.gem
Installed the plugin 'vagrant-vbguest (0.30.0)'!

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant halt
==> default: Attempting graceful shutdown of VM...

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant up
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
==> default: Machine booted and ready!
[default] No Virtualbox Guest Additions installation found.
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
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
warning: /var/cache/yum/x86_64/7/updates/packages/centos-release-7-9.2009.1.el7.centos.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for centos-release-7-9.2009.1.el7.centos.x86_64.rpm is not installed
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
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
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

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant centos78 status
Usage: vagrant [options] <command> [<args>]

    -h, --help                       Print this help.

Common commands:
     autocomplete    manages autocomplete installation on host
     box             manages boxes: installation, removal, etc.
     cloud           manages everything related to Vagrant Cloud
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     upload          upload to machine via communicator
     validate        validates the Vagrantfile
     vbguest         plugin: vagrant-vbguest: install VirtualBox Guest Additions to the machine
     version         prints current and latest Vagrant version
     winrm           executes commands on a machine via WinRM
     winrm-config    outputs WinRM configuration to connect to the machine

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
        --[no-]color                 Enable or disable color output
        --machine-readable           Enable machine readable output
    -v, --version                    Display Vagrant version
        --debug                      Enable debug output
        --timestamp                  Enable timestamps on log output
        --debug-timestamp            Enable debug output with timestamps
        --no-tty                     Enable non-interactive output

G:\virtualbox\centos78>
G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant vbguest
[default] No Virtualbox Guest Additions installation found.
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
Package centos-release-7-9.2009.1.el7.centos.x86_64 already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
No package kernel-devel-3.10.0-1127.el7.x86_64 available.
Error: Nothing to do
Unmounting Virtualbox Guest Additions ISO from: /mnt
umount: /mnt: not mounted
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

umount /mnt

Stdout from the command:



Stderr from the command:

umount: /mnt: not mounted

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant halt
==> default: Attempting graceful shutdown of VM...

G:\virtualbox\centos78>vagrant vbguest
VM must be running to open SSH connection. Run `vagrant up`
to start the virtual machine.

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant up
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
    default: Warning: Connection reset. Retrying...
==> default: Machine booted and ready!
[default] No Virtualbox Guest Additions installation found.
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
Package centos-release-7-9.2009.1.el7.centos.x86_64 already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * extras: mirrors.cn99.com
 * updates: mirrors.cn99.com
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

G:\virtualbox\centos78>
G:\virtualbox\centos78>
```

移除box：
> vagrant box remove centos78

```
G:\virtualbox\centos78>vagrant box list
centos67 (virtualbox, 0)
centos78 (virtualbox, 0)

G:\virtualbox\centos78>vagrant box remove centos78
Box 'centos78' (v0) with provider 'virtualbox' appears
to still be in use by at least one Vagrant environment. Removing
the box could corrupt the environment. We recommend destroying
these environments first:

default (ID: 18e072c308bc4ae68daee25c80745443)

Are you sure you want to remove this box? [y/N] y
Removing box 'centos78' (v0) with provider 'virtualbox'...

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant box list
centos67 (virtualbox, 0)

G:\virtualbox\centos78>
G:\virtualbox\centos78>
```

销毁当前虚拟机：
> vagrant destroy

```
G:\virtualbox\centos78>vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant status
Current machine states:

default                   not created (virtualbox)

The environment has not yet been created. Run `vagrant up` to
create the environment. If a machine is not created, only the
default provider will be shown. So if a provider is not listed,
then the machine is not created for that environment.

G:\virtualbox\centos78>
G:\virtualbox\centos78>vagrant global-status
id       name    provider   state    directory
-------------------------------------------------------------------------
d512ab0  default virtualbox poweroff G:/vagrant

The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date (use "vagrant global-status --prune" to prune invalid
entries). To interact with any of the machines, you can go to that
directory and run Vagrant, or you can use the ID directly with
Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"

G:\virtualbox\centos78>
```

<br/><br/><br/><br/><br/>
## 参考资料

centos/7 Vagrant box <https://app.vagrantup.com/centos/boxes/7>
