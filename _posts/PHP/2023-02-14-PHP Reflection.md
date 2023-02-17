---
layout: post
categories: PHP
title: PHP Reflection
meta: PHP Reflection
---
* content
{:toc}

## 说明一

### 一、概念

反射是指在PHP运行状态中，扩展分析PHP程序，导出或提出关于类、方法、属性、参数等的详细信息，包括注释。
这种动态获取信息以及动态调用对象方法的功能称为反射API。

### 二、php反射的基本语法

实现反射的方法有很多，可以通过实例化一个专门控制类的ReflectionClass类来实现反射，也可以在已有类实例的情况下，
通过直接实例化ReflectionMethod类来执行反射方法，原理如图：

![]({{site.baseurl}}/images/20230217/pic20230217183310.png)

![]({{site.baseurl}}/images/20230217/pic20230217183316.png)

以下是对反射类和反射方法类的基本用法：

#### 1、反射类

(1) $reflectClass = new ReflectionClass(<类名>);

传入类名字符串，返回控制目标类的ReflectionClass类实例；

(2) $reflectClass->getConstant(<常量名>);

传入类中定义了的常量名，返回常量值，可通过$reflectClass->getConstants返回类中所有定义的常量的数组；

(3) $class = $reflectClass->newInstance();

实例化类，返回目标类实例；也可通过$reflectClass->newInstanceArgs(<参数数组>)传入实例化的构造函数参数进行实例化;

#### 2、反射方法

(1) $reflectMethod = new ReflectionMethod(<方法名>);

传入方法名名字符串，返回控制目标方法的ReflectionMethod类实例；

(2) $parameters = $reflectMethod->getParameters();

获取该类所需的参数名，该方法返回一个包含所有参数名的二维数组；

(3) $name = $parameters->getName();

返回要执行的方法所需参数数组的单个参数名，可通过foreach循环逐一获取和赋值；

(4) $reflectMethod->invokeArgs(<类实例>,<执行该方法所需参数数组>);

传入类实例和方法参数，执行方法，返回执行结果。

#### 3、反射类和反射方法中其他常用的用法：

ReflectionClass:

![]({{site.baseurl}}/images/20230217/pic20230217183320.png)

ReflectionMethod:

![]({{site.baseurl}}/images/20230217/pic20230217183326.png)

#### 4、

除了ReflectionClass和ReflectionMethod，我们对于类中的参数、属性和php服务的环境变量、
扩展等参数也是可以通过反射API的一些方法来执行的，如下：

![]({{site.baseurl}}/images/20230217/pic20230217183330.png)

### 三、反射在实际应用中的使用

1. 反射可以用于文档、文件生成。可以用它对文件里的类进行扫描，逐个生成描述文档;
2. 既然反射可以探知类的内部结构，那么可以用它做hook实现插件功能；
3. 可以用于做动态代理，在未知或者不确定类名的情况下，动态生成和实例化一些类和执行方法；
4. 对于多次继承的类，我们可以通过多次反射探索到基类的结构，或者采用递归的形式反射，
实现实例化所有继承类，这即是PHP依赖注入的原理。

### 四、PHP反射的优缺点

优点

1. 支持反射的语言提供了一些在低级语言中难以实现的运行时特性。
2. 可以在一定程度上避免硬编码，提供灵活性和通用性。
3. 可以作为一个第一类对象发现并修改源代码的结构（如代码块、类、方法、协议等）。
4. 可以在运行时像对待源代码语句一样计算符号语法的字符串（类似JavaScript的eval()函数），
进而可将跟class或function匹配的字符串转换成class或function的调用或引用。
5. 可以创建一个新的语言字节码解释器来给编程结构一个新的意义或用途。

劣势

1. 此技术的学习成本高。面向反射的编程需要较多的高级知识，包括框架、关系映射和对象交互，以利用更通用的代码执行。
2. 同样因为反射的概念和语法都比较抽象，过多地滥用反射技术会使得代码难以被其他人读懂，不利于合作与交流。
3. 由于将部分信息检查工作从编译期推迟到了运行期，此举在提高了代码灵活性的同时，牺牲了一点点运行效率。
4. 通过深入学习反射的特性和技巧，它的劣势可以尽量避免，但这需要许多时间和经验的积累。

## 说明二

如何使用反射API

```php
<?php
class person
{
    public $name;
    public $gender;
    
    public function say()
    {
        echo $this->name, "\tis ", $this->gender, "\r\n";
    }

    public function set($name, $value)
    {
        echo "Setting $name to $value \r\n";
        $this->$name = $value;
    }

    public function get($name)
    {
        if (!isset($this->$name)) {
            echo '未设置';
            $this->$name = "正在为你设置默认值";
        }

        return $this->$name;
    }
}

$student = new person();
$student->name = 'Tom';
$student->gender = 'male';
$student->age = 24;
```
    

