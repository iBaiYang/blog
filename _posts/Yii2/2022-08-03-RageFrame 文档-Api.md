---
layout: post
categories: Yii2
title: RageFrame 文档-Api
meta: RageFrame 文档-Api
---
* content
{:toc}

## Api

### 接口说明

目录

- 测试域名
- 接口版本
- 北京时间格式
- 公共头部参数
- 公共出参说明
- 公用请求方法
- 公共状态码说明

> api 文档下的大部分接口商户 api 也通用

#### 测试域名

用户 api

```
http://www.example.com/api/接口版本/
```

商户 api

```
http://www.example.com/merapi/接口版本/
```

#### 接口版本

v1

#### 北京时间格式

YYYY-mm-dd HH:ii:ss

#### 公共入参说明

> 注意是通过Url传递
> 例如 `http://www.example.com/api/v1/member/info

Query 入参说明

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
access-token | string | 否 | 无 | 授权秘钥 | 需登录验证(出现401错误)必传,与下面的x-api-key 2选1即可
merchant_id | int | 否 | 无 | 商户id | 

Header 入参说明

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
x-api-key | string | 否 |  | 授权秘钥 | 与上面的access-token 2选1即可
merchant-id | string | 否 |  | 商户id | 
device-id | string | 否 |  | 设备ID |
device-name | string | 否 |  | 设备名称 | 
width | int | 否 |  | 屏幕宽度 |
height | int | 否 |  | 屏幕高度 |
os | string | 否 |  | 操作系统 |
os-version | string | 否 |  | 操作系统版本 |
is-root | int | 否 |  | 是否越狱 | 0:未越狱， 1:已越狱
network | string | 否 |  | 网络类型 |
wifi-ssid | string | 否 |  | wifi的编号 |
wifi-mac | string | 否 |  | wifi的mac |
xyz | string | 否 |  | 三轴加速度 |
version-name | string | 否 |  | APP版本名 |
api-version | string | 否 |  | API的版本号 |
channel | string | 否 |  | 渠道名 |
app-name | int | 否 |  | APP编号 | 1:android， 2:iphone
dpi | int | 否 |  | 屏幕密度 |
api-level | string | 否 |  | android的API的版本号 |
operator | string | 否 |  | 运营商 |
idfa | string | 否 |  | iphone的IDFA |
idfv | string | 否 |  | iphone的IDFV |
open-udid | string | 否 |  | iphone的OpenUdid |
wlan-ip | string | 否 |  | 局网ip地址 |
time | int | 否 |  | 客户端时间 |


#### 公共出参说明

出参说明

参数名 | 参数类型 | 说明 | 备注
---|---|---|---
code | int | 状态码 | 
message | string | 状态说明 | 
data | array | 接口数据 |

成功返回

```
{
    "code": 200,
    "message": "ok",
    "data": [
    
    ]
}
``` 

错误返回

```
{
    "code": 422,
    "message": "错误说明",
    "data": [
    
    ]
}
```

header出参说明

参数名 | 参数类型 | 说明 | 备注
---|---|---|---
X-Rate-Limit-Limit | int | 同一个时间段所允许的请求的最大数目 | 
X-Rate-Limit-Remaining | int | 在当前时间段内剩余的请求的数量 |
X-Rate-Limit-Reset | int | 为了得到最大请求数所等待的秒数 |
X-Pagination-Total-Count | int | 总数量 | 
X-Pagination-Page-Count | int | 总页数 | 
X-Pagination-Current-Page | int | 当前页数 |
X-Pagination-Per-Page | int | 每页数量 |

> 注意：如果自行修改了系统默认的首页查询，需要自行设置header头

#### 公用请求方法

针对不同操作，服务器向用户返回的结果应该符合以下规范。

方法 | 说明 | 对应控制器方法(路由)
---|---|---
GET /article | 获取文章列表 | /article/index
GET /article/1 | 获取文章详情(id为1) | /article/view?id=1
POST /article | 创建一篇文章 | /article/create
PUT /article/1 | 更新文章(id为1) | /article/update?id=1
DELETE /article/1 | 删除文章(id为1) | /article/delete?id=1

> 如果想自定义控制器内的方法(不包含：index/view/create/update/delete)，需要自行在`rule`里面配置`extraPatterns`

#### 公共状态码说明

* `200`: OK。一切正常。
* `201`: 响应 `POST` 请求时成功创建一个资源。`Location` header 包含的URL指向新创建的资源。
* `204`: 该请求被成功处理，响应不包含正文内容 (类似 `DELETE` 请求)。
* `304`: 资源没有被修改。可以使用缓存的版本。
* `400`: 错误的请求。可能通过用户方面的多种原因引起的，例如在请求体内有无效的JSON数据，无效的操作参数，等等。
* `401`: 验证失败。
* `403`: 已经经过身份验证的用户不允许访问指定的 API 末端。
* `404`: 所请求的资源不存在。
* `405`: 不被允许的方法。 请检查 `Allow` header 允许的HTTP方法。
* `415`: 不支持的媒体类型。 所请求的内容类型或版本号是无效的。
* `422`: 数据验证失败 (例如，响应一个 `POST` 请求)。 请检查响应体内详细的错误消息。
* `429`: 请求过多。 由于限速请求被拒绝。
* `500`: 内部服务器错误。 这可能是由于内部程序错误引起的。

### 接口加密

目录

- 启动加密
- 基本说明
- Get请求
- Post请求
- Put/Delete请求

> 注意默认是关闭加密，可自行开启

#### 启动加密

具体查看文件 `api/config/params.php`

```
// 签名验证默认关闭验证，如果开启需了解签名生成及验证
'user.httpSignValidity' => false,
// 签名授权公钥秘钥
'user.httpSignAccount' => [
    'doormen' => 'e3de3825cfbf',
],
```

#### 基本说明

Url测试访问地址:

```
// 注意系统默认关闭了该测试控制器 请去 api 的 main 文件内开启 sign-secret-key 路由
http://www.example.com/api/sign-secret-key
```

签名sign的生成规则：  
将需要参与签名的参数按照参数名字符串顺序升序排列，并用请求查询串的形式依次拼接。  
格式为：p1=v1&p2=v2&p3=v3  
将以上拼好的结果后面直接加上appSecret,形成待签名字符串  
对待签名字符串按照UTF-8编码做MD5摘要运算，结果转化为32位小写签名摘要。

固定参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
appId | string | 是 | 无 | 授权公钥 | 后台给予，文档测试值:doormen
nonceStr | string | 是 | 无 | 随机数 | 自行生成 默认8位，文档测试值:z7cl7WR9
time | int | 是 | 无 | 时间戳 | 自行生成 10位，注意和当前校验时间不能大于60秒，文档测试值:1539846942
sign | string | 是 | 无 | 签名 | 加密后出现

不用传参但是需要参与加密

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
appSecret | string | 是 | 无 | 授权秘钥 | 后台给与，文档测试值:e3de3825cfbf

#### Get 请求

1、例如以下的接口请求方式

请求地址(Get)

```
/v1/site/send
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
mobile | string| 是 | 无 | 账号 | 测试值为：15888888888

