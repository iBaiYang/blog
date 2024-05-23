---
layout: post
categories: jQuery
title: jQuery分页插件jqPaginator
meta: 我们有时在前段需要用到翻页功能，有没有比较到的插件可用呢？可以考虑下jQuery分页插件jqPaginator。
---
* content
{:toc}

## 引言

jqPaginator 是一个用来快速分页的 Query插件。官网 <https://jqpaginator.keenwon.com/>

用法很简单，首先引入jQuery和jqPaginator，之后就可以初始化分页了：

```
$('#id').jqPaginator({
    totalPages: 100,
    visiblePages: 10,
    currentPage: 1,
    onPageChange: function (num, type) {
        $('#text').html('当前第' + num + '页');
    }
});
```

**参数**

![]({{site.baseurl}}/images/20240523/20240523185459.png)

**扩展方法**

jqPaginator提供了两个扩展方法，方便初始化后对组件进行操作。

`$('#id').jqPaginator('option', options)`

初始化后，动态修改配置
```
$('#id').jqPaginator('option', {
    currentPage: 1
});
```

销毁jqPaginator
`$('#id').jqPaginator('destroy');`

**源码**

```
/*! jqPaginator-1.2.0 
 * http://jqPaginator.keenwon.com
 */
!function (a) {
    "use strict";
    a.jqPaginator = function (b, c) {
        if (!(this instanceof a.jqPaginator)) return new a.jqPaginator(b, c);
        var d = this;
        return d.$container = a(b), d.$container.data("jqPaginator", d), d.init = function () {
            (c.first || c.prev || c.next || c.last || c.page) && (c = a.extend({}, {
                first: "",
                prev: "",
                next: "",
                last: "",
                page: ""
            }, c)), d.options = a.extend({}, a.jqPaginator.defaultOptions, c), d.verify(), d.extendJquery(), d.render(), d.fireEvent(this.options.currentPage, "init")
        }, d.verify = function () {
            var a = d.options;
            if (!d.isNumber(a.totalPages)) throw new Error("[jqPaginator] type error: totalPages");
            if (!d.isNumber(a.totalCounts)) throw new Error("[jqPaginator] type error: totalCounts");
            if (!d.isNumber(a.pageSize)) throw new Error("[jqPaginator] type error: pageSize");
            if (!d.isNumber(a.currentPage)) throw new Error("[jqPaginator] type error: currentPage");
            if (!d.isNumber(a.visiblePages)) throw new Error("[jqPaginator] type error: visiblePages");
            if (!a.totalPages && !a.totalCounts) throw new Error("[jqPaginator] totalCounts or totalPages is required");
            if (!a.totalPages && !a.totalCounts) throw new Error("[jqPaginator] totalCounts or totalPages is required");
            if (!a.totalPages && a.totalCounts && !a.pageSize) throw new Error("[jqPaginator] pageSize is required");
            if (a.totalCounts && a.pageSize && (a.totalPages = Math.ceil(a.totalCounts / a.pageSize)), a.currentPage < 1 || a.currentPage > a.totalPages) throw new Error("[jqPaginator] currentPage is incorrect");
            if (a.totalPages < 1) throw new Error("[jqPaginator] totalPages cannot be less currentPage")
        }, d.extendJquery = function () {
            a.fn.jqPaginatorHTML = function (b) {
                return b ? this.before(b).remove() : a("<p>").append(this.eq(0).clone()).html()
            }
        }, d.render = function () {
            d.renderHtml(), d.setStatus(), d.bindEvents()
        }, d.renderHtml = function () {
            for (var b = [], c = d.getPages(), e = 0, f = c.length; f > e; e++) b.push(d.buildItem("page", c[e]));
            d.isEnable("prev") && b.unshift(d.buildItem("prev", d.options.currentPage - 1)), d.isEnable("first") && b.unshift(d.buildItem("first", 1)), d.isEnable("statistics") && b.unshift(d.buildItem("statistics")), d.isEnable("next") && b.push(d.buildItem("next", d.options.currentPage + 1)), d.isEnable("last") && b.push(d.buildItem("last", d.options.totalPages)), d.options.wrapper ? d.$container.html(a(d.options.wrapper).html(b.join("")).jqPaginatorHTML()) : d.$container.html(b.join(""))
        }, d.buildItem = function (b, c) {
            var e = d.options[b].replace(/{{page}}/g, c).replace(/{{totalPages}}/g, d.options.totalPages).replace(/{{totalCounts}}/g, d.options.totalCounts);
            return a(e).attr({"jp-role": b, "jp-data": c}).jqPaginatorHTML()
        }, d.setStatus = function () {
            var b = d.options;
            d.isEnable("first") && 1 !== b.currentPage || a("[jp-role=first]", d.$container).addClass(b.disableClass), d.isEnable("prev") && 1 !== b.currentPage || a("[jp-role=prev]", d.$container).addClass(b.disableClass), (!d.isEnable("next") || b.currentPage >= b.totalPages) && a("[jp-role=next]", d.$container).addClass(b.disableClass), (!d.isEnable("last") || b.currentPage >= b.totalPages) && a("[jp-role=last]", d.$container).addClass(b.disableClass), a("[jp-role=page]", d.$container).removeClass(b.activeClass), a("[jp-role=page][jp-data=" + b.currentPage + "]", d.$container).addClass(b.activeClass)
        }, d.getPages = function () {
            var a = [], b = d.options.visiblePages, c = d.options.currentPage, e = d.options.totalPages;
            b > e && (b = e);
            var f = Math.floor(b / 2), g = c - f + 1 - b % 2, h = c + f;
            1 > g && (g = 1, h = b), h > e && (h = e, g = 1 + e - b);
            for (var i = g; h >= i;) a.push(i), i++;
            return a
        }, d.isNumber = function (a) {
            var b = typeof a;
            return "number" === b || "undefined" === b
        }, d.isEnable = function (a) {
            return d.options[a] && "string" == typeof d.options[a]
        }, d.switchPage = function (a) {
            d.options.currentPage = a, d.render()
        }, d.fireEvent = function (a, b) {
            return "function" != typeof d.options.onPageChange || d.options.onPageChange(a, b) !== !1
        }, d.callMethod = function (b, c) {
            switch (b) {
                case"option":
                    d.options = a.extend({}, d.options, c), d.verify(), d.render();
                    break;
                case"destroy":
                    d.$container.empty(), d.$container.removeData("jqPaginator");
                    break;
                default:
                    throw new Error('[jqPaginator] method "' + b + '" does not exist')
            }
            return d.$container
        }, d.bindEvents = function () {
            var b = d.options;
            d.$container.off(), d.$container.on("click", "[jp-role]", function () {
                var c = a(this);
                if (!c.hasClass(b.disableClass) && !c.hasClass(b.activeClass)) {
                    var e = +c.attr("jp-data");
                    d.fireEvent(e, "change") && d.switchPage(e)
                }
            })
        }, d.init(), d.$container
    }, a.jqPaginator.defaultOptions = {
        wrapper: "",
        first: '<li class="first"><a href="javascript:;">First</a></li>',
        prev: '<li class="prev"><a href="javascript:;">Previous</a></li>',
        next: '<li class="next"><a href="javascript:;">Next</a></li>',
        last: '<li class="last"><a href="javascript:;">Last</a></li>',
        page: '<li class="page"><a href="javascript:;">{{page}}</a></li>',
        totalPages: 0,
        totalCounts: 0,
        pageSize: 0,
        currentPage: 1,
        visiblePages: 7,
        disableClass: "disabled",
        activeClass: "active",
        onPageChange: null
    }, a.fn.jqPaginator = function () {
        var b = this, c = Array.prototype.slice.call(arguments);
        if ("string" == typeof c[0]) {
            var d = a(b).data("jqPaginator");
            if (d) return d.callMethod(c[0], c[1]);
            throw new Error("[jqPaginator] the element is not instantiated")
        }
        return new a.jqPaginator(this, c[0])
    }
}(jQuery);
```

