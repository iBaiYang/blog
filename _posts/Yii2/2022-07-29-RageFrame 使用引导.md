---
layout: post
categories: Yii2
title: RageFrame 使用引导
meta: RageFrame 使用引导
---
* content
{:toc}

## 正文

### 克隆

> git clone https://github.com/jianyan74/rageframe2.git

### 安装依赖

> cd rageframe2
> 
> composer install

```
root@02891538d8c9:/var/www/html/rageframe2# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Package operations: 184 installs, 0 updates, 0 removals
  - Downloading easywechat-composer/easywechat-composer (1.4.0)
  - Downloading khanamiryan/qrcode-detector-decoder (1.0.3)
  - Downloading bacon/bacon-qr-code (1.0.3)
  - Downloading 2amigos/qrcode-library (1.1.2)
  - Downloading bower-asset/jquery (3.5.1)
  - Downloading yidas/yii2-bower-asset (2.0.13.1)
  - Downloading bower-asset/bootstrap (v3.4.1)
  - Downloading bower-asset/punycode (v1.3.2)
  - Downloading league/mime-type-detection (1.5.1)
  - Downloading league/flysystem (1.1.3)
  - Downloading psr/log (1.1.3)
  - Downloading cache/tag-interop (1.0.0)
  - Downloading cache/adapter-common (1.1.0)
  - Downloading cache/filesystem-adapter (1.0.0)
  - Downloading symfony/polyfill-ctype (v1.20.0)
  - Downloading symfony/yaml (v4.4.15)
  - Downloading symfony/finder (v4.4.15)
  - Downloading symfony/event-dispatcher-contracts (v1.1.9)
  - Downloading symfony/event-dispatcher (v4.4.15)
  - Downloading symfony/polyfill-mbstring (v1.20.0)
  - Downloading symfony/dom-crawler (v4.4.15)
  - Downloading symfony/css-selector (v4.4.15)
  - Downloading psr/container (1.0.0)
  - Downloading symfony/service-contracts (v2.2.0)
  - Downloading symfony/polyfill-php80 (v1.20.0)
  - Downloading symfony/polyfill-php73 (v1.20.0)
  - Downloading symfony/console (v4.4.15)
  - Downloading symfony/browser-kit (v4.4.15)
  - Downloading guzzlehttp/psr7 (1.7.0)
  - Downloading sebastian/diff (2.0.1)
  - Downloading sebastian/recursion-context (3.0.0)
  - Downloading sebastian/exporter (3.1.2)
  - Downloading sebastian/comparator (2.1.3)
  - Downloading sebastian/resource-operations (1.0.0)
  - Downloading sebastian/object-reflector (1.1.1)
  - Downloading sebastian/object-enumerator (3.0.3)
  - Downloading sebastian/global-state (2.0.0)
  - Downloading sebastian/environment (3.1.0)
  - Downloading doctrine/instantiator (1.3.1)
  - Downloading phpunit/phpunit-mock-objects (5.0.10)
  - Downloading phpunit/php-timer (1.0.9)
  - Downloading phpunit/php-file-iterator (1.4.5)
  - Downloading theseer/tokenizer (1.2.0)
  - Downloading sebastian/code-unit-reverse-lookup (1.0.1)
  - Downloading phpunit/php-token-stream (2.0.2)
  - Downloading phpunit/php-code-coverage (5.3.2)
  - Downloading webmozart/assert (1.9.1)
  - Downloading phpdocumentor/type-resolver (1.4.0)
  - Downloading phpdocumentor/reflection-docblock (5.2.2)
  - Downloading phpspec/prophecy (v1.10.3)
  - Downloading phar-io/version (1.0.1)
  - Downloading phar-io/manifest (1.0.1)
  - Downloading myclabs/deep-copy (1.10.1)
  - Downloading phpunit/phpunit (6.5.14)
  - Downloading codeception/phpunit-wrapper (6.8.2)
  - Downloading codeception/stub (2.1.0)
  - Downloading behat/gherkin (v4.6.2)
  - Downloading codeception/base (2.5.6)
  - Downloading codeception/verify (1.0.0)
  - Downloading php-curl-class/php-curl-class (8.8.0)
  - Downloading monitor/bot-monitor (v1.4.0)
  - Downloading dueros/bot-sdk (v2.2.0)
  - Downloading symfony/polyfill-php72 (v1.20.0)
  - Downloading symfony/polyfill-intl-normalizer (v1.20.0)
  - Downloading symfony/polyfill-intl-idn (v1.20.0)
  - Downloading doctrine/lexer (1.2.1)
  - Downloading egulias/email-validator (2.1.22)
  - Downloading ezyang/htmlpurifier (v4.13.0)
  - Downloading symfony/var-exporter (v5.1.7)
  - Downloading symfony/cache-contracts (v2.2.0)
  - Downloading symfony/cache (v4.4.15)
  - Downloading guzzlehttp/promises (1.4.0)
  - Downloading guzzlehttp/guzzle (6.5.5)
  - Downloading fastgoo/getui-php (1.0)
  - Downloading finecho/logistics (3.0.0)
  - Downloading firebase/php-jwt (v5.2.0)
  - Downloading griffinledingham/php-apple-signin (1.1)
  - Downloading guzzlehttp/command (1.0.0)
  - Downloading hashids/hashids (3.0.0)
  - Downloading symfony/var-dumper (v5.1.7)
  - Downloading tightenco/collect (v8.0.4)
  - Downloading jaeger/phpquery-single (1.0.1)
  - Downloading jaeger/g-http (V1.7.1)
  - Downloading jaeger/querylist (V4.2.6)
  - Downloading jianyan74/yii2-console-migration (1.0.1)
  - Downloading yiisoft/yii2 (2.0.38)
  - Downloading symfony/mime (v5.1.7)
  - Downloading symfony/http-foundation (v4.4.15)
  - Downloading symfony/psr-http-message-bridge (v2.0.2)
  - Downloading pimple/pimple (v3.3.0)
  - Downloading overtrue/socialite (2.0.21)
  - Downloading monolog/monolog (1.25.5)
  - Downloading overtrue/wechat (4.2.35)
  - Downloading jianyan74/yii2-easy-wechat (1.1.3)
  - Downloading jianyan74/yii2-treegrid (1.0.1)
  - Downloading jpush/jpush (v3.6.6)
  - Downloading kartik-v/yii2-krajee-base (v2.0.5)
  - Downloading kartik-v/yii2-date-range (v1.7.2)
  - Downloading kartik-v/yii2-widget-colorinput (v1.0.6)
  - Downloading kartik-v/yii2-widget-datepicker (dev-master fada92b)
  - Downloading kartik-v/yii2-widget-datetimepicker (dev-master f64114b)
  - Downloading select2/select2 (4.0.13)
  - Downloading kartik-v/yii2-widget-select2 (dev-master dbfe450)
  - Downloading kartik-v/yii2-widget-timepicker (v1.0.4)
  - Downloading league/event (2.2.0)
  - Downloading lcobucci/jwt (3.3.3)
  - Downloading paragonie/random_compat (v9.99.99)
  - Downloading defuse/php-encryption (v2.2.1)
  - Downloading league/oauth2-server (7.4.0)
  - Downloading php-http/promise (1.1.0)
  - Downloading php-http/httplug (2.2.0)
  - Downloading php-http/guzzle6-adapter (v2.0.1)
  - Downloading php-http/message-factory (v1.0.2)
  - Downloading clue/stream-filter (v1.5.0)
  - Downloading php-http/message (1.9.1)
  - Downloading php-http/discovery (1.12.0)
  - Downloading moneyphp/money (v3.3.1)
  - Downloading omnipay/common (v3.0.4)
  - Downloading linslin/yii2-curl (1.4.0)
  - Downloading phpseclib/phpseclib (2.0.29)
  - Downloading phpseclib/bcmath_compat (1.0.5)
  - Downloading lokielse/omnipay-alipay (v3.1.2)
  - Downloading lokielse/omnipay-unionpay (v3.0.3)
  - Downloading lokielse/omnipay-wechatpay (v3.0.11)
  - Downloading overtrue/http (1.2.0)
  - Downloading mingyoung/dingtalk (2.0.0)
  - Downloading mobiledetect/mobiledetectlib (2.8.34)
  - Downloading moontoast/math (1.2.1)
  - Downloading myclabs/php-enum (1.7.6)
  - Downloading symfony/process (v4.4.15)
  - Downloading dragonmantank/cron-expression (v1.2.1)
  - Downloading omnilight/yii2-scheduling (1.1.4)
  - Downloading omnipay/stripe (v3.1.0)
  - Downloading overtrue/easy-sms (1.1.24)
  - Downloading guzzlehttp/guzzle-services (1.1.3)
  - Downloading qcloud/cos-sdk-v5 (v2.1.1)
  - Downloading overtrue/flysystem-cos (2.1.2)
  - Downloading qiniu/php-sdk (v7.3.0)
  - Downloading overtrue/flysystem-qiniu (1.0.5)
  - Downloading overtrue/pinyin (3.0.6)
  - Downloading markbaker/matrix (2.0.0)
  - Downloading markbaker/complex (2.0.0)
  - Downloading maennchen/zipstream-php (2.1.0)
  - Downloading phpoffice/phpspreadsheet (1.15.0)
  - Downloading ramsey/uuid (3.9.3)
  - Downloading swoole/ide-helper (dev-master aefd9d1)
  - Downloading symfony/polyfill-iconv (v1.20.0)
  - Downloading tencentcloud/tencentcloud-sdk-php (3.0.94)
  - Downloading unclead/yii2-multiple-input (2.22.0)
  - Downloading yiisoft/yii2-httpclient (2.0.12)
  - Downloading yiisoft/yii2-authclient (2.1.8)
  - Downloading xj/yii2-oauth (2.0.4)
  - Downloading aliyuncs/oss-sdk-php (v2.4.1)
  - Downloading xxtime/flysystem-aliyun-oss (1.5.0)
  - Downloading yiisoft/yii2-bootstrap (2.0.10)
  - Downloading yiisoft/yii2-debug (2.0.14)
  - Downloading yiisoft/yii2-elasticsearch (dev-master 14dbc0d)
  - Downloading fzaninotto/faker (v1.9.1)
  - Downloading yiisoft/yii2-faker (2.0.4)
  - Downloading bower-asset/typeahead.js (v0.11.1)
  - Downloading yiisoft/yii2-gii (2.0.8)
  - Downloading imagine/imagine (v0.5.0)
  - Downloading yiisoft/yii2-imagine (2.0.4)
  - Downloading yiisoft/yii2-queue (dev-master 8d043ef)
  - Downloading yiisoft/yii2-redis (2.0.13)
  - Downloading swiftmailer/swiftmailer (v6.2.3)
  - Downloading yiisoft/yii2-swiftmailer (2.1.2)
  - Downloading yly-openapi/yly-openapi-sdk (1.0.1)
  - Downloading zhuzhichao/ip-location-zh (v2.4.4)
 103/169 [=================>----------]  60%
```

