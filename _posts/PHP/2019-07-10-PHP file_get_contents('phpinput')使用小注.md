---
layout: post
categories: PHP
title: PHP file_get_contents('php://input')使用小注
meta: file_get_contents('php://input')使用小注
---
* content
{:toc}

### 正文

这个问题比较接近php底层了。

#### file_get_contents

file_get_contents() 把整个文件读入一个字符串中。

该函数是用于把文件的内容读入到一个字符串中的首选方法。如果服务器操作系统支持，还会使用内存映射技术来增强性能。

语法 file_get_contents(path,include_path,context,start,max_length) 

提示：该函数是二进制安全的。（意思是二进制数据（如图像）和字符数据都可以使用此函数写入。）

#### php://input

php:// — 访问各个输入/输出流（I/O streams）

PHP 提供了一些杂项输入/输出（IO）流，允许访问 PHP 的输入输出流、标准输入输出和错误描述符， 
内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。 

php://input 是个可以访问请求的原始数据的只读流。 POST 请求的情况下，最好使用 php://input 来代替 $HTTP_RAW_POST_DATA，
因为它不依赖于特定的 php.ini 指令。 而且，这样的情况下 $HTTP_RAW_POST_DATA 默认没有填充， 
比激活 always_populate_raw_post_data 潜在需要更少的内存。 enctype="multipart/form-data" 的时候 php://input 是无效的。 

$HTTP_RAW_POST_DATA 在php7.0中被移除了。

post提交的原始数据是这样的：key1=value1&key2=value2

$_POST是php对post原始数据分割后填充的，最后形如：$_POST = 【"key1" => "value1", "key2" => "value2"】;

php://input获取到的就是post提交的原始数据，如key1=value1&key2=value2，但一般更多的是获取post提交的json格式化数据包，如：
```
$body =  @file_get_contents('php://input');
$postData = json_decode($body,true);
echo $postData['key1'];
```

注意点：

1. Content-Type仅在取值为application/x-www-data-urlencoded和multipart/form-data两种情况下，PHP才会将http请求数据包中相应的数据填入全局变量$_POST
2. PHP不能识别的Content-Type类型的时候，会将http请求包中相应的数据填入变量$HTTP_RAW_POST_DATA
3. 只有Coentent-Type为multipart/form-data的时候，PHP不会将http请求数据包中的相应数据填入php://input，否则其它情况都会。填入的长度，由Coentent-Length指定。
4. 只有Content-Type为application/x-www-data-urlencoded时，php://input数据才跟$_POST数据相一致。
5. php://input数据总是跟$HTTP_RAW_POST_DATA相同，但是php://input比$HTTP_RAW_POST_DATA更凑效，且不需要特殊设置php.ini
6. PHP会将PATH字段的query_path部分，填入全局变量$_GET。通常情况下，GET方法提交的http请求，body为空。

<br/><br/><br/><br/><br/>
### 参考资料

file_get_contents() <https://www.runoob.com/php/func-filesystem-file-get-contents.html> 

file_get_contents <https://www.php.net/manual/zh/function.file-get-contents.php>

php:// <https://www.php.net/manual/zh/wrappers.php.php>

("php://input")的使用方法包含HTTP请求内容发送 <https://www.jianshu.com/p/14f7d3e9b362?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation>

file_get_contents('php://input') 和POST的区别 <https://www.cnblogs.com/phpper/p/9574419.html>

file_get_contents('php://input')的使用解析 <https://www.jianshu.com/p/00b558bb33eb>

php://input、$_POST、$_GET、$HTTP_RAW_POST_DATA 关系梳理 <https://www.jianshu.com/p/c24eadc6c0dc?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation>

深入剖析PHP输入流 php://input <http://www.nowamagic.net/academy/detail/12220520>

$HTTP_RAW_POST_DATA <https://www.php.net/manual/zh/reserved.variables.httprawpostdata.php>