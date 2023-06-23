---
layout: post
categories: PHP
title: PHP Socket使用
meta: PHP Socket使用
---
* content
{:toc}

## 文件服务器

开发中中碰到一个问题，B项目要用到A项目中的图片，如何实现呢？

这里就要用到文件服务器，A项目和B项目共同使用文件服务器，包括文件新增、修改、删除。

PHP中使用Stream系列函数，折腾了一天没实现，问题卡在`fread()`函数上。
因为文件会比较大，如何读取和组装就成了问题，关键就是要解决这个问题。

最后选择了使用Socket实现。

实现过程中需要考虑应用如何认证、文件是新增还是删除。下面写了一个无认证的新增文件服务端程序。

服务端`server.php`：
```php
<?php
//设置不超时
set_time_limit(0);

class SocketServer
{
    public function __construct($port)
    {
        $listen_socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
        socket_bind($listen_socket, '0.0.0.0', $port);
        socket_listen($listen_socket);

        while (true) {
            $conn = socket_accept($listen_socket);
            $data = '';

            try {
                while (!preg_match('/\r\n\r\n/', $data)) {
                    $data .= socket_read($conn, 1024);
                }
                $data = preg_replace('/\r\n\r\n/', '', $data);
                $data = explode('<<<<<<>>>>>>', $data);

                $file = new FileCreate($data['0']);
                $file->write($data['1']);

                $data = [
                    'code' => '1',
                    'data' => [
                        'path' => $file->getFilePath()
                    ],
                    'msg' => 'ok'
                ];
            } catch (Throwable $e) {
                $data = [
                    'code' => '-1',
                    'data' => [],
                    'msg' => $e->getMessage()
                ];
            }

            $data = json_encode($data);

            socket_write($conn, $data, strlen($data));
            socket_close($conn);
        }

        socket_close($listen_socket);
    }
}

class FileCreate
{
    private $fd;
    private $path_out;

    public function __construct(string $name = '')
    {
        $path_upload = '/upload/' .date('Ymd');

        $path_www = './www' .$path_upload;

        $suffix = strtolower(pathinfo($name, PATHINFO_EXTENSION));
        $suffix = $suffix && preg_match("/^[a-zA-Z0-9]+$/", $suffix) ? $suffix : 'file';
        $filename = time() .uniqid() .'.' .$suffix;

        if (!is_dir($path_www)) {
            mkdir($path_www, 0755, true);
        }

        $this->fd = fopen($path_www .'/' .$filename, 'w+');

        $this->path_out = $path_upload .'/' .$filename;
    }

    public function write($stream)
    {
        fwrite($this->fd, $stream);
        fclose($this->fd);
    }

    public function getFilePath()
    {
        return $this->path_out;
    }
}

new SocketServer(1034);
```

运行：
> php server.php 1>/dev/null 2>&1 &

最好使用Supervisor。

具体在使用中，后端还要可以对文件进行管理，借用Yii2搭了个管理项目，命令行也使用项目中的console部分。

