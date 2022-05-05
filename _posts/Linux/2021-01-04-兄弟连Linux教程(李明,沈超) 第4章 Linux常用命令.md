---
layout: post
categories: Linux
title: 兄弟连Linux教程(李明,沈超) 第4章 Linux常用命令
meta: 兄弟连Linux教程(李明,沈超) 第4章 Linux常用命令
---
* content
{:toc}

### 正文

#### 4.1 文件处理命令

##### 4.1.1 命令格式与目录处理命令ls

命令格式 ：`命令  [-选项]  [参数]`

例 ： `ls  -la  /etc`

说明：
1. 个别命令使用不遵循此格式
2. 当有多个选项时，可以写在一起
3. 简化选项与完整选项
    `-a 等于 --all `

##### 4.1.2 目录处理命令

**目录处理命令：ls**

命令名称：ls

命令英文原意：list

命令所在路径：/bin/ls

执行权限：所有用户

功能描述：显示目录文件

语法：`ls  选项[-ald]  [文件或目录]`

    -a    显示所有文件，包括隐藏文件 all
    -l    详细信息显示 long
    -d    查看目录属性 directory
    -h    人性化显示 human
    -i    节点号 inode
    
> ls -l



`-rw-r--r--`	

文件类型（`-` 文件 `d` 目录  `l` 软链接文件）
  
    rw-  r--   r--
    u     g     o       
    u所有者   g所属组   o其他人
    r读  w写  x执行

> ls -ld /etc

> ls -i

**目录处理命令：mkdir**

命令名称：mkdir

命令英文原意：make directories

命令所在路径：/bin/mkdir

执行权限：所有用户

语法：`mkdir -p  [目录名]`

功能描述：创建新目录

    -p  递归创建
         
范例： 
> $ mkdir -p /tmp/Japan/boduo
> 
> $ mkdir /tmp/Japan/longze /tmp/Japan/cangjing

**目录处理命令：cd**

命令名称：cd

命令英文原意：change directory

命令所在路径：shell内置命令

执行权限：所有用户

语法：`cd [目录]`

功能描述：切换目录

范例： 
> $ cd  /tmp/Japan/boduo     切换到指定目录
> 
> $ cd  ..        回到上一级目录                 

**目录处理命令：pwd**

命令名称：pwd

命令英文原意：print working directory

命令所在路径：/bin/pwd

执行权限：所有用户

语法：`pwd`

功能描述：显示当前目录

范例：
> $ pwd
> 
> /tmp/Japan

**文件处理命令：rmdir**

命令名称：rmdir

命令英文原意：remove empty directories

命令所在路径：/bin/rmdir

执行权限：所有用户

语法：`rmdir [目录名]`

功能描述： 删除空目录

范例： 
> $ rmdir /tmp/Japan/boduo             

**目录处理命令：cp**

命令名称：cp

命令英文原意：copy

命令所在路径：/bin/cp

执行权限：所有用户

语法：`cp  -rp  [原文件或目录] [目标目录] `

    -r  复制目录
    -p  保留文件属性
    
功能描述：复制文件或目录

范例：

将目录/tmp/Japan/cangjing复制到目录/root下
> $ cp  -r /tmp/Japan/cangjing  /root

将/tmp/Japan目录下的boduo和longze目录复制到 /root下，保持目录属性
> $ cp  -rp /tmp/Japan/boduo /tmp/Japan/longze /root

**目录处理命令：mv**

命令名称：mv

命令英文原意：move

命令所在路径：/bin/mv

执行权限：所有用户

语法：`mv  [原文件或目录]  [目标目录]`

功能描述：剪切文件、改名

**目录处理命令：rm**

命令名称：rm

命令英文原意：remove

命令所在路径：/bin/rm

执行权限：所有用户

语法：`rm  -rf   [文件或目录]`

    -r  删除目录
    -f  强制执行
                   
功能描述：删除文件

范例：

删除文件 /tmp/yum.log
> $ rm  /tmp/yum.log

