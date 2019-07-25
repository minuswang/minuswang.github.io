---
title: Android mk and so issue
date: 2019-07-25 14:56:05
tags:
---

Android JNI 的使用，之前很少使用这个，所以这次用到占用了很多时间搞，不熟悉C++好痛苦的说啊～

Android JNI如何用呢？

下面这段拷贝别人的，是没问题的：
常用有两种方法：

1、在windows/linux下，首先把so动态库文件 编译出来(通过NDK)，然后再 拷贝到android的工程里 libs/armeabi/下，如果libs/armeabi 不存在，那就自己创建，最后用eclipse编译本项目，这样将自动把so文件打包到apk里；eclipse在打包的时候会根据文件名的命名规则（lib****.so）去打包so文件，开头和结尾必须分别为“lib”和“.so”，否则是不会打包到apk文件中的。

2、在android源码里，使用mm命令编译apk工程。

   在project根目录下创建一个Android.mk，文件内容如：

LOCAL_PATH:= $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := user  
LOCAL_SRC_FILES := $(call all-subdir-java-files)
LOCAL_PACKAGE_NAME := jnisample
LOCAL_JNI_SHARED_LIBRARIES := libtest
include $(BUILD_PACKAGE)
include $(LOCAL_PATH)/jni/Android.mk
\#include $(call all-makefiles-under,$(LOCAL_PATH)) 
LOCAL_JNI_SHARED_LIBRARIES := libtest 就是把so文件放到apk文件里的libs/armeabi里，而include $(LOCAL_PATH)/jni/Android.mk为了编译so文件。

把工程文件放到SDK platform/packages/apps目录下,然后进入该目录,命令里输入mm,进行该工程的编译,这样编译出来的apk，在libs/armeabi文件夹里面包含so文件
--------------------- 

LOCAL_MODULE_CLASS用于制定LOCAL_MODULE_PATH的路径所在。
如果在Android.mk没有直接明确LOCAL_MODULE_PATH 的话，需要通过以下规则来自动生成base_rules.mk：
 LOCAL_MODULE_PATH := $(strip $(LOCAL_MODULE_PATH))
 ifeq ($(LOCAL_MODULE_PATH),)
         #LOCAL_MODULE_CLASS :=
   LOCAL_MODULE_PATH := $($(my_prefix)OUT$(partition_tag)_$(LOCAL_MODULE_CLASS))
   $(info *******//////////$(LOCAL_MODULE_PATH))
 ifeq ($(strip $(LOCAL_MODULE_PATH)),)
     $(error $(LOCAL_PATH): unhandled LOCAL_MODULE_CLASS "$(LOCAL_MODULE_CLASS)")
   endif
 endif

在不同的Android.mk文件中，对于模块include()不同的编译类型选项，比如对于Library或者app,execut等在调用对应的处理mk文件时，会默认就指定当前的LOCAL_MODULE_CLASS的值，比如EXECUTABLES、SHARED_LIBRARIES等。所以在自己编写的Android.mk可不显示的指定LOCAL_MODULE_CLASS的值。但当遇到include $(BUILD_PREBUILT)的预编译选项时不会指定模块模块编译输出的类型CLASS，需要在自己编写的Android.mk中明确指定LOCAL_MODULE_CLASS的值如ETC/APP等，使其值为非空，从而帮助系统确定LOCAL_MODULE_PATH的路径，比如最终编译输出LOCAL_MODULE_PATH  := $(TARGRT_OUT_ETC)。

[Android JNI以及打包so文件到apk中](https://blog.csdn.net/shichaosong/article/details/22050029)

[Android 动态加载多个so--包含so引用so](https://www.jianshu.com/p/00e7d382725b)

[Android.mk编译so，提示ninja: error](https://blog.csdn.net/u010867436/article/details/88821480)

[系统级应用apk集成和mk文件配置介绍](https://www.jianshu.com/p/683138f155c9)
