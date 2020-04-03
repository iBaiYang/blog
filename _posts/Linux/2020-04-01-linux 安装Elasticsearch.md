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

