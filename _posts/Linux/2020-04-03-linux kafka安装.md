---
layout: post
categories: Linux
title: linux kafka安装
meta: linux kafka安装
---
* content
{:toc}

### 正文

#### 编译安装

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

> bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic logstash

现在就可以往kafka中写数据了。

查看topic有哪些：

> bin/kafka-topics.sh --list --bootstrap-server localhost:9092

#### 停止服务

停止ZooKeeper服务：

> sudo bin/zookeeper-server-stop.sh

启动Kafka服务：

> sudo bin/kafka-server-stop.sh

#### 其他问题

启动启动ZooKeeper服务时发现报错了：
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
