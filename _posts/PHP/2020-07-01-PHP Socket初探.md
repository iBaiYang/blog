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

接着[上篇](https://ibaiyang.github.io/blog/php/2020/06/30/PHP-多进程初探.html)多进程编程继续讲socket编程。

#### 9 先从一个简单的socket服务器开始

socket的中文名字叫做套接字，这种东西就是对TCP/IP的“封装”。现实中的网络实际上只有四层而已，从上至下分别是应用层、传输层、网络层、数据链路层。
最常用的http协议则是属于应用层的协议，而socket，可以简单粗暴的理解为是传输层的一种东西。
如果还是很难理解，那再粗暴地点儿tcp://218.221.11.23:9999，看到没？这就是一个tcp socket。

socket赋予了我们操控传输层和网络层的能力，从而得到更强的性能和更高的效率，socket编程是解决高并发网络服务器的最常用解决和成熟的解决方案。
任何一名服务器程序员都应当掌握socket编程相关技能。

在php中，可以操控socket的函数一共有两套，一套是`socket_*`系列的函数，另一套是`stream_*`系列的函数。
`socket_*`是php直接将C语言中的socket抄了过来得到的实现，而`stream_*`系则是php使用流的概念将其进行了一层封装。
下面用`socket_*`系函数简单为这一系列文章开个篇。

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
while( true )
{
    // 此处将会阻塞住，一直到有客户端来连接服务器。阻塞状态的进程是不会占据CPU的
    /*
    之所以不会占据CPU,因为CPU运算的时候,类似有个指挥官的家伙会调度,进程切换,简称调度,它只会指挥准备开始打战和正在打战的人,
    而正在休息军人(阻塞中)不需要命令他们打战,这样也符合常理了
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

![](/blog/images/20200921/20200921002101.jpeg)

将文件保存为server.php，然后执行php server.php运行起来。客户端我们使用telnet就可以了，
打开另外一个终端执行telnet 127.0.0.1 9999按下回车即可。运行结果如下：

![](/blog/images/20200921/20200921002102.png)

简单解析一下上述代码来说明一下tcp socket服务器的流程：
- 1.首先，根据协议族（或地址族）、套接字类型以及具体的的某个协议来创建一个socket。
- 2.第二，将上一步创建好的socket绑定（bind）到一个ip:port上。
- 3.第三，开启监听listen。
- 4.第四，使服务器代码进入无限循环不退出，当没有客户端连接时，程序阻塞在accept上，有连接进来时才会往下执行，然后再次循环下去，为客户端提供持久服务。

上面这个案例中，有两个很大的缺陷：
- 1.一次只可以为一个客户端提供服务，如果正在为第一个客户端发送helloworld期间有第二个客户端来连接，那么第二个客户端就必须要等待片刻才行。
- 2.很容易受到攻击，造成拒绝服务。

分析了上述问题后，又联想到了前面说的多进程，那我们可以在accpet到一个请求后就fork一个子进程来处理这个客户端的请求，
这样当accept了第二个客户端后再fork一个子进程来处理第二个客户端的请求，这样问题不就解决了吗？OK！撸一把代码演示一下：
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
while( true )
{
    // 此处将会阻塞住，一直到有客户端来连接服务器。阻塞状态的进程是不会占据CPU的
    // 所以你不用担心while循环会将机器拖垮，不会的 
    $connection_socket = socket_accept( $listen_socket );
    // 当accept了新的客户端连接后，就fork出一个子进程专门处理
    $pid = pcntl_fork();
    // 在子进程中处理当前连接的请求业务
    if ( 0 == $pid ) {
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

将代码保存为server.php，然后执行php server.php，客户端依然使用telnet 127.0.0.1 9999，只不过这次我们开启两个终端来执行telnet。
重点观察当第一个客户端连接上去后，第二个客户端时候也可以连接上去。运行结果如下：

![](/blog/images/20200921/20200921002103.png)

通过接受到客户端请求的时间戳可以看到现在服务器可以同时为N个客户端服务的。但是，接着想，如果先后有1万个客户端来请求呢？
这个时候服务器会fork出1万个子进程来处理每个客户端连接，这是会死人的。fork本身就是一个很浪费系统资源的系统调用，
1W次fork足以让系统崩溃，即便当下系统承受住了1W次fork，那么fork出来的这1W个子进程也够系统内存喝一壶了，
最后是好不容易费劲fork出来的子进程在处理完毕当前客户端后又被关闭了，
下次请求还要重新fork，这本身就是一种浪费，不符合社会主义主流价值观。如果是有人恶意攻击，那么系统fork的数量还会呈直线上涨一直到系统崩溃。

所以，我们就再次提出增进型解决方案。我们可以预估一下业务量，然后在服务启动的时候就fork出固定数量的子进程，
每个子进程处于无限循环中并阻塞在accept上，当有客户端连接挤进来就处理客户请求，当处理完成后仅仅关闭连接但本身并不销毁，
而是继续等待下一个客户端的请求。这样，不仅避免了进程反复fork销毁巨大资源浪费，而且通过固定数量的子进程来保护系统不会因无限fork而崩溃。
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
// 主进程不可以退出，代码演示比较粗暴，为了保证不退出直接走while循环，休眠一秒钟
// 实际上，主进程真正该做的应该是收集子进程pid，监控各个子进程的状态等等
while( true ){
    sleep( 1 );
}
socket_close( $listen_socket );
```

将文件保存为server.php后php server.php执行，然后再用 `ps -ef | grep phpserver | grep -v grep` 来看下服务器进程状态：

![](/blog/images/20200921/20200921002104.png)

可以看到master进程存在，除此之外还有10个子进程处于等待服务状态，在同一个时刻可以同时为10个客户端提供服务。
我们通过telnet 127.0.0.1 9999来尝试一下，运行结果如下图：

![](/blog/images/20200921/20200921002105.png)

好啦，php新的征程系列就先通过一个简单的入门开始啦！下篇将会讲述一些比较深刻的理论基础知识。

#### 10 关于IO的一些枯燥理论

要想更好了解socket编程，有一个不可绕过的环节就是IO．

在Linux中，一切皆文件．实际上要文件干啥？不就是读写么？所以，这句话本质就是"IO才是王道"．
用php的fopen打开文件关闭文件读读写写，这叫本地文件IO．在socket编程中，本质就是网络IO．

所以，在开始进一步的socket编程前，我们必须先从概念上认识好IO．

如果到这里你还对IO没啥概念，那么我就通过几个词来给你一个大概的印象：同步，异步，阻塞，非阻塞，
甚至是同步阻塞，同步非阻塞，异步阻塞，异步非阻塞．是不是晕了？截至到目前为止，
你可以简单地认为只要搞明白这几个名词的含义以及区别，就算弄明白IO了，至少了可以继续往下看了．

先机械记忆一波儿：IO分为两大种，同步和异步．

**同步IO**：
- 阻塞IO
- 非阻塞IO 
- IO多路复用（包括select，poll，epoll三种）
- 信号驱动IO

**异步IO**

那么如何理解区别这几个概念呢？尤其是同步和阻塞，异步和非阻塞，看起来就是一样的．

我先举个例子结合自己的理解来说明一下：
1. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，你自己看着点儿！＂．于是你就站在旁边只等馒头．此时的你，**是阻塞的**，**是同步的**．
阻塞表现在你除了等馒头，别的什么都不做了．同步表现在等馒头的过程中，阿梅不提供通知服务，你不得不自己要等到＂馒头出炉＂的消息．
2. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，你自己看着点儿！＂．于是你就站在旁边发微信，然后问一句：＂好了没？＂，
然后发QQ，然后再问一句：＂好了没？＂．此时的你，**是非阻塞的**，**是同步的**．非阻塞表现在你除了等馒头，
自己还干干别的时不时会主动问问馒头好没好．同步表现在等馒头的过程中，阿梅不提供通知服务，你不得不自己要等到＂馒头出炉＂的消息．
3. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，蒸好了我打电话告诉你！＂．但你依然站在旁边只等馒头，此时的你，**是阻塞的**，**是异步的**．
阻塞表现在你除了等馒头，别的什么都不做了．异步表现在等馒头的过程中，阿梅提供电话通知＂馒头出炉＂的消息，你只需要等阿梅的电话．
4. 你去甜在心馒头店买太极馒头，阿梅说：＂暂时没，正在蒸呢，蒸好了我打电话告诉你！＂．于是你就走了，去买了双新球鞋，看了看武馆，
总之，从此不再过问馒头的事情，一心只等阿梅电话．此时的你，**是非阻塞的**，**是异步的**．非阻塞表现在你除了等馒头，
自己还干干别的时不时会主动问问馒头好没好．异步表现在等馒头的过程中，阿梅提供电话通知＂馒头出炉＂的消息，你只需要等阿梅的电话．

如果你仔细品过上面案例中的每一个字，你就能慢慢体会到之所以异步和非阻塞，同步和阻塞容易混淆，仅仅是因为二者的表现形式稍微有点儿相似而已．

阻塞和非阻塞关注的是：在等馒头的过程中，你在干啥．

同步和异步关注的是：等馒头这件事，你是一直等到＂馒头出炉＂的结果，还是立即跑路等阿梅告诉你的＂馒头出炉＂．重点的是你是如何得知＂馒头出炉＂的．

所以现实世界中，最傻的人才会采用异步阻塞的IO方式去写程序．其余三种方式，更多的人都会选择同步阻塞或者异步非阻塞．
同步非阻塞最大的问题在于，你需要不断在各个任务中忙碌着，导致你的大脑混乱，非常累．

#### 11 select系统调用

socket初探 --- 先从一个简单的socket服务器开始，依次讲解了三个逐渐进步的服务器：
- 只能服务于一个客户端的服务器
- 利用fork可以服务于多个客户端的服务器
- 利用预fork派生进程服务于多个客户端的服务器

最后一种服务器的进程模型基本上的大概原理其实跟我们常用的apache是非常相似的．

其实这种模型最大的问题在于需要根据实际业务预估进程数量，依旧是需要大量进程来解决问题，可能会出现CPU浪费在进程间切换上，
还有可能会出现惊群现象（简单理解就是100个进程在等带客户端连接，来了一个客户端但是所有进程都被唤醒了，但最终只有一个进程为这个客户端服务，
其余99个白白折腾），那么，有没有一种解决方案可以使得少量进程服务于多个客户端呢？

答案就是在 PHP socket初探 --- 关于IO的一些枯燥理论中提到的＂IO多路复用＂．多路是指多个客户端连接socket，复用就是指复用少数几个进程，
多路复用本身依然隶属于同步通信方式，只是表现出的结果看起来像异步，这点值得注意．目前多路复用有三种常用的方案，依次是：

- select，最早的解决方案
- poll，算是select的升级版
- epoll，目前的最终解决版，解决c10k问题的功臣

今天说的是select，这个东西本身是个Linux系统调用．在Linux中一切皆为文件，socket也不例外，
每当Linux打开一个文件系统都会返回一个对应该文件的标记叫做文件描述符．文件描述符是一个非负整数，当文件描述数达到最大的时候，
会重新回到小数重新开始（题外话：按照传统，一般情况下标准输入是0，标准输出是1，标准错误是2）．对文件的读写操作就是利用对文件描述符的读写操作．
一个进程可以操作的文件描述符的数量是有限制的，不同系统有不同的数量，在linux中，可以通过调整ulimit来调整控制．

先通过一个简单的例子说明下select的作用和功能．双11到了，你给少林足球队买了很多很多球鞋，分别有10个快递给你运送，
然后你就不断地电话询问这10个快递员，你觉得有点儿累．阿梅很心疼你，于是阿梅就说："这事儿你不用管了，你去专心练大力金刚腿吧，
等任何一个快递到了，我告诉你"．当其中一个快递来了后，阿梅就喊你：＂下来啦，有快递！＂，但是，这个阿梅比较缺心眼，
她不告诉你是具体哪双鞋子的快递，只告诉你有快递到了．所以，你只能依次查询一遍所有快递单的状态才能确认是哪个签收了．

上面这个例子通过结合术语演绎一遍就是，你就是服务器软件，阿梅就是select，10个快递就是10个客户端（也就是10个连接socket fd）．
阿梅负责替你管理着这10个连接socket fd，当其中任何一个fd有反应了也就是可以读数据或可以发送数据了，阿梅（select）就会告诉你有可以读写的fd了，
但是阿梅（select）不会告诉你是哪个fd可读写，所以你必须轮循所有fd来看看是哪个fd，是可读还是可写．

是时候机械记忆一波儿了：

当你启动select后，需要将三组不同的socket fd加入到作为select的参数，传统意义上这种fd的集合就叫做fd_set，
三组fd_set依次是可读集合，可写集合，异常集合．三组fd_set由系统内核来维护，每当select监控管理的三个fd_set中有可读或者可写或者异常出现的时候，
就会通知调用方．调用方调用select后，调用方就会被select阻塞，等待可读可写等事件的发生．一旦有了可读可写或者异常发生，
需要将三个fd_set从内核态全部copy到用户态中，然后调用方通过轮询的方式遍历所有fd，从中取出可读可写或者异常的fd并作出相应操作．
如果某次调用方没有理会某个可操作的fd，那么下一次其余fd可操作时，也会再次将上次调用方未处理的fd继续返回给调用方，
也就是说去遍历fd的时候，未理会的fd依然是可读可写等状态，一直到调用方理会．

上面都是我个人的理解和汇总，有错误可以指出，希望不会误人子弟．下面通过php代码实例来操作一波儿select系统调用．
在php中，你可以通过stream_select或者socket_select来操作select系统调用，下面演示socket_select进行代码演示：
```php
<?php
// BEGIN 创建一个tcp socket服务器
$host = '0.0.0.0';
$port = 9999;
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $listen_socket, $host, $port );
socket_listen( $listen_socket );
// END 创建服务器完毕 

// $client用来保存连接的客户端，也将监听socket放入到read fd set中去，因为select也要监听listen_socket上发生事件
$client = [ $listen_socket ];
// 先暂时只引入读事件，避免有同学晕头
$write = [];
$exp = [];

// 开始进入循环
while( true )
{
    // $read读取的客户端为连接的客户端
    $read = $client;
    // 当select监听到了fd变化，注意第四个参数为null
    // 如果写成大于0的整数那么表示将在规定时间内超时
    // 如果写成等于0的整数那么表示不断调用select，执行后立马返回，然后继续
    // 如果写成null，那么表示select会阻塞一直到监听发生变化
    /*
    第一个参数必须是数组,数组里面含有待检测的套接字,而且第四个参数写成null阻塞就是代表程序就一直停在socket_select这个函数上,
    什么都不干,等你有连接或者有数据发送,我才继续执行,所以你使用var_dump后,再进行telnet 才会有返回值,否则没有任何输出的
    */
    if ( socket_select( $read, $write, $exp, null ) > 0 ) {
        // 判断listen_socket有没有发生变化，如果有就是有客户端发生连接操作了。刚开始把$listen_socket放入了$client，$client又放入了$read
        if (in_array( $listen_socket, $read )) {
            // 将客户端socket加入到客户端连接client数组中
            // socket_accept创建一个可用套接字传送数据，后面准备给其他客户端发送数据用的
            $client_socket = socket_accept( $listen_socket );
            //下面这句很有用,避免了  unset( $read[ $key ] )后,在while时,客户端进来再次用  $client赋值给$read
            $client[] = $client_socket;
            // 然后将listen_socket从read中去除掉
            $key = array_search( $listen_socket, $read );
            unset( $read[ $key ] );
        }
        // 查看去除listen_socket中是否还有client_socket
        // 已经进行telnet连接后,会直接走这一步,不会进去上面代码的in_array。第一个客户端连接第一次到这里$read数为0，第二次循环执行到这里$read中为其自身的连接
        // 或者其他后续的客户端连接发生了连接，会执行到这里
        if ( count( $read ) > 0 ) {
            $msg = 'hello world';
            // 循环监听的所有客户端连接
            foreach( $read as $socket_item )
            {
                // 从可读取的客户端连接fd中读取出来数据内容，然后发送给其他客户端
                $content = socket_read( $socket_item, 2048 );
                // 循环client数组，将内容发送给其余所有客户端
                foreach ( $client as $client_socket )
                {
                    // 因为client数组中包含了 listen_socket 以及当前发送者自己socket，$client_socket != $socket_item 再次排除自已,所以需要排除二者
                    if( $client_socket != $listen_socket && $client_socket != $socket_item ){
                        socket_write( $client_socket, $content, strlen( $content ) );
                    }
                }
            }
        }
    } else {
        // 当select没有监听到可操作fd的时候，直接continue进入下一次循环
        continue;
    }
}
```

将文件保存为server.php，然后执行php server.php运行服务，同时再打开三个终端，执行telnet 127.0.0.1 9999，
然后在任何一个telnet终端中输入"I am xiaoming!"，再看其他两个telnet窗口，是不是感觉很屌？

不完全截图图下：

![](/blog/images/20200921/20200921002106.png)

还没意识到问题吗？如果我们看到有三个telnet客户端连接服务器并且可以彼此之间发送消息，但是我们只用了一个进程就可以服务三个客户端，
如果你愿意，可以开更多的telnet，但是服务器只需要一个进程就可以搞定，这就是IO多路复用diao的地方！

最后，我们重点解析一下 socket_select 函数，我们看下这个函数的[原型](https://www.php.net/socket_select)：

```php
int socket_select ( array &$read , array &$write , array &$except , int $tv_sec [, int $tv_usec = 0 ] )
```

值得注意的是$read，$write，$except三个参数前面都有一个&，也就是说这三个参数是引用类型的，是可以被改写内容的．在上面代码案例中，
服务器代码第一次执行的时候，我们要把需要监听的所有fd全部放到了read数组中，然而在当系统经历了select后，这个数组的内容就会发生改变，
由原来的全部read fds变成了只包含可读的read fds，这也就是为什么声明了一个client数组，然后又声明了一个read数组，然后read = client．
如果我们直接将client当作socket_select的参数，那么client数组内容就被修改．假如有5个用户保存在client数组中，只有1个可读，
在经过socket_select后client中就只剩下那个可读的fd了，其余4个客户端将会丢失，此时客户端的表现就是连接莫名其妙发生丢失了．

#### 12 颤颤抖抖开篇epoll（一）

正如标题所言，颤颤抖抖开篇epoll。颤颤抖抖的原因大概也就是以前几乎没有亲自“手刃”epoll的经验，仅仅靠epoll的理论知识骗吃骗喝骗人事哄小孩儿装高手，
现如今，没有了大师兄的铁头功照顾，没有了六师弟的轻功水上漂背，没有了阿梅的太极功护身，不得不自己个儿当一次排头兵了。

说到底，还是因为自己虚。

先立个flag，那就是epoll比select牛逼，尽管select是POSIX标准。即便是select的高配版本poll，也比epoll差太多太多。
网络如此发达的今天，epoll是解决c10k问题的功臣，这是没有办法的事情。epoll虽然是后出生的，但是却有着与生俱来的高傲，
就像王思聪；select就是普通屌丝，花点儿钱使劲装扮自己也顶多就是个poll。这poll和epoll，可差一个e呢，没办法，与生俱来的差距。

坊间传闻，在epoll出世前，QQ用户量剧增，但是select以及select的高配版本poll都无法解决他们的问题，于是乎QQ当年的服务器就不得不用UDP协议来避规这个问题，
一直到后来有了epoll，QQ开始逐步在PC客户端中的配置项中允许用户选择UDP服务器或TCP服务器。

还是通过浅显的示例来说明下为啥epoll比select厉害（这个例子在前面文章中应该提过，今儿再回放一遍）。

你要去继续练习大力金刚腿，阿梅还是要替你收双十一的10个快递。为了方便自己记忆这些快递，你把十个快递记录到了一个清单上给了阿梅。
但这个时候阿梅显然不太清楚怎么应付这场景，于是每当收到X个快递，阿梅都是直接把快递清单抄写一份再拿给你并告诉你：“有快递来了！”，
至于来了几个快递以及是分别是哪个镖局护送的，阿梅是不会告诉你的。于是只能是你自己，把单子上的10个快递逐次和收到的对比一遍，
然后对比完毕后再把这个单子给了阿梅，然后阿梅继续等。

又是一年双十一，阿梅这次学聪明了，经历过那场球赛后，她已经得到了自我，实现了人生价值，今年的阿梅是一个全新的阿梅，一个剃了光头的阿梅。

你要去继续练习大力金刚腿，阿梅还是要替你收双十一的10个快递。为了方便自己记忆这些快递，你把十个快递记录到了一个清单上给了阿梅。
但这个时候的阿梅显然已经得到了自我，是升华了的阿梅，于是每收到X个（ X >= 1 ）快递，阿梅都会在冲你喊一句：“顺丰镖局大师兄的铁头套，
圆通镖局六师弟的鸡蛋到了！”，而你，不用再去依次对单子，阿梅会直接告诉你是哪个镖局护送的哪个快递，然后她还会按照你提前告诉她的“如果收到鸡蛋就给六师弟，
收到铁头套就给大师兄”。哪怕你买了10000个快递，阿梅照样四两拨千斤，太极功夫收快递，而你，只需要安静的练习大力金刚腿。

剃光头前的阿梅，就是select，不敢正眼看老板娘一眼。
剃光头后的阿梅，就是epoll，可徒手接魔鬼队的死亡之球。

快递就相当于是socket fd，包括监听socket和连接socket；那个清单就是fd的集合；阿梅就是select或者epoll；你就是当前的一个进程；
某个快递到了，就相当于是某个fd已经可读或可写。

select虽然一定程度上解决了一个进程可以读写多个fd的问题，但是select有如下致命缺点：
- 默认情况下，select可管理的fd的数量是1024个（阿梅最多帮你收1024个快递）
- select每次检测到fd集合中有可读写的fd时，它会把整个fd全部复制一遍给你，然后你自己再去逐个轮询究竟是哪个fd可读写
- 正如以上所说，它会把整个fd全部复制给你（她把整个清单抄了一份给你），从术语上讲，这个过程是将fd从内核态复制一遍给用户态的调用进程
- 正如以上所说，你自己逐个轮询所有fd才能知道究竟是哪个可读写（反正就是有快递来了，来了几个都是谁你自己个儿对着清单查去）
- 你自己个轮询的过程是线性的，如果有个n个fd，那么时间复杂度一定是O(n)

而epoll则拥有更加专业的高端大气上档次的技能指标：
- 理论上可以搞定无上限的fd（可以收无数个快递的阿梅）
- 只挑出可读写（其实严格意义上还有异常）的活跃的fd，其余的fd不理会
- 使用MMAP加速内核态数据拷贝

除此之外，需要特殊指出的是，epoll本身的两种模式：
- 水平触发。这种方式下，如果监听到了有X个事件发生，那么内核态会将这些事件拷贝到用户态，但是可惜的是，如果用户只处理了其中一件，
剩余的X-1件出于某种原因并没有理会，那么下次的时候，这些未处理完的X-1个事件依然会从内核态拷贝到用户态。这样做是有阴阳两面的，
阳面是事件安全的不会发生丢失，阴面是对于性能来说是一种浪费。其实这个时候的epoll颇有些类似于poll的工作方式。
- 边缘触发。这种方式下，是鸡血版本的epoll，是释放自我的epoll，也是应该是正确的使用方式。这种情况下，如果发生了X个事件，
然而你只处理了其中1个事件，那么剩余的X-1个事件就算“丢失”了。性能是上去了，与之俱来的就是可能的事件丢失。

那么，你以为是时候写代码演示epoll了，然而并不是，原因有两个：
- 通过C语言可以直接操作epoll，但是，为了避免装逼失败，我决定不用C来演示（放到后面再深入的时候）
- 如果说通过PHP来操作，我不得不提一件悲催的事情，据我自己得到的经验告诉我，那就是PHP无法直接操控epoll，而是要通过操作libevent来搞定epoll。

那么，什么是Libevent呢？怎么听着好耳熟，不光耳熟，你看下下图，是不是还有点儿眼熟？没错，
这的博客的前端页面就是抄的[Libevent官网](http://libevent.org/ "Libevent官网")的。

我先从Libevent官网抄袭一段话：
“Currently, libevent supports /dev/poll, kqueue(2), event ports, POSIX select(2), Windows select(), poll(2), and epoll(4). ”，
你就能大概知道Libevent是干啥的了。大概意思就是Libevent对/dev/poll、Mac中的kqueue、select、poll以及epoll的API进行了封装，
屏蔽了这几个多路复用开发上的一些细节和不同点，对外提供统一的API的一个高性能网络事件库。

额外提醒一点，这个东西是用C语言编写的，几十年过去了，你大爷还是你大爷。

回到正路上来，就是“PHP中如何使用Libevent”。在pecl.php.net上，有两个扩展都可以使phper方便地操控libevent，
一个就叫libevent，另一个叫做event，推荐大家用后者。前者不知道什么原因版本一直停留在0.10 Beta状态，开发日期则停留在了2013-05-22日，
我没怎么试过，估计可能不支持php7，不过，还是要感谢开发者。event扩展就比较屌了，版本迭代不错，看起来开发者挺积极的，也支持php7，
目前的稳定版本是2.3.0，所以推荐大家使用event扩展。

正好在此补充一下php扩展的安装方式，以event扩展为例。

- 下载event 2.3.0的稳定版本，wget https://pecl.php.net/get/event-2.3.0.tgz

![](/blog/images/20200921/20200921002107.png)

- 解压tgz源码包，tar -zxvf event-2.3.0.tgz

![](/blog/images/20200921/20200921002108.png)

- cd event-2.3.0进入到主目录中，然后执行phpize，再执行./configure

![](/blog/images/20200921/20200921002109.png)


- 执行make

![](/blog/images/20200921/20200921002110.png)


- 执行make install安装

![](/blog/images/20200921/20200921002111.png)


- 配置php的cli环境配置文件，注意不是apache2，也不是fpm的，而是cli的php.ini，添加一句:`extension = '/usr/lib/php/20151012/event.so'`，
然后在终端中执行php -m看下，是不是有event呢？

好了，今天到这里正式收官，下一篇继续嗑php和他的event扩展二三事！

#### 13 硬着头皮继续libevent（二）

实际上php.net上是有event扩展的使用说明手册，但是呢，对于初学者来说却并没有什么卵用，因为没有太多的强有力使用案例代码，
也没有给力的User Contributed Notes，所以可能造成的结果就是：根本就看不懂。

这就是event文档，[点击这里](http://php.net/manual/en/book.event.php "点击这里")，你们可以感受一下。从文档上看，
event扩展一共实现了如下图几个基础类，其中最常用重要的就是Event和EventBase以及EventConfig三个类了，所以，先围绕这三位开展一下工作。

考虑到你们、我、还有正在看这个文章的其他未知物种，大多数可能并不是搞C语言的老兵油子，所以我得用一些可能并不恰当的案例和比喻来尝试引入这些概念。

libevent中有五个字母是event，实际上就是说“event才是王道”。

Event类就是产生各种不同类型事件的产出器，比如定时器事件、读写事件等等，为了提升民族荣誉感，我们将这些各种事件比作各种战斗机：比如歼10、歼15和歼20。

EventBase类就相对容易介入了，这玩意显然就是一个航空母舰了，为了提升民族荣誉感，我们就把EventBase类当作是辽宁舰。
各种Event都必须依靠EventBase才能混口饭吃，这和战斗机有辽宁舰才有底气飞的更高更远是一个道理。一定是先有航母（EventBase），
其次是战斗机（Event）挂在航母（EventBase）上。

EventConfig则是一个配置类，实例化后的对象作为参数可以传递给EventBase类，这样在初始化EventBase类的时候会根据这个配置初始化出不同的EventBase实例。
类比的话，这个类则有点儿类似于辽宁舰的舰岛，可以配置指挥整个辽宁舰。航空母舰的发展趋势是不需要舰岛的，
同样，在实例化EventBase类时候同样也可以不传入EventConfig对象，直接进行实例化也是没有问题的。

下面我们从开始写一个php定时器来步入到代码的节奏中。定时器是大家常用的一个工具，一般phper一说定时器，脑海中第一个想起的绝逼是Linux中的crontab。
难道phper们离开了crontab真的就没法混了吗？是的，真的好羞耻，现实告诉我们就是这样的，他们离开了crontab真的就没法混了。
那么，是时候通过纯php来搞一波儿定时器实现了！

注意是真的纯php，连Event扩展都不用的那种。
```php
<?php
// 给当前php进程安装一个alarm信号处理器
// 当进程收到alarm时钟信号后会作出动作
pcntl_signal( SIGALRM, function(){
  echo "tick.".PHP_EOL;
} );
// 定义一个时钟间隔时间，1秒钟吧
$tick = 1;
while( true ){
  // 当过了tick时间后，向进程发送一个alarm信号
  pcntl_alarm( $tick );
  // 分发信号，呼唤起安装好的各种信号处理器
  pcntl_signal_dispatch();
  // 睡个1秒钟，继续
  sleep( $tick );
}
```
代码保存成timer.php，然后php timer.php运行下，如果不出问题应该能跑起来。但是吧，这个代码有一坨问题。
- 首先是性能一般（ 但是，比使用declare(ticks=1)还是要好不少的 ）
- 其次是代码量确实短小，短小的都让人怀疑：这特么玩意能用？
- 最后是即便我硬着头皮用，但这玩意只能精确到秒级，逗我？

所以，为了解决以上问题，是时候操作一波儿Event扩展了！
```php
<?php
// 初始化一个EventConfig（舰岛），虽然是个仅用于演示的空配置
$eventConfig = new EventConfig();
// 根据EventConfig初始化一个EventBase（辽宁舰，根据舰岛配置下辽宁舰）
$eventBase = new EventBase( $eventConfig );
// 初始化一个定时器event（歼15，然后放到辽宁舰机库中）
$timer = new Event( $eventBase, -1, Event::TIMEOUT | Event::PERSIST, function(){
  echo microtime( true )." : 歼15，滑跃，起飞！".PHP_EOL;
} );
// tick间隔为0.05秒钟，我们还可以改成0.5秒钟甚至0.001秒，也就是毫秒级定时器
$tick = 0.05;
// 将定时器event添加（将歼15拖到甲板加上弹射器）
$timer->add( $tick );
// eventBase进入loop状态（辽宁舰！走你！）
$eventBase->loop();
```
将代码保存为tick.php，然后php tick.php执行一下，如下图所示：

![](/blog/images/20200921/20200921002112.jpeg)

这种定时器是持久的定时器（每隔X时间一定会执行一次），如果想要一次性的定时器（隔X时间后就会执行一次，执行过后再也不执行了），
那么将上述代码中的`Event::TIMEOUT | Event::PERSIST`修改为`Event::TIMEOUT`即可。

如果你有一些自定义用户数据传递给回调函数，可以利用new Event()的第五个参数，这五个参数可以给回调函数用，如下所示：
```php
<?php
$timer = new Event( $eventBase, -1, Event::TIMEOUT | Event::PERSIST, function() use( &$custom ){
  //echo microtime( true )." : 歼15，滑跃，起飞！".PHP_EOL;
  print_r( $custom );
}, $custom = array(
  'name' => 'woshishui',
) );
```

需要重点说明的是new Event()这行代码了，我把原型贴过来给大家看下：
```php
public Event::__construct ( EventBase $base , mixed $fd , int $what , callable $cb [, mixed $arg = NULL ] )
```
- 第一个参数是一个eventBase对象即可
- 第二个参数是文件描述符，可以是一个监听socket、一个连接socket、一个fopen打开的文件或者stream流等。如果是时钟时间，
则传入-1。如果是其他信号事件，用相应的信号常量即可，比如SIGHUP、SIGTERM等等
- 第三个参数表示事件类型，依次是Event::READ、Event::WRITE、Event::SIGNAL、Event::TIMEOUT。其中，加上Event::PERSIST则表示是持久发生，
而不是只发生一次就再也没反应了。比如`Event::READ | Event::PERSIST`就表示某个文件描述第一次可读的时候发生一次，后面如果又可读就绪了那么还会继续发生一次。
- 第四个参数就熟悉的很了，就是事件回调了，意思就是当某个事件发生后那么应该具体做什么相应
- 第五个参数是自定义数据，这个数据会传递给第四个参数的回调函数，回调函数中可以用这个数据。

通过以上的案例代码可以总结一下日常流程：
1. 创建EventConfig（非必需）
2. 创建EventBase
3. 创建Event
4. 将Event挂起，也就是执行了Event对象的add方法，不执行add方法那么这个event对象就无法挂起，也就不会执行
5. 将EventBase执行进入循环中，也就是loop方法

捋清楚了定时器代码，我们尝试来解决一个信号的问题。比如我们的进程是常驻内存的daemon，在接收到某个信号后就会作出相应的动作，
比如收到term信号后进程就会退出、收到usr1信号就会执行reload等等。

```php
<?php
// 依然是照例行事，尽管暂时没什么实际意义上的配置
$eventConfig = new EventConfig();
// 初始化eventBase
$eventBase = new EventBase( $eventConfig );
// 初始化event
$event = new Event( $eventBase, SIGTERM, Event::SIGNAL, function(){
  echo "signal come.".PHP_EOL;
} );
// 挂起event对象
$event->add();
// 进入循环
echo "进入循环".PHP_EOL;
$eventBase->loop();
```

将代码保存成tick.php，然后执行php tick.php，代码已经进入循环了，然后我们打开另外一个终端，
输入`ps aux|grep tick`查看一个php进程的pid进程号，对这个进程发送term信号，如下图所示：

![](/blog/images/20200921/20200921002113.jpeg)

![](/blog/images/20200921/20200921002114.jpeg)

奇怪啊，从第一张图看到确实收到term信号了，但是很奇怪为什么这个php进程退出了呢？是因为没有添加Event::PERSIST，修改如下代码如下：
```php
<?php
$event = new Event( $eventBase, SIGTERM, Event::SIGNAL | Event::PERSIST, function(){
  echo "signal come.".PHP_EOL;
} );
```

可能还有一些同学觉得php进程退出就退出,收到kill就退出,好正常,你仔细看下,tick.php进程只要求收到一个term就echo输出,并没有说规定进程退出,
而且使用kill要看发送什么信号的,kill -15,这个参数通过kill -l查看多种信号值,-15是属于SIGTERM

```shell
root@jingmian:/# kill -l
 1) SIGHUP     2) SIGINT     3) SIGQUIT     4) SIGILL     5) SIGTRAP
 6) SIGABRT     7) SIGBUS     8) SIGFPE     9) SIGKILL    10) SIGUSR1
