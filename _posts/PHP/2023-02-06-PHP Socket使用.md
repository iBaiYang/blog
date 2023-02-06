---
layout: post
categories: PHP
title: PHP Socket使用
meta: PHP Socket使用
---
* content
{:toc}

## 正文

### 文件服务器

开发中中碰到一个问题，B项目要用到A项目中的图片，如何实现呢？

这里就要用到文件服务器，A项目和B项目共同使用文件服务器，包括文件新增、修改、删除。

PHP中使用Stream系列函数，折腾了一天没实现，问题卡在`fread()`函数上。
因为文件会大较大，如何读取和组装就成了问题，关键就是要解决这个问题。

最后选择了使用Socket实现。

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
> php server.php 

客户端
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

$response = fread($socket, 1024);
$response = json_decode($response, true);

// 返回的文件地址
$file_path = '';  
if ($response['code'] == 1) {
    $file_path = $response['data']['path'];
}
```

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

<br/><br/><br/><br/><br/>
## 参考资料


