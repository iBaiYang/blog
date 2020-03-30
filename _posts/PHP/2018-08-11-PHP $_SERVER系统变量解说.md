---
layout: post
categories: PHP
title: PHP $_SERVER系统变量解说
meta: $_SERVER系统变量应该是最基本要了解的，下面整理下。
---
* content
{:toc}

### 正文

我们在本地开发环境打印以下$_SERVER：

```
print_r( $_SERVER );
```

看一下结果：
```
Array
(
    [USER] => apache
    [HOME] => /var/www
    [HTTP_CACHE_CONTROL] => no-cache
    [HTTP_PRAGMA] => no-cache
    [HTTP_UPGRADE_INSECURE_REQUESTS] => 1
    [HTTP_CONNECTION] => keep-alive    // 当前请求头中 Connection: 项的内容，如果存在的话。
    [HTTP_COOKIE] => advanced-backend=j1ed1satr2o83vrrne1qphsd57; _csrf-backend=77180f9813f30581775717caf6d24a1aeaa8a4b2143bb2df77641388b4e5ae54a%3A2%3A%7Bi%3A0%3Bs%3A13%3A%22_csrf-backend%22%3Bi%3A1%3Bs%3A32%3A%22t%0C%0A7%CC%D6%14%8E%C2%1Fg%D6%3D%13%1F%1C%AA%FCl%FA%C9%84U%11%0D%3F%C8%9D%D9%DCL%26%22%3B%7D    // 当前请求头中 Cookie: 项的内容
    [HTTP_ACCEPT_ENCODING] => gzip, deflate    // 当前请求头中 Accept-Encoding: 项的内容，如果存在的话。
    [HTTP_ACCEPT_LANGUAGE] => zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2    // 当前请求头中 Accept-Language: 项的内容，如果存在的话。
    [HTTP_ACCEPT] => text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8    // 当前请求头中 Accept: 项的内容
    [HTTP_USER_AGENT] => Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:61.0) Gecko/20100101 Firefox/61.0    // 当前请求头中 User-Agent: 项的内容，如果存在的话。该字符串表明了访问该页面的用户代理的信息。一个典型的例子是：Mozilla/4.5 [en] (X11; U; Linux 2.2.9 i586)。除此之外，你可以通过 get_browser() 来使用该值，从而定制页面输出以便适应用户代理的性能。
    [HTTP_HOST] => yii2_test_backend.host    // 当前请求头中 Host: 项的内容，如果存在的话。 
    [SCRIPT_FILENAME] => /var/www/yii2_test/backend/web/index.php    // 当前执行脚本的绝对路径。 
    [REDIRECT_STATUS] => 200
    [SERVER_NAME] => yii2_test_backend.host    // 当前运行脚本所在的服务器的主机名。如果脚本运行于虚拟主机中，该名称是由那个虚拟主机所设置的值决定。 
    [SERVER_PORT] => 80    // Web 服务器使用的端口。默认值为 “80”。如果使用 SSL 安全连接，则这个值为用户设置的 HTTP 端口。 
    [SERVER_ADDR] => 10.0.2.11    // 当前运行脚本所在的服务器的 IP 地址
    [REMOTE_PORT] => 57881    // 用户机器上连接到 Web 服务器所使用的端口号
    [REMOTE_ADDR] => 10.0.2.23    // 浏览当前页面的用户的 IP 地址
    [SERVER_SOFTWARE] => nginx/1.12.2    // 服务器标识字符串，在响应请求时的头信息中给出
    [GATEWAY_INTERFACE] => CGI/1.1    // 服务器使用的 CGI 规范的版本；例如，“CGI/1.1”。 
    [REQUEST_SCHEME] => http  
    [SERVER_PROTOCOL] => HTTP/1.1    // 请求页面时通信协议的名称和版本。例如，“HTTP/1.0”
    [DOCUMENT_ROOT] => /var/www/yii2_test/backend/web    // 当前运行脚本所在的文档根目录。在服务器配置文件中定义。
    [DOCUMENT_URI] => /index.php
    [REQUEST_URI] => /index.php?r=site%2Flogin    // URI 用来指定要访问的页面。例如 “/index.html”。 
    [SCRIPT_NAME] => /index.php    // 包含当前脚本的路径。这在页面需要指向自己时非常有用。__FILE__ 常量包含当前脚本(例如包含文件)的完整路径和文件名。 
    [CONTENT_LENGTH] => 
    [CONTENT_TYPE] => 
    [REQUEST_METHOD] => GET    // 访问页面使用的请求方法；例如，“GET”, “HEAD”，“POST”，“PUT”。
    [QUERY_STRING] => r=site%2Flogin    // query string（查询字符串），如果有的话，通过它进行页面访问
    [FCGI_ROLE] => RESPONDER
    [PHP_SELF] => /index.php    // 当前执行脚本的文件名，与 document root 有关。例如，在地址为 http://example.com/foo/bar.php 的脚本中使用 $_SERVER['PHP_SELF'] 将得到 /foo/bar.php。__FILE__ 常量包含当前(例如包含)文件的完整路径和文件名。 从 PHP 4.3.0 版本开始，如果 PHP 以命令行模式运行，这个变量将包含脚本名。之前的版本该变量不可用。
    [REQUEST_TIME_FLOAT] => 1533972823.4286    // 请求开始时的时间戳，微秒级别的精准度。 自 PHP 5.4.0 开始生效。 
    [REQUEST_TIME] => 1533972823    // 请求开始时的时间戳。从 PHP 5.1.0 起可用。
)
```

