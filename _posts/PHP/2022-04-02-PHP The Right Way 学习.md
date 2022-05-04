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

##### 关于 l10n 键的讨论

你可能已经注意到，我们将英语中实际的句子作为源 ID 使用。`msgid` 在你的 `.po` 文件中所有的使用都是相同的，
这意味着其他语言具有相同的格式和相同的 `msgid` 字段，但会翻译 `msgstr` 行。

谈到翻译键，主要有两个「学派」：

1、`msgid` 作为真实的句子，主要的优点是：

* 如果软件中有未翻译成任何给定语言的部分，则显示的键仍将保留某些含义。
举个例子：如果您碰巧背诵了英语到西班牙语的翻译，但需要帮助将其翻译为法语，
可能你的的新页面上就会缺少法语的句子，网站的部分内容将以英语显示；
* 译者更容易理解正在发生的事情，并根据 `msgid` 进行适当的翻译；
* 它为你的其中一种语言提供了「免费的」l10n —— 源语言；
* 唯一的缺点： 如果需要更改实际文本，则需要在多个语言文件中替换相同的 `msgid`。
        
2、`msgid` 作为唯一的、结构化的键。 它将以结构化的方式描述应用程序中的句子角色，包括字符串所在的模板或部分，而不是其内容。

* 这是组织代码、将文本内容与模板逻辑分离的好方法。
* 但是，这可能会给翻译人员带来问题，使其无法理解上下文。需要一个源语言文件作为其他翻译的基础。
举个例子，开发人员最好有一个 `en.po` 文件，翻译人员可以阅读该文件，以了解在 `fr.po` 中要写什么。
* 缺少的翻译内容会导致在屏幕上显示无用的键名 （会在未翻译的页面上显示 `top_menu.welcome` 而不是 `用户，您好！`）。
这是个好事，这样会迫使翻译在页面发布之前完成 —— 然而，随着翻译的糟糕，会导致页面会糟糕得更明显。
但是某些库包含一个选项，可以为给定语言指定为「回退」，其具有与其他方法类似的行为。

Gettext 文档 中更倾向于使用第一种方法，因为通常情况下，翻译人员和用户在遇到问题时更容易使用。 
这也是我们在这里所采用的方式。但是，Symfony 文档 则更倾向于使用基于关键字的翻译，
以便在不影响模板的情况下对所有翻译进行独立更改。

##### 日常应用

在一个典型的应用中，编写页面静态文件时，你可能会用上一些 Gettext 函数。
那些语句将出现在`.po` 文件中，被翻译、编译成 `.mo` 文件，然后由 Gettext 呈现在实际的界面中。
有鉴于此，让我们用一个循序渐进的例子将我们目前讨论的内容联系起来:

**1、一个示例模板文件，包括一些不同的 gettext 调用**

```php
<?php include 'i18n_setup.php' ?>
<div id="header">
    <h1><?=sprintf(gettext('Welcome, %s!'), $name)?></h1>
    <!-- code indented this way only for legibility -->
    <?php if ($unread): ?>
        <h2><?=sprintf(
            ngettext('Only one unread message',
                     '%d unread messages',
                     $unread),
            $unread)?>
        </h2>
    <?php endif ?>
</div>

<h1><?=gettext('Introduction')?></h1>
<p><?=gettext('We\'re now translating some strings')?></p>
```

* `gettext()` 将指定语言的 `msgid` 简单翻译成相应的 `msgstr`。它还有一个简写形式 `_()`。
* `ngettext()` 作用同上，但它有复数规则；
* 还有两个函数， `dgettext()` 和 `dngettext()`，它们允许你为单个调用覆盖域。在下一个示例中我们将介绍有关域配置的更多信息。

**2、安装文件示例（上面用到的 `i18n_setup.php`），选择正确的区域设置并配置 Gettext**

```php
<?php
/**
 * 校验项目中是否支持给定的 $locale
 * @param string $locale
 * @return bool
 */
function valid($locale) {
   return in_array($locale, ['en_US', 'en', 'pt_BR', 'pt', 'es_ES', 'es']);
}

// 设置 初始/默认 区域，以供参考
$lang = 'en_US';

if (isset($_GET['lang']) && valid($_GET['lang'])) {
    // 可以通过查询字符串更改区域设置
    $lang = $_GET['lang'];    // 这里应该做个转换过滤！
    setcookie('lang', $lang); // 将其存储在 cookie 中，后续可以重复使用
} elseif (isset($_COOKIE['lang']) && valid($_COOKIE['lang'])) {
    // 如果已经有 cookie，继续保留它即可
    $lang = $_COOKIE['lang']; // 这里应该做个转换过滤！
} elseif (isset($_SERVER['HTTP_ACCEPT_LANGUAGE'])) {
    // 默认设置：通过浏览器查找用户使用的语言
    $langs = explode(',', $_SERVER['HTTP_ACCEPT_LANGUAGE']);
    array_walk($langs, function (&$lang) { $lang = strtr(strtok($lang, ';'), ['-' => '_']); });
    foreach ($langs as $browser_lang) {
        if (valid($browser_lang)) {
            $lang = $browser_lang;
            break;
        }
    }
}

// 在这里，我们根据找到的语言定义全局系统语言环境
putenv("LANG=$lang");

// 这对于日期函数（LC_TIME）或货币格式（LC_MONETARY）可能很有用
setlocale(LC_ALL, $lang);

// 这会使 Gettext 查找 ../locales/<lang>/LC_MESSAGES/main.mo 文件
bindtextdomain('main', '../locales');

// 设置应以何种编码读取文件
bind_textdomain_codeset('main', 'UTF-8');

// 如前所述，如果你的应用程序还有其他域，应该在此处把它们也做个绑定
bindtextdomain('forum', '../locales');
bind_textdomain_codeset('forum', 'UTF-8');

// 这里我们设置调用 gettext() 默认响应的域
textdomain('main');

// 这将在 forum.mo 中查找字符串，而不是 main.mo 
// echo dgettext('forum', 'Welcome back!');
?>
```

**3、准备第一次运行翻译**

与定制框架 i18n 包相比，其广泛而强大的文件格式是 Gettext 的一个巨大优势。
「兄弟，这又难改又难理解，一个简单的数组会比这更容易！」毫无疑问，像 Poedit 这样的应用程序可以提供很多帮助。
你可以从他们的网站 下载到，它是免费的，并且适用于所有平台。
这是一个非常好用也非常强大的工具，提供了 Gettext 所具备的所有功能。本文基于 PoEdit 1.8。

第一次运行时，你需要从菜单栏选择 「File > New…」。你将被直接询问语言：
在这里，您可以选择 / 过滤您想要翻译的语言，或者使用我们前面提到的格式，例如 `en_US` 或 `pt\u BR`。

现在，使用我们提到过的目录结构保存文件。然后你应该点击「Extract from sources（从源文件中提取）」，
在这里你将为提取和翻译活动配置各种配置项。稍后您可以通过「Catalog>Properties」找到所有这些文件夹：

* 源路径：这里必须包括调用 `gettext()`（和同级）的项目中的所有文件夹 —— 这通常是您的模板 / 视图文件夹。这是唯一的强制性设置。

* 翻译属性：

    项目名和版本，团队名和团队的电子邮件地址：将被放在 .po 文件头部的有用的信息。
    复数形式：下面是我们前面提到的规则 —— 其中也有示例的链接。您可以在大多数情况下保留默认选项，因为 PoEdit 已经包含了一个包含多种语言的多种规则的便捷数据库。
    字符集：最好是 UTF-8;
    源代码字符集：使用你代码库的字符集 —— 可能也是 UTF-8

* 源代码关键词：底层软件知道在几种编程语言中如何使用 `gettext()` 和类似的函数调用，但您也可以创建自己的翻译函数。
在这里，您将添加其他方法。这将在后面的「提示」部分讨论。

完成这些设置后，它将扫描源文件以查找所有本地化调用。每次扫描结束后 Poedit 都会显示一个源文件内容变动的摘要，
例如哪些是新增的文件，哪些文件被移除了。新条目将向翻译表中填入空，你需要填入这些字符串的本地化版本。
保存文件，然后一个.mo 文件将重新编译成同一文件夹。这样一来，您的项目就是国际化的了。

**4、翻译字符串**

可能你已经注意到了，主要有两种本地化字符串：简单的类型和带有复数形式的。第一个有两个部分：源和本地化的字符串。
无法修改源字符串，因为 GetText/Poedit 不能更改源文件 —— 您应该更改源本身并重新扫描文件。
提示：您可以右键单击一个翻译行，它将提示您使用正在使用该字符串的源文件和行。
另一方面，复数形式字符串包括两个部分以显示两个源字符串和选项卡，因此您可以配置不同的最终形式。

