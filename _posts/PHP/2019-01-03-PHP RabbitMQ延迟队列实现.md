---
layout: post
categories: PHP
title: PHP RabbitMQ延迟队列实现
meta: 经常见到的“请在30分钟内付款，否则取消订单”，这里就是多用延迟队列实现的，下面看一下RabbitMQ延迟队列的实现。
---
* content
{:toc}

### 正文

经常见到的“请在30分钟内付款，否则取消订单”，这里就是多用延迟队列实现的，下面看一下RabbitMQ延迟队列的实现。

信息推送到延迟队列：

```
$message = [
    'ticket_id' => 10102
];
$message = json_encode($message);
// 消息生存时间(1000 = 1s)
$ttl = 30 * 60 * 1000;  
// 交换机名称
$exchange = 'delay';
// 路由名称
$routing = 'delay_ticket';

// RabbitMQ配置信息
$setting => [
    'host'     => 'dev.rabbitmq.demo.com',
    'port'     => '5672',
    'login'    => 'admin',
    'password' => '123+-*abc'
],

// RabbitMQ连接
$conn = new \AMQPConnection($setting);
$channel = new \AMQPChannel($conn);

$ex = new \AMQPExchange($channel);
$ex->setName($exchange);
$ex->setType(AMQP_EX_TYPE_DIRECT);
$ex->setFlags(AMQP_DURABLE);
$ex->declareExchange();

$argument = [
    'delivery_mode' => 2,
    'expiration' => $ttl
];

if ($ex->publish($message, $routing, 1, $argument)) {
    echo 'succ';
}
echo 'end';
```

例如我们在消费者中的配置是：

```
'CTicket' => [
    'queue' => 'queue_ticket',
    'exchange' => 'async',
    'routing' => 'ticket'
],
'DTicket' => [
    'queue' => 'queue_delay_ticket',
    'exchange' => 'delay',
    'routing' => 'delay_ticket',
    'x_exchange' => 'async',
    'x_routing' => 'ticket'
],
```

延迟队列消费者运行后，过了信息生存期后，会把delay_ticket路由到x_routing指向的ticket路由队列中，
然后由ticket路由的消费者来进行后续处理。

延迟队列并不是消费，而是对队列服务器上延迟队列绑定关系的声明，所以只需要跑一次脚本就可以了，不需要常驻运行。

<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ的几种应用场景 <https://blog.csdn.net/zyz511919766/article/details/41946521>

rabbitmq 实现延迟队列的两种方式 <https://blog.csdn.net/u014308482/article/details/53036770>

rabbitmq 延迟队列的实现 <https://blog.csdn.net/u012119576/article/details/74677835>

模拟器 <http://tryrabbitmq.com/>




