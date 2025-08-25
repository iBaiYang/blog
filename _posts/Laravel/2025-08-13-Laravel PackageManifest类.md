---
layout: post
categories: Laravel
title: Laravel PackageManifest类
meta: Laravel PackageManifest类
---
* content
{:toc}

## 基本概念

我们来深入、详细地讲解 Laravel 6 中的 `PackageManifest` 类。这个类是 Laravel 包自动发现（Package Auto-Discovery）功能的基石，虽然它不直接参与业务逻辑，但对框架的启动过程和开发者体验至关重要。

---

### 一、背景与要解决的问题

在 Laravel 5.5 之前，当你安装了一个第三方包（例如，用于调试的 `barryvdh/laravel-debugbar`），你需要手动完成以下步骤：

1.  在 `config/app.php` 的 `providers` 数组中添加服务提供者：`Barryvdh\Debugbar\ServiceProvider::class`
2.  在 `config/app.php` 的 `aliases` 数组中添加门面别名：`'Debugbar' => Barryvdh\Debugbar\Facade::class`
3.  可能还需要执行 `php artisan vendor:publish` 来发布配置文件。

这被称为“手动注册”，虽然不难，但繁琐且容易忘记。

Laravel 5.5 引入了“包自动发现”（Package Auto-Discovery），使得上述步骤（1和2）在安装包时自动完成。`PackageManifest` 类就是实现这一功能的**核心引擎**。

---

### 二、PackageManifest 是什么？

`PackageManifest` 类（位于 `Illuminate\Foundation\PackageManifest`）是一个在框架启动初期就被实例化的核心组件。它的主要职责是：

1.  扫描所有已安装的 Composer 包，寻找那些包含了 `extra.laravel` 声明的包。
2.  编译一个“服务清单”（manifest）文件，将所有这些包的自动发现配置（ providers, aliases, commands 等）聚合到一个 PHP 数组中。
3.  在框架构建服务容器时，提供这个清单信息，以便自动注册服务提供者和别名。

它的核心目的是在框架启动时极大地提升效率。如果没有它，Laravel 需要在每次请求时扫描所有 `vendor/` 目录下的 `composer.json` 文件，这将带来巨大的 I/O 开销。通过将结果缓存到一个文件中，它将昂贵的操作变成了一次性的（只在包更新时发生）。

---

### 三、工作原理与流程

`PackageManifest` 的工作流程可以清晰地划分为几个关键阶段，其核心目标是生成并利用缓存文件来加速框架启动。下图概括了其完整生命周期：

![]({{site.baseurl}}/images/Laravel/20250820114110.png)

下面是每个阶段的详细说明：

#### 1. 构建阶段 

- (`build` 方法) - 由 Composer 事件触发

这个过程并非在每次 HTTP 请求时发生，而是在你执行 `composer install` 或 `composer update` 时触发。

*   Composer 钩子： 在项目的 `composer.json` 中，Laravel 定义了一个 `post-autoload-dump` 脚本：

```json
"scripts": {
    "post-autoload-dump": [
        "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
        "@php artisan package:discover --ansi"
    ],
    "post-root-package-install": [
        "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
    ],
    "post-create-project-cmd": [
        "@php artisan key:generate --ansi"
    ]
}
```

*   执行命令： 当 Composer 的自动加载文件被dump后（即 `vendor/autoload.php` 被更新后），它会执行 `@php artisan package:discover`。
*   Artisan 命令： `package:discover` 命令的核心是调用 `PackageManifest->build()` 方法。
*   `build()` 方法的工作：
    *   遍历所有依赖： 读取 `vendor/composer/installed.json` 文件（Composer 生成的所有已安装包列表）。
    *   解析 `composer.json`： 对于每个包，检查其 `composer.json` 中的 `extra.laravel` 部分。
    *   聚合配置： 将所有包的自动发现配置收集到一个大数组中。
    *   写入缓存文件： 将这个大数据写入 `bootstrap/cache/packages.php` 文件中。这个文件就是一个返回数组的 PHP 文件，格式如下：

