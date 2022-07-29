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

> 必须先看[环境搭建文档](#环境搭建)，安装完毕后务必配置站点和对应的[伪静态](./#伪静态)还有[常见问题文档](.#常见问题文档)

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

> 注意：Nginx/IIS 先要设置好[伪静态](start-rewrite.md)，Apache 默认已配置

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
#### 常见问题
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