每当您更改源并需要更新翻译时，只需点击「Refresh 刷新」，Poedit 就会重新扫描代码，
删除不存在的条目，合并更改的条目，并添加新的条目。它还可能尝试根据您所做的其他翻译来猜测一些翻译。
这些猜测和更改的条目将标记为「模糊」，表明它需要审查，并在列表中显示为金色。
如果您有一个翻译团队，并且有人试图写一些他们不确定的内容，这也很有用：只需标记模糊，其他人稍后会审阅。

最后，建议勾选「View > Untranslated entries first」，它有助于你避免忘记一些条目。
从该菜单中，您还可以打开用户界面，允许您在需要时留下翻译器者上下文信息。

##### 提示和技巧

**可能存在的缓存问题**

如果你的 PHP 正在作为 Apache 的一个模块（`mod_php`）运行，你可能会面临 `.mo` 文件已缓存的问题。
这个问题出现在该第一次被读取，然后要更新它的时候，这时你可能需要重新启动服务器。
在 nginx 和 PHP 5 上，通常只需要刷新几个页面来刷新翻译缓存。在 PHP 7 上几乎不需要了。

**添加辅助函数**

很多人都选择使用 `_()` 而不是 `gettext()`。很多定制的 i18n 库也开始使用类似于 `t()` 的函数来使翻译代码更加简短。
然而，这是唯一一个有快捷方式的函数。你可能想给项目中添加其他的函数，像是 `__()` 或 `_n()` ，
用作 `ngettext()`，或者是将一个花里胡哨的 `_r()` 加入 `gettext()` 和 `sprintf()` 的调用中。
其他像 oscarotero’s Gettext 的库中也提供了类似这样的辅助函数。

在这些情况下，您需要告诉 Gettext 如何从这些新函数中提取字符串。不要担心，这很容易。
它只是`.po` 文件中的一个字段，或者 Poedit 上的一个设置窗口。在编辑器中，该选项位于「目录 > 属性 > 源关键字」内。
记住：Gettext 已经知道许多语言的默认函数，所以如果该列表看起来是空的，也无需担心。
你需要在那里遵循 特定格式 引入这些新函数的规范。

* 如果你创建了像 `t()` 这样的函数，将翻译结果作为字符串返回，你可以将它指定为 `t`。
Gettext 会知道唯一的函数参数就是要翻译的字符串。

* 如果函数具有大于一个的参数，你可以指定哪个是第一个字符串，如果有需要，还可以指定复数形式。
例如我们像这样调用函数：`__('one user', '%d users', $number)`，规则就要写作 `__:1,2`，意思是第一个形式是第第一个参数，
第二个形式是第二个参数。如果你的第一个参数是数字，那么规则就是 `__:2,3`，表示第一个形式是第二个参数，等等。

在 `.po` 文件包含了这些新的规则之后，新的扫描将像以前一样轻松引入新的字符串。

##### 参考文献

    Wikipedia: i18n and l10n
    Wikipedia: Gettext
    LingoHub: PHP internationalization with gettext tutorial
    PHP Manual: Gettext
    Gettext Manual

### 依赖注入

来自 Wikipedia:

    依赖注入是这样一种软件设计模式，它允许你避免硬编码依赖项，并可以在运行或者编译时修改这些依赖项。

这个引用让这个概念比它实际更难理解了。

依赖注入通过构造器注入、调用方法、设置属性来提供组件。就是这么简单。

#### 基础概念

我们可以通过一个简单、朴实的例子来展示这个概念。

这里我们有一个 Database 类， 它需要一个适配器来连接数据库。我们在这个类的构造方法中通过硬编码实例化了适配器。
这样使得测试变得困难，同样意味着 Database 这个类和它的适配器强耦合。

```php
<?php
namespace Database;

class Database
{
    protected $adapter;

    public function __construct()
    {
        $this->adapter = new MySqlAdapter;
    }
}

class MysqlAdapter {}
```

这段代码可以改成使用依赖注入来摆脱这种依赖。

```php
<?php
namespace Database;

class Database
{
    protected $adapter;

    public function __construct(MySqlAdapter $adapter)
    {
        $this->adapter = $adapter;
    }
}

class MysqlAdapter {}
```

现在我们可以传递给 Database 它的依赖项，而不是让它自己创建。
我们甚至可以创建一个方法，用参数的形式将依赖项传递给它，或者设置 $adapter 为公共属性，这样我们可以直接设置它。

#### 复杂的问题

假如你了解过依赖注入，那么你大概率看到过 “控制反转” 或者 “依赖反转原则”。 这些复杂的问题都是通过依赖注入解决的。

##### 控制反转

控制反转正如字面意思，将对一个系统的组织控制反转过来，完全和我们的对象剥离开。
就依赖注入而言，这意味着在系统其它地方控制和实例化依赖项来减少耦合。

多年来，PHP 框架一直在致力于实现依赖反转。然而，问题变成了，我们需要反转哪一部分的控制，以及在哪里做控制？
例如，MVC 框架通常会提供一个超级对象或者基类控制器，由其他控制器继承并获得访问权限。
这种控制反转，其实是简单移开了依赖，而不是解耦依赖。

依赖注入允许我们优雅地注入依赖当且仅当我们需要它们时，而且不需要硬编码这些依赖项。

##### S.O.L.I.D.

**单一功能原则**

单一功能原则是关于动作和高层次架构的。它被描述为 “一个类应当只有一个原因被改变”。
这意味着每个类应当 仅 负责软件提供的一个功能。这样做的最大好处是提升了代码的 可复用性 。
通过把我们的类设计成只做一件事，我们可以使用（或者复用）它在其他程序中而不需要修改它。

**开闭原则**

开闭原则是关于类的设计和功能扩展的。它被描述为 “软件实例（类、模块、函数等等）应该对扩展开放，
但是拒绝修改”。这意味着我们应该这样设计我们的模块、类或者函数，当新功能被提出时，
我们不应该修改已有的代码，而是写新的代码给已有的代码使用。
实际就是说，我们应该写那些实现并遵守了接口的类，然后引用接口而不是特定的类。

这样做的最大好处是我们可以容易的扩展我们的代码来支持新的功能而不需要修改已经存在的代码。
这意味着我们可以减少提问回答的时间，而且应用的负面影响的风险也实质上降低了。
我们可以更快地，更自信地部署我们的代码。

**里氏替换原则**

里氏替换原则是关于子类和继承的。它被描述为 “子类永远不应该破坏父类的定义”，
或者用 Robert C. Martin 的话说，“子类必须可以被父类替代”

例如，我们有个 FileInterface 接口定义了 embed() 方法，并且有 Audio 和 Video 两个类都实现了 FileInterface 接口，
那么我们可以预期 embed() 总是按我们期待的那样做。
假如我们稍后又创建了实现了 FileInterface 接口的一个 PDF 和 一个 Gist 类，我们也会明白 embed() 方法是干什么的。
这么做最大的好处是我们可以建立灵活且易配置的程序，因为当我们改变了一个对象的类型（比如 FileInterface ）为另一个，
我们不需要修改程序里的其它任何地方。

**接口隔离原则**

接口隔离原则 (ISP) 是关于 业务逻辑到客户 的沟通的。它被描述为 “客户不应被迫使用对其而言无用的方法或功能”。
这意味着不应该用一个单一整体的接口适应所有符合的类，而是应该提供一系列更小的、概念明确的接口给符合的类实现其中的一个或多个。

例如，Car 或 Bus 类对 steeringWheel() 方法感兴趣，而 Motorcycle 或 Tricycle 类则对其不感兴趣。
相反，Motorcycle 或 Tricycle 类对 handlebars() 方法感兴趣，而 Car 或 Bus 类则对其不感兴趣。
没必要让不同类型的车同时实现对 steeringWheel() 和 handlebars() 的支持，我们应该分离源接口。

**依赖倒置原则**

依赖倒置原则用于解除离散的类之间的硬链接，通过传递不同的类来提供不同的新功能。
它倡导应该 “依赖抽象，而不是依赖具体实现。”。简单地说，这意味着依赖关系应该是接口 / 约定或抽象类，而不是具体的实现。
我们可以很简单地重构上述示例以遵循这一原则。

```php
<?php
namespace Database;

class Database
{
    protected $adapter;

    public function __construct(AdapterInterface $adapter)
    {
        $this->adapter = $adapter;
    }
}

interface AdapterInterface {}

class MysqlAdapter implements AdapterInterface {}
```

现在 `Database` 类依赖于接口，相比依赖于具体实现有更多的优势。