看一下封装：
```php
<?php
namespace console\controllers;

use common\models\App;
use common\models\File;
use Yii;
use yii\console\Controller;

class FileController extends Controller
{
    public function actionSocket()
    {
        if (($listen_socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP)) === false) {
            echo "socket_create() failed: reason: " . socket_strerror(socket_last_error()) . "\n";
            die;
        }

        if ((socket_bind($listen_socket, '0.0.0.0', Yii::$app->fileShare->port)) === false) {
            echo "socket_bind() failed: reason: " . socket_strerror(socket_last_error($listen_socket)) . "\n";
            die;
        }

        if (socket_listen($listen_socket) === false) {
            echo "socket_listen() failed: reason: " . socket_strerror(socket_last_error($listen_socket)) . "\n";
            die;
        }

        while (true) {
            if (($conn = socket_accept($listen_socket)) === false) {
                continue;
            }

            $data = '';

            try {
                while (!preg_match('/\r\n\r\n/', $data)) {
                    $data .= socket_read($conn, 4096);
                }
                $data = preg_replace('/\r\n\r\n/', '', $data);
                $data = explode('<<<<<<>>>>>>', $data);

                if (!in_array(count($data), [4, 5]) || empty($data['0']) || empty($data['1']) || !in_array($data['2'], ['add', 'delete'])) {
                    throw new \yii\base\Exception('参数错误');
                }

                $modelApp = App::find()
                    ->where(['app_uuid' => $data['0']])
                    ->one();
                if (empty($modelApp) || $modelApp->status != App::STATUS_ENABLE) {
                    throw new \yii\base\Exception('应用未找到或不可用');
                }

                if (!Yii::$app->security->validatePassword($data['1'], $modelApp->password_hash)) {
                    throw new \yii\base\Exception('应用密码错误');
                }

                if ($data['2'] == 'add') {
                    if (empty($data['3']) || empty($data['4'])) {
                        throw new \yii\base\Exception('文件参数为空');
                    }

                    $file_path = Yii::$app->fileShare->write($data['3'], $data['4']);

                    $modelFile = new File();
                    $modelFile->app_id = $modelApp->app_id;
                    $modelFile->location = $file_path;
                    $modelFile->status = File::STATUS_ENABLE;
                    $modelFile->created_at = $modelFile->updated_at = time();
                    if (!($modelFile->save())) {
                        throw new \yii\base\Exception('文件记录保存失败');
                    }

                    $data = [
                        'code' => '1',
                        'data' => [
                            'path' => $file_path
                        ],
                        'msg' => 'ok'
                    ];
                } elseif ($data['2'] == 'delete') {
                    if (empty($data['3'])) {
                        throw new \yii\base\Exception('文件地址为空');
                    }

                    $modelFile = File::find()
                        ->where(['app_id' => $modelApp->app_id, 'location' => $data['3']])
                        ->one();
                    if (empty($modelFile) || $modelFile->status != File::STATUS_ENABLE) {
                        throw new \yii\base\Exception('文件未找到或已被删除');
                    }

                    $modelFile->status = File::STATUS_DELETE;
                    if (!($modelFile->save())) {
                        throw new \yii\base\Exception('文件删除失败');
                    }

                    $data = [
                        'code' => '1',
                        'data' => [],
                        'msg' => 'ok'
                    ];
                }
            } catch (\Throwable $e) {
                $data = [
                    'code' => '-1',
                    'data' => [],
                    'msg' => $e->getMessage()
                ];
            }

            $data = json_encode($data) ."\r\n\r\n";

            socket_write($conn, $data, strlen($data));
            socket_close($conn);
        }

        socket_close($listen_socket);
    }
}
```

```php
<?php

namespace common\components;

use yii;
use yii\base\Component;

class FileShare extends Component
{
    public $path_www;
    public $path_upload;
    public $port;

    public function init()
    {
        parent::init();
    }

    public function write($name, $stream)
    {
        $path_upload = $this->path_upload .date('Ymd');
        $path_www = Yii::getAlias($this->path_www) .$path_upload;

        $suffix = strtolower(pathinfo($name, PATHINFO_EXTENSION));
        $suffix = $suffix && preg_match("/^[a-zA-Z0-9]+$/", $suffix) ? $suffix : 'file';
        $filename = time() .uniqid() .'.' .$suffix;

        if (!is_dir($path_www)) {
            mkdir($path_www, 0755, true);
        }

        $fd = fopen($path_www .'/' .$filename, 'w+');
        fwrite($fd, $stream);
        fclose($fd);

        return $path_upload .'/' .$filename;
    }
}
```

配置文件：
```
'components' => [
    'fileShare' => [
        'class' => 'common\components\FileShare',
        'path_www' => '@backend/web',
        'path_upload' => '/upload/',
        'port' => '1055',
    ],
]
```

启动命令：
> php yii file/socket 1>/dev/null 2>&1 &

