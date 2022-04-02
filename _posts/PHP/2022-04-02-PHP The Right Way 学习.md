---
layout: post
categories: PHP
title: PHP The Right Way 学习
meta: PHP The Right Way 学习
---
* content
{:toc}

## 引言
 
《PHP The Right Way》内容有：

    欢迎阅读
        其他语言版本
        参与贡献
        分享与推广
    入门指南
        使用当前稳定版本 (7.1)
        内置的 Web 服务器
        Mac 安裝
        Windows 安裝
    代码风格指南
    语言亮点
        编程范式
        命名空间
        PHP 标准库
        命令行接口
        Xdebug 调试工具
    依赖管理
        Composer 与 Packagist
        PEAR
    开发实践
        基础知识
        日期和时间
        设计模式
        使用 UTF-8 编码
        国际化和本地化
    依赖注入
        基本概念
        复杂的问题
        容器
        延伸阅读
    数据库
        MySQL 扩展
        PDO 扩展
        数据库交互
        数据库抽象层
    使用模板
        好处
        原生 PHP 模板
        编译模板
        延伸阅读
    错误与异常
        错误
        异常
    安全
        Web 应用程序安全
        密码哈希
        数据过滤
        配置文件
        注册全局变量
        错误报告
    测试
        测试驱动开发
        行为驱动开发
        共享服务器
    服务器与部署
        平台即服务 (PaaS)
        虚拟或专用服务器
        共享服务器
        Building Your Application
    虚拟化
        Vagrant
        Docker
    缓存
        Opcode 缓存
        对象缓存
    代码注释
        PHPDoc
    资源
        PHP 官方
        值得关注的大牛
        指导
        PHP Paas 提供商
        框架
        组件
        其他有用的资源
        视频教程
        书籍
    社区
        用户群
        PHP 大会
        Elephpants
    荣誉

## 正文

### 欢迎

目前网络上充斥着大量的过时资讯，让 PHP 新手误入歧途，并且传播着错误的实践以及不安全的代码。
PHP 之道 收集了现有的 PHP 最佳实践、编码规范和权威学习指南，方便 PHP 开发者阅读和查找。

使用 PHP 沒有规范化的方式。本网站主要是向 PHP 新手介绍一些他们没有发现或者是太晚发现的主题， 
或是经验丰富的专业人士已经实践已久的做法提供一些新想法。
本网站也不会告诉您应该使用什么样的工具，而是提供多种选择的建议，并尽可能地说明方法及用法上的差异。

### 入门指南

#### 使用当前稳定版本 (7.1)

如果你刚开始学习 PHP，请使用最新的稳定版本 PHP 7.1。PHP 7.1 非常新，相较于 5.x 版本增加了强大的 新特性。
PHP 引擎大部分被重写，PHP 的运行速度已经远远超越过去。

在很长的一段时间内，你会发现 5.x 还在广泛的被使用，目前最新的 5.x 版本是 5.6。对于当下，这并不是一个坏选项。
然而，你应该尽快升级 - PHP 5.6 将会在 2018 停止安全更新。7.1 做了 向下兼容 处理，所以你的升级应该不会很难。
如果你想查找一个函数及其用法，可以去官方手册 php.net 中查找。

#### 内置的 web 服务器

PHP 5.4 之后, 你可以不用安装和配置功能齐全的 Web 服务器，就可以开始学习 PHP。 
要启动内置的 Web 服务器，需要从你的命令行终端进入项目的 Web 根目录，执行下面的命令:

> php -S localhost:8000

了解更多内置的命令行服务器

#### Mac 安装

OS X 系统会预装 PHP， 只是一般情况下版本会比最新稳定版低一些。目前 Mavericks 是 5.4.17、Yosemite 则是 5.5.9，El Capitan 是 5.5.29、Sierra 是 5.6.24， 但在 PHP 7.1 出来之后， 这些往往是不够的。

以下介绍几种在 OS X 上安装 PHP 的方法。

**通过 Homebrew 安装 PHP**

Homebrew 是一个强大的 OS X 专用包管理器， 它可以帮助你轻松的安装 PHP 和各种扩展。 Homebrew PHP 是一个包含与 PHP 相关的 Formulae，能让你通过 homebrew 安装 PHP 的仓库。

也就是说, 你可以通过 brew install 命令安装 php53、php54、php55、php56、php70或者 php71，并且通过修改 PATH 变量来切换各个版本。或者你也可以使用 brew-php-switcher 来自动切换。

**Install PHP via Macports**

**通过 Macports 安装 PHP**

MacPorts 是一个开源的，社区发起的项目，它的目的在于设计一个易于使用的系统，方便编译，安装以及升级 OS X 系统上的 command-line, X11 或者基于 Aqua 的开源软件。

MacPorts 支持预编译的二进制文件，因此你不必每次都重新从源码压缩包编译，如果你的系统没有安装这些包，它会节省你很多时间。

此时，你可以通过 port install 命名来安装 php54, php55, php56, php70 或者 php71，比如：

    sudo port install php56
    sudo port install php71

你也可以执行 select 命令来切换当前的 php 版本：

    sudo port select --set php php71

**通过 phpbrew 安装 PHP**

phpbrew 是一个安装与管理多个 PHP 版本的工具。它在应用程序或者项目需要不同版本的 PHP 时非常有用，让你不再需要使用虚拟机来处理这些情况。

