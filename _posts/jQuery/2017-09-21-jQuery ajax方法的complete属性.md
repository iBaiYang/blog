---
layout: post
categories: jQuery
title: jQuery ajax方法的complete属性
meta: ajax方法的complete属性
---
* content
{:toc}

## 正文

有时我们会碰到这样一种情况，ajax请求时，调试模式打断点没有问题，但实际运行就总是出错，这时你就要注意了，
这个问题很可能是ajax未完成再次调用ajax导致的，说白了就是一个ajax请求中又嵌套另一个ajax方法。这时的解决办法就是用好success、和complete。
    
举例：
    
```javascript
$.ajax({
    url: 'www.ceshi.com/test/test01',
    dataType: 'json',
    data: {"id": pid},
    success: function (res) {
        var html = "\<\option value='100'>全部版本<\/option>";
        $.each(res, function (index, item) {
            html = html + "<\option value='" + item.id + "'>" + item.version_name + "<\/option>";
        });
        $(".content-filter #bug_product_version").empty().append(html);
        $('#bug_product_version').val(plan_id);
    },
    complete: function(){
        // 移除按钮的不可选状态，刷新列表
        $('#viewModal .modal-body #confirm_add_bug').text("确 定").removeAttr("disabled");
        ZENG.msgbox.show("正在加载数据...", 6);
        $("#bug_trigger").trigger("change");
        ZENG.msgbox.hide();
    }
}); 
```

用这么负责的处理的例子，就是为了接近实际，让人一眼能看懂在开发中怎么用。

什么是 AJAX？
AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。
简短地说，在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。

jQuery 提供多个与 AJAX 有关的方法：`$.ajax()`、 `$.get()`、 `$.post()`、 `load()`等。

<br/><br/><br/><br/><br/>
## 参考资料 

jQuery - AJAX 简介 <https://www.runoob.com/jquery/jquery-ajax-intro.html>

jQuery AJAX 方法 <https://www.runoob.com/jquery/jquery-ref-ajax.html>

jQuery ajax() 方法 <http://www.runoob.com/jquery/ajax-ajax.html>

<http://www.w3school.com.cn/jquery/jquery_ref_ajax.asp>
    
<http://www.jb51.net/article/43194.htm>
    
<http://www.runoob.com/ajax/ajax-tutorial.html>

