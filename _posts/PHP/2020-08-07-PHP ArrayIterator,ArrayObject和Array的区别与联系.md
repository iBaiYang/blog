---
layout: post
categories: PHP
title: PHP ArrayIterator,ArrayObject和Array的区别与联系
meta: PHP ArrayIterator,ArrayObject和Array的区别与联系
---
* content
{:toc}

## 正文

`Array`是php类型。您可以使用php语言结构`array()`创建一个，或者从php 5.4开始用`[]` 。

`ArrayObject`是`object`，其工作方式与数组完全相同，可以使用`new`关键字创建这些内容。

`ArrayIterator`与`ArrayObject`类似，但它可以自行迭代，也是使用`new`创建的。

### Array

Array是PHP的类型：
```
namespace {

    /**
     * Creates an array.
     * @link https://php.net/manual/en/function.array.php
     * @param mixed ...$_ [optional] <p>
     * Syntax "index => values", separated by commas, define index and values.
     * index may be of type string or integer. When index is omitted, an integer index is automatically generated,
     * starting at 0. If index is an integer, next generated index will be the biggest integer index + 1.
     * Note that when two identical index are defined, the last overwrite the first.
     * </p>
     * <p>
     * Having a trailing comma after the last defined array entry, while unusual, is a valid syntax.
     * </p>
     * @return array an array of the parameters. The parameters can be given an index with the => operator.
     */
    function PS_UNRESERVE_PREFIX_array(...$_){};
}
```

array() 是一个语言结构，用于字面上表示数组，不是常规的函数。

使用示例：
```php
<?php
$fruits = array (
    "fruits"  => array("a" => "orange", "b" => "banana", "c" => "apple"),
    "numbers" => array(1, 2, 3, 4, 5, 6),
    "holes"   => array("first", 5 => "second", "third")
);

echo $fruits["fruits"]["b"];
```

### ArrayObject

