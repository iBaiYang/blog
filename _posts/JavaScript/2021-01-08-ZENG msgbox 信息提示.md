---
layout: post
categories: JavaScript
title: ZENG msgbox 信息提示
meta: ZENG msgbox 信息提示
---
* content
{:toc}

### 正文

ZENG msgbox 是腾讯UED 提示信息组件，可以设定显示时间。用起来顺手，也比较美观。

![]({{site.baseurl}}/images/20210108/20210108114206.png)

先[下载包](https://github.com/scepterscythe/ZENG-msgbox/tree/master) ，可以看一下使用示例，使用时在页面引入：
```javascript
<link type="text/css" href="msgbox.css" rel="stylesheet"/>
<script type="text/javascript" src="msgbox.js"></script>

<script type="text/javascript">
    ZENG.msgbox.show("设置成功！", 4, 3000);
    /*
    ZENG.msgbox.show("您当前没有任何修改", 1, 6000);
    ZENG.msgbox.show("恭喜，添加成功！", 4, 8000);
    ZENG.msgbox.show("服务器出错了", 5, 4000);
    ZENG.msgbox.show(" 正在加载中，请稍后...", 6, 2000);
    */
    /*
    ZENG.msgbox.show(" 正在加载中，请稍后...", 6, 600000);  // 停留10分钟
    ZENG.msgbox.hide();
    */
    /*
    ZENG.msgbox.show("正在加载数据...", 6);  // 不必设定显示时间
    // 执行一些ajax操作
    ZENG.msgbox.hide();  // 手动隐藏
    */
</script>
```

<br/><br/><br/><br/><br/>
### 参考资料 

ZENG-msgbox <https://github.com/scepterscythe/ZENG-msgbox>

腾讯UED 漂亮的提示信息 <https://www.cnblogs.com/zengxiangzhan/archive/2011/09/12/2173939.html>

