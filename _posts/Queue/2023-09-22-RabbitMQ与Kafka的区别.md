---
layout: post
categories: Queue
title: RabbitMQ与Kafka的区别
meta: RabbitMQ与Kafka的区别
---
* content
{:toc}

## 概述

Kafka和RabbitMQ是两种不同的消息中间件，它们在多个方面存在显著的差异。以下是它们之间的主要区别：

1. **语言与开发**：
   - Kafka：采用Scala语言开发，主要用于处理活跃的流式数据和大数据量的数据处理。
   - RabbitMQ：由内在高并发的Erlang语言开发，适用于实时的、对可靠性要求比较高的消息传递。

2. **结构与组成**：
   - Kafka：采用mq结构，其broker有partition分区的概念。每个topic可以分多个partition，consumer group对partition进行consume操作。
   - RabbitMQ：基于AMQP（高级消息队列协议），其broker由Exchange、Binding、queue组成，支持多种交换机类型和绑定选项。

3. **Brokerr与Consume交互方式**：
   - Kafka：采用pull的方式，消费者主动从broker拉取数据。
   - RabbitMQ：采用push的方式，生产者将消息发送到队列，消费者从队列中接收消息。

4. **集群负载均衡**：
   - Kafka：通过Zookeeper对集群中的broker、consumer进行管理，确保负载均衡和容错性。
   - RabbitMQ：负载均衡需要单独的loadbalancer进行支持。

5. **吞吐量与性能**：
   - Kafka：以高吞吐量和低延迟为特点，每秒可以处理几十万条消息，延迟只有几毫秒。内部采用消息的批量处理、zero-copy机制等，提高消息处理的效率。
   - RabbitMQ：虽然也支持高并发，但主要关注消息的可靠传递和处理。

6. **可靠性与持久性**：
   - Kafka：具有持久性和可靠性，消息被持久化到本地磁盘，并且支持数据备份防止数据丢失。基于副本机制实现高可用。
   - RabbitMQ：也提供持久化和通知机制来确保消息被成功地传递和处理。

7. **使用场景**：
   - Kafka：常用于大数据处理和流式数据场景，如日志收集、网站活性跟踪等。
   - RabbitMQ：适用于需要可靠消息传递的实时系统，如金融交易、在线购物等。

8. **扩展性**：
   - Kafka和RabbitMQ都支持集群的扩展，但Kafka的扩展性更多地体现在处理大数据和流式数据的能力上，而RabbitMQ则更关注消息传递的可靠性和实时性。

总结来说，Kafka和RabbitMQ各有特点，适用于不同的场景。Kafka更适合于大数据处理和流式数据场景，
而RabbitMQ则更适用于需要可靠消息传递的实时系统。在选择时，需要根据具体的需求和场景进行考虑。

## RabbitMQ 七战 Kafka

参阅 <https://zhuanlan.zhihu.com/p/339564726>

## RocketMQ是什么

RocketMQ 是什么？它的架构是怎么样的？和 Kafka 又有什么区别？ 

参阅 <https://mp.weixin.qq.com/s/oje7PLWHz_7bKWn8M72LUw>

RocketMQ 是阿里自研的国产消息队列，目前已经是 Apache 的顶级项目。
和其他消息队列一样，它接受来自生产者的消息，将消息分类，每一类是一个 topic，消费者根据需要订阅 topic，获取里面的消息。

## 其他

RabbitMQ和Kafka的比较 <https://zhuanlan.zhihu.com/p/138652597>

选型必看：RabbitMQ 七战 Kafka，差异立现 <https://zhuanlan.zhihu.com/p/339564726>










<br/><br/><br/><br/><br/>
## 参考资料

选型必看：RabbitMQ 七战 Kafka，差异立现 <https://zhuanlan.zhihu.com/p/339564726>

<https://github.com/chenjiabing666/JavaFamily>

