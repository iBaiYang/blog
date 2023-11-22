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







<br/><br/><br/><br/><br/>
## 参考资料

EasySwoole文档 <https://www.easyswoole.com/Preface/intro.html>

Swoole文档 <https://wiki.swoole.com>

EasySwoole 3.x 中文文档 <https://www.mianshigee.com/tutorial/easyswoole-3.x-zh/noobcourse-introduction.md>

