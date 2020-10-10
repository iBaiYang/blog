---
layout: post
categories: PHP
title: PHP socket编程
meta: socket编程
---
* content
{:toc}

### 正文

#### 什么是TCP/IP

TCP/IP（Transmission Control Protocol/Internet Protocol）即传输控制协议/网间协议，是一个工业标准的协议集，它是为广域网（WANs）设计的。

UDP（User Data Protocol，用户数据报协议）是与TCP相对应的协议。它是属于TCP/IP协议族中的一种。

![]({{site.baseurl}}/images/20190827/20190827142429.png)

TCP/IP协议族包括运输层、网络层、链路层。

#### 什么是Socket

我们没有看到Socket的影子，那么它到底在哪里呢？

![]({{site.baseurl}}/images/20190827/20190827143345.png)

原来Socket在这里。

Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket其实就是一个门面模式，
它把复杂的TCP /IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。
Socket使网络间的通信编程简单了许多。

#### 什么是HTTP

你可能会好奇HTTP，HTTP是什么？

HTTP协议（HyperText Transfer Protocol，超文本传输协议）是用于从WWW服务器传输超文本到本地浏览器的传送协议。

HTTP的发展是万维网协会（World Wide Web Consortium）和Internet工作小组IETF（Internet Engineering Task Force）合作的结果，
最终发布了一系列的RFC，RFC 1945定义了HTTP/1.0版本。其中最著名的就是RFC 2616。RFC 2616定义了今天普遍使用的一个版本——HTTP 1.1。

HTTP是一个应用层协议，由请求和响应构成，是一个标准的客户端服务器模型。HTTP是一个无状态的协议。

HTTP协议通常承载于TCP协议之上，有时也承载于TLS或SSL协议层之上，这个时候，就成了我们常说的HTTPS。

![]({{site.baseurl}}/images/20190827/20190827142817.png)

默认HTTP的端口号为80，HTTPS的端口号为443。

HTTP协议永远都是客户端发起请求，服务器回送响应。无法实现在客户端没有发起请求的时候，服务器将消息推送给客户端。

![]({{site.baseurl}}/images/20190827/20190827143028.png)

HTTP协议是一个无状态的协议，同一个客户端的这次请求和上次请求没有对应关系。

#### Socket编程的工作原理

Socket模型：

![]({{site.baseurl}}/images/20190827/20190827144004.png)

先从服务器端说起。服务器端先初始化Socket，然后与端口绑定(bind)，对端口进行监听(listen)，调用accept阻塞，等待 客户端连接。
在这时如果有个客户端初始化一个Socket，然后连接服务器(connect)，如果连接成功，这时客户端与服务器端的连接就建立了。客户端发送数据请求，
服务器端接收请求并处理请求，然后把回应数据发送给客户端，客户端读取数据，最后关闭连接，一次交互结束。

#### socket相关函数

