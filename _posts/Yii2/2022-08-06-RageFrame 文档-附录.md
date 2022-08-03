---
layout: post
categories: Yii2
title: RageFrame 文档-附录
meta: RageFrame 文档-附录
---
* content
{:toc}

## 附录

### 安全防范

- XSS攻击防范
- CSRF攻击防范
- SQL注入
- Nginx指定目录禁止运行PHP

#### XSS攻击

直接显示的数据加上，防止XSS攻击

```
// 页面直接输出
\common\helpers\Html::encode($title) // 纯文本
\common\helpers\HtmlPurifier::process($content) // html显示的文本 HtmlPurifier帮助类的处理过程较为费时，建议增加缓存

// json输出
\yii\helpers\Json::htmlEncode($content);
```
#### CSRF攻击防范

如果不用Yii2自带的表单组件可在form表单加入这个开启csrf防范
```
\common\helpers\Html::csrfMetaTags()
```

#### SQL注入

请用Yii2自带的AR或者DB操作类来防范SQL注入

#### Nginx指定目录禁止运行PHP

这段配置文件一定要放在匹配 `.php` 的规则前面才可以生效，防止上传可执行文件攻击

```
// 单个目录
location ~* ^/attachment/.*\.(php|php5)$ 
{
    deny all;
}

// 多个目录
location ~* ^/(attachment|uploads)/.*\.(php|php5)$ 
{
    deny all; 
}
```

其他

```
# deny accessing php files for the /assets directory
location ~ ^/assets/.*\.php$ {
    deny all;
}
```

### SearchModel

- 示例一
- 示例二
- 示例三

> 该模型方便用户查询，不再每次都要单独的 SearchModel

```
use common\models\base\SearchModel;
```

**示例一**

```
$searchModel = new SearchModel([
   'model' => Topic::class,
   'scenario' => 'default',
]);

$dataProvider = $searchModel->search(Yii::$app->request->queryParams);

return $this->render('index', [
   'dataProvider' => $dataProvider,
   'searchModel' => $searchModel,
]);
```
   
**示例二**


```
$searchModel = new SearchModel([
   'defaultOrder' => ['id' => SORT_DESC],
   'model' => Topic::class,
   'scenario' => 'default',
   'relations' => ['comment' => []], // 关联表（可以是Model里面的关联）
   'partialMatchAttributes' => ['title'], // 模糊查询
   'pageSize' => 15
]);

$dataProvider = $searchModel->search(Yii::$app->request->queryParams);
$dataProvider->query->andWhere([Topic::tableName() . '.user_id' => 23, Comment::tableName() . '.status' => 1]);

return $this->render('index', [
   'dataProvider' => $dataProvider,
   'searchModel' => $searchModel,
]);
  ```
  
**示例三**
  
> 关联多表查询
  
```
$searchModel = new SearchModel([
     'defaultOrder' => ['id' => SORT_DESC],
     'model' => Topic::class,
     'scenario' => 'default',
     'relations' => ['member' => ['nickname']], // 关联 member 表的 nickname 字段
     'partialMatchAttributes' => ['code', 'member.nickname'], // 模糊查询，注意 member_nickname 为关联表的别名 表名_字段
     'pageSize' => 15
]);

$dataProvider = $searchModel->search(Yii::$app->request->queryParams);
$dataProvider->query->andWhere([Topic::tableName() . '.user_id' => 23, Comment::tableName() . '.status' => 1]);

return $this->render('index', [
     'dataProvider' => $dataProvider,
     'searchModel' => $searchModel,
]);
```

GridView 片段

```
[
    'attribute' => 'member.nickname',
    'label'=> '昵称',
    'filter' => Html::activeTextInput($searchModel, 'member.nickname', [
            'class' => 'form-control'
        ]
    ),
],
```

来源：https://getyii.com/topic/364

### 应用配置独立域名

#### 1、在群里([加群链接](https://jq.qq.com/?_wv=1027&k=4BeVA2r))下载应用配置独立域名包：

解压后可见有2个文件夹

- environments 目录
- web 目录

#### 2、替换根目录下的 `environments` 为下载内 `environments`

#### 3、将下载好的web目录整个单独放入各种的应用下例如：

```
backend/web
frontend/web
api/web
html5/web
merchant/web
oauth2/web
```

#### 4、将根目录web下各自的resources放入各自的web下例如：

