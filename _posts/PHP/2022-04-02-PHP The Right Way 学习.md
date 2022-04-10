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

#### 编程范式

PHP 是一个灵活的动态语言，支持多种编程技巧。这几年一直不断的发展，
重要的里程碑包含 PHP 5.0 (2004) 增加了完善的面向对象模型，PHP 5.3 (2009) 增加了匿名函数与命名空间以及 PHP 5.4 (2012) 增加的 traits。

**面向对象编程**

PHP 拥有完整的面向对象编程的特性，包括类，抽象类，接口，继承，构造函数，克隆和异常等。

    阅读 PHP 面向对象编程
    阅读 Traits

**函数式编程**

PHP 支持函数是「第一等公民」，即函数可以被赋值给一个变量，包括用户自定义的或者是内置函数，然后动态调用它。
函数可以作为参数传递给其他函数（称为_高阶函数_），也可以作为函数返回值返回。

PHP 支持递归，也就是函数自己调用自己，但多数 PHP 代码使用迭代。

自从 PHP 5.3 (2009) 之后开始引入对闭包以及匿名函数的支持。

PHP 5.4 增加了将闭包绑定到对象作用域中的特性，并改善其可调用性，如此即可在大部分情况下使用匿名函数取代一般的函数。

    学习更多 PHP 函数式编程
    阅读匿名函数
    阅读闭包类
    更多关于 Closures RFC
    阅读 Callables
    阅读动态调用函数 call_user_func_array ()

**元编程**

PHP 通过反射 API 和魔术方法等机制，支持多种形式的元编程。有许多魔术方法，
如 `__get()`， `__set()`， `__clone()`， `__toString()`， `__invoke()` 等，可供开发者改变类的行为。 
Ruby 开发者常说 PHP 缺少 method_missing 方法，实际上通过 `__call()` 和 `__callStatic()` 就可以完成相同的功能。

    阅读魔术方法
    阅读反射
    阅读重载

#### 命名空间

如前所述，PHP 社区已经有许多开发者开发了大量的代码。这意味着一个类库的 PHP 代码可能使用了另外一个类库中相同的类名。
如果他们都使用了同一个命名空间，那将会产生冲突导致异常。

命名空间 解决了这个问题。如 PHP 手册里所描述，命名空间好比操作系统中的目录，同名的两个文件可以共存于不同的目录中。
同理同名的两个 PHP 类可以在不同的 PHP 命名空间下共存，就这么简单。

因此把你的代码放在你自己的命名空间下就非常重要，这样它可以被其他开发者使用，而不必担心与第三方库发生冲突。

PSR-4 提供了一种命名空间的推荐使用方式，用于提供标准的文件、类和命名空间的约定，进而让代码做到即插即用。

2014 年 10 月，PHP-FIG 废弃了原先的自动加载标准： PSR-0 ，但新的自动加载标准 PSR-4 要求 PHP 5.3 以上的版本，
而许多项目仍在使用 PHP 5.2，所以目前新旧两个标准都是完全可用的。

如果您要为新应用程序或扩展包使用自动装载标准，请查看 PSR-4 规范。

    关于命名空间
    关于 PSR-0
    关于 PSR-4

#### PHP 标准库

PHP 标准库 (SPL) 与 PHP 打包在一起， 并提供了类和接口的集合。
它主要由常见的数据结构类 (栈，队列，堆等) 和迭代器组成， 可以遍历这些数据结构或您自己的实现 spl 接口的类。

    关于 SPL
    SPRINDA.com 上的 SPL 视频课程 (付费)

#### 命令行界面

PHP 是为编写 Web 应用程序而创建的，而且对于编写命令行界面 (CLI) 程序脚本也很有用。 
命令行 PHP 程序可以帮助自动执行测试、部署和管理应用程序等常见任务。

CLI PHP 程序功能强大，因为您可以直接使用应用程序的代码，而无需为其创建和获得 Web GUI。
只需确保 不要 将您的 CLI PHP 脚本放在您的公共 Web 根目录中！

尝试从您的命令行运行 php：

> php -i

`-i` 选项将像 `phpinfo()` 函数一样打印您的 PHP 配置.

`-a` 选项提供了一个交互式 shell，类似于 Ruby 的 IRB 或 Python 的交互式 shell。这也有许多其他有用的 命令行选项。

接下来编写一个简单的 「Hello, $name」 CLI 程序， 先创建名为 hello.php 的脚本