删除目录 /tmp/Japan/longze
> $ rm -rf  /tmp/Japan/longze

##### 4.1.3 文件处理命令

**文件处理命令：touch**

命令名称：touch

命令所在路径：/bin/touch

执行权限：所有用户

语法：`touch  [文件名]`       
         
功能描述：创建空文件

范例： 
> $ touch Japanlovestory.list             

**文件处理命令：cat**

命令名称：cat

命令所在路径：/bin/cat

执行权限：所有用户

语法：`cat [文件名]`

功能描述：显示文件内容

    -n  显示行号  number

范例： 
> $ cat  /etc/issue
>
> $ cat  -n /etc/services            

**文件处理命令：tac**

命令名称：tac

命令所在路径：/usr/bin/tac

执行权限：所有用户

语法：`tac [文件名]`

功能描述：显示文件内容（反向列示）

范例：
> $ tac  /etc/issue

**文件处理命令：more**

命令名称：more

命令所在路径：/bin/more

执行权限：所有用户

语法：`more  [文件名]`
    
    (空格) 或f       翻页
    (Enter)             换行
    q或Q              退出
    
功能描述：分页显示文件内容

范例：   
> $ more  /etc/services

**文件处理命令：less**

命令名称：less

命令所在路径：/usr/bin/less

执行权限：所有用户

语法：`less  [文件名]`

功能描述：分页显示文件内容（可向上翻页，还可使用`/`搜索关键字）

范例：   
> $  less  /etc/services

**文件处理命令：head**

命令名称：head

命令所在路径：/usr/bin/head

执行权限：所有用户

语法：`head  [文件名]`

功能描述：显示文件前面几行

    -n 指定行数
			
范例：   
> $ head -n 20 /etc/services

**文件处理命令：tail**

命令名称：tail

命令所在路径：/usr/bin/tail

执行权限：所有用户

语法：`tail  [文件名]`

功能描述：显示文件后面几行

    -n 指定行数
    -f  动态显示文件末尾内容
			
范例：   
> $ tail -n 18 /etc/services

##### 4.1.4 链接命令

**文件处理命令：ln**

命令名称：ln

命令英文原意：link

命令所在路径：/bin/ln

执行权限：所有用户

语法：`ln  -s  [原文件]  [目标文件]`

    -s  创建软链接
                 
功能描述：生成链接文件

范例：

创建文件/etc/issue的软链接/tmp/issue.soft
> $ ln -s  /etc/issue  /tmp/issue.soft

创建文件/etc/issue的硬链接/tmp/issue.hard
> $ ln  /etc/issue  /tmp/issue.hard

软链接特征：类似Windows快捷方式

1、lrwxrwxrwx    l 软链接

软链接文件权限都为rwxrwxrwx

2、文件大小-只是符号链接

3、/tmp/issue.soft -> /etc/issue

箭头指向原文件

硬链接特征：

1、拷贝cp -p + 同步更新

`echo "this is a test" >> /etc/motd`

2、可通过i节点识别

3、不能跨分区

4、不能针对目录使用

#### 4.2 权限管理命令

##### 4.2.1 权限管理命令chmod

**权限管理命令：chmod**

命令名称：chmod

命令英文原意：change the permissions mode of a file

命令所在路径：/bin/chmod

执行权限：所有用户

语法：

    chmod  [{ugoa}{+-=}{rwx}] [文件或目录] 
    [mode=421 ]  [文件或目录] 
    -R  递归修改
    
功能描述：改变文件或目录权限               

权限的数字表示

     r  ---- 4
     w ---- 2
     x  ---- 1

    rwxrw-r-- 
      7   6    4

范例：

赋予文件testfile所属组写权限
> $ chmod  g+w  testfile

修改目录testfile及其目录下文件为所有用户具有全部权限   
> $ chmod  -R 777  testdir

**文件目录权限总结**

