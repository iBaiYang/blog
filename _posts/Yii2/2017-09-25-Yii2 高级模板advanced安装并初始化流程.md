---
layout: post
categories: Yii2
title: Yii2 高级模板advanced安装并初始化流程
meta: Yii2 高级模板advanced安装并初始化流程
---
* content
{:toc}

### 正文

<http://blog.sina.com.cn/s/blog_6aba78b40102x55u.html>

下面流程：

先安装yii2包，有多种方式，或composer、或归档文件等。这里举一下composer的例子：

```
composer global require "fxp/composer-asset-plugin:~1.1"
```

![]({{site.baseurl}}/images/20200417/20200417132730.png)

这一步命令安装 Composer asset plugin， 它是通过 Composer 管理 bower 和 npm 包所必须的，此命令全局生效，一劳永逸。

```
composer create-project --prefer-dist yiisoft/yii2-app-advanced yii2_test
```

第一个箭头所指token那里，需要在github生成：

setting->Developer settings->Personal access tokens->Generate new token按钮，全勾生成，复制生成的字符串后，在箭头那里粘贴，回车。

![]({{site.baseurl}}/images/20200417/20200417132731.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132732.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132733.jpeg)

运行途中遇到bug类问题，可以去这看一下：

<http://blog.csdn.net/u014175572/article/details/55510825>

<http://blog.csdn.net/zqtsx/article/details/41517965>

接着域名相关：

本地开发环境域名配置：

windows下  C:\Windows\System32\drivers\etc\hosts

linux下  /etc/hosts

加入重定向配置：
```
127.0.0.1 yii2_advanced_backend.host
127.0.0.1 yii2_advanced_frontend.host
```

vhost配置：

/etc/nginx/nginx.conf 文件内容：
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
    include /var/www/vhost/*.conf;
}


#mail {
#    # See sample authentication script at:
#    # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#    # auth_http localhost/auth.php;
#    # pop3_capabilities "TOP" "USER";
#    # imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#    server {
#        listen     localhost:110;
#        protocol   pop3;
#        proxy      on;
#    }
# 
#    server {
#        listen     localhost:143;
#        protocol   imap;
#        proxy      on;
#    }
#}
```

/var/www/vhost/yii2_advanced_backend.host 文件内容：
```
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name yii2_advanced_backend.host;
    root        /var/www/yii2_advanced/backend/web;
    index       index.php;

    access_log  /var/www/yii2_advanced/backend/web/log/access.log;
    error_log   /var/www/yii2_advanced/backend/web/log/error.log;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # uncomment to avoid processing of calls to non-existing static files by Yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

/var/www/vhost/yii2_advanced_frontend.host 文件内容：
```
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name yii2_advanced_frontend.host;
    root        /var/www/yii2_advanced/frontend/web;
    index       index.php;

    access_log  /var/www/yii2_advanced/frontend/web/log/access.log;
    error_log   /var/www/yii2_advanced/frontend/web/log/error.log;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # uncomment to avoid processing of calls to non-existing static files by Yii
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

开始初始化：

运行项目根目录下的init文件

> php init

![]({{site.baseurl}}/images/20200417/20200417132736.jpeg)

![]({{site.baseurl}}/images/20200417/20200417132737.jpeg)

看一下init文件内容：
```
#!/usr/bin/env php
<?php
/**
 * Yii Application Initialization Tool
 *
 * In order to run in non-interactive mode:
 *
 * init --env=Development --overwrite=n
 */

if (!extension_loaded('openssl')) {
    die('The OpenSSL PHP extension is required by Yii2.');
}

$params = getParams();
$root = str_replace('\\', '/', __DIR__);
$envs = require "$root/environments/index.php";
$envNames = array_keys($envs);

echo "Yii Application Initialization Tool v1.0\n\n";

$envName = null;
if (empty($params['env']) || $params['env'] === true) {
    echo "Which environment do you want the application to be initialized in?\n\n";
    foreach ($envNames as $i => $name) {
        echo "  [$i] $name\n";
    }
    echo "\n  Your choice [0-" . (count($envs) - 1) . ', or "q" to quit] ';
    $answer = trim(fgets(STDIN));

    if (!ctype_digit($answer) || !in_array($answer, range(0, count($envs) - 1))) {
        echo "\n  Quit initialization.\n";
        exit(0);
    }

    if (isset($envNames[$answer])) {
        $envName = $envNames[$answer];
    }
} else {
    $envName = $params['env'];
}

if (!in_array($envName, $envNames, true)) {
    $envsList = implode(', ', $envNames);
    echo "\n  $envName is not a valid environment. Try one of the following: $envsList. \n";
    exit(2);
}

$env = $envs[$envName];

if (empty($params['env'])) {
    echo "\n  Initialize the application under '{$envNames[$answer]}' environment? [yes|no] ";
    $answer = trim(fgets(STDIN));
    if (strncasecmp($answer, 'y', 1)) {
        echo "\n  Quit initialization.\n";
        exit(0);
    }
}