```php
<?php
if ($argc !== 2) {
    echo "Usage: php hello.php <name>" . PHP_EOL;
    exit(1);
}
$name = $argv[1];
echo "Hello, $name" . PHP_EOL;
```

PHP 会在脚本运行时根据参数设置两个特殊的变量，$argc 是一个整数，表示参数个数，
$argv 是一个数组变量，包含每个参数的值，它的第一个元素一直是 PHP 脚本的名称，在本例中为 hello.php。

命令运行失败时，可以通过 `exit()` 表达式返回一个非 0 整数来通知 shell，常用的 exit 返回码可以查看列表。

运行上面的脚本，在命令行输入：
```
> php hello.php
Usage: php hello.php <name>
> php hello.php world
Hello, world
```

    学习如何在命令行运行 PHP 脚本

#### Xdebug

合适的调试器是软件开发中最有用的工具之一， 使你可以跟踪程序执行结果并监视程序堆栈中的信息。
Xdebug 是一个 php 的调试器，它可以被用来在很多 IDE (集成开发环境) 中做断点调试以及堆栈检查。
它还可以像 PHPUnit 和 KCacheGrind 一样，做代码覆盖检查或者程序性能跟踪。

如果你仍在使用 `var_dump()`/`print_r()` 调试，经常会发现自己处于困境，并且仍然找不到解决办法。这时，你该使用调试器了。

安装 Xdebug 可能很棘手，但是其中最重要的一种特性「远程调试」 - 当您在本地开发代码，
然后在虚拟机或者其他服务器上测试时，远程调试可能是你想要的一种方式

一般情况下你需要修改你的 Apache VHost 或者 .htaccess 文件的这些值:
```
php_value xdebug.remote_host 192.168.?.?
php_value xdebug.remote_port 9000
```

「remote host」 和 「remote port」 这两项对应和你本地开发机监听的地址和端口。
然后将你的 IDE 设置成「listen for connections」模式，并访问网址：
`http://your-website.example.com/index.php?XDEBUG_SESSION_START=1`

你的 IDE 将会拦截当前执行的脚本状态，运行你设置的断点并查看内存中的值。

图形化的调试器可以让你非常容易的逐步的查看代码、变量，以及运行时的 evel 代码。
许多 IDE 已经内置或提供了插件支持 XDebug 图形化调试器。比如 MacGDBp 是 Mac 上的一个免费，开源的单机调试器。

    学习更多 Xdebug
    学习更多 MacGDBp

### 依赖管理 

PHP 有大量的库、框架和组件可供选择。你可能会在项目中使用其中的一部分，这就是项目的依赖。
以前，PHP 没有一个好的方法来管理这些项目依赖。即使你手动管理它们，你仍然不得不担心自动加载器。
现在这不再是个问题了。

目前有两个主要的 PHP 依赖管理工具 - Composer 和 PEAR 。Composer 是目前最流行的 PHP 依赖管理工具，
而 PEAR 在之前很长一段时间里是主要的依赖管理工具。了解 PEAR 的历史是一个好主意，
因为即使你从来没有使用过它，你可能仍然会发现对它的引用。

#### Composer 与扩展包

Composer 是值得推荐的 PHP 的依赖管理工具。在 composer.json 文件中列出项目的依赖项，通过几个简单的命令，
Composer 会自动下载项目的依赖项并为你设置自动加载。Composer 类似于 node.js 中的 NPM，或者 Ruby 世界中的 Bundler 。

**如何安装 Composer**

下载 Composer 的最安全方式是 按照官方说明（ 本站文档）。这将验证安装程序是否损坏或被篡改。
安装程序会在你的 当前工作目录中 安装一个 composer.phar 二进制文件。

我们推荐你 全局 安装 Composer（例如，在 `/usr/local/bin` 中安装一个副本）。要做到这一点，接下来运行这个命令：

> mv composer.phar /usr/local/bin/composer

注意：如果上述操作因权限问题而失败，请使用 sudo 作为前缀。

本地使用 Composer 的话，你可以运行 php composer.phar ，全局的话是：composer。

**Windows 环境下安装**

对于 Windows 用户来说，最简单的方法是使用 ComposerSetup 安装程序，它执行全局安装并设置你的 $PATH ，
这样你就可以在任何目录下用命令行调用 composer。

**如何配置和安装依赖项**

Composer 在一个 composer.json 的文件中记录了你的项目的依赖关系。如果你愿意，你可以手工管理它，或者使用 Composer 本身。
`composer require` 命令添加一个依赖项，如果你没有 composer.json 文件它将会创建一个。
下面是一个例子，它将 Twig 作为你的项目的依赖项。
```
composer require twig/twig:^2.0
```

