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

命令格式 ：命令  [-选项]  [参数]
        
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

语法：ls  选项[-ald]  [文件或目录]

    -a    显示所有文件，包括隐藏文件 all
    -l    详细信息显示 long
    -d    查看目录属性 directory
    -h    人性化显示 human
    -i    节点号 inode
    
> ls -l

`-rw-r--r--`	

    - 文件类型（- 文件 d 目录  l 软链接文件）
    
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

语法：mkdir -p  [目录名]

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

语法：cd [目录]

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

语法：pwd

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

语法：rmdir [目录名]

功能描述： 删除空目录

范例： 
> $ rmdir /tmp/Japan/boduo             

**目录处理命令：cp**

命令名称：cp

命令英文原意：copy

命令所在路径：/bin/cp

执行权限：所有用户

语法：cp  -rp  [原文件或目录] [目标目录] 

    -r  复制目录
    -p  保留文件属性
    
功能描述：复制文件或目录

范例：
> $ cp  -r /tmp/Japan/cangjing  /root

将目录/tmp/Japan/cangjing复制到目录/root下

> $ cp  -rp /tmp/Japan/boduo /tmp/Japan/longze /root

将/tmp/Japan目录下的boduo和longze目录复制到 /root下，保持目录属性

**目录处理命令：mv**

命令名称：mv

命令英文原意：move

命令所在路径：/bin/mv

执行权限：所有用户

语法：mv  [原文件或目录]  [目标目录]

功能描述：剪切文件、改名

**目录处理命令：rm**

命令名称：rm

命令英文原意：remove

命令所在路径：/bin/rm

执行权限：所有用户

语法：rm  -rf   [文件或目录]

    -r  删除目录
    -f  强制执行
                   
功能描述：删除文件

范例：
> $ rm  /tmp/yum.log

删除文件/tmp/yum.log

> $ rm -rf  /tmp/Japan/longze

删除目录/tmp/Japan/longze             


##### 4.1.3 文件处理命令

**文件处理命令：touch**

命令名称：touch

命令所在路径：/bin/touch

执行权限：所有用户

语法：touch  [文件名]       
         
功能描述：创建空文件

范例： 
> $ touch Japanlovestory.list             

**文件处理命令：cat**

命令名称：cat

命令所在路径：/bin/cat

执行权限：所有用户

语法：cat [文件名]

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

语法：tac [文件名]

功能描述：显示文件内容（反向列示）

范例：
> $ tac  /etc/issue

**文件处理命令：more**

命令名称：more

命令所在路径：/bin/more

执行权限：所有用户

语法：more  [文件名]
    
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

语法：less  [文件名]

功能描述：分页显示文件内容（可向上翻页，还可使用`/`搜索关键字`）

范例：   
> $  less  /etc/services

**文件处理命令：head**

命令名称：head

命令所在路径：/usr/bin/head

执行权限：所有用户

语法：head  [文件名]

功能描述：显示文件前面几行

    -n 指定行数
			
范例：   
> $ head -n 20 /etc/services

**文件处理命令：tail**

命令名称：tail

命令所在路径：/usr/bin/tail

执行权限：所有用户

语法：tail  [文件名]

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

语法：ln  -s  [原文件]  [目标文件]

    -s  创建软链接
                 
功能描述：生成链接文件

范例：
> $ ln -s  /etc/issue  /tmp/issue.soft

创建文件/etc/issue的软链接/tmp/issue.soft

> $ ln  /etc/issue  /tmp/issue.hard

创建文件/etc/issue的硬链接/tmp/issue.hard

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
> grep -i ^# /etc/inittab

#### 4.4 帮助命令


#### 4.5 用户管理命令


#### 4.6 压缩解压命令


#### 4.7 网络命令


#### 4.8 关机重启命令


<br/><br/><br/><br/><br/>
### 参考资料




