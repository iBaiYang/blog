---
layout: post
categories: PHP
title: webman中Redis队列实现分析
meta: webman中Redis队列实现分析
---
* content
{:toc}

## 正文

webman中Redis队列实现用的包是 `webman/redis-queue` ，版本是 `v1.1.0` 。

Composer 安装 `webman/redis-queue` 。

Redis配置文件自动生成在 `config/plugin/webman/redis-queue/redis.php`，内容类似如下：
```php
<?php
return [
    'default' => [
        'host' => 'redis://127.0.0.1:6379',
        'options' => [
            'auth' => '',         // 密码，可选参数
            'db' => 0,            // 数据库
            'max_attempts'  => 5, // 消费失败后，重试次数
            'retry_seconds' => 5, // 重试间隔，单位秒
        ]
    ],
];
```

消费进程配置文件自动生成在 `config/plugin/webman/redis-queue/process.php`，内容类似如下：
```php
<?php
return [
    'consumer'  => [
        'handler'     => Webman\RedisQueue\Process\Consumer::class,
        'count'       => 8, // 可以设置多进程同时消费
        'constructor' => [
            // 消费者类目录
            'consumer_dir' => app_path() . '/queue/redis'
        ]
    ]
];
```

以及自动生成的该插件是否可用配置文件 `config/plugin/webman/redis-queue/app.php` ：
```php
<?php
return [
    'enable' => true,
];
```

### 进程配置文件加载分析

在 项目根目录下的 `start.php` 文件中有：
```php
#!/usr/bin/env php
<?php
require_once __DIR__ . '/vendor/autoload.php';

use Workerman\Worker;
use Workerman\Protocols\Http;
use Workerman\Connection\TcpConnection;
use Webman\App;
use Webman\Config;
use Webman\Route;
use Webman\Middleware;
use Dotenv\Dotenv;
use support\Request;
use support\Log;
use support\Container;

ini_set('display_errors', 'on');
error_reporting(E_ALL);

// 加载环境配置文件
if (class_exists('Dotenv\Dotenv') && file_exists(base_path() . '/.env')) {
    if (method_exists('Dotenv\Dotenv', 'createUnsafeImmutable')) {
        Dotenv::createUnsafeImmutable(base_path())->load();
    } else {
        Dotenv::createMutable(base_path())->load();
    }
}

// 加载应用配置文件，包括 自动加载、容器、数据库、插件进程 等
Config::load(config_path(), ['route', 'container']);

if ($timezone = config('app.default_timezone')) {
    date_default_timezone_set($timezone);
}

$runtime_logs_path = runtime_path() . DIRECTORY_SEPARATOR . 'logs';
if ( !file_exists($runtime_logs_path) || !is_dir($runtime_logs_path) ) {
    if (!mkdir($runtime_logs_path,0777,true)) {
        throw new \RuntimeException("Failed to create runtime logs directory. Please check the permission.");
    }
}

$runtime_views_path = runtime_path() . DIRECTORY_SEPARATOR . 'views';
if ( !file_exists($runtime_views_path) || !is_dir($runtime_views_path) ) {
    if (!mkdir($runtime_views_path,0777,true)) {
        throw new \RuntimeException("Failed to create runtime views directory. Please check the permission.");
    }
}

Worker::$onMasterReload = function () {
    if (function_exists('opcache_get_status')) {
        if ($status = opcache_get_status()) {
            if (isset($status['scripts']) && $scripts = $status['scripts']) {
                foreach (array_keys($scripts) as $file) {
                    opcache_invalidate($file, true);
                }
            }
        }
    }
};

$config = config('server');
Worker::$pidFile = $config['pid_file'];
Worker::$stdoutFile = $config['stdout_file'];
Worker::$logFile = $config['log_file'];
Worker::$eventLoopClass = $config['event_loop'] ?? '';
TcpConnection::$defaultMaxPackageSize = $config['max_package_size'] ?? 10 * 1024 * 1024;
if (property_exists(Worker::class, 'statusFile')) {
    Worker::$statusFile = $config['status_file'] ?? '';
}

if ($config['listen']) {
    $worker = new Worker($config['listen'], $config['context']);
    $property_map = [
        'name',
        'count',
        'user',
        'group',
        'reusePort',
        'transport',
    ];
    foreach ($property_map as $property) {
        if (isset($config[$property])) {
            $worker->$property = $config[$property];
        }
    }

    $worker->onWorkerStart = function ($worker) {
        require_once base_path() . '/support/bootstrap.php';
        $app = new App($worker, Container::instance(), Log::channel('default'), app_path(), public_path());
        Http::requestClass(config('app.request_class', config('server.request_class', Request::class)));
        $worker->onMessage = [$app, 'onMessage'];
    };
}

// 自定义进程
// Windows does not support custom processes.
if (\DIRECTORY_SEPARATOR === '/') {
    // 一级进程
    foreach (config('process', []) as $process_name => $config) {
        worker_start($process_name, $config);
    }
    // 插件进程
    foreach (config('plugin', []) as $firm => $projects) {
        foreach ($projects as $name => $project) {
            foreach ($project['process'] ?? [] as $process_name => $config) {
                worker_start("plugin.$firm.$name.$process_name", $config);
            }
        }
    }
}

Worker::runAll();
```

加载应用配置文件：`Config::load(config_path(), ['route', 'container']);` 。

`config_path()` 获取配置文件所在目录地址，在 `support/helpers.php` 文件中。

`support/helpers.php` 是一个帮助函数库：
```php
<?php

use support\Container;
use Workerman\Worker;
use Webman\Config;
// 省略若干...

/**
 * @return bool
 */
function is_phar()
{
    return class_exists(\Phar::class, false) && Phar::running();
}

// Phar support.
if (is_phar()) {
    define('BASE_PATH', dirname(__DIR__));
} else {
    define('BASE_PATH', realpath(__DIR__ . '/../'));
}
define('WEBMAN_VERSION', '1.3.0');

/**
 * @param $return_phar
 * @return false|string
 */
function base_path($return_phar = true)
{
    static $real_path = '';
    if (!$real_path) {
        $real_path = is_phar() ? dirname(Phar::running(false)) : BASE_PATH;
    }
    return $return_phar ? BASE_PATH : $real_path;
}

/**
 * @return string
 */
function app_path()
{
    return BASE_PATH . DIRECTORY_SEPARATOR . 'app';
}

/**
 * @return string
 */
function public_path()
{
    static $path = '';
    if (!$path) {
        $path = config('app.public_path', BASE_PATH . DIRECTORY_SEPARATOR . 'public');
    }
    return $path;
}

/**
 * 获取配置目录路径
 * @return string
 */
function config_path()
{
    return BASE_PATH . DIRECTORY_SEPARATOR . 'config';
}

/**
 * Phar support.
 * Compatible with the 'realpath' function in the phar file.
 *
 * @return string
 */
function runtime_path()
{
    static $path = '';
    if (!$path) {
        $path = config('app.runtime_path', BASE_PATH . DIRECTORY_SEPARATOR . 'runtime');
    }
    return $path;
}

// 省略若干...

/**
 * 获取配置参数值
 * @param $key
 * @param null $default
 * @return mixed
 */
function config($key = null, $default = null)
{
    return Config::get($key, $default);
}

// 省略若干...

/**
 * @param $worker
 * @param $class
 */
function worker_bind($worker, $class)
{
    $callback_map = [
        'onConnect',
        'onMessage',
        'onClose',
        'onError',
        'onBufferFull',
        'onBufferDrain',
        'onWorkerStop',
        'onWebSocketConnect'
    ];
    foreach ($callback_map as $name) {
        if (method_exists($class, $name)) {
            $worker->$name = [$class, $name];
        }
    }
    if (method_exists($class, 'onWorkerStart')) {
        call_user_func([$class, 'onWorkerStart'], $worker);
    }
}

/**
 * @param $process_name
 * @param $config
 * @return void
 */
function worker_start($process_name, $config)
{
    $worker = new Worker($config['listen'] ?? null, $config['context'] ?? []);
    $property_map = [
        'count',
        'user',
        'group',
        'reloadable',
        'reusePort',
        'transport',
        'protocol',
    ];
    $worker->name = $process_name;
    foreach ($property_map as $property) {
        if (isset($config[$property])) {
            $worker->$property = $config[$property];
        }
    }

    $worker->onWorkerStart = function ($worker) use ($config) {
        require_once base_path() . '/support/bootstrap.php';

        foreach ($config['services'] ?? [] as $server) {
            if (!class_exists($server['handler'])) {
                echo "process error: class {$server['handler']} not exists\r\n";
                continue;
            }
            $listen = new Worker($server['listen'] ?? null, $server['context'] ?? []);
            if (isset($server['listen'])) {
                echo "listen: {$server['listen']}\n";
            }
            $instance = Container::make($server['handler'], $server['constructor'] ?? []);
            worker_bind($listen, $instance);
            $listen->listen();
        }

        if (isset($config['handler'])) {
            if (!class_exists($config['handler'])) {
                echo "process error: class {$config['handler']} not exists\r\n";
                return;
            }

            $instance = Container::make($config['handler'], $config['constructor'] ?? []);
            worker_bind($worker, $instance);
        }

    };
}

// 省略若干...
```

