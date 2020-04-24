---
layout: post
categories: HTML
title: WebSocket 技术探究
meta: WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
---

* content
{:toc}

### 正文

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

看一下方法及事件示例：

```
if ("WebSocket" in window) {
    // 打开一个 web socket
    var ws = new WebSocket("ws://localhost:9605");

    // 通信发生错误
    ws.onerror = function() {
        alert("WebSocket连接发生错误");
    }

    // 成功打开连接通路
    ws.onopen = function() {
        alert("WebSocket连接成功");

        ws.send("发送数据");  // 向服务器端发送数据
    }

    // 收到服务端数据
    ws.onmessage = function(event) {
        alert("收到服务端数据");

        var received_msg = evt.data;  // 收到的服务端数据
    }

    // 连接关闭时触发
    ws.onclose = function() {
        alert("连接已关闭..."); 
    }

    // 监听窗口关闭事件，当窗口关闭时，主动关闭websocket连接，防止连接还没断开就关闭窗口server端抛异常
    window.onbeforeunload = function () {
        ws.close();
    }

    // 发送心跳包，一分钟一次
    function heartBeat(){
        ws.send('{"url":"/chat/heart-beat"}');
    }
    setInterval(heartBeat, 60000);
} else {
    alert("您的浏览器不支持 WebSocket!");
}
```

这里基本把整个过程及处理都举出来了，多操作会有更深一层理解。

#### 原生举例

js 原生举例：

```
<html>
    <head>
    <meta charset="utf-8">
    <title>菜鸟教程(runoob.com)</title>
    <script type="text/javascript">
        function WebSocketTest()
        {
            if ("WebSocket" in window) {
                // 打开一个 web socket
                var ws = new WebSocket("ws://192.168.0.23:9605");
            
                // 通信发生错误
                ws.onerror = function() {
                    alert("WebSocket连接发生错误");
                }
            
                // 成功打开连接通路
                ws.onopen = function() {
                    // Web Socket 已连接上，进行相应处理，如使用 send() 方法向服务器端发送数据
                    ws.send("发送数据");
                }
            
                // 收到服务端数据
                ws.onmessage = function(event) {           
                    var received_msg = event.data;  // 收到的服务端数据
                }
            
                // 连接关闭时触发
                ws.onclose = function() {
                    // 关闭 websocket 可以是双向的，客户端可以关闭，服务端也可以关闭
                    alert("连接已关闭..."); 
                }
            
                // 监听窗口关闭事件，当窗口关闭时，主动关闭websocket连接，防止连接还没断开就关闭窗口server端抛异常
                window.onbeforeunload = function () {
                    ws.close();
                }
            
                // 发送心跳包，一分钟一次
                function heartBeat(){
                    ws.send('{"url":"/chat/heart-beat"}');
                }
                setInterval(heartBeat, 60000);
            } else {
                alert("您的浏览器不支持 WebSocket!");
            }
        }
    </script>
    </head>
    <body>
        <div id="sse">
            <a href="javascript:WebSocketTest()">运行 WebSocket</a>
        </div>
    </body>
</html>
```

#### 实例

下面举一个实际使用的例子：

```
if ( 'WebSocket' in window ) {
    websocket = new WebSocket("ws://localhost:9998/echo");
} else {
    alert('当前浏览器不支持websocket');
}

// 连接发生错误的回调方法
websocket.onerror = function () {
    sweetAlert("WebSocket连接发生错误", "","error");
};

// 连接成功的回调方法
websocket.onopen = function () {
    sweetAlert("WebSocket连接成功", "","success");
}

// 接收到消息的回调方法
websocket.onmessage = function (event) {
    var data = JSON.parse( event.data );
    switch ( data.event ) 
    {
        case 'CONNECT':
            var clientId = data.response.client_id;
            $.ajax({
                type: 'GET',
                url: '/socket/chat-uuid',
                data: '',
                dataType: 'json',
                success: function( rs ) {
                    if ( rs.data.uuid ) {
                        var conMessage = '{"url":"/chat/online","chat_token":"abc","uuid":"'+ rs.data.uuid +'"}';
                        websocket.send(conMessage);
                    }
                }
            });
            break;
        case 'ONLINE':
            $.ajax({
                type: 'GET',
                url: '/socket/get-msg',
                data: '',
                dataType: 'json',
                success: function(rs) {
                    // 针对返回的操作
                }
            });
            break;
        case 'MSG':
            // 一些针对MSG的操作
            break;
        default:
            break;
    }
}

// 连接关闭的回调方法
websocket.onclose = function () {
    sweetAlert("WebSocket连接关闭", "","error");
}

// 监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
window.onbeforeunload = function () {
    closeWebSocket();
}

// 关闭WebSocket连接
function closeWebSocket() {
    websocket.close();
}

// 心跳包
function heartBeat() {
    websocket.send('{"url":"/chat/heart-beat","chat_token":"abc"}');
}
setInterval( heartBeat, 60000 );
```

上面用到了SweetAlert。

<br/><br/><br/><br/><br/>
### 参考资料

HTML5 WebSocket <http://www.runoob.com/html/html5-websocket.html>

The WebSocket Protocol <https://tools.ietf.org/html/rfc6455>

<http://www.runoob.com/html/html5-websocket.html>

<http://www.ruanyifeng.com/blog/2017/05/websocket.html>

<http://mishengqiang.com/sweetalert/>

WebSocket 协议 1~4 节 <https://www.jianshu.com/p/867274a5e054>

WebSocket 协议 5~10 节 <https://www.jianshu.com/p/fc09b0899141>

The WebSocket API (Windows) <https://technet.microsoft.com/en-us/windows/hh673567(v=vs.60)>

Window 对象 <http://www.runoob.com/jsref/obj-window.html>

WebSocket-技术探究 <https://ibaiyang.github.io/blog/html/2018/05/25/WebSocket-技术探究.html>

<https://www.cnblogs.com/yingge/p/5998606.html>

<https://www.cnblogs.com/jiahuafu/p/4599959.html>

Swoole学习 <https://ibaiyang.github.io/blog/php/2018/05/26/PHP-Swoole%E5%AD%A6%E4%B9%A0.html>

PHP Stream实现服务器客户端 <https://ibaiyang.github.io/blog/php/2018/06/04/PHP-Stream%E5%AE%9E%E7%8E%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%A2%E6%88%B7%E7%AB%AF.html>

WebSocket 前世今生 <https://www.ibm.com/developerworks/cn/java/j-lo-WebSocket/>

<https://www.cnblogs.com/davidwang456/p/4786636.html>

细说WebSocket - Node篇 <https://www.cnblogs.com/hustskyking/p/websocket-with-node.html>

看完让你彻底搞懂Websocket原理 <https://blog.csdn.net/frank_good/article/details/50856585> 




