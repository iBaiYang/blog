---
layout: post
categories: jQuery
title: jQuery分页插件jqPaginator
meta: 我们有时在前段需要用到翻页功能，有没有比较到的插件可用呢？可以考虑下jQuery分页插件jqPaginator。
---
* content
{:toc}

### 正文

首先在项目下安装jQuery分页插件jqPaginator的源码。

然后在项目中使用：
```html
<div id="text"></div>
<div id="pagination-box"></div>

$('#pagination-box').jqPaginator({
    totalPages: 100,
    visiblePages: 10,
    currentPage: 1,

    first: '<li class="first"><a href="javascript:void(0);">首页</a></li>',
    prev: '<li class="prev"><a href="javascript:void(0);">上一页</a></li>',
    next: '<li class="next"><a href="javascript:void(0);">下一页</a></li>',
    last: '<li class="last"><a href="javascript:void(0);">末页</a></li>',
    page: '<li class="page"><a href="javascript:void(0);">{{page1}}</a></li>',
    onPageChange: function (num) {
        $('#text').html('当前第' + num + '页');
    }
});
```

参数解释：

| 参数 | 	默认值 	| 说明 | 
| :--------   | :-----  | :----  |
| totalPages | 	0  | 	设置分页的总页数 | 
| totalCounts | 	0  | 	设置分页的总条目数 | 
| pageSize |  	0  | 	设置每一页的条目数。 注意：要么设置totalPages，要么设置totalCounts + pageSize，否则报错；设置了totalCounts和pageSize后，会自动计算出totalPages。 | 
| currentPage  | 	1  | 	设置当前的页码 | 
| visiblePages |  	7 |  	设置最多显示的页码数（例如有100页，当前第1页，则显示1 - 7页） | 
| disableClass |  	'disabled'  | 	设置首页，上一页，下一页，末页的"禁用状态"样式 | 
| activeClass  | 	'active'  | 	设置当前页码样式 | 
| first  | 	(无)  | 	设置"首页"的Html结构 | 
| prev  | 	(无)  | 	设置"上一页"的Html结构 | 
| next |  	(无)  | 	设置"下一页"的Html结构 | 
| last  | 	(无)  | 	设置"末页"的Html结构 | 
| page |  	(无)  | 	设置页码的Html结构,其中可以使用`{{page1}}`代表当前页，`{{totalPages}}`代表总页数，`{{totalCounts}}`代表总条目数（例如：上面的"极简风格"的Demo，就是使用了`{{占位符}}`，并将visiblePages设为1实现的。）`| 
| wrapper  | 	(无)  | 	分页结构的Html包裹，例如：`<div class="your class"></div>`，一般不会用到 | 
| onPageChange  | 	(无)  | 	回调函数，当换页时触发（包括初始化第一页的时候），会传入"目标页"的页码 | 

**扩展方法**

jqPaginator提供了两个扩展方法，方便初始化后对组件进行操作。 

1、修改组件参数：
```
$('#pagination-box').jqPaginator('option', options)
```

如，修改当前所在页到首页位置：
```
$('#pagination-box').jqPaginator('option', {
    currentPage: 1
});
```

如，修改当前页面总条数为0：
```
$('#pagination-box').jqPaginator('option', {
    totalCounts: 0
});
```

2、动态销毁jqPaginator：
```
$('#pagination-box').jqPaginator('destroy');
```

#### jQuery中传统方式实现

```
<div class="table-responsive lists-box">

</div>
<!-- /.table-responsive -->
<div class="pages-box">
    <ul class="pagination pull-left">
        <li>
            <a href="javascript:;">
                <span class="text-info">共 <b><?= $count ?></b> 条</span>
            </a>
        </li>
    </ul>
    <ul id="pagination-box" class="pagination pull-right"></ul>
</div>