发现CPU占用过高，win10会自动控制进程，导致虚拟机异常。
打开任务管理器，杀死虚拟机进程后，重新加载，多尝试几次。

```
root@02891538d8c9:/var/www/html/rageframe2# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Package operations: 184 installs, 0 updates, 0 removals
  - Downloading 2amigos/qrcode-library (1.1.2)
  - Downloading bower-asset/jquery (3.5.1)
  - Downloading yidas/yii2-bower-asset (2.0.13.1)
  - Downloading bower-asset/bootstrap (v3.4.1)
  - Downloading bower-asset/punycode (v1.3.2)
  - Downloading symfony/console (v4.4.15)
  - Downloading sebastian/exporter (3.1.2)
  - Downloading sebastian/comparator (2.1.3)
  - Downloading sebastian/object-reflector (1.1.1)
  - Downloading sebastian/object-enumerator (3.0.3)
  - Downloading sebastian/global-state (2.0.0)
  - Downloading sebastian/environment (3.1.0)
  - Downloading doctrine/instantiator (1.3.1)
  - Downloading phpunit/phpunit-mock-objects (5.0.10)
  - Downloading phpunit/php-timer (1.0.9)
  - Downloading phpunit/php-file-iterator (1.4.5)
  - Downloading theseer/tokenizer (1.2.0)
  - Downloading sebastian/code-unit-reverse-lookup (1.0.1)
  - Downloading phpunit/php-token-stream (2.0.2)
  - Downloading phpunit/php-code-coverage (5.3.2)
  - Downloading webmozart/assert (1.9.1)
  - Downloading phpdocumentor/type-resolver (1.4.0)
  - Downloading phpdocumentor/reflection-docblock (5.2.2)
  - Downloading phpspec/prophecy (v1.10.3)
  - Downloading phar-io/version (1.0.1)
  - Downloading phar-io/manifest (1.0.1)
  - Downloading myclabs/deep-copy (1.10.1)
  - Downloading phpunit/phpunit (6.5.14)
  - Downloading codeception/phpunit-wrapper (6.8.2)
  - Downloading codeception/stub (2.1.0)
  - Downloading behat/gherkin (v4.6.2)
  - Downloading codeception/base (2.5.6)
  - Downloading codeception/verify (1.0.0)
  - Downloading php-curl-class/php-curl-class (8.8.0)
  - Downloading monitor/bot-monitor (v1.4.0)
  - Downloading dueros/bot-sdk (v2.2.0)
  - Downloading symfony/polyfill-php72 (v1.20.0)
  - Downloading symfony/polyfill-intl-normalizer (v1.20.0)
  - Downloading symfony/polyfill-intl-idn (v1.20.0)
  - Downloading doctrine/lexer (1.2.1)
  - Downloading egulias/email-validator (2.1.22)
  - Downloading ezyang/htmlpurifier (v4.13.0)
  - Downloading symfony/var-exporter (v5.1.7)
  - Downloading symfony/cache-contracts (v2.2.0)
  - Downloading symfony/cache (v4.4.15)
  - Downloading guzzlehttp/promises (1.4.0)
  - Downloading guzzlehttp/guzzle (6.5.5)
  - Downloading fastgoo/getui-php (1.0)
  - Downloading finecho/logistics (3.0.0)
  - Downloading firebase/php-jwt (v5.2.0)
  - Downloading griffinledingham/php-apple-signin (1.1)
  - Downloading guzzlehttp/command (1.0.0)
  - Downloading hashids/hashids (3.0.0)
  - Downloading symfony/var-dumper (v5.1.7)
  - Downloading tightenco/collect (v8.0.4)
  - Downloading jaeger/phpquery-single (1.0.1)
  - Downloading jaeger/g-http (V1.7.1)
  - Downloading jaeger/querylist (V4.2.6)
  - Downloading jianyan74/yii2-console-migration (1.0.1)
  - Downloading yiisoft/yii2 (2.0.38)
  - Downloading symfony/mime (v5.1.7)
  - Downloading symfony/http-foundation (v4.4.15)
  - Downloading symfony/psr-http-message-bridge (v2.0.2)
  - Downloading pimple/pimple (v3.3.0)
  - Downloading overtrue/socialite (2.0.21)
  - Downloading monolog/monolog (1.25.5)
  - Downloading overtrue/wechat (4.2.35)
  - Downloading jianyan74/yii2-easy-wechat (1.1.3)
  - Downloading jianyan74/yii2-treegrid (1.0.1)
  - Downloading jpush/jpush (v3.6.6)
  - Downloading kartik-v/yii2-krajee-base (v2.0.5)
  - Downloading kartik-v/yii2-date-range (v1.7.2)
  - Downloading kartik-v/yii2-widget-colorinput (v1.0.6)
  - Downloading kartik-v/yii2-widget-datepicker (dev-master fada92b)
  - Downloading kartik-v/yii2-widget-datetimepicker (dev-master f64114b)
  - Downloading select2/select2 (4.0.13)
  - Downloading kartik-v/yii2-widget-select2 (dev-master dbfe450)
  - Downloading kartik-v/yii2-widget-timepicker (v1.0.4)
  - Downloading league/event (2.2.0)
  - Downloading lcobucci/jwt (3.3.3)
  - Downloading paragonie/random_compat (v9.99.99)
  - Downloading defuse/php-encryption (v2.2.1)
  - Downloading league/oauth2-server (7.4.0)
  - Downloading php-http/promise (1.1.0)
  - Downloading php-http/httplug (2.2.0)
  - Downloading php-http/guzzle6-adapter (v2.0.1)
  - Downloading php-http/message-factory (v1.0.2)
  - Downloading clue/stream-filter (v1.5.0)
  - Downloading php-http/message (1.9.1)
  - Downloading php-http/discovery (1.12.0)
  - Downloading moneyphp/money (v3.3.1)
  - Downloading omnipay/common (v3.0.4)
  - Downloading linslin/yii2-curl (1.4.0)
  - Downloading phpseclib/phpseclib (2.0.29)
  - Downloading phpseclib/bcmath_compat (1.0.5)
  - Downloading lokielse/omnipay-alipay (v3.1.2)
  - Downloading lokielse/omnipay-unionpay (v3.0.3)
  - Downloading lokielse/omnipay-wechatpay (v3.0.11)
  - Downloading overtrue/http (1.2.0)
  - Downloading mingyoung/dingtalk (2.0.0)
  - Downloading mobiledetect/mobiledetectlib (2.8.34)
  - Downloading moontoast/math (1.2.1)
  - Downloading myclabs/php-enum (1.7.6)
  - Downloading symfony/process (v4.4.15)
  - Downloading dragonmantank/cron-expression (v1.2.1)
  - Downloading omnilight/yii2-scheduling (1.1.4)
  - Downloading omnipay/stripe (v3.1.0)
  - Downloading overtrue/easy-sms (1.1.24)
  - Downloading guzzlehttp/guzzle-services (1.1.3)
  - Downloading qcloud/cos-sdk-v5 (v2.1.1)
  - Downloading overtrue/flysystem-cos (2.1.2)
  - Downloading qiniu/php-sdk (v7.3.0)
  - Downloading overtrue/flysystem-qiniu (1.0.5)
  - Downloading overtrue/pinyin (3.0.6)
  - Downloading markbaker/matrix (2.0.0)
  - Downloading markbaker/complex (2.0.0)
  - Downloading maennchen/zipstream-php (2.1.0)
  - Downloading phpoffice/phpspreadsheet (1.15.0)
  - Downloading ramsey/uuid (3.9.3)
  - Downloading swoole/ide-helper (dev-master aefd9d1)
  - Downloading symfony/polyfill-iconv (v1.20.0)
  - Downloading tencentcloud/tencentcloud-sdk-php (3.0.94)
  - Downloading unclead/yii2-multiple-input (2.22.0)
  - Downloading yiisoft/yii2-httpclient (2.0.12)
  - Downloading yiisoft/yii2-authclient (2.1.8)
  - Downloading xj/yii2-oauth (2.0.4)
  - Downloading aliyuncs/oss-sdk-php (v2.4.1)
  - Downloading xxtime/flysystem-aliyun-oss (1.5.0)
  - Downloading yiisoft/yii2-bootstrap (2.0.10)
  - Downloading yiisoft/yii2-debug (2.0.14)
  - Downloading yiisoft/yii2-elasticsearch (dev-master 14dbc0d)
  - Downloading fzaninotto/faker (v1.9.1)
  - Downloading yiisoft/yii2-faker (2.0.4)
  - Downloading bower-asset/typeahead.js (v0.11.1)
  - Downloading yiisoft/yii2-gii (2.0.8)
  - Downloading imagine/imagine (v0.5.0)
  - Downloading yiisoft/yii2-imagine (2.0.4)
  - Downloading yiisoft/yii2-queue (dev-master 8d043ef)
  - Downloading yiisoft/yii2-redis (2.0.13)
  - Downloading swiftmailer/swiftmailer (v6.2.3)
  - Downloading yiisoft/yii2-swiftmailer (2.1.2)
  - Downloading yly-openapi/yly-openapi-sdk (1.0.1)
  - Downloading zhuzhichao/ip-location-zh (v2.4.4)
  - Installing yiisoft/yii2-composer (2.0.10): Extracting archive
yiisoft/yii2-composer contains a Composer plugin which is currently not in your allow-plugins config. See https://getcomposer.org/allow-plugins
Do you trust "yiisoft/yii2-composer" to execute code and wish to enable it now? (writes "allow-plugins" to composer.json) [y,n,d,?] y
  - Installing easywechat-composer/easywechat-composer (1.4.0): Extracting archive
    Failed to extract easywechat-composer/easywechat-composer: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2' -d '/var/www/html/rageframe2/vendor/composer/cd7624d4'

[/var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2 or
        /var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd887b2.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of easywechat-composer/easywechat-composer failed

In ZipDownloader.php line 209:

  '/var/www/html/rageframe2/vendor/composer/tmp-1631c7bad7e60f8b8ed308201dd88
  7b2' is a corrupted zip archive (0 bytes), try again.


install [--prefer-source] [--prefer-dist] [--prefer-install PREFER-INSTALL] [--dry-run] [--dev] [--no-suggest] [--no-dev] [--no-autoloader] [--no-progress] [--no-install] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [-a|--classmap-authoritative] [--apcu-autoloader] [--apcu-autoloader-prefix APCU-AUTOLOADER-PREFIX] [--ignore-platform-req IGNORE-PLATFORM-REQ] [--ignore-platform-reqs] [--] [<packages>...]

root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Package operations: 183 installs, 0 updates, 0 removals
  - Downloading easywechat-composer/easywechat-composer (1.4.0)
  - Installing easywechat-composer/easywechat-composer (1.4.0): Extracting archive
easywechat-composer/easywechat-composer contains a Composer plugin which is currently not in your allow-plugins config. See https://getcomposer.org/allow-plugins
Do you trust "easywechat-composer/easywechat-composer" to execute code and wish to enable it now? (writes "allow-plugins" to composer.json) [y,n,d,?] y
  - Installing khanamiryan/qrcode-detector-decoder (1.0.3): Extracting archive
  - Installing bacon/bacon-qr-code (1.0.3): Extracting archive
  - Installing 2amigos/qrcode-library (1.1.2): Extracting archive
  - Installing bower-asset/jquery (3.5.1): Extracting archive
  - Installing yidas/yii2-bower-asset (2.0.13.1): Extracting archive
  - Installing bower-asset/bootstrap (v3.4.1): Extracting archive
  - Installing bower-asset/inputmask (3.3.11): Extracting archive
  - Installing bower-asset/punycode (v1.3.2): Extracting archive
  - Installing bower-asset/yii2-pjax (2.0.7.1): Extracting archive
  - Installing psr/simple-cache (1.0.1): Extracting archive
  - Installing psr/cache (1.0.1): Extracting archive
  - Installing league/mime-type-detection (1.5.1): Extracting archive
  - Installing league/flysystem (1.1.3): Extracting archive
  - Installing psr/log (1.1.3): Extracting archive
  - Installing cache/tag-interop (1.0.0): Extracting archive
  - Installing cache/adapter-common (1.1.0): Extracting archive
  - Installing cache/filesystem-adapter (1.0.0): Extracting archive
  - Installing cebe/markdown (1.2.1): Extracting archive
  - Installing symfony/polyfill-ctype (v1.20.0): Extracting archive
  - Installing symfony/yaml (v4.4.15): Extracting archive
  - Installing symfony/finder (v4.4.15): Extracting archive
  - Installing symfony/event-dispatcher-contracts (v1.1.9): Extracting archive
  - Installing symfony/event-dispatcher (v4.4.15): Extracting archive
  - Installing symfony/polyfill-mbstring (v1.20.0): Extracting archive
  - Installing symfony/dom-crawler (v4.4.15): Extracting archive
  - Installing symfony/css-selector (v4.4.15): Extracting archive
  - Installing psr/container (1.0.0): Extracting archive
  - Installing symfony/service-contracts (v2.2.0): Extracting archive
  - Installing symfony/polyfill-php80 (v1.20.0): Extracting archive
  - Installing symfony/polyfill-php73 (v1.20.0): Extracting archive
  - Installing symfony/console (v4.4.15): Extracting archive
  - Installing symfony/browser-kit (v4.4.15): Extracting archive
  - Installing ralouphie/getallheaders (3.0.3): Extracting archive
  - Installing psr/http-message (1.0.1): Extracting archive
  - Installing guzzlehttp/psr7 (1.7.0): Extracting archive
  - Installing sebastian/diff (2.0.1): Extracting archive
  - Installing sebastian/recursion-context (3.0.0): Extracting archive
  - Installing sebastian/exporter (3.1.2): Extracting archive
  - Installing sebastian/comparator (2.1.3): Extracting archive
  - Installing sebastian/version (2.0.1): Extracting archive
  - Installing sebastian/resource-operations (1.0.0): Extracting archive
  - Installing sebastian/object-reflector (1.1.1): Extracting archive
  - Installing sebastian/object-enumerator (3.0.3): Extracting archive
  - Installing sebastian/global-state (2.0.0): Extracting archive
  - Installing sebastian/environment (3.1.0): Extracting archive
  - Installing phpunit/php-text-template (1.2.1): Extracting archive
  - Installing doctrine/instantiator (1.3.1): Extracting archive
  - Installing phpunit/phpunit-mock-objects (5.0.10): Extracting archive
  - Installing phpunit/php-timer (1.0.9): Extracting archive
  - Installing phpunit/php-file-iterator (1.4.5): Extracting archive
  - Installing theseer/tokenizer (1.2.0): Extracting archive
  - Installing sebastian/code-unit-reverse-lookup (1.0.1): Extracting archive
  - Installing phpunit/php-token-stream (2.0.2): Extracting archive
  - Installing phpunit/php-code-coverage (5.3.2): Extracting archive
  - Installing webmozart/assert (1.9.1): Extracting archive
  - Installing phpdocumentor/reflection-common (2.2.0): Extracting archive
  - Installing phpdocumentor/type-resolver (1.4.0): Extracting archive
  - Installing phpdocumentor/reflection-docblock (5.2.2): Extracting archive
  - Installing phpspec/prophecy (v1.10.3): Extracting archive
  - Installing phar-io/version (1.0.1): Extracting archive
  - Installing phar-io/manifest (1.0.1): Extracting archive
  - Installing myclabs/deep-copy (1.10.1): Extracting archive
  - Installing phpunit/phpunit (6.5.14): Extracting archive
  - Installing codeception/phpunit-wrapper (6.8.2): Extracting archive
  - Installing codeception/stub (2.1.0): Extracting archive
  - Installing behat/gherkin (v4.6.2): Extracting archive
  - Installing codeception/base (2.5.6): Extracting archive
  - Installing codeception/verify (1.0.0): Extracting archive
  - Installing php-curl-class/php-curl-class (8.8.0): Extracting archive
  - Installing monitor/bot-monitor (v1.4.0): Extracting archive
  - Installing dueros/bot-sdk (v2.2.0): Extracting archive
  - Installing symfony/polyfill-php72 (v1.20.0): Extracting archive
  - Installing symfony/polyfill-intl-normalizer (v1.20.0): Extracting archive
  - Installing symfony/polyfill-intl-idn (v1.20.0): Extracting archive
  - Installing doctrine/lexer (1.2.1): Extracting archive
  - Installing egulias/email-validator (2.1.22): Extracting archive
  - Installing ezyang/htmlpurifier (v4.13.0): Extracting archive
  - Installing symfony/var-exporter (v5.1.7): Extracting archive
  - Installing symfony/cache-contracts (v2.2.0): Extracting archive
  - Installing symfony/cache (v4.4.15): Extracting archive
  - Installing guzzlehttp/promises (1.4.0): Extracting archive
  - Installing guzzlehttp/guzzle (6.5.5): Extracting archive
  - Installing fastgoo/getui-php (1.0): Extracting archive
  - Installing finecho/logistics (3.0.0): Extracting archive
  - Installing firebase/php-jwt (v5.2.0): Extracting archive
  - Installing griffinledingham/php-apple-signin (1.1): Extracting archive
  - Installing guzzlehttp/command (1.0.0): Extracting archive
  - Installing hashids/hashids (3.0.0): Extracting archive
  - Installing symfony/var-dumper (v5.1.7): Extracting archive
  - Installing tightenco/collect (v8.0.4): Extracting archive
  - Installing jaeger/phpquery-single (1.0.1): Extracting archive
  - Installing jaeger/g-http (V1.7.1): Extracting archive
  - Installing jaeger/querylist (V4.2.6): Extracting archive
  - Installing jianyan74/yii2-console-migration (1.0.1): Extracting archive
  - Installing yiisoft/yii2 (2.0.38): Extracting archive
  - Installing symfony/mime (v5.1.7): Extracting archive
  - Installing symfony/http-foundation (v4.4.15): Extracting archive
  - Installing symfony/psr-http-message-bridge (v2.0.2): Extracting archive
  - Installing pimple/pimple (v3.3.0): Extracting archive
  - Installing overtrue/socialite (2.0.21): Extracting archive
  - Installing monolog/monolog (1.25.5): Extracting archive
  - Installing overtrue/wechat (4.2.35): Extracting archive
  - Installing jianyan74/yii2-easy-wechat (1.1.3): Extracting archive
  - Installing jianyan74/yii2-treegrid (1.0.1): Extracting archive
  - Installing jpush/jpush (v3.6.6): Extracting archive
  - Installing kartik-v/yii2-krajee-base (v2.0.5): Extracting archive
  - Installing kartik-v/yii2-date-range (v1.7.2): Extracting archive
  - Installing kartik-v/yii2-widget-colorinput (v1.0.6): Extracting archive
  - Installing kartik-v/yii2-widget-datepicker (dev-master fada92b): Extracting archive
  - Installing kartik-v/yii2-widget-datetimepicker (dev-master f64114b): Extracting archive
  - Installing select2/select2 (4.0.13): Extracting archive
  - Installing kartik-v/yii2-widget-select2 (dev-master dbfe450): Extracting archive
  - Installing kartik-v/yii2-widget-timepicker (v1.0.4): Extracting archive
  - Installing league/event (2.2.0): Extracting archive
  - Installing lcobucci/jwt (3.3.3): Extracting archive
  - Installing paragonie/random_compat (v9.99.99): Extracting archive
  - Installing defuse/php-encryption (v2.2.1): Extracting archive
  - Installing league/oauth2-server (7.4.0): Extracting archive
  - Installing psr/http-client (1.0.1): Extracting archive
  - Installing php-http/promise (1.1.0): Extracting archive
  - Installing php-http/httplug (2.2.0): Extracting archive
  - Installing php-http/guzzle6-adapter (v2.0.1): Extracting archive
  - Installing php-http/message-factory (v1.0.2): Extracting archive
  - Installing clue/stream-filter (v1.5.0): Extracting archive
  - Installing php-http/message (1.9.1): Extracting archive
  - Installing php-http/discovery (1.12.0): Extracting archive
  - Installing moneyphp/money (v3.3.1): Extracting archive
  - Installing omnipay/common (v3.0.4): Extracting archive
  - Installing league/omnipay (v3.0.2)
  - Installing linslin/yii2-curl (1.4.0): Extracting archive
  - Installing phpseclib/phpseclib (2.0.29): Extracting archive
  - Installing phpseclib/bcmath_compat (1.0.5): Extracting archive
  - Installing lokielse/omnipay-alipay (v3.1.2): Extracting archive
  - Installing lokielse/omnipay-unionpay (v3.0.3): Extracting archive
  - Installing lokielse/omnipay-wechatpay (v3.0.11): Extracting archive
  - Installing overtrue/http (1.2.0): Extracting archive
  - Installing mingyoung/dingtalk (2.0.0): Extracting archive
  - Installing mobiledetect/mobiledetectlib (2.8.34): Extracting archive
  - Installing moontoast/math (1.2.1): Extracting archive
  - Installing myclabs/php-enum (1.7.6): Extracting archive
  - Installing symfony/process (v4.4.15): Extracting archive
  - Installing dragonmantank/cron-expression (v1.2.1): Extracting archive
  - Installing omnilight/yii2-scheduling (1.1.4): Extracting archive
  - Installing omnipay/stripe (v3.1.0): Extracting archive
  - Installing overtrue/easy-sms (1.1.24): Extracting archive
  - Installing guzzlehttp/guzzle-services (1.1.3): Extracting archive
  - Installing qcloud/cos-sdk-v5 (v2.1.1): Extracting archive
  - Installing overtrue/flysystem-cos (2.1.2): Extracting archive
  - Installing qiniu/php-sdk (v7.3.0): Extracting archive
  - Installing overtrue/flysystem-qiniu (1.0.5): Extracting archive
  - Installing overtrue/pinyin (3.0.6): Extracting archive
  - Installing psr/http-factory (1.0.1): Extracting archive
  - Installing markbaker/matrix (2.0.0): Extracting archive
  - Installing markbaker/complex (2.0.0): Extracting archive
  - Installing maennchen/zipstream-php (2.1.0): Extracting archive
  - Installing phpoffice/phpspreadsheet (1.15.0): Extracting archive
  - Installing ramsey/uuid (3.9.3): Extracting archive
  - Installing swoole/ide-helper (dev-master aefd9d1): Extracting archive
  - Installing symfony/polyfill-iconv (v1.20.0): Extracting archive
  - Installing tencentcloud/tencentcloud-sdk-php (3.0.94): Extracting archive
  - Installing unclead/yii2-multiple-input (2.22.0): Extracting archive
  - Installing yiisoft/yii2-httpclient (2.0.12): Extracting archive
  - Installing yiisoft/yii2-authclient (2.1.8): Extracting archive
  - Installing xj/yii2-oauth (2.0.4): Extracting archive
  - Installing aliyuncs/oss-sdk-php (v2.4.1): Extracting archive
  - Installing xxtime/flysystem-aliyun-oss (1.5.0): Extracting archive
  - Installing yiisoft/yii2-bootstrap (2.0.10): Extracting archive
  - Installing yiisoft/yii2-debug (2.0.14): Extracting archive
  - Installing yiisoft/yii2-elasticsearch (dev-master 14dbc0d): Extracting archive
  - Installing fzaninotto/faker (v1.9.1): Extracting archive
  - Installing yiisoft/yii2-faker (2.0.4): Extracting archive
  - Installing phpspec/php-diff (v1.1.3): Extracting archive
  - Installing bower-asset/typeahead.js (v0.11.1): Extracting archive
  - Installing yiisoft/yii2-gii (2.0.8): Extracting archive
  - Installing imagine/imagine (v0.5.0): Extracting archive
  - Installing yiisoft/yii2-imagine (2.0.4): Extracting archive
  - Installing yiisoft/yii2-queue (dev-master 8d043ef): Extracting archive
  - Installing yiisoft/yii2-redis (2.0.13): Extracting archive
  - Installing swiftmailer/swiftmailer (v6.2.3): Extracting archive
  - Installing yiisoft/yii2-swiftmailer (2.1.2): Extracting archive
  - Installing yly-openapi/yly-openapi-sdk (1.0.1): Extracting archive
  - Installing zhuzhichao/ip-location-zh (v2.4.4): Extracting archive
    Failed to extract khanamiryan/qrcode-detector-decoder: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f' -d '/var/www/html/rageframe2/vendor/composer/ef80db3a'

[/var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f or
        /var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947dc9f.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of khanamiryan/qrcode-detector-decoder failed
    Failed to extract bacon/bacon-qr-code: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a' -d '/var/www/html/rageframe2/vendor/composer/673cea0a'

[/var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a or
        /var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-8ae16f778d65e10f42d0d5e199b91b6a.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of bacon/bacon-qr-code failed
   5/179 [>---------------------------]   2%    Failed to extract league/mime-type-detection: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0' -d '/var/www/html/rageframe2/vendor/composer/262f53ce'

[/var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0 or
        /var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-81fd034be3ff72d1fd2788ffe9412cc0.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of league/mime-type-detection failed
    Failed to extract league/flysystem: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a' -d '/var/www/html/rageframe2/vendor/composer/2ed67cb2'

[/var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a or
        /var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-45cba50958f94d5e1f2bdc2026a9a29a.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of league/flysystem failed
    Failed to extract psr/log: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b' -d '/var/www/html/rageframe2/vendor/composer/213db32f'

[/var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b or
        /var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-1d6160e98b9b25620dc7b870059d586b.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of psr/log failed
    Failed to extract cache/tag-interop: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4' -d '/var/www/html/rageframe2/vendor/composer/b7594b19'

[/var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4 or
        /var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-99506db48ed867953bfc0ebde47cd3e4.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of cache/tag-interop failed
    Failed to extract cache/adapter-common: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe' -d '/var/www/html/rageframe2/vendor/composer/471a166c'

[/var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe or
        /var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-a2c71cd48d18b55e66f09b5bdae48ebe.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of cache/adapter-common failed
    Failed to extract cache/filesystem-adapter: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914' -d '/var/www/html/rageframe2/vendor/composer/7317a150'

[/var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914 or
        /var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-46fc3725fe9a95029122d5d36aa0b914.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of cache/filesystem-adapter failed
  14/179 [==>-------------------------]   7%    Failed to extract symfony/polyfill-ctype: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a' -d '/var/www/html/rageframe2/vendor/composer/098525b8'

[/var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a or
        /var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-fa799f2b6d4b4d0284623201b9c1c43a.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/polyfill-ctype failed
    Failed to extract symfony/yaml: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4' -d '/var/www/html/rageframe2/vendor/composer/cfe1ff47'

[/var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4 or
        /var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-836896ad05ed23bd54613643514a83d4.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/yaml failed
    Failed to extract symfony/finder: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac' -d '/var/www/html/rageframe2/vendor/composer/5b615df9'

[/var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac or
        /var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-74865895157cda76ce67331e9734b9ac.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/finder failed
    Failed to extract symfony/event-dispatcher-contracts: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec' -d '/var/www/html/rageframe2/vendor/composer/2618556f'

[/var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec or
        /var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-7a80335fa08c09c61a22fde86fb6bcec.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/event-dispatcher-contracts failed
    Failed to extract symfony/event-dispatcher: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5' -d '/var/www/html/rageframe2/vendor/composer/4ae9af78'

[/var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5 or
        /var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-775aff041386397bc6337d9ff59a4ea5.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/event-dispatcher failed
  19/179 [==>-------------------------]  10%    Failed to extract symfony/polyfill-mbstring: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4' -d '/var/www/html/rageframe2/vendor/composer/3d54a143'

[/var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4 or
        /var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-892611ac02f59a606bf58c23b15376a4.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/polyfill-mbstring failed
    Failed to extract symfony/dom-crawler: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77' -d '/var/www/html/rageframe2/vendor/composer/53b9823c'

[/var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77 or
        /var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-c948f5874cc82365b46ee44e5dd52c77.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/dom-crawler failed
    Failed to extract symfony/css-selector: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c' -d '/var/www/html/rageframe2/vendor/composer/42995f5e'

[/var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c or
        /var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-42bc42795990a135bb259c4d242f8b4c.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/css-selector failed
    Failed to extract psr/container: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652' -d '/var/www/html/rageframe2/vendor/composer/809cb365'

[/var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652 or
        /var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-485c326ecd391fdff8e42be4c4719652.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of psr/container failed
    Failed to extract symfony/service-contracts: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382' -d '/var/www/html/rageframe2/vendor/composer/60a2a74c'

[/var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382 or
        /var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-37990ed50e5c63872a89ed12e5618382.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/service-contracts failed
    Failed to extract symfony/polyfill-php80: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0' -d '/var/www/html/rageframe2/vendor/composer/b1abc863'

[/var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0 or
        /var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-701c8496c5850c46408beb34c414e9c0.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/polyfill-php80 failed
    Failed to extract symfony/polyfill-php73: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224' -d '/var/www/html/rageframe2/vendor/composer/aa3a7a00'

[/var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224 or
        /var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-a2a6900a0cc227ce03ee44f6f0f37224.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/polyfill-php73 failed
  27/179 [====>-----------------------]  15%    Failed to extract symfony/browser-kit: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5' -d '/var/www/html/rageframe2/vendor/composer/ffa3f511'

[/var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5 or
        /var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-b307d758c89cfbdc6b5e572dccbb04e5.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of symfony/browser-kit failed
    Failed to extract guzzlehttp/psr7: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af' -d '/var/www/html/rageframe2/vendor/composer/dfb150d3'

[/var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af or
        /var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-4467bfc488efed661c2f169b122747af.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of guzzlehttp/psr7 failed
    Failed to extract sebastian/diff: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd' -d '/var/www/html/rageframe2/vendor/composer/73b045cf'

[/var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd or
        /var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-9381aa934fc3565ec4621bb7c7ed18fd.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of sebastian/diff failed
    Failed to extract sebastian/recursion-context: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538' -d '/var/www/html/rageframe2/vendor/composer/d175af3c'

[/var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538 may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538 or
        /var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-0906175f573913ef7a7b6e542107f538.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of sebastian/recursion-context failed
    Failed to extract sebastian/resource-operations: (9) '/usr/bin/unzip' -qq '/var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d' -d '/var/www/html/rageframe2/vendor/composer/9ea9d735'

[/var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d]
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
note:  /var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d may be a plain executable, not an archive
unzip:  cannot find zipfile directory in one of /var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d or
        /var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d.zip, and cannot find /var/www/html/rageframe2/vendor/composer/tmp-a45a0b75ab7bd6c247b0b314b7fa7f6d.ZIP, period.

    The archive may contain identical file names with different capitalization (which fails on case insensitive filesystems)
    Unzip with unzip command failed, falling back to ZipArchive class
    Install of sebastian/resource-operations failed
 179/179 [============================] 100%
In ZipDownloader.php line 209:

  '/var/www/html/rageframe2/vendor/composer/tmp-074eeaa9fa770783e895b964d947d
  c9f' is a corrupted zip archive (0 bytes), try again.


install [--prefer-source] [--prefer-dist] [--prefer-install PREFER-INSTALL] [--dry-run] [--dev] [--no-suggest] [--no-dev] [--no-autoloader] [--no-progress] [--no-install] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [-a|--classmap-authoritative] [--apcu-autoloader] [--apcu-autoloader-prefix APCU-AUTOLOADER-PREFIX] [--ignore-platform-req IGNORE-PLATFORM-REQ] [--ignore-platform-reqs] [--] [<packages>...]

root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Package operations: 25 installs, 0 updates, 0 removals
  - Downloading khanamiryan/qrcode-detector-decoder (1.0.3)
  - Downloading bacon/bacon-qr-code (1.0.3)
  - Downloading league/mime-type-detection (1.5.1)
  - Downloading league/flysystem (1.1.3)
  - Downloading psr/log (1.1.3)
  - Downloading cache/tag-interop (1.0.0)
  - Downloading cache/adapter-common (1.1.0)
  - Downloading cache/filesystem-adapter (1.0.0)
  - Downloading symfony/polyfill-ctype (v1.20.0)
  - Downloading symfony/yaml (v4.4.15)
  - Downloading symfony/finder (v4.4.15)
  - Downloading symfony/event-dispatcher-contracts (v1.1.9)
  - Downloading symfony/event-dispatcher (v4.4.15)
  - Downloading symfony/polyfill-mbstring (v1.20.0)
  - Downloading symfony/dom-crawler (v4.4.15)
  - Downloading symfony/css-selector (v4.4.15)
  - Downloading psr/container (1.0.0)
  - Downloading symfony/service-contracts (v2.2.0)
  - Downloading symfony/polyfill-php80 (v1.20.0)
  - Downloading symfony/polyfill-php73 (v1.20.0)
  - Downloading symfony/browser-kit (v4.4.15)
  - Downloading guzzlehttp/psr7 (1.7.0)
  - Downloading sebastian/diff (2.0.1)
  - Downloading sebastian/recursion-context (3.0.0)
  - Downloading sebastian/resource-operations (1.0.0)
  - Installing khanamiryan/qrcode-detector-decoder (1.0.3): Extracting archive
  - Installing bacon/bacon-qr-code (1.0.3): Extracting archive
  - Installing league/mime-type-detection (1.5.1): Extracting archive
  - Installing league/flysystem (1.1.3): Extracting archive
  - Installing psr/log (1.1.3): Extracting archive
  - Installing cache/tag-interop (1.0.0): Extracting archive
  - Installing cache/adapter-common (1.1.0): Extracting archive
  - Installing cache/filesystem-adapter (1.0.0): Extracting archive
  - Installing symfony/polyfill-ctype (v1.20.0): Extracting archive
  - Installing symfony/yaml (v4.4.15): Extracting archive
  - Installing symfony/finder (v4.4.15): Extracting archive
  - Installing symfony/event-dispatcher-contracts (v1.1.9): Extracting archive
  - Installing symfony/event-dispatcher (v4.4.15): Extracting archive
  - Installing symfony/polyfill-mbstring (v1.20.0): Extracting archive
  - Installing symfony/dom-crawler (v4.4.15): Extracting archive
  - Installing symfony/css-selector (v4.4.15): Extracting archive
  - Installing psr/container (1.0.0): Extracting archive
  - Installing symfony/service-contracts (v2.2.0): Extracting archive
  - Installing symfony/polyfill-php80 (v1.20.0): Extracting archive
  - Installing symfony/polyfill-php73 (v1.20.0): Extracting archive
  - Installing symfony/browser-kit (v4.4.15): Extracting archive
  - Installing guzzlehttp/psr7 (1.7.0): Extracting archive
  - Installing sebastian/diff (2.0.1): Extracting archive
  - Installing sebastian/recursion-context (3.0.0): Extracting archive
  - Installing sebastian/resource-operations (1.0.0): Extracting archive
Package moontoast/math is abandoned, you should avoid using it. Use brick/math instead.
Package codeception/base is abandoned, you should avoid using it. No replacement was suggested.
Package phpunit/php-token-stream is abandoned, you should avoid using it. No replacement was suggested.
Package phpunit/phpunit-mock-objects is abandoned, you should avoid using it. No replacement was suggested.
Generating autoload files
44 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2# composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Nothing to install, update or remove
Package moontoast/math is abandoned, you should avoid using it. Use brick/math instead.
Package codeception/base is abandoned, you should avoid using it. No replacement was suggested.
Package phpunit/php-token-stream is abandoned, you should avoid using it. No replacement was suggested.
Package phpunit/phpunit-mock-objects is abandoned, you should avoid using it. No replacement was suggested.
Generating autoload files
44 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
root@02891538d8c9:/var/www/html/rageframe2#
root@02891538d8c9:/var/www/html/rageframe2#
```


