---
layout: post
categories: Yii2
title: RageFrame 文档-助手类
meta: RageFrame 文档-助手类
---
* content
{:toc}

## 助手类

### Auth - 权限辅助类

目录

- 校验权限是否拥有
- 批量校验权限是否拥有

> 未登录默认不校验权限，注意需要判断权限的地方先引入登录认证

引入

```
use common\helpers\Auth;
```

**校验权限是否拥有**

```
/**
 * 校验权限是否拥有
 *
 * @param string $route
 * @return bool
 */
Auth::verify($route);
```

**批量校验权限是否拥有**

```
/**
 * 过滤自己拥有的权限
 * 传递权限数组返回自己拥有的权限数组
 * 
 * @param array $route
 * @return array|bool
 */
Auth::verifyBatch($route);
```

### Url - Url生成辅助类

目录

- 生成前台 Url
- 生成微信 Url
- 生成Api Url
- 生成OAuth2 Url
- 生成Storage Url
- 生成不自带 merchant_id 的 Url

引入

```
use common\helpers\Url;
```

**生成前台 Url**

```
Url::toFront(['index']);
```

**生成微信 Url**

```
Url::toHtml5(['index']);
```

**生成Api Url**

```
Url::toApi(['index']);
```

**生成OAuth2 Url**

```
Url::toOAuth2(['index']);
```

**生成Storage Url**

```
Url::toStorage(['index']);
```

**生成不自带 merchant_id 的 Url**

> 下面例子生成为不带 merchant_id 的前台地址

```
Url::removeMerchantIdUrl('toFront', ['index']);
```


### Html - Html辅助类

目录

- 新增按钮
- 编辑按钮
- 删除按钮
- 普通按钮
- 状态标签
- 是否标签

> 关于链接按钮的用法和Yii2自带的Html辅助类一样，只不过系统在其上面封装了一层

引入

```
use common\helpers\Html;
```

**新增按钮**

```
Html::create(['index']);
```

**编辑按钮**

```
Html::edit(['edit', 'id' => 1]);
```

**删除按钮**

```
Html::create(['delete', 'id' => 1]);
```

**普通按钮**

```
Html::linkButton(['test', 'id' => 1], '测试');
```

**状态标签**

```
// 0：显示;1：隐藏
Html::status(1);
```

**是否标签**

```
// 1：是;0：否
Html::whether(1);
```
   
### ArrayHelper - 数组辅助类
### FileHelper - 文件辅助类
### DateHelper - 日期格式辅助类
### PayHelper - 支付辅助类
### ImageHelper - 图片辅助类

### StringHelper - 字符串辅助类

目录

- uuid 生成
- 日期和时间戳互转
- 获取缩略图地址
- 创建缩略图地址
- 分析枚举类型配置值
- 返回字符串在另一个字符串中第一次出现的位置
- XML 字符串转对象
- 字符串提取汉字
- 字母大小写转驼峰命名
- 驼峰命名法转下划线风格
- 获取字符串后面的字符串
- 获取随机字符串
- 获取随机数字

引入

```
use common\helpers\StringHelper;
```

**uuid 生成**

```
/**
 * 生成Uuid
 * 
 * @param string $type 类型 默认时间 time/md5/random/sha1/uniqid 其中uniqid不需要特别开启php函数
 * @param string $name 加密名
 * @return string
 */
StringHelper::uuid($type = 'time', $name = 'php.net');
```

**日期转时间戳**

```
/**
 * 日期转时间戳
 *
 * @param $value
 * @return false|int
 */
StringHelper::dateToInt($value)
```

**获取缩略图地址**

```
/**
 * 获取缩略图地址
 *
 * @param string $url
 * @param int $width
 * @param int $height
 */
StringHelper::getThumbUrl($url, $width, $height)
```

**创建缩略图地址**

```
/**
 * 创建缩略图地址
 *
 * @param string $url
 * @param int $width
 * @param int $height
 */
StringHelper::createThumbUrl($url, $width, $height)
```

**分析枚举类型配置值**

**返回字符串在另一个字符串中第一次出现的位置**

**XML 字符串转对象**

**字符串提取汉字**

**字母大小写转驼峰命名**

**驼峰命名法转下划线风格**

**获取字符串后面的字符串**

**获取随机字符串**

**获取随机数字**

### RegularHelper - 正则验证辅助类
### HashidsHelper - ID加密辅助类
### UploadHelper - 上传辅助类

### AddonHelper - 插件辅助类

目录

- 获取资源文件路径
- 获取/设置当前模块配置信息

#### 获取资源文件路径

> 注意 : 方法只能在视图里面使用，如果改动了资源，请清理对应应用的 assets 目录，因为初始化创建后不会再重新创建资源

```
use common\helpers\AddonHelper;
```

```
// 获取静态资源文件所在目录 该方法获取的内容为 addons\[模块]\resources\
$path = AddonHelper::filePath();

// 获取静态资源文件 该方法获取的内容为 addons\[模块]\resources\img\test.jpg 文件
AddonHelper::file('img/test.jpg');
```

#### 获取/设置当前模块配置信息

```
// 获取
AddonHelper::getConfig();

// 设置
AddonHelper::setConfig($config)
```

### ResultHelper - 格式化数据返回辅助类

目录

- Json
- Api
- 返回示例

引入

```
use common\helpers\ResultHelper;
```

#### Json

> 如果是 `api/oauth2/merapi` 那边调用注意 要符合 http 状态码

```
/**
* 返回json数据格式
*
* @param int $code 状态码
* @param string $message 返回的报错信息
* @param array|object $data 返回的数据结构
*/
ResultHelper::json($code, $message, $data)
```

#### 返回示例

```
{
    "code": 200,
    "message": "OK",
    "data": [
        
    ]
}
```

### ExcelHelper - Excel导入导出辅助类

目录

- 前置说明
- 导出
- 导入

#### 前置说明

引入

```
use common\helpers\ExcelHelper;
```

demo

```
// [名称, 字段名, 类型, 类型规则]
$header = [
    ['ID', 'id', 'text'],
    ['手机号码', 'mobile'], // 规则不填默认text
    ['openid', 'fans.openid', 'text'],
    ['昵称', 'fans.nickname', 'text'],
    ['关注/扫描', 'type', 'selectd', [1 => '关注', 2 => '扫描']],
    ['性别', 'sex', 'function', function($model){
        return $model['sex'] == 1 ? '男' : '女';
    }],
    ['创建时间', 'created_at', 'date', 'Y-m-d'],
];

$list = [
    [
        'id' => 1,
        'type' => 1,
        'fans' => [
            'openid' => '123',
            'nickname' => '昵称',
        ],
        'sex' => 1,
        'create_at' => time(),
    ]
];
```

#### 导出

```
// 简单使用
return ExcelHelper::exportData($list, $header);

// 定制 默认导出xlsx 支持 : xlsx/xls/html/csv
return ExcelHelper::exportData($list, $header, '测试', 'xlsx');
```

#### 导入

```
/**
 * 导入
 *
 * @param $filePath 文件路径
 * @param int $startRow 开始行数 默认 1
 * @return array|bool|mixed
 */
$data = ExcelHelper::import($filePath, $startRow);
```

### ...


 

<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

