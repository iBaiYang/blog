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
```
// 明确定义的全局变量
var global1 = "global1";
// 隐含全局变量
function func() {
    global2 = "global2";
}
func();
// 删除这两个全局变量
delete global1;
delete global2;

// 测试删除情况
console.log(typeof global1);    // string
console.log(typeof global2);    // undefined
```

3、全局变量保存在静态存贮区，程序开始运行时为其分配内存，程序结束释放该内存。
与局部变量的动态分配、动态释放相比，生存期比较长，因此过多的全局变量会占用较多的内存单元。

4、全局变量破坏了函数的封装性能。函数象一个黑匣子，一般是通过函数参数和返回值进行输入输出，函数内部实现相对独立。
但函数中如果使用了全局变量，那么函数体内的语句就可以绕过函数参数和返回值进行存取，这种情况破坏了函数的独立性，
使函数对全局变量产生依赖。同时，也降低了该函数的可移植性。

5、全局变量使函数的代码可读性降低。由于多个函数都可能使用全局变量，函数执行时全局变量的值可能随时发生变化，
对于程序的查错和调试都非常不利。

因此，如果不是万不得已，最好不要使用全局变量。

看例子：
```
function func() {
    var a = b = 0;
    /*
     * a 为局部变量，b 为全局变量
     * 产生的原因很简单：从右到左的操作符优先级。先执行 b = 0，而此刻 b 未声明，
     * 因此 b 为全局变量。整个过程等价于：
     * var a;
     * b = 0;
     * a = b;
     */
}
// 正确的做法：
function func() {
    var a, b;
    a = b = 0;
}
```

```
myName = "global"; // 全局变量 
function func() {
    alert(myName); // "undefined" 
    var myName = "local"; 
    alert(myName); // 局部变量 
} 
func(); 
 
// 前面代码片段运行结果等同于下述代码 

myName = "global"; // 全局变量 
function func() { 
    var myName; // 等同于 -> var myName = undefined 
    alert(myName); // undefined 
    myName = "local"; 
    alert(myName); // 局部变量 
} 
```

#### 实际使用

##### 访问全局对象

在浏览器下，可通过 window 属性在代码的任意位置访问到全局对象（除非做了特别处理而发生了意外，如声明了一个名为 window 的局部对象）。

存在的问题：在其他环境下，全局对象的标识可能不叫 window，而是其他名字，甚至可能对于程序员是看不见的。那么在这种情况下，如何访问全局对象呢？

解决的方法：从内嵌函数的作用域访问。按这种通常能获得全局对象，因为 this 在函数内部作为一个函数调用（不通过构造器 new 创建）时，往往指向该全局对象。

```
var global = (function() {
    return this;
}());
console.log(global);    // window 对象
```

注意事项：在 ECMAScript 5 的严格模式下不能这么做。

##### 单一 var 模式（Single var Pattern）

使用一个 var 关键字在函数顶部用逗号进行多个变量声明。

好处：
* 提供一个单一的地址以查找到函数需要的所有局部变量。
* 防止出现变量在定义（赋值）前就被使用的逻辑错误。
* 帮助牢记要声明变量，以尽可能少地使用全局变量。
* 更少的编码（无论是输入代码还是传输代码）。

示例：
```
function func() {
    var a = 1,
        b = 2,
        sum = a + b,
        myObject = {},
        i,
        j;
        
    // 函数体...
}
```

##### 初始化变量

声明变量的同时初始化变量（为变量赋初值）。

好处：
* 防止逻辑错误（所有未初始化且未声明的变量，其值都为 undefined）。
* 提高代码可读性（当你在以后重新看到这段代码时，你可以根据变量的初始值知道使用这些变量的意图，比如该变量应该是对象还是数组？）。

##### 提升：凌散变量的问题

提升：允许在函数的任意地方声明多个变量，无论在哪里声明，效果都等同于在函数顶部进行声明。

存在的问题：在变量声明前使用变量，会导致逻辑错误。因为变量提升，在同一个范围（同一函数）里声明的变量会默认提到函数顶部，
因此无论在函数哪个位置使用该变量，该变量都可以视为已经声明。

```
myName = "global"; // 全局变量 
function func() {
    alert(myName); // "undefined" 
    var myName = "local"; 
    alert(myName); // 局部变量 
} 
func(); 
 
// 前面代码片段运行结果等同于下述代码 

myName = "global"; // 全局变量 
function func() { 
    var myName; // 等同于 -> var myName = undefined 
    alert(myName); // undefined 
    myName = "local"; 
    alert(myName); // 局部变量 
} 
```

