---
title: Android系统分区理解及分区目录细解
date: 2019-09-10 15:26:26
tags:
---

Android 通常有以下分区：
System分区: 就是我们刷ROM的分区
Data分区:   分区就是我们装APK的分区
Catch分区：是缓存分区
SDCard分区：就是挂载的SD卡。

可以在adb中使用df 来查看分区情况。

	1、跨分区不能用 MV命令来拷贝。但是可以用CP命令。如PWD，当前目录为：/data/local/tmp 。此目录下有个busybox和1.txt文件。则利用CP命令拷贝如下：./busybox cp1.txt /system.
	2、Android的用户组有 System， root， shell
	3、怎么样才能操作分区
	分区操作是需要权限的。一般来说System分区的权限限制得比较严，Data分区限制比较严，用户可以操作的目录有local，app目录。比如/data/local/tmp.
	data分区常用目录：app , system , data ,local，misc 其中system，local可以进入并使用ls等命令。data，app可以进入，但不能用ls命令。
	data/data目录：存放的是所有APK程序数据的目录，每个APK对就一个自己的Data目录，就是在data/data/目录下，会产生一个跟Package一样的目录。如有一个APK,它的包名叫com.test.hello则,在data/data/目录下会有一个com.test.hello的目录,这个APK只能操作此目录,不能操作其它APK的目录.这个在LINUX下叫做用户进程只能操作自己的进程目录.
	data/app目录：用户安装的APK放在这里。我们如果把APK放入这个文件夹下面的话，就算安装好了。这就叫静默安装。不用管APK文件里面的lib目录下的库文件，系统会自动帮我们放入调用库的。
	data/system目录下面有packages.xml ,packages.list,appwidgets.xml, 等等一些记录手机安装的软件，Widget等信息。
	data/misc目录：保存WIFI帐号，VPN设置信息等。如保存了一个WIFI连接帐号，则此目录下的WIFI目录下面可以查看到。
	system分区常用目录： app , lib, xbin, bin , media，framework.
	system/app目录：存放系统自带的APK。没有测试过是否将APK放入到System/app目录下，也是静默安装APK。？
	system/lib目录：存放APK程序用到的库文件。
	system/bin目录和system/xbin目录：存放的是shell命令。
	system/framework目录：启用Android系统所用到框架，如一些jar文件。
	4、 Android下面的目录都是有权限的,要操作目录都需要有此权限才能操作,如果没有,则使用chomd777来修改.如果是分区根目录,如/data分区, /system分区.都没有权限,则需要重新挂载. 使用 mount -oremount XX

作　　者：Kproxy
出　　处：https://www.cnblogs.com/airoot/p/4131891.html