`Config::load()` 在 `Webman\Config` 类文件中：
```php
<?php
/**
 * This file is part of webman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */

namespace Webman;

class Config
{

    /**
     * @var array
     */
    protected static $_config = [];

    /**
     * @var string
     */
    protected static $_configPath = '';

    /**
     * @var bool
     */
    protected static $_loaded = false;

    /**
     * @param $config_path 配置文件路径
     * @param array $exclude_file 排除的文件
     */
    public static function load($config_path, $exclude_file = [])
    {
        static::$_configPath = $config_path;
        if (!$config_path) {
            return;
        }
        // RecursiveDirectoryIterator 提供了一个用于递归地遍历文件系统目录的接口
        $dir_iterator = new \RecursiveDirectoryIterator($config_path, \FilesystemIterator::FOLLOW_SYMLINKS);
        // RecursiveIteratorIterator 可用于遍历递归迭代器
        $iterator = new \RecursiveIteratorIterator($dir_iterator);
        foreach ($iterator as $file) {
            /** var SplFileInfo $file */
            if (is_dir($file) || $file->getExtension() != 'php' || \in_array($file->getBaseName('.php'), $exclude_file)) {
                // 是目录、或非.php后缀文件、或在排除的文件中，跳过本次
                // getBaseName('.php') 返回没有路径信息及.php后缀的文件名称
                continue;
            }
            
            /* 判断当前配置是否可用 */ 
            $app_config_file = $file->getPath() . '/app.php';
            if (!is_file($app_config_file)) {
                continue;
            }
            // 如果配置文件的相对深度大于等于2层，则说明是个插件，查看该插件是否可用
            $relative_path = str_replace($config_path . DIRECTORY_SEPARATOR, '', substr($file, 0, -4));
            $explode = array_reverse(explode(DIRECTORY_SEPARATOR, $relative_path));
            if (count($explode) >= 2) {
                $app_config = include $app_config_file;
                if (empty($app_config['enable'])) {
                    // 如果enable属性未配置、或为空、或false，则说明该插件不可用，跳过本次
                    continue;
                }
            }
            
            $config = include $file;
            foreach ($explode as $section) {
                $tmp = [];
                $tmp[$section] = $config;
                $config = $tmp;
            }
            static::$_config = array_replace_recursive(static::$_config, $config);
        }

        // Merge database config
        foreach (static::$_config['plugin'] ?? [] as $firm => $projects) {
            foreach ($projects as $name => $project) {
                foreach ($project['database']['connections'] ?? [] as $key => $connection) {
                    static::$_config['database']['connections']["plugin.$firm.$name.$key"] = $connection;
                }
            }
        }
        if (!empty(static::$_config['database']['connections'])) {
            static::$_config['database']['default'] = static::$_config['database']['default'] ?? key(static::$_config['database']['connections']);
        }
        
        // Merge thinkorm config
        foreach (static::$_config['plugin'] ?? [] as $firm => $projects) {
            foreach ($projects as $name => $project) {
                foreach ($project['thinkorm']['connections'] ?? [] as $key => $connection) {
                    static::$_config['thinkorm']['connections']["plugin.$firm.$name.$key"] = $connection;
                }
            }
        }
        if (!empty(static::$_config['thinkorm']['connections'])) {
            static::$_config['thinkorm']['default'] = static::$_config['thinkorm']['default'] ?? key(static::$_config['thinkorm']['connections']);
        }
        
        // Merge redis config
        foreach (static::$_config['plugin'] ?? [] as $firm => $projects) {
            foreach ($projects as $name => $project) {
                foreach ($project['redis'] ?? [] as $key => $connection) {
                    static::$_config['redis']["plugin.$firm.$name.$key"] = $connection;
                }
            }
        }

        static::$_loaded = true;
    }

    /**
     * @param null $key
     * @param null $default
     * @return array|mixed|null
     */
    public static function get($key = null, $default = null)
    {
        if ($key === null) {
            return static::$_config;
        }
        $key_array = \explode('.', $key);
        $value = static::$_config;
        $finded = true;
        foreach ($key_array as $index) {
            if (!isset($value[$index])) {
                if (static::$_loaded) {
                    return $default;
                }
                $finded = false;
                break;
            }
            $value = $value[$index];
        }
        if ($finded) {
            return $value;
        }
        return static::read($key, $default);
    }

    /**
     * @param $key
     * @param $default
     * @return array|mixed|void|null
     */
    protected static function read($key, $default = null)
    {
        $path = static::$_configPath;
        if ($path === '') {
            return $default;
        }
        $keys = $key_array = \explode('.', $key);
        foreach ($key_array as $index => $section) {
            unset($keys[$index]);
            if (is_file($file = "$path/$section.php")) {
                $config = include $file;
                return static::find($keys, $config, $default);
            }
            if (!is_dir($path = "$path/$section")) {
                return $default;
            }
        }
        return $default;
    }

    /**
     * @param $key_array
     * @param $stack
     * @param $default
     * @return array|mixed
     */
    protected static function find($key_array, $stack, $default)
    {
        if (!is_array($stack)) {
            return $default;
        }
        $value = $stack;
        foreach ($key_array as $index) {
            if (!isset($value[$index])) {
                return $default;
            }
            $value = $value[$index];
        }
        return $value;
    }


    /**
     * @param $config_path
     * @param array $exclude_file
     */
    public static function reload($config_path, $exclude_file = [])
    {
        static::$_config = [];
        static::load($config_path, $exclude_file);
    }

}
```

`start.php` 中加载自定义进程：
```
if (\DIRECTORY_SEPARATOR === '/') {
    foreach (config('process', []) as $process_name => $config) {
        worker_start($process_name, $config);
    }
    foreach (config('plugin', []) as $firm => $projects) {
        foreach ($projects as $name => $project) {
            foreach ($project['process'] ?? [] as $process_name => $config) {
                worker_start("plugin.$firm.$name.$process_name", $config);
            }
        }
    }
}
```

