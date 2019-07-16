---
layout: post
categories: Yii2
title: Yii2 Web应用Request
meta: Web应用Request
---
* content
{:toc}

### 正文

Web应用Request由 yii\web\Request 实现，主要是一些功能的封装。只是涉及到许多HTTP的有关知识，读者朋友们可以自行查看相关的规范文档， 
如 HTTP 1.1 协议 ， CGI 1.1 规范 等。

同时，Yii大量引用了 $_SERVER ， 具体可以查看 PHP文档关于$_SERVER的内容 ， 此外，还涉及到PHP运行于不同的环境和模式下的一些细微差别。 

#### 请求的方法

根据 HTTP 1.1 协议 ，HTTP的请求可以有：GET, POST, PUT, DELETE, OPTIONS, HEAD, TRACE, CONNECT 8种方法 (Request Method)。
除了用不到的 CONNECT 外，Yii支持全部的HTTP请求方法。

要获取当前用户请求的方法，可以使用 yii\web\Request::getMethod()
```
// 返回当前请求的方法，请留意方法名称是大小写敏感的，按规范应转换为大写字母
public function getMethod()
{
    // $this->methodParam 默认值为 '_method'
    // 如果指定 $_POST['_method'] ，表示使用POST请求来模拟其他方法的请求。
    // 此时 $_POST['_method'] 即为所模拟的请求类型。
    if (isset($_POST[$this->methodParam])) {
        return strtoupper($_POST[$this->methodParam]);

    // 或者使用 $_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE'] 的值作为方法名。
    } elseif (isset($_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE'])) {
        return strtoupper($_SERVER['HTTP_X_HTTP_METHOD_OVERRIDE']);

    // 或者使用 $_SERVER['REQUEST_METHOD'] 作为方法名，未指定时，默认为 GET 方法
    } else {
        return isset($_SERVER['REQUEST_METHOD']) ?
            strtoupper($_SERVER['REQUEST_METHOD']) : 'GET';
    }
}
```

这个方法使用了3种方法来获取当前用户的请求，优先级从高到低依次为：

* 当使用POST请求来模拟其他请求时，以 $_POST['_method'] 作为当前请求的方法；
* 否则，如果存在 X_HTTP_METHOD_OVERRIDE HTTP头时，以该HTTP头所指定的方法作为请求方法， 如 X-HTTP-Method-Override: PUT 表示该请求所要执行的是 PUT 方法；
* 如果 X_HTTP_METHOD_OVERRIDE 不存在，则以 REQUEST_METHOD 的值作为当前请求的方法。 如果连 REQUEST_METHOD 也不存在，则视该请求是一个 GET 请求。

前面两种方法，主要是针对一些只支持GET和POST等有限方法的User Agent而设计的。

其中第一种方法是从Ruby on Rails中借鉴过来的， 通过在发送POST请求时，加入一个 $_POST['_method'] 的隐藏字段，来表示所要模拟的方法， 
如PUT，DELETE等。这样，就可以使得这些功能有限的User Agent也可以正常与服务器交互。 这种方法胜在简便，随手就来。

第二种方法则是使用 X_HTTP_METHOD_OVERRIDE HTTP头的办法来指定所使用的请求类型。 这种方法胜在直接明了，约定俗成，更为规范、合理。

至于 REQUEST_METHOD 是 CGI 1.1 规范 所定义的环境变量， 专门用来表明当前请求方法的。上面的代码只是在未指定时默认为GET请求罢了。

当然，我们在开发过程中，其实并不怎么在乎当前的用户请求是什么类型的请求，我们更在乎是不是某一类型的请求。 
比如，对于同一个URL地址 http://api.digpage.com/post/123 ， 如果是正常的GET请求，应该是查看编号为123的文章的意思。 
但是如果是一个DELETE请求，则是表示删除编号为123的文章的意思。我们在开发中，很可能就会这么写:
```
if ($app->request->isDelete()){
    $post->delete();
} else {
    $post->view();
}
```