Spl 中 ArrayObject类：
```
/**
 * This class allows objects to work as arrays.
 * @link https://php.net/manual/en/class.arrayobject.php
 */
class ArrayObject implements IteratorAggregate, ArrayAccess, Serializable, Countable {
    /**
     * Properties of the object have their normal functionality when accessed as list (var_dump, foreach, etc.).
     */
    const STD_PROP_LIST = 1;

    /**
     * Entries can be accessed as properties (read and write).
     */
    const ARRAY_AS_PROPS = 2;


    /**
     * Construct a new array object
     * @link https://php.net/manual/en/arrayobject.construct.php
     * @param array|object $array The input parameter accepts an array or an Object.
     * @param int $flags Flags to control the behaviour of the ArrayObject object.
     * @param string $iteratorClass Specify the class that will be used for iteration of the ArrayObject object. ArrayIterator is the default class used.
     *
     */
    public function __construct($array = array(), $flags = 0, $iteratorClass = "ArrayIterator") { }

    /**
     * Returns whether the requested index exists
     * @link https://php.net/manual/en/arrayobject.offsetexists.php
     * @param mixed $key <p>
     * The index being checked.
     * </p>
     * @return bool true if the requested index exists, otherwise false
     */
    public function offsetExists($key) { }

    /**
     * Returns the value at the specified index
     * @link https://php.net/manual/en/arrayobject.offsetget.php
     * @param mixed $key <p>
     * The index with the value.
     * </p>
     * @return mixed|false The value at the specified index or false.
     */
    public function offsetGet($key) { }

    /**
     * Sets the value at the specified index to newval
     * @link https://php.net/manual/en/arrayobject.offsetset.php
     * @param mixed $key <p>
     * The index being set.
     * </p>
     * @param mixed $value <p>
     * The new value for the <i>index</i>.
     * </p>
     * @return void
     */
    public function offsetSet($key, $value) { }

    /**
     * Unsets the value at the specified index
     * @link https://php.net/manual/en/arrayobject.offsetunset.php
     * @param mixed $key <p>
     * The index being unset.
     * </p>
     * @return void
     */
    public function offsetUnset($key) { }

    /**
     * Appends the value
     * @link https://php.net/manual/en/arrayobject.append.php
     * @param mixed $value <p>
     * The value being appended.
     * </p>
     * @return void
     */
    public function append($value) { }

    /**
     * Creates a copy of the ArrayObject.
     * @link https://php.net/manual/en/arrayobject.getarraycopy.php
     * @return array a copy of the array. When the <b>ArrayObject</b> refers to an object
     * an array of the public properties of that object will be returned.
     */
    public function getArrayCopy() { }

    /**
     * Get the number of public properties in the ArrayObject
     * When the <b>ArrayObject</b> is constructed from an array all properties are public.
     * @link https://php.net/manual/en/arrayobject.count.php
     * @return int The number of public properties in the ArrayObject.
     */
    public function count() { }

    /**
     * Gets the behavior flags.
     * @link https://php.net/manual/en/arrayobject.getflags.php
     * @return int the behavior flags of the ArrayObject.
     */
    public function getFlags() { }

    /**
     * Sets the behavior flags.
     * @link https://php.net/manual/en/arrayobject.setflags.php
     * @param int $flags <p>
     * The new ArrayObject behavior.
     * It takes on either a bitmask, or named constants. Using named
     * constants is strongly encouraged to ensure compatibility for future
     * versions.
     * </p>
     * <p>
     * The available behavior flags are listed below. The actual
     * meanings of these flags are described in the
     * predefined constants.
     * <table>
     * ArrayObject behavior flags
     * <tr valign="top">
     * <td>value</td>
     * <td>constant</td>
     * </tr>
     * <tr valign="top">
     * <td>1</td>
     * <td>
     * ArrayObject::STD_PROP_LIST
     * </td>
     * </tr>
     * <tr valign="top">
     * <td>2</td>
     * <td>
     * ArrayObject::ARRAY_AS_PROPS
     * </td>
     * </tr>
     * </table>
     * </p>
     * @return void
     */
    public function setFlags($flags) { }

    /**
     * Sort the entries by value
     * @link https://php.net/manual/en/arrayobject.asort.php
     * @param int $flags [optional]
     * @return void
     */
    public function asort($flags = SORT_REGULAR) { }

    /**
     * Sort the entries by key
     * @link https://php.net/manual/en/arrayobject.ksort.php
     * @param int $flags [optional]
     * @return void
     */
    public function ksort($flags = SORT_REGULAR) { }

    /**
     * Sort the entries with a user-defined comparison function and maintain key association
     * @link https://php.net/manual/en/arrayobject.uasort.php
     * @param callback $callback <p>
     * Function <i>cmp_function</i> should accept two
     * parameters which will be filled by pairs of entries.
     * The comparison function must return an integer less than, equal
     * to, or greater than zero if the first argument is considered to
     * be respectively less than, equal to, or greater than the
     * second.
     * </p>
     * @return void
     */
    public function uasort($callback) { }

    /**
     * Sort the entries by keys using a user-defined comparison function
     * @link https://php.net/manual/en/arrayobject.uksort.php
     * @param callback $callback <p>
     * The callback comparison function.
     * </p>
     * <p>
     * Function <i>cmp_function</i> should accept two
     * parameters which will be filled by pairs of entry keys.
     * The comparison function must return an integer less than, equal
     * to, or greater than zero if the first argument is considered to
     * be respectively less than, equal to, or greater than the
     * second.
     * </p>
     * @return void
     */
    public function uksort($callback) { }

    /**
     * Sort entries using a "natural order" algorithm
     * @link https://php.net/manual/en/arrayobject.natsort.php
     * @return void
     */
    public function natsort() { }

    /**
     * Sort an array using a case insensitive "natural order" algorithm
     * @link https://php.net/manual/en/arrayobject.natcasesort.php
     * @return void
     */
    public function natcasesort() { }

    /**
     * Unserialize an ArrayObject
     * @link https://php.net/manual/en/arrayobject.unserialize.php
     * @param string $data <p>
     * The serialized <b>ArrayObject</b>.
     * </p>
     * @return void The unserialized <b>ArrayObject</b>.
     */
    public function unserialize($data) { }

    /**
     * Serialize an ArrayObject
     * @link https://php.net/manual/en/arrayobject.serialize.php
     * @return string The serialized representation of the <b>ArrayObject</b>.
     */
    public function serialize() { }

    /**
     * @return array
     * @since 7.4
     */
    public function __debugInfo(){}


    /**
     * @return array
     * @since 7.4
     */
    public function __serialize(): array {}

    /**
     * @param array $data
     * @since 7.4
     */
    public function __unserialize(array $data): void {}

    /**
     * Create a new iterator from an ArrayObject instance
     * @link https://php.net/manual/en/arrayobject.getiterator.php
     * @return ArrayIterator An iterator from an <b>ArrayObject</b>.
     */
    public function getIterator() { }

    /**
     * Exchange the array for another one.
     * @link https://php.net/manual/en/arrayobject.exchangearray.php
     * @param mixed $array <p>
     * The new array or object to exchange with the current array.
     * </p>
     * @return array the old array.
     */
    public function exchangeArray($array) { }

    /**
     * Sets the iterator classname for the ArrayObject.
     * @link https://php.net/manual/en/arrayobject.setiteratorclass.php
     * @param string $iteratorClass <p>
     * The classname of the array iterator to use when iterating over this object.
     * </p>
     * @return void
     */
    public function setIteratorClass($iteratorClass) { }

    /**
     * Gets the iterator classname for the ArrayObject.
     * @link https://php.net/manual/en/arrayobject.getiteratorclass.php
     * @return string the iterator class name that is used to iterate over this object.
     */
    public function getIteratorClass() { }
}
```

