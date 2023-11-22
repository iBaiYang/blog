---
layout: post
categories: Swoole
title: EasySwoole 使用
meta: EasySwoole 使用
---
* content
{:toc}

## 引言

这里写一些EasySwoole使用中碰到的一些知识点。

## 异步任务Task

安装：
> composer require easyswoole/task

配置：
```php
<?php

// 这里省略

return [
    // 这里省略 ...

    'MAIN_SERVER' => [

        // 这里省略 ...

        'TASK' => [
            'workerNum' => 4,
            'maxRunningNum' => 128,
            'timeout' => 15
        ]
    ],

    // 这里省略 ...
];
```

查看所有Task进程的状态：
> php easyswoole task status

输出挺有意思：
```
┌---------┬---------┬------┬-----┬---------------------┬-------------┐
│ running │ success │ fail │ pid │     startUpTime     │ workerIndex │
├---------┼---------┼------┼-----┼---------------------┼-------------┤
│ 0       │ 10      │ 0    │ 78  │ 2023-11-22 10:40:26 │ 0           │
├---------┼---------┼------┼-----┼---------------------┼-------------┤
│ 0       │ 6       │ 0    │ 79  │ 2023-11-22 10:40:26 │ 1           │
├---------┼---------┼------┼-----┼---------------------┼-------------┤
│ 0       │ 3       │ 0    │ 80  │ 2023-11-22 10:40:26 │ 2           │
├---------┼---------┼------┼-----┼---------------------┼-------------┤
│ 0       │ 6       │ 0    │ 81  │ 2023-11-22 10:40:26 │ 3           │
└---------┴---------┴------┴-----┴---------------------┴-------------┘
```

具体使用有两种方式：闭包函数、任务模板。

一、异步调用选择闭包函数

调用：
```
\EasySwoole\EasySwoole\Task\TaskManager::getInstance()->async(function () {
    echo 'async';
}, function ($reply, $taskId, $workerIndex) {
    // $reply 返回的执行结果
    // $taskId 任务id
    echo 'async success';
});
```

二、异步调用选择任务模版

先定义模板：
```php
<?php

namespace App\Task;

use EasySwoole\Task\AbstractInterface\TaskInterface;

class CustomTask implements TaskInterface
{
    protected $data;

    public function __construct($data)
    {
        // 保存投递过来的数据
        $this->data = $data;
    }

    public function run(int $taskId, int $workerIndex)
    {
        // 执行逻辑
        // echo $this->data['user'];
    }

    public function onException(\Throwable $throwable, int $taskId, int $workerIndex)
    {
        // 异常处理
    }
}
```

调用：
```
$task = \EasySwoole\EasySwoole\Task\TaskManager::getInstance();

// 投递异步任务
$task->async(new CustomTask(['user' => 'custom']));
```

## 客户端fd链接

如何获取客户端fd链接，这里说一下。

服务通过单例模式启动，我们在项目中可以随时获取该服务：
```
$server = \EasySwoole\EasySwoole\ServerManager::getInstance()->getSwooleServer();
```

服务监听和管理客户端fd链接，我们可以在该服务中获取客户端fd链接的信息。

一、获取服务中的所有fd链接并群发信息

```
$server = \EasySwoole\EasySwoole\ServerManager::getInstance()->getSwooleServer();

$start_fd = 0;
while(true)
{
    $conn_list = $server->getClientList($start_fd, 10);
    if ($conn_list === false or count($conn_list) === 0) {
        echo "finish\n";
        break;
    }

    $start_fd = end($conn_list);

    foreach($conn_list as $fd) {
        $server->send($fd, "broadcast");
    }
}
```

二、获取一个fd链接的信息

```
$server = \EasySwoole\EasySwoole\ServerManager::getInstance()->getSwooleServer();
$fdinfo = $server->getClientInfo($fd);  
```

输出信息看一下：
```
Array
(
    [server_port] => 9252
    [server_fd] => 3
    [socket_fd] => 63
    [socket_type] => 1
    [remote_port] => 14575
    [remote_ip] => 192.168.0.143
    [reactor_id] => 3
    [connect_time] => 1700624918
    [last_time] => 1700624924
    [last_recv_time] => 1700624924.2119
    [last_send_time] => 1700624922.3476
    [last_dispatch_time] => 1700624924.2119
    [close_errno] => 0
    [recv_queued_bytes] => 0
    [send_queued_bytes] => 0
)
```

## Timer定时器






<br/><br/><br/><br/><br/>
## 参考资料

EasySwoole文档 <https://www.easyswoole.com/Preface/intro.html>

Swoole文档 <https://wiki.swoole.com>

EasySwoole 3.x 中文文档 <https://www.mianshigee.com/tutorial/easyswoole-3.x-zh/noobcourse-introduction.md>

