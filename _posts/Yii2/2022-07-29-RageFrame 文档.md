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

Linux 环境下如果是文件缓存去 `backend/runtime`  目录执行一下 `chmod -R 777 cache`，不执行可能会造成修改了网站设置缓存不生效的情况

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

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/07/30/RageFrame-文档-系统开发.html>

## 微信开发

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/07/31/RageFrame-文档-微信开发.html>

## 插件模块开发

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/08/01/RageFrame-文档-插件模块开发.html>

## Api

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/08/03/RageFrame-文档-Api.html>

## OAuth2

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/08/04/RageFrame-文档-OAuth2.html>

## 助手类

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/08/05/RageFrame-文档-助手类.html>

## 附录

内容 详见 <https://ibaiyang.github.io/blog/yii2/2022/08/06/RageFrame-文档-附录.html>


<br/><br/><br/><br/><br/>
## 参考资料

RageFrame 官网 <http://www.rageframe.com/>

官方在线文档 <https://github.com/jianyan74/rageframe2/blob/master/docs/guide-zh-CN/README.md>

微商城插件 <https://github.com/jianyan74/TinyShop>

