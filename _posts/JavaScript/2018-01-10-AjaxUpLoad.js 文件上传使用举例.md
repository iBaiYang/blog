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

![](http://s8.sinaimg.cn/mw690/001XbchKzy7hgXXg8o747&690)

在html页面中引入后使用：

![](http://s15.sinaimg.cn/mw690/001XbchKzy7hgY5Cj5Ice&690)

![](http://s10.sinaimg.cn/mw690/001XbchKzy7hgYd1xupa9&690)

页面效果：

![](http://s16.sinaimg.cn/mw690/001XbchKzy7hgYi1YVFff&690)

看一下后端的处理：

![](http://s11.sinaimg.cn/mw690/001XbchKzy7hgYtIHbkda&690)

举一反三，看看就明白了。

move_uploaded_file()的路径文件夹要存在。

删除文件时可以用 @unlink(文件路径)


<br/><br/><br/><br/><br/>
### 参考资料

<http://blog.csdn.net/lishuangzhe7047/article/details/39969637>
