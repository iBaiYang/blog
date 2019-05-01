---
layout: post
categories: HTML
title: 客户端WebSocket实现
meta: WebSocket可以用来实现即时聊天功能，下面讲一下客户端的实现方式。
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

<br/><br/><br/><br/><br/>
### 参考资料 

HTML5 WebSocket <http://www.runoob.com/html/html5-websocket.html>

The WebSocket API (Windows) <https://technet.microsoft.com/en-us/windows/hh673567(v=vs.60)>

Window 对象 <http://www.runoob.com/jsref/obj-window.html>

WebSocket-技术探究 <https://ibaiyang.github.io/ibaiyang.github.io/html/2018/05/25/WebSocket-技术探究.html>

<https://www.cnblogs.com/yingge/p/5998606.html>

<https://www.cnblogs.com/jiahuafu/p/4599959.html>

Swoole学习 <https://ibaiyang.github.io/ibaiyang.github.io/php/2018/05/26/PHP-Swoole%E5%AD%A6%E4%B9%A0.html>

PHP Stream实现服务器客户端 <https://ibaiyang.github.io/ibaiyang.github.io/php/2018/06/04/PHP-Stream%E5%AE%9E%E7%8E%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%A2%E6%88%B7%E7%AB%AF.html>

WebSocket 前世今生 <https://www.ibm.com/developerworks/cn/java/j-lo-WebSocket/>

<https://www.cnblogs.com/davidwang456/p/4786636.html>

细说WebSocket - Node篇 <https://www.cnblogs.com/hustskyking/p/websocket-with-node.html>

看完让你彻底搞懂Websocket原理 <https://blog.csdn.net/frank_good/article/details/50856585> 