<script>
$(function () {
    if ( <?= $count ?> > 0 ) {
        var getParams = <?= json_encode(Yii::$app->request->get(), JSON_UNESCAPED_SLASHES) ?>;
        var type = !getParams.type ? '' : $.trim(getParams.type);
        var status = !getParams.status ? '' : $.trim(getParams.status);
        var nickname = !getParams.nickname ? '' : $.trim(getParams.nickname);

        var order_name = $(".search-box-order .users-sort li.current").attr('id');
        switch ( order_name )
        {
            case '1':
                order_name = 'id';
                break;
            case '2':
                order_name = 'type';
                break;
            case '3':
                order_name = 'username';
                break;
            case '4':
                order_name = 'nickname';
                break;
            case '5':
                order_name = 'phone';
                break;
            case '6':
                order_name = 'email';
                break;
            case '7':
                order_name = 'status';
                break;
            case '8':
                order_name = 'created_time';
                break;
            case '9':
                order_name = 'updated_time';
                break;
            default:
                order_name = 'id';
        }
        var order_type = $(".search-box-order .users-sort li.current").attr('class');
        if ( new RegExp('down').test(order_type) ) {
            order_type = '1';
        } else {
            order_type = '0';
        }

        $('#pagination-box').jqPaginator({
            totalCounts: <?= $count ?>,
            pageSize: 15,
            visiblePages: 8,
            currentPage: 1,
            onPageChange: function (num) {
                $(".lists-box").load('/user/users-lists?type=' + type
                    + "&status=" + status
                    + "&nickname=" + nickname
                    + '&order_name=' + order_name
                    + '&order_type=' + order_type
                    + "&page_num=" + num
                );
            }
        });
    }
});
</script>
```

#### Vue中实现

```
<div class='row background-body' id='vueOrderList'>
    <div class="col-md-12 search">
    </div>
    <div class="col-md-12 lists">
    </div>
    <div class="right-pagination" v-show='orderList.length > 0'>
        <span class="count-style">共 {{orderCount}} 条数据</span>
        <ul id="orders-paginator" class="pagination"></ul>
    </div>
<div>
<script>
var vueOrderList = new Vue({
    el: '#vueOrderList',
    data: {
        currentState: 10,
        listRequest: null,
        currentPage: 1,
        search: {
            order_no: "<?= $request_params["order_no"] ?>",
            user_id: "<?= $request_params["user_id"] ?>",
            user_nickname: '',
            user_mobile: '',
        },
        orderList: [],
        orderCount: 0,
    },
    computed: {
        noDataFound: function () {
            return (null === this.listRequest) && (0 === this.orderList.length);
        },
    },
    methods: {
        buildSearchQuery: function () {
            var list = [];
            for (var k in this.search) {
                var v = this.search[k];
                if (v == false) {
                    continue;
                }

                list.push(k + '=' + v);
            }
            return list.join('&');
        },
        doGetList: function (isNew) {
            if (isNew) {
                this.currentPage = 1;
                $('#orders-paginator').jqPaginator('option', {totalCounts: 0, "currentPage": 1});
            }

            var that = this;
            if (null !== that.listRequest) {
                that.listRequest.abort();
            }
            var query = ['action=list'];
            query.push("page=" + this.currentPage);
            if (search_query = that.buildSearchQuery()) {
                query.push(search_query);
            }
            var url = '/order/list?' + query.join('&');
            that.listRequest = $.get(url, function (json) {
                that.listRequest = null;
                if (200 != json.code) {
                    return swal('错误', json.msg, 'error');
                } else {
                    that.orderList = json.data.list;
                    that.orderCount = parseInt(json.data.count);
                    var show_count = that.orderCount == 0 ? 1 : that.orderCount;
                    $('#orders-paginator').jqPaginator('option', {totalCounts: show_count});
                }
            }, 'json');
        },
    },
    watch: {
        currentState: function (newVal, oldVal) {
            if (null !== oldVal) {
                this.doGetList(true);
            }
        },
        search: {
            deep: true,
            handler: function () {
                this.doGetList(true);
            }
        }
    },
    created: function () {
        var that = this;
        that.currentState = that.stateList[that.initStateIndex];
    },
    mounted: function () {
        var that = this;
        $('#orders-paginator').jqPaginator({
            totalCounts: 8,
            pageSize: 20,
            visiblePages: 4,
            currentPage: 1,
            first: '<li class="first"><a href="javascript:void(0);" class="jqpage-new">首页<\/a><\/li>',
            prev: '<li class="prev"><a href="javascript:void(0);" class="jqpage-new"><i class="arrow arrow2"><\/i>上一页<\/a><\/li>',
            next: '<li class="next"><a href="javascript:void(0);" class="jqpage-new">下一页<i class="arrow arrow3"><\/i><\/a><\/li>',
            last: '<li class="last"><a href="javascript:void(0);" class="jqpage-new">末页<\/a><\/li>',
            page: '<li class="page"><a href="javascript:void(0);" class="jqpage-new">{{page}}<\/a><\/li>',
            onPageChange: function (num, type)
            {
                that.currentPage = num;
                that.doGetList();
            }
        });
    },
});
</script>
```



<br/><br/><br/><br/><br/>
### 参考资料 

jqPaginator 源码包位置 <https://github.com/keenwon/jqPaginator>

jQuery 分页插件 : jqPaginator <https://www.runoob.com/w3cnote/jquery-jqpaginator.html>