### 初始化项目

> php init

```
root@02891538d8c9:/var/www/html/rageframe2# php init
Yii Application Initialization Tool v1.0

Which environment do you want the application to be initialized in?

  [0] Development
  [1] Production

  Your choice [0-1, or "q" to quit] 0

  Initialize the application under 'Development' environment? [yes|no] yes

  Start initialization ...

   generate api/config/main-local.php
   generate api/config/params-local.php
   generate api/config/test-local.php
   generate backend/config/main-local.php
   generate backend/config/params-local.php
   generate backend/config/test-local.php
   generate common/config/main-local.php
   generate common/config/params-local.php
   generate common/config/test-local.php
   generate console/config/main-local.php
   generate console/config/params-local.php
   generate console/config/test-local.php
   generate frontend/config/main-local.php
   generate frontend/config/params-local.php
   generate frontend/config/test-local.php
   generate html5/config/main-local.php
   generate html5/config/params-local.php
   generate html5/config/test-local.php
   generate merapi/config/main-local.php
   generate merapi/config/params-local.php
   generate merapi/config/test-local.php
   generate merchant/config/main-local.php
   generate merchant/config/params-local.php
   generate merchant/config/test-local.php
   generate oauth2/config/main-local.php
   generate oauth2/config/params-local.php
   generate oauth2/config/test-local.php
   generate storage/web/index.php
   generate storage/web/robots.txt
   generate web/api/index-test.php
   generate web/api/index.php
   generate web/api/robots.txt
   generate web/backend/index-test.php
   generate web/backend/index.php
   generate web/backend/robots.txt
   generate web/html5/index-test.php
   generate web/html5/index.php
   generate web/html5/robots.txt
   generate web/index-test.php
   generate web/index.php
   generate web/merapi/index-test.php
   generate web/merapi/index.php
   generate web/merapi/robots.txt
   generate web/merchant/index-test.php
   generate web/merchant/index.php
   generate web/merchant/robots.txt
   generate web/oauth2/index-test.php
   generate web/oauth2/index.php
   generate web/oauth2/robots.txt
   generate web/robots.txt
   generate yii
   generate yii_test
   generate yii_test.bat
   generate cookie validation key in backend/config/main-local.php
   generate cookie validation key in frontend/config/main-local.php
   generate cookie validation key in html5/config/main-local.php
   generate cookie validation key in api/config/main-local.php
   generate cookie validation key in oauth2/config/main-local.php
   generate cookie validation key in merchant/config/main-local.php
   generate cookie validation key in merapi/config/main-local.php
      chmod 0777 backend/runtime
      chmod 0777 frontend/runtime
      chmod 0777 html5/runtime
      chmod 0777 api/runtime
      chmod 0777 oauth2/runtime
      chmod 0777 merchant/runtime
      chmod 0777 merapi/runtime
      chmod 0777 /web/assets
      chmod 0777 /web/backend/assets
      chmod 0777 /web/html5/assets
      chmod 0777 /web/api/assets
      chmod 0777 /web/oauth2/assets
      chmod 0777 /web/merchant/assets
      chmod 0777 /web/merapi/assets
      chmod 0777 web/attachment
      chmod 0777 addons
      chmod 0755 yii
      chmod 0755 yii_test

  ... initialization completed.

root@02891538d8c9:/var/www/html/rageframe2#
```

