---
layout: post
categories: PHP
title: PHP $this，static，self，const的区别和联系
meta: $this，static，self，const的区别和联系
---
* content
{:toc}

### 正文

#### $this

$this 指向的是实际调用时的对象，也就是说，实际运行过程中，谁调用了类的属性或方法，
$this 指向的就是哪个对象。但 $this 不能访问类的静态属性和常量，且 $this 不能存在于静态方法中。

$this表示当前实例，在类的内部方法访问未声明为const及static的属性时，使用$this->属性, $this->方法 的形式。如：

```
class Person
{
    private $name='张三';
    public  $sex;
    
    public function run(){
        return $this->name.'能跑';
    }
    
    public function length(){
        echo $this->run().'800米';
    }
}
 
$person=new Person();
$person->length();
```

#### static

static 关键字除了可以声明类的静态成员（属性和方法）外，还有一个非常重要的作用就是后期静态绑定。

static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
静态调用时，static 指向的是实际调用时的类；非静态调用时，static 指向的是实际调用时的对象所属的类。

声明及调用静态变量如下：
```
class Person
{
    static  $sex=1;
    public  $name='张三';
    
    static function qianDao(){
        return self::$sex++;
    }
    
    static function printQianDao(){
        echo self::qianDao();
    }
    
    static function printQianDao2(){
        echo $this->qianDao();
    }
    
    static function printQianDao3(){
        echo $this->name;
    }
    
    public function printQianDao4(){
        echo $this->name;
    }
}

$person=new Person();
$person->printQianDao();  // 输出1
Person::printQianDao();  // 输出2
$person->printQianDao2();  // 报错：Using $this when not in object context
$person->printQianDao3();  // 报错：Using $this when not in object context
$person->printQianDao4();  // 输出“张三”;
Person::printQianDao4();  // 报错1：Non-static method Person::printQianDao4() should not be called statically，报错2：Using $this when not in object context
```

注意事项：
1. 在静态方法内部,不能使用$this(即在静态方法内部只能调用静态成员);
2. 调用静态成员的方法只能是self::方法名(属性名)或者parent::方法名(属性名)或者类名::方法名(属性名)
3. 在类的外部，调用静态方法时，可以不用实例化，直接类名::方法名(属性名)
4. 静态方法执行之后变量的值不会丢失，只会初始化一次，这个值对所有实例都是有效的

#### self

self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前定义所在的类，表示类本身，这是 self 的限制。

self 和 __CLASS__，都是对当前类的静态引用，取决于定义当前方法所在的类。也就是说，self 写在哪个类里面， 它引用的就是谁。

#### const

定义及调用类的常量如下：
```
class Person
{
    const PI=1;
    
    static function getPi(){
        return self::PI;
    }
    
    static function printPi(){
        echo self::getPi();
    }
}

Person::printPi();
```

<br/><br/><br/><br/><br/>
### 参考资料

php类中的$this，static，const，self这几个关键字使用方法 <https://www.cnblogs.com/freelyflying/p/7008072.html>

PHP 中 self、static、$this 的区别 & 后期静态绑定详解 <https://blog.csdn.net/lamp_yang_3533/article/details/79912453>

