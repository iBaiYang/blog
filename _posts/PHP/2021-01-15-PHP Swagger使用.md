---
layout: post
categories: PHP
title: PHP Swagger使用
meta: PHP Swagger使用
---
* content
{:toc}

### 正文

Swagger是什么？

Swagger的使用目的是方便优美的呈现出接口API的各种定义, 生成API文档, 包括参数, 路径之类. 有时后端改了API的参数或者其他设置, 
前端直接看这个Swagger UI就可以, 方便项目管理和团队协作.

这东西怎么使用? 说白了就是安装Swagger套件, 然后API代码里写注释, 用Swagger后端程序跑API来提取注释, 生成一个json文件, 
再通过Swagger前端来美化,整理JSON数据.

#### Yii2使用

1. 在`composer.json`文件中，添加 `"zircote/swagger-php":"=3.0.2"`
2. 执行`composer require zircote/swagger-php:"=3.0.2"`
3. 写好接口文档组织
4. 写好访问控制器
5. 访问UI

**写好接口文档组织**

在项目根目录中新建`doc/backend`、 `doc/components`目录。

`doc`目录下配置文件，`base.php`文件内容：
```php
<?php
use OpenApi\Annotations as OA;

/**
 * @OA\OpenApi(
 *     @OA\Info(
 *         version="1.0.0",
 *         title="活动项目类后端接口",
 *         description="一些活动项目如拼团、花呗等。",
 *     ),
 *     @OA\Server(
 *         description="测试",
 *         url="https://abc-qa.test.com"
 *     ),
 *     @OA\Server(
 *         description="预发布",
 *         url="https://abc-pre.test.com"
 *     ),
 *     @OA\Server(
 *         description="生产",
 *         url="https://abc.test.com"
 *     ),
 *     @OA\Server(
 *         description="本地",
 *         url="http://abc.test.host"
 *     ),
 * )
 */
```

`doc/components`目录下，组件文件`address.php`、 `security.php`。

`address.php`文件内容：
```php
<?php
use OpenApi\Annotations\Schema;
use OpenApi\Annotations\Property;

/**
 * @Schema(
 *     schema="userAddressIn",
 *     required={ "user_name", "mobile", "city", "address", },
 *     @Property(property="user_name", type="string", description="用户名"),
 *     @Property(property="mobile", type="string", description="手机号"),
 *     @Property(property="province", type="string", description="省"),
 *     @Property(property="city", type="string", description="市"),
 *     @Property(property="county", type="string", description="县"),
 *     @Property(property="address", type="string", description="详细地址"),
 * )
 */
```

`security.php`文件内容：
```php
<?php
use OpenApi\Annotations as OA;

/**
 * @OA\SecurityScheme(
 *     type="http",
 *     scheme="bearer",
 *     in="header",
 *     name="Authorization",
 *     securityScheme="bearerAuth",
 *     bearerFormat="JWT"
 * )
 */
```

`doc/backend`目录下组织具体接口，如`doc/backend/Acts/ApiController.php`文件内容：
```php
<?php
use OpenApi\Annotations\JsonContent;
use OpenApi\Annotations\MediaType;
use OpenApi\Annotations\Get;
use OpenApi\Annotations\Post;
use OpenApi\Annotations\Property;
use OpenApi\Annotations\RequestBody;
use OpenApi\Annotations\Response;
use OpenApi\Annotations\Schema;
use OpenApi\Annotations\Items;

interface ApiController
{
    /**
     * @Get(
     *     deprecated=false,
     *     path="/Acts/api/user-info",
     *     tags={"活动对外接口"},
     *     summary="获取用户信息,需要带上token来请求哦",
     *     @Response(
     *         response="200",
     *         description="ok",
     *         links={},
     *         @JsonContent(
     *             type="object",
     *             @Property(property="code", type="integer",description="返回码"),
     *             @Property(property="msg", type="string",description="描述信息"),
     *             @Property(
     *                  property="data", type="object",description="返回数据",
     *                  @Property(property="name", type="integer", description="用户昵称"),
     *                  @Property(property="head", type="integer", description="用户头像"),
     *             ),
     *             example={
     *                       "code": 200,
     *                       "msg": "ok",
     *                       "data": {
     *                           "name": "测试",
     *                           "head": "http://abc.test.cn/openwx/vi_32/KUBySuNOZp8ur1iadyvZmVa0eDnvL8mxNMAefdsGGwj2LshHjJg/132"
     *                       }
     *             }
     *         )
     *     ),
     *     @Response(response="401", description="Unauthorized，token缺失或失效", links={}),
     *     @Response(response="403", description="Forbidden，当前token无权访问", links={}),
     *     @Response(response="500", description="Internal Error，服务器内部异常", links={}),
     * )
     */
    public function actionUserInfo();

}
```

增加安全验证可以在 `summary` 参数下添加一行：`security=双大括号 "bearerAuth": {} 双大括号`,