11) SIGSEGV    12) SIGUSR2    13) SIGPIPE    14) SIGALRM    15) SIGTERM
16) SIGSTKFLT    17) SIGCHLD    18) SIGCONT    19) SIGSTOP    20) SIGTSTP
21) SIGTTIN    22) SIGTTOU    23) SIGURG    24) SIGXCPU    25) SIGXFSZ
26) SIGVTALRM    27) SIGPROF    28) SIGWINCH    29) SIGIO    30) SIGPWR
31) SIGSYS    34) SIGRTMIN    35) SIGRTMIN+1    36) SIGRTMIN+2    37) SIGRTMIN+3
38) SIGRTMIN+4    39) SIGRTMIN+5    40) SIGRTMIN+6    41) SIGRTMIN+7    42) SIGRTMIN+8
43) SIGRTMIN+9    44) SIGRTMIN+10    45) SIGRTMIN+11    46) SIGRTMIN+12    47) SIGRTMIN+13
48) SIGRTMIN+14    49) SIGRTMIN+15    50) SIGRTMAX-14    51) SIGRTMAX-13    52) SIGRTMAX-12
53) SIGRTMAX-11    54) SIGRTMAX-10    55) SIGRTMAX-9    56) SIGRTMAX-8    57) SIGRTMAX-7
58) SIGRTMAX-6    59) SIGRTMAX-5    60) SIGRTMAX-4    61) SIGRTMAX-3    62) SIGRTMAX-2
63) SIGRTMAX-1    64) SIGRTMAX    
```

有些心眼多鸡贼的，IO多路复用的方法一共有三个select、poll和epoll（Mac下叫做kqueue），那么我们当前的event扩展用的是哪个方法呢？那么，再表演一波儿：
```php
<?php
// 查看当前系统平台支持的IO多路复用的方法都有哪些？
$method = Event::getSupportedMethods();
print_r( $method );
// 查看当前用的方法是哪一个？
$eventBase = new EventBase();
echo "当前event的方法是：".$eventBase->getMethod().PHP_EOL;
// 跑了许久龙套的config这次也得真的露露手脚了
$eventConfig = new EventConfig;
// 避免使用方法kqueue
$eventConfig->avoidMethod('kqueue');
// 利用config初始化event base
$eventBase = new EventBase( $eventConfig );
echo "当前event的方法是：".$eventBase->getMethod().PHP_EOL;
```
将代码保存了，然后执行一下，可以看到结果如下图所示：

![](/blog/images/20200921/20200921002115.jpeg)

那么，还有一些更鸡贼的人继续发问，前面提到的边缘触发和水平触发，如何确认呢？既然都用上epoll或者kqueue了，就一定要用边缘触发。
```php
<?php
$base = new EventBase();
echo "特性：".PHP_EOL;
$features = $base->getFeatures();
// 看不到这个判断条件的，请反思自己“位运算”相关欠缺
if( $features & EventConfig::FEATURE_ET ){
  echo "边缘触发".PHP_EOL;
}
if( $features & EventConfig::FEATURE_O1 ){
  echo "O1添加删除事件".PHP_EOL;
}
if( $features & EventConfig::FEATURE_FDS ){
  echo "任意文件描述符，不光socket".PHP_EOL;
}
```

运行结果如下图所示：

![](/blog/images/20200921/20200921002116.jpeg)

小小装个逼总结一下，今儿这些个内容就是讲述event的基础三大类，下个篇章依然是围绕这三个家伙和IO操作结合到一起。


#### 14 PHP socket初探 --- 含着泪也要磕完libevent（三）

其实在上一篇libevent文章中 《PHP socket初探 --- 硬着头皮继续libevent（二）》，如果你总结能力很好的话，
可以观察出来我们尝试利用libevent做了至少两件事情：
- 毫秒级别定时器
- 信号监听工具

大家都是码php的，也喜欢把自己说的洋气点儿：“ 我是写服务器的 ”。所以，今天的第一个案例就是拿libevent来构建一个简单粗暴的http服务器：

```php
<?php
$host = '0.0.0.0';
$port = 9999;
$listen_socket = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $listen_socket, $host, $port );
socket_listen( $listen_socket );