假设我们现在进行团队开发，一位同事负责设计开发适配器。在第一个例子中，我们必须等待适配器完成后，
才能正确的模拟它进行单元测试。现在由于依赖的是一个接口 / 约定，我们能轻松地模拟接口测试，
因为我们知道同事会基于约定实现那个适配器

这种方法一个更大的好处是，代码的扩展性变得更高。如果一年后我们决定迁移到另一种数据库，
我们只需要编写一个实现相应接口的适配器并且注入进去。由于适配器遵循接口的约定，我们无须再进行额外的重构。

#### 容器

你应该明白的第一件事是，依赖注入容器和依赖注入是不相同的概念。容器是帮助我们更方便地实现依赖注入的工具，
但是它们通常被误用来实现反模式设计：服务定位（Service Location）。
把依赖注入容器作为服务定位器（ Service Locator ）注入进类中，对容器的依赖性比你原想要替换的依赖性更强，
而且还会让你的代码变得更不透明，最终更难进行测试。

大多数现代的框架都有自己的依赖注入容器，允许你通过配置将依赖绑定在一起。
这实际上意味着，你可以写出和框架层同样干净、解耦的应用程序代码。

#### 延伸阅读

* 依赖注入是什么？
* 依赖注入：一个形象的类比
* 依赖注入：嗯？
* 依赖注入作为测试工具

### 数据库 

许多时候你的 PHP 程序都需要使用数据库来长久地保存数据。这时你有几种不同的选择来连接数据库并与进行交互。
在 PHP 5.1.0 之前，我们推荐的方式是使用例如 mysqli，pgsql，mssql 等原生驱动。

在只使用 一个 数据库的情况下，原生驱动是不错的方式，但如果你同时使用了 MySQL 和一点点 MSSQL，
或者你需要使用 Oracle 的数据库，那你就不能只使用一个数据库驱动了。
你需要为每个数据库学习各自不同的 API — 这样做显然不科学。

#### MySQL 扩展

PHP 的 mysql 扩展非常古老，已被其他两个扩展取代：
* mysqli
* pdo

PHP 中的 mysql 扩展已经不再进行新的开发了，在 PHP 5.5.0 版本中正式标记为废弃，并在 7.0 正式被移除 。

想要辨别是否使用了 mysql ，你不需要到 php.ini 去查看。只需要使用编辑器打开你的项目，全局搜索 mysql_* ，
如果有类似 `mysql_connect()` 或者 `mysql_query()` 方法出现，那么说明使用了 mysql。

即使你现在还没有使用 PHP 7.x，但最好是考虑使用 mysqli 或 PDO 替换 mysql 的使用，这样当以后需要升级的时候，才不会一时间焦头烂额。

如果你是从 mysql 升级到 mysqli，请尽量不要使用全局替换 `mysql_*` 为 `mysqli_*`，这不仅是一种过度简化，
同时还忽略了 mysqli 提供的优秀特性，例如数据参数绑定，而 PDO 也一样提供了参数绑定功能。

    MySQLi 预处理语句
    PHP：选择 MySQL 的 API

#### PDO 扩展

PDO 是一个数据库连接抽象库，自 5.1.0 版本以来就内置于 PHP 中，它提供了一个通用接口与多种不同的数据库进行交互。
例如你可以使用基本相同的代码来连接 MySQL 或 SQLite ：
```php
<?php
// PDO + MySQL
$pdo = new PDO('mysql:host=example.com;dbname=database', 'user', 'password');
$statement = $pdo->query("SELECT some_field FROM some_table");
$row = $statement->fetch(PDO::FETCH_ASSOC);
echo htmlentities($row['some_field']);

// PDO + SQLite
$pdo = new PDO('sqlite:/path/db/foo.sqlite');
$statement = $pdo->query("SELECT some_field FROM some_table");
$row = $statement->fetch(PDO::FETCH_ASSOC);
echo htmlentities($row['some_field']);
```

PDO 不会对 SQL 请求进行转换，或模拟实现缺少的功能特性。它只是单纯地使用相同的 API 连接到不同类型的数据库。

更重要的是，PDO 允许你将外部输入（例如 ID）安全地插入到 SQL 查询中，而不必担心数据库 SQL 注入攻击。
这可以通过使用 PDO 语句和绑定参数来实现。

假设我们传入一个 id 作为查询参数。该 id 用于从数据中获取用户记录。这种使用方式是错误的：
```php
<?php
$pdo = new PDO('sqlite:/path/db/users.db');
$pdo->query("SELECT name FROM users WHERE id = " . $_GET['id']); // <-- 不可以这样操作！
```

这样操作是及其危险的！ 将原始参数直接插入 SQL 语句中， 这会造成 SQL 注入的风险。 
如果黑客通过调用 URL（如 `domain.com/?id=1%3BDELETE+FROM+user...` ）传入一个修改过的的 id 参数。
使用 `$_GET['id']` 获取到的参数为 `1;DELETE FROM users` 将会删除所有用户！ 相反，应该使用 PDO 绑定参数
```php
<?php
$pdo = new PDO('sqlite:/path/db/users.db');
$stmt = $pdo->prepare('SELECT name FROM users WHERE id = :id');
$id = filter_input(INPUT_GET, 'id', FILTER_SANITIZE_NUMBER_INT); // <-- 过滤传入数据（请参考 PHP 过滤器）, 对于INSERT、UPDATE等操作尤为重要。
$stmt->bindParam(':id', $id, PDO::PARAM_INT); // <-- 通过PDO自动过滤SQL
$stmt->execute();
```

这是正确的操作方式。它在 PDO 语句上使用绑定参数。这会将外来输入的 id 在引入数据库之前对其进行转义，防止潜在的 SQL 注入攻击。

对于写入操作，例如 INSERT 或者 UPDATE 等，仍然需要先使用过滤对其进行清理， 
以便进行其他操作 (移除 Html 标签、javascript 等)。 PDO 只会针对 SQL 进行清理，而不会针对程序进行清理。

    了解 PDO

您还应该知道，数据库连接会消耗资源，并且在没有隐式关闭连接的情况下，有可能会造成可用资源枯竭的情况。
不过这通常在其他语言中更为常见一些。使用 PDO 您可以通过销毁（destroy）对象，也就是将值设为 NULL，
来隐式地关闭这些连接，确保所有剩余的引用对象的连接都被删除。如果您不显式地这样做，
PHP 会在您的脚本结束时自动关闭连接 —— 当然，除非您使用的是持久连接。

    了解 PDO 连接

#### 数据库交互

当开发者第一次接触 PHP 时，他们经常会把数据库交互和表示逻辑混在一起，使用的代码可能是这样的：
```php
<ul>
<?php
foreach ($db->query('SELECT * FROM table') as $row) {
    echo "<li>".$row['field1']." - ".$row['field1']."</li>";
}
?>
</ul>
```

这从很多方面来看都是错误的做法，主要是由于它不易阅读又难以测试和调试。而且如果你不加以限制的话，它会输出非常多的字段。

虽然有很多其他的解决方案可以实现这一点 — 取决于您倾向于 面向对象编程（OOP）还是 函数式编程 — 但必须有一些分离的元素。

来看一下最基本的做法：
```php
<?php
function getAllFoos($db) {
    return $db->query('SELECT * FROM table');
}

$results = getAllFoos($db);
foreach ($results as $row) {
    echo "<li>".$row['field1']." - ".$row['field1']."</li>"; // BAD!!
}
```

这是一个良好的开端。将这两个元素放入了两个不同的文件中，您就得到了一些清晰的分离。

创建一个类来放置上面的函数，您就得到了一个「Model」。创建一个简单的.php 文件来存放表示逻辑，您就得到了一个「View」。
这已经很接近 MVC — 一个大多数框架常用的面向对象的架构。

foo.php
```php
<?php
$db = new PDO('mysql:host=localhost;dbname=testdb;charset=utf8mb4', 'username', 'password');

// 使您的模型可用
include 'models/FooModel.php';

// 创建一个实例
$fooModel = new FooModel($db);
// 获取 foo 列表
$fooList = $fooModel->getAllFoos();

// 显示视图
include 'views/foo-list.php';
```

models/FooModel.php
```php
<?php
class FooModel
{
    protected $db;

    public function __construct(PDO $db)
    {
        $this->db = $db;
    }

    public function getAllFoos() {
        return $this->db->query('SELECT * FROM table');
    }
}
```

views/foo-list.php
```php
<?php foreach ($fooList as $row): ?>
    <li><?= $row['field1'] ?> - <?= $row['field1'] ?></li>
<?php endforeach ?>
```

这在本质上与大多数现代框架所做的工作相同，只是稍微多了一些手工操作。
您可能不需要每次都完全这么做，但是如果您想对应用程序进行单元测试，
那么将太多的表示逻辑和数据库交互掺杂在一起可能会带来一系列问题。