最后说一下这里使用 传统socket 与 select、epoll、协程 的区别。
对于这里的 监听socket，无论是在 传统socket，还是 select 或 epoll 中，都是同步阻塞服务，QPS没有什么区别，
都是对单端口的监听，多开几个进程也一样。
区别是在使用协程上。
使用PHP原生的yield，这里数据库连接需要一段时间，发起数据库连接的同时可以用协程把文件内容写入，这样在一定程度上可以提高QPS。
不过使用Swoole的协程，可以在 监听socket 上做文章，相当于在 各监听socket 间做协程切换，所以可以大幅度提高QPS。

### 客户端

客户端原本想用Socket实现的，发现Nginx请求php的web服务时报错：
```
Call to undefined function socket_create()
```

也是神奇，最后用了stream来实现，`fread()` 时需要注意第二个参数，这个参数是必填，
注意读取的大小，小了会不完整，大了又具体不知道究竟多大，尽量大一些。

客户端：
```php
<?php
$file_name = $_FILES['image']['name'];
$file_path = $_FILES['image']['tmp_name'];

$data = $file_name .'<<<<<<>>>>>>';
$data .= file_get_contents($file_path);

$socket = stream_socket_client('tcp://192.168.56.108:1034', $errno, $errstr);
if (!$socket) {
    die($errno . $errstr);
}
fwrite($socket, $data);
fwrite($socket, "\r\n\r\n");

$response = fread($socket, 8192);
$response = json_decode($response, true);

// 返回的文件地址
$file_path = '';  
if ($response['code'] == 1) {
    $file_path = $response['data']['path'];
}
```

除了新增文件，还要删除文件，这里自己写了个封装类：
```php
<?php

namespace common\components;

class FileShare
{
    private $link;
    private $app_uuid;
    private $app_password;

    public function __construct($ip ='', $port = '', $app_uuid = '', $app_password = '')
    {
        $this->link = 'tcp://' .$ip .':' .$port;
        $this->app_uuid = $app_uuid;
        $this->app_password = $app_password;
    }

    public function add($file_name = '', $file_path = '')
    {
        if (empty($file_name)) {
            throw new \Exception('file_name is empty');
        }

        if (!file_exists($file_path)) {
            throw new \Exception('file is not found');
        }

        $data = $this->app_uuid .'<<<<<<>>>>>>'
            . $this->app_password .'<<<<<<>>>>>>'
            . 'add' .'<<<<<<>>>>>>'
            . $file_name .'<<<<<<>>>>>>';
        $data .= file_get_contents($file_path) ."\r\n\r\n";

        $socket = stream_socket_client($this->link, $errno, $errstr);
        if (!$socket) {
            throw new \Exception($errno . $errstr);
        }

        fwrite($socket, $data);

        $response = fread($socket, 8192);
        $response = json_decode($response, true);

        fclose($socket);

        if ($response['code'] != 1) {
            throw new \Exception($response['msg']);
        }

        return $response['data']['path'];
    }

    public function delete($path = '')
    {
        if (empty($file_name)) {
            throw new \Exception('path is empty');
        }

        $data = $this->app_uuid .'<<<<<<>>>>>>'
            . $this->app_password .'<<<<<<>>>>>>'
            . 'delete' .'<<<<<<>>>>>>'
            . $path
            . "\r\n\r\n";

        $socket = stream_socket_client($this->link, $errno, $errstr);
        if (!$socket) {
            throw new \Exception($errno . $errstr);
        }

        fwrite($socket, $data);

        $response = fread($socket, 8192);
        $response = json_decode($response, true);

        fclose($socket);

        if ($response['code'] != 1) {
            throw new \Exception($response['msg']);
        }

        return true;
    }
}
```

### 图片压缩

