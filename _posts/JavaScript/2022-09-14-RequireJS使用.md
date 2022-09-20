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
define(function(require, exports, module) {})
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

## FastAdmin 中使用分析

具体backend端分析下。

`./application/admin/view/common/script.html` 文件内容：
```
<script src="__CDN__/assets/js/require{$Think.config.app_debug?'':'.min'}.js" data-main="__CDN__/assets/js/require-backend{$Think.config.app_debug?'':'.min'}.js?v={$site.version|htmlentities}"></script>
```

开发环境，先加载 `./publlic/assets/js/require.js` ，`require.js`就是上面说的 RequireJS 源码文件，完成后再加载的就是 `./publlic/assets/js/require-backend.js`。

`./publlic/assets/js/require-backend.js`文件内容：
```
require.config({
    urlArgs: "v=" + requirejs.s.contexts._.config.config.site.version,
    packages: [{
        name: 'moment',
        location: '../libs/moment',
        main: 'moment'
    }],
    //在打包压缩时将会把include中的模块合并到主文件中
    include: ['css', 'layer', 'toastr', 'fast', 'backend', 'backend-init', 'table', 'form', 'dragsort', 'drag', 'drop', 'addtabs', 'selectpage'],
    paths: {
        'lang': "empty:",
        'form': 'require-form',
        'table': 'require-table',
        'upload': 'require-upload',
        'drag': 'jquery.drag.min',
        'drop': 'jquery.drop.min',
        'dropzone': 'dropzone.min',
        'echarts': 'echarts.min',
        'echarts-theme': 'echarts-theme',
        'adminlte': 'adminlte',
        'bootstrap-table-commonsearch': 'bootstrap-table-commonsearch',
        'bootstrap-table-template': 'bootstrap-table-template',
        //
        // 以下的包从bower的libs目录加载
        'jquery': '../libs/jquery/dist/jquery.min',
        'bootstrap': '../libs/bootstrap/dist/js/bootstrap.min',
        'bootstrap-datetimepicker': '../libs/eonasdan-bootstrap-datetimepicker/build/js/bootstrap-datetimepicker.min',
        'bootstrap-daterangepicker': '../libs/bootstrap-daterangepicker/daterangepicker',
        'bootstrap-select': '../libs/bootstrap-select/dist/js/bootstrap-select.min',
        'bootstrap-select-lang': '../libs/bootstrap-select/dist/js/i18n/defaults-zh_CN',
        'bootstrap-table': '../libs/bootstrap-table/dist/bootstrap-table.min',
        'bootstrap-table-export': '../libs/bootstrap-table/dist/extensions/export/bootstrap-table-export.min',
        'bootstrap-table-fixed-columns': '../libs/bootstrap-table/dist/extensions/fixed-columns/bootstrap-table-fixed-columns',
        'bootstrap-table-mobile': '../libs/bootstrap-table/dist/extensions/mobile/bootstrap-table-mobile',
        'bootstrap-table-lang': '../libs/bootstrap-table/dist/locale/bootstrap-table-zh-CN',
        'bootstrap-table-jumpto': '../libs/bootstrap-table/dist/extensions/page-jumpto/bootstrap-table-jumpto',
        'bootstrap-slider': '../libs/bootstrap-slider/bootstrap-slider',
        'tableexport': '../libs/tableExport.jquery.plugin/tableExport.min',
        'dragsort': '../libs/fastadmin-dragsort/jquery.dragsort',
        'sortable': '../libs/Sortable/Sortable.min',
        'addtabs': '../libs/fastadmin-addtabs/jquery.addtabs',
        'slimscroll': '../libs/jquery-slimscroll/jquery.slimscroll',
        'validator': '../libs/nice-validator/dist/jquery.validator',
        'validator-lang': '../libs/nice-validator/dist/local/zh-CN',
        'toastr': '../libs/toastr/toastr',
        'jstree': '../libs/jstree/dist/jstree.min',
        'layer': '../libs/fastadmin-layer/dist/layer',
        'cookie': '../libs/jquery.cookie/jquery.cookie',
        'cxselect': '../libs/fastadmin-cxselect/js/jquery.cxselect',
        'template': '../libs/art-template/dist/template-native',
        'selectpage': '../libs/fastadmin-selectpage/selectpage',
        'citypicker': '../libs/fastadmin-citypicker/dist/js/city-picker.min',
        'citypicker-data': '../libs/fastadmin-citypicker/dist/js/city-picker.data',
    },
    // shim依赖配置
    shim: {
        'addons': ['backend'],
        'bootstrap': ['jquery'],
        'bootstrap-table': {
            deps: ['bootstrap'],
            exports: '$.fn.bootstrapTable'
        },
        'bootstrap-table-lang': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-export': {
            deps: ['bootstrap-table', 'tableexport'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-fixed-columns': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-mobile': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-advancedsearch': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-commonsearch': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-template': {
            deps: ['bootstrap-table', 'template'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'bootstrap-table-jumpto': {
            deps: ['bootstrap-table'],
            exports: '$.fn.bootstrapTable.defaults'
        },
        'tableexport': {
            deps: ['jquery'],
            exports: '$.fn.extend'
        },
        'slimscroll': {
            deps: ['jquery'],
            exports: '$.fn.extend'
        },
        'adminlte': {
            deps: ['bootstrap', 'slimscroll'],
            exports: '$.AdminLTE'
        },
        'bootstrap-daterangepicker': [
            'moment/locale/zh-cn'
        ],
        'bootstrap-datetimepicker': [
            'moment/locale/zh-cn',
        ],
        'bootstrap-select-lang': ['bootstrap-select'],
        'jstree': ['css!../libs/jstree/dist/themes/default/style.css'],
        'validator-lang': ['validator'],
        'citypicker': ['citypicker-data', 'css!../libs/fastadmin-citypicker/dist/css/city-picker.css']
    },
    baseUrl: requirejs.s.contexts._.config.config.site.cdnurl + '/assets/js/', //资源基础路径
    map: {
        '*': {
            'css': '../libs/require-css/css.min'
        }
    },
    waitSeconds: 60,
    charset: 'utf-8' // 文件编码
});

require(['jquery', 'bootstrap'], function ($, undefined) {
    //初始配置
    var Config = requirejs.s.contexts._.config.config;
    //将Config渲染到全局
    window.Config = Config;
    // 配置语言包的路径
    var paths = {};
    paths['lang'] = Config.moduleurl + '/ajax/lang?callback=define&controllername=' + Config.controllername + '&lang=' + Config.language + '&v=' + Config.site.version;
    // 避免目录冲突
    paths['backend/'] = 'backend/';
    require.config({paths: paths});

    // 初始化
    $(function () {
        require(['fast'], function (Fast) {
            require(['backend', 'backend-init', 'addons'], function (Backend, undefined, Addons) {
                //加载相应模块
                if (Config.jsname) {
                    require([Config.jsname], function (Controller) {
                        if (Controller.hasOwnProperty(Config.actionname)) {
                            Controller[Config.actionname]();
                        } else {
                            if (Controller.hasOwnProperty("_empty")) {
                                Controller._empty();
                            }
                        }
                    }, function (e) {
                        console.error(e);
                        // 这里可捕获模块加载的错误
                    });
                }
            });
        });
    });
});
```

