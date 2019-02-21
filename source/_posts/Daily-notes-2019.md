---
title: Daily notes 2019
date: 2019-01-08 16:40:34
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

11. 