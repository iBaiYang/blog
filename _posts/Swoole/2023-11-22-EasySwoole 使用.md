---
layout: post
categories: Swoole
title: EasySwoole 使用
meta: EasySwoole 使用
---
* content
{:toc}

## 引言

这里写一些EasySwoole使用中碰到的知识点。

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
        echo $this->data['user'];  // 运行在Task进程中，主进程看不到输出
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

注意点：
* Task任务运行在Task进程上，在主进程无法追踪运行过程（如echo在主进程看不到输出）。

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

## IOC 容器

Dependency Injection 依赖注入。EasySwoole 实现了简单版的 IOC 容器。

设置：
```php
<?php
// 获取依赖注入组件 IoC 容器 对象
$di = \EasySwoole\Component\Di::getInstance();

$di->set('test', new TestClass());
$di->set('test', TestClass::class);

// set 的时候储存的是[类名, 方法名]的数组，需要自己手动调用 call_user_func() 执行（不要被错误与异常章节的 demo 误解为会自动执行)
$di->set('test', [TestClass::class,'testFunction']);

// set 的时候传递了类名，get 的时候才去 new 对象，并且将可变变量传递进构造函数，返回实例化后的对象
$di->set('test', TestClass::class, $arg_one, $arg_tow);
```

使用：
```php
<?php
// 获取依赖注入组件 IoC 容器 对象
$di = \EasySwoole\Component\Di::getInstance();

// 获取 IoC 容器 中某个注入的内容
$val = $di->get('test');

// 删除 IoC 容器 中某个注入的内容
$di->delete('test');

// 清空 IoC 容器 的所有内容
$di->clear('test');
```

## 自定义进程

一、定义自定义进程类

```php
<?php
namespace App\Process;

use EasySwoole\Component\Process\AbstractProcess;
use Swoole\Process;

class CustomProcess extends AbstractProcess
{
    protected function run($arg)
    {
        // TODO: Implement run() method.
        $processName = $this->getProcessName(); // 获取 注册进程名称
        $swooleProcess = $this->getProcess(); // 获取 注册进程的实例 \Swoole\Process
        $processPid = $this->getPid(); // 获取 当前进程 Pid
        $args = $this->getArg(); // 获取 注册进程时传递的参数

        var_dump('### 开始运行自定义进程 start ###');
        var_dump($processName, $swooleProcess, $processPid, $args);
        var_dump('### 运行自定义进程结束 end ###');
    }

    protected function onPipeReadable(Process $process)
    {
        // 该回调可选
        // 当主进程对子进程发送消息的时候 会触发
        $recvMsgFromMain = $process->read(); // 用于获取主进程给当前进程发送的消息
        var_dump('收到主进程发送的消息: ');
        var_dump($recvMsgFromMain);
    }

    protected function onException(\Throwable $throwable, ...$args)
    {
        // 该回调可选
        // 捕获 run 方法内抛出的异常
        // 这里可以通过记录异常信息来帮助更加方便地知道出现问题的代码
    }

    protected function onShutDown()
    {
        // 该回调可选
        // 进程意外退出 触发此回调
        // 大部分用于清理工作
    }

    protected function onSigTerm()
    {
        // 当进程接收到 SIGTERM 信号触发该回调
    }
}
```

二、注册进程

在项目根目录的 EasySwooleEvent.php 文件的 mainServerCreate 方法中进行进程注册

```php
<?php

namespace EasySwoole\EasySwoole;

use EasySwoole\EasySwoole\AbstractInterface\Event;
use EasySwoole\EasySwoole\Swoole\EventRegister;

class EasySwooleEvent implements Event
{
    public static function initialize()
    {
        date_default_timezone_set('Asia/Shanghai');
    }

    public static function mainServerCreate(EventRegister $register)
    {
        $processConfig = new \EasySwoole\Component\Process\Config([
            'processName' => 'CustomProcess', // 设置 进程名称为 TickProcess
            'processGroup' => 'Custom', // 设置 进程组名称为 Tick
            'arg' => [
                'arg1' => 'this is arg1!',
            ], // 传递参数到自定义进程中
            'enableCoroutine' => true, // 设置 自定义进程自动开启协程环境
        ]);

        // 【推荐】使用 \EasySwoole\Component\Process\Manager 类注册自定义进程
        $customProcess = (new \App\Process\CustomProcess($processConfig));
        // 【可选操作】把 tickProcess 的 Swoole\Process 注入到 Di 中，方便在后续控制器等业务中给自定义进程传输信息(即实现主进程与自定义进程间通信)
        \EasySwoole\Component\Di::getInstance()->set('customSwooleProcess', $customProcess->getProcess());
        // 注册进程
        \EasySwoole\Component\Process\Manager::getInstance()->addProcess($customProcess);
    }
}
```

三、向自定义进程中传递消息