ArrayObject允许对象以数组方式工作。

使用示例：
```php
<?php                                                    
$a = new ArrayObject(array(), ArrayObject::STD_PROP_LIST);
    $a['arr'] = 'array data';                            
    $a->prop = 'prop data';                              
$b = new ArrayObject();                                  
    $b['arr'] = 'array data';                            
    $b->prop = 'prop data';                              
                                                                                                          
print_r($a);                                             
//ArrayObject Object
//(
//    [prop] => prop data
//    [storage:ArrayObject:private] => Array
//        (
//            [arr] => array data
//        )
//
//)                                                     
                                               
print_r($b); 
//ArrayObject Object
//(
//    [prop] => prop data
//    [storage:ArrayObject:private] => Array
//        (
//            [arr] => array data
//        )
//
//)
```

### ArrayIterator

Spl 中 ArrayIterator类：
```
/**
 * This iterator allows to unset and modify values and keys while iterating
 * over Arrays and Objects.
 * @link https://php.net/manual/en/class.arrayiterator.php
 */
class ArrayIterator implements SeekableIterator, ArrayAccess, Serializable, Countable {
    const STD_PROP_LIST = 1;
    const ARRAY_AS_PROPS = 2;


    /**
     * Construct an ArrayIterator
     * @link https://php.net/manual/en/arrayiterator.construct.php
     * @param array $array The array or object to be iterated on.
     * @param int $flags Flags to control the behaviour of the ArrayObject object.
     * @see ArrayObject::setFlags()
     */
    public function __construct($array = array(), $flags = 0) { }

    /**
     * Check if offset exists
     * @link https://php.net/manual/en/arrayiterator.offsetexists.php
     * @param string $key <p>
     * The offset being checked.
     * </p>
     * @return bool true if the offset exists, otherwise false
     */
    public function offsetExists($key) { }

    /**
     * Get value for an offset
     * @link https://php.net/manual/en/arrayiterator.offsetget.php
     * @param string $key <p>
     * The offset to get the value from.
     * </p>
     * @return mixed The value at offset <i>index</i>.
     */
    public function offsetGet($key) { }

    /**
     * Set value for an offset
     * @link https://php.net/manual/en/arrayiterator.offsetset.php
     * @param string $key <p>
     * The index to set for.
     * </p>
     * @param string $value <p>
     * The new value to store at the index.
     * </p>
     * @return void
     */
    public function offsetSet($key, $value) { }

    /**
     * Unset value for an offset
     * @link https://php.net/manual/en/arrayiterator.offsetunset.php
     * @param string $key <p>
     * The offset to unset.
     * </p>
     * @return void
     */
    public function offsetUnset($key) { }

    /**
     * Append an element
     * @link https://php.net/manual/en/arrayiterator.append.php
     * @param mixed $value <p>
     * The value to append.
     * </p>
     * @return void
     */
    public function append($value) { }

    /**
     * Get array copy
     * @link https://php.net/manual/en/arrayiterator.getarraycopy.php
     * @return array A copy of the array, or array of public properties
     * if ArrayIterator refers to an object.
     */
    public function getArrayCopy() { }

    /**
     * Count elements
     * @link https://php.net/manual/en/arrayiterator.count.php
     * @return int The number of elements or public properties in the associated
     * array or object, respectively.
     */
    public function count() { }

    /**
     * Get flags
     * @link https://php.net/manual/en/arrayiterator.getflags.php
     * @return string The current flags.
     */
    public function getFlags() { }

    /**
     * Set behaviour flags
     * @link https://php.net/manual/en/arrayiterator.setflags.php
     * @param string $flags <p>
     * A bitmask as follows:
     * 0 = Properties of the object have their normal functionality
     * when accessed as list (var_dump, foreach, etc.).
     * 1 = Array indices can be accessed as properties in read/write.
     * </p>
     * @return void
     */
    public function setFlags($flags) { }

    /**
     * Sort array by values
     * @link https://php.net/manual/en/arrayiterator.asort.php
     * @param int $flags [optional]
     * @return void
     */
    public function asort($flags = SORT_REGULAR) { }

    /**
     * Sort array by keys
     * @link https://php.net/manual/en/arrayiterator.ksort.php
     * @param int $flags [optional]
     * @return void
     */
    public function ksort($flags = SORT_REGULAR) { }

    /**
     * User defined sort
     * @link https://php.net/manual/en/arrayiterator.uasort.php
     * @param callable $callback <p>
     * The compare function used for the sort.
     * </p>
     * @return void
     */
    public function uasort($callback) { }

    /**
     * User defined sort
     * @link https://php.net/manual/en/arrayiterator.uksort.php
     * @param callable $callback <p>
     * The compare function used for the sort.
     * </p>
     * @return void
     */
    public function uksort($callback) { }

    /**
     * Sort an array naturally
     * @link https://php.net/manual/en/arrayiterator.natsort.php
     * @return void
     */
    public function natsort() { }

    /**
     * Sort an array naturally, case insensitive
     * @link https://php.net/manual/en/arrayiterator.natcasesort.php
     * @return void
     */
    public function natcasesort() { }

    /**
     * Unserialize
     * @link https://php.net/manual/en/arrayiterator.unserialize.php
     * @param string $data <p>
     * The serialized ArrayIterator object to be unserialized.
     * </p>
     * @return string The <b>ArrayIterator</b>.
     */
    public function unserialize($data) { }

    /**
     * Serialize
     * @link https://php.net/manual/en/arrayiterator.serialize.php
     * @return string The serialized <b>ArrayIterator</b>.
     */
    public function serialize() { }

    /**
     * Rewind array back to the start
     * @link https://php.net/manual/en/arrayiterator.rewind.php
     * @return void
     */
    public function rewind() { }

    /**
     * Return current array entry
     * @link https://php.net/manual/en/arrayiterator.current.php
     * @return mixed The current array entry.
     */
    public function current() { }

    /**
     * Return current array key
     * @link https://php.net/manual/en/arrayiterator.key.php
     * @return string|float|int|bool|null The current array key.
     */
    public function key() { }

    /**
     * Move to next entry
     * @link https://php.net/manual/en/arrayiterator.next.php
     * @return void
     */
    public function next() { }

    /**
     * Check whether array contains more entries
     * @link https://php.net/manual/en/arrayiterator.valid.php
     * @return bool
     */
    public function valid() { }

    /**
     * Seek to position
     * @link https://php.net/manual/en/arrayiterator.seek.php
     * @param int $offset <p>
     * The position to seek to.
     * </p>
     * @return void
     */
    public function seek($offset) { }

    /**
     * @return array
     * @since 7.4
     */
    public function __debugInfo(){}


    /**
     * @return array
     * @since 7.4
     */
    public function __serialize(): array {}

    /**
     * @param array $data
     * @since 7.4
     */
    public function __unserialize(array $data): void {}

}

/**
 * This iterator allows to unset and modify values and keys while iterating over Arrays and Objects
 * in the same way as the ArrayIterator. Additionally it is possible to iterate
 * over the current iterator entry.
 * @link https://php.net/manual/en/class.recursivearrayiterator.php
 */
class RecursiveArrayIterator extends ArrayIterator implements RecursiveIterator {
    const CHILD_ARRAYS_ONLY = 4;


    /**
     * Returns whether current entry is an array or an object.
     * @link https://php.net/manual/en/recursivearrayiterator.haschildren.php
     * @return bool true if the current entry is an array or an object,
     * otherwise false is returned.
     */
    public function hasChildren() { }

    /**
     * Returns an iterator for the current entry if it is an array or an object.
     * @link https://php.net/manual/en/recursivearrayiterator.getchildren.php
     * @return RecursiveArrayIterator An iterator for the current entry, if it is an array or object.
     */
    public function getChildren() { }
}
```

