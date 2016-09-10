---
layout: post
title:  "Window 7 安装 Jekyll 的正确姿势"
date:   2016-09-10 19:10:00 +0200
categories: jekyll install
---

摘要：这是一篇介绍如何零基础安装Jekyll的教程，从下载安装Ruby开始，到Jekyll在本地部署并测试成功，包括其中遭遇的困难和解决方案。本文仅提供Jekyll安装教程，未涉及博客注册、编辑和发布。

## 前言
作为一个没有Ruby基础的技术小白，按照网上的教程，跌跌撞撞总算是啃下了这块硬骨头。回头再看这些教程，多数是几年前的，有些环节现在已不再适用；有些细节没有描述清楚，总要四处寻找答案。所以，我的博客搭建完成后，我要将我的经验、遇到和解决的问题记录下来，仅供参考。其中有的环节我依然未能彻底掌握，以后还要慢慢学习。感谢各位博客作者，你们的教程给我提供了巨大帮助。

## 1.安装准备
使用Jekyll需要下载安装Ruby和DevKit。Ruby的安装方法多种多样，Window用户只需下载RubyInstallers.exe安装即可。DevKit是Ruby的工具，与RubyInstallers的下载地址相同：http：//rubyinstaller.org/downloads/。该站有时无法正常访问，请翻墙。
![下载Ruby][image_root](/images/downloadRuby.jpg)
![下载DevKit][image_root](/images/downloadDevKit.jpg)
注意：两者均有32位和64位版本，下载时请选择相同的版本。

## 2.安装Ruby
### 2.1开始安装
运行RubyInstallers.exe，开始安装Ruby。建议安装路径不要包含中文、不要包含空格及任何奇怪的符号，这可能会导致Ruby不能运行。
安装时选择同意添加Path环境变量。如果忘选了，可以手动添加。计算机-属性-高级系统设置-环境变量-系统变量-Path-编辑：在变量值最前面添加“安装路径\bin;”注意：不要加双引号，只加双引号里的内容（含分号）。

安装完成后，打开命令行，验证安装是否成功，命令为：
{% highlight css %}
ruby -v
{% endhighlight %}
|执行结果：
{% highlight css %}
rbby 2.3.1p112(2016-04-26 revision 54768) [x64-mingw32]
{% endhighlight %}
|能显示版本信息，证明安装成功。
|失败怎么办？我一次就成功了，还真不知道失败了怎么办。

