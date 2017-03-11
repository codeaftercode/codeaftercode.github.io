---
layout: post
title:  "Ubuntu-Android studio安装配置升级除错"
date:   2017-03-08 10:00:00 +0800
categories: Android
---
| 摘要：ubuntu安装配置完毕，开始安装Android studio。

## 0.Jdk
ubuntu默认安装openJDK，但我用oracle的JDK，在[Java SE Downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载了最新的jdk1.8.0_121。

### 0.0解压安装
在/usr/lib/下创建jvm文件夹，把jdk解压到此
{%  highlight linux %}
sudo mkdir /usr/lib/jvm
tar zxvf jdk-8u121-linux-x64.tar.gz -C /usr/lib/jvm
{% endhighlight %}

### 0.1设置全局环境变量
打开配置文件
{%  highlight linux %}
gedit /etc/profile
{% endhighlight %}

在打开的profile文件末尾添加以下内容
{%  highlight linux %}
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_121
export CLASSPATH=".:$JAVA_HOME/lib:$CLASSPATH"
export PATH="$JAVA_HOME/bin:$PATH"
{% endhighlight %}

### 0.2设置系统默认JDK
{%  highlight linux %}
update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_121/bin/java 300
update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_121/bin/javac 300
update-alternatives --config java
update-alternatives --config javac
{% endhighlight %}

### 0.3测试
输入命令java -version见到JDK的信息则表示成功。
{%  highlight linux %}
java version "1.8.0_121"
Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
{% endhighlight %}

## 1.Android Studio
网上教程多是先安装SDK，再安装Android Studio。我偏不，我就要先安装Android Studio，用Android Studio的SDK Manager安装SDK。

### 1.0解压安装
{%  highlight linux %}
unzip -d /opt android-studio-ide-145.3537739-linux.zip
{% endhighlight %}

### 1.1首次运行前
解压后就可以运行了，但是Android Studio第一次运行时，会联网检查SDK，因为众所周知的原因，联网失败并报错：Unable to access Android SDK add-on list。可以先绕过这个检查。打开bin/idea.properties文件
{%  highlight linux %}
sudo gedit /opt/android-studio/bin/idea.properties
{% endhighlight %}

在最后追加一句
{%  highlight linux %}
disable.android.first.run=true
{% endhighlight %}

### 1.2运行
{%  highlight linux %}
sudo /opt/android-studio/bin/studio.sh
{% endhighlight %}

用sudo命令可以启动Android Studio，而用root用户启动可能会报错
{%  highlight linux %}
(java:4335): GLib-GIO-CRITICAL **: g_dbus_connection_register_object: assertion 'G_IS_DBUS_CONNECTION (connection)' failed。
{% endhighlight %}

在终端用root用户运行其他有图形介面的程序，有时也会发生类似错误。原因是sudo命令使用的是普通用户的环境变量，而切换到root用户后，使用的是root用户的环境变量。只需退出root，以普通用户身份运行就不报错了。

### 1.3创建快捷启动图标
如果不设置快捷启动图标，每次启动软件都要输入命令，进入..../android-studio/bin/下运行studio.sh，特别麻烦。 ubuntu的所有的快捷启动图标都在/usr/share/applications/内，在此创建Android Studio对应的快捷启动图标后，只需双击此图标就可以直接启动软件。
{%  highlight linux %}
sudo gedit /usr/share/applications/AndroidStudio.desktop
{% endhighlight %}

在打开的文档中添加下面的内容：
{%  highlight linux %}
[Desktop Entry]
Name=Android Studio
Comment=android studio
Exec=/opt/android-studio/bin/studio.sh
Icon=/opt/android-studio/bin/studio.png
Terminal=false
Type=Application
Categories=Application;Development;
{% endhighlight %}

注意，每一行必须紧靠左侧，每行结尾不能有空格，否则会失败。

× Name：快捷方式的名字

× Exec：程序路径

× Icon：图标路径

× Terminal：是否使用终端。false表示启动时不启动命令行窗口，true表示启动命令行窗口

× Type：程序类型

× Categories：快捷方式显示的位置。决定创建出的启动器在应用程序菜单中的位置，按照上面的写法创建的起动器将出现在应用程序－Developer分类中。如果想在应用程序－办公中创建启动器，上述最后一行应该写成：Categories=Application;Office;

### 1.4添加到启动器
再进一步，把快捷启动图标添加到启动器，启动软件更便捷。

× 方法0：在/usr/share/applications中找到刚创建的这个文件，把文件拖动到左侧启动器。

× 方法1：按快捷键Super+A，找到Android Studio图标，拖动到启动器。可以从筛选结果-类别-Developer中快速找到该应用，也可以输入android studio快速定位该应用。

× 方法2：应用启动后，启动器栏会出现此图标，点击锁定到启动器，以后就可以直接从启动器启动程序。

## 2.安装SDK
因为还没有安装SDK，所以启动Android Studio后，首先在Configure打开SDK Manager，为Android SDK 指定Location，该目录必须具有写入权限，所以我在/home分区里建立了一个文件夹专门用于存放SDK。然后选择SDK版本，开始下载安装。下载完成后软件会自动设置。考虑到HDD读写速度比SSD慢很多，我又将SDK移动到SSD上。
{%  highlight linux %}
sudo mv 原sdk目录 /usr/local/
{% endhighlight %}

重新启动Android Studio，再次打开SDK Manager，把Location指向/usr/local/Android/SDK。确定后会自动检查SDK，然后报错unable to run mksdcard sdk tool，据说是因为缺少一些32位的库，使用以上命令安装：
{%  highlight linux %}
sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6 
{% endhighlight %}

## 3.真机调试

### 3.0获取设备ID
手机不要连接电脑，在终端输入：lsusb，可以看到类似输出
{%  highlight linux %}
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 003: ID 1c4f:0002 SiGma Micro Keyboard TRACER Gamma Ivory
Bus 001 Device 002: ID 24ae:2000
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
{% endhighlight %}

现在把手机用数据线连接电脑，正确识别后，在终端输入：lsusb，可以看到类似输出
{%  highlight linux %}
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 003: ID 1c4f:0002 SiGma Micro Keyboard TRACER Gamma Ivory
Bus 001 Device 004: ID 2717:1268
Bus 001 Device 002: ID 24ae:2000
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
{% endhighlight %}

对比之下，发现多了一个ID 2717:1268的设备。（小米手机的ID一般都是2717开头），记住2717这个数字，马上要用到。

### 3.1创建规则文件
在/etc/udev/rules.d文件夹下新建文件50-android.rules并打开 
{%  highlight linux %}
sudo touch /etc/udev/rules.d/50-android.rules 
sudo gedit /etc/udev/rules.d/50-android.rules
{% endhighlight %}

在文件内输入以下内容：
{%  highlight linux %}
SUBSYSTEM=="usb", ATTR{idVendor}=="2717", MODE="0666", GROUP="plugdev"
{% endhighlight %}

其中的2717就是刚刚查到的设备ID头。

| 最新修改方法（我没测试，不知能用否）-不用去看设备的ID，直接在rules.d下增加一个文件50-android.rules，内容为：SUBSYSTEM=="usb" ENV{DEVTYPE}=="usb_device", MODE="0666"

修改文件权限
{%  highlight linux %}
sudo chmod a+r /etc/udev/rules.d/50-android.rules
{% endhighlight %}

### 3.2重启udev服务
{%  highlight linux %}
sudo service udev restart
{% endhighlight %}

### 3.3设置usb调试
在手机的开发者选项中打开USB调试功能。小米手机升级到MIUI8以后，开发者选项是默认隐藏的，要在设置-我的设备中，连续按MIUI版本，直到提示已打开开发者模式后，退到上一层，在更多设置中，多了开发者选项。打开USB调试，打开USB安装（需要联网，原因不详）

### 3.4测试
在终端切入Android的SDK的platform-tools/文件夹路径，终端输入adb devices，如果连接了手机，就能找到设备名 。
如果不能，重启adb服务再试试。
{%  highlight linux %}
sudo adb kill-server
sudo adb start-server
adb devices
{% endhighlight %}

通过以上设置，在Android Studio中就可以用真机调试app了，而且可以使用Tools->Android->Android Device Monitor来管理手机。

## 4.升级到2.3
安装版本为2.2.3，检测到新版本2.3。此次升级坑太多，不得不在此记录。
### 4.0用root用户升级
因为我把软件安装在/opt下，普通用户没有写入权限，所以不能直接升级。有人说要修改/opt/android-studio文件夹的权限为777，也有人反对说这样不安全。我认为，此目录平时只要有读取权限足够了，升级前临时切换到root用户，升级后切换到普通用户即可。
{%  highlight linux %}
su root
/opt/android-studio/bin/studio.sh
{% endhighlight %}

打开软件后，执行升级操作。注：用sudo /opt/android-studio/bin/studio.sh命令不行，必须用su root才行。

### 4.1导入项目
升级成功后，打开软件，导入项目报错：
{%  highlight linux %}
Studio does not have write access
{% endhighlight %}

找到项目文件一看，所有文件都上了把锁。查看文件属性-权限-所有人：root。这就不对了，所有人应该是我（普通用户）啊？！仔细一想，应该是刚才升级前切换到root用户启动软件后，自动打开了最近的项目，又自动编译了，所以项目文件都成为root用户的文件了。在终端把权限改回来。
{%  highlight linux %}
chown -R myusername myproject
{% endhighlight %}

执行上述命令后，文件所有人变成我（普通用户），文件上的锁清除了。

### 4.2设置gradle
#### 4.2.0下载最新gradle
在网上搜索最新的gradle（目前是3.4），下载下来，解压到Android Studio安装路径下的gradle文件夹下（我的是/opt/android-studio/gradle），解压后文件夹名应该是gradle-3.4。这里原来有个文件夹是gradle-3.2，删除它（不删也行）。

注：我在Android Studio 2.2.3用的gradle版本是2.14.1，升级到Android Studio 2.3后，软件自动把我的gradle2.14.1给删除了，并下载安装了gradle3.2。按后续步骤重新设置了Gradle Home和项目Gradle版本后，项目仍然无法编译，提示我更新Studio或Gradle版本。而Studio已经是最新版本了，所以只好更新Gradle版本，然后再重新设置Gradle Home和项目Gradle版本。

#### 4.2.1配置软件的Gradle Home路径
但是在Settings->Build,Execution,Deployment->Gradle里看到，Gradle Home指向gradle-2.14.1-all文件夹，这显然是不对的，应该修改为gradle-3.4。
注：如果不修改，新建项目会卡在Building project...，导入项目会卡在Refreshing gradle project。因为软件的Gradle指向的版本与当前安装的Gradle版本不一致，所以软件自动去下载Gradle，但下载过程奇慢无比，竟然卡死了。
又注：软件可能会卡死无法退出。可用ps -e指令查看进程，找到java对应的PID（如9876），再用kill 9876指令强行杀之。如果有多个java进程，全都要杀。

#### 4.2.2更新旧项目的Gradle版本
导入项目前，先用文本编辑器打开项目/gradle/warpper/gradle-wrapper.properties文件，把最后一行distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip中的gradle-2.14.1-all.zip改为gradle-3.4.zip。
注：如果不修改，导入项目时会卡在Refreshing gradle project...

#### 4.2.3Clean Project
重新启动Android Studio，Build->Clean Project后，项目可以编译了。
注：如果不clean，可能会报错：

{%  highlight linux %}
Error running app: This version of Android Studio is incompatible with the Gradle Plugin used. Try disabling Instant Run (or updating either the IDE or the Gradle plugin to the latest version)
{% endhighlight %}

## 参考
[Android Studio2.3升级之旅](http://blog.csdn.net/hylczp/article/details/60137958) 作者应该是个被code耽误了的相声大师，而此文所述与我经历高度一致。
