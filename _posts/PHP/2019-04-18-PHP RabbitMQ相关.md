---
layout: post
categories: PHP
title: PHP RabbitMQ相关
meta: RabbitMQ相关
---
* content
{:toc}

### 正文

![]({{site.baseurl}}/images/20191115/20191115175726.png)

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

##### 生产者

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

// RabbitMQ连接配置信息
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

new \AMQPConnection($setting)，创建一个AMQPConnection实例。该实例表示与AMQP代理的连接：
```
/**
 * Create an instance of AMQPConnection.
 *
 * Creates an AMQPConnection instance representing a connection to an AMQP
 * broker. A connection will not be established until
 * AMQPConnection::connect() is called.
 *
 *  $credentials = array(
 *      'host'  => amqp.host The host to connect too. Note: Max 1024 characters.
 *      'port'  => amqp.port Port on the host.
 *      'vhost' => amqp.vhost The virtual host on the host. Note: Max 128 characters.
 *      'login' => amqp.login The login name to use. Note: Max 128 characters.
 *      'password' => amqp.password Password. Note: Max 128 characters.
 *      'read_timeout'  => Timeout in for income activity. Note: 0 or greater seconds. May be fractional.
 *      'write_timeout' => Timeout in for outcome activity. Note: 0 or greater seconds. May be fractional.
 *      'connect_timeout' => Connection timeout. Note: 0 or greater seconds. May be fractional.
 *
 *      Connection tuning options (see https://www.rabbitmq.com/amqp-0-9-1-reference.html#connection.tune for details):
 *      'channel_max' => Specifies highest channel number that the server permits. 0 means standard extension limit
 *                       (see PHP_AMQP_MAX_CHANNELS constant)
 *      'frame_max'   => The largest frame size that the server proposes for the connection, including frame header
 *                       and end-byte. 0 means standard extension limit (depends on librabbimq default frame size limit)
 *      'heartbeat'   => The delay, in seconds, of the connection heartbeat that the server wants.
 *                       0 means the server does not want a heartbeat. Note, librabbitmq has limited heartbeat support,
 *                       which means heartbeats checked only during blocking calls.这个参数可以设置延长心跳检查时间
 *
 *      TLS support (see https://www.rabbitmq.com/ssl.html for details):
 *      'cacert' => Path to the CA cert file in PEM format..
 *      'cert'   => Path to the client certificate in PEM foramt.
 *      'key'    => Path to the client key in PEM format.
 *      'verify' => Enable or disable peer verification. If peer verification is enabled then the common name in the
 *                  server certificate must match the server name. Peer verification is enabled by default.
 * )
 *
 * @param array $credentials Optional array of credential information for
 *                           connecting to the AMQP broker.
 */
public function __construct(array $credentials = array()) { }
```

这里说到 在调用AMQPConnection::connect（）之前，不会建立连接。所以我们一般的做法是：
```
$conn = new \AMQPConnection($setting); 
if (!$conn->->isConnected()) {
    $conn->connect();
}
```

```
/**
 * Establish a transient connection with the AMQP broker.
 *
 * This method will initiate a connection with the AMQP broker.
 *
 * @throws AMQPConnectionException
 * @return boolean TRUE on success or throw an exception on failure.
 */
public function connect() { }

/**
 * Check whether the connection to the AMQP broker is still valid.
 *
 * It does so by checking the return status of the last connect-command.
 *
 * @return boolean True if connected, false otherwise.
 */
public function isConnected() { }
```

`new \AMQPChannel($conn)`，创建一个AMQPChannel对象的实例，`AMQPConnection $amqp_connection` 参数是与代理的活动连接的AMQPConnection的实例：
```
/**
 * Create an instance of an AMQPChannel object.
 *
 * @param AMQPConnection $amqp_connection An instance of AMQPConnection
 *                                        with an active connection to a broker.
 *
 * @throws AMQPConnectionException        If the connection to the broker was lost.
 */
public function __construct(AMQPConnection $amqp_connection) { }
```

`new \AMQPExchange($channel)`，创建一个AMQPExchange实例，返回与给定的AMQPChannel对象关联的AMQPExchange对象的新实例：
 ```
/**
 * Create an instance of AMQPExchange.
 *
 * Returns a new instance of an AMQPExchange object, associated with the
 * given AMQPChannel object.
 *
 * @param AMQPChannel $amqp_channel A valid AMQPChannel object, connected to a broker.
 *                                  
 * @throws AMQPExchangeException   When amqp_channel is not connected to a broker.                           
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *                                 
 */
public function __construct(AMQPChannel $amqp_channel) { }
```

