---
layout: post
categories: JavaScript
title: SweetAlert 常用示例
meta: SweetAlert 常用示例
---

* content
{:toc}

### 正文

普通alert：
```javascript
alert("哎呦……出错了！");
```

样式取决于各个浏览器自己，可以肯定，样式很简单，不美观，拓展性也不强。

无论你是在电脑、手机还是平板上，SweetAlert都会在页面上自动居中显示，这看起来非常棒。SweetAlert还可高度定制，正如下面你所看到的。

#### 常用示例

##### 基本信息弹窗

```javascript
swal("这是一条信息！");
```

![]({{site.baseurl}}/images/20210105/20210105214100.png)

##### 标题与文本的信息弹窗

```javascript
swal("这是一条信息！", "很漂亮，不是吗？");
```

![]({{site.baseurl}}/images/20210105/20210105214222.png)

##### 成功信息弹窗

```javascript
swal("干得漂亮！", "你点击了按钮！","success")
```

![]({{site.baseurl}}/images/20210105/20210105214235.png)

##### 警告信息弹窗

“确认”按钮带有一个函数：
```javascript
swal({
        title: "确定删除吗？",
        text: "你将无法恢复该虚拟文件！",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#DD6B55",
        confirmButtonText: "确定删除！",
        closeOnConfirm: false
    },
    function () {
        swal("删除！", "你的虚拟文件已经被删除。", "success");
    }
);
```

![]({{site.baseurl}}/images/20210105/20210105214514.png)

"取消"按钮也可以执行（函数传入参数）：
```javascript
swal({
        title: "确定删除吗？",
        text: "你将无法恢复该虚拟文件！",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#DD6B55",
        confirmButtonText: "确定删除！",
        cancelButtonText: "取消删除！",
        closeOnConfirm: false,
        closeOnCancel: false
    },
    function (isConfirm) {
        if (isConfirm) {
            swal("删除！", "你的虚拟文件已经被删除。", "success");
        } else {
            swal("取消！", "你的虚拟文件是安全的:)", "error");
        }
    }
);
```

![]({{site.baseurl}}/images/20210105/20210105214537.png)

##### 自定义图标信息弹窗

```javascript
swal({
    title: "漂亮！",
    text: "这是自定义图标。",
    imageUrl: "images/thumbs-up.jpg"
});
```

![]({{site.baseurl}}/images/20210105/20210105214729.png)

##### HTML信息弹窗

```javascript
swal({
    title: "HTML <small>标题</small>!",
    text: "自定义<span style="color: #F8BB86">html<span>信息。",
    html: true
});
```

![]({{site.baseurl}}/images/20210105/20210105214747.png)

##### 定时关闭信息弹窗

```javascript
swal({
    title: "自动关闭弹窗！",
    text: "2秒后自动关闭。",
    timer: 2000,
    showConfirmButton: false
});
```

![]({{site.baseurl}}/images/20210105/20210105214823.png)

##### 输入信息弹窗

```javascript
swal({
        title: "输入！",
        text: "输入一些有趣的话：",
        type: "input",
        showCancelButton: true,
        closeOnConfirm: false,
        animation: "slide-from-top",
        inputPlaceholder: "输入一些话"
    },
    function (inputValue) {
        if (inputValue === false) return false;

        if (inputValue === "") {
            swal.showInputError("你需要输入一些话！");
            return false
        }

        swal("非常好！", "你输入了：" + inputValue, "success");
    }
);
```

![]({{site.baseurl}}/images/20210105/20210105214910.png)

##### 多输入框

但是实际开发中会遇到多个输入框的情况：

![]({{site.baseurl}}/images/20210104/20210104231123.png)

