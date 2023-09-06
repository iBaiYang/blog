---
layout: post
categories: jQuery
title: jQuery学习二 DOM操作
meta: DOM （文档对象模型(Document Object Model)）。
---
* content
{:toc}

### 正文

#### 第三章：jQuery中的DOM操作

DOM （文档对象模型(Document Object Model)）。

一般DOM操作分为3个方面：DOM Core、HTML-DOM、CSS-DOM。

HTML文档：

![]({{site.baseurl}}/images/20210415/20210415105501.jpeg)

查找节点：

![]({{site.baseurl}}/images/20210415/20210415105503.jpeg)

创建节点：

jQuery的工厂函数 $()，创建DOM对象，并封装成jQuery对象后返回。

![]({{site.baseurl}}/images/20210415/20210415105505.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-2-3</title>
<!--   引入jQuery -->
<script src="../../scripts/jquery-1.3.1.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    var $li_1 = $("<li title='香蕉'>香蕉</li>");    // 创建一个<li>元素
                                                    // 包括元素节点,文本节点和属性节点
                                                    // 其中title='香蕉' 就是创建的属性节点
    var $li_2 = $("<li title='雪梨'>雪梨</li>");     // 创建一个<li>元素
                                                    // 包括元素节点,文本节点和属性节点
                                                    // 其中title='雪梨' 就是创建的属性节点  
    var $parent = $("ul");        // 获取<ul>节点。<li>的父节点
    
    $parent.append($li_1);        // 添加到<ul>节点中，使之能在网页中显示
    $parent.append($li_2);        // 等价于：$parent.append($li_1).append($li_2);
});
//]]>
</script>
</head>
<body>
<p title="选择你最喜欢的水果." >你最喜欢的水果是?</p>
<ul>
    <li title='苹果'>苹果</li>
    <li title='橘子'>橘子</li>
    <li title='菠萝'>菠萝</li>
</ul>
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105507.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105509.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105511.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105513.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105515.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105517.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-4-2</title>
<!--   引入jQuery -->
<script src="http://cdn.staticfile.org/jquery/1.3.1/jquery.min.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    var $li = $("ul li:eq(1)").remove();  // 获取第二个<li>元素节点后，将它从网页中删除。
    $li.appendTo("ul");                      // 把刚才删除的又重新添加到<ul>元素里
    // 所以，删除只是从网页中删除，在jQuery对象中，这个元素还是存在的，我们可以重新获取它
});
//]]>
</script>
</head>
<body>
    <p title="选择你最喜欢的水果." >你最喜欢的水果是?</p>
    <ul>
        <li title='苹果'>苹果</li>
        <li title='橘子'>橘子</li>
        <li title='菠萝'>菠萝</li>
    </ul>
