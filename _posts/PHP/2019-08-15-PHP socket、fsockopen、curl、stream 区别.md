---
layout: post
categories: PHP
title: PHP socket、fsockopen、curl、stream 区别
meta: socket、fsockopen、curl、stream 区别
---
* content
{:toc}

### 正文



socket 水泥、沙子，底层的东西

fsockopen 水泥预制件，可以用来搭房子

curl 毛坯房，自己装修一下就能住了

水泥、沙子不但可以修房子，还能修路、修桥、大型雕塑。socket也是，不但可以用于网页传输，还能传送其他东西，可以做聊天工具、下载器、ftp……几乎可以用网络传送的东西都能用它写出来，当然，需要掌握的知识也不少，例如建墙你就要知道怎么让墙笔直、不易倒、防冻、隔热等等都需要自己学

预制件你就不用管它是否笔直、结构如何、怎样隔热了，这些造的人帮你想好了，你想的就是怎样搭成你想要的形状就行。fsockopen就是，你可以忽略socket里面的creat, connect, send, recv等等函数的用法，直接就open了

毛坯房就更简单了，你装修就能住，最简单刷墙就行了，但想更舒适，就用更多更好的装修材料吧，但缺点就是——这是房子，你不能把它改造为渡河、
交通的用途，只能住curl也一样，各种连接什么的都帮你做好了，底层容错处理也做了，你就传参数给它就能得到你想要的结果，
但缺点就是只能http / ftp，你想把它改成聊天工具，那就难难难了

stream_socket_client 和 fsockopen 没有本质上的区别

socket 是一个封装了 TCP/IP 操作的工具包
stream_socket_client 和 fsockopen 分属不同流派的对 socket 的封装

就好比有人喜欢用 zf 框架，有人喜欢 tp 框架一般
都不喜欢的就直接用原生 php 代码，好比直接用 socket 函数集

fsockopen 是比较底层的调用，属于网络系统的socket调用，而curl经过的包装支持HTTPS认证，HTTP POST方法， HTTP PUT方法， FTP上传， 
kerberos认证，HTTP上传，代理服务器， cookies，用户名/密码认证，下载文件断点续传，上载文件断点续传，http代理服务器管道（ proxy tunneling），
甚至它还支持IPv6， socks5代理服务器,，通过http代理服务器上传文件到FTP服务器等等，功能十分强大。fsockopen 返回的是没有处理过的数据，
包括数据的长度数据内容和数据的结束符。而curl是处理后的内容。

在用户使用时，curl 更加方便，但其参数很多，配置稍微复杂，fsockopen 则有固定的几个参数，简单，但获取结果可能需要再做处理。

那么file_get_contents呢？

有些时候用 file_get_contents() 调用外部文件容易超时报错。curl 效率比 file_get_contents() 和 fsockopen() 高一些，原因是CURL会自动对DNS信息进行缓存。

file_get_contents / curl / fsockopen 在当前所请求环境下选择性操作，没有一概而论。

file_get_contents 需要php.ini里开启allow_url_fopen，请求http时，使用的是http_fopen_wrapper，不会keeplive的话curl是可以的。 file_get_contents()单个执行效率高，返回没有头的信息。 

这个是读取一般文件的时候并没有什么问题，但是在读取远程问题的时候有可能就会出现问题。如果是要打一个持续连接，多次请求多个页面。那么file_get_contents和fopen就会出问题。取得的内容也可能会不对。所以做一些类似采集工作的时候，肯定就有问题了。 

fsockopen 较底层，可以设置基于UDP或是TCP协议去交互，配置麻烦，不易操作。返回完整信息。 

总之，file_get_contents 和 curl 能干的，socket都能干。socket能干的，curl 就不一定能干了。file_get_contents 更多的时候只是去拉取数据。效率比较高也比较简单。 

只讨论 curl 与file_get_contents 的话，有这么一些结论：

1. fopen /file_get_contents 每次请求都会重新做DNS查询，并不对DNS信息进行缓存。但是CURL会自动对DNS信息进行缓存。对同一域名下的网页或者图片的请求只需要一次DNS查询。这大大减少了DNS查询的次数。所以CURL的性能比fopen /file_get_contents 好很多。

2. fopen /file_get_contents在请求HTTP时，使用的是http_fopen_wrapper，不会keeplive。而curl却可以。这样在多次请求多个链接时，curl效率会好一些。

3. fopen / file_get_contents函数会受到php.ini文件中allow_url_open选项配置的影响。如果该配置关闭了，则该函数也就失效了。而curl不受该配置的影响。

4. curl可以模拟多种请求，例如：POST数据，表单提交等，用户可以按照自己的需求来定制请求。而fopen / file_get_contents只能使用get方式获取数据。