`config('plugin', [])` 获取插件配置，返回的结果：
```
array(1) {
  ["webman"]=>
  array(3) {
    ["console"]=>
    array(1) {
      ["app"]=>
      array(7) {
        ["enable"]=>
        bool(true)
        ["phar_file_output_dir"]=>
        string(34) "/var/www/html/webman/build"
        ["phar_filename"]=>
        string(11) "webman.phar"
        ["signature_algorithm"]=>
        int(3)
        ["private_key_file"]=>
        string(0) ""
        ["exclude_pattern"]=>
        string(228) "#^(?!.*(config/plugin/webman/console/app.php|webman/console/src/Commands/(PharPackCommand.php|ReloadCommand.php)|LICENSE|composer.json|.github|.idea|doc|docs|.git|.setting|runtime|test|test_old|tests|Tests|vendor-bin|.md))(.*)$#"
        ["exclude_files"]=>
        array(5) {
          [0]=>
          string(4) ".env"
          [1]=>
          string(7) "LICENSE"
          [2]=>
          string(13) "composer.json"
          [3]=>
          string(13) "composer.lock"
          [4]=>
          string(9) "start.php"
        }
      }
    }
    ["push"]=>
    array(2) {
      ["app"]=>
      array(7) {
        ["enable"]=>
        bool(true)
        ["websocket"]=>
        string(24) "websocket://0.0.0.0:2121"
        ["api"]=>
        string(19) "http://0.0.0.0:2222"
        ["app_key"]=>
        string(32) "dfa7def75955c62f88e02526691ee155"
        ["app_secret"]=>
        string(32) "cd88c5033d8e591dd248408734be233f"
        ["channel_hook"]=>
        string(45) "http://127.0.0.1:8787/plugin/webman/push/hook"
        ["auth"]=>
        string(24) "/plugin/webman/push/auth"
      }
      ["process"]=>
      array(1) {
        ["server"]=>
        array(5) {
          ["handler"]=>
          string(18) "Webman\Push\Server"
          ["listen"]=>
          string(24) "websocket://0.0.0.0:2121"
          ["count"]=>
          int(1)
          ["reloadable"]=>
          bool(false)
          ["constructor"]=>
          array(2) {
            ["api_listen"]=>
            string(19) "http://0.0.0.0:2222"
            ["app_info"]=>
            array(1) {
              ["dfa7def75955c62f88e02526691ee155"]=>
              array(2) {
                ["channel_hook"]=>
                string(45) "http://127.0.0.1:8787/plugin/webman/push/hook"
                ["app_secret"]=>
                string(32) "cd88c5033d8e591dd248408734be233f"
              }
            }
          }
        }
      }
    }
    ["redis-queue"]=>
    array(3) {
      ["app"]=>
      array(1) {
        ["enable"]=>
        bool(true)
      }
      ["process"]=>
      array(1) {
        ["consumer"]=>
        array(3) {
          ["handler"]=>
          string(34) "Webman\RedisQueue\Process\Consumer"
          ["count"]=>
          int(6)
          ["constructor"]=>
          array(1) {
            ["consumer_dir"]=>
            string(44) "/var/www/html/webman/app/queue/redis"
          }
        }
      }
      ["redis"]=>
      array(1) {
        ["default"]=>
        array(2) {
          ["host"]=>
          string(25) "redis://192.168.56.108:6379"
          ["options"]=>
          array(4) {
            ["auth"]=>
            string(12) "eszEDCrdx341"
            ["db"]=>
            string(1) "3"
            ["max_attempts"]=>
            int(5)
            ["retry_seconds"]=>
            int(5)
          }
        }
      }
    }
  }
}
```

`worker_start()` 启动进程，调用的 `Workerman\Worker` 类实现。 

从`php start.php start`启动中可以看到加载的进程名：
```
root@514444b84e17:/var/www/html/webman# php start.php start
Workerman[start.php] start in DEBUG mode
------------------------------------------------------ WORKERMAN -------------------------------------------------------
Workerman version:4.0.30          PHP version:7.4.28
------------------------------------------------------- WORKERS --------------------------------------------------------
proto   user            worker                                listen                      processes    status
tcp     root            webman                                http://0.0.0.0:8787         8             [OK]
tcp     root            monitor                               none                        1             [OK]
tcp     root            plugin.webman.push.server             websocket://0.0.0.0:2121    1             [OK]
tcp     root            plugin.webman.redis-queue.consumer    none                        6             [OK]
------------------------------------------------------------------------------------------------------------------------
Press Ctrl+C to stop. Start success.
```

### Redis队列实现分析

`Webman\RedisQueue\Process\Consumer` 类文件内容：
```php
<?php
/**
 * This file is part of webman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */

namespace Webman\RedisQueue\Process;

use support\Container;
use Webman\RedisQueue\Client;

/**
 * Class Consumer
 * @package process
 */
class Consumer
{
    /**
     * @var string
     */
    protected $_consumerDir = '';

    /**
     * StompConsumer constructor.
     * @param string $consumer_dir
     */
    public function __construct($consumer_dir = '')
    {
        $this->_consumerDir = $consumer_dir;
    }

    /**
     * onWorkerStart.
     */
    public function onWorkerStart()
    {
        if (!is_dir($this->_consumerDir)) {
            echo "Consumer directory {$this->_consumerDir} not exists\r\n";
            return;
        }
        $dir_iterator = new \RecursiveDirectoryIterator($this->_consumerDir);
        $iterator = new \RecursiveIteratorIterator($dir_iterator);
        foreach ($iterator as $file) {
            if (is_dir($file)) {
                continue;
            }
            $fileinfo = new \SplFileInfo($file);
            $ext = $fileinfo->getExtension();
            if ($ext === 'php') {
                $class = str_replace('/', "\\", substr(substr($file, strlen(base_path())), 0, -4));
                if (is_a($class, 'Webman\RedisQueue\Consumer', true)) {
                    $consumer = Container::get($class);
                    $connection_name = $consumer->connection ?? 'default';
                    $queue = $consumer->queue;
                    $connection = Client::connection($connection_name);
                    $connection->subscribe($queue, [$consumer, 'consume']);
                }
            }
        }

    }
}
```

`Webman\RedisQueue\Client` 类文件内容：
```php
<?php
/**
 * This file is part of webman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Webman\RedisQueue;

use Workerman\RedisQueue\Client as RedisClient;

/**
 * Class RedisQueue
 * @package support
 *
 * Strings methods
 * @method static void send($queue, $data, $delay=0)
 */
class Client
{
    /**
     * @var Client[]
     */
    protected static $_connections = null;
    

    /**
     * @param string $name
     * @return RedisClient
     */
    public static function connection($name = 'default') {
        if (!isset(static::$_connections[$name])) {
            $config = config('redis_queue', config('plugin.webman.redis-queue.redis', []));
            if (!isset($config[$name])) {
                throw new \RuntimeException("RedisQueue connection $name not found");
            }
            $host = $config[$name]['host'];
            $options = $config[$name]['options'];
            $client = new RedisClient($host, $options);
            static::$_connections[$name] = $client;
        }
        return static::$_connections[$name];
    }

    /**
     * @param $name
     * @param $arguments
     * @return mixed
     */
    public static function __callStatic($name, $arguments)
    {
        return static::connection('default')->{$name}(... $arguments);
    }
}
```

