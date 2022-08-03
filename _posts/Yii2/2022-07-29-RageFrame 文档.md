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

更新历史 详见 <>

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

## 微信开发

### 微信扩展
### 公众号绑定
### 用户信息获取
### JSSDK
### 微信支付
### 模版消息
### 微信红包
### 关键字回复
### 消息概述和响应
### Access Token

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

