---
layout: post
categories: HTML
title: Font Awesome 图标
meta: Font Awesome 图标
---
* content
{:toc}

### 正文

在HTML开发中，用到一些图标时，我们可以快速使用Font Awesome 图标，简单、易用、美观。

Font Awesome 是一套绝佳的图标字体库和CSS框架。

Font Awesome 字体为您提供可缩放矢量图标,它可以被定制大小、颜色、阴影以及任何可以用CSS的样式。

#### 安装

要使用Font Awesome图标，请在HTML页面的 部分中添加以下行：

* 国内推荐 CDN：

```html
<link href="https://cdn.staticfile.org/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet">
```

* 海外推荐 CDN

```html
<link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet" >
```

* 直接下载到本地

<https://static.runoob.com/download/font-awesome-4.7.0.zip>

#### 使用

可以使用前缀 fa 和图标的名称来放置 Font Awesome 图标。
```
<i class="fa fa-car"></i>
<i class="fa fa-car" style="font-size:48px;"></i>
<i class="fa fa-car" style="font-size:60px;color:red;"></i>
```

![]({{site.baseurl}}/images/20210105/20210105224235.png)

#### 大图标

fa-lg (增加33％)，fa-2x，fa-3x， fa-4x，或 fa-5x 类用于增加相对于其容器的图标大小。

```html
<i class="fa fa-car fa-lg"></i>
<i class="fa fa-car fa-2x"></i>
<i class="fa fa-car fa-3x"></i>
<i class="fa fa-car fa-4x"></i>
<i class="fa fa-car fa-5x"></i>
```

#### 列表图标

fa-ul 和 fa-li 类用于替换无序列表中的默认前缀。

```html
<ul class="fa-ul">
  <li><i class="fa-li fa fa-check-square"></i>List icons</li>
  <li><i class="fa-li fa fa-spinner fa-spin"></i>List icons</li>
  <li><i class="fa-li fa fa-square"></i>List icons</li>
</ul>
```

#### 边界、被拉的图标 

fa-border，fa-pull-right 或 fa-pull-left 类用于拉式引用或文章图标。

```html
<i class="fa fa-quote-left fa-3x fa-pull-left fa-border"></i>
菜鸟教程 -- 学的不仅是技术，更是梦想！！！<br>
菜鸟教程 -- 学的不仅是技术，更是梦想！！！<br>
菜鸟教程 -- 学的不仅是技术，更是梦想！！！<br>
菜鸟教程 -- 学的不仅是技术，更是梦想！！！
```

#### 动态图标

fa-spin 类可以让图标旋转, fa-pulse 类可以使图标以 8 步为周期进行旋转。

```html
<i class="fa fa-spinner fa-spin"></i>
<i class="fa fa-circle-o-notch fa-spin"></i>
<i class="fa fa-refresh fa-spin"></i>
<i class="fa fa-cog fa-spin"></i>
<i class="fa fa-spinner fa-pulse"></i>
```

#### 旋转和翻转的图标

fa-rotate-* 和 fa-flip-* 类用于旋转和翻转图标。

```html
<i class="fa fa-shield"></i>
<i class="fa fa-shield fa-rotate-90"></i>
<i class="fa fa-shield fa-rotate-180"></i>
<i class="fa fa-shield fa-rotate-270"></i>
<i class="fa fa-shield fa-flip-horizontal"></i>
<i class="fa fa-shield fa-flip-vertical"></i>
```

#### 堆叠的图标

要堆叠多个图标，请使用父级上的 fa-stack 类，fa-stack-1x 类用于常规大小的图标，fa-stack-2x 用于较大的图标。

fa-inverse 类可以用作替代图标颜色。您还可以向父级添加更大的图标类，以进一步控制尺寸。

```html
<span class="fa-stack fa-lg">
  <i class="fa fa-circle-thin fa-stack-2x"></i>
  <i class="fa fa-twitter fa-stack-1x"></i>
</span>
fa-twitter on fa-circle-thin<br>
 
<span class="fa-stack fa-lg">
  <i class="fa fa-circle fa-stack-2x"></i>
  <i class="fa fa-twitter fa-stack-1x fa-inverse"></i>
</span>
fa-twitter (inverse) on fa-circle<br>
 
<span class="fa-stack fa-lg">
  <i class="fa fa-camera fa-stack-1x"></i>
  <i class="fa fa-ban fa-stack-2x text-danger" style="color:red;"></i>
</span>
fa-ban on fa-camera
```

#### 固定宽度图标

fa-fw 类用于设置固定宽度的图标。 当不同的图标宽度偏离对齐时，此类非常有用。 特别适用于Bootstrap的导航列表和列表组。

```html
<div class="list-group">
  <a href="#" class="list-group-item"><i class="fa fa-home fa-fw"></i> Home</a>
  <a href="#" class="list-group-item"><i class="fa fa-book fa-fw"></i> Library</a>
  <a href="#" class="list-group-item"><i class="fa fa-pencil fa-fw"></i> Applications</a>
  <a href="#" class="list-group-item"><i class="fa fa-cog fa-fw"></i> Settings</a>
</div>
```


<br/><br/><br/><br/><br/>
### 参考资料 

Font Awesome 图标 <https://www.runoob.com/font-awesome/fontawesome-tutorial.html>

Font Awesome 参考手册 <https://www.runoob.com/font-awesome/fontawesome-reference.html>
