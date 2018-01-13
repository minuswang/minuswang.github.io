---
title: ActionBar、TitleBar、ToolBar的联系和区别
date: 2017-10-26 10:36:14
tags:
---
![](http://img.blog.csdn.net/20160719223712558)

[ToolBar的使用](http://blog.csdn.net/qianjfsjf/article/details/51960249)

![](http://img.blog.csdn.net/20160727155516635?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

[Android 设置应用程序的主题颜色](http://blog.csdn.net/u010670151/article/details/52046307)

![](http://mmbiz.qpic.cn/mmbiz_jpg/6GYDCe0a269E77c1hnTGwJ05Sjpia1icTkmZhnicQGBdBM8JLvqvOHhoq14TibyHeqGDsEEhDO9FoRYic4VnDLWRjqg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

[轻听变色之谜-QQ音乐技术团队](http://mp.weixin.qq.com/s/m_wZM8xtJnph8PTdbH6s7Q)

![](\ActionBar、TitleBar、ToolBar的联系和区别\tu1.png)


## [android：ToolBar详解（手把手教程）](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html)

> ## ActionBar

ActionBar 是android 3.0的推出的，当时Google 想要逐渐改善过去 android 纷乱的界面设计，希望让终端使用者尽可能在 android 手机有个一致的操作体验。ActionBar 过去最多人使用的两大套件就是 ActionBarSherlock 以及官方提供在 support library v 7 里的 AppCompat。

Action bar被包含在所有的使用Theme.Hole主题的Activity（或者是这些Activity的子类）中。

删除actionbar

如果不想用ActionBar，那么只要在theme主题后面" .NoActionBar", 就可以了。

由于现在用的不多了，所以就一带而过了。

> ## Toolbar

Toolbar 是android 5.0的推出的，放在了v7包中作为控件，它是为了取代actionbar而产生的，意味着官方在某些程度上认为 ActionBar 限制了 android app 的开发与设计的弹性，而在 material design 也对之做了名称的定义：App bar

## 一、ToolBar
1. 概述
Google在2015的IO大会上发布了系列的Material Design风格的控件。其中ToolBar是替代ActionBar的控件。由于ActionBar在各个安卓版本和定制Rom中的效果表现不一，导致严重的碎片化问题，ToolBar应运而生。

>与ActionBar区别

显示效果跟ActionBar并没有区别。
优点：自定义视图的操作更加简单，状态栏的颜色可以调（Android 4.4以上）。

2. 使用方法

2.1 导包(配置Gradle)

    `compile 'com.android.support:appcompat-v7:22.0.0'`

2.2 在`AndroidManifest.xml`设置此Activity的主题
```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
```
注意：务必将ActionBar消除，否则Activity将会崩溃

2.3 重定义主题(颜色等信息)

Android 4.4及以上，在values文件夹中新建`values-v19.xml`，内容如下

```xml
<resources>
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="windowActionBar">false</item>
        <item name="android:windowNoTitle">true</item>
        <!--Toolbar颜色-->
        <item name="colorPrimary">@android:color/holo_blue_bright</item>
        <!-- 状态栏颜色 -->
        <item name="colorPrimaryDark">@android:color/holo_blue_bright</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:windowTranslucentStatus">true</item>
    </style>
</resources>
```

Android 4.4以下：

```xml
<resources>
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="windowActionBar">false</item>
        <item name="android:windowNoTitle">true</item>
        <!--Toolbar颜色-->
        <item name="colorPrimary">@color/Indigo_colorPrimary</item>
    </style>
</resources>
```

2.4 ToolBar.xml文件中定义

```xml
<?xml version="1.0" encoding="utf-8"?>  
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"  
    android:id="@+id/toolbar"  
    android:layout_height="wrap_content"  
    android:layout_width="match_parent"  
    android:background="?attr/colorPrimary"  //设置toolbar的背景颜色
    android:minHeight="?android:attr/actionBarSize">  <!-- 最小高度 -->
</android.support.v7.widget.Toolbar>  
android:fitsSystemWindows="true" 防止ToolBar与StatusBar融合
```

2.5 java代码中实现

```java
private Toolbar mToolbar;  
   private WebFragment mWebFragment;  
   private long mBeforeTime;  
   @Override  
   protected void onCreate(Bundle savedInstanceState) {  
       super.onCreate(savedInstanceState);  
       setContentView(R.layout.activity_main);
       if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {  //版本检测
           SystemBarTintManager tintManager = new SystemBarTintManager(this);  
           mToolbar = (Toolbar) findViewById(R.id.toolbar);  
           tintManager.setStatusBarTintEnabled(true);  //更改状态栏设置
           setSupportActionBar(mToolbar);  //将ToolBar设置成ActionBar
           tintManager.setStatusBarTintResource(android.R.color.holo_blue_bright);  
       }  
       getSupportActionBar().setHomeButtonEnabled(true); //设置返回键可用  
       getSupportActionBar().setDisplayHomeAsUpEnabled(true);  
}
```

2.6 参考文章

http://blog.csdn.net/qq284565035/article/details/47086439

http://www.itstrike.cn/Question/9ae75549-b316-4844-938e-c22c0f4e6692.html

