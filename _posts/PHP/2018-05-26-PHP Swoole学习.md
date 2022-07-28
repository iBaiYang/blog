---
layout: post
categories: PHP
title: Swoole学习
meta: 想用php实现一个聊天功能，怎么做呢？js定时每秒获取一次信息？其实用php拓展——Swoole可以实现。
---
* content
{:toc}

## 引言

Swoole 是一个使用 C++ 语言编写的基于异步事件驱动和协程的并行网络通信引擎，为 PHP 提供协程、高性能网络编程支持。
提供了多种通信协议的网络服务器和客户端模块，
可以方便快速的实现 **TCP/UDP服务**、**高性能Web**、**WebSocket服务**、**物联网**、**实时通讯**、**游戏**、**微服务**等，
使 PHP 不再局限于传统的 **Web 领域**。

那为什么要使用 Swoole？有以下几点：
* 常驻内存，避免重复加载带来的性能损耗，提升海量性能
* 协程异步，提高对 I/O 密集型场景并发处理能力（如：微信开发、支付、登录等）
* 方便地开发 Http、WebSocket、TCP、UDP 等应用，可以与硬件通信

### Swoole 使 PHP 高性能微服务架构成为现实

**常驻内存**

目前传统 PHP框架，在处理每个请求之前，都要做一遍加载框架文件、配置的操作。
这可能已经成为性能问题的一大原因，而使用 Swoole 则没有这个问题，一次加载多次使用。

**协程**

说到协程，就得先简单说说进程和线程，众所周知进程是很占用资源的，为了处理请求大量创建进程肯定是得不偿失的。
而多线程应用就比较多了，在 CPU 层面有几个核心就会执行几个任务，线程一旦创建的多了，就会有线程调度的损耗。

协程是在单线程基础上实现的，它可以最大限度利用 CPU 资源，而不会在等待 I/O 时白白浪费。

当然，协程数越多占用的内存也就越多，不过这个是可以接受的，相比进程和线程，占用的资源是相对较少的。

使用协程时，遇到读写文件、请求接口等场景，会自动挂起协程，把 CPU 让给其它协程执行任务，
这样可以提升单线程的 CPU 资源利用率，减少浪费，从而提高性能。

协程代码示例：
```
<?php
use Swoole\Coroutine as co;

// 协程
$time = microtime(true);
// 创建10个协程
for ($i = 1; $i < 10; $i++) {
    go(function() use ($i){
        co.sleep(1.0);  // 模拟请求接口、读写文件等I/O
        echo $i, PHP_EOL;
    });
}
seoole_event_wait();
echo 'co time:', microtime(true) - $time, ' s', PHP_EOL;

// 同步
$time = microtime(true);
for ($i = 1; $i < 10; $i++) {
    sleep(1);  // 模拟请求接口、读写文件等I/O
    echo $i, PHP_EOL;
}
echo 'sync time:', microtime(true) - $time, ' s', PHP_EOL;
```

## 正文

### Swoole 进程模型

![]({{site.baseurl}}/images/20200330/20200330191533.jpeg)

### Swoole 类图

![]({{site.baseurl}}/images/20210316/20210316110034.png)

### 基于swoole的echo服务器

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

### Swoole的异步任务Task

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

### 服务模式

![]({{site.baseurl}}/images/20190816/20190816115205.png)

有多个应用，每个应用有多种角色，每种角色有多个用户，用户可以连接自己需要的服务端，
跨服务端通信通过代理服务实现。

![]({{site.baseurl}}/images/20190123/20190123150130.jpg)

其实这个就是负载均衡。

### 实例分析

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

swoole主进程监听message消息事件，在收到的信息中把连接swoole服务器的客户端标示fd也补充到信息中：
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

swoole主进程异步调用Task进程，Task进程初步判断（如果发生错误，就把错误信息发送到数据包的consumer_fd标示的客户端），
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

Task进程根据数据包中的url运行相应处理逻辑器并把数据包也发过去。处理逻辑器整理真正要推送给客服的数据：
```
[
    "event" => "WorksheetMsg",
    "response" => [
        "type" => "new_worksheet_to_deal",
        "infos" => "你有一条新工单待处理"
    ] 
]
```

然后处理逻辑器通过数据包中的客服唯一标示uuid从数据库的在线客服表中获取客服连接信息，
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

proxy代理服务器主进程监听receive消息事件，调用Task进程，通过数据包的proxy_info知道了客服连接的swoole服务器ip和进程端口，
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

另一台swoole主进程监听message消息事件，在收到的信息中把连接swoole服务器的客户端标示fd也补充到信息中，
这里的过程和消费者连接swoole服务器有点像了，把连接swoole服务器的客户端标示fd也补充到信息中：
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

