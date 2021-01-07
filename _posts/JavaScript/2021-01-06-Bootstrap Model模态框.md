---
layout: post
categories: JavaScript
title: Bootstrap Model模态框
meta: Bootstrap Model模态框
---
* content
{:toc}

### 正文

引入Bootstrap的js、css文件，使用：
```javascript
<!-- 视图模态框 -->
<div class="modal fade" id="viewModal">
    <div class="modal-dialog">
        <div class="modal-content">
            <!-- 模态框头部 -->
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal">&times;</button>
                <h4 class="modal-title">模态框头部</h4>
            </div>
            <!-- 模态框主体 -->
            <div class="modal-body" style="min-height: 300px;">
                模态框内容..
            </div>
        </div>
    </div>
</div>

<!-- 操作模态框 -->
<div class="modal fade" id="optModal">
    <div class="modal-dialog">
        <div class="modal-content">
            <!-- 模态框头部 -->
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal">&times;</button>
                <h4 class="modal-title">模态框头部</h4>
            </div>
            <!-- 模态框主体 -->
            <div class="modal-body" style="min-height: 300px;">
                模态框内容..
            </div>
            <!-- 模态框底部 -->
            <div class="modal-footer">
                <button type="button" class="btn btn-default pull-left" data-dismiss="modal">关 闭</button>
                <button type="button" class="btn btn-info pull-right">确 定</button>
            </div>
        </div>
    </div>
</div>
```

调用方法1：
```javascript
<button type="button" class="btn btn-primary" data-toggle="modal" data-target="#viewModal">
打开模态框
</button>
```

这种方法无法传递参数，只是把Model模态框展示出来，我们更多的是要进行相关操作，看一下下面的方法2。

调用方法2：
```javascript
<a rel="remind_plan_edit" data-remind_plan_id="100" class="btn btn-sm" href="javascript:;"><span class="fa fa-edit"></span></a>

$(document).on('click', 'a[rel=remind_plan_edit]', function () {
    var remind_plan_id = $(this).data("remind_plan_id");
    $("#optModal").modal({backdrop: "static"});
    $("#optModal .modal-title").text("提醒计划编辑");
    $("#optModal .modal-footer .pull-right").attr('id', 'confirm_remind_plan_edit');
    $('#optModal .modal-body').empty().load('/record/remind-plan-edit?remind_plan_id=' + remind_plan_id);
});
```

![]({{site.baseurl}}/images/20210106/20210106204758.png)

如果觉得上面模态框不够宽，可以在 modal-dialog 上加上宽度样式。如下：
```javascript
<div class="modal fade" id="optModal">
    <div class="modal-dialog" style="width: 66%;">
        <div class="modal-content">
            <!-- 模态框头部 -->
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal">&times;</button>
                <h4 class="modal-title">模态框头部</h4>
            </div>
            <!-- 模态框主体 -->
            <div class="modal-body" style="min-height: 300px;">
                模态框内容..
            </div>
            <!-- 模态框底部 -->
            <div class="modal-footer">
                <button type="button" class="btn btn-default pull-left" data-dismiss="modal">关 闭</button>
                <button type="button" class="btn btn-info pull-right">确 定</button>
            </div>
        </div>
    </div>
</div>
```

<br/><br/><br/><br/><br/>
### 参考资料 

Bootstrap4 模态框 <https://www.runoob.com/bootstrap4/bootstrap4-modal.html>