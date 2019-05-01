---
layout: post
categories: PHP
title: PHP RabbitMQ相关
meta: RabbitMQ相关
---
* content
{:toc}

### 正文

#### 引入举例

先看一个生产者的例子：
```
$message = [
    'ticket_id' => 10102
];
$message = json_encode($message);
// 交换机名称，自己命名
$exchange = 'async';
// 路由名称，自己命名
$routing = 'ticket';

// RabbitMQ配置信息
$setting => [
    'host'     => 'dev.rabbitmq.demo.com',
    'port'     => '5672',
    'login'    => 'admin',
    'password' => '123+-*abc'
],

// RabbitMQ连接
$conn = new \AMQPConnection($setting);  // 创建对象
$channel = new \AMQPChannel($conn);  // 创建channel
$ex = new \AMQPExchange($channel);  // 创建交换机
$ex->setName($exchange);  // 创建交换机名字 
$ex->setType(AMQP_EX_TYPE_DIRECT);  // 设置交换机类型
$ex->setFlags(AMQP_DURABLE);  // 设置模式
$ex->declareExchange();

// 发布消息到交换机并指定路由关键字
if ($ex->publish($message, $routing, 1)) {
    echo 'succ';
}
echo 'end';
```

这里就是生产者发送信息到队列的实现。

在RabbitMQ中定义 名为async的Exchange交换机，
在这个交换机上Binding绑定名为ticket的binding key虚拟连接到名为queue_ticket的Queue队列。

然后我们在生产者这端就可以声明AMQP高级信息队列协议对象并连接到目标Exchange交换机，
接着把要发送的信息连同routing key发送到目标Exchange交换机，
目标交换机再把这条信息发送到交换机中binding key等于信息中routing key的目标Queue队列中，
现在信息就在目标队列中了。

再看消费者的例子：
```
// 连接名称，自己命名
$bindingkey = 'ticket';

// RabbitMQ配置信息
$setting => [
    'host'     => 'dev.rabbitmq.demo.com',
    'port'     => '5672',
    'login'    => 'admin',
    'password' => '123+-*abc'
],

// RabbitMQ连接
$conn = new \AMQPConnection($setting);  // 创建对象
$conn->connect();
$channel = new \AMQPChannel($conn);  // 创建channel
$q = new AMQPQueue($channel);  // 创建队列
$q->setName('queue_ticket');  // 设置队列名称
$q->setFlags(AMQP_DURABLE);　　// 设置模式
$q->declare();
$q->bind('exchange', $bindingkey);  // 绑定交换机
$messages = $q->get(AMQP_AUTOACK);  // 获取消息
// 处理消息
if ($messages) {
    var_dump(json_decode($messages->getBody(), true));
}
$conn->disconnect();  // 断开连接
```

#### AMQP messaging 中的基本概念

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418181901.jpg)

Broker: 接收和分发消息的应用，RabbitMQ Server就是Message Broker。

Virtual host: 出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，
类似于网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ server提供的服务时，
可以划分出多个vhost，每个用户在自己的vhost创建exchange／queue等。

Connection: publisher／consumer和broker之间的TCP连接。断开连接的操作只会在client端进行，
Broker不会断开连接，除非出现网络故障或broker服务出现问题。

Channel: 如果每一次访问RabbitMQ都建立一个Connection，
在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。
Channel是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，
AMQP method包含了channel id帮助客户端和message broker识别channel，所以channel之间是完全隔离的。
Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销。

Exchange: message到达broker的第一站，根据分发规则，匹配查询表中的routing key，
分发消息到queue中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout (multicast)。

Queue: 消息最终被送到这里等待consumer取走。一个message可以被同时拷贝到多个queue中。

Binding: exchange和queue之间的虚拟连接，binding中可以包含routing key。
Binding信息被保存到exchange中的查询表中，用于message的分发依据。

#### 典型的“生产／消费”消息模型

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418182644.jpg)

生产者发送消息到broker server（RabbitMQ）。
在Broker内部，用户创建Exchange／Queue，通过Binding规则将两者联系在一起。
Exchange分发消息，根据类型／binding的不同分发策略有区别。消息最后来到Queue中，等待消费者取走。

#### Exchange类型

Exchange有多种类型，最常用的是Direct／Topic／Fanout三种类型。

Direct (point-to-point)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418182946.jpg)

Message中的“routing key”如果和Binding中的“binding key”一致， Direct exchange则将message发到对应的queue中。

Topic (publish-subscribe)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418183016.jpg)

根据routing key，及通配规则，Topic exchange将分发到目标queue中。Routing key中可以包含两种通配符，类似于正则表达式：
```
“#” 通配任何零个或多个word
“*” 通配任何单个word
```

Fanout (multicast)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418182958.jpg)

每个发到Fanout类型Exchange的message都会分到所有绑定的queue上去。


#### 生产者封装类

一般我们都会对使用方法进行封装，看一下Yii2中的封装类：

