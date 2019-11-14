---
layout: post
categories: PHP
title: PHP Stream实现服务器客户端
meta: PHP Stream实现服务器客户端
---
* content
{:toc}

### 正文

php中除了用Swoole拓展实现服务器客户端，其实用php原生的Stream也可以实现服务器/客户端。

server.php的代码：

```
//设置不超时
set_time_limit(0);

class SocketServer
{
    public function __construct($port)
    {
        global $errno, $errstr;

        $socket = stream_socket_server('tcp://127.0.0.1:'.$port, $errno, $errstr);
        while($conn = stream_socket_accept($socket, -1))
        {
            $buff = '';
            $data = '';
            file_put_contents('log.txt', 'SERVER-1|| server：conn->' .$conn .';  buff->'  .$buff  .'; data->' .$data  ."\r\n", FILE_APPEND);
            //读取请求数据直到遇到\r\n结束符
            while(!preg_match('#\r\n#', $buff))
            {
                $buff = fread($conn, 1024);
                file_put_contents('log.txt', 'SERVER-2|| buff：' .$buff ."\r\n" , FILE_APPEND);
                $data .= preg_replace('#\r\n#', '', $buff);
                file_put_contents('log.txt', 'SERVER-3|| data：' .$data ."\r\n" , FILE_APPEND);
            }
            file_put_contents('log.txt', 'SERVER-4|| data：' .$data ."\r\n" , FILE_APPEND);
            fwrite($conn, $data);
            file_put_contents('log.txt', 'SERVER-5|| data：' .$data ."\r\n" , FILE_APPEND);
            fclose($conn);
        }
        fclose($socket);
    }
}

new SocketServer(1212);
```

client.php的代码：

```
if ( isset($argv[1]) ) {
    $msg = $argv[1];
    file_put_contents('log.txt', 'CLIENT-1|| msg：' .$msg ."\r\n" , FILE_APPEND);
    $socket = stream_socket_client('tcp://127.0.0.1:1212', $errno, $errstr);
    if (!$socket) {
        die($errno.$errstr);
    } else {
        // stream_set_blocking($socket, 0);
        for($index = 0; $index < 3; $index++)
        {
            fwrite($socket, " client: $msg $index ");
            file_put_contents('log.txt', 'CLIENT-2|| for： msg' .$msg  .'; index：' . $index ."\r\n" , FILE_APPEND);
            usleep(100000);
        }
        fwrite($socket, "\r\n");
        $response = fread($socket, 1024);
        file_put_contents('log.txt', 'CLIENT-3|| client：time->'. date("[H:i:s] ", time()) .'; reponse->' .$response  .'; msg->' .$msg ."\r\n", FILE_APPEND);
        fclose($socket);
    }
} else {
    for($index = 0; $index < 3; $index++)
    {
        file_put_contents('log.txt', 'CLIENT-4|| start：' .$index ."\r\n" , FILE_APPEND);
        system('php '.__FILE__." $index:test");
    }
}
```

看一下实现结果：

服务端运行：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190114/20190114150405.jpg)

客户端运行：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190114/20190114150432.jpg)

缩略图：

