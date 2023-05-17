---
layout: post
categories: Git
title: gitignore 使用说明
meta: .gitignore 使用说明
---
* content
{:toc}

## 一、为什么使用 .gitignore

在一些项目中，我们不想让本地仓库的所有文件都上传到远程仓库中，而是有选择的上传，
比如：一些依赖文件（node_modules下的依赖）、bin 目录下的文件、测试文件等。
一方面将一些依赖、测试文件都上传到远程传输量很大，另一方面，一些文件对于你这边是可用的，
在另一个人那可能就不可用了，比如：本地配置文件。

为了解决上述问题，git 引入了 .gitignore 文件，使用该文件来选择性的上传文件。

## 二、使用规则

### 2.1 注释

注释使用 # 开头，后面跟注释内容。如下所示：
```
/nbproject/
# /thinkphp/
# /vendor/
/runtime/*
# /addons/*
# /application/admin/command/Install/*.lock
# /public/assets/libs/
# /public/assets/addons/*
/public/uploads/*
.idea
composer.lock
*.log
*.css.map
!.gitkeep
.env
.svn
.vscode
node_modules
```

上例中，以 # 开头的便是注释。

### 2.2 忽略文件

（1）忽略文件和目录

例如：folderName : 表示忽略 folderName 文件和 folderName 目录，会自动搜索多级目录，比如：`*/*/folderName`。

来看一个简单的例子，本地仓库的目录结构如下所示：
```
linuxy@linuxy:~/linuxgit$ tree
.
├── folder
│   └── file1
└── src
    ├── folder
    └── utils
        └── folder
  
3 directories, 3 files
linuxy@linuxy:~/linuxgit$
```

其中，.gitignore 文件内容如下所示：
```
linuxy@linuxy:~/linuxgit$ cat .gitignore 
# this is .gitignore file.
# 以下是忽略的文件
  
folder
linuxy@linuxy:~/linuxgit$
```

故在本地仓库中，同名的 folder 目录、src/folder 文件、src/utils/folder 文件都会被忽略，即：不会被提交到远程仓库中。

（2）仅忽略文件

模式如下所示：
```
foldername
  
!foldername/
```

仅忽略 foldername 文件，而不忽略 foldername 目录，其中，感叹号“!”表示反向操作。

来看一个简单的例子，本地仓库的目录结构如下所示：
```
linuxy@linuxy:~/linuxgit$ tree
.
├── folder
│   └── file1
└── src
    ├── folder
    └── utils
        └── folder
  
3 directories, 3 files
linuxy@linuxy:~/linuxgit$
```

其中，.gitignore 文件内容如下所示：
```
linuxy@linuxy:~/linuxgit$ cat .gitignore 
# this is .gitignore file.
# 以下是忽略的文件
  
folder
!folder/
linuxy@linuxy:~/linuxgit$
```

故在本地仓库中，src/folder 文件、src/utils/folder 文件会被忽略，而同名的 folder 目录不会被忽略。

### 2.3 忽略目录

模式如下所示：
```
foldername/
```

忽略 foldername 目录，包括：

（1）当前目录下的foldernname，例如：`foldername/`；

（2）多级目录下的 foldername，例如：`*/*/foldername/`；

来看一个简单的例子，本地仓库的目录结构如下所示：
```
linuxy@linuxy:~/linuxgit$ tree
.
├── folder
│   └── file1
└── src
    ├── folder
    └── utils
        └── folder
  
3 directories, 3 files
linuxy@linuxy:~/linuxgit$
```

其中，.gitignore 文件内容如下所示：
```
linuxy@linuxy:~/linuxgit$ cat .gitignore 
# this is .gitignore file.
# 以下是忽略的文件
  
folder/
linuxy@linuxy:~/linuxgit$
```

故在本地仓库中，folder 目录会被忽略，而同名的 src/folder 文件和 src/utils/folder 文件不会被忽略。

### 2.4 使用通配符

常用的通配符有：

（1）星号“*” ：匹配多个字符；

（2）问号“?”：匹配除 `/` 外的任意一个字符；

（3）方括号`[xxxx]`：匹配多个列表中的字符；

来看一个简单的例子，本地仓库的目录结构如下所示：
```
linuxy@linuxy:~/linuxgit$ tree
.
├── src
│   ├── add.c
│   ├── add.i
│   └── add.o
├── test.c
├── test.i
└── test.o
  
1 directory, 6 files
linuxy@linuxy:~/linuxgit$
```

