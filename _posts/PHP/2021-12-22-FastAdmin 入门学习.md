---
layout: post
categories: PHP
title: FastAdmin 入门学习
meta: FastAdmin 入门学习
---
* content
{:toc}

## 正文

大致先翻一遍文档，对项目框架有个印象 <https://doc.fastadmin.net/doc>

然后看一下视频，找找感觉 <https://www.bilibili.com/video/BV1Ji4y1V7ZV>

再就是项目实战，慢慢上手。

### 开始前的准备

#### 相关文档

1.fastadmin的文档 <https://doc.fastadmin.net/docs>

2.thinkphp的文档 <https://www.kancloud.cn/manual/thinkphp5_1/353947>

3.bootstrap-table中文文档 <https://www.bootstrap-table.com.cn/doc/getting-started/introduction/>

4.bootstrap中文文档 <https://www.bootcss.com/>

5.selectPage 中文文档 <https://terryz.gitee.io/selectpage/docs.html>

#### 必读帖子

首先是必读的帖子，是fa的作者整理出的，可以说没有一句废话，所以每句都仔细看下，不然会漏掉一些知识点。

1.关于表格（bootstrap-table）的问题

一张图解析FastAdmin中的表格列表的功能 <https://ask.fastadmin.net/article/323.html>

2.所有关于弹出窗口的问题，在下面链接里找答案

一张图解析FastAdmin中的弹出窗口的功能 <https://ask.fastadmin.net/article/2527.html>

3.关于表单生成器

一张图解析FastAdmin中的FormBuilder表单生成器 <https://ask.fastadmin.net/article/5567.html>

4.官方常见问题必看

<https://doc.fastadmin.net/docs/faq.html#toc-0>

5.列表用echats渲染，瞬间高大上

使用表格的templateView实现一个图表渲染的功能 <https://ask.fastadmin.net/article/117.html>

6.如何移除图片上传后预览中的删除按钮

<https://ask.fastadmin.net/article/1204.html>

7.数据库创建说明文档

<https://doc.fastadmin.net/doc/database.html>

### 完整包安装

* 前往官网下载页面 <https://www.fastadmin.net/download.html> 下载完整包解压到你的项目目录
* 添加站点并绑定到项目中的public目录为运行目录，参考下面 nginx配置
* 访问 http://www.yoursite.com ，会自动重定向到 install.php 进行安装，填写信息中 站点名称 不能出现 admin 字样
* 为了安全，安装完成后会在public目录生成随机后台入口，请通过随机后台入口登录管理后台 （也可以不执行）

现在访问 admin 后台，无法进入，需要修改配置文件 `application/config.php` 中内容：
```
    // 默认模块名
    'default_module'         => 'admin',
    // 禁止访问模块
    'deny_module_list'       => ['common'],
```

### nginx配置

root指向项目的public目录。

`location /` 要重写：
```
    location / {
        if (!-e $request_filename){
            rewrite  ^(.*)$  /index.php?s=$1  last;  
            break;
        }
    }
```

页面刷新出问题时，是缓存的原因，把 禁用缓存 开启。

参考laragon实现的例子：
```
server {
    listen 80;
    server_name fastadmin_test1.test *.fastadmin_test1.test;
    root "G:/www/fastadmin_test1/public";
    
    index index.html index.htm index.php;
 
    location / {
        if (!-e $request_filename){
            rewrite  ^(.*)$  /index.php?s=$1  last;  
            break;
        }
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass php_upstream;		
        #fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
	
	
    charset utf-8;
	
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }
    location ~ /\.ht {
        deny all;
    }
}

# This file is auto-generated.
# If you want Laragon to respect your changes, just remove the [auto.] prefix
# If you want to use SSL, enable it at: Menu > Nginx > SSL > Enabled
```

### 架构

#### 功能模块

后台开发的每一个功能模块都是基于MVC的设计模式进行开发 。
在FastAdmin中，我们提供了一键生成CRUD的功能，这个一键生成CRUD生成的文件也就是我们标准的MVC文件。

一键生成test表的CRUD：
> php think crud -t test

或者：
```
> php think crud --table=fa_test
```

以下是一个标准的功能模块所涉及到的文件
```
├── application
│   └── admin
│       ├── controller
│       │   └── Test.php        //控制器类
│       ├── lang
│       │   ├── zh-cn
│       │   │   └── test.php    //功能语言包,按需加载
│       │   └── zh-cn.php       //后台语言包,默认加载
│       ├── model
│       │   └── Test.php        //模型类
│       ├── validate
│       │   └── Test.php        //验证器类
│       └── view
│           └── test
│               ├── index.html   //列表视图
│               ├── add.html     //添加视图
│               └── edit.html    //编辑视图
└── public
    └── assets
        └── js
            └── backend
                └── test.js      //功能模块JS文件
```

在FastAdmin中每一个功能模块至少对应一个功能模块JS文件，也就是说每一个控制器都对应一个同名的JS文件，
其次每一个控制器的方法对应JS文件中同名的方法。

### 数据库

<https://doc.fastadmin.net/doc/database.html>

这里提供的是数据库表字段规则在你创建表时使用，当按如下的规则进行字段命名、
类型设置和备注时使用 `php think crud -t 表名` 生成CRUD时会自动生成对应的控制器、模型、视图、JS等。

根据字段类型

特殊字段

以特殊字符结尾的规则

