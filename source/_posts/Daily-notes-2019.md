---
title: Daily notes 2019
date: 2019-09-12
tags:
---
1. Linux beyond compare 免试用
>$ rm -rf ~/.config/bcompare/registry.dat

2. 
第一步：查询所有安装的apk  `adb shell pm list packages`  --->找到自己要备份出来的apk的包名
第二步：查询apk路径（以Settings.apk为例） `adb shell pm path com.android.settings`
第三步：取出apk `adb pull /data/app/com.android.settings-cY2nu6V9nroDo1Jp_wLvDQ==/base.apk Settings.apk`

3. Android P编译apk问题
问题：最近拿到一个9.0的android平台，编译一个应用无法安装，并且跟以前android版本有个区别，编译出来会多一个odex的文件来。

原因：用mm编译apk时，会在out目录下生成.apk和.odex两个文件，是为了加快运行速度，把dex分出来做的预处理。但是一般开发时需要mm编译出一个完整的apk，直接用adb进行安装就可以进行调试。

解决：在要编译的package包中（例如/packages/apps/Settings），找到对应的Android.mk文件，在里面加一个属性，LOCAL_DEX_PREOPT  ：= false ；就可编译出完整的apk了

4. ActivityManager: Start proc 27854 (class)/u0a66 for service?

ActivityManager is one of the system services started by the system_server process during Android boot up. ActivityManager is responsible for managing the application components (Activities, Services, BroadcastReceivers)

27854：This is the Process Identifier (PID).

This is the User Identifier (UID). u0 means the system supports multiple users (0 is for "primary" user), a66 - is the application ID (66) set on its installation.

"Start proc XXXXX: (Class)/u0a66 for service (Class)" means ActivityManager started a process due to a call to start/bind the service, while "Start proc XXXXX: (package)/u0a66 for broadcast (Class)" means starting a process due to intent received by the broadcast receiver. 

It means that the app was started when a service hosted within the app was launched (from some external code ) and in the second case it meant that your app launched in response to a broadcast. Rest of the identifiers in the log have already been explained by the answer 

servicemanager runs as uid 1000 (system) and thus a call to open() is bound by the normal filesystem permissions (i.e., what you see in an ls -l). In other words, you can only create a file where uid 1000 has the proper filesystem permissions.

5. App dies with “Sending signal.” but no exception or other info

Signal 9 is SIGKILL, which will terminate a process immediately (no handlers inside the process will run). From the log line, the process is killing itself, so its not an external agent that is issuing the SIGKILL.

My guess (and its really a guess) is that the memory management code running inside your process (as part of the infrastructure, not code that you wrote) is deciding that you've exhausted some resource and the only recourse is to die. I would expect there to be more messages before this point is reached in the log, so it may be worth browsing the log history to see if there are useful warnings from the process before this point.

The line immediately before this is a GC log, which implies that some sort of memory resource is running low. But it looks like the heaps are not full, so failing allocations seems unlikely. You can still get allocation failures if the object being allocated was too large to fit on the heap, or fragmentation prevented it from being allocated. I'd expect to see more relevant log messages in this case, though.

