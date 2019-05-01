---
layout: post
categories: PHP
title: PHP 汉字字符串相关处理（如mb_substr等）
meta: 汉字与数字和英文字母在PHP中的区别还是挺大的，下面讲一下。
---
* content
{:toc}

### 正文

汉字与数字和英文字母在PHP中的区别还是挺大的，数字和英文字母占一个字节，汉字占两个字节，
所以PHP对汉字需要特殊处理。

如截取字符串长度，数字和英文字母用substr(),汉字要用mb_substr()；
如计算字符串长度，数字和英文字母用strlen(),汉字要用mb_strlen()；

如果对一个包含汉字的字符串截取用了substr()，可能导致意想不到的错误，如：

```
$str = 'a一二三四五六七';
$str2 = $res1 = substr($str, 0, 1);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a
echo $res2 .'\n';  // a
```
```
$str = 'a一二三四五六七';
$str2 = $res1 = substr($str, 0, 2);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a�
echo $res2 .'\n';  // 为空
```

```
$str = 'a一二三四五六七';
$str2 = $res1 = substr($str, 0, 3);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a��
echo $res2 .'\n';  // 为空
```

```
$str = 'a一二三四五六七';
$str2 = $res1 = substr($str, 0, 4);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a一
echo $res2 .'\n';  // "a\u4e00"
```

这里json_encode()后，你就能看到意向之外的结果。所以中文专用的字符处理函数，如：

```
$str = 'a一二三四五六七';
$str2 = $res1 = mb_substr($str, 0, 1);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a
echo $res2 .'\n';  // a
```
```
$str = 'a一二三四五六七';
$str2 = $res1 = mb_substr($str, 0, 2);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a一
echo $res2 .'\n';  // "a\u4e00"
```

```
$str = 'a一二三四五六七';
$str2 = $res1 = mb_substr($str, 0, 3);
$res2 = json_encode($str2);

echo $res1 .'\n';  // a一二
echo $res2 .'\n';  // "a\u4e00\u4e8c"
```

json-encode()，对不认识的符号不能处理，会返回false。

<br/><br/>
插一句题外话，对于换行：

Unix和Linux用的是 \n

Mac用的是 \r

Windows用的是 \r\n

可以发现还有一个新的大操作系统的机会：\n\r  

^_-

<br/><br/><br/><br/><br/>
### 参考资料

<http://www.php.net/manual/zh/function.mb-substr.php>

<http://www.php.net/mb_strlen>

<http://php.net/manual/en/function.json-encode.php>

<http://www.dooccn.com/php7/>

<http://www.w3school.com.cn/php/php_ref_string.asp>

<http://www.runoob.com/php/php-ref-string.html>