这个迭代器允许在遍历数组和对象时删除和更新值与键。

使用示例：
```php
<?php
// 在迭代到值时，需要一个回调函数做处理后返回，可以使用如下示例
class ArrayCallbackIterator extends ArrayIterator {
  private $callback;
  public function __construct($value, $callback) {
    parent::__construct($value);
    $this->callback = $callback;
  }

  public function current() {
    $value = parent::current();
    return call_user_func($this->callback, $value);
  }
} 

$iterator1 = new ArrayCallbackIterator($valueList, "callback_function");
$iterator2 = new ArrayCallbackIterator($valueList, array($object, "callback_class_method")); 
```

再看一个示例：
```php
<?php
$fruits = array(
    "apple" => "yummy",
    "orange" => "ah ya, nice",
    "grape" => "wow, I love it!",
    "plum" => "nah, not me"
);
$obj = new ArrayObject( $fruits );
$it = $obj->getIterator();

// How many items are we iterating over?

echo "Iterating over: " . $obj->count() . " values\n";

// Iterate over the values in the ArrayObject:
while( $it->valid() )
{
    echo $it->key() . "=" . $it->current() . "\n";
    $it->next();
}

// The good thing here is that it can be iterated with foreach loop

foreach ($it as $key=>$val) {
    echo $key.":".$val."\n";
}
```

