---
layout: post
categories: PHP
title: PHP spl_autoload_register()相关
meta: spl_autoload_register() 注册给定的函数作为 __autoload 的实现，可以实现文件自动require，函数自动加载
---
* content
{:toc}

### 正文

先看一个例子：
```php
<?php
class autoloader 
{
  public static $loader;
  
  public static function init() {
    if (self::$loader == NULL)
      self::$loader = new self ();
    return self::$loader;
  }
  
  public function __construct() {
    spl_autoload_register ( array ($this, 'model' ) );
    spl_autoload_register ( array ($this, 'helper' ) );
    spl_autoload_register ( array ($this, 'controller' ) );
    spl_autoload_register ( array ($this, 'library' ) );
  }
  
  public function library($class) {
    set_include_path ( get_include_path () . PATH_SEPARATOR . '/lib/' );
    spl_autoload_extensions ( '.library.php' );
    spl_autoload ( $class );
  }
  
  public function controller($class) {
    $class = preg_replace ( '/_controller$/ui', '', $class );
    set_include_path ( get_include_path () . PATH_SEPARATOR . '/controller/' );
    spl_autoload_extensions ( '.controller.php' );
    spl_autoload ( $class );
  }
  
  public function model($class) {
    $class = preg_replace ( '/_model$/ui', '', $class );
    set_include_path ( get_include_path () . PATH_SEPARATOR . '/model/' );
    spl_autoload_extensions ( '.model.php' );
    spl_autoload ( $class );
  }
  
  public function helper($class) {
    $class = preg_replace ( '/_helper$/ui', '', $class );
    set_include_path ( get_include_path () . PATH_SEPARATOR . '/helper/' );
    spl_autoload_extensions ( '.helper.php' );
    spl_autoload ( $class );
  }
}

// call
autoloader::init ();
?>
```

SPL是Standard PHP Library(标准PHP库)的缩写。
它是PHP5引入的一个扩展库，其主要功能包括autoload机制的实现及包括各种Iterator接口或类。
SPL autoload机制的实现是通过将函数指针autoload_func指向自己实现的具有自动装载功能的函数来实现的。
SPL有两个不同的函数spl_autoload, spl_autoload_call，
通过将autoload_func指向这两个不同的函数地址来实现不同的自动加载机制。

在了解这个函数之前先来看另一个函数：__autoload

#### __autoload

__autoload 这是一个自动加载函数，在PHP5中，当我们实例化一个未定义的类时，就会触发此函数。看下面例子：

printit.class.php：
```php
<?php
class PRINTIT 
{
    function doPrint() {
    echo 'hello world';
 }
}
?>
```

index.php
```php
<?
function __autoload( $class ) {
    $file = $class . '.class.php';
    if (is_file($file)) {
        require_once($file);
    }
}

$obj = new PRINTIT();
$obj->doPrint();?>
```

运行index.php后正常输出hello world。
在index.php中，由于没有包含printit.class.php，在实例化printit时，
自动调用__autoload函数，参数$class的值即为类名printit，此时printit.class.php就被引进来了。

在面向对象中这种方法经常使用，可以避免书写过多的引用文件，同时也使整个系统更加灵活。

#### spl_autoload_register()

再看spl_autoload_register()，这个函数与__autoload有异曲同工之妙，看个简单的例子
```php
<?php
function loadprint( $class ) {
    $file = $class . '.class.php';
    if (is_file($file)) {
        require_once($file);
    }
}

spl_autoload_register( 'loadprint' );
$obj = new PRINTIT();
$obj->doPrint();
?>
```

将__autoload换成loadprint函数。但是loadprint不会像__autoload自动触发，
这时spl_autoload_register()就起作用了，它告诉PHP碰到没有定义的类就执行loadprint()。

上面是spl_autoload_register()直接调用函数实现文件自动加载，
下面看一下spl_autoload_register()调用类静态方法实现文件自动加载：
```php
<?
class test 
{
    public static function loadprint( $class ) 
    {
        $file = $class . '.class.php';
        if (is_file($file)) {
            require_once($file);
        }
    }
}

spl_autoload_register( array('test','loadprint') );
// 另一种写法：spl_autoload_register( "test::loadprint" );
$obj = new PRINTIT();
$obj->doPrint();
?>
```

php官网说明
```
spl_autoload_register
(PHP 5 >= 5.1.0, PHP 7)

spl_autoload_register — 注册给定的函数作为 __autoload 的实现

说明
spl_autoload_register ([ callable $autoload_function [, bool $throw = true [, bool $prepend = false ]]] ) : bool
将函数注册到SPL __autoload函数队列中。如果该队列中的函数尚未激活，则激活它们。
如果在你的程序中已经实现了__autoload()函数，它必须显式注册到__autoload()队列中。因为 spl_autoload_register()函数会将Zend Engine中的__autoload()函数取代为spl_autoload()或spl_autoload_call()。
如果需要多条 autoload 函数，spl_autoload_register() 满足了此类需求。 它实际上创建了 autoload 函数的队列，按定义时的顺序逐个执行。相比之下， __autoload() 只可以定义一次。

参数
autoload_function
    欲注册的自动装载函数。如果没有提供任何参数，则自动注册 autoload 的默认实现函数spl_autoload()。
throw
    此参数设置了 autoload_function 无法成功注册时， spl_autoload_register()是否抛出异常。
prepend
    如果是 true，spl_autoload_register() 会添加函数到队列之首，而不是队列尾部。

返回值
成功时返回 TRUE， 或者在失败时返回 FALSE。
```

如果同时用spl_autoload_register注册了一个类的方法和__autoload函数，
那么，会根据注册的先后，如果在第一个注册的方法或函数里加载了类文件，
就不会再执行第二个被注册的类的方法或函数。反之就会执行第二个被注册的类的方法或函数。

<br/><br/><br/><br/><br/>
### 参考资料

spl_autoload_register() <https://www.php.net/manual/zh/function.spl-autoload-register.php>

PHP中spl_autoload_register()函数用法实例详解 <https://www.jianshu.com/p/cc7ed9f0b378>

Yii的类自动加载机制 <http://www.digpage.com/autoload.html>

Inversion of Control Containers and the Dependency Injection pattern <https://martinfowler.com/articles/injection.html> 