```php
<?php
namespace common\widgets;

use Yii;

class Queue 
{
    /**
     * 日志进队
     * @param $message
     * @param $exchange
     * @param $routing
     * @return bool
     */
    public static function produceLogs($message, $exchange, $routing)
    {
        try {
            $conn = new \AMQPConnection(Yii::$app->params['queue']);

            if (!$conn->connect()) {
                return false;
            }

            $message = json_encode($message);

            $channel = new \AMQPChannel($conn);
            $ex = new \AMQPExchange($channel);
            $ex->setName($exchange);
            $ex->setType(AMQP_EX_TYPE_DIRECT);
            $ex->setFlags(AMQP_DURABLE);

            $ex->declareExchange();

            if (!$ex->publish($message, $routing, 1)) {
                return false;
            }

            return true;

        } catch (\Throwable $ex) {
            return true;
        }
    }


    /**
     * 通用进队方法
     * 消息持久化
     * @param $message
     * @param $exchange
     * @param $routing
     * @param int $isPersistent  
     * @return bool
     */
    public static function produce($message, $exchange, $routing, $isPersistent = 1)
    {
        $conn = new \AMQPConnection(Yii::$app->params['queue']);

        if (!$conn->connect()){
            return false;
        }

        $message = json_encode($message);

        $channel = new \AMQPChannel($conn);
        $ex = new \AMQPExchange($channel);
        $ex->setName($exchange);
        $ex->setType(AMQP_EX_TYPE_DIRECT);
        $ex->setFlags(AMQP_DURABLE);

        $ex->declareExchange();

        if ($isPersistent == 1) {
            if (!$ex->publish($message,$routing,1, ['delivery_mode' => 2])) {
                return false;
            }
        } else {
            if (!$ex->publish($message, $routing, 1)) {
                return false;
            }
        }

        return true;
    }

    /**
     * 通用批量进队方法,消息持久化
     * @param $messageList
     * @param $exchange
     * @param $routing
     * @param int $isPersistent  
     * @return bool
     */
    public static function batchProduce($messageList, $exchange, $routing, $isPersistent = 1)
    {
        try {
            $conn = new \AMQPConnection(Yii::$app->params['queue']);

            if (!$conn->connect()) {
                return false;
            }

            $channel = new \AMQPChannel($conn);
            $ex = new \AMQPExchange($channel);
            $ex->setName($exchange);
            $ex->setType(AMQP_EX_TYPE_DIRECT);
            $ex->setFlags(AMQP_DURABLE);

            $ex->declareExchange();

            foreach ($messageList as $message)
            {
                $messageJson = json_encode($message);

                if ($isPersistent == 1) {
                    if (!$ex->publish($messageJson, $routing, 1, ['delivery_mode' => 2])) {
                        return false;
                    }
                } else {
                    if (!$ex->publish($messageJson, $routing, 1)) {
                        return false;
                    }
                }
            }

            return true;
        } catch (\Throwable $ex) {
            return true ;
        }
    }
}
```

使用举例：
```
Queue::produce(['indexname' => 'weberror', 'type' => 'adminWeb', 'error' => 303020], 'logstash', 'app_logs_routing');
Queue::produce(['id' => 101], 'async', 'ticket');
```

#### 消费者封装类

具体看github项目代码： <https://github.com/peachpear/peach-rabbitmq>

其中消费部分：
```
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
```

参数举例：
```
$this->setting = [
    'host' => 'test.rabbitmq.demo.com',
    'port' => '5672',
    'login' => 'mqadmin',
    'password' => 'demo_+-*123'
];

$this->bindSetting = [
    'queue' => 'queue_ticket',
    'exchange' => 'async',
    'routing' => 'ticket'
];
```

run方法实现：
```
// console\workers\Ticket->run()
public function run($envelope, $queue)
{
    $msg = $envelope->getBody();
    $msg = json_decode($msg, true);
    $event = isset($msg['event']) ?  $msg['event'] : '';

    switch ($event) {
        case 'ticket_callback':
            TicketService::callbackRequest($msg['data']);
            break;
        default:
            break;
    }

    Yii::$app->demoDB->close();
    $queue->ack($envelope->getDeliveryTag());
}
```

再看一下延迟队列消费者的实现：
```
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
    $q->setArguments([
        'x-dead-letter-exchange' => $this->bindSetting['x_exchange'],
        'x-dead-letter-routing-key' => $this->bindSetting['x_routing']
    ]);
    $q->declareQueue();

    $q->bind(
        $this->bindSetting['exchange'],
        $this->bindSetting['routing']
    );

    $conn->disconnect();
}
```

参数举例：
```
$this->setting = [
    'host' => 'test.rabbitmq.demo.com',
    'port' => '5672',
    'login' => 'mqadmin',
    'password' => 'demo_+-*123'
];

$this->bindSetting = [
    'queue' => 'queue_delay_ticket',
    'exchange' => 'delay',
    'routing' => 'delay_ticket',
    'x_exchange' => 'async',
    'x_routing' => 'ticket'
];
```

#### 其他说明

命令行运行消费者，需要常驻进程，就需要守护进程程序，涉及到 pcntl_fork()、posix_setsid() 函数。

