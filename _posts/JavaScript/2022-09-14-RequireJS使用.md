---
layout: post
categories: JavaScript
title: RequireJS使用
meta: RequireJS使用
---
* content
{:toc}

## 模块系统

### 现状

伴随着移动互联的大潮，当今越来越多的网站已经从网页模式进化到了 Webapp 模式。
它们运行在现代的高级浏览器里，使用 HTML5、 CSS3、 ES6 等更新的技术来开发丰富的功能，
网页已经不仅仅是完成浏览的基本需求，并且webapp通常是一个单页面应用，每一个视图通过异步的方式加载，
这导致页面初始化和使用过程中会加载越来越多的 JavaScript 代码，这给前端开发的流程和资源组织带来了巨大的挑战。

前端开发和其他开发工作的主要区别，首先是前端是基于多语言、多层次的编码和组织工作，其次前端产品的交付是基于浏览器，
这些资源是通过增量加载的方式运行到浏览器端，如何在开发环境组织好这些碎片化的代码和资源，
并且保证他们在浏览器端快速、优雅的加载和更新，就需要一个模块化系统，
这个理想中的模块化系统是前端工程师多年来一直探索的难题。

### 模块系统的演进

模块系统主要解决模块的定义、依赖和导出，先来看看已经存在的模块系统。

#### script标签

```
<script src="module1.js"></script>
<script src="module2.js"></script>
<script src="libraryA.js"></script>
<script src="module3.js"></script>
```

