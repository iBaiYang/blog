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
    ZENG.msgbox.show("正在加载数据...", 6);  // 不必设定显示时间，默认时间是5s
    // 执行一些ajax操作
    ZENG.msgbox.hide();  // 手动隐藏
    */
</script>
```

#### 再次定制

##### loading图片定制

如果你在页面中直接使用，当是类型6，loading时，loading图片会加载不出来，我们需要定制。

看一下 msgbox.js 文件内容：
```javascript

window.ZENG=window.ZENG || {};

ZENG.dom = {getById: function(id) {
        return document.getElementById(id);
    },get: function(e) {
        return (typeof (e) == "string") ? document.getElementById(e) : e;
    },createElementIn: function(tagName, elem, insertFirst, attrs) {
        var _e = (elem = ZENG.dom.get(elem) || document.body).ownerDocument.createElement(tagName || "div"), k;
        if (typeof (attrs) == 'object') {
            for (k in attrs) {
                if (k == "class") {
                    _e.className = attrs[k];
                } else if (k == "style") {
                    _e.style.cssText = attrs[k];
                } else {
                    _e[k] = attrs[k];
                }
            }
        }
        insertFirst ? elem.insertBefore(_e, elem.firstChild) : elem.appendChild(_e);
        return _e;
    },getStyle: function(el, property) {
        el = ZENG.dom.get(el);
        if (!el || el.nodeType == 9) {
            return null;
        }
        var w3cMode = document.defaultView && document.defaultView.getComputedStyle, computed = !w3cMode ? null : document.defaultView.getComputedStyle(el, ''), value = "";
        switch (property) {
            case "float":
                property = w3cMode ? "cssFloat" : "styleFloat";
                break;
            case "opacity":
                if (!w3cMode) {
                    var val = 100;
                    try {
                        val = el.filters['DXImageTransform.Microsoft.Alpha'].opacity;
                    } catch (e) {
                        try {
                            val = el.filters('alpha').opacity;
                        } catch (e) {
                        }
                    }
                    return val / 100;
                } else {
                    return parseFloat((computed || el.style)[property]);
                }
                break;
            case "backgroundPositionX":
                if (w3cMode) {
                    property = "backgroundPosition";
                    return ((computed || el.style)[property]).split(" ")[0];
                }
                break;
            case "backgroundPositionY":
                if (w3cMode) {
                    property = "backgroundPosition";
                    return ((computed || el.style)[property]).split(" ")[1];
                }
                break;
        }
        if (w3cMode) {
            return (computed || el.style)[property];
        } else {
            return (el.currentStyle[property] || el.style[property]);
        }
    },setStyle: function(el, properties, value) {
        if (!(el = ZENG.dom.get(el)) || el.nodeType != 1) {
            return false;
        }
        var tmp, bRtn = true, w3cMode = (tmp = document.defaultView) && tmp.getComputedStyle, rexclude = /z-?index|font-?weight|opacity|zoom|line-?height/i;
        if (typeof (properties) == 'string') {
            tmp = properties;
            properties = {};
            properties[tmp] = value;
        }
        for (var prop in properties) {
            value = properties[prop];
            if (prop == 'float') {
                prop = w3cMode ? "cssFloat" : "styleFloat";
            } else if (prop == 'opacity') {
                if (!w3cMode) {
                    prop = 'filter';
                    value = value >= 1 ? '' : ('alpha(opacity=' + Math.round(value * 100) + ')');
                }
            } else if (prop == 'backgroundPositionX' || prop == 'backgroundPositionY') {
                tmp = prop.slice(-1) == 'X' ? 'Y' : 'X';
                if (w3cMode) {
                    var v = ZENG.dom.getStyle(el, "backgroundPosition" + tmp);
                    prop = 'backgroundPosition';
                    typeof (value) == 'number' && (value = value + 'px');
                    value = tmp == 'Y' ? (value + " " + (v || "top")) : ((v || 'left') + " " + value);
                }
            }
            if (typeof el.style[prop] != "undefined") {
                el.style[prop] = value + (typeof value === "number" && !rexclude.test(prop) ? 'px' : '');
                bRtn = bRtn && true;
            } else {
                bRtn = bRtn && false;
            }
        }
        return bRtn;
    },getScrollTop: function(doc) {
        var _doc = doc || document;
        return Math.max(_doc.documentElement.scrollTop, _doc.body.scrollTop);
    },getClientHeight: function(doc) {
        var _doc = doc || document;
        return _doc.compatMode == "CSS1Compat" ? _doc.documentElement.clientHeight : _doc.body.clientHeight;
    }
};

