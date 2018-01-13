---
title: 内存泄露自动探测神器—LeakCanary
date: 2017-11-09 14:31:04
tags:
---
## LeakCanary

内存泄露自动探测神器，它是一个Android和Java的内存泄露检测库，由Square开源

>“A small leak will sink a great ship.” - Benjamin Franklin

>Getting started

在项目的`build.gradle`文件添加：

```java
 dependencies {
   debugCompile 'com.squareup.leakcanary:leakcanary-android:1.3'
   releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3'
 }
```

在`Application`类添加：

```java
public class ExampleApplication extends Application {

  @Override public void onCreate() {
    super.onCreate();
    LeakCanary.install(this);
  }
}
```

当在你的`debug`构建过程中出现内存泄露时，`LeakCanary`将会自动展示一个通知栏。

使用RefWatcher观察引用什么时候应该被GC：
```java
RefWatcher refWatcher = {...};
// We expect schrodingerCat to be gone soon (or not), let's watch it.
refWatcher.watch(schrodingerCat);
```
`LeakCanary.install() `返回一个先前配置的`RefWatcher`，它也安装一个`ActivityRefWatcher`以便在`Activity.onDestroy()`被调用后自动检测`Activity`是否出现泄露

```java
public class ExampleApplication extends Application {

  public static RefWatcher getRefWatcher(Context context) {
    ExampleApplication application = (ExampleApplication) context.getApplicationContext();
    return application.refWatcher;
  }

  private RefWatcher refWatcher;

  @Override public void onCreate() {
    super.onCreate();
    refWatcher = LeakCanary.install(this);
  }
}
```

你可以使用`RefWatcher`观察`Fragment`的内存泄露
```java
public abstract class BaseFragment extends Fragment {

  @Override public void onDestroy() {
    super.onDestroy();
    RefWatcher refWatcher = ExampleApplication.getRefWatcher(getActivity());
    refWatcher.watch(this);
  }
}
```
How does it work?
```java
1.RefWatcher.watch()创建一个KeyedWeakReference去检测对象；
2.接着，在后台线程，它将会检查是否有引用在不是GC触发的情况下需要被清除的；
3.如果引用引用仍然没有被清除，将会转储堆到.hprof文件到系统文件中(it them dumps the heap into a .hprof file stored on the app file system.)
4.HeapAnalyzerService是在一个分离的进程中开始的，HeapAnalyzer通过使用HAHA解析heap dump;
5.由于一个特殊的引用key和定位的泄露引用，HeapAnalyzer可以在heap dump中找到KeyedWeakReference；
6.如果有一个泄露，HeapAnalyzer计算到GC Roots的最短的强引用路径，然后创建造成泄露的引用链；
7.结果在`app`的进程中传回到`DisplayLeakService`，并展示泄露的通知消息；
```
怎样拷贝l`eak trace`?

你可以在`Logcat`上看`leak trace`:
```java
In com.example.leakcanary:1.0:1 com.example.leakcanary.MainActivity has leaked:
* GC ROOT thread java.lang.Thread.<Java Local> (named 'AsyncTask #1')
* references com.example.leakcanary.MainActivity$3.this$0 (anonymous class extends android.os.AsyncTask)
* leaks com.example.leakcanary.MainActivity instance

* Reference Key: e71f3bf5-d786-4145-8539-584afaecad1d
* Device: Genymotion generic Google Nexus 6 - 5.1.0 - API 22 - 1440x2560 vbox86p
* Android Version: 5.1 API: 22
* Durations: watch=5086ms, gc=110ms, heap dump=435ms, analysis=2086ms
```
你也可以分享`leak trace`和`heap dump`文件通过`action bar`的菜单。

`My leak is caused by the SDK implementation!`

随着时间过去越来越多熟知的内存泄露问题被制造商在`android`开源项目中修复。当这样一个泄露发生时，你能作为一个应用程序开发员来修复它。出于这个原因，`LeakCanary`有一个内置`Android`泄露的列表`AndroidExcludedRefs.java`来监测它，如果你找到一个新的泄露，请用`leaktrace`创建一个`issue`,标明设备和`Android`版本。如果你提供一个`heap dump`的文件链接就更好了。

这是对于新发布的`Android`版本来说是特别重要的。你有机会更早地帮助检测新的内存泄露，这有益于整个`Android`社区。
开发版快照可以通过`Sonatype's snapshots repository`找到。

`Beyond the leak trace`

有时`leak trace`不够清晰，你需要使用`MAT`和`YourKit`深入研究`heap dump`。这里教你怎样在`head dump`找到泄露的实例：

```java
1.找出包com.squareup.leakcanary.KeyedWeakReference下所有实例;
2.对于每个实例，考虑它的key域;
3.找到 KeyedWeakReference 有一个key域等于被LeakCanary报出的引用的key;
4.KeyedWeakReference的referent域是程序中内存泄露的对象；
5.从那时起，问题就转到你的手上了。一个好的开始是找到最短的GC roots的路径(排除弱引用)
```
自定义`Customizing`

图标和标签` Icon and label`

`DisplayLeakActivity`自带一个默认的`icon`和`label`，可以通过提供的`R.drawable.leak_canary_icon`和`R.string.leak_canary_display_activity_label`来修改：

```xml
res/
  drawable-hdpi/
    __leak_canary_icon.png
  drawable-mdpi/
    __leak_canary_icon.png
  drawable-xhdpi/
    __leak_canary_icon.png
  drawable-xxhdpi/
    __leak_canary_icon.png
  drawable-xxxhdpi/
    __leak_canary_icon.png
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="__leak_canary_display_activity_label">MyLeaks</string>
</resources>
```
储存leak traces

DisplayLeakActivity可以在你的app目录保存7个heap dumps和leak traces，你可以在app中通过提供R.integer.__leak_canary_max_stored_leaks的值改变这个数量：
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <integer name="__leak_canary_max_stored_leaks">20</integer>
</resources>
```
上传到服务器

你可以改变默认的行为去上传leak trace并heap dump到你选择的服务器。
创建你自己的AbstractAnalysisResultService，最容易的方式是在你debug的源码中继承DefaultAnalysisResultService：

```java
public class LeakUploadService extends DefaultAnalysisResultService {
  @Override protected void afterDefaultHandling(HeapDump heapDump, AnalysisResult result, String leakInfo) {
    if (!result.leakFound || result.excludedLeak) {
      return;
    }
    myServer.uploadLeakBlocking(heapDump.heapDumpFile, leakInfo);
  }
}
```
确定在你正式发布的Application类中使RefWatcher失效：

```java
public class ExampleApplication extends Application {

  public static RefWatcher getRefWatcher(Context context) {
    ExampleApplication application = (ExampleApplication) context.getApplicationContext();
    return application.refWatcher;
  }

  private RefWatcher refWatcher;

  @Override public void onCreate() {
    super.onCreate();
    refWatcher = installLeakCanary();
  }

  protected RefWatcher installLeakCanary() {
    return RefWatcher.DISABLED;
  }
}
```

在你的debug的Application类创建一个定制的RefWatcher：

```java
public class DebugExampleApplication extends ExampleApplication {
  protected RefWatcher installLeakCanary() {
    return LeakCanary.install(app, LeakUploadService.class);
  }
}
```

不要忘记了在你debug的manifest中注册service:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    >
  <application android:name="com.example.DebugExampleApplication">
    <service android:name="com.example.LeakUploadService" />
  </application>
</manifest>
```