| 代表字符      | 权限    | 对文件的含义    | 对目录的含义      |
| :--------   | :-----  | :-----  | :-----  |
|  r    |  读权限   | 可以查看文件内容    | 可以列出目录中的内容     |
|  w    | 写权限    | 可以修改文件内容    |  可以在目录中创建、删除文件    |
|  x    | 执行权限    | 可以执行文件    | 可以进入目录     |

##### 4.2.2 其他权限管理命令

**权限管理命令：chown**

命令名称：chown

命令英文原意：change file ownership

命令所在路径：/bin/chown

执行权限：所有用户

语法：chown  [用户] [文件或目录] 

功能描述：改变文件或目录的所有者

范例：

改变文件fengjie的所有者为shenchao
> $ chown  shenchao fengjie
               
**权限管理命令：chgrp**

命令名称：chgrp

命令英文原意：change file group ownership

命令所在路径：/bin/chgrp

执行权限：所有用户

语法：chgrp  [用户组]  [文件或目录] 

功能描述：改变文件或目录的所属组

范例：

改变文件fengjie的所属组为lampbrother       
> $ chgrp lampbrother fengjie
                     
**权限管理命令：umask**

命令名称：umask

命令英文原意：the user file-creation mask

命令所在路径：Shell内置命令

执行权限：所有用户

语法：umask [-S]

    -S   以rwx形式显示新建文件缺省权限
            
功能描述：显示、设置文件的缺省权限

范例：

查看 
> $ umask -S  

u=rwx,g=rx,o=rx             

文件默认没有x执行权限

> umask

0022

    0 特殊权限
    022  --- -w- -w-
    
    777 rwx rwx rwx
    022 --- -w- -w-
    755 rwx r-x r-x  目录
        rw- r-- r--  文件

修改
> umask 077

    777 rwx rwx rwx
    077 --- rwx rwx
    700 rwx --- ---  目录
        rw- --- ---  文件

#### 4.3 文件搜索命令

##### 4.3.1 文件搜索命令find

**文件搜索命令：find**

命令名称：find

命令所在路径：/bin/find

执行权限：所有用户

语法：find  [搜索范围]  [匹配条件]

功能描述：文件搜索

在目录/etc中查找文件init
>  find  /etc  -name  init  

    -iname 不区分大小写
    * 任意字符
    ? 单个字符

```
find  /etc  -name  *init*
find  /etc  -name  init???
find  /etc  -iname  init
```  

在根目录下查找大于100MB的文件  (1数据块 = 512字节 = 0.5k ,  100MB = 102400KB = 204800)
> $ find  /  -size  +204800  

    +n  大于   -n 小于   n 等于

在根目录下查找所有者为shenchao的文件
> $ find  /home  -user  shenchao

    -group  根据所属组查找

在/etc下查找5分钟内被修改过属性的文件和目录
> $ find  /etc  -cmin  -5

    -amin  访问时间 access
    -cmin  文件属性 change
    -mmin  文件内容 modify

在/etc下查找大于80MB小于100MB的文件
> $ find  /etc -size  +163840 -a -size  -204800 

    -a 两个条件同时满足
    -o 两个条件满足任意一个即可
   
在/etc下查找inittab文件并显示其详细信息 
> $ find /etc -name inittab -exec ls -l {} \;

`-exec/-ok 命令 {} \;`  对搜索结果执行操作

> $ find  /etc -size  +163840 -a -type f

    -type 根据文件类型查找
         f 文件    d 目录    l 软链接文件
             
> find . -inum 31531 -exec rm {} \;

    -inum  根据i节点查找
   
windows的搜索工具：Everything

##### 4.3.2 其他搜索命令

**文件搜索命令：locate**

命令名称：locate

命令所在路径：/usr/bin/locate

执行权限：所有用户

语法：locate  文件名 

功能描述：在文件资料库中查找文件

范例：
> $ locate inittab

更新文件资料库
> updatedb

> $ locate -i cang

**文件搜索命令：which**

命令名称：which

命令所在路径：/usr/bin/which

执行权限：所有用户

语法：which  命令

功能描述：搜索命令所在目录及别名信息

范例：
> $ which ls                                

**文件搜索命令：whereis**

