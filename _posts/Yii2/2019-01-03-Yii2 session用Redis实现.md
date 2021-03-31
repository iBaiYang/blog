---
layout: post
categories: Yii2
title: Yii2 session用Redis实现
meta: session除了服务器内存实现，还可以用Redis实现，下面说一下。
---
* content
{:toc}

#### 正文

session除了服务器内存实现，还可以用Redis实现，下面说一下。

Yii2中对session处理进行操作，可以用服务器内存实现：

```
'components' => [
    'session' => [
        'class' => 'yii\web\Session',
        'name' => 'test-api',
    ],
]
```

如果项目部署在集群环境，使用文件方式的session，你就会发现访问项目时一下是登录状态、一下是离线状态，这是因为session文件只在一台服务器上，
当你被nginx引导到另一台服务器时，肯定是未登录状态。这时我们可以使用redis实现session，解决这个问题。

如果用Redis实现（记得先引入yiisoft/yii2-redis）：

```
'components' => [
    'session' => [
        'class' => 'yii\redis\Session',
        'timeout' => 86400,  // 1天
        'keyPrefix'=>'test_',
        'redis' => [
            'class' => 'yii\redis\Connection',
            'hostname' => 'test.redis.yuming.com',
            'port' => 6379,
            'database' => 0,
        ],
    ],
],
```

如果配置过redis服务，则session配置：

```
'components' => [
    'session' => [
        'class' => 'yii\redis\Session',
        'timeout' => 86400,  // 1天
        'keyPrefix'=>'test_',
    ],
],
```

设置session：

```
$session = Yii::$app->session;

// 设置一个session变量
$session->set('language', 'en-US');

// 获取session中的变量值 
$language = $session->get('language');

// 删除一个session变量
$session->remove('language');

// 检查session变量是否已存在
if ($session->has('language')) ...

// 遍历所有session变量
foreach ($session as $name => $value) ...
```

<br/><br/><br/><br/><br/>
#### 参考资料

Sessions 和 Cookies <https://www.yiichina.com/doc/guide/2.0/runtime-sessions-cookies>

Yii2 session使用 <https://ibaiyang.github.io/blog/yii2/2018/07/20/Yii2-session%E4%BD%BF%E7%94%A8.html>

yii2-redis 扩展详解-会话组件 <https://www.yiichina.com/doc/guide/2.0/yii2-redis?language=zh-cn>

Yii2 Redis使用 <https://ibaiyang.github.io/blog/yii2/2019/01/03/Yii2-Redis%E4%BD%BF%E7%94%A8.html>

