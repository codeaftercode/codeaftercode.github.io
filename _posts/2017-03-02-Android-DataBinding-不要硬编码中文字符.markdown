---
layout: post
title:  "Android-DataBinding-不要硬编码中文字符"
date:   2017-03-02 10:20:00 +0800
categories: Android
---
| 摘要：在Android布局文件中使用DataBinding表达式，不要硬编码中文字符。如需显示中文字符，请放到string文件里。

目测，在Windows系统上，DataBinding表达式中只有中文字符或只有英文字符时，可正常运行。中英文混排时，含有英文字符+偶数个中文字符，可正常运行；<font color="#ff0000">含有英文字符+奇数个中文字符时，编译报错</font>。如果把中文字符放到string文件里，再引用string文件，则无论多少个中文字符都没问题。

此写法报错:

{% highlight android linenos %}
<TextView
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:text="@{`正确率:` + Integer.toString(accuracy)}" />
{% endhighlight %}

此写法正确:
{% highlight android linenos %}
    <TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@{@string/accuracy + Integer.toString(accuracy)}" />
{% endhighlight %}

{% highlight android %}
<string name="accuracy">正确率</string>
{% endhighlight %}



* ps. 我的环境是Win7 64bit、Android Studio2.2.2。所说在Mac上直接硬编码也不会出现此问题。