<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ与AMQP协议详解 <http://www.cnblogs.com/frankyou/p/5283539.html>

PHP RabbitMQ延迟队列实现 <https://ibaiyang.github.io/ibaiyang.github.io/php/2019/01/03/PHP-RabbitMQ%E5%BB%B6%E8%BF%9F%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0.html>

模拟器 <http://tryrabbitmq.com/>

安装 RabbitMQ – centos 6  <http://www.fancyecommerce.com/2017/06/02/%e5%ae%89%e8%a3%85-rabbitmq-centos-6/>

rabbitmq php 安装amqp扩展 <http://www.fancyecommerce.com/2017/06/02/rabbitmq-php-%e5%ae%89%e8%a3%85amqp%e6%89%a9%e5%b1%95/>

谈谈 API 网关 <https://www.jianshu.com/p/b52a2773e75f?utm_campaign=maleskine&utm_content=note&utm_medium=pc_all_hots&utm_source=recommendation>

消息队列之 RabbitMQ <https://www.jianshu.com/p/79ca08116d57>

RabbitMQ简介，各种MQ选型对比 <https://www.sojson.com/blog/48.html>

PHP中的AMQP类 <https://blog.csdn.net/csdn265/article/details/75370398>

RabbitMQ官方中文入门教程(PHP版) 第一部分:Hello World  <https://yuanxuxu.com/2013/05/31/rabbitmqguan-fang-zhong-wen-ru-men-jiao-cheng-phpban--di-yi-bu-fen-hello-world/>

RabbitMQ官方中文入门教程(PHP版) 第二部分:工作队列（Work queues） <https://yuanxuxu.com/2013/05/31/rabbitmqguan-fang-zhong-wen-ru-men-jiao-cheng-phpban--di-er-bu-fen-gong-zuo-dui-lie--work-queues/>

RabbitMQ官方中文入门教程(PHP版) 第三部分:发布／订阅（Publish/Subscribe） <https://yuanxuxu.com/2013/05/31/rabbitmqguan-fang-zhong-wen-ru-men-jiao-cheng-phpban--di-san-bu-fen-fa-bu--ding-yue--publishsubscr/>

RabbitMQ官方中文入门教程(PHP版) 第四部分:路由(Routing) <https://yuanxuxu.com/2013/05/31/rabbitmqguan-fang-zhong-wen-ru-men-jiao-cheng-phpban--di-si-bu-fen-lu-you-routing/>

rabbitmq-tutorial-php-demo <https://github.com/yuansir/rabbitmq-tutorial-php-demo>

初识RabbitMQ,附RabbitMQ+PHP演示实例 <https://www.cnblogs.com/miketwais/p/RabbitMQ.html>

RabbitMQ入门教程 <https://blog.fundebug.com/2018/04/20/rabbitmq_tutorial/>

使用 RabbitMQ 实现 RPC <https://zhuanlan.zhihu.com/p/48230422?utm_source=weibo&utm_medium=social&utm_oi=64577924497408&utm_content=snapshot>

RabbitMQ PHP客户端AMQP　DEMO <https://zhuanlan.zhihu.com/p/25504430>

基于AMQP实现的golang消息队列MaxQ <https://zhuanlan.zhihu.com/p/28967866>

RabbitMQ+PHP 消息队列环境配置 <https://www.cnblogs.com/chunguang/p/5634342.html>

什么是“堆”,"栈","堆栈","队列",它们的区别？ <https://jingyan.baidu.com/article/6c67b1d6a09f9a2786bb1e4a.html>

消息中间件选型分析——从Kafka与RabbitMQ的对比来看全局 <https://blog.csdn.net/u013256816/article/details/79838428>

kafka介绍 <https://www.cnblogs.com/hei12138/p/7805475.html>

RabbitMQ教程之php-amqplib（一）安装RabbitMQ <https://blog.csdn.net/demon3182/article/details/77335123>

RabbitMQ教程之php-amqplib（二）简介、入门 <https://blog.csdn.net/demon3182/article/details/77335206>

PHP-AMQP实践应用 <http://blog.sina.com.cn/s/blog_695b12c601019lvx.html>

PHP中使用AMQP消息队列 使用举例 <https://blog.csdn.net/taohaoge/article/details/9878751>

手把手教你搭建PHP版RabbitMQ消息队列开发环境及Demo实践 <http://mdsa.51cto.com/art/201707/545193.htm>

windows下安装rabbitmq的php扩展amqp <https://blog.haohtml.com/tag/amqp>

wamp2.4+composer+rabbitmq环境部署 <https://blog.51cto.com/daddysgirl/1436412>

MQ对比之RabbitMQ & Redis <https://zhuanlan.zhihu.com/p/41850085?utm_source=weibo&utm_medium=social&utm_oi=64577924497408&utm_content=snapshot>

RabbitMQ 消息顺序、消息幂等、消息重复、消息事务、集群 <https://www.cnblogs.com/xishuai/p/9174719.html>

RabbitMQ的几种应用场景 <https://blog.csdn.net/zyz511919766/article/details/41946521>