echo PHP_EOL.PHP_EOL."Http Server ON : http://{$host}:{$port}".PHP_EOL;

// 将服务器设置为非阻塞，此处概念可能略拐弯，建议各位查阅一下手册
socket_set_nonblock( $listen_socket );
// 创建事件基础体，还记得航空母舰吗？
$event_base = new EventBase();
// 创建一个事件，还记得歼15舰载机吗？我们将“监听socket”添加到事件监听中，触发条件是read，
// 也就是说，一旦“监听socket”上有客户端来连接，就会触发这里，我们在回调函数里来处理接受到新请求后的反应
$event = new Event( $event_base, $listen_socket, Event::READ | Event::PERSIST, function( $listen_socket ) {
    // 为什么写成这样比较执拗的方式？因为，“监听socket”已经被设置成了非阻塞，这种情况下，accept是立即返回的，
    // 所以，必须通过判定accept的结果是否为true来执行后面的代码。一些实现里，包括workerman在内，
    // 可能是使用@符号来压制错误，个人不太建议这样做
    if( ( $connect_socket = socket_accept( $listen_socket ) ) != false){
        echo "有新的客户端：".intval( $connect_socket ).PHP_EOL;
        $msg = "HTTP/1.0 200 OK\r\nContent-Length: 2\r\n\r\nHi";
        socket_write( $connect_socket, $msg, strlen( $msg ) );
        socket_close( $connect_socket );
    }
}, $listen_socket );
$event->add();
$event_base->loop();
```

将代码保存为 test.php，然后`php test.php`运行起来。再开一个终端，使用curl的GET方式去请求服务器，效果如下：

![](/blog/images/20200921/20200921002117.jpeg)

这是一个非常非常简单地不能再简单的http demo了，对于一个完整的http服务器而言，他还差比较完整的http协议的实现、多核CPU的利用等等。
这些，我们会放到后面继续深入的文章中开始细化丰富。

还记得我们使用select系统调用实现了一个粗暴的在线聊天室，select这种业余的都敢出来混个聊天室，专业的绝对不能怂。

无数个专业👍👍👍👍👍👍👍👍👍👍👍👍👍👍👍送给libevent！

啦啦啦啦，开始码：
```php
<?php
$host = '0.0.0.0';
$port = 9999;
$fd = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $fd, $host, $port );
socket_listen( $fd );
// 注意，将“监听socket”设置为非阻塞模式
socket_set_nonblock( $fd );

