---
layout: post
categories: PHP
title: webman env配置文件加载分析
meta: webman env配置文件加载分析
---
* content
{:toc}

## 正文

webman env配置文件加载，使用的包是 `vlucas/phpdotenv`，Laravel 也使用的这个包加载env配置文件。

获取配置使用的是包 `illuminate/support`，Laravel 也使用的这个包。

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

项目根目录下的`.env` 文件中写入配置内容，如：
```
# 数据库配置
database.hostname = 127.0.0.1
database.database = dataku
database.username = xyz
database.password = abc123
database.hostport = 3306
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

### .env加载分析

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

### 获取分析

在项目代码中获取环境配置，如：
```
$host = env('database.hostname', '127.0.0.1');
```

使用包 `illuminate/support` ,好多包依赖于这个包，如 `illuminate/bus` 、 `illuminate/database` 、 `illuminate/events` 、
`illuminate/events` 、 `illuminate/pagination` 、 `illuminate/pipeline` 、 `illuminate/redis` 等。

项目 `composer.json` 文件 中增加 `psr/container` 、 `illuminate/support`：
```
  "require": {
    "php": ">=7.2",
    "workerman/webman-framework": "^1.3.12",
    "monolog/monolog": "^2.0",
    "vlucas/phpdotenv": "^5.4",
    "psr/container": "^1.0",
    "illuminate/support": "^8.83"
  },
```

安装包：
> composer update psr/container
>
> composer update illuminate/support

```
root@02891538d8c9:/var/www/html/webman_v1.3.7# composer update illuminate/support
Loading composer repositories with package information
Updating dependencies
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - illuminate/contracts[v8.0.0, ..., v8.83.11] require psr/container ^1.0 -> found psr/contaut the package is fixed to 2.0.2 (lock file version) by a partial update and that version does s an argument for the update command.
    - illuminate/support[v8.83.0, ..., v8.83.11] require illuminate/contracts ^8.0 -> satisfiab ..., v8.83.11].
    - Root composer.json requires illuminate/support ^8.83 -> satisfiable by illuminate/support

Use the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packversions.
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7# composer update psr/container
Loading composer repositories with package information

Updating dependencies
Lock file operations: 11 installs, 1 update, 0 removals
  - Locking doctrine/inflector (2.0.4)
  - Locking illuminate/collections (v8.83.11)
  - Locking illuminate/contracts (v8.83.11)
  - Locking illuminate/macroable (v8.83.11)
  - Locking illuminate/support (v8.83.0)
  - Locking nesbot/carbon (2.58.0)
  - Downgrading psr/container (2.0.2 => 1.1.2)
  - Locking psr/simple-cache (1.0.1)
  - Locking symfony/deprecation-contracts (v2.5.1)
  - Locking symfony/translation (v5.4.8)
  - Locking symfony/translation-contracts (v2.5.1)
  - Locking voku/portable-ascii (1.6.1)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 11 installs, 1 update, 0 removals
  - Downloading symfony/translation-contracts (v2.5.1)
  - Downloading symfony/deprecation-contracts (v2.5.1)
  - Downloading symfony/translation (v5.4.8)
  - Downloading nesbot/carbon (2.58.0)
  - Downloading illuminate/collections (v8.83.11)
  - Installing voku/portable-ascii (1.6.1): Extracting archive
  - Installing symfony/translation-contracts (v2.5.1): Extracting archive
  - Installing symfony/deprecation-contracts (v2.5.1): Extracting archive
  - Installing symfony/translation (v5.4.8): Extracting archive
  - Installing nesbot/carbon (2.58.0): Extracting archive
  - Installing illuminate/macroable (v8.83.11): Extracting archive
  - Installing psr/simple-cache (1.0.1): Extracting archive
  - Downgrading psr/container (2.0.2 => 1.1.2): Extracting archive
  - Installing illuminate/contracts (v8.83.11): Extracting archive
  - Installing illuminate/collections (v8.83.11): Extracting archive
  - Installing doctrine/inflector (2.0.4): Extracting archive
  - Installing illuminate/support (v8.83.0): Extracting archive
 11/12 [=========================>--]  91%    Skipped installation of bin bin/carbon for package nesbot/carbon: file not found in package
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
> support\Plugin::install
9 package suggestions were added by new dependencies, use `composer suggest` to see details.
Generating autoload files
15 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7# composer update illuminate/support
Loading composer repositories with package information
Updating dependencies
Lock file operations: 0 installs, 1 update, 0 removals
  - Upgrading illuminate/support (v8.83.0 => v8.83.11)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 0 installs, 1 update, 0 removals
  - Downloading illuminate/support (v8.83.11)
  - Upgrading illuminate/support (v8.83.0 => v8.83.11): Extracting archive
