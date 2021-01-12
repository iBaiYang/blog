---
layout: post
categories: Bootstrap
title: Bootstrap 提醒框实现
meta: tooltip、popover都可以实现提醒框，tooltip鼠标划过及时弹出，popover鼠标点击弹出。
---
* content
{:toc}

### 正文

tooltip、popover都可以实现提醒框，tooltip鼠标划过及时弹出，popover鼠标点击弹出。
这两个点击后提醒框都会持久留在页面，除非再次点击才会消失。

#### Tooltip

![]({{site.baseurl}}/images/20210112/20210112144100.png)

提示工具（Tooltip）插件，使用 CSS 实现动画效果，用 data 属性存储标题信息等。
引用 bootstrap.js 或压缩版的 bootstrap.min.js，就可以使用Tooltip插件。如果您想要单独引用该插件的功能，那么您需要引用 tooltip.js。

提示工具（Tooltip）插件根据需求生成内容和标记，默认情况下是把提示工具（tooltip）放在它们的触发元素后面。
您可以有以下两种方式添加提示工具（tooltip）：

方式1，通过 data 属性：如需添加一个提示工具（tooltip），只需向一个锚标签添加 `data-toggle="tooltip"` 即可。
锚的 title 即为提示工具（tooltip）的文本。默认情况下，插件把提示工具（tooltip）设置在顶部。 
```
<a href="#" data-toggle="tooltip" title="Example tooltip">请悬停在我的上面</a>
```

方式2，通过 JavaScript：通过 JavaScript 触发指定元素的提示工具（tooltip）： 
```
$('#identifier').tooltip(options)
```

**注意**：提示工具（Tooltip）插件不像之前所讨论的下拉菜单及其他插件那样，它不是纯 CSS 插件。如需使用该插件，
您必须使用 jquery 激活它（读取 javascript）。使用下面的脚本来启用页面中的所有的提示工具（tooltip）： 
```
$(function () { $("[data-toggle='tooltip']").tooltip(); });
```

综合实践：

<https://www.runoob.com/try/try2.php?filename=bootstrap3-plugin-tooltip>

更多完整功能看一下 [Bootstrap 提示工具（Tooltip）插件](https://www.runoob.com/bootstrap/bootstrap-tooltip-plugin.html) 。

#### Popover

![]({{site.baseurl}}/images/20210112/20210112144123.png)

弹出框（Popover）与工具提示（Tooltip）类似，提供了一个扩展的视图。它依赖于 工具提示（Tooltip）插件。
引用 bootstrap.js 或压缩版的 bootstrap.min.js，就可以使用Popover插件。如果您想要单独引用该插件的功能，那么您需要引用 popover.js。

弹出框（Popover）插件根据需求生成内容和标记，默认情况下是把弹出框（popover）放在它们的触发元素后面。
您可以有以下两种方式添加弹出框（popover）：

方式1，通过 data 属性：如需添加一个弹出框（popover），只需向一个锚/按钮标签添加 `data-toggle="popover"` 即可。
锚的 title 即为弹出框（popover）的文本。默认情况下，插件把弹出框（popover）设置在顶部。 
```
<button type="button" class="btn btn-primary" title="Popover title"  
        data-container="body" data-toggle="popover" data-placement="top" 
        data-content="顶部的 Popover 中的一些内容">
    顶部的 Popover
</button>
```

方式2，通过 JavaScript：通过 JavaScript 启用指定元素的弹出框（popover）： 
```
$('#identifier').popover(options)
```

**注意**：弹出框（Popover）插件不像之前所讨论的下拉菜单及其他插件那样，它不是纯 CSS 插件。如需使用该插件，
您必须使用 jquery 激活它（读取 javascript）。使用下面的脚本来启用页面中的所有的弹出框（popover）： 
```
$(function () { $("[data-toggle='popover']").popover(); });
```

综合实践：

<https://www.runoob.com/try/try.php?filename=bootstrap3-plugin-popover>

弹出内容中换行等实现可以使用Popover的html属性。

更多完整功能看一下 [Bootstrap 弹出框（Popover）插件](https://www.runoob.com/bootstrap/bootstrap-popover-plugin.html) 。

### 参考资料 

Bootstrap 提示工具（Tooltip）插件 <https://www.runoob.com/bootstrap/bootstrap-tooltip-plugin.html>

Bootstrap 弹出框（Popover）插件 <https://www.runoob.com/bootstrap/bootstrap-popover-plugin.html>