**通过 Liip’s binary installer 安装 PHP**

php-osx.liip.ch 是另一种流行的选择，它提供了从 5.3 到 7.1 版本的单行安装功能。 它并不会覆盖Apple集成的PHP文件，而是将其安装在了一个独立的目录中(/usr/local/php5)。

**源码编译**

另一个让你控制安装 PHP 版本的选择就是 自行编译。 如果使用这种方法， 你必须先确认是否已经通过 「Apple’s Mac Developer Center」 下载、安装 Xcode 或者 “Command Line Tools for XCode”。

**集成包 (All-in-One Installers)**

上面列出的解决方案主要是针对 PHP 本身， 并不包含：比如 Apache，Nginx 或者 SQL 服务器。 集成包比如 MAMP 和 XAMPP 会安装这些软件并且将他们绑在一起，不过易于安装的背后也牺牲了一定的弹性。

#### Windows 安装

你可以从 windows.php.net/download 下载二进制包。 
解压后, 最好为你的 PHP 所在的根目录（php.exe 所在的文件夹）设置 PATH，这样就可以从命令行中直接执行 PHP。

如果只是学习或者本地开发，可以直接使用 PHP 5.4+ 内置的 Web 服务器， 还能省去配置服务器的麻烦。
如果你想要包含有网页服务器以及 MySql 的集成包，那么像是Web Platform Installer, XAMPP, 
EasyPHP 和 WAMP 这类工具将会帮助你快速建立 Windows 开发环境。不过这些工具将会与线上环境有些许差别，
如果你是在 Windows 下开发，而生产环境则部署至 Linux ，请小心。

如果你需要将生产环境部署在 Windows 上，那 IIS7 将会提供最稳定和最佳的性能。
你可以使用 phpmanager (IIS7 的图形化插件) 让你简单的设置并管理 PHP。
IIS7 也有内置的 FastCGI ，你只需要将 PHP 配置为它的处理器即可。更多详情请见dedicated area on iis.net。

一般情况下，使用不同环境开发，会导致你在上线代码时出现 Bug。
如果你在 Window 下开发将会用于 Linux 下运行的代码，请应该考虑使用 虚拟机.

这里推荐 Chris Tankersley 的一篇关于 Window 下工具使用的文章 - Windows 下的 PHP 开发.

### 代码风格指南

PHP 社区是庞大而多样的，由无数的库、框架和组件组成。PHP 开发人员通常会选择其中的几个并将它们组合到一个项目中。
PHP 代码遵循 (尽可能接近) 通用的代码样式很重要，这样开发人员就可以轻松地为他们的项目混合和匹配各种库。

PHP-FIG 已经提出并批准了一系列风格建议。并不是所有的都与代码风格有关，但是确实与代码风格有关的是  PSR-1, PSR-12 and PSR-4。
这些建议仅仅是 Drupal、 Zend、 Symfony、 Laravel、 CakePHP、 phpBB、 AWS SDK、 FuelPHP、 Lithium 等许多项目正在采用的一系列规则。
你可以在你自己的项目中使用它们，或者继续使用你自己的个人风格。

理想情况下，您应该编写遵循已知标准的 PHP 代码。这可能是 PSRs 的任何组合，或者是 PEAR 或 Zend 制定的编码标准之一。
这意味着其他开发人员可以轻松地阅读和使用您的代码，而实现组件的应用程序甚至可以在使用大量第三方代码时保持一致性。

    阅读 PSR-1
    阅读 PSR-12
    阅读 PSR-4
    阅读关于 PEAR 编码标准
    阅读关于 Symfony 编码标准

你可以使用 PHP_CodeSniffer 根据这些建议中的任何一个检查代码，并为诸如 Sublime Text 之类的文本编辑器提供实时反馈插件。

你可以使用以下工具之一自动修复代码布局:

    一个是 PHP Coding Standards Fixer 它有一个经过很好测试的代码库
    此外 PHP Code Beautifier and Fixer 可以使用 PHP _ codesniffer 附带的工具相应地调整代码

你可以在 shell 中手动运行 phpcs:

    phpcs -sw --standard=PSR1 file.php

它将显示错误并描述如何修复它们。在 git hook 中包含这个命令也很有帮助。
这样，包含违反所选标准的分支就不能进入存储库，直到这些违反被修复。

如果你有 PHP_CodeSniffer, 那么你可以通过 PHP Code Beautifier 和 Fixer 自动修复它报告的代码布局问题。

    phpcbf -w --standard=PSR1 file.php

Another option is to use the  It will show which kind of errors the code structure had before it fixed them.

另一个选择是使用 PHP Coding Standards Fixer。它将显示代码结构在修复之前出现了哪些错误。

    php-cs-fixer fix -v --rules=@PSR1 file.php

所有符号名称和代码基础结构都首选英语。注释可以用任何语言编写，所有当前和未来可能正在编写代码库的各方都可以轻松阅读。

最后，编写干净的 PHP 代码的一个很好的补充资源是 Clean Code PHP.

### 语言亮点




## 参考资料

PHP 之道（PHP The Right Way 中文版） 新版 <https://learnku.com/docs/php-the-right-way/PHP8.0>

PHP 之道（PHP: The Right Way）：书写更好的 PHP 代码 （<http://www.phptherightway.com/>）

PHP 之道 中文版 （<http://laravel-china.github.io/php-the-right-way/>）。