怎么解决呢？可以用到下面的代码：
```javascript
$(document).on('click', 'a[rel=play-time-add]', function () {
    var user_id = this.data('id');
    
    var text = '<fieldset><input tabindex="3" id="play_time_add_num" placeholder="请输入要增加的娱乐数" type="text" style="display:block">\n'
        +'<input tabindex="4" id="play_time_add_remark" placeholder="请输入备注" type="text" style="display:block"></fieldset>\n'
        +'<div class="class_del_error hide" style="background-color: #f1f1f1;margin-left: -17px;margin-right: -17px;overflow: hidden;padding: 10px 0;">\n'
        +'<div style="display: inline-block;width: 24px;height: 24px;border-radius: 50%;background-color: #ea7d7d;color: white;line-height: 24px;text-align: center;margin-right: 3px;">!</div>\n'
        +'<p style="display: inline-block;color: #797979;font-size: 14px;font-weight: 300;position: relative;text-align: inherit;float: none;margin: 0;padding: 0;line-height: normal;"></p> </div>';

    swal({
        title: "用户增加娱乐数",
        text: text,
        html: true,
        confirmButtonText:"确认",
        cancelButtonText:"取消",
        showCancelButton:true,
        closeOnConfirm:false,
    }, function () {
        var num = $('.sweet-alert  #play_time_add_num').val();
        var play_time_add_remark = $('.sweet-alert  #play_time_add_remark').val();

        if ( num === false || num === "" ) {
            class_del_error_show( "请填写课时数!" );
            return false;
        }
        var pattern = /^\+?[0-9]*$/;
        if ( !pattern.test(num ) || num < 1 ) {
            class_del_error_show( "课时数必须为正整数!" );
            return false;
        }
        if (play_time_add_remark === false || play_time_add_remark === "") {
            class_del_error_show( "请输入备注!" );
            return false;
        }

        $.post("/play/playtime-add", {'user_id': user_id, 'num': num, 'remark':play_time_add_remark}, function (res) {
            if ( res.code == 200 ) {
                sweetAlert("成功!", "该用户娱乐数增加成功", "success");
            } else {
                class_del_error_show( res.msg );
                return false;
            }
        }, 'json');
    });
});


function class_del_error_show( msg ) {
    $('.sweet-alert .class_del_error').removeClass("hide");
    $('.sweet-alert .class_del_error').addClass("show");
    $('.sweet-alert .class_del_error p').text( msg );
};
```

##### 加载(AJAX请求示例)弹窗

```javascript
swal({
        title: "Ajax请求示例",
        text: "提交运行ajax请求",
        type: "info",
        showCancelButton: true,
        closeOnConfirm: false,
        showLoaderOnConfirm: true,
    },
    function () {
        setTimeout(function () {
            swal("Ajax请求完成！");
        }, 2000);
    }
);
```

![]({{site.baseurl}}/images/20210105/20210105215118.png)

##### 改变SweetAlert的主题样式

通过引入自己写的定制css文件改变SweetAlert的主题样式：
```javascript
<link rel="stylesheet" type="text/css" href="dist/sweetalert.css">
<link rel="stylesheet" type="text/css" href="themes/twitter.css">
```

![]({{site.baseurl}}/images/20210105/20210105215247.png)

#### 下载 和 安装

方式 1：通过bower安装：
> bower install sweetalert

方式 2：通过NPM安装：
> npm install sweetalert

方式 3：下载sweetAlert的CSS和JavaScript文件。

<https://github.com/t4t5/sweetalert/archive/master.zip>

引用必要的文件初始化插件：
```javascript
<script src="dist/sweetalert.min.js"></script> 
<link rel="stylesheet" type="text/css" href="dist/sweetalert.css">
```

页面加载完成后调用sweetAlert函数:
```javascript
swal({
    title: "Error!",
    text: "Here's my error message!",
    type: "error",
    confirmButtonText: "Cool"
});
```

#### 配置

你可以使用这些参数，通过一个对象传入到sweetAlert中：

