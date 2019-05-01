---
layout: post
categories: Yii2
title: Yii2 session使用
meta: session使用
---
* content
{:toc}

#### 正文

session使用举例：

```
$session = \Yii::$app->session;

(一)、添加一个session
$session->set('name_string','value');
$session->set('name_array',[1,2,3]);  

(二)、读取一个session
$session->get('name_string');
$session->get('name_array');  

(三)、删除一个session
$session->remove('name_array');  

(四)、删除所有session
$session->removeAll();

(五)、判断session是否已定义
$session->has( 'name_string' )
```

如果Yii::$app->session这样用报错： Unknown component ID: session

可以这样用：

```
use yii\web\Session;

$session = new Session;
$session->open();
// 下面和上面一样用

```

不过还是建议用component组件的方式来用，在config配置文件中components数组下写入：
```
'session' => [
    'class' => 'yii\web\Session',
    'name' => 'test-api',  // 可以不写，yii2默认命名
],
```


<br/><br/><br/><br/><br/>
#### 参考资料

<https://www.yiichina.com/doc/guide/2.0/runtime-sessions-cookies>

<https://www.yiichina.com/tutorial/840>

应用组件 <https://www.yiichina.com/doc/guide/2.0/structure-application-components>

服务定位器（Service Locator） <https://www.yiichina.com/doc/guide/2.0/concept-service-locator>