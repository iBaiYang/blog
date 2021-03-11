---
layout: post
categories: Elasticsearch
title: Elasticsearch 节点重启
meta: Elasticsearch 节点重启
---
* content
{:toc}

### 正文

日常运维中我们经常碰到升级服务器重启操作，本文主要介绍计划内重启节点操作。

#### 禁止索引更新

如有可能的话，停止索引新的数据。这一步一般很难做到。

#### 禁止分片分配

```
# curl -XPUT -H "Content-Type: application/json" 11.191.0.6:9200/_cluster/settings -d '{ "transient" : {"cluster.routing.allocation.enable" : "none"}}'
```

transient：临时

persistent：永久

#### 关闭节点ES服务

```
# ps -ef |grep elastic |grep -v auto
elastic+ 24358     1 43  2019 ?        147-11:03:13 /usr/local/jdk1.8/bin/java -Xms31g -Xmx31g -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -server -Xss1m -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djna.nosys=true -Djdk.io.permissionsUseCanonicalPath=true -Dio.netty.noUnsafe=true -Dio.netty.noKeySetOptimization=true -Dio.netty.recycler.maxCapacityPerThread=0 -Dlog4j.shutdownHookEnabled=false -Dlog4j2.disable.jmx=true -Dlog4j.skipJansi=true -XX:+HeapDumpOnOutOfMemoryError -Des.path.home=/usr/local/elasticsearch -cp /usr/local/elasticsearch/lib/elasticsearch-5.3.0.jar:/usr/local/elasticsearch/lib/* org.elasticsearch.bootstrap.Elasticsearch -d
# kill 24358
```

修改配置、维护升级。

#### 重启节点

重启节点确认加入集群

```
# su - elasticsearch
$ cd /usr/local/elasticsearch/bin/
$ ./elasticsearch -d
# curl '18.18.0.6:9200/_cat/nodes?v'
```

#### 重启分片分配

重启分片分配并观察集群状态直到状态变为green

```
# curl -XPUT -H "Content-Type: application/json" 18.18.0.6:9200/_cluster/settings -d '{ "transient" : {"cluster.routing.allocation.enable" : "all"}}'
# curl '18.18.0.6:9200/_cat/health?v'
```

重复以上步骤重启其它节点。

<br/><br/><br/><br/><br/>
### 参考资料