| 参数 | 	默认值  |     描述 |
| :--------   | :-----  | :----  |
| title 	| null (required) 	| 弹窗的标题。可以通过对象的”title”属性或第一个参数进行传递。| 
| text 	| null | 	弹窗的描述。可以通过对象的”text”属性或第二个参数进行传递。| 
| type 	| null 	| 弹窗的类型。SweetAlert有四个内置类型，可以展示相应的图标动画: "warning","error", "success" and "info"。你也可以设置为"input"类型变成输入弹窗。可以通过对象的”type”属性或第三个参数进行传递。| 
| allowEscapeKey 	| true 	| 如果设置为true，用户可以通过按下Escape键关闭弹窗。|
| customClass 	| null | 	弹窗的自定义样式，可以通过对象的"customClass"属性进行添加。|
| allowOutsideClick 	| false 	| 如果设置为true，用户点击弹窗外部可关闭弹窗。|
| showCancelButton 	| false 	| 如果设置为true，“取消”按钮将会显示，用户点击取消按钮会关闭弹窗。|
| showConfirmButton 	| true 	| 如果设置为false，“确认”按钮将会隐藏。为了良好的用户体验，最好你设置了定时关闭或者allowOutsideClick为true时才将该参数设置为false。|
| confirmButtonText 	| "OK" 	| 使用该参数来修改“确认”按钮的显示文本。如果showCancelButton设置为true，确定按钮的显示文本将会自动使用“Confirm”而不是“OK”。|
| confirmButtonColor 	| "#AEDEF4" 	| 使用该参数来修改“确认”按钮的背景颜色（必须是十六进制值）。|
| cancelButtonText 	| "Cancel" 	| 使用该参数来修改“取消”按钮的显示文本。|
| closeOnConfirm 	| true 	| 设置为false，用户点击“确认”按钮后，弹窗会继续保持打开状态。如果点击“确认”按钮后需要打开另一个SweetAlert弹窗，这是非常有用的。|
| closeOnCancel 	| true 	| 这和closeOnConfirm的功能相似，只不过这个是“取消”按钮。|
| imageUrl 	| null 	| 为弹窗添加一个自定义的图标。这个参数是一个字符串图片路径。|
| imageSize 	| "80x80" 	| 如果设置了imageUrl，你可以使用像素（px）指定图片大小来描述你想要多大的图标。在一个字符串中使用“x”来分割两个值，第一个值是宽度，第二值是高度。|
| timer 	| null 	| 自动关闭弹窗的定时器。单位为毫秒（ms）。|
| html 	| false 	| 如果你设置为true，参数title和参数text的值将会被html解析显示而不是纯文本。（如果你担心被XSS攻击那就设置为false。）|
| animation 	| true 	| 如果设置为false, 弹窗的动画将会被禁用。其它字符串值：pop(这是animation设置为true时的默认值), slide-from-top, slide-from-bottom|
| inputType 	| "text" 	| 当使用type: "input"时，该参数用来改变输入的类型(例如：如果你想让用户输入密码，这可能是有用的)。|
| inputPlaceholder 	| null 	| 当使用输入类型时，你可以使用placeholder来帮助用户明白应该输入什么内容。|
| inputValue 	| null 	| 当使用type: "input"时，你希望在输入前展示默认值时，可以指定一个默认值。|
| showLoaderOnConfirm 	| false 	| 设置为true，当处于加载时会禁用确认按钮并显示为加载样式。|

#### 方法

SweetAlert还带有一些简单的方法，你可以调用它们：

| 方法 	| 示例    | 描述| 
| :--------   | :-----  | :----  |
| setDefaults 	| swal.setDefaults({ confirmButtonColor: '#0000' }); 	| 当调用SweetAlert时，如果你使用很多相同的设置，您可以在程序的开始使用setDefaults设置它们！| 
| close 	| swal.close(); 	| 通过编程的方式将当前打开的SweetAlert弹窗关闭。| 
| showInputError 	| swal.showInputError("Invalid email!"); 	| 如果用户输入的数据是错误的，在验证输入字段后会显示一个错误的信息。| 
| enableButtons, disableButtons 	| swal.disableButtons(); 	| 禁用或启用用户点击取消按钮和确认按钮。| 


<br/><br/><br/><br/><br/>
### 参考资料

SweetAlert 中文文档 <http://mishengqiang.com/sweetalert/>

SweetAlert 官网 <https://sweetalert.js.org/>

SweetAlert 中文网 <https://www.sweetalert.cn/>

SweetAlert 中文文档 <https://sweetalert.bootcss.com/>

漂亮的弹窗口插件——sweetAlert的使用 <https://www.cnblogs.com/lamp01/p/7215408.html>

