---
layout: post
categories: PHP
title: PHP header()函数相关
meta: PHP header()函数相关
---
* content
{:toc}

## 正文

### header响应头

有时我们会在一些php代码中看到如下的写法：

```php
header( 'Content-type:text/html;charset=utf-8' );
header( 'Expires: Mon, 26 Jul 1997 05:00:00 GMT' );
header( 'Cache-Control: no-store, no-cache, must-revalidate' );
header( 'Cache-Control: post-check=0, pre-check=0', false );
header( 'Pragma: no-cache' ); 
```

我们就会想了，这是什么？做什么用的？有没有详细的用法说明？等等......

下面就来说一下header函数相关的东西。

header() 用于发送原生的 HTTP 头，告诉用户浏览器该做些什么，如页面编码、不储存缓存、页面发生跳转、弹出下载框等。
在这个header() 这个函数前，不能有任何输出，包括空格在内。

header()的语法：header(string,replace,http_response_code)

说明：

| 参数 	|  描述  | 
| :--------   | :--------   |
| string 	| 必需。规定要发送的报头字符串。 | 
| replace 	| 可选。指示该报头是否替换之前的报头，或添加第二个报头。默认是 true（替换）。false（允许相同类型的多个报头）。 | 
| http_response_code 	|  可选。把 HTTP 响应代码强制为指定的值。（PHP 4 以及更高版本可用） | 

```
/**
 * Send a raw HTTP header
 * @link https://php.net/manual/en/function.header.php
 * @param string $string <p>
 * The header string.
 * </p>
 * <p>
 * There are two special-case header calls. The first is a header
 * that starts with the string "HTTP/" (case is not
 * significant), which will be used to figure out the HTTP status
 * code to send. For example, if you have configured Apache to
 * use a PHP script to handle requests for missing files (using
 * the ErrorDocument directive), you may want to
 * make sure that your script generates the proper status code.
 * </p>
 * <p>
 * The second special case is the "Location:" header. Not only does
 * it send this header back to the browser, but it also returns a
 * REDIRECT (302) status code to the browser
 * unless the 201 or
 * a 3xx status code has already been set.
 * </p>
 * @param bool $replace [optional] <p>
 * The optional replace parameter indicates
 * whether the header should replace a previous similar header, or
 * add a second header of the same type. By default it will replace,
 * but if you pass in false as the second argument you can force
 * multiple headers of the same type. For example:
 * </p>
 * @param int $http_response_code [optional] <p>
 * Forces the HTTP response code to the specified value.
 * </p>
 * @return void 
 * @since 4.0
 * @since 5.0
 */
function header ($string, $replace = true, $http_response_code = null) {}
```

常见用法：

1、重定向

    header('Location: http://www.example.com/');

2、指定内容：

    header('Content-type: application/pdf');

3、附件：

    header('Content-type: application/pdf');

  //指定内容为附件，指定下载显示的名字

    header('Content-Disposition: attachment; filename="downloaded.pdf"');

  //打开文件，并输出

    readfile('original.pdf');

  以上代码可以在浏览器产生文件对话框的效果

4、让用户获取最新的资料和数据而不是缓存

    header("Cache-Control: no-cache, must-revalidate"); // HTTP/1.1
    header("Expires: Sat, 26 Jul 1997 05:00:00 GMT");   // 设置临界时间


#### 详细例子

```php
header('HTTP/1.1 200 OK'); // ok 正常访问
header('HTTP/1.1 404 Not Found'); //通知浏览器 页面不存在
header('HTTP/1.1 301 Moved Permanently'); //设置地址被永久的重定向 301
header('Location: http://www.ithhc.cn/'); //跳转到一个新的地址
header('Refresh: 10; url=http://www.ithhc.cn/'); //延迟转向 也就是隔几秒跳转
header('X-Powered-By: PHP/6.0.0'); //修改 X-Powered-By信息
header('Content-language: en'); //文档语言
header('Content-Length: 1234'); //设置内容长度
header('Last-Modified: '.gmdate('D, d M Y H:i:s', $time).' GMT'); //告诉浏览器最后一次修改时间
header('HTTP/1.1 304 Not Modified'); //告诉浏览器文档内容没有发生改变

###内容类型###
header('Content-Type: text/html; charset=utf-8'); //网页编码
header('Content-Type: text/plain'); //纯文本格式
header('Content-Type: image/jpeg'); //JPG、JPEG
header('Content-Type: application/zip'); // ZIP文件
header('Content-Type: application/pdf'); // PDF文件
header('Content-Type: audio/mpeg'); // 音频文件
header('Content-type: text/css'); //css文件
header('Content-type: text/javascript'); //js文件
header('Content-type: application/json'); //json
header('Content-type: application/pdf'); //pdf
header('Content-type: text/xml'); //xml
header('Content-Type: application/x-shockw**e-flash'); //Flash动画

######

###声明一个下载的文件###
header('Content-Type: application/octet-stream');
header('Content-Disposition: attachment; filename="ITblog.zip"');
header('Content-Transfer-Encoding: binary');
readfile('test.zip');
######

###对当前文档禁用缓存###
header('Cache-Control: no-cache, no-store, max-age=0, must-revalidate');
header('Expires: Mon, 26 Jul 1997 05:00:00 GMT');
######

###显示一个需要验证的登陆对话框###
header('HTTP/1.1 401 Unauthorized');
header('WWW-Authenticate: Basic realm="Top Secret"');
######


###声明一个需要下载的xls文件###
header('Content-Disposition: attachment; filename=ithhc.xlsx');
header('Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
header('Content-Length: '.filesize('./test.xls'));
header('Content-Transfer-Encoding: binary');
header('Cache-Control: must-revalidate');
header('Pragma: public');
readfile('./test.xls');
###### 
```

