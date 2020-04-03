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

apt安装这个很费劲，还是手动下载软件包安装吧。

<br/><br/><br/><br/><br/>
### 参考资料

Download Logstash <https://www.elastic.co/cn/downloads/logstash>

Installing Logstash <https://www.elastic.co/guide/en/logstash/current/installing-logstash.html>




