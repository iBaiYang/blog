---
layout: post
categories: PHP
title: PHP ob_start缓冲区使用举例
meta: ob_start缓冲区使用举例
---
* content
{:toc}

### 正文

从一个网址或文件读取内容，然后写入本地文件：

```
$uri = "内容地址";
// 上传路径
$uploadfile = realpath(Yii::$app->getBasePath().'/web/tmp/test.txt');   // 文件绝对地址
// 开始缓冲
ob_start();
readfile($uri);
$file = ob_get_contents();
// 清除缓冲
ob_end_clean();
$size = strlen($file);
$src = fopen($uploadfile, 'a');
fwrite($src, $file);
fclose($src);

// $uploadfile  文件地址
// $size  文件大小
```

<br/><br/><br/><br/><br/>
### 参考资料

<http://www.php.net/manual/zh/function.ob-start.php>

<https://www.cnblogs.com/w10234/p/5480670.html>

readfile() 函数 <http://www.w3school.com.cn/php/func_filesystem_readfile.asp>

fopen() 函数 <http://www.w3school.com.cn/php/func_filesystem_fopen.asp>

fwrite() 函数 <http://www.w3school.com.cn/php/func_filesystem_fwrite.asp>