现在，要获取这个student对象的方法和属性列表该怎么做呢？如以下代码所示：
```php
<?php
$reflect = newReflectionObject($student);

// 获取对象属性列表
$props = $reflect->getProperties();
foreach ($props as $prop) {
    print$prop->getName() . "\n";
}

// 获取对象方法列表
$m = $reflect->getMethods();
foreach ($m as $prop) {
    print$prop->getName() . "\n";
}
```

也可以不用反射API，使用class函数，返回对象属性的关联数组以及更多的信息：
```php
<?php
// 返回对象属性的关联数组
var_dump(get_object_vars($student));

// 类属性
var_dump(get_class_vars(get_class($student)));

// 返回由类的方法名组成的数组
var_dump(get_class_methods(get_class($student)));
````

假如这个对象是从其他页面传过来的，怎么知道它属于哪个类呢？一句代码就可以搞定：
```php
<?php
// 获取对象属性列表所属的类
echo get_class($student);
```

反射API的功能显然更强大，甚至能还原这个类的原型，包括方法的访问权限等，如：
```php
<?php
// 反射获取类的原型

$obj = newReflectionClass('person');

$className = $obj->getName();

$Methods = $Properties = array();

foreach ($obj->getProperties() as $v) {
    $Properties[$v->getName()] = $v;
    }

foreach ($obj->getMethods() as $v) {
    $Methods[$v->getName()] = $v;
    }

echo "class {$className}\n{\n";

is_array($Properties) && ksort($Properties);

foreach ($Properties as $k => $v) {
    echo "t";
    echo $v->isPublic() ? ' public' : '', $v->isPrivate() ? ' private' : '',
    $v->isProtected() ? ' protected' : '',
    $v->isStatic() ? ' static' : '';
    echo "t{$k}\n";

}

echo "\n";

if (is_array($Methods)) ksort($Methods);

foreach ($Methods as $k => $v) {
    echo "\tfunction {$k}(){}\n";
}

echo "}n";
````
输出如下：
```php
<?php
class person
{
    public $gender;
    public $name;

    function get()
    {
    }

    function set()
    {
    }

    function say()
    {
    }
}
````
不仅如此，PHP手册中关于反射API更是有几十个，可以说，反射完整地描述了一个类或者对象的原型。反射不仅可以用于类和对象，还可以用于函数、扩展模块、异常等。

反射有什么作用

反射可以用于文档生成。因此可以用它对文件里的类进行扫描，逐个生成描述文档。

既然反射可以探知类的内部结构，那么是不是可以用它做hook实现插件功能呢？或者是做动态代理呢？

例如：
```php
<?php
class mysql
{
    function connect($db)
    {
        echo "连接到数据库${db［0］}\r\n";
    }
}

class sqlproxy
{
    private $target;

    function construct($tar)
    {
        $this->target［］ = new$tar();
    }

    function call($name, $args)
    {
        foreach ($this->target as $obj) {
            $r = newReflectionClass($obj);
            if ($method = $r->getMethod($name)) {
                if ($method->isPublic() && !$method->isAbstract()) {
                    echo "方法前拦截记录LOG\r\n";
                    $method->invoke($obj, $args);
                    echo "方法后拦截\r\n";
                }
            }
        }
    }
}

$obj = new sqlproxy('mysql');
$obj->connect('member');
````

### 1

为了更好地理解，我们通过一个例子来看类的反射，以及如何实现依赖注入。
下面这个类代表了坐标系里的一个点，有两个属性横坐标x和纵坐标y。
```php
<?php
/** * Class Point */

class Point
{
    public $x;
    public $y;

    /** * Point constructor. * @param int $x horizontal value of point's coordinate * @param int $y vertical value of point's coordinate */
    public function __construct($x = 0, $y = 0)
    {
        $this->x = $x;
        $this->y = $y;
    }
}
```

接下来这个类代表圆形，可以看到在它的构造函数里有一个参数是Point类的，即Circle类是依赖与Point类的。

```php
<?php
class Circle
{
    /** * @var int */
    public $radius; //半径
    /** * @var Point */
    public $center;

    //圆心点
    const PI = 3.14;

    public function __construct(Point $point, $radius = 1)
    {
        $this->center = $point;
        $this->radius = $radius;
    }

    //打印圆点的坐标
    public function printCenter()
    {
        printf('center coordinate is (%d, %d)', $this->center->x, $this->center->y);
    }

    //计算圆形的面积
    public function area()
    {
        return 3.14 * pow($this->radius, 2);
    }
}
```