#### 数据库抽象层

许多框架都提供了自己的数据库抽象层，其中一些是设计在 PDO 上层的。
这些抽象层通常将你的请求在 PHP 方法中包装起来，通过模拟的方式来使你的数据库拥有一些之前不支持的功能。
这种抽象是真正的数据库抽象，而不单单只是 PDO 提供的数据库连接抽象。
这类抽象的确会增加一定程度的性能开销，但如果你正在设计的应用程序需要同时使用 MySQL、PostgreSQL 和 SQLite 时，
一点点的额外性能开销对于代码整洁度的提高来说还是很值得的。

有一些抽象层是使用 PSR-0 或 PSR-4 命名空间标准构建的，因此可以安装在任何你需要的应用程序中：

    Atlas
    Aura SQL
    Doctrine2 DBAL
    Propel
    Zend-db

### 使用模板

模板提供了一种将控制器和域逻辑与表示逻辑分离的方便方法。模板通常包含应用程序的 HTML，但也可以用于其他格式，
如 XML。模板通常被称为 “视图”，它构成了模型 - 视图 - 控制器 (MVC) 软件体系结构模式的第二个组件的一部分。

**好处**

使用模板的主要好处是它们在表示逻辑和应用程序的其余部分之间创建了明确的分离。模板只负责显示格式化的内容。
它们不负责数据查找、持久化或其他更复杂的任务。这将导致更清晰、更易读的代码，
这对于开发人员处理服务器端代码 (控制器、模型) 和前端人员处理前端代码特别有帮助。

模板还可以改进代码结构。模板通常放在 “views” 文件夹中，每个模板都在一个文件中定义。
这种方法鼓励代码重用，即将较大的代码块拆分成较小的、可重用的部分，通常称为分区。
例如，可以将网站页眉和页脚分别定义为模板，然后在每个页面模板之前和之后分别包含模板。

最后，根据你使用的库，模板可以通过自动转义用户生成内容来提供更多的安全性。
有些库甚至提供沙盒 (sand-boxing) ，模板设计者只能访问白名单中的变量和函数。

**普通 PHP 模板**

普通 PHP 模板只是使用原生 PHP 代码的模板。它们是一个自然的选择，因为 PHP 实际上是一种模板语言本身。
这意味着您可以在其他代码中组合 PHP 代码，比如 HTML。这对 PHP 开发人员是有益的，因为没有新的语法需要学习，
他们知道可用的函数，他们的代码编辑器已经有了 PHP 语法突显和自动完成内置。
此外，普通 PHP 模板往往非常快，因为不需要编译阶段。

现代 PHP 框架都会使用某种模板系统，其中大多数使用原生的 PHP 语法。
在框架之外，像 Plates 或 Aura.View 这些类库提供了继承、布局和扩展等现代模板功能，使原生 PHP 模板变得更易用。

**原生 PHP 模板简单示例**

**使用 Plates 类库**

![]({{site.baseurl}}/images/20220502/20220502143844.png)

**原生 PHP 模板使用继承的示例**

使用 Plates 类库

![]({{site.baseurl}}/images/20220502/20220502143904.png)

**编译模板**

尽管 PHP 已经发展成为一种成熟的面向对象语言，但它作为模板语言 没有太大改进。
编译模板，如 Twig，Brainy，或 Smarty【注】，提供了模板专用的新语法填补这一空白。
从自动转义到继承以及简化的控制结构，编译模板设计为更易于编写、更清晰易读和使用更安全。
编译模板甚至可以跨不同语言共享，Mustache 就是一个很好的例子。由于这些模板必须编译，
因此会对性能造成轻微的影响，但是在适当地使用缓存后影响就非常小了。

【注】：虽然 Smarty 提供了自动转义功能，不过默认情况下是关闭的

**编译模板简单示例**

使用 Twig 类库。

![]({{site.baseurl}}/images/20220502/20220502143917.png)

**使用继承的编译模板示例**

使用 Twig 类库。

![]({{site.baseurl}}/images/20220502/20220502143937.png)

**延伸阅读**

文章与教程

    PHP 的模板引擎
    CodeIgniter 中的视图和模板简介
    PHP 模板入门
    在 PHP 中运行自己的模板系统
    母版页（Master Pages）
    在 Symfony 2 中使用模板
    编写更安全的模板

类库

    Aura.View (原生)
    Blade (编译，特定框架)
    Brainy (编译)
    Dwoo (编译)
    Latte (编译)
    Mustache (编译)
    PHPTAL (编译)
    Plates (原生)
    Smarty (编译)
    Twig (编译)
    Zend\View (原生，特定框架)

### 错误与异常 

#### 错误

在很多「重异常（exception-heavy）」编程语言中，无论出现什么问题，都会抛出异常。
这确实是一种可行的方法，但是 PHP 是「轻异常」的编程语言。虽然确实存在异常，
并且很多核心在使用对象的时候已经开始使用这些异常了，但 PHP 更多的时候会尝试保持运行，除非发生了致命错误。

举个例子：
```
$ php -a
php > echo $foo;
Notice: Undefined variable: foo in php shell code on line 1
```

这只是一个提醒级别的错误，PHP 会继续运行。这会让那些来自「重异常」语言的开发者很困惑，
因为例如在 Python 中，引用一个不存在的变量将抛出异常：
```
$ python
>>> print foo
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'foo' is not defined
```

唯一的区别是 Python 会在任何一件小事上终止程序，所以开发人员可以十分确认任何问题或边界值问题都会被捕获，
而相同的情况 PHP 会继续进行。除非是发生极端问题，PHP 才会抛出错误并报告问题。

**错误级别**

PHP 有多个错误级别。三个最常见的错误级别是：错误、提醒、警告。这些错误的级别不同，`E_ERROR`、`E_NOTICE` 和 `E_WARNING`。
「错误」 是运行时的致命错误，通常是代码错误导致的。由于会导致程序停止，所以需要及时修正。
「提醒」 是因为代码而出现的建议消息，在执行期间不一定会导致问题，程序运行也不会停止。
「警告」 是非致命错误，不会导致程序停止。

另一种错误信息是在编译时报告的 `E_STRICT`。这些消息用于确保与即将发布的新版本 PHP 的兼容性，对代码的更改建议。

**更改 PHP 的错误报告行为**

错误报告可以通过 PHP 配置或 PHP 函数来更改。你可以在脚本执行期间，使用内置的 PHP 函数 `error_reporting()`，
传入一个表示错误级别的预定义常量，来设置错误报告级别。例如你只想要看到错误和警告，不想看到提醒，你可以这样设置：

```
<?php
error_reporting(E_ERROR | E_WARNING);
```

你还可以控制错误信息是否显示在屏幕上（适用于开发环境）或者是记录到日志（适用于生产环境）。
这方面的更多信息你可以查看 错误报告 部分（原文）。

**行内错误抑制**

你可以使用 `@` 符号来告诉 PHP 抑制特定错误。你可以将此操作符放在表达式的开头，表达式导致的任何错误都将被抑制。
```
<?php
echo @$foo['bar'];
```

这行代码将会在值存在的时候输出 `$foo['bar']`，但是当 $foo 或 bar 这个键不存在时，这句表达式会返回空值并且不输出任何内容。
如果没有错误控制运算符，这个表达式会导致出现 `PHP Notice: Undefined variable: foo` 通知，
或是 `PHP Notice: Undefined index: bar` 错误。

这似乎看上去不错，但也有一些不可取。PHP 处理带 `@` 符号的表达式时性能会收到影响。
过早的性能优化可能在所有编程语言中都是争议点，但如果性能表现在你的应用 / 库中至关重要，那么理解错误抑制符对性能的影响就很重要了。

其次，错误控制操作符会完全吃掉错误。不但没有显示，而且也不会记录在错误日志中。
此外，在正式环境中 PHP 也没有办法关闭错误控制操作符。也许你认为那些错误是无害的，不过那些较具伤害性的错误同时也会被隐藏。

如果有方法可以避免错误抑制符，你应该考虑避免使用。举个例子，上面的程序代码可以这样重写：
```
<?php
// 使用双问号运算符
echo $foo['bar'] ?? '';
```

当 `fopen()` 载入文件失败时，也许是一个使用错误抑制符的合理例子。你可以在尝试载入文件前检查是否存在，
但是如果这个文件在检查后才被删除，而此时 `fopen()` 还未执行 （听起来有点不太可能，但是确实会发生），
这时 `fopen()` 会返回 false 并且 抛出操作。这也许应该由 PHP 本身来解决，但这时一个错误抑制符才能有效解决的例子。