</body>
</html>
```

> $("ul li").remove("li[title!=菠萝]");  // 把li元素中属性title不等于"菠萝"的li元素删除 

![]({{site.baseurl}}/images/20210415/20210415105519.jpeg)

> $("ul li:eq(1)").empty(); // 找到第二个li元素节点后，清空此元素里的内容 

```
$("ul li").click(function(){
    $(this).clone().appendTo("ul"); // 复制当前点击的节点，并将它追加到ul元素
}) 
```

```
$("ul li").click(function(){
    $(this).clone(true).appendTo("ul");  // 注意参数true
    // 可以复制自己，并且他的副本也有同样功能。
}) 
```

```
$(function(){
    $("p").replaceWith("你最不喜欢的水果是?");
    // 同样的实现： $("你最不喜欢的水果是?").replaceAll("p");
}); 
```

![]({{site.baseurl}}/images/20210415/20210415105521.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105523.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105525.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105527.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105529.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105531.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-9-1</title>
<style type="text/css">
.high{
    font-weight:bold;   /* 粗体字 */
    color : red;        /* 字体颜色设置红色*/
}
.another{
    font-style:italic;
    color:blue;
}
</style>
<!--   引入jQuery -->
<script src="http://cdn.staticfile.org/jquery/1.3.1/jquery.min.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    // 获取样式
    $("input:eq(0)").click(function(){
        alert( $("p").attr("class") );
    });
    // 设置样式
    $("input:eq(1)").click(function(){
        $("p").attr("class","high");
    });
    // 追加样式
    $("input:eq(2)").click(function(){
        $("p").addClass("another");
    });    
    // 删除全部样式
    $("input:eq(3)").click(function(){
        $("p").removeClass();
    });  
    // 删除指定样式
    $("input:eq(4)").click(function(){
        $("p").removeClass("high");
    });   
    // 重复切换样式
    $("input:eq(5)").click(function(){
        $("p").toggleClass("another");
    });  
    // 判断元素是否含有某样式
    $("input:eq(6)").click(function(){
        alert( $("p").hasClass("another") )
        alert( $("p").is(".another") )
    });  
});
//]]>
</script>
</head>
<body>
    <input type="button" value="输出class类"/>
    <input type="button" value="设置class类"/>
    <input type="button" value="追加class类"/>
    <input type="button" value="删除全部class类"/>
    <input type="button" value="删除指定class类"/>
    <input type="button" value="重复切换class类"/>
    <input type="button" value="判断元素是否含有某个class类"/>
    
    <p class="myClass" title="选择你最喜欢的水果." >你最喜欢的水果是?</p>
    <ul>
        <li title='苹果'>苹果</li>
        <li title='橘子'>橘子</li>
        <li title='菠萝'>菠萝</li>
    </ul>
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105533.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-10-1</title>
<style type="text/css">
.test{
    font-weight:bold;
    color : red;
}
.add{
    font-style:italic;
}
</style>
<!--   引入jQuery -->
<script src="http://cdn.staticfile.org/jquery/1.3.1/jquery.min.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    //获取<p>元素的HTML代码
    $("input:eq(0)").click(function(){
        alert(  $("p").html() );
    });
    //获取<p>元素的文本
    $("input:eq(1)").click(function(){
        alert(  $("p").text() );
    });
    //设置<p>元素的HTML代码
    $("input:eq(2)").click(function(){
        $("p").html("<strong>你最喜欢的水果是?</strong>");
    });    
    //设置<p>元素的文本
    $("input:eq(3)").click(function(){
        $("p").text("你最喜欢的水果是?");
    });  
    //设置<p>元素的文本
    $("input:eq(4)").click(function(){
        $("p").text("<strong>你最喜欢的水果是?</strong>");
    });  
    //获取按钮的value值
    $("input:eq(5)").click(function(){
        alert( $(this).val() );
    });   
    //设置按钮的value值
    $("input:eq(6)").click(function(){
        $(this).val("我被点击了!");
    });  
});
//]]>
</script>
</head>
<body>
    <input type="button" value="获取<p>元素的HTML代码"/>
    <input type="button" value="获取<p>元素的文本"/>
    <input type="button" value="设置<p>元素的HTML代码"/>
    <input type="button" value="设置<p>元素的文本"/>
    <input type="button" value="设置<p>元素的文本(带HTML)"/>
    <input type="button" value="获取按钮的value值"/>
    <input type="button" value="设置按钮的value值"/>
    
    <p title="选择你最喜欢的水果." ><strong>你最喜欢的水果是?</strong></p>
    <ul>
        <li title='苹果'>苹果</li>
        <li title='橘子'>橘子</li>
        <li title='菠萝'>菠萝</li>
    </ul>
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105535.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105537.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105539.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105541.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105543.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-10-4</title>
<style type="text/css">
.test{
    font-weight:bold;
    color : red;
}
.add{
    font-style:italic;
}
</style>
<!--   引入jQuery -->
<script src="../../scripts/jquery-1.3.1.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    //设置单选下拉框选中
    $("input:eq(0)").click(function(){
        $("#single option").removeAttr("selected");  //移除属性selected
        $("#single option:eq(1)").attr("selected",true); //设置属性selected
    });
    //设置多选下拉框选中
    $("input:eq(1)").click(function(){
        $("#multiple option").removeAttr("selected");  //移除属性selected
        $("#multiple option:eq(2)").attr("selected",true);//设置属性selected
        $("#multiple option:eq(3)").attr("selected",true);//设置属性selected
    });
    //设置单选框和多选框选中
    $("input:eq(2)").click(function(){
        $(":checkbox").removeAttr("checked"); //移除属性checked
        $(":radio").removeAttr("checked"); //移除属性checked
        $("[value=check2]:checkbox").attr("checked",true);//设置属性checked
        $("[value=check3]:checkbox").attr("checked",true);//设置属性checked
        $("[value=radio2]:radio").attr("checked",true);//设置属性checked
    });   
});
//]]>
</script>
</head>
<body>
<input type="button" value="设置单选下拉框选中"/>
<input type="button" value="设置多选下拉框选中"/>
<input type="button" value="设置单选框和多选框选中"/>

<br/><br/>

<select id="single">
    <option>选择1号</option>
    <option>选择2号</option>
    <option>选择3号</option>
</select>

<select id="multiple" multiple="multiple" style="height:120px;">
    <option selected="selected">选择1号</option>
    <option>选择2号</option>
    <option>选择3号</option>
    <option>选择4号</option>
    <option selected="selected">选择5号</option>
</select>

<br/><br/>

<input type="checkbox" value="check1"/> 多选1
<input type="checkbox" value="check2"/> 多选2
<input type="checkbox" value="check3"/> 多选3
<input type="checkbox" value="check4"/> 多选4

<br/>

<input type="radio" value="radio1" name="a"/> 单选1
<input type="radio" value="radio2" name="a"/> 单选2
<input type="radio" value="radio3" name="a"/> 单选3
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105545.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105547.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105549.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105551.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105553.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>3-12-1</title>
<style type="text/css">
.test{
    font-weight:bold;
    color : red;
}
.add{
    font-style:italic;
}
</style>
<!--   引入jQuery -->
<script src="../../scripts/jquery-1.3.1.js" type="text/javascript"></script>
<script type="text/javascript">
//<![CDATA[
$(function(){
    //获取<p>元素的color
    $("input:eq(0)").click(function(){
        alert(  $("p").css("color") );
    });
    //设置<p>元素的color
    $("input:eq(1)").click(function(){
        $("p").css("color","red")
    });
    //设置<p>元素的fontSize和backgroundColor
    $("input:eq(2)").click(function(){
        $("p").css({"fontSize":"30px" ,"backgroundColor":"#888888"})
    });    
    //获取<p>元素的高度
    $("input:eq(3)").click(function(){
        alert( $("p").height() );
    });  
    //获取<p>元素的宽度
    $("input:eq(4)").click(function(){
        alert( $("p").width() );
    });   
    
    //设置<p>元素的高度
    $("input:eq(5)").click(function(){
        $("p").height("100px");
    });  
    //设置<p>元素的宽度
    $("input:eq(6)").click(function(){
        $("p").width("400px");
    }); 
    //获取<p>元素的的左边距和上边距
    $("input:eq(7)").click(function(){
        var offset = $("p").offset();
        var left = offset.left;
        var top =  offset.top;
        alert("left:"+left+";top:"+top);
    });  
});
//]]>
</script>
</head>
<body>
<input type="button" value="获取<p>元素的color"/>
<input type="button" value="设置<p>元素的color"/>
<input type="button" value="设置<p>元素的fontSize和backgroundColor"/>
<input type="button" value="获取<p>元素的高度"/>
<input type="button" value="获取<p>元素的宽度"/>
<input type="button" value="设置<p>元素的高度"/>
<input type="button" value="设置<p>元素的宽度"/>
<input type="button" value="获取<p>元素的的左边距和上边距"/>

<p title="选择你最喜欢的水果."><strong>你最喜欢的水果是?</strong></p>
<ul>
    <li title='苹果'>苹果</li>
    <li title='橘子'>橘子</li>
    <li title='菠萝'>菠萝</li>
</ul>
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105555.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105557.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105559.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105561.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105563.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>文字提示</title>
<!--   引入jQuery -->
<script src="../../scripts/jquery-1.3.1.js" type="text/javascript"></script>
<style type="text/css">
body{
    margin:0;
    padding:40px;
    background:#fff;
    font:80% Arial, Helvetica, sans-serif;
    color:#555;
    line-height:180%;
}
p{
    clear:both;
    margin:0;
    padding:.5em 0;
}
/* tooltip */
#tooltip{
    position:absolute;
    border:1px solid #333;
    background:#f7f5d1;
    padding:1px;
    color:#333;
    display:none;
}
</style>
<script type="text/javascript">
//<![CDATA[
$(function(){
    var x = 10;  
    var y = 20;
    $("a.tooltip").mouseover(function(e){
        this.myTitle = this.title;
        this.title = "";    
        var tooltip = "<div id='tooltip'>"+ this.myTitle +"<\/div>"; //创建 div 元素
        $("body").append(tooltip);    //把它追加到文档中
        $("#tooltip")
            .css({
                "top": (e.pageY+y) + "px",
                "left": (e.pageX+x)  + "px"
            }).show("fast");      //设置x坐标和y坐标，并且显示
    }).mouseout(function(){        
        this.title = this.myTitle;
        $("#tooltip").remove();   //移除 
    }).mousemove(function(e){
        $("#tooltip")
            .css({
                "top": (e.pageY+y) + "px",
                "left": (e.pageX+x)  + "px"
            });
    });
})
//]]>
</script>
</head>
<body>
    <p><a href="#" class="tooltip" title="这是我的超链接提示1.">提示1.</a></p>
    <p><a href="#" class="tooltip" title="这是我的超链接提示2.">提示2.</a></p>
    <p><a href="#" title="这是自带提示1.">自带提示1.</a></p>
    <p><a href="#" title="这是自带提示2.">自带提示2.</a> </p>
</body>
</html>
```

