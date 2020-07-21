---
layout: post
categories: PHP
title: PHP $_SERVER详解
meta: PHP $_SERVER详解
---
* content
{:toc}

### 正文

php提供了一些预定义变量，这些变量将所有的外部变量表示成内建环境变量，并且将错误信息表示成返回头。 

外部变量 是说 来自 PHP 之外的变量，如 HTML 表单（GET 和 POST）、 HTTP Cookies 等。

内建环境变量有：
```
超全局变量 — 超全局变量是在全部作用域中始终可用的内置变量
    $GLOBALS — 引用全局作用域中可用的全部变量
    $_SERVER — 服务器和执行环境信息
    $_GET — HTTP GET 变量
    $_POST — HTTP POST 变量
    $_FILES — HTTP 文件上传变量
    $_COOKIE — HTTP Cookies
    $_SESSION — Session 变量
    $_REQUEST — HTTP Request 变量
    $_ENV — 环境变量
    
$php_errormsg — 前一个错误信息
$HTTP_RAW_POST_DATA — 原生POST数据
$http_response_header — HTTP 响应头
$argc — 传递给脚本的参数数目
$argv — 传递给脚本的参数数组
```

#### $_SERVER

$_SERVER 是一个包含了诸如头信息(header)、路径(path)、以及脚本位置(script locations)等等信息的数组。
这个数组中的项目由 Web 服务器创建。不能保证每个服务器都提供全部参数；服务器可能会忽略一些，或者提供一些没有在这里列举出来的参数。

我们可以把$_SERVER 数组打印出来看一下：

![]({{site.baseurl}}/images/20190901/20190901001413.jpeg)

再放一张最近的，有对比，更明了：

![]({{site.baseurl}}/images/20190901/20190901001448.jpeg)

看一下linux下的：
```
array(34) {
  ["USER"]=>  string(8) "www-data"
  ["HOME"]=>  string(8) "/var/www"
  ["HTTP_COOKIE"]=>  string(420) "_identity=a8d00b623A2%3A%3A%22_identity%22%3Bi%3A13B%7D; PHPSESSID=6qrpjm5kvk4; _csrf=4c446993231ac7695850e1d4cA5%3A%22_csrf%22%3Bi%qKP56CeKoyskz87%22%3B%7D"
  ["HTTP_CONNECTION"]=>  string(10) "keep-alive"
  ["HTTP_X_REQUESTED_WITH"]=>  string(14) "XMLHttpRequest"
  ["HTTP_REFERER"]=>  string(18) "http://admin.host/"
  ["HTTP_ACCEPT_ENCODING"]=>  string(13) "gzip, deflate"
  ["HTTP_ACCEPT_LANGUAGE"]=>  string(59) "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2"
  ["HTTP_ACCEPT"]=>  string(22) "text/html, */*; q=0.01"
  ["HTTP_USER_AGENT"]=>  string(68) "Mozilla/5.0 (X11; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0"
  ["HTTP_HOST"]=>  string(10) "admin.host"
  ["SCRIPT_FILENAME"]=>  string(39) "/var/www/YiiAdmin/admin/web/index.php"
  ["REDIRECT_STATUS"]=>  string(3) "200"
  ["SERVER_NAME"]=>  string(10) "admin.host"
  ["SERVER_PORT"]=>  string(2) "80"
  ["SERVER_ADDR"]=>  string(9) "127.0.0.1"
  ["REMOTE_PORT"]=>  string(5) "34058"
  ["REMOTE_ADDR"]=>  string(9) "127.0.0.1"
  ["SERVER_SOFTWARE"]=>  string(12) "nginx/1.10.3"
  ["GATEWAY_INTERFACE"]=>  string(7) "CGI/1.1"
  ["REQUEST_SCHEME"]=>  string(4) "http"
  ["SERVER_PROTOCOL"]=>  string(8) "HTTP/1.1"
  ["DOCUMENT_ROOT"]=>  string(29) "/var/www/YiiAdmin/admin/web"
  ["DOCUMENT_URI"]=>  string(10) "/index.php"
  ["REQUEST_URI"]=>  string(32) "/support/redpack?_=1567234611976"
  ["SCRIPT_NAME"]=>  string(10) "/index.php"
  ["CONTENT_LENGTH"]=>  string(0) ""
  ["CONTENT_TYPE"]=>  string(0) ""
  ["REQUEST_METHOD"]=>  string(3) "GET"
  ["QUERY_STRING"]=>  string(15) "_=1567234611976"
  ["FCGI_ROLE"]=>  string(9) "RESPONDER"YiiAdmin
  ["PHP_SELF"]=>  string(10) "/index.php"
  ["REQUEST_TIME_FLOAT"]=>  float(1567234621.2886)
  ["REQUEST_TIME"]=>  int(1567234621)
}
```

下表列出了一部分 $_SERVER 变量中的重要元素:

