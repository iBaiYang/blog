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

或者：
```
array(51) {
  ["PHP_EXTRA_CONFIGURE_ARGS"]=>
  string(77) "--enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data --disable-cgi"
  ["HOSTNAME"]=>
  string(12) "4ca2d34d9eb2"
  ["PHP_INI_DIR"]=>
  string(18) "/usr/local/etc/php"
  ["HOME"]=>
  string(8) "/var/www"
  ["PHP_LDFLAGS"]=>
  string(34) "-Wl,-O1 -Wl,--hash-style=both -pie"
  ["PHP_CFLAGS"]=>
  string(40) "-fstack-protector-strong -fpic -fpie -O2"
  ["PHP_MD5"]=>
  string(0) ""
  ["PHP_VERSION"]=>
  string(6) "7.1.30"
  ["GPG_KEYS"]=>
  string(122) "A917B1ECDA84AEC2B568FED6F50ABC807BD5DCD0 528995BFEDFBA7191D46839EF9BA0ADA31CBD89E 1729F83938DA44E27BA0F4D3DBDB397470D12172"
  ["PHP_CPPFLAGS"]=>
  string(40) "-fstack-protector-strong -fpic -fpie -O2"
  ["PHP_ASC_URL"]=>
  string(62) "https://www.php.net/get/php-7.1.30.tar.xz.asc/from/this/mirror"
  ["PHP_URL"]=>
  string(58) "https://www.php.net/get/php-7.1.30.tar.xz/from/this/mirror"
  ["PATH"]=>
  string(60) "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
  ["PHPIZE_DEPS"]=>
  string(76) "autoconf 		dpkg-dev 		file 		g++ 		gcc 		libc-dev 		make 		pkg-config 		re2c"
  ["PWD"]=>
  string(13) "/var/www/html"
  ["PHP_SHA256"]=>
  string(64) "6310599811536dbe87e4bcf212bf93196bdfaff519d0c821e4c0068efd096a7c"
  ["USER"]=>
  string(8) "www-data"
  ["HTTP_CACHE_CONTROL"]=>
  string(9) "max-age=0"
  ["HTTP_UPGRADE_INSECURE_REQUESTS"]=>
  string(1) "1"
  ["HTTP_COOKIE"]=>
  string(1280) "sensorsdata2015jssdkcross=%7B%22distinct_id%22%3A%22zhengjunyuan%22%2C%22%24device_id%22%3A%22175da0e8a50132-02155b944a92658-386b4644-1049088-175da0e8a522d2%22%2C%22props%22%3A%7B%22%24latest_referrer%22%3A%22%22%2C%22%24latest_traffic_source_type%22%3A%22%E7%9B%B4%E6%8E%A5%E6%B5%81%E9%87%8F%22%2C%22%24latest_search_keyword%22%3A%22%E6%9C%AA%E5%8F%96%E5%88%B0%E5%80%BC_%E7%9B%B4%E6%8E%A5%E6%89%93%E5%BC%80%22%7D%2C%22first_id%22%3A%22175da0e8a50132-02155b944a92658-386b4644-1049088-175da0e8a522d2%22%7D; MKDataAnalysis=%7B%22%24distinct_id%22%3A%22175da0e8a9033f-03555212d5097d8-386b4644-1049088-175da0e8a9194%22%2C%22%24user_id%22%3A%22zhengjunyuan%22%2C%22%24device_id%22%3A%22175da0e8a9033f-03555212d5097d8-386b4644-1049088-175da0e8a9194%22%2C%22%24latest_traffic_source_type%22%3A%22%E8%8E%B7%E5%8F%96url%E5%BC%82%E5%B8%B8%22%2C%22%24latest_referrer%22%3A%22%22%2C%22%24latest_referrer_host%22%3A%22%22%2C%22%24latest_search_keyword%22%3A%22%E6%9C%AA%E5%8F%96%E5%88%B0%E5%80%BC_%E7%9B%B4%E6%8E%A5%E6%89%93%E5%BC%80%22%7D; _identity=e69fcf5552e88b1b599465f0dce0021a410e63e316b4544e09723fb113f6b724a%3A2%3A%7Bi%3A0%3Bs%3A9%3A%22_identity%22%3Bi%3A1%3Bs%3A48%3A%22%5B973%2C%22IWY1_81yjeneXT5D3EWvHz6K-3qpDN9j%22%2C2592000%5D%22%3B%7D; PHPSESSID=533a84b9650c4b7d63656156b0a06c75"
  ["HTTP_CONNECTION"]=>
  string(10) "keep-alive"
  ["HTTP_ACCEPT_ENCODING"]=>
  string(13) "gzip, deflate"
  ["HTTP_ACCEPT_LANGUAGE"]=>
  string(59) "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2"
  ["HTTP_ACCEPT"]=>
  string(63) "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8"
  ["HTTP_USER_AGENT"]=>
  string(68) "Mozilla/5.0 (X11; Linux x86_64; rv:70.0) Gecko/20100101 Firefox/70.0"
  ["HTTP_HOST"]=>
  string(10) "admin.host"
  ["REDIRECT_STATUS"]=>
  string(3) "200"
  ["SERVER_NAME"]=>
  string(10) "admin.host"
  ["SERVER_PORT"]=>
  string(2) "80"
  ["SERVER_ADDR"]=>
  string(10) "172.17.0.3"
  ["REMOTE_PORT"]=>
  string(5) "40684"
  ["REMOTE_ADDR"]=>
  string(10) "172.17.0.1"
  ["SERVER_SOFTWARE"]=>
  string(12) "nginx/1.19.4"
  ["GATEWAY_INTERFACE"]=>
  string(7) "CGI/1.1"
  ["REQUEST_SCHEME"]=>
  string(4) "http"
  ["SERVER_PROTOCOL"]=>
  string(8) "HTTP/1.1"
  ["DOCUMENT_ROOT"]=>
  string(34) "/var/www/html/YiiAdmin/admin/web"
  ["DOCUMENT_URI"]=>
  string(10) "/index.php"
  ["REQUEST_URI"]=>
  string(1) "/"
  ["SCRIPT_NAME"]=>
  string(10) "/index.php"
  ["CONTENT_LENGTH"]=>
  string(0) ""
  ["CONTENT_TYPE"]=>
  string(0) ""
  ["REQUEST_METHOD"]=>
  string(3) "GET"
  ["QUERY_STRING"]=>
  string(0) ""
  ["SCRIPT_FILENAME"]=>
  string(44) "/var/www/html/YiiAdmin/admin/web/index.php"
  ["FCGI_ROLE"]=>
  string(9) "RESPONDER"
  ["PHP_SELF"]=>
  string(10) "/index.php"
  ["REQUEST_TIME_FLOAT"]=>
  float(1607000980.2391)
  ["REQUEST_TIME"]=>
  int(1607000980)
  ["argv"]=>
  array(0) {
  }
  ["argc"]=>
  int(0)
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


$_SERVER 除了查看 外部服务器变量，有时获取到的可能是错误的，我们还可以修正为正确的环境变量，如：
```
// set correct script paths
$_SERVER['SERVER_NAME'] = 'localhost';
$_SERVER['SERVER_PORT'] = '80';
```

#### 拓展

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

PHP 手册 语言参考 预定义变量 $_SERVER <https://www.php.net/manual/zh/reserved.variables.server.php>

PHP 手册 语言参考 预定义变量 <https://www.php.net/manual/zh/reserved.variables.php>

PHP 手册 语言参考 变量 来自 PHP 之外的变量 <https://www.php.net/manual/zh/language.variables.external.php>    

<https://my.oschina.net/miaowang/blog/299553>

<http://www.jb51.net/article/20894.htm>

<http://www.jb51.net/article/17108.htm>


<http://www.php.net/manual/en/reserved.variables.server.php>

<http://php.net/manual/zh/reserved.variables.server.php>

超全局变量 <http://php.net/manual/zh/language.variables.superglobals.php>

$GLOBALS  <http://php.net/manual/zh/reserved.variables.globals.php>

变量范围 global 关键字 <http://php.net/manual/zh/language.variables.scope.php> 

PHP 手册 语言参考 <http://php.net/manual/zh/langref.php>

PHP 手册 <http://php.net/manual/zh/index.php>

RFC 3875 - The Common Gateway Interface (CGI) Version 1.1 <RFC 3875 - The Common Gateway Interface (CGI) Version 1.1>  <http://www.faqs.org/rfcs/rfc3875.html>