ZENG.string = {RegExps: {trim: /^\s+|\s+$/g,ltrim: /^\s+/,rtrim: /\s+$/,nl2br: /\n/g,s2nb: /[\x20]{2}/g,URIencode: /[\x09\x0A\x0D\x20\x21-\x29\x2B\x2C\x2F\x3A-\x3F\x5B-\x5E\x60\x7B-\x7E]/g,escHTML: {re_amp: /&/g,re_lt: /</g,re_gt: />/g,re_apos: /\x27/g,re_quot: /\x22/g},escString: {bsls: /\\/g,sls: /\//g,nl: /\n/g,rt: /\r/g,tab: /\t/g},restXHTML: {re_amp: /&amp;/g,re_lt: /&lt;/g,re_gt: /&gt;/g,re_apos: /&(?:apos|#0?39);/g,re_quot: /&quot;/g},write: /\{(\d{1,2})(?:\:([xodQqb]))?\}/g,isURL: /^(?:ht|f)tp(?:s)?\:\/\/(?:[\w\-\.]+)\.\w+/i,cut: /[\x00-\xFF]/,getRealLen: {r0: /[^\x00-\xFF]/g,r1: /[\x00-\xFF]/g},format: /\{([\d\w\.]+)\}/g},commonReplace: function(s, p, r) {
        return s.replace(p, r);
    },format: function(str) {
        var args = Array.prototype.slice.call(arguments), v;
        str = String(args.shift());
        if (args.length == 1 && typeof (args[0]) == 'object') {
            args = args[0];
        }
        ZENG.string.RegExps.format.lastIndex = 0;
        return str.replace(ZENG.string.RegExps.format, function(m, n) {
            v = ZENG.object.route(args, n);
            return v === undefined ? m : v;
        });
    }};


ZENG.object = {
	routeRE: /([\d\w_]+)/g,
	route: function(obj, path) {
        obj = obj || {};
        path = String(path);
        var r = ZENG.object.routeRE, m;
        r.lastIndex = 0;
        while ((m = r.exec(path)) !== null) {
            obj = obj[m[0]];
            if (obj === undefined || obj === null) {
                break;
            }
        }
        return obj;
    }};



var ua = ZENG.userAgent = {}, agent = navigator.userAgent;
ua.ie = 9 - ((agent.indexOf('Trident/5.0') > -1) ? 0 : 1) - (window.XDomainRequest ? 0 : 1) - (window.XMLHttpRequest ? 0 : 1);



if (typeof (ZENG.msgbox) == 'undefined') {
    ZENG.msgbox = {};
}
ZENG.msgbox._timer = null;
ZENG.msgbox.loadingAnimationPath = ZENG.msgbox.loadingAnimationPath || ("loading.gif");
ZENG.msgbox.show = function(msgHtml, type, timeout, opts) {
    if (typeof (opts) == 'number') {
        opts = {topPosition: opts};
    }
    opts = opts || {};
    var _s = ZENG.msgbox,
	 template = '<span class="zeng_msgbox_layer" style="display:none;z-index:10000;" id="mode_tips_v2"><span class="gtl_ico_{type}"></span>{loadIcon}{msgHtml}<span class="gtl_end"></span></span>', loading = '<img src="' + (opts.customIcon || _s.loadingAnimationPath) + '" alt="" />', typeClass = [0, 0, 0, 0, "succ", "fail", "clear"], mBox, tips;
    _s._loadCss && _s._loadCss(opts.cssPath);
    mBox = ZENG.dom.get("q_Msgbox") || ZENG.dom.createElementIn("div", document.body, false, {className: "zeng_msgbox_layer_wrap"});
    mBox.id = "q_Msgbox";
    mBox.style.display = "";
    mBox.innerHTML = ZENG.string.format(template, {type: typeClass[type] || "hits",msgHtml: msgHtml || "",loadIcon: type == 6 ? loading : ""});
    _s._setPosition(mBox, timeout, opts.topPosition);
};
ZENG.msgbox._setPosition = function(tips, timeout, topPosition) {
    timeout = timeout || 5000;
    var _s = ZENG.msgbox, bt = ZENG.dom.getScrollTop(), ch = ZENG.dom.getClientHeight(), t = Math.floor(ch / 2) - 40;
    ZENG.dom.setStyle(tips, "top", ((document.compatMode == "BackCompat" || ZENG.userAgent.ie < 7) ? bt : 0) + ((typeof (topPosition) == "number") ? topPosition : t) + "px");
    clearTimeout(_s._timer);
    tips.firstChild.style.display = "";
    timeout && (_s._timer = setTimeout(_s.hide, timeout));
};
ZENG.msgbox.hide = function(timeout) {
    var _s = ZENG.msgbox;
    if (timeout) {
        clearTimeout(_s._timer);
        _s._timer = setTimeout(_s._hide, timeout);
    } else {
        _s._hide();
    }
};
ZENG.msgbox._hide = function() {
    var _mBox = ZENG.dom.get("q_Msgbox"), _s = ZENG.msgbox;
    clearTimeout(_s._timer);
    if (_mBox) {
        var _tips = _mBox.firstChild;
        ZENG.dom.setStyle(_mBox, "display", "none");
    }
};

```

**方法1**

看代码逻辑，我们调用时可以指定loading图片地址参数customIcon，如：
```javascript
ZENG.msgbox.show("正在加载数据...", 6, 3000, {customIcon:'/plugins/ZENG-msgbox/src/loading.gif'});
```

**方法2**

在文件引入后，在全局定义loading文件地址：
```javascript
<link type="text/css" href="/plugins/ZENG-msgbox/src/msgbox.css" rel="stylesheet"/>
<script type="text/javascript" src="/plugins/ZENG-msgbox/src/msgbox.js"></script>

<script type="text/javascript">
    ZENG.msgbox.loadingAnimationPath = "/plugins/ZENG-msgbox/src/loading.gif";
</script>
```

**方法3**

最复杂，所以不提倡。

把msgbox.js文件中，loading那一行内容定制一下：
```javascript
// 原内容
loading = '<img src="' + (opts.customIcon || _s.loadingAnimationPath) + '" alt="" />'

// 修改为
loading = '<span class="gtl_ico_loading"></span>'
```

msgbox.css 文件内容：
```css
@charset "utf-8";
/* CSS Document */

.zeng_msgbox_layer,
.zeng_msgbox_layer .gtl_ico_succ,
.zeng_msgbox_layer .gtl_ico_fail,
.zeng_msgbox_layer .gtl_ico_hits,
.zeng_msgbox_layer .gtl_ico_clear,
.zeng_msgbox_layer .gtl_end{display:inline-block;height:54px;line-height:54px;font-weight:bold;font-size:14px;color:#606060;background-image:url("gb_tip_layer.png");_background-image:url("gb_tip_layer_ie6.png");background-repeat:no-repeat;}
.zeng_msgbox_layer_wrap{width:100%;position:fixed;_position:absolute;top:46%;left:0;text-align:center;z-index:65533;}
.zeng_msgbox_layer{background-position:0 -161px;background-repeat:repeat-x;padding:0 18px 0 9px;margin:0 auto;position:relative;}
.zeng_msgbox_layer .gtl_ico_succ{background-position:-6px 0;left:-45px;top:0;width:45px;position:absolute;}
.zeng_msgbox_layer .gtl_end{background-position:0 0;position:absolute;right:-6px;top:0;width:6px;}
.zeng_msgbox_layer .gtl_ico_fail{background-position:-6px -108px;position:absolute;left:-45px;top:0;width:45px;}
.zeng_msgbox_layer .gtl_ico_hits{background-position:-6px -54px;position:absolute;left:-45px;top:0;width:45px;}
.zeng_msgbox_layer .gtl_ico_clear{background-position:-6px 0;left:-5px;width:5px;position:absolute;top:0;}
.zeng_msgbox_layer img{float:left;margin:19px 10px 0 5px ;}
```

下面增加一行：
```css
.zeng_msgbox_layer .gtl_ico_loading{ width:16px;height:16px;border:0;background-image:url('loading.gif');float:left;margin:19px 10px 0 5px}
```

##### 背景色定制

默认背景色是白色的：
```css
.zeng_msgbox_layer .gtl_end{display:inline-block;height:54px;line-height:54px;font-weight:bold;font-size:14px;color:#606060;background-image:url("gb_tip_layer.png");_background-image:url("gb_tip_layer_ie6.png");background-repeat:no-repeat;}
```

我们可以改为黑色：
```css
.zeng_msgbox_layer .gtl_end{display:inline-block;height:55px;line-height:55px;font-weight:bold;font-size:14px;color:#fff;background: #333;background-image:url("gb_tip_layer.png");_background-image:url("gb_tip_layer_ie6.png");background-repeat:no-repeat;}
```

记得把`gb_tip_layer_ie6.png` 、 `gb_tip_layer.png` 这两张矢量图从黑色背景文件夹移动到这里。

##### 默认显示时长定制

现在默认显示的时间是5s，我们也可以扩大为50s，修改js文件内容:
```javascript
// 原内容
timeout = timeout || 5000;

// 修改为
timeout = timeout || 50000;
```

<br/><br/><br/><br/><br/>
### 参考资料 

ZENG-msgbox <https://github.com/scepterscythe/ZENG-msgbox>

腾讯UED 漂亮的提示信息 <https://www.cnblogs.com/zengxiangzhan/archive/2011/09/12/2173939.html>