上面的代码只是一个示意，与实际编码是有一定出入的，主要看判断分支的用法。 就是判断请求是否是某一特定类型的请求。
这些判断在实际开发中，是很常用的。 于是Yii为我们封装了许多方法专门用于执行这些判断：

* getIsAjax() 是否是AJAX请求，这其实不是HTTP请求方法，但是实际使用上，这个是用得最多的。
* getIsDelete() 是否是DELETE请求
* getIsFlash() 是否是Adobe Flash 或 Adobe Flex 发出的请求，这其实也不是HTTP请求方法。
* getIsGet() 是否是一个GET请求
* getIsHead() 是否是一个HEAD请求
* getIsOptions() 是否是一个OPTIONS请求
* getIsPatch() 是否是PATCH请求
* getIsPjax() 是否是一个PJAX请求，这也并非是HTTP请求方法。
* getIsPost() 是否是一个POST请求
* getIsPut() 是否是一个PUT请求

上面10个方法请留意其中有3个并未是HTTP请求方法，主要是用于特定HTTP请求类型（AJAX、Flash、PJAX）的判断。

除了这3个之外的其余7个方法，正好对应于HTTP 1.1 协议定义的7个方法。 而CONNECT方法由于Web开发在用不到，主要用于HTTP代理， 
因此，Yii也就没有为其设计一个所谓的 isConnect() 了，这是无用功。

上面的10个方法，再加一开始说的 getMehtod() 一共是11个方法，按照我们在 属性（Property） 部分所说的， 
这相当于定义了11个只读属性。我们以其中几个为例，看看具体实现:
```
// Yii实现的7个HTTP方法都是这个路子。
public function getIsOptions()
{
    // 注意在getMethod()时，输出的是全部大写的字符串
    return $this->getMethod() === 'OPTIONS';
}

// AJAX请求是通过 X_REQUESTED_WITH 消息头来判断的
public function getIsAjax()
{
    // 注意这里的XMLHttpRequest没有全部大写
    return isset($_SERVER['HTTP_X_REQUESTED_WITH']) && $_SERVER['HTTP_X_REQUESTED_WITH'] === 'XMLHttpRequest';
}

// PJAX请求是AJAX请求的一种，增加了X_PJAX消息头的定义
public function getIsPjax()
{
    return $this->getIsAjax() && !empty($_SERVER['HTTP_X_PJAX']);
}

// HTTP_USER_AGENT消息头中包含 'Shockwave' 或 'Flash' 字眼的（不区分大小写），就认为是FLASH请求
public function getIsFlash()
{
    return isset($_SERVER['HTTP_USER_AGENT']) && (stripos($_SERVER['HTTP_USER_AGENT'], 'Shockwave') !== false || stripos($_SERVER['HTTP_USER_AGENT'], 'Flash') !== false);
}
```

上面提到的AJAX、PJAX、FLASH请求比较特殊，并非是HTTP协议所规定的请求类型，但是在实现中是会使用到的。 
比如，对于一个请求，在非AJAX时，需要整个页面返回给客户端，而在AJAX请求时，只需要返回页面片段即可。

这些特殊请求是通过特殊的消息头实现的，具体的可以自行搜索相关的定义和规范。 
至于那7个HTTP方法的判断，摆明了是同一个路子，换瓶不换酒， getMethod() 前人栽树，他们后人乘凉。

#### 请求的参数


<br/><br/><br/><br/><br/>
### 参考资料

深入理解Yii2.0 » 请求与响应(TBD) » Web应用Request <http://www.digpage.com/web_request.html>

HTTP 1.1 协议 <https://tools.ietf.org/html/rfc2616>

CGI 1.1 规范 <https://tools.ietf.org/html/rfc3875.html>

$_SERVER <https://php.net/manual/en/reserved.variables.server.php>