```
web/resources => frontend/web/resources
web/backend/resources => backend/web/resources
web/html5/resources => html5/web/resources
web/merchant/resources => merchant/web/resources
web/oauth2/resources => oauth2/web/resources
```

> 注意: api是没有此目录


#### 5、设置各自的应用的虚拟站点到各自应用的web下例如：

```
127.0.0.1       rageframe.local
127.0.0.1       backend.rageframe.local
127.0.0.1       html5.rageframe.local
127.0.0.1       merchant.rageframe.local
127.0.0.1       api.rageframe.local
127.0.0.1       oauth2.rageframe.local
127.0.0.1       storage.rageframe.local
```

#### 6、修改 `common/config/bootstrap.php` 下的 `attachment`和`attachurl`：

```
Yii::setAlias('@attachment', dirname(dirname(__DIR__)) . '/storage/web/attachment');
// 注意这里的域名为你的文件存储域名,记得带上http://前缀
Yii::setAlias('@attachurl', 'http://storage.rageframe.local/attachment');
```

#### 7、修改 `common/config/params.php` 

关于 fullPath 变量都设置为false，注意有4处

```
'fullPath' => false, // 是否开启返回完整的文件路径
```

并在 `common/config/bootstrap.php` 里面填写对应的域名信息例如：

```
Yii::setAlias('@backendUrl', 'http://backend.rageframe.local');
Yii::setAlias('@frontendUrl', 'http://frontend.rageframe.local');
Yii::setAlias('@html5Url', 'http://html5.rageframe.local');
Yii::setAlias('@merchantUrl', 'http://merchant.rageframe.local');
Yii::setAlias('@apiUrl', 'http://api.rageframe.local');
Yii::setAlias('@oauth2Url', 'http://oauth2.rageframe.local');
```

#### 8、删除根目录下的`web`目录

### 代码模板

目录

- 首页
- 编辑/创建页
- Ajax 模态框

#### 首页

示例一

```
<?php

use yii\grid\GridView;
use common\helpers\Html;

$this->title = '代码模板';
$this->params['breadcrumbs'][] = ['label' => $this->title, 'url' => ['index']];
?>

<div class="row">
    <div class="col-xs-12">
        <div class="box">
            <div class="box-header">
                <h3 class="box-title"><?= $this->title; ?></h3>
            </div>
            <div class="box-body table-responsive">
                <!-- 你的显示代码 -->
            </div>
        </div>
    </div>
</div>

 ```
 
 示例二
 
  ```
<div class="row">
    <div class="col-sm-12">
        <div class="nav-tabs-custom">
            <ul class="nav nav-tabs">
                <li class="active"><a href="<?= Url::to(['test/index1']) ?>"> 测试1</a></li>
                <li><a href="<?= Url::to(['test/index2']) ?>"> 测试2</a></li>
                <li class="pull-right">
                    <?= Html::create(['ajax-edit'], '创建', [
                        'data-toggle' => 'modal',
                        'data-target' => '#ajaxModalLg',
                    ]); ?>
                </li>
            </ul>
            <div class="tab-content">
                <div class="active tab-pane">
                    <!-- 你的显示代码 -->
                </div>
            </div>
        </div>
    </div>
</div>
  ```
 
#### 编辑/创建页
 
 ```
 <?php
 
 use yii\widgets\ActiveForm;
 use common\enums\GenderEnum;
 use common\enums\StatusEnum;
 
 $this->title = '编辑';
 $this->params['breadcrumbs'][] = ['label' => '首页', 'url' => ['index']];
 $this->params['breadcrumbs'][] = ['label' => $this->title];
 ?>
 
<div class="row">
 <div class="col-lg-12">
     <div class="box">
         <div class="box-header with-border">
             <h3 class="box-title">基本信息</h3>
         </div>
         <?php $form = ActiveForm::begin([
             'fieldConfig' => [
                 'template' => "<div class='col-sm-2 text-right'>{label}</div><div class='col-sm-10'>{input}{hint}{error}</div>",
             ]
         ]); ?>
         <div class="box-body">
             <!-- 你的表单代码 -->
         </div>
         <!-- /.box-body -->
         <div class="box-footer">
             <div class="col-sm-12 text-center">
                 <button class="btn btn-primary" type="submit">保存</button>
                 <span class="btn btn-white" onclick="history.go(-1)">返回</span>
             </div>
         </div>
         <?php ActiveForm::end(); ?>
     </div>
 </div>
</div>
```

