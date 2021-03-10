---
layout: post
categories: Elasticsearch
title: Elasticsearch 节点安装
meta: Elasticsearch 节点安装
---
* content
{:toc}

### 正文

#### Hosts修改

```
172.16.xx vpc-business-log-es004
172.16.xx vpc-business-log-es003
172.16.xx vpc-business-log-es001
172.16.xx vpc-business-log-es005
172.16.xx vpc-business-log-es002
```

#### JDK安装

准备工作：下载jdk1.8解压到/usr/local/下，然后环境变量/etc/profile中增加以下部分。
```
export JAVA_HOME=/usr/local/jdk1.8
export JRE_HOME=/usr/local/jdk1.8/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:
export PATH=$JAVA_HOME/bin:$PATH
```

#### ES安装

准备工作：下载elasticsearch并解压到 /usr/local/elasticsearch/

```
# groupadd -g 1001 elasticsearch #gid 1001
# useradd -r -g elasticsearch elasticsearch -u 997 #uid 997
# uid与gid 在一组集群中必须保持一致，否则在使用nfs创建仓库的时候会有问题。
# mkdir -p /data/es/logs
# mkdir -p /backup
# chown -R elasticsearch.elasticsearch /usr/local/elasticsearch/
# chown -R elasticsearch.elasticsearch /data/es/
# chown -R elasticsearch.elasticsearch /backup
```

#### 修改系统limits.conf和sysctl.conf

/etc/security/limits.conf

```
elasticsearch soft nproc 40000
elasticsearch hard nproc 40000
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
elasticsearch soft memlock -1
elasticsearch hard memlock -1
```

/etc/sysctl.conf（ #进程中内存映射区域的最大数量）

```
vm.max_map_count=655360
```

```
# sysctl -p
```

#### 修改ES配置文件

/usr/local/elasticsearch/config/elasticsearch.yml
```
cluster.name: cluster_test_gaofeng
node.name: 192.168.40.44
path:
data:
- /data/es
path.logs: /data/es/logs
bootstrap.memory_lock: true
network.host: 192.168.40.44
network.publish_host: 192.168.40.44
http.port: 9200
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["192.168.40.44", "192.168.40.45", "192.168.40.46"]
discovery.zen.minimum_master_nodes: 2
node.master: true
node.data: true
http.cors.enabled: true
http.cors.allow-origin: "*"
http.enabled: true
path.repo: ["/backup"]
indices.memory.index_buffer_size: "20%"
```

jvm.options
```
-Xms8g
-Xmx8g
```

注意：最大不超过32G

#### 启动ES

```
# su - elasticsearch
# cd /usr/local/elasticsearch/bin/
# ./elasticsearch -d
```

#### 验证节点

```
# curl '192.168.40.44:9200/_cat/health?v'
# curl '192.168.40.44:9200/_cat/nodes?v'
```

<br/><br/><br/><br/><br/>
### 参考资料


