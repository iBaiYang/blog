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

PHP中流的形式：`<scheme>://<target>`。其中`<scheme>`是包装器的名字，`<target>`中的内容是由包装器的语法指定，
不同的包装器的语法会有所不同。

默认的包装器是`file://`，也就是说每次我们访问文件系统的时候都使用了流。例如，我们可以使用如下两种方式来读取文件：
`readfile('/path/to/somefile.txt')` 和 `readfile('file:///path/to/somefile.txt')`，
使用这两种方式读取文件，可以得到相同的结果。

```
$content = readfile('/home/www/bin/count.sh');
var_dump($content); 
// #!/bin/bash for ((COUNT = 1; COUNT <= 10; COUNT++)); do echo $COUNT sleep 1 done

$content2 = readfile('file:///home/www/bin/count.sh');
var_dump($content2); 
// #!/bin/bash for ((COUNT = 1; COUNT <= 10; COUNT++)); do echo $COUNT sleep 1 done

// 逐行读取文件
$file = fopen("file:///home/www/bin/count.sh", "r") or exit("无法打开文件!");
// 读取文件每一行，直到文件结尾
while (!feof($file)) {
    echo fgets($file) . "<br>";
}
fclose($file);
// #!/bin/bash
// for ((COUNT = 1; COUNT <= 10; COUNT++)); do
// echo $COUNT
// sleep 1
// done

// 逐字符读取文件
$file2 = fopen("file:///home/www/bin/count.sh", "r") or exit("无法打开文件!");
while (!feof($file2)) {
    echo fgetc($file2); 
    // #!/bin/bash for ((COUNT = 1; COUNT <= 10; COUNT++)); do echo $COUNT sleep 1 done
}
fclose($file2);
```

PHP 内置的包装器：
```
/**
 * 获取已注册的套接字传输协议列表
 */
var_dump(stream_get_transports());
//array (size=9)
//  0 => string 'tcp' (length=3)
//  1 => string 'udp' (length=3)
//  2 => string 'unix' (length=4)
//  3 => string 'udg' (length=3)
//  4 => string 'ssl' (length=3)
//  5 => string 'tls' (length=3)
//  6 => string 'tlsv1.0' (length=7)
//  7 => string 'tlsv1.1' (length=7)
//  8 => string 'tlsv1.2' (length=7)

/**
 * 获取已注册的流类型
 */
var_dump(stream_get_wrappers());
// array (size=17)
//   0 => string 'https' (length=5)
//   1 => string 'ftps' (length=4)
//   2 => string 'compress.zlib' (length=13)
//   3 => string 'compress.bzip2' ength=14)
//   4 => string 'php' (length=3)
//   5 => string 'file' (length=4)
//   6 => string 'glob' (length=4)
//   7 => string 'data' (length=4)
//   8 => string 'http' (length=4)
//   9 => string 'ftp' (length=3)
//   10 => string 'phar' (length=4)
//   11 => string 'zip' (length=3)
//   12 => string 'ssh2.shell' (length=10)
//   13 => string 'ssh2.exec' (length=9)
//   14 => string 'ssh2.tunnel' (length=11)
//   15 => string 'ssh2.scp' (length=8)
//   16 => string 'ssh2.sftp' (length=9)

/**
 * 获取已注册的数据流过滤器列表
 */
var_dump(stream_get_filters());
//array (size=12)
//  0 => string 'zlib.*' (length=6)
//  1 => string 'bzip2.*' (length=7)
//  2 => string 'convert.iconv.*' (length=15)
//  3 => string 'mcrypt.*' (length=8)
//  4 => string 'mdecrypt.*' (length=10)
//  5 => string 'string.rot13' (length=12)
//  6 => string 'string.toupper' (length=14)
//  7 => string 'string.tolower' (length=14)
//  8 => string 'string.strip_tags' (length=17)
//  9 => string 'convert.*' (length=9)
//  10 => string 'consumed' (length=8)
//  11 => string 'dechunk' (length=7)
```

#### 流封装协议

流式数据的种类各异，每种类型需要独特的协议，以便读写数据，我们称这些协议为流封装协议。例如，我们可以读写文件系统，
可以通过 HTTP、HTTPS 或 SSH 与远程 Web 服务器通信，还可以打开并读写 ZIP、RAR 或 PHAR 压缩文件。这些通信方式都包含下述相同的过程：

1. 开始通信
2. 读取数据
3. 写入数据
4. 结束通信

虽然过程是一样的，但是读写文件系统中文件的方式与收发 HTTP 消息的方式有所不同，流封装协议的作用是使用通用的接口封装这种差异。

每个流都有一个协议和一个目标。指定协议和目标的方法是使用流标识符：`<scheme>://<target>`，其中 `<scheme>` 是流的封装协议，`<target>` 是流的数据源。

##### http://流封装协议

下面使用 HTTP 流封装协议创建了一个与 Flicker API 通信的 PHP 流：
```
<?php
$json = file_get_contents(
    'http://api.flickr.com/services/feeds/photos_public.gne?format=json'
);
```