请求时候测试拼接字符串为：

Get请求所有的参数都是在url里面所以mobile加入计算

```
appId=doormen&mobile=15888888888&nonceStr=z7cl7WR9&time=1539846942e3de3825cfbf

// php版加密方式
$sign = strtolower(md5('上面的字符串'));

// 测试生成的sign：
94c897114201d7f9b4adf03b5e3afc8f

// 查看最后生成的Url：
appId=doormen&mobile=15888888888&nonceStr=z7cl7WR9&time=1539846942&sign=94c897114201d7f9b4adf03b5e3afc8f
```

> 注意：如果是请求详情例如url为 /v1/member/info/1,你的参与加密参数要额外带上一个 id为1的参数 比如

```
// 原因见 Put/Delete 请求说明 一个道理
appId=doormen&id=1&mobile=15888888888&nonceStr=z7cl7WR9&time=1539846942e3de3825cfbf
```

#### Post请求

1、例如以下的接口请求方式

请求地址(Post)

```
/v1/site/login
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
username | string| 是 | 无 | 账号 |
password | string| 是 | 无 | 密码 | 

2、上面的参数无需加密，只需要加密基本的参数

```
// 默认参数
appId=doormen&nonceStr=z7cl7WR9&time=1539846942e3de3825cfbf

// php版加密方式
$sign = strtolower(md5('上面的字符串'));

