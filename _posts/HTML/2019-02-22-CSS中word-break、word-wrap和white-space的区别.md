---
layout: post
categories: HTML
title: CSS中word-break、word-wrap和white-space的区别
meta: CSS中word-break、word-wrap和white-space的区别
---
* content
{:toc}

### 正文

一次实际编程中使用了word-wrap: break-all的错误用法，导致以为个别浏览器不支持强制单词换行，
系统查阅资料后才发现word-wrap只有normal|break-word两个属性值。
所以有必要总结一下CSS中word-break、word-wrap和white-space的区别，以防日后忘记又不能快速定位用法。

```
word-break: normal|break-all|keep-all;
```

word-break 定义单词内部的换行规则，特别注意word-break:break-all会立即发生单词内断行。

```
word-wrap: normal|break-word;
```

word-wrap 定义单词是否换行，特别注意word-wrap:break-word首先会判断该长单词在当前行能否放下，
如果不能，会换行，倘若第二行也放不下，才会发生单词内断行。

```
white-space: normal | pre | nowrap | pre-wrap | pre-line | inherit
```

white-space 规定文本中空白符的换行规则。



<br/><br/><br/><br/><br/>
### 参考资料 

<https://blog.csdn.net/foreverling_ling/article/details/52438874>

<https://baijiahao.baidu.com/s?id=1578623236521030997&wfr=spider&for=pc>

word-break <http://www.runoob.com/cssref/css3-pr-word-break.html>

word-wrap <http://www.runoob.com/cssref/css3-pr-word-wrap.html>

white-space <http://www.runoob.com/cssref/pr-text-white-space.html>

CSS3 文本效果 <http://www.runoob.com/css3/css3-text-effects.html>

