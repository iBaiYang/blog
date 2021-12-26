---
layout: post
categories: Linux
title: Linux PHP安装PostgreSQL的pdo拓展
meta: Linux PHP安装PostgreSQL的pdo拓展
---
* content
{:toc}

## 正文

一次项目开发中，接触到了Laravel8，PHP版本要求为7.4，数据库是PostgreSQL，Laravel和PostgreSQL属于头次接触。
PHP可以通过pdo像操作MySQL一样操作PostgreSQL，不过PHP要安装pdo_pgsql拓展。
服务器的环境他人已经部署好，但你对这个服务器一无所知。下面记录下经历。

项目composer.json文件：
```
{
    "name": "laravel/laravel",
    "type": "project",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "require": {
        "php": "^7.3|^8.0",
        "fruitcake/laravel-cors": "^2.0",
        "guzzlehttp/guzzle": "^7.0.1",
        "laravel/framework": "^8.54",
        "laravel/tinker": "^2.5",
        "maatwebsite/excel": "3.1.*",
        "predis/predis": "^1.1",
        "tymon/jwt-auth": "dev-develop"
    },
    "require-dev": {
        "facade/ignition": "^2.5",
        "fakerphp/faker": "^1.9.1",
        "laravel/sail": "^1.0.1",
        "mockery/mockery": "^1.4.2",
        "nunomaduro/collision": "^5.0",
        "phpunit/phpunit": "^9.3.3"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        },
        "files": [
            "app/Helpers/Helpers.php"
        ]
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    "scripts": {
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover --ansi"
        ],
        "post-update-cmd": [
            "@php artisan vendor:publish --tag=laravel-assets --ansi"
        ],
        "post-root-package-install": [
            "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "@php artisan key:generate --ansi"
        ]
    },
    "extra": {
        "laravel": {
            "dont-discover": []
        }
    },
    "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

### 项目准备

在服务器`/var/www/html`目录下拉取项目文件
> git clone ***

加载vendor包
> composer install

环境配置文件准备：
> cp .env.example .env

把配置修改好。

修改指定目录权限
> chmod 0777 -R ./storage

### 配置Nginx

查看系统居然是Ubuntu：
```
root@localhost:/usr/local/src/# uname -a
Linux localhost 4.4.0-131-generic #157-Ubuntu SMP Thu Jul 12 15:51:36 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# cat /proc/version
Linux version 4.4.0-131-generic (buildd@lgw01-amd64-015) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.10) ) #157-Ubuntu SMP Thu Jul 12 15:51:36 UTC 2018
root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# cat /etc/issue
Ubuntu 16.04.5 LTS \n \l

