---
layout: post
categories: PHP
title: PHP Socket初探
meta: PHP Socket初探
---
* content
{:toc}

### 正文

来自博客 <https://github.com/elarity/advanced-php>, 因为文本换行等没处理好，所以这里整理下。

#### 9 PHP Socket初探---先从一个简单的socket服务器开始

socket的中文名字叫做套接字，这种东西就是对TCP/IP的“封装”。现实中的网络实际上只有四层而已，从上至下分别是应用层、传输层、网络层、数据链路层。最常用的http协议则是属于应用层的协议，而socket，可以简单粗暴的理解为是传输层的一种东西。如果还是很难理解，那再粗暴地点儿tcp://218.221.11.23:9999，看到没？这就是一个tcp socket。

socket赋予了我们操控传输层和网络层的能力，从而得到更强的性能和更高的效率，socket编程是解决高并发网络服务器的最常用解决和成熟的解决方案。任何一名服务器程序员都应当掌握socket编程相关技能。

在php中，可以操控socket的函数一共有两套，一套是socket_*系列的函数，另一套是stream_*系列的函数。socket_*是php直接将C语言中的socket抄了过来得到的实现，而stream_*系则是php使用流的概念将其进行了一层封装。下面用socket_*系函数简单为这一系列文章开个篇。

先来做个最简单socket服务器：
```php
<?php
$host = '0.0.0.0';
$port = 9999;
// 创建一个tcp socket
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
// 将socket bind到IP：port上
socket_bind( $listen_socket, $host, $port );
// 开始监听socket
socket_listen( $listen_socket );
// 进入while循环，不用担心死循环死机，因为程序将会阻塞在下面的socket_accept()函数上
while( true ){
  // 此处将会阻塞住，一直到有客户端来连接服务器。阻塞状态的进程是不会占据CPU的
  /*
   之所以不会占据CPU,因为CPU运算的时候,类似有个指挥官的家伙会调度,进程切换,简称调度,它只会指挥准备开始打战和正在打战的人,而正在休息军人(阻塞中)不需要命令他们打战,这样也符合常理了
  你也可以看到下图,调度只在运行和就绪之间的,所以cpu不会傻傻等正在休息的士兵起来了,在再指挥
  */
  // 所以你不用担心while循环会将机器拖垮，不会的 
  $connection_socket = socket_accept( $listen_socket );
  // 向客户端发送一个helloworld
  $msg = "helloworld\r\n";
  socket_write( $connection_socket, $msg, strlen( $msg ) );
  socket_close( $connection_socket );
}
socket_close( $listen_socket );
```
![](./进程状态图.jpeg)

将文件保存为server.php，然后执行php server.php运行起来。客户端我们使用telnet就可以了，打开另外一个终端执行telnet 127.0.0.1 9999按下回车即可。运行结果如下：