前面我们提到在正式的 PHP 环境中没有办法关闭错误控制操作符。但是 Xdebug 有一个 `xdebug.scream` 的 ini 配置项，
可以关闭错误控制操作符。你可以按照下面的方式修改 `php.ini`。
```
xdebug.scream = On
```

你也可以在运行时使用 `ini_set` 来设置。
```
<?php
ini_set('xdebug.scream', '1')
```

「Scream」这个 PHP 扩展提供了和 xDebug 类似的功能，只是 Scream 的 ini 设置项叫做 `scream.enabled` 。

当你在调试代码而错误信息被隐藏时，这是最有用的方法。请务必小心使用 scream ，仅把它当作暂时性的调试工具。
有许多的 PHP 函数类库代码也许无法在错误抑制操作符停用时正常使用。

    Error Control Operators
    SitePoint
    Xdebug
    Scream

**错误异常类**

PHP 可以完美化身为「重异常」的程序语言，只需要几行代码就能切换过去。
基本上你可以利用 `ErrorException` 类抛出「错误」来当做「异常」，这个类是继承自 `Exception` 类。

这在大量的现代框架中是一个常见的做法，比如 Symfony 和 Laravel。如果开启调试模式，
或者进入开发环境的话，这两个框架都会将显示美观清晰的 调用栈。

还有一些可用的包可以更好地处理错误异常，像 Whoops! ，它随 Laravel 默认安装，也可以在任何框架中使用。

在开发过程中将错误当作异常抛出可以更好的处理它，如果在开发时发生异常，
你可以将它包在一个 catch 语句中具体说明这种情况如何处理。每捕捉一个异常，都会使你的应用程序越来越健壮。

更多关于如何使用 `ErrorException` 来处理错误的细节，可以参考 ErrorException Class。

* Error Control Operators
* Predefined Constants for Error Handling
* `error_reporting()`
* Reporting

#### 异常

异常是许多流行编程语言的标配，但它们往往被 PHP 开发人员所忽视。像 Ruby 就是一个极度重视异常的语言，无
论有什么错误发生，像是 HTTP 请求失败，或者数据库查询有问题，甚至是图片资源丢失，Ruby （或是所使用的 gems），
都将会抛出异常，你可以通过屏幕立刻知道所发生的问题。

PHP 处理这个问题则比较随意，调用 `file_get_contents()` 函数通常只会给出 `FALSE` 值和警告。
许多较早的 PHP 框架比如 CodeIgniter 只是返回 false，将信息写入专有的日志，
或者让你使用类似 `$this->upload->get_error()` 的方法来查看错误原因。这里的问题在于你必须找出错误所在，
并且通过翻阅文档来查看这个类使用了什么样的错误的方法，而不是明确的暴露错误。

另一个问题发生在当类自动抛出错误到屏幕时会导致程序终止。这样做会妨碍其他开发者动态处理错误。
正确的做法应该是抛出异常，让开发人员意识到错误的存在，让他们可以选择处理的方式，例如：
```php
<?php
$email = new Fuel\Email;
$email->subject('这是邮件主题');
$email->body('你到底要怎样？');
$email->to('guy@example.com', '某个家伙');

try {
    $email->send();
} catch(Fuel\Email\ValidationFailedException $e) {
    // 验证失败
} catch(Fuel\Email\SendingFailedException $e) {
    // 邮件模块无法发送
} finally {
    // 无论抛出什么样的异常都会执行，并且在正常程序继续之前执行
}
```

**SPL 异常**

原生的 `Exception` 类提供给开发人员的调试上下文不多。不过可以通过建立一个特殊的 `Exception` 来弥补它，
方式就是建立一个继承自原生 `Exception` 类的子类：

```
<?php
class ValidationException extends Exception {}
```

这意味着你可以添加多个 catch 块，并根据不同的方式处理不同的异常。这可能会导致创建 过多 自定义异常，
其中有部分已经在 SPL 扩展 中提供的 SPL 异常中定义了。

例如，如果你使用了 `__call()` 魔术方法，并且请求了一个无效的方法，那么你可以直接 `throw new BadMethodCallException;`，
而不是抛出一个模糊的标准 `Exception` ，或者为此创建一个自定义异常。

    关于异常
    关于 SPL 异常
    PHP 中的异常嵌套
    
### 安全

我找到的关于 PHP 安全性的最佳资源是由 Paragon Initiative 公司编写的 2018 年 PHP 系统安全构架指南 。

#### Web 应用程序安全

对于每个 PHP 开发人员来说，学习 web 应用程序安全基础 是非常重要的，其内容可拆分为几个较大的主题：

1、代码与数据分离

* 当数据可以当做代码执行时，你将遭遇 SQL 注入、跨站点脚本、本地 / 远程文件包含漏洞等攻击。
* 当代码允许以数据形式打印时，会出现信息泄漏（例如泄露源代码，或者在 C 程序中，有足够的信息绕过 地址空间布局随机化（ASLR） 机制）

2、应用程序逻辑

* 缺少身份验证或授权控制。
* 输入校验。
        
3、操作环境

* PHP 版本
* 第三方类库
* 操作系统
        
4、加密缺陷

* 脆弱的随机数.
* 选择密文攻击.
* 侧信道信息泄露.

攻击者无时无刻不在准备对你的 Web 应用程序进行攻击，采取必要的预防措施来加强 Web 应用程序的安全性是很重要的。
幸运的是，来自 开放式 Web 应用程序安全项目 (OWASP) 的有心人已经整理了一份包含了已知安全问题和防御方式的详尽的清单。
对于具有安全意识的开发者来说这是必读的资料。
由 Padraic Brady 编写的 生存手册：PHP 安全 也是一份很不错的 PHP web 应用程序安全指南。

    阅读 OWASP 安全指南

#### 密码哈希

每个人最终都会构建一个依赖于用户登录的 PHP 应用。用户名和密码存储在数据库中，稍后用于在登录时对用户进行身份验证。

在存储密码之前，适当地对密码进行 哈希 处理至关重要。哈希和加密是经常混淆的 两个截然不同的。

哈希是不可逆的单向函数。哈希会生成一个固定长度的字符串，该字符串不能回溯为原串。
这意味着你可以比较两个哈希串，以此判断它们是不是来自同一个原串，但无法得到原串。
如果未对密码进行哈希处理，并且数据库允许第三方未授权访问，那么所有的用户账户都面临危险。

与哈希不同，加密是可逆的（前提是你有密钥）。加密在其他领域很有用，但在密码存储上就有些捉襟见肘。

密码也应该分别 加盐，也就是在每个密码哈希处理之前拼接一个随机字符串。
这可以防止使用「彩虹表」（常见密码的哈希反向列表）进行字典攻击。

哈希和加盐非常重要，因为用户经常在多个网站使用相同的密码，而且密码质量很差。

此外，你还应该使用 一个专用的密码哈希算法，而不是使用通用快速的哈希函数（例如 SHA256）。
可以使用的密码哈希算法（截止 2018 年 6 月）：

* Argon2 （PHP 7.2 以及更新的版本中可用）
* Scrypt
* Bcrypt （PHP 提供了此算法，见下文）
* 带有 HMAC-SHA256 或 HMAC-SHA512 的 PBKDF2

幸运的是，现在 PHP 将这些都变得更简单了。

**使用 password_hash 来哈希密码**

`password_hash` 函数在 PHP 5.5 时被引入。 此函数现在使用的是目前 PHP 所支持的最强大的加密算法 BCrypt 。 
当然，此函数未来会支持更多的加密算法。 `password_compat` 库的出现是为了提供对 PHP >= 5.3.7 版本的支持。

在下面例子中，我们哈希一个字符串，然后和新的哈希值对比。
因为我们使用的两个字符串是不同的（'secret-password' 与 'bad-password'），所以登录失败。

```
<?php
require 'password.php';

$passwordHash = password_hash('secret-password', PASSWORD_DEFAULT);

if (password_verify('bad-password', $passwordHash)) {
    // 密码正确
} else {
    // 密码错误
}
```

`password_hash()` 已经帮你进行了加盐处理。加进去的随机子串通过加密算法自动保存着，成为哈希的一部分。
`password_verify()` 会从中提取随机子串，所以你不必使用另一个数据库来记录这些随机子串。

* 了解 `password_hash()`
* `password_compat` for PHP >= 5.3.7 && < 5.5
* 了解密码学中的哈希
* 了解「盐」
* PHP `password_hash()` RFC

#### 数据过滤

永远不要信任外部输入。请在使用外部输入前进行过滤和验证。
`filter_var()` 和 `filter_input()` 函数可以过滤文本并对格式进行校验（例如 email 地址）。

