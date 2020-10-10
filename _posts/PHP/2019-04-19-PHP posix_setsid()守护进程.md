---
layout: post
categories: PHP
title: PHP posix_setsid()守护进程
meta: pcntl_fork()复制生成子进程，posix_setsid()守护进程，可以实现程序常驻
---
* content
{:toc}

### 正文

在*NIX下，是通过setsid()创建一个新的会话。但是值得注意的是，组长进程不能创建会话，
简单理解就是在组长进程中，执行setsid函数会报错，这点很重要。所以一般都是组长进程执行fork，然后主进程退出，
因为子进程的进程ID是新分配的，而子进程的进程组ID是继承父进程的，所以子进程就注定不可能是组长进程，
从而可以确保子进程中一定可以执行setsid函数。在执行setsid函数时候，一般会发生下面三个比较重要的事情：
  - 该进程会创建一个新的进程组，该进程为进程组组长（或者你可以认为这是一种提升）
  - 该进程会创建一个会话组并成为该会话的会话首进程（会话首进程就是创建该会话的进程）
  - 该进程会失去控制终端。如果该进程本来就没有控制终端，则罢了（liao）。如果有，那么该进程也将脱离该控制终端，与之失去联系。
  
关于多进程相关的内容可以参考[这里](https://ibaiyang.github.io/blog/php/2020/06/30/PHP-%E5%A4%9A%E8%BF%9B%E7%A8%8B%E5%88%9D%E6%8E%A2.html)

一般程序被调用时只执行一次就够了，但有些场景却需要一次调用，多次反复执行，比如队列消费者的实现。

看一下消费者的例子：
```php
<?php
namespace console\controllers;

use console\components\BaseController;
use Yii;

class ConsumerController extends BaseController
{
    /**
     * 项目根目录
     * @var string
     */
    private $root;

    /**
     * worker pidfile
     * @var string
     */
    private $pidfile;

    /**
     * worker进程pid
     * @var array
     */
    private $pids = [];

    /**
     * consumer
     * @var string
     */
    private $consumer;

    /**
     * rabbitMQ连接参数
     * @var array
     */
    private $setting = [];

    /**
     * worker对应queue,exchange,routing绑定配置
     * @var array
     */
    private $bindSetting = [];

    /**
     * worker实例
     * @var object
     */
    private $worker;

    /**
     * consumer初始化
     * @param $consumer
     */
    private function initConsumer($consumer)
    {
        $this->consumer = ucfirst($consumer);

        $this->root = Yii::$app->params['root'];

        $this->pidfile = Yii::$app->params['pidfile_root'] .$this->consumer;

        $this->setting = Yii::$app->queue->credentials;

        $this->bindSetting = Yii::$app->params['C' .$this->consumer];

        $this->pids = file_exists($this->pidfile)
            ? explode(',', file_get_contents($this->pidfile))
            : null;
    }

    /**
     * 启动consumer-worker
     * @param $consumer
     */
    public function actionStart($consumer)
    {
        $this->initConsumer($consumer);

        $pid = pcntl_fork();

        if ($pid == -1) {
            die('could not fork');
        } elseif ($pid) {
            exit('parent process');
        } else {
            posix_setsid();

            if (file_exists($this->pidfile)) {
                file_put_contents($this->pidfile, ',' .posix_getpid(), FILE_APPEND);
            } else {
                file_put_contents($this->pidfile, posix_getpid());
            }

            $this->msgQueue();
        }
    }

    /**
     * 停止consumer-worker
     * @param $consumer
     */
    public function actionStop($consumer)
    {
        $this->initConsumer($consumer);

        if (empty($this->pids)) {
            echo "\n" .'worker not start' ."\n";
            exit();
        }

        foreach ($this->pids as $pid)
        {
            posix_kill($pid, SIGTERM);
        }

        unlink($this->pidfile);

        echo "\n" .'Stop Success' ."\n";
    }

    /**
     * 重启consumer-worker
     * @param $consumer
     */
    public function actionRestart($consumer)
    {
        $this->actionStop($consumer);

        sleep(2);

        $this->actionStart($consumer);
    }

    /**
     * 连接MQ并消费
     */
    private function msgQueue()
    {
        $conn = new \AMQPConnection($this->setting);

        if (!$conn->connect()) {
            echo "\n" ."Connect Failed" ."\n";
            exit();
        }
        echo "\n" ."Connect Success" ."\n";

        $channel = new \AMQPChannel($conn);

        $ex = new \AMQPExchange($channel);
        $ex->setName($this->bindSetting['exchange']);
        $ex->setType(AMQP_EX_TYPE_DIRECT);
        $ex->setFlags(AMQP_DURABLE);
        $ex->declareExchange();

        $q = new \AMQPQueue($channel);
        $q->setName($this->bindSetting['queue']);
        $q->setFlags(AMQP_DURABLE);
        $q->declareQueue();

        $q->bind(
            $this->bindSetting['exchange'],
            $this->bindSetting['routing']
        );

        if (!file_exists($this->root . '/workers/' . $this->consumer . '.php')) {
            echo "\n" ."workerService does not exist" ."\n";
            exit();
        }
        $workerName = 'console\\workers\\' . $this->consumer;

        $this->worker = new $workerName();

        $channel->qos(0,1);
        $q->consume(array($this->worker, 'run'));

        $conn->disconnect();
    }
}
```

// ticket队列消费者开始运行
php yii consumer/start ticket

actionStart、actionStop就是完整的守护进程开始、运行、关闭。

消费者项目具体看github项目代码： <https://github.com/peachpear/peach-rabbitmq>

<br/><br/><br/><br/><br/>
### 参考资料

posix_setsid <https://php.net/manual/zh/function.posix-setsid.php>

pcntl_fork <https://www.php.net/manual/en/function.pcntl-fork.php>

记录php daemon 进程 遇到的问题--posix_setsid函数 <https://segmentfault.com/a/1190000005979154>

用php编写守护进程 <http://imhuchao.com/638.html>
	
php写守护进程（Daemon） <https://blog.csdn.net/tengzhaorong/article/details/9764655>

深入探讨pcntl_fork函数 <https://www.jianshu.com/p/22aca20c84e4>

PHP多进程-- pcntl_fork实现 <https://www.cnblogs.com/aiweixiao/p/6425653.html>

pcntl_fork() 执行过程详解 <https://www.cnblogs.com/php-linux/p/7070865.html>

linux系统编程之进程（八）：守护进程详解及创建，daemon()使用 <http://www.cnblogs.com/mickole/p/3188321.html>

守护进程以及PHP的实现 <https://yuanxuxu.com/2013/11/24/shou-hu-jin-cheng-yi-ji-phpde-shi-xian--2/>