生成后，页面修改重点修改的是页面的js文件，具体操作细节看下 [一张图解析FastAdmin中的表格列表的功能](https://ask.fastadmin.net/article/323.html)

### 控制器 

权限控制

数据限制

关联查询

数据校验

视图渲染

### 前端

视图页，使用 if 条件：
```
<select  id="c-status" data-rule="required" class="form-control selectpicker" name="row[status]">
    <option value="10" {if condition="$row.status == 10"}selected{/if}>关闭</option>
    <option value="20" {if condition="$row.status == 20"}selected{/if}>正常</option>
</select>
```

### 组件





## fastadmin系列教程:小白学习之路

这是 写代码的猫叔 的一个 fastadmin系列视频教程，目录：
```
P1(0): 学习前的准备 软件 链接整理    02:56
P2(1): phpstudy 用安装包的方式去安装框架    04:48
P3(2): 整体框架的简单分析    02:54
P4(3): 数据库 字段类型    10:34
P5(4): 数据库 特殊字段    05:34
P6(5): 数据库 以特殊字符结尾的规则    07:52
P7(6): 控制器 修改add方法 解除email限制    08:56
P8(7): 控制器 登录鉴权权限管理 数据限制    07:16
P9(8): 控制器 一对一关联查询 根据用户id查询用户名 关联搜索    07:56
P10(9): 控制器 数据校验和视图渲染 弹窗提醒    15:31
P11(10): api 方法类分析解读    09:35
P12(11): api 自动注册登录    12:05
P13(12): api 注册后自动退出    04:02
P14(13): api 登录后token的使用方法和简单分析    07:20
P15(14): 前端 table表格讲解    10:21
P16(15): 前端 form表单讲解    07:29
P17(16): selectpage组件（动态下拉菜单列表）讲解    07:24
P18(17): $this->success 网址携带参数跳转 页面读取参数    08:07
P19(18): 随意引入自己写的js或外部js 非require.js    07:11
P20(19): 权限管理树形结构jsTree详细解读    13:50
```



### selectpage组件

可以看下文档中的 [动态下拉(SelectPage) ](https://doc.fastadmin.net/doc/178.html)

### 注入js文件

页面原生js中注入js文件的方法

```
function zhuruJs(url) { 
    let temp = document.createElement('script'); 
    temp.setAttribute('type', 'text/javascript'); 
    temp.src = url; 
    document.head.appendChild(temp); 
}
```

使用方法：
```
zhuruJs( location.origin+'/assets/js/xlsx.full.min.js')
```

### 权限管理树形结构jsTree详细解读

菜单规则 和 角色组 中，用到了 树形结构，使用 jsTree 实现的。

示例：
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>jsTree test</title>
  <!-- 2 包含主题模板CSS文件 -->
  <link rel="stylesheet" href="dist/themes/default/style.min.css" />
</head>
<body>
  <!-- 3 设置容器元素 -->
  <div id="jstree">
    <!-- 在本例中，树是从内联HTML填充的 -->
    <ul>
      <li>根节点 1
        <ul>
          <li id="child_node_1">子节点1</li>
          <li>子节点2</li>
        </ul>
      </li>
      <li>根节点 2</li>
    </ul>
  </div>
  <button>演示按钮</button>

  <!-- 4 包含jQuery库 -->
  <script src="dist/libs/jquery.js"></script>
  <!-- 5 包括缩小的jstree源文件 -->
  <script src="dist/jstree.min.js"></script>
  <script>
  $(function () {
    // 6 当DOM准备好时创建一个实例
    $('#jstree').jstree();
    // 7 绑定到树上触发的事件
    $('#jstree').on("changed.jstree", function (e, data) {
      console.log(data.selected);
    });
    // 8 与树交互-任何一种方式都可以
    $('button').on('click', function () {
      $('#jstree').jstree(true).select_node('child_node_1');
      $('#jstree').jstree('select_node', 'child_node_1');
      $.jstree.reference('#jstree').select_node('child_node_1');
    });
  });
  </script>
</body>
</html>
```

## 使用小结

### 图片压缩

有时用户上传的图片很大，如15M，会导致页面加载速度变慢，我们需要对用户上传的图片进行压缩。如：
```php
<?php

namespace app\admin\controller;

use app\common\exception\UploadException;
use app\common\library\Upload;
use app\common\controller\Backend;

/**
 * Class Candy
 * @package app\admin\controller
 */
class Candy extends Backend
{
    /**
     * 上传文件
     */
    public function upload()
    {
        $attachment = null;

        $file = $this->request->file('file');
        try {
            $info = $file->getInfo();
            $tmp_name = $info["tmp_name"];

            list($width, $height, $type) = getimagesize($tmp_name);
            if ($width > 750 || $height > 620) {
                $this->compress($tmp_name, $tmp_name, 750, 620, 100, $info["name"]);

                $image_info = getimagesize($tmp_name);
                $info["type"] = $image_info["mime"];
                $info["size"] = filesize($tmp_name);

                $file = $file->setUploadInfo($info);
            }

            $upload = new Upload($file);
            $attachment = $upload->upload();
        } catch (UploadException $e) {
            $this->error($e->getMessage());
        }

        $this->success(__('Uploaded successful'), '', ['url' => $attachment->url, 'fullurl' => cdnurl($attachment->url, true)]);
    }

    /**
     * @param $image_src
     * @param $image_dist
     * @param int $dist_width
     * @param int $dist_height
     * @param int $quality
     * @param string $filename
     * @return bool
     * @throws UploadException
     */
    protected function compress($image_src, $image_dist, $dist_width = 320, $dist_height = 240, $quality = 70, $filename = "")
    {
        $imagecreate_list = [
            1 => function ($path) {
                return imagecreatefromgif($path);
            },
            2 => function ($path) {
                return imagecreatefromjpeg($path);
            },
            3 => function ($path) {
                return imagecreatefrompng($path);
            },
            // 4   =>  function($path) {return imagecreatefromswf($path);},
            // 5   =>  function($path) {return imagecreatefrompsd($path);},
            6 => function ($path) {
                return imagecreatefrombmp($path);
            },
            // 7   =>  function($path) {return imagecreatefromtiff($path);},
            // 8   =>  function($path) {return imagecreatefromtiff($path);},
            9 => function ($path) {
                return imagecreatefromjpeg($path);
            },
            10 => function ($path) {
                return imagecreatefromjpeg($path);
            },
            11 => function ($path) {
                return imagecreatefromjpeg($path);
            },
            12 => function ($path) {
                return imagecreatefromjpeg($path);
            },
            // 13   =>  function($path) {return imagecreatefromswc($path);},
            // 14   =>  function($path) {return imagecreatefromiff($path);},
            15 => function ($path) {
                return imagecreatefromwbmp($path);
            },
            16 => function ($path) {
                return imagecreatefromxbm($path);
            },
            // 17   =>  function($path) {return imagecreatefromico($path);},
            18 => function ($path) {
                return imagecreatefromwebp($path);
            },
        ];

        // 后缀名
        $file_extension = null;
        if (!empty($filename) && strrpos($filename, '.') !== false) {
            $file_extension = substr($filename, strrpos($filename, '.') + 1);
        }

        try {
            list($src_width, $src_height, $stype) = getimagesize($image_src);
            if ($dist_width && ($src_width < $src_height)) {
                $dist_width = ($dist_height / $src_height) * $src_width;
            } else {
                $dist_height = ($dist_width / $src_width) * $src_height;
            }

            $image_resource = imagecreatetruecolor($dist_width, $dist_height);

            $src_image = $imagecreate_list[$stype]($image_src);

            imagecopyresampled($image_resource, $src_image, 0, 0, 0, 0, $dist_width, $dist_height, $src_width, $src_height);

            if (strtolower($file_extension) == 'jpg' || strtolower($file_extension ) == 'jpeg') {
                imagejpeg($image_resource, $image_dist, $quality);
            } else {
                imagepng($image_resource, $image_dist, intval(($quality - 10) / 10));
            }

            imagedestroy($image_resource);
            imagedestroy($src_image);

            return true;
        } catch (\Exception $e) {
            throw new UploadException($e->getMessage());

            return false;
        }
    }
}
```

前端指定上传地址：
```
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">图片:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="input-group">
            <input id="c-image" class="form-control" size="50" name="row[image_url]" type="text" value="{$row.image_url|htmlentities}">
            <div class="input-group-addon no-border no-padding">
                <span><button type="button" id="faupload-image" class="btn btn-danger faupload" data-url="candy/upload" data-input-id="c-image" data-mimetype="image/gif,image/jpeg,image/png,image/jpg,image/bmp" data-multiple="false" data-preview-id="p-image"><i class="fa fa-upload"></i> {:__('Upload')}</button></span>
            </div>
            <span class="msg-box n-right" for="c-image"></span>
        </div>
        <ul class="row list-inline faupload-preview" id="p-image"></ul>
    </div>
</div>
```

也可以翻阅下 [fastAdmin 文件上传 文档](https://doc.fastadmin.net/doc/177.html)

### 表单动态下拉列表搜索功能

如我们要在一个form表单中加入一个动态下拉列表搜索框：通过名称搜索公司。这里使用到了selectpage组件。

前端代码实现：
```
<form id="add-form" class="form-horizontal" role="form" data-toggle="validator" method="POST" action="">
    <div class="form-group">
        <label class="control-label col-xs-12 col-sm-2">企业名称:</label>
        <div class="col-xs-12 col-sm-8">
            <input id="c-enterprise_id" data-rule="required" class="form-control selectpage"
                   data-source="enterprise/enterpriseLists" data-field="name" data-primary-key="id"
                   data-pagination="false" data-multiple="false"
                   name="row[enterprise_id]" type="text" value="">
        </div>
    </div>
    <div class="form-group layer-footer">
        <label class="control-label col-xs-12 col-sm-2"></label>
        <div class="col-xs-12 col-sm-8">
            <button type="submit" class="btn btn-success btn-embossed disabled">{:__('OK')}</button>
            <button type="reset" class="btn btn-default btn-embossed">{:__('Reset')}</button>
        </div>
    </div>
</form>
```

指定了数据源`enterprise/enterpriseLists`，后端控制器代码：
```php
<?php

namespace app\admin\controller;

use app\common\model\Enterprise as EnterpriseModel;
use app\common\controller\Backend;

class Enterprise extends Backend
{
    protected $model = null;
    
    public function _initialize()
    {
        parent::_initialize();
    }

    public function enterpriseLists()
    {
        // 设置过滤方法
        $this->request->filter(['strip_tags', 'trim']);

        // 转发到Selectpage
        $this->model = new EnterpriseModel();
        return $this->selectpage();
    }
    
     public function index()
    {
        // 设置过滤方法
        $this->request->filter(['strip_tags', 'trim']);

        if (!$this->request->isAjax()) {

            return $this->view->fetch();
        } else {
            $this->model = new EnterpriseModel();
            
            //如果发送的来源是Selectpage，则转发到Selectpage
            if ($this->request->request('keyField')) {
                return $this->selectpage();
            }
            
            list($where, $sort, $order, $offset, $limit) = $this->buildparams();

            $list = $this->model->where($where)
                ->order($sort, $order)
                ->paginate($limit);

            $result = array("total" => $list->total(), "rows" => $list->items());

            return json($result);
        }
    }
}
```

`app\common\controller\Backend` 内容：
```php
<?php

namespace app\common\controller;

use app\admin\library\Auth;
use think\Config;
use think\Controller;
use think\Hook;
use think\Lang;
use think\Loader;
use think\Model;
use think\Session;
use fast\Tree;
use think\Validate;

/**
 * 后台控制器基类
 */
class Backend extends Controller
{

    /**
     * 无需登录的方法,同时也就不需要鉴权了
     * @var array
     */
    protected $noNeedLogin = [];

    /**
     * 无需鉴权的方法,但需要登录
     * @var array
     */
    protected $noNeedRight = [];

    /**
     * 布局模板
     * @var string
     */
    protected $layout = 'default';

    /**
     * 权限控制类
     * @var Auth
     */
    protected $auth = null;

    /**
     * 模型对象
     * @var \think\Model
     */
    protected $model = null;

    /**
     * 快速搜索时执行查找的字段
     */
    protected $searchFields = 'id';

    /**
     * 是否是关联查询
     */
    protected $relationSearch = false;

    /**
     * 是否开启数据限制
     * 支持auth/personal
     * 表示按权限判断/仅限个人
     * 默认为禁用,若启用请务必保证表中存在admin_id字段
     */
    protected $dataLimit = false;

    /**
     * 数据限制字段
     */
    protected $dataLimitField = 'admin_id';

    /**
     * 数据限制开启时自动填充限制字段值
     */
    protected $dataLimitFieldAutoFill = true;

    /**
     * 是否开启Validate验证
     */
    protected $modelValidate = false;

    /**
     * 是否开启模型场景验证
     */
    protected $modelSceneValidate = false;

    /**
     * Multi方法可批量修改的字段
     */
    protected $multiFields = 'status';

    /**
     * Selectpage可显示的字段
     */
    protected $selectpageFields = '*';

    /**
     * 前台提交过来,需要排除的字段数据
     */
    protected $excludeFields = "";

    /**
     * 导入文件首行类型
     * 支持comment/name
     * 表示注释或字段名
     */
    protected $importHeadType = 'comment';

    /**
     * 引入后台控制器的traits
     */
    use \app\admin\library\traits\Backend;

    public function _initialize()
    {
        $modulename = $this->request->module();
        $controllername = Loader::parseName($this->request->controller());
        $actionname = strtolower($this->request->action());

        $path = str_replace('.', '/', $controllername) . '/' . $actionname;

        // 定义是否Addtabs请求
        !defined('IS_ADDTABS') && define('IS_ADDTABS', input("addtabs") ? true : false);

        // 定义是否Dialog请求
        !defined('IS_DIALOG') && define('IS_DIALOG', input("dialog") ? true : false);

        // 定义是否AJAX请求
        !defined('IS_AJAX') && define('IS_AJAX', $this->request->isAjax());

        // 检测IP是否允许
        check_ip_allowed();

        $this->auth = Auth::instance();

        // 设置当前请求的URI
        $this->auth->setRequestUri($path);
        // 检测是否需要验证登录
        if (!$this->auth->match($this->noNeedLogin)) {
            //检测是否登录
            if (!$this->auth->isLogin()) {
                Hook::listen('admin_nologin', $this);
                $url = Session::get('referer');
                $url = $url ? $url : $this->request->url();
                if (in_array($this->request->pathinfo(), ['/', 'index/index'])) {
                    $this->redirect('index/login', [], 302, ['referer' => $url]);
                    exit;
                }
                $this->error(__('Please login first'), url('index/login', ['url' => $url]));
            }
            // 判断是否需要验证权限
            if (!$this->auth->match($this->noNeedRight)) {
                // 判断控制器和方法是否有对应权限
                if (!$this->auth->check($path)) {
                    Hook::listen('admin_nopermission', $this);
                    $this->error(__('You have no permission'), '');
                }
            }
        }

        // 非选项卡时重定向
        if (!$this->request->isPost() && !IS_AJAX && !IS_ADDTABS && !IS_DIALOG && input("ref") == 'addtabs') {
            $url = preg_replace_callback("/([\?|&]+)ref=addtabs(&?)/i", function ($matches) {
                return $matches[2] == '&' ? $matches[1] : '';
            }, $this->request->url());
            if (Config::get('url_domain_deploy')) {
                if (stripos($url, $this->request->server('SCRIPT_NAME')) === 0) {
                    $url = substr($url, strlen($this->request->server('SCRIPT_NAME')));
                }
                $url = url($url, '', false);
            }
            $this->redirect('index/index', [], 302, ['referer' => $url]);
            exit;
        }

        // 设置面包屑导航数据
        $breadcrumb = [];
        if (!IS_DIALOG && !config('fastadmin.multiplenav') && config('fastadmin.breadcrumb')) {
            $breadcrumb = $this->auth->getBreadCrumb($path);
            array_pop($breadcrumb);
        }
        $this->view->breadcrumb = $breadcrumb;

        // 如果有使用模板布局
        if ($this->layout) {
            $this->view->engine->layout('layout/' . $this->layout);
        }

        // 语言检测
        $lang = strip_tags($this->request->langset());

        $site = Config::get("site");

        $upload = \app\common\model\Config::upload();

        // 上传信息配置后
        Hook::listen("upload_config_init", $upload);

        // 配置信息
        $config = [
            'site'           => array_intersect_key($site, array_flip(['name', 'indexurl', 'cdnurl', 'version', 'timezone', 'languages'])),
            'upload'         => $upload,
            'modulename'     => $modulename,
            'controllername' => $controllername,
            'actionname'     => $actionname,
            'jsname'         => 'backend/' . str_replace('.', '/', $controllername),
            'moduleurl'      => rtrim(url("/{$modulename}", '', false), '/'),
            'language'       => $lang,
            'referer'        => Session::get("referer")
        ];
        $config = array_merge($config, Config::get("view_replace_str"));

        Config::set('upload', array_merge(Config::get('upload'), $upload));

        // 配置信息后
        Hook::listen("config_init", $config);
        //加载当前控制器语言包
        $this->loadlang($controllername);
        //渲染站点配置
        $this->assign('site', $site);
        //渲染配置信息
        $this->assign('config', $config);
        //渲染权限对象
        $this->assign('auth', $this->auth);
        //渲染管理员对象
        $this->assign('admin', Session::get('admin'));
    }

    /**
     * 加载语言文件
     * @param string $name
     */
    protected function loadlang($name)
    {
        $name = Loader::parseName($name);
        Lang::load(APP_PATH . $this->request->module() . '/lang/' . $this->request->langset() . '/' . str_replace('.', '/', $name) . '.php');
    }

    /**
     * 渲染配置信息
     * @param mixed $name  键名或数组
     * @param mixed $value 值
     */
    protected function assignconfig($name, $value = '')
    {
        $this->view->config = array_merge($this->view->config ? $this->view->config : [], is_array($name) ? $name : [$name => $value]);
    }

    /**
     * 生成查询所需要的条件,排序方式
     * @param mixed   $searchfields   快速查询的字段
     * @param boolean $relationSearch 是否关联查询
     * @return array
     */
    protected function buildparams($searchfields = null, $relationSearch = null)
    {
        $searchfields = is_null($searchfields) ? $this->searchFields : $searchfields;
        $relationSearch = is_null($relationSearch) ? $this->relationSearch : $relationSearch;
        $search = $this->request->get("search", '');
        $filter = $this->request->get("filter", '');
        $op = $this->request->get("op", '', 'trim');
        $sort = $this->request->get("sort", !empty($this->model) && $this->model->getPk() ? $this->model->getPk() : 'id');
        $order = $this->request->get("order", "DESC");
        $offset = $this->request->get("offset/d", 0);
        $limit = $this->request->get("limit/d", 999999);
        //新增自动计算页码
        $page = $limit ? intval($offset / $limit) + 1 : 1;
        if ($this->request->has("page")) {
            $page = $this->request->get("page/d", 1);
        }
        $this->request->get([config('paginate.var_page') => $page]);
        $filter = (array)json_decode($filter, true);
        $op = (array)json_decode($op, true);
        $filter = $filter ? $filter : [];
        $where = [];
        $alias = [];
        $bind = [];
        $name = '';
        $aliasName = '';
        if (!empty($this->model) && $this->relationSearch) {
            $name = $this->model->getTable();
            $alias[$name] = Loader::parseName(basename(str_replace('\\', '/', get_class($this->model))));
            $aliasName = $alias[$name] . '.';
        }
        $sortArr = explode(',', $sort);
        foreach ($sortArr as $index => & $item) {
            $item = stripos($item, ".") === false ? $aliasName . trim($item) : $item;
        }
        unset($item);
        $sort = implode(',', $sortArr);
        $adminIds = $this->getDataLimitAdminIds();
        if (is_array($adminIds)) {
            $where[] = [$aliasName . $this->dataLimitField, 'in', $adminIds];
        }
        if ($search) {
            $searcharr = is_array($searchfields) ? $searchfields : explode(',', $searchfields);
            foreach ($searcharr as $k => &$v) {
                $v = stripos($v, ".") === false ? $aliasName . $v : $v;
            }
            unset($v);
            $where[] = [implode("|", $searcharr), "LIKE", "%{$search}%"];
        }
        $index = 0;
        foreach ($filter as $k => $v) {
            if (!preg_match('/^[a-zA-Z0-9_\-\.]+$/', $k)) {
                continue;
            }
            $sym = isset($op[$k]) ? $op[$k] : '=';
            if (stripos($k, ".") === false) {
                $k = $aliasName . $k;
            }
            $v = !is_array($v) ? trim($v) : $v;
            $sym = strtoupper(isset($op[$k]) ? $op[$k] : $sym);
            //null和空字符串特殊处理
            if (!is_array($v)) {
                if (in_array(strtoupper($v), ['NULL', 'NOT NULL'])) {
                    $sym = strtoupper($v);
                }
                if (in_array($v, ['""', "''"])) {
                    $v = '';
                    $sym = '=';
                }
            }

            switch ($sym) {
                case '=':
                case '<>':
                    $where[] = [$k, $sym, (string)$v];
                    break;
                case 'LIKE':
                case 'NOT LIKE':
                case 'LIKE %...%':
                case 'NOT LIKE %...%':
                    $where[] = [$k, trim(str_replace('%...%', '', $sym)), "%{$v}%"];
                    break;
                case '>':
                case '>=':
                case '<':
                case '<=':
                    $where[] = [$k, $sym, intval($v)];
                    break;
                case 'FINDIN':
                case 'FINDINSET':
                case 'FIND_IN_SET':
                    $v = is_array($v) ? $v : explode(',', str_replace(' ', ',', $v));
                    $findArr = array_values($v);
                    foreach ($findArr as $idx => $item) {
                        $bindName = "item_" . $index . "_" . $idx;
                        $bind[$bindName] = $item;
                        $where[] = "FIND_IN_SET(:{$bindName}, `" . str_replace('.', '`.`', $k) . "`)";
                    }
                    break;
                case 'IN':
                case 'IN(...)':
                case 'NOT IN':
                case 'NOT IN(...)':
                    $where[] = [$k, str_replace('(...)', '', $sym), is_array($v) ? $v : explode(',', $v)];
                    break;
                case 'BETWEEN':
                case 'NOT BETWEEN':
                    $arr = array_slice(explode(',', $v), 0, 2);
                    if (stripos($v, ',') === false || !array_filter($arr, function($v){
                        return $v != '' && $v !== false && $v !== null;
                    })) {
                        continue 2;
                    }
                    //当出现一边为空时改变操作符
                    if ($arr[0] === '') {
                        $sym = $sym == 'BETWEEN' ? '<=' : '>';
                        $arr = $arr[1];
                    } elseif ($arr[1] === '') {
                        $sym = $sym == 'BETWEEN' ? '>=' : '<';
                        $arr = $arr[0];
                    }
                    $where[] = [$k, $sym, $arr];
                    break;
                case 'RANGE':
                case 'NOT RANGE':
                    $v = str_replace(' - ', ',', $v);
                    $arr = array_slice(explode(',', $v), 0, 2);
                    if (stripos($v, ',') === false || !array_filter($arr)) {
                        continue 2;
                    }
                    //当出现一边为空时改变操作符
                    if ($arr[0] === '') {
                        $sym = $sym == 'RANGE' ? '<=' : '>';
                        $arr = $arr[1];
                    } elseif ($arr[1] === '') {
                        $sym = $sym == 'RANGE' ? '>=' : '<';
                        $arr = $arr[0];
                    }
                    $tableArr = explode('.', $k);
                    if (count($tableArr) > 1 && $tableArr[0] != $name && !in_array($tableArr[0], $alias) && !empty($this->model)) {
                        //修复关联模型下时间无法搜索的BUG
                        $relation = Loader::parseName($tableArr[0], 1, false);
                        $alias[$this->model->$relation()->getTable()] = $tableArr[0];
                    }
                    $where[] = [$k, str_replace('RANGE', 'BETWEEN', $sym) . ' TIME', $arr];
                    break;
                case 'NULL':
                case 'IS NULL':
                case 'NOT NULL':
                case 'IS NOT NULL':
                    $where[] = [$k, strtolower(str_replace('IS ', '', $sym))];
                    break;
                default:
                    break;
            }
            $index++;
        }
        if (!empty($this->model)) {
            $this->model->alias($alias);
        }
        $model = $this->model;
        $where = function ($query) use ($where, $alias, $bind, &$model) {
            if (!empty($model)) {
                $model->alias($alias);
                $model->bind($bind);
            }
            foreach ($where as $k => $v) {
                if (is_array($v)) {
                    call_user_func_array([$query, 'where'], $v);
                } else {
                    $query->where($v);
                }
            }
        };
        return [$where, $sort, $order, $offset, $limit, $page, $alias, $bind];
    }

    /**
     * 获取数据限制的管理员ID
     * 禁用数据限制时返回的是null
     * @return mixed
     */
    protected function getDataLimitAdminIds()
    {
        if (!$this->dataLimit) {
            return null;
        }
        if ($this->auth->isSuperAdmin()) {
            return null;
        }
        $adminIds = [];
        if (in_array($this->dataLimit, ['auth', 'personal'])) {
            $adminIds = $this->dataLimit == 'auth' ? $this->auth->getChildrenAdminIds(true) : [$this->auth->id];
        }
        return $adminIds;
    }

    /**
     * Selectpage的实现方法
     *
     * 当前方法只是一个比较通用的搜索匹配,请按需重载此方法来编写自己的搜索逻辑,$where按自己的需求写即可
     * 这里示例了所有的参数，所以比较复杂，实现上自己实现只需简单的几行即可
     *
     */
    protected function selectpage()
    {
        //设置过滤方法
        $this->request->filter(['trim', 'strip_tags', 'htmlspecialchars']);

        //搜索关键词,客户端输入以空格分开,这里接收为数组
        $word = (array)$this->request->request("q_word/a");
        //当前页
        $page = $this->request->request("pageNumber");
        //分页大小
        $pagesize = $this->request->request("pageSize");
        //搜索条件
        $andor = $this->request->request("andOr", "and", "strtoupper");
        //排序方式
        $orderby = (array)$this->request->request("orderBy/a");
        //显示的字段
        $field = $this->request->request("showField");
        //主键
        $primarykey = $this->request->request("keyField");
        //主键值
        $primaryvalue = $this->request->request("keyValue");
        //搜索字段
        $searchfield = (array)$this->request->request("searchField/a");
        //自定义搜索条件
        $custom = (array)$this->request->request("custom/a");
        //是否返回树形结构
        $istree = $this->request->request("isTree", 0);
        $ishtml = $this->request->request("isHtml", 0);
        if ($istree) {
            $word = [];
            $pagesize = 999999;
        }
        $order = [];
        foreach ($orderby as $k => $v) {
            $order[$v[0]] = $v[1];
        }
        $field = $field ? $field : 'name';

        //如果有primaryvalue,说明当前是初始化传值
        if ($primaryvalue !== null) {
            $where = [$primarykey => ['in', $primaryvalue]];
            $pagesize = 999999;
        } else {
            $where = function ($query) use ($word, $andor, $field, $searchfield, $custom) {
                $logic = $andor == 'AND' ? '&' : '|';
                $searchfield = is_array($searchfield) ? implode($logic, $searchfield) : $searchfield;
                $searchfield = str_replace(',', $logic, $searchfield);
                $word = array_filter(array_unique($word));
                if (count($word) == 1) {
                    $query->where($searchfield, "like", "%" . reset($word) . "%");
                } else {
                    $query->where(function ($query) use ($word, $searchfield) {
                        foreach ($word as $index => $item) {
                            $query->whereOr(function ($query) use ($item, $searchfield) {
                                $query->where($searchfield, "like", "%{$item}%");
                            });
                        }
                    });
                }
                if ($custom && is_array($custom)) {
                    foreach ($custom as $k => $v) {
                        if (is_array($v) && 2 == count($v)) {
                            $query->where($k, trim($v[0]), $v[1]);
                        } else {
                            $query->where($k, '=', $v);
                        }
                    }
                }
            };
        }
        $adminIds = $this->getDataLimitAdminIds();
        if (is_array($adminIds)) {
            $this->model->where($this->dataLimitField, 'in', $adminIds);
        }
        $list = [];
        $total = $this->model->where($where)->count();
        if ($total > 0) {
            if (is_array($adminIds)) {
                $this->model->where($this->dataLimitField, 'in', $adminIds);
            }

            $fields = is_array($this->selectpageFields) ? $this->selectpageFields : ($this->selectpageFields && $this->selectpageFields != '*' ? explode(',', $this->selectpageFields) : []);

            //如果有primaryvalue,说明当前是初始化传值,按照选择顺序排序
            if ($primaryvalue !== null && preg_match("/^[a-z0-9_\-]+$/i", $primarykey)) {
                $primaryvalue = array_unique(is_array($primaryvalue) ? $primaryvalue : explode(',', $primaryvalue));
                //修复自定义data-primary-key为字符串内容时，给排序字段添加上引号
                $primaryvalue = array_map(function ($value) {
                    return '\'' . $value . '\'';
                }, $primaryvalue);

                $primaryvalue = implode(',', $primaryvalue);

                $this->model->orderRaw("FIELD(`{$primarykey}`, {$primaryvalue})");
            } else {
                $this->model->order($order);
            }

            $datalist = $this->model->where($where)
                ->page($page, $pagesize)
                ->select();

            foreach ($datalist as $index => $item) {
                unset($item['password'], $item['salt']);
                if ($this->selectpageFields == '*') {
                    $result = [
                        $primarykey => isset($item[$primarykey]) ? $item[$primarykey] : '',
                        $field      => isset($item[$field]) ? $item[$field] : '',
                    ];
                } else {
                    $result = array_intersect_key(($item instanceof Model ? $item->toArray() : (array)$item), array_flip($fields));
                }
                $result['pid'] = isset($item['pid']) ? $item['pid'] : (isset($item['parent_id']) ? $item['parent_id'] : 0);
                $list[] = $result;
            }
            if ($istree && !$primaryvalue) {
                $tree = Tree::instance();
                $tree->init(collection($list)->toArray(), 'pid');
                $list = $tree->getTreeList($tree->getTreeArray(0), $field);
                if (!$ishtml) {
                    foreach ($list as &$item) {
                        $item = str_replace('&nbsp;', ' ', $item);
                    }
                    unset($item);
                }
            }
        }
        //这里一定要返回有list这个字段,total是可选的,如果total<=list的数量,则会隐藏分页按钮
        return json(['list' => $list, 'total' => $total]);
    }

    /**
     * 刷新Token
     */
    protected function token()
    {
        $token = $this->request->param('__token__');

        //验证Token
        if (!Validate::make()->check(['__token__' => $token], ['__token__' => 'require|token'])) {
            $this->error(__('Token verification error'), '', ['__token__' => $this->request->token()]);
        }

        //刷新Token
        $this->request->token();
    }
}
```

这几个地址也可以参考下：
<https://www.freesion.com/article/87751253716/>

<https://blog.csdn.net/qq_36129701/article/details/108364882>

<https://blog.csdn.net/weixin_46043704/article/details/110079225>

[Bootstrap table教程](https://zhuanlan.zhihu.com/p/46609913)

### 列表动态下拉搜索

有两种方法。

**方法一**

在js的`table.bootstrapTable`的columns中直接写：
```
{field: 'enterprise_id', title: "企业名称", operate: '=', addclass:'selectpage', extend:'data-source="enterprise/enterpriseLists" data-field="name" data-primaryKey="id"'},
```

注意：这里的 `enterprise_id` 需要在控制器中替换成 企业名称，页面就不会显示 企业ID 了。搜索时是指定用这个名称搜索的，改不了。

**方法二**

找到对应文件的js文件，修改如下
```
{field: 'enterprise_id', title: "企业名称", operate: '='},
```

然后在js文件中添加如下代码，必须添加在`var table = $("#table");`之后：
```
table.on('post-common-search.bs.table', function (event, table) {
    var form = $("form", table.$commonsearch);
    $("input[name='enterprise_id']", form).addClass("selectpage").data("source", "enterprise/enterpriseLists").data("primaryKey", "id").data("field", "name").data("orderBy", "id desc");
    Form.events.cxselect(form);
    Form.events.selectpage(form);
});
```

`primaryKey` 是 列表搜索中渲染的键值，也就是列表搜索时提交的 enterprise_id 值；field 是 selectPage 中搜索的字段名。

这块可以参考下： 

<https://blog.csdn.net/qq_36303853/article/details/123558843>

<https://blog.csdn.net/weixin_46043704/article/details/110079225>

### 搜索条件定制

开发中碰到一个需求，需要查 已结单 的订单，包含 待评价、已评价 的订单，怎么实现呢？

js 中 可以把 已结单 定义为 100，如：
```
{field: 'status', title: "状态", searchList: {"10":"待付款","15":"已取消","17":"已超时","20":"待服务","30":"待评价","40":"已评价","50":"已退款","100":"已结单"}, formatter: Table.api.formatter.status},
```

但控制器中怎么实现呢，现在用的是：
```
list($where, $sort, $order, $offset, $limit) = $this->buildparams();

$list = $this->model
    ->where($where)
    ->order($sort, $order)
    ->paginate($limit);

$total = $list->total();
$rows = $list->items();
```

$where 是一个 Closure 闭包，不好处理。

难道要重新写查询类，查来查去最后找到一个解决方案，在 buildparams() 前重新定义request。

如：
```
$filter = json_decode($this->request->get('filter'), true);
$op = json_decode($this->request->get('op'), true);

if (isset($filter['status']) && isset($op['status']) && $filter['status'] == 100 && $op['status'] == '=') {
    $filter['status'] = [30, 40];
    $op['status'] = 'IN';
}

$this->request->get(['filter' => json_encode($filter)]);
$this->request->get(['op' => json_encode($op)]);

list($where, $sort, $order, $offset, $limit) = $this->buildparams();

$list = $this->model->with(["enterprise", "address"])
    ->where($where)
    ->order($sort, $order)
    ->paginate($limit);

$total = $list->total();
$rows = $list->items();
```

原来以为 `$this->request->get()` 只能获取参数值，现在发现居然可以定义值，神奇：
```
/**
 * 设置获取GET参数
 * @access public
 * @param string|array $name    变量名
 * @param mixed        $default 默认值
 * @param string|array $filter  过滤方法
 * @return mixed
 */
public function get($name = '', $default = null, $filter = '')
{
    if (empty($this->get)) {
        $this->get = $_GET;
    }
    if (is_array($name)) {
        $this->param      = [];
        $this->mergeParam = false;
        return $this->get = array_merge($this->get, $name);
    }
    return $this->input($this->get, $name, $default, $filter);
}

/**
 * 获取变量 支持过滤和默认值
 * @param array        $data    数据源
 * @param string|false $name    字段名
 * @param mixed        $default 默认值
 * @param string|array $filter  过滤函数
 * @return mixed
 */
public function input($data = [], $name = '', $default = null, $filter = '')
{
    if (false === $name) {
        // 获取原始数据
        return $data;
    }
    $name = (string) $name;
    if ('' != $name) {
        // 解析name
        if (strpos($name, '/')) {
            list($name, $type) = explode('/', $name);
        } else {
            $type = 's';
        }
        // 按.拆分成多维数组进行判断
        foreach (explode('.', $name) as $val) {
            if (isset($data[$val])) {
                $data = $data[$val];
            } else {
                // 无输入数据，返回默认值
                return $default;
            }
        }
        if (is_object($data)) {
            return $data;
        }
    }

    // 解析过滤器
    $filter = $this->getFilter($filter, $default);

    if (is_array($data)) {
        array_walk_recursive($data, [$this, 'filterValue'], $filter);
        reset($data);
    } else {
        $this->filterValue($data, $name, $filter);
    }

    if (isset($type) && $data !== $default) {
        // 强制类型转换
        $this->typeCast($data, $type);
    }
    return $data;
}
```

看一下 `$this->buildparams()` 方法的实现：
```
/**
 * 生成查询所需要的条件,排序方式
 * @param mixed   $searchfields   快速查询的字段
 * @param boolean $relationSearch 是否关联查询
 * @return array
 */
protected function buildparams($searchfields = null, $relationSearch = null)
{
    $searchfields = is_null($searchfields) ? $this->searchFields : $searchfields;
    $relationSearch = is_null($relationSearch) ? $this->relationSearch : $relationSearch;
    $search = $this->request->get("search", '');
    $filter = $this->request->get("filter", '');
    $op = $this->request->get("op", '', 'trim');
    $sort = $this->request->get("sort", !empty($this->model) && $this->model->getPk() ? $this->model->getPk() : 'id');
    $order = $this->request->get("order", "DESC");
    $offset = $this->request->get("offset/d", 0);
    $limit = $this->request->get("limit/d", 999999);
    //新增自动计算页码
    $page = $limit ? intval($offset / $limit) + 1 : 1;
    if ($this->request->has("page")) {
        $page = $this->request->get("page/d", 1);
    }
    $this->request->get([config('paginate.var_page') => $page]);
    $filter = (array)json_decode($filter, true);
    $op = (array)json_decode($op, true);
    $filter = $filter ? $filter : [];
    $where = [];
    $alias = [];
    $bind = [];
    $name = '';
    $aliasName = '';
    if (!empty($this->model) && $this->relationSearch) {
        $name = $this->model->getTable();
        $alias[$name] = Loader::parseName(basename(str_replace('\\', '/', get_class($this->model))));
        $aliasName = $alias[$name] . '.';
    }
    $sortArr = explode(',', $sort);
    foreach ($sortArr as $index => & $item) {
        $item = stripos($item, ".") === false ? $aliasName . trim($item) : $item;
    }
    unset($item);
    $sort = implode(',', $sortArr);
    $adminIds = $this->getDataLimitAdminIds();
    if (is_array($adminIds)) {
        $where[] = [$aliasName . $this->dataLimitField, 'in', $adminIds];
    }
    if ($search) {
        $searcharr = is_array($searchfields) ? $searchfields : explode(',', $searchfields);
        foreach ($searcharr as $k => &$v) {
            $v = stripos($v, ".") === false ? $aliasName . $v : $v;
        }
        unset($v);
        $where[] = [implode("|", $searcharr), "LIKE", "%{$search}%"];
    }
    $index = 0;
    foreach ($filter as $k => $v) {
        if (!preg_match('/^[a-zA-Z0-9_\-\.]+$/', $k)) {
            continue;
        }
        $sym = isset($op[$k]) ? $op[$k] : '=';
        if (stripos($k, ".") === false) {
            $k = $aliasName . $k;
        }
        $v = !is_array($v) ? trim($v) : $v;
        $sym = strtoupper(isset($op[$k]) ? $op[$k] : $sym);
        //null和空字符串特殊处理
        if (!is_array($v)) {
            if (in_array(strtoupper($v), ['NULL', 'NOT NULL'])) {
                $sym = strtoupper($v);
            }
            if (in_array($v, ['""', "''"])) {
                $v = '';
                $sym = '=';
            }
        }

        switch ($sym) {
            case '=':
            case '<>':
                $where[] = [$k, $sym, (string)$v];
                break;
            case 'LIKE':
            case 'NOT LIKE':
            case 'LIKE %...%':
            case 'NOT LIKE %...%':
                $where[] = [$k, trim(str_replace('%...%', '', $sym)), "%{$v}%"];
                break;
            case '>':
            case '>=':
            case '<':
            case '<=':
                $where[] = [$k, $sym, intval($v)];
                break;
            case 'FINDIN':
            case 'FINDINSET':
            case 'FIND_IN_SET':
                $v = is_array($v) ? $v : explode(',', str_replace(' ', ',', $v));
                $findArr = array_values($v);
                foreach ($findArr as $idx => $item) {
                    $bindName = "item_" . $index . "_" . $idx;
                    $bind[$bindName] = $item;
                    $where[] = "FIND_IN_SET(:{$bindName}, `" . str_replace('.', '`.`', $k) . "`)";
                }
                break;
            case 'IN':
            case 'IN(...)':
            case 'NOT IN':
            case 'NOT IN(...)':
                $where[] = [$k, str_replace('(...)', '', $sym), is_array($v) ? $v : explode(',', $v)];
                break;
            case 'BETWEEN':
            case 'NOT BETWEEN':
                $arr = array_slice(explode(',', $v), 0, 2);
                if (stripos($v, ',') === false || !array_filter($arr)) {
                    continue 2;
                }
                //当出现一边为空时改变操作符
                if ($arr[0] === '') {
                    $sym = $sym == 'BETWEEN' ? '<=' : '>';
                    $arr = $arr[1];
                } elseif ($arr[1] === '') {
                    $sym = $sym == 'BETWEEN' ? '>=' : '<';
                    $arr = $arr[0];
                }
                $where[] = [$k, $sym, $arr];
                break;
            case 'RANGE':
            case 'NOT RANGE':
                $v = str_replace(' - ', ',', $v);
                $arr = array_slice(explode(',', $v), 0, 2);
                if (stripos($v, ',') === false || !array_filter($arr)) {
                    continue 2;
                }
                //当出现一边为空时改变操作符
                if ($arr[0] === '') {
                    $sym = $sym == 'RANGE' ? '<=' : '>';
                    $arr = $arr[1];
                } elseif ($arr[1] === '') {
                    $sym = $sym == 'RANGE' ? '>=' : '<';
                    $arr = $arr[0];
                }
                $tableArr = explode('.', $k);
                if (count($tableArr) > 1 && $tableArr[0] != $name && !in_array($tableArr[0], $alias) && !empty($this->model)) {
                    //修复关联模型下时间无法搜索的BUG
                    $relation = Loader::parseName($tableArr[0], 1, false);
                    $alias[$this->model->$relation()->getTable()] = $tableArr[0];
                }
                $where[] = [$k, str_replace('RANGE', 'BETWEEN', $sym) . ' TIME', $arr];
                break;
            case 'NULL':
            case 'IS NULL':
            case 'NOT NULL':
            case 'IS NOT NULL':
                $where[] = [$k, strtolower(str_replace('IS ', '', $sym))];
                break;
            default:
                break;
        }
        $index++;
    }
    if (!empty($this->model)) {
        $this->model->alias($alias);
    }
    $model = $this->model;
    $where = function ($query) use ($where, $alias, $bind, &$model) {
        if (!empty($model)) {
            $model->alias($alias);
            $model->bind($bind);
        }
        foreach ($where as $k => $v) {
            if (is_array($v)) {
                call_user_func_array([$query, 'where'], $v);
            } else {
                $query->where($v);
            }
        }
    };
    return [$where, $sort, $order, $offset, $limit, $page, $alias, $bind];
}
```

### 表格长度限制，鼠标移动显示

有种需求场景，如 表格中简介部分不能太长，鼠标移动上去后页面弹出具体详情，示例代码：
```
{field: 'profile', title: "简介", operate: false,
    formatter: function(value){
        var span = document.createElement("span");
        span.setAttribute("title", value);
        span.innerHTML = value;
        return span.outerHTML;
    },
    cellStyle: function(value, row, index) {
        return {
            css: {
                "white-space": "nowrap",
                "text-overflow": "ellipsis",
                "overflow": "hidden",
                "min-width": "100px",
                "max-width": "200px"
            }
        }
    },
},
```

### 在线支付实现

FastAdmin 框架中在线支付可以使用 微信支付宝整合epay插件 <https://www.fastadmin.net/store/epay.html>

支持在线安装，和离线安装。

### 支付退款实现

上面 微信支付宝整合epay插件 实现了支付，不过缺少退款这块，需要自己在 `addons/epayyq/library/Service.php` 文件中补充：
```
/**
 * 提交退款
 * @param null $amount  订单金额
 * @param $refund_money   退款金额
 * @param $orderid   订单号
 * @param $refund_sn    退款订单号
 * @param $type    支付类型，alipay,wechat
 * @param null $remark   退款原因
 * @param null $notifyurl   通知url
 * @param null $returnurl   跳转url
 * @param string $method    支付方法
 * @return null|\Yansongda\Supports\Collection|static
 */
public static function submitRefund($amount = null, $refund_money, $orderid, $refund_sn, $type, $remark = null, $notifyurl = null, $returnurl = null, $method = 'app')
{
    if (!is_array($amount)) {
        $params = [
            'amount' => $amount,
            'type' => $type,
            'notifyurl' => $notifyurl,
            'returnurl' => $returnurl,
            'method' => $method
        ];
    } else {
        $params = $amount;
    }

    $type = isset($params['type']) && in_array($params['type'], ['alipay', 'wechat']) ? $params['type'] : 'wechat';
    $request = request();
    $notifyurl = isset($params['notifyurl']) ? $params['notifyurl'] : $request->root(true) . '/addons/epay/index/' . $type . 'notify';
    $returnurl = isset($params['returnurl']) ? $params['returnurl'] : $request->root(true) . '/addons/epay/index/' . $type . 'return/out_trade_no/' . $orderid;

    $config = Service::getConfig($type);
    $config['notify_url'] = $notifyurl;
    $config['return_url'] = $returnurl;
    $result = null;

    $order_data = [
        'out_trade_no' => $orderid
    ];

    if ($type == 'wechat') {
        $pay = Pay::wechat($config);
        $total_fee = $amount * 100;
        $refund_fee = $refund_money * 100;
        $order_data = array_merge($order_data, [
            'out_refund_no' => $refund_sn,
            'total_fee' => $total_fee,
            'refund_fee' => $refund_fee,
            'refund_desc' => $remark,
            'type' => $method,
        ]);
    } else {
        $pay = Pay::alipay($config);
        $order_data = array_merge($order_data, [
            'out_request_no' => $refund_sn,
            'refund_amount' => $refund_money,
        ]);
    }

    $result = $pay->refund($order_data);

    if ($result instanceof \Symfony\Component\HttpFoundation\RedirectResponse) {
        $result = RedirectResponse::create($result->getTargetUrl());
    } elseif ($result instanceof \Symfony\Component\HttpFoundation\Response) {
        $result = Response::create($result->getContent());
    } elseif ($result instanceof \Yansongda\Supports\Collection) {
        $result = Collection::make($result->all());
    }

    return $result;
}
```
### 多个支付主体实现

开发中碰到一个问题，在同一个项目中，会有多个微信小程序主体，会用这多个小程序进行支付。

跨小程序支付会报错：`ERROR_GATEWAY: ERROR_BUSINESS: Wechat Business Error: PARAM_ERROR - appid和openid不匹配`

也就是说 一个用户在多个小程序中会有各自的 openid , 付款时 用户的 openid 要与 小程序 appid 匹配才可以支付。

我们安装的 微信支付宝整合epay插件 只能配置一个微信小程序，怎么才能使用第二个小程序支付呢。

**重新打包一个插件** 

我们用 微信支付宝整合epay插件 为基础，重新打包一个支付插件，然后离线安装，配置后就可以使用了。

说下过程：

1、在FastAdmin插件市场搜索 [微信支付宝整合插件](https://www.fastadmin.net/store/epay.html)，下载插件包。

2、解压后，编辑项目

* 把 `library` 文件夹中的 `Yansongda`文件夹 改名为 `YansongdaYq`，全局替换 `Yansongda` 为 `YansongdaYq`。

* 把 `Epay.php`  改名为 `Epayyq.php`，在把 `application/admin/controller/` 文件夹中的 `Epay.php`文件 改名为 `Epayyq.php`，
全局替换 `Epay` 为 `Epayyq`。

* 全局替换 `epay` 为 `epayyq`。
这里误改了两个地方 `prepayyq_id`、`prepayyqid`，我们再替换回来`prepay_id`、`prepayid`。

* 在这个插件项目根目录下，选中这些文件，打包为 `epayyq.zip` 压缩包，移动到指定位置。

* 安装时会进行是否允许未知来源的插件压缩包的配置判断，修改 `application/config.php` 中 `unknownsources` 为 `true`。

* 安装时会进行压缩包验证、版本依赖判断，绕过判断，直接安装，需要注释代码。
把项目 `vendor/karsonzhang/fastadmin-addons/src/addons/Service.php` 文件中 `Service::valid($params);` 这行代码注释掉。

* 打开项目的插件管理页面，点击 离线安装，选中上面的压缩包，等待安装完成。

* 进行插件参数配置。

接下来对这个插件进行使用就可以了。

### 新建一个后台模块

有时会碰到需求，需要在项目中新增一个模块，如给企业使用，怎么实现呢，下面记录一下。

1、修改项目根目录下的 `build.php` 文件内容，增加 enterprise 模块定义：
```php
<?php

return [
    // 生成应用公共文件
    '__file__' => ['hello.php', 'test.php'],
    // 定义demo模块的自动生成 （按照实际定义的文件名生成）
    'demo' => [
        '__file__'   => ['common.php'],
        '__dir__'    => ['behavior', 'controller', 'model', 'view'],
        'controller' => ['Index', 'Test', 'UserType'],
        'model'      => ['User', 'UserType'],
        'view'       => ['index/index'],
    ],
    // 其他更多的模块定义
    'enterprise' => [
        '__file__'   => ['common.php'],
        '__dir__'    => ['controller', 'model', 'view'],
        'controller' => ['Index'],
        'model'      => [],
        'view'       => ['index/index'],
    ],
];
```

2、在项目根目录下执行命令：
```
> php think build --config build.php
```

执行后比较简单，只生成了上面写的那几个文件，并没有对其他文件进行修改。

然后浏览器访问 `域名/enterprise` 就可以看到模块页面内容了。

3、类文件的复制，注意把文件名修改为 `Enterprise`、命名空间名称 修改为 `Enterprise`。

把 `application/common/controller` 文件夹下的 `Backend.php` 复制一份为 `Enterprise.php` ，
修改该文件内容 `use \app\admin\library\traits\Enterprise;` 为 `use \app\enterprise\library\traits\Enterprise;`、
修改 `'jsname'         => 'admin/' . str_replace('.', '/', $controllername),` 
为 `'jsname'         => 'enterprise/' . str_replace('.', '/', $controllername),` ，这里修改的是js自动加载的文件夹路径。

把 `application/admin` 文件夹下的 `library` 文件夹 复制到 `application/enterprise` 文件夹下；

把 `application/admin/controller` 文件夹下的 `Ajax.php`、`Dashboard.php` 、`Index.php` 文件 也复制过去。

4、函数文件的复制。

把 `application/admin` 文件夹下的 `common.php` 文件 复制到 `application/enterprise` 文件夹下；

5、视图文件的复制。

把 `application/admin/view` 文件夹下的 `common`、`dashboard`、`index`、`layout` 文件夹 也复制到 `application/enterprise/view` 文件夹下去。

修改 `application/enterprise/view/common/script.html` 的 `require-backend` 为 `require-enterprise`，把引用的js修改掉。

6、Js文件的复制。

把 `public/assets/js` 文件夹下的 `backend.js`、`backend-init.js`、`require-backend.js`、`require-backend.min.js` 复制为 
`enterprise.js`、`enterprise-init.js`、`require-enterprise.js`、`require-enterprise.min.js` ，
把这几个文件中的 `backend` 修改为 `enterprise`、`Backend` 修改为 `Enterprise`。

在 `public/assets/js` 文件夹下新建 `enterprise` 文件夹，以后控制器相关的js都放在该文件夹下。

7、Css文件的复制。

css 部分可以和 admin 模块的共用，可过也可以复制转移下。

把 `public/assets/css` 文件夹下的 `backend.css` 复制为 `enterprise.css` ，视图文件中改下引入。

8、语言包文件的复制。

把 `application/admin` 文件夹下的 `lang` 文件夹 复制到 `application/enterprise` 文件夹下；

### 独立表单页

开发中碰到一个情况，企业要开通自己的店铺，并在该页面更新信息，如何实现。

折腾了半天，发现这页的Js并没有列表页的多和复杂。

看下html页文件代码：
```
<div class="box box-success">
    <div class="panel-heading">店铺资料</div>
    <div class="panel-body">
        <form id="update-form" class="form-horizontal" role="form" data-toggle="validator" method="POST" action="">
            <input type="hidden" name="row[id]" value="{$row.id ?? 0}" />

            <div class="form-group">
                <label class="control-label col-xs-8 col-sm-2">店铺Logo:</label>
                <div class="col-xs-6">
                    <div class="input-group">
                        <input id="c-logo" class="form-control" size="50" name="row[logo]" type="text" {if isset($row.logo)} value="{$row.logo|htmlentities}" {else} value="" {/if}>
                        <div class="input-group-addon no-border no-padding">
                            <span><button type="button" id="faupload-logo" class="btn btn-danger faupload" data-url="candy/upload" data-input-id="c-logo" data-mimetype="image/gif,image/jpeg,image/png,image/jpg,image/bmp" data-multiple="false" data-preview-id="p-logo"><i class="fa fa-upload"></i> {:__('Upload')}</button></span>
                        </div>
                        <span class="msg-box n-right" for="c-logo"></span>
                    </div>
                    <ul class="row list-inline faupload-preview" id="p-logo"></ul>
                </div>
                <label class="control-label col-xs-4 col-sm-2" style=" text-align: left;">{:__('建议尺寸240*240')}</label>
            </div>

            <div class="form-group">
                <label class="control-label col-xs-12 col-sm-2">店铺名称:</label>
                <div class="col-xs-12 col-sm-8">
                    <input id="shop_name" class="form-control" name="row[name]" type="text" {if isset($row.name)} value="{$row.name|htmlentities}" {else} value="" {/if}>
                </div>
            </div>

            <div class="form-group">
                <label class="control-label col-xs-12 col-sm-2">店铺简介:</label>
                <div class="col-xs-12 col-sm-8">
                    <textarea id="profile" class="form-control" rows="5" name="row[profile]" cols="50">{if isset($row.profile)}{$row.profile|htmlentities}{/if}</textarea>
                </div>
            </div>

            {if isset($row.id)}
            <div class="form-group">
                <label class="control-label col-xs-12 col-sm-2">审核状态:</label>
                <div class="col-xs-12 col-sm-8">
                    {:Form::selectpicker('row[audit_status]', ['10' => '待审核', '20' => '审核通过'], $row.audit_status, ['data-rule' => 'required', 'disabled' => 'disabled'])}
                </div>
            </div>
            <div class="form-group">
                <label class="control-label col-xs-12 col-sm-2">审核内容:</label>
                <div class="col-xs-12 col-sm-8">{$row.audit_content|htmlentities}</div>
            </div>
            {/if}

            <div class="form-group">
                <label class="control-label col-xs-12 col-sm-2"></label>
                <div class="col-xs-12 col-sm-8">
                    {if isset($row.audit_status)}
                    <button type="submit" class="btn btn-success">更新</button>
                    {else}
                    <button type="submit" class="btn btn-success">开通</button>
                    {/if}
                    <button type="reset" class="btn btn-default">{:__('Reset')}</button>
                </div>
            </div>

        </form>
    </div>
</div>
```

看下js文件代码：
```
define(['jquery', 'bootstrap', 'backend', 'table', 'form', 'upload'], function ($, undefined, Backend, Table, Form, Upload) {
    var Controller = {
        index: function () {
            // 初始化表格参数配置
            Table.api.init({
                extend: {
                    "index_url": "shop/index",
                    "add_url": "",
                    "edit_url": "",
                    "del_url": "",
                    "multi_url": "",
                }
            });

            // 文件上传按钮绑定
            Upload.api.custom.customUploadSuccess = function(data, ret) {
                data.url = data.fullurl;
            };
            Controller.api.bindevent();

        },

        api: {
            bindevent: function () {
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };
    return Controller;
});
```

表单组件，可以参考 <https://blog.csdn.net/wangshifan116/article/details/114639557>









<br/><br/><br/><br/><br/>
## 参考资料

FastAdmin 官方文档 <https://doc.fastadmin.net/doc>

写代码的猫叔：fastadmin系列教程:小白学习之路 <https://www.bilibili.com/video/BV1Ji4y1V7ZV>

写代码的猫叔 fastadmin笔记 <https://www.yuque.com/siri/fastadmin>

FastAdmin开发系列之视频教程 <https://www.fastadmin.net/video.html>

FastAdmin开发者文档 <https://doc.fastadmin.net/developer/55.html>

一张图解析FastAdmin中的表格列表的功能 <https://ask.fastadmin.net/article/323.html>

fastadmin后台前端页面原生js注入js文件的方法 <https://blog.csdn.net/qq978165754/article/details/114726297>

jsTree 中文网 <http://www.jstree.com.cn/>

获取$where 修改$where条件 在 buildparams 之前 <https://zhuanlan.zhihu.com/p/501150222>

fast-admin 开发教程 视频 <https://v-wb.youku.com/v_show/id_XMzk3MjA4NjE0MA==.html>

FastAdmin 插件-手动打包-离线安装 <https://www.jianshu.com/p/8bde6fac9fa7>

Fastadmin插件开发流程简要记录 <https://blog.csdn.net/muyibu/article/details/115915254>

微信支付错误: PARAM_ERROR 原因:appid和openid不匹配 <https://developers.weixin.qq.com/community/pay/doc/00040e24064440aaba1acaa8156800>

fastadmin踩坑日记-1.初识插件开发 <https://blog.csdn.net/xzy565143480/article/details/104173888>

fastadmin 手动建一个新模块流程 <https://www.likecs.com/show-876138.html>

thinkPHP5 添加新模块 <https://www.cnblogs.com/flyphper/p/8693120.html>

ThinkPHP5使用build类实现模块自动化搭建 <https://blog.csdn.net/sqz635262108/article/details/78571122>







