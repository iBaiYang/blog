---
layout: post
categories: PHP
title: PHP socket、fsockopen、stream、curl 区别
meta: socket 水泥、沙子，底层的东西；fsockopen 、 stream 水泥预制件，可以用来搭房子；curl 毛坯房，自己装修一下就能住了。
---
* content
{:toc}

### 正文

socket 水泥、沙子，底层的东西

fsockopen 、 stream 水泥预制件，可以用来搭房子

curl 毛坯房，自己装修一下就能住了

水泥、沙子不但可以修房子，还能修路、修桥、大型雕塑。socket也是，不但可以用于网页传输，还能传送其他东西，可以做聊天工具、下载器、ftp……
几乎可以用网络传送的东西都能用它写出来，当然，需要掌握的知识也不少，例如建墙你就要知道怎么让墙笔直、不易倒、防冻、隔热等等都需要自己学。

预制件你就不用管它是否笔直、结构如何、怎样隔热了，这些造的人帮你想好了，你想的就是怎样搭成你想要的形状就行。fsockopen就是，
你可以忽略socket里面的creat, connect, send, recv等等函数的用法，直接就open了。

毛坯房就更简单了，你装修就能住，最简单刷墙就行了，但想更舒适，就用更多更好的装修材料吧，但缺点就是——这是房子，你不能把它改造为渡河、
交通的用途，只能住。curl也一样，各种连接什么的都帮你做好了，底层容错处理也做了，你就传参数给它就能得到你想要的结果，
但缺点就是只能http / ftp，你想把它改成聊天工具，那就难了。

socket 是一个封装了 TCP/IP 操作的工具包。

stream_socket_client 和 fsockopen 没有本质上的区别。stream_socket_client 和 fsockopen 分属不同流派的对 socket 的封装。
stream_socket_client 属于流操作，而 fsockopen 属于文件操作。

fsockopen 是比较底层的调用，属于网络系统的socket调用，而curl经过的包装支持HTTPS认证。
fsockopen支持 HTTP POST方法， HTTP PUT方法， FTP上传， 
kerberos认证，HTTP上传，代理服务器， cookies，用户名/密码认证，下载文件断点续传，上载文件断点续传，http代理服务器管道（ proxy tunneling），
甚至它还支持IPv6， socks5代理服务器，通过http代理服务器上传文件到FTP服务器等等，功能十分强大。fsockopen 返回的是没有处理过的数据，
包括数据的长度数据内容和数据的结束符。而curl是处理后的内容。

在用户使用时，curl 更加方便，但其参数很多，配置稍微复杂，fsockopen 则有固定的几个参数，简单，但获取结果可能需要再做处理。

上面提到的相关内容，过去都有过记录：

socket <https://ibaiyang.github.io/blog/php/2019/08/27/PHP-socket编程.html>

stream <https://ibaiyang.github.io/blog/php/2018/06/04/PHP-Stream实现服务器客户端.html>

curl <https://ibaiyang.github.io/blog/php/2016/09/08/PHP-cURL详解.html>

#### Sockets

在PHP中，通过官方自带的Sockets扩展库，Stream 函数扩展库可以创建多种协议的服务器和客户端。Stream 函数扩展库是封装好了的Sockets扩展库，更容易使用。

通信过程:
```
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
```

##### TCP协议

TCP服务端：
```php
//确保在连接客户端时不会超时
set_time_limit(0);

$ip = '127.0.0.1';
$port = 1222;

if (($sock = socket_create(AF_INET, SOCK_STREAM, SOL_TCP)) < 0) {
    echo "socket_create() 失败的原因是:" . socket_strerror($sock) . "\n";
}

if (($ret = socket_bind($sock, $ip, $port)) < 0) {
    echo "socket_bind() 失败的原因是:" . socket_strerror($ret) . "\n";
}

if (($ret = socket_listen($sock, 4)) < 0) {
    echo "socket_listen() 失败的原因是:" . socket_strerror($ret) . "\n";
}

do {
    if (($msgsock = socket_accept($sock)) < 0) {
        echo "socket_accept() failed: reason: " . socket_strerror($msgsock) . "\n";
        break;
    } else {

        //发到客户端
        $msg = "发送成功";
        socket_write($msgsock, $msg, strlen($msg));

        //接收客户端数据
        $buf = socket_read($msgsock, 8192);

    }
    //echo $buf;
    socket_close($msgsock);

} while (true);

socket_close($sock);
```

TCP客户端：
```php
function tcp_client($port, $ip, $in)
{
    set_time_limit(0);

    //创建socket
    $socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
    if ($socket < 0) {
        $this->return_data(null, "socket创建失败，原因：" . socket_strerror($socket), RESPONSE_SOCKET_ERROR);
    }

    //进行socket连接
    $result = socket_connect($socket, $ip, $port);
    if ($result < 0) {
        $this->return_data(null, "socket连接失败，原因：" . socket_strerror($result), RESPONSE_SOCKET_ERROR);
    }
    $result_data = '';

    //发送socket数据到服务器
    if (!socket_write($socket, $in, strlen($in))) {
        $this->return_data(null, "socket发送数据失败，原因：" . socket_strerror($socket), RESPONSE_SOCKET_ERROR);
    }

    //获取socket返回值
    while ($out = socket_read($socket, 8192)) {
        $result_data = $out;
    }

    //关闭socket
    socket_close($socket);

    return $result_data;
}
```

##### UDP协议

UDP服务端：
```php
//Reduce errors
error_reporting(~E_WARNING);

//Create a UDP socket
if (!($sock = socket_create(AF_INET, SOCK_DGRAM, 0))) {
    $errorcode = socket_last_error();
    $errormsg = socket_strerror($errorcode);

    die("Couldn't create socket: [$errorcode] $errormsg \n");
}

echo "Socket created \n";

// Bind the source address
if (!socket_bind($sock, "127.0.0.1", 9999)) {
    $errorcode = socket_last_error();
    $errormsg = socket_strerror($errorcode);

    die("Could not bind socket : [$errorcode] $errormsg \n");
}

echo "Socket bind OK \n";

//Do some communication, this loop can handle multiple clients
while (1) {
    echo "Waiting for data ... \n";

    //Receive some data
    $r = socket_recvfrom($sock, $buf, 512, 0, $remote_ip, $remote_port);
    echo "$remote_ip : $remote_port -- " . $buf;

    //Send back the data to the client
    socket_sendto($sock, "OK " . $buf, 100, 0, $remote_ip, $remote_port);
}

socket_close($sock);
```

