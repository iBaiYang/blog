---
layout: post
categories: Yii2
title: Yii2 cache用Redis实现
meta: 缓存cache可以用Redis实现，下面说一下。
---
* content
{:toc}

#### 正文

###### 缓存

缓存是提升 Web 应用性能简便有效的方式。 通过将相对静态的数据存储到缓存并在收到请求时取回缓存， 
应用程序便节省了每次重新生成这些数据所需的时间。

缓存可以应用在 Web 应用程序的任何层级任何位置。 

在服务器端，在较的低层面，缓存可能用于存储基础数据，例如从数据库中取出的最新文章列表； 

在较高的层面，缓存可能用于存储一段或整个 Web 页面， 例如最新文章的渲染结果。

在客户端，HTTP 缓存可能用于 将最近访问的页面内容存储到浏览器缓存中。

###### redis存储缓存

可以用 redis 来存储缓存。

配置：

```
'components' => [
    'cache' => [
        // 'class' => 'yii\caching\FileCache',
        'class' => 'yii\redis\Cache',
    ],
    ...
],
```

如果没有配置过 redis 组件，需要在 cache 组件下配置 redis 服务相关参数，完整配置如下：

```
'components' => [
    'cache' => [
        'class' => 'yii\redis\Cache',
        'redis' => [
            'hostname' => 'localhost',
            'port' => 6379,
            'database' => 0,
        ],
    ],
    ...
],
```

缓存的基本使用：

```
// 获取 cache 组件
$cache = Yii::$app->cache;

// 判断 key 为 username 的缓存是否存在，有则打印，没有则赋值
$key = 'username';
if ($cache->exists($key)) {
    var_dump($cache->get($key));
} else {
    $cache->set($key, 'xiaozhang', 5 * 60);
}
```

使用文件缓存（FileCache）时，缓存是存储在 runtime/cache 目录下；
改用 redis 缓存后，缓存将存储在 redis 数据库中，性能将大大提高。

<br/><br/><br/><br/><br/>
#### 参考资料

缓存 <https://www.yiichina.com/doc/guide/2.0/caching-overview>

数据缓存 <https://www.yiichina.com/doc/guide/2.0/caching-data>

片段缓存 <https://www.yiichina.com/doc/guide/2.0/caching-fragment>

页面缓存 <https://www.yiichina.com/doc/guide/2.0/caching-page>

HTTP 缓存 <https://www.yiichina.com/doc/guide/2.0/caching-http>

yii2-redis 扩展详解 <https://www.yiichina.com/doc/guide/2.0/yii2-redis>

yii2-redis 扩展详解 <https://www.yiichina.com/tutorial/1608>

Yii2 Redis使用 <https://ibaiyang.github.io/blog/yii2/2019/01/03/Yii2-Redis%E4%BD%BF%E7%94%A8.html>

查询缓存 <https://www.yiichina.com/doc/guide/2.0/caching-data#query-caching>

缓存组件 <https://www.yiichina.com/doc/guide/2.0/caching-data#cache-components>