`$ex->setName($exchange)`，设置交换机名字：
```
/**
 * Set the name of the exchange.
 *
 * @param string $exchange_name The name of the exchange to set as string.
 *
 * @return void
 */
public function setName($exchange_name) { }
```

`$ex->setType()` 设置交换机类型，有direct、fanout、topic三种类型，php中这里定义的常量为 AMQP_EX_TYPE_DIRECT 、 AMQP_EX_TYPE_FANOUT 、 AMQP_EX_TYPE_TOPIC，待会下面会详细解说：
```
/**
 * Set the type of the exchange.
 *
 * Set the type of the exchange. This can be any of AMQP_EX_TYPE_DIRECT,
 * AMQP_EX_TYPE_FANOUT, AMQP_EX_TYPE_HEADERS or AMQP_EX_TYPE_TOPIC.
 *
 * @param string $exchange_type The type of exchange as a string.
 *
 * @return void
 */
public function setType($exchange_type) { }
```

`$ex->setFlags()` 设置交换机模式：
```
/**
 * Set the flags on an exchange.
 *
 * @param integer $flags A bitmask of flags. This call currently only
 *                       considers the following flags:
 *                       AMQP_DURABLE, AMQP_PASSIVE
 *                       (and AMQP_DURABLE, if librabbitmq version >= 0.5.3)
 *
 * @return void
 */
public function setFlags($flags) { }
```

交换机模式有 AMQP_DURABLE, AMQP_PASSIVE 两种，表示了如果MQ服务器重启,这个交换机是否要重新建立(如果设置为AMQP_DURABLE，则重启mq，该交换机还是存在，相当于持久化。)

AMQP_DURABLE，Durable exchanges and queues will survive a broker restart, complete with all of their data. 
持久的交换和队列将在代理重新启动后幸存下来，并保留所有数据。

AMQP_PASSIVE，Passive exchanges and queues will not be redeclared, but the broker will throw an error if the exchange or queue does not exist.
不会重新声明被动交换和队列，但是如果交换或队列不存在，则代理将引发错误。

`$ex->declareExchange()`，在broker代理上申明一个新的交换机：
```
/**
 * Declare a new exchange on the broker.
 *
 * @throws AMQPExchangeException   On failure.
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return boolean TRUE on success or FALSE on failure.
 */
public function declareExchange() { }
```

`$ex->publish()` 将信息发布到交换机，将消息发布到由AMQPExchange对象表示的交换机：
```
/**
 * Publish a message to an exchange.
 *
 * Publish a message to the exchange represented by the AMQPExchange object.
 *
 * @param string  $message     The message to publish.
 * @param string  $routing_key The optional routing key to which to
 *                             publish to.
 * @param integer $flags       One or more of AMQP_MANDATORY and
 *                             AMQP_IMMEDIATE.
 * @param array   $attributes  One of content_type, content_encoding,
 *                             message_id, user_id, app_id, delivery_mode,
 *                             priority, timestamp, expiration, type
 *                             or reply_to, headers.
 *
 * @throws AMQPExchangeException   On failure.
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return boolean TRUE on success or FALSE on failure.
 */
public function publish(
    $message,
    $routing_key = null,
    $flags = AMQP_NOPARAM,
    array $attributes = array()
) {
}
```

`$message` 要发布的信息体。

`$routing_key` 要发布到的可选路由关键字。