UDP客户端：
```

```

#### Stream

通信过程:
```
  +-------------------------------
  *    @socket通信整个过程
  +-------------------------------
  *    @stream_socket_server
  *    @stream_socket_accept
  *    @stream_socket_recvfrom
  *    @stream_socket_sendto
  *    @stream_get_contents
  *    @stream_socket_shutdown
```

```
stream_socket_client
fread
fwrite
fclose
```

TCP服务端：
```php
//设置不超时
set_time_limit(0);

class SocketServer
{
    public function __construct($port)
    {
        global $errno, $errstr;

        $socket = stream_socket_server('tcp://127.0.0.1:' . $port, $errno, $errstr);
        while ($conn = stream_socket_accept($socket, -1)) {
            $buff = '';
            $data = '';
            file_put_contents('log.txt', 'SERVER-1|| server：conn->' . $conn . ';  buff->' . $buff . '; data->' . $data . "\r\n", FILE_APPEND);
            
            //读取请求数据直到遇到\r\n结束符
            while (!preg_match('#\r\n#', $buff)) {
                $buff = fread($conn, 1024);
                file_put_contents('log.txt', 'SERVER-2|| buff：' . $buff . "\r\n", FILE_APPEND);
                $data .= preg_replace('#\r\n#', '', $buff);
                file_put_contents('log.txt', 'SERVER-3|| data：' . $data . "\r\n", FILE_APPEND);
            }
            file_put_contents('log.txt', 'SERVER-4|| data：' . $data . "\r\n", FILE_APPEND);
            
            fwrite($conn, $data);
            file_put_contents('log.txt', 'SERVER-5|| data：' . $data . "\r\n", FILE_APPEND);
            
            fclose($conn);
        }
        fclose($socket);
    }
}

new SocketServer(1212);
```

TCP客户端：
```php
if (isset($argv[1])) {
    $msg = $argv[1];
    file_put_contents('log.txt', 'CLIENT-1|| msg：' . $msg . "\r\n", FILE_APPEND);
    $socket = stream_socket_client('tcp://127.0.0.1:1212', $errno, $errstr);
    if (!$socket) {
        die($errno . $errstr);
    } else {
        // stream_set_blocking($socket, 0);
        for ($index = 0; $index < 3; $index++) {
            fwrite($socket, " client: $msg $index ");
            file_put_contents('log.txt', 'CLIENT-2|| for： msg' . $msg . '; index：' . $index . "\r\n", FILE_APPEND);
            usleep(100000);
        }
        fwrite($socket, "\r\n");
        $response = fread($socket, 1024);
        file_put_contents('log.txt', 'CLIENT-3|| client：time->' . date("[H:i:s] ", time()) . '; reponse->' . $response . '; msg->' . $msg . "\r\n", FILE_APPEND);
        fclose($socket);
    }
} else {
    for ($index = 0; $index < 3; $index++) {
        file_put_contents('log.txt', 'CLIENT-4|| start：' . $index . "\r\n", FILE_APPEND);
        system('php ' . __FILE__ . " $index:test");
    }
}
```

UDP客户端：
```php
function udp_client($port, $ip, $sendMsg)
{
    set_time_limit(0);
    $handle = stream_socket_client("udp://{$ip}:{$port}", $errno, $errstr);
    if (!$handle) {
        $this->return_data(null, "连接失败，{$errno} - {$errstr}", RESPONSE_UNDATA_ERROR);
    }
    fwrite($handle, $sendMsg);
    $result = fread($handle, 1024);
    fclose($handle);
    return $result;
}
```

#### fsockopen

```php
$fp = fsockopen("www.example.com", 80, $errno, $errstr, 30);
if (!$fp) {
    echo "$errstr ($errno)<br />\n";
} else {
      $out = "GET / HTTP/1.1\r\n";
      $out .= "Host: www.example.com\r\n";
      $out .= "Connection: Close\r\n\r\n";
  fwrite($fp, $out);
while (!feof($fp)) {
    echo fgets($fp, 128);
}
fclose($fp);
}
```

使用UDP连接:
```php
$fp = fsockopen("udp://127.0.0.1", 13, $errno, $errstr);
if (!$fp) {
    echo "ERROR: $errno - $errstr<br />\n";
} else {
    fwrite($fp, "\n");
    echo fread($fp, 26);
    fclose($fp);
}
```

#### cUrl

```php
<?php
function Get($url)
{
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
    $result = curl_exec($ch);
    if ($result === false) {
        // 记录日志
        // write_logs("test",  "Get is false", curl_error($ch));
    }
    curl_close($ch);
    
    return $result;
}

function Post($url, $postData = array())
{
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($postData));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
    $result = curl_exec($ch);
    if ($result === false) {
        // 记录日志
        // write_logs("test",  "Post is false", curl_error($ch));
    }
    curl_close($ch);
    
    return $result;
}
```

#### file_get_contents

那么file_get_contents呢？

file_get_contents()除了获取本地文件内容，还可以获取远程文件内容。

有些时候用 file_get_contents() 调用外部文件容易超时报错。curl 效率比 file_get_contents() 和 fsockopen() 高一些，原因是cURL会自动对DNS信息进行缓存。

file_get_contents / curl / fsockopen 在当前所请求环境下选择性操作，没有一概而论。

file_get_contents 需要php.ini里开启allow_url_fopen，请求http时，使用的是http_fopen_wrapper，不会keeplive的话curl是可以的。
file_get_contents()单个执行效率高，返回没有头的信息。

这个是读取一般文件的时候并没有什么问题，但是在读取远程文件的时候有可能就会出现问题。如果是要一个持续连接，多次请求多个页面。
那么file_get_contents和fopen就会出问题。取得的内容也可能会不对。所以做一些类似采集工作的时候，肯定就有问题了。

fsockopen 较底层，可以设置基于UDP或是TCP协议去交互，配置麻烦，不易操作。返回完整信息。