Task进程根据数据包中的url运行相应处理逻辑器并把数据包也发过去。处理逻辑器整理出真正要推送给客服的数据：
```
[
    "event" => "WorksheetMsg",
    "response" => [
        "type" => "new_worksheet_to_deal",
        "infos" => "你有一条新工单待处理"
    ] 
]
```

然后处理逻辑器通过数据包中的client_id获取到客服连接swoole的连接标示fd，再通过server对象的send()方法把数据包直接推送给客服。
客服可能是通过浏览器的websocket连接的swoole服务器。

大致流程就是这样，理解下。

### Hyperf

[Hyperf](https://hyperf.wiki/2.1/#/) 是一个Swoole框架，学习它可以更好的理解Swoole及使用。

php高性能框架Hyperf 视频学习地址 [https://www.bilibili.com/video/BV1WZ4y1x7Hw](https://www.bilibili.com/video/BV1WZ4y1x7Hw)



## 拓展

### 加速php

**PHP-CGI 的黑历史**

对于 PHP 处理网络请求，大家基本上也都是再用 CGI 的方式来做的。那么，什么是 CGI 呢。

**CGI**

CGI，全称 CommonGatewayInterface，中文称作“公共网关接口”。也许有很多人认为 CGI 是一个程序，
没错，曾经的我也是这么认为的。直到我从《图解HTTP》开始细细地研究 HTTP协议之后，我才知道，原来 CGI 是一种协议。
任何编程语言，都可以实现 CGI，所以任何语言都可以作为网站的后台语言（扯远了）。

**PHP-CGI**

上面说了， CGI 是一个协议，所以， PHP 有自己对 CGI 的实现，那就是 PHP-CGI。可是呢，随着技术的发展，
人们开始意识到， PHP-CGI 的性能不是那么尽如人意。我们知道， PHP 在运行的时候，是依赖配置文件 php.ini的。
所以，每当 PHP-CGI 开始工作的时候，它是完完全全的一个新进程，它需要重新加载配置文件并初始化，
这就造成了很大的资源和时间的浪费。

**FastCGI**

那么，怎么才能避免这种浪费呢，聪明的程序员们想出了另外一种方法：我们为什么不预先加载好配置，
然后，每一个执行的任务只需要复制当前的进程，不就能避免上面的浪费了么。于是， FastCGI 便横空出世。

FastCGI，全称 FastCommonGatewayInterface，中文译作“快速公共网管接口”。没错，这又是个协议。
当然，这个协议并不是因为 PHP 才有的。

**Apache (httpd)**

几乎所有的 Web 容器都实现了 FastCGI 的功能。首先是 httpd。对于 PHP 来说， httpd 是通过自身来实现一个 FastCGI 的模块的。
它会预先加载好 php.ini 文件中的配置。待到有请求进入需要 PHP 处理时， PHP 就不需要再对 php.ini 重新加载了。
这也就是每改动过 php.ini 后都要重启 httpd 服务的原因。

**Nginx 与 php-fpm**

php-fpm 也是 FastCGI 的一种实现。通常我们是将 Nginx 的 PHP 处理部分代理到 php-fpm 的端口上，交给 php-fpm 来处理。
而 php-fpm 同样是通过预先加载配置，然后给到子进程的方式的，它会对进程做一些管理。

Nginx和PHP之间是如何协同工作的？

Nginx是一个高性能的 HTTP 和 反向代理 服务器。也是一个IMAP/POP3/SMTP代理服务器。
因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。

当nginx接收到一个http请求时，通过配置文件找到对应的server。然后匹配server中的所有location，找到最匹配的。

而在location中的命令会启动不同的模块去完成工作，比如rewrite模块、index模块。因此在nginx中模块可以看作真正的劳动工作者。

nginx的模块是被编译到nginx中的，属于静态方式。启动nginx时，模块被自动加载。不像apache，把模块单独编译成so文件，
在配置文件中指定是否加载。所以，单比模块加载方面，nginx也比apache速度上有提升。

那nginx是怎么调用php的呢？先看下面的nginx中关于php的配置
```
location ~ \.php$ {
    root /webpath;
    
    fastcgi_pass 127.0.0.1:9000;
    
    …
}
```

这个location指令把以php为文件后缀的请求，交给`127.0.0.1:9000`处理。我想你看到这个应该猜到了，这是一个C/S架构东西。

而这里的IP地址和端口(`127.0.0.1:9000`)就是fastcgi进程监听的IP地址和端口。
fastcgi是一个可伸缩地、高速地在http server和动态脚本语言间通信的接口。

多数流行的http server都支持fastcgi，包括apache、nginx和lighttpd等。同时，fastcgi也被许多脚本语言支持，其中就有php。

那这个fastcgi的配置IP和端口从何而来呢？在php-fpm.conf中可以看到如下：
```
listen = 127.0.0.1:9000  #这个表示php的fastcgi进程监听的ip地址以及端口
pm.start_servers = 2
```

php-fpm作为fastcgi的进程管理器，可以有效控制内存和进程，并且平滑重载php配置。
php5.3以后，php-fpm被集成到php的core中，默认安装，无需配置。

fastcgi进程管理器php-fpm自身初始化，启动主进程php-fpm和启动start_servers个fastcgi子进程。
主进程php-fpm主要是管理fastcgi子进程，监听9000端口，fastcgi子进程等待请求。

当客户端请求到达nginx时，nginx通过location指令，将所有以php为后缀的文件都交给 127.0.0.1:9000 来处理。
php-fpm选择并连接到一个fastcgi子进程，并将环境变量和标准输入发送到fastcgi子进程。

fastcgi子进程完成处理后将标准输出和错误信息返回。当fastcgi子进程关闭连接时，请求便告处理完成，等待下次处理。

普通的cgi模式是进程，但存在一个问题，每来一个请求，就必须fork一个进程，导致效率缓慢。

所以才有了 fast cgi fpm 就是 fast cgi manager

fpm的模式也是进程，但他会prefork 就是服务启动的时候，先fork一部分进程等待处理请求。

**Swoole**

那么问题来了， php-fpm 虽然实现了 FastCGI，但是，它在处理请求的时候，依然要重新运行一个脚本，像 Laravel 一样的框架，
一开始就要加载那么多依赖和文件，依然是一个不小的开销。我们看一下 Laravel 的 `public/index.php` 的源码。
```
require __DIR__.'/../bootstrap/autoload.php';

$app = require_once __DIR__.'/../bootstrap/app.php';
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

$response->send();
$kernel->terminate($request, $response);
```

看看前面两条语句，这需要加载多少个依赖啊，这都是大把大把的时间和资源啊，每一次请求都需要加载一边，真是心疼啊。

那么，我们为什么不能像之前一样，能够不重新加载配置文件的 FastCGI ，来一个不用加载这么多的依赖的方式呢？

当然可以啦，这时候 Swoole 就派上用场了。既然是通过 $app->make 的方式来生成一个新的 Kernel 对象，
那么 Application 的对象 $app 自然是不会有什么改变的了。所以，我们可以在收到请求之前，就把 $app 给生成好，
这样就会快了，不是么？我们可以对它进行一个简单的改造。
```
require __DIR__.'/../bootstrap/autoload.php';

$app = require_once __DIR__.'/../bootstrap/app.php';
$serv = new \Swoole\Server\Http('127.0.0.1', 9501);
$serv->on('request', function ($req, $res) use ($app) {
    $kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
    $response = $kernel->handle(
        $request = Illuminate\Http\Request::capture()
    );
    $res->end($response);
    $kernel->terminate($request, $response);
});

$serv->start();
```

好了，我们现在就可以通过执行这个脚本来监听9501端口了。然后就像 Nginx 配置 php-fpm 一样来配置它就可以了。
这样我们可以看到，在收到请求之前，就已经把依赖加载干净了，剩下的就是处理请求了。

当然我的这个改动很简陋，根本无法用于生产环境的，只是提供一个例子。

### 实现web版的shell客户端

参阅文章 <https://mp.weixin.qq.com/s/tvhEOWr4Wi8ejNsJiSYG8Q>

### 静态化API

参阅文章 <https://mp.weixin.qq.com/s/ycGkvfYjcJhKiQC_9OYcPQ>

什么是静态化API？

静态化API可以理解成把一些接口的数据存储在服务器本地。常用的是存成json文件，也可以是放在swoole的table中，
总之是用户不从数据库直接读取数据，而是从本地加载的方式来大幅提高性能，因为很多系统的性能瓶颈是在数据库的位置。

解决方案
* 方案1 easySwoole + crontab
* 方案2 easySwoole定时器
* 方案3 Swoole table
* 方案4 Redis


<br/><br/><br/><br/><br/>
## 参考资料

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

Hyperf框架学习 <https://hyperf.wiki/2.1/#/>

为什么 Phper 要使用 Swoole？ <https://mp.weixin.qq.com/s/JyRGyN0r1DsUvh99d760fQ>

为什么Swoole可以加速php <https://mp.weixin.qq.com/s/I4Uiadg-LJK4JEhh_6jd1A>

Nginx和PHP之间是如何协同工作的？ <https://mp.weixin.qq.com/s/REMD9WEaBxv3NIwf7sVIPw>
