---
layout: post
categories: PHP
title: PHP The Right Way 学习2
meta: PHP The Right Way 学习2
---
* content
{:toc}

## 正文

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

```
<?php // user_profile.php ?>

<?php $this->insert('header', ['title' => 'User Profile']) ?>

<h1>User Profile</h1>
<p>Hello, <?=$this->escape($name)?></p>

<?php $this->insert('footer') ?>
```

**原生 PHP 模板使用继承的示例**

使用 Plates 类库

```
<?php // template.php ?>

<html>
<head>
    <title><?=$title?></title>
</head>
<body>

<main>
    <?=$this->section('content')?>
</main>

</body>
</html>
```

```
<?php // user_profile.php ?>

<?php $this->layout('template', ['title' => 'User Profile']) ?>

<h1>User Profile</h1>
<p>Hello, <?=$this->escape($name)?></p>
```

**编译模板**

尽管 PHP 已经发展成为一种成熟的面向对象语言，但它作为模板语言 没有太大改进。
编译模板，如 Twig，Brainy，或 Smarty【注】，提供了模板专用的新语法填补这一空白。
从自动转义到继承以及简化的控制结构，编译模板设计为更易于编写、更清晰易读和使用更安全。
编译模板甚至可以跨不同语言共享，Mustache 就是一个很好的例子。由于这些模板必须编译，
因此会对性能造成轻微的影响，但是在适当地使用缓存后影响就非常小了。

【注】：虽然 Smarty 提供了自动转义功能，不过默认情况下是关闭的

**编译模板简单示例**

使用 Twig 类库。

```
{% include 'header.html' with {'title': 'User Profile'} %}

<h1>User Profile</h1>
<p>Hello, {{ name }}</p>

{% include 'footer.html' %}
```

**使用继承的编译模板示例**

使用 Twig 类库。

```
// template.html

<html>
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>

<main>
    {% block content %}{% endblock %}
</main>

</body>
</html>
```

```
// user_profile.html

{% extends "template.html" %}

{% block title %}User Profile{% endblock %}
{% block content %}
    <h1>User Profile</h1>
    <p>Hello, {{ name }}</p>
{% endblock %}
```

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

另一种错误信息是在编译时报告的 E_STRICT。这些消息用于确保与即将发布的新版本 PHP 的兼容性，对代码的更改建议。

**更改 PHP 的错误报告行为**

错误报告可以通过 PHP 配置或 PHP 函数来更改。你可以在脚本执行期间，使用内置的 PHP 函数 error_reporting()，
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
此外，在正式环境中 PHP 也没有办法关闭错误控制操作符。也许你认为那些错误时无害的，不过那些较具伤害性的错误同时也会被隐藏。

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










## 参考资料

PHP 之道（PHP The Right Way 中文版） 新版 <https://learnku.com/docs/php-the-right-way/PHP8.0>

PHP 之道（PHP: The Right Way）：书写更好的 PHP 代码 （<http://www.phptherightway.com/>）

PHP 之道 中文版 （<http://laravel-china.github.io/php-the-right-way/>）。

