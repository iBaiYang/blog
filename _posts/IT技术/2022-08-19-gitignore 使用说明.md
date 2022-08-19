---
layout: post
categories: IT技术
title: gitignore 使用说明
meta: .gitignore 使用说明
---
* content
{:toc}
  
## 正文

### 一、为什么使用 .gitignore

在一些项目中，我们不想让本地仓库的所有文件都上传到远程仓库中，而是有选择的上传，
比如：一些依赖文件（node_modules下的依赖）、bin 目录下的文件、测试文件等。
一方面将一些依赖、测试文件都上传到远程传输量很大，另一方面，一些文件对于你这边是可用的，
在另一个人那可能就不可用了，比如：本地配置文件。

为了解决上述问题，git 引入了 .gitignore 文件，使用该文件来选择性的上传文件。

### 二、使用规则

#### 2.1 注释

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

#### 2.2 忽略文件

（1）忽略文件和目录

例如：folderName : 表示忽略 folderName 文件和 folderName 目录，会自动搜索多级目录，比如：`*/*/folderName`。

来看一个简单的例子，本地仓库的目录结构如下所示：



#### 2.3 忽略目录

#### 2.4 使用通配符

#### 2.5 反向操作

#### 2.6 双星号

#### 2.7 其它规则

（1）空行不匹配任何文件；

（2）git 跟踪文件，而不是目录；

（3）在 .gitignore 文件中，每行表示一种模式；

（4）如果本地仓库文件已被跟踪，那么即使在 .gitignore 中设置了忽略，也不起作用。

（5）.gitignore 文件也会被上传的到远程仓库，所以，同一个仓库的人可以使用同一个.gitignore 文件。


### 三、总结

在使用 git 过程中，掌握 .gitignore 的使用很重要，可以减少不必要的文件上传到远程。








<br/><br/><br/><br/><br/>
## 参考资料

Git 开发必备 .gitignore 详解 <https://blog.csdn.net/nyist_zxp/article/details/119887324> 





