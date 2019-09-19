---
layout: post
categories: PHP
title: PHP stdClass是什么
meta: stdClass是什么
---
* content
{:toc}

### 正文

有时候我们会在项目中看到这样的代码：
```
$user = new stdClass();
$user->name = 'huanghe';
```

这样的代码的意义是什么？stdClass又是什么类？

我们查手册，发现基本没有什么介绍，只说是 由一些内建在 PHP 中的标准函数定义 ，再就找不到更详细的介绍了。

翻了下其他资料。stdClass在PHP5才开始被流行。而stdClass也是zend的一个保留类。
这个类有一个特殊的地方，就是没有方法，所以不会出现调用它的方法的用法。
不过可以定义属性，也可以访问定义的属性。

或者，我们可以又这么理解一下，正因为PHP5的对象的独特性，对象在任何地方被调用，都是引用地址型的，所以相对消耗的资源会少一点。
在其它页面为它赋值时是直接修改，而不是引用一个拷贝。例如：
```
$user = new stdClass();
$user->name = 'huanghe';
$myUser = $user;
$myUser->name = 'changjiang';

echo $user->name;  // 输出 changjiang
```

如果在PHP4时代，这样的代码就是在消耗系统资源。因为：

$myUser = $user;

这是创建了一个拷贝。所以，在PHP4的时候，都是这样使用：

$myUser = & $user;

手册中说 stdClass由内建在 PHP 中的标准函数定义，那我们看一下 标准函数 是什么使用stdClass的。

#### 转换为对象

如果将一个对象转换成对象，它将不会有任何变化。如果其它任何类型的值被转换成对象，将会创建一个内置类 stdClass 的实例。

如果该值为 NULL，则新的实例为空。 
```
$obj = (object) [];
var_dump($obj);

// 输出
object(stdClass)#1 (0) {
}
```

array 转换成 object 将使键名成为属性名并具有相对应的值。
```
$obj = (object) array('a' => 'A');
echo $obj->a;  // 输出 A
echo $obj->{'a'};  // 输出 A  
```

```
$obj = (object) array('a' => 'A');
var_dump($obj);

//输出 
object(stdClass)#1 (1) {
  ["a"]=>
  string(1) "A"
}
```

上面可以看到 stdClass 的身影。可以说 stdClass 就是 (object) 标准函数来定义的。

注意：在这个例子里， 使用 PHP 7.2.0 之前的版本，数字键只能通过迭代访问。 
```
$obj = (object) array('1' => 'foo');
var_dump(isset($obj->{'1'}));  // PHP 7.2.0 后输出 'bool(true)'，之前版本会输出 'bool(false)' 
var_dump(key($obj));  // PHP 7.2.0 后输出 'string(1) "1"'，之前版本输出  'int(1)' 
```

对于其他值，会包含进成员变量名 scalar。 
```
$obj = (object) 'abc';
echo $obj->scalar;  // 输出 abc
```

#### 空对象

php创建空对象的三种方法：

```
$obj1 = new \stdClass; // Instantiate stdClass object
$obj2 = new class{}; // Instantiate anonymous class
$obj3 = (object)[]; // Cast empty array to object

var_dump($obj1); // object(stdClass)#1 (0) {}
var_dump($obj2); // object(class@anonymous)#2 (0) {}
var_dump($obj3); // object(stdClass)#3 (0) {}
```

$obj1 和 $obj3 是相同的类型, 但是 $obj1 !== $obj3。 但是这三个在经过json_encode()后都成为简单的js对象:
```
echo json_encode([
    new \stdClass,
    new class{},
    (object)[],
]);

输出：  [{},{},{}]
```

#### 对象转为数组

上面说了数组转为对象，这里说一下对象转为数组，对象转为数组可以使用 (array) 函数。

```
$user = new stdClass();
$user->name = 'huanghe';

$user_array = (array) $user;

var_dump($user_array);

// 输出
array(1) {
  ["name"]=>
  string(7) "huanghe"
}
```


#### 思考

你可能会问为什么一定要用对象呢，PHP中使用数组不是更方便吗。确实。数组在程序的使用中应该是最方便的，
然而数组的每次被引用（$a = $b），其实都是创建了一个副本，而且，数组被unset后，
还是占用了内存（释放的内存存到php缓存区中，避免新的变量再去申请新的内存空间，直接使用php缓存区的内存）。
不过SPL的标准类库里，还有一个函数ArrayObject()，可以直接将数组转化为对象这也是一个好办法。


<br/><br/><br/><br/><br/>
### 参考资料

PHP 手册 附录 保留字列表 预定义类 <https://www.php.net/manual/zh/reserved.classes.php>

PHP 手册 语言参考 类型 Object对象 转换为对象 <www.php.net/manual/zh/language.types.object.php#language.types.object.casting>

理解PHP中的stdClass类 <https://www.jianshu.com/p/e4b4ed61e7b5>