`Workerman\RedisQueue\Client` 类文件内容：
```php
<?php
/**
 * This file is part of workerman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Workerman\RedisQueue;

use RuntimeException;
use Workerman\Lib\Timer;
use Workerman\Redis\Client as Redis;

/**
 * Class Client
 * @package Workerman\RedisQueue
 */
class Client
{
    /**
     * Queue waiting for consumption
     */
    const QUEUE_WAITING = '{redis-queue}-waiting';

    /**
     * Queue with delayed consumption
     */
    const QUEUE_DELAYED = '{redis-queue}-delayed';

    /**
     * Queue with consumption failure
     */
    const QUEUE_FAILD = '{redis-queue}-failed';

    /**
     * @var Redis
     */
    protected $_redisSubscribe;

    /**
     * @var Redis
     */
    protected $_redisSend;

    /**
     * @var array
     */
    protected $_subscribeQueues = [];

    /**
     * @var array
     */
    protected $_options = [
        'retry_seconds' => 5,
        'max_attempts'  => 5,
        'auth'          => '',
        'db'            => 0,
    ];

    /**
     * Client constructor.
     * @param $address
     * @param array $options
     */
    public function __construct($address, $options = [])
    {
        $this->_redisSubscribe = new Redis($address, $options);
        $this->_redisSubscribe->brPoping = 0;
        $this->_redisSend = new Redis($address, $options);
        if (isset($options['auth'])) {
            $this->_redisSubscribe->auth($options['auth']);
            $this->_redisSend->auth($options['auth']);
        }
        if (isset($options['db'])) {
            $this->_redisSubscribe->select($options['db']);
            $this->_redisSend->select($options['db']);
        }
        $this->_options = array_merge($this->_options, $options);
    }

    /**
     * Send.
     *
     * @param $queue
     * @param $data
     * @param int $delay
     * @param callable $cb
     */
    public function send($queue, $data, $delay = 0, $cb = null)
    {
        static $_id = 0;
        $id = \microtime(true) . '.' . (++$_id);
        $now = time();
        $package_str = \json_encode([
            'id'       => $id,
            'time'     => $now,
            'delay'    => $delay,
            'attempts' => 0,
            'queue'    => $queue,
            'data'     => $data
        ]);
        if (\is_callable($delay)) {
            $cb = $delay;
            $delay = 0;
        }
        if ($cb) {
            $cb = function ($ret) use ($cb) {
               $cb((bool)$ret);
            };
            if ($delay == 0) {
                $this->_redisSend->lPush(static::QUEUE_WAITING . $queue, $package_str, $cb);
            } else {
                $this->_redisSend->zAdd(static::QUEUE_DELAYED, $now + $delay, $package_str, $cb);
            }
            return;
        }
        if ($delay == 0) {
            $this->_redisSend->lPush(static::QUEUE_WAITING . $queue, $package_str);
        } else {
            $this->_redisSend->zAdd(static::QUEUE_DELAYED, $now + $delay, $package_str);
        }
    }

    /**
     * Subscribe.
     *
     * @param string|array $queue
     * @param callable $callback
     */
    public function subscribe($queue, callable $callback)
    {
        $queue = (array)$queue;
        foreach ($queue as $q) {
            $redis_key = static::QUEUE_WAITING . $q;
            $this->_subscribeQueues[$redis_key] = $callback;
        }
        $this->pull();
    }

    /**
     * Unsubscribe.
     *
     * @param string|array $queue
     * @return void
     */
    public function unsubscribe($queue)
    {
        $queue = (array)$queue;
        foreach($queue as $q) {
            $redis_key = static::QUEUE_WAITING . $q;
            unset($this->_subscribeQueues[$redis_key]);
        }
    }

    /**
     * tryToPullDelayQueue.
     */
    protected function tryToPullDelayQueue()
    {
        static $retry_timer = 0;
        if ($retry_timer) {
            return;
        }
        $retry_timer = Timer::add(1, function () {
            $now = time();
            $options = ['LIMIT', 0, 128];
            $this->_redisSend->zrevrangebyscore(static::QUEUE_DELAYED, $now, '-inf', $options, function($items){
                if ($items === false) {
                    throw new RuntimeException($this->_redisSend->error());
                }
                foreach ($items as $package_str) {
                    $this->_redisSend->zRem(static::QUEUE_DELAYED, $package_str, function ($result) use ($package_str) {
                        if ($result !== 1) {
                            return;
                        }
                        $package = \json_decode($package_str, true);
                        if (!$package) {
                            $this->_redisSend->lPush(static::QUEUE_FAILD , $package_str);
                            return;
                        }
                        $this->_redisSend->lPush(static::QUEUE_WAITING . $package['queue'], $package_str);
                    });
                }
            });
        });
    }

    /**
     * pull.
     */
    public function pull()
    {
        $this->tryToPullDelayQueue();
        if (!$this->_subscribeQueues || $this->_redisSubscribe->brPoping) {
            return;
        }
        $cb = function($data) use (&$cb) {
            if ($data) {
                $this->_redisSubscribe->brPoping = 0;
                $redis_key = $data[0];
                $package_str = $data[1];
                $package = json_decode($package_str, true);
                if (!$package) {
                    $this->_redisSend->lPush(static::QUEUE_FAILD, $package_str);
                } else {
                    if (!isset($this->_subscribeQueues[$redis_key])) {
                        // 取消订阅，放回队列
                        $this->_redisSend->rPush($redis_key, $package_str);
                    } else {
                        $callback = $this->_subscribeQueues[$redis_key];
                        try {
                            \call_user_func($callback, $package['data']);
                        } catch (\Exception $e) {
                            if (++$package['attempts'] > $this->_options['max_attempts']) {
                                $package['error'] = (string) $e;
                                $this->fail($package);
                            } else {
                                $this->retry($package);
                            }
                        } catch (\Error $e) {
                            if (++$package['attempts'] > $this->_options['max_attempts']) {
                                $package['error'] = (string) $e;
                                $this->fail($package);
                            } else {
                                $this->retry($package);
                            }
                        }
                    }
                }
            }
            if ($this->_subscribeQueues) {
                $this->_redisSubscribe->brPoping = 1;
                Timer::add(0.000001, [$this->_redisSubscribe, 'brPop'], [\array_keys($this->_subscribeQueues), 1, $cb] ,false);
            }
        };
        $this->_redisSubscribe->brPoping = 1;
        $this->_redisSubscribe->brPop(\array_keys($this->_subscribeQueues), 1, $cb);
    }

    /**
     * @param $package
     */
    protected function retry($package)
    {
        $delay = time() + $this->_options['retry_seconds'] * ($package['attempts']);
        $this->_redisSend->zAdd(static::QUEUE_DELAYED, $delay, \json_encode($package));
    }

    /**
     * @param $package
     */
    protected function fail($package)
    {
        $this->_redisSend->lPush(static::QUEUE_FAILD , \json_encode($package));
    }

}
```