|        |         | 
| --------   | :-----  |
| socket_accept() | 接受一个Socket连接 |
| socket_bind() | 把socket绑定在一个IP地址和端口上 |
| socket_clear_error() | 清除socket的错误或者最后的错误代码 |
| socket_close() | 关闭一个socket资源 |
| socket_connect() | 开始一个socket连接 |
| socket_create_listen() | 在指定端口打开一个socket监听 |
| socket_create_pair() | 产生一对没有区别的socket到一个数组里 |
| socket_create() | 产生一个socket，相当于产生一个socket的数据结构 |
| socket_get_option() | 获取socket选项 |
| socket_getpeername() | 获取远程类似主机的ip地址 |
| socket_getsockname() | 获取本地socket的ip地址 |
| socket_iovec_add() | 添加一个新的向量到一个分散/聚合的数组 |
| socket_iovec_alloc() | 这个函数创建一个能够发送接收读写的iovec数据结构 |
| socket_iovec_delete() | 删除一个已经分配的iovec |
| socket_iovec_fetch() | 返回指定的iovec资源的数据 |
| socket_iovec_free() | 释放一个iovec资源 |
| socket_iovec_set() | 设置iovec的数据新值 |
| socket_last_error() | 获取当前socket的最后错误代码 |
| socket_listen() | 监听由指定socket的所有连接 |
| socket_read() | 读取指定长度的数据 |
| socket_readv() | 读取从分散/聚合数组过来的数据 |
| socket_recv() | 从socket里结束数据到缓存 |
| socket_recvfrom() | 接受数据从指定的socket，如果没有指定则默认当前socket |
| socket_recvmsg() | 从iovec里接受消息 |
| socket_select() | 多路选择 |
| socket_send() | 这个函数发送数据到已连接的socket |
| socket_sendmsg() | 发送消息到socket |
| socket_sendto() | 发送消息到指定地址的socket |
| socket_set_block() | 在socket里设置为块模式 |
| socket_set_nonblock() | socket里设置为非块模式 |
| socket_set_option() | 设置socket选项 |
| socket_shutdown() | 这个函数允许你关闭读、写、或者指定的socket |
| socket_strerror() | 返回指定错误号的详细错误 |
| socket_write() | 写数据到socket缓存 |
| socket_writev() | 写数据到分散/聚合数组 |


#### socket编程案例

服务器端：
```
<?php
/*
 +-------------------------------
 *    @socket通信整个过程
 +-------------------------------
 *    @socket_create
 *    @socket_bind
 *    @socket_listen
 *    @socket_accept
 *    @socket_read
 *    @socket_write
 *    @socket_close
 +--------------------------------
 */


//确保在连接客户端时不会超时
set_time_limit(0);

$ip = '127.0.0.1';
$port = 1935;

/*----------------    以下操作都是手册上的    -------------------*/
if(($sock = socket_create(AF_INET,SOCK_STREAM,SOL_TCP)) < 0) {
    echo "socket_create() 失败的原因是:".socket_strerror($sock)."\n";
}

if(($ret = socket_bind($sock,$ip,$port)) < 0) {
    echo "socket_bind() 失败的原因是:".socket_strerror($ret)."\n";
}

if(($ret = socket_listen($sock,4)) < 0) {
    echo "socket_listen() 失败的原因是:".socket_strerror($ret)."\n";
}

$count = 0;

do {
    if (($msgsock = socket_accept($sock)) < 0) {
        echo "socket_accept() failed: reason: " . socket_strerror($msgsock) . "\n";
        break;
    } else {
        
        //发到客户端
        $msg ="测试成功！\n";
        socket_write($msgsock, $msg, strlen($msg));
        
        echo "测试成功了啊\n";
        $buf = socket_read($msgsock,8192);
        
        
        $talkback = "收到的信息:$buf\n";
        echo $talkback;
        
        if(++$count >= 5){
            break;
        };
        
    
    }
    //echo $buf;
    socket_close($msgsock);

} while (true);

socket_close($sock);
```

使用 php 脚本名.php 执行脚本后，服务端的程序已经开始运行，端口已经开始监听了。运行 netstat -ano 可以查看端口情况，这里是1935端口

![]({{site.baseurl}}/images/20190827/20190827151937.png)

此时端口已经处于LISTENING状态了。接下来我们只要运行客户端程序即可连接上。

客户端：
```
<?php
/*
 +-------------------------------
 *    @socket连接整个过程
 +-------------------------------
 *    @socket_create
 *    @socket_connect
 *    @socket_write
 *    @socket_read
 *    @socket_close
 +--------------------------------
 */
 
error_reporting(E_ALL);
set_time_limit(0);
echo "<h2>TCP/IP Connection</h2>\n";

$port = 1935;
$ip = "127.0.0.1";

$socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
if ($socket < 0) {
    echo "socket_create() failed: reason: " . socket_strerror($socket) . "\n";
}else {
    echo "OK.\n";
}

echo "试图连接 '$ip' 端口 '$port'...\n";
$result = socket_connect($socket, $ip, $port);
if ($result < 0) {
    echo "socket_connect() failed.\nReason: ($result) " . socket_strerror($result) . "\n";
}else {
    echo "连接OK\n";
}

$in = "Ho\r\n";
$in .= "first blood\r\n";
$out = '';

if(!socket_write($socket, $in, strlen($in))) {
    echo "socket_write() failed: reason: " . socket_strerror($socket) . "\n";
}else {
    echo "发送到服务器信息成功！\n";
    echo "发送的内容为:<font color='red'>$in</font> <br>";
}

while($out = socket_read($socket, 8192)) {
    echo "接收服务器回传信息成功！\n";
    echo "接受的内容为:",$out;
}


echo "关闭SOCKET...\n";
socket_close($socket);
echo "关闭OK\n";
```

