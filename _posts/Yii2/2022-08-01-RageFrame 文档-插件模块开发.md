---
layout: post
categories: Yii2
title: RageFrame 文档-插件模块开发
meta: RageFrame 文档-插件模块开发
---
* content
{:toc}

## 插件模块开发

### 模块介绍及目录

目录

- 模块介绍
- 启动流程
- 商户映射
- 目录结构
- 其他说明

#### 模块介绍

> 定位: 实现独立的功能模块或者临时性使用的功能模块或者就是工具类型的功能，例如：小游戏(大转盘/消消乐/抽奖/大屏互动/红包等),小插件(广告管理/文章管理/友情链接等等),小模块(报名/投票/签到),小程序,大型插件微商城等等  

#### 启动流程

Yii 入口文件->启动文件(common\components\Init.php)->初始化已安装的插件->写入模块组->根据 Yii 正常的开发和访问流程去开发访问插件

#### 商户映射

开启了开启了商户映射，访问后台的路由会直接转到商户端去访问商户端的路由，由商户端的控制器方法来处理该路由。此功能方便有些插件商户端和后台端功能一致，又不想重复开发进行的一个便利功能

注意：

开启商户映射后，需要开启插件的启动项，并加入以下代码

```
Yii::$app->services->merchant->addId(0);
```

#### 目录结构

目录 | 说明
---|---
**Demo** | 模块名称
--- **backend** | 模块后台应用
--- --- resources | 资源(js/css/image)目录(不可修改命名)
--- --- controllers | 控制器目录
--- --- --- SettingController.php | 配置文件控制器, 有配置项的话可选
--- --- views | 视图目录
--- --- asset | 资源配置目录
--- --- ---  Asset.php | 后台静态资源载入器
--- --- --- setting | 配置视图目录
--- --- --- --- hook.php | 钩子视图文件
--- --- --- --- display.php | 配置视图文件
--- **frontend** | 模块前台应用
--- --- resources | 资源(js/css/image)目录(不可修改命名)
--- --- controllers | 控制器目录
--- --- views | 视图目录
--- --- asset | 资源配置目录
--- --- ---  Asset.php | 前台静态资源载入器
--- **html5** | 模块html5应用
--- --- resources | 资源(js/css/image)目录(不可修改命名)
--- --- controllers | 控制器目录
--- --- views | 视图目录
--- --- asset | 资源配置目录
--- --- ---  Asset.php | html5静态资源载入器
--- **merchant** | 模块html5应用
--- --- resources | 资源(js/css/image)目录(不可修改命名)
--- --- controllers | 控制器目录
--- --- views | 视图目录
--- --- asset | 资源配置目录
--- --- ---  Asset.php | merchant静态资源载入器
--- **api** | 模块api应用(主要用于小程序)
--- --- controllers | 控制器目录
--- **common** | 公用
--- --- models | 公共模型层
--- --- components | 组件
--- --- --- Bootstrap | 引导文件，插件启动前会访问该文件
--- --- config | 配置：例如权限、菜单、导航入口
--- **console** | 控制层
--- --- migrations | 数据迁移文件
--- AddonConfig.php | 模块配置文件(必须有)
--- AddonMessage.php | 模块微信消息接收处理文件(可选)
--- Install | 安装SQL文件(文件名可自定义详细看DebrisAddon.php)
--- UnInstall | 卸载SQL文件(同上)
--- Upgrade | 更新SQL文件(同上)

#### 其他说明

安装插件完毕后，会有几个按钮，在此进行说明

- 更新配置：会执行 AddonConfig 文件去获取配置信息菜单信息权限信息等等对配置进行更新
- 升级数据库：会执行 Upgrade 文件对数据库表字段进行升级
- 卸载：会清除所有的数据表和已安装的信息
- 安装：会执行安装数据库和写入插件配置

### 模块开发流程

目录

- 创建/维护
- 权限
- 数据迁移
- 继承的基类说明
- 开发
- 访问路径