```php
<?php 
return [
    'packages' => [
        'barryvdh/laravel-debugbar' => [
            'providers' => [
                'Barryvdh\\Debugbar\\ServiceProvider',
            ],
            'aliases' => [
                'Debugbar' => 'Barryvdh\\Debugbar\\Facade',
            ],
        ],
        'laravel/tinker' => [
            'providers' => [
                'Laravel\\Tinker\\TinkerServiceProvider',
            ],
        ],
        // ... 其他包
    ],
];
```

#### 2. 读取阶段 

- 在每次请求时发生

在每次请求的框架启动阶段（即在 `public/index.php` 中创建 `$app = new Illuminate\Foundation\Application(...)` 之后），会发生以下事情：

*   应用实例创建： 在 `Illuminate\Foundation\Application` 的构造函数中，会注册一些基础的服务提供者，其中就包括 `PackageManifest` 本身。
*   清单初始化： `PackageManifest` 被实例化时，其构造函数会尝试**加载第一步中生成的 `bootstrap/cache/packages.php` 缓存文件**到内存中。
*   提供服务： 框架随后会调用 `PackageManifest->providers()` 和 `PackageManifest->aliases()` 方法。
*   自动注册： 在 `Illuminate\Foundation\Bootstrap\RegisterProviders` 这个启动类中，框架会获取 `PackageManifest->providers()` 返回的数组，并将其与 `config/app.php` 中的 `providers` 数组合并，然后一起注册到服务容器中。别名也是类似的过程。

这样一来，所有通过自动发现声明的服务提供者和别名，就和你在 `config/app.php` 中手动配置的没有任何区别了。

---

### 四、包开发者如何利用它

- （extra.laravel）

作为一个包开发者，要让你的包支持自动发现，你需要在你的 `composer.json` 中添加 `extra.laravel` 部分：

```json
{
    "name": "your-vendor/your-package",
    "extra": {
        "laravel": {
            "providers": [
                "YourVendor\\YourPackage\\YourPackageServiceProvider"
            ],
            "aliases": {
                "YourPackage": "YourVendor\\YourPackage\\Facades\\YourPackage"
            },
            "dont-discover": [
                "some-other-package/you-want-to-ignore"
            ]
        }
    }
}
```

*   providers: 指定要自动注册的服务提供者。
*   aliases: 指定要自动注册的门面别名。
*   dont-discover: 告诉 Laravel 即使安装了某些特定的包，也不要自动注册它们的服务提供者和别名。这在你的包与其他包发生冲突时非常有用。

当用户安装你的包时，Composer 会触发 `post-autoload-dump` 脚本，`PackageManifest` 会读取到这个配置并将其加入到清单缓存中，从而实现自动注册。

---

### 五、常见问题与操作

1、缓存问题： 最常见的问题是，你安装了一个包，但它没有自动注册。这通常是因为缓存未更新。

*   解决方案： 手动运行 `composer dump-autoload` 或 `php artisan package:discover`。这会强制 `PackageManifest` 重新构建缓存文件。

2、禁用自动发现：

*   有时你可能想完全禁用某个包的自动发现功能（例如，在特定环境不想使用它）。
*   方法： 在你的项目 `composer.json` 中配置 `extra.laravel.dont-discover`：

```json
"extra": {
    "laravel": {
        "dont-discover": [
            "barryvdh/laravel-debugbar"
        ]
    }
}
```

*   你也可以完全禁用整个自动发现功能（不推荐），通过重写 `PackageManifest` 类或移除 Composer 脚本，但这会破坏很多包的功能。

3、文件位置：

*   清单缓存文件： `bootstrap/cache/packages.php`
*   类文件位置： `vendor/laravel/framework/src/Illuminate/Foundation/PackageManifest.php`

---

## 总结

`PackageManifest` 类是 Laravel 架构中一个卓越的“性能优化”和“开发者体验”组件。它通过：

