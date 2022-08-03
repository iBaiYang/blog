---
layout: post
categories: Yii2
title: RageFrame 文档-系统开发
meta: RageFrame 文档-系统开发
---
* content
{:toc}

##  系统开发

### 目录结构

目录 | 说明
---|---
**addons** | 模块插件目录
**api** | 接口应用目录
**backend** | 总后台应用目录
--- tests | 单元测试文件目录
--- modules | 模块
--- --- base | 系统管理模块目录
--- --- common | 公共模块目录
**common** | 公用应用目录
--- tests | 单元测试文件目录
--- models | 公共模型目录
--- --- backend | 系统模块模型目录
--- --- member | 会员模块模型目录
--- --- common | 公用模型目录
--- behaviors | 公共行为目录
--- components | 公共组件目录
--- enums | 枚举目录
--- helpers | 小助手目录
--- replace | 重写、替换文件目录
--- mail | 邮件模版目录
**console** | 控制台目录
--- migrations | 数据迁移目录
**environments** | 初始化设置目录
**storage** | 存储目录
**services** | 服务层目录
**vendor** | 第三方加载文件目录
**frontend** | 前台目录
--- tests | 单元测试文件目录
**merchant** | 商户应用目录
--- tests | 单元测试文件目录
**oauth2** | oauth2 接口应用目录
**merapi** | 商户接口应用目录
**html5** | html5应用目录
--- tests | 单元测试文件目录
**web** | 网站统一入口目录

### 开发规范

目录

- 开发说明
- 编码规范
- 框架规范
- 插件规范
- 数据库规范
- 类功能规范

#### 开发说明

- 正常开发只需要开发商户端，没有 Saas 的时候商户端就是总后台，有了 Saas，商户端就是子后台
- 插件勾选了商户映射，则所有的代码开发插件对应目录的 merchant 下开发，总后台(backend)访问的 url 对应的代码在 merchant 下
- 插件不勾选商户映射，总后台和商家后台的插件代码都是独立的

#### 编码规范

- 符合 PSR-1/PSR-2 的 PHP 编程
- 方法和函数有注释，注释内容包括功用，参数，返回值，必要时还有示例
- 方法命名规范，驼峰命名，不能出现下划线
- 类属性规范，类属性和类方法一致,统一使用驼峰
- 函数命名 小写字母加下划线
- 常量全部大写下划线分割
- 变量、函数名一律为小写格式
- 属性名称开头小写驼峰
- 类私有成员属性，下划线小写开头驼峰
- 以标准计算机英文为蓝本，杜绝一切拼音、或拼音英文混杂的命名方式
- PHP 的内建值 true、false 和 null 必须全部采用小写字母书写
- 文件编码为 UTF-8，并关闭 UTF-8 BOM((Byte Order Mark))的功能。切记请不要使用windows自带的记事本编辑项目文件

#### 框架规范

- 自定义助手函数都放在 `common\helpers\` 且方法都为static方法
- Url助手函数必须使用 `common\helpers\Url`，用法和Yii2自带一样多了功能
- Html助手函数必须使用 `common\helpers\Html`，用法和Yii2自带一样多了功能
- 模型统一在`common\models\`下对应的是各个模块的模型，私有的表单模型可在各种应用/模块下面创建
- 状态枚举统一调用`common\enums\StatusEnum.php`中的常量和属性
- 每个应用下面应有一个基础控制器，做为该应用的父控制器，方便做一些公共操作,默认父类控制器为`BaseController`
- 超级管理员在各自应用下的 `config\params.php` 的adminAccount项中定义其UID，超级管理员不需要进行RBAC权限检查
- 公共别名在 `common\config\bootstrap.php` 中定义，使用 `Yii::getAlias()` 访问
- View 视图调用 `Model` 的变量及数据不能直接调用，须控制器传递才可使用
- Service 层在根目录下，统一带上`Service`后缀，例如`TestService`
- Service 层不允许使用事务
- Service 层如果需要当前用户信息必须通过传递参数获取
- 商户 ID 为 1 的默认被总后台占用，如果使用多商户请不要占用该 ID

#### 插件规范

- 插件命名统一大写驼峰
- 资源文件在 当前插件根目录/应用/resource下
- 模型为当前插件的common/models下
- 更新文件更新表结构时候无法触发事务，需要单独设定版本号避免全部事务触发失败

#### 数据库规范

命名规范

- 数据表名小写，多关键字使用下划线分割（关键字尽量全称）
- 字段名小写，多关键字使用下划线分割（关键字尽量全称）
- 禁止使用保留字并且尽量少用含有关键词来命名
- 临时表必须以tmp_开头、以日期结尾，备份表必须以bak_开头、以日期结尾

基础规范

- 所有的字段必须添加注释
- 尽可能地使用InnoDB作为表的存储引擎
- 数据库和数据表统一使用 `UTF8MB4` 字符编码
- 所有的表和字段必须添加注释
- 尽量控制表行数在500万以内
- 尽可能采用冷热数据分离策略
- 禁止以图片、文件等二进制数据

表设计规范

- 尽可能每张表的索引数量控制在5个以内
- 每一张InnoDB表都必须含有一个主键
- 尽可能避免使用外键约束
- 设置数据表架构应考虑后期扩展型
- 遵循范式与冗余平衡原则

字段设计规范

- 尽可能将所有的数据列定义为 `NOT NULL` 类型
- 避免 ENUM 数据类型
- json 存储的数据用 `json`字段代替 `text`
- 表与表关联的键名保持一致或以关联表名的缩写为前缀
- 固定长度的字符串字段务必使用 `char`
- 使用 `UNSIGNEG` 存储非负整数
- 禁止敏感数据以明文形式存储
- 金额相关的数据必须使用 `DECIMAL` 数据类型
- 尽量所有表有 `status` 字段来标注数据状态(-1:已删除,0:禁用,1:正常)，业务状态请使用其他字段；`status`字段类型 为带符号的 `tinyint(4)`。如果还需要其他的数据状态 请先判断该状态的数据是有用的数据还是无意义的数据,有用的数据状态 > 2,无意义的数据状态 < -1
- 所有的删除（除开清空回收站操作） 请标记 `status` 为 -1
- 创建时间字段为`created_at`，修改时间字段为`updated_at`，类型int(10)
- 用户关联 id 为 `member_id`
- 商户 id 为 `merchant_id`
- 多应用用户 id 混合使用字段用 `user_id`
- 排序字段为 `sort`
- 区分应用字段为 `app_id`
- 区分插件来源需要增加字段为 `addons_name` 和 `is_addon`

#### 类功能规范

##### Model(模型)

- 数据库字段管理
- 数据库关联管理
- 数据库触发行为
- 数据库入库验证

##### Form(模型 - 验证)

- 对提交的复杂数据进行验证处理返回

##### View(视图)

- 数据处理显示

##### Controller(控制器)

- 数据接收
- 服务调用
- 简单CURD入库
- 页面渲染

##### Service(服务 - 包含仓库「Repository」功能)

- 数据逻辑处理
- 数据入库
- 数据查询 (Repository)

### 控制台

目录

- 自带默认任务
- 数据迁移
- 定时任务

#### 自带默认任务

```
// 定时拉取钉钉提醒
yii pull-remind/ding-talk

// 定时拉取系统提醒
yii pull-remind/sys

// 定时清理微信历史消息(需安装微信插件)
yii wechat/msg-history/index

// 定时群发微信消息(需安装微信插件)
yii wechat/send-message/index
```

#### 数据迁移

备份表

```
 # 备份全部表
php ./yii migrate/backup all
 
php ./yii migrate/backup table1,table2,table3... # 备份多张表
php ./yii migrate/backup table1 #备份一张表
```

恢复全部表

```
php ./yii migrate/up
```

#### 定时任务

> 注意需要在Linux环境下运行，且让PHP的system函数取消禁用  
> 表达式帮助：[cron表达式生成器](http://cron.qqe2.com/)

表达式:

```
Linux
*    *    *    *    *    *
-    -    -    -    -    -
|    |    |    |    |    |
|    |    |    |    |    + year [optional]
|    |    |    |    +----- day of week (0 - 7) (Sunday=0 or 7)
|    |    |    +---------- month (1 - 12)
|    |    +--------------- day of month (1 - 31)
|    +-------------------- hour (0 - 23)
+------------------------- min (0 - 59)
```

1、需要先设置cron ，让 ./yii schedule/run --scheduleFile=@console/config/schedule.php 可以每分钟运行。

例如:

```
// 每分钟执行一次定时任务
* * * * * /path-to-your-project/yii schedule/run --scheduleFile=@console/config/schedule.php 1>> /tmp/rageframeConsoleLog.text 2>&1
```

2、在 console/config/schedule.php 中加入新的定时任务：

```
/**
 * 清理过期的微信历史消息记录
 *
 * 每天凌晨执行一次
 */
$schedule->command('test/index')->cron('0 0 * * *');

/**
 * 定时群发微信消息
 *
 * 每分钟执行一次
 */
$schedule->command('test/index')->cron('* * * * *');
```

4、具体例子

查看控制器 `console\controllers\MsgHistoryController`

更多使用文档：https://github.com/omnilight/yii2-scheduling

### 表单控件

目录

- 颜色选择器
- 日期控件
- 时间控件
- 日期时间控件
- 日期区间控件
- 图片上传控件
- 多图上传控件
- 文件上传控件
- 多文件上传控件
- 图片裁剪上传
- 多 Input 框控件
- 地图经纬度选择
- Select2
- 省市区控件
- 省市区控件(复选框)
- 百度编辑器
- Markdown 编辑器
- 表情包选择
- 递归多级联动选择器
- TreeGrid

#### 颜色选择器

```
<?= $form->field($model, 'color')->widget(kartik\color\ColorInput::class, [
    'options' => ['placeholder' => '请选择颜色'],
]);?>
```

#### 日期控件

```
<?= $form->field($model, 'date')->widget(kartik\date\DatePicker::class, [
    'language' => 'zh-CN',
    'layout'=>'{picker}{input}',
    'pluginOptions' => [
        'format' => 'yyyy-mm-dd',
        'todayHighlight' => true, // 今日高亮
        'autoclose' => true, // 选择后自动关闭
        'todayBtn' => true, // 今日按钮显示
    ],
    'options'=>[
        'class' => 'form-control no_bor',
    ]
]);?>
```

#### 时间控件

```
<?= $form->field($model, 'time')->widget(kartik\time\TimePicker::class, [
    'language' => 'zh-CN',
    'pluginOptions' => [
        'showSeconds' => true
    ]
]);?>
```

#### 日期时间控件

```
<?= $form->field($model, 'start_time')->widget(kartik\datetime\DateTimePicker::class, [
    'language' => 'zh-CN',
    'options' => [
        'value' => $model->isNewRecord ? date('Y-m-d H:i:s') : date('Y-m-d H:i:s',$model->start_time),
    ],
    'pluginOptions' => [
        'format' => 'yyyy-mm-dd hh:ii',
        'todayHighlight' => true, // 今日高亮
        'autoclose' => true, // 选择后自动关闭
        'todayBtn' => true, // 今日按钮显示
    ]
]);?>
```

#### 日期区间控件

```
use kartik\daterange\DateRangePicker;
```

```
$addon = <<< HTML
<span class="input-group-addon">
    <i class="glyphicon glyphicon-calendar"></i>
