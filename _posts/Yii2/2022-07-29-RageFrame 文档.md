---
layout: post
categories: Yii2
title: RageFrame 文档
meta: RageFrame 文档
---
* content
{:toc}

目录：

    介绍安装
        系统介绍
            前言
            特色
            思维导图
            应用架构流程
            系统快照
            开始之前
            Demo
            官网
            文档
            插件
            问题反馈
            特别鸣谢
            版权信息
        环境搭建
            Windows环境
            Windows微信开发环境
            Linux环境
            Mac环境
        系统安装
            环境要求
            安装
            站点配置
        伪静态
            Nginx
            Apache
            IIS
        如何提问
            说明环境情况
            你做了什么？
            出现什么错误？
        常见问题
            出现 vendor/bower/jquery/dist 找不到的解决方案
            访问项目样式全部加载不到失调
            设置站点后会出现个别样式加载 404
            配置成功后首页访问正常，其他页面访问报404错误
            访问微信应用 出现 redirect_url 参数错误
            小程序Post提交服务器无法接收到数据
            Windows环境打开后台微信报错
            如何配置权限管理的路由和菜单绑定
            数据迁移出现 Specified key was too long; max key length is 767 bytes
            Api应用增加了控制器方法，还是出现404
            商户端如何进行登录
            安装数据库出现 json 报错不支持
            安装插件一直有几个数据库文件未安装成功
        更新历史
            v2.6.57
            v2.6.43
            v2.6.37
            v2.6.23
            v2.6.10
            v2.5.36
            v2.5.22
            v2.4.33
            v2.4.27
            v2.4.21
            v2.3.137
            v2.3.130
            v2.3.127
            v2.3.124
            v2.3.94
            v2.3.65
            v2.3.39
            v2.2.52
            v2.2.50
            v2.2.46
            v2.2.35
            v2.2.32
            v2.2.21
            v2.2.2
            v2.1.41
            v2.1.18
            v2.0.96
            v2.0.85
            v2.0.75
            v2.0.69
            v2.0.64
            v2.0.50
            v2.0.38
            v2.0.34
            v2.0.28
            v2.0.27
            v2.0.23
            v2.0.18
            v2.0.13
            v2.0.8
            v2.0.2
            v2.0.1
            v2.0.0
    系统开发
        目录结构
        开发规范
            开发说明
            编码规范
            框架规范
            插件规范
            数据库规范
            类功能规范
                Model(模型)
                Form(模型 - 验证)
                View(视图)
                Controller(控制器)
                Service(服务 - 包含仓库「Repository」功能)
        控制台
            自带默认任务
            数据迁移
            定时任务
        表单控件
            颜色选择器
            日期控件
            时间控件
            日期时间控件
            日期区间控件
            图片上传控件
            多图上传控件
            文件上传控件
            多文件上传控件
            图片裁剪上传
            多Input框控件
            地图经纬度选择
            Select2
            省市区控件
            省市区控件(复选框)
            百度编辑器
            Markdown 编辑器
            表情包选择
            递归多级联动选择器
            TreeGrid
        系统组件
            基本组件
                获取全部配置信息
                打印调试
                行为日志记录
                微信接口验证及报错
                解析 model 报错
            文件上传
            生成二维码
            IP地址转地区
            快递查询
            小票打印
                易联云
            Curl
            中文转拼音
            爬虫
            Glide
        系统JS
            弹出框
            内页打开新标签页面
            关闭当前标签页
            Ajax 更新数据
            Js 模板引擎 Demo
            用 Iframe 进行表单提交
            Bootstrap-table 支持
        Gii
            Model
            CURD
        权限控制
            控制说明
            如何页面块/按钮自由控制显示权限
            不进行权限认证的路由
            新增应用的 RBAC 授权
        公用支付
            支付宝
            微信
            银联
        消息队列
            DEMO 类
            处理队列
            作业状态
            Debug
            控制台
            进程监视器 Supervisor
            定时任务监控 Cron
        服务层
            配置
            调用
        WebSocket
            使用
            测试
        公用服务
        RESTful Api
            继承的基类说明
            速率和参数配置
            不进行格式化返回
            签名验证
            Url权限配置
            方法权限验证
            返回数据格式修改
            自定义code返回
            解析Model首个报错信息
            获取当前登录的用户信息
        数据字典
            rf_api_access_token
                api_授权秘钥表
            rf_backend_member
                系统_后台管理员表
            rf_backend_notify
                系统_消息公告表
            rf_backend_notify_member
                系统_消息查看时间记录表
            rf_backend_notify_pull_time
                系统_消息拉取表
            rf_backend_notify_subscription_config
                系统_消息配置表
            rf_common_action_behavior
                系统_行为表
            rf_common_action_log
                系统_行为表
            rf_common_addons
                公用_插件表
            rf_common_addons_binding
                公用_插件菜单表
            rf_common_addons_config
                公用_插件配置值表
            rf_common_attachment
                公用_文件管理
            rf_common_auth_assignment
                公用_会员授权角色表
            rf_common_auth_item
                公用_权限表
            rf_common_auth_item_child
                公用_授权角色权限表
            rf_common_auth_role
                公用_角色表
            rf_common_config
                公用_配置表
            rf_common_config_cate
                公用_配置分类表
            rf_common_config_value
                公用_配置值表
            rf_common_ip_blacklist
                公用_ip黑名单
            rf_common_log
                公用_日志
            rf_common_menu
                系统_菜单导航表
            rf_common_menu_cate
                系统_菜单分类表
            rf_common_pay_log
                公用_支付日志
            rf_common_provinces
                公用_省市区记录表
            rf_common_sms_log
                公用_短信发送日志
            rf_member
                用户_会员表
            rf_member_account
                会员_账户统计表
            rf_member_address
                用户_收货地址表
            rf_member_auth
                用户_第三方登录
            rf_member_balance_withdraw
                会员_余额提现记录表
            rf_member_bank_account
                会员提现账号
            rf_member_credits_log
                会员_积分等变动表
            rf_member_invoice
                会员_发票信息
            rf_member_level
                会员等级表
            rf_member_recharge_config
                会员_充值配置
            rf_merchant
                商户
            rf_merchant_member
                系统_后台管理员表
            rf_oauth2_access_token
                oauth2_授权令牌
            rf_oauth2_authorization_code
                oauth2_授权回调code
            rf_oauth2_client
                oauth2_授权客户端
            rf_oauth2_refresh_token
                oauth2_授权令牌
        单元测试
    微信开发
        微信扩展
        公众号绑定
        用户信息获取
        JSSDK
        微信支付
        模版消息
        微信红包
        关键字回复
        消息概述和响应
        Access Token
    插件模块开发
        模块介绍及目录
    模块介绍
        启动流程
        商户映射
        目录结构
        其他说明
        模块开发流程
            创建/维护
            权限
            数据迁移
            继承的基类说明
                后台
                api
                merapi
            开发
            访问路径
                前台插件访问路径
                后台插件访问路径
                api 插件访问路径
        模块辅助说明
            获取模块信息
            获取模块导航和菜单信息
            辅助方法
        钩子
            如何使用钩子?
            运行过程
            什么是钩子？
    Api
        接口说明
            测试域名
            接口版本
            北京时间格式
            公共入参说明
            公共出参说明
            公用请求方法
            公共状态码说明
        接口加密
            启动加密
            基本说明
            Get 请求
            Post请求
            Put/Delete请求
        登录重置
            登录
            重置令牌
        小程序登录注册
            加密数据进行解密认证
        文件上传
            图片上传
            视频上传
            语音上传
            文件上传
            base64上传
            获取 oss 直传配置
        个人信息
            详情
            修改
        支付生成参数
            生成支付配置
    OAuth2
        授权/刷新Token
            authorization_code(授权码模式)
            implicit(简化模式)
            password(密码模式)
            client_credentials(客户端模式)
            refresh_token(刷新access_token)
        个人信息
            当前用户
    助手类
        Auth - 权限辅助类
        Url - Url生成辅助类
        Html - Html辅助类
        ArrayHelper - 数组辅助类
        FileHelper - 文件辅助类
        DateHelper - 日期格式辅助类
        PayHelper - 支付辅助类
        ImageHelper - 图片辅助类
        StringHelper - 字符串辅助类
        RegularHelper - 正则验证辅助类
        HashidsHelper - ID加密辅助类
        UploadHelper - 上传辅助类
        AddonHelper - 插件辅助类
            获取资源文件路径
            获取/设置当前模块配置信息
        ResultHelper - 格式化数据返回辅助类
            Json
            返回示例
        ExcelHelper - Excel导入导出辅助类
            前置说明
            导出
            导入
        …
    附录
        安全防范
            XSS攻击
            CSRF攻击防范
            SQL注入
            Nginx指定目录禁止运行PHP
        SearchModel
        应用配置独立域名
            1、在群里(加群链接)下载应用配置独立域名包：
            2、替换根目录下的 environments 为下载内 environments
            3、将下载好的web目录整个单独放入各种的应用下例如：
            4、将根目录web下各自的resources放入各自的web下例如：
            5、设置各自的应用的虚拟站点到各自应用的web下例如：
            6、修改 common/config/bootstrap.php 下的 attachment和attachurl：
            7、修改 common/config/params.php
            8、删除根目录下的web目录
        代码模板
            首页
            编辑/创建页
            Ajax 模态框
        线上性能优化
            1、开启生产环境，在项目初始化的时候执行以下代码，并配置项目信息
            2、开启 OPC 缓存
            3、开启 AR 数据库的 schema 缓存
            4、使用别的存储方式session
            5、多使用局部缓存，整页缓存 ，http 缓存等，复杂数据库查询也可以做缓存依赖
            6、数据库索引等优化，尽量多的使用视图（当有必要）
            7、查询操作limit限制
            8、最小化的使用assets
            9、Composer Atuoloader 优化
            10、通过脚本处理中间数据
        团队编辑器代码规范
            PhpStorm 原生支持 PSR-2
        PHP编程规范
        Yii2核心框架代码风格
        扩展依赖安装使用
            MongoDB
        网址收录