`$flags` ：
```
/**
 * Passing in this constant as a flag will forcefully disable all other flags.
 * Use this if you want to temporarily disable the amqp.auto_ack ini setting.
 */
define('AMQP_NOPARAM', 0);

/**
 * Passing in this constant as a flag to proper methods will forcefully ignore all other flags.
 * Do not send basic.consume request during AMQPQueue::consume(). Use this if you want to run callback on top of previously
 * declared consumers.
 */
define('AMQP_JUST_CONSUME', 1);

/**
 * Durable exchanges and queues will survive a broker restart, complete with all of their data.
 */
define('AMQP_DURABLE', 2);

/**
 * Passive exchanges and queues will not be redeclared, but the broker will throw an error if the exchange or queue does not exist.
 */
define('AMQP_PASSIVE', 4);

/**
 * Valid for queues only, this flag indicates that only one client can be listening to and consuming from this queue.
 */
define('AMQP_EXCLUSIVE', 8);

/**
 * For exchanges, the auto delete flag indicates that the exchange will be deleted as soon as no more queues are bound
 * to it. If no queues were ever bound the exchange, the exchange will never be deleted. For queues, the auto delete
 * flag indicates that the queue will be deleted as soon as there are no more listeners subscribed to it. If no
 * subscription has ever been active, the queue will never be deleted. Note: Exclusive queues will always be
 * automatically deleted with the client disconnects.
 */
define('AMQP_AUTODELETE', 16);

/**
 * Clients are not allowed to make specific queue bindings to exchanges defined with this flag.
 */
define('AMQP_INTERNAL', 32);

/**
 * When passed to the consume method for a clustered environment, do not consume from the local node.
 */
define('AMQP_NOLOCAL', 64);

/**
 * When passed to the {@link AMQPQueue::get()} and {@link AMQPQueue::consume()} methods as a flag,
 * the messages will be immediately marked as acknowledged by the server upon delivery.
 */
define('AMQP_AUTOACK', 128);

/**
 * Passed on queue creation, this flag indicates that the queue should be deleted if it becomes empty.
 */
define('AMQP_IFEMPTY', 256);

/**
 * Passed on queue or exchange creation, this flag indicates that the queue or exchange should be
 * deleted when no clients are connected to the given queue or exchange.
 */
define('AMQP_IFUNUSED', 512);

/**
 * When publishing a message, the message must be routed to a valid queue. If it is not, an error will be returned.
 */
define('AMQP_MANDATORY', 1024);

/**
 * When publishing a message, mark this message for immediate processing by the broker. (High priority message.)
 */
define('AMQP_IMMEDIATE', 2048);

/**
 * If set during a call to {@link AMQPQueue::ack()}, the delivery tag is treated as "up to and including", so that multiple
 * messages can be acknowledged with a single method. If set to zero, the delivery tag refers to a single message.
 * If the AMQP_MULTIPLE flag is set, and the delivery tag is zero, this indicates acknowledgement of all outstanding
 * messages.
 */
define('AMQP_MULTIPLE', 4096);

/**
 * If set during a call to {@link AMQPExchange::bind()}, the server will not respond to the method.The client should not wait
 * for a reply method. If the server could not complete the method it will raise a channel or connection exception.
 */
define('AMQP_NOWAIT', 8192);

/**
 * If set during a call to {@link AMQPQueue::nack()}, the message will be placed back to the queue.
 */
define('AMQP_REQUEUE', 16384);
```

`array $attributes = array()`， 可以设置一些参数：

