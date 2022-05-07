---
layout: post
categories: PHP
title: webman env配置文件加载分析
meta: webman env配置文件加载分析
---
* content
{:toc}

## 正文

### webman安装

composer 安装 webman：
> composer create-project workerman/webman

```
root@02891538d8c9:/var/www/html# composer create-project workerman/webman webman_v1.3.7
Creating a "workerman/webman" project at "./webman_v1.3.7"
Installing workerman/webman (v1.3.7)
  - Installing workerman/webman (v1.3.7): Extracting archive
Created project in /var/www/html/webman_v1.3.7
Loading composer repositories with package information
Updating dependencies
Lock file operations: 6 installs, 0 updates, 0 removals
  - Locking monolog/monolog (2.4.0)
  - Locking nikic/fast-route (v1.3.0)
  - Locking psr/container (2.0.2)
  - Locking psr/log (1.1.4)
  - Locking workerman/webman-framework (v1.3.12)
  - Locking workerman/workerman (v4.0.36)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 6 installs, 0 updates, 0 removals
  - Installing psr/log (1.1.4): Extracting archive
  - Installing monolog/monolog (2.4.0): Extracting archive
  - Installing workerman/workerman (v4.0.36): Extracting archive
  - Installing psr/container (2.0.2): Extracting archive
  - Installing nikic/fast-route (v1.3.0): Extracting archive
  - Installing workerman/webman-framework (v1.3.12): Extracting archive
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
Create start.php
Create support/bootstrap.php
Create support/Plugin.php
16 package suggestions were added by new dependencies, use `composer suggest` to see details.
Generating autoload files
3 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html#
root@02891538d8c9:/var/www/html# cd webman_v1.3.7/
```

把 `vlucas/phpdotenv` 包写入项目 `composer.json` 文件：
```
{
  "name": "workerman/webman",
  "type": "project",
  "keywords": [
    "high performance",
    "http service"
  ],
  "homepage": "http://www.workerman.net",
  "license": "MIT",
  "description": "High performance HTTP Service Framework.",
  "authors": [
    {
      "name": "walkor",
      "email": "walkor@workerman.net",
      "homepage": "http://www.workerman.net",
      "role": "Developer"
    }
  ],
  "support": {
    "email": "walkor@workerman.net",
    "issues": "https://github.com/walkor/webman/issues",
    "forum": "http://wenda.workerman.net/",
    "wiki": "http://workerman.net/doc/webman",
    "source": "https://github.com/walkor/webman"
  },
  "require": {
    "php": ">=7.2",
    "workerman/webman-framework": "^1.3.12",
    "monolog/monolog": "^2.0",
    "vlucas/phpdotenv": "^5.4"
  },
  "suggest": {
    "ext-event": "For better performance. "
  },
  "autoload": {
    "psr-4": {
      "": "./",
      "App\\": "./app"
    },
    "files": [
      "./support/helpers.php"
    ]
  },
  "scripts": {
    "post-package-install": [
      "support\\Plugin::install"
    ],
    "post-package-update": [
      "support\\Plugin::install"
    ],
    "pre-package-uninstall": [
      "support\\Plugin::uninstall"
    ]
  }
}
```

安装 vlucas/phpdotenv 包：
> composer update vlucas/phpdotenv

```
root@02891538d8c9:/var/www/html/webman_v1.3.7# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Warning: The lock file is not up to date with the latest changes in composer.json. You may be getting outdated dependencies. It is recommended that you run `composer update` or `composer update <package name>`.
Nothing to install, update or remove
Generating autoload files
3 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7# composer update vlucas/phpdotenv
Loading composer repositories with package information
Updating dependencies
Lock file operations: 6 installs, 0 updates, 0 removals
  - Locking graham-campbell/result-type (v1.0.4)
  - Locking phpoption/phpoption (1.8.1)
  - Locking symfony/polyfill-ctype (v1.25.0)
  - Locking symfony/polyfill-mbstring (v1.25.0)
  - Locking symfony/polyfill-php80 (v1.25.0)
  - Locking vlucas/phpdotenv (v5.4.1)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 6 installs, 0 updates, 0 removals
  - Downloading symfony/polyfill-php80 (v1.25.0)
  - Installing symfony/polyfill-php80 (v1.25.0): Extracting archive
  - Installing symfony/polyfill-mbstring (v1.25.0): Extracting archive
  - Installing symfony/polyfill-ctype (v1.25.0): Extracting archive
  - Installing phpoption/phpoption (1.8.1): Extracting archive
  - Installing graham-campbell/result-type (v1.0.4): Extracting archive
  - Installing vlucas/phpdotenv (v5.4.1): Extracting archive
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
Generating autoload files
9 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/webman_v1.3.7#
```

### webman启动

webman启动：
> php start.php start

```
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7# php start.php start
Workerman[start.php] start in DEBUG mode
----------------------------------------- WORKERMAN -----------------------------------------
Workerman version:4.0.36          PHP version:7.4.28
------------------------------------------ WORKERS ------------------------------------------
proto   user            worker          listen                 processes    status
tcp     root            webman          http://0.0.0.0:8787    8             [OK]
tcp     root            monitor         none                   1             [OK]
---------------------------------------------------------------------------------------------
Press Ctrl+C to stop. Start success.
^CWorkerman[start.php] stopping ...
Workerman[start.php] has been stopped
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7# php start.php stop
Workerman[start.php] stop
Workerman[start.php] not run
root@02891538d8c9:/var/www/html/webman_v1.3.7#
```

### env加载分析

start.php 内容：
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

if (class_exists('Dotenv\Dotenv') && file_exists(base_path() . '/.env')) {
    if (method_exists('Dotenv\Dotenv', 'createUnsafeImmutable')) {
        Dotenv::createUnsafeImmutable(base_path())->load();
    } else {
        Dotenv::createMutable(base_path())->load();
    }
}

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
if (property_exists(Worker::class, 'stopTimeout')) {
    Worker::$stopTimeout = $config['stop_timeout'] ?? 2;
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
        'protocol'
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

// Windows does not support custom processes.
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

Worker::runAll();
```

里面这一段，就是加载 .env 配置：
```
if (class_exists('Dotenv\Dotenv') && file_exists(base_path() . '/.env')) {
    if (method_exists('Dotenv\Dotenv', 'createUnsafeImmutable')) {
        Dotenv::createUnsafeImmutable(base_path())->load();
    } else {
        Dotenv::createMutable(base_path())->load();
    }
}
```


## 参考资料

webman 安装 <https://www.workerman.net/doc/webman/install.html>

新版webman,取消env的原因是什么?  <https://www.workerman.net/q/7534>