> 注意：修改完毕配置后需要手动取插件列表更新配置或者卸载重新安装，不然配置是不会生效的

#### 创建/维护

进入后台 - 系统管理 - 应用管理 - 设计新插件

> 创建成功后会在 根目录的 addons 目录下生成插件文件

#### 权限

权限请在创建的模块下的 common/config 的各自应用文件内手动填写，安装后会自动注册进系统权限管理

例如：

```
    // ----------------------- 默认配置 ----------------------- //

    'config' => [
        // 菜单配置
        'menu' => [
            'location' => 'addons', // default:系统顶部菜单;addons:应用中心菜单
            'icon' => 'fa fa-puzzle-piece',
        ],
        // 子模块配置，代表注册插件的子模块进系统，方便模块化开发
        'modules' => [
            'v1' => [
                'class' => 'addons\TinyShop\api\modules\v1\Module',
            ],
            'v2' => [
                'class' => 'addons\TinyShop\api\modules\v2\Module',
            ],
        ],
    ],

    /**
     * 可授权权限
     *
     * @var array
     */
    'authItem' => [
        [
            'title' => '一级权限',
            'name' => 'test',
            'child' =>[
                'title' => '二级权限',
                'name' => 'test/*', // 支持通配符 *， 插件下所有以 test/ 为前缀的路由都可以通过
            ]
        ],
    ];
    
    // ----------------------- 快捷入口 ----------------------- //
    
    'cover' => [

    ],

    // ----------------------- 菜单配置 ----------------------- //

    'menu' => [
        [
            'title' => '一级菜单',
            'route' => 'curd/index',
            'icon' => '',
            'params' => [
                'test' => '1'
            ],
            'child' => [
                [
                    'title' => '二级菜单',
                    'route' => 'test/index',
                ],
            ]
        ]
    ],
```

查看设置权限：系统管理->用户权限->角色管理->创建/编辑

#### 数据迁移

可以用系统自带的开发工具插件生成对应的数据迁移文件，并遵循系统规范进行数据安装

#### 继承的基类说明

##### 后台

默认继承插件各自内自动生成的 BaseController，如果自己有特殊需求可以修改继承

默认 BaseController 渲染的视图会自动载入左侧菜单，如果不需要请在 BaseController 里加入以下代码

```
/**
 * @var string
 */
public $layout = "@backend/views/layouts/main";
```

##### api

> 注意：开发 api 的时候能使用 restful 的基类，但是不受路由规则管辖

- 控制器请全部继承 `api\controllers\OnAuthController`,注意Curd是改过的，不想用系统的Curd可直接继承 `api\controllers\ActiveController`，如果设置控制器内方法不需要验证请设置 `authOptional` 属性
- 用户私有控制器请全部继承 `api\controllers\UserAuthController`

##### merapi

> 注意：开发 merapi 的时候能使用 restful 的基类，但是不受路由规则管辖

- 控制器请全部继承 `merapi\controllers\OnAuthController`,注意Curd是改过的，不想用系统的Curd可直接继承 `merapi\controllers\ActiveController`，如果设置控制器内方法不需要验证请设置 `authOptional` 属性
- 用户私有控制器请全部继承 `merapi\controllers\UserAuthController`


#### 开发

完全可以根据Yii2正常的开发流程去开发对应的控制器、视图、插件内的应用

#### 访问路径

> 具体前缀入口看你是默认的系统配置还是独立域名

##### 前台插件访问路径

```
// 域名/插件名称/控制器/方法
域名/tiny-shop/index/index
```
对应路径：`addons/TinyShop/frontend/controllers/IndexController`

##### 后台插件访问路径

```
// 域名/backend/插件名称/控制器/方法
域名/backend/tiny-shop/index/index
```

对应路径：`addons/TinyShop/backend/controllers/IndexController`

##### api 插件访问路径

```
// 域名/api/插件名称/插件模块/控制器/方法
域名/api/tiny-shop/v1/index/index
```

对应路径：`addons/TinyShop/api/modules/v1/controllers/IndexController`