其中初始化时 `Controller[Config.actionname]();` 就是根据控制器名和方法名自动加载对应的js文件和对应的js方法。

总结：
1. 自己写一个js，要使用define，并在function体中return
2. 调用其他js，使用require(),并在function体中编写业务代码
3. require-backend.js中根据当前控制器名和方法名自动帮我们调用了对应的js文件和其中的方法




我们自己写了控制器 Category 和相应js文件`category.js`，定义引用的组件：
```
define(['jquery', 'bootstrap', 'backend', 'table', 'form'], function ($, undefined, Backend, Table, Form) {

    var Controller = {
        ...
        add: function () {
            Controller.api.bindevent();
        },
        ...
        api: {
            bindevent: function () {
                $(document).on("change", "#c-type", function () {
                      $("#c-pid option[data-type='all']").prop("selected", true);
                      $("#c-pid option").removeClass("hide");
                      $("#c-pid option[data-type!='" + $(this).val() + "'][data-type!='all']").addClass("hide");
                      $("#c-pid").selectpicker("refresh");
                 });  
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };
    return Controller;
});
```

`add` 代码，代表调用 api 对象的 bindevent 函数。函数定义如下：
```
bindevent: function () {
     $(document).on("change", "#c-type", function () {
          $("#c-pid option[data-type='all']").prop("selected", true);
          $("#c-pid option").removeClass("hide");
          $("#c-pid option[data-type!='" + $(this).val() + "'][data-type!='all']").addClass("hide");
          $("#c-pid").selectpicker("refresh");
     });    
     Form.api.bindevent($("form[role=form]"));
}
```

