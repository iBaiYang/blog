---
layout: post
categories: PHP
title: Swoole学习
meta: 想用php实现一个聊天功能，怎么做呢？js定时每秒获取一次信息？其实用php拓展——Swoole可以实现。
---
* content
{:toc}

### 正文

#### swoole进程模型

![]({{site.baseurl}}/images/20200330/20200330191533.jpeg)

#### 基于swoole的echo服务器

先举一个 基于swoole的echo服务器 示例：

server.php 文件内容：

```
<?php
// Server
class Server
{
    private $serv;

    public function __construct() {
        $this->serv = new swoole_server("0.0.0.0", 9501);
        $this->serv->set(array(
            'worker_num' => 8,
            'daemonize' => false,
            'max_request' => 10000,
            'dispatch_mode' => 2,
            'debug_mode'=> 1
        ));

        $this->serv->on('Start', array($this, 'onStart'));
        $this->serv->on('Connect', array($this, 'onConnect'));
        $this->serv->on('Receive', array($this, 'onReceive'));
        $this->serv->on('Close', array($this, 'onClose'));

        $this->serv->start();
    }

    public function onStart( $serv ) {
        echo "Start\n";
    }

    public function onConnect( $serv, $fd, $from_id ) {
        $serv->send( $fd, "Hello fd:{$fd}! from_id:{$from_id}" );
    }

    public function onReceive( swoole_server $serv, $fd, $from_id, $data ) {
        echo "Get Message From Client fd:{$fd} from_id:{$from_id}  data:{$data}\n";
    }

    public function onClose( $serv, $fd, $from_id ) {
        echo "Client fd:{$fd} from_id:{$from_id} close connection\n";
    }
}

// 启动服务器
$server = new Server();
```

set()配置参数有些多，具体参看这里：<https://wiki.swoole.com/#/server/setting>

client.php 文件内容：

```
<?php
class Client
{
	private $client;

	public function __construct() {
		$this->client = new swoole_client(SWOOLE_SOCK_TCP);
	}
	
	public function connect() {
		if( !$this->client->connect("127.0.0.1", 9501 , 1) ) {
			echo "Error: {$fp->errMsg}[{$fp->errCode}]\n";
		}
		$message = $this->client->recv();
		echo "Get Message From Server:{$message}\n";

		fwrite(STDOUT, "请输入消息：");  
		$msg = trim(fgets(STDIN));
		$this->client->send( $msg );
	}
}

$client = new Client();
$client->connect();
```

调试看一下：

![]({{site.baseurl}}/images/20200330/20200330191534.jpeg)

#### Swoole的异步任务Task

swoole_task_server.php 文件内容：

```
<?php
class Server
{
    private $serv;
    public function __construct() {
        $this->serv = new swoole_server("0.0.0.0", 9501);
        $this->serv->set(array(
            'worker_num' => 8,
            'daemonize' => false,
            'max_request' => 10000,
            'dispatch_mode' => 2,
            'debug_mode'=> 1,
            'task_worker_num' => 8
        ));
        $this->serv->on('Start', array($this, 'onStart'));
        $this->serv->on('Connect', array($this, 'onConnect'));
        $this->serv->on('Receive', array($this, 'onReceive'));
        $this->serv->on('Close', array($this, 'onClose'));
        // bind callback
        $this->serv->on('Task', array($this, 'onTask'));
        $this->serv->on('Finish', array($this, 'onFinish'));
        $this->serv->start();
    }

    public function onStart( $serv ) {
        echo "Start\n";
    }

    public function onConnect( $serv, $fd, $from_id ) {
        echo "Client {$fd} connect\n";
    }

    public function onReceive( swoole_server $serv, $fd, $from_id, $data ) {
        echo "Get Message From Client {$fd}:{$data}\n";
        // send a task to task worker.
        $param = array(
            'fd' => $fd
        );
        $serv->task( json_encode( $param ) );
        echo "Continue Handle Worker\n";
    }

    public function onClose( $serv, $fd, $from_id ) {
        echo "Client {$fd} close connection\n";
    }

    public function onTask($serv, $task_id, $from_id, $data) {
        echo "This Task {$task_id} from Worker {$from_id}\n";
        echo "Data: {$data}\n";
        for($i = 0 ; $i < 3 ; $i ++ ) {
            sleep(1);
            echo "Taks {$task_id} Handle {$i} times...\n";
        }
        $fd = json_decode( $data , true )['fd'];
        $serv->send( $fd , "Data in Task {$task_id}");
        return "Task {$task_id}'s result";
    }

    public function onFinish($serv, $task_id, $data) {
        echo "Task {$task_id} finish\n";
        echo "Result: {$data}\n";
    }
}

$server = new Server();
```