总之，file_get_contents 和 curl 能干的，socket都能干。socket能干的，curl 就不一定能干了。
file_get_contents 更多的时候只是去拉取数据。效率比较高也比较简单。

只讨论 curl 与file_get_contents 的话，有这么一些结论：

1. fopen /file_get_contents 每次请求都会重新做DNS查询，并不对DNS信息进行缓存。但是cURL会自动对DNS信息进行缓存。
   对同一域名下的网页或者图片的请求只需要一次DNS查询。这大大减少了DNS查询的次数。所以cURL的性能比fopen /file_get_contents 好很多。

2. fopen /file_get_contents在请求HTTP时，使用的是http_fopen_wrapper，不会keeplive。而curl却可以。这样在多次请求多个链接时，curl效率会好一些。

3. fopen / file_get_contents函数会受到php.ini文件中allow_url_open选项配置的影响。如果该配置关闭了，则该函数也就失效了。而curl不受该配置的影响。

4. curl可以模拟多种请求，例如：POST数据，表单提交等，用户可以按照自己的需求来定制请求。而fopen / file_get_contents只能使用get方式获取数据。

PS：file_get_contents()函数获取https链接内容的时候，需要php 中mod_ssl的支持(或安装opensll)。

结论就是，curl 效率及稳定都比 file_get_contents() 要好，fsockopen 也很强大，但是比较偏底层。

#### HTTP功能工厂方法类

兼容 Curl/Socket/Stream 的 HTTP 客户端操作类：

```php
<?php
/**  
 * HTTP功能工厂方法类  
 *  
 * 调用示例代码：  
    try {  
        $http = Http::factory('http://www.baidu.com', Http::TYPE_SOCK );  
        echo $http->get();    
        $http = Http::factory('http://127.0.0.1/test/i.php', Http::TYPE_SOCK );  
        echo $http->post('', array('user'=>'我们', 'nick'=>'ASSADF@#!32812989+-239%ASDF'), '', array('aa'=>'bb', 'cc'=>'dd'));  
    } catch (Exception $e) {  
        echo $e->getMessage();  
    }  
 */  
class Http
{
    /**
     * @var 使用 cURL
     */
    const TYPE_cURL = 1;
    /**
     * @var 使用 Socket
     */
    const TYPE_SOCK = 2;
    /**
     * @var 使用 Stream
     */
    const TYPE_STREAM = 3;

    /**
     * 保证对象不被clone
     */
    private function __clone()
    {
    }

    /**
     * 构造函数
     */
    private function __construct()
    {
    }

    /**
     * HTTP工厂操作方法
     *
     * @param string $url 需要访问的URL
     * @param int $type 需要使用的HTTP类
     * @return object
     */
    public static function factory($url = '', $type = self::TYPE_SOCK)
    {
        if ($type == '') {
            $type = self::TYPE_SOCK;
        }
        switch ($type) {
            case self::TYPE_cURL :
                if (!function_exists('curl_init')) {
                    throw new Exception(__CLASS__ . " PHP cURL extension not install");
                }
                $obj = Http_Curl::getInstance($url);
                break;
            case self::TYPE_SOCK :
                if (!function_exists('fsockopen')) {
                    throw new Exception(__CLASS__ . " PHP function fsockopen() not support");
                }
                $obj = Http_Sock::getInstance($url);
                break;
            case self::TYPE_STREAM :
                if (!function_exists('stream_context_create')) {
                    throw new Exception(__CLASS__ . " PHP Stream extension not install");
                }
                $obj = Http_Stream::getInstance($url);
                break;
            default:
                throw new Exception("http access type $type not support");
        }
        return $obj;
    }

    /**
     * 生成一个供Cookie或HTTP GET Query的字符串
     *
     * @param array $data 需要生产的数据数组，必须是 Name => Value 结构
     * @param string $sep 两个变量值之间分割的字符，缺省是 &
     * @return string 返回生成好的Cookie查询字符串
     */
    public static function makeQuery($data, $sep = '&')
    {
        $encoded = '';
        while (list($k, $v) = each($data)) {
            $encoded .= ($encoded ? "$sep" : "");
            $encoded .= rawurlencode($k) . "=" . rawurlencode($v);
        }
        return $encoded;
    }
}   
```