```php
<?php

namespace App\HttpController;

use EasySwoole\Component\Di;
use EasySwoole\Http\AbstractInterface\Controller;

class Index extends Controller
{
    public function index()
    {
        // 获取 Di 中注入的 自定义进程
        $customProcess = Di::getInstance()->get('customSwooleProcess');
        // 向自定义进程中传输信息，会触发自定义进程的 onPipeReadable 回调
        $customProcess->write('this is test!');
    }
}
```

四、进程管理命令

执行 `php easyswoole process -h` 来查看具体操作。

显示所有进程

    php easyswoole process show

如果想要以 MB 形式显示：

    php easyswoole process show -d

杀死指定进程(PID)

```
php easyswoole process kill --pid=PID
```

杀死指定进程组(GROUP)

```
php easyswoole process kill --group=GROUP_NAME
```

杀死所有进程

    php easyswoole process killAll

强制杀死进程

```
php easyswoole process kill --pid=PID -f
```

## 协程

一、创建协程

方式一、go

```php
<?php
function task1(){
    for ($i=0;$i<=300;$i++){
        //写入文件,大概要3000微秒
        usleep(3000);
        echo "写入文件{$i}\n";
        Co::sleep(0.001);//挂起当前协程,0.001秒后恢复//相当于切换协程
    }
}
function task2(){
    for ($i=0;$i<=500;$i++){
        //发送邮件给500名会员,大概3000微秒
        usleep(3000);
        echo "发送邮件{$i}\n";
        Co::sleep(0.001);//挂起当前协程,0.001秒后恢复//相当于切换协程
    }
}
function task3(){
    for ($i=0;$i<=100;$i++){
        //模拟插入100条数据,大概3000微秒
        usleep(3000);
        echo "插入数据{$i}\n";
        Co::sleep(0.001);//挂起当前协程,0.001秒后恢复//相当于切换协程
    }
}

$pid1 = go('task1');//go函数是swoole的开启协程函数,用于开启一个协程
$pid2 = go('task2');
$pid3 = go('task3');
```

方式二、Coroutine

```php
<?php
\Swoole\Coroutine::create(function () {
    /* 调用协程API */

    // 用户可以在这里调用上述协程 API
});
```

二、WaitGroup等待所有任务完成

```php
<?php
go(function () {
    $ret = [];

    $wait = new \EasySwoole\Component\WaitGroup();

    $wait->add();
    // 启动第 1 个协程
    go(function () use ($wait, &$ret) {
        // 模拟耗时任务 1
        \co::sleep(0.1);
        $ret[] = time();
        $wait->done();
    });

    $wait->add();
    // 启动第 2 个协程
    go(function () use ($wait, &$ret) {
        // 模拟耗时任务 2
        \co::sleep(2);
        $ret[] = time();
        $wait->done();
    });

    // 挂起当前协程，等待所有任务完成后恢复
    $wait->wait();

    // 这里 $ret 包含了 2 个任务执行结果
    var_dump($ret);
});
```

## Timer定时器

一、循环执行

```
// 每隔 10 秒执行一次
\EasySwoole\Component\Timer::getInstance()->loop(10 * 1000, function () {
    echo "this timer runs at intervals of 10 seconds\n";
});
```

二、延时执行

```
// 10 秒后执行一次
\EasySwoole\Component\Timer::getInstance()->after(10 * 1000, function () {
    echo "ten seconds later\n";
});
```

三、清除定时器

```
$timerId = \EasySwoole\Component\Timer::getInstance()->loop(2 * 1000, function () {
    echo "timeout\n";
},'time_name');

var_dump($timerId); // int(1)

// 清除该定时器
$ret = \EasySwoole\Component\Timer::getInstance()->clear($timerId);
var_dump($ret); // bool(true)

// 定时器得不到执行 不输出：timeout
```

四、注意点一

在定时器循环执行中为了防止因为任务阻塞而引起定时器不准确，可以把任务给异步进程处理：
```
// 每隔 10 秒执行一次
\EasySwoole\Component\Timer::getInstance()->loop(10 * 1000, function () {
    // 把任务给异步进程处理
    \EasySwoole\EasySwoole\Task\TaskManager::getInstance()->async(function () {
        echo 'async';
    }, function ($reply, $taskId, $workerIndex) {
        // $reply 返回的执行结果
        // $taskId 任务id
        echo 'async success';
    });
});
```

五、注意点二

当定时器在类的方法中时，可以在定时器内直接调用类中的变量：
```
Class A 
{
    pretected $x;

    public function abc () 
    {
        $this->x = 1;
        $y = 2;
        
        \EasySwoole\Component\Timer::getInstance()->after(10 * 1000, function () use ($y) {
            echo $this->x . PHP_EOL;  // 类变量可以直接使用
            echo $y . PHP_EOL;  // 这个变量必须要 use 引入
        });
    }
}

// 输出结果
1
2
```

## Crontab分钟级定时任务

一、定义定时任务类，新增 \App\Crontab\CustomCrontab 文件

