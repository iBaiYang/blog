---
layout: post
categories: PHP
title: PHP RabbitMQ相关
meta: RabbitMQ相关
---
* content
{:toc}

### 正文

#### 简单介绍

**什么叫消息队列?**

消息（Message）是指在应用间传送的数据。消息可以非常简单，比如只包含文本字符串，也可以更复杂，可能包含嵌入对象。

消息队列（Message Queue）是一种应用间的通信方式，消息发送后可以立即返回，由消息系统来确保消息的可靠传递。
消息发布者只管把消息发布到 MQ 中而不用管谁来取，消息使用者只管从 MQ 中取消息而不管是谁发布的。这样发布者和使用者都不用知道对方的存在。

**为何用消息队列？**

从上面的描述中可以看出消息队列是一种应用间的异步协作机制。

那什么时候需要使用 MQ 呢？

以常见的订单系统为例，用户点击【下单】按钮之后的业务逻辑可能包括：扣减库存、生成相应单据、发红包、发短信通知。
在业务发展初期这些逻辑可能放在一起同步执行，随着业务的发展订单量增长，需要提升系统服务的性能，
这时可以将一些不需要立即生效的操作拆分出来异步执行，比如发放红包、发短信通知等。这种场景下就可以用 MQ ，
在下单的主流程（比如扣减库存、生成相应单据）完成之后发送一条消息到 MQ 让主流程快速完结，
而由另外的单独线程拉取MQ的消息（或者由 MQ 推送消息），当发现 MQ 中有发红包或发短信之类的消息时，执行相应的业务逻辑。

以上是用于业务解耦的情况，其它常见场景包括最终一致性、广播、错峰流控等等。

**RabbitMQ 特点**

RabbitMQ 是一个由 Erlang 语言开发的 AMQP 的开源实现。

AMQP ：Advanced Message Queue Protocol，高级消息队列协议。它是应用层协议的一个开放标准，为面向消息的中间件设计，
基于此协议的客户端与消息中间件可传递消息，并不受产品、开发语言等条件的限制。

RabbitMQ 最初起源于金融系统，用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。具体特点包括：

* 可靠性（Reliability）

RabbitMQ 使用一些机制来保证可靠性，如持久化、传输确认、发布确认。

* 灵活的路由（Flexible Routing）

在消息进入队列之前，通过 Exchange 来路由消息的。对于典型的路由功能，RabbitMQ 已经提供了一些内置的 Exchange 来实现。针对更复杂的路由功能，可以将多个 Exchange 绑定在一起，也通过插件机制实现自己的 Exchange 。

* 消息集群（Clustering）

多个 RabbitMQ 服务器可以组成一个集群，形成一个逻辑 Broker 。

* 高可用（Highly Available Queues）

队列可以在集群中的机器上进行镜像，使得在部分节点出问题的情况下队列仍然可用。

* 多种协议（Multi-protocol）

RabbitMQ 支持多种消息队列协议，比如 STOMP、MQTT 等等。

* 多语言客户端（Many Clients）

RabbitMQ 几乎支持所有常用语言，比如 Java、.NET、Ruby 等等。

* 管理界面（Management UI）

RabbitMQ 提供了一个易用的用户界面，使得用户可以监控和管理消息 Broker 的许多方面。

* 跟踪机制（Tracing）

如果消息异常，RabbitMQ 提供了消息跟踪机制，使用者可以找出发生了什么。

* 插件机制（Plugin System）

RabbitMQ 提供了许多插件，来从多方面进行扩展，也可以编写自己的插件。


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

Message: 消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，
这些属性包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等。

Publisher: 消息的生产者，也是一个向交换器发布消息的客户端应用程序。

Broker: 表示消息队列服务器实体。 接收和分发消息的应用，RabbitMQ Server就是Message Broker。

Virtual host: 虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。
每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。
vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。
出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，
类似于网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ server提供的服务时，
可以划分出多个vhost，每个用户在自己的vhost创建exchange／queue等。

Connection: 网络连接，比如一个TCP连接。publisher／consumer和broker之间的TCP连接。断开连接的操作只会在client端进行，
Broker不会断开连接，除非出现网络故障或broker服务出现问题。

Channel: 信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，
不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，
所以引入了信道的概念，以复用一条 TCP 连接。
如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。
Channel是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，
AMQP method包含了channel id帮助客户端和message broker识别channel，所以channel之间是完全隔离的。
Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销。

Exchange: 交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。
message到达broker的第一站，根据分发规则，匹配查询表中的routing key，
分发消息到queue中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout (multicast)。

Queue: 消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。
消息一直在队列里面，等待消费者连接到这个队列将其取走。消息最终被送到这里等待consumer取走。一个message可以被同时拷贝到多个queue中。

Binding: 绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，
所以可以将交换器理解成一个由绑定构成的路由表。exchange和queue之间的虚拟连接，binding中可以包含routing key。
Binding信息被保存到exchange中的查询表中，用于message的分发依据。

Consumer: 消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

#### 典型的“生产／消费”消息模型

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20190418/20190418182644.jpg)

生产者发送消息到broker server（RabbitMQ）。
在Broker内部，用户创建Exchange／Queue，通过Binding规则将两者联系在一起。
Exchange分发消息，根据类型 binding 的不同分发策略有区别。消息最后来到Queue中，等待消费者取走。
生产者把消息发布到 Exchange 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到那个队列。

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

PHP RabbitMQ延迟队列实现 <https://ibaiyang.github.io/blog/php/2019/01/03/PHP-RabbitMQ%E5%BB%B6%E8%BF%9F%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0.html>

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

RabbitMQ官方中文入门教程(PHP版) Demo 源码 <https://github.com/yuansir/rabbitmq-tutorial-php-demo>

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



