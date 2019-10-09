---
layout: post
categories: HTML
title: WebSocket 技术探究
meta: WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
---

* content
{:toc}

### 正文

WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

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
                var ws = new WebSocket("ws://localhost:9605");
            
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
                    var received_msg = evt.data;  // 收到的服务端数据
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

The WebSocket Protocol <https://tools.ietf.org/html/rfc6455>

<http://www.runoob.com/html/html5-websocket.html>

<http://www.ruanyifeng.com/blog/2017/05/websocket.html>

<http://mishengqiang.com/sweetalert/>

WebSocket 协议 1~4 节 <https://www.jianshu.com/p/867274a5e054>

WebSocket 协议 5~10 节 <https://www.jianshu.com/p/fc09b0899141>





