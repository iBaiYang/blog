---
layout: post
categories: PHP
title: PHP static延迟静态绑定
meta: parent、self、static 这些关键字混合使用，里面的区别能详细分清吗。这篇文章简单的理一理。
---
* content
{:toc}

### 正文

#### 测试

看看下面这个例子输出什么：

```php
<?php
class A {
    public static function foo() {
        static::who();
    }

    public static function who() {
        echo __CLASS__;
    }
}

class B extends A {
    public static function test() {
        A::foo();
        parent::foo();
        self::foo();
        A::who();
        parent::who();
        self::who();
        B::foo();
    }

    public static function who() {
        echo __CLASS__;
    }
}

class C extends B {
    public static function who() {
        echo __CLASS__;
    }
}

C::test();
```

输出：

ACCAABB，你答对了吗？

#### 解说

静态属性与静态方法可以在不实例化类的情况下调用，直接使用`类名::方法名`的方式进行调用。
静态属性只能用`::`方式访问，即使类实例化为对象后也不允许对象使用 `->` 操作符访问；
不过静态方法在类实例化为对象后可以使用 `->` 操作符调用。

```
class Car {
    private static $speed = 10;
    
    public static function getSpeed() {
        return self::$speed;
    }
}

echo Car::getSpeed();  // 调用静态方法
```

静态方法也可以通过变量来进行动态调用
```
$func = 'getSpeed';
$className = 'Car';
echo $className::$func();  // 动态调用静态方法
```

静态方法中，`$this`伪变量不允许使用。可以使用self，parent，static在内部调用静态方法与属性。

```
class Car {
    private static $speed = 10;
    
    public static function getSpeed() {
        return self::$speed;
    }
    
    public static function speedUp() {
        return self::$speed += 10;
    }
}
class BigCar extends Car {
    public static function start() {
        parent::speedUp();
    }
}

BigCar::start();
echo BigCar::getSpeed();  // 输出20
```

很明显，根据上面代码可知调用本类方法可以使用self关键字，调用父类方法可以使用parent关键字，但是如果想在父类中调用子类方法呢？
这就是static关键字设计出来的原因：根据延迟静态绑定，父类调用子类方法。
这里给出static的另外一个用法，这里如果在调用的方法前面的加static关键字的话，PHP会根据该类的继承层次来计算最终的静态方法，
这就是延迟静态绑定。延迟静态绑定丰富了类的设计，使类的使用更加灵活。

```
class Test1
{
    public function t1()
    {
        static::t2();
    }
    
    public static function t2()
    {
        echo 'Test1 ';
    }
}

class Test2 extends Test1
{
    public static function t2()
    {
        echo 'Test2 ';
    }
}

$t = new Test2();
$t->t1();  // 输出 Test2
Test2::t2();  // 输出 Test2
```

#### 解惑

```
static $a = 3;
function num1(){
    static $a = 0;
    return $a += 1;
}

function num2(){
    $a = 5;
    global $a;
    echo $a += 1;
}
	
function num3(){
    static $a = 10;
    return $a += 1;
}
	
echo num1();	// 输出 1

num2();         // 输出 4
echo $a;        // 输出 4

echo num3();    // 输出 11
echo num3();    // 输出 12
echo num3();    // 输出 13
```

从中我们看出几点：
1. 函数体中的静态变量与全局中的静态变量不冲突，函数体中相当于重定义
2. 函数体中global作用下会使局部与全局统一，相当于内存地址引用
3. 函数体中的静态变量在函数调用的时候只会被初始化一次

因为静态变量在全局数据区分配内存；

此处要提一下 缓存在逻辑上大致区分为：栈、堆、全局区、程序代码区、文字常量区

栈：存放变量名(会有一部分的整型，浮点型，字符串存放在此处)

堆：存放变量值

全局区：存放静态变量，全局变量

程序代码区：存放要执行的函数及方法

文字常量区：存放常量等

![]({{site.baseurl}}/images/20190228/20190228150107.jpg)

#### 延申

```
class first
{    
    public $num = 1;
    // 声明一个静态属性
    static $name = '456 ';
    
    // 声明一个静态方法
    public static function self_use(){
        echo self::$name;
        echo self::method();
    }
    
    public static function static_use(){
        echo static::method();
    }
    
    public static function method(){
        echo 'first ';
    }
}

class second extends first
{
    public static function method(){
        echo 'second ';
    }
}

$one = new first();
echo $one::$name;
$one->self_use();
$one->static_use();

$two = new second();
$two->self_use();
$two->static_use();
```

输出：456 456 first first 456 first second 

再看下下面这个：
```
class first
{    
    static $num = 1;
	
    public static function self_use(){
        static::$num += 1;
    }
}
class second extends first{}

echo first::$num;
first::self_use();
echo first::$num;

echo second::$num;
second::self_use();
echo second::$num;
```

输出结果：1223，可以看出 函数体中的静态变量在函数调用的时候只会被初始化一次，继承也一样。

<br/><br/><br/><br/><br/>
### 参考资料

PHP $this，static，self，const的区别和联系 <https://ibaiyang.github.io/blog/php/2019/07/23/PHP-$this-static-self-const的区别和联系.html>

<http://www.jellythink.com/archives/956>

<http://www.jianshu.com/p/1d39fa27b2fb>

<http://www.jianshu.com/p/155ce4301f93>

<https://www.cnblogs.com/haizizhu/p/8040236.html>

<https://www.jellythink.com/archives/237>

<https://www.cnblogs.com/haizizhu/p/8040236.html>

<http://baijiahao.baidu.com/s?id=1600853651477190798&wfr=spider&for=pc>