不要以为这是普通的网页 URL，file_get_contents() 函数的字符串参数其实是一个流标识符。http 协议会让 PHP 使用 HTTP 流封装协议，
在这个参数中，http 之后是流的目标。

注：很多 PHP 开发者可能并不知道普通的 URL 其实是 PHP 流封装协议标识符的伪装。

##### file://流封装协议

我们通常使用 file_get_contents()、fopen()、fwrite() 和 fclose() 等函数读写文件系统，
因为 PHP 默认使用的流封装协议是 file://，所以我们很少认为这些函数使用的是 PHP 流。
下面的示例演示了使用 file:// 流封装协议创建一个读写 /etc/hosts 文件的流：
```
$handle = fopen('file:///etc/hosts', 'rb');
while (feof($handle) !== TRUE) {
    echo fgets($handle);
}
fclose($handle);
```

我们通常会省略掉 file:// 协议，因为这是 PHP 使用的默认值。

##### php://流封装协议

编写命令行脚本的 PHP 开发会用到 php:// 流封装协议，这个流封装协议的作用是与 PHP 脚本的标准输入、标准输出和标准错误文件描述符通信。
我们可以使用 PHP 提供的文件系统函数打开、读取或写入下面四个流：

1. php://stdin：这是个只读 PHP 流，其中的数据来自标准输入。PHP 脚本可以使用这个流接收命令行传入脚本的信息；
2. php://stdout：把数据写入当前的输出缓冲区，这个流只能写，无法读或寻址；
3. php://memory：从系统内存中读取数据，或者把数据写入系统内存。缺点是系统内存有限，所有使用 php://temp 更安全；
4. php://temp：和 php://memory 类似，不过，没有可用内存时，PHP 会把数据写入这个临时文件。

PHP有基本的php://stdin，php://stdout，php://stderr包装器对应默认的I/O资源。还有一个php://input流，它是一个只读的流，
流内容是post请求的数据。当我们将数据放在一个post请求的body体内用来请求一个远程服务的时候，这个流特别好用。

因为php://input是最常用到的流，所以这里列出一些知识点：
1. php://input 可以读取没有处理过的POST数据。相较于$HTTP_RAW_POST_DATA而言，它给内存带来的压力较小，并且不需要特殊的php.ini设置。php://input不能用于enctype=multipart/form-data。
2. 仅当Content-Type为application/x-www-form-urlencoded且提交方法是POST方法时，$_POST数据与php://input数据才是”一致”(打上引号，表示它们格式不一致，内容一致)的。其它情况，它们都不一致
3. php://input读取不到GET数据。是因为_GET数据作为query_path写在http请求头部(header)的PATH字段，而不是写在http请求的body部分。

##### 其他流封装协议

```
file:// — 访问本地文件系统
http:// — 访问 HTTP(s) 网址
ftp:// — 访问 FTP(s) URLs
php:// — 访问各个输入/输出流（I/O streams）
zlib:// — 压缩流
data:// — 数据（RFC 2397）
glob:// — 查找匹配的文件路径模式
phar:// — PHP 归档
ssh2:// — Secure Shell 2
rar:// — RAR
ogg:// — 音频流
expect:// — 处理交互式的流
```

#### 流上下文

有些 PHP 流能够接受一系列可选的参数，这些参数叫流上下文，用于定制流的行为。不同的流封装协议使用的流上下文有所不同，
流上下文使用 stream_context_create() 函数创建，这个函数返回的上下文对象可以传入大多数文件系统函数。

例如，我们可以使用 file_get_contents() 发送 HTTP POST 请求，使用一个流上下文对象即可实现：
```
$requestBody = '{"username":"nonfu"}';
$context = stream_context_create([
    'http' => [
        'method' => 'POST',
        'header' => "Content-Type: application/json;charset=utf-8;\r\nContent-Length: " . mb_strlen($requestBody),
        'content' => $requestBody
    ]
]);
$response = file_get_contents('https://my-api.com/users', false, $context);
```

##### 流过滤器

目前为止我们讨论了如何打开流，读取流中的数据，以及把数据写入流。不过，PHP 流真正强大的地方在于过滤、转换、添加或删除流中传输的数据，
例如，我们可以打开一个流处理 Markdown 文件，在把文件内容读入内存的过程中自动将其转化为 HTML。

若想把过滤器附加到现有的流上，要使用 stream_filter_append() 函数，下面我们以 `string.toupper` 过滤器演示如何把文件中的内容转换成大写字母：
```
$handle = fopen('test.txt', 'rb');
stream_filter_append($handle, 'string.toupper');
while (feof($handle) !== true) {
    echo fgets($handle);
}
fclose($handle);
```

运行该脚本，输出的都是大写字母：
```
ABCDEEFGHIJKLMN
HELLO LARAVELACADEMY!
```

PHP 所有可用流过滤器请参考官方文档：<http://php.net/manual/zh/filters.php>。

我们还可以使用 php://filter 流封装协议把过滤器附加到流上，不过，使用这种方式之前必须先打开 PHP 流：
```
$handle = fopen('php://filter/read=string.toupper/resource=test.txt', 'rb');
while (feof($handle) !== true) {
    echo fgets($handle);
}
fclose($handle);
```

