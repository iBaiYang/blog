---
layout: post
categories: JavaScript
title: flatpickr 日期选择器
meta: flatpickr 日期选择器
---
* content
{:toc}

### 正文

flatpickr是一款功能强大的纯JS扁平风格日期时间选择器插件。该日期时间选择器支持移动手机，提供多种内置的主题效果，
并且提供对中文的支持。它的特点还有：
1. 使用SVG作为界面的图标。
2. 兼容jQuery。
3. 支持对各种日期格式的解析。
4. 轻量级，高性能，压缩后的版本仅6K大小。
5. 对手机原生日期格式的支持。

#### 安装

方式1：
> npm install flatpickr

方式2：
> bower install flatpickr-calendar

方式3，[下载包](http://down.htmleaf.com/1608/201608211550.zip)后引入：
```
<link rel="stylesheet" type="text/css" href="/path/to/flatpickr.css">
<script src="/path/to/flatpickr.js"></script>     
```

#### 使用

```javascript
<input id="try" data-enable-time="true" data-time_24hr="true" placeholder="请选择时间">
    
<script>
    $(function () {
        $("#try").flatpickr();
    });
</script>
```

![]({{site.baseurl}}/images/20210106/20210106213959.png)

其他具体属性，可以参考下[文档](http://www.htmleaf.com/jQuery/Calendar-Date-Time-picker/201608213894.html)。

<br/><br/><br/><br/><br/>
### 参考资料 

中文文档 <http://www.htmleaf.com/jQuery/Calendar-Date-Time-picker/201608213894.html>

原文文档 <https://flatpickr.js.org/getting-started/>