`Workerman\Redis\Client` 类文件内容：
```php
<?php
/**
 * This file is part of workerman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the LICENSE
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Workerman\Redis;

use Workerman\Connection\AsyncTcpConnection;
use Workerman\Lib\Timer;

/**
 * Class Client
 * @package Workerman\Redis
 *
 * Strings methods
 * @method static int append($key, $value, $cb = null)
 * @method static int bitCount($key, $cb = null)
 * @method static int decrBy($key, $value, $cb = null)
 * @method static string|bool get($key, $cb = null)
 * @method static int getBit($key, $offset, $cb = null)
 * @method static string getRange($key, $start, $end, $cb = null)
 * @method static string getSet($key, $value, $cb = null)
 * @method static int incrBy($key, $value, $cb = null)
 * @method static float incrByFloat($key, $value, $cb = null)
 * @method static array mGet(array $keys, $cb = null)
 * @method static array getMultiple(array $keys, $cb = null)
 * @method static bool setBit($key, $offset, $value, $cb = null)
 * @method static bool setEx($key, $ttl, $value, $cb = null)
 * @method static bool pSetEx($key, $ttl, $value, $cb = null)
 * @method static bool setNx($key, $value, $cb = null)
 * @method static string setRange($key, $offset, $value, $cb = null)
 * @method static int strLen($key, $cb = null)
 * Keys methods
 * @method static int del(...$keys, $cb = null)
 * @method static int unlink(...$keys, $cb = null)
 * @method static false|string dump($key, $cb = null)
 * @method static int exists(...$keys, $cb = null)
 * @method static bool expire($key, $ttl, $cb = null)
 * @method static bool pexpire($key, $ttl, $cb = null)
 * @method static bool expireAt($key, $timestamp, $cb = null)
 * @method static bool pexpireAt($key, $timestamp, $cb = null)
 * @method static array keys($pattern, $cb = null)
 * @method static bool|array scan($it, $cb = null)
 * @method static void migrate($host, $port, $keys, $dbIndex, $timeout, $copy = false, $replace = false, $cb = null)
 * @method static bool move($key, $dbIndex, $cb = null)
 * @method static string|int|bool object($information, $key, $cb = null)
 * @method static bool persist($key, $cb = null)
 * @method static string randomKey(, $cb = null)
 * @method static bool rename($srcKey, $dstKey, $cb = null)
 * @method static bool renameNx($srcKey, $dstKey, $cb = null)
 * @method static string type($key, $cb = null)
 * @method static int ttl($key, $cb = null)
 * @method static int pttl($key, $cb = null)
 * @method static void restore($key, $ttl, $value, $cb = null)
 * Hashes methods
 * @method static false|int hSet($key, $hashKey, $value, $cb = null)
 * @method static bool hSetNx($key, $hashKey, $value, $cb = null)
 * @method static false|string hGet($key, $hashKey, $cb = null)
 * @method static false|int hLen($key, $cb = null)
 * @method static false|int hDel($key, ...$hashKeys, $cb = null)
 * @method static array hKeys($key, $cb = null)
 * @method static array hVals($key, $cb = null)
 * @method static bool hExists($key, $hashKey, $cb = null)
 * @method static int hIncrBy($key, $hashKey, $value, $cb = null)
 * @method static float hIncrByFloat($key, $hashKey, $value, $cb = null)
 * @method static array hScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * @method static int hStrLen($key, $hashKey, $cb = null)
 * Lists methods
 * @method static array blPop($keys, $timeout, $cb = null)
 * @method static array brPop($keys, $timeout, $cb = null)
 * @method static false|string bRPopLPush($srcKey, $dstKey, $timeout, $cb = null)
 * @method static false|string lIndex($key, $index, $cb = null)
 * @method static int lInsert($key, $position, $pivot, $value, $cb = null)
 * @method static false|string lPop($key, $cb = null)
 * @method static false|int lPush($key, ...$entries, $cb = null)
 * @method static false|int lPushx($key, $value, $cb = null)
 * @method static array lRange($key, $start, $end, $cb = null)
 * @method static false|int lRem($key, $value, $count, $cb = null)
 * @method static bool lSet($key, $index, $value, $cb = null)
 * @method static false|array lTrim($key, $start, $end, $cb = null)
 * @method static false|string rPop($key, $cb = null)
 * @method static false|string rPopLPush($srcKey, $dstKey, $cb = null)
 * @method static false|int rPush($key, ...$entries, $cb = null)
 * @method static false|int rPushX($key, $value, $cb = null)
 * @method static false|int lLen($key, $cb = null)
 * Sets methods
 * @method static int sAdd($key, $value, $cb = null)
 * @method static int sCard($key, $cb = null)
 * @method static array sDiff($keys, $cb = null)
 * @method static false|int sDiffStore($dst, $keys, $cb = null)
 * @method static false|array sInter($keys, $cb = null)
 * @method static false|int sInterStore($dst, $keys, $cb = null)
 * @method static bool sIsMember($key, $member, $cb = null)
 * @method static array sMembers($key, $cb = null)
 * @method static bool sMove($src, $dst, $member, $cb = null)
 * @method static false|string|array sPop($key, $count = 0, $cb = null)
 * @method static false|string|array sRandMember($key, $count = 0, $cb = null)
 * @method static int sRem($key, ...$members, $cb = null)
 * @method static array sUnion(...$keys, $cb = null)
 * @method static false|int sUnionStore($dst, ...$keys, $cb = null)
 * @method static false|array sScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * Sorted sets methods
 * @method static array bzPopMin($keys, $timeout, $cb = null)
 * @method static array bzPopMax($keys, $timeout, $cb = null)
 * @method static int zAdd($key, $score, $value, $cb = null)
 * @method static int zCard($key, $cb = null)
 * @method static int zCount($key, $start, $end, $cb = null)
 * @method static double zIncrBy($key, $value, $member, $cb = null)
 * @method static int zinterstore($keyOutput, $arrayZSetKeys, $arrayWeights = [], $aggregateFunction = '', $cb = null)
 * @method static array zPopMin($key, $count, $cb = null)
 * @method static array zPopMax($key, $count, $cb = null)
 * @method static array zRange($key, $start, $end, $withScores = false, $cb = null)
 * @method static array zRangeByScore($key, $start, $end, $options = [], $cb = null)
 * @method static array zRevRangeByScore($key, $start, $end, $options = [], $cb = null)
 * @method static array zRangeByLex($key, $min, $max, $offset = 0, $limit = 0, $cb = null)
 * @method static int zRank($key, $member, $cb = null)
 * @method static int zRevRank($key, $member, $cb = null)
 * @method static int zRem($key, ...$members, $cb = null)
 * @method static int zRemRangeByRank($key, $start, $end, $cb = null)
 * @method static int zRemRangeByScore($key, $start, $end, $cb = null)
 * @method static array zRevRange($key, $start, $end, $withScores = false, $cb = null)
 * @method static double zScore($key, $member, $cb = null)
 * @method static int zunionstore($keyOutput, $arrayZSetKeys, $arrayWeights = [], $aggregateFunction = '', $cb = null)
 * @method static false|array zScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * HyperLogLogs methods
 * @method static int pfAdd($key, $values, $cb = null)
 * @method static int pfCount($keys, $cb = null)
 * @method static bool pfMerge($dstKey, $srcKeys, $cb = null)
 * Geocoding methods
 * @method static int geoAdd($key, $longitude, $latitude, $member, ...$items, $cb = null)
 * @method static array geoHash($key, ...$members, $cb = null)
 * @method static array geoPos($key, ...$members, $cb = null)
 * @method static double geoDist($key, $members, $unit = '', $cb = null)
 * @method static int|array geoRadius($key, $longitude, $latitude, $radius, $unit, $options = [], $cb = null)
 * @method static array geoRadiusByMember($key, $member, $radius, $units, $options = [], $cb = null)
 * Streams methods
 * @method static int xAck($stream, $group, $arrMessages, $cb = null)
 * @method static string xAdd($strKey, $strId, $arrMessage, $iMaxLen = 0, $booApproximate = false, $cb = null)
 * @method static array xClaim($strKey, $strGroup, $strConsumer, $minIdleTime, $arrIds, $arrOptions = [], $cb = null)
 * @method static int xDel($strKey, $arrIds, $cb = null)
 * @method static mixed xGroup($command, $strKey, $strGroup, $strMsgId, $booMKStream = null, $cb = null)
 * @method static mixed xInfo($command, $strStream, $strGroup = null, $cb = null)
 * @method static int xLen($stream, $cb = null)
 * @method static array xPending($strStream, $strGroup, $strStart = 0, $strEnd = 0, $iCount = 0, $strConsumer = null, $cb = null)
 * @method static array xRange($strStream, $strStart, $strEnd, $iCount = 0, $cb = null)
 * @method static array xRead($arrStreams, $iCount = 0, $iBlock = null, $cb = null)
 * @method static array xReadGroup($strGroup, $strConsumer, $arrStreams, $iCount = 0, $iBlock = null, $cb = null)
 * @method static array xRevRange($strStream, $strEnd, $strStart, $iCount = 0, $cb = null)
 * @method static int xTrim($strStream, $iMaxLen, $booApproximate = null, $cb = null)
 * Pub/sub methods
 * @method static mixed publish($channel, $message, $cb = null)
 * @method static mixed pubSub($keyword, $argument = null, $cb = null)
 * Generic methods
 * @method static mixed rawCommand(...$commandAndArgs, $cb = null)
 * Transactions methods
 * @method static \Redis multi($cb = null)
 * @method static mixed exec($cb = null)
 * @method static mixed discard($cb = null)
 * @method static mixed watch($keys, $cb = null)
 * @method static mixed unwatch($keys, $cb = null)
 * Scripting methods
 * @method static mixed eval($script, $args = [], $numKeys = 0, $cb = null)
 * @method static mixed evalSha($sha, $args = [], $numKeys = 0, $cb = null)
 * @method static mixed script($command, ...$scripts, $cb = null)
 * @method static mixed client(...$args, $cb = null)
 * @method static null|string getLastError($cb = null)
 * @method static bool clearLastError($cb = null)
 * @method static mixed _prefix($value, $cb = null)
 * @method static mixed _serialize($value, $cb = null)
 * @method static mixed _unserialize($value, $cb = null)
 * Introspection methods
 * @method static bool isConnected($cb = null)
 * @method static mixed getHost($cb = null)
 * @method static mixed getPort($cb = null)
 * @method static false|int getDbNum($cb = null)
 * @method static false|double getTimeout($cb = null)
 * @method static mixed getReadTimeout($cb = null)
 * @method static mixed getPersistentID($cb = null)
 * @method static mixed getAuth($cb = null)
 */
class Client
{
    /**
     * @var AsyncTcpConnection
     */
    protected $_connection = null;

    /**
     * @var array
     */
    protected $_options = [];

    /**
     * @var string
     */
    protected $_address = '';

    /**
     * @var array
     */
    protected $_queue = [];

    /**
     * @var int
     */
    protected $_db = 0;

    /**
     * @var string|array
     */
    protected $_auth = null;

    /**
     * @var bool
     */
    protected $_waiting = true;

    /**
     * @var Timer
     */
    protected $_connectTimeoutTimer = null;

    /**
     * @var Timer
     */
    protected $_reconnectTimer = null;

    /**
     * @var callable
     */
    protected $_connectionCallback = null;

    /**
     * @var Timer
     */
    protected $_waitTimeoutTimer = null;

    /**
     * @var string
     */
    protected $_error = '';

    /**
     * @var bool
     */
    protected $_subscribe = false;

    /**
     * @var bool
     */
    protected $_firstConnect = true;

    /**
     * Client constructor.
     * @param $address
     * @param array $options
     * @param null $callback
     */
    public function __construct($address, $options = [], $callback = null)
    {
        if (!\class_exists('Protocols\Redis')) {
            \class_alias('Workerman\Redis\Protocols\Redis', 'Protocols\Redis');
        }
        $this->_address = $address;
        $this->_options = $options;
        $this->_connectionCallback = $callback;
        $this->connect();
        $timer = Timer::add(1, function () use (&$timer) {
            if (empty($this->_queue)) {
                return;
            }
            if ($this->_subscribe) {
                Timer::del($timer);
                return;
            }
            reset($this->_queue);
            $current_queue = current($this->_queue);
            $current_command = $current_queue[0][0];
            $ignore_first_queue = in_array($current_command, ['BLPOP', 'BRPOP']);
            $time = time();
            $timeout = isset($this->_options['wait_timeout']) ? $this->_options['wait_timeout'] : 600;
            $has_timeout = false;
            $first_queue = true;
            foreach ($this->_queue as $key => $queue) {
                if ($first_queue && $ignore_first_queue) {
                    $first_queue = false;
                    continue;
                }
                if ($time - $queue[1] > $timeout) {
                    $has_timeout = true;
                    unset($this->_queue[$key]);
                    $msg = "Workerman Redis Wait Timeout ($timeout seconds)";
                    if ($queue[2]) {
                        $this->_error = $msg;
                        \call_user_func($queue[2], false, $this);
                    } else {
                        echo new Exception($msg);
                    }
                }
            }
            if ($has_timeout && !$ignore_first_queue) {
                $this->closeConnection();
                $this->connect();
            }
        });
    }

    /**
     * connect
     */
    public function connect()
    {
        if ($this->_connection) {
            return;
        }

        $timeout = isset($this->_options['connect_timeout']) ? $this->_options['connect_timeout'] : 5;
        $context = isset($this->_options['context']) ? $this->_options['context'] : [];
        $this->_connection = new AsyncTcpConnection($this->_address, $context);

        $this->_connection->onConnect = function () {
            $this->_waiting = false;
            Timer::del($this->_connectTimeoutTimer);
            if ($this->_reconnectTimer) {
                Timer::del($this->_reconnectTimer);
                $this->_reconnectTimer = null;
            }

            if ($this->_db) {
                $this->_queue = \array_merge([[['SELECT', $this->_db], time(), null]], $this->_queue);
            }

            if ($this->_auth) {
                $this->_queue = \array_merge([[['AUTH', $this->_auth], time(), null]],  $this->_queue);
            }

            $this->_connection->onError = function ($connection, $code, $msg) {
                echo new \Exception("Workerman Redis Connection Error $code $msg");
            };
            $this->process();
            $this->_firstConnect && $this->_connectionCallback && \call_user_func($this->_connectionCallback, true, $this);
            $this->_firstConnect = false;
        };

        $time_start = microtime(true);
        $this->_connection->onError = function ($connection) use ($time_start) {
            $time = microtime(true) - $time_start;
            $msg = "Workerman Redis Connection Failed ($time seconds)";
            $this->_error = $msg;
            $exception = new \Exception($msg);
            if (!$this->_connectionCallback) {
                echo $exception;
                return;
            }
            $this->_firstConnect && \call_user_func($this->_connectionCallback, false, $this);
        };

        $this->_connection->onClose = function () use ($time_start) {
            $this->_subscribe = false;
            if ($this->_connectTimeoutTimer) {
                Timer::del($this->_connectTimeoutTimer);
            }
            if ($this->_reconnectTimer) {
                Timer::del($this->_reconnectTimer);
                $this->_reconnectTimer = null;
            }
            $this->closeConnection();
            if (microtime(true) - $time_start > 5) {
                $this->connect();
            } else {
                $this->_reconnectTimer = Timer::add(5, function () {
                    $this->connect();
                }, null, false);
            }
        };

        $this->_connection->onMessage = function ($connection, $data) {
            $this->_error = '';
            $this->_waiting = false;
            reset($this->_queue);
            $queue = current($this->_queue);
            $cb = $queue[2];
            $type = $data[0];
            if (!$this->_subscribe) {
                unset($this->_queue[key($this->_queue)]);
            }
            $success = $type === '-' || $type === '!' ? false : true;
            $exception = false;
            $result = false;
            if ($success) {
                $result = $data[1];
                if ($type === '+' && $result === 'OK') {
                    $result = true;
                }
            } else {
                $this->_error = $data[1];
            }
            if (!$cb) {
                $this->process();
                return;
            }
            // format.
            if (!empty($queue[3])) {
                $result = \call_user_func($queue[3], $result);
            }
            try {
                \call_user_func($cb, $result, $this);
            } catch (\Exception $exception) {
            }

            if ($type === '!') {
                $this->closeConnection();
                $this->connect();
            } else {
                $this->process();
            }
            if ($exception) {
                throw $exception;
            }
        };

        $this->_connectTimeoutTimer = Timer::add($timeout, function () use ($timeout) {
            $this->_connectTimeoutTimer = null;
            if ($this->_connection && $this->_connection->getStatus(false) === 'ESTABLISHED') {
                return;
            }
            $this->closeConnection();
            $this->_error = "Workerman Redis Connection to {$this->_address} timeout ({$timeout} seconds)";
            if ($this->_firstConnect && $this->_connectionCallback) {
                \call_user_func($this->_connectionCallback, false, $this);
            } else {
                echo $this->_error . "\n";
            }

        });
        $this->_connection->connect();
    }

    /**
     * process
     */
    public function process()
    {
        if (!$this->_connection || $this->_waiting || empty($this->_queue) || $this->_subscribe) {
            return;
        }
        \reset($this->_queue);
        $queue = \current($this->_queue);
        if ($queue[0][0] === 'SUBSCRIBE' || $queue[0][0] === 'PSUBSCRIBE') {
            $this->_subscribe = true;
        }
        $this->_waiting = true;
        $this->_connection->send($queue[0]);
        $this->_error = '';
    }

    /**
     * subscribe
     *
     * @param $channels
     * @param $cb
     */
    public function subscribe($channels, $cb)
    {
        $new_cb = function ($result) use ($cb) {
            if (!$result) {
                echo $this->error();
                return;
            }
            $response_type = $result[0];
            switch ($response_type) {
                case 'subscribe':
                    return;
                case 'message':
                    \call_user_func($cb, $result[1], $result[2], $this);
                    return;
                default:
                    echo 'unknow response type for subscribe. buffer:' . serialize($result) . "\n";
            }
        };
        $this->_queue[] = [['SUBSCRIBE', $channels], time(), $new_cb];
        $this->process();
    }

    /**
     * psubscribe
     *
     * @param $patterns
     * @param $cb
     */
    public function pSubscribe($patterns, $cb)
    {
        $new_cb = function ($result) use ($cb) {
            if (!$result) {
                echo $this->error();
                return;
            }
            $response_type = $result[0];
            switch ($response_type) {
                case 'psubscribe':
                    return;
                case 'pmessage':
                    \call_user_func($cb, $result[1], $result[2], $result[3], $this);
                    return;
                default:
                    echo 'unknow response type for psubscribe. buffer:' . serialize($result) . "\n";
            }
        };
        $this->_queue[] = [['PSUBSCRIBE', $patterns], time(), $new_cb];
        $this->process();
    }

    /**
     * select
     *
     * @param $db
     * @param null $cb
     */
    public function select($db, $cb = null)
    {
        $format = function ($result) use ($db) {
            $this->_db = $db;
            return $result;
        };
        $cb = $cb ? $cb : function(){};
        $this->_queue[] = [['SELECT', $db], time(), $cb, $format];
        $this->process();
    }

    /**
     * auth
     *
     * @param string|array $auth
     * @param null $cb
     */
    public function auth($auth, $cb = null)
    {
        $format = function ($result) use ($auth) {
            $this->_auth = $auth;
            return $result;
        };
        $cb = $cb ? $cb : function(){};
        $this->_queue[] = [['AUTH', $auth], time(), $cb, $format];
        $this->process();
    }

    /**
     * set
     *
     * @param $key
     * @param $value
     * @param null $cb
     * @return null
     */
    public function set($key, $value, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $timeout = $cb;
            $cb = null;
            if (\count($args) > 3) {
                $cb = $args[3];
            }
            $this->_queue[] = [['SETEX', $key, $timeout, $value], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['SET', $key, $value], time(), $cb];
        $this->process();
    }

    /**
     * incr
     *
     * @param $key
     * @param null $cb
     * @return null
     */
    public function incr($key, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $num = $cb;
            $cb = null;
            if (\count($args) > 2) {
                $cb = $args[2];
            }
            $this->_queue[] = [['INCRBY', $key, $num], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['INCR', $key], time(), $cb];
        $this->process();
    }


    /**
     * decr
     *
     * @param $key
     * @param null $cb
     * @return null
     */
    public function decr($key, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $num = $cb;
            $cb = null;
            if (\count($args) > 2) {
                $cb = $args[2];
            }
            $this->_queue[] = [['DECRBY', $key, $num], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['DECR', $key], time(), $cb];
        $this->process();
    }

    /**
     * sort
     *
     * @param $key
     * @param $options
     * @param null $cb
     */
    function sort($key, $options, $cb = null)
    {
        $args = [];
        if (isset($options['sort'])) {
            $args[] = $options['sort'];
            unset($options['sort']);
        }

        foreach ($options as $op => $value) {
            $args[] = $op;
            if (!is_array($value)) {
                $args[] = $value;
                continue;
            }
            foreach ($value as $sub_value) {
                $args[] = $sub_value;
            }
        }
        \array_unshift($args, 'SORT', $key);
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * mSet
     *
     * @param array $array
     * @param null $cb
     */
    public function mSet(array $array, $cb = null)
    {
        $this->mapCb('MSET', $array, $cb);
    }

    /**
     * mSetNx
     *
     * @param array $array
     * @param null $cb
     */
    public function mSetNx(array $array, $cb = null)
    {
        $this->mapCb('MSETNX', $array, $cb);
    }

    /**
     * mapCb
     *
     * @param $command
     * @param array $array
     * @param $cb
     */
    protected function mapCb($command, array $array, $cb)
    {
        $args = [$command];
        foreach ($array as $key => $value) {
            $args[] = $key;
            $args[] = $value;
        }
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * hMSet
     *
     * @param $key
     * @param array $array
     * @param null $cb
     */
    public function hMSet($key, array $array, $cb = null)
    {
        $this->keyMapCb('HMSET', $key, $array, $cb);
    }

    /**
     * hMGet
     *
     * @param $key
     * @param array $array
     * @param null $cb
     */
    public function hMGet($key, array $array, $cb = null)
    {
        $format = function ($result) use ($array) {
            if (!is_array($result)) {
                return $result;
            }
            return \array_combine($array, $result);
        };
        $this->_queue[] = [['HMGET', $key, $array], time(), $cb, $format];
        $this->process();
    }

    /**
     * hGetAll
     *
     * @param $key
     * @param null $cb
     */
    public function hGetAll($key, $cb = null)
    {
        $format = function ($result) {
            if (!\is_array($result)) {
                return $result;
            }
            $return = [];
            $key = '';
            foreach ($result as $index => $item) {
                if ($index % 2 == 0) {
                    $key = $item;
                    continue;
                }
                $return[$key] = $item;
            }
            return $return;
        };
        $this->_queue[] = [['HGETALL', $key], time(), $cb, $format];
        $this->process();
    }

    /**
     * keyMapCb
     *
     * @param $command
     * @param $key
     * @param array $array
     * @param $cb
     */
    protected function keyMapCb($command, $key, array $array, $cb)
    {
        $args = [$command, $key];
        foreach ($array as $key => $value) {
            $args[] = $key;
            $args[] = $value;
        }
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * __call
     *
     * @param $method
     * @param $args
     */
    public function __call($method, $args)
    {
        $cb = null;
        if (\is_callable(end($args))) {
            $cb = array_pop($args);
        }

        \array_unshift($args, \strtoupper($method));
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * closeConnection
     */
    public function closeConnection()
    {
        if (!$this->_connection) {
            return;
        }
        $this->_subscribe = false;
        $this->_connection->onConnect = $this->_connection->onError = $this->_connection->onClose =
        $this->_connection->onMessge = null;
        $this->_connection->close();
        $this->_connection = null;
    }

    /**
     * error
     *
     * @return string
     */
    function error()
    {
        return $this->_error;
    }

    /**
     * close
     */
    public function close()
    {
        $this->closeConnection();
        $this->_queue = [];
    }

    /**
     * scan
     *
     * @throws Exception
     */
    public function scan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function hScan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function sScan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function zScan()
    {
        throw new Exception('Not implemented');
    }
}
```