### 配置数据库信息

找到 `common/config/main-local.php` 并配置相应的信息：

```php
<?php
return [
    'components' => [
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=192.168.56.108;dbname=rageframe',
            'username' => 'root',
            'password' => '123456',
            'charset' => 'utf8mb4',
            'tablePrefix' => 'rf_',
            'attributes' => [
                // PDO::ATTR_STRINGIFY_FETCHES => false, // 提取的时候将数值转换为字符串
                // PDO::ATTR_EMULATE_PREPARES => false, // 启用或禁用预处理语句的模拟
            ],
            // 'enableSchemaCache' => true, // 是否开启缓存, 请了解其中机制在开启，不了解谨慎
            // 'schemaCacheDuration' => 3600, // 缓存时间
            // 'schemaCache' => 'cache', // 缓存名称
            // 断线重连，主要用于 websocket
            // 'commandMap' => [
            //     'mysql' => 'common\replaces\Command'
            // ],
        ],
        /**
        // redis缓存
        // 注意：系统默认开启了file缓存的保存路径，如果开启redis或者其他缓存请去main里面删除
         * 'cache' => [
            'class' => 'yii\redis\Cache',
        ],
        // session写入缓存配置
        'session' => [
            'class' => 'yii\redis\Session',
            'redis' => [
                'class' => 'yii\redis\Connection',
                'hostname' => 'localhost',
                'port' => 6379,
                'database' => 0,
            ],
        ],
        */
        'mailer' => [
            'class' => 'yii\swiftmailer\Mailer',
            'viewPath' => '@common/mail',
            // send all mails to a file by default. You have to set
            // 'useFileTransport' to false and configure a transport
            // for the mailer to send real emails.
            'useFileTransport' => true,
        ],
    ],
];
```

