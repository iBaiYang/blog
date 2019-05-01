---
layout: post
categories: JavaScript
title: JavaScript 前端根据服务器环境修改参数
meta: 前端有时我们要根据所处环境改变请求的地址，如测试环境请求测试地址，正式环境请求正式地址，怎么解决呢。
---

* content
{:toc}

### 正文

前端有时我们要根据所处环境改变请求的地址，如测试环境请求测试地址，正式环境请求正式地址，怎么解决呢。

用原生js代码就能解决，看示例：

```
var hostName = window.location.host,
    wsUrl = '',
    chat_token = '';
if ( hostName.indexOf('jd.dev.ali.com') == 0 ) {
    wsUrl = "ws://192.168.10.10:9505";
    chat_token = 'abc';
} else if ( hostName.indexOf('d.pre.ali.com') == 0 ) {
    wsUrl = "ws://192.168.20.20:9505";
    chat_token = 'efg';
} else {
    wsUrl = "ws://192.168.30.30:9605";
    chat_token = 'hijklmn';
}

websocket = new WebSocket( wsUrl );
```

另外一种方法，就是定义全局js变量，实现方式是新建一个config.js的文件，里面写入变量，如：

```
var wsUrl = "ws://192.168.10.10:9505";
var chat_token = 'abc';
（整个文件只有这几行就够了）
```

js文件加载时，把config.js也一同加载，不过顺序要放在前面，然后其他js文件中就可以使用这个全局变量了。

<br/><br/><br/><br/><br/>
### 参考资料