Http_Curl类：
```php
/**  
* 使用cURL 作为核心操作的HTTP访问类  
*  
* @desc cURL 以稳定、高效、移植性强作为很重要的HTTP协议访问客户端，必须在PHP中安装 cURL 扩展才能使用本功能  
*/  
class Http_Curl
{
    /**
     * @var object 对象单例
     */
    static $_instance = NULL;

    /**
     * @var string 需要发送的cookie信息
     */
    private $cookies = '';
    /**
     * @var array 需要发送的头信息
     */
    private $header = array();
    /**
     * @var string 需要访问的URL地址
     */
    private $uri = '';
    /**
     * @var array 需要发送的数据
     */
    private $vars = array();

    /**
     * 构造函数
     *
     * @param string $configFile 配置文件路径
     */
    private function __construct($url)
    {
        $this->uri = $url;
    }

    /**
     * 保证对象不被clone
     */
    private function __clone()
    {
    }

    /**
     * 获取对象唯一实例
     *
     * @param string $configFile 配置文件路径
     * @return object 返回本对象实例
     */
    public static function getInstance($url = '')
    {
        if (!(self::$_instance instanceof self)) {
            self::$_instance = new self($url);
        }
        return self::$_instance;
    }

    /**
     * 设置需要发送的HTTP头信息
     *
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *       或单一的一条类似于 'Host: example.com' 头信息字符串
     * @return void
     */
    public function setHeader($header)
    {
        if (emptyempty($header)) {
            return;
        }
        if (is_array($header)) {
            foreach ($header as $k => $v) {
                $this->header[] = is_numeric($k) ? trim($v) : (trim($k) . ": " . trim($v));
            }
        } elseif (is_string($header)) {
            $this->header[] = $header;
        }
    }

    /**
     * 设置Cookie头信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setCookie($cookie)
    {
        if (emptyempty($cookie)) {
            return;
        }
        if (is_array($cookie)) {
            $this->cookies = Http::makeQuery($cookie, ';');
        } elseif (is_string($cookie)) {
            $this->cookies = $cookie;
        }
    }

    /**
     * 设置要发送的数据信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param array 设置需要发送的数据信息，一个类似于 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setVar($vars)
    {
        if (emptyempty($vars)) {
            return;
        }
        if (is_array($vars)) {
            $this->vars = $vars;
        }
    }

    /**
     * 设置要请求的URL地址
     *
     * @param string $url 需要设置的URL地址
     * @return void
     */
    public function setUrl($url)
    {
        if ($url != '') {
            $this->uri = $url;
        }
    }


    /**
     * 发送HTTP GET请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('GET', $timeout);
    }

    /**
     * 发送HTTP POST请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('POST', $timeout);
    }

    /**
     * 发送HTTP请求核心函数
     *
     * @param string $method 使用GET还是POST方式访问
     * @param array $vars 需要另外附加发送的GET/POST数据
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return string 返回服务器端读取的返回数据
     */
    public function send($method = 'GET', $timeout = 5, $options = array())
    {
        //处理参数是否为空   
        if ($this->uri == '') {
            throw new Exception(__CLASS__ . ": Access url is empty");
        }

        //初始化cURL   
        $ch = curl_init();
        curl_setopt($ch, cURLOPT_HEADER, 0);
        curl_setopt($ch, cURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($ch, cURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, cURLOPT_TIMEOUT, $timeout);

        //设置特殊属性   
        if (!emptyempty($options)) {
            curl_setopt_array($ch, $options);
        }
        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)) {
            $query = Http::makeQuery($this->vars);
            $parse = parse_url($this->uri);
            $sep = isset($parse['query']) ? '&' : '?';
            $this->uri .= $sep . $query;
        }
        //处理POST请求数据   
        if ($method == 'POST') {
            curl_setopt($ch, cURLOPT_POST, 1);
            curl_setopt($ch, cURLOPT_POSTFIELDS, $this->vars);
        }

        //设置cookie信息   
        if (!emptyempty($this->cookies)) {
            curl_setopt($ch, cURLOPT_COOKIE, $this->cookies);
        }
        //设置HTTP缺省头   
        if (emptyempty($this->header)) {
            $this->header = array(
                'User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)',
                //'Accept-Language: zh-cn',             
                //'Cache-Control: no-cache',   
            );
        }
        curl_setopt($ch, cURLOPT_HTTPHEADER, $this->header);

        //发送请求读取输数据   
        curl_setopt($ch, cURLOPT_URL, $this->uri);
        $data = curl_exec($ch);
        if (($err = curl_error($ch))) {
            curl_close($ch);
            throw new  Exception(__CLASS__ . " error: " . $err);
        }
        curl_close($ch);
        return $data;
    }
}   
```

Http_Sock类：
```php
/**  
 * 使用 Socket操作( fsockopen()函数实现 ) 作为核心操作的HTTP访问接口  
 *  
 * @desc Network/fsockopen 是PHP内置的一个Sokcet网络访问接口，必须安装/打开 fsockopen 函数本类才能工作，  
 *    同时确保其他相关网络环境和配置是正确的  
 */  
class Http_Sock
{
    /**
     * @var object 对象单例
     */
    static $_instance = NULL;

    /**
     * @var string 需要发送的cookie信息
     */
    private $cookies = '';
    /**
     * @var array 需要发送的头信息
     */
    private $header = array();
    /**
     * @var string 需要访问的URL地址
     */
    private $uri = '';
    /**
     * @var array 需要发送的数据
     */
    private $vars = array();

    /**
     * 构造函数
     *
     * @param string $configFile 配置文件路径
     */
    private function __construct($url)
    {
        $this->uri = $url;
    }

    /**
     * 保证对象不被clone
     */
    private function __clone()
    {
    }

    /**
     * 获取对象唯一实例
     *
     * @param string $configFile 配置文件路径
     * @return object 返回本对象实例
     */
    public static function getInstance($url = '')
    {
        if (!(self::$_instance instanceof self)) {
            self::$_instance = new self($url);
        }
        return self::$_instance;
    }

    /**
     * 设置需要发送的HTTP头信息
     *
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *       或单一的一条类似于 'Host: example.com' 头信息字符串
     * @return void
     */
    public function setHeader($header)
    {
        if (emptyempty($header)) {
            return;
        }
        if (is_array($header)) {
            foreach ($header as $k => $v) {
                $this->header[] = is_numeric($k) ? trim($v) : (trim($k) . ": " . trim($v));
            }
        } elseif (is_string($header)) {
            $this->header[] = $header;
        }
    }

    /**
     * 设置Cookie头信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setCookie($cookie)
    {
        if (emptyempty($cookie)) {
            return;
        }
        if (is_array($cookie)) {
            $this->cookies = Http::makeQuery($cookie, ';');
        } elseif (is_string($cookie)) {
            $this->cookies = $cookie;
        }
    }

    /**
     * 设置要发送的数据信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param array 设置需要发送的数据信息，一个类似于 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setVar($vars)
    {
        if (emptyempty($vars)) {
            return;
        }
        if (is_array($vars)) {
            $this->vars = $vars;
        }
    }

    /**
     * 设置要请求的URL地址
     *
     * @param string $url 需要设置的URL地址
     * @return void
     */
    public function setUrl($url)
    {
        if ($url != '') {
            $this->uri = $url;
        }
    }


    /**
     * 发送HTTP GET请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('GET', $timeout);
    }


    /**
     * 发送HTTP POST请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('POST', $timeout);
    }

    /**
     * 发送HTTP请求核心函数
     *
     * @param string $method 使用GET还是POST方式访问
     * @param array $vars 需要另外附加发送的GET/POST数据
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return string 返回服务器端读取的返回数据
     */
    public function send($method = 'GET', $timeout = 5, $options = array())
    {
        //处理参数是否为空   
        if ($this->uri == '') {
            throw new Exception(__CLASS__ . ": Access url is empty");
        }

        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)) {
            $query = Http::makeQuery($this->vars);
            $parse = parse_url($this->uri);
            $sep = isset($parse['query']) && ($parse['query'] != '') ? '&' : '?';
            $this->uri .= $sep . $query;
        }

        //处理POST请求数据   
        $data = '';
        if ($method == 'POST' && !emptyempty($this->vars)) {
            $data = Http::makeQuery($this->vars);
            $this->setHeader('Content-Type: application/x-www-form-urlencoded');
            $this->setHeader('Content-Length: ' . strlen($data));
        }

        //解析URL地址   
        $url = parse_url($this->uri);
        $host = $url['host'];
        $port = isset($url['port']) && ($url['port'] != '') ? $url['port'] : 80;
        $path = isset($url['path']) && ($url['path'] != '') ? $url['path'] : '/';
        $path .= isset($url['query']) ? "?" . $url['query'] : '';

        //组织HTTP请求头信息   
        array_unshift(&$this->header, $method . " " . $path . " HTTP/1.1");
        $this->setHeader('User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)');
        if (!preg_match("/^[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}$/", $host)) {
            $this->setHeader("Host: " . $host);
        }
        if ($this->cookies != '') {
            $this->setHeader("Cookie: " . $this->cookies);
        }
        $this->setHeader("Connection: Close");
        //'Accept-Language: zh-cn',   
        //'Cache-Control: no-cache',   

        //构造请求信息   
        $header = '';
        foreach ($this->header as $h) {
            $header .= $h . "\r\n";
        }
        $header .= "\r\n";
        if ($method == 'POST' && $data != '') {
            $header .= $data . "\r\n";
        }

        //连接服务器发送请求数据   
        $ip = gethostbyname($host);
        if (!($fp = fsockopen($ip, $port, &$errno, &$errstr, $timeout))) {
            throw new Exception(__CLASS__ . ": Can't connect $host:$port, errno:$errno,message:$errstr");
        }
        fputs($fp, $header);
        $lineSize = 1024;

        //处理301,302跳转页面访问   
        $line = fgets($fp, $lineSize);
        $first = preg_split("/\s/", trim($line));
        if (isset($first[1]) && in_array($first[1], array('301', '302'))) {
            while (!feof($fp)) {
                $line = fgets($fp, $lineSize);
                $second = preg_split("/\s/", trim($line));
                if (ucfirst(trim($second[0])) == 'Location:' && $second[1] != '') {
                    $this->header = array();
                    return $this->get(trim($second[1]));
                }
            }
        }

        //正常读取返回数据     
        $buf = '';
        $inheader = 1;
        while (!feof($fp)) {
            if ($inheader && ($line == "\n" || $line == "\r\n")) {
                $inheader = 0;
            }
            $line = fgets($fp, $lineSize);
            if ($inheader == 0) {
                $buf .= $line;
            }
        }
        fclose($fp);

        return $buf;
    }
}   
```

