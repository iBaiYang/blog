---
layout: post
categories: Linux
title: linux kafka安装
meta: linux kafka安装
---
* content
{:toc}

### 正文

Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者在网站中的所有动作流数据。Kafka是由Apache软件基金会开发的一个开源流处理平台。
Kafka同RabbitMQ一样，也是一个MQ队列服务器。

Kafka有如下特性：
* 通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能够保持长时间的稳定性能。
* 高吞吐量：即使是非常普通的硬件Kafka也可以支持每秒数百万的消息。
* 支持通过Kafka服务器和消费机集群来分区消息。
* 支持Hadoop并行数据加载。

Kafka相关术语介绍：
* Broker    -Kafka集群包含一个或多个服务器，这种服务器被称为broker
* Topic    -每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）
* Partition    -Partition是物理上的概念，每个Topic包含一个或多个Partition.
* Producer    -负责发布消息到Kafka broker
* Consumer    -消息消费者，向Kafka broker读取消息的客户端。
* Consumer Group    -每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。

我们一般把Kafka用作log日志队列服务，日志流流程：

![]({{site.baseurl}}/images/20200710/20200710111105.png)

#### 源码包安装

在/usr/local/src目录下下载：

> sudo wget https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.4.1/kafka_2.12-2.4.1.tgz

解压：

> sudo tar zxvf kafka_2.12-2.4.1.tgz

安装完成。

#### 启动服务

> cd kafka_2.12-2.4.1

启动ZooKeeper服务：

> sudo bin/zookeeper-server-start.sh config/zookeeper.properties

启动Kafka服务：

> sudo bin/kafka-server-start.sh config/server.properties

创建一个名为logstash的topic：

```
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic logstash
```

现在就可以往kafka中写数据了。

查看topic有哪些：

```
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

消费topic名为logstash中的队列数据：

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic logstash --from-beginning
```

可以实时看到数据内容。

#### 停止服务

停止Kafka服务：

> sudo bin/kafka-server-stop.sh

停止ZooKeeper服务：

> sudo bin/zookeeper-server-stop.sh

#### 其他问题

启动ZooKeeper服务时发现报错了：
```
[2020-04-03 14:56:53,228] INFO binding to port 0.0.0.0/0.0.0.0:2181 (org.apache.zookeeper.server.NIOServerCnxnFactory)
[2020-04-03 14:56:53,229] ERROR Unexpected exception, exiting abnormally (org.apache.zookeeper.server.ZooKeeperServerMain)
java.net.BindException: 地址已在使用
	at sun.nio.ch.Net.bind0(Native Method)
	at sun.nio.ch.Net.bind(Net.java:433)
	at sun.nio.ch.Net.bind(Net.java:425)
	at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
	at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
	at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:67)
	at org.apache.zookeeper.server.NIOServerCnxnFactory.configure(NIOServerCnxnFactory.java:687)
	at org.apache.zookeeper.server.ZooKeeperServerMain.runFromConfig(ZooKeeperServerMain.java:143)
	at org.apache.zookeeper.server.ZooKeeperServerMain.initializeAndRun(ZooKeeperServerMain.java:106)
	at org.apache.zookeeper.server.ZooKeeperServerMain.main(ZooKeeperServerMain.java:64)
	at org.apache.zookeeper.server.quorum.QuorumPeerMain.initializeAndRun(QuorumPeerMain.java:128)
	at org.apache.zookeeper.server.quorum.QuorumPeerMain.main(QuorumPeerMain.java:82)
```

2181端口被占用了。

看一下这个端口是否在监听:

```
netstat -tunl | grep 2181
```

看一下是什么服务在监听这个端口：

> lsof -i :2181

发现是java:
```
COMMAND  PID    USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
java    9914 baiyang  120u  IPv6 7073484      0t0  TCP *:2181 (LISTEN)
```

杀掉这个进程：

> kill -9 9914

再重新启动就可以了。



<br/><br/><br/><br/><br/>
### 参考资料

kafka Quickstart <http://kafka.apache.org/quickstart>

Kafka史上最详细原理总结上 <https://www.jianshu.com/p/734cf729d77b>

Ubuntu简单安装Kafka <https://www.cnblogs.com/yanghe123/p/11937914.html>

Kafka史上最详细原理总结上 <https://www.jianshu.com/p/734cf729d77b>

Kafka史上最详细原理总结下 <https://www.jianshu.com/p/acf010e67a19>

DMA：为什么Kafka这么快？ <https://weibo.com/ttarticle/p/show?id=2309404597571816391045>