### failed 队列数据写入数据库

发现上面把处理不了的队列数据都扔到了 `{redis-queue}-failed`  队列中，我们这里想办法把这个队列中的数据写入到数据库中。

**消费者相关类**

新建`lib/webman/redisQueue/failed/Consumer.php` 文件，内容： 
```php
<?php

namespace lib\webman\redisQueue\failed;

use support\Container;
use Webman\RedisQueue\Process\Consumer as webmanConsumer;

/**
 * Class Consumer
 * @package process
 */
class Consumer extends webmanConsumer
{
    /**
     * onWorkerStart.
     */
    public function onWorkerStart()
    {
        if (!is_dir($this->_consumerDir)) {
            echo "Consumer directory {$this->_consumerDir} not exists\r\n";
            return;
        }
        $dir_iterator = new \RecursiveDirectoryIterator($this->_consumerDir);
        $iterator = new \RecursiveIteratorIterator($dir_iterator);
        foreach ($iterator as $file) {
            if (is_dir($file)) {
                continue;
            }
            $fileinfo = new \SplFileInfo($file);
            $ext = $fileinfo->getExtension();
            if ($ext === 'php') {
                $class = str_replace('/', "\\", substr(substr($file, strlen(base_path())), 0, -4));
                if (is_a($class, 'Webman\RedisQueue\Consumer', true)) {
                    $consumer = Container::get($class);
                    $connection_name = $consumer->connection ?? 'default';
                    $queue = $consumer->queue;
                    $connection = Client::connection($connection_name);
                    $connection->subscribe($queue, [$consumer, 'consume']);
                }
            }
        }
    }
}
```