### 安装数据库

> php ./yii migrate/up

```
root@02891538d8c9:/var/www/html/rageframe2# php ./yii migrate/up
Yii Migration Tool (based on Yii v2.0.38)

Exception 'yii\db\Exception' with message 'SQLSTATE[HY000] [1049] Unknown database 'rageframe''

in /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php:642

Error Info:
Array
(
    [0] => HY000
    [1] => 1049
    [2] => Unknown database 'rageframe'
)

Caused by: Exception 'PDOException' with message 'SQLSTATE[HY000] [1049] Unknown database 'rageframe''

in /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php:710

Stack trace:
#0 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php(710): PDO->__construct('mysql:host=192....', 'root', '123456', Array)
#1 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php(631): yii\db\Connection->createPdoInstance()
#2 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php(1030): yii\db\Connection->open()
#3 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Connection.php(1017): yii\db\Connection->getMasterPdo()
#4 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Command.php(255): yii\db\Connection->getSlavePdo()
#5 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Command.php(1154): yii\db\Command->prepare(true)
#6 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Command.php(401): yii\db\Command->queryInternal('fetchAll', NULL)
#7 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/mysql/Schema.php(319): yii\db\Command->queryAll()
#8 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/mysql/Schema.php(125): yii\db\mysql\Schema->findColumns(Object(yii\db\TableSchema))
#9 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Schema.php(757): yii\db\mysql\Schema->loadTableSchema('rf_migration')
#10 /var/www/html/rageframe2/vendor/yiisoft/yii2/db/Schema.php(193): yii\db\Schema->getTableMetadata('{{%migration}}', 'schema', true)
#11 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/controllers/MigrateController.php(211): yii\db\Schema->getTableSchema('{{%migration}}', true)
#12 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/controllers/BaseMigrateController.php(877): yii\console\controllers\MigrateController->getMigrationHistory(NULL)
#13 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/controllers/BaseMigrateController.php(169): yii\console\controllers\BaseMigrateController->getNewMigrations()
#14 [internal function]: yii\console\controllers\BaseMigrateController->actionUp(0)
#15 /var/www/html/rageframe2/vendor/yiisoft/yii2/base/InlineAction.php(57): call_user_func_array(Array, Array)
#16 /var/www/html/rageframe2/vendor/yiisoft/yii2/base/Controller.php(180): yii\base\InlineAction->runWithParams(Array)
#17 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/Controller.php(179): yii\base\Controller->runAction('up', Array)
#18 /var/www/html/rageframe2/vendor/yiisoft/yii2/base/Module.php(528): yii\console\Controller->runAction('up', Array)
#19 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/Application.php(180): yii\base\Module->runAction('migrate/up', Array)
#20 /var/www/html/rageframe2/vendor/yiisoft/yii2/console/Application.php(147): yii\console\Application->runAction('migrate/up', Array)
#21 /var/www/html/rageframe2/vendor/yiisoft/yii2/base/Application.php(386): yii\console\Application->handleRequest(Object(yii\console\Request))
#22 /var/www/html/rageframe2/yii(26): yii\base\Application->run()
#23 {main}
root@02891538d8c9:/var/www/html/rageframe2#
```