swoole_async_client.php 文件内容：

```
<?php
class Client
{
    private $client;
    
    public function __construct() {
        $this->client = new swoole_client(SWOOLE_SOCK_TCP, SWOOLE_SOCK_ASYNC);
        $this->client->on('Connect', array($this, 'onConnect'));
        $this->client->on('Receive', array($this, 'onReceive'));
        $this->client->on('Close', array($this, 'onClose'));
        $this->client->on('Error', array($this, 'onError'));
    }
    
    public function connect() {
        $fp = $this->client->connect("127.0.0.1", 9501 , 1);
        if( !$fp ) {
            echo "Error: {$fp->errMsg}[{$fp->errCode}]\n";
            return;
        }
    }
    
    public function onReceive( $cli, $data ) {
        echo "Get Message From Server: {$data}\n";
    }
    
    public function onConnect( $cli) {
        fwrite(STDOUT, "Enter Msg:");
        swoole_event_add(STDIN, function($fp){
            global $cli;
            fwrite(STDOUT, "Enter Msg:");
            $msg = trim(fgets(STDIN));
            $cli->send( $msg );
        });
    }
    
    public function onClose( $cli) {
        echo "Client close connection\n";
    }
    
    public function onError() {
    }
    
    public function send($data) {
        $this->client->send( $data );
    }
    
    public function isConnected() {
        return $this->client->isConnected();
    }
}

$cli = new Client();
$cli->connect();
```

调试看一下：

![]({{site.baseurl}}/images/20200330/20200330191535.jpeg)

#### 服务模式

![]({{site.baseurl}}/images/20190816/20190816115205.png)

有多个应用，每个应用有多种角色，每种角色有多个用户，用户可以连接自己需要的服务端，
跨服务端通信通过代理服务实现。

![]({{site.baseurl}}/images/20190123/20190123150130.jpg)

其实这个就是负载均衡。

#### 实例分析

看一个实例：工单服务器 推送数据到 队列服务器，队列消费者 通过stream_socket_client连接把信息推送到 swoole服务器，
swoole主进程 通过监听message消息事件异步调用 Task进程，Task进程 再把信息推送到 proxy代理服务器，
proxy代理服务器 通过判断把信息推送到 另一台客户端连接的 相应swoole服务器， swoole服务器 最后把信息推送到 客户端。

看起来过程比较复杂，我们追踪数据结构看一下。

项目代码可以参考这个： <https://github.com/peachpear/pear-swoole>

工单服务器生成一条提示客户的信息：
```
$msg = [
    "url" => "/worksheet/send-message",
    "chat_token" => "sdfd3sdfsd3324cx",
    "data" => [
        [
            “uuid” => "badf677b-23df-2a5b-23ds-81d18g23ec69",    // 客服的唯一标示
            "data" => [
                "type" => "new_worksheet_to_deal",
                "infos" => "你有一条新工单待处理"
            ] 
        ]
    ]
];
```

然后把这条信息推送到队列，队列消费者连接swoole服务器，再把这条信息推送过去。

swoole主进程监听message消息事件，在收到的信息中把连接swoole服务器的客户端标示fd也补充道信息中：
```
$msg = [
    "url" => "/worksheet/send-message",
    "chat_token" => "sdfd3sdfsd3324cx",
    "data" => [
        [
            “uuid” => "badf677b-23df-2a5b-23ds-81d18g23ec69",
            "data" => [
                "type" => "new_worksheet_to_deal",
                "infos" => "你有一条新工单待处理"
            ] 
        ]
    ],
    "consumer_fd" => 2093
];
```