## 介绍安装

### 系统介绍

重量级全栖框架，为二次开发而生

#### 前言

这是一款现代化、快速、高效、便捷、灵活、方便扩展的应用开发骨架。

RageFrame 创建于 2016 年 4 月 16 日，一个基于 Yii2 高级框架的快速开发引擎，目前正在成长中，目的是为了集成更多的基础功能，不再为相同的基础功能重复制造轮子，开箱即用，让开发变得更加简单。  
2018 年 9 月 10 日 2.0 版本正式上线，经过 1.0 版本一年多的开源反馈磨合，以更加优秀的形态出现。对 1.0 的版本进行了重构优化完善，更好的面向开发者进行二次开发。2.3.x 版本更是优化了底层突出了服务层，分离业务逻辑，支持多商户。  

#### 特色

- 极强的可扩展性，应用化，模块化，插件化机制敏捷开发。
- 极致的插件机制，微核架构，良好的功能延伸性，功能之间是隔离，可定制性高，可以渐进式地开发，逐步增加功能，安装和卸载不会对原来的系统产生影响,强大的功能完全满足各阶段的需求，支持用户多端访问(后台、微信、Api、前台等)。
- 极完善的 RBAC 权限控制管理、无限父子级权限分组、可自由分配子级权限，且按钮/链接/自定义内容/插件等都可加入权限控制。
- 只做基础底层内容，不会在上面开发过多的业务内容，满足绝大多数的系统二次开发。
- 多入口模式，多入口分为 Backend (后台)、Merchant (商户端)、Frontend (PC前端)、Html5 (手机端)、Console (控制台)、Api (对内接口)、OAuth2 Server (对外接口)、MerApi (商户接口)、Storage (静态资源)，不同的业务，不同的设备，进入不同的入口。
- 对接微信公众号且支持小程序，使用了一款优秀的微信非官方 SDK Easywechat 4.x，开箱即用，预置了绝大部分功能，大幅度的提升了微信开发效率。
- 整合了第三方登录，目前有 QQ、微信、微博、GitHub 等等。
- 整合了第三方支付，目前有微信支付、支付宝支付、银联支付，二次封装为网关多个支付一个入口一个出口。
- 整合了 RESTful API，支持前后端分离接口开发和 App 接口开发，可直接上手开发业务。
- 一键切换云存储，本地存储、腾讯 COS、阿里云 OSS、七牛云存储都可一键切换，且增加其他第三方存储也非常方便。
- 全面监控系统报错，报错日志写入数据库，方便定位错误信息。支持直接钉钉提醒。
- 快速高效的 Servises (服务层)，遵循 Yii2 的懒加载方式，只初始化使用到的组件服务。
- 丰富的表单控件(时间、日期、时间日期、日期范围选择、颜色选择器、省市区三级联动、省市区勾选、单图上传、多图上传、单文件上传、多文件上传、百度编辑器、百度图表、多文本编辑框、地图经纬度选择器、图片裁剪上传、TreeGrid、JsTree、Markdown 编辑器)和组件(二维码生成、Curl、IP地址转地区)，快速开发，不必再为基础组件而担忧。
- 快速生成 CURD ,无需编写代码，只需创建表设置路径就能出现一个完善的 CURD ,其中所需表单控件也是勾选即可直接生成。
- 正常开发只需要开发商户端，没有 Saas 的时候商户端就是总后台，有了 Saas，商户端就是子后台
- 完善的文档和辅助类，方便二次开发与集成。