没有库会报错。新建数据库 `rageframe` ，然后再执行：
```
root@02891538d8c9:/var/www/html/rageframe2# php ./yii migrate/up
Yii Migration Tool (based on Yii v2.0.38)

Creating migration history table "rf_migration"...Done.
Total 56 new migrations to be applied:
        m200529_152443_api_access_token
        m200529_152443_backend_member_auth
        m200529_152443_backend_notify
        m200529_152443_backend_notify_member
        m200529_152443_backend_notify_pull_time
        m200529_152443_backend_notify_subscription_config
        m200529_152443_common_action_behavior
        m200529_152443_common_action_log
        m200529_152443_common_addons
        m200529_152443_common_addons_binding
        m200529_152443_common_addons_config
        m200529_152443_common_attachment
        m200529_152444_common_config_value
        m200529_152444_common_ip_blacklist
        m200529_152444_common_log
        m200529_152444_common_menu_cate
        m200529_152444_common_pay_log
        m200529_152445_common_provinces
        m200529_152445_common_report_log
        m200529_152445_common_sms_log
        m200529_152445_member
        m200529_152445_member_account
        m200529_152445_member_address
        m200529_152445_member_auth
        m200529_152445_member_balance_withdraw
        m200529_152445_member_bank_account
        m200529_152445_member_credits_log
        m200529_152445_member_invoice
        m200529_152445_member_level
        m200529_152445_member_recharge_config
        m200529_152446_merapi_access_token
        m200529_152446_merchant
        m200529_152446_merchant_account
        m200529_152446_merchant_auth
        m200529_152446_merchant_bank_account
        m200529_152446_merchant_base_config
        m200529_152446_merchant_cate
        m200529_152446_merchant_commission_withdraw
        m200529_152446_merchant_credits_log
        m200529_152446_merchant_member
        m200529_152446_merchant_member_auth
        m200529_152446_oauth2_access_token
        m200529_152446_oauth2_authorization_code
        m200529_152447_oauth2_client
        m200529_152447_oauth2_refresh_token
        m200529_152447_rbac_auth_assignment
        m200529_152447_rbac_auth_item
        m200529_152447_rbac_auth_item_child
        m200529_152447_rbac_auth_role
        m200529_155245_backend_member
        m200529_155245_common_menu
        m200711_160132_common_config
        m200711_160402_common_config_cate
        m200806_080040_common_pay_refund
        m200906_133303_common_bank_number
        m200906_133303_common_commission_withdraw_log

Apply the above migrations? (yes|no) [no]:yes
*** applying m200529_152443_api_access_token
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.001s)
    > create table {{%api_access_token}} ... done (time: 0.293s)
    > create unique index access_token on {{%api_access_token}} (access_token) ... done (time: 0.167s)
    > create unique index refresh_token on {{%api_access_token}} (refresh_token) ... done (time: 0.119s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152443_api_access_token (time: 0.653s)

*** applying m200529_152443_backend_member_auth
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.019s)
    > create table {{%backend_member_auth}} ... done (time: 0.132s)
    > create index oauth_client on {{%backend_member_auth}} (oauth_client, oauth_client_user_id) ... done (time: 0.125s)
    > create index member_id on {{%backend_member_auth}} (member_id) ... done (time: 0.150s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.005s)
*** applied m200529_152443_backend_member_auth (time: 0.487s)

*** applying m200529_152443_backend_notify
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%backend_notify}} ... done (time: 0.146s)
    > create index created_at on {{%backend_notify}} (type, created_at) ... done (time: 0.123s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_backend_notify (time: 0.329s)

*** applying m200529_152443_backend_notify_member
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.007s)
    > create table {{%backend_notify_member}} ... done (time: 0.156s)
    > create index member_id on {{%backend_notify_member}} (member_id) ... done (time: 0.115s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_backend_notify_member (time: 0.318s)

*** applying m200529_152443_backend_notify_pull_time
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.001s)
    > create table {{%backend_notify_pull_time}} ... done (time: 0.171s)
    > create index member_id on {{%backend_notify_pull_time}} (member_id, type, alert_type) ... done (time: 0.114s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_backend_notify_pull_time (time: 0.325s)

*** applying m200529_152443_backend_notify_subscription_config
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%backend_notify_subscription_config}} ... done (time: 0.152s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_backend_notify_subscription_config (time: 0.197s)

*** applying m200529_152443_common_action_behavior
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%common_action_behavior}} ... done (time: 0.146s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_common_action_behavior (time: 0.187s)

*** applying m200529_152443_common_action_log
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%common_action_log}} ... done (time: 0.143s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200529_152443_common_action_log (time: 0.179s)

*** applying m200529_152443_common_addons
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%common_addons}} ... done (time: 0.177s)
    > create index name on {{%common_addons}} (name) ... done (time: 0.129s)
    > create index update on {{%common_addons}} (updated_at) ... done (time: 0.168s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152443_common_addons (time: 0.520s)

*** applying m200529_152443_common_addons_binding
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.003s)
    > create table {{%common_addons_binding}} ... done (time: 0.158s)
    > create index addons_name on {{%common_addons_binding}} (addons_name) ... done (time: 0.153s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152443_common_addons_binding (time: 0.369s)

*** applying m200529_152443_common_addons_config
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.003s)
    > create table {{%common_addons_config}} ... done (time: 0.138s)
    > create index addons_name on {{%common_addons_config}} (addons_name) ... done (time: 0.129s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152443_common_addons_config (time: 0.310s)

*** applying m200529_152443_common_attachment
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.002s)
    > create table {{%common_attachment}} ... done (time: 0.195s)
    > create index md5 on {{%common_attachment}} (md5) ... done (time: 0.179s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152443_common_attachment (time: 0.421s)

*** applying m200529_152444_common_config_value
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.023s)
    > create table {{%common_config_value}} ... done (time: 0.129s)
    > create index config_id on {{%common_config_value}} (config_id) ... done (time: 0.141s)
    > insert into {{%common_config_value}} ... done (time: 0.028s)
    > insert into {{%common_config_value}} ... done (time: 0.042s)
    > insert into {{%common_config_value}} ... done (time: 0.035s)
    > insert into {{%common_config_value}} ... done (time: 0.025s)
    > insert into {{%common_config_value}} ... done (time: 0.030s)
    > insert into {{%common_config_value}} ... done (time: 0.024s)
    > insert into {{%common_config_value}} ... done (time: 0.023s)
    > insert into {{%common_config_value}} ... done (time: 0.035s)
    > insert into {{%common_config_value}} ... done (time: 0.032s)
    > insert into {{%common_config_value}} ... done (time: 0.023s)
    > insert into {{%common_config_value}} ... done (time: 0.028s)
    > insert into {{%common_config_value}} ... done (time: 0.034s)
    > insert into {{%common_config_value}} ... done (time: 0.025s)
    > insert into {{%common_config_value}} ... done (time: 0.030s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.002s)
*** applied m200529_152444_common_config_value (time: 0.789s)

# 省略若干

*** applying m200906_133303_common_commission_withdraw_log
    > execute SQL: SET foreign_key_checks = 0 ... done (time: 0.003s)
    > create table {{%common_commission_withdraw_log}} ... done (time: 0.152s)
    > execute SQL: SET foreign_key_checks = 1; ... done (time: 0.001s)
*** applied m200906_133303_common_commission_withdraw_log (time: 0.213s)


56 migrations were applied.

Migrated up successfully.
root@02891538d8c9:/var/www/html/rageframe2#
```