ReflectionClass

下面我们通过反射来对Circle这个类进行反向工程。
把Circle类的名字传递给reflectionClass来实例化一个ReflectionClass类的对象。

```php
<?php
$reflectionClass = new reflectionClass(Circle::class);

//返回值如下
object(ReflectionClass)#1 (1) {
  ["name"]=>
  string(6) "Circle" 
}
```

反射出类的常量

```php
<?php
$reflectionClass->getConstants(); 
```

返回一个由常量名称和值构成的关联数组
```
array(1) { 
  ["PI"]=> 
  float(3.14) 
} 
```

通过反射获取属性
```php
<?php
$reflectionClass->getProperties(); 
```

返回一个由ReflectionProperty对象构成的数组
```
array(2) { [0]=> object(ReflectionProperty)#2 (2) { ["name"]=> string(6) "radius" ["class"]=> string(6) "Circle" } [1]=> object(ReflectionProperty)#3 (2) { ["name"]=> string(6) "center" ["class"]=> string(6) "Circle" } } 
```

反射出类中定义的方法
```php
<?php
$reflectionClass->getMethods(); 
```

返回ReflectionMethod对象构成的数组
```
array(3) { [0]=> object(ReflectionMethod)#2 (2) { ["name"]=> string(11) "__construct" ["class"]=> string(6) "Circle" } [1]=> object(ReflectionMethod)#3 (2) { ["name"]=> string(11) "printCenter" ["class"]=> string(6) "Circle" } [2]=> object(ReflectionMethod)#4 (2) { ["name"]=> string(4) "area" ["class"]=> string(6) "Circle" } } 
```

我们还可以通过getConstructor()来单独获取类的构造方法，其返回值为一个ReflectionMethod对象。

```php
<?php
$constructor = $reflectionClass->getConstructor(); 
```

反射出方法的参数
```php
<?php
$parameters = $constructor->getParameters(); 
```

其返回值为ReflectionParameter对象构成的数组。

```
array(2) { [0]=> object(ReflectionParameter)#3 (1) { ["name"]=> string(5) "point" } [1]=> object(ReflectionParameter)#4 (1) { ["name"]=> string(6) "radius" } } 
```

依赖注入

好了接下来我们编写一个名为make的函数，传递类名称给make函数返回类的对象，在make里它会帮我们注入类的依赖，即在本例中帮我们注入Point对象给Circle类的构造方法。

```php
<?php
//构建类的对象 function make($className) { $reflectionClass = new ReflectionClass($className); $constructor = $reflectionClass->getConstructor(); $parameters = $constructor->getParameters(); $dependencies = getDependencies($parameters); return $reflectionClass->newInstanceArgs($dependencies); } //依赖解析 function getDependencies($parameters) { $dependencies = []; foreach($parameters as $parameter) { $dependency = $parameter->getClass(); if (is_null($dependency)) { if($parameter->isDefaultValueAvailable()) { $dependencies[] = $parameter->getDefaultValue(); } else { //不是可选参数的为了简单直接赋值为字符串0 //针对构造方法的必须参数这个情况 //laravel是通过service provider注册closure到IocContainer, //在closure里可以通过return new Class($param1, $param2)来返回类的实例 //然后在make时回调这个closure即可解析出对象 //具体细节我会在另一篇文章里面描述 $dependencies[] = '0'; } } else { //递归解析出依赖类的对象 $dependencies[] = make($parameter->getClass()->name); } } return $dependencies; } 
```

定义好make方法后我们通过它来帮我们实例化Circle类的对象:

```php
$circle = make('Circle'); $area = $circle->area(); /*var_dump($circle, $area); object(Circle)#6 (2) { ["radius"]=> int(1) ["center"]=> object(Point)#11 (2) { ["x"]=> int(0) ["y"]=> int(0) } } float(3.14)*/ 
```

过上面这个实例我简单描述了一下如何利用PHP类的反射来实现依赖注入，Laravel的依赖注入也是通过这个思路来实现的，只不过设计的更精密大量地利用了闭包回调来应对各种复杂的依赖注入，详情可以参考我的另一篇介绍Laravel服务容器的文章。







<br/><br/><br/><br/><br/>
## 参考资料

php反射描述 <https://blog.csdn.net/weixin_40172337/article/details/115025059>

PHP Reflection与依赖注入 <https://blog.csdn.net/kevin_tech/article/details/90547265>

PHP高级特性之反射（Reflection）  <https://www.sohu.com/a/211728040_470018>





