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

准备logstash.conf ：


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
        bootstrap_servers => "localhost:9092",
        topics => ["logstash"],
        codec => "json"
    }
}

output {
    elasticsearch {
        hosts => ["http://localhost:9200"]
        index => "demo_logs_dev-%{+YYYY.MM.dd}"
    }
}
```

RabbitMQ源的相应配置：

logstash.conf
```
input {
    rabbitmq {
        exclusive => false,
        host => '172-16-56-23',
        user => 'mquser',
        password => 'ABCabc123',
        vhost => '/',
        ack => false,
        prefetct_count => 50,
        auto_delete => true,
        exchange => 'exname'.
        key => 'log_routing',
        queue => 'logs',
        threads => 2
    }
}

output {
    elasticsearch {
        hosts => ["http://localhost:9200"]
        index => "demo_logs_dev-%{+YYYY.MM.dd}"
    }
}
```


运行：

> cd /usr/local/src

> ./bin/logstash -f logstash.conf

<br/><br/><br/><br/><br/>
### 参考资料

Download Logstash <https://www.elastic.co/cn/downloads/logstash>

Installing Logstash <https://www.elastic.co/guide/en/logstash/current/installing-logstash.html>

Yii2 错误计入日志可查询方案学习 <https://ibaiyang.github.io/blog/yii2/2018/01/24/Yii2-%E9%94%99%E8%AF%AF%E8%AE%A1%E5%85%A5%E6%97%A5%E5%BF%97%E5%8F%AF%E6%9F%A5%E8%AF%A2%E6%96%B9%E6%A1%88%E5%AD%A6%E4%B9%A0.html> 

Yii2 kafka使用 <https://ibaiyang.github.io/blog/yii2/2018/08/09/Yii2-kafka%E4%BD%BF%E7%94%A8.html>

使用Logstash将Kafka中的数据导入到ElasticSearch <https://www.jianshu.com/p/166af5b76435>