函数第一部分是绑定类别变动的事件。

第二部分是是绑定窗体时间。

绑定窗体的代码在`./public/assets/js/require-form.js`文件里面。

这里面定义了Form对象，在这里我们可以看到events事件。

里面包含validator，主要是做客户端验证。有了这个就等于自动绑定了form验证，验证规则自己定制。

selectpicker 主要用于select下拉选择。

此外还有selectpage、cxselect、citypicker、datetimepicker、plupload、faselect、fieldlist，此外可以自己定制。

实验下来，上面 `bindevent: function () { }`中 写 `$(document).on` 是错误的，应该如下：
```
define(['jquery', 'bootstrap', 'backend', 'table', 'form'], function ($, undefined, Backend, Table, Form) {

    var Controller = {
        index: function () {
            // 初始化表格参数配置
            Table.api.init({
                extend: {
                    ...
                }
            });
    
            ...
    
            // 为表格绑定事件
            Table.api.bindevent(table);
    
            $(document).on("change", "#c-type", function () {
                  $("#c-pid option[data-type='all']").prop("selected", true);
                  $("#c-pid option").removeClass("hide");
                  $("#c-pid option[data-type!='" + $(this).val() + "'][data-type!='all']").addClass("hide");
                  $("#c-pid").selectpicker("refresh");
             });   
        },
        ...
        add: function () {
            Controller.api.bindevent();
        },
        ...
        api: {
            bindevent: function () {
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };
    return Controller;
});
```