$rootPath = "$root/environments/{$env['path']}";
if (!is_dir($rootPath)) {
    printError("$rootPath directory does not exist. Check path in $envName environment.");
    exit(3);
}

echo "\n  Start initialization ...\n\n";

$files = getFileList($rootPath);
if (isset($env['skipFiles'])) {
    $skipFiles = $env['skipFiles'];
    array_walk($skipFiles, function(&$value) use($env, $root) { $value = "$root/$value"; });
    $files = array_diff($files, array_intersect_key($env['skipFiles'], array_filter($skipFiles, 'file_exists')));
}
$all = false;
foreach ($files as $file) {
    if (!copyFile($root, "environments/{$env['path']}/$file", $file, $all, $params)) {
        break;
    }
}

$filesToRemove = [];
$skipFiles = !empty($env['skipFiles']) ? $env['skipFiles'] : [];
foreach(array_column($envs, 'path') as $envPath) {
    if ($env['path'] === $envPath) continue;

	$filesToRemove =
        array_merge(
            $filesToRemove,
            array_diff(getFileList("$root/environments/{$envPath}"), $files, $filesToRemove, $skipFiles)
        );
}
$filesToRemove = array_filter($filesToRemove, 'file_exists');
if ($filesToRemove) {
	echo "\n  Remove files from other environments ...\n\n";

	$all = false;
	foreach ($filesToRemove as $file) {
		if (!removeFile($root, $file, $all, $params)) {
			break;
		}
	}
	echo "\n";
}

$callbacks = ['setCookieValidationKey', 'setWritable', 'setExecutable', 'createSymlink'];
foreach ($callbacks as $callback) {
    if (!empty($env[$callback])) {
        $callback($root, $env[$callback]);
    }
}

echo "\n  ... initialization completed.\n\n";

function getFileList($root, $basePath = '')
{
    $files = [];
    $handle = opendir($root);
    while (($path = readdir($handle)) !== false) {
        if ($path === '.git' || $path === '.svn' || $path === '.' || $path === '..') {
            continue;
        }
        $fullPath = "$root/$path";
        $relativePath = $basePath === '' ? $path : "$basePath/$path";
        if (is_dir($fullPath)) {
            $files = array_merge($files, getFileList($fullPath, $relativePath));
        } else {
            $files[] = $relativePath;
        }
    }
    closedir($handle);
    return $files;
}

function copyFile($root, $source, $target, &$all, $params)
{
    if (!is_file($root . '/' . $source)) {
        echo "       skip $target ($source not exist)\n";
        return true;
    }
    if (is_file($root . '/' . $target)) {
        if (file_get_contents($root . '/' . $source) === file_get_contents($root . '/' . $target)) {
            echo "  unchanged $target\n";
            return true;
        }
        if ($all) {
            echo "  overwrite $target\n";
        } else {
            echo "      exist $target\n";
            echo "            ...overwrite? [Yes|No|All|Quit] ";


            $answer = !empty($params['overwrite']) ? $params['overwrite'] : trim(fgets(STDIN));
            if (!strncasecmp($answer, 'q', 1)) {
                return false;
            } else {
                if (!strncasecmp($answer, 'y', 1)) {
                    echo "  overwrite $target\n";
                } else {
                    if (!strncasecmp($answer, 'a', 1)) {
                        echo "  overwrite $target\n";
                        $all = true;
                    } else {
                        echo "       skip $target\n";
                        return true;
                    }
                }
            }
        }
        file_put_contents($root . '/' . $target, file_get_contents($root . '/' . $source));
        return true;
    }
    echo "   generate $target\n";
    @mkdir(dirname($root . '/' . $target), 0777, true);
    file_put_contents($root . '/' . $target, file_get_contents($root . '/' . $source));
    return true;
}

function removeFile($root, $target, &$all, $params)
{
	if (is_file($root . '/' . $target)) {
		if ($all) {
			echo "  delete $target\n";
		} else {
			echo "      delete $target\n";
			echo "            ...confirm? [Yes|No|All|Quit] ";

			$answer = !empty($params['delete']) ? $params['delete'] : trim(fgets(STDIN));
			if (!strncasecmp($answer, 'q', 1)) {
				return false;
			} else {
				if (!strncasecmp($answer, 'y', 1)) {
					echo "  delete $target\n";
				} else {
					if (!strncasecmp($answer, 'a', 1)) {
						echo "  delete $target\n";
						$all = true;
					} else {
						echo "       skip $target\n";
						return true;
					}
				}
			}
		}
		return unlink($root . '/' . $target);
	}

	return true;
}

function getParams()
{
    $rawParams = [];
    if (isset($_SERVER['argv'])) {
        $rawParams = $_SERVER['argv'];
        array_shift($rawParams);
    }

    $params = [];
    foreach ($rawParams as $param) {
        if (preg_match('/^--([\w-]*\w)(=(.*))?$/', $param, $matches)) {
            $name = $matches[1];
            $params[$name] = isset($matches[3]) ? $matches[3] : true;
        } else {
            $params[] = $param;
        }
    }
    return $params;
}