图片太大时，会用到压缩，这里提供一个图片压缩函数：
```php
<?php
/**
 * @param $image_src  源文件地址
 * @param $image_dist  目的文件地址
 * @param int $dist_width  目的宽度
 * @param int $dist_height  目的高度
 * @param int $quality  压缩质量
 * @param string $filename  文件名称，用于确定目的文件后缀
 * @return bool
 * @throws UploadException
 */
function compress($image_src, $image_dist, $dist_width = 320, $dist_height = 240, $quality = 70, $filename = "")
{
    $imagecreate_list = [
        1 => function ($path) {
            return imagecreatefromgif($path);
        },
        2 => function ($path) {
            return imagecreatefromjpeg($path);
        },
        3 => function ($path) {
            return imagecreatefrompng($path);
        },
        // 4   =>  function($path) {return imagecreatefromswf($path);},
        // 5   =>  function($path) {return imagecreatefrompsd($path);},
        6 => function ($path) {
            return imagecreatefrombmp($path);
        },
        // 7   =>  function($path) {return imagecreatefromtiff($path);},
        // 8   =>  function($path) {return imagecreatefromtiff($path);},
        9 => function ($path) {
            return imagecreatefromjpeg($path);
        },
        10 => function ($path) {
            return imagecreatefromjpeg($path);
        },
        11 => function ($path) {
            return imagecreatefromjpeg($path);
        },
        12 => function ($path) {
            return imagecreatefromjpeg($path);
        },
        // 13   =>  function($path) {return imagecreatefromswc($path);},
        // 14   =>  function($path) {return imagecreatefromiff($path);},
        15 => function ($path) {
            return imagecreatefromwbmp($path);
        },
        16 => function ($path) {
            return imagecreatefromxbm($path);
        },
        // 17   =>  function($path) {return imagecreatefromico($path);},
        18 => function ($path) {
            return imagecreatefromwebp($path);
        },
    ];

    // 后缀名
    $file_extension = null;
    if (!empty($filename) && strrpos($filename, '.') !== false) {
        $file_extension = substr($filename, strrpos($filename, '.') + 1);
    }

    try {
        list($src_width, $src_height, $stype) = getimagesize($image_src);
        if ($dist_width && ($src_width < $src_height)) {
            $dist_width = ($dist_height / $src_height) * $src_width;
        } else {
            $dist_height = ($dist_width / $src_width) * $src_height;
        }

        $image_resource = imagecreatetruecolor($dist_width, $dist_height);

        $src_image = $imagecreate_list[$stype]($image_src);

        imagecopyresampled($image_resource, $src_image, 0, 0, 0, 0, $dist_width, $dist_height, $src_width, $src_height);

        if (strtolower($file_extension) == 'jpg' || strtolower($file_extension ) == 'jpeg') {
            imagejpeg($image_resource, $image_dist, $quality);
        } else {
            imagepng($image_resource, $image_dist, intval(($quality - 10) / 10));
        }

        imagedestroy($image_resource);
        imagedestroy($src_image);

        return true;
    } catch (\Exception $e) {
        throw new UploadException($e->getMessage());

        return false;
    }
}
```

## 问题说明

### socket_read

在看一篇文章说到：

`socket_read()`：从连接资源中读取指定字节数的数据，读取成功时，返回字符串，失败时，返回FALSE。没有数据时，返回空字符串。

所以可以用：
```
$data = '';
// 循环读取指定长度的服务器响应数据
while($response = socket_read($sock, 4))
{
    $data .= $response;
}
```

在服务端尝试下来才发现，这样是可以用，但是是在客户端使用了 `socket_close()` 断开连接的情况下。
只适合于单向通信，如果服务端要把处理结果发送给客户端（因为连接已断开），就使用不了了。
不过客户端倒是可以使用这个函数，当服务端使用了 `socket_close()` 断开连接时，客户端获取到所有返回的信息，
然后进行处理。





<br/><br/><br/><br/><br/>
## 参考资料

<https://php.p2hp.com/manual/zh/function.socket-read.php>

PHP 下的 Socket 编程 <https://zhuanlan.zhihu.com/p/374036250>  ，下面有几个文章集合，可以看看