`ArrayObject::getIterator`从ArrayObject实例创建新迭代器。

输出：
```
Iterating over: 4 values
apple=yummy
orange=ah ya, nice
grape=wow, I love it!
plum=nah, not me
```

### 比较Array 与（ArrayObject / ArrayIterator）

它们都可以使用php的数组语法来使用，例如：
```
$array[] = 'foo';
$object[] = 'foo';
// adds new element with the expected numeric key

$array['bar'] = 'foo';
$object['bar'] = 'foo';
// adds new element with the key "bar"

foreach($array as $value);
foreach($object as $value);
// iterating over the elements
```

但是，它们仍然是对象与数组，因此您会注意到其中的差异：
```
is_array($array); // true
is_array($object); // false

is_object($array); // false
is_object($object); // true
```

大多数php数组函数都需要数组，因此使用对象会产生错误。有很多这样的功能，例如：
```
sort($array); // works as expected
sort($object); // Warning: sort() expects parameter 1 to be array, object given in ......
```

最后，对象可以执行您对stdClass对象的期望，即使用对象语法访问公共属性
```
$object->foo = 'bar'; // works
$array->foo = 'bar'; // Warning: Attempt to assign property of non-object in ....
```

数组（作为本机类型）比对象快得多。另一方面，ArrayObject、ArrayIterator类定义了您可以使用的某些方法，而数组则没有。

### 比较ArrayObject与ArrayIterator

这两个方面的主要区别在于类的方法。

ArrayIterator实现Iterator接口，该接口为其提供与迭代/循环元素相关的方法。 
ArrayObject有一个名为exchangeArray的方法，它将其内部数组与其他数组交换。
在ArrayIterator中实现类似的东西意味着要么创建一个新对象，要么循环键入他们一个一个然后逐个设置新数组的元素。

接下来，由于ArrayObject无法迭代，因此当您在foreach中使用它时，它会在内部创建一个ArrayIterator对象（与数组相同）。
这意味着php会创建原始数据的副本。现在有2个具有相同内容的对象。对于大型阵列来说，这将被证明是低效的。
但是，您可以指定要用于迭代器的类，因此您可以在代码中使用自定义迭代器。

## 参考资料

PHP 手册 函数参考 变量与类型相关扩展 数组 数组 函数 array <https://www.php.net/manual/zh/function.array.php>

PHP 手册 函数参考 其它基本扩展 SPL 各种类及接口 The ArrayObject class <https://www.php.net/manual/zh/class.arrayobject.php>

PHP 手册 函数参考 其它基本扩展 SPL 迭代器 ArrayIterator 类 <https://www.php.net/manual/zh/class.arrayiterator.php>

PHP中ArrayIterator,ArrayObject和Array之间的区别 <http://www.voidcn.com/article/p-ytcfleff-btd.html>

PHP中ArrayIterator，ArrayObject和Array之间的区别 <https://www.thinbug.com/q/10502719>


