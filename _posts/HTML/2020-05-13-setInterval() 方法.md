---
layout: post
categories: HTML
title: setInterval() 方法
meta: setInterval() 方法，可按照指定的周期（以毫秒计）来调用函数或计算表达式。
---
* content
{:toc}

### 正文

setInterval() 属于 Browser对象 的 Window对象 的 方法。

setInterval() 方法可按照指定的周期（以毫秒计）来调用函数或计算表达式。

setInterval() 方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。由 setInterval() 返回的 ID 值可用作 clearInterval() 方法的参数。

如果只执行1次，不需要循环，可以使用setTimeout() 方法。

#### 语法

```
setInterval(code, milliseconds);

setInterval(function, milliseconds, param1, param2, ...)
```

参数     描述

code/function     必需。要调用一个代码串，也可以是一个函数。

milliseconds     必须。周期性执行或调用 code/function 之间的时间间隔，以毫秒计。

param1, param2, ...     可选。 传给执行函数的其他参数（IE9 及其更早版本不支持该参数）。

返回值:     返回一个 ID（数字），可以将这个ID传递给clearInterval()，clearTimeout() 以取消执行。

#### 示例

示例1，每隔3秒弹出一次Hello提示：
```
setInterval(function(){ alert("Hello"); }, 3000);
```

也可以是：
```
var myVar;
 
function myFunction() {
    myVar = setInterval(alertFunc, 3000);
}
 
function alertFunc() {
    alert("Hello!");
}
```

示例2，实现实时刷新进度条：
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>进度条</title>
</head>
<style>
#myProgress {
  width: 100%;
  height: 30px;
  position: relative;
  background-color: #ddd;
}

#myBar {
  background-color: #4CAF50;
  width: 10px;
  height: 30px;
  position: absolute;
}
</style>
<body>

<h1>JavaScript 进度条</h1>

<div id="myProgress">
  <div id="myBar"></div>
</div>

<br>
<button onclick="move()">点我</button> 

<script>
function move() {
  var elem = document.getElementById("myBar");   
  var width = 0;
  var id = setInterval(frame, 10);
  function frame() {
    if (width == 100) {
      clearInterval(id);
    } else {
      width++; 
      elem.style.width = width + '%'; 
    }
  }
}
</script>

</body>
</html>
```

<br/><br/><br/><br/><br/>
### 参考资料 

Window setInterval() 方法 <https://www.runoob.com/jsref/met-win-setinterval.html>

HTML DOM setInterval() 方法 <https://www.w3school.com.cn/jsref/met_win_setinterval.asp>

Window clearInterval() 方法 <https://www.runoob.com/jsref/met-win-clearinterval.html>