##### 命名空间

简单的通过全局对象的单一属性表示的功能性分组。

* 举例：
YUI 依照命名空间的思路来管理其代码，例如 Y.DOM 下的所有方法都是和 DOM 操作相关，Y.Event 下的所有方法都是和事件相关，以此类推。

* 优点：
将功能按照命名空间进行分组，可以让你的单全局对象变得井然有序，同时可以让团队成员能够知晓新功能应该属于哪个部分，或者知道去哪里查找已有的功能。

创建命名空间的方法：

* 对象字面量：
创建一个简单的对象字面量来打包所有的相关对象与函数，达到模拟命名空间的作用。

```
// 创建一个命名空间
var namespace = {};

// 在该命名空间上挂载一个名为 DOM 的对象
namespace.DOM = {};
// 在该命名空间上挂载一个名为 Event 的对象
namespace.Event = {};
```

* 函数创建：
通过声明 function 实现，函数里设置初始变量，公共方法写入 prototype。

1. 普通写法：

```
// 创建一个命名空间
var NameSpace = NameSpace || {};
// 在该命名空间上挂载一个名为 Hello 的构造函数
NameSpace.Hello = function() {
    this.name = 'world';
};
// 为 Hello 对象写入公共方法
NameSpace.Hello.prototype.sayHello = function(_name) {
    return 'Hello ' + (_name || this.name);
};
var hello = new NameSpace.Hello();
```

2. 简洁写法：

```
var NameSpace = window.NameSpace || {};
NameSpace.Hello = new function() {
    var self = this,
        name = 'world';
    self.sayHello = function(_name) {
        return 'Hello' + (_name || name);
    };
};
```

* 闭包和 Object 实现：
在闭包中声明好所有变量和方法，并通过一个 JSON Object 返回公有接口。

```
NameSpace.Hello = (function() {
    // 待返回的公有对象
    var self = {};
    // 私有变量或方法
    var name = 'world';
    // 公有方法或变量
    self.sayHello = function(_name) {
        return 'Hello' + (_name || name);
    }
    // 返回的公有对象
    return self;
}());
```

* 注意事项：
命名空间本身也是一个全局对象，在添加该“命名空间”时，有可能覆盖全局空间中的同名对象。因此我们需要在声明命名空间前进行检查，保证全局空间的安全。

```
var NameSpace = NameSpace || {};
```

如果已存在同名的全局对象，那么就将所有的函数与变量都挂载到这同名的全局对象上。如果不存在，就创建一个新的全局对象。

除了上述对全局对象是否存在进行判断之外，还能使用非破坏性处理命名空间的方式。

```
var YourGlobal = {
    namespace : function(ns) {
        var parts = ns.split("."),
            object = this,
            len = 0,
            i = 0;
        
        for(i = 0, len = parts.length; i < len; i++) {
            if(!object[parts[i]]) {
                object[parts[i]] = {};
            }
            object = object[parts[i]];
        }
        
        return object;
    }
};

/*
 * 同时创建 YourGlobal.Books 和 YourGlobal.Books.Event
 * 因为之前没有创建过它们，因此每个都是全新创建的
 */
YourGlobal.namespace("Books.Event");

// 现在你可以使用这个命名空间
YourGlobal.Books.Event.click = function() {
    //...
}

/*
 * 不会操作 YourGlobal.Books 本身，只给它添加 DOM 对象
 * 它会保持 YourGlobal.Books.Event 保持不变
 */
YourGlobal.namespace("Books.DOM");

// 仍然是合法的引用
console.log(YourGlobal.Books.Event.click);

// 同样可以在方法调用之后立即给它添加新属性
YourGlobal.namespace("Books").ANewBook = {};
```

优点：

基于你的单全局对象使用 namespace() 方法可以让开发者放心地认为命名空间总是存在的。
这样，每个文件都可以首先调用 namespace() 来声明开发者将要使用的命名空间，这样做不会对已有的命名空间造成任何破坏。
这个方法可以让开发者从使用命名空间之前判断是否存在中解放出来。

##### 多次重复使用

在window.onload = function(){ } 中使用var 变量名 声明全局变量，以后可以在与浏览器该页面多次操作交互的子函数中随时访问和修改这个全局变量，
这样就使这个变量像数据库中的具体字段值一样，可以保存唯一值，具体操作中也可以作为唯一的判断标准。

同理，在Jquery中，$(function(){}) 或者 $(document).ready(function(){}) 中使用var 变量名 也是声明为全局变量。

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