</span>
HTML;
```

```
<?= DateRangePicker::widget([
    'name' => 'queryDate',
    'value' => date('Y-m-d') . '-' . date('Y-m-d'),
    'readonly' => 'readonly',
    'useWithAddon' => true,
    'convertFormat' => true,
    'startAttribute' => 'from_date',
    'endAttribute' => 'to_date',
    'startInputOptions' => ['value' => date('Y-m-d')],
    'endInputOptions' => ['value' => date('Y-m-d')],
    'pluginOptions' => [
        'locale' => ['format' => 'Y-m-d'],
    ]
]) . $addon;?>
```

具体参考：http://demos.krajee.com/date-range

#### 图片上传控件

> 配置部分在 `common\config\params.php` 文件，可用自行在里面切换全局上传配置  
> 注意OSS/七牛暂不支持切片和缩略图操作，以下是完整案例

```
<?= $form->field($model, 'cover')->widget('common\widgets\webuploader\Files', [
        'config' => [
              // 可设置自己的上传地址, 不设置则默认地址
              // 'server' => '',
             'pick' => [
                 'multiple' => false,
             ],
             'formData' => [
                 // 不配置则不生成缩略图
                 'thumb' => [
                     [
                         'width' => 100,
                         'height' => 100,
                     ],
                     [
                         'width' => 200,
                         'height' => 200,
                     ],
                 ],
                 'drive' => 'local',// 默认本地 支持 qiniu/oss/cos 上传
             ],
             'chunked' => false,// 开启分片上传
             'chunkSize' => 512 * 1024,// 分片大小
             'independentUrl' => false, // 独立上传地址, 如果设置了true则不受全局上传地址控制 
         ],
]);?>
```
获取缩略图路径查看 [字符串辅助类](helper-string.md) 的 `获取缩略图地址`  
config 更多参考 http://fex.baidu.com/webuploader/doc/

#### 多图上传控件

> 配置部分在 `common\config\params.php` 文件，可用自行在里面切换全局上传配置  
> 注意传入的value值为数组,例如: array('img1.jpg', 'img2.jpg')

```
<?= $form->field($model, 'covers')->widget('common\widgets\webuploader\Files', [
        'config' => [ // 配置同图片上传
             // 'server' => '',
             'pick' => [
                 'multiple' => true,
             ],
             'formData' => [
                 // 不配置则不生成缩略图
                 // 'thumb' => [
                 //     [
                 //         'width' => 100,
                 //         'height' => 100,
                 //     ],
                 // ]
             ],
        ]
]);?>

config 更多参考 http://fex.baidu.com/webuploader/doc/
```

#### 文件上传控件

> 配置部分在 `common\config\params.php` 文件，可用自行在里面切换全局上传配置  
> 注意文件上传不支持缩略图配置

```
<?= $form->field($model, 'file')->widget('common\widgets\webuploader\Files', [
     'type' => 'files',
     'config' => [ // 配置同图片上传
         // 'server' => \yii\helpers\Url::to(['file/files']), // 默认files 支持videos/voices/images方法验证
         'pick' => [
             'multiple' => false,
         ]
     ]
]);?>
```

#### 多文件上传控件

> 配置部分在 `common\config\params.php` 文件，可用自行在里面切换全局上传配置  
> 注意多文件上传不支持缩略图配置  
> 注意传入的value值为数组,例如: array('img1.jpg', 'img2.jpg')

```
<?= $form->field($model, 'files')->widget('common\widgets\webuploader\Files', [
     'type' => 'files',
     'config' => [ // 配置同图片上传
          // 'server' => '',
         'pick' => [
             'multiple' => true,
         ]
     ]
]);?>
```

#### 图片裁剪上传

```
<?= $form->field($model, 'head_portrait')->widget(\common\widgets\cropper\Cropper::class, [
        'config' => [
              // 可设置自己的上传地址, 不设置则默认地址
              // 'server' => '',
         ],
        'formData' => [
            'drive' => 'local',// 默认本地 支持 qiniu/oss/cos 上传
        ],
]); ?>
```

#### 多Input框控件

```
<?= $form->field($model, 'schedule')->widget(unclead\multipleinput\MultipleInput::class, [
    'max' => 4,
    'columns' => [
        [
            'name'  => 'user_id',
            'type'  => 'dropDownList',
            'title' => 'User',
            'defaultValue' => 1,
            'items' => [
                1 => 'User 1',
                2 => 'User 2'
            ]
        ],
        [
            'name'  => 'day',
            'type'  => \kartik\date\DatePicker::class,
            'title' => 'Day',
            'value' => function($data) {
                return $data['day'];
            },
            'items' => [
                '0' => 'Saturday',
                '1' => 'Monday'
            ],
            'options' => [
                'pluginOptions' => [
                    'format' => 'dd.mm.yyyy',
                    'todayHighlight' => true
                ]
            ]
        ],
        [
            'name'  => 'priority',
            'title' => 'Priority',
            'enableError' => true,
            'options' => [
                'class' => 'input-priority'
            ]
        ]
    ]
 ]);
?>
```
更多参考：https://github.com/unclead/yii2-multiple-input

#### 地图经纬度选择

```
// 注意提前申请好对应地图的key
<?= $form->field($model, 'address')->widget(\common\widgets\selectmap\Map::class, [
    'type' => 'amap', // amap高德;tencent:腾讯;baidu:百度
]); ?>
```

#### Select2

```
<?= $form->field($model, 'tag')->widget(kartik\select2\Select2::class, [
    'data' => [
        1 => "First", 2 => "Second", 3 => "Third",
        4 => "Fourth", 5 => "Fifth"
    ],
    'options' => ['placeholder' => '请选择'],
    'pluginOptions' => [
        'allowClear' => true
    ],
]);?>
```

更多参考：http://demos.krajee.com/widget-details/select2

#### 省市区控件

```
<?= \common\widgets\provinces\Provinces::widget([
    'form' => $form,
    'model' => $model,
    'provincesName' => 'province_id',// 省字段名
    'cityName' => 'city_id',// 市字段名
    'areaName' => 'area_id',// 区字段名
    // 'template' => 'short' //合并为一行显示
]); ?>
```

#### 省市区控件(复选框)

> 注意：目前一个页面仅支持调用一次本控件

触发按钮

```
 <a class="js-select-city btn btn-primary btn-sm" data-toggle="modal" data-target="#ajaxModalLgForExpress">指定地区城市</a>
```

会把选择的省中文显示在此处(可选)

```
<span class="js-region-info region-info"></span>
```

调用

```
<?= \common\widgets\area\Area::widget([
    'form' => $form,
    'model' => $model,
    'provincesName' => 'province_ids',// 省字段名
    'cityName' => 'city_ids',// 市字段名
    'areaName' => 'area_ids',// 区字段名
    'notChooseProvinceIds' => [], // 不可选省id数组
    'notChooseCityIds' => [], // 不可选市id数组
    'notChooseAreaIds' => [], // 不可选区id数组
    'level' => 3 // 可以只选省/省市/省市区对应为1/2/3
]); ?>
```

#### 百度编辑器

视图

```
<?= $form->field($model, 'content')->widget(\common\widgets\ueditor\UEditor::class) ?>

// 自定义配置参数用法
<?= $form->field($model, 'content')->widget(\common\widgets\ueditor\UEditor::class, [
     'config' => [

      ],
    'formData' => [
        'drive' => 'local', // 默认本地 支持qiniu/oss/cos 上传
        'poster' => false, // 上传视频时返回视频封面图，开启此选项需要安装 ffmpeg 命令
        'thumb' => [ // 图片缩略图
            [
                'width' => 100,
                'height' => 100,
            ],
        ]
    ],
]) ?>
```

更多文档：http://fex.baidu.com/ueditor/#start-start

#### Markdown 编辑器


```
<?= $form->field($model, 'content')->widget(\common\widgets\markdown\Markdown::class, [
        // 'server' => '', // 图片上传路径 + 驱动
]); ?>
```

解析

```
<?= \common\helpers\MarkdownHelper::toHtml($content); ?>
```

更多文档：https://pandao.github.io/editor.md/examples/index.html

#### 表情包选择

引入

```
<?= \common\widgets\emoji\Emoji::widget([
    'name' => 'emoji',
    'theme' => 'default', // 可选择微信的表情包主题: wechat
    'options' => [],
    'bind_id' => '', // 绑定的文本域 id，如果填写会自动追加表情到文本域
]); ?>
```

解析文本内的表情包

```
var text = $('.emoji').html();
html = qqWechatEmotionParser(text);
$('.emoji').html(html)
```

表情包提供回调触发事件提供用户自由发挥

```
// 表情回调 回调名称为 emoji-select-[初始化时候填写的 name]
$(document).on('emoji-select-emoji', function (e, emoji) {
    // 自由发挥，例如追加到 message 的光标文本域
    $('#message').insertAtCaret(emoji);
});
```

#### 递归多级联动选择器

使用

> 注意 ajax 这边会传递 pid 过来，只需要根据 pid 返回所有数据即可

```
<?= $form->field($model, 'cate_id')->widget(\common\widgets\selectlinkage\Linkage::class, [
    'url' => Url::to(['cate/select']), // ajax数据 要求返回数据必须是带 id 和 title 的 二维数组 json 格式
    'level' => 3, // 联动级别
    'item' => $cate, // 当前数据
    'allItem' => $cates, // 所有的数据
]); ?>
```

ajax 返回数据案例

```
{
  "code": "200",
  "message": "获取成功",
  "data": [
    {
      "id": "430",
      "title": "电子词典"
    },
    {
      "id": "431",
      "title": "电纸书"
    },
  ]
}
```

#### TreeGrid

控制器

```
use yii\web\Controller;
use Yii;
use yii\data\ActiveDataProvider;

class TreeController extends Controller
{