**写好访问控制器**
```php
<?php 
namespace backend\controllers;

use yii\web\Controller;
use Yii;
use yii\web\Response;
use function OpenApi\scan;

class SwaggerController extends Controller
{
    public function beforeAction($action)
    {
        // 只有线下环境支持
        if (!defined('ENV_CONFIG') or ('test' !== ENV_CONFIG and 'dev' !== ENV_CONFIG)) {
            exit('doc-not-allowed');
        }
        
        return parent::beforeAction($action);
    }

    /**
     * 生成json文件
     */
    public function actionJson()
    {
        $path = dirname(dirname(dirname(__FILE__)));
        $response = Yii::$app->response;
        $response->format = Response::FORMAT_JSON;
        // 异常处理
        set_error_handler(function ($error_no, $error_msg, $error_file, $error_line) use ($response) {
            $response->data = [
                'openapi' => '3.0.0',
                'info' => [
                    'title' => '解析出错',
                    'description' => $error_msg
                ]
            ];
            $response->send();
        });
  
        try {
            $response->data = json_decode(scan($path . '/doc')->toJson(), true);
        } catch (\Exception $e) {
            $response->data = [
                'openapi' => '3.0.0',
                'info' => [
                    'title' => '解析出错',
                    'description' => $e->getMessage()
                ]
            ];
        }
    }
    
    /**
     * 访问页面
     */
    public function actionIndex()
    {
        $content = '<!doctypehtml><html lang=en><meta charset=UTF-8><title>{Swagger}-项目对外接口 - Swagger文档</title><link href="https://cdn.staticfile.org/swagger-ui/3.32.5/swagger-ui.min.css" rel=stylesheet><link href="https://cdn.jsdelivr.net/npm/swagger-ui-dist@3.32.4/favicon-32x32.png" rel=icon sizes=32x32 type=image/png><style>html{box-sizing:border-box;overflow:-moz-scrollbars-vertical;overflow-y:scroll}*,:after,:before{box-sizing:inherit}body{margin:0;background:#fafafa}.topbar{position:fixed;width:100%;top:0;z-index:999;}.swagger-ui table tbody tr td{vertical-align: baseline!important;}</style><div id=swagger-ui></div><script charset=UTF-8 src="https://cdn.staticfile.org/swagger-ui/3.32.5/swagger-ui-bundle.min.js"></script><script charset=UTF-8 src="https://cdn.staticfile.org/swagger-ui/3.32.5/swagger-ui-standalone-preset.min.js"></script><script>window.onload = function() {
          const ui=SwaggerUIBundle({url:"/swagger/json",dom_id:"#swagger-ui",deepLinking:!0,presets:[SwaggerUIBundle.presets.apis,SwaggerUIStandalonePreset],plugins:[SwaggerUIBundle.plugins.DownloadUrl],layout:"StandaloneLayout"});window.ui=ui;
        }</script>';
        $response = Yii::$app->response;
        $response->format = "html";
        $response->data = $content;
    }
}
```

**访问UI**

方法1，用php内置服务器。

可以在项目目录下运行：
> php -S localhost:8080 -t public

```
-S  url地址
-t  访问目录
```

方法2，借用搭建好的项目服务器。

访问地址`test.host/swagger/index`：

![]({{site.baseurl}}/images/20210115/20210115145344.png)

#### 补充

```
/**
 * 注解在action上
 * @OA\Get(
 *     path="/sms/batch-send",
 *     description="批量发送",
 *     summary="批量发送",
 *     parameters={
 *      @OA\Parameter(name="userId",in="query",required=true,example="1",@OA\Schema(type="integer")),
 *     },
 *     @OA\Response(
 *      response="200",
 *      description="成功",
 *      content={
 *          @OA\MediaType(mediaType="application/json",
 *              @OA\Schema(
 *                  @OA\Property(property="code",type="integer",example="200"),
 *                  @OA\Property(property="message",type="string",example="成功"),
 *                  @OA\Property(property="data",properties={
 *                          @OA\Property(property="test",type="string",example="name"),
 *                  })
 *              )
 *          )
 *     }
 *     )
 * )
 */

/**
 * 注解在action上
 * @OA\Post(
 *     path="/sms/single-send",
 *     tags={"sms"},
 *     description="单条发送",
 *     summary="单条发送",
 *     parameters={
 *      @OA\Parameter(name="userId",in="query",required=true,example="1",@OA\Schema(type="integer")),
 *     },
 *     @OA\RequestBody(
 *       content={
 *         @OA\MediaType(mediaType="application/json",
 *           @OA\Schema(
 *              required={"username","password"},
 *              @OA\Property(property="username",type="string",example="lalal"),
 *              @OA\Property(property="password",type="string",example="test",enum={"test","none"}),
 *           )
 *         )
 *       }
 *     ),
 *     @OA\Response(
 *        response="200",
 *        description="An example resource",
 *        content={
 *          @OA\MediaType(mediaType="application/json",
 *              @OA\Schema(
 *                  @OA\Property(property="code",type="integer",example="200"),
 *                  @OA\Property(property="message",type="string",example="成功"),
 *                  @OA\Property(property="data",properties={
 *                          @OA\Property(property="test",type="string",example="name"),
 *                  })
 *              )
 *          )
 *        }
 *     )
 *)
 */
```

* 在项目根目录下，执行 vendor/zircote/swagger-php/bin/openapi doc/ -o swagger.json
* 执行docker run -p 你希望暴露的地址:8080 -e SWAGGER_JSON=/foo/swagger.json -v `pwd`:/foo swaggerapi/swagger-ui
* 在浏览器中打开http://本机ip:暴露的地址/，就能看到你的文档啦

<br/><br/><br/><br/><br/>
### 参考资料

官网 <http://swagger.io/>

swagger-ui <https://github.com/swagger-api/swagger-ui>

文档 <https://xiaoxiami.gitbook.io/swagger/>

Swagger PHP使用指南 <https://www.cnblogs.com/derrck/p/5234961.html>

Swagger 生成 PHP API 接口文档 <https://www.cnblogs.com/lalalagq/p/9964464.html>

Swagger介绍及使用 <https://www.jianshu.com/p/349e130e40d5>