function setWritable($root, $paths)
{
    foreach ($paths as $writable) {
        if (is_dir("$root/$writable")) {
            if (@chmod("$root/$writable", 0777)) {
                echo "      chmod 0777 $writable\n";
            } else {
                printError("Operation chmod not permitted for directory $writable.");
            }
        } else {
            printError("Directory $writable does not exist.");
        }
    }
}

function setExecutable($root, $paths)
{
    foreach ($paths as $executable) {
        if (file_exists("$root/$executable")) {
            if (@chmod("$root/$executable", 0755)) {
                echo "      chmod 0755 $executable\n";
            } else {
                printError("Operation chmod not permitted for $executable.");
            }
        } else {
            printError("$executable does not exist.");
        }
    }
}

function setCookieValidationKey($root, $paths)
{
    foreach ($paths as $file) {
        echo "   generate cookie validation key in $file\n";
        $file = $root . '/' . $file;
        $length = 32;
        $bytes = openssl_random_pseudo_bytes($length);
        $key = strtr(substr(base64_encode($bytes), 0, $length), '+/=', '_-.');
        $content = preg_replace('/(("|\')cookieValidationKey("|\')\s*=>\s*)(""|\'\')/', "\\1'$key'", file_get_contents($file));
        file_put_contents($file, $content);
    }
}

function createSymlink($root, $links)
{
    foreach ($links as $link => $target) {
        //first removing folders to avoid errors if the folder already exists
        @rmdir($root . "/" . $link);
        //next removing existing symlink in order to update the target
        if (is_link($root . "/" . $link)) {
            @unlink($root . "/" . $link);
        }
        if (@symlink($root . "/" . $target, $root . "/" . $link)) {
            echo "      symlink $root/$target $root/$link\n";
        } else {
            printError("Cannot create symlink $root/$target $root/$link.");
        }
    }
}

/**
 * Prints error message.
 * @param string $message message
 */
function printError($message)
{
    echo "\n  " . formatMessage("Error. $message", ['fg-red']) . " \n";
}

/**
 * Returns true if the stream supports colorization. ANSI colors are disabled if not supported by the stream.
 *
 * - windows without ansicon
 * - not tty consoles
 *
 * @return boolean true if the stream supports ANSI colors, otherwise false.
 */
function ansiColorsSupported()
{
    return DIRECTORY_SEPARATOR === '\\'
        ? getenv('ANSICON') !== false || getenv('ConEmuANSI') === 'ON'
        : function_exists('posix_isatty') && @posix_isatty(STDOUT);
}

/**
 * Get ANSI code of style.
 * @param string $name style name
 * @return integer ANSI code of style.
 */
function getStyleCode($name)
{
    $styles = [
        'bold' => 1,
        'fg-black' => 30,
        'fg-red' => 31,
        'fg-green' => 32,
        'fg-yellow' => 33,
        'fg-blue' => 34,
        'fg-magenta' => 35,
        'fg-cyan' => 36,
        'fg-white' => 37,
        'bg-black' => 40,
        'bg-red' => 41,
        'bg-green' => 42,
        'bg-yellow' => 43,
        'bg-blue' => 44,
        'bg-magenta' => 45,
        'bg-cyan' => 46,
        'bg-white' => 47,
    ];
    return $styles[$name];
}

/**
 * Formats message using styles if STDOUT supports it.
 * @param string $message message
 * @param string[] $styles styles
 * @return string formatted message.
 */
function formatMessage($message, $styles)
{
    if (empty($styles) || !ansiColorsSupported()) {
        return $message;
    }

    return sprintf("\x1b[%sm", implode(';', array_map('getStyleCode', $styles))) . $message . "\x1b[0m";
}
```

配置数据库：

![]({{site.baseurl}}/images/20200417/20200417132738.jpeg)

数据库迁移：

> php yii migrate

![]({{site.baseurl}}/images/20200417/20200417132739.jpeg)

看一下yii文件内容：
```
#!/usr/bin/env php
<?php
/**
 * Yii console bootstrap file.
 */

defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

require __DIR__ . '/vendor/autoload.php';
require __DIR__ . '/vendor/yiisoft/yii2/Yii.php';
require __DIR__ . '/common/config/bootstrap.php';
require __DIR__ . '/console/config/bootstrap.php';

$config = yii\helpers\ArrayHelper::merge(
    require __DIR__ . '/common/config/main.php',
    require __DIR__ . '/common/config/main-local.php',
    require __DIR__ . '/console/config/main.php',
    require __DIR__ . '/console/config/main-local.php'
);

$application = new yii\console\Application($config);
$exitCode = $application->run();
exit($exitCode);
```


<br/><br/><br/><br/><br/>
### 参考资料 

<http://www.weixistyle.com/yii2-video-play.php?chapter=3.1&title=博客系统需求>

