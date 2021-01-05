---
layout: post
categories: JavaScript
title: SweetAlert 弹出窗口多输入框解决方案
meta: SweetAlert的示例中有单个输入框的例子，但是实际开发中会遇到多个输入框的情况，怎么解决呢。
---

* content
{:toc}

### 正文

#### 单输入框

SweetAlert的示例中有单个输入框的例子：

![]({{site.baseurl}}/images/20210104/20210104223552.png)

代码如下：
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
function(inputValue){ 
    if (inputValue === false) return false; 
    
    if (inputValue === "") { 
        swal.showInputError("你需要输入一些话！");
        return false 
    } 
    
    swal("非常好！", "你输入了：" + inputValue,"success"); 
});		
```

#### 多输入框

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

样式用的是SweetAlert自带的样式。

细节方面可能不一样，但是解决问题的方法已给出，理解思想就可以发挥。

如果输入区域要操作的内容比较多，可以考虑 Bootstrap 的[模态框](https://www.runoob.com/bootstrap/bootstrap-modal-plugin.html)（Modal）插件。

<br/><br/><br/><br/><br/>
### 参考资料

SweetAlert 中文文档 <http://mishengqiang.com/sweetalert/>

SweetAlert 官网 <https://sweetalert.js.org/>

SweetAlert 中文网 <https://www.sweetalert.cn/>

SweetAlert 中文文档 <https://sweetalert.bootcss.com/>

漂亮的弹窗口插件——sweetAlert的使用 <https://www.cnblogs.com/lamp01/p/7215408.html>

Bootstrap 教程 <http://www.runoob.com/bootstrap/bootstrap-tutorial.html>