### 初始化账号密码

> php ./yii password/init

```
root@02891538d8c9:/var/www/html/rageframe2# php ./yii password/init
username; F3Kr8
password; HQG3mOK09Z
root@02891538d8c9:/var/www/html/rageframe2#
```

这是登录 Backend 模块的账号和密码，注意保存下。

### Nginx配置

复习下 Nginx 的配置：
`/etc/nginx/nginx.conf` 内容：
```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    #include /etc/nginx/conf.d/*.conf;
    #修改为下面内容
    include /usr/share/nginx/html/vhost/virtualbox/docker/*.conf;
}
```

编辑 `C:\Windows\System32\drivers\etc\hosts`，加入：
```
192.168.56.108      rageframe2.test
192.168.56.108      backend.rageframe2.test
```

server配置：
```
server {
    listen       80;
    server_name rageframe2.test;

    index index.php index.html index.htm default.php default.htm default.html;
    root /usr/share/nginx/html/rageframe2/web;

    #ERROR-PAGE-START  错误页配置，可以注释、删除或修改
    #error_page 404 /404.html;
    #error_page 502 /502.html;
    #ERROR-PAGE-END

    #PHP-INFO-START  PHP引用配置，可以注释或修改
    #include enable-php-72.conf;
    #PHP-INFO-END

    location ~ \.php$ {
        root   /var/www/html/rageframe2/web;
        fastcgi_pass   php:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    #伪静态
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    location /backend {
        try_files $uri $uri/ /backend/index.php$is_args$args;
    }
    location /api {
        try_files $uri $uri/ /api/index.php$is_args$args;
    }
    location /merchant {
        try_files $uri $uri/ /merchant/index.php$is_args$args;
    }
    location /merapi {
        try_files $uri $uri/ /merapi/index.php$is_args$args;
    }
    location /html5 {
        try_files $uri $uri/ /html5/index.php$is_args$args;
    }
    location /oauth2 {
        try_files $uri $uri/ /oauth2/index.php$is_args$args;
    }
    
    location ~* ^/attachment/.*\.(php|php5)$ 
    {
        deny all;
    }

    #REWRITE-START URL重写规则引用,修改后将导致设置的伪静态规则失效
    #include /www/server/panel/vhost/rewrite/rageframe2.test.conf;
    #REWRITE-END

    #禁止访问的文件或目录
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
    {
        return 404;
    }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        #expires      30d;
        error_log /dev/null;
        access_log /dev/null;
    }
    
    location ~ .*\.(js|css)?$
    {
        #expires      12h;
        error_log /dev/null;
        access_log /dev/null; 
    }

    #access_log  /www/wwwlogs/rageframe2.test.log;
    #error_log  /www/wwwlogs/rageframe2.test.error.log;
}
```