    /**
     * Lists all Tree models.
     * @return mixed
     */
    public function actionIndex()
    {
        $query = Tree::find();
        $dataProvider = new ActiveDataProvider([
            'query' => $query,
            'pagination' => false
        ]);

        return $this->render('index', [
            'dataProvider' => $dataProvider
        ]);
    }
```

视图


```
use jianyan\treegrid\TreeGrid;
  
<?= TreeGrid::widget([
    'dataProvider' => $dataProvider,
    'keyColumnName' => 'id',
    'parentColumnName' => 'pid',
    'parentRootValue' => '0', //first parentId value
    'pluginOptions' => [
        'initialState' => 'collapsed',
    ],
    'options' => ['class' => 'table table-hover'],
    'columns' => [
        [
            'attribute' => 'title',
            'format' => 'raw',
            'value' => function ($model, $key, $index, $column){
                return $model->title . Html::a(' <i class="icon ion-android-add-circle"></i>', ['ajax-edit', 'pid' => $model['id']], [
                        'data-toggle' => 'modal',
                        'data-target' => '#ajaxModal',
                    ]);
            }
        ],
        [
            'attribute' => 'sort',
            'format' => 'raw',
            'headerOptions' => ['class' => 'col-md-1'],
            'value' => function ($model, $key, $index, $column){
                return  Html::sort($model->sort);
            }
        ],
        [
            'header' => "操作",
            'class' => 'yii\grid\ActionColumn',
            'template'=> '{edit} {status} {delete}',
            'buttons' => [
                'edit' => function ($url, $model, $key) {
                    return Html::edit(['ajax-edit','id' => $model->id], '编辑', [
                        'data-toggle' => 'modal',
                        'data-target' => '#ajaxModal',
                    ]);
                },
                'status' => function ($url, $model, $key) {
                    return Html::status($model->status);
                },
                'delete' => function ($url, $model, $key) {
                    return Html::delete(['delete','id' => $model->id]);
                },
            ],
        ],
    ]
]); ?>
```

更多文档：https://github.com/jianyan74/yii2-treegrid

### 系统组件

目录

- 基本组件
  - 获取单个配置信息
  - 获取全部配置信息
  - 打印调试
  - 行为日志记录
  - 微信接口验证及报错获取
  - 解析 model 报错
- 文件上传
- 生成二维码
- IP地址转地区
- 快递查询
- 小票打印
  - 易联云
- Curl
- 中文转拼音
- 爬虫
- Glide

#### 基本组件

读取后台的配置

```
// 后台配置
Yii::$app->debris->backendConfig($fildName);

// 强制不从缓存读取配置
Yii::$app->debris->backendConfig($fildName, true);

// 强制不从缓存读取所有配置
Yii::$app->debris->backendConfigAll(true);
```

读取商户端的配置

```
// 商户端配置
Yii::$app->debris->merchantConfig($fildName);

// 强制不从缓存读取配置
Yii::$app->debris->merchantConfig($fildName, true);

// 强制不从缓存读取所有配置
Yii::$app->debris->merchantConfigAll(true);
```

自动读取对应配置信息

> 不了解其机制的话请谨慎使用  
> 规则：如果有 merchant_id 的话，则直接读取后台配置，没有的话会去读取商户端配置

```
// 注意$fildName 为你的配置标识,默认从缓存读取
Yii::$app->debris->config($fildName);

// 强制不从缓存读取
Yii::$app->debris->config($fildName, true);

// 从缓存中强制读取商户 ID 为 1 配置(注意: 1 为总后台的 ID)
Yii::$app->debris->config($fildName, false);
```

##### 获取全部配置信息

```
// 注意默认从缓存读取
Yii::$app->debris->configAll();

// 强制不从缓存读取
Yii::$app->debris->configAll(true);

// 从缓存中强制读取商户 ID 为 1 全部配置(注意: 1 为总后台的 ID)
Yii::$app->debris->configAll(false, 1);
```

读取某一端的配置

```
// 商户端配置
Yii::$app->debris->merchantConfigAll();
```

##### 打印调试

```
Yii::$app->debris->p();
```

##### 行为日志记录

```
/**
 * 行为日志
 *
 * @param string $behavior 行为标识
 * @param string $remark 备注 注意长度为255
 * @param bool $noRecordData 是否记录 post 数据 [true||false]
 * @throws \yii\base\InvalidConfigException
 */
Yii::$app->services->actionLog->create($behavior, $remark, $noRecordData)
```

##### 微信接口验证及报错

```
// 默认直接报错
Yii::$app->debris->getWechatError($message);

// 如果想不直接报错并返回报错信息
$error = Yii::$app->debris->getWechatError($message, false);
```

##### 解析 model 报错

```
// 注意 $firstErrors 为 $model->getFirstErrors();
Yii::$app->debris->analyErr($firstErrors);
```

#### 文件上传

获取组件全部实现 `League\Flysystem\Filesystem` 接口

```
// 支持 oss/cos/qiniu/local, 配置不传默认使用总后台
$entity = Yii::$app->uploadDrive->local($config)->entity()；
```

使用案例

```
$entity = Yii::$app->uploadDrive->local()->entity()；
$stream = fopen('文件绝对路径', 'r+');
$result = $entity->writeStream('存储相对路径', $stream);

// 直接写入base64数据
$entity->write('存储相对路径', $base64Data);
```

更多说明：新增驱动请放入 `common\components\uploaddrive` 目录, 并在 `common\components\UploadDrive` 类内实现可实例化的方法

#### 生成二维码

```
$qr = Yii::$app->get('qr');
Yii::$app->response->format = Response::FORMAT_RAW;
Yii::$app->response->headers->add('Content-Type', $qr->getContentType());

return $qr->setText('www.rageframe.com')
    ->setLabel('2amigos consulting group llc')
    ->setSize(150)
    ->setMargin(7)
    ->writeString();
```
or

```
use Da\QrCode\QrCode;

$qrCode = (new QrCode('This is my text'))
    ->setSize(250)
    ->setMargin(5)
    ->useForegroundColor(51, 153, 255);

// 把图片保存到文件中:
$qrCode->writeFile(Yii::getAlias('@attachment') . '/code.png'); // 没有指定的时候默认为png格式

// 直接显示在浏览器 
header('Content-Type: '.$qrCode->getContentType());
echo $qrCode->writeString();
```

[二维码文档](http://qrcode-library.readthedocs.io/en/latest/)

#### IP地址转地区

```
use Zhuzhichao\IpLocationZh\Ip;

var_dump(Ip::find('171.12.10.156'));
```

输出结果

```
array (size=4)
  0 => string '中国' (length=6)
  1 => string '河南' (length=6)
  2 => string '郑州' (length=6)
  3 => string '' (length=0)
  4 => string '410100' (length=6)
```

#### 快递查询

```
// 查询所有的可用快递公司
$companies = Yii::$app->logistics->companies('aliyun');

/**
 * 支持 aliyun(阿里云)、juhe(聚合)、kdniao(快递鸟)、kd100(快递100)
 *
 * @param string $no 快递单号
 * @param null $company 快递公司
 * @param bool $isCache 是否缓存读取默认缓存1小时
 * @return OrderInterface
 */
$order = Yii::$app->logistics->aliyun($no, $company, $isCache);
```

#### 小票打印

##### 易联云


用法1

```
$orderSn = rand(1, 9);

$content = "<FS2><center>**#1 美团**</center></FS2>";
$content .= str_repeat('.', 32);
$content .= "<FS2><center>--在线支付--</center></FS2>";
$content .= "<FS><center>张周兄弟烧烤</center></FS>";
$content .= "订单时间:". date("Y-m-d H:i") . "\n";
$content .= "订单编号:40807050607030\n";
$content .= str_repeat('*', 14) . "商品" . str_repeat("*", 14);
$content .= "<table>";
$content .= "<tr><td>烤土豆(超级辣)</td><td>x3</td><td>5.96</td></tr>";
$content .= "<tr><td>烤豆干(超级辣)</td><td>x2</td><td>3.88</td></tr>";
$content .= "<tr><td>烤鸡翅(超级辣)</td><td>x3</td><td>17.96</td></tr>";
$content .= "<tr><td>烤排骨(香辣)</td><td>x3</td><td>12.44</td></tr>";
$content .= "<tr><td>烤韭菜(超级辣)</td><td>x3</td><td>8.96</td></tr>";
$content .= "</table>";
$content .= str_repeat('.', 32);
$content .= "<QR>这是二维码内容</QR>";
$content .= "小计:￥82\n";
$content .= "折扣:￥４ \n";
$content .= str_repeat('*', 32);
$content .= "订单总价:￥78 \n";
$content .= "<FS2><center>**#1 完**</center></FS2>";

/**
 * 打印文字
 * 
 * @param string $data 打印内容 具体看文档
 * @param string $orderSn 订单号 不超过 32位
 */
Yii::$app->services->printerYiLianYun->text($content, $orderSn);
```

用法2

```
$orderSn = rand(1, 9);
$data = [
    'title' => '美团', // 商城名称
    'merchantTitle' => '张周兄弟烧烤', // 门店名称
    'orderTime' => time(), // 下单时间
    'orderSn' => $orderSn, // 下单编号
    'orderMoney' => 100, // 订单总价
    'orderMarketingMoney' => 80, // 折扣金额
    'payMoney' => 20, // 小计金额
    'products' => [ // 产品列表
        [
            'title' => '烤土豆(超级辣)', // 商品名称
            'num' => 2, // 商品数量
            'price' => 1.88, // 商品金额
        ],
        [
            'title' => '烤鸡翅',
            'num' => 5,
            'price' => 9.88,
        ],
    ],
    'qr' => '', // 二维码内容
];

Yii::$app->services->printerYiLianYun->text($data, $orderSn);
```

更多文档：http://doc2.10ss.net/331992

更多操作

```
 $order->getCode(); // 状态码
 $order->getMsg(); // 状态信息
 $order->getCompany(); // 物流公司简称
 $order->getNo(); // 物流单号
 $order->getStatus(); // 当前物流单状态
 
 注：物流状态可能不一定准确
 