```php
<?php

namespace App\Crontab;

use EasySwoole\Crontab\JobInterface;

class CustomCrontab implements JobInterface
{
    public function jobName(): string
    {
        // 定时任务的名称
        return 'CustomCrontab';
    }

    public function crontabRule(): string
    {
        // 定义执行规则 根据 Crontab 来定义
        // 这里是每分钟执行 1 次
        return '*/1 * * * *';
    }

    public function run()
    {
        // 定时任务的执行逻辑

        // 相当于每分钟打印1次时间戳，这里只是参考示例。
        echo time();
    }

    public function onException(\Throwable $throwable)
    {
        // 捕获 run 方法内所抛出的异常
    }
}
```

二、注册定时任务，在项目根目录的 EasySwooleEvent.php 文件的 mainServerCreate 方法中进行定时任务注册

```php
<?php

namespace EasySwoole\EasySwoole;

use App\Crontab\CustomCrontab;
use EasySwoole\EasySwoole\AbstractInterface\Event;
use EasySwoole\EasySwoole\Swoole\EventRegister;
use EasySwoole\EasySwoole\Crontab\Crontab;

class EasySwooleEvent implements Event
{
    public static function initialize()
    {
        date_default_timezone_set('Asia/Shanghai');
    }

    public static function mainServerCreate(EventRegister $register)
    {
        ###### 注册一个定时任务 ######
        // 配置定时任务
        $crontabConfig = new \EasySwoole\Crontab\Config();

        // 1.设置执行定时任务的 socket 服务的 socket 文件存放的位置，默认值为 当前文件所在目录
        // 这里设置为框架的 Temp 目录
        $crontabConfig->setTempDir(EASYSWOOLE_TEMP_DIR);

        // 2.设置执行定时任务的 socket 服务的名称，默认值为 'EasySwoole'
        $crontabConfig->setServerName('EasySwoole');

        // 3.设置用来执行定时任务的 worker 进程数，默认值为 3
        $crontabConfig->setWorkerNum(3);

        // 4.设置定时任务执行出现异常的异常捕获回调
        $crontabConfig->setOnException(function (\Throwable $throwable) {
            // 定时任务执行发生异常时触发（如果未在定时任务类的 onException 中进行捕获异常则会触发此异常回调）
        });

        // 创建定时任务实例
        $crontab = \EasySwoole\EasySwoole\Crontab\Crontab::getInstance($crontabConfig);

        // 注册定时任务
        $crontab->register(new CustomCrontab());
    }
}
```

## Crontab秒级定时任务

EasySwoole 没有直接提供最小粒度为秒级的定时任务，可以通过使用 EasySwoole 的自定义进程组件 + 协程 Sleep 实现。

一、创建一个秒级定时任务类（自定义进程类）

```php
<?php

namespace App\Crontab;

use EasySwoole\Component\Process\AbstractProcess;

class SecondCrontab extends AbstractProcess
{
    protected function run($arg)
    {
        while(1) {

            // 这里写执行逻辑
            // to do something.

            // 这里表示每秒打印一个日期时间字符串，仅供参考
            var_dump(date('Y-m-d H:i:s'));

            // 休息1秒
            \Co::sleep(1);
        }
    }
}
```

二、注册秒级定时任务

项目根目录的 EasySwooleEvent.php 文件的 initialize 或者 mainServerCreate 方法中进行秒级定时任务注册。

```php
<?php

namespace EasySwoole\EasySwoole;

use App\Crontab\SecondCrontab;
use EasySwoole\EasySwoole\AbstractInterface\Event;
use EasySwoole\EasySwoole\Swoole\EventRegister;

class EasySwooleEvent implements Event
{
    public static function initialize()
    {
        date_default_timezone_set('Asia/Shanghai');

        ###### 注册秒级定时任务 ######
        $process = new SecondCrontab(new \EasySwoole\Component\Process\Config([
            'enableCoroutine' => true
        ]));
        \EasySwoole\Component\Process\Manager::getInstance()->addProcess($process);
    }

    public static function mainServerCreate(EventRegister $register)
    {

    }
}
```





## 问题

### 报错

初次运行时会报错：
```
Fatal error: Namespace declaration statement has to be the very first statement or after any declare call 
in the script in /var/www/html/easyswoole_test/vendor/bin/easyswoole on line 14
```

需要修改项目内文件 `/vendor/bin/easyswoole`  的头部内容。

```
#!/usr/bin/env php
<?php
```

把上面一行屏蔽掉：

```
<?php
//#!/usr/bin/env php
```



<br/><br/><br/><br/><br/>
## 参考资料

EasySwoole文档 <https://www.easyswoole.com/Preface/intro.html>

Swoole文档 <https://wiki.swoole.com>

EasySwoole 3.x 中文文档 <https://www.mianshigee.com/tutorial/easyswoole-3.x-zh/noobcourse-introduction.md>

