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

![]({{site.baseurl}}/images/20200329/20200329191532.png)

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

![](http://s4.sinaimg.cn/mw690/001XbchKzy7kRRvtSfx43)

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

![](http://s6.sinaimg.cn/orignal/001XbchKzy7kRRwb6Tzd5)


#### 服务模式

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190816/20190816115205.png)

有多个应用，每个应用有多种角色，每种角色有多个用户，用户可以连接自己需要的服务端，
跨服务端通信通过代理服务实现。

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190123/20190123150130.jpg)

<br/><br/><br/><br/><br/>
### 参考资料

<https://www.swoole.com/>

<https://wiki.swoole.com/>

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

客户端WebSocket实现 <https://ibaiyang.github.io/blog/html/2019/01/04/%E5%AE%A2%E6%88%B7%E7%AB%AFWebSocket%E5%AE%9E%E7%8E%B0.html>