```php
$_SERVER['PHP_SELF']  // 当前正在执行 脚本的文件名，与 document root相关。

$_SERVER['DOCUMENT_ROOT']  // 当前运行脚本所在的文档根目录。 在服务器配置文件中定义。

$_SERVER['argv']  // 传递给该 脚本的参数。

$_SERVER['argc']  // 包含传递给程序的 命令行参数的个数（如果运行在命令行模式）。

$_SERVER['GATEWAY_INTERFACE']  // 服务器使用的 CGI 规范的版本。例如，“CGI/1.1”。

$_SERVER['SERVER_NAME']  // 当前运行脚本所在的服务器的主机名。如果脚本运行于虚拟主机中，该名称是由那个虚拟主机所设置的值决定。

$_SERVER['SERVER_SOFTWARE']  // 服务器标识的字串，在响应请求时的头部中给出。

$_SERVER['SERVER_PROTOCOL']  // 请求页面时通信协议的名称和版本。例如，“HTTP/1.0”。

$_SERVER['REQUEST_METHOD']  // 访问页面时的请求方法。例如：“GET”、 “HEAD”， “POST”， “PUT”。

$_SERVER['QUERY_STRING']  // 查询(query)的字符串。

$_SERVER['HTTP_ACCEPT']  // 当前请求的 Accept: 头部的内容。

$_SERVER['HTTP_ACCEPT_CHARSET']  // 当前请求的 Accept-Charset: 头部的内容。例如：“iso-8859-1,*,utf-8”。

$_SERVER['HTTP_ACCEPT_ENCODING']  // 当前请求的 Accept-Encoding: 头部的内容。例如：“gzip”。

$_SERVER['HTTP_ACCEPT_LANGUAGE']  // 当前请求的 Accept-Language: 头部的内容。例如：“en”。

$_SERVER['HTTP_CONNECTION']  // 当前请求的 Connection: 头部的内容。例如：“Keep-Alive”。

$_SERVER['HTTP_HOST']  // 当前请求的 Host: 头部的内容。

$_SERVER['HTTP_REFERER']  // 链接到当前页面的前一页面的 URL 地址。引导用户代理到当前页的前一页的地址（如果存在）。由 user agent 设置决定。并不是所有的用户代理都会设置该项，有的还提供了修改 HTTP_REFERER 的功能。简言之，该值并不可信。)

$_SERVER['HTTP_USER_AGENT']  // 当前请求的 User-Agent: 头部的内容。

$_SERVER['HTTPS']  // 如果通过https访问,则被设为一个非空的值(on)，否则返回off

$_SERVER['REMOTE_ADDR']  // 正在浏览当前页面用户的 IP 地址。

$_SERVER['REMOTE_HOST']  // 正在浏览当前页面用户的 主机名。

$_SERVER['REMOTE_PORT']  // 用户连接到服务器时所使用的端口。

$_SERVER['SCRIPT_FILENAME']  // 当前执行 脚本的 绝对路径名。

$_SERVER['SERVER_ADMIN']  // 管理员信息 。该值指明了 Apache 服务器配置文件中的 SERVER_ADMIN 参数。如果脚本运行在一个虚拟主机上，则该值是那个虚拟主机的值。

$_SERVER['SERVER_PORT']  // Web 服务器使用的端口。默认值为 "80"。如果使用 SSL 安全连接，则这个值为用户设置的 HTTP 端口。

$_SERVER['SERVER_SIGNATURE']  // 包含服务器版本和 虚拟主机名的字符串。

$_SERVER['PATH_TRANSLATED']  // 当前脚本所在文件系统（非文档根目录）的基本路径。这是在服务器进行虚拟到真实路径的映像后的结果。

$_SERVER['SCRIPT_NAME']  // 包含当前脚本的路径。这在页面需要指向自己时非常有用。__FILE__ 常量包含当前脚本(例如包含文件)的完整路径和文件名。

$_SERVER['REQUEST_URI']  // 访问此页面所需的 URI。例如，“/index.html”

$_SERVER['PHP_AUTH_USER']  // 当 PHP 运行在 Apache 模块方式下，并且正在使用 HTTP 认证功能，这个变量便是用户输入的用户名。

$_SERVER['PHP_AUTH_PW'] #当 PHP 运行在 Apache 模块方式下，并且正在使用 HTTP 认证功能，这个变量便是用户输入的密码。

$_SERVER['AUTH_TYPE'] #当 PHP 运行在 Apache 模块方式下，并且正在使用 HTTP 认证功能，这个变量便是认证的类型 
```

![]({{site.baseurl}}/images/20190901/20190901001514.jpeg)


$_SERVER 除了查看 外部服务器变量，有是获取到的可能是错误的，我们还可以修正为正确的环境变量，如：
```
// set correct script paths
$_SERVER['SERVER_NAME'] = 'localhost';
$_SERVER['SERVER_PORT'] = '80';
```


<br/><br/><br/><br/><br/>
### 参考资料

PHP 手册 语言参考 预定义变量 $_SERVER <https://www.php.net/manual/zh/reserved.variables.server.php>

PHP 手册 语言参考 预定义变量 <https://www.php.net/manual/zh/reserved.variables.php>

PHP 手册 语言参考 变量 来自 PHP 之外的变量 <https://www.php.net/manual/zh/language.variables.external.php>    

<https://my.oschina.net/miaowang/blog/299553>

<http://www.jb51.net/article/20894.htm>

<http://www.jb51.net/article/17108.htm>