I think capturing more of the log (perhaps filtering it by your app's PID if necessary) will help you make progress.

6. [adb shell am force-stop com.sohu.sohuvideo](https://blog.csdn.net/ztguang/article/details/52020357)

7. [【Android端 APP 启动时长获取】启动时长获取方案及具体实施](https://www.cnblogs.com/keke-xiaoxiami/p/6180563.html)

8. [Android安全开发之安全使用HTTPS](https://www.cnblogs.com/alisecurity/p/5939336.html)

9. 启动拨号界面 或 在Contact里点击搜索对应暗码 
工程模式  原始 mtk 平台的进入方法为：启动拨号界面，输入 *#*#3646633#*#* 即可进入
查询手机IMEI   *#06#
切换国家码  ####6020#   查看暗码  ####59930#

10. 如果想获取手机的当前系统语言，可以通过Locale类获取，主要方法：Locale.getDefault().getLanguage()，返回的是es或者zh；通过Locale.getDefault().getCountry()获取当前国家或地区，返回为CN或US；如果当前手机设置为中文-中国，则使用此方法返回zh-CN，同理可得到其他语言与地区的信息

// BEGIN 2019-07-10
11. Java正确URL解码方式：URLDecoder.decode
Java调用 URLDecoder.decode(str, "UTF-8"); 抛出以上的异常，其主要原因是% 在URL中是特殊字符，需要特殊转义一下，
解决办法：使用%25替换字符串中的%号
url = url.replaceAll("%(?![0-9a-fA-F]{2})", "%25");
String urlStr = URLDecoder.decode(url, "UTF-8");

12. [对于httpclient在android5.0及以下的系统中，访问多证书的服务器时出现hostname验证不过](https://blog.csdn.net/happy668ha/article/details/79227980)

13. Android设置屏幕亮度的两种方式
最近项目需要设置Android设备的亮度，本来以为很简单的功能，结果搞了好久才搞定，Android在这里埋了一个坑。下面具体说下：
Android系统的亮度值是0~255，数据类型为int型。设置屏幕亮度有两种方式，一种是通过WindowManager去设置当前界面的亮度——注意，是当前界面，不是系统的亮度。这种方式代码如下

private void setLight(Activity context, int brightness) {
        WindowManager.LayoutParams lp = context.getWindow().getAttributes();
        lp.screenBrightness = Float.valueOf(brightness) * (1f / 255f);
        context.getWindow().setAttributes(lp);
}

需要注意其中的context的类型是Activity，不能是Context。这种方式的特点，是**只在当前设置的界面生效**，离开此界面后，屏幕亮度受亮度自动调节的开关控制。换句话说，用这种方式设置当前界面的亮度时，会使亮度自动调节失效。只有离开此界面，亮度自动调节继续生效。这种方式适用某些特殊的，需要高亮显示界面。
 
第二种方式时通过修改系统数据库来设置亮度。代码如下
public void saveBrightness(Activity activity, int brightness) {
        Uri uri = Settings.System.getUriFor(Settings.System.SCREEN_BRIGHTNESS);
        Settings.System.putInt(contentResolver, Settings.System.SCREEN_BRIGHTNESS, brightness);
        activity.getContentResolver().notifyChange(uri, null);
}
这种方式的特点是可以修改系统亮度，即使退出当前界面，设置的亮度值依然生效。这种方式设置的亮度值受亮度自动调节开关的影响。即开关关闭时，此值生效；开关关闭时，此值其实并没有什么卵用。需要注意的时，设置时需要向系统数据库写入数据，因此需要相应的权限才行。这两种方式各有各的作用与使用场景，使用的时候需要千万注意，不要混淆。功能虽小，坑却不小，特作文以记之
[Android 屏幕亮度调节](https://www.jianshu.com/p/70c1ddf87df4)
[Android之调节屏幕亮度（文末附源码）Kotlin](https://www.jianshu.com/p/0984f450dc0b)

14. [Android安全开发之安全使用HTTPS](https://www.cnblogs.com/alisecurity/p/5939336.html)

15. 支持和禁止分屏功能
android:resizeableActivity="true|false" 通过AndroidManifest中进行配置，来支持或者禁止分屏功能

16. Activity 配置项里的 android:configChanges。
列出 Activity 将自行处理的配置更改。在运行时发生配置更改时，默认情况下会关闭 Activity 然后将其重新启动，但使用该属性声明配置将阻止 Activity 重新启动。 Activity 反而会保持运行状态，并且系统会调用其 onConfigurationChanged() 方法 

17. 查看Android中的AlarmManager事件

adb中提供了查看alarmmanager的命令
adb shell dumpsys alarm

通过这命令可以查看被放到定时队列里面的事件

    RTC_WAKEUP #1: Alarm{52c2ad84 type 0 com.sina.weibo}
    type=0 when=+3m1s330ms repeatInterval=0 count=0
    operation=PendingIntent{529fa514: PendingIntentRecord{52a7b220 com.sina.weibo broadcastIntent}}

18. 
我们这边抓log有两种方法，1是联系人中输入####3333#，打开log，home键返回，这样log一直开着了，你就做操作就行，等出问题了再调出这个log界面点击保存按钮。log存在了设备根目录的mtklog下面

第2种就是adb logcat -v time > XXX.txt，一直抓着。

19. 
