---
layout: post
categories: Bootstrap
title: Bootstrap 弹出多个模态框解决方案
meta: 有时需要在页面上弹出多个模态框，但Bootstrap只允许弹出一个模态框，这里谈谈解决方案。
---
* content
{:toc}

## 正文

模态框（Modal）是覆盖在父窗体上的子窗体。通常，目的是显示来自一个单独的源的内容，
可以在不离开父窗体的情况下有一些互动。子窗体可提供信息、交互等。
引用 bootstrap.js 或压缩版的 bootstrap.min.js，就可以使用模态框（Modal）插件。
如果您想要单独引用该插件的功能，那么您需要引用 modal.js。

您可以有以下两种方式切换模态框（Modal）：

方式1，通过 data 属性：在控制器元素（比如按钮或者链接）上设置属性 `data-toggle="modal"`，
同时设置 `data-target="#identifier"` 或 `href="#identifier"` 来指定要切换的特定的模态框（带有 `id="identifier"`）。

方式2，通过 JavaScript：使用这种技术，您可以通过简单的一行 JavaScript 来调用带有 `id="identifier"` 的模态框： 
```
$('#identifier').modal(options);
```

### 实例

![]({{site.baseurl}}/images/20210112/20210112141856.png)

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link href="http://cdn.staticfile.org/twitter-bootstrap/3.3.4/css/bootstrap.min.css" rel="stylesheet">
    <style>
        #container{
            height:1000px;
            background:#ccc;
        }
    </style>
</head>
<body>
    <div id="container">
        <span>这是一个很长的div,使页面出现滚动条。</span>
        <h2>使用Bootstrap创建多模态框</h2>
        <div id="example1" class="modal fade">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <a class="close" data-dismiss="modal">×</a>
                        <h3>这是第一个模态框</h3>
                    </div>
                    <div class="modal-body">
                        <h4>第一个模态框中的文本</h4>
                        <p>你可以在这添加一些文本。</p>
                    </div>
                    <div class="modal-footer">
                        <a data-toggle="modal" href="#example2" class="btn btn-primary btn-large">弹出第二个模态框</a>
                        <a href="#" class="btn" data-dismiss="modal">关闭</a>
                    </div>
                </div>
            </div>
        </div>
        <div id="example2" class="modal fade">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <a class="close" data-dismiss="modal">×</a>
                        <h3>这是第二个模态框</h3>
                    </div>
                    <div class="modal-body">
                        <h4>第二个模态框中的文本</h4>
                        <p style="height:900px">你可以在这添加一些文本。</p>
                    </div>
                    <div class="modal-footer">
                        <a data-toggle="modal" href="#example3" class="btn btn-primary btn-large">弹出第三个模态框</a>
                        <a href="#" class="btn" data-dismiss="modal">关闭</a>
                    </div>
                </div>
            </div>
        </div>
        <div id="example3" class="modal fade">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <a class="close" data-dismiss="modal">×</a>
                        <h3>这是第三个模态框</h3>
                    </div>
                    <div class="modal-body">
                        <h4>第三个模态框中的文本</h4>
                        <p>你可以在这添加一些文本。</p>
                    </div>
                    <div class="modal-footer">
                        <!--<a data-toggle="modal" href="#example2" class="btn btn-primary btn-large">发动演示模态框</a>-->
                        <a href="#" class="btn" data-dismiss="modal">关闭</a>
                    </div>
                </div>
            </div>
        </div>
        <p><a data-toggle="modal" href="#example1" class="btn btn-primary btn-large">发动演示模态框</a></p>
    </div>
    
    <script src="http://cdn.staticfile.org/jquery/3.0.0/jquery.min.js"></script>
    <script src="http://cdn.staticfile.org/twitter-bootstrap/3.3.4/js/bootstrap.min.js"></script>
    <script>
        jQuery(function($){
            //解决模态框背景色越来越深的问题
            $(document).on('show.bs.modal', '.modal', function(event) {
                $(this).appendTo($('body'));
            }).on('shown.bs.modal', '.modal.in', function(event) {
                setModalsAndBackdropsOrder();
            }).on('hidden.bs.modal', '.modal', function(event) {
                setModalsAndBackdropsOrder();
            });

            function setModalsAndBackdropsOrder() {
                var modalZIndex = 1040;
                $('.modal.in').each(function(index) {
                    var $modal = $(this);
                    modalZIndex++;
                    $modal.css('zIndex', modalZIndex);
                    $modal.next('.modal-backdrop.in').addClass('hidden').css('zIndex', modalZIndex - 1);
                });
                $('.modal.in:visible:last').focus().next('.modal-backdrop.in').removeClass('hidden');
            }

            //覆盖Modal.prototype的hideModal方法
            $.fn.modal.Constructor.prototype.hideModal = function () {
                var that = this
                this.$element.hide()
                this.backdrop(function () {
                    //判断当前页面所有的模态框都已经隐藏了之后body移除.modal-open，即body出现滚动条。
                    $('.modal.fade.in').length === 0 && that.$body.removeClass('modal-open')
                    that.resetAdjustments()
                    that.resetScrollbar()
                    that.$element.trigger('hidden.bs.modal')
                })
            }
        });
    </script>
