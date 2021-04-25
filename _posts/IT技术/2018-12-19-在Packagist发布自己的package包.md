---
layout: post
categories: IT技术
title: 在Packagist发布自己的package包
meta: 在Packagist发布自己的package包。
---
* content
{:toc}

### 正文

我们在自己的应用开发中，总是会用到一些现成的dependency依赖，我们在应用根目录下的composer.json中有：

```
{
    "name": "peachpear/yii2-app-advanced",
    "description": "Yii 2 Advanced Project Template",
    "keywords": ["yii2", "framework", "advanced", "project template"],
    "homepage": "http://www.yiiframework.com/",
    "type": "project",
    "license": "BSD-3-Clause",
    "support": {
        "issues": "https://github.com/yiisoft/yii2/issues?state=open",
        "forum": "http://www.yiiframework.com/forum/",
        "wiki": "http://www.yiiframework.com/wiki/",
        "irc": "irc://irc.freenode.net/yii",
        "source": "https://github.com/yiisoft/yii2"
    },
    "require": {
        "php": ">=5.4.0",
        "yiisoft/yii2": "2.0.8",
        "yiisoft/yii2-bootstrap": "~2.0.0",
        "yiisoft/yii2-swiftmailer": "~2.0.0 || ~2.1.0",
        "phpoffice/phpspreadsheet": "1.5.2",
        "pictree/blog": ">=1.0.1",
        "peachpear/pear-leaf": ">=1.0.1"
    },
    "require-dev": {
        "yiisoft/yii2-debug": "~2.0.0",
        "yiisoft/yii2-gii": "~2.0.0",
        "yiisoft/yii2-faker": "~2.0.0",
        "codeception/base": "^2.2.3",
        "codeception/verify": "~0.3.1"
    },
    "config": {
        "process-timeout": 1800,
        "fxp-asset": {
            "enabled": false
        }
    },
    "repositories": [
        {
            "type": "composer",
            "url": "https://packagist.phpcomposer.com"
        }
    ]
}
```

其中require、require-dev就是我们应用所需的依赖package。

我们可以发布自己的package吗？这样以后也可以像这里一样加载自己的可用的依赖包了。

我们也可以在 Packagist<https://packagist.org/> 上发布自己的package包。

#### 第一步，开发自己的package

可以如下组织应用结构：

```
└── pear-leaf
    ├── docs
    |    └── usage.md
    ├── simples
    |    └── demo.json
    ├── src
    |    └── pearLeaf
    |        └── ConfigService.php
    ├── .gitignore
    ├── composer.json
    ├── LICENSE
    └── README.md
```

composer.json中示例：

```
{
    "name": "peachpear/pear-leaf",
    "description": "project's setting leaf on linux",
    "license": "MIT",
    "authors": [
        {
            "name": "baiyang",
            "email": "zhuyingu@126.com"
        }
    ],
    "require": {},
    "autoload": {
        "psr-4": {
            "peachpear\\pearLeaf\\": "src/pearLeaf"
        }
    }
}
```

其他文件自己看着写。

完成后，上传到github。

#### 第二步，发布的package

首先在 <https://packagist.org/> 注册账号并登陆，也可以用github账号登陆Packagist。

点击导航栏的Submit，填写自己package包地址，点击Check，没问题，然后提交：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20181219/20181219200600.jpg)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20181219/20181219200620.jpg)

自动跳转到：

![]({{site.baseurl}}/images/20181219/20181219200637.jpg)

现在版本默认是dev-master。

测试没问题，可以发布稳定版本。

项目每次修改后提交后，打上版本号：

> git tag -a 1.0.2 -m 'ver1.0.2'

然后提交：

```
git push origin --tags
```
 
然后在Packagist更新，就可以了。

不过需要等待一段时间（大概十几分钟后）才可以用。

可以查看本地有哪些版本：

> git tag

composer.json引入所需依赖：

```
    "require": {
        "pictree/blog": ">=1.0.1"
    },
```

<br/><br/><br/><br/><br/>
### 参考资料

[Git 基础 - 打标签](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)