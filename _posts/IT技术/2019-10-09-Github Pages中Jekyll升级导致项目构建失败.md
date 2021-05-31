---
layout: post
categories: IT技术
title: Github Pages中Jekyll升级导致项目构建失败
meta: Github Pages中Jekyll升级导致项目构建失败
---
* content
{:toc}

### 正文

今天更新了blog，发现Github Pages没有更新，接着收到Github Pages的构建失败的通知邮件：
```
The page build failed for the `master` branch with the following error: 

Page build failed. For more information, see https://help.github.com/en/articles/troubleshooting-jekyll-build-errors-for-github-pages-sites#troubleshooting-build-errors. 

For information on troubleshooting Jekyll see: 

  https://help.github.com/articles/troubleshooting-jekyll-builds 

If you have any questions you can contact us by replying to this email. 
```

过去几年一直用的好好的，今天突然出现这个情况，不会是NBA为港独站队，Github不让大陆这面使用了吧。然后各种搜索，发现有好多人过去也碰到过这个情况，
所以应该是其他原因。查来查去，解决方法是只能本地安装Jekyll，然后构建寻找失败原因。邮件中给的网址一直看不到想要的解决办法，
好多人说是Github Pages升级了jekyll，新版本导致的问题。所以只能本地想办法复现问题了。看文档，jekyll安装需要先安装Ruby,而且不同的版本对Ruby的版本有要求。

自己先在自己的deepin系统用apt-get方式安装了Ruby,版本是：
```
ruby 2.3.3p222 (2016-11-21) [x86_64-linux-gnu]
```

然后被要求又安装了ruby-dev，发现还是不能安装Jekyll，Jekyll4.0最新版本要求Ruby的最低版本是2.4，怎么升级Ruby呢？又成了一个问题。
折腾来折腾去，只能用rvm方式安装2.4版的Ruby了，又开始安装rvm。

先把上面apt安装的Ruby卸载：

> sudo apt remove ruby

> sudo apt remove ruby-dev

> sudo apt autoremove 

准备安装rvm，切换到国内源：

> sudo apt-get install gem
> 
> sudo apt-get install bundler

```
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
```

再看一下gem的版本：

> gem -v

版本是3.0.6。

安装rvm：
```
curl -L get.rvm.io | bash -s stable
```

可能报错，按照报错提示：

```
gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```

然后再安装rvm：
```
curl -L get.rvm.io | bash -s stable
```

看到 Thanks for installing RVM 🙏 ，说明安装成功。

```
echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"' >>~/.bashrc
```

> source ~/.bashrc

现在就可以用rvm -v查看版本了：

> rvm -v

输出：
```
rvm 1.29.9 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
```

下面安装Ruby，列出已知的ruby版本：

> rvm list known

看到最新版是2.6，那我们就安装2.6：

> rvm install 2.6

完成后查看Ruby版本：

> ruby -v

结果是ruby-2.6.3。

然后安装我们的主角Jekyll：

> gem install jekyll

完成后，我们看一下Jekyll的版本：

> jekyll -v

输出 jekyll 4.0.0。

#### 定位问题

现在我们可以定位问题了，cd切换到项目目录下，也就是_config.yml所在的目录下，构建：

> jekyll build

输出问题文件：
```
Configuration file: /home/baiyang/www/blog/_config.yml
            Source: /home/baiyang/www/blog
       Destination: /home/baiyang/www/blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
  Conversion error: Jekyll::Converters::Markdown encountered an error while converting '_posts/HTML/2018-05-25-WebSocket 技术探究.md':
                    Rouge::Guesser::Ambiguous
                    ------------------------------------------------
      Jekyll 4.0.0   Please append `--trace` to the `build` command 
                     for any additional information or backtrace. 
                    ------------------------------------------------
```

这里已经点明了问题所在的文件，Rouge::Guesser::Ambiguous 这句话还是有些模糊，还需要在这个文件中一步一步缩小范围定位问题，多次jekyll build查看问题在哪一行。

最后定下来居然是这个地方的问题，html文件头标记：

```
!DOCTYPE html  
```

把这一行删了就好了，jekyll build看一下：
```
Configuration file: /home/bai/www/blog/_config.yml
            Source: /home/bai/www/blog
       Destination: /home/bai/www/blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 2.427 seconds.
```

然后提交到github上，发现构建成功了，问题解决。

<br/><br/><br/><br/><br/>
### 参考资料

［solved］Page build failed(Jekyll) <https://blog.csdn.net/wuzhimang/article/details/77132014>

deepin15.11使用RVM安装ruby <https://www.cnblogs.com/Zlcode/p/11490724.html>

Jekyll基本用法 <http://jekyllcn.com/docs/usage/>