1. 将昂贵的 I/O 操作（扫描所有包） 从**每次请求**转移到**每次包安装/更新时**（通过 Composer 钩子），显著提升了框架的启动速度。
2. 提供了一个优雅的约定（`extra.laravel`），让包生态系统能够无缝集成到框架中，极大地简化了开发者的安装流程。
3. 其设计完美体现了 Laravel 的理念：约定优于配置，以及通过缓存和优化来提升性能。

理解 `PackageManifest` 有助于你更好地理解 Laravel 的启动过程、包机制，以及在遇到相关问题时能够快速定位和解决。

## 源码

### packages.php 文件

bootstrap/cache/packages.php 文件实例：

```php
<?php return array (
  'facade/ignition' => 
  array (
    'aliases' => 
    array (
      'Flare' => 'Facade\\Ignition\\Facades\\Flare',
    ),
    'providers' => 
    array (
      0 => 'Facade\\Ignition\\IgnitionServiceProvider',
    ),
  ),
  'fideloper/proxy' => 
  array (
    'providers' => 
    array (
      0 => 'Fideloper\\Proxy\\TrustedProxyServiceProvider',
    ),
  ),
  'laravel/tinker' => 
  array (
    'providers' => 
    array (
      0 => 'Laravel\\Tinker\\TinkerServiceProvider',
    ),
  ),
  'nesbot/carbon' => 
  array (
    'providers' => 
    array (
      0 => 'Carbon\\Laravel\\ServiceProvider',
    ),
  ),
  'nunomaduro/collision' => 
  array (
    'providers' => 
    array (
      0 => 'NunoMaduro\\Collision\\Adapters\\Laravel\\CollisionServiceProvider',
    ),
  ),
);
```

### PackageManifest 类

Illuminate\Foundation\PackageManifest 源码：

```php
<?php

namespace Illuminate\Foundation;

use Exception;
use Illuminate\Filesystem\Filesystem;

class PackageManifest
{
    /**
     * The filesystem instance.
     *
     * @var \Illuminate\Filesystem\Filesystem
     */
    public $files;

    /**
     * The base path.
     *
     * @var string
     */
    public $basePath;

    /**
     * The vendor path.
     *
     * @var string
     */
    public $vendorPath;

    /**
     * The manifest path.
     *
     * @var string|null
     */
    public $manifestPath;

    /**
     * The loaded manifest array.
     *
     * @var array
     */
    public $manifest;

    /**
     * Create a new package manifest instance.
     *
     * @param  \Illuminate\Filesystem\Filesystem  $files
     * @param  string  $basePath
     * @param  string  $manifestPath
     * @return void
     */
    public function __construct(Filesystem $files, $basePath, $manifestPath)
    {
        $this->files = $files;
        $this->basePath = $basePath;
        $this->manifestPath = $manifestPath;
        $this->vendorPath = $basePath.'/vendor';
    }

    /**
     * Get all of the service provider class names for all packages.
     *
     * @return array
     */
    public function providers()
    {
        return $this->config('providers');
    }

    /**
     * Get all of the aliases for all packages.
     *
     * @return array
     */
    public function aliases()
    {
        return $this->config('aliases');
    }

    /**
     * Get all of the values for all packages for the given configuration name.
     *
     * @param  string  $key
     * @return array
     */
    public function config($key)
    {
        return collect($this->getManifest())->flatMap(function ($configuration) use ($key) {
            return (array) ($configuration[$key] ?? []);
        })->filter()->all();
    }

    /**
     * Get the current package manifest.
     *
     * @return array
     */
    protected function getManifest()
    {
        if (! is_null($this->manifest)) {
            return $this->manifest;
        }

        if (! file_exists($this->manifestPath)) {
            $this->build();
        }

        return $this->manifest = file_exists($this->manifestPath) ?
            $this->files->getRequire($this->manifestPath) : [];
    }

    /**
     * Build the manifest and write it to disk.
     *
     * @return void
     */
    public function build()
    {
        $packages = [];

        if ($this->files->exists($path = $this->vendorPath.'/composer/installed.json')) {
            $installed = json_decode($this->files->get($path), true);

            $packages = $installed['packages'] ?? $installed;
        }

        $ignoreAll = in_array('*', $ignore = $this->packagesToIgnore());

        $this->write(collect($packages)->mapWithKeys(function ($package) {
            return [$this->format($package['name']) => $package['extra']['laravel'] ?? []];
        })->each(function ($configuration) use (&$ignore) {
            $ignore = array_merge($ignore, $configuration['dont-discover'] ?? []);
        })->reject(function ($configuration, $package) use ($ignore, $ignoreAll) {
            return $ignoreAll || in_array($package, $ignore);
        })->filter()->all());
    }

    /**
     * Format the given package name.
     *
     * @param  string  $package
     * @return string
     */
    protected function format($package)
    {
        return str_replace($this->vendorPath.'/', '', $package);
    }

    /**
     * Get all of the package names that should be ignored.
     *
     * @return array
     */
    protected function packagesToIgnore()
    {
        if (! file_exists($this->basePath.'/composer.json')) {
            return [];
        }

        return json_decode(file_get_contents(
            $this->basePath.'/composer.json'
        ), true)['extra']['laravel']['dont-discover'] ?? [];
    }

    /**
     * Write the given manifest array to disk.
     *
     * @param  array  $manifest
     * @return void
     *
     * @throws \Exception
     */
    protected function write(array $manifest)
    {
        if (! is_writable(dirname($this->manifestPath))) {
            throw new Exception('The '.dirname($this->manifestPath).' directory must be present and writable.');
        }

        $this->files->replace(
            $this->manifestPath, '<?php return '.var_export($manifest, true).';'
        );
    }
}
```