命令名称：whereis

命令所在路径：/usr/bin/whereis

执行权限：所有用户

语法：whereis  [命令名称] 

功能描述：搜索命令所在目录及帮助文档路径

范例：
> $ whereis ls                                

**文件搜索命令：grep**

命令名称：grep

命令所在路径：/bin/grep

执行权限：所有用户

语法：grep  -iv [指定字串] [文件]

功能描述：在文件中搜寻字串匹配的行并输出

    -i  不区分大小写
    -v 排除指定字串
    
范例：
> grep  mysql  /root/install.log
>
> grep -i multiuser /etc/inittab
>
> grep -v ^# /etc/inittab

#### 4.4 帮助命令

**帮助命令：man**

命令名称：man

命令英文原意：manual

命令所在路径：/usr/bin/man

执行权限：所有用户

语法：man  [命令或配置文件]

功能描述：获得帮助信息

范例： 

查看ls命令的帮助信息
> $ man ls
>
> /-l
>
> n

查看配置文件services的帮助信息
> $ man services

> whatis ls

`ls --help`

> info ls

> apropos 配置文件

**帮助命令：help**

命令名称：help

命令所在路径：Shell内置命令

执行权限：所有用户

语法：help 命令

功能描述：获得Shell内置命令的帮助信息

范例：

查看umask命令的帮助信息
> $ help umask

#### 4.5 用户管理命令

**用户管理命令：useradd**

命令名称：useradd

命令所在路径：/usr/sbin/useradd

执行权限：root

语法：useradd 用户名

功能描述：添加新用户

范例： 
> $ useradd yangmi

**用户管理命令：passwd**

命令名称：passwd

命令所在路径：/usr/bin/passwd

执行权限：所有用户

语法：passwd 用户名

功能描述：设置用户密码

范例： 
> $ passwd yangmi

**用户管理命令：who**

命令名称：who

命令所在路径：/usr/bin/who

执行权限：所有用户

语法：who

功能描述：查看登录用户信息

范例： 
> $ who

```
vagrant  pts/0        2021-04-02 13:29 (10.0.2.2)
```

tty 本地登录的

pts 远程终端登录的


**用户管理命令：w**

命令名称：w

命令所在路径：/usr/bin/w

执行权限：所有用户

语法：w

功能描述：查看登录用户详细信息

范例：
> $ w

```
 13:49:05 up 20 min,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
vagrant  pts/0    10.0.2.2         13:29    0.00s  0.21s  0.05s sshd: vagrant [priv]
```

#### 4.6 压缩解压命令

**压缩命令：gzip**

命令名称：gzip

命令英文原意：GNU zip

命令所在路径：/bin/gzip

执行权限：所有用户

语法：gzip [文件]

功能描述：压缩文件，只能压缩文件，不能压缩目录，压缩完会删除原文件

压缩后文件格式：.gz

**解压命令：gunzip**

命令名称：gunzip

命令英文原意：GNU unzip

命令所在路径：/bin/gunzip

执行权限：所有用户

语法：gunzip [压缩文件]

功能描述：解压缩.gz的压缩文件

范例： 
> $ gunzip boduo.gz

**压缩解压命令：tar**

命令名称：tar

命令所在路径：/bin/tar

执行权限：所有用户

语法：tar  选项[-zcf] [压缩后文件名] [目录]          

    -c    打包
    -v    显示详细信息，查看打包的文件有哪些
    -f     指定文件名
    -z     打包同时压缩

功能描述：打包目录，打包的同时可以选择压缩

压缩后文件格式：.tar.gz

范例：

将目录Japan打包并压缩为.tar.gz文件
> $ tar  -zcf   Japan.tar.gz  Japan

tar命令解压缩语法：

    -x     解包
    -v    显示详细信息
    -f     指定解压文件
    -z     解压缩

范例：
> $ tar  -zxvf  Japan.tar.gz

**压缩命令：zip**

命令名称：zip

命令所在路径：/usr/bin/zip

执行权限：所有用户