Http_Stream类：
```php
/**  
 * 使用文件流操作函数为核心操作的HTTP访问接口 （stream_context_creat()函数实现）  
 *  
 * @desc stream_* 和 fopen/file_get_contents 是PHP内置的一个流和文件操作接口，必须打开 fsockopen 函数本类才能工作，  
 *    同时确保其他相关网络环境和配置是正确的，包括 allow_url_fopen 等设置  
 */  
class Http_Stream
{
    /**
     * @var object 对象单例
     */
    static $_instance = NULL;

    /**
     * @var string 需要发送的cookie信息
     */
    private $cookies = '';
    /**
     * @var array 需要发送的头信息
     */
    private $header = array();
    /**
     * @var string 需要访问的URL地址
     */
    private $uri = '';
    /**
     * @var array 需要发送的数据
     */
    private $vars = array();

    /**
     * 构造函数
     *
     * @param string $configFile 配置文件路径
     */
    private function __construct($url)
    {
        $this->uri = $url;
    }

    /**
     * 保证对象不被clone
     */
    private function __clone()
    {
    }


    /**
     * 获取对象唯一实例
     *
     * @param string $configFile 配置文件路径
     * @return object 返回本对象实例
     */
    public static function getInstance($url = '')
    {
        if (!(self::$_instance instanceof self)) {
            self::$_instance = new self($url);
        }
        return self::$_instance;
    }

    /**
     * 设置需要发送的HTTP头信息
     *
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *       或单一的一条类似于 'Host: example.com' 头信息字符串
     * @return void
     */
    public function setHeader($header)
    {
        if (emptyempty($header)) {
            return;
        }
        if (is_array($header)) {
            foreach ($header as $k => $v) {
                $this->header[] = is_numeric($k) ? trim($v) : (trim($k) . ": " . trim($v));
            }
        } elseif (is_string($header)) {
            $this->header[] = $header;
        }
    }

    /**
     * 设置Cookie头信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setCookie($cookie)
    {
        if (emptyempty($cookie)) {
            return;
        }
        if (is_array($cookie)) {
            $this->cookies = Http::makeQuery($cookie, ';');
        } elseif (is_string($cookie)) {
            $this->cookies = $cookie;
        }
    }

    /**
     * 设置要发送的数据信息
     *
     * 注意：本函数只能调用一次，下次调用会覆盖上一次的设置
     *
     * @param array 设置需要发送的数据信息，一个类似于 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @return void
     */
    public function setVar($vars)
    {
        if (emptyempty($vars)) {
            return;
        }
        if (is_array($vars)) {
            $this->vars = $vars;
        }
    }

    /**
     * 设置要请求的URL地址
     *
     * @param string $url 需要设置的URL地址
     * @return void
     */
    public function setUrl($url)
    {
        if ($url != '') {
            $this->uri = $url;
        }
    }

    /**
     * 发送HTTP GET请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('GET', $timeout);
    }


    /**
     * 发送HTTP POST请求
     *
     * @param string $url 如果初始化对象的时候没有设置或者要设置不同的访问URL，可以传本参数
     * @param array $vars 需要单独返送的GET变量
     * @param array/string 需要设置的头信息，可以是一个 类似 array('Host: example.com', 'Accept-Language: zh-cn') 的头信息数组
     *         或单一的一条类似于 'Host: example.com' 头信息字符串
     * @param string/array 需要设置的Cookie信息，一个类似于 'name1=value1&name2=value2' 的Cookie字符串信息，
     *         或者是一个 array('name1'=>'value1', 'name2'=>'value2') 的一维数组
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return unknown
     */
    public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array())
    {
        $this->setUrl($url);
        $this->setHeader($header);
        $this->setCookie($cookie);
        $this->setVar($vars);
        return $this->send('POST', $timeout);
    }

    /**
     * 发送HTTP请求核心函数
     *
     * @param string $method 使用GET还是POST方式访问
     * @param array $vars 需要另外附加发送的GET/POST数据
     * @param int $timeout 连接对方服务器访问超时时间，单位为秒
     * @param array $options 当前操作类一些特殊的属性设置
     * @return string 返回服务器端读取的返回数据
     */
    public function send($method = 'GET', $timeout = 5, $options = array())
    {
        //处理参数是否为空   
        if ($this->uri == '') {
            throw new Exception(__CLASS__ . ": Access url is empty");
        }
        $parse = parse_url($this->uri);
        $host = $parse['host'];

        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)) {
            $query = Http::makeQuery($this->vars);
            $sep = isset($parse['query']) && ($parse['query'] != '') ? '&' : '?';
            $this->uri .= $sep . $query;
        }

        //处理POST请求数据   
        $data = '';
        if ($method == 'POST' && !emptyempty($this->vars)) {
            $data = Http::makeQuery($this->vars);
        }

        //设置缺省头   
        $this->setHeader('User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)');
        if (!preg_match("/^[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}$/", $host)) {
            $this->setHeader("Host: " . $host);
        }
        if ($this->cookies != '') {
            $this->setHeader("Cookie: " . $this->cookies);
        }
        $this->setHeader("Connection: Close");
        //'Accept-Language: zh-cn',   
        //'Cache-Control: no-cache',           

        //构造头信息   
        $opts = array(
            'http' => array(
                'method' => $method,
                'timeout' => $timeout,
            )
        );
        if ($data != '') {
            $opts['http']['content'] = $data;
        }
        $opts['http']['header'] = '';
        foreach ($this->header as $h) {
            $opts['http']['header'] .= $h . "\r\n";
        }
        //print_r($opts);exit;   

        //读取扩展设置选项   
        if (!emptyempty($options)) {
            isset($options['proxy']) ? $opts['http']['proxy'] = $options['proxy'] : '';
            isset($options['max_redirects']) ? $opts['http']['max_redirects'] = $options['max_redirects'] : '';
            isset($options['request_fulluri']) ? $opts['http']['request_fulluri'] = $options['request_fulluri'] : '';
        }

        //发送数据返回   
        $context = stream_context_create($opts);
        if (($buf = file_get_contents($this->uri, null, $context)) === false) {
            throw new Exception(__CLASS__ . ": file_get_contents(" . $this->uri . ") fail");
        }

        return $buf;
    }
}
```

