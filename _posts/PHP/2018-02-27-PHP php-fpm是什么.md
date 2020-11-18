---
layout: post
categories: PHP
title: PHP php-fpm是什么
meta: 大家都知道，PHP的解释器是php-cgi。php-cgi只是个CGI程序，他自己本身只能解析请求，返回结果，不会进程管理,所以就出现了一些能够调度php-cgi进程的程序，比如说由lighthttpd分离出来的spawn-fcgi。好了PHP-FPM也是这么个东东，在长时间的发展后，逐渐得到了大家的认可，也越来越流行。
---
* content
{:toc}

### 正文

![]({{site.baseurl}}/images/20190816/20190816134124.png)

![]({{site.baseurl}}/images/20191018/20191018105918.png)

引入参考：

刚开始对这个问题我也挺纠结的，看了《HTTP权威指南》后，感觉清晰了不少。

首先，CGI（Common Gateway Interface/通用网关接口）是干嘛的？CGI是为了保证web server传递过来的数据是标准格式的，方便CGI程序的编写者。

<blockquote>
<p>web server（比如说nginx）只是内容的分发者。比如，如果请求<code>/index.html</code>，那么web server会去文件系统中找到这个文件，发送给浏览器，这里分发的是静态数据。好了，如果现在请求的是<code>/index.php</code>，根据配置文件，nginx知道这个不是静态文件，需要去找PHP解析器来处理，那么他会把这个请求简单处理后交给PHP解析器。Nginx会传哪些数据给PHP解析器呢？url要有吧，查询字符串也得有吧，POST数据也要有，HTTP
 
header不能少吧，好的，CGI就是规定要传哪些数据、以什么样的格式传递给后方处理这个请求的协议。仔细想想，你在PHP代码中使用的用户从哪里来的。</p>
<p>当web server收到<code>/index.php</code>这个请求后，会启动对应的CGI程序，这里就是PHP的解析器。接下来PHP解析器会解析php.ini文件，初始化执行环境，然后处理请求，再以规定CGI规定的格式返回处理后的结果，退出进程。web server再把结果返回给浏览器。</p>
</blockquote>

好了，CGI是个协议，跟进程什么的没关系。那fastcgi又是什么呢？Fastcgi是用来提高CGI程序性能的。

<blockquote>
<p>提高性能，那么CGI程序的性能问题在哪呢？"PHP解析器会解析php.ini文件，初始化执行环境"，就是这里了。标准的CGI对每个请求都会执行这些步骤（不闲累啊！启动进程很累的说！），所以处理每个时间的时间会比较长。这明显不合理嘛！那么Fastcgi是怎么做的呢？首先，Fastcgi会先启一个master，解析配置文件，初始化执行环境，然后再启动多个worker。当请求过来时，master会传递给一个worker，然后立即可以接受下一个请求。这样就避免了重复的劳动，效率自然是高。而且当worker不够用时，master可以根据配置预先启动几个worker等着；当然空闲worker太多时，也会停掉一些，这样就提高了性能，也节约了资源。这就是fastcgi的对进程的管理。</p>
</blockquote>

那PHP-FPM又是什么呢？是一个实现了Fastcgi的程序，被PHP官方收了。

<blockquote>
<p>大家都知道，PHP的解释器是php-cgi。php-cgi只是个CGI程序，他自己本身只能解析请求，返回结果，不会进程管理（皇上，臣妾真的做不到啊！）所以就出现了一些能够调度php-cgi进程的程序，比如说由lighthttpd分离出来的spawn-fcgi。好了PHP-FPM也是这么个东东，在长时间的发展后，逐渐得到了大家的认可（要知道，前几年大家可是抱怨PHP-FPM稳定性太差的），也越来越流行。</p>
</blockquote>

好了，最后来回来你的问题。

网上有的说，fastcgi是一个协议，php-fpm实现了这个协议

<blockquote>
<p>对。</p>
</blockquote>

有的说，php-fpm是fastcgi进程的管理器，用来管理fastcgi进程的

<blockquote>
<p>对。php-fpm的管理对象是php-cgi。但不能说php-fpm是fastcgi进程的管理器，因为前面说了fastcgi是个协议，似乎没有这么个进程存在，就算存在php-fpm也管理不了他（至少目前是）。
  有的说，php-fpm是php内核的一个补丁</p>
<p>以前是对的。因为最开始的时候php-fpm没有包含在PHP内核里面，要使用这个功能，需要找到与源码版本相同的php-fpm对内核打补丁，然后再编译。后来PHP内核集成了PHP-FPM之后就方便多了，使用<code>--enalbe-fpm</code>这个编译参数即可。</p>
</blockquote>

有的说，修改了php.ini配置文件后，没办法平滑重启，所以就诞生了php-fpm

<blockquote>
<p>是的，修改php.ini之后，php-cgi进程的确是没办法平滑重启的。php-fpm对此的处理机制是新的worker用新的配置，已经存在的worker处理完手上的活就可以歇着了，通过这种机制来平滑过度。</p>
</blockquote>

还有的说PHP-CGI是PHP自带的FastCGI管理器，那这样的话干吗又弄个php-fpm出

<blockquote>
<p>不对。php-cgi只是解释PHP脚本的程序而已。</p>
</blockquote>

![]({{site.baseurl}}/images/20190902/20190902112145.jpeg)

#### web服务器

php是后端语言，对外提供服务要借助于web服务器，或者说用户通过访问web服务器才能收到php提供的服务。

常用的web服务器有：apache、nginx、IIS、lighttpd、tomcat等。

例如用apache当作web服务器，一次完整的php访问： 

![]({{site.baseurl}}/images/20201118/20201118104555.jpg)

#### mod_php模式