**特别说明**

如果 jqPaginator 中的参数 totalPages 或者 totalCounts 为 0 时，会报错：
`Uncaught Error: [jqPaginator] totalCounts or totalPages is required` ，
所以知道这两个值为 0 时，就不要使用 jqPaginator 方法。

## 使用

首先在项目下安装jQuery分页插件jqPaginator的 [源码](https://github.com/keenwon/jqPaginator) ,引入：

```
<script src="./dist/jq-paginator.min.js"></script>
```

然后在项目中使用：
```html
<div id="text"></div>
<div id="pagination-box" class="pagination"></div>

$('#pagination-box').jqPaginator({
    totalPages: 100,
    visiblePages: 10,
    currentPage: 1,

    first: '<li class="first"><a href="javascript:void(0);">首页</a></li>',
    prev: '<li class="prev"><a href="javascript:void(0);">上一页</a></li>',
    next: '<li class="next"><a href="javascript:void(0);">下一页</a></li>',
    last: '<li class="last"><a href="javascript:void(0);">末页</a></li>',
    page: '<li class="page"><a href="javascript:void(0);">双大括号 page 双大括号</a></li>',
    onPageChange: function (num) {
        $('#text').html('当前第' + num + '页');
    }
});
```

![]({{site.baseurl}}/images/20210108/20210108110336.png)

上例就是第一Demo，Bootstrap风格的分页(class属性)。这里要了解的是，如果使用的不是id，而是class，就会初始化该class的所有元素，实现上面"两个分页联动"的效果。

看一个最简单的使用实例：

```javascript
<div id="text"></div>
<div id="pagination-box" class="pagination"></div>

<script>
    $('#pagination-box').jqPaginator({
        totalPages: 100,
        currentPage: 1,

        onPageChange: function (num) {
            $('#text').html('当前第' + num + '页');
        }
    });
</script>
```

![]({{site.baseurl}}/images/20210108/20210108110805.png)

还有这种：

```javascript
<div class="list-box"></div>
<div class="page-box">
    <ul class="pagination pull-left">
        <li>
            <a href="javascript:;">
                <span class="text-info">共 <b><?= $count; ?></b> 条</span>
            </a>
        </li>
    </ul>
    <ul id="pagination-box" class="pagination pull-right"></ul>
</div>

<script>
    $(function () {
        var getParams = <?= json_encode(Yii::$app->request->get(), JSON_UNESCAPED_SLASHES) ?>;
        var status = !getParams.status ? '' : getParams.status;
        var time_start = !getParams.time_start ? '' : getParams.time_start;
        var time_end = !getParams.time_end ? '' : getParams.time_end;
        var content = !getParams.content ? '' : getParams.content;

        $('#pagination-box').jqPaginator({
            totalCounts: <?= $count; ?>,
            pageSize: 1,
            visiblePages: 6,
            currentPage: 1,

            first: '<li class="first"><a href="javascript:void(0);">首页</a></li>',
            prev: '<li class="prev"><a href="javascript:void(0);">上一页</a></li>',
            next: '<li class="next"><a href="javascript:void(0);">下一页</a></li>',
            last: '<li class="last"><a href="javascript:void(0);">末页</a></li>',
            page: '<li class="page"><a href="javascript:void(0);">双大括号 page 双大括号</a></li>',
            onPageChange: function (num) {
                $(".list-box").load('/record/record-list?status=' + status
                    + '&time_start=' + time_start + '&time_end=' + time_end
                    + '&content=' + content + "&page_num=" + num);
            }
        });
    });
</script>
```

![]({{site.baseurl}}/images/20210108/20210108111157.png)

### 参数解释

| 参数 |     默认值     | 说明 | 
| :--------   | :-----  | :----  |
| totalPages |     0  |     设置分页的总页数 | 
| totalCounts |     0  |     设置分页的总条目数 | 
| pageSize |      0  |     设置每一页的条目数。 注意：要么设置totalPages，要么设置totalCounts + pageSize，否则报错；设置了totalCounts和pageSize后，会自动计算出totalPages。 | 
| currentPage  |     1  |     设置当前的页码 | 
| visiblePages |      7 |      设置最多显示的页码数（例如有100页，当前第1页，则显示1 - 7页） | 
| disableClass |      'disabled'  |     设置首页，上一页，下一页，末页的"禁用状态"样式 | 
| activeClass  |     'active'  |     设置当前页码样式 | 
| first  |     (无)  |     设置"首页"的Html结构 | 
| prev  |     (无)  |     设置"上一页"的Html结构 | 
| next |      (无)  |     设置"下一页"的Html结构 | 
| last  |     (无)  |     设置"末页"的Html结构 | 
| page |      (无)  |     设置页码的Html结构,其中可以使用 双大括号加 `page`代表当前页，`totalPages`代表总页数，`totalCounts`代表总条目数（例如：上面的"极简风格"的Demo，就是使用了 双大括号`占位符`双大括号，并将visiblePages设为1实现的。）| 
| wrapper  |     (无)  |     分页结构的Html包裹，例如：`<div class="your class"></div>`，一般不会用到 | 
| onPageChange  |     (无)  |     回调函数，当换页时触发（包括初始化第一页的时候），会传入"目标页"的页码 | 

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

### jQuery中传统方式实现

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

### Vue中实现

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
            page: '<li class="page"><a href="javascript:void(0);" class="jqpage-new">双大括号page双大括号<\/a><\/li>',
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
## 参考资料 

jqPaginator 源码包位置 <https://github.com/scepterscythe/jqPaginator>

<https://github.com/keenwon/jqPaginator>

jQuery 分页插件 : jqPaginator <https://www.runoob.com/w3cnote/jquery-jqpaginator.html>
