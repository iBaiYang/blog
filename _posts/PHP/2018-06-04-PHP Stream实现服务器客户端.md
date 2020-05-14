---
layout: post
categories: PHP
title: PHP Stream实现服务器客户端
meta: PHP Stream实现服务器客户端
---
* content
{:toc}

### 正文

PHP 官方文档对流的解释如下：

流的作用是提供统一的公共函数来处理文件、网络和数据压缩等操作。简单而言，流是具有流式行为的资源对象，
也就是说，流可以线性读写，并且可以通过 fseek() 之类的函数定位到流中的任何位置。

简化一下，流的作用是在出发地和目的地之间传输数据。出发地和目的地可以是文件、命令行进程、网络连接、ZIP 或 TAR 压缩文件、临时内存、标准输入或输出，
或者是通过 PHP 流封装协议实现的任何其他资源。

PHP中对流的描述如下：

每一种流都实现了一个包装器(wrapper)，包装器包含一些额外的代码用来处理特殊的协议和编码。
PHP提供了一些内置的包装器，我们也可以很轻松的创建和注册自定义的包装器。
我们甚至可以使用上下文(contexts)和过滤器来改变和增强包装器。

PHP中流的形式：<scheme>://<target>。其中<scheme>是包装器的名字，<target>中的内容是由包装器的语法指定，
不同的包装器的语法会有所不同。

默认的包装器是file://，也就是说每次我们访问文件系统的时候都使用了流。例如，我们可以使用如下两种方式来读取文件：
readfile('/path/to/somefile.txt') 和 readfile('file:///path/to/somefile.txt')，
使用这两种方式读取文件，可以得到相同的结果。


#### Stream实现服务器客户端

php中除了用Swoole拓展实现服务器客户端，其实用php原生的Stream拓展也可以实现服务器/客户端。

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

![]({{site.baseurl}}/images/20200330/20200330191536.jpeg)

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

#### stream系列函数

![]({{site.baseurl}}/images/20200514/20200514160924.png)

PHP中streams函数列表如下：

stream_bucket_append函数：为队列添加数据　

stream_bucket_make_writeable函数：从操作的队列中返回一个数据对象

stream_bucket_new函数：为当前队列创建一个新的数据

stream_bucket_prepend函数：预备数据到队列　

stream_context_create函数：创建数据流上下文

stream_context_get_default函数：获取默认的数据流上下文

stream_context_get_options函数：获取数据流的设置

stream_context_set_option函数：对数据流、数据包或者上下文进行设置

stream_context_set_params函数：为数据流、数据包或者上下文设置参数

stream_copy_to_stream函数：在数据流之间进行复制操作

stream_filter_append函数：为数据流添加过滤器

stream_filter_prepend函数：为数据流预备添加过滤器

stream_filter_register函数：注册一个数据流的过滤器并作为PHP类执行

stream_filter_remove函数：从一个数据流中移除过滤器

stream_get_contents函数：读取数据流中的剩余数据到字符串

stream_get_filters函数：返回已经注册的数据流过滤器列表

stream_get_line函数：按照给定的定界符从数据流资源中获取行

stream_get_meta_data函数：从封装协议文件指针中获取报头/元数据

stream_get_transports函数：返回注册的Socket传输列表

stream_get_wrappers函数：返回注册的数据流列表

stream_register_wrapper函数：注册一个用PHP类实现的URL封装协议

stream_select函数：接收数据流数组并等待它们状态的改变

stream_set_blocking函数：将一个数据流设置为堵塞或者非堵塞状态

stream_set_timeout函数：对数据流进行超时设置

stream_set_write_buffer函数：为数据流设置缓冲区

stream_socket_accept函数：接受由函数stream_ socket_server()创建的Socket连接

stream_socket_client函数：打开网络或者UNIX主机的Socket连接

stream_socket_enable_crypto函数：为一个已经连接的Socket打开或者关闭数据加密

stream_socket_get_name函数：获取本地或者网络Socket的名称

stream_socket_pair函数：创建两个无区别的Socket数据流连接

stream_socket_recvfrom函数：从Socket获取数据，不管其连接与否

stream_socket_sendto函数：向Socket发送数据，不管其连接与否

stream_socket_server函数：创建一个网络或者UNIX Socket服务端

stream_wrapper_restore函数：恢复一个事先注销的数据包

stream_wrapper_unregister函数：注销一个URL地址包


<br/><br/><br/><br/><br/>
### 参考资料

PHP 手册 函数参考 其它基本扩展 Streams <https://www.php.net/manual/zh/book.stream.php>

PHP流（Stream）的概述与使用详解 <https://www.jianshu.com/p/79591db09b5a>

PHP 统一资源处理 API —— 流（Stream）的概述与使用详解 <https://xueyuanjun.com/post/7459.html>

PHP7 学习笔记（十二）Stream 函数详解 <https://blog.csdn.net/qq756684177/article/details/81518647>

php中stream(流)的用法 <http://www.luyixian.cn/php_show_111303.aspx>

PHP Socket实现websocket（三）Stream函数 <https://www.cnblogs.com/yangxunwu1992/p/5563450.html>

Socket 深度探索 4 PHP （一） <https://blog.csdn.net/shagoo/article/details/6396089>

Swoole学习 <https://ibaiyang.github.io/blog/php/2018/05/26/PHP-Swoole%E5%AD%A6%E4%B9%A0.html>

客户端WebSocket实现 <https://ibaiyang.github.io/blog/html/2019/01/04/%E5%AE%A2%E6%88%B7%E7%AB%AFWebSocket%E5%AE%9E%E7%8E%B0.html>

PHP 文件处理相关函数 <https://ibaiyang.github.io/blog/php/2018/08/11/PHP-%E6%96%87%E4%BB%B6%E5%A4%84%E7%90%86%E7%9B%B8%E5%85%B3%E5%87%BD%E6%95%B0.html>

system函数 <http://www.php.net/manual/zh/function.system.php>

PHP执行系统外部命令函数:exec()、passthru()、system()、shell_exec() <https://www.cnblogs.com/gaohj/p/3267692.html>









