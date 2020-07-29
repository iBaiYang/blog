---
layout: post
categories: Linux
title: linux 安装Elasticsearch
meta: linux 安装Elasticsearch
---
* content
{:toc}

### 正文

#### 手动安装

> sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-amd64.deb

> wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-amd64.deb.sha512

> shasum -a 512 -c elasticsearch-7.6.2-amd64.deb.sha512

> sudo dpkg -i elasticsearch-7.6.2-amd64.deb

安装完成。

SysV init系统操作如下 -->

设置开机自启动：

> sudo update-rc.d elasticsearch defaults 95 10

启动：

> sudo -i service elasticsearch start

停止：

> sudo -i service elasticsearch stop

查看运行状态：

> service elasticsearch status

systemd系统操作如下 -->

设置开机自启动：

> sudo /bin/systemctl daemon-reload

> sudo /bin/systemctl enable elasticsearch.service

启动：

> sudo systemctl start elasticsearch.service

停止：

> sudo systemctl stop elasticsearch.service

查看运行状态：

> sudo systemctl status elasticsearch.service

如果Elasticsearch由于任何原因启动失败，它将把失败原因打印到STDOUT。 日志文件可以在 /var/log/elasticsearch/ 中找到。

#### apt 安装Elasticsearch

下载安装 public signing key ：

> wget https://artifacts.elastic.co/GPG-KEY-elasticsearch -O GPG-KEY-elasticsearch.key

> sudo apt-key add GPG-KEY-elasticsearch.key

安装 apt-transport-https 包：

> sudo apt-get install apt-transport-https

保存 repository definition 到 /etc/apt/sources.list.d/elastic-7.x.list 中：

