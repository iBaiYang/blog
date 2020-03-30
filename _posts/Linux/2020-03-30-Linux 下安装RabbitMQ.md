---
layout: post
categories: Linux
title: Linux 下安装RabbitMQ
meta: 记录一下 Linux 下安装RabbitMQ流程
---
* content
{:toc}

### 正文

#### 安装Erlang环境

安装之前要装一些必要的库:

> sudo apt-get install build-essential 

> sudo apt-get install libncurses5-dev 

> sudo apt-get install libssl-dev 

> sudo apt-get install m4 

> sudo apt-get install unixodbc unixodbc-dev 

> sudo apt-get install freeglut3-dev libwxgtk2.8-dev 

> sudo apt-get install xsltproc 

> sudo apt-get install fop 

> sudo apt-get install tk8.5

安装好之后安装erlang：

> sudo apt-get install erlang

安装完毕后，查看erlang安装版本情况:

> erl

![]({{site.baseurl}}/images/20200330/20200330184710.png)

#### 安装RabbitMQ

> sudo apt-get install rabbitmq-server

安装完毕后，查看运行状态：

> service rabbitmq-server status

![]({{site.baseurl}}/images/20200330/20200330184756.png)

#### RabbitMQ配置

![]({{site.baseurl}}/images/20200330/20200330184436.png)

![]({{site.baseurl}}/images/20200330/20200330184520.png)

可以看到rabbitmq.config文件不存在，我们需要添加该文件:

> cd /etc/rabbitmq
> 
> ls
>
> vim rabbitmq.config

写入：
```
[{rabbit, [{loopback_users, []}]}].
```

这里的意思是开放使用，rabbitmq默认创建的用户guest，密码也是guest，这个用户默认只能是本机访问，
localhost或者127.0.0.1，从外部访问需要添加上面的配置。

![]({{site.baseurl}}/images/20200330/20200330185328.png)

保存配置后重启服务：

> service rabbitmq-server restart

#### 安装插件

现在还不用通过web页面管理rabbitmq，我们需要安装插件：

> sudo rabbitmq-plugins enable rabbitmq_management

然后重启：

> service rabbitmq-server restart

![]({{site.baseurl}}/images/20200330/20200330190203.png)

现在,就可以通过http://localhost:15672访问web管理页面，使用 guest,guest 进行登陆了：

![]({{site.baseurl}}/images/20200330/20200330190553.png)

![]({{site.baseurl}}/images/20200330/20200330190649.png)

可以看到首页概览中只有Exchanges 有8个，看一下详情：

![]({{site.baseurl}}/images/20200330/20200330193355.png)


    
<br/><br/><br/><br/><br/>
### 参考资料

Rabbitmq安装教程（Deepin15.11） <https://blog.csdn.net/qq_41999455/article/details/103006950>

安装 RabbitMQ – centos 6 <http://www.fancyecommerce.com/2017/06/02/%e5%ae%89%e8%a3%85-rabbitmq-centos-6/>

deepin安装rabbit-mq <https://blog.csdn.net/weixin_33910385/article/details/92030750>