![]({{site.baseurl}}/images/20210415/20210415105565.jpeg)

![]({{site.baseurl}}/images/20210415/20210415105567.jpeg)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>图片提示</title>
<!--   引入jQuery -->
<script src="../../scripts/jquery-1.3.1.js" type="text/javascript"></script>
<style type="text/css">
body{
    margin:0;
    padding:40px;
    background:#fff;
    font:80% Arial, Helvetica, sans-serif;
    color:#555;
    line-height:180%;
}
img{
    border:none;
}
ul,li{
    margin:0;
    padding:0;
}
li{
    list-style:none;
    float:left;
    display:inline;
    margin-right:10px;
    border:1px solid #AAAAAA;
}

/* tooltip */
#tooltip{
    position:absolute;
    border:1px solid #ccc;
    background:#333;
    padding:2px;
    display:none;
    color:#fff;
}
</style>
<script type="text/javascript">
//<![CDATA[
$(function(){
    var x = 10;
    var y = 20;
    $("a.tooltip").mouseover(function(e){
        this.myTitle = this.title;
        this.title = "";    
        var imgTitle = this.myTitle? "<br/>" + this.myTitle : "";
        var tooltip = "<div id='tooltip'><img src='"+ this.href +"' alt='产品预览图'/>"+imgTitle+"<\/div>"; //创建 div 元素
        $("body").append(tooltip);    //把它追加到文档中                         
        $("#tooltip")
            .css({
                "top": (e.pageY+y) + "px",
                "left":  (e.pageX+x)  + "px"
            }).show("fast");      //设置x坐标和y坐标，并且显示
    }).mouseout(function(){
        this.title = this.myTitle;    
        $("#tooltip").remove();     //移除 
    }).mousemove(function(e){
        $("#tooltip")
            .css({
                "top": (e.pageY+y) + "px",
                "left":  (e.pageX+x)  + "px"
            });
    });
})
//]]>
</script>
</head>
<body>
<h3>有效果：</h3>
<ul>
    <li><a href="images/apple_1_bigger.jpg" class="tooltip" title="苹果 iPod"><img src="images/apple_1.jpg" alt="苹果 iPod" /></a></li>
    <li><a href="images/apple_2_bigger.jpg" class="tooltip" title="苹果 iPod nano"><img src="images/apple_2.jpg" alt="苹果 iPod nano"/></a></li>
    <li><a href="images/apple_3_bigger.jpg" class="tooltip" title="苹果 iPhone"><img src="images/apple_3.jpg" alt="苹果 iPhone"/></a></li>
    <li><a href="images/apple_4_bigger.jpg" class="tooltip" title="苹果 Mac"><img src="images/apple_4.jpg" alt="苹果 Mac"/></a></li>
</ul>

<br/><br/><br/><br/>
<br/><br/><br/><br/>

<h3>无效果：</h3>
<ul>
    <li><a href="images/apple_1_bigger.jpg" title="苹果 iPod"><img src="images/apple_1.jpg" alt="苹果 iPod" /></a></li>
    <li><a href="images/apple_2_bigger.jpg" title="苹果 iPod nano"><img src="images/apple_2.jpg" alt="苹果 iPod nano"/></a></li>
    <li><a href="images/apple_3_bigger.jpg" title="苹果 iPhone"><img src="images/apple_3.jpg" alt="苹果 iPhone"/></a></li>
    <li><a href="images/apple_4_bigger.jpg" title="苹果 Mac"><img src="images/apple_4.jpg" alt="苹果 Mac"/></a></li>
</ul>
</body>
</html>
```

### 参考资料

<http://blog.sina.com.cn/s/blog_6aba78b40102xafy.html>