运行看一下效果：

![]({{site.baseurl}}/images/20190827/20190827152354.png)

#### 过程代码详解

服务端：
```
// 设置一些基本的变量
$host = "192.168.1.99";
$port = 1234;
// 设置超时时间
set_time_limit(0);
// 创建一个Socket
$socket = socket_create(AF_INET, SOCK_STREAM, 0) or die("Could not createsocket\n");
//绑定Socket到端口
$result = socket_bind($socket, $host, $port) or die("Could not bind tosocket\n");
// 开始监听链接
$result = socket_listen($socket, 3) or die("Could not set up socketlistener\n");
// accept incoming connections
// 另一个Socket来处理通信
$spawn = socket_accept($socket) or die("Could not accept incomingconnection\n");
// 获得客户端的输入
$input = socket_read($spawn, 1024) or die("Could not read input\n");
// 清空输入字符串
$input = trim($input);
//处理客户端输入并返回结果
$output = strrev($input) . "\n";
socket_write($spawn, $output, strlen ($output)) or die("Could not write
output\n");
// 关闭sockets
socket_close($spawn);
socket_close($socket);
```

下面是其每一步骤的详细说明:

1.第一步是建立两个变量来保存Socket运行的服务器的IP地址和端口.你可以设置为你自己的服务器和端口(这个端口可以是1到65535之间的数字),前提是这个端口未被使用.

PHP CODE:
// 设置两个变量
$host = "192.168.1.99";
$port = 1234;

2.在服务器端可以使用set_time_out()函数来确保PHP在等待客户端连接时不会超时.

PHP CODE:
// 超时时间
set_time_limit(0);

3.在前面的基础上,现在该使用socket_creat()函数创建一个Socket了—这个函数返回一个Socket句柄,这个句柄将用在以后所有的函数中.