```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

安装 Elasticsearch Debian 包：

> sudo apt-get update && sudo apt-get install elasticsearch

设置：

> sudo /bin/systemctl daemon-reload

> sudo /bin/systemctl enable elasticsearch.service

启动服务：

> sudo systemctl start elasticsearch.service

停止服务

> sudo systemctl stop elasticsearch.service

输入以下命令查看服务是否启动成功

> sudo systemctl status elasticsearch.service

但是发现apt安装总是失败。

#### 安装IK分词器

> sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.6.2/elasticsearch-analysis-ik-7.6.2.zip

#### 安装head插件

> sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install https://github.com/mobz/elasticsearch-head/archive/v5.0.0.zip

重启：

> sudo -i service elasticsearch restart

然后web页面访问：http://localhost:9200/_plugin/head/

![]({{site.baseurl}}/images/20200401/20200401173255.png)

可以看到管理页面。

不过从Es5.0版本开始，这个插件不再支持，需要以服务站点的方式运行：

```
git clone http://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
```

然后打开 http://localhost:9100/ 网址看一下。

#### 安装Kibana

安装前的设置与Es相同，上面已经设置过一遍，这里可以直接执行：

> apt-get update && apt-get install kibana

发现apt安装方式还是不好使，手动安装吧：

> sudo wget https://artifacts.elastic.co/downloads/kibana/kibana-7.6.2-amd64.deb

> shasum -a 512 kibana-7.6.2-amd64.deb

> sudo dpkg -i kibana-7.6.2-amd64.deb

设置开机自启动：

> sudo update-rc.d kibana defaults 95 10

启动：

> sudo -i service kibana start

停止：

> sudo -i service kibana stop

查看运行状态：

> service kibana status

如果kibana由于任何原因启动失败，它将把失败原因打印到STDOUT。 日志文件可以在 /var/log/kibana/ 中找到。

web访问 http://127.0.0.1:5601/app/kibana 可以看到页面：

![]({{site.baseurl}}/images/20200401/20200401191829.png)

示例数据：

http://127.0.0.1:5601/app/kibana#/home/tutorial_directory/sampleData

建议安装 Version: 5.2.2 版本，越新版越难用。

#### 具体使用

Elasticsearch中有个元数据的概念：元数据（Metadata），又称中介数据、中继数据，为描述数据的数据（data about data），
主要是描述数据属性（property）的信息，用来支持如指示存储位置、历史数据、资源查找、文件记录等功能；元数据算是一种电子式目录，
为了达到编制目录的目的，必须在描述并收藏数据的内容或特色，进而达成协助数据检索的目的。

元数据是关于数据的组织、数据域及其关系的信息，简言之，元数据就是关于数据的数据。  

Elasticsearch中的元数据有：

**_index**

1. 代表一个document存储在哪个index中。
2. 类似的数据（document）放在同一个index，不同类型的数据放在不同的index。
3. 索引名必须是小写，不能用下划线（_）开头，不能包含逗号。

**_type**

1. 代表document属于index下 的哪个类别（type）。
2. 一个index通常包含多个type。
3. type名称可以大写或小写，不能用下划线（_）开头，不能包含逗号。

**_id**

1. 代表document的唯一标识，与index和type一起确定一个唯一的document。
2. 可以手动指定，也可es自动生成。 手动指定：PUT /index/type/id； 自动生成： PUT /index/type/ 生成base64编码的20长度的字符串ID，分布式集群下不可能重复。

**_score**

1. 相关度分数：匹配程度，分数越高越相关。

**_source**

1. 代表一个document，是一个json对象（{json}）,包含一个实例对象。


##### 命令行下使用

命令行下curl请求：
```
curl -XPUT '127.0.0.1:9200/demo_logs_dev_2020.04.03/2/AXE_baZOxB5-91nMXmB1?pretty'  
-H 'content-Type:application/json'  
-d '{"log_id":"5e86e67015791405619","indexname":"demo_logs_dev","time":"2020-04-03 15:32:00",
"category":"yii\\base\\View::renderFile","level":"trace","step":7,"ip_address":"127.0.0.1",
"msg":"Rendering view file: \/var\/www\/pear-adminlte\/backend\/views\/user\/users-lists.php"}'
```

返回：
```
{
  "_index" : "demo_logs_dev_2020.04.03",
  "_type" : "2",
  "_id" : "AXE_baZOxB5-91nMXmB1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

##### Kibana下使用

**查看集群的健康状况**

Dev Tools   Console：
> GET _cat/health?v

结果：
```
epoch      timestamp cluster         status node.total node.data shards  pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1595840177 16:56:17  offlline-es-v53 green           3         3   9347 9316    0    0        0             0                  -                100.0%
```

**查看集群中有哪些索引**

Dev Tools   Console：
> GET _cat/indices?v 

结果：
```
health status index                                                   uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   app-api-v5-test-flink-bzlog-2020.06.11                    aEXDULs-QeOypayZ_1kb6Q  10   0      41959            0     63.7mb         63.7mb
       close  pre_project-api_service-logs-2020.05                      LIdIuTrLQlSdyTC3uzu7uA                                                          
       close  netease_log-2020.05                                       fSEj43FASyeqv-kKYI1ubw                                                          
green  open   mapi_logs_new-2020.07.18                                  qD_6azC0Ro-QBYUDc1RXUg   3   0       7556            0      8.8mb          8.8mb
       close  online_net-2020.05.30                                    vbS4IikURDSELteMfv6S4w                                                          
       close  online-api-2020.05.10                                    CueV9GJ1QiKStK7iz1AY2A       
```

**创建索引**

Dev Tools   Console：
> PUT /index_name?pretty 

index_name是要创建的索引名。

如：
> PUT /abc_123?pretty 

返回：
```
{
  "acknowledged": true,
  "shards_acknowledged": true
}
```

**删除索引**

Dev Tools   Console：
> DELETE /index_name?pretty

输出：
```
{
  "acknowledged": true
}
```

**写入数据**

Dev Tools   Console：
```
POST /index/type/id
{json}
```

或者
```
PUT /index/type/id 
{json}
```

也可以不指定id, es会生成base64的20长度的字符串ID，集群下不可能重复。

示例
```
POST /abc_123/in/abc123abc123abc123
{
    "a":1
}
```

返回：
```
{
  "_index": "abc_123",
  "_type": "in",
  "_id": "abc123abc123abc123",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "created": true
}
```

**获取内容**

Dev Tools   Console：
> GET /index/type/id

返回：
```
{
  "_index": "abc_123",
  "_type": "in",
  "_id": "abc123abc123abc123",
  "_version": 2,
  "found": true,
  "_source": {
    "a": 1
  }
}
```

**修改内容**

Dev Tools   Console：
```
POST /index/type/id
{json}
```

或者
```
PUT /index/type/id 
{json}
```

**搜索内容**

所有内容有多种方式，可以是query string search、 query DSL、 过滤、 分页、 关键字高亮 等。

query string search

搜索全部：
> GET http://ip:9200/test/test/_search

以about字段中含有climbing字符查询并根据age字段降序排列 可以多个排序，用逗号分隔：
> GET http://ip:9200/test/test/_search?q=about:climbing&sort=age:desc,price:desc

DSL搜索

搜索全部：
```
POST http://ip:9200/index/type/_search

{
    "query":{
        "match_all":{}
    }
}
```

以about字段中含有climbing字符查询并根据age字段降序排列 可以多个排序，用逗号分隔：
```
POST http://ip:9200/index/_search

{
    "query":{
        "match":{
            "about":"climbing"
        }
    },
    "sort":[
        {
            "age":"desc"
        }
    ]
}
```

分页数据：
```
POST http://ip:9200/test/test/_search

{
    "query":{
        "match_all":{}         # 查询所有
    },
    "from":0,               # 从第几条数据开始  0：第一条
    "size":1                # 展示几条数据
}
```

只展示指定的filed：
```
POST http://ip:9200/test/test/_search

{
    "query":{
        "match_all":{}
    },
    "_source":[
        "first_name",
        "age"
    ]
}
```

多个查询条件：about字段必须包含"climbing"；age大于20岁：
```
POST http://ip:9200/test/test/_search

{
    "query":{
        "bool":{ 
            "must":{ 
                "match":{ 
                    "about":"climbing" 
                }    
            },
            "filter":{ 
                "range":{ 
                    "age":{ 
                        "gt":20 
                    } 
                } 
            } 
        } 
    }
}
```

多个查询条件：must、 should、 must_not：
```
POST http://ip:9200/test/test/_search

{ 
    "query":{ 
        "bool":{ 
            "must":{ # 必须匹配 
                "match":{ 
                    "first_name":"小翠" 
                }    
            }, 
            "should":{ # 可以匹配，也可以不匹配 
                "match":{ "last_name": "xue" } 
            }, 
            "must_not":{    # 必须不匹配 
                "match":{ "last_name": "cui" } 
            } 
        } 
    } 
}
```

全文检索：
```
POST http://ip:9200/test/test/_search

{
    "query":{
            "match":{
                "about":"go climbing"
            }        
    }
}
```

分析：es将about这个filed拆解成每个词（term），建立倒排索引，每个term对应相应的document_id

短语搜索:匹配短语：
```
POST http://ip:9200/test/test/_search

{
    "query":{
            "match_phrase":{
                "about":"rock climbing"
            }        
    }
}
```

关键字高亮：
```
{
    "query":{
            "match":{
                "about":"climbing"    # 关键字
            }        
    },
    "highlight":{
        "fields":{
            "about":{}                # 字段
        }
    }
}
```

**DSL返回结果**

DSL搜索返回结果结构，我们看到一些元数据（took，timed_out等）和一个名为hits的数组，这代表我们的搜索结果。 
hits内部是另一个名为hits的数组，其中包含单个搜索结果，如下：
```
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 3,
    "successful": 3,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 7.0329185,
    "hits": [
      {
        "_index": "adminlog-2020.07",
        "_type": "in",
        "_id": "AXMIPktTxB5-91lMn9cZ",
        "_score": 7.0329185,
        "_source": {
          "log_id": "1593570969841844",
          "@timestamp": "2020-07-01T02:36:09.604Z",
          "indexname": "adminlog",
          "timing": "103",
          "time_output": 1593570969,
          "@version": "1",
          "type": "in"
        }
      }
    ]
  }
}
```

**页面操作**

Kibana页面创建Index patterns：

![]({{site.baseurl}}/images/20200403/20200403183339.png)

![]({{site.baseurl}}/images/20200403/20200403183434.png)

![]({{site.baseurl}}/images/20200403/20200403183458.png)

然后就可以看到刚才添加那条数据了：

![]({{site.baseurl}}/images/20200403/20200403183656.png)

##### Postman下使用

**查看集群的健康状况**

GET HTTP://IP:PORT/_cat/health?v

<br/><br/><br/><br/><br/>
### 参考资料

Install Elasticsearch with Debian Package <https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-key>

Install Kibana with Debian Package <https://www.elastic.co/guide/en/kibana/current/deb.html>

Installing Logstash <https://www.elastic.co/guide/en/logstash/current/installing-logstash.html>

ElasticSearch(1) 安装es服务端和Kibana客户端 <https://blog.csdn.net/qq_38225558/article/details/85844833>

ElasticSearch : APT-GET安装方式 <https://www.cnblogs.com/cccy0/p/10113538.html>

deepin下安装ElasticSearch <https://blog.csdn.net/u010570551/article/details/72909838>

ES(elasticsearch)搜索引擎安装和使用 <https://blog.csdn.net/weixin_38040473/article/details/81082968>

linux下elasticsearch 安装、配置及示例 <https://blog.csdn.net/sinat_28224453/article/details/51134978>

本地elasticsearch搭建 <https://www.jianshu.com/p/dfd547f4588f>

ubuntu 安装 ES (ElasticSearch) <https://blog.csdn.net/weixin_44596128/article/details/103970665>

浅析init和systemd <https://blog.51cto.com/babylater/1895056>

Elasticsearch教程（一），全程直播（小白级别） <https://www.sojson.com/blog/81.html>

es安装和应用 <https://www.jianshu.com/p/c596caf31688?from=singlemessage>

元数据(Metadata) <https://blog.csdn.net/lgstudyvc/article/details/48732799>

Elasticsearch学习（三）————元数据（_index、_type、_id、_score、_source） <https://blog.csdn.net/qq_42513284/article/details/90678516>

Elasticsearch学习(一)————简单命令 <https://blog.csdn.net/qq_42513284/article/details/90613639>

Elasticsearch学习（二）————搜索 <https://blog.csdn.net/qq_42513284/article/details/90613889>

