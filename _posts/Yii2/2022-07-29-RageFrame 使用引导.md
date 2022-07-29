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

### 系统开发

#### 目录结构
#### 开发规范
#### 控制台
#### 表单控件
#### 系统组件
#### 系统JS
#### Gii
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

