---
title: Android Support V4/V7/V13
date: 2017-10-23 16:13:41
tags:
---
## 一、Android Support V4, V7, V13

1. Android Support V4, V7, V13是什么？
本质上就是三个java library。

2. 为什么要有support库？
如果在低版本Android平台上开发一个应用程序，而应用程序又想使用高版本才拥有的功能，就需要使用Support库。

3. 三个Support 库的区别和作用是什么？

>Android Support v4 是最早（2011年4月份）实现的库。用在Android1.6 (API lever 4)或者更高版本之上。它包含了相对V4, V13大的多的功能。

例如：Fragment，NotificationCompat，LoadBroadcastManager，ViewPager，PageTabAtrip，Loader，FileProvider 等。
[详细API 参考](http://developer.android.com/reference/android/support/v4/app/package-summary.html)

Gradle引用方法：
`compile 'com.android.support:support-v4:21.0.3'`

>Android Support v7:  
这个包是为了考虑Android2.1(API level 7) 及以上版本而设计的，但是v7是要依赖v4这个包的，也就是如果要使用，两个包得同时被引用。v7支持了Action Bar。

Gradle引用方法:

`compile 'com.android.support:appcompat-v7:21.0.3'`

>Android Support v13:这个包的设计是为了android 3.2及更高版本的，一般我们都不常用，平板开发中能用到。

4. 如何使用？
首先要保证Android Support Library 被安装
![](http://developer.android.com/images/tools/sdk-manager-support-libs.png)

然后在工程中增加（例如 support-v4 Library）
在ADT中需要按照以下步骤：
```
1、右击当前工程，查找Properties
2、选择Java Build Path
3、选择Libraries tab，点击右边面板的Add External JARs按钮
4、选择android-support-v4.jar文件，这一文件的常见路径为:YOUR_DRIVE\android-sdks\extras\android\support\v4\android-support-v4.jar
5、完成添加后，选择Order and Export标签，确认即可。
```

5. v4、v7、v13 的例子代码

4v : `<sdk>/extras/android/support/samples/Support4Demos/`

7v : `<sdk>/extras/android/support/samples/Support7Demos/`

13v : `<sdk>/extras/android/support/samples/Support13Demos/`

App Navigation: `<sdk>/extras/android/support/samples/SupportAppNavigation/`

## 二、app包下的fragment 和 v4 包下的fragment的区别

1. 最低支持版本不同
`android.app.Fragment` 兼容的最低版本是`android:minSdkVersion="11"` 即3.0版
`android.support.v4.app.Fragment` 兼容的最低版本是`android:minSdkVersion="4"` 即1.6版

>尽量不要用app包中的fragment，因为这个是在3.0之后才有的，支持的版本太高，在低版本中是是用不了的。`android.support.v4.app.Fragment`可以兼容到1.6的版本

2. 需要导jar包
`fragment android.support.v4.app.Fragment` 需要引入包`android-support-v4.jar`

3. 在Activity中取的方法不同
`android.app.Fragment`使用 `(ListFragment)getFragmentManager().findFragmentById(R.id.userList)` 获得 ，继承Activity
`android.support.v4.app.Fragment`使用 `(ListFragment)getSupportFragmentManager().findFragmentById(R.id.userList)` 获得 ，需要继承`android.support.v4.app.FragmentActivity`

>getFragmentManager()和getSupportFragmentManager()区别
当api>=14时，使用getFragmentManager()。有时候使用Fragment的时候为了兼容低版本，会引入android-support-v4.jar支持包，这时候需要使用getSupportFragmentManager()

4. 关于这两个fragment使用<fragment>标签的问题

`app.fragment`和`v4.fragment`都是可以使用<fragment>标签。在使用的时候如果是`app.fragment`则没有什么特殊的地方，继承`Activity`即可。

当`v4.fragment`使用<fragment>标签的时候就要特别注意了：
当这个`Activity`的布局中有<fragment>标签的时候，这个`Activity`必须继承`FragmentActivity`，否则就会报错。

5. 为什么还要用V7呢？V4向下兼容的版本不是更多吗？

V7版本不是为了提供一些V4提供不了的内容，它不是补丁。V7以后你如果创建一个工程，它给你创建的都是FragmentActivity了。也就是说，以后Android开发所有的界面都可以是碎片模式了。V7是一种新的框架和更优解决方案.

## 三、Fragment使用V4包的注意事项

1. 使用V4包下的Fragment, FragmentManager也需要从v4中获取

2. 当前Activity要使用FragmentActivity

3. 通过getSupportFragmentManager()获取FragmentManager对象

>注意：

>Android Support v4: 
这个包是为了照顾1.6及更高版本而设计的 这个包是使用最广泛的 eclipse新建工程时 默认带有

>Android Support v7: 
这个包是为了考虑照顾2.1及以上版本而设计的 v7是依赖于v4包的

>Android Support v13:
这个包是为了考虑照顾3.2及以上版本而设计的 我们平常手机开发不常用，平板中能用到

MainActivity:

```
import android.os.Bundle;
import android.support.v4.app.FragmentActivity;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentTransaction;
import android.view.Menu;
//使用V4包时继承的是FragmentActivity
public class MainActivity extends FragmentActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //使用V4包,用getSupportFragmentManager()得到FragmentManager的对象
        FragmentManager manager = getSupportFragmentManager();
        FragmentTransaction transaction = manager.beginTransaction();
        transaction.add(R.id.layout_container_fragment, new MyFragment());
        transaction.commit();       
    }   
}
```
activity_main布局

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.ljavadroid.day16_fragmentv4.MainActivity" 
    >

    <LinearLayout
        android:id="@+id/layout_container_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:background="#50ff"
        />
</RelativeLayout>
```


自定义MyFragment继承Fragment

```
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class MyFragment extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        return inflater.inflate(R.layout.activity_fragmnet, container, false);
    }
}
```

MyFragment布局

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical" >
  <TextView 
      android:id="@+id/fragment_v4"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="我是V4包Fragment"
      android:background="#50ff"
      />  
</LinearLayout>
```

>上面说到了supportV4、V7、V13的区别及作用，那我们要怎么来使用它们呢，其实在android studio里面非常简单，看下面的代码:

```
android {     
compileSdkVersion 22     
buildToolsVersion "22.0.1"     
 defaultConfig {        
 applicationId "com.example.scarlettli.viewpagerdemo"        
 minSdkVersion 15         
targetSdkVersion 22         
versionCode 1         
versionName "1.0"     }     
buildTypes {         
release {             
minifyEnabled false             
proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'         }     } }  
dependencies {    
 compile fileTree(dir: 'libs', include: ['*.jar'])     
//引入support V4     
compile 'com.android.support:support-v4:19.0.0'     
//这是引入support V7     
compile 'com.android.support:appcompat-v7:22.1.1'     
//引入support V13    
 compile 'com.android.support:support-v13:21.0.+' }
```