swoole主进程异步调用Task进程，Task进程初步判断（如果发生错误，就把错误信息发送到数据包的"consumer_fd"标示的客户端），
把调用它的swoole主进程的相关信息整理到数据包中（这里同时可以把消费者连接swoole服务器的日志记录下来）：
```
$msg = [
    "server" => {***},   // swoole主进程信息，
    "data" => [
        "port" => "9605",    // swoole主进程服务端口
        "url" => "/worksheet/send-message",
        "chat_token" => "sdfd3sdfsd3324cx",
        "data" => [
            [
                “uuid” => "badf677b-23df-2a5b-23ds-81d18g23ec69",
                "data" => [
                    "type" => "new_worksheet_to_deal",
                    "infos" => "你有一条新工单待处理"
                ] 
            ]
        ],
        "consumer_fd" => 2093,
    ]
];
```

Task进程根据数据包中的"url"运行相应处理逻辑器并把数据包也发过去。处理逻辑器整理真正要推送给客服的数据：
```
[
    "event" => "WorksheetMsg",
    "response" => [
        "type" => "new_worksheet_to_deal",
        "infos" => "你有一条新工单待处理"
    ] 
]
```

然后处理逻辑器通过数据包中的客服唯一标示"uuid"从数据库的在线客服表中获取客服连接信息，
其实想获取的就是这个客服连接在哪一个swoole服务器上及连接标示：swoole服务器IP_swoole服务器PORT_连接标示fd，
对比运行Task进程的swoole服务器进程与客服连接的swoole服务器进程是不是一个，就是对比 服务器IP 和 进程端口 相不相等。
如果相等，说明客服就连接在这台服务器的swoole主进程上，直接把整理好的数据包通过swoole连接推送给客服。

如果不相等，说明客服连接的不是当前swoole服务，通过当前swoole主进程把信息发送到自己的proxy代理服务器，整理发送的数据包为：
```
[
    "url" => "/chat/push-local",
    "chat_token" => "sdfd3sdfsd3324cx",
    "proxy_info" => [
        "ip" => "127.0.12.2",        // 客服连接的swoole服务器IP
        "port" => 9605               // 客服连接的swoole服务器PORT
    ],
    "client_id" => "",              // swoole服务器IP_swoole服务器PORT_连接标示fd  hash后的字符串
    "remote_data" => [
        "event" => "WorksheetMsg",
        "response" => [
            "type" => "new_worksheet_to_deal",
            "infos" => "你有一条新工单待处理"
        ] 
    ]
]
```

proxy代理服务器主进程监听receive消息事件，调用Task进程，通过数据包的"proxy_info"知道了客服连接的swoole服务器ip和进程端口，
proxy代理服务器主进程通过stream_socket_client直接连接客服连接的swoole服务器进程，然后把数据包发送过去：
```
[
    "url" => "/chat/push-local",
    "chat_token" => "sdfd3sdfsd3324cx",
    "client_id" => "",              // swoole服务器IP_swoole服务器PORT_连接标示fd  hash后的字符串
    "remote_data" => [
        "event" => "WorksheetMsg",
        "response" => [
            "type" => "new_worksheet_to_deal",
            "infos" => "你有一条新工单待处理"
        ] 
    ]
]
```

另一台swoole主进程监听message消息事件，在收到的信息中把连接swoole服务器的客户端标示fd也补充道信息中，
这里的过程和消费者连接swoole服务器有点像了，把连接swoole服务器的客户端标示fd也补充道信息中：
```
[
    "url" => "/chat/push-local",
    "chat_token" => "sdfd3sdfsd3324cx",
    "client_id" => "",              // swoole服务器IP_swoole服务器PORT_连接标示fd  hash后的字符串
    "remote_data" => [
        "event" => "WorksheetMsg",
        "response" => [
            "type" => "new_worksheet_to_deal",
            "infos" => "你有一条新工单待处理"
        ] 
    ],
    "proxy_fd" => 1212          
]
```