...

### 模块辅助说明

目录

- 获取模块信息
- 获取模块导航和菜单信息
- 辅助方法

#### 获取模块信息

```
Yii::$app->params['addon'];
```

#### 获取模块导航和菜单信息

```
Yii::$app->params['addonBinding'];
```

#### 辅助方法

[模块辅助类](https://ibaiyang.github.io/blog/yii2/2022/08/05/RageFrame-文档-助手类.html#addonhelper---插件辅助类)

### 钩子

> 注意：如果钩子方法存在但是无法调用，请检查配置内(Addon.php)是否把hook设置了false或者该模块已经被卸载。修改配置文件后需要重新安装才会生效。  

> 注意：钩子不适合挂太重的业务

目录

- 如何使用钩子?
- 运行过程
- 什么是钩子？

#### 如何使用钩子?

参数说明

```
/**
* 实例化钩子
*
* @param string $addonsName 模块名称 如果想自定义钩子方法可以用插件名称.方法，例如 RfExample.test
* @param array $params 传递参数
* @param bool $debug 开发者模式 是否开启报错 默认false
* @return bool
* @throws NotFoundHttpException
*/
<?= \common\helpers\Hook::to($addonsName, $params, $debug); ?>

// 实际案例
<?= \common\helpers\Hook::to('RfExample', []); ?>
```

实际例子

```
<?= \common\helpers\Hook::to('RfExample', ['test' => 1]); ?>
```

#### 运行过程

- 初始化了模块信息
- 初始化的路由信息
- 实例化控制器
- 执行钩子

上面的例子实例化

```
\addons\RfExample\backend\controllers\SettingController
```

执行钩子

```
/**
 * 钩子
 *
 * @param array $param
 * @return mixed|string
 */
public function actionHook($param = [])
{
    return $this->render('hook', [
        'param' => $param
    ]);
}
```

在钩子视图或者控制器打印一下`$param`

```
Array
(
    [test] => 1
)
```    

#### 什么是钩子？

> 引用onethink的钩子说明

讲到插件，不得不讲钩子。首先，我们之前说明了插件是一个扩展的功能实现。

既然是扩展的，那么就要很灵活、可复用，并不是像我们之前开发项目，一个功能实现了，就写死在代码里了。

项目其他地方要用了，怎么办，复制一份改个名，改的那个地方能调用实现。这样一次两次可以，次数多了就不行了。

因为后面每次开发的底层架构在不断变化。不断重复的功能版本造成人力的浪费。我们做成插件的目的就是为了方便大家扩展我们这个产品的功能。到时候形成规模，大家自由的搭建自己的站点就方便了。

那么如何让一个扩展的功能在多个地方可随意的使用呢。那就用到了我们的钩子。

为什么叫它钩子呢？因为它的作用就是如此和生活中的钩子类似。

打个比方，我们做的网站比作一个有多个功能的立式衣架。

这个衣架给什么人用就有不同的用途。

假如你专门用来挂大衣的，那就是大衣衣架。如果你专门挂袋子，那就是一个储物衣架。

当你不想要某个挂件、衣服时，取下来即可。并不会破坏原有的袋子或者衣服的功能。

你挂与不挂，钩子就在那里。

为什么能挂那么多东西呢？说明被挂的东西都符合一个标准：能挂的住。

换作你挂一个橡皮泥、或者棉花之类的。挂不了多久就会掉了。因为他们不符合要有部分封闭的可固定的这一个部分的标准。

还有挂一个太重的比如10个背包挂一个钩子上。要么架子毁了，要么钩子断了。总之就是挂不住。

因为任何一个钩子都有其承重上限。你加起来的超过了，肯定不行。

所以我们不能把插件当成万能的使，什么东西都整成插件，不管功能的大小。

任何系统都有瓶颈，你不能把个重量级的东西做成插件后挂上，说不定以后就会影响整个站点。就违背了插件的独立性原则。那些就不应该做成插件而是做成模型扩展或者应用扩展。








<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

