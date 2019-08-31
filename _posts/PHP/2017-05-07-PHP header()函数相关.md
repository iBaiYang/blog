---
layout: post
categories: PHP
title: PHP header()函数相关
meta: PHP header()函数相关
---
* content
{:toc}

### 正文

有时我们会在一些php代码中看到如下的写法：

```php
header('Content-type:text/html;charset=utf-8');
header( 'Expires: Mon, 26 Jul 1997 05:00:00 GMT' );
header( 'Cache-Control: no-store, no-cache, must-revalidate' );
header( 'Cache-Control: post-check=0, pre-check=0', false );
header( 'Pragma: no-cache' ); 
```

我们就会想了，这是什么？做什么用的？有没有详细的用法说明？等等......

下面就来说一下header函数相关的东西。

header() 用于发送原生的 HTTP 头，告诉用户浏览器该做些什么，如页面编码、不储存缓存、页面发生跳转、弹出下载框等。在这个header() 这个函数前，不能有任何输出，包括空格在内。

header()的语法：header(string,replace,http_response_code)

说明：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190831/20190831233043.jpeg)

常见用法：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190831/20190831233141.jpeg)

详细例子：

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



<br/><br/><br/><br/><br/>
### 参考资料

[HTTP 函数](http://www.w3school.com.cn/php/php_ref_http.asp)

[header() 函数](http://www.w3school.com.cn/php/func_http_header.asp)

[php官网header函数](http://cn.php.net/manual/zh/function.header.php)

[百度百科header](http://baike.baidu.com/link?url=KDdQJrpOzxE_Z6fGuBuqsG3oSuglbbTVqmaBVwrSASgCPf43iQcWvjZRxjUkIR6koej9T1QyVltyBe8RaFWM6K)

[header函数的几大作用](http://www.cnblogs.com/huyihao/p/6004800.html)

[页面编码声明方法详解(header或meta)](http://www.jb51.net/article/22501.htm)