| 配置项   | 类型	| 说明 | 
| --------   | :-----  | :----  |
| content_type	| 短文本	| MIME类型表示消息是一种什么类型的格式,参考[MIME类型](https://baike.baidu.com/item/MIME/2900607)，一般为text/plain | 
| content_encoding		| 短文本		| 正文传输编码,比如内容是gzip压缩的.值就是gzip,[参考](https://www.tuicool.com/articles/b6BNNfN)，一般为UTF-8 | 
| application_headers		|  数组 	| 	请求的headers信息 | 
| delivery_mode		| 数字 	| 	表示是否持久化,1为否,2为是 [参考](https://www.cnblogs.com/xiazh/archive/2011/04/29/2004859.html)   | 
| priority		| 数字 	| 	发送权重,也就是优先级  | 
| correlation_id	| 短文本		| 相关性ID [参考](http://www.01happy.com/python-rabbitmq-rfc-correlation-id/)  | 
| reply_to	| 	短文本	| 	消息被发送者处理完后,返回回复时执行的回调(在rpc时会用到)  | 
| expiration		| 短文本	|    存活时间,毫秒数   | 
| message_id	| 	短文本	| 	扩展属性   | 
| timestamp	| 	数字 	| 	时间戳   | 
| type	| 	短文本		| 扩展属性   | 
| user_id		| 短文本	| 	扩展属性   | 
| app_id   	|  短文本 	| 	扩展属性  | 
| cluster_id 	|  短文本 	| 扩展属性  | 

如延迟队列实现：
```
$attributes = [
    "delivery_mode" => 2,
    "expiration" => 10000
];
```

`delivery_mode`，表示是否持久化,1为否,2为是

`expiration`是延迟时长，单位是毫秒，1s=1000毫秒。

以上示例是说 在RabbitMQ中定义名为async的Exchange交换机，在这个交换机上推送信息到routing关键字为ticket的路由空间上。

消费者那面连接到borker服务器，创建队列实例，声明队列名queue_name，并声明与Exchange交换机bind绑定的具体routing关键字。
然后生产者这面通过Exchange交换机推送到指定routing关键字的信息就会被交换到指定的目标Queue队列中。

##### 消费者

再看消费者的例子：
```
// 连接名称，自己命名
$routing_key = 'ticket';

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
$q = new \AMQPQueue($channel);  // 创建队列
$q->setName('queue_ticket');  // 设置队列名称
$q->setFlags(AMQP_DURABLE);　　// 设置模式
$q->declareQueue();
$q->bind('async', $routing_key);  // 绑定交换机
$messages = $q->get(AMQP_AUTOACK);  // 获取消息
// 处理消息
if ($messages) {
    var_dump(json_decode($messages->getBody(), true));
}
$conn->disconnect();  // 断开连接
```

`new AMQPConnection()` 、 `new AMQPChannel()` 和生产者一样，声明broker连接实例和channel通道。

`new AMQPQueue($channel)` 创建一个AMQPQueue对象的实例：
```
/**
 * Create an instance of an AMQPQueue object.
 *
 * @param AMQPChannel $amqp_channel The amqp channel to use.
 *
 * @throws AMQPQueueException      When amqp_channel is not connected to a
 *                                 broker.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 */
public function __construct(AMQPChannel $amqp_channel) { }
```

`$q->setName()` 设置队列名字：
```
/**
 * Set the queue name.
 *
 * @param string $queue_name The name of the queue.
 *
 * @return boolean
 */
public function setName($queue_name) { }
```

`$q->setFlags()` 设置队列模式：
```
/**
 * Set the flags on the queue.
 *
 * @param integer $flags A bitmask of flags:
 *                       AMQP_DURABLE, AMQP_PASSIVE,
 *                       AMQP_EXCLUSIVE, AMQP_AUTODELETE.
 *
 * @return boolean
 */
public function setFlags($flags) { }
```

`AMQP_EXCLUSIVE` ， Valid for queues only, this flag indicates that only one client can be listening to and consuming from this queue.
独占队列，仅对队列有效，该标志指示只有一个客户端可以侦听并使用该队列。

`AMQP_AUTODELETE` ， For exchanges, the auto delete flag indicates that the exchange will be deleted as soon as no more queues are bound
 to it. If no queues were ever bound the exchange, the exchange will never be deleted. For queues, the auto delete
 flag indicates that the queue will be deleted as soon as there are no more listeners subscribed to it. If no
 subscription has ever been active, the queue will never be deleted. Note: Exclusive queues will always be
 automatically deleted with the client disconnects.
 对于交换，自动删除标志指示一旦不再绑定任何队列，交换将被删除。 如果没有队列绑定该交换，则该交换将永远不会被删除。 
 对于队列，自动删除标志指示一旦没有更多的侦听器订阅该队列，该队列就会被删除。 如果没有订阅处于活动状态，该队列将永远不会被删除。 
 注意：独占队列将始终在客户端断开连接时自动删除。
 
`$q->declareQueue()` 在borker代理上声明一个新队列。：
```
/**
 * Declare a new queue on the broker.
 *
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return integer the message count.
 */
public function declareQueue() { }
```

`$q->bind()` 将当前给定队列通过routing_key绑定到指定交换机上：
```
/**
 * Bind the given queue to a routing key on an exchange.
 *
 * @param string $exchange_name Name of the exchange to bind to.
 * @param string $routing_key   Pattern or routing key to bind with.
 * @param array  $arguments     Additional binding arguments.
 *
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return boolean
 */
public function bind($exchange_name, $routing_key = null, array $arguments = array()) { }
```

`$q->get()` 从队列中检索下一条消息：
```
/**
 * Retrieve the next message from the queue.
 *
 * Retrieve the next available message from the queue. If no messages are
 * present in the queue, this function will return FALSE immediately. This
 * is a non blocking alternative to the AMQPQueue::consume() method.
 * Currently, the only supported flag for the flags parameter is
 * AMQP_AUTOACK. If this flag is passed in, then the message returned will
 * automatically be marked as acknowledged by the broker as soon as the
 * frames are sent to the client.
 *
 * @param integer $flags A bitmask of supported flags for the
 *                       method call. Currently, the only the
 *                       supported flag is AMQP_AUTOACK. If this
 *                       value is not provided, it will use the
 *                       value of ini-setting amqp.auto_ack.
 *
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return AMQPEnvelope|boolean
 */
public function get($flags = AMQP_NOPARAM) { }
```

从队列中检索下一条可用消息。 如果队列中没有消息，此函数将立即返回FALSE。 
这是AMQPQueue::consume（）方法的一种非阻塞替代方法。 当前，flags参数唯一受支持的标志是AMQP_AUTOACK。 
如果传递了此标志，则将帧发送到客户端后，返回的消息将自动由代理标记为已确认。

`disconnect()` 关闭与AMQP代理的瞬时连接： 
```
/**
 * Closes the transient connection with the AMQP broker.
 *
 * This method will close an open connection with the AMQP broker.
 *
 * @return boolean true if connection was successfully closed, false otherwise.
 */
public function disconnect() { }
```

看一下下面这种回调函数消费的方法：
```
// 回调函数
function run($envelope, $queue)
{
    // 获取信息体
    $msg = $envelope->getBody();
    $msg = json_decode($msg, true);
    
    // 信息体逻辑处理
    ...
    
    // 确认响应
    $queue->ack($envelope->getDeliveryTag());
}
$callback = "run";

// 交换器名
$exchange_name = "async";
// routing_key 路由连接名称，自己命名
$routing_key = 'ticket';
// 队列名
$queue_name = "queue_ticket";
// 要预取的消息数
$offset = 1;

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

// 声明交换器，以防声明队列时找不到要绑定的交换器
$ex = new \AMQPExchange($channel);
$ex->setName($exchange_name);
$ex->setType(AMQP_EX_TYPE_DIRECT);
$ex->setFlags(AMQP_DURABLE);
$ex->declareExchange();

// 声明队列
$q = new \AMQPQueue($channel);  // 创建队列
$q->setName($queue_name);  // 设置队列名称
$q->setFlags(AMQP_DURABLE);　　// 设置模式
$q->declareQueue();
$q->bind($exchange_name, $routing_key);  // 绑定交换机

$channel->qos(0, $offset);

$q->consume($callback);

$conn->disconnect();
```

`$channel->qos(0, $offset)` 给给定channel通道设置服务数量：
```
/**
 * Set the Quality Of Service settings for the given channel.
 *
 * Specify the amount of data to prefetch in terms of window size (octets)
 * or number of messages from a queue during a AMQPQueue::consume() or
 * AMQPQueue::get() method call. The client will prefetch data up to size
 * octets or count messages from the server, whichever limit is hit first.
 * Setting either value to 0 will instruct the client to ignore that
 * particular setting. A call to AMQPChannel::qos() will overwrite any
 * values set by calling AMQPChannel::setPrefetchSize() and
 * AMQPChannel::setPrefetchCount(). If the call to either
 * AMQPQueue::consume() or AMQPQueue::get() is done with the AMQP_AUTOACK
 * flag set, the client will not do any prefetching of data, regardless of
 * the QOS settings.
 *
 * @param integer $size  The window size, in octets, to prefetch.
 * @param integer $count The number of messages to prefetch.
 *
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return bool TRUE on success or FALSE on failure.
 */
public function qos($size, $count) { }
```

在`AMQPQueue::consume()`或`AMQPQueue::get()`方法调用期间从队列发送的消息数中，指定要预取的数据量条数 或 窗口大小（8位字节）。
客户端将从服务器中预取最大为设定的八位字节的数据或条数消息，以先达到限制为准。 将任一值设置为0将指示客户端忽略该特定设置。 
调用`AMQPChannel::qos()`将覆盖通过调用`AMQPChannel::setPrefetchSize()`和`AMQPChannel::setPrefetchCount()`设置的任何值。 
如果对`AMQPQueue::consume()`或`AMQPQueue::get()`的调用是在设置了AMQP_AUTOACK标志的情况下完成的，
则无论QOS设置如何，客户端都不会进行任何数据预取。

`$q->consume($callback)` 调用回调函数从队列中消费信息，是个阻塞函数，它将在队列中可用时检索下一条消息，并将其传递给回调函数：
```
/**
 * Consume messages from a queue.
 *
 * Blocking function that will retrieve the next message from the queue as
 * it becomes available and will pass it off to the callback.
 *
 * @param callable | null $callback    A callback function to which the
 *                              consumed message will be passed. The
 *                              function must accept at a minimum
 *                              one parameter, an AMQPEnvelope object,
 *                              and an optional second parameter
 *                              the AMQPQueue object from which callback
 *                              was invoked. The AMQPQueue::consume() will
 *                              not return the processing thread back to
 *                              the PHP script until the callback
 *                              function returns FALSE.
 *                              If the callback is omitted or null is passed,
 *                              then the messages delivered to this client will
 *                              be made available to the first real callback
 *                              registered. That allows one to have a single
 *                              callback consuming from multiple queues.
 * @param integer $flags        A bitmask of any of the flags: AMQP_AUTOACK,
 *                              AMQP_JUST_CONSUME. Note: when AMQP_JUST_CONSUME
 *                              flag used all other flags are ignored and
 *                              $consumerTag parameter has no sense.
 *                              AMQP_JUST_CONSUME flag prevent from sending
 *                              `basic.consume` request and just run $callback
 *                              if it provided. Calling method with empty $callback
 *                              and AMQP_JUST_CONSUME makes no sense.
 * @param string   $consumerTag A string describing this consumer. Used
 *                              for canceling subscriptions with cancel().
 *
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 * @throws AMQPEnvelopeException   When no queue found for envelope.
 *
 * @return void
 */
public function consume(
    callable $callback = null,
    $flags = AMQP_NOPARAM,
    $consumerTag = null
) {
}
```

`$callback` 回调函数。消费消息将传递到的这个回调函数。 该回调函数有2个参数，第一个AMQPEnvelope对象和第二个可选的参数：
即从中调用回调的AMQPQueue对象。 在回调函数返回`FALSE`之前，`AMQPQueue::consume()`不会将处理线程返回到PHP脚本。 
如果省略了该回调函数参数或传递了null，则传递给此客户端的消息将可用于已注册的第一个实际回调函数。 
这样一来，一个回调函数就可以处理多个队列。

$flags 标志位掩码：AMQP_AUTOACK，AMQP_JUST_CONSUME。 
注意：使用AMQP_JUST_CONSUME标志时，将忽略所有其他标志，并且 $consumerTag 参数没有意义。 
AMQP_JUST_CONSUME 标志阻止发送 `basic.consume` 请求，如果提供则只运行$callback。 
如果调用的$callback方法为空，则AMQP_JUST_CONSUME没有意义。

`$consumerTag` 描述此使用者的字符串。 用于通过cancel()取消订阅。

AMQPEnvelope 结构体：
```
/**
 * stub class representing AMQPEnvelope from pecl-amqp
 */
class AMQPEnvelope extends AMQPBasicProperties
{
    public function __construct() { }

    /**
     * Get the body of the message.
     *
     * @return string The contents of the message body.
     */
    public function getBody() { }

    /**
     * Get the routing key of the message.
     *
     * @return string The message routing key.
     */
    public function getRoutingKey() { }

    /**
     * Get the consumer tag of the message.
     *
     * @return string The consumer tag of the message.
     */
    public function getConsumerTag() { }

    /**
     * Get the delivery tag of the message.
     *
     * @return string The delivery tag of the message.
     */
    public function getDeliveryTag() { }

    /**
     * Get the exchange name on which the message was published.
     *
     * @return string The exchange name on which the message was published.
     */
    public function getExchangeName() { }

    /**
     * Whether this is a redelivery of the message.
     *
     * Whether this is a redelivery of a message. If this message has been
     * delivered and AMQPEnvelope::nack() was called, the message will be put
     * back on the queue to be redelivered, at which point the message will
     * always return TRUE when this method is called.
     *
     * @return bool TRUE if this is a redelivery, FALSE otherwise.
     */
    public function isRedelivery() { }

    /**
     * Get a specific message header.
     *
     * @param string $header_key Name of the header to get the value from.
     *
     * @return string|boolean The contents of the specified header or FALSE
     *                        if not set.
     */
    public function getHeader($header_key) { }

    /**
     * Check whether specific message header exists.
     *
     * @param string $header_key Name of the header to check.
     *
     * @return boolean
     */
    public function hasHeader($header_key) { }
}
```

`$envelope->getDeliveryTag()` 获取信息的传递标签。

`$queue->ack($tag)` 向队列确认收到$tag的消息：
```
/**
 * Acknowledge the receipt of a message.
 *
 * This method allows the acknowledgement of a message that is retrieved
 * without the AMQP_AUTOACK flag through AMQPQueue::get() or
 * AMQPQueue::consume()
 *
 * @param string  $delivery_tag The message delivery tag of which to
 *                              acknowledge receipt.
 * @param integer $flags        The only valid flag that can be passed is
 *                              AMQP_MULTIPLE.
 *
 * @throws AMQPChannelException    If the channel is not open.
 * @throws AMQPConnectionException If the connection to the broker was lost.
 *
 * @return boolean
 */
public function ack($delivery_tag, $flags = AMQP_NOPARAM) { }
```

在没有 AMQP_AUTOACK 标志时，这个方法可以通过调用 `AMQPQueue::get()` 或 `AMQPQueue::consume()` 确认检索到的信息。

#### AMQP messaging 中的基本概念

![]({{site.baseurl}}/images/20190418/20190418181901.jpg)

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

![]({{site.baseurl}}/images/20190418/20190418182644.jpg)

生产者发送消息到broker server（RabbitMQ）。
在Broker内部，用户创建Exchange／Queue，通过Binding规则将两者联系在一起。
Exchange分发消息，根据类型 binding 的不同分发策略有区别。消息最后来到Queue中，等待消费者取走。
生产者把消息发布到 Exchange 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到那个队列。

#### Exchange类型

Exchange有多种类型，最常用的是Direct／Topic／Fanout三种类型。

Direct (point-to-point)

![]({{site.baseurl}}/images/20190418/20190418182946.jpg)

Message中的“routing key”如果和Binding中的“binding key”一致， Direct exchange则将message发到对应的queue中。

Topic (publish-subscribe)

![]({{site.baseurl}}/images/20190418/20190418183016.jpg)

根据routing key，及通配规则，Topic exchange将分发到目标queue中。Routing key中可以包含两种通配符，类似于正则表达式：
```
“#” 通配任何零个或多个word
“*” 通配任何单个word
```

Fanout (multicast)

![]({{site.baseurl}}/images/20190418/20190418182958.jpg)

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
     * 推送延迟队列
     * @param $message
     * @param $exchange
     * @param $routing
     * @param int $ttl 延迟时长，单位秒s
     * @return bool
     * @throws \AMQPChannelException
     * @throws \AMQPConnectionException
     * @throws \AMQPExchangeException
     */
    public static function produceTtl($message, $exchange, $routing, $ttl = 0)
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
        
        $attributes = [
            'delivery_mode' => 2,
            "expiration" => $ttl * 1000,  // 转化为毫秒，1s = 1000毫秒
        ];

        if (!$ex->publish($message,$routing,1, $attributes)) {
            return false;
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

可以看到每次消费后都断开了连接，我们需要用进程池来管理消费进程。

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

#### 延迟队列声明

延迟队列并不消费，只是交换器上延迟绑定的声明，我们需要声明这个延迟队列过了延迟时长应该推送到哪个常规队列中，所以我们只跑一次脚本就可以，不需要常驻进程：
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

命令行运行消费者，需要常驻进程，就需要守护进程程序，PHP开发守护进程涉及到 pcntl_fork()、posix_setsid() 等函数。
队列消费者连接RabbitMQ使用的是TCP长连接，可能会连接断开，导致消费者无法消费。可以使用python开发的守护进程的小工具supervisor实现，
消费者进程异常后，还会重新拉起。

<br/><br/><br/><br/><br/>
### 参考资料

RabbitMQ官网 <https://www.rabbitmq.com/>

RabbitMQ官网 PHP <https://www.rabbitmq.com/tutorials/tutorial-one-php.html>

RabbitMQ与AMQP协议详解 <http://www.cnblogs.com/frankyou/p/5283539.html>

PHP RabbitMQ延迟队列实现 <https://ibaiyang.github.io/blog/php/2019/01/03/PHP-RabbitMQ%E5%BB%B6%E8%BF%9F%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0.html>

模拟器 <http://tryrabbitmq.com/>

Rabbitmq各方法的作用详解 <https://blog.csdn.net/wwwwxyxy/article/details/84654787>

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

Supervisor-守护进程工具 <https://www.jianshu.com/p/39b476e808d8>

RabbitMQ（七）心跳控制 -- heartbeat <https://blog.csdn.net/jiao_fuyou/article/details/23186407/>