外部输入可以是任何东西：`$_GET` 和 `$_POST` 等表单输入数据、`$_SERVER` 超全局变量中的某些值、
还有通过 `fopen('php://input', 'r')` 得到的 HTTP 请求体。记住，外部输入的定义并不局限于用户通过表单提交的数据。
上传和下载的文档，session 值，cookie 数据，还有来自第三方 web 服务的数据，这些都是外部输入。

虽然外部输入可以被存储、组合并在以后继续使用，但它依旧是外部输入。每次你处理、输出、连结或在代码中包含时，
请提醒自己检查数据是否已经安全地完成了过滤。

数据可以根据不同的目的进行不同的 过滤 。比如，当未经过滤的外部输入被传入到了 HTML 页面的输出当中，
它可以在你的站点上执行 HTML 和 JavaScript 脚本！这属于跨站脚本攻击（XSS），是一种很有杀伤力的攻击方式。
一种避免 XSS 攻击的方法是在输出到页面前对所有用户生成的数据进行清理，
使用 `strip_tags()` 函数来去除 HTML 标签或者使用 `htmlentities()` 
或是 `htmlspecialchars()` 函数来对特殊字符分别进行转义从而得到各自的 HTML 实体。

另一个例子是传入能够在命令行中执行的选项。这是非常危险的（同时也是一个不好的做法），
但是你可以使用自带的 `escapeshellarg()` 函数来过滤执行命令的参数。

最后的一个例子是接受外部输入来从文件系统中加载文件。这可以通过将文件名修改为文件路径来进行利用。
你需要过滤掉 "`/`", "`../`", `null` 字符，或者其他文件路径的字符，以此来确保不会去加载隐藏、私有或者敏感的文件。

* 了解数据过滤
* 了解 `filter_var`
* 了解 `filter_input`
* 了解如何掌握空字符

**数据清理**

数据清理是指删除（或转义）外部输入中的非法和不安全的字符。

例如，你需要在将外部输入包含在 HTML 中或者插入到原始的 SQL 请求之前对它进行过滤。
当你使用 PDO 中的变量绑定功能时，它会自动为你完成过滤的工作。

有些时候你可能需要允许一些安全的 HTML 标签输入进来并被包含在输出的 HTML 页面中，但这实现起来并不容易。
尽管有一些像 HTML Purifier 的白名单类库为了这个原因而出现，
实际上更多的人通过使用其他更加严格的格式限制方式例如使用 Markdown 或 BBCode 来避免出现问题。

查看 Sanitization Filters

**反序列化 Unserialization**

使用 `unserialize()` 从用户或者其他不可信的渠道中提取数据是非常危险的事情。
这样做会触发恶意实例化对象（包含用户定义的属性），即使对象没有被使用，也会触发运行对象的析构函数。
所以你应该避免从不可信渠道反序列化数据。

如果你必须这样做，请你使用 PHP 7 的 `allowed_classes` 选项来限制反序列化的对象类型。

**有效性验证**

验证是来确保外部输入的是你所想要的内容。比如，你也许需要在处理注册申请时验证 email 地址、手机号码或者年龄等信息的有效性。

查看 Validation Filters

#### 配置文件

当你在为你的应用程序创建配置文件时，最好的选择时参照以下的做法：

* 推荐你将你的配置信息存储在无法被直接读取和上传的位置上。
* 如果你一定要存储配置文件在根目录下，那么请使用 .php 的扩展名来进行命名。
这将可以确保即使脚本被直接访问到，它也不会被以明文的形式输出出来。
* 配置文件中的信息需要被针对性的保护起来，对其进行加密或者设置访问权限。
* 建议不要把敏感信息如密码或者 API 令牌放到版本控制器中。

#### 注册全局变量

注意： `register_globals` 设置在 PHP 5.4.0 中已经被删除，无法再使用了。这仅在任何升级旧版本程序的过程中作为警告显示。

启用后，`register_globals` 配置会在应用程序的全局范围内提供几种类型的变量（包括 `$_POST` 、 `$_GET` 和 `$_REQUEST`）。
这很容易导致安全问题，因为你的应用程序无法有效地判断数据来自哪里。

举个例子：`$_GET['foo']` 可以通过 `$foo` 被访问到，也就是说会覆盖已经定义的变量。

如果你正在使用小于 5.4.0 的 PHP，请确保 `register_globals` 是 关闭的。

    PHP 手册：注册全局变量

#### 错误报告

错误日志在查找应用程序中的问题时十分有用，但它也会将应用程序的结构信息暴露给外部。
为了有效地保护应用程序不受消息输出可能导致的问题的影响，你需要在开发和生产环境下对服务器进行不同的配置。

**开发环境**

想要在开发过程中显示每一个可能出现的问题，这样配置你的 `php.ini`：
```
display_errors = On
display_startup_errors = On
error_reporting = -1
log_errors = On
```

传入 `-1` 会显示所有可能的错误，甚至包括在未来的 PHP 版本中新增加的类型和参数。 
从 PHP 5.4 开始， 使用 `E_ALL` 也是一样的。- php.net

`E_STRICT` 类型的错误级别常量是在 5.3.0 中引入的，然而一开始并没有被包含在 `E_ALL` 中。
直到 5.4.0 开始它才被包含进了 `E_ALL` 。这代表着什么呢？
这意味着，在 5.3 中你需要使用 `-1` 或者 `E_ALL | E_STRICT`，才能显示所有的错误信息。

不同 PHP 版本下如何显示全部错误

* < 5.3 `-1` 或 `E_ALL`
* 5.3 `-1` 或 `E_ALL | E_STRICT`
* 大于 5.3 `-1` 或 `E_ALL`

**生产环境**

要隐藏 生产 环境中的错误信息，请将 `php.ini` 配置为：
```
display_errors = Off
display_startup_errors = Off
error_reporting = E_ALL
log_errors = On
```

在生产环境中使用这些配置时，错误仍会记录到 web 服务器的错误日志中，但不会显示给用户。
更多关于配置的信息，请参考 PHP 手册：

    错误报告
    显示错误
    显示启动错误
    记录错误

### 测试

为 PHP 代码编写自动化测试被认为是一种最佳实践，可以帮助构建良好的应用程序。
自动化测试是一个非常棒的工具，可以确保你的应用程序在进行更改或添加新功能时不会影响现有的功能，因此不应被忽视。

PHP 有几种不同类型的测试工具（或框架），使用了不同的方法 - 它们都试图避免手动测试，
满足大型 QA 团队的需要，确保最近的变更不会破坏既有功能。

#### 测试驱动开发

Wikipedia 上的定义：

测试驱动开发（TDD）是一个软件开发过程，以非常短的开发周期不断迭代：
首先，开发者针对将要实现的功能或者新的方法，编写一个失败的自动化测试用例，
然后生成代码以通过该测试，最后对新代码进行重构达到可接受的标准。
肯特・贝克（Kent Beck）在 2003 年表示，TDD 鼓励简单的设计并激发自信。
他被认为是这项技术的创始人，或者说是 “重新发现者” 。

你可以为应用程序执行的测试有下面这些不同类型：

**单元测试**

单元测试是一种编程方法，用于确保函数、类和方法在整个开发周期中都能按预期工作。
通过检查各个函数和方法的输入输出，可以确保内部逻辑正常执行。通过使用依赖项注入和构建 “模拟” 类和 stubs，
可以验证依赖项是否被正确地使用，提高了测试覆盖率。

当你创建一个类或函数时，需要为它应有的每个行为都创建一个单元测试。
最起码的要求是，如果发送了错误的参数，应该确保它会触发错误，如果发送了有效的参数，你应该确保它是正常生效的。
这将有助于确保在以后的开发周期中对这些类或函数进行更改时，旧功能仍能按预期工作。
唯一可替代方法是在你的 test.php 文件中，使用 `var_dump()` 来测试，但别指望用这种方式来构建应用程序，无论规模大小。

单元测试的另一个用途是开源。如果你可以编写一个输出功能中断（即失败）的测试，然后修复它，
并显示测试通过，那么补丁会更容易被接受。如果你在维护一个接受 pull request 的项目，
那么应该建议将单元测试作为一项要求。

PHPUnit 是业界 PHP 应用程序编写单元测试标准的测试框架，但是也有其他几种可选的框架：

    atoum
    Kahlan
    Peridot
    SimpleTest

**集成测试**

Wikipedia 上的定义：

集成测试（有时称为集成和测试，缩写为 “I&T”）是软件测试中的一个阶段，将各个软件模块组合作为一个整体进行测试。
它处于单元测试之后，验收测试之前。集成测试将经过单元测试的模块作为其输入，将其组合为更大的集合，
运行集成测试用例，输出一个可以进行系统测试的集成系统。

许多相同的测试工具既可以运用到单元测试，也可以运用到集成测试，因为其中很多原理都相同。