这是最原始的 JavaScript 文件加载方式，如果把每一个文件看做是一个模块，那么他们的接口通常是暴露在全局作用域下，
也就是定义在 window 对象中，不同模块的接口调用都是一个作用域中，一些复杂的框架，
会使用命名空间的概念来组织这些模块的接口，典型的例子如 [YUI](https://clarle.github.io/yui3/) 库。

这种原始的加载方式暴露了一些显而易见的弊端：
* 全局作用域下容易造成变量冲突
* 文件只能按照 `<script>` 的书写顺序进行加载
* 开发人员必须主观解决模块和代码库的依赖关系
* 在大型项目中各种资源难以管理，长期积累的问题导致代码库混乱不堪

#### CommonJS

服务器端的 Node.js 遵循 [CommonJS规范](wiki.commonjs.org/wiki/CommonJS)，
该规范的核心思想是允许模块通过 `require` 方法来同步加载所要依赖的其他模块，
然后通过 `exports` 或 `module.exports` 来导出需要暴露的接口。

```
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

优点：
* 服务器端模块便于重用
* [NPM](https://www.npmjs.com/) 中已经有将近20万个可以使用模块包
* 简单并容易使用

缺点：
* 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的
* 不能非阻塞的并行加载多个模块

实现：
* 服务器端的 [Node.js](https://www.nodejs.org)
* [Browserify](https://browserify.org/)，浏览器端的 CommonJS 实现，可以使用 NPM 的模块，但是编译打包后的文件体积可能很大
* [modules-webmake](https://github.com/medikoo/modules-webmake)，类似Browserify，还不如 Browserify 灵活
* [wreq](https://github.com/substack/wreq)，Browserify 的前身

**CommonJS 规范**

CommonJS 是以在浏览器环境之外构建 JavaScript 生态系统为目标而产生的项目，比如在服务器和桌面环境中。

这个项目最开始是由 Mozilla 的工程师 Kevin Dangoor 在2009年1月创建的，当时的名字是 ServerJS。

Kevin Dangoor's [What Server Side JavaScript needs](https://www.blueskyonmars.com/2009/01/29/what-server-side-javascript-needs/)：
“我在这里描述的并不是一个技术问题，而是一件重大的事情，让大家走到一起来做决定，迈出第一步，来建立一个更大更酷的东西。”

2009年8月，这个项目改名为 CommonJS，以显示其 API 的更广泛实用性。CommonJS 是一套规范，它的创建和核准是开放的。
这个规范已经有很多版本和具体实现。CommonJS 并不是属于 ECMAScript TC39 小组的工作，但 TC39 中的一些成员参与 CommonJS 的制定。
2013年5月，Node.js 的包管理器 NPM 的作者 Isaac Z. Schlueter 说 
[CommonJS 已经过时，Node.js 的内核开发者已经废弃了该规范](https://github.com/nodejs/node-v0.x-archive/issues/5132#issuecomment-15432598)。

CommonJS 规范是为了解决 JavaScript 的作用域问题而定义的模块形式，可以使每个模块它自身的命名空间中执行。
该规范的主要内容是，模块必须通过 `module.exports` 导出对外的变量或接口，通过 `require()` 来导入其他模块的输出到当前模块作用域中。

一个直观的例子：
```
// moduleA.js
module.exports = function( value ){
    return value * 2;
}
```

```
// moduleB.js
var multiplyBy2 = require('./moduleA');
var result = multiplyBy2(4);
```

CommonJS 是同步加载模块，但其实也有浏览器端的实现，其原理是现将所有模块都定义好并通过 `id` 索引，
这样就可以方便的在浏览器环境中解析了，可以参考 [require1k](https://github.com/Stuk/require1k) 
和 [tiny-browser-require](https://github.com/ruanyf/tiny-browser-require) 的源码来理解其解析（resolve）的过程。

更多关于 CommonJS 规范的内容请查看 [http://wiki.commonjs.org/wiki/CommonJS](http://wiki.commonjs.org/wiki/CommonJS)。

#### AMD

[Asynchronous Module Definition](https://github.com/amdjs/amdjs-api) 规范其实只有一个主要接口 `define(id?, dependencies?, factory)`，
它要在声明模块的时候指定所有的依赖 `dependencies`，并且还要当做形参传到 `factory` 中，对于依赖的模块提前执行，依赖前置。

```
define("module", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
require(["module", "../file"], function(module, file) { /* ... */ });
```

优点：
* 适合在浏览器环境中异步加载模块
* 可以并行加载多个模块

缺点：
* 提高了开发成本，代码的阅读和书写比较困难，模块定义方式的语义不顺畅
* 不符合通用的模块化思维方式，是一种妥协的实现

实现：
* [RequireJS](https://requirejs.org/)
* [curl](https://github.com/cujojs/curl)

**AMD 规范**

AMD（异步模块定义）是为浏览器环境设计的，因为 CommonJS 模块系统是同步加载的，当前浏览器环境还没有准备好同步加载模块的条件。

AMD 定义了一套 JavaScript 模块依赖异步加载标准，来解决同步加载的问题。

模块通过 `define` 函数定义在闭包中，格式如下：
```
define(id?: String, dependencies?: String[], factory: Function|Object);
```

`id` 是模块的名字，它是可选的参数。

`dependencies` 指定了所要依赖的模块列表，它是一个数组，也是可选的参数，每个依赖的模块的输出将作为参数一次传入 `factory` 中。
如果没有指定 `dependencies`，那么它的默认值是 `["require", "exports", "module"]`。
```
define(function(require, exports, module) {}）
```

`factory` 是最后一个参数，它包裹了模块的具体实现，它是一个函数或者对象。如果是函数，那么它的返回值就是模块的输出接口或值。

一些用例：

定义一个名为 `myModule` 的模块，它依赖 `jQuery` 模块：
```
define('myModule', ['jquery'], function($) {
    // $ 是 jquery 模块的输出
    $('body').text('hello world');
});
// 使用
require(['myModule'], function(myModule) {});
```
注意：在 webpack 中，模块名只有局部作用域，在 Require.js 中模块名是全局作用域，可以在全局引用。

定义一个没有 `id` 值的匿名模块，通常作为应用的启动函数：
```
define(['jquery'], function($) {
    $('body').text('hello world');
});
```

依赖多个模块的定义：
```
define(['jquery', './math.js'], function($, math) {
    // $ 和 math 一次传入 factory
    $('body').text('hello world');
});
```

模块输出：
```
define(['jquery'], function($) {

    var HelloWorldize = function(selector){
        $(selector).text('hello world');
    };

    // HelloWorldize 是该模块输出的对外接口
    return HelloWorldize;
});
```

在模块定义内部引用依赖：
```
define(function(require) {
    var $ = require('jquery');
    $('body').text('hello world');
});
```

#### CMD

[Common Module Definition](https://github.com/cmdjs/specification/blob/master/draft/module.md) 规范和 AMD 很相似，尽量保持简单，
并与 CommonJS 和 Node.js 的 Modules 规范保持了很大的兼容性。

```
define(function(require, exports, module) {
  var $ = require('jquery');
  var Spinning = require('./spinning');
  exports.doSomething = ...
  module.exports = ...
})
```

优点：
* 依赖就近，延迟执行
* 可以很容易在 Node.js 中运行

缺点：
* 依赖 SPM 打包，模块的加载逻辑偏重

实现：
* [Sea.js](http://www.seajs.org)
* [coolie](https://github.com/cooliejs/coolie.js)

#### UMD

[Universal Module Definition](https://github.com/umdjs/umd) 规范类似于兼容 CommonJS 和 AMD 的语法糖，是模块定义的跨平台解决方案。

#### ES6 模块

ECMAScript6 标准（European Computer Manufacturers Association，欧洲计算机制造商协会）增加了 JavaScript 语言层面的模块体系定义。
[ES6 模块](https://es6.ruanyifeng.com/#docs/module)的设计思想，
是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，
都只能在运行时确定这些东西。

```
import "jquery";
export function doStuff() {}
module "localModule" {}
```

优点：
* 容易进行静态分析
* 面向未来的 ECMAScript 标准

缺点：
* 原生浏览器端还没有实现该标准
* 全新的命令字，新版的 Node.js才支持

实现：
* [Babel](https://babeljs.io/)

#### 期望的模块系统

可以兼容多种模块风格，尽量可以利用已有的代码，不仅仅只是 JavaScript 模块化，还有 CSS、图片、字体等资源也需要模块化。

### 前端模块加载

前端模块要在客户端中执行，所以他们需要增量加载到浏览器中。

模块的加载和传输，我们首先能想到两种极端的方式，一种是每个模块文件都单独请求，
另一种是把所有模块打包成一个文件然后只请求一次。显而易见，每个模块都发起单独的请求造成了请求次数过多，
导致应用启动速度慢；一次请求加载所有模块导致流量浪费、初始化过程慢。这两种方式都不是好的解决方案，它们过于简单粗暴。

分块传输，按需进行懒加载，在实际用到某些模块的时候再增量更新，才是较为合理的模块加载方案。

要实现模块的按需加载，就需要一个对整个代码库中的模块进行静态分析、编译打包的过程。

### 所有资源都是模块

在上面的分析过程中，我们提到的模块仅仅是指JavaScript模块文件。
然而，在前端开发过程中还涉及到样式、图片、字体、HTML 模板等等众多的资源。
这些资源还会以各种方言的形式存在，比如 coffeescript、 less、 sass、众多的模板库、多语言系统（i18n）等等。

如果他们都可以视作模块，并且都可以通过require的方式来加载，将带来优雅的开发体验，比如：

```
require("./style.css");
require("./style.less");
require("./template.jade");
require("./image.png");
```

那么如何做到让 `require` 能加载各种资源呢？

### 静态分析

在编译的时候，要对整个代码进行静态分析，分析出各个模块的类型和它们依赖关系，
然后将不同类型的模块提交给适配的加载器来处理。比如一个用 LESS 写的样式模块，
可以先用 LESS 加载器将它转成一个CSS 模块，在通过 CSS 模块把他插入到页面的 `<style>` 标签中执行。
Webpack 就是在这样的需求中应运而生。

同时，为了能利用已经存在的各种框架、库和已经写好的文件，我们还需要一个模块加载的兼容策略，来避免重写所有的模块。

## RequireJS

随着网站功能逐渐丰富，网页中的js也变得越来越复杂和臃肿，
原有通过script标签来导入一个个的js文件这种方式已经不能满足现在互联网开发模式，
我们需要团队协作、模块复用、单元测试等等一系列复杂的需求。

RequireJS是一个非常小巧的JavaScript模块载入框架，是AMD规范最好的实现者之一。
最新版本的RequireJS压缩后只有14K，堪称非常轻量。它还同时可以和其他的框架协同工作，
使用RequireJS必将使您的前端代码质量得以提升。

requirejs能带来什么好处

官方对requirejs的描述：

RequireJS is a JavaScript file and module loader. It is optimized for in-browser use, 
but it can be used in other JavaScript environments, like Rhino and Node. 
Using a modular script loader like RequireJS will improve the speed and quality of your code.

大致意思：

在浏览器中可以作为js文件的模块加载器，也可以用在Node和Rhino环境，balabala...。
这段话描述了requirejs的基本功能"模块化加载"，什么是模块化加载？我们要从之后的篇幅中一一解释

先来看一段常见的场景，通过示例讲解如何运用requirejs 

**正常编写方式**

index.html: 
```
<!DOCTYPE html>
<html>
    <head>
        <script type="text/javascript" src="a.js"></script>
    </head>
    <body>
      <span>body</span>
    </body>
</html>
```


a.js:
```
function fun1(){
  alert("it works");
}

fun1();
```

可能你更喜欢这样写
```
(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})()
```

第二种方法使用了块作用域来申明function防止污染全局变量，本质还是一样的，当运行上面两种例子时不知道你是否注意到，
alert执行的时候，html内容是一片空白的，即`<span>body</span>`并未被显示，
当点击确定后，才出现，这就是JS阻塞浏览器渲染导致的结果。

**requirejs写法**

当然首先要到requirejs的网站去下载js -> [requirejs.org](https://requirejs.org/)

index.html:
```
<!DOCTYPE html>
<html>
    <head>
        <script type="text/javascript" src="require.js"></script>
        <script type="text/javascript">
            require(["a"]);
        </script>
    </head>
    <body>
      <span>body</span>
    </body>
</html>
```

a.js:
```
define(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})
```

浏览器提示了"it works"，说明运行正确，但是有一点不一样，这次浏览器并不是一片空白，body已经出现在页面中，
目前为止可以知道requirejs具有如下优点：
* 防止js加载阻塞页面渲染
* 使用程序调用的方式加载js，防出现如下丑陋的场景

```
<script type="text/javascript" src="a.js"></script>
<script type="text/javascript" src="b.js"></script>
<script type="text/javascript" src="c.js"></script>
<script type="text/javascript" src="d.js"></script>
<script type="text/javascript" src="e.js"></script>
<script type="text/javascript" src="f.js"></script>
<script type="text/javascript" src="g.js"></script>
<script type="text/javascript" src="h.js"></script>
<script type="text/javascript" src="i.js"></script>
<script type="text/javascript" src="j.js"></script>
```

### 基本API

require会定义三个变量：define,require,requirejs，其中require === requirejs，一般使用require更简短

* define 从名字就可以看出这个api是用来定义一个模块
* require 加载依赖模块，并执行加载完后的回调函数

前一篇中的a.js：
```
define(function(){
    function fun1(){
      alert("it works");
    }

    fun1();
})
```

通过define函数定义了一个模块，然后再页面中使用：
```
require(["js/a"]);
```

来加载该模块(注意require中的依赖是一个数组，即使只有一个依赖，你也必须使用数组来定义)，
require API的第二个参数是callback，一个function，是用来处理加载完毕后的逻辑，如：
```
require(["js/a"], function(){
    alert("load finished");
})
```

### 加载文件

之前的例子中加载模块都是本地js，但是大部分情况下网页需要加载的JS可能来自本地服务器、其他网站或CDN，
这样就不能通过这种方式来加载了，我们以加载一个jquery库为例：
```
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery"]   
    }
})
require(["jquery","js/a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

这边涉及了require.config，require.config是用来配置模块加载位置，简单点说就是给模块起一个更短更好记的名字，
比如将百度的jquery库地址标记为jquery，这样在require时只需要写`["jquery"]`就可以加载该js，本地的js我们也可以这样配置：
```
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery"],
        "a" : "js/a"   
    }
})
require(["jquery","a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

通过paths的配置会使我们的模块名字更精炼，paths还有一个重要的功能，就是可以配置多个路径，
如果远程cdn库没有加载成功，可以加载本地的库，如：
```
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery", "js/jquery"],
        "a" : "js/a"   
    }
})
require(["jquery","a"],function($){
    $(function(){
        alert("load finished");  
    })
})
```

这样配置后，当百度的jquery没有加载成功后，会加载本地js目录下的jquery
* 在使用requirejs时，加载模块时不用写`.js`后缀的，当然也是不能写后缀
* 上面例子中的callback函数中发现有`$`参数，这个就是依赖的jquery模块的输出变量，如果你依赖多个模块，可以依次写入多个参数来使用：

```
require(["jquery","underscore"],function($, _){
    $(function(){
        _.each([1,2,3],alert);
    })
})
```

如果某个模块不输出变量值，则没有，所以尽量将输出的模块写在前面，防止位置错乱引发误解

### 全局配置

上面的例子中重复出现了`require.config`配置，如果每个页面中都加入配置，必然显得十分不雅，
requirejs提供了一种叫"主数据"的功能，我们首先创建一个main.js：
```
require.config({
    paths : {
        "jquery" : ["http://libs.baidu.com/jquery/2.0.3/jquery", "js/jquery"],
        "a" : "js/a"   
    }
})
```

然后再页面中使用下面的方式来使用requirejs：
```
<script data-main="js/main" src="js/require.js"></script>
```

解释一下，加载 requirejs 脚本的 script 标签加入了`data-main`属性，这个属性指定的 js 将在加载完 require.js 后处理，
我们把require.config的配置加入到`data-main`后，就可以使每一个页面都使用这个配置，
然后页面中就可以直接使用require来加载所有的短模块名。

data-main还有一个重要的功能，当script标签指定data-main属性时，require会默认的将data-main指定的js为根路径，是什么意思呢？
如上面的`data-main="js/main"`设定后，我们在使用`require(['jquery'])`后(不配置jquery的paths)，
require会自动加载`js/jquery.js`这个文件，而不是`jquery.js`，相当于默认配置了：
```
require.config({
    baseUrl : "js"
})
```

### 第三方模块

通过require加载的模块一般都需要符合AMD规范即使用define来申明模块，但是部分时候需要加载非AMD规范的js，
这时候就需要用到另一个功能：shim，shim解释起来也比较难理解，shim直接翻译为"垫"，
其实也是有这层意思的，目前我主要用在两个地方：

1、 非AMD模块输出，将非标准的AMD模块"垫"成可用的模块，例如：在老版本的jquery中，是没有继承AMD规范的，
所以不能直接`require["jquery"]`，这时候就需要shim，比如我要是用underscore类库，但是他并没有实现AMD规范，那我们可以这样配置
```
require.config({
    shim: {
        "underscore" : {
            exports : "_";
        }
    }
})
```

这样配置后，我们就可以在其他模块中引用underscore模块：
```
require(["underscore"], function(_){
    _.each([1,2,3], alert);
})
```

2、 插件形式的非AMD模块，我们经常会用到jquery插件，而且这些插件基本都不符合AMD规范，比如jquery.form插件，这时候就需要将form插件"垫"到jquery中：

```
require.config({
    shim: {
        "underscore" : {
            exports : "_";
        },
        "jquery.form" : {
            deps : ["jquery"]
        }
    }
})
```

也可以简写为：
```
require.config({
    shim: {
        "underscore" : {
            exports : "_";
        },
        "jquery.form" : ["jquery"]
    }
})
```

这样配置之后我们就可以使用加载插件后的jquery了
```
require.config(["jquery", "jquery.form"], function($){
    $(function(){
        $("#form").ajaxSubmit({...});
    })
})
```

好了，requirejs的基本配置大致就是这么多。






<br/><br/><br/><br/><br/>
## 参考资料 

Webpack 中文指南 <http://shouce.jb51.net/webpack/>

脚本之家 <http://shouce.jb51.net/>

30个在线学习设计与开发的站点 <https://www.runoob.com/w3cnote/30-best-websites-to-learn.html>

RequireJS 官网 <https://requirejs.org/>

JS模块化工具requirejs教程(一)：初识requirejs <https://www.runoob.com/w3cnote/requirejs-tutorial-1.html>

JS模块化工具requirejs教程(二)：基本知识 <https://www.runoob.com/w3cnote/requirejs-tutorial-2.html>

博客园 requirejs学习 <https://www.cnblogs.com/goodjobluo/p/8781410.html>