root@localhost:/usr/local/src/# 
root@localhost:/usr/local/src/# lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.5 LTS
Release:	16.04
Codename:	xenial
root@localhost:/usr/local/src/#
```

简化请求，不需要DNS服务了，直接通过 IP:端口 方式提供服务。Nginx配置监听端口就可以了。

查看哪个端口可用。
> netstat -anpt

查看php-fpm监听信息的方式，在 `/etc/php/7.4/fpm/php-fpm.conf` 配置文件中有：
```
include=/etc/php/7.4/fpm/pool.d/*.conf
```

在 `/etc/php/7.4/fpm/pool.d/www.conf` 中有：
```
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = www-data
group = www-data

; The address on which to accept FastCGI requests.
; Valid syntaxes are:
;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
;                            a specific port;
;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
;                            a specific port;
;   'port'                 - to listen on a TCP socket to all addresses
;                            (IPv6 and IPv4-mapped) on a specific port;
;   '/path/to/unix/socket' - to listen on a unix socket.
; Note: This value is mandatory.
listen = /run/php/php7.4-fpm.sock
```

信息交互方式是socket方式。


查看配置文件位置，在Nginx配置文件`/etc/nginx/nginx.conf` 的http部分中有：
```
        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*.conf;
        include /etc/nginx/sites-enabled/dean/*.conf;
```

我们可以在 `/etc/nginx/conf.d/` 目录下写项目的配置文件`test.conf`：
```
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 8096; 

    server_name 10.1.1.10;
    root        /var/www/html/test/public/;
    index       index.php;

    location / {
        # Redirect everything that isn't a real file to index.php
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        #fastcgi_pass 127.0.0.1:9000;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        try_files $uri =404;
    }

    location ~* /\. {
        deny all;
    }
}
```

重启Nginx
> nginx -s reload

### 安装pgsql拓展

`php -i` 查看phpinfo信息：
```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# php -i
phpinfo()
PHP Version => 7.4.15

System => Linux localhost 4.4.0-131-generic #157-Ubuntu SMP Thu Jul 12 15:51:36 UTC 2018 x86_64
Build Date => Feb 19 2021 15:42:10
Server API => Command Line Interface
Virtual Directory Support => disabled
Configuration File (php.ini) Path => /etc/php/7.4/cli
Loaded Configuration File => /etc/php/7.4/cli/php.ini
Scan this dir for additional .ini files => /etc/php/7.4/cli/conf.d
Additional .ini files parsed => /etc/php/7.4/cli/conf.d/10-mysqlnd.ini,
/etc/php/7.4/cli/conf.d/10-opcache.ini,
/etc/php/7.4/cli/conf.d/10-pdo.ini,
/etc/php/7.4/cli/conf.d/15-xml.ini,
/etc/php/7.4/cli/conf.d/20-calendar.ini,
/etc/php/7.4/cli/conf.d/20-ctype.ini,
/etc/php/7.4/cli/conf.d/20-curl.ini,
/etc/php/7.4/cli/conf.d/20-dom.ini,
/etc/php/7.4/cli/conf.d/20-exif.ini,
/etc/php/7.4/cli/conf.d/20-ffi.ini,
/etc/php/7.4/cli/conf.d/20-fileinfo.ini,
/etc/php/7.4/cli/conf.d/20-ftp.ini,
/etc/php/7.4/cli/conf.d/20-gd.ini,
/etc/php/7.4/cli/conf.d/20-gettext.ini,
/etc/php/7.4/cli/conf.d/20-iconv.ini,
/etc/php/7.4/cli/conf.d/20-json.ini,
/etc/php/7.4/cli/conf.d/20-ldap.ini,
/etc/php/7.4/cli/conf.d/20-mbstring.ini,
/etc/php/7.4/cli/conf.d/20-mongodb.ini,
/etc/php/7.4/cli/conf.d/20-mysqli.ini,
/etc/php/7.4/cli/conf.d/20-pdo_mysql.ini,
/etc/php/7.4/cli/conf.d/20-phar.ini,
/etc/php/7.4/cli/conf.d/20-posix.ini,
/etc/php/7.4/cli/conf.d/20-readline.ini,
/etc/php/7.4/cli/conf.d/20-shmop.ini,
/etc/php/7.4/cli/conf.d/20-simplexml.ini,
/etc/php/7.4/cli/conf.d/20-sockets.ini,
/etc/php/7.4/cli/conf.d/20-sysvmsg.ini,
/etc/php/7.4/cli/conf.d/20-sysvsem.ini,
/etc/php/7.4/cli/conf.d/20-sysvshm.ini,
/etc/php/7.4/cli/conf.d/20-tokenizer.ini,
/etc/php/7.4/cli/conf.d/20-xmlreader.ini,
/etc/php/7.4/cli/conf.d/20-xmlwriter.ini,
/etc/php/7.4/cli/conf.d/20-xsl.ini,
/etc/php/7.4/cli/conf.d/20-zip.ini

PHP API => 20190902
PHP Extension => 20190902
Zend Extension => 320190902
Zend Extension Build => API320190902,NTS
PHP Extension Build => API20190902,NTS
Debug Build => no
Thread Safety => disabled
Zend Signal Handling => enabled
Zend Memory Manager => enabled
Zend Multibyte Support => provided by mbstring
IPv6 Support => enabled
DTrace Support => available, disabled

Registered PHP Streams => https, ftps, compress.zlib, php, file, glob, data, http, ftp, phar, zip
Registered Stream Socket Transports => tcp, udp, unix, udg, ssl, tls, tlsv1.0, tlsv1.1, tlsv1.2, tlsv1.3
Registered Stream Filters => zlib.*, string.rot13, string.toupper, string.tolower, string.strip_tags, convert.*, consumed, dechunk, convert.iconv.*

This program makes use of the Zend Scripting Language Engine:
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.15, Copyright (c), by Zend Technologies


 _______________________________________________________________________


Configuration

calendar

Calendar support => enabled

Core

PHP Version => 7.4.15

Directive => Local Value => Master Value
allow_url_fopen => On => On
allow_url_include => Off => Off
arg_separator.input => & => &
arg_separator.output => & => &
auto_append_file => no value => no value
auto_globals_jit => On => On
auto_prepend_file => no value => no value
browscap => no value => no value
default_charset => UTF-8 => UTF-8
default_mimetype => text/html => text/html
disable_classes => no value => no value
disable_functions => no value => no value
display_errors => Off => Off
display_startup_errors => Off => Off
doc_root => no value => no value
docref_ext => no value => no value
docref_root => no value => no value
enable_dl => Off => Off
enable_post_data_reading => On => On
error_append_string => no value => no value
error_log => no value => no value
error_prepend_string => no value => no value
error_reporting => 22527 => 22527
expose_php => On => On
extension_dir => /usr/lib/php/20190902 => /usr/lib/php/20190902
file_uploads => On => On
hard_timeout => 2 => 2
highlight.comment => <font style="color: #FF8000">#FF8000</font> => <font style="color: #FF8000">#FF8000</font>
highlight.default => <font style="color: #0000BB">#0000BB</font> => <font style="color: #0000BB">#0000BB</font>
highlight.html => <font style="color: #000000">#000000</font> => <font style="color: #000000">#000000</font>
highlight.keyword => <font style="color: #007700">#007700</font> => <font style="color: #007700">#007700</font>
highlight.string => <font style="color: #DD0000">#DD0000</font> => <font style="color: #DD0000">#DD0000</font>
html_errors => Off => Off
ignore_repeated_errors => Off => Off
ignore_repeated_source => Off => Off
ignore_user_abort => Off => Off
implicit_flush => On => On
include_path => .:/usr/share/php => .:/usr/share/php
input_encoding => no value => no value
internal_encoding => no value => no value
log_errors => On => On
log_errors_max_len => 1024 => 1024
mail.add_x_header => Off => Off
mail.force_extra_parameters => no value => no value
mail.log => no value => no value
max_execution_time => 0 => 0
max_file_uploads => 20 => 20
max_input_nesting_level => 64 => 64
max_input_time => -1 => -1
max_input_vars => 1000 => 1000
memory_limit => -1 => -1
open_basedir => no value => no value
output_buffering => 0 => 0
output_encoding => no value => no value
output_handler => no value => no value
post_max_size => 8M => 8M
precision => 14 => 14
realpath_cache_size => 4096K => 4096K
realpath_cache_ttl => 120 => 120
register_argc_argv => On => On
report_memleaks => On => On
report_zend_debug => Off => Off
request_order => GP => GP
sendmail_from => no value => no value
sendmail_path => /usr/sbin/sendmail -t -i => /usr/sbin/sendmail -t -i
serialize_precision => -1 => -1
short_open_tag => Off => Off
SMTP => localhost => localhost
smtp_port => 25 => 25
sys_temp_dir => no value => no value
syslog.facility => LOG_USER => LOG_USER
syslog.filter => no-ctrl => no-ctrl
syslog.ident => php => php
track_errors => Off => Off
unserialize_callback_func => no value => no value
upload_max_filesize => 2M => 2M
upload_tmp_dir => no value => no value
user_dir => no value => no value
user_ini.cache_ttl => 300 => 300
user_ini.filename => .user.ini => .user.ini
variables_order => GPCS => GPCS
xmlrpc_error_number => 0 => 0
xmlrpc_errors => Off => Off
zend.assertions => -1 => -1
zend.detect_unicode => On => On
zend.enable_gc => On => On
zend.exception_ignore_args => On => On
zend.multibyte => Off => Off
zend.script_encoding => no value => no value
zend.signal_check => Off => Off

ctype

ctype functions => enabled

curl

cURL support => enabled
cURL Information => 7.47.0
Age => 3
Features
AsynchDNS => Yes
CharConv => No
Debug => No
GSS-Negotiate => No
IDN => Yes
IPv6 => Yes
krb4 => No
Largefile => Yes
libz => Yes
NTLM => Yes
NTLMWB => Yes
SPNEGO => Yes
SSL => Yes
SSPI => No
TLS-SRP => Yes
HTTP2 => No
GSSAPI => Yes
KERBEROS5 => Yes
UNIX_SOCKETS => Yes
PSL => No
Protocols => dict, file, ftp, ftps, gopher, http, https, imap, imaps, ldap, ldaps, pop3, pop3s, rtmp, rtsp, smb, smbs, smtp, smtps, telnet, tftp
Host => x86_64-pc-linux-gnu
SSL Version => OpenSSL/1.0.2g
ZLib Version => 1.2.8

Directive => Local Value => Master Value
curl.cainfo => no value => no value

date

date/time support => enabled
timelib version => 2018.04
"Olson" Timezone Database Version => 0.system
Timezone Database => internal
Default timezone => Asia/Chongqing

Directive => Local Value => Master Value
date.default_latitude => 31.7667 => 31.7667
date.default_longitude => 35.2333 => 35.2333
date.sunrise_zenith => 90.583333 => 90.583333
date.sunset_zenith => 90.583333 => 90.583333
date.timezone => no value => no value

dom

DOM/XML => enabled
DOM/XML API Version => 20031129
libxml Version => 2.9.10
HTML Support => enabled
XPath Support => enabled
XPointer Support => enabled
Schema Support => enabled
RelaxNG Support => enabled

exif

EXIF Support => enabled
Supported EXIF Version => 0220
Supported filetypes => JPEG, TIFF
Multibyte decoding support using mbstring => enabled
Extended EXIF tag formats => Canon, Casio, Fujifilm, Nikon, Olympus, Samsung, Panasonic, DJI, Sony, Pentax, Minolta, Sigma, Foveon, Kyocera, Ricoh, AGFA, Epson

Directive => Local Value => Master Value
exif.decode_jis_intel => JIS => JIS
exif.decode_jis_motorola => JIS => JIS
exif.decode_unicode_intel => UCS-2LE => UCS-2LE
exif.decode_unicode_motorola => UCS-2BE => UCS-2BE
exif.encode_jis => no value => no value
exif.encode_unicode => ISO-8859-15 => ISO-8859-15

FFI

FFI support => enabled

Directive => Local Value => Master Value
ffi.enable => preload => preload
ffi.preload => no value => no value

fileinfo

fileinfo support => enabled
libmagic => 537

filter

Input Validation and Filtering => enabled

Directive => Local Value => Master Value
filter.default => unsafe_raw => unsafe_raw
filter.default_flags => no value => no value

ftp

FTP support => enabled
FTPS support => enabled

gd

GD Support => enabled
GD headers Version => 2.3.0
GD library Version => 2.1.1
FreeType Support => enabled
FreeType Linkage => with freetype
GIF Read Support => enabled
GIF Create Support => enabled
JPEG Support => enabled
PNG Support => enabled
WBMP Support => enabled
XPM Support => enabled
XBM Support => enabled
WebP Support => enabled
BMP Support => enabled
TGA Read Support => enabled

Directive => Local Value => Master Value
gd.jpeg_ignore_warning => 1 => 1

gettext

GetText Support => enabled

hash

hash support => enabled
Hashing Engines => md2 md4 md5 sha1 sha224 sha256 sha384 sha512/224 sha512/256 sha512 sha3-224 sha3-256 sha3-384 sha3-512 ripemd128 ripemd160 ripemd256 ripemd320 whirlpool tiger128,3 tiger160,3 tiger192,3 tiger128,4 tiger160,4 tiger192,4 snefru snefru256 gost gost-crypto adler32 crc32 crc32b crc32c fnv132 fnv1a32 fnv164 fnv1a64 joaat haval128,3 haval160,3 haval192,3 haval224,3 haval256,3 haval128,4 haval160,4 haval192,4 haval224,4 haval256,4 haval128,5 haval160,5 haval192,5 haval224,5 haval256,5 

MHASH support => Enabled
MHASH API Version => Emulated Support

iconv

iconv support => enabled
iconv implementation => glibc
iconv library version => 2.23

Directive => Local Value => Master Value
iconv.input_encoding => no value => no value
iconv.internal_encoding => no value => no value
iconv.output_encoding => no value => no value

json

json support => enabled

ldap

LDAP Support => enabled
Total Links => 0/unlimited
API Version => 3001
Vendor Name => OpenLDAP
Vendor Version => 20442
SASL Support => Enabled

Directive => Local Value => Master Value
ldap.max_links => Unlimited => Unlimited

libxml

libXML support => active
libXML Compiled Version => 2.9.10
libXML Loaded Version => 20903
libXML streams => enabled

mbstring

Multibyte Support => enabled
Multibyte string engine => libmbfl
HTTP input encoding translation => disabled
libmbfl version => 1.3.2

mbstring extension makes use of "streamable kanji code filter and converter", which is distributed under the GNU Lesser General Public License version 2.1.

Multibyte (japanese) regex support => enabled
Multibyte regex (oniguruma) version => 5.9.6

Directive => Local Value => Master Value
mbstring.detect_order => no value => no value
mbstring.encoding_translation => Off => Off
mbstring.func_overload => 0 => 0
mbstring.http_input => no value => no value
mbstring.http_output => no value => no value
mbstring.http_output_conv_mimetypes => ^(text/|application/xhtml\+xml) => ^(text/|application/xhtml\+xml)
mbstring.internal_encoding => no value => no value
mbstring.language => neutral => neutral
mbstring.regex_retry_limit => 1000000 => 1000000
mbstring.regex_stack_limit => 100000 => 100000
mbstring.strict_detection => Off => Off
mbstring.substitute_character => no value => no value

mongodb

MongoDB support => enabled
MongoDB extension version => 1.6.1
MongoDB extension stability => stable
libbson bundled version => 1.15.2
libmongoc bundled version => 1.15.2
libmongoc SSL => enabled
libmongoc SSL library => OpenSSL
libmongoc crypto => enabled
libmongoc crypto library => libcrypto
libmongoc crypto system profile => disabled
libmongoc SASL => enabled
libmongoc ICU => disabled
libmongoc compression => enabled
libmongoc compression snappy => disabled
libmongoc compression zlib => enabled

Directive => Local Value => Master Value
mongodb.debug => no value => no value

mysqli

MysqlI Support => enabled
Client API library version => mysqlnd 7.4.15
Active Persistent Links => 0
Inactive Persistent Links => 0
Active Links => 0

Directive => Local Value => Master Value
mysqli.allow_local_infile => Off => Off
mysqli.allow_persistent => On => On
mysqli.default_host => no value => no value
mysqli.default_port => 3306 => 3306
mysqli.default_pw => no value => no value
mysqli.default_socket => no value => no value
mysqli.default_user => no value => no value
mysqli.max_links => Unlimited => Unlimited
mysqli.max_persistent => Unlimited => Unlimited
mysqli.reconnect => Off => Off
mysqli.rollback_on_cached_plink => Off => Off

mysqlnd

mysqlnd => enabled
Version => mysqlnd 7.4.15
Compression => supported
core SSL => supported
extended SSL => supported
Command buffer size => 4096
Read buffer size => 32768
Read timeout => 86400
Collecting statistics => Yes
Collecting memory statistics => No
Tracing => n/a
Loaded plugins => mysqlnd,debug_trace,auth_plugin_mysql_native_password,auth_plugin_mysql_clear_password,auth_plugin_caching_sha2_password,auth_plugin_sha256_password
API Extensions => mysqli,pdo_mysql

openssl

OpenSSL support => enabled
OpenSSL Library Version => OpenSSL 1.1.1d  10 Sep 2019
OpenSSL Header Version => OpenSSL 1.1.1j  16 Feb 2021
Openssl default config => /usr/lib/ssl/openssl.cnf

Directive => Local Value => Master Value
openssl.cafile => no value => no value
openssl.capath => no value => no value

pcntl

pcntl support => enabled

pcre

PCRE (Perl Compatible Regular Expressions) Support => enabled
PCRE Library Version => 10.34 2019-11-21
PCRE Unicode Version => 12.1.0
PCRE JIT Support => enabled
PCRE JIT Target => x86 64bit (little endian + unaligned)

Directive => Local Value => Master Value
pcre.backtrack_limit => 1000000 => 1000000
pcre.jit => 1 => 1
pcre.recursion_limit => 100000 => 100000

PDO

PDO support => enabled
PDO drivers => mysql

pdo_mysql

PDO Driver for MySQL => enabled
Client API version => mysqlnd 7.4.15

Directive => Local Value => Master Value
pdo_mysql.default_socket => /var/run/mysqld/mysqld.sock => /var/run/mysqld/mysqld.sock

Phar

Phar: PHP Archive support => enabled
Phar API version => 1.1.1
Phar-based phar archives => enabled
Tar-based phar archives => enabled
ZIP-based phar archives => enabled
gzip compression => enabled
bzip2 compression => disabled (install ext/bz2)
Native OpenSSL support => enabled


Phar based on pear/PHP_Archive, original concept by Davey Shafik.
Phar fully realized by Gregory Beaver and Marcus Boerger.
Portions of tar implementation Copyright (c) 2003-2009 Tim Kientzle.
Directive => Local Value => Master Value
phar.cache_list => no value => no value
phar.readonly => On => On
phar.require_hash => On => On

posix

POSIX support => enabled

readline

Readline Support => enabled
Readline library => EditLine wrapper

Directive => Local Value => Master Value
cli.pager => no value => no value
cli.prompt => \b \>  => \b \> 

Reflection

Reflection => enabled

session

Session Support => enabled
Registered save handlers => files user 
Registered serializer handlers => php_serialize php php_binary 

Directive => Local Value => Master Value
session.auto_start => Off => Off
session.cache_expire => 180 => 180
session.cache_limiter => nocache => nocache
session.cookie_domain => no value => no value
session.cookie_httponly => no value => no value
session.cookie_lifetime => 0 => 0
session.cookie_path => / => /
session.cookie_samesite => no value => no value
session.cookie_secure => 0 => 0
session.gc_divisor => 1000 => 1000
session.gc_maxlifetime => 1440 => 1440
session.gc_probability => 0 => 0
session.lazy_write => On => On
session.name => PHPSESSID => PHPSESSID
session.referer_check => no value => no value
session.save_handler => files => files
session.save_path => /var/lib/php/sessions => /var/lib/php/sessions
session.serialize_handler => php => php
session.sid_bits_per_character => 5 => 5
session.sid_length => 26 => 26
session.upload_progress.cleanup => On => On
session.upload_progress.enabled => On => On
session.upload_progress.freq => 1% => 1%
session.upload_progress.min_freq => 1 => 1
session.upload_progress.name => PHP_SESSION_UPLOAD_PROGRESS => PHP_SESSION_UPLOAD_PROGRESS
session.upload_progress.prefix => upload_progress_ => upload_progress_
session.use_cookies => 1 => 1
session.use_only_cookies => 1 => 1
session.use_strict_mode => 0 => 0
session.use_trans_sid => 0 => 0

shmop

shmop support => enabled

SimpleXML

SimpleXML support => enabled
Schema support => enabled

sockets

Sockets Support => enabled

sodium

sodium support => enabled
libsodium headers version => 1.0.18
libsodium library version => 1.0.18

SPL

SPL support => enabled
Interfaces => OuterIterator, RecursiveIterator, SeekableIterator, SplObserver, SplSubject
Classes => AppendIterator, ArrayIterator, ArrayObject, BadFunctionCallException, BadMethodCallException, CachingIterator, CallbackFilterIterator, DirectoryIterator, DomainException, EmptyIterator, FilesystemIterator, FilterIterator, GlobIterator, InfiniteIterator, InvalidArgumentException, IteratorIterator, LengthException, LimitIterator, LogicException, MultipleIterator, NoRewindIterator, OutOfBoundsException, OutOfRangeException, OverflowException, ParentIterator, RangeException, RecursiveArrayIterator, RecursiveCachingIterator, RecursiveCallbackFilterIterator, RecursiveDirectoryIterator, RecursiveFilterIterator, RecursiveIteratorIterator, RecursiveRegexIterator, RecursiveTreeIterator, RegexIterator, RuntimeException, SplDoublyLinkedList, SplFileInfo, SplFileObject, SplFixedArray, SplHeap, SplMinHeap, SplMaxHeap, SplObjectStorage, SplPriorityQueue, SplQueue, SplStack, SplTempFileObject, UnderflowException, UnexpectedValueException

standard

Dynamic Library Support => enabled
Path to sendmail => /usr/sbin/sendmail -t -i

Directive => Local Value => Master Value
assert.active => 1 => 1
assert.bail => 0 => 0
assert.callback => no value => no value
assert.exception => 0 => 0
assert.quiet_eval => 0 => 0
assert.warning => 1 => 1
auto_detect_line_endings => 0 => 0
default_socket_timeout => 60 => 60
from => no value => no value
session.trans_sid_hosts => no value => no value
session.trans_sid_tags => a=href,area=href,frame=src,form= => a=href,area=href,frame=src,form=
unserialize_max_depth => 4096 => 4096
url_rewriter.hosts => no value => no value
url_rewriter.tags => form= => form=
user_agent => no value => no value

sysvmsg

sysvmsg support => enabled

sysvsem

sysvsem support => enabled

sysvshm

sysvshm support => enabled

tokenizer

Tokenizer Support => enabled

xml

XML Support => active
XML Namespace Support => active
libxml2 Version => 2.9.10

xmlreader

XMLReader => enabled

xmlwriter

XMLWriter => enabled

xsl

XSL => enabled
libxslt Version => 1.1.28
libxslt compiled against libxml Version => 2.9.3
EXSLT => enabled
libexslt Version => 1.1.28

Zend OPcache

Opcode Caching => Disabled
Optimization => Disabled
SHM Cache => Enabled
File Cache => Disabled
Startup Failed => Opcode Caching is disabled for CLI

Directive => Local Value => Master Value
opcache.blacklist_filename => no value => no value
opcache.consistency_checks => 0 => 0
opcache.dups_fix => Off => Off
opcache.enable => On => On
opcache.enable_cli => Off => Off
opcache.enable_file_override => Off => Off
opcache.error_log => no value => no value
opcache.file_cache => no value => no value
opcache.file_cache_consistency_checks => On => On
opcache.file_cache_only => Off => Off
opcache.file_update_protection => 2 => 2
opcache.force_restart_timeout => 180 => 180
opcache.huge_code_pages => Off => Off
opcache.interned_strings_buffer => 8 => 8
opcache.lockfile_path => /tmp => /tmp
opcache.log_verbosity_level => 1 => 1
opcache.max_accelerated_files => 10000 => 10000
opcache.max_file_size => 0 => 0
opcache.max_wasted_percentage => 5 => 5
opcache.memory_consumption => 128 => 128
opcache.opt_debug_level => 0 => 0
opcache.optimization_level => 0x7FFEBFFF => 0x7FFEBFFF
opcache.preferred_memory_model => no value => no value
opcache.preload => no value => no value
opcache.preload_user => no value => no value
opcache.protect_memory => Off => Off
opcache.restrict_api => no value => no value
opcache.revalidate_freq => 2 => 2
opcache.revalidate_path => Off => Off
opcache.save_comments => On => On
opcache.use_cwd => On => On
opcache.validate_permission => Off => Off
opcache.validate_root => Off => Off
opcache.validate_timestamps => On => On

zip

Zip => enabled
Zip version => 1.15.6
Libzip headers version => 1.7.3
Libzip library version => 1.5.1

zlib

ZLib Support => enabled
Stream Wrapper => compress.zlib://
Stream Filter => zlib.inflate, zlib.deflate
Compiled Version => 1.2.8
Linked Version => 1.2.8

Directive => Local Value => Master Value
zlib.output_compression => Off => Off
zlib.output_compression_level => -1 => -1
zlib.output_handler => no value => no value

Additional Modules

Module Name

Environment

Variable => Value
LS_COLORS => rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LESSCLOSE => /usr/bin/lesspipe %s %s
LANG => en_US.UTF-8
SUDO_GID => 1000
DISPLAY => localhost:11.0
USERNAME => root
SUDO_COMMAND => /bin/su
USER => root
PWD => /usr/local/src/PDO_PGSQL-1.0.2
HOME => /root
SUDO_USER => user
SUDO_UID => 1000
MAIL => /var/mail/root
SHELL => /bin/bash
TERM => xterm
SHLVL => 1
LOGNAME => root
PATH => /data1/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
LESSOPEN => | /usr/bin/lesspipe %s
_ => /usr/bin/php
OLDPWD => /usr/local/src

PHP Variables

Variable => Value
$_SERVER['LS_COLORS'] => rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
$_SERVER['LESSCLOSE'] => /usr/bin/lesspipe %s %s
$_SERVER['LANG'] => en_US.UTF-8
$_SERVER['SUDO_GID'] => 1000
$_SERVER['DISPLAY'] => localhost:11.0
$_SERVER['USERNAME'] => root
$_SERVER['SUDO_COMMAND'] => /bin/su
$_SERVER['USER'] => root
$_SERVER['PWD'] => /usr/local/src/PDO_PGSQL-1.0.2
$_SERVER['HOME'] => /root
$_SERVER['SUDO_USER'] => user
$_SERVER['SUDO_UID'] => 1000
$_SERVER['MAIL'] => /var/mail/root
$_SERVER['SHELL'] => /bin/bash
$_SERVER['TERM'] => xterm
$_SERVER['SHLVL'] => 1
$_SERVER['LOGNAME'] => root
$_SERVER['PATH'] => /data1/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
$_SERVER['LESSOPEN'] => | /usr/bin/lesspipe %s
$_SERVER['_'] => /usr/bin/php
$_SERVER['OLDPWD'] => /usr/local/src
$_SERVER['PHP_SELF'] => 
$_SERVER['SCRIPT_NAME'] => 
$_SERVER['SCRIPT_FILENAME'] => 
$_SERVER['PATH_TRANSLATED'] => 
$_SERVER['DOCUMENT_ROOT'] => 
$_SERVER['REQUEST_TIME_FLOAT'] => 1634814079.3904
$_SERVER['REQUEST_TIME'] => 1634814079
$_SERVER['argv'] => Array
(
)

$_SERVER['argc'] => 0

PHP License
This program is free software; you can redistribute it and/or modify
it under the terms of the PHP License as published by the PHP Group
and included in the distribution in the file:  LICENSE

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

If you did not receive a copy of the PHP license, or have any
questions about PHP licensing, please contact license@php.net.
```

里面有拓展的安装位置：
```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# php -i | grep extension_dir
extension_dir => /usr/lib/php/20190902 => /usr/lib/php/20190902
```

可以到`/usr/lib/php/20190902`目录下看下安装好的拓展有：
```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# ls -l /usr/lib/php/20190902
total 10448
-rw-r--r-- 1 root root   30824 Feb 20  2021 calendar.so
-rw-r--r-- 1 root root   14440 Feb 20  2021 ctype.so
-rw-r--r-- 1 root root   88168 Feb 20  2021 curl.so
-rw-r--r-- 1 root root  153704 Feb 20  2021 dom.so
-rw-r--r-- 1 root root   88168 Feb 20  2021 exif.so
-rw-r--r-- 1 root root  153704 Feb 20  2021 ffi.so
-rw-r--r-- 1 root root 5740656 Feb 20  2021 fileinfo.so
-rw-r--r-- 1 root root   59496 Feb 20  2021 ftp.so
-rw-r--r-- 1 root root  104552 Feb 20  2021 gd.so
-rw-r--r-- 1 root root   14440 Feb 20  2021 gettext.so
-rw-r--r-- 1 root root   43112 Feb 20  2021 iconv.so
-rw-r--r-- 1 root root   43112 Feb 20  2021 json.so
-rw-r--r-- 1 root root   88168 Feb 20  2021 ldap.so
-rw-r--r-- 1 root root 1045344 Feb 20  2021 mbstring.so
-rw-r--r-- 1 root root 1126624 Dec 19  2019 mongodb.so
-rw-r--r-- 1 root root  133224 Feb 20  2021 mysqli.so
-rw-r--r-- 1 root root  255832 Feb 20  2021 mysqlnd.so
-rw-r--r-- 1 root root  543248 Feb 20  2021 opcache.so
-rw-r--r-- 1 root root   30824 Feb 20  2021 pdo_mysql.so
-rw-r--r-- 1 root root  108648 Feb 20  2021 pdo.so
-rw-r--r-- 1 root root  264296 Feb 20  2021 phar.so
-rw-r--r-- 1 root root   34920 Feb 20  2021 posix.so
-rw-r--r-- 1 root root   30824 Feb 20  2021 readline.so
-rw-r--r-- 1 root root   14440 Feb 20  2021 shmop.so
-rw-r--r-- 1 root root   51304 Feb 20  2021 simplexml.so
-rw-r--r-- 1 root root   88168 Feb 20  2021 sockets.so
-rw-r--r-- 1 root root   18536 Feb 20  2021 sysvmsg.so
-rw-r--r-- 1 root root   10344 Feb 20  2021 sysvsem.so
-rw-r--r-- 1 root root   14440 Feb 20  2021 sysvshm.so
-rw-r--r-- 1 root root   18536 Feb 20  2021 tokenizer.so
-rw-r--r-- 1 root root   30824 Feb 20  2021 xmlreader.so
-rw-r--r-- 1 root root   47208 Feb 20  2021 xml.so
-rw-r--r-- 1 root root   43112 Feb 20  2021 xmlwriter.so
-rw-r--r-- 1 root root   30824 Feb 20  2021 xsl.so
-rw-r--r-- 1 root root   63592 Feb 20  2021 zip.so
```

#### 源码包方式

> cd /usr/local/src
> 
> wget http://pecl.php.net/get/PDO_PGSQL-1.0.2.tgz
> 
> gunzip PDO_PGSQL-1.0.2.tgz
> 
> tar -xf PDO_PGSQL-1.0.2.tar
> 
> cd PDO_PGSQL-1.0.2


```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis phpize
phpize: /usr/bin/phpize /usr/bin/phpize7.1 /usr/share/man/man1/phpize.1.gz
```

```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis php-config
php-config: /usr/bin/php-config7.1 /usr/bin/php-config /usr/share/man/man1/php-config.1.gz
```

```
> /usr/bin/phpize
> 
> ./configure --with-php-config=/usr/bin/php-config
>
> make && make install
```

查找php配置文件地址：
> whereis php.ini

或：
> find / -name php.ini

如：
```
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# whereis php.ini
php: /usr/bin/php7.4 /usr/bin/php /usr/bin/php7.1 /usr/lib/php /etc/php /usr/include/php /usr/share/php7.1-opcache 
/usr/share/php7.1-mysql /usr/share/php7.4-gd /usr/share/php7.4-mbstring /usr/share/php7.4-xml /usr/share/php7.4-curl 
/usr/share/php7.4-ldap /usr/share/php7.1-common /usr/share/php7.1-gd /usr/share/php7.1-xml /usr/share/php7.1-curl 
/usr/share/php7.1-imap /usr/share/php7.4-readline /usr/share/php7.1-readline /usr/share/php7.4-zip 
/usr/share/php7.1-mbstring /usr/share/php7.1-zip /usr/share/php7.1-xmlrpc /usr/share/php7.1-pgsql 
/usr/share/php7.1-json /usr/share/php7.4-opcache /usr/share/php7.4-mysql /usr/share/php7.4-common 
/usr/share/php7.1-gmp /usr/share/php /usr/share/php7.4-json /usr/share/man/man1/php.1.gz
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# 
root@localhost:/usr/local/src/PDO_PGSQL-1.0.2# find / -name php.ini
/etc/php/7.1/cli/php.ini
/etc/php/7.1/fpm/php.ini
/etc/php/7.1/apache2/php.ini
/etc/php/7.4/cli/php.ini
/etc/php/7.4/fpm/php.ini
/etc/php/7.4/apache2/php.ini
```

编辑
> vim /etc/php/7.4/cli/php.ini

文件最后写入：
```
extension=pgsql.so
extension=pdo_pgsql.so
```

查看拓展是否安装成功：
> php -m

这里只是给cli添加了拓展，fpm还没有，
添加方式可以参考 [Win10家庭版WSL2安装Centos7.8](ttps://ibaiyang.github.io/blog/linux/2021/08/28/Win10家庭版WSL2安装Centos7.8.html) 中 Swoole 拓展的安装。

#### apt方式

添加apt源，在Ubuntu下安装软件的时候，一般都直接使用 PPA 源安装方式：
> sudo add-apt-repository ppa:ondrej/php

PPA 全称为 Personal Package Archives（个人软件包档案），是 Ubuntu Launchpad 网站提供的一项服务，当然不仅限于 Launchpad 。
它允许个人用户上传软件源代码，通过 Launchpad 进行编译并发布为二进制软件包，作为 apt/新立得源供其他用户下载和更新。
在Launchpad网站上的每一个用户和团队都可以拥有一个或多个PPA。

通常 PPA 源里的软件是官方源里没有的，或者是最新版本的软件。相对于通过 Deb 包安装来说，使用 PPA 的好处是，
一旦软件有更新，通过 sudo apt-get upgrade 这样命令就可以直接升级到新版本。

> sudo apt-get update

上面会有大段输出，我们选择后台运行的方式：
> sudo apt-get update 1>/dev/null 2>&1 &

安装pdo_pgsql拓展：
> sudo apt-get install php7.4-pdo_pgsql

### Docker实现

一个陌生的服务器环境，而且软件管理的很混乱，可以通过Docker容器实现一个新的纯净的环境。



<br/><br/><br/><br/><br/>
## 参考资料

add-apt-repository添加源 <https://blog.csdn.net/u012901451/article/details/16923855>

10 分钟上手 Vim，常用命令大盘点 <https://zhuanlan.zhihu.com/p/89042423>

Php7安装pdo_pgsql，pgsql扩展 <https://www.cnblogs.com/feixiablog/p/9249347.html>

PECL PDO_PGSQL <http://pecl.php.net/package/PDO_PGSQL>

tgz文件如何解压 <https://jingyan.baidu.com/article/0aa22375610d1088cd0d646b.html>



php7 安装pg_sql, pdo_pgsql扩展 <https://www.clarkhu.net/?p=9929>

deepin docker 容器安装php7.2扩展报错： Couldn't find any package by glob 'php7.2-*' <https://blog.csdn.net/one312/article/details/104949126>