#### 函数原型

stream_socket_client():
```
/**
 * Open Internet or Unix domain socket connection
 * @link https://php.net/manual/en/function.stream-socket-client.php
 * @param string $remote_socket <p>
 * Address to the socket to connect to.
 * </p>
 * @param int $errno [optional] <p>
 * Will be set to the system level error number if connection fails.
 * </p>
 * @param string $errstr [optional] <p>
 * Will be set to the system level error message if the connection fails.
 * </p>
 * @param float|null $timeout [optional] <p>
 * Number of seconds until the connect() system call
 * should timeout.
 * This parameter only applies when not making asynchronous
 * connection attempts.
 * <p>
 * To set a timeout for reading/writing data over the socket, use the
 * stream_set_timeout, as the
 * timeout only applies while making connecting
 * the socket.
 * </p>
 * </p>
 * @param int $flags [optional] <p>
 * Bitmask field which may be set to any combination of connection flags.
 * Currently the select of connection flags is limited to
 * STREAM_CLIENT_CONNECT (default),
 * STREAM_CLIENT_ASYNC_CONNECT and
 * STREAM_CLIENT_PERSISTENT.
 * </p>
 * @param resource $context [optional] <p>
 * A valid context resource created with stream_context_create.
 * </p>
 * @return resource|false On success a stream resource is returned which may
 * be used together with the other file functions (such as
 * fgets, fgetss,
 * fwrite, fclose, and
 * feof), false on failure.
 */
function stream_socket_client ($remote_socket, &$errno = null, &$errstr = null, $timeout = null, $flags = null, $context = null) {}
```

fsockopen():
```
/**
 * Open Internet or Unix domain socket connection
 * @link https://php.net/manual/en/function.fsockopen.php
 * @param string $hostname <p>
 * If you have compiled in OpenSSL support, you may prefix the
 * hostname with either ssl://
 * or tls:// to use an SSL or TLS client connection
 * over TCP/IP to connect to the remote host.
 * </p>
 * @param int $port [optional] <p>
 * The port number.
 * </p>
 * @param int &$errno [optional] <p>
 * If provided, holds the system level error number that occurred in the
 * system-level connect() call.
 * </p>
 * <p>
 * If the value returned in errno is
 * 0 and the function returned false, it is an
 * indication that the error occurred before the
 * connect() call. This is most likely due to a
 * problem initializing the socket.
 * </p>
 * @param string &$errstr [optional] <p>
 * The error message as a string.
 * </p>
 * @param float $timeout [optional] <p>
 * The connection timeout, in seconds.
 * </p>
 * <p>
 * If you need to set a timeout for reading/writing data over the
 * socket, use stream_set_timeout, as the
 * timeout parameter to
 * fsockopen only applies while connecting the
 * socket.
 * </p>
 * @return resource|false fsockopen returns a file pointer which may be used
 * together with the other file functions (such as
 * fgets, fgetss,
 * fwrite, fclose, and
 * feof). If the call fails, it will return false
 */
function fsockopen ($hostname, $port = null, &$errno = null, &$errstr = null, $timeout = null) {}
```

file_get_contents():
```
/**
 * Reads entire file into a string
 * @link https://php.net/manual/en/function.file-get-contents.php
 * @param string $filename <p>
 * Name of the file to read.
 * </p>
 * @param bool $use_include_path [optional] <p>
 * Note: As of PHP 5 the FILE_USE_INCLUDE_PATH constant can be
 * used to trigger include path search.
 * </p>
 * @param resource $context [optional] <p>
 * A valid context resource created with
 * stream_context_create. If you don't need to use a
 * custom context, you can skip this parameter by &null;.
 * </p>
 * @param int $offset [optional] <p>
 * The offset where the reading starts.
 * </p>
 * @param int $maxlen [optional] <p>
 * Maximum length of data read. The default is to read until end
 * of file is reached.
 * </p>
 * @return string|false The function returns the read data or false on failure.
 */
function file_get_contents ($filename, $use_include_path = false, $context = null, $offset = 0, $maxlen = null) {}
```

