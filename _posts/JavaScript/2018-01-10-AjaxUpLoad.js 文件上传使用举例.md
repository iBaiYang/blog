---
layout: post
categories: JavaScript
title: AjaxUpLoad.js 文件上传使用举例
meta: 一般文件上传，我们可以用AjaxUpLoad.js。
---

* content
{:toc}

### 正文

一般文件上传，我们可以用AjaxUpLoad.js：

![]({{site.baseurl}}/images/20210415/20210415105120.jpeg)

在html页面中引入后使用：

![]({{site.baseurl}}/images/20210415/20210415105122.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105124.jpeg)

页面效果：

![]({{site.baseurl}}/images/20210415/20210415105126.jpeg)

看一下后端的处理：

![]({{site.baseurl}}/images/20210415/20210415105128.jpeg)

举一反三，看看就明白了。

move_uploaded_file()的路径文件夹要存在。

删除文件时可以用 @unlink(文件路径)


<br/><br/><br/><br/><br/>
### 参考资料

<http://blog.csdn.net/lishuangzhe7047/article/details/39969637>