// 测试生成的sign：
94c897114201d7f9b4adf03b5e3afc8f

// 查看最后生成的Url：
/v1/site/login?appId=doormen&nonceStr=z7cl7WR9&time=1539846942&sign=94c897114201d7f9b4adf03b5e3afc8f
```

#### Put/Delete请求

1、例如以下的接口请求方式

请求地址(Put/Delete)

```
/v1/member/member/[ID]
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
nickname | string | 是 | 无 | 昵称 | 

2、上面的参数无需加密，只需要加密基本的参数

> 但是由于restful的特殊机制其实以上url省略了一个id参数，所以我们加密的时候要附带一个id参数

```
// 默认参数
appId=doormen&id=1&nonceStr=z7cl7WR9&time=1539846942e3de3825cfbf

// php版加密方式
$sign = strtolower(md5('上面的字符串'));

// 测试生成的sign：
94c897114201d7f9b4adf03b5e3afc8f

// 查看最后生成的Url：
/v1/member/member/1?appId=doormen&nonceStr=z7cl7WR9&time=1539846942&sign=94c897114201d7f9b4adf03b5e3afc8f
```

### 登录重置

group (组别) | 说明
---|---
pc | PC
ios | 苹果
android | 安卓
h5 | H5
wechat | 微信小程序
ali | 支付宝小程序
qq | QQ小程序
dingTalk | 钉钉小程序
touTiao | 头条小程序

> 注意：如果是商户 api 的需要安装商户插件，并创建用户信息进行登录

目录

- 登录
- 重置令牌

#### 登录

请求地址(Post)

```
/v1/site/login
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
username | string| 是 | 无 | 账号 |
password | string| 是 | 无 | 密码 | 
`group` | string| 是 | 无 | 组别 | 

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "refresh_token": "1V1XLG8DQkROK6g-Rh5k17hZuZHQVZB9_1527339048",
        "access_token": "neRlaCJcbMQHgPmZqRjqYgVBfFZUe7lm_1527339048",
        "expiration_time": 172800,
        "member": {
            "id": 1,
            "username": "admin",
            "type": 1,
            "nickname": "简言",
            "realname": null,
            "head_portrait": null,
            "sex": 1,
            "qq": null,
            "email": "1@qq.com",
            "birthday": null,
            "user_money": "0.00",
            "accumulate_money": "0.00",
            "frozen_money": "0.00",
            "user_integral": 0,
            "address_id": "0",
            "visit_count": 8,
            "home_phone": null,
            "mobile": null,
            "role": 10,
            "last_time": 1527339048,
            "last_ip": "127.0.0.1",
            "provinces": 0,
            "city": 0,
            "area": 0,
            "allowance": 2,
            "allowance_updated_at": 1527339048,
            "status": 10,
            "append": 1511169880,
            "updated": 1527339048
        }
    }
}
```

#### 重置令牌

请求地址(Post)