### Filesystem 类

Illuminate\Filesystem\Filesystem 源码：

```php
<?php

namespace Illuminate\Filesystem;

use ErrorException;
use FilesystemIterator;
use Illuminate\Contracts\Filesystem\FileNotFoundException;
use Illuminate\Support\Traits\Macroable;
use Symfony\Component\Finder\Finder;

class Filesystem
{
    use Macroable;

    /**
     * Determine if a file or directory exists.
     *
     * @param  string  $path
     * @return bool
     */
    public function exists($path)
    {
        return file_exists($path);
    }

    /**
     * Determine if a file or directory is missing.
     *
     * @param  string  $path
     * @return bool
     */
    public function missing($path)
    {
        return ! $this->exists($path);
    }

    /**
     * Get the contents of a file.
     *
     * @param  string  $path
     * @param  bool  $lock
     * @return string
     *
     * @throws \Illuminate\Contracts\Filesystem\FileNotFoundException
     */
    public function get($path, $lock = false)
    {
        if ($this->isFile($path)) {
            return $lock ? $this->sharedGet($path) : file_get_contents($path);
        }

        throw new FileNotFoundException("File does not exist at path {$path}");
    }

    /**
     * Get contents of a file with shared access.
     *
     * @param  string  $path
     * @return string
     */
    public function sharedGet($path)
    {
        $contents = '';

        $handle = fopen($path, 'rb');

        if ($handle) {
            try {
                if (flock($handle, LOCK_SH)) {
                    clearstatcache(true, $path);

                    $contents = fread($handle, $this->size($path) ?: 1);

                    flock($handle, LOCK_UN);
                }
            } finally {
                fclose($handle);
            }
        }

        return $contents;
    }

    /**
     * Get the returned value of a file.
     *
     * @param  string  $path
     * @return mixed
     *
     * @throws \Illuminate\Contracts\Filesystem\FileNotFoundException
     */
    public function getRequire($path)
    {
        if ($this->isFile($path)) {
            return require $path;
        }

        throw new FileNotFoundException("File does not exist at path {$path}");
    }

    /**
     * Require the given file once.
     *
     * @param  string  $file
     * @return mixed
     */
    public function requireOnce($file)
    {
        require_once $file;
    }

    /**
     * Get the MD5 hash of the file at the given path.
     *
     * @param  string  $path
     * @return string
     */
    public function hash($path)
    {
        return md5_file($path);
    }

    /**
     * Write the contents of a file.
     *
     * @param  string  $path
     * @param  string  $contents
     * @param  bool  $lock
     * @return int|bool
     */
    public function put($path, $contents, $lock = false)
    {
        return file_put_contents($path, $contents, $lock ? LOCK_EX : 0);
    }

    /**
     * Write the contents of a file, replacing it atomically if it already exists.
     *
     * @param  string  $path
     * @param  string  $content
     * @return void
     */
    public function replace($path, $content)
    {
        // If the path already exists and is a symlink, get the real path...
        clearstatcache(true, $path);

        $path = realpath($path) ?: $path;

        $tempPath = tempnam(dirname($path), basename($path));

        // Fix permissions of tempPath because `tempnam()` creates it with permissions set to 0600...
        chmod($tempPath, 0777 - umask());

        file_put_contents($tempPath, $content);

        rename($tempPath, $path);
    }

    /**
     * Prepend to a file.
     *
     * @param  string  $path
     * @param  string  $data
     * @return int
     */
    public function prepend($path, $data)
    {
        if ($this->exists($path)) {
            return $this->put($path, $data.$this->get($path));
        }

        return $this->put($path, $data);
    }

    /**
     * Append to a file.
     *
     * @param  string  $path
     * @param  string  $data
     * @return int
     */
    public function append($path, $data)
    {
        return file_put_contents($path, $data, FILE_APPEND);
    }

    /**
     * Get or set UNIX mode of a file or directory.
     *
     * @param  string  $path
     * @param  int|null  $mode
     * @return mixed
     */
    public function chmod($path, $mode = null)
    {
        if ($mode) {
            return chmod($path, $mode);
        }

        return substr(sprintf('%o', fileperms($path)), -4);
    }

    /**
     * Delete the file at a given path.
     *
     * @param  string|array  $paths
     * @return bool
     */
    public function delete($paths)
    {
        $paths = is_array($paths) ? $paths : func_get_args();

        $success = true;

        foreach ($paths as $path) {
            try {
                if (! @unlink($path)) {
                    $success = false;
                }
            } catch (ErrorException $e) {
                $success = false;
            }
        }

        return $success;
    }

    /**
     * Move a file to a new location.
     *
     * @param  string  $path
     * @param  string  $target
     * @return bool
     */
    public function move($path, $target)
    {
        return rename($path, $target);
    }

    /**
     * Copy a file to a new location.
     *
     * @param  string  $path
     * @param  string  $target
     * @return bool
     */
    public function copy($path, $target)
    {
        return copy($path, $target);
    }

    /**
     * Create a symlink to the target file or directory. On Windows, a hard link is created if the target is a file.
     *
     * @param  string  $target
     * @param  string  $link
     * @return void
     */
    public function link($target, $link)
    {
        if (! windows_os()) {
            return symlink($target, $link);
        }

        $mode = $this->isDirectory($target) ? 'J' : 'H';

        exec("mklink /{$mode} ".escapeshellarg($link).' '.escapeshellarg($target));
    }

    /**
     * Extract the file name from a file path.
     *
     * @param  string  $path
     * @return string
     */
    public function name($path)
    {
        return pathinfo($path, PATHINFO_FILENAME);
    }

    /**
     * Extract the trailing name component from a file path.
     *
     * @param  string  $path
     * @return string
     */
    public function basename($path)
    {
        return pathinfo($path, PATHINFO_BASENAME);
    }

    /**
     * Extract the parent directory from a file path.
     *
     * @param  string  $path
     * @return string
     */
    public function dirname($path)
    {
        return pathinfo($path, PATHINFO_DIRNAME);
    }

    /**
     * Extract the file extension from a file path.
     *
     * @param  string  $path
     * @return string
     */
    public function extension($path)
    {
        return pathinfo($path, PATHINFO_EXTENSION);
    }

    /**
     * Get the file type of a given file.
     *
     * @param  string  $path
     * @return string
     */
    public function type($path)
    {
        return filetype($path);
    }

    /**
     * Get the mime-type of a given file.
     *
     * @param  string  $path
     * @return string|false
     */
    public function mimeType($path)
    {
        return finfo_file(finfo_open(FILEINFO_MIME_TYPE), $path);
    }

    /**
     * Get the file size of a given file.
     *
     * @param  string  $path
     * @return int
     */
    public function size($path)
    {
        return filesize($path);
    }

    /**
     * Get the file's last modification time.
     *
     * @param  string  $path
     * @return int
     */
    public function lastModified($path)
    {
        return filemtime($path);
    }

    /**
     * Determine if the given path is a directory.
     *
     * @param  string  $directory
     * @return bool
     */
    public function isDirectory($directory)
    {
        return is_dir($directory);
    }

    /**
     * Determine if the given path is readable.
     *
     * @param  string  $path
     * @return bool
     */
    public function isReadable($path)
    {
        return is_readable($path);
    }

    /**
     * Determine if the given path is writable.
     *
     * @param  string  $path
     * @return bool
     */
    public function isWritable($path)
    {
        return is_writable($path);
    }

    /**
     * Determine if the given path is a file.
     *
     * @param  string  $file
     * @return bool
     */
    public function isFile($file)
    {
        return is_file($file);
    }

    /**
     * Find path names matching a given pattern.
     *
     * @param  string  $pattern
     * @param  int  $flags
     * @return array
     */
    public function glob($pattern, $flags = 0)
    {
        return glob($pattern, $flags);
    }

    /**
     * Get an array of all files in a directory.
     *
     * @param  string  $directory
     * @param  bool  $hidden
     * @return \Symfony\Component\Finder\SplFileInfo[]
     */
    public function files($directory, $hidden = false)
    {
        return iterator_to_array(
            Finder::create()->files()->ignoreDotFiles(! $hidden)->in($directory)->depth(0)->sortByName(),
            false
        );
    }

    /**
     * Get all of the files from the given directory (recursive).
     *
     * @param  string  $directory
     * @param  bool  $hidden
     * @return \Symfony\Component\Finder\SplFileInfo[]
     */
    public function allFiles($directory, $hidden = false)
    {
        return iterator_to_array(
            Finder::create()->files()->ignoreDotFiles(! $hidden)->in($directory)->sortByName(),
            false
        );
    }

    /**
     * Get all of the directories within a given directory.
     *
     * @param  string  $directory
     * @return array
     */
    public function directories($directory)
    {
        $directories = [];

        foreach (Finder::create()->in($directory)->directories()->depth(0)->sortByName() as $dir) {
            $directories[] = $dir->getPathname();
        }

        return $directories;
    }

    /**
     * Ensure a directory exists.
     *
     * @param  string  $path
     * @param  int  $mode
     * @param  bool  $recursive
     * @return void
     */
    public function ensureDirectoryExists($path, $mode = 0755, $recursive = true)
    {
        if (! $this->isDirectory($path)) {
            $this->makeDirectory($path, $mode, $recursive);
        }
    }

    /**
     * Create a directory.
     *
     * @param  string  $path
     * @param  int  $mode
     * @param  bool  $recursive
     * @param  bool  $force
     * @return bool
     */
    public function makeDirectory($path, $mode = 0755, $recursive = false, $force = false)
    {
        if ($force) {
            return @mkdir($path, $mode, $recursive);
        }

        return mkdir($path, $mode, $recursive);
    }

    /**
     * Move a directory.
     *
     * @param  string  $from
     * @param  string  $to
     * @param  bool  $overwrite
     * @return bool
     */
    public function moveDirectory($from, $to, $overwrite = false)
    {
        if ($overwrite && $this->isDirectory($to) && ! $this->deleteDirectory($to)) {
            return false;
        }

        return @rename($from, $to) === true;
    }

    /**
     * Copy a directory from one location to another.
     *
     * @param  string  $directory
     * @param  string  $destination
     * @param  int|null  $options
     * @return bool
     */
    public function copyDirectory($directory, $destination, $options = null)
    {
        if (! $this->isDirectory($directory)) {
            return false;
        }

        $options = $options ?: FilesystemIterator::SKIP_DOTS;

        // If the destination directory does not actually exist, we will go ahead and
        // create it recursively, which just gets the destination prepared to copy
        // the files over. Once we make the directory we'll proceed the copying.
        if (! $this->isDirectory($destination)) {
            $this->makeDirectory($destination, 0777, true);
        }

        $items = new FilesystemIterator($directory, $options);

        foreach ($items as $item) {
            // As we spin through items, we will check to see if the current file is actually
            // a directory or a file. When it is actually a directory we will need to call
            // back into this function recursively to keep copying these nested folders.
            $target = $destination.'/'.$item->getBasename();

            if ($item->isDir()) {
                $path = $item->getPathname();

                if (! $this->copyDirectory($path, $target, $options)) {
                    return false;
                }
            }

            // If the current items is just a regular file, we will just copy this to the new
            // location and keep looping. If for some reason the copy fails we'll bail out
            // and return false, so the developer is aware that the copy process failed.
            else {
                if (! $this->copy($item->getPathname(), $target)) {
                    return false;
                }
            }
        }

        return true;
    }

    /**
     * Recursively delete a directory.
     *
     * The directory itself may be optionally preserved.
     *
     * @param  string  $directory
     * @param  bool  $preserve
     * @return bool
     */
    public function deleteDirectory($directory, $preserve = false)
    {
        if (! $this->isDirectory($directory)) {
            return false;
        }

        $items = new FilesystemIterator($directory);

        foreach ($items as $item) {
            // If the item is a directory, we can just recurse into the function and
            // delete that sub-directory otherwise we'll just delete the file and
            // keep iterating through each file until the directory is cleaned.
            if ($item->isDir() && ! $item->isLink()) {
                $this->deleteDirectory($item->getPathname());
            }

            // If the item is just a file, we can go ahead and delete it since we're
            // just looping through and waxing all of the files in this directory
            // and calling directories recursively, so we delete the real path.
            else {
                $this->delete($item->getPathname());
            }
        }

        if (! $preserve) {
            @rmdir($directory);
        }

        return true;
    }

    /**
     * Remove all of the directories within a given directory.
     *
     * @param  string  $directory
     * @return bool
     */
    public function deleteDirectories($directory)
    {
        $allDirectories = $this->directories($directory);

        if (! empty($allDirectories)) {
            foreach ($allDirectories as $directoryName) {
                $this->deleteDirectory($directoryName);
            }

            return true;
        }

        return false;
    }

    /**
     * Empty the specified directory of all files and folders.
     *
     * @param  string  $directory
     * @return bool
     */
    public function cleanDirectory($directory)
    {
        return $this->deleteDirectory($directory, true);
    }
}
```