新建`lib/webman/redisQueue/failed/Client.php` 文件，内容： 
```php
<?php

namespace lib\webman\redisQueue\failed;

use Webman\RedisQueue\Client as webmanClient;

/**
 * Class Client
 * @package lib\webman\redisQueue\failed
 */
class Client extends webmanClient
{
    /**
     * @param string $name
     * @return RedisClient
     */
    public static function connection($name = 'default')
    {
        if (!isset(static::$_connections[$name])) {
            $config = config('redis_queue', config('plugin.webman.redis-queue.redis', []));
            if (!isset($config[$name])) {
                throw new \RuntimeException("RedisQueue connection $name not found");
            }
            $host = $config[$name]['host'];
            $options = $config[$name]['options'];
            $client = new RedisClient($host, $options);
            static::$_connections[$name] = $client;
        }

        return static::$_connections[$name];
    }
}
```

新建`lib/webman/redisQueue/failed/RedisClient.php` 文件，内容： 
```php
<?php

namespace lib\webman\redisQueue\failed;

use Workerman\Lib\Timer;
use Workerman\RedisQueue\Client;
use support\Log;

/**
 * Class RedisClient
 * @package lib\webman\redisQueue\failed
 */
class RedisClient extends Client
{
    /**
     * @param array|string $queue
     * @param callable $callback
     */
    public function subscribe($queue, callable $callback)
    {
        $this->_subscribeQueues[$queue] = $callback;

        $this->pull();
    }

    /**
     * pull
     */
    public function pull()
    {
        if (!$this->_subscribeQueues || $this->_redisSubscribe->brPoping) {
            return;
        }
        $cb = function($data) use (&$cb) {
            if ($data) {
                $this->_redisSubscribe->brPoping = 0;
                $redis_key = $data[0];
                $package_str = $data[1];
                $package = json_decode($package_str, true);
                if (!$package) {
                    $log = Log::channel('default');
                    $log->error('lib\webman\redisQueue\failed\RedisClient::pull', ["package_str" => $package_str]);
                } else {
                    if (!isset($this->_subscribeQueues[$redis_key])) {
                        // 取消订阅，放回队列
                        $this->_redisSend->rPush($redis_key, $package_str);
                    } else {
                        $callback = $this->_subscribeQueues[$redis_key];
                        try {
                            \call_user_func($callback, $package);
                        } catch (\Throwable $e) {
                            // 记录异常日志
                            $package['Throwable'] = (string) $e;

                            $log = Log::channel('default');
                            $log->error('lib\webman\redisQueue\failed\RedisClient::pull', $package);
                        }
                    }
                }
            }
            if ($this->_subscribeQueues) {
                $this->_redisSubscribe->brPoping = 1;
                Timer::add(0.000001, [$this->_redisSubscribe, 'brPop'], [\array_keys($this->_subscribeQueues), 1, $cb] ,false);
            }
        };
        $this->_redisSubscribe->brPoping = 1;
        $this->_redisSubscribe->brPop(\array_keys($this->_subscribeQueues), 1, $cb);
    }
}
```