PS：file_get_contents()函数获取https链接内容的时候，需要php 中mod_ssl的支持(或安装opensll)。

结论就是，curl 效率及稳定都比 file_get_contents() 要好，fsockopen 也很强大，但是比较偏底层。


兼容 Curl/Socket/Stream 的 HTTP 操作类：

```
代码

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
  * @var 使用 CURL  
  */  
 const TYPE_CURL   = 1;   
 /**  
  * @var 使用 Socket  
  */    
 const TYPE_SOCK   = 2;   
 /**  
  * @var 使用 Stream  
  */    
 const TYPE_STREAM  = 3;   
    
    
 /**  
  * 保证对象不被clone  
  */  
 private function __clone() {}   
  
    /**  
  * 构造函数  
  */  
 private function __construct() {}   
  
  
 /**  
  * HTTP工厂操作方法  
  *  
  * @param string $url 需要访问的URL  
  * @param int $type 需要使用的HTTP类  
  * @return object  
  */  
 public static function factory($url = '', $type = self::TYPE_SOCK){   
  if ($type == ''){   
   $type = self::TYPE_SOCK;   
  }   
  switch($type) {   
   case self::TYPE_CURL :   
    if (!function_exists('curl_init')){   
     throw new Exception(__CLASS__ . " PHP CURL extension not install");   
    }   
    $obj = Http_Curl::getInstance($url);   
    break;   
   case self::TYPE_SOCK :   
    if (!function_exists('fsockopen')){   
     throw new Exception(__CLASS__ . " PHP function fsockopen() not support");   
    }       
    $obj = Http_Sock::getInstance($url);   
    break;   
   case self::TYPE_STREAM :   
    if (!function_exists('stream_context_create')){   
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
 public static function makeQuery($data, $sep = '&'){   
  $encoded = '';   
  while (list($k,$v) = each($data)) {    
   $encoded .= ($encoded ? "$sep" : "");   
   $encoded .= rawurlencode($k)."=".rawurlencode($v);    
  }    
  return $encoded;     
 }   
    
}   
  
  
  
  
/**  
 * 使用CURL 作为核心操作的HTTP访问类  
 *  
 * @desc CURL 以稳定、高效、移植性强作为很重要的HTTP协议访问客户端，必须在PHP中安装 CURL 扩展才能使用本功能  
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
 private function __construct($url){   
  $this->uri = $url;   
 }   
  
 /**  
  * 保证对象不被clone  
  */  
 private function __clone() {}   
  
  
 /**  
  * 获取对象唯一实例  
  *  
  * @param string $configFile 配置文件路径  
  * @return object 返回本对象实例  
  */  
 public static function getInstance($url = ''){   
  if (!(self::$_instance instanceof self)){   
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
 public function setHeader($header){   
  if (emptyempty($header)) {   
   return;   
  }   
  if (is_array($header)){   
   foreach ($header as $k => $v){   
    $this->header[] = is_numeric($k) ? trim($v) : (trim($k) .": ". trim($v));       
   }   
  } elseif (is_string($header)){   
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
 public function setCookie($cookie){   
  if (emptyempty($cookie)) {   
   return;   
  }   
  if (is_array($cookie)){   
   $this->cookies = Http::makeQuery($cookie, ';');   
  } elseif (is_string($cookie)){   
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
 public function setVar($vars){   
  if (emptyempty($vars)) {   
   return;   
  }   
  if (is_array($vars)){   
   $this->vars = $vars;   
  }    
 }   
    
 /**  
  * 设置要请求的URL地址  
  *  
  * @param string $url 需要设置的URL地址  
  * @return void  
  */  
 public function setUrl($url){   
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
 public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function send($method = 'GET', $timeout = 5, $options = array()){   
  //处理参数是否为空   
  if ($this->uri == ''){   
   throw new Exception(__CLASS__ .": Access url is empty");   
  }   
       
  //初始化CURL   
        $ch = curl_init();   
        curl_setopt($ch, CURLOPT_HEADER, 0);   
        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);   
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);   
        curl_setopt($ch, CURLOPT_TIMEOUT, $timeout);   
           
        //设置特殊属性   
        if (!emptyempty($options)){   
         curl_setopt_array($ch , $options);   
        }           
        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)){   
         $query = Http::makeQuery($this->vars);   
         $parse = parse_url($this->uri);   
         $sep = isset($parse['query'])  ?  '&'  : '?';   
         $this->uri .= $sep . $query;   
        }   
        //处理POST请求数据   
        if ($method == 'POST'){   
            curl_setopt($ch, CURLOPT_POST, 1 );   
            curl_setopt($ch, CURLOPT_POSTFIELDS, $this->vars);   
        }   
           
        //设置cookie信息   
        if (!emptyempty($this->cookies)){   
            curl_setopt($ch, CURLOPT_COOKIE, $this->cookies);   
        }   
        //设置HTTP缺省头   
        if (emptyempty($this->header)){   
         $this->header = array(   
          'User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)',   
          //'Accept-Language: zh-cn',             
          //'Cache-Control: no-cache',   
         );   
        }   
        curl_setopt($ch, CURLOPT_HTTPHEADER, $this->header);   
  
        //发送请求读取输数据   
        curl_setopt($ch, CURLOPT_URL, $this->uri);           
        $data = curl_exec($ch);   
        if( ($err = curl_error($ch)) ){   
            curl_close($ch);   
   throw new  Exception(__CLASS__ ." error: ". $err);   
        }   
        curl_close($ch);   
        return $data;   
 }   
    
    
    
}   
  
  
  
  
/**  
 * 使用 Socket操作(fsockopen) 作为核心操作的HTTP访问接口  
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
 private function __construct($url){   
  $this->uri = $url;   
 }   
  
 /**  
  * 保证对象不被clone  
  */  
 private function __clone() {}   
  
  
 /**  
  * 获取对象唯一实例  
  *  
  * @param string $configFile 配置文件路径  
  * @return object 返回本对象实例  
  */  
 public static function getInstance($url = ''){   
  if (!(self::$_instance instanceof self)){   
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
 public function setHeader($header){   
  if (emptyempty($header)) {   
   return;   
  }   
  if (is_array($header)){   
   foreach ($header as $k => $v){   
    $this->header[] = is_numeric($k) ? trim($v) : (trim($k) .": ". trim($v));       
   }   
  } elseif (is_string($header)){   
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
 public function setCookie($cookie){   
  if (emptyempty($cookie)) {   
   return;   
  }   
  if (is_array($cookie)){   
   $this->cookies = Http::makeQuery($cookie, ';');   
  } elseif (is_string($cookie)){   
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
 public function setVar($vars){   
  if (emptyempty($vars)) {   
   return;   
  }   
  if (is_array($vars)){   
   $this->vars = $vars;   
  }    
 }   
    
 /**  
  * 设置要请求的URL地址  
  *  
  * @param string $url 需要设置的URL地址  
  * @return void  
  */  
 public function setUrl($url){   
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
 public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function send($method = 'GET', $timeout = 5, $options = array()){   
  //处理参数是否为空   
  if ($this->uri == ''){   
   throw new Exception(__CLASS__ .": Access url is empty");   
  }   
     
        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)){   
         $query = Http::makeQuery($this->vars);   
         $parse = parse_url($this->uri);   
         $sep = isset($parse['query'])&&($parse['query']!='')  ?  '&'  : '?';   
         $this->uri .= $sep . $query;   
        }   
           
        //处理POST请求数据   
        $data = '';   
        if ($method == 'POST' && !emptyempty($this->vars)){   
         $data = Http::makeQuery($this->vars);   
          $this->setHeader('Content-Type: application/x-www-form-urlencoded');     
         $this->setHeader('Content-Length: '. strlen($data));   
        }   
           
  //解析URL地址   
  $url = parse_url($this->uri);   
  $host = $url['host'];   
  $port = isset($url['port']) && ($url['port']!='') ? $url['port'] : 80;   
  $path = isset($url['path']) && ($url['path']!='') ? $url['path'] : '/';   
  $path .= isset($url['query']) ? "?". $url['query'] : '';   
     
  //组织HTTP请求头信息   
  array_unshift(&$this->header, $method ." ". $path ." HTTP/1.1");   
        $this->setHeader('User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)');   
  if (!preg_match("/^[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}$/", $host)){   
   $this->setHeader("Host: ".$host);   
  }   
  if ($this->cookies != ''){   
   $this->setHeader("Cookie: ". $this->cookies);   
  }   
  $this->setHeader("Connection: Close");   
  //'Accept-Language: zh-cn',   
     //'Cache-Control: no-cache',   
        
     //构造请求信息   
     $header = '';   
  foreach ($this->header as $h) {   
   $header .= $h ."\r\n";   
  }   
  $header .= "\r\n";   
  if ($method == 'POST' && $data != ''){   
   $header .= $data ."\r\n";   
  }   
     
  //连接服务器发送请求数据   
  $ip = gethostbyname($host);   
  if (!($fp = fsockopen($ip, $port, &$errno, &$errstr, $timeout))){   
   throw new Exception(__CLASS__ .": Can't connect $host:$port, errno:$errno,message:$errstr");   
  }   
  fputs($fp, $header);   
  $lineSize = 1024;     
     
  //处理301,302跳转页面访问   
  $line = fgets($fp, $lineSize);   
  $first = preg_split("/\s/", trim($line));   
  if ( isset($first[1]) && in_array($first[1], array('301','302')) ){   
   while (!feof($fp)) {      
    $line = fgets($fp, $lineSize);   
    $second = preg_split("/\s/", trim($line));   
    if (ucfirst(trim($second[0]))=='Location:' && $second[1]!=''){   
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
    $buf .=$line;    
   }    
  }   
  fclose($fp);   
     
  return $buf;    
 }   
     
  
}   
  
  
  
  
  
  
/**  
 * 使用文件流操作函数为核心操作的HTTP访问接口  
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
 private function __construct($url){   
  $this->uri = $url;   
 }   
  
 /**  
  * 保证对象不被clone  
  */  
 private function __clone() {}   
  
  
 /**  
  * 获取对象唯一实例  
  *  
  * @param string $configFile 配置文件路径  
  * @return object 返回本对象实例  
  */  
 public static function getInstance($url = ''){   
  if (!(self::$_instance instanceof self)){   
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
 public function setHeader($header){   
  if (emptyempty($header)) {   
   return;   
  }   
  if (is_array($header)){   
   foreach ($header as $k => $v){   
    $this->header[] = is_numeric($k) ? trim($v) : (trim($k) .": ". trim($v));       
   }   
  } elseif (is_string($header)){   
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
 public function setCookie($cookie){   
  if (emptyempty($cookie)) {   
   return;   
  }   
  if (is_array($cookie)){   
   $this->cookies = Http::makeQuery($cookie, ';');   
  } elseif (is_string($cookie)){   
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
 public function setVar($vars){   
  if (emptyempty($vars)) {   
   return;   
  }   
  if (is_array($vars)){   
   $this->vars = $vars;   
  }    
 }   
    
 /**  
  * 设置要请求的URL地址  
  *  
  * @param string $url 需要设置的URL地址  
  * @return void  
  */  
 public function setUrl($url){   
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
 public function get($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function post($url = '', $vars = array(), $header = array(), $cookie = '', $timeout = 5, $options = array()){   
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
 public function send($method = 'GET', $timeout = 5, $options = array()){   
  //处理参数是否为空   
  if ($this->uri == ''){   
   throw new Exception(__CLASS__ .": Access url is empty");   
  }   
  $parse = parse_url($this->uri);   
  $host = $parse['host'];   
     
        //处理GET请求参数   
        if ($method == 'GET' && !emptyempty($this->vars)){   
         $query = Http::makeQuery($this->vars);   
         $sep = isset($parse['query'])&&($parse['query']!='')  ?  '&'  : '?';   
         $this->uri .= $sep . $query;    
        }   
           
           
        //处理POST请求数据   
        $data = '';   
        if ($method == 'POST' && !emptyempty($this->vars)){   
         $data = Http::makeQuery($this->vars);    
        }   
           
        //设置缺省头   
        $this->setHeader('User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; InfoPath.1)');   
  if (!preg_match("/^[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}\.[\d]{1,3}$/", $host)){   
   $this->setHeader("Host: ".$host);   
  }   
  if ($this->cookies != ''){   
   $this->setHeader("Cookie: ". $this->cookies);   
  }   
  $this->setHeader("Connection: Close");   
  //'Accept-Language: zh-cn',   
     //'Cache-Control: no-cache',           
           
        //构造头信息   
        $opts = array(   
         'http' => array(   
          'method'   => $method,   
          'timeout'  => $timeout,   
         )   
        );   
        if ($data != ''){   
         $opts['http']['content'] = $data;   
        }   
        $opts['http']['header'] = '';   
        foreach ($this->header as $h){   
         $opts['http']['header'] .= $h . "\r\n";   
        }   
        //print_r($opts);exit;   
           
        //读取扩展设置选项   
        if (!emptyempty($options)){   
         isset($options['proxy']) ? $opts['http']['proxy'] = $options['proxy'] : '';            
         isset($options['max_redirects']) ? $opts['http']['max_redirects'] = $options['max_redirects'] : '';   
         isset($options['request_fulluri']) ? $opts['http']['request_fulluri'] = $options['request_fulluri'] : '';   
        }   
           
        //发送数据返回   
       $context = stream_context_create($opts);   
       if (($buf = file_get_contents($this->uri, null, $context)) === false){   
   throw new Exception(__CLASS__ .": file_get_contents(". $this->uri .") fail");   
       }   
          
       return $buf;     
          
 }   
     
  
}
```

<br/><br/><br/><br/><br/>
### 参考资料

php中socket、fsockopen、curl、stream 区别 <https://blog.csdn.net/chadxia88_go/article/details/78465072>

PHP 兼容 Curl/Socket/Stream 的 HTTP 操作类 <https://www.cnblogs.com/softwaredevelop/archive/2010/04/08/1707018.html>

了解PHP中Stream（流）的概念与用法 <http://www.nowamagic.net/librarys/veda/detail/2587>