或者，`composer init` 命令会指导你为你的项目创建一个完整的 composer.json 文件。
无论哪种方式，一旦你创建了 composer.json 文件，你就可以告诉 Composer 下载并安装你的依赖项到 `vendor/` 目录中。
这也适用于你已经下载的已提供 composer.json 文件的项目。

> composer install

接下来，在你的应用程序的主要 PHP 文件中添加这一行，这将告诉 PHP 使用 Composer 的自动加载器来获取你的依赖项。

```
<?php
require 'vendor/autoload.php';
```

现在，你可以使用你的项目依赖，并且依赖会按需自动加载。

**更新依赖项**

Composer 创建了一个名为 `composer.lock` 的文件，存放了当你首次运行 `composer install` 时每个包的准确版本。
如果你将项目共享给他人，要确保 `composer.lock` 也一起共享出去，
这样别人在执行 `composer install` 的时候也会安装和你一样的包版本。想要更新依赖项，执行 `composer update` 命令就可以了。
在项目部署时不要使用 `composer update`，只执行 `composer install` 就可以了，
不然你会在生产环境中安装了和开发环境不同版本的依赖包。

这在你需要灵活定义版本时非常有用。例如 `~1.8` 意味着任何高于 `1.8.0` 的版本，但要小于 `2.0.x-dev`。
你同样可以使用 `*` 通配符，例如 `1.8.*`。
这样配置后，Composer 的 `composer update` 命令会将所有的依赖项升级到符合你设定的最新版本。

**更新通知**

要接收有关新版本发布的通知，你可以注册 libraries.io，它是一种 web 服务，可以在依赖有新版本时向你发送通知。

**检查你的依赖项是否存在安全问题**

PHP 本地安全检查助手（Local PHP Security Checker） 是一个命令行工具，
它会检查你的 `composer.lock` 文件，并告诉你是否需要更新哪些依赖项。

**使用 Composer 处理全局依赖**

Composer 同样可以处理全局依赖以及它们的二进制文件。用法很简单，只需要在命令前面加上 `global` 前缀。
例如你想安装 PHPUnit 并让它全局可用，你只需要执行这条命令：
```
composer global require phpunit/phpunit
```

这将创建一个 `~/.composer` 文件夹，您的全局依赖项位于该文件夹中。 
为了让已安装的软件包的二进制文件随处可用，您需要将 `~/.composer/vendor/bin` 文件夹添加到您的 `$PATH` 变量中。

    了解 Composer

#### PEAR

一些 PHP 开发人员喜欢的资深包管理器是 PEAR。 它的行为与 Composer 相似，但有一些显着差异。

PEAR 要求每个包都具有特定的结构，这意味着包的作者必须准备好与 PEAR 一起使用。 
使用未准备好与 PEAR 一起工作的项目是不可能的。

PEAR 全局安装包，这意味着一旦它们可用于该服务器上的所有项目，就在安装它们之后。 
如果许多项目依赖具有相同版本的相同包，这可能很好，但如果两个项目之间出现版本冲突，则可能会导致问题。

**如何安装 PEAR**

您可以通过下载 “.phar” 安装程序并执行来安装 PEAR。 PEAR 文档对每个操作系统都有详细的安装说明。

如果您使用的是 Linux，您还可以查看您的分发包管理器。 例如，Debian 和 Ubuntu 有一个 apt `php-pear` 包。

**如何安装包**

如果该包在 PEAR 包列表 中，您可以通过指定官方名称进行安装：
> pear install foo

如果软件包托管在另一个频道上，您需要先 “发现” 该频道，并在安装时指定它。 
有关此主题的更多信息，请参阅 使用频道文档。

    了解 PEAR

**使用 Composer 来安装 PEAR 扩展包**

如果你已经使用着 Composer ，并且还想安装一些 PEAR 扩展包，那么可以使用 Composer 来处理 PEAR 依赖项。
下面是从 `pear2.php.net` 安装扩展包的示例：
```
{
    "repositories": [
        {
            "type": "pear",
            "url": "https://pear2.php.net"
        }
    ],
    "require": {
        "pear-pear2/PEAR2_Text_Markdown": "*",
        "pear-pear2/PEAR2_HTTP_Request": "*"
    }
}
```

第一部分 "`repositories`" 是告知 Composer 它应该 “初始化”（即在 PEAR 的术语 “发现”）一个 pear 包。
然后， `require` 部分将在包名称前加上前缀，如下所示：
```
pear-channel/Package
```