#### 思维导图

![]({{site.baseurl}}/images/RageFrame2/RageFrame2.png)

#### 应用架构流程

![]({{site.baseurl}}/images/RageFrame2/app-flow.png)

#### 系统快照

【系统 - 首页】
![]({{site.baseurl}}/images/RageFrame2/sys-index.png)
【系统 - 配置管理】
![]({{site.baseurl}}/images/RageFrame2/sys-config.png)
【系统 - 角色编辑】
![]({{site.baseurl}}/images/RageFrame2/rbac-role.png)
【系统 - 日志统计】
![]({{site.baseurl}}/images/RageFrame2/log-stat.png)
【会员 - 信息】
![]({{site.baseurl}}/images/RageFrame2/member-info.png)
【微信 - 自定义菜单】
![]({{site.baseurl}}/images/RageFrame2/wechat-menu.png)
【插件模块 - 列表】
![]({{site.baseurl}}/images/RageFrame2/addon-list.png)
【插件模块 - 文章模块】
![]({{site.baseurl}}/images/RageFrame2/addon-activity.png)
【插件模块 - 系统监控】
![]({{site.baseurl}}/images/RageFrame2/system.png)

#### 开始之前

- 具备 PHP 基础知识
- 具备 Yii2 基础开发知识
- 具备 开发环境的搭建
- 仔细阅读文档，一般常见的报错可以自行先解决，解决不了再来提问
- 如果要做小程序或微信开发需要明白微信接口的组成，自有服务器、微信服务器、公众号（还有其它各种号）、测试号、以及通信原理（交互过程）
- 如果需要做接口开发(RESTful API)了解基本的 HTTP 协议，Header 头、请求方式（`GET\POST\PUT\PATCH\DELETE`）等
- 能查看日志和 Debug 技能
- 一定要仔细走一遍文档