```
/v1/site/refresh
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
refresh_token | string| 是 | 无 | 重置令牌 |
group | string| 是 | 无 | 组别 | 

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "refresh_token": "ZQqIzE91lZsOsiBZUzX_HRvH_er71IA3_1527339061",
        "access_token": "y7ch3kQtRq7dEkqf6le2LOyRNOB_xzQV_1527339061",
        "expiration_time": 172800,
        "member": {
            "id": 1,
            "username": "admin",
            "type": 1,
            "nickname": "简言",
            "realname": null,
            "head_portrait": null,
            "sex": 1,
            "qq": null,
            "email": "1@qq.com",
            "birthday": null,
            "user_money": "0.00",
            "accumulate_money": "0.00",
            "frozen_money": "0.00",
            "user_integral": 0,
            "address_id": "0",
            "visit_count": 9,
            "home_phone": null,
            "mobile": null,
            "role": 10,
            "last_time": 1527339061,
            "last_ip": "127.0.0.1",
            "provinces": 0,
            "city": 0,
            "area": 0,
            "allowance": 2,
            "allowance_updated_at": 1527339061,
            "status": 10,
            "append": 1511169880,
            "updated": 1527339061
        }
    }
}
```

### 小程序登录注册

#### 加密数据进行解密认证

请求地址(Post)

```
/v1/mini-program/login
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明
---|---|---|---|---
signature | string| 是 | 无 | 使用 sha1( rawData + sessionkey ) 得到字符串，用于校验用户信息
encryptedData | string| 是 | 无 | 包括敏感数据在内的完整用户信息的加密数据
rawData | string| 是 | 无 | 不包括敏感信息的原始数据字符串，用于计算签名
iv | string| 是 | 无 | 加密算法的初始向量
code | string| 是 | 无 | 小程序code

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "refresh_token": "ZQqIzE91lZsOsiBZUzX_HRvH_er71IA3_1527339061",
        "access_token": "y7ch3kQtRq7dEkqf6le2LOyRNOB_xzQV_1527339061",
        "expiration_time": 172800,
        "member": {
            "id": 1,
            "username": "admin",
            "type": 1,
            "nickname": "简言",
            "realname": null,
            "head_portrait": null,
            "sex": 1,
            "qq": null,
            "email": "1@qq.com",
            "birthday": null,
            "user_money": "0.00",
            "accumulate_money": "0.00",
            "frozen_money": "0.00",
            "user_integral": 0,
            "address_id": "0",
            "visit_count": 9,
            "home_phone": null,
            "mobile": null,
            "role": 10,
            "last_time": 1527339061,
            "last_ip": "127.0.0.1",
            "provinces": 0,
            "city": 0,
            "area": 0,
            "allowance": 2,
            "allowance_updated_at": 1527339061,
            "status": 10,
            "append": 1511169880,
            "updated": 1527339061
        }
    }
}
```

### 文件上传

目录

- 图片上传
- 视频上传
- 语音上传
- 文件上传
- base64上传
- 获取 oss 直传配置

#### 图片上传

请求地址(Post)

```
/v1/file/images?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
file | file| 是 | 无 | 文件 | 
drive | string| 否 | local | 本地上传 | oss:阿里云;qiniu:七牛;cos:腾讯
thumb | array| 否 | 无 | 生成缩略图(具体看例子) | 

thumb 数组例子(生成`100*100`和`200*200`的缩略图)

```
{
	"thumb": [{
		"width": 100,
		"height": 100
	}, {
		"width": 200,
		"height": 200
	}]
}
```

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "url": "1.jpg",
    }
}
```

#### 视频上传

请求地址(Post)

```
/v1/file/videos?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
file | file| 是 | 无 | 文件 | 
drive | string| 否 | local | 本地上传 | oss:阿里云;qiniu:七牛;cos:腾讯

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "url": "1.mp4",
    }
}
```

#### 语音上传

请求地址(Post)

```
/v1/file/voices?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
file | file| 是 | 无 | 文件 | 
drive | string| 否 | local | 本地上传 | oss:阿里云;qiniu:七牛;cos:腾讯

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "url": "1.jpg",
    }
}
```

