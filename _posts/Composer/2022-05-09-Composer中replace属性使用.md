---
layout: post
categories: Composer
title: Composer中replace属性使用
meta: Composer中replace属性使用
---
* content
{:toc}

## 正文

官方对 replace 的说明：

Map of packages that are replaced by this package. This allows you to fork a package, 
publish it under a different name with its own version numbers, 
while packages requiring the original package continue to work with your fork because it replaces the original package.

This is also useful for packages that contain sub-packages, 
for example the main symfony/symfony package contains all the Symfony Components which are also available as individual packages. 
If you require the main package it will automatically fulfill any requirement of one of the individual components, since it replaces them.

Caution is advised when using replace for the sub-package purpose explained above. 
You should then typically only replace using self.version as a version constraint, 
to make sure the main package only replaces the sub-packages of that exact version, 
and not any other version, which would be incorrect.

举个例子：

    我有一个名为 「My App」的应用程序
    「My App」 需要两个软件包作为依赖，即 「original/library」 和 「other/package」
    「other/package」 依赖于「original/library」
    「original/library」的维护者不再更新它，并且上面有很多错误。
    您想解决 「original/library」上的问题，但他们不接受您的请求请求
    您在名为 「yourfork/library」的包中 fork 了「original/library」,指定 replace 「original/library」
    您在「My App」 项目中指定使用「yourfork/library」

![]({{site.baseurl}}/images/20220510/20220510093449.png)

现在，即使您没有直接「告诉」「other/package」 停止使用「original/library」，Composer 也不会再加载「original/library」，
因为你告诉他可以将其替换为「yourfork/library」。



## 参考资料

<https://getcomposer.org/doc/04-schema.md>

如何使用 Composer 中的 replace 属性？  <https://learnku.com/laravel/t/55168>

使用replace属性来避免Composer的依赖冲突 <https://www.php.cn/tool/composer/472103.html>

如何使用 replace 属性来避免 Composer 的依赖冲突？  <https://learnku.com/laravel/t/55200>

视频：composer.json 依赖 replace 代替 <https://www.bilibili.com/video/av75736677>