fopen():
```
/**
 * Opens file or URL
 * @link https://php.net/manual/en/function.fopen.php
 * @param string $filename <p>
 * If filename is of the form "scheme://...", it
 * is assumed to be a URL and PHP will search for a protocol handler
 * (also known as a wrapper) for that scheme. If no wrappers for that
 * protocol are registered, PHP will emit a notice to help you track
 * potential problems in your script and then continue as though
 * filename specifies a regular file.
 * </p>
 * <p>
 * If PHP has decided that filename specifies
 * a local file, then it will try to open a stream on that file.
 * The file must be accessible to PHP, so you need to ensure that
 * the file access permissions allow this access.
 * If you have enabled &safemode;,
 * or open_basedir further
 * restrictions may apply.
 * </p>
 * <p>
 * If PHP has decided that filename specifies
 * a registered protocol, and that protocol is registered as a
 * network URL, PHP will check to make sure that
 * allow_url_fopen is
 * enabled. If it is switched off, PHP will emit a warning and
 * the fopen call will fail.
 * </p>
 * <p>
 * The list of supported protocols can be found in . Some protocols (also referred to as
 * wrappers) support context
 * and/or &php.ini; options. Refer to the specific page for the
 * protocol in use for a list of options which can be set. (e.g.
 * &php.ini; value user_agent used by the
 * http wrapper).
 * </p>
 * <p>
 * On the Windows platform, be careful to escape any backslashes
 * used in the path to the file, or use forward slashes.
 * </p>
 * <pre>
 * <?php
 * $handle = fopen("c:\\folder\\resource.txt", "r");
 * ?>
 * </pre>
 * @param string $mode <p>
 * The mode parameter specifies the type of access
 * you require to the stream. It may be any of the following:
 * <table>
 * A list of possible modes for fopen
 * using mode
 * <tr valign="top">
 * <td>mode</td>
 * <td>Description</td>
 * </tr>
 * <tr valign="top">
 * <td>'r'</td>
 * <td>
 * Open for reading only; place the file pointer at the
 * beginning of the file.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'r+'</td>
 * <td>
 * Open for reading and writing; place the file pointer at
 * the beginning of the file.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'w'</td>
 * <td>
 * Open for writing only; place the file pointer at the
 * beginning of the file and truncate the file to zero length.
 * If the file does not exist, attempt to create it.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'w+'</td>
 * <td>
 * Open for reading and writing; place the file pointer at
 * the beginning of the file and truncate the file to zero
 * length. If the file does not exist, attempt to create it.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'a'</td>
 * <td>
 * Open for writing only; place the file pointer at the end of
 * the file. If the file does not exist, attempt to create it.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'a+'</td>
 * <td>
 * Open for reading and writing; place the file pointer at
 * the end of the file. If the file does not exist, attempt to
 * create it.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'x'</td>
 * <td>
 * Create and open for writing only; place the file pointer at the
 * beginning of the file. If the file already exists, the
 * fopen call will fail by returning false and
 * generating an error of level E_WARNING. If
 * the file does not exist, attempt to create it. This is equivalent
 * to specifying O_EXCL|O_CREAT flags for the
 * underlying open(2) system call.
 * </td>
 * </tr>
 * <tr valign="top">
 * <td>'x+'</td>
 * <td>
 * Create and open for reading and writing; place the file pointer at
 * the beginning of the file. If the file already exists, the
 * fopen call will fail by returning false and
 * generating an error of level E_WARNING. If
 * the file does not exist, attempt to create it. This is equivalent
 * to specifying O_EXCL|O_CREAT flags for the
 * underlying open(2) system call.
 * </td>
 * </tr>
 * </table>
 * </p>
 * <p>
 * Different operating system families have different line-ending
 * conventions. When you write a text file and want to insert a line
 * break, you need to use the correct line-ending character(s) for your
 * operating system. Unix based systems use \n as the
 * line ending character, Windows based systems use \r\n
 * as the line ending characters and Macintosh based systems use
 * \r as the line ending character.
 * </p>
 * <p>
 * If you use the wrong line ending characters when writing your files, you
 * might find that other applications that open those files will "look
 * funny".
 * </p>
 * <p>
 * Windows offers a text-mode translation flag ('t')
 * which will transparently translate \n to
 * \r\n when working with the file. In contrast, you
 * can also use 'b' to force binary mode, which will not
 * translate your data. To use these flags, specify either
 * 'b' or 't' as the last character
 * of the mode parameter.
 * </p>
 * <p>
 * The default translation mode depends on the SAPI and version of PHP that
 * you are using, so you are encouraged to always specify the appropriate
 * flag for portability reasons. You should use the 't'
 * mode if you are working with plain-text files and you use
 * \n to delimit your line endings in your script, but
 * expect your files to be readable with applications such as notepad. You
 * should use the 'b' in all other cases.
 * </p>
 * <p>
 * If you do not specify the 'b' flag when working with binary files, you
 * may experience strange problems with your data, including broken image
 * files and strange problems with \r\n characters.
 * </p>
 * <p>
 * For portability, it is strongly recommended that you always
 * use the 'b' flag when opening files with fopen.
 * </p>
 * <p>
 * Again, for portability, it is also strongly recommended that
 * you re-write code that uses or relies upon the 't'
 * mode so that it uses the correct line endings and
 * 'b' mode instead.
 * </p>
 * @param bool $use_include_path [optional] <p>
 * The optional third use_include_path parameter
 * can be set to '1' or true if you want to search for the file in the
 * include_path, too.
 * </p>
 * @param resource $context [optional] &note.context-support;
 * @return resource|false a file pointer resource on success, or false on error.
 */
function fopen ($filename, $mode, $use_include_path = false, $context = null) {}
```