**功能性测试**

有时候也被称之为验收测试，功能测试是通过使用工具来生成自动化的测试用例，
而不是单元测试中简单的验证单个模块的正确性和集成测试中验证各个模块间交互的正确性。
这些工具通常会使用真实数据来模拟真实用户的行为。

功能测试的工具

    Selenium
    Mink
    Codeception 是一个全栈的测试框架包括验收性测试工具。
    Storyplayer 是一个全栈的测试框架并且支持随时创建和销毁测试环境。

#### 行为驱动开发

有两种不同的行为驱动开发 (BDD): SpecBDD 和 StoryBDD。 SpecBDD 专注于代码的技术行为，
而 StoryBDD 专注于业务逻辑或功能的行为和互动。这两种 BDD 都有对应的 PHP 框架。

采用 StoryBDD 时，你编写可读的故事来描述应用程序的行为。接着这些故事可以作为应用程序的实际测试案例执行。
Behat 是使用在 PHP 应用程序中的 StoryBDD 框架，它受到 Ruby 的 Cucumber 项目的启发并且实现了 Gherkin DSL 來描述功能的行为。

采用 SpecBDD 时，你编写规格来描述实际的代码应该有什么行为。
你应该描述函数或者方法应该有什么行为，而不是测试函数或者方法。
PHP 提供了 PHPSpec 框架来达到这个目的，这个框架受到了 Ruby 的 RSpec project 项目的启发。

BDD 链接

    Behat, PHP 的 StoryBDD 框架， 受到了 Ruby 的 Cucumber 项目启发；
    PHPSpec, PHP 的 SpecBDD 框架， 受到了 Ruby 的 RSpec 项目启发；
    Codeception 是一个遵循 BDD 准则的全栈测试框架。

#### 其他测试工具

除了个别的测试驱动和行为驱动框架之外，还有一些通用的框架和辅助函数类库，对任何的测试方法都很有用。

测试工具链接

    Selenium 是一种浏览器自动化工具，可以 和 PHPUnit 集成
    Mockery 是一个可以跟 PHPUnit 或者 PHPSpec 集成的 Mock 对象框架
    Prophecy 是个相当有特性，且非常强大灵活的 PHP 对象 mocking 框架。它整合了  PHPSpec 并且可以配合 PHPUnit 一起使用。
    php-mock 是一个生成 MOCK 模拟数据的 PHP 类库。
    Infection 一个 PHP 变异测试 工具，用来帮助你评估测试用例的有效性。

### 服务器与部署 

部署 PHP 应用程序到生产环境中有多种方式。

**平台即服务 (PaaS)**

PaaS 提供了运行 PHP 应用程序所必须的系统环境和网络架构。这意味着几乎不用做什么配置就可以运行 PHP 应用或 PHP 框架。

现在，PaaS 已经成为一种部署、托管和扩展各种规模的 PHP 应用程序的流行方式。
你可以在我们的 资源区 查看 PHP PaaS “平台即服务” 提供商。

**虚拟或专用服务器**

如果你喜欢系统管理员的工作，或者对这方面感兴趣，虚拟或者专用服务器可以让你完全控制自己的生产环境。

**nginx 和 PHP-FPM**

PHP, 通过 PHP 内置的 FastCGI 进程管理器 (FPM), 可以很好的与轻量级的高性能 web 服务器软件 nginx 一起工作。 
Nginx 比 Apache 占用更少内存并且处理更多的并发请求，这对那些没有太多内存的虚拟服务器特别重要。

    查看更多关于 nginx
    查看更多关于 PHP-FPM
    查看关于安全地设置 nginx 和 PHP-FPM

**Apache 和 PHP**

PHP 和 Apache 有很长的共同工作历史. Apache 有很强的可配置性和大量的 模块 可扩展功能。
它是共享主机的常见选择，并且很容易为诸如 WordPress 的 PHP 开源框架进行配置。
可惜，Apache 不能处理非常多的访问请求，而且会比 nginx 消耗更多的资源。

Apache 有多种方式运行 PHP，使用 mode_php5 是 prefork MPM 最简单常见的方式。
虽然它对内存的利用效率并不高，但它是最容易使用的，如果你不想深入研究服务器管理方面，那么这种方式可能是你最佳选择。
需要注意的是如果你使用 mod_php5 ，就必须使用 prefork MPM。

如果你追求更高性能和高稳定性的 Apache ，可以选择与 nginx 类似的 FPM 系统 worker MPM 或者 event MPM，
它们分别使用 mod_fastcgi 和 mod_fcgid。这种方式将显著提高内存效率和速度，但是配置也相对复杂一些。

如果你运行的是 Apache2.4 或更高版本，则可以使用 mod_proxy_fcgi ，可以通过简单的设置就获得出色性能。

    阅读更多 Apache
    阅读更多 Apache 多进程模块
    阅读更多 mod_fastcgi
    阅读更多 mod_fcgid
    阅读更多 mod_proxy_fcgi
    使用 mod_proxy_fcgi 设置 Apache 和 PHP-FPM

**共享主机**

PHP 的流行很大程度上要归功于共享主机。你基本找不到没有安装 PHP 的共享主机，不过最好要用最新的 PHP 版本。 
共享主机是把您和其他开发者的网站一起部署在一台服务器上。这样做的好处是价格便宜。
缺点也很明显，就是你永远不知道其他开发者的程序会给你造成什么影响，还有服务器负载高、安全漏洞等都会对影响你程序的稳定性。 
如果你的项目预算充足，应该尽量避免使用共享主机。

要确保您的共享主机是最新版本的 PHP，可以查看 各个共享主机 PHP 版本.

**构建和部署您的应用程序**

如果你发现每次发布部署新版本都是手动上传文件、手动修改数据库结构以及手动测试程序，请慎重！
手动操作很容易出错。 无论您是做最简单的文件上传、迭代更新或者完整版本的发布，请考虑自动化部署 。

您可能希望自动化的任务包括：

    依赖管理
    编译压缩你的静态资源
    运行测试
    创建文档
    打包
    部署

**部署工具**

部署工具可以看做部署任务中常见的脚本集合。部署工具不是你软件系统的一部分，它一般在外部帮助实现自动化部署。

有很多可用的开源工具可以帮助你自动构建和部署，这些工具中有的是用 PHP 编写的，有的不是。
应该根据你的实际项目来选择最适合的工具，不要让语言阻碍了你使用这些工具。如下是一些例子：

通过 Phing 你可以控制打包，部署或者测试，只需要一个简单的 XML 构建文件。
Phing (基于 Apache Ant) 提供了在安装或者升级 web 应用时的一套丰富的任务脚本，并且可以通过 PHP 编写额外的任务脚本来扩展。
它是一个可靠而健壮的工具，并且已经存在很长一段时间。由于是使用 XML 文件处理配置，有的时候会被认为有一点过时。

Capistrano 是一个为 中高级程序员 准备的系统，以一种结构化、可复用的方式在一台或多台远程机器上执行命令。
对于部署 Ruby on Rails 的应用，它提供了预定义的配置，不过也可以用它来 部署 PHP 应用 。
如果要成功的使用 Capistrano ，需要一定的 Ruby 和 Rake 的知识。

Ansistrano 是一对 Ansible 角色，用于轻松管理 PHP、Python 和 Ruby 等脚本应用程序的部署过程（部署和回滚）。
它是 Capistrano 的一个 Ansible 端口。它已经被相当多的 PHP 公司使用。

Rocketeer 从 Laravel 框架中得到了很多灵感。 目标是默认智能化配置、高速、优雅的自动化部署工具。
他支持多服务器，多阶段，并行部署等功能。工具的扩展性极强，一切都支持热插拔扩展，并且是由 PHP 编写。

Deployer 是一个用 PHP 编写的部署工具，它很简单且实用。并行执行任务，原子化部署，在多台服务器之间保持一致性。
为 Symfony、Laravel、Zend Framework 和 Yii 提供了通用的任务脚本。
推荐阅读 Younes Rafie 的博文 快速使用 Deployer 部署 PHP 应用，该博文是此工具的绝佳教程。

Magallanes 是另一个由 PHP 编写的自动化部署工具。使用 YAML 作为配置信息，支持多服务器和多环境，自动化部署。
并且自带了许多通用的任务，可用于常见的工具和框架。

扩展阅读：
* Automate your project with Apache Ant
* Deploying PHP Applications —— 关于 PHP 部署的最佳实践和工具的付费书籍

**服务器布置**

在多台服务器的场景下，管理服务器系统配置信息将会是一个令人棘手的事情。
接下来介绍几种工具来让你自动化这些工作，以确保你得到了正确的服务，并且服务器配置无误。
一般情况下，一些大型的云托管商（如：Amazon Web Services, Heroku, DigitalOcean 等）会集成自动化管理工具，
这使得扩展应用程序变得更加容易。