> support\Plugin::install
Generating autoload files
15 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/webman_v1.3.7#
root@02891538d8c9:/var/www/html/webman_v1.3.7#
```

在`./vendor/illuminate/support/helpers.php` 文件中有：
```php
<?php

use Illuminate\Contracts\Support\DeferringDisplayableValue;
use Illuminate\Contracts\Support\Htmlable;
use Illuminate\Support\Arr;
use Illuminate\Support\Env;
use Illuminate\Support\HigherOrderTapProxy;
use Illuminate\Support\Optional;

// 省略若干

if (! function_exists('env')) {
    /**
     * Gets the value of an environment variable.
     *
     * @param  string  $key
     * @param  mixed  $default
     * @return mixed
     */
    function env($key, $default = null)
    {
        return Env::get($key, $default);
    }
}

// 省略若干
```

在`./vendor/illuminate/support/Env.php` 文件中有：
```php
<?php

namespace Illuminate\Support;

use Dotenv\Repository\Adapter\PutenvAdapter;
use Dotenv\Repository\RepositoryBuilder;
use PhpOption\Option;

class Env
{
    /**
     * Indicates if the putenv adapter is enabled.
     *
     * @var bool
     */
    protected static $putenv = true;

    /**
     * The environment repository instance.
     *
     * @var \Dotenv\Repository\RepositoryInterface|null
     */
    protected static $repository;

    /**
     * Enable the putenv adapter.
     *
     * @return void
     */
    public static function enablePutenv()
    {
        static::$putenv = true;
        static::$repository = null;
    }

    /**
     * Disable the putenv adapter.
     *
     * @return void
     */
    public static function disablePutenv()
    {
        static::$putenv = false;
        static::$repository = null;
    }

    /**
     * Get the environment repository instance.
     *
     * @return \Dotenv\Repository\RepositoryInterface
     */
    public static function getRepository()
    {
        if (static::$repository === null) {
            $builder = RepositoryBuilder::createWithDefaultAdapters();

            if (static::$putenv) {
                $builder = $builder->addAdapter(PutenvAdapter::class);
            }

            static::$repository = $builder->immutable()->make();
        }

        return static::$repository;
    }

    /**
     * Gets the value of an environment variable.
     *
     * @param  string  $key
     * @param  mixed  $default
     * @return mixed
     */
    public static function get($key, $default = null)
    {
        return Option::fromValue(static::getRepository()->get($key))
            ->map(function ($value) {
                switch (strtolower($value)) {
                    case 'true':
                    case '(true)':
                        return true;
                    case 'false':
                    case '(false)':
                        return false;
                    case 'empty':
                    case '(empty)':
                        return '';
                    case 'null':
                    case '(null)':
                        return;
                }

                if (preg_match('/\A([\'"])(.*)\1\z/', $value, $matches)) {
                    return $matches[2];
                }

                return $value;
            })
            ->getOrCall(function () use ($default) {
                return value($default);
            });
    }
}
```

在这里看到了类 `Dotenv\Repository\RepositoryBuilder`，这个类是 `vlucas/phpdotenv` 包中的类。

## 参考资料

webman 安装 <https://www.workerman.net/doc/webman/install.html>

vlucas/phpdotenv <https://packagist.org/packages/vlucas/phpdotenv>

vlucas/phpdotenv <https://github.com/vlucas/phpdotenv>

新版webman,取消env的原因是什么?  <https://www.workerman.net/q/7534>

laravel的vlucas/phpdotenv源码解读 <https://blog.csdn.net/linqing727/article/details/81986894>




