---
layout: post
categories: MySQL
title: MySQL Index column size too large. The maximum column size is 767 bytes
meta: MySQL Index column size too large. The maximum column size is 767 bytes
---
* content
{:toc}

### 正文

今天新建一个表时报错了：

```
SQL 错误 [1709] [HY000]: Index column size too large. The maximum column size is 767 bytes.
```

错误是指超出索引字节的限制。

问题暂时未解决。

<br/><br/><br/><br/><br/>
### 参考资料：
   
ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes <https://www.cnblogs.com/kerrycode/p/9680881.html>