#### Demo

地址：http://demo2.rageframe.com/backend  
账号：demo  
密码：123456

#### 官网

http://www.rageframe.com

#### 文档

[安装文档](#系统安装) · [本地文档](#文档) · [更新历史](#更新历史) · [常见问题](#常见问题)

#### 插件

- 微商城：https://github.com/jianyan74/TinyShop
- 微信公众号：https://github.com/jianyan74/Wechat
- 商家管理：https://github.com/jianyan74/Merchants
- 在线文档：https://github.com/jianyan74/RfOnlineDoc

#### 问题反馈 

在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

QQ群1：[655084090](https://jq.qq.com/?_wv=1027&k=4BeVA2r) (2000人快满)  
QQ群2：[1148015133](https://jq.qq.com/?_wv=1027&k=Wk663e9N) (新群)

GitHub：https://github.com/jianyan74/rageframe2/issues

#### 特别鸣谢

感谢以下的项目，排名不分先后

Yii：http://www.yiiframework.com

EasyWechat：https://www.easywechat.com

Bootstrap：http://getbootstrap.com

AdminLTE：https://adminlte.io

...

#### 版权信息

RageFrame 遵循 Apache2 开源协议发布，并提供免费使用。

本项目包含的第三方源码和二进制文件之版权信息另行标注。

版权所有Copyright © 2016-2020 by RageFrame [www.rageframe.com](http://www.rageframe.com)

All rights reserved。

### 环境搭建

目录

- Windows环境
- Windows微信开发环境
- Linux环境
- Mac环境

#### Windows环境

在Windows环境下，建议开发者使用phpStudy环境套件，下面引用官方的介绍：
该程序包集成最新的Apache+Nginx+LightTPD+PHP+MySQL+phpMyAdmin+Zend Optimizer+Zend Loader，一次性安装，无须配置即可使用，是非常方便、好用的PHP调试环境。该程序绿色小巧简易迷你仅有32M，有专门的控制面板。总之学习PHP只需一个包。
对学习PHP的新手来说，WINDOWS下环境配置是一件很困难的事；对老手来说也是一件烦琐的事。因此无论你是新手还是老手，该程序包都是一个不错的选择。
全面适合 Win2000/XP/2003/win7/win8/win2008 操作系统 ,支持Apache、IIS、Nginx和LightTPD。
新特征：完美支持win10，支持自定义php版本
下载地址：[phpStudy](http://www.phpstudy.net/)

#### Windows微信开发环境

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

#### Linux环境

- 使用 [宝塔面板](https://www.bt.cn/)，该面板使用起来简单，功能强大。
- 使用 [LNMP](https://lnmp.org/) 一键安装包
- 使用 [Docker安装](https://github.com/jianyan74/dockerfiles)

#### Mac环境

在Mac环境下，推荐使用[Mamp](https://www.mamp.info/en/)

### 系统安装

目录

- 环境要求
- 安装
- 站点配置及说明

#### 环境要求

- PHP >= 7.2
- PHP cURL 扩展
- PHP OpenSSL 扩展
- PHP fileinfo 拓展 素材管理模块需要用到
- Mysql >= 5.7.7
- Apache 或 Nginx
- Composer (用于管理第三方扩展包)
- 安装CA证书 (windows开发环境下)

> 必须先看[环境搭建文档](#环境搭建)，安装完毕后务必配置站点和对应的[伪静态](#伪静态)还有[常见问题文档](#常见问题文档)

#### 安装

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

#### 站点配置

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


### 伪静态

目录

- Nginx
- Apache
- IIS

#### Nginx

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

#### Apache

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

#### IIS

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

### 如何提问

#### 说明环境情况

- 平台: windows/linux
- 软件与版本：nginx 1.9, PHP 5.6 ...
- 系统版本：rageframe 2.5.22

#### 你做了什么？

- 我按文档（附链接）上写了一个控制器xxx，代码如下：xxxx
- 然后我使用xxx访问
- 这其中我还做了啥
- ...

#### 出现什么错误？

- 当我试图xxx的时候，xxx报了如下错误：（截图）
- 我确认过我的目录权限是正确的，xxx是正确的（必要时带上截图）

以上是举例提问时所应该描述的情况，当别人看到详细的情况时，也便于快速定位问题所在。

### 常见问题

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

#### 出现 vendor/bower/jquery/dist 找不到的解决方案

执行以下代码

```
composer global require "fxp/composer-asset-plugin:^1.4.0"
```

#### 访问项目样式全部加载不到失调

由于是未设置站点，请配置站点到根目录的web下

#### 设置站点后会出现个别样式加载 404

找到 `web/backend/assets` 下面的所有目录文件都删除重新试一次，不行就多试几次

#### 配置成功后首页访问正常，其他页面访问报404错误

请配置对应的伪静态配置

#### 访问微信应用 出现 redirect_url 参数错误

这是由于程序使用了网页授权而公众号没有正确配置【网页授权域名】所致。此时你需要登录微信公众平台，在【开发】->【接口权限】页面找到网页授权获取用户基本信息进行配置并保存。

- 网页授权域名应该为通过 ICP 备案的有效域名，否则保存时无法通过安全监测。
- 网页授权域名即程序完成授权获得授权 code 后跳转到的页面的域名，一般情况下为你的业务域名。
- 网页授权域名配置成功后会立即生效。
- 公众号的网页授权域名只可配置一个，请合理规划你的业务，否则你会发现……授权域名不够用哈。

#### 小程序Post提交服务器无法接收到数据

$_POST 只能接收 Content-Type 为 application/x-www-form-urlencoded 和 multipart/form-data 的 POST 数据。

如果你要用 $_POST 的话，你就改一下 Content-Type 这里替换为上面的其中一个：

```
header: {
      'Content-Type': 'application/json'
 }
```

把上面的 application/json 改成 application/x-www-form-urlencoded (如果要上传文件的话就改成 multipart/form-data，但是微信小程序里的上传文件用的是另外一个 API，具体的你可以仔细看一下文档)。

#### Windows环境打开后台微信报错

注意查看[环境搭建](#环境搭建)的Windows微信环境配置

#### 如何配置权限管理的路由和菜单绑定

只要把权限路由和菜单的路由统一，就能自由控制菜单显示了，比如菜单路由 menu-sys 那么权限这边也要相对应的加上 menu-sys

#### 数据迁移出现 Specified key was too long; max key length is 767 bytes 

> 由于数据库版本问题，解决方法找到数据迁移目录下的那张表，修改编码 utf8mb4 为 utf8

#### Api应用增加了控制器方法，还是出现404

> 由于开启了路由严格验证，所有的控制器方法都需要在main里面去配置rule，且单独的方法(不是CURD)也需要单独配置

#### 商户端如何进行登录

> 请去 系统管理->应用管理 安装商户管理插件，自行创建账号密码及角色信息
>

#### 安装数据库出现 json 报错不支持

> 请安装 mysql5.7 及以上版本的数据库

#### 安装插件一直有几个数据库文件未安装成功

```
// 里面的 TinyShop 可以替换成为你对应要安装的插件名称 
php ./yii migrate --migrationPath=@addons/TinyShop/console/migrations
```

### 更新历史

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/07/29/RageFrame-文档-更新历史.html>

## 系统开发

内容 详见 <>

## 微信开发

内容 详见 <>

## 插件模块开发

### 模块介绍及目录

目录

- 模块介绍
- 启动流程
- 商户映射
- 目录结构
- 其他说明

## 模块介绍

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

模块辅助类 [AddonHelper](#AddonHelper)

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

## OAuth2

### 授权/刷新Token

目录

- authorization_code(授权码模式)
- implicit(简化模式)
- password(密码模式)
- client_credentials(客户端模式)
- refresh_token(刷新access_token)

#### authorization_code(授权码模式)

*第一步*

> 先登录获取code

请求地址(Get)

```
/open/authorize/index
```

Params 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
response_type | string| 是 | 无 | 固定填写 code |
client_id | string| 是 | 无 | 客户端标识 | 
redirect_uri | string| 否 | 无 | 回调跳转Url | 如果不填写者默认调用系统内填写的授权Url
state | string| 否 | 无 | CSRF令牌 | CSRF令牌的值存储在用户会话中，以便在他们返回时进行验证

返回

```

```

*第二步*

> 获取授权令牌

请求地址(Post)

```
/oauth2/authorize
```

Body 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
grant_type | string| 是 | 无 | 固定填写 authorization_code |
client_id | string| 是 | 无 | 客户端标识 | 
client_secret | string| 是 | 无 | 客户端秘钥 | 
redirect_uri | string| 是 | 无 | 授权Url |
code | string| 是 | 无 | 授权回调code | 

返回

```

```

#### implicit(简化模式)

请求地址(Get)

```
/open/implicit
```

Params 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
response_type | string| 是 | 无 | 固定填写 token |
client_id | string| 是 | 无 | 客户端标识 | 
redirect_uri | string| 否 | 无 | 回调跳转Url | 如果不填写者默认调用系统内填写的授权Url
scope | string| 否 | 无 | 授权获取信息类型 |
state | string| 否 | 无 | CSRF令牌 | CSRF令牌的值存储在用户会话中，以便在他们返回时进行验证

返回

```

```

#### password(密码模式)

请求地址(Post)

```
/oauth2/password
```

Body 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
grant_type | string| 是 | 无 | 固定填写 password |
client_id | string| 是 | 无 | 客户端标识 | 
client_secret | string| 是 | 无 | 客户端秘钥 | 
username | string| 是 | 无 | 账号 | 
password | string| 是 | 无 | 密码 | 
scope | string| 否 | 无 | 授权获取信息类型 |

返回

```

```

#### client_credentials(客户端模式)

请求地址(Post)

```
/oauth2/client-credentials
```

Body 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
grant_type | string| 是 | 无 | 固定填写 client_credentials |
client_id | string| 是 | 无 | 客户端标识 | 
client_secret | string| 是 | 无 | 客户端秘钥 | 
scope | string| 否 | 无 | 授权获取信息类型 |

返回

```

```

#### refresh_token(刷新access_token)

请求地址(Post)

```
/oauth2/refresh-token
```

Body 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
grant_type | string| 是 | 无 | 固定填写 refresh_token |
refresh_token | string| 是 | 无 | 重置令牌 | 
client_id | string| 是 | 无 | 客户端标识 | 
client_secret | string| 是 | 无 | 客户端秘钥 | 
scope | string| 否 | 无 | 授权获取信息类型 |

返回

```

```

### 个人信息

目录

- 当前用户

> 注意：apache 环境会获取不到 authorization 请自行百度配置好环境

#### 当前用户

请求地址(Get)

```
/oauth2/v1/default
```

Header 参数

参数名 | 参数类型 | 必填 | 默认 | 说明 | 备注
---|---|---|---|---|---
authorization | string| 是 | 无 | Bearer + 空格 + access_token |

返回

```
{
    "code": 200,
    "message": "OK",
    "data": {
        "id": 2,
        "access_token": "5aad206263fa4be72cb3241b28be3",
        "merchant_id": 1,
        "client_id": "1111111111",
        "member_id": "",
        "expires": "2019-06-06 12:07:21",
        "scope": [],
        "grant_type": "client_credentials",
        "status": 1,
        "created_at": 1559779635,
        "updated_at": 1559790441
    }
}
```



<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

