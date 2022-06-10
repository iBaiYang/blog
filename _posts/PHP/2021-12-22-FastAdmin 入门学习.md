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



## 参考资料

FastAdmin 官方文档 <https://doc.fastadmin.net/doc>

写代码的猫叔：fastadmin系列教程:小白学习之路 <https://www.bilibili.com/video/BV1Ji4y1V7ZV>

写代码的猫叔 fastadmin笔记 <https://www.yuque.com/siri/fastadmin>

FastAdmin开发系列之视频教程 <https://www.fastadmin.net/video.html>

FastAdmin开发者文档 <https://doc.fastadmin.net/developer/55.html>

一张图解析FastAdmin中的表格列表的功能 <https://ask.fastadmin.net/article/323.html>

fastadmin后台前端页面原生js注入js文件的方法 <https://blog.csdn.net/qq978165754/article/details/114726297>

jsTree 中文网 <http://www.jstree.com.cn/>

