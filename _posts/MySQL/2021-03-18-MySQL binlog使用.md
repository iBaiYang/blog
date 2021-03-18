---
layout: post
categories: MySQL
title: MySQL binlog使用
meta: MySQL binlog使用
---
* content
{:toc}

### 正文

有时会碰到需要把一个库中数据的变动及时同步到另一个库中：一种方法是项目更新一个库数据时就同时更新另一个库，这种方式需要修改业务的代码；
还有一种方式就是使用MySQL的binary log，根据操作日志信息更新其他数据源数据。

根据binlog同步数据，解放了项目代码的负担，也保证了数据的完整。可以使用的开源项目有Canal，看一下功能示意：

![](/blog/images/20210318/20210318110035.png)

实际项目我们是配置MQ模式，配合RocketMQ或者Kafka，canal会把数据发送到MQ的topic中，然后通过消息队列的消费者进行处理：

![](/blog/images/20210318/20210318110037.png)

在队列消费那里，我们可以采用Flink负责数据拉取。

<br/><br/><br/><br/><br/>
### 参考资料

超详细的Canal入门，看这篇就够了！ <https://blog.csdn.net/yehongzhi1994/article/details/107880162>

基于Canal与Flink实现数据实时增量同步(二) <https://blog.csdn.net/jmx_bigdata/article/details/105176983>