### ComposerScripts 类

Illuminate\Foundation\ComposerScripts 源码：

```php
<?php

namespace Illuminate\Foundation;

use Composer\Script\Event;

class ComposerScripts
{
    /**
     * Handle the post-install Composer event.
     *
     * @param  \Composer\Script\Event  $event
     * @return void
     */
    public static function postInstall(Event $event)
    {
        require_once $event->getComposer()->getConfig()->get('vendor-dir').'/autoload.php';

        static::clearCompiled();
    }

    /**
     * Handle the post-update Composer event.
     *
     * @param  \Composer\Script\Event  $event
     * @return void
     */
    public static function postUpdate(Event $event)
    {
        require_once $event->getComposer()->getConfig()->get('vendor-dir').'/autoload.php';

        static::clearCompiled();
    }

    /**
     * Handle the post-autoload-dump Composer event.
     *
     * @param  \Composer\Script\Event  $event
     * @return void
     */
    public static function postAutoloadDump(Event $event)
    {
        require_once $event->getComposer()->getConfig()->get('vendor-dir').'/autoload.php';

        static::clearCompiled();
    }

    /**
     * Clear the cached Laravel bootstrapping files.
     *
     * @return void
     */
    protected static function clearCompiled()
    {
        $laravel = new Application(getcwd());

        if (file_exists($servicesPath = $laravel->getCachedServicesPath())) {
            @unlink($servicesPath);
        }

        if (file_exists($packagesPath = $laravel->getCachedPackagesPath())) {
            @unlink($packagesPath);
        }
    }
}
```









## 参考资料