// 这里值得注意，我们声明两个数组用来保存 事件 和 连接socket
$event_arr = []; 
$conn_arr = []; 

echo PHP_EOL.PHP_EOL."欢迎来到ti-chat聊天室!发言注意遵守当地法律法规!".PHP_EOL;
echo "        tcp://{$host}:{$port}".PHP_EOL;

$event_base = new EventBase();
$event = new Event( $event_base, $fd, Event::READ | Event::PERSIST, function( $fd ){
    // 使用全局的event_arr 和 conn_arr
    global $event_arr,$conn_arr,$event_base;
    
    // 非阻塞模式下，注意accpet的写法会稍微特殊一些。如果不想这么写，请往前面添加@符号，不过不建议这种写法
    if ( ( $conn = socket_accept( $fd ) ) != false ) {
        echo date('Y-m-d H:i:s').'：欢迎'.intval( $conn ).'来到聊天室'.PHP_EOL;
        
        // 将连接socket也设置为非阻塞模式
        socket_set_nonblock( $conn );
        
        // 此处值得注意，我们需要将连接socket保存到数组中去
        $conn_arr[ intval( $conn ) ] = $conn;
        
        $event = new Event( $event_base, $conn, Event::READ | Event::PERSIST, function( $conn )  { 
            global $conn_arr;
            $buffer = socket_read( $conn, 65535 );
            foreach( $conn_arr as $conn_key => $conn_item )
            {
                if ( $conn != $conn_item ) {
                    $msg = intval( $conn ).'说 : '.$buffer;
                    socket_write( $conn_item, $msg, strlen( $msg ) );
                }   
            }   
        }, $conn );
        
        $event->add();
        
        // 此处值得注意，我们需要将事件本身存储到全局数组中，如果不保存，连接会话会丢失，也就是说服务端和客户端将无法保持持久会话
        $event_arr[ intval( $conn ) ] = $event;
    }
}, $fd );
$event->add();
$event_base->loop();
```
将代码保存为server.php，然后php server.php运行，再打开其他三个终端使用telnet连接上聊天室，运行效果如下所示：

![](/blog/images/20200921/20200921002118.jpeg)

尝试放一张动态图试试，看看行不行，自己制作的gif都特别大，不知道带宽够不够。

![](/blog/images/20200921/20200921002119.gif)

截止到这篇为止，死磕Libevent系列的大体核心三把斧就算是抡完了，弄完这些，你在遇到这些代码的时候，就应该不会一脸楞逼了。

#### 15 PHP socket初探 --- 一些零碎细节的拾漏补缺（一）

前面可以说是弄了一系列的php socket和多进程的一大坨内容，知识浅显、代码粗暴、风格简陋，总的说来，还是差了一些细节。今天，就一些漏掉的细节补充一下。

1. 一些有志青年可能最近手刃了Workerman源码，对于里面那一大坨stream_select()、stream_socket_server()表示疑惑，
这个玩意和socket_create、socket_set_nonblock()有啥区别？其实，php官方手册里也提到过一嘴，socket系函数就是基于BSD Socket那一套玩意搞的，
几乎就是将那些东西简单包装了一下直接抄过来用的，抄到甚至连名字都和C语言操控socket的函数一模一样，
所以说socket系函数是一种比较低级（Low-Level，这里的低级是指软件工程中分层中层次的高低）socket操控方式，
可以最大程度给你操作socket的自由以及细腻度。在php中，socket系本身是作为php扩展而体现的，这个你可以通过php -m来查看有没有socket，
这件事情意味着有些php环境可能没有安装这个扩展，这个时候你就无法使用socket系的函数了。但stream则不同了，这货是内建于php中的，
除了能处理socket网络IO外，还能操控普通文件的打开写入读取等，stream系将这些输入输出统一抽象成了流，通过流来对待一切。
有人可能会问二者性能上差距，但是本人没有测试过，这个我就不敢轻易妄言了，但是从正常逻辑上推演的话，应该不会有什么太大差距之类的。

2. 一定要分清楚监听socket和连接socket，我们服务器监听的是监听socket，然后accept一个客户端连接后的叫做连接socket。

3. 关于“异步非阻塞”，这五个字到底体现在哪儿了。swoole我就不说了，我源码也才阅读了一小部分，我就说Workerman吧，
它在github上称：“Workerman is an asynchronous event driven PHP framework with high performance for easily building fast, 
scalable network applications.”，看到其中有asynchronous（异步）的字样，打我脸的是我并没有看到有non-block（非阻塞）的字样，
不过无妨，脸什么的不重要，重要的是我文章里那一坨又一坨的代码里哪里体现了非阻塞、哪里体现了异步。来吧，看代码吧。

看代码前，你要理解异步和非阻塞的区别是什么，因为这二者在表现结果上看起来是有点儿相似的，如果你没搞明白，
那么一定要通过这个来理解一下《PHP socket初探 --- 关于IO的一些枯燥理论》。

 ```php