PHP CODE:
// 创建Socket
$socket = socket_create(AF_INET, SOCK_STREAM, 0) or die("Could not create
socket\n");

第一个参数”AF_INET”用来指定域名;
第二个参数”SOCK_STREM”告诉函数将创建一个什么类型的Socket(在这个例子中是TCP类型)

因此,如果你想创建一个UDP Socket的话,你可以使用如下的代码:

PHP CODE:
// 创建 socket
$socket = socket_create(AF_INET, SOCK_DGRAM, 0) or die("Could not create
socket\n");

4.一旦创建了一个Socket句柄,下一步就是指定或者绑定它到指定的地址和端口.这可以通过socket_bind()函数来完成.

PHP CODE:
// 绑定 socket to 指定地址和端口
$result = socket_bind($socket, $host, $port) or die("Could not bind to
socket\n");

5.当Socket被创建好并绑定到一个端口后,就可以开始监听外部的连接了.PHP允许你由socket_listen()函数来开始一个监听,同时你可以指定一个数字(在这个例子中就是第二个参数:3)

PHP CODE:
// 开始监听连接
$result = socket_listen($socket, 3) or die("Could not set up socket
listener\n");

6.到现在,你的服务器除了等待来自客户端的连接请求外基本上什么也没有做.一旦一个客户端的连接被收到,socket_accept()函数便开始起作用了,它接收连接请求并调用另一个子Socket来处理客户端–服务器间的信息.

PHP CODE:
//接受请求链接
// 调用子socket 处理信息
$spawn = socket_accept($socket) or die("Could not accept incoming
connection\n");

这个子socket现在就可以被随后的客户端–服务器通信所用了.

7.当一个连接被建立后,服务器就会等待客户端发送一些输入信息,这写信息可以由socket_read()函数来获得,并把它赋值给PHP的$input变量.

PHP CODE:
// 读取客户端输入
$input = socket_read($spawn, 1024) or die("Could not read input\n");
?&gt;

socker_read的第二个参数用以指定读入的字节数,你可以通过它来限制从客户端获取数据的大小.

注意:socket_read函数会一直读取客户端数据,直到遇见\n,\t或者\0字符.PHP脚本把这写字符看做是输入的结束符.

8.现在服务器必须处理这些由客户端发来是数据(在这个例子中的处理仅仅包含数据的输入和回传到客户端).这部分可以由socket_write()函数来完成(使得由通信socket发回一个数据流到客户端成为可能)

PHP CODE:
// 处理客户端输入并返回数据
$output = strrev($input) . "\n";
socket_write($spawn, $output, strlen ($output)) or die("Could not write
output\n");

9.一旦输出被返回到客户端,父/子socket都应通过socket_close()函数来终止

PHP CODE:
// 关闭 sockets
socket_close($spawn);
socket_close($socket);

##### 客户端socket

```php
<?php
    if(!($socket = socket_create(AF_INET,SOCK_STREAM,0))){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("Couldn't create socket");
    }
    
    if(!socket_connect($socket,'104.193.88.77',80)){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("connect failed !");
    }
    
    $message = "GET / HTTP/1.1\r\n\r\n";
    if(!socket_send($socket,$message,strlen($message),0)){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("send failed !");
    }
    
    if(socket_recv($socket,$buf,2045,MSG_WAITALL) === false){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("receive failed !");
    }
    echo $buf;
    
    socket_close($socket);
```

1.创建socket

$socket = socket_create(AF_INET, SOCK_STREAM, 0);

该函数返回socket描述符，三个参数分别是：
* 地址协议：AF_INET (这里是ipv4)
* 连接接类型：SOCK_STREAM（面向连接的TCP协议）
* 协议：`0|IPPROTO_IP`  (IP协议)

【错误处理】
如果任何socket函数失败，可以使用 socket_last_errr 和  socket_strerror 函数检索错误信息
```
    if(!($socket = socket_create(AF_INET,SOCK_STREAM,0))){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("Couldn't create socket");
    }
```
【注意】
除了 SOCK_STREAM  类型的socket协议之外， 还有 SOCK_DGRAM 的类型 表示 UDP协议，因此需要知道远程服务器的 ip 地址

2.连接到服务器
```
    if(!socket_connect($socket,'104.193.88.77',80)){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("connect failed !");
    }
```
到这里，我们运行 
$ php socket.php 
就能检测 ip 为104.193.88.77 端口号为 80 是否开启，由此可以构建一个端口扫描程序。

注意：这里连接必须是一个ip地址，有时候我们只知道域名，可以使用如下方式转换：

$ip_address = gethostbyname('www.baidu.com');

连接的概念适用于 SOCK_STREAM/TCP ，而 UDP，ICMP，ARP等其他基于非连接的通信没有连接的概念

3.发送数据

函数 send 只会发送数据，它需要socket描述符，发送的数据及其大小。
```
    $message = "GET / HTTP/1.1\r\n\r\n";
    if(!socket_send($socket,$message,strlen($message),0)){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("send failed !");
    }
```
该消息实际上是一个获取网站主页的http命令

【注意】
将数据发送到socket时，您基本上是将数据写入socket 类似于将数据写入文件，因此还可以使用 write 函数 将数据发送到socket。

4.接收数据

使用 recv 函数接收套接字上的数据
```
    if(socket_recv($socket,$buf,2045,MSG_WAITALL) === false){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("receive failed !");
    }
    echo $buf;
```

5.关闭socket

socket_close($socket);

总结一下：客户端发起socket请求流程
* 》创建socket
* 》连接到远程服务器
* 》发送数据
* 》接收数据
* 》关闭socket

其实就是类似于打开浏览器访问www.baidu.com一样的整个流程

##### 服务端socket

1.创建一个服务端master socket
```
    if(!($socket = socket_create(AF_INET,SOCK_STAREAM,0))){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("create socket failed ");
    }
```

2.绑定到地址(和端口)

函数 bind 可用于套接字绑定到特定的地址和端口，他需要一个类似 connect 函数的结构 服务socket、ip 和端口
```
    if(!socket_bind($socket,'127.0.0.1',5001)){
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
        die("bind socket failed ");
    }
```
如果你想接收所有的 ip 可以将 ip 改为 0.0.0.0

3.监听连接

socket_listen($socket, 10);

第二个参数是控制程序在已经很忙碌的时候保持等待的传入连接数，比如 已经有10个连接等待处理，第11个连接请求将会被拒绝

4.接受连接，关闭连接
```
    $client = socket_accept($socket);
    if(socket_getpeername($client,$address,$port)){
        echo "";
    }
    socket_close($client);
    socket_close($socket);
```

> php server.php

此时该程序正在等待端口5001上的传入连接我们新开一个窗口

> telnet localhost 5001

【注意】
socket_getpeername 函数用于获取有关通过特定socket连接到服务器的客户端详细信息

我们建立了连接，并立即关闭了他，这并没有什么实际的用处，那我们回复客户端，我们可以使用socket_write 函数向传入的套接字
写入内容

5.读取/发送数据
```
$input = socket_read($client,102400);
$response = "ok ... $input";
socket_write($client,$response);
socket_close($client);
```
在终端中运行
```
$ telnet localhost 5001
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
happy
OK .. h
Connection closed by foreign host
```

我们收到回复之后，连接被立即关闭了，而像百度这样的服务器总是一直在接收传入的链接，这意味着服务器应该一直在
运行，所以我们想让自己的服务器一直运行，最简单的方法是将 accept 置于一个循环中以便他能一直接收传入的连接

6.在线服务器
```
$address = '0.0.0.0';
$port = '5001';
while(true){
    $client = socket_accept($socket);
    if(socket_getpeername($client,$address,$port)){
      echo ''; 
    }
    $input = socket_read($client, 1024000);
    $response = "OK .. $input";
    socket_write($client, $response);
}
```
这里我们将 accpet 加入了循环，但是每个发起的客户端，仍然没有有效的通信，他无法一次处理多个连接

7.多连接处理

为了处理每个连接，我们需要一个单独的处理代码来与主服务器接受连接一起运行。实现此目的的一种方法是使用线程。
主服务器程序接受连接并创建一个新线程来处理连接的通信，然后服务器返回以接受更多连接。但是php不直接支持线程。

另一种方法是使用 select 函数。select 函数基本上`轮询`或观察一组套接字用于某些事件，比如它是否可读，可写或有问题等等。
因此 select 函数可用于监视多个客户端并检查哪个客户端发送了一条消息。
```php
<?php
error_reporting(~E_NOTICE);
set_time_limit (0);
 
$address = "0.0.0.0";
$port = 5000;
$max_clients = 10;
//1. 创建连接 
if(!($sock = socket_create(AF_INET, SOCK_STREAM, 0)))
{
    $errorcode = socket_last_error();
    $errormsg = socket_strerror($errorcode);     
    die("Couldn't create socket: [$errorcode] $errormsg \n");
}
 
echo "Socket created \n";
 
// 绑定连接
if( !socket_bind($sock, $address , $port) )
{
    $errorcode = socket_last_error();
    $errormsg = socket_strerror($errorcode);
     
    die("Could not bind socket : [$errorcode] $errormsg \n");
}
 
echo "Socket bind OK \n";
 
//监听 
if(!socket_listen ($sock , 10))
{
    $errorcode = socket_last_error();
    $errormsg = socket_strerror($errorcode);
     
    die("Could not listen on socket : [$errorcode] $errormsg \n");
}
 
echo "Socket listen OK \n";
 
echo "Waiting for incoming connections... \n";
 
//array of client sockets
$client_socks = array();
 
//array of sockets to read
$read = array();
 
//开始循环接收进来的连接和已经存在的连接
while (true) 
{
    //准备一个存储socket的数组
    $read = array();
     
    //第一个是主服务的 socket
    $read[0] = $sock;
     
    //然后添加存在的 client sockets
    for ($i = 0; $i < $max_clients; $i++)
    {
        if($client_socks[$i] != null)
        {
            $read[$i+1] = $client_socks[$i];
        }
    }
     
    //调用 select - 遇到错误 终止调用
    if(socket_select($read , $write , $except , null) === false)
    {
        $errorcode = socket_last_error();
        $errormsg = socket_strerror($errorcode);
     
        die("Could not listen on socket : [$errorcode] $errormsg \n");
    }
     
    //如果包含了主服务的 socket, 那么就可以就收新的客户端socket连接
    if (in_array($sock, $read)) 
    {
        for ($i = 0; $i < $max_clients; $i++)
        {
            if ($client_socks[$i] == null) 
            {
                $client_socks[$i] = socket_accept($sock);//接受连接
                 
                //显示连接信息
                if(socket_getpeername($client_socks[$i], $address, $port))
                {
                    echo "Client $address : $port is now connected to us. \n";
                }
                 
                //发送语句给客户端
                $message = "Welcome to php socket server version 1.0 \n";
                $message .= "Enter a message and press enter, and i shall reply back \n";
                socket_write($client_socks[$i] , $message);
                break;
            }
        }
    }
 
    //检查每个客户端是否有数据发送
    for ($i = 0; $i < $max_clients; $i++)
    {
        if (in_array($client_socks[$i] , $read))
        {
            $input = socket_read($client_socks[$i] , 1024);//读取数据
             
            if ($input == null) 
            {
                //如果输入为空 那么意味着客户端失去连接 关闭客户端并移除
                unset($client_socks[$i]);
                socket_close($client_socks[$i]);
            }
 
            $n = trim($input);
 
            $output = "OK ... $input";
             
            echo "Sending output to client \n";
             
            //发送回复信息给客户端
            socket_write($client_socks[$i] , $output);
        }
    }
}
```

运行上述服务器并像以前一样打开3个终端。现在，服务器将为连接到它的每个客户端创建一个线程。

总结一下:  服务端创建一个socket服务需要如下几步
* 》1. 打开socket
* 》2. 绑定到地址(和端口)
* 》3. 监听传入的连接
* 》4. 接受数据
* 》5. 读取数据/发送数据

关于 socket_select 部分内容可以看[这里](https://ibaiyang.github.io/blog/php/2020/07/01/PHP-Socket%E5%88%9D%E6%8E%A2.html)

<br/><br/><br/><br/><br/>
### 参考资料

PHP Socket初探 <https://ibaiyang.github.io/blog/php/2020/07/01/PHP-Socket%E5%88%9D%E6%8E%A2.html>

深入浅出讲解：php的socket通信  <https://www.cnblogs.com/aipiaoborensheng/p/6708963.html>

PHP中socket的使用入门 <https://blog.csdn.net/wujiangwei567/article/details/81144852>

PHP 手册 函数参考 其它服务 <https://www.php.net/manual/zh/book.sockets.php>

深入理解HTTP协议、HTTP协议原理分析 <https://www.cnblogs.com/chenliyang/p/6558756.html>

php socket简单原理及实现笔记 <https://blog.csdn.net/u012728971/article/details/80685796>
     
PHP socket初探 先从一个简单的socket服务器开始 <https://segmentfault.com/a/1190000016226578>

PHP常用技术（五）之socket的简单使用 <https://www.cnblogs.com/qiye5757/p/9721401.html>

socket，tcp，http三者之间的区别和原理 <https://www.jianshu.com/p/a24ba459e306>