这个方式实现效果和 stream_filter_append() 函数一样，但是相比之下更为繁琐。
不过，PHP 的某些文件系统函数在调用后无法附加过滤器，例如 file() 和 fpassthru()，
使用这些函数时只能使用 php://filter 流封装协议附加流过滤器。

###### 自定义流过滤器

我们还可以编写自定义的流过滤器。其实，大多数情况下都要使用自定义的流过滤器，自定义的流过滤器是个 PHP 类，
继承内置的 php_user_filter 类（http://php.net/manual/zh/class.php-user-filter.php），
且必须实现 filter()、onCreate() 和 onClose() 方法，最后，必须使用 stream_filter_register() 函数注册自定义的流过滤器。

注：PHP 流会把数据分成按次序排列的桶，一个桶中盛放的流数据是固定的（如 4096 字节），如果还用管道比喻，
就是把水放在一个个水桶中，顺着管道从出发地漂流到目的地，在漂流过程中会经过过滤器，过滤器一次可以接收并处理一个或多个桶，
一定时间内过滤器接收到的桶叫做桶队列。桶队列中的每个桶对象都有两个公共属性：data 和 datalen，分别表示桶的内容和长度。

下面我们自定义一个流过滤器 DirtyWordsFilter，把流数据读入内存时审查其中的脏字：
```
<?php
class DirtyWordsFilter extends php_user_filter
{
    /**
     * @param resource $in 流入的桶队列
     * @param resource $out 流出的桶队列
     * @param int $consumed 处理的字节数
     * @param bool $closing 是否是流中最后一个桶队列
     * @return int
     * 接收、处理再转运桶中的流数据，在该方法中，我们迭代桶队列对象，把脏字替换成审查后的值
     */
    public function filter($in, $out, &$consumed, $closing)
    {
        $words = ['grime', 'dirt', 'grease'];
        $wordData = [];
        foreach ($words as $word) {
            $replacement = array_fill(0, mb_strlen($word), '*');
            $wordData[$word] = implode('', $replacement);
        }
        $bad = array_keys($wordData);
        $good = array_values($wordData);
 
        // 迭代桶队列中的每个桶
        while ($bucket = stream_bucket_make_writeable($in)) {
            // 审查桶对象中的脏字
            $bucket->data = str_replace($bad, $good, $bucket->data);
            // 增加已处理的数据量
            $consumed += $bucket->datalen;
            // 把桶放入流向下游的队列中
            stream_bucket_append($out, $bucket);
        }
 
        return PSFS_PASS_ON;
    }
}
```

然后，我们必须使用 stream_filter_register() 函数注册这个自定义的 DirtyWordsFilter 流过滤器：
```
stream_filter_register('dirty_words_filter', 'DirtyWordsFilter');
```

第一个参数用于标识这个自定义过滤器的过滤器名，第二个参数是这个自定义过滤器的类名。接下来就可以使用这个自定义的流过滤器了：
```
$handle = fopen('test.txt', 'rb');
stream_filter_append($handle, 'dirty_words_filter');
while (feof($handle) !== true) {
    echo fgets($handle);
}
fclose($handle);
```

修改 test.txt 内容如下：
```
abcdeefghijklmn
Hello LaravelAcademy!
grime
I hate dirty things!
```

运行上面的自定义过滤器脚本，结果如下：
```
abcdeefghijklmn
Hello LaravelAcademy!
*****
I hate ****y things!
```

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

![]({{site.baseurl}}/images/20190114/20190114150405.jpg)

客户端运行：

![]({{site.baseurl}}/images/20190114/20190114150432.jpg)

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

![]({{site.baseurl}}/images/20190114/20190114151758.jpg)

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

PHP 手册 语言参考 支持的协议和封装协议 php:// <https://www.php.net/manual/zh/wrappers.php.php>

PHP Socket实现websocket（三）Stream函数 <https://www.cnblogs.com/yangxunwu1992/p/5563450.html>

Socket 深度探索 4 PHP （一） <https://blog.csdn.net/shagoo/article/details/6396089>

Swoole学习 <https://ibaiyang.github.io/blog/php/2018/05/26/PHP-Swoole%E5%AD%A6%E4%B9%A0.html>

客户端WebSocket实现 <https://ibaiyang.github.io/blog/html/2019/01/04/%E5%AE%A2%E6%88%B7%E7%AB%AFWebSocket%E5%AE%9E%E7%8E%B0.html>

PHP 文件处理相关函数 <https://ibaiyang.github.io/blog/php/2018/08/11/PHP-%E6%96%87%E4%BB%B6%E5%A4%84%E7%90%86%E7%9B%B8%E5%85%B3%E5%87%BD%E6%95%B0.html>

system函数 <http://www.php.net/manual/zh/function.system.php>

PHP执行系统外部命令函数:exec()、passthru()、system()、shell_exec() <https://www.cnblogs.com/gaohj/p/3267692.html>

PHP 手册 语言参考 支持的协议和封装协议 <https://www.php.net/manual/zh/wrappers.php>

PHP 手册 附录 可用过滤器列表 <https://www.php.net/manual/zh/filters.php>