 $order->getDisplayStatus(); // 当前物流单状态展示名
 $order->getAbstractStatus(); // 当前抽象物流单状态
 $order->getCourier(); // 快递员姓名
 $order->getCourierPhone(); // 快递员手机号
 $order->getList(); // 物流单状态详情
 $order->getOriginal(); // 获取接口原始返回信息
```

#### Curl

```
use linslin\yii2\curl;
$curl = new curl\Curl();

//get http://example.com/
$response = $curl->get('http://example.com/');

if ($curl->errorCode === null) {
   echo $response;
} else {
     // List of curl error codes here https://curl.haxx.se/libcurl/c/libcurl-errors.html
    switch ($curl->errorCode) {
    
        case 6:
            //host unknown example
            break;
    }
} 
```

文档地址：https://github.com/linslin/Yii2-Curl

#### 中文转拼音

```
use Overtrue\Pinyin\Pinyin;

// 小内存型
$pinyin = new Pinyin(); // 默认
// 内存型
// $pinyin = new Pinyin('Overtrue\Pinyin\MemoryFileDictLoader');
// I/O型
// $pinyin = new Pinyin('Overtrue\Pinyin\GeneratorFileDictLoader');

$pinyin->convert('带着希望去旅行，比到达终点更美好');
// ["dai", "zhe", "xi", "wang", "qu", "lyu", "xing", "bi", "dao", "da", "zhong", "dian", "geng", "mei", "hao"]

$pinyin->convert('带着希望去旅行，比到达终点更美好', PINYIN_TONE);
// ["dài","zhe","xī","wàng","qù","lǚ","xíng","bǐ","dào","dá","zhōng","diǎn","gèng","měi","hǎo"]

$pinyin->convert('带着希望去旅行，比到达终点更美好', PINYIN_ASCII_TONE);
//["dai4","zhe","xi1","wang4","qu4","lyu3","xing2","bi3","dao4","da2","zhong1","dian3","geng4","mei3","hao3"]
```

更多文档：https://github.com/overtrue/pinyin

#### 爬虫

```
use QL\QueryList;

$data = QueryList::get('https://www.baidu.com/s?wd=QueryList')
  // 设置采集规则
  ->rules([ 
      'title'=>array('h3','text'),
      'link'=>array('h3>a','href')
  ])
  ->queryData();

print_r($data);
```

更多文档：http://www.querylist.cc/docs/guide/v4/

#### Glide

> Glide是一个用PHP编写的非常简单的按需图像处理库  
> 注意：系统默认只能在storage下使用，已经基础配置完毕，但是系统内未安装，如果需要使用请先安装

```
php composer.phar require --prefer-dist trntv/yii2-glide
```

**用法：**

直接输出一个图像

```
Yii::$app->glide->outputImage('new-upload.jpg', ['w' => 100, 'fit' => 'crop'])
```

创建一个图像

```
Yii::$app->glide->makeImage('new-upload.jpg', ['w' => 100, 'fit' => 'crop'])
```

创建一个带签名才能访问的图像

```
Yii::$app->glide->createSignedUrl(['glide/index', 'path' => 'images/2018-12/27/image_154588883551485657.jpg', 'w' => 175]);
```

> 注意开启设置 `storage/config/main.php` 内的 glide 组件的signKey，否则无效

来源：https://github.com/trntv/yii2-glide  
配套文档：http://glide.thephpleague.com/

### 系统JS

目录

- 弹出框
- 内页打开新标签页面
- 关闭当前标签页
- Ajax 更新数据
- Js 模板引擎 Demo
- 用 Iframe 进行表单提交
- Bootstrap-table 支持

#### 弹出框

```
/**
* 错误提示
* 
* @param string title 标题
* @param string text 副标题
*/
rfError(title,text);

// 警告提示
rfWarning(title,text)

// 普通提示
rfAffirm(title,text)

// 信息提示
rfInfo(title,text)

// 成功提示
rfSuccess(title,text)

// 消息提示，layer风格
rfMsg(title)
```

删除二次确认

```
<a href="<?= Url::to(['delete','id' => $model['id']])?>"  onclick="rfDelete(this);return false;">
    <span class="btn btn-warning btn-sm">删除</span>
</a>
```

#### 内页打开新标签页面

达到此效果只需要给此元素一个class为openContab，指定新窗口的链接地址为href   
> 注：并不仅限于a元素，任意元素只要给class为openContab， href 属性皆可打开新tab

```
 <a class="openContab" href="<?= Url::to(['test/index'])?>">测试标签</a>
```

#### 关闭当前标签页

达到此效果只需要给此元素一个 class 为 closeCurrentConTab

```
<span class="closeCurrentConTab">关闭当前标签页</span>
```

#### Ajax 更新数据

> 注意tr上面的id为model主键

```
<tr id = "<?= $model['id']?>">
    <td>
         <?= \common\helpers\Html::sort($model['sort']); ?>
    </td>
    <td>
        <?= \common\helpers\Html::status($model['status']); ?>
    </td>
</tr>
```

#### Js 模板引擎 Demo

页面模板

```
<script type="text/html" id="listModel">
    {{each data as value i}}
    <tr id = "{{value.id}}">
        <td>
            <h4>{{value.title}}</h4>
        </td>
    </tr>
    {{/each}}
</script>
```

获取数据并渲染

```
$.ajax({
    type:"get",
    url:"",
    dataType: "json",
    success: function(data){
        if (data.code == 200) {
            var html = template('listModel', data);
            // 渲染添加数据
            $('#listAddons').append(html);
        } else {
            rfAffirm(data.message);
        }
    }
});
```

相关文档：https://github.com/aui/art-template/wiki/syntax:simple

#### 用 Iframe 进行表单提交

```
// class带上 openIframe 即可，提交表单默认id为w0，具体案例看 功能案例->Curd Grid
<?= Html::create(['edit'], '创建', [
        'class' => 'btn btn-primary btn-xs openIframe',
]); ?>
```

#### Bootstrap-table 支持

GridView 可以自行加入这几行代码

```
'tableOptions' => [
    'class' => 'table table-hover rf-table',
    'fixedNumber' => 3, // 固定前几列
    'fixedRightNumber' => 1, // 固定最后几列
],
```

普通的 table 视图

```
<table class="table table-hover rf-table" fixedNumber="3" fixedRightNumber="1"></table>
```

### Gii

目录

- Model
- CURD

使用条件

- 开发模式
- rageframe 版本号 >= 2.3.94
- backend/gii

CURD 使用条件

- 表自增长字段为 `id`
- 表必须带有 `status` 字段
- 控制器必须继承  `backend\controllers\BaseController`

#### Model

- 表字段已经备注
- Code Template 选择 rageframe

那么你选择一个表进行生成 Model 你会发现 attributeLabels 全部替换成为字段备注，不用再重新修改

```
<?php

namespace app\models;

use Yii;

/**
 * This is the model class for table "{{%addon_article_adv}}".
 *
 * @property int $id 序号
 * @property string $merchant_id 商户id
 * @property string $title 标题
 * @property string $cover 图片
 * @property int $location_id 广告位ID
 * @property string $silder_text 图片描述
 * @property int $start_time 开始时间
 * @property int $end_time 结束时间
 * @property string $jump_link 跳转链接
 * @property int $jump_type 跳转方式[1:新标签; 2:当前页]
 * @property int $sort 优先级
 * @property int $status 状态
 * @property string $created_at 创建时间
 * @property string $updated_at 更新时间
 */
class AddonArticleAdv extends \yii\db\ActiveRecord
{
    /**
     * {@inheritdoc}
     */
    public static function tableName()
    {
        return '{{%addon_article_adv}}';
    }

    /**
     * {@inheritdoc}
     */
    public function rules()
    {
        return [
            [['merchant_id', 'location_id', 'start_time', 'end_time', 'jump_type', 'sort', 'status', 'created_at', 'updated_at'], 'integer'],
            [['title'], 'string', 'max' => 30],
            [['cover'], 'string', 'max' => 100],
            [['silder_text', 'jump_link'], 'string', 'max' => 150],
        ];
    }

    /**
     * {@inheritdoc}
     */
    public function attributeLabels()
    {
        return [
            'id' => '序号',
            'merchant_id' => '商户id',
            'title' => '标题',
            'cover' => '图片',
            'location_id' => '广告位ID',
            'silder_text' => '图片描述',
            'start_time' => '开始时间',
            'end_time' => '结束时间',
            'jump_link' => '跳转链接',
            'jump_type' => '跳转方式[1:新标签; 2:当前页]',
            'sort' => '优先级',
            'status' => '状态',
            'created_at' => '创建时间',
            'updated_at' => '更新时间',
        ];
    }
}
```

#### CURD

1、设置好模型路径(注意是已经创建好的Model)，选择生成控制器命名空间和视图文件路径，选择模板为 rageframe 即可进行生成预览

![]({{site.baseurl}}/images/RageFrame2/gii-curd.jpg)

2、 勾选你首页需要显示的字段和编辑页面需要显示的字段和控件类型即可直接生成一个定制化的 CURD

![]({{site.baseurl}}/images/RageFrame2/gii-curd-view.jpg)

3、选择后点击 `Preview` 预览，没有问题了可直接点击 `Generate` 创建进行生成

![]({{site.baseurl}}/images/RageFrame2/gii-curd-view-files.jpg)

4、访问对应的路径进行查看，这样子关于页面显示、查询、编辑就完成了，就那么简单

### 权限控制

- 控制说明
- 如何页面块/按钮自由控制显示权限
- 不进行权限认证的路由
- 新增应用的 RBAC 授权

> RageFrame已经内置的 RBAC 权限管理，并对其进行了二次开发，无限父子级权限分组、可自由分配子级权限

#### 控制说明

可以把权限理解为一个大的库，往里面丢各种名称，可以是路由、自定义名称等

系统自定义了几个默认的名称

- cate:1 => 平台管理导航菜单
- cate:2 => 微信公众号导航菜单
- cate:3 => 系统管理导航菜单
- cate:4 => 应用中心导航菜单

> 为什么要命名为cate:1这种名称呢，因为是后面带的是菜单分类数据库自带的id，如果自己有改动比如删除后添加注意修改权限名称  

> 注意！！！  
> 注意！！！  
> 注意！！！  
>
> 权限的路由一定要包含菜单路由才会显示

**举个例子**

已授权权限

```
    [
        '/test/index',
        '/test/system',
        
        ...
    ],
```

你的菜单

```
    [
        'testList', // 这里是菜单的顶级别名
        'child' => [
            '/test/index', // 子菜单
            '/test/system', // 子菜单
            '/test/test', // 子菜单
            ...
         ]
         
         ...
    ],
```

这里看到你授权菜单的子菜单但是没有授权 testList 别名，那么你整个菜单都会被隐藏，如果想显示你必须把 testList 也加入权限

#### 如何页面块/按钮自由控制显示权限

按钮快捷方式：使用 [Html](#Html) 辅助类  
自定义验证如下：使用 [Auth](#Auth) 辅助类

#### 不进行权限认证的路由

> 修改地址 `backend/config/params` 找到 `noAuthRoute`

```
/**
 * 不需要验证的路由全称
 *
 * 注意: 前面以绝对路径/为开头
 */
'noAuthRoute' => [
    '/main/index',// 系统主页
    '/main/system',// 系统首页
    '/menu-provinces/index',// 微信个性化菜单省市区
    '/wechat/common/select-news',// 微信自动回复获取图文
    '/wechat/common/select-attachment',// 微信自动回复获取图片/视频/
    '/wechat/analysis/image',// 微信显示素材图片
],
```
    
#### 新增应用的 RBAC 授权

获取分配角色列表

```
/**
 * @param string $app_id 应用id
 * @param bool $sourceAuthChild 权限来源(false:所有权限，true：当前角色)
 * @return array
 */
Yii::$app->services->rbacAuthRole->getDropDown($app_id, $sourceAuthChild)
```

分配为用户角色

```
/**
 * @param array $role_ids 角色id
 * @param int $user_id 用户id
 * @param string $app_id 应用id
 * @throws UnprocessableEntityHttpException
 */
Yii::$app->services->rbacAuthAssignment->assign($role_ids, $user_id, $app_id);
```

权限管理

> 下面为新增应用权限(比如增加前台会员的权限)案例

```
<?php

namespace addons\Merchants\backend\controllers;

use common\enums\AppEnum;
use common\models\rbac\AuthItem;
use common\traits\AuthItemTrait;

/**
 * Class AuthItemController
 * @package backend\modules\common\controllers
 * @author jianyan74 <751393839@qq.com>
 */
class AuthItemController extends BaseController
{
    use AuthItemTrait;

    /**
     * @var AuthItem
     */
    public $modelClass = AuthItem::class;

    /**
     * 默认应用
     *
     * @var string
     */
    public $appId = AppEnum::MERCHANT;

    /**
     * 渲染视图前缀(默认)
     *
     * @var string
     */
    public $viewPrefix = '@backend/modules/base/views/auth-item/';
}
```

角色管理

> 下面为新增应用权限(比如增加前台会员的角色)案例

```
<?php

namespace addons\Merchants\backend\controllers;

use Yii;
use common\traits\AuthRoleTrait;
use common\models\rbac\AuthRole;
use common\enums\AppEnum;

/**
 * Class RoleController
 * @package addons\Merchants\backend\controllers
 * @author jianyan74 <751393839@qq.com>
 */
class AuthRoleController extends BaseController
{
    use AuthRoleTrait;

    /**
     * @var AuthRole
     */
    public $modelClass = AuthRole::class;

    /**
     * 默认应用
     *
     * @var string
     */
    public $appId = AppEnum::MERCHANT;

    /**
     * 权限来源
     *
     * false:所有权限，true：当前角色
     *
     * @var bool
     */
    public $sourceAuthChild = false;

    /**
     * 渲染视图前缀(默认)
     *
     * @var string
     */
    public $viewPrefix = '@backend/modules/base/views/auth-role/';