</body>
</html>
```

下面定制那一段代码去掉也可以运行，不过随着弹框的增多，背景色会越来越深。

<br/><br/><br/><br/><br/>
## 参考资料 

Bootstrap 模态框（Modal）插件 <https://www.runoob.com/bootstrap/bootstrap-modal-plugin.html>

<http://blog.csdn.net/k358971707/article/details/71908862>

<http://blog.csdn.net/koalashane/article/details/53516411>

<http://blog.csdn.net/qq_24591547/article/details/72830599>


<br/><br/><br/><br/><br/>
## 源码

<pre style="background-color:#272822;color:#f8f8f2;font-family:'source Code pro';font-size:12.0pt;">
<span style="color:#6ccab8;font-weight:bold;">jQuery</span><span style="color:#ffffff;">(</span><span style="color:#66d9ef;">function</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">$</span><span style="color:#ffffff;">){<br></span><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp;    </span><span style="color:#736f5c;">//</span><span style="color:#736f5c;font-family:'宋体';">解决模态框背景色越来越深的问题<br></span><span style="color:#736f5c;font-family:'宋体';">    </span><span style="color:#bf9bf8;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>$</span><span style="color:#ffffff;">(</span><span style="color:#6ccab8;font-weight:bold;">document</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;">on</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'show.bs.modal'</span><span style="color:#ffffff;">, </span><span style="color:#ffe792;">'.modal'</span><span style="color:#ffffff;">, </span><span style="color:#66d9ef;">function</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">event</span><span style="color:#ffffff;">) {<br></span><span style="color:#ffffff;">        </span><span style="color:#bf9bf8;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#bf9bf8;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>$</span><span style="color:#ffffff;">(</span><span style="color:#66d9ef;">this</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;font-weight:bold;">appendTo</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">$</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'body'</span><span style="color:#ffffff;">));<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>}).</span><span style="color:#6ccab8;">on</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'shown.bs.modal'</span><span style="color:#ffffff;">, </span><span style="color:#ffe792;">'.modal.in'</span><span style="color:#ffffff;">, </span><span style="color:#66d9ef;">function</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">event</span><span style="color:#ffffff;">) {<br></span><span style="color:#ffffff;">        </span><span style="color:#f72671;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#f72671;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>setModalsAndBackdropsOrder</span><span style="color:#ffffff;">();<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>}).</span><span style="color:#6ccab8;">on</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'hidden.bs.modal'</span><span style="color:#ffffff;">, </span><span style="color:#ffe792;">'.modal'</span><span style="color:#ffffff;">, </span><span style="color:#66d9ef;">function</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">event</span><span style="color:#ffffff;">) {<br></span><span style="color:#ffffff;">        </span><span style="color:#f72671;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#f72671;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>setModalsAndBackdropsOrder</span><span style="color:#ffffff;">();<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>});<br></span><span style="color:#ffffff;"><br></span><span style="color:#ffffff;">    </span><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>function </span><span style="color:#f72671;">setModalsAndBackdropsOrder</span><span style="color:#ffffff;">() {<br></span><span style="color:#ffffff;">        </span><span style="color:#66d9ef;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>var </span><span style="color:#a6e22e;">modalZIndex </span><span style="color:#f72671;">= </span><span style="color:#66d9ef;">1040</span><span style="color:#ffffff;">;<br></span><span style="color:#ffffff;">        </span><span style="color:#bf9bf8;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>$</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'.modal.in'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;">each</span><span style="color:#ffffff;">(</span><span style="color:#66d9ef;">function</span><span style="color:#ffffff;">(</span><span style="color:#bf9bf8;">index</span><span style="color:#ffffff;">) {<br></span><span style="color:#ffffff;">            </span><span style="color:#66d9ef;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span></span><span style="color:#66d9ef;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>var </span><span style="color:#a6e22e;">$modal </span><span style="color:#f72671;">= </span><span style="color:#bf9bf8;">$</span><span style="color:#ffffff;">(</span><span style="color:#66d9ef;">this</span><span style="color:#ffffff;">);<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span></span><span style="color:#a6e22e;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>modalZIndex</span><span style="color:#f72671;">++</span><span style="color:#ffffff;">;<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;</span></span></span></span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp; </span></span></span>$modal</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">css</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'zIndex'</span><span style="color:#ffffff;">, </span><span style="color:#a6e22e;">modalZIndex</span><span style="color:#ffffff;">);<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;</span></span></span></span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp; </span></span></span>$modal</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">next</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'.modal-backdrop.in'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;">addClass</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'hidden'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;font-weight:bold;">css</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'zIndex'</span><span style="color:#ffffff;">, </span><span style="color:#a6e22e;">modalZIndex </span><span style="color:#f72671;">- </span><span style="color:#66d9ef;">1</span><span style="color:#ffffff;">);<br></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>});<br></span><span style="color:#ffffff;">        </span><span style="color:#bf9bf8;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>$</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'.modal.in:visible:last'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;font-weight:bold;">focus</span><span style="color:#ffffff;">().</span><span style="color:#6ccab8;">next</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'.modal-backdrop.in'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;">removeClass</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'hidden'</span><span style="color:#ffffff;">);<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>}<br></span><span style="color:#ffffff;"><br></span><span style="color:#ffffff;">    </span><span style="color:#736f5c;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>//</span><span style="color:#736f5c;font-family:'宋体';">覆盖</span><span style="color:#736f5c;">Modal.prototype</span><span style="color:#736f5c;font-family:'宋体';">的</span><span style="color:#736f5c;">hideModal</span><span style="color:#736f5c;font-family:'宋体';">方法<br></span><span style="color:#736f5c;font-family:'宋体';">    </span><span style="color:#bf9bf8;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>$</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">fn</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">modal</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">Constructor</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">prototype</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">hideModal </span><span style="color:#f72671;">= </span><span style="color:#66d9ef;">function </span><span style="color:#ffffff;">() {<br></span><span style="color:#ffffff;">        </span><span style="color:#66d9ef;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>var </span><span style="color:#a6e22e;">that </span><span style="color:#f72671;">= </span><span style="color:#66d9ef;">this<br></span><span style="color:#66d9ef;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>this</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">$element</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">hide</span><span style="color:#ffffff;">()<br></span><span style="color:#ffffff;">        </span><span style="color:#66d9ef;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>this</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">backdrop</span><span style="color:#ffffff;">(</span><span style="color:#66d9ef;">function </span><span style="color:#ffffff;">() {<br></span><span style="color:#ffffff;">            </span><span style="color:#736f5c;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span></span><span style="color:#736f5c;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span></span></span>//</span><span style="color:#736f5c;font-family:'宋体';">判断当前页面所有的模态框都已经隐藏了之后</span><span style="color:#736f5c;">body</span><span style="color:#736f5c;font-family:'宋体';">移除</span><span style="color:#736f5c;">.modal-open</span><span style="color:#736f5c;font-family:'宋体';">，即</span><span style="color:#736f5c;">body</span><span style="color:#736f5c;font-family:'宋体';">出现滚动条。<br></span><span style="color:#736f5c;font-family:'宋体';">            </span><span style="color:#bf9bf8;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;</span></span></span></span><span style="color:#bf9bf8;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp; </span></span></span>$</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'.modal.fade.in'</span><span style="color:#ffffff;">).</span><span style="color:#6ccab8;font-weight:bold;">length </span><span style="color:#f72671;">=== </span><span style="color:#66d9ef;">0 </span><span style="color:#f72671;">&amp;&amp; </span><span style="color:#a6e22e;">that</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">$body</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">removeClass</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'modal-open'</span><span style="color:#ffffff;">)<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;</span></span></span></span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp;&nbsp; </span></span></span>that</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">resetAdjustments</span><span style="color:#ffffff;">()<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;</span></span></span></span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp;&nbsp; </span></span></span>that</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">resetScrollbar</span><span style="color:#ffffff;">()<br></span><span style="color:#ffffff;">            </span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span><span style="color:#a6e22e;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#66d9ef;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>&nbsp;&nbsp;&nbsp;&nbsp; </span></span></span>that</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;font-weight:bold;">$element</span><span style="color:#ffffff;">.</span><span style="color:#6ccab8;">trigger</span><span style="color:#ffffff;">(</span><span style="color:#ffe792;">'hidden.bs.modal'</span><span style="color:#ffffff;">)<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span><span style="color:#ffffff;"><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span></span>})<br></span><span style="color:#ffffff;"><span style="color:#ffffff;">&nbsp;&nbsp;&nbsp;&nbsp; </span>}<br></span><span style="color:#ffffff;">});</span>
</pre>
<font style="font-size: 16px;"><font style="font-size: 16px;"><br>
<br>