![](http://s9.sinaimg.cn/mw690/001XbchKzy7kZxJHsPm78)

```
CLIENT-4|| start：0
CLIENT-1|| msg：0:test
CLIENT-2|| for： msg0:test; index：0
SERVER-1|| server：conn->Resource id #6;  buff->; data->
SERVER-2|| buff： client: 0:test 0 
SERVER-3|| data： client: 0:test 0 
CLIENT-2|| for： msg0:test; index：1
SERVER-2|| buff： client: 0:test 1 
SERVER-3|| data： client: 0:test 0  client: 0:test 1 
SERVER-2|| buff： client: 0:test 2 
CLIENT-2|| for： msg0:test; index：2
SERVER-3|| data： client: 0:test 0  client: 0:test 1  client: 0:test 2 
SERVER-2|| buff：

SERVER-3|| data： client: 0:test 0  client: 0:test 1  client: 0:test 2 
SERVER-4|| data： client: 0:test 0  client: 0:test 1  client: 0:test 2 
SERVER-5|| data： client: 0:test 0  client: 0:test 1  client: 0:test 2 
CLIENT-3|| client：time->[07:01:57] ; reponse-> client: 0:test 0  client: 0:test 1  client: 0:test 2 ; msg->0:test
CLIENT-4|| start：1
CLIENT-1|| msg：1:test
SERVER-1|| server：conn->Resource id #18;  buff->; data->
CLIENT-2|| for： msg1:test; index：0
SERVER-2|| buff： client: 1:test 0 
SERVER-3|| data： client: 1:test 0 
CLIENT-2|| for： msg1:test; index：1
SERVER-2|| buff： client: 1:test 1 
SERVER-3|| data： client: 1:test 0  client: 1:test 1 
CLIENT-2|| for： msg1:test; index：2
SERVER-2|| buff： client: 1:test 2 
SERVER-3|| data： client: 1:test 0  client: 1:test 1  client: 1:test 2 
SERVER-2|| buff：

SERVER-3|| data： client: 1:test 0  client: 1:test 1  client: 1:test 2 
SERVER-4|| data： client: 1:test 0  client: 1:test 1  client: 1:test 2 
SERVER-5|| data： client: 1:test 0  client: 1:test 1  client: 1:test 2 
CLIENT-3|| client：time->[07:01:57] ; reponse-> client: 1:test 0  client: 1:test 1  client: 1:test 2 ; msg->1:test
CLIENT-4|| start：2
CLIENT-1|| msg：2:test
CLIENT-2|| for： msg2:test; index：0
SERVER-1|| server：conn->Resource id #30;  buff->; data->
SERVER-2|| buff： client: 2:test 0 
SERVER-3|| data： client: 2:test 0 
CLIENT-2|| for： msg2:test; index：1
SERVER-2|| buff： client: 2:test 1 
SERVER-3|| data： client: 2:test 0  client: 2:test 1 
SERVER-2|| buff： client: 2:test 2 
CLIENT-2|| for： msg2:test; index：2
SERVER-3|| data： client: 2:test 0  client: 2:test 1  client: 2:test 2 
SERVER-2|| buff：

SERVER-3|| data： client: 2:test 0  client: 2:test 1  client: 2:test 2 
SERVER-4|| data： client: 2:test 0  client: 2:test 1  client: 2:test 2 
SERVER-5|| data： client: 2:test 0  client: 2:test 1  client: 2:test 2 
CLIENT-3|| client：time->[07:01:58] ; reponse-> client: 2:test 0  client: 2:test 1  client: 2:test 2 ; msg->2:test
```

客户端运行：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190114/20190114151758.jpg)

```
CLIENT-1|| msg：abc
CLIENT-2|| for： msgabc; index：0
SERVER-1|| server：conn->Resource id #6;  buff->; data->
SERVER-2|| buff： client: abc 0 
SERVER-3|| data： client: abc 0 
SERVER-2|| buff： client: abc 1 
CLIENT-2|| for： msgabc; index：1
SERVER-3|| data： client: abc 0  client: abc 1 
SERVER-2|| buff： client: abc 2 
CLIENT-2|| for： msgabc; index：2
SERVER-3|| data： client: abc 0  client: abc 1  client: abc 2 
SERVER-2|| buff：

SERVER-3|| data： client: abc 0  client: abc 1  client: abc 2 
SERVER-4|| data： client: abc 0  client: abc 1  client: abc 2 
SERVER-5|| data： client: abc 0  client: abc 1  client: abc 2 
CLIENT-3|| client：time->[07:17:43] ; reponse-> client: abc 0  client: abc 1  client: abc 2 ; msg->abc
```

<br/><br/><br/><br/><br/>
### 参考资料

Streams <http://php.net/manual/en/book.stream.php>

PHP Socket实现websocket（三）Stream函数 <https://www.cnblogs.com/yangxunwu1992/p/5563450.html>

Socket 深度探索 4 PHP （一） <https://blog.csdn.net/shagoo/article/details/6396089>

Swoole学习 <https://ibaiyang.github.io/blog/php/2018/05/26/PHP-Swoole%E5%AD%A6%E4%B9%A0.html>

客户端WebSocket实现 <https://ibaiyang.github.io/blog/html/2019/01/04/%E5%AE%A2%E6%88%B7%E7%AB%AFWebSocket%E5%AE%9E%E7%8E%B0.html>

PHP 文件处理相关函数 <https://ibaiyang.github.io/blog/php/2018/08/11/PHP-%E6%96%87%E4%BB%B6%E5%A4%84%E7%90%86%E7%9B%B8%E5%85%B3%E5%87%BD%E6%95%B0.html>

system函数 <http://www.php.net/manual/zh/function.system.php>

PHP执行系统外部命令函数:exec()、passthru()、system()、shell_exec() <https://www.cnblogs.com/gaohj/p/3267692.html>