    /**
     * @throws \yii\base\InvalidConfigException
     */
    public function init()
    {
        parent::init();

        $this->merchant_id = Yii::$app->request->get('merchant_id');
        $this->merchant_id && Yii::$app->services->merchant->setId($this->merchant_id);
    }
}
```

### 公用支付

目录

- 支付宝
- 微信
- 银联

#### 支付宝

订单

```
// 配置
$config = [
    'notify_url' => 'http://rageframe.com/notify.php', // 支付通知回调地址
    'return_url' => 'http://rageframe.com/return.php', // 买家付款成功跳转地址
    'sandbox' => false, // 沙盒模式
];

// 生成订单
$order = [
    'out_trade_no' => date('YmdHis') . mt_rand(1000, 9999),
    'total_amount' => 0.01,
    'subject'      => 'test',
];
```

生成参数

```
// 电脑网站支付
$resConfig = Yii::$app->pay->alipay($config)->pc($order);

// app支付
$resConfig = Yii::$app->pay->alipay($config)->app($order);

// 面对面支付(创建收款二维码)
$resConfig = Yii::$app->pay->alipay($config)->f2f($order);

// 手机网站支付
$resConfig = Yii::$app->pay->alipay($config)->wap($order);
```

扫码收款

```
$request = Yii::$app->pay->alipay->capture();
$request->setBizContent([
    'out_trade_no' => date('YmdHis') . mt_rand(1000, 9999),
    'scene'        => 'bar_code',
    'auth_code'    => '288412621343841260',  //购买者手机上的付款二维码
    'subject'      => 'test',
    'total_amount' => 0.01,
]);

/** @var \Omnipay\Alipay\Responses\AopCompletePurchaseResponse $response */
try {
    $response = $request->send();
    
    if($response->isPaid()){
        /**
         * Payment is successful
         */
    }else{
        /**
         * Payment is not successful
         */
    }
} catch (Exception $e) {
    /**
     * Payment is not successful
     */
}
```


退款

```
$info = [
      'out_trade_no' => 'The existing Order ID',
      'trade_no' => 'The Transaction ID received in the previous request',
      'refund_amount' => 18.4,
      'out_request_no' => date('YmdHis') . mt_rand(1000, 9999)
];
 
   
Yii::$app->pay->alipay->refund($info); 
```

异步/同步通知

```
$request = Yii::$app->pay->alipay([
    'ali_public_key' => '', // 支付宝公钥
])->notify()

try {
    /** @var \Omnipay\Alipay\Responses\AopCompletePurchaseResponse $response */
    $response = $request->send();
    
    if($response->isPaid()){
        /**
         * Payment is successful
         */
        die('success'); //The response should be 'success' only
    } else {
        /**
         * Payment is not successful
         */
        die('fail');
    }
} catch (Exception $e) {
    /**
     * Payment is not successful
     */
    die('fail');
}
```

更多文档：https://github.com/lokielse/omnipay-alipay


单笔转账

```
$info = [
     'out_biz_no' => '转账单号',
     'payee_type' => '收款人账号类型', // ALIPAY_USERID:支付宝唯一号;ALIPAY_LOGONID:支付宝登录号
     'payee_account' => '收款人账号',
     'amount' => '收款金额',
     'payee_real_name' => '收款方真实姓名', // 非必填
     'remark' => '账业务的标题，用于在支付宝用户的账单里显示', // 非必填
]
```

转账案例

```
$res = Yii::$app->pay->alipay([
    'notify_url' => Url::toFront(['transfer/alipay'])
])->transfer([
    'out_biz_no' => time() . StringHelper::random(10),
    'payee_account' => 13484261295,
    'amount' => 1.00
]);
```

转账查询

```
$result = Yii::$app->pay->alipay->transferQuery([
    'out_biz_no' => '转账单号',
    'order_id' => '回调单号',
]);
```

单笔转账文档：https://opendocs.alipay.com/apis/api_28/alipay.fund.trans.toaccount.transfer

#### 微信

订单

```
// 生成订单
$order = [
    'body' => 'test', // 内容
    'out_trade_no' => date('YmdHis') . mt_rand(1000, 9999), // 订单号
    'total_fee' => 1,
    'notify_url' => 'http://rageframe.com/notify.php', // 回调地址
    // 'open_id' => 'okFAZ0-',  //JS支付必填
    // 'auth_code' => 'ojPztwJ5bRWRt_Ipg',  刷卡支付必填
];
```

生成参数

```
// 原生扫码支付
$resConfig = Yii::$app->pay->wechat->native($order);

// app支付
$resConfig = Yii::$app->pay->wechat->app($order);

// js支付
$resConfig = Yii::$app->pay->wechat->js($order);

// 刷卡支付
$resConfig = Yii::$app->pay->wechat->pos($order);

// H5支付
$resConfig = Yii::$app->pay->wechat->mweb($order);
```

回调

```
$response = Yii::$app->pay->wechat->notify();

if ($response->isPaid()) {
    //pay success
    var_dump($response->getRequestData());
}else{
    //pay fail
}

```

关闭订单

```
$response = Yii::$app->pay->wechat->close($out_trade_no);
```

查询订单

```
$response = Yii::$app->pay->wechat->query($transaction_id);
```

退款

```
$info = [
    'transaction_id' => $transaction_id, //The wechat trade no
    'out_refund_no'  => $outRefundNo,
    'total_fee'      => 1, //=0.01
    'refund_fee'     => 1, //=0.01
];

$response = Yii::$app->pay->wechat->refund($info);
```

#### 银联

订单

```
// 配置
$config = [
    'notify_url' => 'http://rageframe.com/notify.php', // 支付通知回调地址
    'return_url' => 'http://rageframe.com/return.php', // 买家付款成功跳转地址
];

// 生成订单
$order = [
    'orderId'   => date('YmdHis'), //Your order ID
    'txnTime'   => date('YmdHis'), //Should be format 'YmdHis'
    'orderDesc' => 'My order title', //Order Title
    'txnAmt'    => '100', //Order Total Fee
];
```

生成参数

```
// app支付
$resConfig = Yii::$app->pay->union($config)->app($order);

// pc/wap
$resConfig = Yii::$app->pay->union($config)->html($order);
```

回调

```
$response = Yii::$app->pay->union->notify();

if ($response->isPaid()) {
    //pay success
} else {
    //pay fail
}
```

查询订单

```
$response  = Yii::$app->pay->union->query($orderId, $txnTime, $txnAmt);

// 获取 $queryId
$queryId = $response['queryId'];
```

关闭订单

```
$response  = Yii::$app->pay->union->query($orderId, $txnTime, $txnAmt, $queryId);
```

退款

```
$response  = Yii::$app->pay->union->refund($orderId, $txnTime, $txnAmt, $queryId);
```

### 消息队列

目录 

- DEMO 类
- 处理队列
- 作业状态
- 控制台
- 进程监视器 Supervisor
- 定时任务监控

> 注意需要在Linux环境下运行
  该扩展为官方扩展，系统默认的队列驱动为redis，请自行安装redis，更多驱动请查看yii2-queue官方说明
  
#### DEMO 类

每个被发送到队列的任务应该被定义为一个单独的类。
例如，如果您需要下载并保存一个文件，该类可能看起来如下:

```
class DownloadJob extends BaseObject implements \yii\queue\JobInterface
{
    public $url;
    public $file;
    
    public function execute($queue)
    {
        file_put_contents($this->file, file_get_contents($this->url));
    }
}
```

下面是将任务添加到队列:

```
Yii::$app->queue->push(new DownloadJob([
    'url' => 'http://example.com/image.jpg',
    'file' => '/tmp/image.jpg',
]));
```

将作业推送到队列中延时5分钟运行:

```
Yii::$app->queue->delay(5 * 60)->push(new DownloadJob([
    'url' => 'http://example.com/image.jpg',
    'file' => '/tmp/image.jpg',
]));
```

> 注意: 只有一部分驱动支持延时运行。

#### 处理队列

> 具体执行任务的方式取决于所使用的驱动程序。大多数驱动都可以使用控制台命令，组件在您的应用程序中注册。有关更多细节，请参见相关驱动文档。
  
#### 作业状态

该组件具有跟踪被推入队列的作业状态的能力。

```
// 将作业推送到队列并获得其ID
$id = Yii::$app->queue->push(new SomeJob());

// 这个作业等待执行。
Yii::$app->queue->isWaiting($id);

// Worker 从队列获取作业，并执行它。
Yii::$app->queue->isReserved($id);

// Worker 作业执行完成。
Yii::$app->queue->isDone($id);
```

> 注意: RabbitMQ 驱动不支持作业状态。

#### Debug

```
$config['modules']['debug'] = [
    'class' => 'yii\debug\Module',
    'panels' => [
        'queue' => \yii\queue\debug\Panel::class,
    ],
];
```

#### 控制台

控制台用于监听和处理队列任务。

```
yii queue/listen [wait]
```

listen 命令启动一个守护进程，它可以无限查询队列。如果有新的任务，他们立即得到并执行。
wait 是下一次查询队列的时间 当命令正确地通过 supervisor 来实现时，这种方法是最有效的。

```
yii queue/run
```

run 命令获取并执行循环中的任务，直到队列为空。适用与cron。

run 与 listen 命令的参数:

- --verbose, -v: 将执行状态输出到控制台。

- --isolate: 详细模式执行作业。如果启用，将打印每个作业的执行结果。

- --color: 高亮显示输出结果。
    
```
yii queue/info
```

#### 进程监视器 Supervisor

Supervisor 是Linux的进程监视器。
它会自动启动您的控制台进程。
安装在Ubuntu上，你需要运行命令:

```
sudo apt-get install supervisor
```

Supervisor 配置文件通常可用 `/etc/supervisor/conf.d`。
你可以创建任意数量的配置文件。

配置示例:

```
[program:yii-queue-worker]
process_name=%(program_name)s_%(process_num)02d
command=/usr/bin/php /var/www/my_project/yii queue/listen --verbose=1 --color=0
autostart=true
autorestart=true
user=www-data
numprocs=4
redirect_stderr=true
stdout_logfile=/var/www/my_project/log/yii-queue-worker.log
```

在这种情况下，Supervisor 会启动4个 queue/listen worker。输出将写入相应日志文件。

有关 Supervisor 配置和使用的更多信息，请参阅文档。

以守护进程模式启动的Worker使用 queue/listen 命令支持 [File]、 [Db]、 [Redis]、 [RabbitMQ]、 [Beanstalk]、 [Gearman] 驱动。 有关其他参数，请参阅驱动程序指南。

#### 定时任务监控 Cron

可以用 cron 开始 worker。需要使用 queue/run 命令。只要队列包含作业，它就能进行执行。

配置示例:

```
* * * * * /path-to-your-project/yii queue/run
```

在这种情况下，cron将每分钟启动一次命令。

queue/run 命令支持 [File]、[Db]、[Redis]、[Beanstalk]、[Gearman]驱动。有关其他选项，请参阅驱动程序指南。

### 服务层

- 配置
- 调用

#### 配置

如果需要新服务层调用方法 请在 `services\Application` 中的 `childService` 添加，例如：

```
/**
 * @var array
 */
public $childService = [
    'example' => [
        'class' => 'services\example\ExampleService',
        // 子服务
        'childService' => [
            'rule' => [
                'class' => 'services\example\rule\RuleService',
            ],
        ],
    ],
];
```

以上例子代表了添加了一个 example 服务及 example 的子服务 rule

#### 调用

```
// 调用 example 服务里面的 index() 方法
$service = Yii::$app->services->example->index();

// 调用 example 的子服务 rule 里面的 index() 方法
$childService = Yii::$app->services->example->rule->index();
```

> 实际上系统内调用基本上没有用到子服务，自己可以按需使用

扩展说明：[跳转地址](http://www.fancyecommerce.com/2016/07/27/yii2-给yii-添加一个变量，并像组件component那样可以添加单例/)

### WebSocket

#### 使用
 
```
# 启动 
php ./yii websocket/start
# 停止 
php ./yii websocket/stop
# 重启 
php ./yii websocket/restart
```
   
#### 测试

```
<script>
    var wsl = 'ws://[to/your/url]:9501'; // 如果是wss的改成wss://[to/your/url]:9501
    ws = new WebSocket(wsl);// 新建立一个连接
    // 如下指定事件处理
    ws.onopen = function () {
        var login_data = '{"route":"site.test","content":"123"}';
        ws.send(login_data);
    };
    // 接收消息
    ws.onmessage = function (evt) {
        console.log(evt.data);
        /*ws.close();*/
        say("哈哈哈");
    };
    // 关闭
    ws.onclose = function (evt) {
        console.log('WebSocketClosed!');
    };
    // 报错
    ws.onerror = function (evt) {
        console.log('WebSocketError!');
    };

