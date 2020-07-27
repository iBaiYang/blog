---
layout: post
categories: Yii2
title: Yii2 Elasticsearch查询封装实现
meta: Elasticsearch查询封装实现
---
* content
{:toc}

### 正文

PHP查询Elasticsearch数据，可以直接用Packagist中的类库elasticsearch/elasticsearch。具体安装方法，看一下过去的记录。

如果Elasticsearch查询不进行封装，代码看起来会很散，我们在Yii2中封装一下Elasticsearch查询。

Es连接读取层 与 AR活动记录 首先要进行分离， AR活动记录 通过 ActiveQuery活动搜索层 对 Es连接读取层 进行数据读取。

#### Es连接读取层

#### ActiveQuery活动搜索层

#### AR活动记录


<br/><br/><br/><br/><br/>
### 参考资料

elastic/elasticsearch-php <https://github.com/elastic/elasticsearch-php>

Packagist elasticsearch/elasticsearch <https://packagist.org/packages/elasticsearch/elasticsearch>

PHP Elasticsearch查询服务示例 <https://ibaiyang.github.io/blog/php/2018/12/27/PHP-Elasticsearch%E6%9F%A5%E8%AF%A2%E6%9C%8D%E5%8A%A1%E7%A4%BA%E4%BE%8B.html>

linux 安装Elasticsearch <https://ibaiyang.github.io/blog/linux/2020/04/01/linux-%E5%AE%89%E8%A3%85Elasticsearch.html>


