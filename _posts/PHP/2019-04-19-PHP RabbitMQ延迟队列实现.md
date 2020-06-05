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

延迟队列声明：
```
// 交换机名称
$exchange = 'delay';
// 路由名称
$routing = 'delay_ticket';

$setting[
    "queue" => "queue_delay_ticket",
    "x_exchange" => "async",
    "x_routing" => "queue_ticket",
];

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

$q = new \AMQPQueue($channel);
$q->setName($setting['queue']);
$q->setFlags(AMQP_DURABLE);

$q->setArguments([
    'x-dead-letter-exchange' => $setting['x_exchange'],
    'x-dead-letter-routing-key' => $setting['x_routing']
]);
$q->declareQueue();

$q->bind($exchange, $routing);
```

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

#### 循环实现

不过上面的延迟队列中，每个只能有一种延迟时长，如果前面的一条队列数据没超时变为死信，则会阻塞后面的队列数据。
除了新建新的延迟时长的延迟队列，我们还有一种可通用的解决思路：新建1个实时队列 、 3个延迟队列（1小时、1分钟、1秒），
这3个延迟队列的死信都发送到上面的实时队列中。我们首先把要处理的数据推到 实时队列，实时队列消费者根据消息体中发送时间选择推送到哪一个延迟队列，
延迟队列数据过了时长再发送到实时队列，实时队列再根据距离时间选择推送到哪一个延迟队列，直到时间到了这条信息的处理时间，再把这条信息推送到目标队列。
如A项目中有一条信息需要在2小时15分23秒后处理，A项目先把信息推送到 实时队列loop_transfer_wait_data，
实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1小时，
则把这条信息转发到 1小时延迟队列delay_loop_transfer_hour_x 中，等1小时后这条信息成为死信又进入 实时队列loop_transfer_wait_data，
实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1小时，再来一次循环，
实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1分钟，则把这条信息转发到 1分钟延迟队列delay_loop_transfer_minute_x 中，
等1分钟后这条信息成为死信又进入 实时队列loop_transfer_wait_data，实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1分钟，再次循环，
经历15次循环后，实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1秒，则把这条信息转发到 1秒延迟队列delay_loop_transfer_second_x 中，
等1秒后这条信息成为死信又进入 实时队列loop_transfer_wait_data，实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间大于1秒，再次循环，
经历23次循环后，实时队列loop_transfer_wait_data 的消费者发现这条信息的处理截止时间就是现在，再把这条信息推送到我们的目标队列。

下面是代码实例，购物信息1.5小时优惠截止：

```
$msg = ["shipping_process_id" => $shipping_process_id, "seconds" => 1.5 * 60 * 60];
$data = [
    "transfer_exchange" => "async",
    "transfer_routing" => "shipping_confirm",
    "transfer_message" => $msg,
    "transfer_time" => time() + (1.5 * 60 * 60),
];
Queue::produce($data, 'async', 'loop_transfer_wait_data');
```

loop_transfer_wait_data消费者：
```
$now = time();
if ($data['transfer_time'] <= $now) {
    return Queue::produce($data['transfer_message'], $data['transfer_exchange'], $data['transfer_routing']);
} else {
    if (!isset($data['transfer_id'])) {
        $data['transfer_id'] = uniqid() . mt_rand(100000, 999999);      //标识同一条轮转消息
        $data['transfer_count'] = 1;
    } else {
        $data['transfer_count'] ++;
    }
    $wait = $data['transfer_time'] - $now;
    $hour = floor($wait / (60 * 60));
    
    if ($hour > 0) {
        // 转至延迟队列等待，以1小时为维度
        return Queue::produceTtl($data, 'delay', 'delay_loop_transfer_hour_x', 60 * 60 * 1000);
    }
    
    $minute = floor($wait / 60);
    if ($minute > 0) {
        // 转至延迟队列等待，以1分钟为维度
        return Queue::produceTtl($data, 'delay', 'delay_loop_transfer_minute_x', 60  * 1000);
    }
    
    // 转至延迟队列等待，以1秒为维度
    return Queue::produceTtl($data, 'delay', 'delay_loop_transfer_second_x', 1000);
}
```



<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ的几种应用场景 <https://blog.csdn.net/zyz511919766/article/details/41946521>

rabbitmq 实现延迟队列的两种方式 <https://blog.csdn.net/u014308482/article/details/53036770>

rabbitmq 延迟队列的实现 <https://blog.csdn.net/u012119576/article/details/74677835>

模拟器 <http://tryrabbitmq.com/>