为了避免发生冲突，前缀 “pear” 是硬编码写死的，由于 pear-channel 可能会与另一个软件包供应商名称相同，
因此可以用 channel 短名称（或者是完整 URL）来引用软件包所在的 channel 。

安装扩展包后，它将放到你的 vendor 文件夹中，并可以通过 Composer 的自动加载器进行加载:
```
vendor/pear-pear2.php.net/PEAR2_HTTP_Request/pear2/HTTP/Request.php
```

要使用这个 PEAR 包，只需像这样引用它即可：
```
<?php
$request = new pear2\HTTP\Request();
```

    学习更多 PEAR 和 Composer 的使用

### 开发实践 

#### 基础知识

PHP 是一门浩瀚的语言，各个水平层次的开发者都可以利用它进行快速且高效的开发。
然而，在对语言逐渐深入的学习过程中，我们往往会因为走捷径或不良习惯而忘记（或忽视掉）基础的知识。
为了帮助彻底解决这个问题，这一章的目的就是提醒开发人员注意有关 PHP 的基础编程实践。

    学习更多基础知识

#### 日期和时间

PHP 中 DateTime 类的作用是帮助你读、写、比较或者计算日期和时间。
PHP 中除了 DateTime 之外还有许多与日期和时间相关的函数，但 DateTime 类为大多数常规使用提供了优秀的面向对象接口。
它还可以处理时区，不过这并不在这篇简短的介绍之内。

在使用 DateTime 之前，通过 `createFromFormat()` 工厂方法将原始的日期与时间字符串转换为对象
或使用 `new DateTime` 来取得当前的日期和时间。使用 `format()` 将 DateTime 转换回字符串用于输出。

```php
<?php
$raw = '22. 11. 1968';
$start = DateTime::createFromFormat('d. m. Y', $raw);

echo 'Start date: ' . $start->format('Y-m-d') . PHP_EOL;
```

对 DateTime 进行计算时可以使用 DateInterval 类。
DateTime 类具有例如 `add()` 和 `sub()` 等将 DateInterval 当作参数的方法。
编写代码时注意不要认为每一天都是由相同的秒数构成的，不论是夏令时（DST）还是时区转换，
使用时间戳计算都会遇到问题，应当选择日期间隔。使用 `diff()` 方法来计算日期之间的间隔，
它会返回新的 DateInterval，非常容易进行展示。

```php
<?php
// create a copy of $start and add one month and 6 days
$end = clone $start;
$end->add(new DateInterval('P1M6D'));

$diff = $end->diff($start);
echo 'Difference: ' . $diff->format('%m month, %d days (total: %a days)') . PHP_EOL;
// Difference: 1 month, 6 days (total: 37 days)
```

你可以在 DateTime 对象上使用标准比较符：
```php
<?php
if ($start < $end) {
    echo "start 在 end 之前!" . PHP_EOL;}
```

最后一个 DatePeriod 类的例子。它用于迭代重复发生的事件。
它接收两个 DateTime 对象，开始和结束日期，并返回这期间所有的时间间隔。
```php
<?php
// 输出从 start 到 end 之间所有的星期四
$periodInterval = DateInterval::createFromDateString('first thursday');
$periodIterator = new DatePeriod($start, $periodInterval, $end, DatePeriod::EXCLUDE_START_DATE);
foreach ($periodIterator as $date) {
    // 输出每个日期
    echo $date->format('Y-m-d') . ' ';
}
```

一个很流行的 PHP API 扩展 Carbon，它继承了 DateTime 类中所有的内容，因此只修改了很少的代码，
但额外包括了很多功能，包括：本地化支持，DateTime 对象的加、减、格式化方法，
以及通过模拟选择日期和时间的方式来测试代码的方法。

    关于 DateTime
    关于日期格式化 (接受日期格式化字符串选项)

#### 设计模式

当你在构建应用时，在代码中使用通用模式，并且在项目整体结构中使用通用模式，这非常有用。
通用模式可以让你更轻松地管理代码，并让其他开发人员很快地了解各部分程序是如何结合在一起的。

如果你使用框架开发应用，更多的业务代码和项目结构都是基于框架，这样模式上的决策都是框架决定的。
但是你仍然可以为你的代码在框架之上选择最佳的模式。
另一方面，如果你没有使用框架开发，那么你必须基于你应用该程序的类型和大小选择最适合的模式。

