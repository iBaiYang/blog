---
layout: post
categories: Linux
title: linux Logstash安装
meta: linux Logstash安装
---
* content
{:toc}

### 正文

#### 手动安装

下载：

> cd /usr/local/src

> sudo wget https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.tar.gz

解压：

> sudo tar zxvf logstash-7.6.2.tar.gz

准备logstash.conf，针对kafka、RabbitMQ等不同的队列服务有相应不同的配置，具体见下面的具体使用说明。

运行：

> bin/logstash -f logstash.conf

#### apt安装

下载安装Public Signing Key：
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

安装apt-transport-https软件包：

> sudo apt-get install apt-transport-https

将存储库定义保存到/etc/apt/sources.list.d/elastic-7.x.list：

```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

安装：

> sudo apt-get update && sudo apt-get install logstash

apt安装这个很费劲，网速极差，还是手动下载软件包安装吧。

#### 具体使用

Logstash是数据源与目的库之间的中间服务，如从RabbitMQ/kafka等队列服务中获取数据，然后保存到Es库中。

队列数据格式：
```
{
    "log_id":"5e86e67015791405619",
    "indexname":"demo_logs_dev",
    "time":"2020-04-03 15:32:00",
    "category":"yii\\base\\View::renderFile",
    "level":"trace",
    "step":7,
    "ip_address":"127.0.0.1",
    "msg":"Rendering view file: \/var\/www\/pear-adminlte\/backend\/views\/user\/users-lists.php"
}
```

kafka源的相应配置：

logstash.conf
```
input {
    kafka {
        bootstrap_servers => "localhost:9092"
        topics => ["logstash"]
        codec => "json"
    }
}
output {
    elasticsearch {
        hosts => ["http://127.0.0.1:9200"]
        index => "%{indexname}-%{+YYYY.MM.dd}"
    }
}
```

运行：

> cd /usr/local/src

> ./bin/logstash -f logstash.conf

数据就会从kafka到elasticsearch中了，%{indexname}就是数据源中的 "indexname":"demo_logs_dev" ，生成的Es数据如下：
```
{
  "_index": "demo_logs_dev-2020.04.03",
  "_type": "_doc",
  "_id": "9nEWQHEBzd1pqLGtXylI",
  "_version": 1,
  "_score": 0,
  "_source": {
    "ip_address": "127.0.0.1",
    "@timestamp": "2020-04-03T12:49:00.093Z",
    "msg": "Rendering view file: /var/www/pear-adminlte/backend/views/user/users-page.php",
    "step": 7,
    "@version": "1",
    "time": "2020-04-03 20:49:00",
    "indexname": "demo_logs_dev",
    "category": "yii\\base\\View::renderFile",
    "level": "trace",
    "log_id": "5e8730bc014ec405091"
  },
  "fields": {
    "@timestamp": [
      "2020-04-03T12:49:00.093Z"
    ]
  }
}
```

说一个碰到的问题，期间调试了半天，发现logstash配置文件中每个属性最后居然不能加','分隔相邻变量。这个语法看起来虽然像json，但不是json。

看个kafka源复杂点的：
```
input {
    kafka {
        bootstrap_servers => ["192.168.40.001:9092,192.168.40.002:9092,192.168.40.003:9092"]
        client_id => "test"
        group_id => "test"
        #auto_offset_reset => "latest"
        consumer_threads => 6
        #decorate_events => true
        topics => ["logstash"]
        codec => json
    }
}

output {
    if [indexname] =~ "demo_log*" {
        elasticsearch {
            hosts => ["192.168.40.101:9200"]
            index => "%{indexname}-%{+YYYY.MM.dd}"
        }
        elasticsearch {
            hosts => ["192.168.40.102:9200"]
            index => "%{indexname}-%{+YYYY.MM.dd}"
        }
    }
    if [indexname] !~ "demo_log*" {
        elasticsearch {
            hosts => ["192.168.76.103:9200"]
            index => "%{indexname}-%{+YYYY.MM}"
        }
    }
}
```

RabbitMQ源的相应配置：

logstash.conf
```
input {
    rabbitmq {
        exclusive => false
        host => '172.16.5.101'
        user => 'mquser'
        password => 'ABCabc123'
        vhost => '/'
        ack => false
        prefetct_count => 50
        auto_delete => flase
        durable => true
        exchange => 'exname'
        key => 'logs_routing'
        queue => 'logs'
        threads => 2
    }
}

output {
    elasticsearch {
        hosts => ["http://127.0.0.1:9200"]
        index => "%{indexname}-%{+YYYY.MM.dd}"
    }
}
```

<br/><br/><br/><br/><br/>
### 参考资料

Download Logstash <https://www.elastic.co/cn/downloads/logstash>

Installing Logstash <https://www.elastic.co/guide/en/logstash/current/installing-logstash.html>

Yii2 错误计入日志可查询方案学习 <https://ibaiyang.github.io/blog/yii2/2018/01/24/Yii2-%E9%94%99%E8%AF%AF%E8%AE%A1%E5%85%A5%E6%97%A5%E5%BF%97%E5%8F%AF%E6%9F%A5%E8%AF%A2%E6%96%B9%E6%A1%88%E5%AD%A6%E4%B9%A0.html> 

Yii2 kafka使用 <https://ibaiyang.github.io/blog/yii2/2018/08/09/Yii2-kafka%E4%BD%BF%E7%94%A8.html>

使用Logstash将Kafka中的数据导入到ElasticSearch <https://www.jianshu.com/p/166af5b76435>

Structure of a Config File <https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html>
