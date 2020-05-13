---
layout: post
categories: JavaScript
title: JavaScript全局变量
meta: JavaScript全局变量的使用和说明
---
* content
{:toc}

### 正文

JavaScript全局变量 和 JavaScript全局属性 不是一个概念。

JavaScript全局变量 是我们定义的全局通用变量，而 JavaScript全局属性 是JavaScript原生定义的全局属性。

JavaScript 使用函数管理作用域。变量在函数内声明，只在函数内有效，不能在外部使用。
全局变量与之相反，在函数外部声明，在函数内无需声明即可简单地使用。

#### 创建全局变量的方式

有两种创建方式。

方式1，在函数外声明变量：
```
var global1 = "global1";
```

方式2，函数内不使用 var 关键字声明变量：
```
function func() {
    global2 = "global2";
}
```

对比示例：
```
console.log(window);            // window 对象
console.log(this);              // window 对象
console.log(window === this);   // true

// 创建全局变量的方式：
// 方式一：在函数外声明变量
var global1 = "global1";
// 方式二：函数内不使用 var 关键字声明变量
function func() {
    global2 = "global2";
}
func();
console.log(window);             // window 对象，并且该对象上多了 global1、global2 两个属性
console.log(window.global1);     // global1
console.log(window['global2']);  // global2
```

#### 全局对象

每一个 JavaScript 环境都有全局对象，可以在函数外部使用 this 进行访问。创建的每一个全局变量都为全局对象所有。

```
var name = 'spirit';
console.log(this);      // Window 对象，并且该对象上多了一个 name 属性
console.log(this.name); // spirit
```

在浏览器中，为了方便，使用 window 表示全局对象本身。

```
console.log(window);            // window 对象
console.log(this);              // window 对象
console.log(window === this);   // true

// 创建全局变量的方式：
// 方式一：在函数外声明变量
var global1 = "global1";
// 方式二：函数内不使用 var 关键字声明变量
function func() {
    global2 = "global2";
}
func();
console.log(window);             // window 对象，并且该对象上多了 global1、global2 两个属性
console.log(window.global1);     // global1
```

#### 全局变量的优点

1、可以减少变量的个数，减少由于实际参数和形式参数的数据传递带来的时间消耗。

#### 全局变量的缺点

1、变量命名冲突：问题在于全局变量在整个 JavaScript 应用或 Web 页面内共享。它们生存于同一个全局命名空间内，总有可能发生命名冲突。
譬如当一个应用程序中两个独立的部分定义了同名的全局变量，但却有不同目的时。

2、变量释放时的副作用：使用 var 关键字声明的全局变量，不能删除；不使用 var 关键字声明的隐含全局变量，可以删除。
这表明隐含全局变量严格来讲不是真正的变量，而是全局对象的属性。属性可以通过 delete 操作符删除，而变量不行。

3、全局变量保存在静态存贮区，程序开始运行时为其分配内存，程序结束释放该内存。
与局部变量的动态分配、动态释放相比，生存期比较长，因此过多的全局变量会占用较多的内存单元。

4、全局变量破坏了函数的封装性能。函数象一个黑匣子，一般是通过函数参数和返回值进行输入输出，函数内部实现相对独立。
但函数中如果使用了全局变量，那么函数体内的语句就可以绕过函数参数和返回值进行存取，这种情况破坏了函数的独立性，
使函数对全局变量产生依赖。同时，也降低了该函数的可移植性。

5、全局变量使函数的代码可读性降低。由于多个函数都可能使用全局变量，函数执行时全局变量的值可能随时发生变化，
对于程序的查错和调试都非常不利。

因此，如果不是万不得已，最好不要使用全局变量。

#### JavaScript 全局属性

属性 	描述

Infinity 	代表正的无穷大的数值。

NaN 	指示某个值是不是数字值。

undefined 	指示未定义的值。

#### JavaScript 全局函数

函数 	描述

decodeURI() 	解码某个编码的 URI。

decodeURIComponent() 	解码一个编码的 URI 组件。

encodeURI() 	把字符串编码为 URI。

encodeURIComponent() 	把字符串编码为 URI 组件。

escape() 	对字符串进行编码。

eval() 	计算 JavaScript 字符串，并把它作为脚本代码来执行。

isFinite() 	检查某个值是否为有穷大的数。

isNaN() 	检查某个值是否是数字。

Number() 	把对象的值转换为数字。

parseFloat() 	解析一个字符串并返回一个浮点数。

parseInt() 	解析一个字符串并返回一个整数。

String() 	把对象的值转换为字符串。

unescape() 	对由 escape() 编码的字符串进行解码。


<br/><br/><br/><br/><br/>
### 参考资料 

JavaScript 全局 <https://www.runoob.com/jsref/jsref-obj-global.html>

JavaScript 尽量少用全局变量 <https://blog.csdn.net/weixin_43378396/article/details/86029169>