#### Ajax 模态框

```
<?php

use yii\widgets\ActiveForm;
use common\helpers\Url;

$form = ActiveForm::begin([
    'id' => $model->formName(),
    'enableAjaxValidation' => true,
    'validationUrl' => Url::to(['ajax-edit', 'id' => $model['id']]),
    'fieldConfig' => [
        'template' => "<div class='col-sm-2 text-right'>{label}</div><div class='col-sm-10'>{input}\n{hint}\n{error}</div>",
    ]
]);
?>

<div class="modal-header">
    <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">×</span></button>
    <h4 class="modal-title">基本信息</h4>
</div>
<div class="modal-body">
     <!-- 你的表单代码 -->
</div>
<div class="modal-footer">
    <button type="button" class="btn btn-white" data-dismiss="modal">关闭</button>
    <button class="btn btn-primary" type="submit">保存</button>
</div>

<?php ActiveForm::end(); ?>
```

### 线上性能优化
 
#### 1、开启生产环境，在项目初始化的时候执行以下代码，并配置项目信息
 
 ```
 php init // 然后输入1回车,再输入yes回车
 ```
 
#### 2、开启 OPC 缓存  
#### 3、开启 AR 数据库的 schema 缓存
 
> 注意：如果修改数据结构，在更新完SQL语句之后需要先关闭Schema再开启，数据结构的修改才会生效，或者直接清空缓存
 
```
return [
    // ...
    'components' => [
        // ...
        'cache' => [
            'class' => 'yii\caching\FileCache',
        ],
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=127.0.0.1;dbname=rageframe',
            'username' => 'root',
            'password' => '',
            'enableSchemaCache' => true, // 是否开启缓存
            'schemaCacheDuration' => 3600, // 缓存时间
            'schemaCache' => 'cache', // 缓存名称
        ],
    ],
];
```

#### 4、使用别的存储方式session
  
例如：redis、memcache、mysql
  
#### 5、多使用局部缓存，整页缓存 ，http 缓存等，复杂数据库查询也可以做缓存依赖
 
#### 6、数据库索引等优化，尽量多的使用视图（当有必要）

#### 7、查询操作limit限制

查询结果使用AsArray，这样可以节省内存因为这样返回的是数组，而不是对象，譬如：
```
$posts = Post::find()->orderBy('id desc')->limit(100)->asArray()->all();
```
#### 8、最小化的使用assets

这个玩意，是需要加载生成js和css的

#### 9、Composer Atuoloader 优化

命令：
```
php composer.phar dumpautoload -o
```
#### 10、通过脚本处理中间数据
  
可以通过cron定时任务批量处理数据，譬如产品的特价，产品的过滤等等

### 团队编辑器代码规范

- PhpStorm 原生支持 PSR-2

#### PhpStorm 原生支持 PSR-2

设置

```
Preferences -> Editor -> Code Style -> PHP : Set from… -> Predefined Style : PSR1/PSR2
```

快捷键快速格式化

```
ctrl + alert + l
```

PHP_CodeSniffer 文档：https://github.com/squizlabs/PHP_CodeSniffer/blob/master/README.md

更多安装文档：https://segmentfault.com/a/1190000019136556

### PHP编程规范