```
/**
 * Open Internet or Unix domain socket connection
 * @link https://php.net/manual/en/function.stream-socket-client.php
 * @param string $remote_socket <p>
 * Address to the socket to connect to.
 * </p>
 * @param int $errno [optional] <p>
 * Will be set to the system level error number if connection fails.
 * </p>
 * @param string $errstr [optional] <p>
 * Will be set to the system level error message if the connection fails.
 * </p>
 * @param float|null $timeout [optional] <p>
 * Number of seconds until the connect() system call
 * should timeout.
 * This parameter only applies when not making asynchronous
 * connection attempts.
 * <p>
 * To set a timeout for reading/writing data over the socket, use the
 * stream_set_timeout, as the
 * timeout only applies while making connecting
 * the socket.
 * </p>
 * </p>
 * @param int $flags [optional] <p>
 * Bitmask field which may be set to any combination of connection flags.
 * Currently the select of connection flags is limited to
 * STREAM_CLIENT_CONNECT (default),
 * STREAM_CLIENT_ASYNC_CONNECT and
 * STREAM_CLIENT_PERSISTENT.
 * </p>
 * @param resource $context [optional] <p>
 * A valid context resource created with stream_context_create.
 * </p>
 * @return resource|false On success a stream resource is returned which may
 * be used together with the other file functions (such as
 * fgets, fgetss,
 * fwrite, fclose, and
 * feof), false on failure.
 */
function stream_socket_client ($remote_socket, &$errno = null, &$errstr = null, $timeout = null, $flags = null, $context = null) {}

/**
 * Create an Internet or Unix domain server socket
 * @link https://php.net/manual/en/function.stream-socket-server.php
 * @param string $local_socket <p>
 * The type of socket created is determined by the transport specified
 * using standard URL formatting: transport://target.
 * </p>
 * <p>
 * For Internet Domain sockets (AF_INET) such as TCP and UDP, the
 * target portion of the
 * remote_socket parameter should consist of a
 * hostname or IP address followed by a colon and a port number. For
 * Unix domain sockets, the target portion should
 * point to the socket file on the filesystem.
 * </p>
 * <p>
 * Depending on the environment, Unix domain sockets may not be available.
 * A list of available transports can be retrieved using
 * stream_get_transports. See
 * for a list of bulitin transports.
 * </p>
 * @param int $errno [optional] <p>
 * If the optional errno and errstr
 * arguments are present they will be set to indicate the actual system
 * level error that occurred in the system-level socket(),
 * bind(), and listen() calls. If
 * the value returned in errno is
 * 0 and the function returned false, it is an
 * indication that the error occurred before the bind()
 * call. This is most likely due to a problem initializing the socket.
 * Note that the errno and
 * errstr arguments will always be passed by reference.
 * </p>
 * @param string $errstr [optional] <p>
 * See errno description.
 * </p>
 * @param int $flags [optional] <p>
 * A bitmask field which may be set to any combination of socket creation
 * flags.
 * </p>
 * <p>
 * For UDP sockets, you must use STREAM_SERVER_BIND as
 * the flags parameter.
 * </p>
 * @param resource $context [optional] <p>
 * </p>
 * @return resource|false the created stream, or false on error.
 */
function stream_socket_server ($local_socket, &$errno = null, &$errstr = null, $flags = null, $context = null) {}

/**
 * Accept a connection on a socket created by <function>stream_socket_server</function>
 * @link https://php.net/manual/en/function.stream-socket-accept.php
 * @param resource $server_socket
 * @param float $timeout [optional] <p>
 * Override the default socket accept timeout. Time should be given in
 * seconds.
 * </p>
 * @param string $peername [optional] <p>
 * Will be set to the name (address) of the client which connected, if
 * included and available from the selected transport.
 * </p>
 * <p>
 * Can also be determined later using
 * stream_socket_get_name.
 * </p>
 * @return resource|false Returns a stream to the accepted socket connection or FALSE on failure.
 */
function stream_socket_accept ($server_socket, $timeout = null, &$peername = null) {}

/**
 * Sends a message to a socket, whether it is connected or not
 * @link https://php.net/manual/en/function.stream-socket-sendto.php
 * @param resource $socket <p>
 * The socket to send data to.
 * </p>
 * @param string $data <p>
 * The data to be sent.
 * </p>
 * @param int $flags [optional] <p>
 * The value of flags can be any combination
 * of the following:
 * <table>
 * possible values for flags
 * <tr valign="top">
 * <td>STREAM_OOB</td>
 * <td>
 * Process OOB (out-of-band) data.
 * </td>
 * </tr>
 * </table>
 * </p>
 * @param string $address [optional] <p>
 * The address specified when the socket stream was created will be used
 * unless an alternate address is specified in address.
 * </p>
 * <p>
 * If specified, it must be in dotted quad (or [ipv6]) format.
 * </p>
 * @return int|false a result code, as an integer.
 */
function stream_socket_sendto ($socket, $data, $flags = null, $address = null) {}

/**
 * Shutdown a full-duplex connection
 * @link https://php.net/manual/en/function.stream-socket-shutdown.php
 * @param resource $stream <p>
 * An open stream (opened with stream_socket_client,
 * for example)
 * </p>
 * @param int $how <p>
 * One of the following constants: STREAM_SHUT_RD
 * (disable further receptions), STREAM_SHUT_WR
 * (disable further transmissions) or
 * STREAM_SHUT_RDWR (disable further receptions and
 * transmissions).
 * </p>
 * @return bool true on success or false on failure.
 * @since 5.2.1
 */
function stream_socket_shutdown ($stream, $how) {}
```


<br/><br/><br/><br/><br/>
### 参考资料

深入浅出讲解：php的socket通信 <https://www.cnblogs.com/aipiaoborensheng/p/6708963.html>

php中socket、fsockopen、curl、stream 区别 <https://blog.csdn.net/chadxia88_go/article/details/78465072>

PHP 手册 函数参考 其它服务 网络 网络 函数 fsockopen <https://www.php.net/manual/zh/function.fsockopen.php>

PHP 兼容 Curl/Socket/Stream 的 HTTP 操作类 <https://www.cnblogs.com/softwaredevelop/archive/2010/04/08/1707018.html>

了解PHP中Stream（流）的概念与用法 <http://www.nowamagic.net/librarys/veda/detail/2587>

PHP常用技术（五）之socket的简单使用 <https://www.cnblogs.com/qiye5757/p/9721401.html>