swoole主进程异步调用Task进程，Task进程初步判断，把调用它的swoole主进程的相关信息整理到数据包中：
```
[
    "server" => {***},   // swoole主进程信息，
    "data" => [
        "port" => "9605",    // swoole主进程服务端口
        "url" => "/chat/push-local",
        "chat_token" => "sdfd3sdfsd3324cx",
        "client_id" => "",              // swoole服务器IP_swoole服务器PORT_连接标示fd  hash后的字符串
        "remote_data" => [
            "event" => "WorksheetMsg",
            "response" => [
                "type" => "new_worksheet_to_deal",
                "infos" => "你有一条新工单待处理"
            ] 
        ],
        "proxy_fd" => 1212     
    ]
];
```

Task进程根据数据包中的"url"运行相应处理逻辑器并把数据包也发过去。处理逻辑器整理出真正要推送给客服的数据：
```
[
    "event" => "WorksheetMsg",
    "response" => [
        "type" => "new_worksheet_to_deal",
        "infos" => "你有一条新工单待处理"
    ] 
]
```

然后处理逻辑器通过数据包中的"client_id"获取到客服连接swoole的连接标示fd，再通过"server"对象的send()方法把数据包直接推送给客服。
客服可能是通过浏览器的websocket连接的swoole服务器。

大致流程就是这样，理解下。

<br/><br/><br/><br/><br/>
### 参考资料

<https://www.swoole.com/>

<https://wiki.swoole.com/>

<https://wiki.swoole.com/wiki/page/1.html>

<https://legacy.gitbook.com/book/linkeddestiny/easy-swoole/details>

<https://github.com/LinkedDestiny/swoole-concise-guide>

<https://github.com/LinkedDestiny/swoole-doc>

<https://wiki.swoole.com/wiki/page/1.html>

<https://www.w3cschool.cn/swoole/>

<https://segmentfault.com/a/1190000006140097>

<http://www.thinkphp.cn/topic/34678.html>

[利用Swoole实现PHP+websocket 聊天室](https://www.jianshu.com/p/fedbb9d2d999)

[使用PHP+Swoole实现的网页即时聊天工具 ](https://github.com/matyhtf/webim?utm_source=wechat_session&utm_medium=social)

[Tencent Server Framework](Tencent Server Framework)

<https://github.com/scil/LaravelFly>

[当SWOOLE遇上PHP](https://www.jianshu.com/p/dfe94b992a98)

[当SWOOLE遇上SERVER](https://www.jianshu.com/p/b56aefeb08e8)

[当SWOOLE遇上TCP](https://www.jianshu.com/p/83e202c7dc48)

[当SWOOLE遇上PROTOCOL](https://www.jianshu.com/p/19755ad168e1)

[Swoole的进程模型](https://www.jianshu.com/p/1f169849bc18)

基于swoole的task功能实现Map-Reduce <https://www.jianshu.com/p/7ec3f39434f3>

PHP Socket通信 <https://www.jianshu.com/p/36b8f31b41dc>

[阮一峰 WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html?utm_source=tuicool&utm_medium=referral)

[韩天峰的一组swoole视频](http://id.tudou.com/i/UMjkwMzQyMDMyMA==?spm=a2h28.8313471.info.2)

<http://www.phperz.com/article/15/0809/147070.html>

<https://www.cnblogs.com/doudouxiaoye/p/5656681.html>

PHP Stream实现服务器客户端 <https://ibaiyang.github.io/blog/php/2018/06/04/PHP-Stream%E5%AE%9E%E7%8E%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%A2%E6%88%B7%E7%AB%AF.html>

WebSocket 技术探究 <https://ibaiyang.github.io/blog/html/2018/05/25/WebSocket-%E6%8A%80%E6%9C%AF%E6%8E%A2%E7%A9%B6.html>

socket，tcp，http三者之间的区别和原理 <https://www.jianshu.com/p/a24ba459e306>