语法： zip  选项[-r]  [压缩后文件名]  [文件或目录]

    -r    压缩目录

功能描述：压缩文件或目录，对比gzip压缩完能保留原文件，windows和linux都支持

压缩后文件格式：.zip

范例：

压缩文件
> $ zip  buduo.zip  boduo

压缩目录
> $ zip  -r  Japan.zip  Japan

**解压命令：unzip**

命令名称：unzip

命令所在路径：/usr/bin/unzip

执行权限：所有用户

语法：unzip  [压缩文件]

功能描述：解压.zip的压缩文件

范例：
> $ unzip test.zip

**压缩命令：bzip2**

命令名称：bzip2

命令所在路径：/usr/bin/bzip2

执行权限：所有用户

语法： bzip2  选项 [-k] [文件]

    -k   产生压缩文件后保留原文件

功能描述：压缩文件，gzip的升级格式，压缩完保留原文件

压缩后文件格式：.bz2

范例：
> $ bzip2 -k boduo   
> 
> $ tar -cjf  Japan.tar.bz2 Japan

**压缩解压命令：bunzip2**

命令名称：bunzip2

命令所在路径：/usr/bin/bunzip2

执行权限：所有用户

语法： bunzip2  选项 [-k] [压缩文件]

    -k   解压缩后保留原文件

功能描述：解压缩

范例：
> $ bunzip2  -k boduo.bz2
>
> $ tar -xjf  Japan.tar.bz2

#### 4.7 网络命令

**网络命令：write**

指令名称：write

指令所在路径：/usr/bin/write

执行权限：所有用户

语法：write  <用户名>   

功能描述：给用户发信息，以Ctrl+D保存结束

范例：  
> write linzhiling

**网络命令：wall**

指令名称：wall

命令英文原意：write all

指令所在路径：/usr/bin/wall

执行权限：所有用户

语法：wall  [message]  

功能描述：发广播信息，给当前在线的所有用户，包括自己

范例：   
> wall  ShenChao is a honest man!

**网络命令：ping**

命令名称：ping

命令所在路径：/bin/ping

执行权限：所有用户

语法：ping  选项  IP地址

    -c  指定发送次数

功能描述：测试网络连通性

范例：  
> ping 192.168.1.156
> 
> ping -c 4 192.168.1.156

**网络命令：ifconfig**

命令名称：ifconfig

命令英文原意：interface configure

命令所在路径：/sbin/ifconfig

执行权限：root

语法：ifconfig  网卡名称  IP地址

功能描述：查看和设置网卡信息

范例：

查看网卡信息
> ifconfig

```
eth0      Link encap:Ethernet  HWaddr 08:00:27:20:20:F4
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe20:20f4/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:479 errors:0 dropped:0 overruns:0 frame:0
          TX packets:306 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:51836 (50.6 KiB)  TX bytes:45405 (44.3 KiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)
```

lo 回环网卡 127.0.0.1

设置网卡地址
> ifconfig  eth0 192.168.8.250

**网络命令：mail**

命令名称：mail

命令所在路径：/bin/mail

执行权限：所有用户

语法：mail [用户名]

功能描述：查看、发送电子邮件，ctrl + d 发送

范例：
> mail root

接受邮件：
> mail
> 
> 1
> 
> h
>
> d 1
>
> q

**网络命令：last**

命令名称：last

命令所在路径：/usr/bin/last

执行权限：所有用户

语法：last

功能描述：列出目前与过去登入系统的用户信息

范例：
> last

**网络命令：lastlog**

命令名称：lastlog

命令所在路径：/usr/bin/lastlog

执行权限：所有用户

语法：lastlog

功能描述：检查某特定用户上次登录的时间

范例：
> lastlog

