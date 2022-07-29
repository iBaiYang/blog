---
layout: post
categories: Yii2
title: RageFrame 使用引导
meta: RageFrame 使用引导
---
* content
{:toc}

## 正文


## 文档

### 介绍安装

#### 系统介绍


#### 环境搭建

目录

- Windows环境
- Windows微信开发环境
- Linux环境
- Mac环境

##### Windows环境

在Windows环境下，建议开发者使用phpStudy环境套件，下面引用官方的介绍：
该程序包集成最新的Apache+Nginx+LightTPD+PHP+MySQL+phpMyAdmin+Zend Optimizer+Zend Loader，一次性安装，无须配置即可使用，是非常方便、好用的PHP调试环境。该程序绿色小巧简易迷你仅有32M，有专门的控制面板。总之学习PHP只需一个包。
对学习PHP的新手来说，WINDOWS下环境配置是一件很困难的事；对老手来说也是一件烦琐的事。因此无论你是新手还是老手，该程序包都是一个不错的选择。
全面适合 Win2000/XP/2003/win7/win8/win2008 操作系统 ,支持Apache、IIS、Nginx和LightTPD。
新特征：完美支持win10，支持自定义php版本
下载地址：[phpStudy](http://www.phpstudy.net/)

##### Windows微信开发环境

1、微信开发调试会有缓存问题导致无法使用获取用户信息。原因默认的C:\Windows目录无写入权限。调整php.ini中的sys_temp_dir路径即可。

> wechat SDK 遵循了官方建议，所以在调用这些接口时，除了按照官方文档设置操作证书文件外，还需要保证服务器正确安装了 CA 证书。

2、下载 CA 证书

你可以从 [http://curl.haxx.se/ca/cacert.pem](http://curl.haxx.se/ca/cacert.pem) 下载 或者 使用微信官方提供的证书中的 CA 证书 [rootca.pem](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1) 也是同样的效果。在 php.ini 中配置 CA 证书

3、只需要将上面下载好的 CA 证书放置到您的服务器上某个位置，然后修改 php.ini 的 curl.cainfo 为该路径（绝对路径！），重启 php-fpm 服务即可。

```
curl.cainfo = /path/to/downloaded/cacert.pem
```

>  注意证书文件路径为绝对路径！以自己实际情况为准。

其它修改 HTTP 类源文件的方式是不允许的。

##### Linux环境

- 使用 [宝塔面板](https://www.bt.cn/)，该面板使用起来简单，功能强大。
- 使用 [LNMP](https://lnmp.org/) 一键安装包
- 使用 [Docker安装](https://github.com/jianyan74/dockerfiles)

##### Mac环境

在Mac环境下，推荐使用[Mamp](https://www.mamp.info/en/)

#### 系统安装

目录

- 环境要求
- 安装
- 站点配置及说明

##### 环境要求

- PHP >= 7.2
- PHP cURL 扩展
- PHP OpenSSL 扩展
- PHP fileinfo 拓展 素材管理模块需要用到
- Mysql >= 5.7.7
- Apache 或 Nginx
- Composer (用于管理第三方扩展包)
- 安装CA证书 (windows开发环境下)

> 必须先看[环境搭建文档](#环境搭建)，安装完毕后务必配置站点和对应的[伪静态](#伪静态)还有[常见问题文档](#常见问题文档)

##### 安装

> 如果你是群里下载的安装包，可直接第五步开始

1、克隆

```
git clone https://github.com/jianyan74/rageframe2.git
```

2、进入目录

```
cd rageframe2
```

3、安装依赖

```
// (不建议) 如果你只有 php 7.1 也可以忽略版本进行安装 php composer.phar install --ignore-platform-reqs

php composer.phar install 
```

4、初始化项目

```
php init // 然后输入0回车,再输入yes回车
```

5、配置数据库信息

```
找到 common/config/main-local.php 并配置相应的信息, 注意要先创建好数据库
```

6、安装数据库(Mysql5.7及以上)

```
php ./yii migrate/up
```

7、初始化账号密码，一键创建总管理员账号密码(注意保存)

```
php ./yii password/init
```

> 截止到这里就安装完成了，可以去配置站点了，下面(8、9步骤)的都是根据自己实际的情况去执行

8、建议更新第三方扩展包(可选)

```
php composer.phar update
```

9、Linux 下文件缓存权限授权

Linux 环境下如果是文件缓存去  `backend/runtime`  目录执行一下 `chmod -R 777 cache`，不执行可能会造成修改了网站设置缓存不生效的情况

##### 站点配置

> 注意：Nginx/IIS 先要设置好[伪静态](#伪静态)，Apache 默认已配置

站点指向目录为当前项目的web下 

例如: 

```
/path/to/rageframe2/web/
```

访问说明

应用 | Url
---|---
后台 | 当前域名/backend
商户 | 当前域名/merchant
商户接口 | 当前域名/merapi
Html5 | 当前域名/html5
Api | 当前域名/api
OAuth2 | 当前域名/oauth2

> 安装成功后如果需要微信公众号管理、商户管理等等功能，请到 系统管理->应用管理 进行安装插件


#### 伪静态

目录

- Nginx
- Apache
- IIS

##### Nginx

推荐配置

```
location / {
    try_files $uri $uri/ /index.php$is_args$args;
}
location /backend {
    try_files $uri $uri/ /backend/index.php$is_args$args;
}
location /api {
    try_files $uri $uri/ /api/index.php$is_args$args;
}
location /merchant {
    try_files $uri $uri/ /merchant/index.php$is_args$args;
}
location /merapi {
    try_files $uri $uri/ /merapi/index.php$is_args$args;
}
location /html5 {
    try_files $uri $uri/ /html5/index.php$is_args$args;
}
location /oauth2 {
    try_files $uri $uri/ /oauth2/index.php$is_args$args;
}

location ~* ^/attachment/.*\.(php|php5)$ 
{
    deny all;
}
```

类似Apache的配置

```
location / 
{
     index  index.html index.htm index.php;

     if (!-e $request_filename) {
           rewrite ^/backend(.*)$ /backend/index.php?s=$1 last;
           rewrite ^/merchant(.*)$ /merchant/index.php?s=$1 last;
           rewrite ^/merapi(.*)$ /merapi/index.php?s=$1 last;
           rewrite ^/api(.*)$ /api/index.php?s=$1 last;
           rewrite ^/html5(.*)$ /html5/index.php?s=$1 last;
           rewrite ^/oauth2(.*)$ /oauth2/index.php?s=$1 last;
           rewrite ^/(.*)$ /index.php?s=$1 last;
           break;
     }
         
     #autoindex  on;
}
```

##### Apache

> 注意系统默认自带了.htaccess，所以环境如果是apache可以不用再配置

```
Options +FollowSymLinks
IndexIgnore */*
RewriteEngine on

# if a directory or a file exists, use it directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# otherwise forward it to index.php
RewriteRule . index.php
```

##### IIS

> rule 部分配置

```
<rule name="backend" stopProcessing="true">
    <match url="^backend/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="backend/index.php/{R:1}" />
</rule>
<rule name="merchant" stopProcessing="true">
    <match url="^merchant/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="merchant/index.php/{R:1}" />
</rule>
<rule name="merapi" stopProcessing="true">
    <match url="^merapi/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="merapi/index.php/{R:1}" />
</rule>
<rule name="html5" stopProcessing="true">
    <match url="^html5/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="html5/index.php/{R:1}" />
</rule>
<rule name="api" stopProcessing="true">
    <match url="^api/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="api/index.php/{R:1}" />
</rule>
<rule name="oauth2" stopProcessing="true">
    <match url="^oauth2/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="oauth2/index.php/{R:1}" />
</rule>
<rule name="frontend" stopProcessing="true">
    <match url="^(.*)$" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="index.php/{R:1}" />
</rule>
```

#### 如何提问

##### 说明环境情况

- 平台: windows/linux
- 软件与版本：nginx 1.9, PHP 5.6 ...
- 系统版本：rageframe 2.5.22

##### 你做了什么？

- 我按文档（附链接）上写了一个控制器xxx，代码如下：xxxx
- 然后我使用xxx访问
- 这其中我还做了啥
- ...

##### 出现什么错误？

- 当我试图xxx的时候，xxx报了如下错误：（截图）
- 我确认过我的目录权限是正确的，xxx是正确的（必要时带上截图）

以上是举例提问时所应该描述的情况，当别人看到详细的情况时，也便于快速定位问题所在。

#### 常见问题

目录

- 出现 vendor/bower/jquery/dist 找不到的解决方案
- 访问项目样式全部加载不到失调
- 设置站点后会出现个别样式加载 404 
- 配置成功后首页访问正常，其他页面访问报404错误
- 访问微信应用 出现 redirect_url 参数错误
- 小程序Post提交服务器无法接收到数据
- Windows环境打开后台微信报错
- 如何配置权限管理的路由和菜单绑定
- 数据迁移出现 Specified key was too long; max key length is 767 bytes 
- Api应用增加了控制器方法，还是出现404
- 商户端如何进行登录
- 安装数据库出现 json 报错不支持
- 安装插件一直有几个数据库文件未安装成功

##### 出现 vendor/bower/jquery/dist 找不到的解决方案

执行以下代码

```
composer global require "fxp/composer-asset-plugin:^1.4.0"
```

##### 访问项目样式全部加载不到失调

由于是未设置站点，请配置站点到根目录的web下

##### 设置站点后会出现个别样式加载 404

找到 `web/backend/assets` 下面的所有目录文件都删除重新试一次，不行就多试几次

##### 配置成功后首页访问正常，其他页面访问报404错误

请配置对应的伪静态配置

##### 访问微信应用 出现 redirect_url 参数错误

这是由于程序使用了网页授权而公众号没有正确配置【网页授权域名】所致。此时你需要登录微信公众平台，在【开发】->【接口权限】页面找到网页授权获取用户基本信息进行配置并保存。

- 网页授权域名应该为通过 ICP 备案的有效域名，否则保存时无法通过安全监测。
- 网页授权域名即程序完成授权获得授权 code 后跳转到的页面的域名，一般情况下为你的业务域名。
- 网页授权域名配置成功后会立即生效。
- 公众号的网页授权域名只可配置一个，请合理规划你的业务，否则你会发现……授权域名不够用哈。

##### 小程序Post提交服务器无法接收到数据

$_POST 只能接收 Content-Type 为 application/x-www-form-urlencoded 和 multipart/form-data 的 POST 数据。

如果你要用 $_POST 的话，你就改一下 Content-Type 这里替换为上面的其中一个：

```
header: {
      'Content-Type': 'application/json'
 }
```

把上面的 application/json 改成 application/x-www-form-urlencoded (如果要上传文件的话就改成 multipart/form-data，但是微信小程序里的上传文件用的是另外一个 API，具体的你可以仔细看一下文档)。

##### Windows环境打开后台微信报错

注意查看[环境搭建](#环境搭建)的Windows微信环境配置

##### 如何配置权限管理的路由和菜单绑定

只要把权限路由和菜单的路由统一，就能自由控制菜单显示了，比如菜单路由 menu-sys 那么权限这边也要相对应的加上 menu-sys

##### 数据迁移出现 Specified key was too long; max key length is 767 bytes 

> 由于数据库版本问题，解决方法找到数据迁移目录下的那张表，修改编码 utf8mb4 为 utf8

##### Api应用增加了控制器方法，还是出现404

> 由于开启了路由严格验证，所有的控制器方法都需要在main里面去配置rule，且单独的方法(不是CURD)也需要单独配置

##### 商户端如何进行登录

> 请去 系统管理->应用管理 安装商户管理插件，自行创建账号密码及角色信息
>

##### 安装数据库出现 json 报错不支持

> 请安装 mysql5.7 及以上版本的数据库

##### 安装插件一直有几个数据库文件未安装成功

```
// 里面的 TinyShop 可以替换成为你对应要安装的插件名称 
php ./yii migrate --migrationPath=@addons/TinyShop/console/migrations
```

#### 更新历史

注意：

> 各个小版本升级例如 2.1.x 升级到 2.2.x 以上不能完美升级最好重新安装
git 

##### v2.6.57
updated 2020.9.13

- 增加: 易联云小票打印组件
- 增加: 飞鹅小票打印组件
- 增加: jsTree 对数据进行 ajax 编辑表单控件
- 增加: 后台管理员绑定微信功能
- 增加: 支付宝单笔转账和转账查询
- 优化: 全局日志的数据统计
- 优化: 支付接口增加交易流水号获取
- 优化: 安装插件的最大执行时间为 300s
- 优化: 树形插件和多级联动对于特殊字符内容解析错误
- 优化: GridView 在渲染了 bootstrap-table 情况表格头样式
- 修复: 微信多端支付，退款可能失败
- 修复: oauth2 生成 token 记录只能记录一个用户
- 修复: 授权的用户添加商户角色无法添加顶级角色
- 升级: Yii2 版本到2.0.37

##### v2.6.43
updated 2020.7.2

- 增加: 递归多级联动选择器
- 增加: api 可以不进行格式化处理返回
- 增加: md5 校验文件驱动判断
- 修复: 插件的菜单附加参数时候在顶部菜单不显示
- 修复: 在插件下 OSS 直传找不到回调地址
- 修复: 页面分页中选择页面和前往某页会丢失 url 参数

##### v2.6.37
updated 2020.6.1
  
- 增加: 个推服务
- 优化: 管理后台的主页面的线条
- 优化: 远程图片下载
- 优化: 百度编辑器上传图片顺序按照选择顺序来
- 优化: 卸载插件找不到表会导致报错
- 优化: 数据库查询对一些常用的表进行索引建立
- 修复: 商户注册自动审核未分配默认的角色
- 修复: GridView 在渲染了 bootstrap-table 情况下搜索失败
- 修复: 没有菜单情况下安装插件会报找不到 menu 属性
- 修复: Linux 环境下初次安装会导致缓存写入失败
- 修复: 后台添加手机号码未判断唯一
- 修复: 微信公众号扫描二维码次数未增加
- 修复: 微信公众号扫描二维码统计关联二维码 id 未入库
- 升级: Yii2 版本到2.0.35

##### v2.6.23
updated 2020.4.20
  
- 增加: 表情选择器 Widget
- 增加: md5 校验上传支持开启和关闭
- 优化: GridView 对小屏屏幕的展现效果
- 优化: 百度编辑器外链上传支持 md5 校验
- 优化: 编辑页面跳转回首页为原先的页面
- 修复: 微信插件多公众号的情况下同步粉丝导致 merchant_id 错误
- 修复: 禁用插件的情况下卸载还有遗留
- 修复: 个别情况下跨域无效
- 修复: 百度编辑器外链同时多个上传文件名命名错误
- 修复: 百度编辑器使用居中/居左对齐无效
- 修复: 插件创建时候未自动生成 merapi 的配置引入
- 修复: 商户管理子角色无法被分配
- 升级: Yii2 版本到2.0.34
 
##### v2.6.10
updated 2020.3.11
 
- 增加: merapi 应用(商户端 Api)
- 增加: 风控日志记录
- 优化: 获取后台配置和商户端配置隔离，之前的获取配置方式可用，但是请先看文档了解其规则
- 优化: 权限角色创建判断，进行解耦，更加方便增加新的应用权限(破坏性改动，升级需注意)
- 优化: 权限判断路由 [@uutan](https://github.com/uutan)
- 优化: 插件商户映射功能，如果想总后台也可以直接用请在启动项写入 商户 id 为 0
- 优化: 原先后台默认商户 id 为 1, 修改为后台和商户端独立，方便开发
- 修复: Gii Curd 有些字段类型有不存在的情况会报错 [@uutan](https://github.com/uutan)
- 移动: 会员模块代码到总后台端
- 移动: 商户端公用模块和基础模块到商户管理插件

##### v2.5.36
updated 2020.2.5

- 增加: 百度图表[去年]类型查询
- 增加: 全局日志流量统计(PV/UV/IP)
- 优化: 动态注入插件服务，启动即注册，方便其他地方调用
- 优化: 读取配置，读取后直接丢入变量，不用重复去缓存读取
- 增加: 行为日志记录可支持队列写入
- 修复: 独立域名部署后，在系统信息里面显示错误
- 修复: 代码错误的情况下，导致报错失败
- 修复: AccessToken 关联权限 app_id 字段错误
- 修复: 行为日志初始化 url 填写错误
- 修复: 微信插件选择素材翻页时的链接错 [@uutan](https://github.com/uutan)
- 修复: 商户端接收微信消息时，未指定商户ID无法正常找到商户的微信公众号的配置参数 [@uutan](https://github.com/uutan)
- 修复: Html5 应用回调判断 AppId 获取错误 [@uutan](https://github.com/uutan)
- 修复: 在Merchant端调用微信设置时，结果没有按商户ID进行保存和调用 [@uutan](https://github.com/uutan)
- 升级: 后台主题 AminLTE 版本为 2.4.18

##### v2.5.22
updated 2020.1.6

- 增加: b2c 和 b2b2c 相互兼容架构概念，更好的支持 saas 化项目
- 增加: 会员等级功能及服务
- 增加: 快递查询追踪组件
- 增加: 百度图表自定义日期查询
- 优化: 记录日志从方法过滤到个别字段过滤
- 优化: 已有插件开启商户映射的路径
- 优化: 插件安装时候安装默认的权限
- 优化: 支付枚举类，进行拆分为状态、组别、类型
- 优化: 会员账户信息扣减增加，支持第三方支付记录
- 优化: 会员日志记录为消费日志、包含余额变动、积分日志
- 优化: 统一支持和支付回调，更加灵活和安全
- 优化: 网站配置信息的读取和获取
- 优化: \common\helpers\ResultHelper::api 进 \common\helpers\ResultHelper::json
- 修复: 插件生成的默认 api 控制器的登录权限属性还是 optional, 应是 authOptional
- 修复: 队列发送短信找不到 ip
- 修复: 商户角色获取上级的时候会获取其他商户的角色信息
- 修复: gii 生成的文件获取枚举错误
- 移动: 会员模块代码到商户端
- 移动: 微信插件、内容管理插件、示例管理插件等的原先后台的代码到对应的插件商户端
- 调整: 插件模块底层处理及路由优化
- 移除: 插件模块钩子功能
- 升级: Yii2 版本到2.0.32

##### v2.4.33
updated 2019.11.29

- 优化: 解析网站设置解析说明的 html
- 优化: 微信图文素材上传不记录到表 [@yoyo0926](https://github.com/yoyo0926)
- 优化: 兼容 php 7.4
- 修复: 商家管理添加用户未写入商户 id
- 修复: 商家编辑插件分类时写入类型错误
- 修复: 页码数量 20 导致和系统默认数量冲突被移除
- 修复: 未判断父级行为返回导致跨域验证失败

##### v2.4.27
updated 2019.11.25

- 修复: 商户禁用未让用户登录失效
- 修复: 设置 Redis 缓存时候设置省市区过期时间过长导致错误 
- 修复: 商家权限添加无法添加二级以下的权限
- 修复: 按钮在个别情况下会导致权限验证不能通过
- 修复: 微信图片资源路径错误
- 升级: Yii2版本到2.0.30

##### v2.4.21
updated 2019.11.15

- 增加: 商户端 Merchant
- 增加: 文件上传支持获取 OSS 直传配置和 MD5 查询，方便前端直接上传
- 增加: Markdown Widget 预览支持图片放大
- 增加: Markdown Widget 支持自动保存数据和剪切板黏贴图片上传
- 增加: 全局日志记录 Header 的 App 信息
- 增加: 分页支持选择页码
- 增加: AdminLET Widget
- 优化: 商户管理插件并支持菜单管理、权限管理等
- 优化: 枚举继承枚举基类更方便获取键值
- 优化: 上传组件为全局组件
- 优化: 图片上传手机端禁用图片选择
- 优化: ExcelHeler 导出统一转为 String 类型
- 修复: 多商户情况下新增商户后支付回调提示找不到支付订单
- 修复: JsTree Widget 个别情况下判断选中失效
- 修复: 禁用应用中心顶部菜单切换菜单栏左侧菜单栏还会显示的 BUG
- 修复: 微信消息群发记录群发 ID 错误
- 修复: 后台菜单子菜单设置为顶级菜单 ID 设置错误
- 移动: backend\widgets 到 common\widgets
- 移动: 原先的微信模块到插件，并命名为 Wechat
- 改名: wechat 应用为 html5
- 升级: Yii2版本到2.0.29

##### v2.3.137
updated 2019.10.17

- 增加: Markdown Widget
- 增加: PhpStrom IDE 代码提示辅助类
- 增加: 消息通知可全部设为已读
- 优化: SearchModel 组件关联查询字段
- 优化: rageframe.js 兼容 IE 11
- 修复: Excel 导出文件打开有时候提示错误
- 修复: OAuth2 授权码模式不传递 redirect_uri 会导致报错

##### v2.3.130
updated 2019.9.25

- 优化: 公用微信 js 支付，调用 easywechat 的 js 支付视图进行兼容 [@yiirise](https://github.com/yiirise)
- 修复: 树型结构组件导致删除可能会移除其他记录
- 修复: 金额日志变动数据迁移字段缺少

##### v2.3.127
updated 2019.9.20

- 修复: ElaticSearch 案例连接配置问题导致连接失败
- 修复: 菜单分配顶级菜单报错
- 升级: Yii2版本到2.0.27

##### v2.3.124
updated 2019.9.19

- 增加: JsTree Widget
- 增加: Echarts Widget 条形图
- 增加: 增加提醒支持钉钉机器人通知和短信发送异常提醒
- 增加: 支付宝支付支持沙盒模式和 Debug 调试
- 增加: 微信模板/小程序模板发送服务 [@kbdxbt](https://github.com/kbdxbt)
- 增加: 百度编辑器视频支持封面 [@DickensGDC](https://github.com/DickensGDC)
- 优化: 插件调用钩子的方式
- 优化: OSS JS 直传的文件名解析和支持 CNAME 和 MD5 校验
- 优化: 经纬度选择/图片上传对于数据格式的容错程度
- 优化: 插件写入配置缓存和本身缓存
- 优化: 角色权限授予
- 优化: Tree 组件移动子级时候修改数据为递归修改
- 优化: 百度编辑器可配置全局上传驱动
- 优化: 消息提醒以支持后续更多的提醒方式例如钉钉/微信/短信等
- 优化: Api 可查看 Debug 显示
- 修复: Gii 生成 CURD 找不到模型会直接丢出 Error
- 修复: 上传表单组件不显示选择层后点击上传 Icon 无法触发上传
- 修复: 备份缓存移植到插件后清除缓存失败
- 修复: 提醒设置 JSON 解析失败
- 修复: 菜单移动顶级报错
- 修复: 裁剪上传和文件上传控件冲突导致删除图像 UI 显示问题
- 修复: 提醒设置非超级管理员无法设置
- 修复: 微信图文素材分页显示错位
- 修复: 支付宝支付回调地址未添加
- 修复: 经纬度选择 Widget 在 Linux 下调用错误
- 修复: 个别情况下后台 Js 提醒 Alert 组件不生效
- 修复: 微信头像字段长度不足和同步微信数据带来溢出 [@ccc008](https://github.com/ccc008)
- 合并: 会员金额变动总表到详细表
- 拆分: 会员表为会员账户信息(余额、积分等自动)表和基础表
- 升级: Yii2 版本到 2.0.26

##### v2.3.94
updated 2019.8.21

- 增加: 小助手插件支持二维码 Logo 生成
- 增加: 系统消息，支持行为异常提醒，请求异常提醒
- 增加: 数据迁移辅助类
- 增加: Echarts Widget 热点图
- 增加: 小助手插件支持数据迁移生成、Excel导入权限、省市区爬虫等功能，仅支持开发模式
- 增加: api登出功能 [@0xDarker](https://github.com/0xDarker)
- 增加: 权限检查支持 urlManager 中配置的路由规则 [@0xDarker](https://github.com/0xDarker)
- 优化: Gii生成 Curd，可自定义表单组件生成和首页显示
- 优化: Gii生成 Model，字段注释信息可直接当字段说明来用
- 优化: EasyWechat 的缓存改成 Yii2 的缓存
- 优化: 日志的详情格式化输出数据
- 优化: 应用id存储的数据表字段名称(不向前兼容)
- 优化: 插件的安装/卸载/更新，支持 Yii2 自带的数据库操作类和数据迁移方式
- 优化: 缓存前缀统一带上商户 ID
- 修复: 授权角色插件权限下菜单参数丢失
- 修复: 角色更新状态失败
- 修复: COS 初始化失败
- 修复: 独立图片域名情况下，上传图片微信服务器报错
- 修复: 全局配置如果复选框或者单选框未选择情况不提交数据
- 修复: 删除权限未删除已分配的权限的子权限
- 修复: 生成支付回调 Url 有多余的商家id导致验签失败
- 修复: 系统探针个别 php 环境下，导致崩溃 [@kbdxbt](https://github.com/kbdxbt)
- 修复: 导入数据找不到时间戳和生成 md 文档默认值为数组报错 [@kbdxbt](https://github.com/kbdxbt)
- 修复: 权限批量校验 bug [@0xDarker](https://github.com/0xDarker)
- 替换: 数据迁移扩展源为 `jianyan74/yii2-console-migration` 修复 json 字段情况下报错数据格式错误
- 替换: TreeGrid 扩展源为 `jianyan74/yii2-treegrid` 修复父 id 非 0 情况下显示错误
- 移动: 数据备份还原到小助手插件
- 移动: 系统探针到小助手插件
- 升级: Yii2 版本到 2.0.25

##### v2.3.65
updated 2019.7.29

- 增加: 行为Url动态监控记录敏感日志
- 增加: IP黑名单验证，设置后所有应用都不可访问
- 增加: 全局日志异常报表统计和短信发送异常报表统计
- 增加: 文件上传黑名单后缀验证
- 增加: 文件/图片上传的 OSS js 直传(不可以全局使用该驱动)
- 增加: 文件/图片上传 md5 验证实现秒传
- 增加: Url辅助类的 oauth2 和 storage 应用生成
- 增加: Echarts Widget 支持折线图/柱状图/饼图
- 增加: Auth 权限判断支持通配符 *，例如使用 `/*`、`/goods/*`等等
- 增加: 微信消息表情包解析
- 优化: 行为日志记录，更加全面的方便的记录信息
- 优化: 用户积分/余额变动服务
- 优化: 插件安装生成入口和菜单，支持参数生成传递(老的插件需要重新创建)
- 优化: oauth2 支持 windows 环境下进行开发测试
- 优化: 选择图片中的上传驱动和当前上传的一致
- 优化: 后台 Gii 一键生成 Curd
- 优化: 基础 Model 和查询 Model 位置，注意升级需统一替换路径
- 优化: 图片裁剪 widget 支持图片选择和多图片裁剪
- 优化: 短信发送日志报错记录
- 优化: 文件上传进度条显示
- 修复: base64文件上传到服务器追加导致失败
- 修复: 上传文件路径某些情况下会多一个 http 前缀
- 修复: 分片上传多个大文件时候会出现找不到文件错误
- 修复: TreeGrid 下删除Url填写错误
- 合并: upload.css 统一到 rageframe.widget.css
- 升级: Yii2 版本到 2.0.23

##### v2.3.39
updated 2019.6.28

- 增加: 增加个人信息内登录登出日志显示
- 增加: 地区选择(商城省市区选择) Widget
- 增加: 地图经纬度选择(百度、高德、腾讯) Widget
- 增加: 图片裁剪上传 Widget
- 增加: 增加配置管理类型Input组
- 增加: 功能案例的Model展示
- 增加: 腾讯 COS 对象存储上传
- 增加: 用 Iframe 进行表单提交功能和案例
- 增加: TreeGrid 表单控件
- 增加: 插件支持引导文件，一些预加载的方法都可在引导类里面执行
- 增加: oauth2 应用(授权码模式、简化模式、密码模式、客户端模式、刷新access_token)，支持对外部api开放
- 增加: 支持多商户数据库字段和代码结构
- 增加: 短信日志
- 增加: 自定义权限支持 前台/Api/Wechat 端权限控制
- 增加: 充值日志
- 增加: 会员后台直接充值积分/余额
- 优化: 顶部导航栏的消息通知为 Widget
- 优化: 系统内无极级分类都支持随意切换移动上级
- 优化: 整体的代码结构进行解耦，计算查询部分迁移至 Services层
- 优化: UI细节，更加友好
- 优化: 微信视频的同步支持同步视频详情
- 优化: 定时发送的创建更加友好
- 优化: 自动回复的的创建更加友好
- 优化: 微信/支付宝支付成功的回调
- 优化: 上传组件(图片、视频、语音、文件)支持多主题且支持自由增加主题
- 优化: 插件模块的安装、卸载、升级功能
- 优化: 插件模块的路由，使其更加简洁
- 优化: 数据库字段 text 类型存储的改为 json 类型
- 优化: UI细节方面关于输入框、标签、按钮等
- 优化: 原本的页面 Tree 渲染替换为 TreeGrid 表单控件
- 合并：插件权限和系统权限管理
- 合并：自动回复的表结构统一融入规则表内
- 修复: 模块关键字触发找不到控制器的BUG
- 修复: 微信粉丝标签无法删除
- 修复: 上传文件由于触发被动回调导致报错
- 修复: 解析IP辅助类解析失败
- 更换: Yii2自带的 rbac 为自定义的 rbac
- 升级: 后台主题 AminLTE 版本为 2.4.10
- 升级: Yii2版本到2.0.21

##### v2.2.52
updated 2019.6.2

- 修复: 菜单下拉偶尔会出现样式失调的情况
- 修复: 模拟框打开时间/日期控件的时候关闭异常

##### v2.2.50
updated 2019.5.28

- 修复: Html 辅助类变量名称错误
- 修复: Auth 辅助类逻辑错误
- 修复: 某些情况下初始化密码会出现重复显示字符串
- 修复: 插件权限判断未定义在插件内导致判断错误

##### v2.2.46
updated 2019.5.20

- 增加: 省市区三级联动可自由显示级别，例如只需要省市
- 优化: 会员表和用户模型名称
- 优化: 一些文件的命名
- 优化: 修改密码退出效果
- 合并: `AuthHelper` 和 `AddonAuthHelper` 做到插件和系统校验无感切换
- 合并: `HtmlHelper `和 `AddonHtmlHelper` 做到插件和系统无感切换
- 合并: `UrlHelper `和 `AddonUrlHelper` 做到插件和系统无感切换
- 修复: 百度编辑器 ajax model 渲染只渲染一次的问题
- 修复: ExcleHelper辅助类导出报格式错误
- 移动: 服务层 `services` 到根目录
- 移除: 默认路由 `.html` 后缀

##### v2.2.35
updated 2019.3.27

- 修复: gii 生成 curd 的首页报错
- 修复: 文档服务层单词错误
- 修复: api 的 token 命名错误

##### v2.2.32
updated 2019.3.11

- 增加: ID加密辅助类 HashidsHelper
- 优化: 图片/文件选择和微信资源选择
- 优化: 插件模块的应用入口生成二维码改为 jq 生成，加快生成速度
- 修复: 卸载插件模块时未卸载规则关键字
- 修复: 插件模块 Url 辅助类不支持 string 类型生成
- 修复: js 组件 fancybox 多图浏览时候动画失调
- 修复: 数据迁移报错
- 修复: api下用户信息 sex 字段未更改为 gender 字段的 bug
- 修复: 微信素材选择第一次可能会出现无法选中的情况
- 修复: 公用支付扩展由于版本落后导致报错
- 升级: Yii2 版本到 2.0.16.1

##### v2.2.21
updated 2019.2.18

- 增加: 插件模块权限辅助类，来支持页面路由验证
- 增加: 系统权限辅助类，来支持页面路由验证
- 增加: 后台 Tag 页签选择，支持单页面
- 增加: 系统公告和私信
- 增加: 兼容IE9的 js
- 修复: IE浏览器下刷新按钮链接过去错误
- 修复: 后台个人资料头像裁剪失败
- 修复: 百度编辑器 config 的 toolbars 配置数组合并与实际不符
- 修复: Html辅助类权限判断数组报错
- 优化: 微信授权登录为 Trait 类，方便插件模块或其他地方调用
- 优化: 权限校验查找
- 优化: 后台UI主题色彩
- 优化: 模拟框的显示后初始化
- 优化: 页面上面一些秘钥权限的块不进行显示
- 优化: 数据库的一些字段
- 优化: 系统 css/js 的加载
- 优化: 微信、微信支付、小程序等配置从启动文件直接配置
- 升级: Yii2 版本到 2.0.16
- 移除: 后台主题风格记录到数据库

##### v2.2.2
updated 2019.1.17

- 修复: 文件选择时 psd 文件显示错误
- 修复: 导航标签关闭时会出现剩余标签显示不全的情况
- 替换: 当前的后台主题为 AminLTE 2.4.5

##### v2.1.41
updated 2019.1.11

- 增加: 配置应用独立域名的支持
- 增加: 文件上传记录到数据库内
- 增加: 内容管理幻灯片及接口和文档
- 增加: 本地图像支持在线图像动态处理(缩略图/压缩/翻转等等)
- 增加: 文件存储应用
- 增加: 图片/文件上传在线选择功能
- 增加: 只显示权限可见的插件模块菜单
- 增加: 主动发送(客服)消息支持视频/图片/图文/音频
- 修复: 当只有默认菜单，没有个性化菜单时，官方返回的数据上没有 menuid 导致报错
- 修复: 同步个性化菜单，获取组别标签失败
- 修复: 更新图文消息失败
- 优化: 不在对上传的中文名进行转码读取，避免出现不会报错的情况
- 优化: api 速率控制为缓存读取
- 优化: api 的报错返回，不渲染页面再返回
- 优化: api 等权限校验逻辑，校验判断绑定到方法中
- 优化: 百度编辑器在线选择图片/文件
- 优化: 微信素材列表瀑布流显示
- 优化: 微信素材编辑百度编辑器在线选择图片列表为微信图片列表
- 优化: 上传辅助类来支持未来更多的辅助功能
- 优化: 插件模块的导航和菜单配置生成判断
- 更换: 定时任务组件为 `omnilight/yii2-scheduling`
- 重构: 微信选择图片/语音/视频/图文，修改为 widget 方式调用
- 移除: `2amigos/yii2-date-time-picker-widget` 扩展 如需使用看文档 表单控件 有替换组件

##### v2.1.18
updated 2018.12.28

- 增加: 插件模块权限管理
- 增加: 插件模块路由名称改为 Yii2 的风格
- 增加: 网站配置日期、时间、日期时间、复选框选项
- 增加: 新增/编辑/删除管理员、角色都被记录到行为日志
- 增加: api 接口除了 token 验证还支持签名验证，可一键开启
- 增加: Html 辅助类和插件 Html 模块辅助类
- 增加: 后台菜单支持参数传递
- 增加: 发送短信服务支持消息队列
- 修复: 菜单分类删除报错
- 修复: 插件模块 Grid 排序调整错误
- 修复: 小程序登录重置控制器生成日志路径错误
- 修复: 超出可视区域的时候头像截取不全的情况
- 修复: 插件模块多级目录下控制器使用 Url 短路由会出现识别路径错误的情况
- 修复: Mac 环境情况下开启了 ipv6 导致系统检索 ip 地址失败出现的超时
- 优化: api 登录重置功能
- 优化: 代码注释，让IDE开发智能提示更加友好
- 优化: 用户删除、用户地址、用户第三方授权信息删除都为伪删除
- 优化: 系统权限管理，更加符合用户需求
- 优化: 后台管理员操作流程，子角色管理员只可看见自己下属的管理员
- 迁移: 碎片管理中的日志为服务层内
- 移除: 码云 webhook 钩子的 api 接口及配置信息
- 移除: xunsearch 扩展 如果需要请自行安装，案例可在示例管理插件查看

##### v2.0.96
updated 2018.12.7

- 增加: 下载微信临时资源文件Demo
- 增加: 报错日志服务支持队列
- 增加: 发送邮件服务支持队列
- 增加: 显示第三方绑定会员信息
- 增加: Base64 格式图片上传到 Oss/七牛
- 增加: 百度编辑器远程图片上传到 Oss/七牛
- 修复: 关闭 Tag 页签时点击菜单不会重新加载菜单页面
- 修复: 后台头像编辑时候每次打开关闭会让样式往左不会复原
- 修复: 系统服务内 __get 方法内判断子服务类型错误
- 优化: 文件上传的 Oss/七牛 对象存储的接管上传方式
- 兼容: Grid 多主键的 Curd 状态修改

##### v2.0.85
updated 2018.11.29

- 增加: 发送文字消息给微信粉丝
- 增加: 百度编辑器 Oss/Qiniu 上传，目前只支持文件/图片/视频
- 增加: 百度编辑器可拉取远程图片然后上传到本地
- 增加: Ffmpeg 辅助类，提供提取视频信息转码等
- 优化: 微信接口报错机制，获取错误信息更加友好
- 优化: 插件模块后台必须登录才可访问
- 修复: 删除权限/菜单分类时未删除其全部子权限/菜单
- 修复: 实例化插件模块前未关闭 csrf ，导致插件模块内的 csrf 关闭/开启无效
- 修复: 文件辅助类的日志写入创建地址问题
- 修复: Windows 下微信语音中文名上传报错

##### v2.0.75
updated 2018.11.22

- 增加: 多图上传/多文件上传支持拖动排序
- 增加: 插件模块的钩子(hook)支持多个 hook 方法
- 增加: 示例模块的 Grid 案例
- 优化: 代码的规范和命名规则
- 优化: api 日志记录改成全局日志，支持 wechat/backend/frontend 报错记录
- 修复: 插件模块下自带的 ajax 更新找不到 id

##### v2.0.69
updated 2018.11.16

- 优化: api 日志记录，增加记录级别更好的进行区分报错
- 优化: 插件模块的资源目录，更加友好的区分前后台微信的资源，且和 Yii2 资源结构保持一致
- 修复: 修改菜单默认显示时候，未干掉其他正在显示的菜单
- 修复: 开启上传前缀时，百度编辑器选择在线管理的附件/图片前缀为空
- 修复: 角色授权显示时候，会把顶级分类下面的所有子分类都选中

##### v2.0.64
updated 2018.11.10

- 增加: 微信定时发送
- 增加: 前台 i18n 国际化 Demo
- 增加: 前台 第三方登录 Demo
- 增加: 无权限菜单不显示
- 优化: 微信自定义回复的语音和视频选择
- 优化: 省市区数据为最新的 2017.10.31 的国家统计局省市区数据
- 优化: 前台关于图片上传的 js 和 css 引入，避免资源依赖找不到
- 优化: 插件模块查询机制, 增加数据缓存依赖，依赖时间为 360 秒
- 优化: 文件上传的处理
- 优化: 分片上传的文件合并机制
- 修复: Windows 下百度编辑器查找文件列表中文编码报错
- 修复: Windows 下上传文件中文名报错
- 修复: 网站配置的多图上传引入路径错误
- 修复: 定时任务路径问题导致执行失败

##### v2.0.50
updated 2018.11.1

- 增加: 公用支付的银联支付及文档
- 增加: Api二次加密验证机制 Demo
- 增加: `购物节` 模块的微信分享 Demo，需手动开启
- 增加: `示例管理`模块的Api入口 Demo
- 增加: 服务层调用 Demo 及说明文档
- 优化: 必填文本框 Css 显示及一些页面小优化
- 优化: Api 的登录机制，支持多端登录(app/微信/小程序等)
- 优化: 插件模块路由机制，支持多级控制器目录
- 优化: 插件模块 hook 查询机制减少模块内重复调用的数据查询
- 优化: Excel 导出辅助类，优化填写 header 错误导致报错
- 修复: 插件模块左边菜单过长导致显示不全
- 修复: 插件模块Api入口 `POST/PUT/DELETE` 等方法访问失败

##### v2.0.38
updated 2018.10.17

- 增加: JS方法提供内页打开新标签页面
- 优化: 自动回复的图片回复选择方式
- 优化: 自动回复的图文回复选择方式
- 修复: 第三方授权的字段名错误导致用户数据插入失败

##### v2.0.34
updated 2018.10.15

- 增加: 内容管理插件的前台显示 Demo
- 增加: 会员积分/余额变动日志接口
- 增加: 插件模块 `购物节` 签到抽奖微信页面的 Demo
- 优化: 插件模块的 Hook 渲染报错机制
- 修复: 小程序解析数据变量重复导致获取不到 session 信息
- 修复: 第三方授权的字段名错误导致验证失败

##### v2.0.28
updated 2018.10.10

- 优化: Api 解耦速率控制，可自由选择是否被速率控制

##### v2.0.27
updated 2018.10.10

- 优化: 缓存清理各个应用的缓存文件夹读写判断
- 优化: 后台总管理员安全权限，初始化安装时随机总管理员的账号密码
- 修复: Linux 环境下创建插件报找不到模板文件
- 修复: Excel 导入找不到最后一列数据

##### v2.0.23
updated 2018.9.30

- 增加: 图片上传可压缩图片质量，从 `common/config/params` 里面配置
- 增加: 图片水印，从 `common/config/params` 里面配置
- 增加: 示例管理模块的消息队列、ElasticSearch、默认搜索 的 Demo
- 优化: 子权限账号后台权限名称显示
- 修复: 角色权限授权采用了 jstree 后，折叠顶级节点的时候无法获取到子节点

##### v2.0.18
updated 2018.9.26

- 增加: 示例管理模块 `MongoDB` 的 Curd，如需测试请自行安装 php 的 mongodb 扩展和 MongoDb 环境
- 优化: 多图上传的显示样式及功能
- 优化: 微信/小程序支付 Demo ，调整公用的回调控制器
- 修复: 由于上传视频和语音开启了全域名返回导致的上传到微信服务器错误
- 修复: 由于 api 的 controllerMap 位置不对导致的报错

##### v2.0.13
updated 2018.9.21

- 增加: 扩展模块 `文章管理` 提供二开示例及使用
- 修复: 由于默认微信参数没有导致的自动回复报错
- 修复: 由于上传图片开启了全域名返回导致的上传到微信服务器错误
- 修复: 由于上传 S 问题，导致了文件上传后删除时候不显示上传按钮
- 修复: 创建扩展模块时候资源目录为空引起的报错

##### v2.0.8
updated 2018.9.18

- 增加: Url辅助类，用于生成前台、api、微信地址
- 修复: 权限验证时候路由规则的判断错误
- 修复: 控制台中执行 php yii 的报错
- 优化: 图片上传缩略图配置，可设置多个缩略图
- 优化: 角色权限授权，采用了 jstree 插件表现页面
- 优化: 增强安全机制，安装流程中增加初始化随机生成10位密码

##### v2.0.2
updated 2018.9.10

- 2.0 全新上线

##### v2.0.1
updated 2018.6.11

- 增加: IP地址转地区组件
- 增加: 大部分基础功能

#####  v2.0.0
updated 2018.5.20
 
 - 初始化: 2.0 基础框架

###  系统开发

#### 目录结构

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

#### 开发规范

目录

- 开发说明
- 编码规范
- 框架规范
- 插件规范
- 数据库规范
- 类功能规范

##### 开发说明

- 正常开发只需要开发商户端，没有 Saas 的时候商户端就是总后台，有了 Saas，商户端就是子后台
- 插件勾选了商户映射，则所有的代码开发插件对应目录的 merchant 下开发，总后台(backend)访问的 url 对应的代码在 merchant 下
- 插件不勾选商户映射，总后台和商家后台的插件代码都是独立的

##### 编码规范

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

##### 框架规范

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

##### 插件规范

- 插件命名统一大写驼峰
- 资源文件在 当前插件根目录/应用/resource下
- 模型为当前插件的common/models下
- 更新文件更新表结构时候无法触发事务，需要单独设定版本号避免全部事务触发失败

##### 数据库规范

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

##### 类功能规范

###### Model(模型)

- 数据库字段管理
- 数据库关联管理
- 数据库触发行为
- 数据库入库验证

###### Form(模型 - 验证)

- 对提交的复杂数据进行验证处理返回

###### View(视图)

- 数据处理显示

###### Controller(控制器)

- 数据接收
- 服务调用
- 简单CURD入库
- 页面渲染

###### Service(服务 - 包含仓库「Repository」功能)

- 数据逻辑处理
- 数据入库
- 数据查询 (Repository)

#### 控制台

目录

- 自带默认任务
- 数据迁移
- 定时任务

##### 自带默认任务

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

##### 数据迁移

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

##### 定时任务

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

#### 表单控件

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

##### 颜色选择器

```
<?= $form->field($model, 'color')->widget(kartik\color\ColorInput::class, [
    'options' => ['placeholder' => '请选择颜色'],
]);?>
```

##### 日期控件

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

##### 时间控件

```
<?= $form->field($model, 'time')->widget(kartik\time\TimePicker::class, [
    'language' => 'zh-CN',
    'pluginOptions' => [
        'showSeconds' => true
    ]
]);?>
```

##### 日期时间控件

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

##### 日期区间控件

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

##### 图片上传控件

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

##### 多图上传控件

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

##### 文件上传控件

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

##### 多文件上传控件

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

##### 图片裁剪上传

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

##### 多Input框控件

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

##### 地图经纬度选择

```
// 注意提前申请好对应地图的key
<?= $form->field($model, 'address')->widget(\common\widgets\selectmap\Map::class, [
    'type' => 'amap', // amap高德;tencent:腾讯;baidu:百度
]); ?>
```

##### Select2

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

##### 省市区控件

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

##### 省市区控件(复选框)

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

##### 百度编辑器

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

##### Markdown 编辑器


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

##### 表情包选择

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

##### 递归多级联动选择器

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

##### TreeGrid

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

#### 系统组件

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

##### 基本组件

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

###### 获取全部配置信息

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

###### 打印调试

```
Yii::$app->debris->p();
```

###### 行为日志记录

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

###### 微信接口验证及报错

```
// 默认直接报错
Yii::$app->debris->getWechatError($message);

// 如果想不直接报错并返回报错信息
$error = Yii::$app->debris->getWechatError($message, false);
```

###### 解析 model 报错

```
// 注意 $firstErrors 为 $model->getFirstErrors();
Yii::$app->debris->analyErr($firstErrors);
```

##### 文件上传

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

##### 生成二维码

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

##### IP地址转地区

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

##### 快递查询

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

##### 小票打印

###### 易联云


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

##### Curl

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

##### 中文转拼音

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

##### 爬虫

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

##### Glide

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

#### 系统JS

目录

- 弹出框
- 内页打开新标签页面
- 关闭当前标签页
- Ajax 更新数据
- Js 模板引擎 Demo
- 用 Iframe 进行表单提交
- Bootstrap-table 支持

##### 弹出框

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

##### 内页打开新标签页面

达到此效果只需要给此元素一个class为openContab，指定新窗口的链接地址为href   
> 注：并不仅限于a元素，任意元素只要给class为openContab， href 属性皆可打开新tab

```
 <a class="openContab" href="<?= Url::to(['test/index'])?>">测试标签</a>
```

##### 关闭当前标签页

达到此效果只需要给此元素一个 class 为 closeCurrentConTab

```
<span class="closeCurrentConTab">关闭当前标签页</span>
```

##### Ajax 更新数据

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

##### Js 模板引擎 Demo

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

##### 用 Iframe 进行表单提交

```
// class带上 openIframe 即可，提交表单默认id为w0，具体案例看 功能案例->Curd Grid
<?= Html::create(['edit'], '创建', [
        'class' => 'btn btn-primary btn-xs openIframe',
]); ?>
```

##### Bootstrap-table 支持

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

#### Gii

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

##### Model

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

##### CURD

1、设置好模型路径(注意是已经创建好的Model)，选择生成控制器命名空间和视图文件路径，选择模板为 rageframe 即可进行生成预览

![image](images/gii-curd.jpg)

2、 勾选你首页需要显示的字段和编辑页面需要显示的字段和控件类型即可直接生成一个定制化的 CURD

![image](images/gii-curd-view.jpg)

3、选择后点击 `Preview` 预览，没有问题了可直接点击 `Generate` 创建进行生成

![image](images/gii-curd-view-files.jpg)

4、访问对应的路径进行查看，这样子关于页面显示、查询、编辑就完成了，就那么简单

#### 权限控制
#### 公用支付
#### 消息队列
#### 服务层
#### WebSocket
#### 公用服务
#### RESTful Api
#### 数据字典
#### 单元测试

### 微信开发

#### 微信扩展
#### 公众号绑定
#### 用户信息获取
#### JSSDK
#### 微信支付
#### 模版消息
#### 微信红包
#### 关键字回复
#### 消息概述和响应
#### Access Token

### 插件模块开发

#### 模块介绍及目录
#### 模块开发流程
#### 模块辅助说明

### Api

#### 接口说明
#### 接口加密
#### 登录重置
#### 小程序登录注册
#### 文件上传
#### 个人信息
#### 支付生成参数

### OAuth2

#### 授权/刷新Token
#### 个人信息

### 助手类

#### Auth - 权限辅助类
#### Url - Url生成辅助类
#### Html - Html辅助类
#### ArrayHelper - 数组辅助类
#### FileHelper - 文件辅助类
#### DateHelper - 日期格式辅助类
#### PayHelper - 支付辅助类
#### ImageHelper - 图片辅助类
#### StringHelper - 字符串辅助类
#### RegularHelper - 正则验证辅助类
#### HashidsHelper - ID加密辅助类
#### UploadHelper - 上传辅助类
#### AddonHelper - 插件辅助类
#### ResultHelper - 格式化数据返回辅助类
#### ExcelHelper - Excel导入导出辅助类
#### ...

### 附录

#### 安全防范
#### SearchModel
#### 应用配置独立域名
#### 代码模板
#### 线上性能优化
#### 团队编辑器代码规范
#### PHP编程规范
#### Yii2核心框架代码风格
#### 扩展依赖安装使用
#### 网址收录



<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

