---
layout: post
categories: PHP
title: webman env配置文件加载分析
meta: webman env配置文件加载分析
---
* content
{:toc}

## 正文

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


## 参考资料

webman 安装 <https://www.workerman.net/doc/webman/install.html>








