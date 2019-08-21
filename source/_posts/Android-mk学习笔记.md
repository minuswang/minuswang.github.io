---
title: Android.mk学习笔记
date: 2019-08-06 11:10:34
tags:
---

 hardware\led\led\Android.mk

LOCAL_PATH := $(call my-dir)
{
    首先需要指定LOCAL_PATH变量，用于查找源文件
    上面的语句的意思是将LOCAL_PATH变量定义成本文件所在目录路径
}

#Android.mk中可以定义多个编译模块，每个编译模块都是以include $(CLEAR_VARS)开始
#以include $(BUILD_XXX)结束。
include $(CLEAR_VARS) //开始
{
    问： CLEAR_VARS是什么？？
    答： CLEAR_VARS由编译系统提供，指定让GNU MAKEFILE为你清除除LOCAL_PATH以外的所有LOCAL_XXX变量，
    如LOCAL_MODULE，LOCAL_SRC_FILES，LOCAL_SHARED_LIBRARIES，LOCAL_STATIC_LIBRARIES等。    
}
#包含的头文件
LOCAL_C_INCLUDES += \
    hardware/led/include/

LOCAL_PRELINK_MODULE := false  // :=是赋值的意思
LOCAL_MODULE_PATH := $(TARGET_OUT_SHARED_LIBRARIES)/hw 
{
    //LOCAL_MODULE_PATH: 最后的目标安装路径
    
    问： TARGET_OUT_SHARED_LIBRARIES是什么？
    答： 在build/core/envsetup.mk中定义。TARGET_OUT_SHARED_LIBRARIES:= $(TARGET_OUT)/lib
    TARGET_ROOT_OUT：表示根文件系统。
    TARGET_OUT：表示system文件系统。
    TARGET_OUT_DATA：表示data文件系统。
    
}
LOCAL_SHARED_LIBRARIES := liblog  //LOCAL_SHARED_LIBRARIES中加入所需要链接的动态库（*.so）的名称
LOCAL_SRC_FILES := led.cpp
LOCAL_MODULE :=led.default   //LOCAL_MODULE表示模块最终的名称
#LOCAL_MODULE的定义规则，led后面跟有default，led.default能够保证我们的模块总能被硬象抽象层加载到
LOCAL_MODULE_TAGS := eng
include $(BUILD_SHARED_LIBRARY) // 结束
{
    include $(BUILD_STATIC_LIBRARY)表示编译成静态库
    include $(BUILD_SHARED_LIBRARY)表示编译成动态库。
    include $(BUILD_EXECUTABLE)表示编译成可执行程序
}

LOCAL_SRC_FILES中加入源文件路径，LOCAL_C_INCLUDES 中加入所需要包含的头文件路径，
LOCAL_STATIC_LIBRARIES加入所需要链接的静态库（*.a）的名称，
LOCAL_SHARED_LIBRARIES中加入所需要链接的动态库（*.so）的名称，
LOCAL_MODULE表示模块最终的名称，BUILD_EXECUTABLE表示以一个可执行程序的方式进行编译。



hardware\led\Android.mk
include $(call all-subdir-makefiles)