`./public/assets/js/require-form.js`文件内容：
```
define(['jquery', 'bootstrap', 'upload', 'validator', 'validator-lang'], function ($, undefined, Upload, Validator, undefined) {
    var Form = {
        config: {
            fieldlisttpl: '<dd class="form-inline"><input type="text" name="<%=name%>[<%=index%>][key]" class="form-control" value="<%=row.key%>" size="10" /> <input type="text" name="<%=name%>[<%=index%>][value]" class="form-control" value="<%=row.value%>" /> <span class="btn btn-sm btn-danger btn-remove"><i class="fa fa-times"></i></span> <span class="btn btn-sm btn-primary btn-dragsort"><i class="fa fa-arrows"></i></span></dd>'
        },
        events: {
            validator: function (form, success, error, submit) {
                if (!form.is("form"))
                    return;
                //绑定表单事件
                form.validator($.extend({
                    validClass: 'has-success',
                    invalidClass: 'has-error',
                    bindClassTo: '.form-group',
                    formClass: 'n-default n-bootstrap',
                    msgClass: 'n-right',
                    stopOnError: true,
                    display: function (elem) {
                        return $(elem).closest('.form-group').find(".control-label").text().replace(/\:/, '');
                    },
                    dataFilter: function (data) {
                        if (data.code === 1) {
                            return data.msg ? {"ok": data.msg} : '';
                        } else {
                            return data.msg;
                        }
                    },
                    target: function (input) {
                        var target = $(input).data("target");
                        if (target && $(target).size() > 0) {
                            return $(target);
                        }
                        var $formitem = $(input).closest('.form-group'),
                            $msgbox = $formitem.find('span.msg-box');
                        if (!$msgbox.length) {
                            return [];
                        }
                        return $msgbox;
                    },
                    valid: function (ret) {
                        var that = this, submitBtn = $(".layer-footer [type=submit]", form);
                        that.holdSubmit(true);
                        submitBtn.addClass("disabled");
                        //验证通过提交表单
                        var submitResult = Form.api.submit($(ret), function (data, ret) {
                            that.holdSubmit(false);
                            submitBtn.removeClass("disabled");
                            if (false === $(this).triggerHandler("success.form", [data, ret])) {
                                return false;
                            }
                            if (typeof success === 'function') {
                                if (false === success.call($(this), data, ret)) {
                                    return false;
                                }
                            }
                            //提示及关闭当前窗口
                            var msg = ret.hasOwnProperty("msg") && ret.msg !== "" ? ret.msg : __('Operation completed');
                            parent.Toastr.success(msg);
                            parent.$(".btn-refresh").trigger("click");
                            var index = parent.Layer.getFrameIndex(window.name);
                            parent.Layer.close(index);
                            return false;
                        }, function (data, ret) {
                            that.holdSubmit(false);
                            if (false === $(this).triggerHandler("error.form", [data, ret])) {
                                return false;
                            }
                            submitBtn.removeClass("disabled");
                            if (typeof error === 'function') {
                                if (false === error.call($(this), data, ret)) {
                                    return false;
                                }
                            }
                        }, submit);
                        //如果提交失败则释放锁定
                        if (!submitResult) {
                            that.holdSubmit(false);
                            submitBtn.removeClass("disabled");
                        }
                        return false;
                    }
                }, form.data("validator-options") || {}));

                //移除提交按钮的disabled类
                $(".layer-footer [type=submit],.fixed-footer [type=submit],.normal-footer [type=submit]", form).removeClass("disabled");
                //自定义关闭按钮事件
                form.on("click", ".layer-close", function () {
                    var index = parent.Layer.getFrameIndex(window.name);
                    parent.Layer.close(index);
                    return false;
                });
            },
            selectpicker: function (form) {
                //绑定select元素事件
                if ($(".selectpicker", form).size() > 0) {
                    require(['bootstrap-select', 'bootstrap-select-lang'], function () {
                        $('.selectpicker', form).selectpicker();
                        $(form).on("reset", function () {
                            setTimeout(function () {
                                $('.selectpicker').selectpicker('refresh').trigger("change");
                            }, 1);
                        });
                    });
                }
            },
            selectpage: function (form) {
                //绑定selectpage元素事件
                if ($(".selectpage", form).size() > 0) {
                    require(['selectpage'], function () {
                        $('.selectpage', form).selectPage({
                            eAjaxSuccess: function (data) {
                                data.list = typeof data.rows !== 'undefined' ? data.rows : (typeof data.list !== 'undefined' ? data.list : []);
                                data.totalRow = typeof data.total !== 'undefined' ? data.total : (typeof data.totalRow !== 'undefined' ? data.totalRow : data.list.length);
                                return data;
                            }
                        });
                    });
                    //给隐藏的元素添加上validate验证触发事件
                    $(document).on("change", ".sp_hidden", function () {
                        $(this).trigger("validate");
                    });
                    $(document).on("change", ".sp_input", function () {
                        $(this).closest(".sp_container").find(".sp_hidden").trigger("change");
                    });
                    $(form).on("reset", function () {
                        setTimeout(function () {
                            $('.selectpage', form).selectPageClear();
                        }, 1);
                    });
                }
            },
            cxselect: function (form) {
                //绑定cxselect元素事件
                if ($("[data-toggle='cxselect']", form).size() > 0) {
                    require(['cxselect'], function () {
                        $.cxSelect.defaults.jsonName = 'name';
                        $.cxSelect.defaults.jsonValue = 'value';
                        $.cxSelect.defaults.jsonSpace = 'data';
                        $("[data-toggle='cxselect']", form).cxSelect();
                    });
                }
            },
            citypicker: function (form) {
                //绑定城市远程插件
                if ($("[data-toggle='city-picker']", form).size() > 0) {
                    require(['citypicker'], function () {
                        $(form).on("reset", function () {
                            setTimeout(function () {
                                $("[data-toggle='city-picker']").citypicker('refresh');
                            }, 1);
                        });
                    });
                }
            },
            datetimepicker: function (form) {
                //绑定日期时间元素事件
                if ($(".datetimepicker", form).size() > 0) {
                    require(['bootstrap-datetimepicker'], function () {
                        var options = {
                            format: 'YYYY-MM-DD HH:mm:ss',
                            icons: {
                                time: 'fa fa-clock-o',
                                date: 'fa fa-calendar',
                                up: 'fa fa-chevron-up',
                                down: 'fa fa-chevron-down',
                                previous: 'fa fa-chevron-left',
                                next: 'fa fa-chevron-right',
                                today: 'fa fa-history',
                                clear: 'fa fa-trash',
                                close: 'fa fa-remove'
                            },
                            showTodayButton: true,
                            showClose: true
                        };
                        $('.datetimepicker', form).parent().css('position', 'relative');
                        $('.datetimepicker', form).datetimepicker(options).on('dp.change', function (e) {
                            $(this, document).trigger("changed");
                        });
                    });
                }
            },
            daterangepicker: function (form) {
                //绑定日期时间元素事件
                if ($(".datetimerange", form).size() > 0) {
                    require(['bootstrap-daterangepicker'], function () {
                        var ranges = {};
                        ranges[__('Today')] = [Moment().startOf('day'), Moment().endOf('day')];
                        ranges[__('Yesterday')] = [Moment().subtract(1, 'days').startOf('day'), Moment().subtract(1, 'days').endOf('day')];
                        ranges[__('Last 7 Days')] = [Moment().subtract(6, 'days').startOf('day'), Moment().endOf('day')];
                        ranges[__('Last 30 Days')] = [Moment().subtract(29, 'days').startOf('day'), Moment().endOf('day')];
                        ranges[__('This Month')] = [Moment().startOf('month'), Moment().endOf('month')];
                        ranges[__('Last Month')] = [Moment().subtract(1, 'month').startOf('month'), Moment().subtract(1, 'month').endOf('month')];
                        var options = {
                            timePicker: false,
                            autoUpdateInput: false,
                            timePickerSeconds: true,
                            timePicker24Hour: true,
                            autoApply: true,
                            locale: {
                                format: 'YYYY-MM-DD HH:mm:ss',
                                customRangeLabel: __("Custom Range"),
                                applyLabel: __("Apply"),
                                cancelLabel: __("Clear"),
                            },
                            ranges: ranges,
                        };
                        var origincallback = function (start, end) {
                            $(this.element).val(start.format(this.locale.format) + " - " + end.format(this.locale.format));
                            $(this.element).trigger('blur');
                        };
                        $(".datetimerange", form).each(function () {
                            var callback = typeof $(this).data('callback') == 'function' ? $(this).data('callback') : origincallback;
                            $(this).on('apply.daterangepicker', function (ev, picker) {
                                callback.call(picker, picker.startDate, picker.endDate);
                            });
                            $(this).on('cancel.daterangepicker', function (ev, picker) {
                                $(this).val('').trigger('blur');
                            });
                            $(this).daterangepicker($.extend(true, options, $(this).data()), callback);
                        });
                    });
                }
            },
            /**
             * 绑定上传事件
             * @param form
             * @deprecated Use faupload instead.
             */
            plupload: function (form) {
                Form.events.faupload(form);
            },
            /**
             * 绑定上传事件
             * @param form
             */
            faupload: function (form) {
                //绑定上传元素事件
                if ($(".plupload,.faupload", form).size() > 0) {
                    Upload.api.upload($(".plupload,.faupload", form));
                }
            },
            faselect: function (form) {
                //绑定fachoose选择附件事件
                if ($(".faselect,.fachoose", form).size() > 0) {
                    $(".faselect,.fachoose", form).off('click').on('click', function () {
                        var that = this;
                        var multiple = $(this).data("multiple") ? $(this).data("multiple") : false;
                        var mimetype = $(this).data("mimetype") ? $(this).data("mimetype") : '';
                        var admin_id = $(this).data("admin-id") ? $(this).data("admin-id") : '';
                        var user_id = $(this).data("user-id") ? $(this).data("user-id") : '';
                        var url = $(this).data("url") ? $(this).data("url") : (typeof Backend !== 'undefined' ? "general/attachment/select" : "user/attachment");
                        parent.Fast.api.open(url + "?element_id=" + $(this).attr("id") + "&multiple=" + multiple + "&mimetype=" + mimetype + "&admin_id=" + admin_id + "&user_id=" + user_id, __('Choose'), {
                            callback: function (data) {
                                var button = $("#" + $(that).attr("id"));
                                var maxcount = $(button).data("maxcount");
                                var input_id = $(button).data("input-id") ? $(button).data("input-id") : "";
                                maxcount = typeof maxcount !== "undefined" ? maxcount : 0;
                                if (input_id && data.multiple) {
                                    var urlArr = [];
                                    var inputObj = $("#" + input_id);
                                    var value = $.trim(inputObj.val());
                                    if (value !== "") {
                                        urlArr.push(inputObj.val());
                                    }
                                    urlArr.push(data.url)
                                    var result = urlArr.join(",");
                                    if (maxcount > 0) {
                                        var nums = value === '' ? 0 : value.split(/\,/).length;
                                        var files = data.url !== "" ? data.url.split(/\,/) : [];
                                        var remains = maxcount - nums;
                                        if (files.length > remains) {
                                            Toastr.error(__('You can choose up to %d file%s', remains));
                                            return false;
                                        }
                                    }
                                    inputObj.val(result).trigger("change").trigger("validate");
                                } else {
                                    $("#" + input_id).val(data.url).trigger("change").trigger("validate");
                                }
                            }
                        });
                        return false;
                    });
                }
            },
            fieldlist: function (form) {
                //绑定fieldlist
                if ($(".fieldlist", form).size() > 0) {
                    require(['dragsort', 'template'], function (undefined, Template) {
                        //刷新隐藏textarea的值
                        var refresh = function (name) {
                            var data = {};
                            var textarea = $("textarea[name='" + name + "']", form);
                            var container = $(".fieldlist[data-name='" + name + "']");
                            var template = container.data("template");
                            $.each($("input,select,textarea", container).serializeArray(), function (i, j) {
                                var reg = /\[(\w+)\]\[(\w+)\]$/g;
                                var match = reg.exec(j.name);
                                if (!match)
                                    return true;
                                match[1] = "x" + parseInt(match[1]);
                                if (typeof data[match[1]] == 'undefined') {
                                    data[match[1]] = {};
                                }
                                data[match[1]][match[2]] = j.value;
                            });
                            var result = template ? [] : {};
                            $.each(data, function (i, j) {
                                if (j) {
                                    if (!template) {
                                        if (j.key != '') {
                                            result[j.key] = j.value;
                                        }
                                    } else {
                                        result.push(j);
                                    }
                                }
                            });
                            textarea.val(JSON.stringify(result));
                        };
                        //监听文本框改变事件
                        $(document).on('change keyup changed', ".fieldlist input,.fieldlist textarea,.fieldlist select", function () {
                            refresh($(this).closest(".fieldlist").data("name"));
                        });
                        //追加控制
                        $(".fieldlist", form).on("click", ".btn-append,.append", function (e, row) {
                            var container = $(this).closest(".fieldlist");
                            var tagName = container.data("tag") || (container.is("table") ? "tr" : "dd");
                            var index = container.data("index");
                            var name = container.data("name");
                            var template = container.data("template");
                            var data = container.data();
                            index = index ? parseInt(index) : 0;
                            container.data("index", index + 1);
                            row = row ? row : {};
                            var vars = {index: index, name: name, data: data, row: row};
                            var html = template ? Template(template, vars) : Template.render(Form.config.fieldlisttpl, vars);
                            $(html).attr("fieldlist-item", true).insertBefore($(tagName + ":last", container));
                            $(this).trigger("fa.event.appendfieldlist", $(this).closest(tagName).prev());
                        });
                        //移除控制
                        $(".fieldlist", form).on("click", ".btn-remove", function () {
                            var container = $(this).closest(".fieldlist");
                            var tagName = container.data("tag") || (container.is("table") ? "tr" : "dd");
                            $(this).closest(tagName).remove();
                            refresh(container.data("name"));
                        });
                        //渲染数据&拖拽排序
                        $(".fieldlist", form).each(function () {
                            var container = this;
                            var tagName = $(this).data("tag") || ($(this).is("table") ? "tr" : "dd");
                            $(this).dragsort({
                                itemSelector: tagName,
                                dragSelector: ".btn-dragsort",
                                dragEnd: function () {
                                    refresh($(this).closest(".fieldlist").data("name"));
                                },
                                placeHolderTemplate: $("<" + tagName + "/>")
                            });
                            var textarea = $("textarea[name='" + $(this).data("name") + "']", form);
                            if (textarea.val() == '') {
                                return true;
                            }
                            var template = $(this).data("template");
                            textarea.on("fa.event.refreshfieldlist", function () {
                                $("[fieldlist-item]", container).remove();
                                var json = {};
                                try {
                                    json = JSON.parse($(this).val());
                                } catch (e) {
                                }
                                $.each(json, function (i, j) {
                                    $(".btn-append,.append", container).trigger('click', template ? j : {
                                        key: i, value: j
                                    });
                                });
                            });
                            textarea.trigger("fa.event.refreshfieldlist");
                        });
                    });
                }
            },
            switcher: function (form) {
                form.on("click", "[data-toggle='switcher']", function () {
                    if ($(this).hasClass("disabled")) {
                        return false;
                    }
                    var switcher = $.proxy(function () {
                        var input = $(this).prev("input");
                        input = $(this).data("input-id") ? $("#" + $(this).data("input-id")) : input;
                        if (input.size() > 0) {
                            var yes = $(this).data("yes");
                            var no = $(this).data("no");
                            if (input.val() == yes) {
                                input.val(no);
                                $("i", this).addClass("fa-flip-horizontal text-gray");
                            } else {
                                input.val(yes);
                                $("i", this).removeClass("fa-flip-horizontal text-gray");
                            }
                            input.trigger('change');
                        }
                    }, this);
                    if (typeof $(this).data("confirm") !== 'undefined') {
                        Layer.confirm($(this).data("confirm"), function (index) {
                            switcher();
                            Layer.close(index);
                        });
                    } else {
                        switcher();
                    }

                    return false;
                });
            },
            bindevent: function (form) {

            },
            slider: function (form) {
                if ($(".slider", form).size() > 0) {
                    require(['bootstrap-slider'], function () {
                        $('.slider').removeClass('hidden').css('width', function (index, value) {
                            return $(this).parents('.form-control').width();
                        }).slider().on('slide', function (ev) {
                            var data = $(this).data();
                            if (typeof data.unit !== 'undefined') {
                                $(this).parents('.form-control').siblings('.value').text(ev.value + data.unit);
                            }
                        });
                    });
                }
            }
        },
        api: {
            submit: function (form, success, error, submit) {
                if (form.size() === 0) {
                    Toastr.error("表单未初始化完成,无法提交");
                    return false;
                }
                if (typeof submit === 'function') {
                    if (false === submit.call(form, success, error)) {
                        return false;
                    }
                }
                var type = form.attr("method") ? form.attr("method").toUpperCase() : 'GET';
                type = type && (type === 'GET' || type === 'POST') ? type : 'GET';
                url = form.attr("action");
                url = url ? url : location.href;
                //修复当存在多选项元素时提交的BUG
                var params = {};
                var multipleList = $("[name$='[]']", form);
                if (multipleList.size() > 0) {
                    var postFields = form.serializeArray().map(function (obj) {
                        return $(obj).prop("name");
                    });
                    $.each(multipleList, function (i, j) {
                        if (postFields.indexOf($(this).prop("name")) < 0) {
                            params[$(this).prop("name")] = '';
                        }
                    });
                }
                //调用Ajax请求方法
                Fast.api.ajax({
                    type: type,
                    url: url,
                    data: form.serialize() + (Object.keys(params).length > 0 ? '&' + $.param(params) : ''),
                    dataType: 'json',
                    complete: function (xhr) {
                        var token = xhr.getResponseHeader('__token__');
                        if (token) {
                            $("input[name='__token__']").val(token);
                        }
                    }
                }, function (data, ret) {
                    $('.form-group', form).removeClass('has-feedback has-success has-error');
                    if (data && typeof data === 'object') {
                        //刷新客户端token
                        if (typeof data.token !== 'undefined') {
                            $("input[name='__token__']").val(data.token);
                        }
                        //调用客户端事件
                        if (typeof data.callback !== 'undefined' && typeof data.callback === 'function') {
                            data.callback.call(form, data);
                        }
                    }
                    if (typeof success === 'function') {
                        if (false === success.call(form, data, ret)) {
                            return false;
                        }
                    }
                }, function (data, ret) {
                    if (data && typeof data === 'object' && typeof data.token !== 'undefined') {
                        $("input[name='__token__']").val(data.token);
                    }
                    if (typeof error === 'function') {
                        if (false === error.call(form, data, ret)) {
                            return false;
                        }
                    }
                });
                return true;
            },
            bindevent: function (form, success, error, submit) {

                form = typeof form === 'object' ? form : $(form);

                var events = Form.events;

                events.bindevent(form);

                events.validator(form, success, error, submit);

                events.selectpicker(form);

                events.daterangepicker(form);

                events.selectpage(form);

                events.cxselect(form);

                events.citypicker(form);

                events.datetimepicker(form);

                events.faupload(form);

                events.faselect(form);

                events.fieldlist(form);

                events.slider(form);

                events.switcher(form);
            },
            custom: {}
        },
    };
    return Form;
});
```



具体使用，参见 [FastAdmin 前端文档](https://doc.fastadmin.net/doc/frontend.html)







<br/><br/><br/><br/><br/>
## 参考资料 

Webpack 中文指南 <http://shouce.jb51.net/webpack/>

脚本之家 <http://shouce.jb51.net/>

30个在线学习设计与开发的站点 <https://www.runoob.com/w3cnote/30-best-websites-to-learn.html>

RequireJS 官网 <https://requirejs.org/>

JS模块化工具requirejs教程(一)：初识requirejs <https://www.runoob.com/w3cnote/requirejs-tutorial-1.html>

JS模块化工具requirejs教程(二)：基本知识 <https://www.runoob.com/w3cnote/requirejs-tutorial-2.html>

博客园 requirejs学习 <https://www.cnblogs.com/goodjobluo/p/8781410.html>

1分钟带你入门RequireJs并了解FastAdmin中JS是如何调用的 <https://ask.fastadmin.net/index.php/article/6505.html>