你可以参阅以下工作示例，了解更多有关 PHP 设计模式的信息：

[designpatternsphp.readthedocs.io/](https://designpatternsphp.readthedocs.io)

#### 使用 UTF-8 编码

本章最初是由 Alex Cabal 撰写在 PHP 最佳实践（PHP Best Practices） 中，我们使用它作为 UTF-8 编码建议的基础。

**这不是在开玩笑。请小心、细致并前后一致地处理它。**

目前 PHP 仍未在底层实现对 Unicode 的支持。虽然有很多途径可以确保 UTF-8 字符串能够被正确地处理，
但这并不是件很简单的事情，通常需要对 Web 应用进行全方面的检查，从 HTML 到 SQL 再到 PHP。
我们的目标是争取做一个简短、实用的归纳总结。

**PHP 层面的 UTF-8**

最基本的字符串操作，例如连结两个字符串或将字符串赋值给变量，并不需要对 UTF-8 做特殊处理。
然而大多数字符串的函数，如 `strpos()` 和 `strlen()`，的确需要特殊处理。这些函数通常有一个 `mb_*` 的版本：
比如，`mb_strpos()` 和 `mb_strlen()`。这些 `mb_*` 字符串是由 多字节字符串扩展（Multibyte String Extension） 提供支持的，
它专门为操作 Unicode 字符串而特别进行了设计。

在操作 Unicode 字符串时，请务必使用 `mb_*` 系列函数。例如，当你对一个 UTF-8 字符串使用 `substr()` 时，
返回的结果很可能会包含一些乱码。正确的方式是使用多字节版本的 `mb_substr()`。

最难的地方在于要始终记得使用 `mb_*` 函数。即使只忘了一次，你的 Unicode 字符串在接下来的过程中就有变成乱码的风险。

并非所有字符串函数都有一个 `mb_*` 对应项。 如果没有一个适合你想做的事情，那么你可能就不走运了。

您应该在您编写的每个 PHP 脚本的顶部（或在您的全局包含脚本的顶部）使用 `mb_internal_encoding()` 函数，
如果您的脚本输出到浏览器，则应该在它之后使用 `mb_http_output()` 函数。
在每个脚本中明确定义字符串的编码将为您省去很多麻烦。

此外，许多对字符串进行操作的 PHP 函数都有一个可选参数，可让您指定字符编码。 
在给出选项时，您应该始终明确指出 UTF-8。 例如，`htmlentities()` 有一个字符编码选项，如果处理此类字符串，
您应该始终指定 UTF-8。 请注意，从 PHP 5.4.0 开始，UTF-8 是 `htmlentities()` 和 `htmlspecialchars()` 的默认编码。

最后，如果您正在构建分布式应用程序并且不能确定是否会启用 `mbstring` 扩展，
那么请考虑使用 [patchwork/utf8](https://packagist.org/packages/patchwork/utf8) Composer 包。 
如果可用，这将使用 `mbstring`，否则，将自动回退到非 UTF-8 函数。

**数据库级别的 UTF-8**

如果您的 PHP 脚本访问 MySQL，即使您遵循上述所有预防措施，您的字符串也有可能作为非 UTF-8 字符串存储在数据库中。

要确保您的字符串以 UTF-8 格式从 PHP 传输到 MySQL，请确保您的数据库和表都设置为 “utf8mb4” 字符集和排序规则，
并且您在 PDO 连接字符串中使用 “utf8mb4” 字符集。 请参阅下面的示例代码。 这是至关重要的。

请注意，必须使用 utf8mb4 字符集才能获得完整的 UTF-8 支持，而不是 utf8 字符集！请进一步阅读了解原因。

**浏览器级别的 UTF-8**

使用 `mb_http_output()` 函数来确保你的 PHP 脚本在你浏览器上输出 UTF-8 的字符串。

浏览器被告知该 HTTP 响应的页面应被视为 UTF-8 编码。现在，在 HTTP 响应头中设置字符集很常见，如下所示：
```php
<?php
header('Content-Type: text/html; charset=UTF-8')
```

历史上的做法是在页面的 <head> 标签中包含 <meta> 标签来设置字符集。
```php
<?php
// 告诉PHP使用UTF-8编码直到脚本执行结束
mb_internal_encoding('UTF-8');
$utf_set = ini_set('default_charset', 'utf-8');
if (!$utf_set) {
    throw new Exception('could not set default_charset to utf-8, please ensure it\'s set on your system!');
}

// 告诉PHP使用UTF-8编码输出到浏览器
mb_http_output('UTF-8');

// 我们UTF-8测试的字符串
$string = 'Êl síla erin lû e-govaned vîn.';

// 使用字符串函数以某种方式转换字符串
// 现在我们使用非ASCII码字符来剪切字符串
$string = mb_substr($string, 0, 15);

// 连接数据库来存储转换后的字符串
// 有关更多信息，请参阅本文档中的PDO示例
// 注意在数据源中的字符集是 `charset=utf8mb4`
$link = new PDO(
    'mysql:host=your-hostname;dbname=your-db;charset=utf8mb4',
    'your-username',
    'your-password',
    array(
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_PERSISTENT => false
    )
);

// 以UTF-8存储我们转换后字符串到数据库
// 校验下你的DB和tables是不是以utf8mb4编码的
$handle = $link->prepare('insert into ElvishSentences (Id, Body, Priority) values (default, :body, :priority)');
$handle->bindParam(':body', $string, PDO::PARAM_STR);
$priority = 45;
$handle->bindParam(':priority', $priority, PDO::PARAM_INT); // 明确告诉PDO期待一个int类型
$handle->execute();

// 检索我们刚刚存储的字符串是否存储正确
$handle = $link->prepare('select * from ElvishSentences where Id = :id');
$id = 7;
$handle->bindParam(':id', $id, PDO::PARAM_INT);
$handle->execute();

// 存储的结果对象我们稍后会输出在HTML中
// 此对象不会占据你的内存，因为数据可以及时从DB中获取
$result = $handle->fetchAll(\PDO::FETCH_OBJ);

// 一个包装器样例来允许存储的转义数据到HTML中
function escape_to_html($dirty){
    echo htmlspecialchars($dirty, ENT_QUOTES, 'UTF-8');
}

header('Content-Type: text/html; charset=UTF-8'); //如果您的默认字符集已设置为utf-8，则不需要 
?>
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>UTF-8 test page</title>
    </head>
    <body>
        <?php
        foreach($result as $row){
            escape_to_html($row->Body);  //这将正确地将转换后的UTF-8字符串输出到浏览器
        }
        ?>
    </body>
</html>
```

**扩展阅读**

    PHP 手册：字符串操作
    PHP 手册：字符串函数
        strpos()
        strlen()
        substr()
    PHP 手册：多字节字符串函数
        mb_strpos()
        mb_strlen()
        mb_substr()
        mb_internal_encoding()
        mb_http_output()
        htmlentities()
        htmlspecialchars()
    Stack Overflow：什么原因导致 Unicode 不兼容？
    Stack Overflow：PHP 和 MySQL 国际化最佳实践
    MySQL 中如何完美支持 Unicode
    使用简便的 UTF-8 将 Unicode 引入 PHP
    Stack Overflow： DOMDocument::loadHTML 未正确编码 UTF-8

#### 国际化（i18n）和本地化（l10n）

对新人的声明：i18n 和 l10n 都是数字，是一种使用数字来缩短单词的缩写方式 —— 在我们的示例中， 国际化变为 i18n，本地化则变为 l10n。

首先，我们需要给这两个相近的概念和其他相关的事情一个定义：

* **国际化** 是指当你组织代码时，无需重构就可以适配不同的语言地区。
这件事只需要做一次 —— 最好是在项目开始时，否则你可能需要大规模重构源码！
* **本地化** 通常是基于 i18n 已经完成的工作，通过翻译内容来调整接口。通常是在需要支持新语言或区域时，
并且需要添加新接口时来做这件事，因为所有的接口需要在全部支持的语言中保证可用。
* **多元化** 定义了在不同语言之间互相操作字符串所需的规则和计数方法。例如，英语中你只有一个物品，
它是单数的，其余的都是复数。复数在这类语言中会在单词后面加一个 S，有的时候也会改变单词的一部分。
在其他语言中，像是俄罗斯与或塞尔威亚语，除了单数之外还有两种复数形式 —— 你甚至还能找到有四五种复数形式的语言，
例如斯洛文尼亚语、爱尔兰语和阿拉伯语。

##### 常见的实现方式

国际化 PHP 软件的最简单方法是使用数组文件并在模板中使用这些字符串，例如 `<h1><?=$TRANS['title_about_page']?></h1>`。
然而，这种方式几乎不推荐用于严肃的项目，因为它会带来一些沿途的维护问题 —— 有些可能在一开始就出现，例如复数形式。
因此，如果您的项目有多个页面，请不要尝试此操作。

i18n 和 l10n 最经典且常被用作参考的方法是 Unix 工具，名为 `gettext`。
它可以追溯到 1995 年，并且仍然是翻译软件的完整实现。运行起来很容易，同时仍然具有强大的支持工具。
我们将在这里讨论关于 Gettext 的内容。此外，为了帮助您不搞乱命令行，
我们将展示一个出色的 GUI 应用程序，可用于轻松更新您的 l10n 源代码

**其他工具**

有一些常用库支持 Gettext 和 i18n 的其他实现。其中一些看起来更容易安装或运行附加功能或 i18n 文件格式。
在本文档中，我们专注于 PHP 核心提供的工具，但这里我们列出了其他工具以供完成：
* aura/intl：提供国际化 (I18N) 工具，特别是面向包的 `per-locale` 消息翻译。
它对消息使用数组格式。不提供消息提取器，但通过 `intl` 扩展（包括复数消息）提供高级消息格式。
* oscarotero/Gettext：具有面向对象接口的 Gettext 支持；包括改进的辅助功能、
多种文件格式的强大提取器（`gettext` 命令本身不支持其中一些），并且还可以导出为除 `.mo/.po` 文件之外的其他格式。
如果您需要将翻译文件集成到系统的其他部分（如 JavaScript 界面）中，这会很有用。
* symfony/translation：支持许多不同的格式，但建议使用详细的 XLIFF。
不包含辅助函数或内置提取器，但支持在内部使用 `strtr()` 的占位符。
* zend/i18n：支持数组和 INI 文件，或 Gettext 格式。实现一个缓存层，让您免于每次都读取文件系统。
它还包括视图助手、区域感知输入过滤器和验证器。但是，它没有消息提取器。

其他框架也包括 i18n 模块，但这些模块在其代码库之外不可用：
* Laravel 支持基本数组文件，没有自动提取器，但包含用于模板文件的 `@lang` 助手方法。
* Yii 支持数组，Gettext，和基于数据库的翻译，并包括消息提取器。它得到了 `Intl` 扩展的支持，
从 PHP 5.3 之后可用，基于 ICU 项目。这使得 Yii 能运行强大的替换功能，
例如拼写数字，格式化日期、时间、间隔、货币、序号等。

如果你决定使用一个不提供提取器的库，那么可能需要使用 gettext 格式，那么你可以如本章其余部分所述，
使用原始的 gettext 工具链（包括 Poedit）。

#### Gettext

**安装**

你可能需要使用你的包管理器（例如 `apt-get` 或 `yum`）安装 Gettext 和相关的 PHP 库。
安装后，将 `extension=gettext.so` （Linux/Unix）或 `extension=php_gettext.dll` （Windows）添加到你的 `php.ini` 中以启用。

在这里，我们也会使用 Poedit 来创建翻译文件。你可能会在你的系统包管理器中找到它。
它适用于 Unix、Mac，和 Windows，也可以 在他们的网站上免费下载。

**结构**

**文件类型**

当使用 gettext 时你通常会处理三个文件。主要的一个是 PO（便携式对象）和 MO（机器对象）文件，
第一个是可读的「翻译对象列表」，第二个是进行本地化时 gettext 要解释的相应的二进制文件。
还有一个 POT （模板）文件，该文件仅包含源文件中的所有密钥，可作为生成和更新所有 PO 文件的指南。
这些模板文件不是必须的，根据你用于执行 l10n 的工具，你只需要 PO/MO 文件即可。
每个语言和区域始终有一对 PO/MO 文件，但每个域名只有一个 POT 文件。

**域**

某些情况下，在大型项目中，相同的词在上下文中表达不同的含义时，您可能需要分开翻译。
在这种情况下，你可以将它们分到不同的 域。它们一般是成组的 POT/PO/MO 文件，文件名是所谓的 翻译域。
为了简单起见，中小型项目通常只使用一个域，它的名称可以随意取，但我们通常会在示例代码中使用「main」。
举个例子，在 Symfony 项目中，域被用于分隔验证消息的翻译内容。

**区域代码**

区域设置只是标识一种语言版本的代码。它是按照 ISO 639-1 和 ISO 3166-1 alpha-2 规范定义的：
语言的两个小写字母，后面可以跟着一个下划线和两个表示国家或地区的大写字母来作为表示国家或地区的代码。
对于 不常见的语言，则是使用三个字母。

对于某些语言的使用者来说，国家的部分可能显得很多余。事实上，某些语言在不同国家有不同的方言，
如奥地利德语（`de_AT`）或巴西葡萄牙语（`pt_BR`）。第二部分用于区分这些方言 —— 如果这部分不存在，
则将其视为该语言的「通用」或「混合」版本。

**目录结构**

要使用 Gettext，我们需要遵循特定的目录结构。首先，你需要在你的源码库中为 l10n 在任意位置选择任意根目录。
在这里，你需要为每个区域创建一个文件夹，以及一个固定的 `LC_MESSAGES` 文件夹，其中包含你的成对的 `PO/MO` 文件。例如：
```
<project root>
 ├─ src/
 ├─ templates/
 └─ locales/
    ├─ forum.pot
    ├─ site.pot
    ├─ de/
    │  └─ LC_MESSAGES/
    │     ├─ forum.mo
    │     ├─ forum.po
    │     ├─ site.mo
    │     └─ site.po
    ├─ es_ES/
    │  └─ LC_MESSAGES/
    │     └─ ...
    ├─ fr/
    │  └─ ...
    ├─ pt_BR/
    │  └─ ...
    └─ pt_PT/
       └─ ...
```

**复数形式**

正如我们在介绍中所说的，不同的语言可能有不同的复数规则。然而，gettext 再次将我们从麻烦中拯救出来。
当创建新的 `.po` 文件时，必须声明该语言的 复数规则 ，对于复数敏感的翻译片段将针对每个规则具有不同的形式。 
在代码中调用 Gettext 时，你必须指定与句子相关的数字，并且它将确定要使用的正确形式，若有必要的话甚至使用字符串替换。

复数规则包括复数形式的数量，以及带有变量 `n` 的布尔表达式，用来确定给定数字属于哪条规则（从 0 开始计数）。例如：

* 日语： `nplurals=1; plural=0` - 只有一条规则
* 英语： `nplurals=2; plural=(n != 1);` - 有两条规则，当名词数量 N 为 1 时使用第一条规则，其他情况使用第二条规则
* 巴西葡萄牙语： `nplurals=2; plural=(n > 1);` - 有两条规则，当 N 大于 1 时使用第二条规则，其他情况使用第一条规则

现在，你已经了解了复数规则的工作原理 - 假如你还不太清楚，请查看 LingoHub 教程 更深入的解释 
- 可能你希望能直接从 列表 中复制所需规则，而不用手工编写。

当调用 Gettext 对带有数量的句子进行本地化时，你还必须向其提供相应的数字。
Gettext 会根据数量计算出应该生效的规则，并使用正确的本地化版本。对于定义的每条复数规则，你需要在`.po` 文件中包含不同的句子。

**实现示例**

在理论之后，让我们付诸实际。这是一个 `.po` 文件的节选 —— 不要在意它的格式，看它的整体内容。晒后你会知道如何来编辑它。

```
msgid ""
msgstr ""
"Language: pt_BR\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Plural-Forms: nplurals=2; plural=(n > 1);\n"

msgid "We are now translating some strings"
msgstr "Nós estamos traduzindo algumas strings agora"

msgid "Hello %1$s! Your last visit was on %2$s"
msgstr "Olá %1$s! Sua última visita foi em %2$s"

msgid "Only one unread message"
msgid_plural "%d unread messages"
msgstr[0] "Só uma mensagem não lida"
msgstr[1] "%d mensagens não lidas"
```

第一部分的作用类似于标题，特别 `msgid` 和 `msgstr` 都是空的。它描述了文件编码、复数形式和其他不太相关的内容。
第二部分将一个简单的字符串从英语翻译成巴西葡萄牙语，第三部分也做了相同的事，但使用了 `sprintf` 中的字符串替换，
因此翻译中可能包含用户名和访问日期。最后一部分是多元化形式的示例，在英语中显示单数和复数版本为 「msgid」，
对应的翻译为 「msgstr」 0 和 1（取决于复数规则给出的数字）。
这里使用了字符串替换，因此可以使用「% d」直接在句子中看到数字。
复数形式总是有两个 `msgid`（单数和复数），所以不建议使用复杂的语言作为翻译源。

**关于 l10n 键的讨论**










## 参考资料

PHP 之道（PHP The Right Way 中文版） 新版 <https://learnku.com/docs/php-the-right-way/PHP8.0>

PHP 之道（PHP: The Right Way）：书写更好的 PHP 代码 （<http://www.phptherightway.com/>）

PHP 之道 中文版 （<http://laravel-china.github.io/php-the-right-way/>）。

