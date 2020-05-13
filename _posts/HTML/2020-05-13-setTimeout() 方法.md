---
layout: post
categories: HTML
title: setTimeout() 方法
meta: setTimeout() 方法，用于在指定的毫秒数后调用函数或计算表达式。
---
* content
{:toc}

### 正文

setTimeout() 属于 Browser对象 的 Window对象 的 方法。

setTimeout() 方法用于在指定的毫秒数后调用函数或计算表达式。

使用 clearTimeout() 方法来阻止函数的执行。

如果你想重复执行可以使用 setInterval() 方法。

#### 语法

```
setTimeout(code, milliseconds, param1, param2, ...)

setTimeout(function, milliseconds, param1, param2, ...)
```

参数 	描述

code/function 	必需。要调用一个代码串，也可以是一个函数。

milliseconds 	可选。执行或调用 code/function 需要等待的时间，以毫秒计。默认为 0。

param1, param2, ... 	可选。 传给执行函数的其他参数（IE9 及其更早版本不支持该参数）。

返回值: 	返回一个 ID（数字），可以将这个ID传递给 clearTimeout() 来取消执行。

#### 示例

示例1，3 秒（3000 毫秒）后弹出 "Hello" :：
```
setTimeout(function(){ alert("Hello"); }, 3000);
```

也可以是：
```
var myVar;
 
function myFunction() {
    myVar = setTimeout(alertFunc, 3000);
}
 
function alertFunc() {
    alert("Hello!");
}
```

<br/><br/><br/><br/><br/>
### 参考资料 

Window setTimeout() 方法 <https://www.runoob.com/jsref/met-win-settimeout.html>

Window clearTimeout() 方法 <https://www.runoob.com/jsref/met-win-cleartimeout.html>