看结果应该能猜出含义来。

里面有好多请求头的内容，所以下面打一下 信息头 请求头 详情：
```
Accept	text/html,application/xhtml+xm…plication/xml;q=0.9,*/*;q=0.8
Accept-Encoding	gzip, deflate
Accept-Language	zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Cache-Control	no-cache
Connection	keep-alive
Cookie	advanced-backend=j1ed1satr2o83…D%3F%C8%9D%D9%DCL%26%22%3B%7D
Host	yii2_test_backend.host
Pragma	no-cache
Upgrade-Insecure-Requests	1
User-Agent	Mozilla/5.0 (Windows NT 10.0; …) Gecko/20100101 Firefox/61.0
```

信息头 响应头 详情：
```
Connection	keep-alive
Content-Type	text/html; charset=UTF-8
Date	Sat, 11 Aug 2018 07:33:43 GMT
Server	nginx/1.12.2
Transfer-Encoding	chunked
X-Powered-By	PHP/7.0.25
```

Cookie	详情：
```
请求 Cookie	
_csrf-backend	77180f9813f30581775717caf6d24a1aeaa8a4b2143bb2df77641388b4e5ae54a:2:{i:0;s:13:"_csrf-backend";i:1;s:32:"t� 7ÌÖ��Â�gÖ=���ªülúÉ�U� ?È�ÙÜL&";}
advanced-backend	j1ed1satr2o83vrrne1qphsd57
```

参数 详情：
```
查询字符串	
r	site/login
```

响应 就是上面第一个

耗时 详情：
```
阻塞：→ 4 ms
DNS 解析:→ 0 ms
连接:→ 4 ms
TLS 建立:→ 0 ms
发送:→ 0 ms
等待:→ 9 ms
接收:→ 1 ms
```

堆栈追踪 详情：
```
<匿名>   chrome://browser/content/tab-content.js:86:43
wrapHandlingUserInput      resource://gre/modules/E10SUtils.jsm:326:7
<匿名>    chrome://browser/content/tab-content.js:85:5
```

一不小心强迫症犯了，上面把所有可见的调试信息都打了出来，方便加深理解。

![]({{site.baseurl}}/images/20200330/20200330191538.jpeg)


既然提到了__FILE__ ， 也打印出来看一下：
```
/var/www/yii2_test/backend/web/index.php
```

<br/><br/><br/><br/><br/>
### 参考资料

<http://www.php.net/manual/en/reserved.variables.server.php>

<http://php.net/manual/zh/reserved.variables.server.php>

超全局变量 <http://php.net/manual/zh/language.variables.superglobals.php>

$GLOBALS  <http://php.net/manual/zh/reserved.variables.globals.php>

变量范围 global 关键字 <http://php.net/manual/zh/language.variables.scope.php> 

PHP 手册 语言参考 <http://php.net/manual/zh/langref.php>

PHP 手册 <http://php.net/manual/zh/index.php>

RFC 3875 - The Common Gateway Interface (CGI) Version 1.1 <RFC 3875 - The Common Gateway Interface (CGI) Version 1.1>  <http://www.faqs.org/rfcs/rfc3875.html>