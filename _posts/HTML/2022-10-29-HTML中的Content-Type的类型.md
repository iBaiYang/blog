---
layout: post
categories: HTML
title: HTML中的Content-Type的类型
meta: HTML中的Content-Type的类型
---
* content
{:toc}

## 正文

Content-Type 内容类型，是消息中非常重要的内容，表示文档属于什么MIME类型。
用于定义网络文件的类型和网页的编码，决定文件接收方将以什么形式、什么编码读取这个文件。
格式为 `Content-Type: [type]/[subtype]; parameter`。
例如最常见的就是`Content-Type: text/html; charset=UTF-8`，它的意思是说返回内容是文本类型，这个文本又是HTML格式的。

### 常见的媒体格式类型如下：

text/html ： HTML格式

text/plain ：纯文本格式，空格转换为"+"号，但不对特殊字符编码

text/xml ： XML格式

image/gif ：gif图片格式

image/jpeg ：jpg图片格式

image/png：png图片格式

### 以application开头的媒体格式类型：

application/xhtml+xml ：XHTML格式

application/xml ： XML数据格式

application/atom+xml ：Atom XML聚合格式

application/json ： JSON数据格式

application/pdf ：pdf格式

application/msword ： Word文档格式

application/octet-stream ： 二进制流数据（如常见的文件下载）

application/x-www-form-urlencoded ： form表单中默认的encType（encodetype，是编码类型的意思），
form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）

multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式

**注意**

enctype 规定了form表单在发送到服务器时候编码方式，有如下的三个值：

application/x-www-form-urlencoded 不是不能上传文件，是只能上传`文本格式`的文件。
用文本的形式传输 MP3 等大型文件的时候，使用这种编码就显得效率低下。

multipart/form-data 是指表单数据中由多部分构成，既有文本数据，又有文件等二进制数据。
multipart/form-data 是将文件以二进制的形式上传，这样可以实现`多种类型`的文件上传。

text/plain：纯文本的传输。空格转换为"+"号，但不对特殊字符编码。

## 常用对照表

一些常用对照表，可以参阅 <https://tool.oschina.net/commons/> ，
有 HTTP Content-type、HTML转义字符、RGB颜色参考、ASCII对照表、HTTP状态码详解、
运算符优先级、TCP/UDP常见端口参考、网页字体参考 等内容。

## 实例

### xml请求

在一次项目开发中，碰到一种情况，微信公众号中配置回调地址，但这个地址已经配置为B项目了，
A项目也需要这个回调进行相应后续处理，怎么解决呢？

有两种解决办法：一是在B项目中把A项目的这块业务代码写过去，这样会导致代码耦合，不利于以后项目修改迭代；
二是B项目接收到请求后把请求体原样转发给A项目，这是最理想的解决办法。

不过查阅微信开发文档又发现微信回调使用的是xml格式，怎么进行请求体的获取和转发呢。
下面写个实例：
```php
<?php
// 获取请求体
$xml_data = isset($GLOBALS['HTTP_RAW_POST_DATA']) ? $GLOBALS['HTTP_RAW_POST_DATA'] : file_get_contents("php://input");

// 请求地址
$url = 'http://www.b.com';

$header = "Content-type: text/xml";  //定义content-type为xml
$ch = curl_init();   //初始化curl
curl_setopt($ch, CURLOPT_URL, $url);  //设置链接
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);  //设置是否返回信息
curl_setopt($ch, CURLOPT_HTTPHEADER, $header);  //设置HTTP头
curl_setopt($ch, CURLOPT_POST, 1);  //设置为POST方式
curl_setopt($ch, CURLOPT_POSTFIELDS, $xml_data);  //POST数据
$response = curl_exec($ch);  //接收返回信息
if(curl_errno($ch)){  //出错则显示错误信息
    echo curl_error($ch);
}
curl_close($ch);   //关闭curl链接
echo $response;  //显示返回信息
```


<br/><br/><br/><br/><br/>
## 参考资料 

HTML中的Content-Type的类型 <https://blog.csdn.net/qq_24147051/article/details/90477756>

常用对照表 <https://tool.oschina.net/commons/>