```
Username         Port     From             Latest
root                                       **Never logged in**
bin                                        **Never logged in**
daemon                                     **Never logged in**
adm                                        **Never logged in**
lp                                         **Never logged in**
sync                                       **Never logged in**
shutdown                                   **Never logged in**
halt                                       **Never logged in**
mail                                       **Never logged in**
uucp                                       **Never logged in**
operator                                   **Never logged in**
games                                      **Never logged in**
gopher                                     **Never logged in**
ftp                                        **Never logged in**
nobody                                     **Never logged in**
vcsa                                       **Never logged in**
saslauth                                   **Never logged in**
postfix                                    **Never logged in**
sshd                                       **Never logged in**
vagrant          pts/0    10.0.2.2         Sat Apr  3 03:48:06 +0100 2021
vboxadd                                    **Never logged in**
apache                                     **Never logged in**
nginx                                      **Never logged in**
mysql                                      **Never logged in**
```

查看指定用户登录记录：
> lastlog -u mysql

**网络命令：traceroute**

命令名称：traceroute

命令所在路径：/bin/traceroute

执行权限：所有用户

语法：traceroute

功能描述：显示数据包到主机间的路径

范例：
> traceroute www.lampbrother.net

**网络命令：netstat**

命令名称：netstat

命令所在路径：/bin/netstat

执行权限：所有用户

语法：netstat  [选项]

功能描述：显示网络相关信息

选项：

    -t 	：	TCP协议
    -u	：	UDP协议
    -l	：	监听
    -r	：	路由
    -n	：	显示IP地址和端口号

范例：

查看本机监听的端口
>  netstat -tlun		

```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 0.0.0.0:80                  0.0.0.0:*                   LISTEN
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN
tcp        0      0 127.0.0.1:25                0.0.0.0:*                   LISTEN
tcp        0      0 :::3306                     :::*                        LISTEN
tcp        0      0 :::22                       :::*                        LISTEN
tcp        0      0 ::1:25                      :::*                        LISTEN
udp        0      0 0.0.0.0:68                  0.0.0.0:*
```

查看本机所有的网络连接
>  netstat -an		

查看本机路由表
>  netstat -rn	

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 eth0
0.0.0.0         10.0.2.2        0.0.0.0         UG        0 0          0 eth0
```

**网络命令：setup**

命令名称：setup

命令所在路径：/usr/bin/setup

执行权限：root

语法：setup

功能描述：配置网络，redhat系列自带，和ifconfig一样，不过永久生效

范例：
> setup
>
> service network restart

**挂载命令**

命令名称：mount

命令位置：/bin/mount

执行权限：所有用户

命令语法：mount [-t 文件系统] 设备文件名 挂载点

范例：
> mkdir /mnt/cdrom
>
> mount -t iso9660 /dev/sr0 /mnt/cdrom/
> 
> cd /mnt/cdrom/
> 
> cd 
>
> umount /dev/sr0

#### 4.8 关机重启命令

**1、shutdown命令**

> [root@localhost ~]# shutdown [选项] 时间

选项：

    -c： 取消前一个关机命令
    -h： 关机
    -r： 重启

> shutdown -r now

先关闭服务，再关机

**2、其他关机命令**

> [root@localhost ~]# halt
> 
> [root@localhost ~]# poweroff
> 
> [root@localhost ~]# init 0

**3、其他重启命令**

> [root@localhost ~]# reboot
>
> [root@localhost ~]# init 6

**4、系统运行级别**

    0 		关机
    1		单用户
    2		不完全多用户，不含NFS服务
    3		完全多用户
    4		未分配
    5		图形界面
    6		重启

修改系统默认运行级别
> [root@localhost ~]# cat /etc/inittab

```
# inittab is only used by upstart for the default runlevel.
#
# ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# System initialization is started by /etc/init/rcS.conf
#
# Individual runlevels are started by /etc/init/rc.conf
#
# Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
#
# Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
# with configuration in /etc/sysconfig/init.
#
# For information on how to write upstart event handlers, or how
# upstart works, see init(5), init(8), and initctl(8).
#
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
#
id:3:initdefault:
```

查询系统运行级别
> [root@localhost ~]# runlevel

```
N 3
```

**5、退出登录命令**

> [root@localhost ~]# logout

<br/><br/><br/><br/><br/>
### 参考资料