![](http://static.ti-node.com/6382803364538744833)

简单解析一下上述代码来说明一下tcp socket服务器的流程：
- 1.首先，根据协议族（或地址族）、套接字类型以及具体的的某个协议来创建一个socket。
- 2.第二，将上一步创建好的socket绑定（bind）到一个ip:port上。
- 3.第三，开启监听listen。
- 4.第四，使服务器代码进入无限循环不退出，当没有客户端连接时，程序阻塞在accept上，有连接进来时才会往下执行，然后再次循环下去，为客户端提供持久服务。

上面这个案例中，有两个很大的缺陷：
- 1.一次只可以为一个客户端提供服务，如果正在为第一个客户端发送helloworld期间有第二个客户端来连接，那么第二个客户端就必须要等待片刻才行。
- 2.很容易受到攻击，造成拒绝服务。

分析了上述问题后，又联想到了前面说的多进程，那我们可以在accpet到一个请求后就fork一个子进程来处理这个客户端的请求，这样当accept了第二个客户端后再fork一个子进程来处理第二个客户端的请求，这样问题不就解决了吗？OK！撸一把代码演示一下：
```php
<?php
$host = '0.0.0.0';
$port = 9999;
// 创建一个tcp socket
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
// 将socket bind到IP：port上
socket_bind( $listen_socket, $host, $port );
// 开始监听socket
socket_listen( $listen_socket );
// 进入while循环，不用担心死循环死机，因为程序将会阻塞在下面的socket_accept()函数上
while( true ){
  // 此处将会阻塞住，一直到有客户端来连接服务器。阻塞状态的进程是不会占据CPU的
  // 所以你不用担心while循环会将机器拖垮，不会的 
  $connection_socket = socket_accept( $listen_socket );
  // 当accept了新的客户端连接后，就fork出一个子进程专门处理
  $pid = pcntl_fork();
  // 在子进程中处理当前连接的请求业务
  if( 0 == $pid ){
    // 向客户端发送一个helloworld
    $msg = "helloworld\r\n";
    socket_write( $connection_socket, $msg, strlen( $msg ) );
	// 休眠5秒钟，可以用来观察时候可以同时为多个客户端提供服务
	echo time().' : a new client'.PHP_EOL;
	sleep( 5 );
    socket_close( $connection_socket );
	exit;
  }
}
socket_close( $listen_socket );
```

将代码保存为server.php，然后执行php server.php，客户端依然使用telnet 127.0.0.1 9999，只不过这次我们开启两个终端来执行telnet。重点观察当第一个客户端连接上去后，第二个客户端时候也可以连接上去。运行结果如下：

![](http://static.ti-node.com/6382835382009987072)

通过接受到客户端请求的时间戳可以看到现在服务器可以同时为N个客户端服务的。但是，接着想，如果先后有1万个客户端来请求呢？这个时候服务器会fork出1万个子进程来处理每个客户端连接，这是会死人的。fork本身就是一个很浪费系统资源的系统调用，1W次fork足以让系统崩溃，即便当下系统承受住了1W次fork，那么fork出来的这1W个子进程也够系统内存喝一壶了，最后是好不容易费劲fork出来的子进程在处理完毕当前客户端后又被关闭了，下次请求还要重新fork，这本身就是一种浪费，不符合社会主义主流价值观。如果是有人恶意攻击，那么系统fork的数量还会呈直线上涨一直到系统崩溃。

所以，我们就再次提出增进型解决方案。我们可以预估一下业务量，然后在服务启动的时候就fork出固定数量的子进程，每个子进程处于无限循环中并阻塞在accept上，当有客户端连接挤进来就处理客户请求，当处理完成后仅仅关闭连接但本身并不销毁，而是继续等待下一个客户端的请求。这样，不仅避免了进程反复fork销毁巨大资源浪费，而且通过固定数量的子进程来保护系统不会因无限fork而崩溃。
```php
<?php
$host = '0.0.0.0';
$port = 9999;
// 创建一个tcp socket
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
// 将socket bind到IP：port上
socket_bind( $listen_socket, $host, $port );
// 开始监听socket
socket_listen( $listen_socket );
// 给主进程换个名字
cli_set_process_title( 'phpserver master process' );
// 按照数量fork出固定个数子进程
for( $i = 1; $i <= 10; $i++ ){
  $pid = pcntl_fork();
  if( 0 == $pid ){
    cli_set_process_title( 'phpserver worker process' );
    while( true ){
	  $conn_socket = socket_accept( $listen_socket );
	  $msg = "helloworld\r\n";
	  socket_write( $conn_socket, $msg, strlen( $msg ) );
	  socket_close( $conn_socket );
	}
  }
}
// 主进程不可以退出，代码演示比较粗暴，为了不保证退出直接走while循环，休眠一秒钟
// 实际上，主进程真正该做的应该是收集子进程pid，监控各个子进程的状态等等
while( true ){
  sleep( 1 );
}
socket_close( $connection_socket );
```
将文件保存为server.php后php server.php执行，然后再用ps -ef | grep phpserver | grep -v grep来看下服务器进程状态：

![](http://static.ti-node.com/6382839936025886721)

可以看到master进程存在，除此之外还有10个子进程处于等待服务状态，再同一个时刻可以同时为10个客户端提供服务。我们通过telnet 127.0.0.1 9999来尝试一下，运行结果如下图：

![](http://static.ti-node.com/6382840432635674625)

好啦，php新的征程系列就先通过一个简单的入门开始啦！下篇将会讲述一些比较深刻的理论基础知识。

#### 10 PHP socket初探---关于IO的一些枯燥理论

要想更好了解socket编程，有一个不可绕过的环节就是IO．

在Linux中，一切皆文件．实际上要文件干啥？不就是读写么？所以，这句话本质就是"IO才是王道"．用php的fopen打开文件关闭文件读读写写，这叫本地文件IO．在socket编程中，本质就是网络IO．

所以，在开始进一步的socket编程前，我们必须先从概念上认识好IO．

如果到这里你还对IO没啥概念，那么我就通过几个词来给你一个大概的印象：同步，异步，阻塞，非阻塞，甚至是同步阻塞，同步非阻塞，异步阻塞，异步非阻塞．是不是晕了？截至到目前为止，你可以简单地认为只要搞明白这几个名词的含义以及区别，就算弄明白IO了，至少了可以继续往下看了．

先机械记忆一波儿：IO分为两大种，同步和异步．

同步IO：

- 阻塞IO
- 非阻塞IO 
- IO多路复用（包括select，poll，epoll三种）
- 信号驱动IO

异步IO

那么如何理解区别这几个概念呢？尤其是同步和阻塞，异步和非阻塞，看起来就是一样的．

我先举个例子结合自己的理解来说明一下：
1. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，你自己看着点儿！＂．于是你就站在旁边只等馒头．此时的你，**是阻塞的**，**是同步的**．阻塞表现在你除了等馒头，别的什么都不做了．同步表现在等馒头的过程中，阿梅不提供通知服务，你不得不自己要等到＂馒头出炉＂的消息．
2. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，你自己看着点儿！＂．于是你就站在旁边发微信，然后问一句：＂好了没？＂，然后发QQ，然后再问一句：＂好了没？＂．此时的你，**是非阻塞的**，**是同步的**．非阻塞表现在你除了等馒头，自己还干干别的时不时会主动问问馒头好没好．同步表现在等馒头的过程中，阿梅不提供通知服务，你不得不自己要等到＂馒头出炉＂的消息．
3. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，蒸好了我打电话告诉你！＂．但你依然站在旁边只等馒头，此时的你，**是阻塞的**，**是异步的**．阻塞表现在你除了等馒头，别的什么都不做了．异步表现在等馒头的过程中，阿梅提供电话通知＂馒头出炉＂的消息，你只需要等阿梅的电话．
4. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，蒸好了我打电话告诉你！＂．于是你就走了，去买了双新球鞋，看了看武馆，总之，从此不再过问馒头的事情，一心只等阿梅电话．此时的你，**是非阻塞的**，**是异步的**．非阻塞表现在你除了等馒头，自己还干干别的时不时会主动问问馒头好没好．异步表现在等馒头的过程中，阿梅提供电话通知＂馒头出炉＂的消息，你只需要等阿梅的电话．

如果你仔细品过上面案例中的每一个字，你就能慢慢体会到之所以异步和非阻塞，同步和阻塞容易混淆，仅仅是因为二者的表现形式稍微有点儿相似而已．

阻塞和非阻塞关注的是：在等馒头的过程中，你在干啥．

同步和异步关注的是：等馒头这件事，你是一直等到＂馒头出炉＂的结果，还是立即跑路等阿梅告诉你的＂馒头出炉＂．重点的是你是如何得知＂馒头出炉＂的．

所以现实世界中，最傻的人才会采用异步阻塞的IO方式去写程序．其余三种方式，更多的人都会选择同步阻塞或者异步非阻塞．同步非阻塞最大的问题在于，你需要不断在各个任务中忙碌着，导致你的大脑混乱，非常累．

#### 11 PHP socket初探---select系统调用

socket初探 --- 先从一个简单的socket服务器开始 ＞中依次讲解了三个逐渐进步的服务器：
- 只能服务于一个客户端的服务器
- 利用fork可以服务于多个客户端的额服务器
- 利用预fork派生进程服务于多个客户端的服务器

最后一种服务器的进程模型基本上的大概原理其实跟我们常用的apache是非常相似的．

其实这种模型最大的问题在于需要根据实际业务预估进程数量，依旧是需要大量进程来解决问题，可能会出现CPU浪费在进程间切换上，还有可能会出现惊群现象（简单理解就是100个进程在等带客户端连接，来了一个客户端但是所有进程都被唤醒了，但最终只有一个进程为这个客户端服务，其余99个白白折腾），那么，有没有一种解决方案可以使得少量进程服务于多个客户端呢？

答案就是在 PHP socket初探 --- 关于IO的一些枯燥理论 ＞中提到的＂IO多路复用＂．多路是指多个客户端连接socket，复用就是指复用少数几个进程，多路复用本身依然隶属于同步通信方式，只是表现出的结果看起来像异步，这点值得注意．目前多路复用有三种常用的方案，依次是：

- select，最早的解决方案
- poll，算是select的升级版
- epoll，目前的最终解决版，解决c10k问题的功臣

今天说的是select，这个东西本身是个Linux系统调用．在Linux中一切皆为文件，socket也不例外，每当Linux打开一个文件系统都会返回一个对应该文件的标记叫做文件描述符．文件描述符是一个非负整数，当文件描述数达到最大的时候，会重新回到小数重新开始（题外话：按照传统，一般情况下标准输入是0，标准输出是1，标准错误是2）．对文件的读写操作就是利用对文件描述符的读写操作．一个进程可以操作的文件描述符的数量是有限制的，不同系统有不同的数量，在linux中，可以通过调整ulimit来调整控制．

先通过一个简单的例子说明下select的作用和功能．双11到了，你给少林足球队买了很多很多球鞋，分别有10个快递给你运送，然后你就不断地电话询问这10个快递员，你觉得有点儿累．阿梅很心疼你，于是阿梅就说："这事儿你不用管了，你去专心练大力金刚腿吧，等任何一个快递到了，我告诉你"．当其中一个快递来了后，阿梅就喊你：＂下来啦，有快递！＂，但是，这个阿梅比较缺心眼，她不告诉你是具体哪双鞋子的快递，只告诉你有快递到了．所以，你只能依次查询一遍所有快递单的状态才能确认是哪个签收了．

上面这个例子通过结合术语演绎一遍就是，你就是服务器软件，阿梅就是select，10个快递就是10个客户端（也就是10个连接socket fd）．阿梅负责替你管理着这10个连接socket fd，当其中任何一个fd有反应了也就是可以读数据或可以发送数据了，阿梅（select）就会告诉你有可以读写的fd了，但是阿梅（select）不会告诉你是哪个fd可读写，所以你必须轮循所有fd来看看是哪个fd，是可读还是可写．

是时候机械记忆一波儿了：

当你启动select后，需要将三组不同的socket fd加入到作为select的参数，传统意义上这种fd的集合就叫做fd_set，三组fd_set依次是可读集合，可写集合，异常集合．三组fd_set由系统内核来维护，每当select监控管理的三个fd_set中有可读或者可写或者异常出现的时候，就会通知调用方．调用方调用select后，调用方就会被select阻塞，等待可读可写等事件的发生．一旦有了可读可写或者异常发生，需要将三个fd_set从内核态全部copy到用户态中，然后调用方通过轮询的方式遍历所有fd，从中取出可读可写或者异常的fd并作出相应操作．如果某次调用方没有理会某个可操作的fd，那么下一次其余fd可操作时，也会再次将上次调用方未处理的fd继续返回给调用方，也就是说去遍历fd的时候，未理会的fd依然是可读可写等状态，一直到调用方理会．

上面都是我个人的理解和汇总，有错误可以指出，希望不会误人子弟．下面通过php代码实例来操作一波儿select系统调用．在php中，你可以通过stream_select或者socket_select来操作select系统调用，下面演示socket_select进行代码演示：
```php
<?php

// BEGIN 创建一个tcp socket服务器
$host = '0.0.0.0';
$port = 9999;
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $listen_socket, $host, $port );
socket_listen( $listen_socket );
// END 创建服务器完毕 

// 也将监听socket放入到read fd set中去，因为select也要监听listen_socket上发生事件
$client = [ $listen_socket ];
// 先暂时只引入读事件，避免有同学晕头
$write = [];
$exp = [];

// 开始进入循环
while( true ){
  $read = $client;
  // 当select监听到了fd变化，注意第四个参数为null
  // 如果写成大于0的整数那么表示将在规定时间内超时
  // 如果写成等于0的整数那么表示不断调用select，执行后立马返回，然后继续
  // 如果写成null，那么表示select会阻塞一直到监听发生变化
  /*
  第一个参数必须是数组,数组里面含有待检测的套接字,而且第四个参数写成null阻塞就是代表程序就一直停在socket_select这个函数上,
  什么都不干,等你有连接或者有数据发送,我才继续执行,所以你使用var_dump后,再进行telnet 才会有返回值,否则没有任何输出的
  */
  if( socket_select( $read, $write, $exp, null ) > 0 ){
    // 判断listen_socket有没有发生变化，如果有就是有客户端发生连接操作了
    if( in_array( $listen_socket, $read ) ){
	  // 将客户端socket加入到client数组中
	  //socket_accept创建一个可用套接字传送数据,准备给其他客户端发送数据用的
	  $client_socket = socket_accept( $listen_socket );
	  //下面这句很有用,避免了  unset( $read[ $key ] )后,在while时,客户端进来再次用  $client赋值给$read
	  $client[] = $client_socket;
	  // 然后将listen_socket从read中去除掉
	  $key = array_search( $listen_socket, $read );
	  unset( $read[ $key ] );
	}
	// 查看去除listen_socket中是否还有client_socket
	//已经进行telnet连接后,会直接走这一步,不会进去上面代码的in_array,
	if( count( $read ) > 0 ){
	  $msg = 'hello world';
	  foreach( $read as $socket_item ){
      // 从可读取的fd中读取出来数据内容，然后发送给其他客户端
      $content = socket_read( $socket_item, 2048 );
      // 循环client数组，将内容发送给其余所有客户端
      foreach( $client as $client_socket ){
        // 因为client数组中包含了 listen_socket 以及当前发送者自己socket，$client_socket != $socket_item 再次排除自已,所以需要排除二者
        if( $client_socket != $listen_socket && $client_socket != $socket_item ){
          socket_write( $client_socket, $content, strlen( $content ) );
        }
      }
	  }
	}
  } 
  // 当select没有监听到可操作fd的时候，直接continue进入下一次循环
  else {
    continue;
  }
  
}
```

将文件保存为server.php，然后执行php server.php运行服务，同时再打开三个终端，执行telnet 127.0.0.1 9999，然后在任何一个telnet终端中输入"I am xiaoming!"，再看其他两个telnet窗口，是不是感觉很屌？

不完全截图图下：

![](http://static.ti-node.com/6389738812670476289)

还没意识到问题吗？如果我们看到有三个telnet客户端连接服务器并且可以彼此之间发送消息，但是我们只用了一个进程就可以服务三个客户端，如果你愿意，可以开更多的telnet，但是服务器只需要一个进程就可以搞定，这就是IO多路复用diao的地方！

最后，我们重点解析一些socket_select函数，我们看下这个函数的原型：

```php
int socket_select ( array &$read , array &$write , array &$except , int $tv_sec [, int $tv_usec = 0 ] )
```

值得注意的是$read，$write，$except三个参数前面都有一个&，也就是说这三个参数是引用类型的，是可以被改写内容的．在上面代码案例中，服务器代码第一次执行的时候，我们要把需要监听的所有fd全部放到了read数组中，然而在当系统经历了select后，这个数组的内容就会发生改变，由原来的全部read fds变成了只包含可读的read fds，这也就是为什么声明了一个client数组，然后又声明了一个read数组，然后read = client．如果我们直接将client当作socket_select的参数，那么client数组内容就被修改．假如有5个用户保存在client数组中，只有1个可读，在经过socket_select后client中就只剩下那个可读的fd了，其余4个客户端将会丢失，此时客户端的表现就是连接莫名其妙发生丢失了．


<br/><br/><br/><br/><br/>
### 参考资料

<https://github.com/elarity/advanced-php>