    var num = 1;
    // 聊天
    function say(msg){
        if(msg){
            var data = '{"route":"site.test","content":"'+msg + num +'"}';
            ws.send(data);
            num++;
        }
    }
</script>
```

### 公用服务


### RESTful Api

目录

- 继承的基类说明
- 速率和参数配置
  - 不需要速率控制设置
- 不进行格式化返回
- 签名验证
- Url权限配置
- 方法权限验证
- 返回数据格式修改
- 自定义code返回
- 解析Model首个报错信息
- 获取当前登录的用户信息

> ！！！  
> ！！！  
> ！！！  
> 注意：添加好控制器后必须配置rule才能正常访问，具体查看`api/config/main.php` 的路由规则示例

#### 继承的基类说明

- 控制器请全部继承 `api\controllers\OnAuthController`,注意Curd是改过的，不想用系统的Curd可直接继承 `api\controllers\ActiveController`，如果设置控制器内方法不需要验证请设置 `optional` 属性
- 用户私有控制器请全部继承 `api\controllers\UserAuthController`

#### 速率和参数配置

> 可自行修改 `common\models\common\RateLimit` 配置

##### 不需要速率控制设置

找到 `common\models\api\AccessToken` 让其直接继承 `common\models\base\BaseModel` 即可

#### 不进行格式化返回

> 只需要在返回之前加上这行代码就可以不进行格式化返回

```
Yii::$app->params['triggerBeforeSend'] = true
```

#### 签名验证

> 可自行修改 `api/config/params.php` 配置

测试控制器：`api\controllers\SignSecretKeyController`，注意要先开启路由规则匹配才能访问

```
return [
    // 签名验证默认不关闭验证，如果开启需了解签名生成及验证
    'user.httpSignValidity' => false,
    // 签名授权公钥秘钥
    'user.httpSignAccount' => [
        'doormen' => 'e3de3825cfbf',
    ],
];
```

#### Url权限配置

> 系统默认都是严格校验url方式注意在 `api/config/main.php的urlManager`里添加规则，否则访问都是404。
> 如果不想严格校验请在 urlManager 里面注释或删除 `'enableStrictParsing' => true,`

#### 方法权限验证

> 在执行访问方法前系统会先调用 `checkAccess` 方法来检测该方法能不能被验证通过，可以在控制器内添加该方法来判断权限，如果不需要可忽略。下面是个例子，不想让外部访问 delete 和 index 方法

```
/**
 * 权限验证
 *
 * @param string $action 当前的方法
 * @param null $model 当前的模型类
 * @param array $params $_GET变量
 * @throws \yii\web\BadRequestHttpException
 */
public function checkAccess($action, $model = null, $params = [])
{
    // 方法名称
    if (in_array($action, ['delete', 'index'])) {
        throw new \yii\web\BadRequestHttpException('权限不足');
    }
}
```

#### 返回数据格式修改

> 请自行修改 `api\behaviors\BeforeSend` 行为  
> 注意: 有些前端没有接触过状态码在Http头里面返回所以可以 在 BeforeSend 数据处理后开启 `$response->statusCode = 200`;

#### 自定义code返回

```
/**
 * 返回json数据格式
 *
 * 注意：要符合http状态码 否则报错
 * 
 * @param int $code 状态码
 * @param string $message 返回的报错信息
 * @param array|object $data 返回的数据结构
 */
common\helpers\ResultHelper::json($code, $message, $data = []);
```

#### 解析Model首个报错信息

```
/**
 * 注意这里传递的变量一定是 `$model->getFirstErrors()` 的数据
 *
 * @param $fistErrors
 * @return string
 */
$this->analyErr($model->getFirstErrors())
```

#### 获取当前登录的用户信息

```
use common\models\member\Member;

$tokenModel = Yii::$app->user->identity;
$member = Yii::$app->services->member->get($tokenModel['member_id']);
```

### 数据字典

#### rf_api_access_token
##### api_授权秘钥表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
refresh_token | varchar(60) | 是 | | 刷新令牌
access_token | varchar(60) | 是 | | 授权令牌
member_id | int(10) unsigned | 是 | 0 | 用户id
openid | varchar(50) | 是 | | 授权对象openid
group | varchar(100) | 是 | | 组别
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_backend_member
##### 系统_后台管理员表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
username | varchar(20) | 否 | | 帐号
password_hash | varchar(150) | 否 | | 密码
auth_key | varchar(32) | 否 | | 授权令牌
password_reset_token | varchar(150) | 是 | | 密码重置令牌
type | tinyint(1) | 是 | 1 | 1:普通管理员;10超级管理员
realname | varchar(10) | 是 | | 真实姓名
head_portrait | char(150) | 是 | | 头像
gender | tinyint(1) unsigned | 是 | 0 | 性别[0:未知;1:男;2:女]
qq | varchar(20) | 是 | | qq
email | varchar(60) | 是 | | 邮箱
birthday | date | 是 | | 生日
province_id | int(10) | 是 | 0 | 省
city_id | int(10) | 是 | 0 | 城市
area_id | int(10) | 是 | 0 | 地区
address | varchar(100) | 是 | | 默认地址
mobile | varchar(20) | 是 | | 手机号码
home_phone | varchar(20) | 是 | | 家庭号码
dingtalk_robot_token | varchar(100) | 是 | | 钉钉机器人token
visit_count | smallint(5) unsigned | 是 | 0 | 访问次数
last_time | int(10) | 是 | 0 | 最后一次登录时间
last_ip | varchar(16) | 是 | | 最后一次登录ip
role | smallint(6) | 是 | 10 | 权限
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_backend_notify
##### 系统_消息公告表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | bigint(20) | 否 | | 主键
title | varchar(150) | 是 | | 标题
content | text | 是 | | 消息内容
type | tinyint(1) | 是 | 0 | 消息类型[1:公告;2:提醒;3:信息(私信)
target_id | int(10) | 是 | 0 | 目标id
target_type | varchar(100) | 是 | | 目标类型
target_display | int(10) | 是 | 1 | 接受者是否删除
action | varchar(100) | 是 | | 动作
view | int(10) | 是 | 0 | 浏览量
sender_id | int(10) | 是 | 0 | 发送者id
sender_display | tinyint(1) | 是 | 1 | 发送者是否删除
sender_withdraw | tinyint(1) | 是 | 1 | 是否撤回 0是撤回
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) | 否 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_backend_notify_member
##### 系统_消息查看时间记录表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 无
member_id | int(10) unsigned | 否 | 0 | 管理员id
notify_id | int(10) | 是 | 0 | 消息id
is_read | tinyint(2) | 是 | 0 | 是否已读 1已读
type | tinyint(1) | 是 | 0 | 消息类型[1:公告;2:提醒;3:信息(私信)
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) | 否 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_backend_notify_pull_time
##### 系统_消息拉取表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
member_id | int(10) | 否 | | 管理员id
type | tinyint(4) | 是 | 0 | 消息类型[1:公告;2:提醒;3:信息(私信)
alert_type | varchar(20) | 是 | 0 | 提醒消息类型[sys:系统;wechat:微信]
last_time | int(10) | 是 | | 最后拉取时间

#### rf_backend_notify_subscription_config
##### 系统_消息配置表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
member_id | int(10) unsigned | 否 | | 用户id
action | json | 是 | | 订阅事件

#### rf_common_action_behavior
##### 系统_行为表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
app_id | varchar(50) | 是 | | 应用id
url | varchar(200) | 是 | | 提交url
method | varchar(20) | 是 | | 提交类型 *为不限
behavior | varchar(50) | 是 | | 行为类别
action | tinyint(4) unsigned | 是 | 1 | 前置/后置
level | varchar(20) | 是 | | 级别
is_record_post | tinyint(4) unsigned | 是 | 1 | 是否记录post[0;否;1是]
is_ajax | tinyint(4) unsigned | 是 | 2 | 是否ajax请求[1;否;2是;3不限]
remark | varchar(100) | 是 | | 备注
addons_name | varchar(100) | 否 | | 插件名称
is_addon | tinyint(1) unsigned | 是 | 0 | 是否插件
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_action_log
##### 系统_行为表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
app_id | varchar(50) | 是 | | 应用id
user_id | int(10) | 否 | 0 | 用户id
behavior | varchar(50) | 是 | | 行为类别
method | varchar(20) | 是 | | 提交类型
module | varchar(50) | 是 | | 模块
controller | varchar(50) | 是 | | 控制器
action | varchar(50) | 是 | | 控制器方法
url | varchar(200) | 是 | | 提交url
get_data | json | 是 | | get数据
post_data | json | 是 | | post数据
header_data | json | 是 | | header数据
ip | varchar(16) | 是 | | ip地址
addons_name | varchar(100) | 否 | | 插件名称
remark | varchar(1000) | 是 | | 日志备注
country | varchar(50) | 是 | | 国家
provinces | varchar(50) | 是 | | 省
city | varchar(50) | 是 | | 城市
device | varchar(200) | 是 | | 设备信息
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) | 否 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_addons
##### 公用_插件表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 主键
title | varchar(20) | 否 | | 中文名
name | varchar(100) | 否 | | 插件名或标识
title_initial | varchar(1) | 否 | | 首字母拼音
bootstrap | varchar(200) | 是 | | 启用文件
service | varchar(200) | 是 | | 服务调用类
cover | varchar(200) | 是 | | 封面
group | varchar(20) | 是 | | 组别
brief_introduction | varchar(140) | 是 | | 简单介绍
description | varchar(1000) | 是 | | 插件描述
author | varchar(40) | 是 | | 作者
version | varchar(20) | 是 | | 版本号
wechat_message | json | 是 | | 接收微信回复类别
is_setting | tinyint(1) | 是 | -1 | 设置
is_rule | tinyint(4) | 是 | 0 | 是否要嵌入规则
is_merchant_route_map | tinyint(1) | 是 | 0 | 商户路由映射
default_config | json | 是 | | 默认配置
console | json | 是 | | 控制台
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_addons_binding
##### 公用_插件菜单表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 主键
addons_name | varchar(100) | 否 | | 插件名称
app_id | varchar(20) | 否 | | 应用id
entry | varchar(10) | 否 | | 入口类别[menu,cover]
title | varchar(50) | 否 | | 名称
route | varchar(200) | 否 | | 路由
icon | varchar(50) | 是 | | 图标
params | json | 是 | | 参数

#### rf_common_addons_config
##### 公用_插件配置值表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 主键
addons_name | varchar(100) | 否 | | 插件名或标识
merchant_id | int(10) unsigned | 是 | 0 | 商户id
data | json | 是 | | 配置

#### rf_common_attachment
##### 公用_文件管理

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
drive | varchar(50) | 是 | | 驱动
upload_type | varchar(10) | 是 | | 上传类型
specific_type | varchar(100) | 否 | | 类别
base_url | varchar(1000) | 是 | | url
path | varchar(1000) | 是 | | 本地路径
md5 | varchar(100) | 是 | | md5校验码
name | varchar(1000) | 是 | | 文件原始名
extension | varchar(50) | 是 | | 扩展名
size | int(11) | 是 | 0 | 长度
year | int(10) unsigned | 是 | 0 | 年份
month | int(10) | 是 | 0 | 月份
day | int(10) unsigned | 是 | 0 | 日
width | int(10) unsigned | 是 | 0 | 宽度
height | int(10) unsigned | 是 | 0 | 高度
upload_ip | varchar(16) | 是 | | 上传者ip
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_auth_assignment
##### 公用_会员授权角色表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
role_id | int(11) | 否 | | 无
user_id | int(11) | 否 | | 无
app_id | varchar(20) | 是 | | 类型

#### rf_common_auth_item
##### 公用_权限表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
title | varchar(200) | 是 | | 标题
name | varchar(64) | 否 | | 别名
app_id | varchar(20) | 否 | | 应用
addons_name | varchar(100) | 否 | | 插件名称
pid | int(10) | 是 | 0 | 父级id
level | int(5) | 是 | 1 | 级别
is_menu | tinyint(1) unsigned | 是 | 0 | 是否菜单
is_addon | tinyint(1) unsigned | 是 | 0 | 是否插件
sort | int(10) | 是 | 9999 | 排序
tree | varchar(500) | 是 | | 树
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(11) | 是 | 0 | 无
updated_at | int(11) | 是 | 0 | 无

#### rf_common_auth_item_child
##### 公用_授权角色权限表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
role_id | int(11) unsigned | 否 | 0 | 角色id
item_id | int(10) unsigned | 否 | 0 | 权限id
name | varchar(64) | 否 | | 别名
app_id | varchar(20) | 否 | | 类别
addons_name | varchar(100) | 否 | | 插件名称
is_menu | tinyint(1) unsigned | 是 | 0 | 是否菜单
is_addon | tinyint(1) unsigned | 是 | 0 | 是否插件

#### rf_common_auth_role
##### 公用_角色表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
title | varchar(50) | 否 | | 标题
app_id | varchar(20) | 否 | | 应用
pid | int(10) unsigned | 是 | 0 | 上级id
level | tinyint(1) unsigned | 是 | 1 | 级别
sort | int(5) | 是 | 0 | 排序
tree | varchar(300) | 否 | | 树
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 添加时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_config
##### 公用_配置表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 主键
title | varchar(50) | 否 | | 配置标题
name | varchar(50) | 否 | | 配置标识
app_id | varchar(20) | 否 | | 应用
type | varchar(30) | 否 | | 配置类型
cate_id | int(10) unsigned | 否 | 0 | 配置分类
extra | varchar(1000) | 否 | | 配置值
remark | varchar(1000) | 否 | | 配置说明
is_hide_remark | tinyint(4) | 是 | 1 | 是否隐藏说明
default_value | varchar(500) | 是 | | 默认配置
sort | int(10) unsigned | 是 | 0 | 排序
status | tinyint(4) | 否 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 否 | 0 | 创建时间
updated_at | int(10) unsigned | 否 | 0 | 修改时间

#### rf_common_config_cate
##### 公用_配置分类表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
title | varchar(50) | 否 | | 标题
pid | int(10) unsigned | 是 | 0 | 上级id
app_id | varchar(20) | 否 | | 应用
level | tinyint(1) unsigned | 是 | 1 | 级别
sort | int(5) | 是 | 0 | 排序
tree | varchar(300) | 否 | | 树
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 添加时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_config_value
##### 公用_配置值表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 主键
config_id | int(10) | 否 | 0 | 配置id
merchant_id | int(10) unsigned | 是 | 0 | 商户id
data | text | 是 | | 配置内

#### rf_common_ip_blacklist
##### 公用_ip黑名单

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 无
remark | varchar(200) | 是 | | 备注
ip | varchar(20) | 否 | | ip地址
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | | 创建时间
updated_at | int(10) unsigned | 是 | | 修改时间

#### rf_common_log
##### 公用_日志

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
app_id | varchar(50) | 是 | | 应用id
merchant_id | int(10) unsigned | 是 | 0 | 商户id
user_id | int(11) | 是 | 0 | 用户id
method | varchar(20) | 是 | | 提交类型
module | varchar(50) | 是 | | 模块
controller | varchar(100) | 是 | | 控制器
action | varchar(50) | 是 | | 方法
url | varchar(1000) | 是 | | 提交url
get_data | json | 是 | | get数据
post_data | json | 是 | | post数据
header_data | json | 是 | | header数据
ip | varchar(16) | 是 | | ip地址
error_code | int(10) | 是 | 0 | 报错code
error_msg | varchar(1000) | 是 | | 报错信息
error_data | json | 是 | | 报错日志
req_id | varchar(50) | 是 | | 对外id
user_agent | varchar(200) | 是 | | UA信息
device | varchar(30) | 是 | | 设备信息
device_uuid | varchar(50) | 是 | | 设备唯一号
device_version | varchar(20) | 是 | | 设备版本
device_app_version | varchar(20) | 是 | | 设备app版本
status | tinyint(4) | 否 | 1 | 状态(-1:已删除,0:禁用,1:正常)
created_at | int(10) | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_menu
##### 系统_菜单导航表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
title | varchar(50) | 否 | | 标题
app_id | varchar(20) | 否 | | 应用
addons_name | varchar(100) | 否 | | 插件名称
is_addon | tinyint(1) unsigned | 是 | 0 | 是否插件
cate_id | tinyint(5) unsigned | 是 | 0 | 分类id
pid | int(50) unsigned | 是 | 0 | 上级id
url | varchar(50) | 是 | | 路由
icon | varchar(50) | 是 | | 样式
level | tinyint(1) unsigned | 是 | 1 | 级别
dev | tinyint(4) unsigned | 是 | 0 | 开发者[0:都可见;开发模式可见]
sort | int(5) | 是 | 999 | 排序
params | json | 是 | | 参数
tree | varchar(300) | 否 | | 树
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 添加时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_menu_cate
##### 系统_菜单分类表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
title | varchar(50) | 否 | | 标题
app_id | varchar(20) | 否 | | 应用
addons_name | varchar(100) | 否 | | 插件名称
icon | varchar(20) | 是 | | icon
is_default_show | tinyint(2) unsigned | 是 | 0 | 默认显示
is_addon | tinyint(1) unsigned | 是 | 0 | 是否插件
addon_centre | tinyint(1) | 是 | 0 | 应用中心
sort | int(10) | 是 | 999 | 排序
level | tinyint(1) unsigned | 是 | 1 | 级别
tree | varchar(300) | 否 | | 树
pid | int(10) unsigned | 是 | 0 | 上级id
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 添加时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_common_pay_log
##### 公用_支付日志

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(10) unsigned | 是 | 0 | 用户id
app_id | varchar(50) | 是 | | 应用id
addons_name | varchar(100) | 是 | | 插件名称
order_sn | varchar(20) | 是 | | 关联订单号
order_group | varchar(20) | 是 | | 组别[默认统一支付类型]
openid | varchar(50) | 是 | | openid
mch_id | varchar(20) | 是 | | 商户支付账户
body | varchar(100) | 是 | | 支付内容
detail | varchar(100) | 是 | | 支付详情
auth_code | varchar(50) | 是 | | 刷卡码
out_trade_no | varchar(32) | 是 | | 商户订单号
transaction_id | varchar(50) | 是 | | 微信订单号
total_fee | decimal(10,2) | 是 | 0.00 | 微信充值金额
fee_type | varchar(10) | 是 | | 标价币种
pay_type | tinyint(3) | 否 | 0 | 支付类型[1:微信;2:支付宝;3:银联]
pay_fee | decimal(10,2) | 否 | 0.00 | 支付金额
pay_status | tinyint(2) | 是 | 0 | 支付状态
pay_time | int(10) | 是 | 0 | 支付时间
trade_type | varchar(16) | 是 | | 交易类型
refund_sn | varchar(100) | 是 | | 退款编号
refund_fee | decimal(10,2) | 否 | 0.00 | 退款金额
is_refund | tinyint(4) | 是 | 0 | 退款情况[0:未退款;1已退款]
create_ip | varchar(30) | 是 | | 创建者ip
pay_ip | varchar(30) | 是 | | 支付者ip
notify_url | varchar(100) | 是 | | 支付通知回调地址
return_url | varchar(100) | 是 | | 买家付款成功跳转地址
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) | 是 | 0 | 创建时间
updated_at | int(10) | 是 | 0 | 修改时间

#### rf_common_provinces
##### 公用_省市区记录表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | ID
title | varchar(50) | 否 | | 栏目名
pid | int(10) | 否 | 0 | 父栏目
short_title | varchar(50) | 是 | | 缩写
areacode | int(6) | 是 | 0 | 区域编码
zipcode | int(10) | 是 | 0 | 邮政编码
pinyin | varchar(100) | 是 | | 拼音
lng | varchar(20) | 是 | | 经度
lat | varchar(20) | 是 | | 纬度
level | tinyint(4) | 否 | 1 | 级别
tree | varchar(200) | 否 | | 无
sort | tinyint(3) unsigned | 是 | 0 | 排序

#### rf_common_sms_log
##### 公用_短信发送日志

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(11) unsigned | 是 | 0 | 用户id
mobile | varchar(20) | 是 | | 手机号码
code | varchar(6) | 是 | | 验证码
content | varchar(500) | 是 | | 内容
error_code | int(10) | 是 | 0 | 报错code
error_msg | varchar(200) | 是 | | 报错信息
error_data | longtext | 是 | | 报错日志
usage | varchar(20) | 是 | | 用途
used | tinyint(1) | 是 | 0 | 是否使用[0:未使用;1:已使用]
use_time | int(10) | 是 | 0 | 使用时间
ip | varchar(30) | 是 | | ip地址
status | tinyint(4) | 否 | 1 | 状态(-1:已删除,0:禁用,1:正常)
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member
##### 用户_会员表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
username | varchar(20) | 是 | | 帐号
password_hash | varchar(150) | 是 | | 密码
auth_key | varchar(32) | 是 | | 授权令牌
password_reset_token | varchar(150) | 是 | | 密码重置令牌
type | tinyint(1) | 是 | 1 | 类别[1:普通会员;10管理员]
nickname | varchar(50) | 是 | | 昵称
realname | varchar(50) | 是 | | 真实姓名
head_portrait | varchar(150) | 是 | | 头像
current_level | tinyint(4) | 是 | 1 | 当前级别
gender | tinyint(1) unsigned | 是 | 0 | 性别[0:未知;1:男;2:女]
qq | varchar(20) | 是 | | qq
email | varchar(60) | 是 | | 邮箱
birthday | date | 是 | | 生日
visit_count | int(10) unsigned | 是 | 1 | 访问次数
home_phone | varchar(20) | 是 | | 家庭号码
mobile | varchar(20) | 是 | | 手机号码
role | smallint(6) | 是 | 10 | 权限
last_time | int(10) | 是 | 0 | 最后一次登录时间
last_ip | varchar(16) | 是 | | 最后一次登录ip
province_id | int(10) | 是 | 0 | 省
city_id | int(10) | 是 | 0 | 城市
area_id | int(10) | 是 | 0 | 地区
pid | int(10) unsigned | 是 | 0 | 上级id
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_account
##### 会员_账户统计表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(10) unsigned | 是 | 0 | 用户id
level | int(11) | 是 | -1 | 会员等级
user_money | decimal(10,2) | 是 | 0.00 | 当前余额
accumulate_money | decimal(10,2) | 是 | 0.00 | 累计余额
give_money | decimal(10,2) | 是 | 0.00 | 累计赠送余额
consume_money | decimal(10,2) | 是 | 0.00 | 累计消费金额
frozen_money | decimal(10,2) | 是 | 0.00 | 冻结金额
user_integral | int(11) | 是 | 0 | 当前积分
accumulate_integral | int(11) | 是 | 0 | 累计积分
give_integral | int(11) | 是 | 0 | 累计赠送积分
consume_integral | decimal(10,2) | 是 | 0.00 | 累计消费积分
frozen_integral | int(11) | 是 | 0 | 冻结积分
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]

#### rf_member_address
##### 用户_收货地址表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(11) unsigned | 是 | 0 | 用户id
province_id | int(10) unsigned | 是 | 0 | 省id
city_id | int(10) unsigned | 是 | 0 | 市id
area_id | int(10) unsigned | 是 | 0 | 区id
address_name | varchar(200) | 是 | | 地址
address_details | varchar(200) | 是 | | 详细地址
is_default | tinyint(4) unsigned | 是 | 0 | 默认地址
zip_code | int(10) unsigned | 是 | 0 | 邮编
realname | varchar(100) | 是 | | 真实姓名
home_phone | varchar(20) | 是 | | 家庭号码
mobile | varchar(20) | 是 | | 手机号码
status | tinyint(4) | 否 | 1 | 状态(-1:已删除,0:禁用,1:正常)
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_auth
##### 用户_第三方登录

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 主键
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(10) unsigned | 是 | 0 | 用户id
unionid | varchar(64) | 是 | | 唯一ID
oauth_client | varchar(20) | 是 | | 授权组别
oauth_client_user_id | varchar(100) | 是 | | 授权id
gender | tinyint(1) unsigned | 是 | 0 | 性别[0:未知;1:男;2:女]
nickname | varchar(100) | 是 | | 昵称
head_portrait | varchar(150) | 是 | | 头像
birthday | date | 是 | | 生日
country | varchar(100) | 是 | | 国家
province | varchar(100) | 是 | | 省
city | varchar(100) | 是 | | 市
status | tinyint(4) | 是 | 1 | 状态(-1:已删除,0:禁用,1:正常)
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_balance_withdraw
##### 会员_余额提现记录表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
withdraw_no | varchar(100) | 是 | | 提现流水号
member_id | int(11) | 是 | | 会员id
bank_name | varchar(50) | 是 | | 提现银行名称
account_number | varchar(50) | 是 | | 提现银行账号
realname | varchar(30) | 是 | | 提现账户姓名
mobile | varchar(20) | 是 | | 手机
cash | decimal(10,2) | 是 | 0.00 | 提现金额
memo | varchar(200) | 是 | | 备注
withdraw_status | smallint(6) | 是 | 0 | 当前状态 0已申请(等待处理) 1已同意 -1 已拒绝
payment_date | int(11) | 是 | 0 | 到账日期
transfer_type | int(11) | 是 | 1 | 转账方式 1 线下转账 2线上转账
transfer_name | varchar(50) | 是 | | 转账银行名称
transfer_money | decimal(10,2) | 是 | 0.00 | 转账金额
transfer_status | int(11) | 是 | 0 | 转账状态 0未转账 1已转账 -1转账失败
transfer_remark | varchar(200) | 是 | | 转账备注
transfer_result | varchar(200) | 是 | | 转账结果
transfer_no | varchar(100) | 是 | | 转账流水号
transfer_account_no | varchar(100) | 是 | | 转账银行账号
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_bank_account
##### 会员提现账号

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
member_id | int(11) | 否 | | 会员id
branch_bank_name | varchar(50) | 是 | | 支行信息
realname | varchar(50) | 否 | | 真实姓名
account_number | varchar(50) | 否 | | 银行账号
bank_code | varchar(50) | 是 | | 银行编号
mobile | varchar(20) | 否 | | 手机号
is_default | int(11) | 否 | 0 | 是否默认账号
account_type | int(11) | 是 | 1 | 账户类型，1：银行卡，2：微信，3：支付宝
account_type_name | varchar(30) | 是 | 银行卡 | 账户类型名称：银行卡，微信，支付宝
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_credits_log
##### 会员_积分等变动表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(11) unsigned | 是 | 0 | 用户id
pay_type | tinyint(4) | 是 | 0 | 支付类型
credit_type | varchar(30) | 否 | | 变动类型[integral:积分;money:余额]
app_id | varchar(50) | 是 | | 应用id
credit_group | varchar(30) | 是 | | 变动的组别
addons_name | varchar(100) | 否 | | 插件名称
old_num | decimal(10,2) | 是 | 0.00 | 之前的数据
new_num | decimal(10,2) | 是 | 0.00 | 变动后的数据
num | decimal(10,2) | 是 | 0.00 | 变动的数据
remark | varchar(200) | 是 | | 备注
ip | varchar(30) | 是 | | ip地址
map_id | int(10) | 是 | 0 | 关联id
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_invoice
##### 会员_发票信息

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) unsigned | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
member_id | int(11) unsigned | 是 | 0 | 用户id
title | varchar(200) | 是 | | 公司抬头
duty_paragraph | varchar(200) | 是 | | 税号
is_default | tinyint(2) unsigned | 是 | 0 | 默认
type | tinyint(4) | 是 | 1 | 类型 1企业 2个人
status | tinyint(4) | 是 | 1 | 状态(-1:已删除,0:禁用,1:正常)
created_at | int(10) unsigned | 是 | | 创建时间
updated_at | int(10) unsigned | 是 | | 修改时间

#### rf_member_level
##### 会员等级表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 主键
merchant_id | int(11) unsigned | 是 | 0 | 商户id
level | int(11) | 是 | 0 | 等级（数字越大等级越高）
name | varchar(255) | 是 | | 等级名称
money | decimal(10,2) | 是 | 0.00 | 消费额度满足则升级
check_money | tinyint(1) unsigned | 是 | 0 | 选中消费额度
integral | int(11) | 是 | 0 | 消费积分满足则升级
check_integral | tinyint(1) unsigned | 是 | 0 | 选中消费积分
middle | tinyint(1) | 是 | 0 | 条件（0或 1且）
discount | decimal(10,1) | 是 | 10.0 | 折扣
status | int(11) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
detail | varchar(255) | 是 | | 会员介绍
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_member_recharge_config
##### 会员_充值配置

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 无
merchant_id | int(11) | 是 | 0 | 商户
price | decimal(10,2) unsigned | 是 | 0.00 | 充值金额
give_price | decimal(10,2) unsigned | 是 | 0.00 | 赠送金额
sort | int(5) | 是 | 0 | 排序
status | tinyint(4) | 是 | 1 | 状态
created_at | int(10) unsigned | 否 | 0 | 创建时间
updated_at | int(10) unsigned | 否 | 0 | 更新时间

#### rf_merchant
##### 商户

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 无
title | varchar(200) | 是 | | 商户名称
user_money | decimal(10,2) | 是 | 0.00 | 当前余额
accumulate_money | decimal(10,2) | 是 | 0.00 | 累计余额
give_money | decimal(10,2) | 是 | 0.00 | 累计赠送余额
consume_money | decimal(10,2) | 是 | 0.00 | 累计消费金额
frozen_money | decimal(10,2) | 是 | 0.00 | 冻结金额
term_of_validity_type | int(1) | 是 | 0 | 有效期类型 0固定时间 1不限
start_time | int(10) | 是 | 0 | 开始时间
end_time | int(10) | 是 | 0 | 结束时间
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_merchant_member
##### 系统_后台管理员表

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
username | varchar(20) | 否 | | 帐号
password_hash | varchar(150) | 否 | | 密码
auth_key | varchar(32) | 否 | | 授权令牌
password_reset_token | varchar(150) | 是 | | 密码重置令牌
type | tinyint(1) | 是 | 1 | 1:普通管理员;10超级管理员
realname | varchar(10) | 是 | | 真实姓名
head_portrait | char(150) | 是 | | 头像
gender | tinyint(1) unsigned | 是 | 0 | 性别[0:未知;1:男;2:女]
qq | varchar(20) | 是 | | qq
email | varchar(60) | 是 | | 邮箱
birthday | date | 是 | | 生日
province_id | int(10) | 是 | 0 | 省
city_id | int(10) | 是 | 0 | 城市
area_id | int(10) | 是 | 0 | 地区
address | varchar(100) | 是 | | 默认地址
mobile | varchar(20) | 是 | | 手机号码
home_phone | varchar(20) | 是 | | 家庭号码
dingtalk_robot_token | varchar(100) | 是 | | 钉钉机器人token
visit_count | smallint(5) unsigned | 是 | 0 | 访问次数
last_time | int(10) | 是 | 0 | 最后一次登录时间
last_ip | varchar(16) | 是 | | 最后一次登录ip
role | smallint(6) | 否 | 10 | 权限
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_oauth2_access_token
##### oauth2_授权令牌

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(10) unsigned | 否 | | 无
access_token | varchar(80) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
client_id | varchar(64) | 否 | | 无
member_id | varchar(100) | 是 | | 无
expires | timestamp | 否 | | 无
scope | json | 是 | | 无
grant_type | varchar(30) | 是 | | 组别
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_oauth2_authorization_code
##### oauth2_授权回调code

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
authorization_code | varchar(100) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
client_id | varchar(64) | 否 | | 无
member_id | varchar(100) | 是 | | 无
redirect_uri | varchar(2000) | 是 | | 无
expires | timestamp | 否 | | 无
scope | json | 是 | | 无
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_oauth2_client
##### oauth2_授权客户端

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
id | int(11) unsigned | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
title | varchar(100) | 否 | | 标题
client_id | varchar(64) | 否 | | 无
client_secret | varchar(64) | 否 | | 无
redirect_uri | varchar(2000) | 否 | | 回调Url
remark | varchar(200) | 是 | | 备注
group | varchar(30) | 是 | | 组别
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间

#### rf_oauth2_refresh_token
##### oauth2_授权令牌

字段 | 类型 | 允许为空 | 默认值 | 字段说明
---|---|---|---|---
refresh_token | varchar(80) | 否 | | 无
merchant_id | int(10) unsigned | 是 | 0 | 商户id
client_id | varchar(64) | 否 | | 无
member_id | varchar(100) | 是 | | 无
expires | timestamp | 否 | | 无
scope | json | 是 | | 无
grant_type | varchar(30) | 是 | | 组别
status | tinyint(4) | 是 | 1 | 状态[-1:删除;0:禁用;1启用]
created_at | int(10) unsigned | 是 | 0 | 创建时间
updated_at | int(10) unsigned | 是 | 0 | 修改时间


### 单元测试




<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