### header请求头

看了上面内容，我们不禁要问：那么header的请求头信息呢，怎么获取？

我们知道 超全局变量有：$_REQUEST，$_GET，$_POST，$_FILES，$_SERVER，$_COOKIE，$_SESSION 等，
那么是不是也有个获取header请求头信息的 $_HEADER 呢？

答案是没有！

请求经过网络服务器Nginx转发，header信息都成了服务器信息，所以header请求头在$_SERVER中。

我们在$_SERVER信息中可以看到好多以`HTTP_`开头的参数：
```
...
["HTTP_COOKIE"]=>  string(420) "_identity=a8d00b623A2%3A%3A%22_identity%22%3Bi%3A13B%7D; PHPSESSID=6qrpjm5kvk4; _csrf=4c446993231ac7695850e1d4cA5%3A%22_csrf%22%3Bi%qKP56CeKoyskz87%22%3B%7D"
["HTTP_CONNECTION"]=>  string(10) "keep-alive"
["HTTP_X_REQUESTED_WITH"]=>  string(14) "XMLHttpRequest"
["HTTP_REFERER"]=>  string(18) "http://admin.host/"
["HTTP_ACCEPT_ENCODING"]=>  string(13) "gzip, deflate"
["HTTP_ACCEPT_LANGUAGE"]=>  string(59) "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2"
["HTTP_ACCEPT"]=>  string(22) "text/html, */*; q=0.01"
["HTTP_USER_AGENT"]=>  string(68) "Mozilla/5.0 (X11; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0"
["HTTP_HOST"]=>  string(10) "admin.host"
...
```

这些信息就是我们需要的header请求头信息。

我们写一个简单的php文件测试一下：
```
<?php
// header.php

print_r($_SERVER);
?>
```

我们使用postman工具或者curl命令请求下这个地址，并加入我们自定义的一个头 `token: 123456`
```
curl -H "token: 123456" http://localhost/header.php
```

在输出中我们可以找到一项：
```
["HTTP_TOKEN"]=> string(6) "123456"
```

而这个`HTTP_TOKEN`就是我们想要的东西，我们传入的小写`token`， 在这里也转换成了大写。

我们传递两个header：
```
curl -H "test_token: 654321" -H "token: 123456" http://localhost/header.php
```

测试发现，我们在输出中只找到了`HTTP_TOKEN`，缺没有找到我们预想的`HTTP_TEST_TOKEN`。
不要着急，我们把下划线`_`换成连字符`-`试试：
```
curl -H "test-token: 654321" -H "token: 123456" http://localhost/header.php
```

看看输出，这下两个header都找到了：
```
["HTTP_TEST_TOKEN"]=> string(6) "654321"
["HTTP_TOKEN"]=> string(6) "123456"
```

综上所述：传递header的键值，只能是单个词或者以中划线`-`连接的词。不然php会忽略。

好了接下来我们在实际应用中，只要把$_SERVER中的以HTTP_开头的下标都提取出来然后转换成首字母大写的格式就好了。
```php
<?php
function parse_headers(){
    $headers = [];
    foreach ($_SERVER as $name => $value) {
        if (substr($name, 0, 5) == 'HTTP_') {
            $headers[str_replace(' ', '-', ucwords(strtolower(str_replace('_', ' ', substr($name, 5)))))] = $value;
        }
    }
    return $headers;
}

$headers = parse_headers();
print_r($headers);
?>
```

看看输出：
```
array(5) {
  ["Host"]=>  string(9) "localhost"
  ["User-Agent"]=>  string(11) "curl/7.65.3"
  ["Accept"]=>  string(3) "*/*"
  ["Test-Token"]=>  string(6) "654321"
  ["Token"]=> string(6)  "123456"
}
```

<br/><br/><br/><br/><br/>
## 参考资料

PHP 手册 函数参考 其它服务 网络 网络 函数 header <https://www.php.net/manual/zh/function.header.php>

[HTTP 函数](http://www.w3school.com.cn/php/php_ref_http.asp)

[header() 函数](http://www.w3school.com.cn/php/func_http_header.asp)

[php官网header函数](http://cn.php.net/manual/zh/function.header.php)

[百度百科header](http://baike.baidu.com/link?url=KDdQJrpOzxE_Z6fGuBuqsG3oSuglbbTVqmaBVwrSASgCPf43iQcWvjZRxjUkIR6koej9T1QyVltyBe8RaFWM6K)

[header函数的几大作用](http://www.cnblogs.com/huyihao/p/6004800.html)

[页面编码声明方法详解(header或meta)](http://www.jb51.net/article/22501.htm)

PHP之常见Header详解 <https://zhuanlan.zhihu.com/p/66573422>

PHP如何解析header头部信息 <https://zhuanlan.zhihu.com/p/134565445>

使用PHP开发一个HTTP服务器 <https://zhuanlan.zhihu.com/p/32981060>