重启服务容器。

在 server配置 这里折腾了好长时间，因为是用虚拟机中docker安装的 nginx 和 php，所以 web项目的根目录不一样，
nginx 中为：`/usr/share/nginx/html/`，php 中为：`/var/www/html/`，不好处理，想着要不就每个模块分开配置吧，
如 backend 模块：
```
server {
    listen       80;
    server_name backend.rageframe2.test;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/rageframe2/web/backend;
        index  index.php;
        if (!-e $request_filename) {
               rewrite  ^(.*)$  /index.php?s=/$1  last;
               break;
         }
    }

    location ~* ^/attachment/.*\.(php|php5)$ 
    {
         deny all;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root   /var/www/html/rageframe2/web/backend;
        fastcgi_pass   php:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

也是在不懈的折腾下，弄好了上面的配置，现在不用通过各个域名来分别访问各自模块了，如：`backend.rageframe2.test`，
而只需要 `rageframe2.test/backend` 就可以了，方便了不少。
重点是提前在`server`块中配置了 `root`，又在`location`块中配置了 `root`，仔细想一下。

### Linux 下文件缓存权限授权

Linux 环境下如果是文件缓存去 `backend/runtime`  目录执行一下 `chmod -R 777 cache`，不执行可能会造成修改了网站设置缓存不生效的情况


<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

