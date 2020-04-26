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
$this 指向的就是哪个对象。如B继承A，实例化B后调用到了A的方法，此时A的$this指向的还是B这个实际调用的对象，
如果B中$this指向的方法或属性不存在，则取A中的指向内容。

$this 会先到所在定义范围内(如在父类中则在父类)寻找私有方法，再到它指向的对象所属的类中(当前子类)寻找私有方法， 
然后(当前子类)寻找公有方法，最后到所在定义范围内(如在父类中)寻找公共方法。只要找到了匹配的方法，就调用，并停止查找。

$this 不能访问类的静态属性和常量，且 $this 不能存在于静态方法中。

$this表示当前实例，在类的内部方法访问**未声明**为const及static的**属性**时，使用$this->属性 的形式；
申明为const及static的属性，须用 ::属性 的形式(self::/parent::/static::)。如：

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

看一下下面代码的输出结果：
```
class A {
    public function a1()
    {
        $this->c2();
    }
    
    public function c2()
    {
        echo 'a2';
    }
}

 
class B {
    public function b1()
    {
        $a = new A;
        $a->a1();
    }
    
    public function c2()
    {
        echo 'b2';
    }
}

$b = new B;
$b->b1();
```

输出 a2 ，不知道你打对了没。$this指向的是当前实例化对象，在一个对象内再实例化一个子对象，则在子对象中的$this指向子对象。

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
    
    public static function qianDao(){
        return self::$sex++;
    }
    
    /**
     * 不加 public 、protected、private 范围限定关键字，默认为public，属性也一样
     */
    static function printQianDao(){
        echo self::qianDao();
    }
    
    public static function printQianDao2(){
        echo $this->qianDao();
    }
    
    public static function printQianDao3(){
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
2. 类未实例化为对象时，调用静态成员的方法只能是self::方法名(属性名)或者parent::方法名(属性名)或者类名::方法名(属性名)；
类实例化为对象后，静态方法可用 -> 访问，静态属性不能用 -> 访问！
3. 在类的外部，调用静态方法时，可以不用实例化，直接类名::方法名(属性名)
4. 静态方法执行之后变量的值不会丢失，只会初始化一次，这个值对所有实例都是有效的

static 是先到它指向的类中(当前子类)寻找私有方法，再(当前子类)寻找共有方法；
然后到方法体定义所在范围内(如在父类中)寻找私有方法， 再(如在父类中)寻找共有方法。只要找到了匹配的方法，就调用，并停止查找。

#### self

self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前方法体定义所在的类，表示类本身，这是 self 的限制。

self 和 `__CLASS__`，都是对当前类的静态引用，取决于定义当前方法所在的类。也就是说，self 写在哪个类里面， 它引用的就是谁。

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

#### 后期静态绑定

后期静态绑定（也叫延迟静态绑定），可用于在继承范围内引用静态调用的类，也就是代码运行时最初调用的类。

```
class A {
    protected $name = 'A';
    static $alias = 'a';
    const HASH = 'md5';
 
    public function dd() {
        echo $this->name; echo '--';
        echo static::$alias; echo '--';     // 后期静态绑定
        echo static::HASH; echo '--';     // 后期静态绑定
        echo self::$alias; echo '--';
        echo self::HASH; echo '--';
 
        var_dump(new self); echo '--';
        var_dump($this); echo '--';
        var_dump(new static); echo '<br>';   // 后期静态绑定
    }
 
    public static function who() {
        echo __CLASS__;
        echo ' [ This is A ]'; echo '<br>';
    }
 
    public static function test() {
        self::who();
    }
 
    public static function test2() {
        static::who();  // 后期静态绑定
    }
 
    public static function getInstance() {
        var_dump(new self); echo '--';
        var_dump(new static); echo '<br>';  // 后期静态绑定
    }
}
 
class B extends A {
    protected $name = 'B';
    static $alias = 'b';
    const HASH = 'sha1';
 
    public static function who() {
        echo __CLASS__;
        echo ' [ This is B ]'; echo '<br>';
    }
}
 
class C extends B {
    public static function who() {
        echo __CLASS__;
        echo ' [ This is C]'; echo '<br>';
    }
}

(new A)->dd();  // A--a--md5--a--md5--object(A)#2 (1) { ["name":protected]=> string(1) "A" } --object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(A)#2 (1) { ["name":protected]=> string(1) "A" }
(new B)->dd();  // B--b--sha1--a--md5--object(A)#2 (1) { ["name":protected]=> string(1) "A" } --object(B)#1 (1) { ["name":protected]=> string(1) "B" } --object(B)#2 (1) { ["name":protected]=> string(1) "B" }
 
A::who();  // A [ This is A ]
B::who();  // B [ This is B ]
 
A::test();  // A [ This is A ]
B::test();  // A [ This is A ]
 
A::test2(); // A [ This is A ]
B::test2(); // B [ This is B ]
C::test2(); // C [ This is C]
 
A::getInstance();   // object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(A)#1 (1) { ["name":protected]=> string(1) "A" }
B::getInstance();   // object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(B)#1 (1) { ["name":protected]=> string(1) "B" }
```

总结说明：
* self 和` __CLASS__`，都是对当前类的静态引用，取决于定义当前方法所在的类。也就是说，self 写在哪个类里面， 它引用的就是谁。
* $this 指向的是实际调用时的对象，也就是说，实际运行过程中，谁调用了类的属性或方法，$this 指向的就是哪个对象。
但 $this 不能访问类的静态属性和常量，且 $this 不能存在于静态方法中。
* static 关键字除了可以声明类的静态成员（属性和方法）外，还有一个非常重要的作用就是后期静态绑定。
* self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前定义所在的类，这是 self 的限制。
* $this 指向的对象所属的类和 static 指向的类相同。哪个调用，$this 指向的就是哪个对象。如B继承A，实例化B后调用到了A的方法，
此时A的$this指向的还是B这个实际调用的对象，如果B中$this指向的方法或属性不存在，则取A中的指向内容（与static有点类似了）。
* static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
* 静态调用时，static 指向的是实际调用时的类；非静态调用时，static 指向的是实际调用时的对象所属的类。

##### 工作原理

确切地说，static 后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名。

当进行静态方法调用时，该类名（static指向的类名）为明确指定的那个（通常是 :: 运算符的左侧部分），即实际调用时的类。

如上述示例中的：
```
A::test2(); 
B::test2();
```

static 和 self 的区别：
* self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前定义所在的类，这是 self 的限制。
* static 也可以用于访问类的静态属性、静态方法和常量，static 指向的是实际调用时的类。

当进行非静态方法调用时，该类名（static指向的类名）为该对象所属的类，即实际调用时的对象所属的类。

如上述示例中的：
```
(new A)->dd(); 
(new B)->dd();
```

static 和 $this 有点类似，但又有区别：
* $this 指向的对象所属的类和 static 指向的类相同。
* $this 不能用于静态方法中，也不能访问类的静态属性和常量。
* $this 指向的是实际调用的对象。
* static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
* static 指向的是实际调用时的对象所属的类。

##### 转发调用

所谓的转发调用（forwarding call）：指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call() 。
即在进行静态调用时未指名类名的调用属于转发调用。

可用 get_called_class() 函数来获取被调用的方法所在的类名。

以下四种形式的调用，都是转发调用：
* self::
* parent::
* static::
* forward_static_call()

除此之外的调用，就是非转发调用。

##### 非转发调用

非转发调度（non-forwarding call）：非转发调用其实就是明确指定类名的静态调用（foo::bar()）和非静态调用($foo->bar())。
即明确地指定类名的静态调用和非静态调用。

后期静态绑定（Late Static Bindings ）："后期静态绑定"的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。

后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名。

通过具体的类名或具体的对象进行的调用都是非转发调用。

比如：
```
A::test2(); 
B::test2(); 
 
(new A)->dd(); 
(new B)->dd();
```

##### 注意事项

非静态环境下的私有方法的查找顺序

在非静态环境下，在类的非静态方法中，使用 $this 和 static 调用类的私有方法时，执行方式有所不同。
* $this 会优先寻找所在定义范围（父类）中的私有方法，如果存在就调用。
* static 是先到它指向的类（子类）中寻找私有方法，如果找到了就会报错，因为私有方法只能在它所定义的类内部调用；如果没找到，再去所在定义范围（父类）中寻找该私有方法，如果存在就调用。

**具体来说，$this 会先到所在定义范围内(如在父类中则在父类)寻找私有方法，再到它指向的对象所属的类中(当前子类)寻找私有方法，
然后(当前子类)寻找公有方法，最后到所在定义范围内(如在父类中则在父类)寻找公共方法。只要找到了匹配的方法，就调用，并停止查找。**

**而 static 则是先到它指向的类中(当前子类)寻找私有方法，再(当前子类)寻找共有方法；然后到所在定义范围内(如在父类中则在父类)寻找私有方法，
再(如在父类中则在父类)寻找共有方法。只要找到了匹配的方法，就调用，并停止查找。**

下面是一个例子：
```
class  A  {
    private function  foo () {
        var_dump($this); echo '--';
        var_dump(new static); echo '--';
 
        echo __CLASS__; echo '--';
        echo get_called_class();
        echo '<br>';
    }
 
    public function  test () {
        $this -> foo ();
        static:: foo ();
        echo '<br>';
    }
}
 
class  B  extends  A  { }
 
class  C  extends  A  {
    private function foo () {
        echo 'this is C';
    }
}
 
(new  B())->test();
(new  C())->test();
```

输出结果为：
```
object(B)#1 (0) { } --object(B)#2 (0) { } --A--B
object(B)#1 (0) { } --object(B)#2 (0) { } --A--B
 
object(C)#1 (0) { } --object(C)#2 (0) { } --A--C
 
Fatal error: Uncaught Error: Call to private method C::foo() from context 'A'
```

关于后期静态绑定的解析

后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。如果静态调用使用了 parent:: 或者 self:: 等转发调用的形式，将会转发调用信息。

```
class  A  {
    public static function  foo () {
        static:: who ();
    }
 
    public static function  who () {
        echo  __CLASS__ . "\n" ;
    }
}
 
class  B  extends  A  {
    public static function  test () {
        A :: foo ();
        parent :: foo ();
        self :: foo ();
        static::foo();
        forward_static_call(['A', 'foo']);
        echo '<br>';
    }
 
    public static function  who () {
        echo  __CLASS__ . "\n" ;
    }
}
 
class  C  extends  B  {
    public static function  who () {
        echo  __CLASS__ . "\n" ;
    }
 
    public static function test2() {
        self::test();
    }
}
 
class  D  extends  C  {
    public static function  who () {
        echo  __CLASS__ . "\n" ;
    }
}
 
B::foo();
B::test();
 
C::foo();
C::test();
 
D::foo();
D::test2();
```

以上的输出结果为：
```
B A B B B B 
C A C C C C 
D A D D D D
```

**static 后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名**。请记住这句话。

下面的例子是非转发调用。
```
A::foo();  // 输出 A
 
B::foo();   // 输出 B
 
C::foo();   // 输出 C
```

后期静态绑定 static ，是定义在了 foo() 方法中，哪个类通过非转发调用的形式调用 foo() 方法， foo() 方法中的 static 指向的就是哪个类。

但是，如果通过转发调用的形式，调用 foo() 方法，如：
```
parent :: foo ();
self :: foo ();
static::foo();
forward_static_call(['A', 'foo']);
```

那么，就以转发调用代码所在的方法 test() 为准，哪个类通过非转发调用的形式调用 test() 方法， foo() 方法中的 static 指向的就是哪个类。

假如调用 test() 方法时，也采用了转发调用的形式，如：
```
public static function test2() {
    self::test();
}
```

那么，就以 test2() 方法为准 ... 依次类推。

也就是说，在使用了后期静态绑定的基类中，后期静态绑定所在的方法如果被转发调用，则 static 的指向，会一直向上追溯，直到遇到非转发调用的形式。

#### self、static、parent的区别

首先，这个 static 符号跟 static（静态）关键字不是一个东西。这三个符号在PHP对象中共有两种用法:
1. 在类内部，可以使用 new self 、 new static 、 new parent 创建新对象
2. 可以使用 self::、 static::、 parent::调用静态变量和静态或非静态方法。

##### 创建新对象

```
class test{
    public static function test_self(){
        return new self();
    }

    public static function test_static(){
        return new static();
    }

    public static function test_parent(){
        return new parent();
    }
}

class test2 extends test{

    public static function test_parent(){
        return new parent();
    }
}

class test3 extends test2{

}

echo get_class(test::test_self());                  //test
echo get_class(test::test_static());                 //test
echo get_class(test2::test_self());                 //test
echo get_class(test2::test_static());                //test2
echo get_class(test2::test_parent());                //test
echo get_class(test3::test_self());                //test
echo get_class(test3::test_static());                //test3 
echo get_class(test3::test_parent());                //test
```

由以上这个例子可以得出：
* new self 创建的对象 是**定义** new self的类创建的对象
* new static 创建的对象 是**执行** new static的类创建的对象
* new parent 创建的对象 是**定义 new parent 的类**的父类创建的对象（PHP5.3引进）

##### 调用静态变量

概念：
1. 转发调用（forwarding call）:所谓的"转发调用"指的是通过以下几种方式进行的静态调用：
self::、 parent::、 static:: 以及 forward_static_call()，即在进行静态调用时未指名类名的调用属于转发调用。
2. 非转发调度（non-forwarding call）:非转发调用其实就是明确指定类名的静态调用（foo::bar()）和非静态调用($foo->bar())。
即明确地指定类名的静态调用和非静态调用。
3. 后期静态绑定（Late Static Bindings ）："后期静态绑定"的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。

不存在继承的时候，self和static无区别。
* 在静态函数中，self和static可以调用静态属性和静态方法（沒有实例化类，因此不能呼叫非静态的属性和方法）。
* 在非静态函数中, self和static可以调用非静态属性和非静态方法。

```
class Demo
{     
    public static $static;     
    public $Nostatic;      
    
    public function __construct(){         
        self::$static = "static";         
        $this->Nostatic = "Nostatic";
    }
    
    public static function get(){
        return __CLASS__;
    }
    
    public function show(){
        // 注意，$this不能使用在static静态方法中
        return "this is function show with ".$this->Nostatic;
    }
    
    public function test() {
        echo Demo::$static."\n";                //使用类型调用静态属性     static
        echo Demo::get()."\n";                  //使用类名调用静态方法    Demo
        echo Demo::show()."\n";    //使用类名调用非静态方法 this is function show with Nostatic
        echo self::$static."\n";                 //self调用静态属性       static
        echo self::get()."\n";                  //self调用静态方法      Demo
        echo self::show()."\n";     //self调用非静态方法 this is function show with Nostatic
        echo static::$static."\n";               //static调用静态属性     static
        echo static::get()."\n";                //static调用静态方法    Demo
        echo static::show()."\n";   //static调用非静态方法 this is function show with Nostatic
    }
}

$obj = new Demo();
$obj->test();
```

这里发现使用类名通过 :: 直接调用类的非静态方法，也是挺神奇。但是不建议使用类名::直接调用类的非静态方法，PHP这样设计的初衷不是这样使用的，
具体原因可以参考下[这里](http://www.laruence.com/2012/06/14/2628.html) 。 看一下这个熟悉不：
```
class A {
    public function __construct() {
    }
}
class B extends A {
    public function __construct() {
        parent::__construct();
    }
}
```

存在继承关系的时候
* self调用的方法和属性始终表示当前类的方法和属性
* static调用的方法和属性为当前执行的类的方法和属性
* parent调用的方法和属性为当前类的父类的方法和属性

```
class A{
    public static $test = "AAA";
    
    public static function getClassName(){
        return "A";
    }
    
    public function getClassName2(){
        return "AA";
    }
    
    public static function testSelf(){
        echo self::getClassName();
        echo "\n";
        echo self::getClassName2();  
        echo "\n";
        echo self::$test;
    }
    
    public static function testStatic(){
        echo static::getClassName();
        echo "\n";
        echo static::getClassName2();
        echo "\n";
        echo static::$test;
    }
}
    
class B extends A{
    public static $test = "BBB";
    
    public static function getClassName(){
        return "B";
    }
    
    public function getClassName2(){
        return "BB";
    }
    
    public static function testParent(){
        echo parent::getClassName();
        echo "\n";
        echo parent::getClassName2();
        echo "\n";
        echo parent::$test;
    }
}
    
class C extends B{
    public static $test = "CCC";
    
    public static function getClassName(){
        return "C";
    }
    
    public function getClassName2(){
        return "CC";
    }
    
    public static function testParent(){
        echo parent::getClassName();
        echo "\n";
        echo parent::getClassName2();
        echo "\n";
        echo parent::$test;
    }
}

B::testSelf();                  // A AA AAA
echo "\n";
B::testStatic();                 // B BB BBB
echo "\n";
B::testParent();                 // A AA AAA
echo "\n";
C::testSelf();                  // A AA AAA
echo "\n";
C::testStatic();                  // C CC CCC
echo "\n";
C::testParent();                  // B BB BBB
```

再看一下手册里的例子：
```
class A {     
   public static function foo() {         
        static::who();     
   }     
   public static function who() {         
       echo __CLASS__."\n";     
   } 
} 

class B extends A {     
   public static function test() {         
       A::foo();         
       parent::foo();         
       self::foo();     
   }     
   public static function who() {         
        echo __CLASS__."\n";     
    } 
} 

class C extends B {     
    public static function who() {         
       echo __CLASS__."\n";     
    }
} 

C::test(); 
```

最后输出为 A C C

单独拿出进行分析
```
public static function test() {         
   A::foo();         
   parent::foo();         
   self::foo();     
} 
```

1. A::foo()：非转发请求，直接调用A的foo()方法，在任何地方调用结果都是一样的
2. parent::foo():在B类中写着，调用B的父类A的方法foo()（parent的用法）；A类中的foo()中执行 static::who()，
寻找上一个非转发请求的类名（在A类的foo()方法中写上get_called_class()，可得为C，由此可知就是当前执行的类），
所以调用C类的who()方法（这一步就可以理解为后期静态绑定，即为static的用法）；C类中重写了who()方法，所以结果为C；
如果去掉C类中的who()方法，会调用B类中的who()方法；如果再去掉B类中的who()方法，会调用A类中的who()方法。
3. self::foo():执行B类中的foo()方法（self的用法），B类中没有foo()方法，于是继承了A类的foo()方法，如果B类中定义了foo()方法,
则执行B类中的foo()方法;执行A类的foo()方法，如上.



<br/><br/><br/><br/><br/>
### 参考资料

php类中的$this，static，const，self这几个关键字使用方法 <https://www.cnblogs.com/freelyflying/p/7008072.html>

PHP 中 self、static、$this 的区别 & 后期静态绑定详解 <https://blog.csdn.net/lamp_yang_3533/article/details/79912453>

PHP static延迟静态绑定 <https://ibaiyang.github.io/blog/php/2017/09/12/PHP-static%E5%BB%B6%E8%BF%9F%E9%9D%99%E6%80%81%E7%BB%91%E5%AE%9A.html>

php中static 静态关键字 <https://www.cnblogs.com/haizizhu/p/8040236.html>

PHP中静态方法和实例化方法的区别 <https://www.cnblogs.com/-mrl/p/6485616.html>

PHP常见概念混淆（七）之self、static、parent的区别 <https://www.cnblogs.com/qiye5757/p/9437971.html>

PHP 手册 函数参考 变量与类型相关扩展 函数处理 函数处理 函数 forward_static_call <https://www.php.net/manual/zh/function.forward-static-call.php>

PHP的Calling Scope <http://www.laruence.com/2012/06/14/2628.html>

这是php中__call和__callStatic在被继承后会产生的bug? <https://segmentfault.com/q/1010000000095833>




