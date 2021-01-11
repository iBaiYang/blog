---
layout: post
categories: jQuery
title: jQuery-toast 信息提示插件
meta: jQuery-toast 信息提示插件
---
* content
{:toc}

### 正文

![]({{site.baseurl}}/images/20210111/20210111110816.png)

jQuery-toast 信息提示插件,可以看到动态效果，样式比较养眼。

使用时先 [下载包](https://github.com/scepterscythe/jquery-toast) ，在页面引入：
```javascript
<link type="text/css" rel="stylesheet" href="./toast/jquery.toast.min.css"/>

<script type="text/javascript" src="http://cdn.staticfile.org/jquery/3.0.0/jquery.min.js"></script>
<script type="text/javascript" src="./toast/jquery.toast.min.js"></script>
```

具体使用，有成功、失败、详情、警告、一般等提示样式，可以指定页面显示位置：
```javascript
<script type="text/javascript">
    $(function () {
        var type = "success";
        if (type == "error") {
            $.toast({
                position: 'bottom-left', icon: 'error', stack: false,
                heading: 'Error', text: '请填写备注',
            });
        } else if (type == "success") {
            $.toast({
                position: 'mid-center', icon: 'success', stack: false,
                heading: 'Success', text: '添加成功，请继续添加下一条！',
            });
        } else if (type == "info") {
            $.toast({
                position: 'top-center', icon: 'info', stack: false,
                heading: 'Info', text: '你的活动很多啊！',
            });
        } else if (type == "warning") {
            $.toast({
                position: 'bottom-center', icon: 'warning', stack: false,
                heading: 'Warning', text: '你的活动很多啊！',
            });
        } else {
            $.toast({
                position: 'bottom-right', stack: false,
                text: '欢迎光临！',
            });
        }
    });
</script>
```

#### 源码

jquery.toast.css文件源码：
```css
.toast{ position:fixed; list-style: none; padding: 0; top:0; z-index: 999999; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size:14px; line-height:20px; }
.toast li{ margin: 10px 0 0 0; display:block; background-color:#fcf8e3; color:#c09853; border:1px solid #fbeed5; padding:5px 10px; border-radius: 4px; -webkit-border-radius: 4px; text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5); box-shadow: 0 2px 5px rgba(0,0,0,.15); -webkit-box-shadow: 0 2px 5px rgba(0,0,0,.15); }
.toast li:first-child{ margin-top:0; }
.toast li.danger{ color: #b94a48; background-color: #f2dede; border-color: #eed3d7; }
.toast li.info{ color: #3a87ad; background-color: #d9edf7; border-color: #bce8f1; }
.toast li.success{ color: #468847; background-color: #dff0d8; border-color: #d6e9c6; }
.toast button.close{ background: none; border: none; font-weight: bold; font-size: 20px; line-height: 20px; float: right; padding: 0; margin: 0 0 0 5px; color: rgba(0,0,0,.25); cursor: pointer; }
.toast h1, .toast h2, .toast h3, .toast h4 { display: inline; }
```

jquery.toast.js文件源码：
```javascript
(function($){
    var th = null, cf = null, toast = function(m,o){
        // fix option type
        o = $.extend({ duration: 5000, sticky: false, 'type': ''}, o);
        typeof o.duration === 'number' || (o.duration = 5000);
        typeof o.sticky === 'boolean' || (o.sticky = false);
        typeof o.type === 'string' || (o.type = '');
        // create host on first call
        if(!th){ 
            // get/fix config
            cf = toast.config;
            th = $('<ul></ul>').addClass('toast').appendTo(document.body).hide();
            typeof cf.width === 'number' || (cf.width = 500);
            typeof cf.align === 'string' || (cf.align = 'center');
            typeof cf.closeForStickyOnly === 'boolean' || (cf.closeForStickyOnly = false);
            th.width(cf.width);
            (cf.align === 'left' || cf.align === 'right') && th.css('margin','5px').css(cf.align, '0') || th.css({left: '50%', margin: '5px 0 0 -' + (cf.width / 2) + 'px'});
        }
        // create toast
        var ti = $('<li></li>').hide().html(m).appendTo(th), cb = $('<button>&times;</button>').addClass('close').prependTo(ti), to = null;
        // setup close button
        cb.click(function(){
            clearTimeout(to);
            ti.animate({ height: 0, opacity: 0}, 'fast', function(){
                ti.remove();
                th.children().length || th.removeClass('active').hide();
            });
        });
        cf.closeForStickyOnly && !o.sticky && cb.hide();
        // add type class
        o.type !== '' && ti.addClass(o.type);
        // show host if necessary
        !th.hasClass('active') && th.addClass('active').show();
        // setup timeout unless sticky
        !o.sticky && o.duration > 0 && (to = setTimeout(function(){ cb.click(); }, o.duration));
        // show toast
        ti.fadeIn('normal');
    };
    toast.config = { width: 500, align: 'center', closeForStickyOnly: true };
    $.extend({ toast: toast });
})(jQuery);
```

#### 官方文档

**jquery.toast**

A simple jQuery "toast" message plugin. You can view the example page [here](http://htmlpreview.github.com/?https://github.com/Soldier-B/jquery.toast/blob/master/example/jquery.toast.html).


##### Required Files
    <link rel="stylesheet" type="text/css" href="jquery.toast.min.css" />
    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript" src="jquery.toast.min.js"></script>

##### Usage

    $.toast('content', options);

Example:

    $.toast('<h4>Hello, world!</h4> Your content here!');

or

    $.toast('<h4>Error!</h4> Something went wrong.', {sticky: true, type: 'danger'});

##### Options

Options include toast duration, stickiness, and toast type.

* **duration** - Length of time in milliseconds a non-sticky toast will be displayed. (default: 5000)
* **sticky** - Determines if toast notification is sticky or not. (default: false)
* **type** - Determines visual style for toast notification. Values can be any valid css class name (allowing you to define your own toast styles) or any of the following: blank, 'danger', 'info', 'success'. (default is blank)


##### Configuration

Before using the plugin you have the option to define a few global properties. After the first toast has been made, they will be locked in.
These properties include toast width, page alignment, and close button status.

Example:

    $.toast.config.width = 800;
    $.toast.config.align = 'right';
    $.toast.config.closeForStickyOnly = false;

* **width** - Width of toast items in pixels. (default: 500)
* **align** - Alignment of toasts in the page. Possibly values are 'left', 'center', and 'right'. (default: 'center')
* **closeForStickyOnly** - Determines if the close button is displayed for all toasts or just those that are sticky. (default: true)

#### 实例

```
<html>
    <head>
        <style type="text/css">
            body{ margin: 0; padding: 0; font-family: sans-serif; } body>div { width:960px; margin: 0 auto; } a { padding: 4px 8px; }
        </style>
        <link rel="stylesheet" type="text/css" href="../jquery.toast/jquery.toast.css" />
        <script type="text/javascript" src="http://cdn.staticfile.org/jquery/3.0.0/jquery.min.js"></script>
        <script type="text/javascript" src="../jquery.toast/jquery.toast.js"></script>
        <script type="text/javascript">
            function createToast(t){
                var message = 'Hi, I\m just your every day, average kind of toast.';
                var options = {
                    duration: Math.floor(Math.random() * 4001) + 1000,
                    sticky: !!Math.round(Math.random() * 1),
                    type: t
                };
                
                switch(t){
                    case 'danger': message = '<h4>Danger!</h4> Oh no. You\'ve activated a dangerous toast. Beware as it was may (or may not) be sticky.'; break;
                    case 'info': message = '<h4>FYI</h4> I\'m a toast and I just wanted you to know that.'; break;
                    case 'success': message = '<h4>Great!</h4> You\'ve made a toast. Now let\'s\ toast to you.'; break;
                }
                
                $.toast(message, options);
            }
            
            $(document).ready(function(){
                $.toast.config.align = 'right';
                $.toast.config.width = 400;
                
                $('a').click(function(){
                    createToast($(this).attr('class'));
                    return false;
                });
            });
        </script>
    </head>
    <body>
        <div>
            <p>
                <a href="#" class="">Default Toast</a>
                <a href="#" class="danger">Danger Toast</a>
                <a href="#" class="info">Info Toast</a>
                <a href="#" class="success">Success Toast</a>
            </p>
        </div>
    </body>
</html>
```

![]({{site.baseurl}}/images/20210111/20210111115213.png)

<br/><br/><br/><br/><br/>
### 参考资料 

jquery-toast 源码包位置 <https://github.com/scepterscythe/jquery-toast>

<https://github.com/kamranahmedse/jquery-toast-plugin>