frameworks\base\services\forlinx_led_jni\LedService.cpp
frameworks\base\services\forlinx_led_jni\Android.mk
{

    LOCAL_PATH:= $(call my-dir) //LOCAL_PATH变量定义成本文件所在目录路径
    include $(CLEAR_VARS)
    
    # [optional, user, eng] 
    # eng = required
    # optinal = no install on target
    LOCAL_MODULE_TAGS := eng
    {
        LOCAL_MODULE_TAGS :=optional -->> out/target/product/OK6410/symbols/system/
        LOCAL_MODULE_TAGS :=eng    -->> out/target/product/Ok6410/system/
    }
    
    # This is the target being built.
    LOCAL_MODULE:= libforlinx_runtime
    
    # Target install path.
    LOCAL_MODULE_PATH := $(TARGET_OUT_SHARED_LIBRARIES)
    
    # All of the source files that we will compile.
    LOCAL_SRC_FILES:= \
        LedService.cpp
    
    # All of the shared libraries we link against.
    LOCAL_SHARED_LIBRARIES := \
        libandroid_runtime \
        libcutils \
        libhardware \
        libhardware_legacy \
        libnativehelper \
            libsystem_server \
        libutils \
        libui \
            libsurfaceflinger_client
        
    
    # Also need the JNI headers.
    LOCAL_C_INCLUDES += \
        $(JNI_H_INCLUDE) \
            hardware/led/include
    
    
    # No specia compiler flags.
    LOCAL_CFLAGS +=
    {
        LOCAL_CFLAGS += -O3 -fstrict-aliasing -fprefetch-loop-arrays
        通过设定编译器操作，优化级别，-O0表示没有优化,-O1为缺省值，-O3优化级别最高
        LOCAL_CFLAGS += -W -Wall
        LOCAL_CFLAGS += -fPIC -DPIC
        LOCAL_CFLAGS += -O2 -g -DADB_HOST=1 -Wall -Wno-unused-parameter
        LOCAL_CFLAGS += -D_XOPEN_SOURCE -D_GNU_SOURCE -DSH_HISTORY
        LOCAL_CFLAGS += -DUSEOVERLAY2
        根据条件选择相应的编译参数
    }
    
    # Don't prelink this library.  For more efficient code, you may want
    # to add this library to the prelink map and set this to true.
    LOCAL_PRELINK_MODULE := false
    {
        Prelink利用事先链接代替运行时链接的方法来加速共享库的加载，它不仅可以加快起动速度，还可以减少部分内存开销，
        是各种Linux架构上用于减少程序加载时间、缩短系统启动时间和加快应用程序启动的很受欢迎的一个工具。程序运行时的
        动态链接尤其是重定位(relocation)的开销对于大型系统来说是很大的。
        动态链接和加载的过程开销很大，并且在大多数的系统上, 函数库并不会常常被更动, 每次程序被执行时所进行的链接
        动作都是完全相同的，对于嵌入式系统来说尤其如此。因此，这一过程可以改在运行时之前就可以预先处理好，即花一些时间
        利用Prelink工具对动态共享库和可执行文件进行处理，修改这些二进制文件并加入相应的重定位等信息，节约了本来在程序
        启动时的比较耗时的查询函数地址等工作，这样可以减少程序启动的时间，同时也减少了内存的耗用。 
        Prelink的这种做法当然也有代价：每次更新动态共享库时，相关的可执行文件都需要重新执行一遍Prelink才能保
        证有效，因为新的共享库中的符号信息、地址等很可能与原来的已经不同了，这就是为什么 android framework代码一改动，
        这时候就会导致相关的应用程序重新被编译。
        这种代价对于嵌入式系统的开发者来说可能稍微带来一些复杂度，不过好在对用户来说几乎是可以忽略的。
    }
    
    include $(BUILD_SHARED_LIBRARY)
}


今天添加一个feature时，import了IPackageManager类，编译时提示找不到这个类。而这个类确实已经在编译好的源码中存在，并且在其他项目中引用时编译通过。只有一个项目不通过。后来通过对比发现这个项目的Android.mk文件中有这么一行，注释之后，编译通过。
/# 若是添加，编译时会忽略源码隐藏的API，导致编译失败
LOCAL_SDK_VERSION := current

拓展一下，android sdk中的两类特殊的API
Internal API
Hide API

1 . Internal API

翻译为内部API，理解为供sdk内部使用的API。
这类接口最初打算就是不对外公开的，有点private的意思。

2 . Hide API

在源码中看到使用@hide 标记的方法或类，就是hide的。
这类接口本意是要公开，但是当前阶段仍然不稳定或未开发完成。所以暂时不推荐开发者调用。但是，他们都可以被使用

两种方式：

通过反射
使用这些接口的情况是：目标接口已经编译进framework.jar(或其它对应的jar包)中，但是自己应用在编译时会报错。所以只要自己应用编译时同样添加相关jar包的引用，便可以编译通过。