### 2.2更改sources源
由于国内网络原因，导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败，需要把源重定向到国内稳定的镜像上去。按照网上教程切换到[淘宝](https://ruby.taobao.org/) ，屡次尝试结果均为认证错误，后来才知道淘宝的已经不维护了，Ruby China提供了新的解决方案。打开[https://gems.ruby-china.org/](https://gems.ruby-china.org/) ，按照网页上的说明，先清空sources。由于之前试验了淘宝的镜像，因此这里一并清空，查看sources列表确认已全部清空，然后添加Ruby China的镜像。

{% highlight css %}
gem sources –remove https://rubygems.org/
gem sources -remove https://ruby.taobao.org/
gem sources -l
gem sources --add https://gems.ruby-china.org/
{% endhighlight %}
|执行结果：
![证书错误](images/SSLerror.PNG)
什么，又是证书！这不是和淘宝一样需要证书？考虑再三，我决定不用https协议，改用http，这回不用出示证件了吧！
{% highlight css %}
gem sources --add http://gems.ruby-china.org/
{% endhighlight %}
|果然成功了。这时我想到，淘宝改用http协议能成功吗？没敢去验证，好不容易成功了，赶紧往下执行吧，天都黑了。
|如果失败怎么办？翻墙吧！但翻墙只是不得已而为之，因为以后的项目里会经常安装一些包，这墙翻过来翻过去也挺麻烦的吧。

### 2.3 更新gem
别说费话，直接输入命令：
{% highlight css %}
gem update --system
{% endhighlight %}
|因为 sources 已经更改到可以访问的地址，这一步只需要耐心等待几分钟即可。

### 2.4 可选操作
如果使用 Gemfile 和 Bundle （例如 Rails 项目），可以在gem更新完成后应用 Bundler 的 Gem 源代码镜像命令，这样就不用再改 Gemfile 的 source。
{% highlight css %}
bundle config mirror.https://rubygems.org https://gems.ruby-china.org
{% endhighlight %}

## 3.安装DevKit
### 3.1准备安装
解压DevKit到你指定的路径，这个路径将成为DevKit的安装路径。建议安装路径不要包含中文、不要包含空格及任何奇怪的符号，这可能会导致Ruby不能运行。

### 3.2初始化
打开命令行，进入DevKit目录，输入命令：
{% highlight css %}
ruby dk.rb init
{% endhighlight %}

### 3.3配置DevKit
到DevKit目录，用文本编辑器打开config.yml文件，在文件尾添加以下内容：
{% highlight css %}
- xxxx\Ruby23-x64
- xxxx\Ruby23-x64
{% endhighlight %}
|前有减号和空格
|注意是两行
|不要问我同样的内容为什么写2次，反正写1次有时候就出错，我也纳闷呢。
完成后，从命令行进入DevKit目录下，查看配置是否成功：
{% highlight css %}
ruby dk.rb review
{% endhighlight %}

### 3.4安装DevKit
在DevKit目录下输入命令：
{% highlight css %}
ruby dk.rb install
{% endhighlight %}

## 4安装测试Jekyll
### 4.1安装Jekyll
终于见到正主了！其实安装Jekyll非常简单，难的地方是之前的步骤。在命令行输入命令：
{% highlight css %}
gem install jekyll
{% endhighlight %}
需要等待较长时间，gem将下载一大堆文件。

### 4.2可选操作
现在可以安装一些常用的插件，如cucumber，非常流行的语法高亮插件rouge等。安装方法与Jekyll，在命令行输入命令即可，如：
{% highlight css %}
gem install cucumber
{% endhighlight %}

### 4.3Jekyll环境测试
#### 4.3.1新建博客
打开资源管理器，新建一个目录。不不不，我们可以显得更专业些。在命令行执行以下命令，将在当前目录自动创建一个名为blog的文件夹：
{% highlight css %}
jekyll new blog
{% endhighlight %}

#### 4.3.2启动服务
在启动服务之前，需要将工作空间改到子目录中，即执行 cd blog 进入到子目录中，输入下面命令开启服务：
{% highlight css %}
Jekyll serve
{% endhighlight %}
#### 4.3.3安装插件
启动服务后执行结果为：
![找不到文件](images/loadError.jpg)
运行出错，原因是找不到bundler这个文件。在命令行安装bundeler：
{% highlight css %}
gem install bundler
{% endhighlight %}
|下载很快，但是使用bundle需要进行配置
{% highlight css %}
bundle init
bundle install
{% endhighlight %}
|这一步的安装时间较长
|安装完成，再次执行jekyll server，出现新的错误，找不到minima。安装：
{% highlight css %}
gem install minima
{% endhighlight %}
|就这样，又安装了几个包，直到出现新的问题。
#### 4.3.4抢占端口
该安装的包都安装好了，再次启动服务，执行结果：
![端口被占用](/images/permissionDenied.jpg)
Permission denied，端口被占用。来看看是谁占用了我的端口。输入命令：
{% highlight css %}
netstat -anl
{% endhighlight %}
![查看活动连接](/images/netstat.PNG)
Jekyll默认使用4000端口，可以看到被PID为1800的进程占用了。看看这个进程是什么鬼：
{% highlight css %}
tasklist /svc /FI "PID eq 1800"
{% endhighlight %}
![查看进程名](/images/whoUseMyPort.PNG)
可以看到是Foxit（福昕pdf阅读器）在监听4000端口，并不是系统进程，打开任务管理器结束该进程即可。为避免每次都手动关闭进程，可以禁止其开机自动启动，或者把blog改用其他端口，如5001。方法是打开blog/_config.yml文件，在其中加入一句
{% highlight css %}
port: 5001
{% endhighlight %}
![更改端口](/images/changePort.PNG)
|注意有空格，否则出错。
#### 4.5.4测试成功
启动服务，命令行未报错，显示了服务信息。
![测试成功](/images/testSuccess.PNG)
打开浏览器，输入地址
localhost:4000
或修改成新端口：
localhost:5001
![测试页](/images/testPage.PNG)
看到这个页面，证明Jekyll配置成功。
回到命令行，按住 Ctrl + C 结束服务，退出命令行，测试结束。


主要参考文献
[Jekyll安装及写静态博客](http://www.tuicool.com/articles/7Vz6BzJ)
[用jekyll制作高大上的网站](http://www.cnblogs.com/strick/p/5448570.html)
[Ruby China的RubyGems 镜像上线](https://ruby-china.org/topics/29250)
[Running Jekyll on Windows](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)
[Stack Overflow](http://stackoverflow.com/)
[谁占了我的端口 for Windows](http://lxconan.github.io/2016/01/07/who-is-using-my-port/)

关于如何在Github上创建博客，参考[使用Github Pages建独立博客](http://beiyuu.com/github-pages)。
[image_root]: http://codeaftercode.github.io/assets