上面讲清楚了php必须借助于web服务器才能提供web的功能服务，现在看下他俩是怎么搭伙的。

为了使apache能够识别php代码，我们要在apache的配置文件httpd.conf中加上或者修改这样几句：
```
//加入以下2句
LoadModule php5_module D:/php/php5apache2_2.dll
AddType application/x-httpd-php .php

//将下面的
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
//将其修改为：
<IfModule dir_module>
    DirectoryIndex index.html index.htm index.php index.phtml
</IfModule>
```

上面的windows下安装php和apache环境后的手动配置，可以看出，apache是用LoadModule来加载php5_module，就是把php作为apache的一个子模块来运行。
当通过web访问php文件时，apache就会调用php5_module来解析php代码。

那么php5_module是怎么来将数据传给php解析器来解析php代码的呢？

#### sapi

答案是通过sapi。

看一下apache 与 php 与 sapi的关系：

![]({{site.baseurl}}/images/20201118/20201118104556.jpg)

从上面图中，我们看出了sapi就是这样的一个中间过程，SAPI提供了一个和外部通信的接口，有点类似于socket，
使得PHP可以和其他应用进行交互数据（apache,nginx,cli等）。php默认提供了很多种SAPI，
常见的给apache和nginx的php5_module，CGI，给IIS的ISAPI，还有Shell的CLI。

所以，以上的apache调用php执行的过程如下：
> apache -> httpd -> php5_module -> sapi -> php

我们把这种运行方式叫做`mod_php模式`。

#### mod_fastcgi模式

sapi是php提供的统一接口，有两种模式。
一种模式是 mod_php模式，通过php5_module加载模式。
一种模式是 cgi模式，由于cgi比较老所以就出现了fastcgi来取代它，也就是 mod_fastcgi模式。

CGI(Common Gateway Interface)。CGI是应用程序（CGI程序）与Web服务器之间的接口标准。

web服务器收到用户请求，就会把请求提交给cgi程序（php的fastcgi），cgi程序根据请求提交的参数作应处理（解析php），然
后输出标准的html语句返回给web服服务器，再返回给客户端，这就是普通cgi的工作原理。

cgi的好处就是完全独立于任何服务器，仅仅是做为中间分子。提供接口给apache和php。
他们通过cgi搭线来完成搞基动作。这样做的好处了尽量减少2个的关联，使他们2变得更独立。

但是cgi有个蛋疼的地方，就是每一次web请求都会有启动和退出过程，也就是最为人诟病的fork-and-execute模式，这样一在大规模并发下，就死翘翘了。
所以，这个时候fastcgi运用而生了。它事先就早早的启动好了，而且可以启动多个cgi模块，在那里一直运行着等着，
等着web发过来的请求，然后再给php解析运算完成生成html给web后，也不会退出，而且继续等着下一个web请求。
而且这些cgi的模块启动是可控的，可监测的。这种技术还允许把web server和php运行在不同的主机上，以大规模扩展和改进安全性而不损失生产效率。

现在一般操作系统都是fastcgi模式。这种运行方式叫做mod_fastcgi模式。

mod_php 模式图：

![]({{site.baseurl}}/images/20201118/20201118104557.png)

mod_php 模式是将php模块安装到apache中，所以每一次apache结束的请求呢，都会产生一条进程，这个进程就完整的包括php的各种运算计算等操作。
从图中我们很清晰的可以看到，apache每接收一个请求，都会产生一个进程来连接php通过sapi来完成请求，可想而知，如果一旦用户过多，并发数过多，服务器就会承受不住了。
而且，把mod_php编进apache时，出问题时很难定位是php的问题还是apache的问题。

mod_fastcgi模式图：

![]({{site.baseurl}}/images/20201118/20201118104558.png)

fastcgi是一个独立与apache和php的独立个体，它随着apache一起启动，生成多个cgi模块，等着apache的请求。
图中fastcgi早早的启动好了，静静的在哪里等着，已有apache发来的httpd请求就立马接收过来，通过调用sapi给php，完成运算，而且不会退出。
这样就能应对大规模的并发请求，因为web server的要做的事情少了，所以就更快的去处理下一个请求，这样并发就提高了。
由于apache 与 php 独立了。出问题，很好定位到底是哪里出问题了。这点也是这种模式受欢迎的原因之一。

#### php-fpm

fastcgi 是一个与平台无关，与语言无关，任何语言只要按照它的接口来实现，就能实现自己语言的fastcgi能力和web server 通讯。

PHP-CGI就是PHP实现的自带的FastCGI管理器。虽然是php官方出品，自带的，但是这丫的却一点也不给力，性能太差，而且也很麻烦不人性化，主要体现在：

1、php-cgi变更php.ini配置后需重启php-cgi才能让新的php-ini生效，不可以平滑重启。
2、直接杀死php-cgi进程，php就不能运行了。

上面2个问题，一直让很多人诟病了很久，所以很多人一直还是在用mode_php方式。

直到 2004年，一个叫 Andrei Nigmatulin的小伙发明了PHP-FPM，这神器的出现就彻底打破了这种局面，这是一个PHP专用的fastcgi管理器，
它很爽的克服了上面2个问题，而且，还表现在其他方面更表现强劲。[官网](https://php-fpm.org/about/)

PHP在 5.3.3 之后已经将php-fpm写入php源码核心了，不需要单独下载。


<br/><br/><br/><br/><br/>
### 参考资料

php-fpm安装、配置与优化 <https://blog.csdn.net/ivan820819/article/details/54970330>

php中fastcgi和php-fpm是什么东西 <https://www.zybuluo.com/phper/note/50231>

php-fpm的配置和优化 <https://www.zybuluo.com/phper/note/89081>

<https://segmentfault.com/q/1010000000256516>