其中，.gitignore 文件内容如下所示：
```
linuxy@linuxy:~/linuxgit$ cat .gitignore 
# this is .gitignore file.
# 以下是忽略的文件
  
*.[io]
linuxy@linuxy:~/linuxgit$
```

故在本地仓库中，test.i文件、test.o文件、src/add.o文件、src/add.i文件会被忽略，
而 test.c文件和add.c 文件不会被忽略。注意：这里忽略的匹配模式是多级目录的。

### 2.5 反向操作

模式如下所示：
```
!匹配模式
```

表示之前忽略的匹配模式再次包含在跟踪内容里。

例如在仅忽略文件时提到的模式：
```
foldername
  
!foldername/
```

表示仅忽略 foldername 文件，而不忽略 foldername 目录。

### 2.6 双星号

斜杠后紧跟两个连续的星号"**"，表示多级目录。

来看一个简单的例子，.gitignore文件的内容如下所示：
```
linuxy@linuxy:~/linuxgit$ cat .gitignore 
# this is .gitignore file.
# 以下是忽略的文件
  
src/**/file
linuxy@linuxy:~/linuxgit$
```

### 2.7 其它规则

（1）空行不匹配任何文件；

（2）git 跟踪文件，而不是目录；

（3）在 .gitignore 文件中，每行表示一种模式；

（4）如果本地仓库文件已被跟踪，那么即使在 .gitignore 中设置了忽略，也不起作用。

（5）.gitignore 文件也会被上传的到远程仓库，所以，同一个仓库的人可以使用同一个.gitignore 文件。

## 三、总结

在使用 git 过程中，掌握 .gitignore 的使用很重要，可以减少不必要的文件上传到远程。

git对于.ignore配置文件是按行从上到下进行规则匹配的，意味着如果前面的规则匹配的范围更大，则后面的规则将不会生效；
```
#               表示此为注释,将被Git忽略
*.a             表示忽略所有 .a 结尾的文件
!lib.a          表示但lib.a除外
/TODO           表示仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/          表示忽略 build/目录下的所有文件，过滤整个build文件夹；
doc/*.txt       表示会忽略doc/notes.txt但不包括 doc/server/arch.txt
 
bin/:           表示忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin:           表示忽略根目录下的bin文件
/*.c:           表示忽略cat.c，不忽略 build/cat.c
debug/*.obj:    表示忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj
**/foo:         表示忽略/foo,a/foo,a/b/foo等
a/**/b:         表示忽略a/b, a/x/b,a/x/y/b等
!/bin/run.sh    表示不忽略bin目录下的run.sh文件
*.log:          表示忽略所有 .log 文件
config.php:     表示忽略当前路径的 config.php 文件
 
/mtk/           表示过滤整个文件夹
*.zip           表示过滤所有.zip文件
/mtk/do.c       表示过滤某个具体文件
```

## 解惑

### 问题一

如果我只想忽略当前目录下的 a.txt，而不想忽略子级目录下的 a.txt，`.gitignore`文件怎么写？

```
a.txt

!**/a.txt
```

### 问题二

如果我想忽略model目录下的所有非`.php`文件，`.gitignore`文件怎么写？

```
model/*

!model/*.php
```

### 问题三

如果我想忽略目录下的所有文件，但不忽略二级以上目录下的文件或目录，`.gitignore`文件怎么写？

在`.gitignore`中，结合使用`*`和`!filename`的语法，可以达到除特定文件或目录外全部忽略的目的。
但当希望不忽略的文件或目录在二级或多级目录下时，如果这样写：
```
/* 
!/sub/subsub/ 
```

是不能达到预期效果的，这会导致根目录下的全部文件被忽略，包括`/sub/subsub/`。因为`.gitignore`语法中，
`!filename`只能对同级的`/*`生效，因此如果要在根目录中排除多级目录中的文件，需要重复地指明每一级目录的忽略规则。
下面以忽略根目录中的全部文件，但不忽略/1st/2nd/目录中的tgt文件为例：
```
/* 
!/1st/2nd/tgt 
# Wrong! Still ignored. 

/* 
!/1st/ 

/1st/* 
!/1st/2nd/ 

/1st/2nd/*  
!/1st/2nd/tgt 
# Correct! Specify rules for each directory. 
```








<br/><br/><br/><br/><br/>
## 参考资料

Git 开发必备 .gitignore 详解 <https://blog.csdn.net/nyist_zxp/article/details/119887324> 

Git中管理该目录下多级目录下的文件 <https://www.jianshu.com/p/2c13b6616956>

详解Git.gitignore开发必备建议收藏 <https://www.10qianwan.com/articledetail/809677.html>