Ansible 让你使用 YAML 配置文件来管理你的服务器基础设施。入门简单，能支持复杂和大型应用场景。
支持 API 来动态管理云主机实例，可以使用某些工具通过动态库存机制来管理它们。

Puppet 是一种具有自定义语言和文件类型的工具，用于管理服务和配置信息。它支持主从结构或者是 无主结构。
在主从结构模式中，客户机将按设定的时间间隔轮询中央主机以获取新配置，并在必要时进行自我更新。
在无主结构模式中，你需要将更改推送到节点。

Chef 是一个基于 Ruby 的强大的系统集成框架，可以使用它构建整个服务环境或虚拟机。
Amazon Web 提供一个叫 OpsWorks 的服务将其很好地集成进去。

延伸阅读:
* Ansible 教程
* 运维的 Ansible - 关于 Ansible 的付费书籍
* Amazon Web 的 Ansible - 关于集成 Ansible 和 Amazon Web 服务的付费书籍
* 关于使用 Chef、Vagrant 和 EC2 部署 LAMP 应用程序的系列博客
* Chef Cookbook，安装和配置 PHP 和 PEAR 包管理系统
* Chef 视频教程系列

**持续集成**

持续集成是一种软件开发实践，团队成员频繁集成他们的工作，通常每个人至少每天集成一次，因此每天都会有许多的集成。
许多团队发现，这种方法大大减少了集成问题，并帮助团队高效地开发更内聚的软件。

– Martin Fowler

有许多方式可以实现 PHP 的持续集成。Travis CI 在持续集成上做得相当出色，即使对于小项目也是如此。
Travis CI 是一个托管的用于开源社区的持续集成服务。它可以和 GitHub 很好地集成，为包括 PHP 在内的多种语言提供一流的支持。

延伸阅读：
* 使用 Jenkins 进行持续集成
* 使用 PHPCI 进行持续集成
* 使用 PHP Censor 进行持续集成
* 使用 Teamcity 进行持续集成

### 虚拟化 




### 缓存

PHP 本身速度非常快，但当您进行远程连接、加载文件等时，可能会出现瓶颈。
当然，如今有各种工具可用于加速应用程序的某些部分，或减少这些耗时任务需要运行的次数，从而达到加载速度更快！

**操作码缓存**

当执行 PHP 文件时，它必须首先编译成操作码 (CPU 的机器语言指令)。
如果源代码不变，则操作码将是相同的，因此此编译步骤将成为 CPU 资源的浪费。

操作码缓存通过将操作码存储在内存中并在连续调用时重用它们来防止冗余编译。
它通常会首先检查文件的签名或修改时间，以防有任何更改。

操作码缓存可能会显著提高应用程序的速度。自 PHP5.5 以来，有一个内置版本 -Zend OPcache. 
根据您的 PHP 包 / 发行版的不同，它通常在默认情况下打开 - 检查 opcache.enable 以及 `phpinfo()` 的输出以确保。
对于早期版本，有一个 PECL 扩展。

阅读有关操作码缓存的更多信息：

    -Zend OPcache(从 5.5 开始与 PHP 捆绑)
    -Zend OPcache (以前称为 Zend Optimizer+) 现在是开源
    -APC-PHP5.4 及更早版本
    -XCache
    -WinCache(MS Windows Server 的扩展)
    - 维基百科上的 PHP 加速器列表
    -PHP 预加载 -PHP>=7.4

**对象缓存**

有时缓存代码中的单个对象会很有用，比如有些需要很大开销获取的数据，或者一些结果集不怎么变化的数据库查询。
你可以使用对象缓存软件将这些数据存放在内存中以便下次高速获取。
如果你获得数据后把它们存起来，下次请求直接从缓存里面获取数据，可以显著提高性能并降低数据库服务器的负载。

许多流行的字节码缓存方案也能缓存自定义的数据，所以更有理由好好使用它们了。 
APCu 、XCache 以及 WinCache 都提供了 API，以便你将 PHP 代码中的数据缓存到内存中。

最常用的内存对象缓存系统是 APCu 和 Memcached 。APCu 是对象缓存的绝佳选择，
它提供了简单的 API 让你能将数据缓存到内存，并且相当容易设置和使用。
APCu 最大的局限性在于，它与所在的服务器绑定。
Memcached 能够以独立的服务的形式安装，通过网络进行访问，
这意味着你能将对象集中存储在超高速存取数据的中心节点，许多不同的系统能从中获取数据。

需要注意的是，当 Web 服务器以 CGI（FastCGI）应用程序形式运行 PHP 时，每个 PHP 进程将会有各自独立的缓存，
也就是说，APCu 缓存数据无法在多个工作进程中共享。
在这种情况下，你可能得考虑使用 Memcached 了，由于它是独立于 PHP 进程的。

在网络配置中，通常 APCu 在访问速度方面优于 memcached，但 memcached 在扩展上更有优势。
如果你的的应用程序不需要运行在多台服务器上，或者不需要 memcached 提供的其他特性，
那么 APCu 应该是对象缓存的最佳选择。

使用 APCu 的范例：
```
<?php
// 检查缓存中是否已经存有‘expensive_data’的数据
$data = apc_fetch('expensive_data');
if ($data === false) {
    //数据不在缓存中；把这个高开销的请求结果缓存起来，供后面使用
    apc_add('expensive_data', $data = get_expensive_data());
}

print_r($data);
```

注意，在 PHP 5.5 之前，APC 同时提供了对象缓存和字节码缓存两种。
APCu 是一个将 APC 的对象缓存引入 PHP 5.5+ 的项目，因为 PHP 目前已经有了内置的字节码缓存（OPcache）。

更多流行的对象缓存系统：

    APCu
    APC Functions
    Memcached
    Redis
    XCache APIs
    WinCache Functions

### 代码注释 






### 资源 






### 社区

PHP 社区多元化并且规模庞大，成员们已做好准备并乐意随时帮助新人。
你可以考虑加入当地的 PHP 用户社区 （PUG） 或者参加更大型的 PHP 会议，从中学习更多最佳实践。
你也可以使用 IRC 逛逛 irc.freenode.com 上的 #phpc 频道，也可以关注 @phpc 的 Twitter 账号。
试着去多结交一些新的开发者，学习新的东西，总之，交一些新朋友！其他的社区资源包含 StackOverflow 。

阅读 PHP 官方事件日历

**PHP 用户组**

如果你住在较大的城市，附近应该就有 PHP 用户组。
你可以通过基于 PHP.ug 的 usergroup-list at php.net 这个地址找到当地的 PUG。
也可以通过 Meetup.com 或者使用搜索引擎 (例如 Google ) 搜索 `php user group near me` 。
如果你住在比较小的城镇，也许当地还没有 PUG ，如果是这种情形，不妨由你开始来组建一个。

这里要特别提到两个全球的用户组：NomadPHP 和 PHPWomen。NomadPHP 每月提供两次在线用户组会议，
由 PHP 社区里顶尖的高手进行演讲。PHPWomen 原本是针对女性 PHP 开发者的非排他性的用户组。
会员资格发放给那些支持多元化社区的人。PHPWomen 提供了一个支持、指导和教育的网络平台，
并普遍促进 “女性友好” 和专业氛围的营造。

了解关于 PHP Wiki 上的用户群

**PHP 会议**

PHP 社区还在世界各地多个国家主办大型区域性或国际性会议。
知名的社区成员通常会在这些大型活动中现身演讲，因此这是一个直接向业内领袖学习的绝佳机会。

查找 PHP 会议

**ElePHPants**

PHP 项目那可爱的吉祥物名叫 ElePHPant ，设计中是一头大象。
最初是在 1998 年，由文森・庞蒂尔（Vincent Pontier）为 PHP 项目设计的，
他是世界各地成千上万的 ElePHPants 精神之父，在 10 年后，可爱的毛绒大象玩具也诞生了。
现在，ElePHPants 出现在许多 PHP 会议上，并陪同无数 PHP 开发人员在他们的计算机上寻找着乐趣和灵感。

文森・庞蒂尔访谈录

### 贡献 

英文版本维护者

    Josh Lockhart
    Phil Sturgeon
    Project Contributors

中文版本维护者

请查看此文档所有的 参与译者


## 参考资料

PHP 之道（PHP The Right Way 中文版） 新版 <https://learnku.com/docs/php-the-right-way/PHP8.0>

PHP 之道（PHP: The Right Way）：书写更好的 PHP 代码 （<http://www.phptherightway.com/>）

PHP 之道 中文版 （<http://laravel-china.github.io/php-the-right-way/>）。