#### 文件上传

请求地址(Post)

```
/v1/file/files?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
file | file| 是 | 无 | 文件 | 
drive | string| 否 | local | 本地上传 | oss:阿里云;qiniu:七牛;cos:腾讯

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "url": "1.jpg",
    }
}
```

#### base64上传

请求地址(Post)

```
/v1/file/base64?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
image | string| 是 | 无 | 文件 | 
drive | string| 否 | local | 本地上传 | oss:阿里云;qiniu:七牛;cos:腾讯
extend | string| 否 | jpg | 文件后缀 | 

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "url": "1.jpg",
    }
}
```

#### 获取 oss 直传配置

请求地址(Get)

```
/v1/file/oss-accredit?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
type | string| 是 | 无 | 上传类型 | 图片:images;文件:files;视频:videos;音频:voices

> 注意: 上传到 oss 的时候建议多附加几个参数  
>  
> x:upload_id, 上传者IP  
> x:type, 上传类型  
> x:host, 返回的 host 字段  
> x:merchant_id, 当前商户的id  

返回

```
{
"code": "200",
"message": "获取成功",
"data": {
    "Filename": "${filename}",
    "key": "images/2019/10/23/${filename}",
    "OSSAccessKeyId": "",
    "success_action_status": "201",
    "host": "http://file.aliyun-oss.com",
    "policy": "",
    "signature": "",
    "callback": ""
  }
}
```

### 个人信息

目录

- 详情
- 修改

> 注意这里的ID为用户ID

#### 详情

请求地址(Get)

```
/v1/member/member/[ID]?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明
---|---|---|---|---

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "id": "1",
        "username": "admin",
        "nickname": "简言",
        "realname": null,
        "head_portrait": null,
        "sex": "1",
        "qq": null,
        "email": "1@qq.com",
        "birthday": null,
        "user_money": "0.00",
        "user_integral": "0",
        "status": "1",
        "created_at": "1511169880"
    }
}
```

#### 修改

请求地址(Put)

```
/v1/member/member/[ID]?access-token=[access-token]
```

参数

参数名 | 参数类型| 必填 | 默认 | 说明
---|---|---|---|---

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "id": "1",
        "username": "admin",
        "nickname": "简言",
        "realname": null,
        "head_portrait": null,
        "sex": "1",
        "qq": null,
        "email": "1@qq.com",
        "birthday": null,
        "user_money": "0.00",
        "user_integral": "0",
        "status": "1",
        "created_at": "1511169880"
    }
}
```

### 支付生成参数

#### 生成支付配置

请求地址(Post)

```
/v1/pay?access-token=[access-token]
```

参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
pay_type | int | 是 | 无 | 支付类型 | 1:微信;2:支付宝;3:银联;5:余额支付 |
trade_type | string | 是 | 无 | 交易类型 | 具体查看下文具体参数说明 |
order_group | string | 是 | 无 | 订单类型 | recharge:充值;order:订单 | 
data | string | 是 | 无 | json格式数组具体看下文 |

tradeType

> 根据对应的支付类型，选择支持的交易类型即可，例如：app

```
支付宝：'native', 'app', 'js', 'pos', 'mweb'
微信：'pc', 'app', 'f2f', 'wap', 'mini_program'
银联：'app', 'html'
余额：'default'
```

支付宝返回

```
// 不同支付(app/js等)返回的数据不同，以下是app支付返回
{
    "code": 200,
    "message": "OK",
    "data": {
        "config": ""
    }
}
```

微信返回

```
// 不同支付(app/js等)返回的数据不同，以下是app支付返回
{
    "code": 200,
    "message": "OK",
    "data": {
        "appid": "",
        "partnerid": "",
        "prepayid": "",
        "package": "Sign=WXPay",
        "noncestr": "98f97315adbd8992166d57c8b6ce0775",
        "timestamp": ,
        "sign": ""
    }
}
```






<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