<?php
// 创建一个监听socket，这个一个阻塞IO的socket
$listen = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $listen, '0.0.0.0', 9999 );
socket_listen( $listen );
while( true )
{
     // socket_accept也是阻塞的，虽然有while，但是由于accpet是阻塞的，所以这段代码不会进入无限死循环中
     $connect = socket_accept( $listen );
     if( $connect ){
       echo "有新的客户端".PHP_EOL;
     } else {
       echo "客户端连接失败".PHP_EOL;
     }
}
 ```
 
将上面代码保存了运行一下，然后用telnet可以连接上去。但是，这段代码中有两处是阻塞的，最主要就是监听socket是阻塞的。
那么，非阻塞的监听socket会是什么感受？

 ```php
<?php
// 创建一个监听socket，将其设置为非阻塞
$listen = socket_create( AF_INET, SOCK_STREAM, SOL_TCP );
socket_bind( $listen, '0.0.0.0', 9999 );
socket_listen( $listen );
// ⚠️⚠️⚠️⚠️⚠️⚠️ 这里设置非阻塞！
socket_set_nonblock( $listen );
while( true ){
    $connect = socket_accept( $listen );
    if ( $connect ) {
        echo "有新的客户端".PHP_EOL;
    } else {
        echo "客户端连接失败".PHP_EOL;
    }
}
```

将代码保存了运行一下，必定满屏的输出：“客户端连接失败”。

来来来，分析一波儿，为啥会出现这种现象。因为监听socket被设置成了非阻塞，我们知道非阻塞就是程序立马返回，然后再过段时间回来询问，
用例子就是“等馒头过程中，看下微博，抬头问馒头好了吗？然后看下微信，抬头问馒头好了吗？然后看下v2ex，抬头问馒头好了吗？。。。 。。。”，
这样你是不是就能理解了？因为并没有客户端连接进来，所以每当询问一次socket_accept后得到的反馈都是“没有连接”，
所以就直接走到“客户端连接失败”的分支中去了，而且是不断的不停的。这个时候，你用htop或者top命令查看服务器CPU，不出意外应该是100%，
这是非阻塞的极大缺点。

紧接着是异步呢？异步体现在哪儿了？我们说异步，是你去阿梅那里买馒头，阿梅告诉你说“馒头还没好，你去干别的吧，好了我打电话通知你”，
然后你就专心去打游戏去了，直到电话响了你去拿馒头。Workerman的异步更多是体现在对一个完整请求的处理流上，而不是正儿八经的异步的定义概念，
如果你没听明白，那也可能正常，慢慢理解。最后，我补充一句：epoll是同步的，而不是异步。

#### 16 swoole的协程是个什么鬼

swoole的用法实际上对于大多数新手来说一直并不怎么友好，其实这不怪swoole，只能怪萌新们确实底子不够，有些东西理解起来可能真的比较困难。
今天斗胆尝试引入一个应用场景和简单的代码案例来做个简单的入门，算是抛砖头引和田玉吧。

老韩wiki.swoole.com以及一些社区中一直说swoole既可以同步又可以异步，我找一些原话，你们感受一下：

> Swoole不仅支持异步，还支持同步。什么情况下使用同步，什么情况下使用异步。这里说明一下。
我们不赞成用异步回调的方式去做功能开发，传统的PHP同步方式实现功能和逻辑是最简单的，也是最佳的方案。像node.js这样到处callback，只是牺牲可维护性和开发效率。
但有些时候很适合用异步，比如FTP、聊天服务器，smtp，代理服务器等等此类以通信和读写磁盘为主，功能和业务逻辑其次的服务器程序。

继续引用凑行数：


异步的优势
- 高并发，同步阻塞IO模型的并发能力依赖于进程/线程数量，例如 php-fpm开启了200个进程，理论上最大支持的并发能力为200。
  如果每个请求平均需要100ms，那么应用程序就可以提供2000qps。异步非阻塞的并发能力几乎是无限的，可以发起或维持大量并发TCP连接
- 无IO等待，同步模型无法解决IOWait很高的场景，如上述例子每个请求平均要10s，那么应用程序就只能提供20qps了。
  而异步程序不存在IO等待，所以无论请求要花费多长时间，对整个程序的处理能力没有任何影响

同步的优势
- 编码简单，同步模式编写/调试程序更轻松
- 可控性好，同步模式的程序具有良好的过载保护机制，如在下面的情况异步程序就会出问题
- Accept保护，同步模式下一个TCP服务器最大能接受 进程数+Backlog 个TCP连接。一旦超过此数量，Server将无法再接受连接，
  客户端会连接失败。避免服务器Accept太多连接，导致请求堆积
    
最后的引用：

> swoole_http_server继承自swoole_server，是一个完整的http服务器实现。swoole_http_server支持同步和异步2种模式。
无论是同步模式还是异步模式，swoole_http_server都可以维持大量TCP客户端连接。同步/异步仅仅体现在对请求的处理方式上。
示例：
```php
<?php
$http = new swoole_http_server("127.0.0.1", 9501);
$http->on('request', function ($request, $response) {
    $response->end("<h1>Hello Swoole. #".rand(1000, 9999)."</h1>");
});
$http->start();
```

同步模式

这种模式等同于nginx+php-fpm/apache，它需要设置大量worker进程来完成并发请求处理。Worker进程内可以使用同步阻塞IO，
编程方式与普通PHP Web程序完全一致。

与php-fpm/apache不同的是，客户端连接并不会独占进程，服务器依然可以应对大量并发连接。

异步模式

这种模式下整个服务器是异步非阻塞的，服务器可以应对大规模的并发连接和并发请求。但编程方式需要完全使用异步API，
如MySQL、redis、http_client、file_get_contents、sleep等阻塞IO操作必须切换为异步的方式，
如异步swoole_client，swoole_event_add，swoole_timer，swoole_get_mysqli_sock等API。

个人认为最后这段引用是非常具备价值的，仔细品读或许能够从中得到一些感悟。
我在前面曾经写过一篇[swoole的进程模型 ](https://ibaiyang.github.io/blog/php/2020/06/30/PHP-%E5%A4%9A%E8%BF%9B%E7%A8%8B%E5%88%9D%E6%8E%A2.html#5-swoole%E7%9A%84%E8%BF%9B%E7%A8%8B%E6%A8%A1%E5%9E%8B "swoole的进程模型 ")，
实际上你可以这么理解，就是master进程可以hold住上万个TCP连接是没有任何问题的，因为master进程内部异步非阻塞的，
但是仅仅hold住上万个TCP连接本身是没有任何意义的，因为有数据传输的TCP连接才是有意义的。一旦有数据传输就意味着有业务逻辑产生了，
那么master进程并不负责具体业务逻辑代码了，处理这个业务逻辑的活儿交给worker进程来干，然后干完后再由master进程返回给客户端。

同步阻塞模式下，如果说worker进程1秒钟完成1个客户端的业务逻辑，尽管master进程同时hold住了1W个TCP连接，
但是1个worker进程只能服务于1个客户端，1W个客户端全部处理完毕，需要1W秒钟。所以，同步阻塞模式下，如果你想干活猛，
就只能增加worker进程的数量，比如1000个甚至2000个。当然了，看到这里有为青年就会提出问题了，
这样一味地增加进程数量岂不是意味着进程再多的话进程间切换都是极为耗费CPU的？是的，所以很简单，
横向扩展加机器就是了... ...或者，选择异步。

异步非阻塞模式下，这个时候除了master进程是异步非阻塞外，要求worker进程中的业务逻辑代码也得是异步非阻塞工作的方式。
也就说worker进程在处理1个客户端业务逻辑的时候，如果没处理完毕就会立马开始处理第2个客户端的业务逻辑，然后继续第3个... ...持续...
一旦某个客户端的业务逻辑处理完毕了就有回调通知，从此可以做到即便只有少量worker进程但依然可以维持高速高效地处理速度。
所以，这种情况，对编写业务逻辑代码就有了很高的要求了。假如业务逻辑就是“插入1条评论，然后返回最新5条评论”，用伪代码演示如下：

```php
<?php
// 你要创建异步的MySQL客户端，而不是普普通通的pdo mysqli
$async_mysql = new async_mysql();
$async_mysql->on( 'connect', function( $async_mysql ){
    echo '连接成功'.PHP_EOL;
    // 插入评论
    $sql = "insert into pinglun() values()";
    $async_mysql->query( $sql, function( $async_mysql, $result ) {
        // 如果插入成功
        if ( true == $result ) {
            // 获取5条最新评论
            $sql = "select * from pinglun limit 5";
            $async_mysql->query( $sql, function( $async_mysql, $result ){
                // 获取成功后拿数据
                if ( true == $result ) {
                    print_r( $result->fetchAll() );
                } else {
                    echo "获取失败".PHP_EOL;
                }
            });
        
        // 如果插入失败
        } else {
            echo "插入数据失败".PHP_EOL;
        }
    });
});
```

这种代码里，将不可避免地产生大量的类似于on这种回调，如果再有一些条件依赖话，可能不得不层层回调。比如插入最新评论需要依赖connect，
只有connect成功了才能执行插入操作，然后是查询最新5条评论功能依赖插入操作，只有插入操作成功才能继续查询5条最新评论。
最重要的是，需要IO操作的这些函数等等都必须得是异步的才行，传统的pdo、mysqli是统统不可以用的。因为只要有一处是同步阻塞了，
整个worker进程中的业务逻辑代码就算是彻底完蛋沦为同步阻塞了。所以说，如果你要在这种代码里用sleep( 100 )，你会死得惨烈。

“没有这金刚钻，别拦这瓷器活”...

如果说我们用传统的同步阻塞代码的话，伪代码大概如下你们感受一下：

```php
<?php
$pdo = new pdo();
try {
  $pdo->connect( $host, $port );
  $pdo->query( "insert into pinglun() values()" );
  $pdo->query( "select * from pinglun limit 5" );
} catch( Exception $e ) {
  throw new Exception('error.');
}
```

爱不爱？喜不喜欢？高不高兴？而且我还能任意写sleep... ...

当了这么多年的同步阻塞fpm（同步阻塞apache）的CURDer你跟我说你天生就爱异步？你猜我信么？

但是，异步带来的QPS上的提升实在是太明显了（注意，异步并不能提高性能，只是能提高QPS。性能就在那里躺着呢，
该是多少就是多少，只不过异步可以更好的挖掘和压榨，并不能提高TA），但异步的代码实在是难写，辣么，
有没有一种既可以用同步阻塞这种风格写的背后又是异步方式的方法呢？废话，当然有，不然我要这文章有何用？这种东西就是协程！

其实，有为青年在研究Golang的时候早就已经开眼见世界了，那是身经百战见的多了，但是像我这样的蠢货萌新自然是不知道的。
一些人用php的yield来实现协程，不过，我认为swoole的协程要比这个yield好很多。简单说起来，协程这个东西就是用户态的异步IO，
也就说不需要操作系统参与的，这点儿上和真正的异步IO的概念是不一样的。因为严格扣定义的话，异步IO是操作系统内核实现并参与的，
现在协程并不需要系统参与，仅仅用户层就可以解决这些问题。

废话不多说，还是通过代码来感受一下，这坨代码大概意思就是开了一个http服务器，开了一个worker进程，
worker进程中业务逻辑代码就是往数据库里添加一条记录，你们感受一下：

首先，注释掉同步阻塞传统代码，使用协程的写法；其次，注释掉协程写法，开启同步阻塞写法。然后分别使用ab进行简单测试
（apache bench简称ab,它是apache自带的压力测试工具。ab非常实用，它不仅可以对apache服务器进行网站访问压力测试，
也可以对或其它类型的服务器进行压力测试）。

- ab -n 5000 -c 100 -k http://127.0.0.1:9501/
- 只开了一个worker进程
- 数据表你们自己建吧，我就不贴出来了

```php
<?php
$server = new Swoole\Http\Server('127.0.0.1', 9501 );
$server->set( array(
  'worker_num' => 1,
) );
$server->on('Request', function($request, $response) {
    // 数据库插入一条数据
    $sql = "insert into user(`name`) values('iwejf')";

    // 下面这段是传统的同步阻塞写法
    /*  
    $dbh = new PDO('mysql:host=localhost;dbname=meshbox', 'root', 'root');    
    $dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);    
    $dbh->exec('set names utf8mb4');   
    $rs = $dbh->query($sql);
    */

    // 下面这段是协程的写法
    $mysql = new Swoole\Coroutine\MySQL();
    $res = $mysql->connect([
        'host' => '127.0.0.1',
        'user' => 'root',
        'password' => 'root',
        'database' => 'meshbox',
    ]); 
    $ret = $mysql->query( $sql );

    // 回应客户端ok
    $response->end("ok");
});
$server->start();
```

这里是协程的测试结果：

![](/blog/images/20200921/20200921002120.png)

这里是传统同步阻塞的测试结果：

![](/blog/images/20200921/20200921002121.png)

测试结果我们就不分析了，你们应该能看懂。这中间巨大的QPS差距你们应该能感受到了。
话说回来，由于我们知道想提高同步阻塞代码的QPS最有效的办法就是增加进程数量，因此我们将woker进程数量调整为8，再测试一把：

![](/blog/images/20200921/20200921002122.png)

继续调整为16：

![](/blog/images/20200921/20200921002123.png)

继续调整为32（接近协程的成绩，但依然差了1000QPS）：

![](/blog/images/20200921/20200921002124.png)

继续调整为64（终于超过单进程协程4080QPS的1600QPS了）：

![](/blog/images/20200921/20200921002125.png)

最终结果就是，我们用同步阻塞的模型开启了64个进程大概可以超越开启1个进程的协程方式将近1600QPS。

最后，部分有为青年可能想要了解swoole协程原理，我自己因为水准问题（其实我不懂）就不发表自己的看法了，
直接盗链官网资料了：<https://wiki.swoole.com/wiki/page/p-coroutine_realization.html>

#### 17 PHP中的yield（上）

其实，我并不是因为迭代或者生成器或者研究PHP手册才认识的yield，要不是协程，我到现在也不知道PHP中还有yield这么个鬼东西。
人家这个东西是从PHP 5.5就开始引入了，官方名称叫做生成器。你要说为什么5.5年代的东西，现在才拿出来。
我还想问你哟，PHP 5.3就有了的namespace为毛到最近这几年才开始正式投产。

那么，问题来了，这东西到底是有何用？

先来感受一个问题，给你100Kb的内存（是的，你没有看错，就是100Kb），然后让你迭代输出一个从1开始一直到10000的数组，步进为1。

预先迭代数组，必先创造数组。

所以，脑门一拍，代码一坨如下：

```php
<?php
$start_mem = memory_get_usage();
$arr = range( 1, 10000 );
foreach( $arr as $item ){
  //echo $item.',';
}
$end_mem = memory_get_usage();
echo " use mem : ". ( $end_mem - $start_mem ) .'bytes'.PHP_EOL; 
```

一顿操作猛如虎，运行一下成绩1-5，你们感受一下：

![](/blog/images/20200921/20200921002126.jpeg)

528440bytes，约莫就是528Kb，几乎是100Kb的五倍了，妈的这日子没法过了。

毕竟你们也知道，最近内存价格确实贵，国家也在号召低碳节能减排，你多耗费5倍内存，就意味着多排放5倍的二氧化碳，就意味着要为多用的内存多花钱贡献给棒子... ...你想想，那可是棒子。

人都是被逼出来的，于是yield可以来救场了，大概代码如下，注意看操作：

```php
<?php
$start_mem = memory_get_usage();
function yield_range( $start, $end ){
  while( $start <= $end ){
    $start++;
    yield $start;
  }
}
foreach( yield_range( 0, 9999 ) as $item ){
  echo $item.',';
}
$end_mem = memory_get_usage();
echo " use mem : ". ( $end_mem - $start_mem ) .'bytes'.PHP_EOL;
```

运行一下，你们感受一下：

![](/blog/images/20200921/20200921002127.jpeg)

首先，我们观察一下yield_range这个函数跟普通函数不一样的地方，就是普通函数往往都是使用return来返回结果，
而这个中则是yield。其次是普通函数中return只能返回一次，这个yield能返回好多次。

那么，我们来分析一波儿这个神奇的yield_range函数。这个yield关键字到底返回的是什么？我们简单看一下：

```php
<?php
function yield_range( $start, $end ){
  while( $start <= $end ){
    $start++;
    yield $start;
  }
}
$rs = yield_range( 1, 100 );
var_dump( $rs );
/*
object(Generator)#1 (0) {
}
*/
```

yield返回的是一个叫做Generator（中文名就是生成器）的object对象，而这个生成器是实现了Iterator接口（至于Iterator接口，你们去PHP手册上搜索吧）。
所以，既然实现了Iterator接口（也正是因为如此，这个东西可以使用foreach进行迭代，明白了吧？），所以可以有如下代码：

```php
<?php
function yield_range( $start, $end ){
  while( $start <= $end ){
    yield $start;
    $start++;
  }
}
$generator = yield_range( 1, 10 );
// valid() current() next() 都是Iterator接口中的方法
while( $generator->valid() ){
  echo $generator->current().PHP_EOL;
  $generator->next();
}
```

运行结果如下所示：

![](/blog/images/20200921/20200921002128.jpeg)

重点来了：这个yield_range函数似乎能够记住它上一次运行到哪儿了，上一次运行的结果是什么，
然后紧接着在下一次运行的时候继续从上次终止的地方继续开始。这不是普通的PHP函数可以做得到的！

我们知道，操作系统在调度进程的时候，会触发一个叫做“进程上下文切换”的概念。比如CPU从进程A调度给进程B了，
那么当再次从进程B调度给进程A的时候，当初进程A运行到哪儿了、临时的数据结果是什么都是需要被还原的，不然，一切都要从头，
那就要出大问题了。而，这个yield关键字，似乎在用户态（非系统内核级）就可以实现这个概念。所以说，用yield搞迭代，
怕是真的很没出息的一件事，它能做的太多。

紧接着，我们需要认识一个生成器对象的一个方法，叫做send，简单看下下面这坨代码：

```php
<?php
function yield_range( $start, $end ){
  while( $start <= $end ){
    $ret = yield $start;
    $start++;
    echo "yield receive : ".$ret.PHP_EOL;
  }
}
$generator = yield_range( 1, 10 );
$generator->send( $generator->current() * 10 );
```

运行结果如图所示：

![](/blog/images/20200921/20200921002129.jpeg)

send方法可以修改yield的返回值，但是，你也不能想当然，比如下面这坨代码，你们以为运行结果是什么样呢？

```php
<?php
function yield_range( $start, $end ){
  while( $start <= $end ){
    $ret = yield $start;
    $start++;
    echo "yield receive : ".$ret.PHP_EOL;
  }
}
$generator = yield_range( 1, 10 );
foreach( $generator as $item ){
  $generator->send( $generator->current() * 10 );
}
```

本来以为运行结果是类似于这样的：

```php
<?php
yield receive : 10
yield receive : 20
yield receive : 30
yield receive : 40
yield receive : 50
yield receive : 60
yield receive : 70
yield receive : 80
yield receive : 90
yield receive : 100
```

然而，唯物主义告诉我们：

![](/blog/images/20200921/20200921002130.jpeg)

结果是打脸的，你们感受一下：

![](/blog/images/20200921/20200921002131.jpeg)

为什么我把php版本信息什么的打印出来呢？因为，这是个bug，这是个php的bug，至少我正在使用的PHP 7.1.17版本是有这个bug的，
你不要以为这里面有什么高深莫测的技术，就是bug而已。下面是bug链接，你们可以去观摩一下：

<https://bugs.php.net/bug.php?id=76104> 
<https://stackoverflow.com/questions/37817315/how-does-generatorsend-work>

总结一句话，就是不要在foreach中使用生成器的send方法。

然而，我在国内的一些有关php yield的文章中，都没有看到有人提及这个bug，我坑我自己是淌过了，你们是没必要再淌了。


#### 18 填坑之PHP的yield和协程在一起的日子里

首先是，这是我第一次把公众号文章复制粘贴到github来。

其次是，很久很久之前，我挖了一个yield的一个坑，自己挖的坑自己填，不然迟早会把自己埋掉。

最后是，如果想看之前那个坑，请发送“yield”给README中的公众号，我开通了高大上的自动回复功能，稀罕地不得了！

PS：那篇文章中在最后我犯了一个错误，误下了一个结论：foreach中不能使用send并猜测这是PHP的bug，实际上并不是，
真实的原因粗暴简单的理解就是send会让生成器继续执行一次导致。这件事情告诉我们：

> 除了装逼之外，甩锅也是有打脸风险的

那篇坑里，内容和你能在百毒上搜索到的大多数文章都是差不多的，不过我那篇坑标题起得好：《yield是个什么玩意（上）》，
也就是暗示大家还有下篇，所以起标题也是需要一定技术含量的。

我坚信，在座的各位辣鸡在看完上篇坑文后最想说的注定是泰迪熊这句话（这是文化属性，不以各位的意志而转移）：

回到今天主旨上来，强调几点：

- 虽然文章标题中有“yield和协程”这样的关键字，但实际上yield并不是协程，看起来有不少人直接将yield和协程划了等号。yield的本质是生成器，英文名字叫做Generator。

- yield只能用在function中，但用了yield就已经不是传统意义上的function了，同时如果你企图在function之外的其他地方用yield，你会被打脸。

- yield的最重要作用就是：自己中断一坨代码的执行，然后主动让出CPU控制权给路人甲；然后又能通过一些方式从刚才中断的地方恢复运行。这个就比较屌了，假如你请求了一个费时10s的服务器API，此时是可以让出CPU给路人甲。粗暴地说上面的过程就算是协程的基本概念。



多线程和多进程都是操作系统参与的调度，而协程是用户自主实现的调度，协程的关键点实际上是“用户层实现自主调度”，大概有“翻身农奴把歌唱”的意思。

下面我通过一坨代码来体会一把“翻身农奴”，你们感受一下：
```php
<?php
function gen1() {
  for( $i = 1; $i <= 10; $i++ ) {
    echo "GEN1 : {$i}".PHP_EOL;
    // sleep没啥意思，主要就是运行时候给你一种切实的调度感，你懂么
    // 就是那种“你看！你看！尼玛,我调度了！卧槽”
    sleep( 1 );
    // 这句很关键，表示自己主动让出CPU，我不下地狱谁下地狱
    yield;
  }
}
function gen2() {
  for( $i = 1; $i <= 10; $i++ ) {
    echo "GEN2 : {$i}".PHP_EOL;
    // sleep没啥意思，主要就是运行时候给你一种切实的调度感，你懂么
    // 就是那种“你看！你看！尼玛,我调度了！卧槽”
    sleep( 1 );
    // 这句很关键，表示自己主动让出CPU，我不下地狱谁下地狱
    yield;
  }
}
$task1 = gen1();
$task2 = gen2();
while( true ) {
  // 首先我运行task1，然后task1主动下了地狱
  echo $task1->current();
  // 这会儿我可以让task2介入进来了
  echo $task2->current();
  // task1恢复中断
  $task1->next();
  // task2恢复中断
  $task2->next();
}
```
上面代码执行结果如下图：

![](/blog/images/20200921/20200921002132.png)


虽然我话都说到这里了，但是肯定还是有人get不到“所以，到底发生了什么？”。你要知道，如果function gen1和function gen2中没有yield，而是普通函数，你是无法中断其中的for循环的，诸如下面这样的代码：

```php
<?php
function gen1() {
  for( $i = 1; $i <= 10; $i++ ) {
    echo "GEN1 : {$i}".PHP_EOL;
    sleep( 1 );
  }
}
function gen2() {
  for( $i = 1; $i <= 10; $i++ ) {
    echo "GEN2 : {$i}".PHP_EOL;
  }
}
gen1();
gen2();
// 看这里，看这里，看这里！
// 上面的代码一旦运行，一定是先运行完gen1函数中的for循环
// 其次才能运行完gen2函数中的for循环，绝对不会出现
// gen1和gen2交叉运行这种情况
```

> 我似乎已然精通了yield

写到这里后我也开始蹩了，和以往的憋了三天蹦不出来个屁有所不同，我这次蹩出了一个比较典型的应用场景：curl。下面我们基于上面那坨辣鸡代码将gen1修改为一个耗时curl网络请求，gen2将向一个文本文件中写内容，我们的目的就是在耗时的curl开始后主动让出CPU，让gen2去写文件，以实现CPU的最大化利用。

```php
<?php
$ch1 = curl_init();
// 这个地址中的php，我故意sleep了5秒钟，然后输出一坨json
curl_setopt( $ch1, CURLOPT_URL, "http://www.selfctrler.com/index.php/test/test1" );
curl_setopt( $ch1, CURLOPT_HEADER, 0 );
$mh = curl_multi_init();
curl_multi_add_handle( $mh, $ch1 );
function gen1( $mh, $ch1 ) {
  do {
    $mrc = curl_multi_exec( $mh, $running );
    // 请求发出后，让出cpu
    yield;
  } while( $running > 0 );
  $ret = curl_multi_getcontent( $ch1 );
  echo $ret.PHP_EOL;
  return false;
}
function gen2() {
  for ( $i = 1; $i <= 10; $i++ ) {
    echo "gen2 : {$i}".PHP_EOL;
    file_put_contents( "./yield.log", "gen2".$i, FILE_APPEND );
    yield;
  }
}
$gen1 = gen1( $mh, $ch1 );
$gen2 = gen2();
while( true ) {
  echo $gen1->current();
  echo $gen2->current();
  $gen1->next();
  $gen2->next();
}
```

上面的代码，运行以后，我们再等待curl发起请求的5秒钟内，同时可以完成文件写入功能，如果换做平时的PHP程序，就只能是先阻塞等待curl拿到结果后才能完成文件写入。

文章太长，就像“老太太的裹脚布一样，又臭又长”，所以，最后再对代码做个极小幅度的改动就收尾不写了！

```php
<?php
$ch1 = curl_init();
// 这个地址中的php，我故意sleep了5秒钟，然后输出一坨json
curl_setopt( $ch1, CURLOPT_URL, "http://www.selfctrler.com/index.php/test/test1" );
curl_setopt( $ch1, CURLOPT_HEADER, 0 );
$mh = curl_multi_init();
curl_multi_add_handle( $mh, $ch1 );
function gen1( $mh, $ch1 ) {
  do {
    $mrc = curl_multi_exec( $mh, $running );
    // 请求发出后，让出cpu
    $rs = yield;
    echo "外部发送数据{$rs}".PHP_EOL;
  } while( $running > 0 );
  $ret = curl_multi_getcontent( $ch1 );
  echo $ret.PHP_EOL;
  return false;
}
function gen2() {
  for ( $i = 1; $i <= 10; $i++ ) {
    echo "gen2 : {$i}".PHP_EOL;
    file_put_contents( "./yield.log", "gen2".$i, FILE_APPEND );
    $rs = yield;
    echo "外部发送数据{$rs}".PHP_EOL;
  }
}
$gen1 = gen1( $mh, $ch1 );
$gen2 = gen2();
while( true ) {
  echo $gen1->current();
  echo $gen2->current();
  $gen1->send("gen1");
  $gen2->send("gen2");
}
```

我们修改了内容：

将$gen1->next()修改成了$gen1->send("gen1")

在function gen1中yield有了返回值，并且将返回值打印出来

这件事情告诉我们：yield和send，是可以双向通信的，同时告诉我们send可以用来恢复原来中断的代码，而且在恢复中断的同时可以携带信息回去。写到这里，你是不是觉得这玩意的可利用价值是不是比原来高点儿了？

我知道，有人肯定叨叨了：“老李，你代码特么写的真是辣鸡啊！你之前保证过了的 --- 只在公司生产环境写辣鸡代码的。可你看看你这辣鸡光环到笼罩都到demo里了，你连demo都不放过了！你怎么说？！”。兄dei，“又不是不能用”。而且我告诉你，上面这点儿curl demo来讲明白yield还是不够的，后面还有两三篇yield呢，照样是烂代码恶心死你，爱看不看。我劝你心放宽，你想想你这么烂的代码都经历了，还有什么不能经历的？

文章最后补个小故事：其实yield是PHP 5.5就已经添加进来了，这个模块的作者叫做Nikita Popov，网络上的名称是Nikic。我们知道PHP7这一代主力是惠新宸，下一代PHP主力就是Nikic了。早在2012年，Nikic就发表了一篇关于PHP yield多任务的文章，链接我贴出来大家共赏一下 --- http://nikic.github.io/2012/12/22/Cooperative-multitasking-using-coroutines-in-PHP.html



<br/><br/><br/><br/><br/>
### 参考资料

<https://github.com/elarity/advanced-php>

PHP 手册 函数参考 其它服务 Sockets <https://www.php.net/manual/zh/ref.sockets.php>

ab压力测试及结果分析 <https://blog.csdn.net/qq_34252622/article/details/92431267>

socket_select (PHP 手册 函数参考 其它服务 Sockets Socket 函数) <https://www.php.net/socket_select>