```php
<?php

namespaceStandard; // 顶部命名空间
// 空一行
use TestTestClass; //use引入类

/**
 * 类描述
 *
 * 类名必须大写开头驼峰.
 */
abstract class StandardExample
// {}必须换行
{
    /**
     *  常量描述.
     *
     * @var string
     */
    const THIS_IS_A_CONST = ''; // 常量全部大写下划线分割
    
    /**
     * 属性描述.
     *
     * @var string
     */
    public $nameTest = ''; // 属性名称建议开头小写驼峰 protected 一样
    // 成员属性必须添加public（不能省略）， private, protected修饰符
    
    /**
     * 属性描述.
     *
     * @var string
     */
    private $_privateNameTest = ''; // 类私有成员属性，建议下划线小写开头驼峰
    
    /**
     * 构造函数.
     *
     * 构造函数描述
     *
     * @param  string $value 形参名称/描述
     */
    public function __construct($value = '') // 成员方法必须添加public（不能省略）， private, protected修饰符
    { // {}必须换行
        $this->nameTest = new TestClass();
        // 链式操作
        $this->nameTest->functionOne()->functionTwo()->functionThree();
        // 一段代码逻辑执行完毕 换行
        // code...
        
    }
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $value 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     * 返回值类型：string，array，object，mixed（多种，不确定的），void（无返回值）
     */
    public function testFunction($value = '') // 成员方法必须小写开头驼峰
    {
        // code...
        
    }
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $value 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     */
    private function _privateTestFunction($value = '') // 私有成员方法建议下划线小写开头驼峰
    {
        // code...
        
    }
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $value 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     */
    public static function staticFunction($value = '') // static位于修饰符之后
    {
        // code...
        
    }
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $value 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     */
    abstract public function abstractFunction($value = ''); // abstract位于修饰符之前
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $value 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     */
    final public function finalFunction($value = '') // final位于修饰符之前
    {
        // code...
        
    }
    
    /**
     * 成员方法名称.
     *
     * 成员方法描述
     *
     * @param string $valueOne 形参名称/描述
     * @param string $valueTwo 形参名称/描述
     * @param string $valueThree 形参名称/描述
     * @param string $valueFour 形参名称/描述
     * @param string $valueFive 形参名称/描述
     * @param string $valueSix 形参名称/描述
     *
     * @return 返回值类型 返回值描述
     */
    public function tooLangFunction($valueOne = '', // 变量命名可小写开头驼峰或者下划线命名,个人那习惯，据说下划线可读性好
    $valueTwo = '', $valueThree = '', $valueFour = '', $valueFive = '', $valueSix = '') // 参数过多换行
    {
        if ($valueOne === $valueTwo) { // 控制结构=>后加空格,同{一行，（右边和)左边不加空格
            // code...
            
        }
        
        switch ($valueThree) {
            case 'value':
                // code...
                
            break;
            default:
                // code...
                
            break;
        }
        
        do {
            // code...
            
        } while ($valueFour <= 10);
        
        while ($valueFive <= 10) {
            // code...
            
        }
        
        for ($i = 0;$i < $valueSix;$i++) {
            // code...
            
        }
    }
}
```

### Yii2核心框架代码风格

参阅 <https://ibaiyang.github.io/blog/yii2/2022/07/29/Yii2-核心框架代码风格.html>

### 扩展依赖安装使用

#### MongoDB

安装

这个扩展需要MongoDB PHP扩展版本1.0.0或更高。  
这个扩展需要MongoDB服务器版本3.0或更高版本。  

执行

```
composer require --prefer-dist yiisoft/yii2-mongodb
```

或 composer.json 里面添加

```
"yiisoft/yii2-mongodb": "~2.1.0"
```

要使用这个扩展,只需在应用程序中添加以下代码  
配置:

```
return [
    //....
    'components' => [
        'mongodb' => [
            'class' => '\yii\mongodb\Connection',
            'dsn' => 'mongodb://@localhost:27017/mydatabase',
            'options' => [
                "username" => "Username",
                "password" => "Password"
            ]
        ],
    ],
];
```

增加说明

```
    'mongodb' => [
        'class' => 'yii\mongodb\Connection',
        // 有账户的配置
        //'dsn' => 'mongodb://username:password@localhost:27017/datebase',
        // 无账户的配置
        'dsn' => 'mongodb://127.0.0.1:27017/fecshop',
        // 复制集
        //'dsn' => 'mongodb://10.10.10.252:10001/erp,mongodb://10.10.10.252:10002/erp,mongodb://10.10.10.252:10004/erp?replicaSet=terry&readPreference=primaryPreferred',
    ],
```

debug配置

```
$config['modules']['debug'] = [
    'class' => 'yii\debug\Module',
    'panels' => [
        'mongoDB' => \yii\mongodb\debug\MongoDbPanel::class,
    ],
];
```




### 网址收录

- Window
  - [PHP版本下载](http://windows.php.net/download/)
  - [PHP相关扩展下载](https://pecl.php.net/package-stats.php)
- Linux
  - [Swoole](https://www.swoole.com/)
- 系统通用
  - [Redis](https://redis.io/)
- 其他
  - [MongoDB中文社区](http://www.mongoing.com)
  - [FFmpeg](http://ffmpeg.org/)
 



<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