**指定消费者**

把数据表建好：
```
CREATE TABLE `redis_queue_failed_record` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `data` text COLLATE utf8mb4_unicode_ci COMMENT '数据',
  `status` tinyint(4) NOT NULL DEFAULT '10' COMMENT '状态，10：待处理，20：处理成功，30：处理失败',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci ROW_FORMAT=DYNAMIC COMMENT='redis队列错误记录';
```

model模型类也建好，`app/model/RedisQueueFailedRecord.php` 文件内容：
```php
<?php

namespace app\model;

use support\Model;

/**
 * redis队列错误记录
 */
class RedisQueueFailedRecord extends Model
{
    /**
     * 模型的连接名称
     *
     * @var string
     */
    protected $connection = 'mysql';

    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'redis_queue_failed_record';

    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'id';

    /**
     * Indicates if the model should be timestamped.
     * 建议true；自动管理created_at(开始时间)，updated_at(更新时间)
     *
     * @var bool
     */
    public $timestamps = true;
    
    /**
     * 可以适用create方法直接创建的属性 
     */
    protected $fillable = [
        "data",
        "status",
    ];

    /**
     * 类型转换
     */
    protected $casts = [
        "data" => "json",
    ];

    const STATUS_UNDEAL = 10;  // 待处理
    const STATUS_DEALED_SUCC = 20;  // 处理成功
    const STATUS_DEALED_FAIL = 30;  // 处理失败
}
```

新建 `app/queue/redisFailed/RedisQueueFailed.php` 文件，内容： 
```php
<?php
namespace app\queue\redisFailed;

use Webman\RedisQueue\Consumer;
use app\model\RedisQueueFailedRecord;

/**
 * redis队列错误
 * Class fddContractFiling
 * @package app\queue\redis
 */
class RedisQueueFailed implements Consumer
{
    public $queue = \Workerman\RedisQueue\Client::QUEUE_FAILD;

    public $connection = 'default';

    public function consume($data)
    {
        $model = new RedisQueueFailedRecord();
        $model->data = $data;
        $model->status = RedisQueueFailedRecord::STATUS_UNDEAL;

        if (!$model->save()) {
            throw new \RuntimeException("redis_queue_failed_record data save failed");
        }
    }
}
```

**消费者配置**

`config/plugin/webman/redis-queue/process.php` 配置文件中，新增 `consumerFailed` 配置：
```php
<?php
return [
    'consumer'  => [
        'handler'     => Webman\RedisQueue\Process\Consumer::class,
        'count'       => 8, // 可以设置多进程同时消费
        'constructor' => [
            // 消费者类目录
            'consumer_dir' => app_path() . '/queue/redis'
        ]
    ],
    'consumerFailed'  => [
        'handler'     => lib\webman\redisQueue\failed\Consumer::class,
        'count'       => 1, // 可以设置多进程同时消费
        'constructor' => [
            // 消费者类目录
            'consumer_dir' => app_path() . '/queue/redisFailed'
        ]
    ]
];
```

服务停止，然后重启，查看效果。

### 零碎点

#### str_replace

```
 str_replace(
    array|string $search,
    array|string $replace,
    string|array $subject,
    int &$count = null
): string|array
```

```php
<?php
// Provides: <body text='black'>
$bodytag = str_replace("%body%", "black", "<body text='%body%'>");

// Provides: Hll Wrld f PHP
$vowels = array("a", "e", "i", "o", "u", "A", "E", "I", "O", "U");
$onlyconsonants = str_replace($vowels, "", "Hello World of PHP");

// Provides: You should eat pizza, beer, and ice cream every day
$phrase  = "You should eat fruits, vegetables, and fiber every day.";
$healthy = array("fruits", "vegetables", "fiber");
$yummy   = array("pizza", "beer", "ice cream");

$newphrase = str_replace($healthy, $yummy, $phrase);

// Provides: 2
$str = str_replace("ll", "", "good golly miss molly!", $count);
echo $count;
?>
```

#### strtr

```
// 三个参数
 strtr(string $string, string $from, string $to): string
// 两个参数
 strtr(string $string, array $replace_pairs): string
```

```php
<?php
echo strtr("Hilla Warld","ia","eo");  // Hello World

$arr = array("Hello" => "Hi", "world" => "earth");
echo strtr("Hello world",$arr);  // Hi earth
?>
```

#### array_replace_recursive

```
 array_replace_recursive(array $array, array ...$replacements): array
```

```
<?php
$base = array('citrus' => array( "orange") , 'berries' => array("blackberry", "raspberry"), );
$replacements = array('citrus' => array('pineapple'), 'berries' => array('blueberry'));

$basket = array_replace_recursive($base, $replacements);
print_r($basket);

$basket = array_replace($base, $replacements);
print_r($basket);
?>

Array
(
    [citrus] => Array
        (
            [0] => pineapple
        )

    [berries] => Array
        (
            [0] => blueberry
            [1] => raspberry
        )

)
Array
(
    [citrus] => Array
        (
            [0] => pineapple
        )

    [berries] => Array
        (
            [0] => blueberry
        )

)
```

```
<?php
$base = array('citrus' => array("orange") , 'berries' => array("blackberry", "raspberry"), 'others' => 'banana' );
$replacements = array('citrus' => 'pineapple', 'berries' => array('blueberry'), 'others' => array('litchis'));
$replacements2 = array('citrus' => array('pineapple'), 'berries' => array('blueberry'), 'others' => 'litchis');

$basket = array_replace_recursive($base, $replacements, $replacements2);
print_r($basket);
?>

Array
(
    [citrus] => Array
        (
            [0] => pineapple
        )

    [berries] => Array
        (
            [0] => blueberry
            [1] => raspberry
        )

    [others] => litchis
)
```

#### include

```
$config = include $file;
foreach ($explode as $section) {
    $tmp = [];
    $tmp[$section] = $config;
    $config = $tmp;
}
```

示例：
```
<?php
$config = ["a" => 1];
$relative_path = "plugin/webman/redis-queue/process";
$explode = array_reverse(explode(DIRECTORY_SEPARATOR, $relative_path));
print_r($explode);

foreach ($explode as $section) {
    $tmp = [];
    $tmp[$section] = $config;
    $config = $tmp;
}
print_r($config);
?>

Array
(
    [0] => process
    [1] => redis-queue
    [2] => webman
    [3] => plugin
)
Array
(
    [plugin] => Array
        (
            [webman] => Array
                (
                    [redis-queue] => Array
                        (
                            [process] => Array
                                (
                                    [a] => 1
                                )
                        )
                )
        )
)
```

## 参考资料

redis队列插件 <https://www.workerman.net/plugin/12>

workerman/redis-queue <https://www.workerman.net/doc/workerman/components/workerman-redis-queue.html>

walkor/webman <https://github.com/walkor/webman>

str_replace <https://www.php.net/manual/en/function.str-replace.php>

array_replace_recursive <https://www.php.net/manual/en/function.array-replace-recursive.php>


