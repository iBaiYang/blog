---
layout: post
categories: Yii2
title: RageFrame 文档-更新历史
meta: RageFrame 文档-更新历史
---
* content
{:toc}

## 介绍安装

### 更新历史

注意：

> 各个小版本升级例如 2.1.x 升级到 2.2.x 以上不能完美升级最好重新安装
git 

#### v2.6.57
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

#### v2.6.43
updated 2020.7.2

- 增加: 递归多级联动选择器
- 增加: api 可以不进行格式化处理返回
- 增加: md5 校验文件驱动判断
- 修复: 插件的菜单附加参数时候在顶部菜单不显示
- 修复: 在插件下 OSS 直传找不到回调地址
- 修复: 页面分页中选择页面和前往某页会丢失 url 参数

#### v2.6.37
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

#### v2.6.23
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
 
#### v2.6.10
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

#### v2.5.36
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

#### v2.5.22
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

#### v2.4.33
updated 2019.11.29

- 优化: 解析网站设置解析说明的 html
- 优化: 微信图文素材上传不记录到表 [@yoyo0926](https://github.com/yoyo0926)
- 优化: 兼容 php 7.4
- 修复: 商家管理添加用户未写入商户 id
- 修复: 商家编辑插件分类时写入类型错误
- 修复: 页码数量 20 导致和系统默认数量冲突被移除
- 修复: 未判断父级行为返回导致跨域验证失败

#### v2.4.27
updated 2019.11.25

- 修复: 商户禁用未让用户登录失效
- 修复: 设置 Redis 缓存时候设置省市区过期时间过长导致错误 
- 修复: 商家权限添加无法添加二级以下的权限
- 修复: 按钮在个别情况下会导致权限验证不能通过
- 修复: 微信图片资源路径错误
- 升级: Yii2版本到2.0.30

#### v2.4.21
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

#### v2.3.137
updated 2019.10.17

- 增加: Markdown Widget
- 增加: PhpStrom IDE 代码提示辅助类
- 增加: 消息通知可全部设为已读
- 优化: SearchModel 组件关联查询字段
- 优化: rageframe.js 兼容 IE 11
- 修复: Excel 导出文件打开有时候提示错误
- 修复: OAuth2 授权码模式不传递 redirect_uri 会导致报错

#### v2.3.130
updated 2019.9.25

- 优化: 公用微信 js 支付，调用 easywechat 的 js 支付视图进行兼容 [@yiirise](https://github.com/yiirise)
- 修复: 树型结构组件导致删除可能会移除其他记录
- 修复: 金额日志变动数据迁移字段缺少

#### v2.3.127
updated 2019.9.20

- 修复: ElaticSearch 案例连接配置问题导致连接失败
- 修复: 菜单分配顶级菜单报错
- 升级: Yii2版本到2.0.27

#### v2.3.124
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

#### v2.3.94
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

#### v2.3.65
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

#### v2.3.39
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

#### v2.2.52
updated 2019.6.2

- 修复: 菜单下拉偶尔会出现样式失调的情况
- 修复: 模拟框打开时间/日期控件的时候关闭异常

#### v2.2.50
updated 2019.5.28

- 修复: Html 辅助类变量名称错误
- 修复: Auth 辅助类逻辑错误
- 修复: 某些情况下初始化密码会出现重复显示字符串
- 修复: 插件权限判断未定义在插件内导致判断错误

#### v2.2.46
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

#### v2.2.35
updated 2019.3.27

- 修复: gii 生成 curd 的首页报错
- 修复: 文档服务层单词错误
- 修复: api 的 token 命名错误

#### v2.2.32
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

#### v2.2.21
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

#### v2.2.2
updated 2019.1.17

- 修复: 文件选择时 psd 文件显示错误
- 修复: 导航标签关闭时会出现剩余标签显示不全的情况
- 替换: 当前的后台主题为 AminLTE 2.4.5

#### v2.1.41
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

#### v2.1.18
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

#### v2.0.96
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

#### v2.0.85
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

#### v2.0.75
updated 2018.11.22

- 增加: 多图上传/多文件上传支持拖动排序
- 增加: 插件模块的钩子(hook)支持多个 hook 方法
- 增加: 示例模块的 Grid 案例
- 优化: 代码的规范和命名规则
- 优化: api 日志记录改成全局日志，支持 wechat/backend/frontend 报错记录
- 修复: 插件模块下自带的 ajax 更新找不到 id

#### v2.0.69
updated 2018.11.16

- 优化: api 日志记录，增加记录级别更好的进行区分报错
- 优化: 插件模块的资源目录，更加友好的区分前后台微信的资源，且和 Yii2 资源结构保持一致
- 修复: 修改菜单默认显示时候，未干掉其他正在显示的菜单
- 修复: 开启上传前缀时，百度编辑器选择在线管理的附件/图片前缀为空
- 修复: 角色授权显示时候，会把顶级分类下面的所有子分类都选中

#### v2.0.64
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

#### v2.0.50
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

#### v2.0.38
updated 2018.10.17

- 增加: JS方法提供内页打开新标签页面
- 优化: 自动回复的图片回复选择方式
- 优化: 自动回复的图文回复选择方式
- 修复: 第三方授权的字段名错误导致用户数据插入失败

#### v2.0.34
updated 2018.10.15

- 增加: 内容管理插件的前台显示 Demo
- 增加: 会员积分/余额变动日志接口
- 增加: 插件模块 `购物节` 签到抽奖微信页面的 Demo
- 优化: 插件模块的 Hook 渲染报错机制
- 修复: 小程序解析数据变量重复导致获取不到 session 信息
- 修复: 第三方授权的字段名错误导致验证失败

#### v2.0.28
updated 2018.10.10

- 优化: Api 解耦速率控制，可自由选择是否被速率控制

#### v2.0.27
updated 2018.10.10

- 优化: 缓存清理各个应用的缓存文件夹读写判断
- 优化: 后台总管理员安全权限，初始化安装时随机总管理员的账号密码
- 修复: Linux 环境下创建插件报找不到模板文件
- 修复: Excel 导入找不到最后一列数据

#### v2.0.23
updated 2018.9.30

- 增加: 图片上传可压缩图片质量，从 `common/config/params` 里面配置
- 增加: 图片水印，从 `common/config/params` 里面配置
- 增加: 示例管理模块的消息队列、ElasticSearch、默认搜索 的 Demo
- 优化: 子权限账号后台权限名称显示
- 修复: 角色权限授权采用了 jstree 后，折叠顶级节点的时候无法获取到子节点

#### v2.0.18
updated 2018.9.26

- 增加: 示例管理模块 `MongoDB` 的 Curd，如需测试请自行安装 php 的 mongodb 扩展和 MongoDb 环境
- 优化: 多图上传的显示样式及功能
- 优化: 微信/小程序支付 Demo ，调整公用的回调控制器
- 修复: 由于上传视频和语音开启了全域名返回导致的上传到微信服务器错误
- 修复: 由于 api 的 controllerMap 位置不对导致的报错

#### v2.0.13
updated 2018.9.21

- 增加: 扩展模块 `文章管理` 提供二开示例及使用
- 修复: 由于默认微信参数没有导致的自动回复报错
- 修复: 由于上传图片开启了全域名返回导致的上传到微信服务器错误
- 修复: 由于上传 S 问题，导致了文件上传后删除时候不显示上传按钮
- 修复: 创建扩展模块时候资源目录为空引起的报错

#### v2.0.8
updated 2018.9.18

- 增加: Url辅助类，用于生成前台、api、微信地址
- 修复: 权限验证时候路由规则的判断错误
- 修复: 控制台中执行 php yii 的报错
- 优化: 图片上传缩略图配置，可设置多个缩略图
- 优化: 角色权限授权，采用了 jstree 插件表现页面
- 优化: 增强安全机制，安装流程中增加初始化随机生成10位密码

#### v2.0.2
updated 2018.9.10

- 2.0 全新上线

#### v2.0.1
updated 2018.6.11

- 增加: IP地址转地区组件
- 增加: 大部分基础功能

####  v2.0.0
updated 2018.5.20
 
 - 初始化: 2.0 基础框架


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

