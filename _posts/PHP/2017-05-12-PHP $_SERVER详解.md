---
layout: post
categories: PHP
title: PHP $_SERVER详解
meta: PHP $_SERVER详解
---
* content
{:toc}

### 正文

$_SERVER 是一个包含了诸如头信息(header)、路径(path)、以及脚本位置(script locations)等等信息的数组。这个数组中的项目由 Web 服务器创建。不能保证每个服务器都提供全部参数；服务器可能会忽略一些，或者提供一些没有在这里列举出来的参数。

我们可以把$_SERVER 数组打印出来看一下：

![](http://s3.sinaimg.cn/mw690/001XbchKzy76uyQzjrA62&690)

再放一张最近的，有对比，更明了：

![](http://s3.sinaimg.cn/mw690/001XbchKzy7b1e64CY262&690)

下表列出了所有 $_SERVER 变量中的重要元素:

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

$_SERVER[' HTTP_REFERER']  // 链接到当前页面的前一页面的 URL 地址。引导用户代理到当前页的前一页的地址（如果存在）。由 user agent 设置决定。并不是所有的用户代理都会设置该项，有的还提供了修改 HTTP_REFERER 的功能。简言之，该值并不可信。)

$_SERVER[' HTTP_USER_AGENT']  // 当前请求的 User-Agent: 头部的内容。

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

![](http://s12.sinaimg.cn/mw690/001XbchKzy73SUWv7Orfb&690)

<br/><br/><br/><br/><br/>
### 参考资料

<https://my.oschina.net/miaowang/blog/299553>

<http://www.jb51.net/article/20894.htm>

<http://www.jb51.net/article/17108.htm>
