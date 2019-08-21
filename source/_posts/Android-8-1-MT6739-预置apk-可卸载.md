---
title: Android 8.1 MT6739 预置apk 可卸载
date: 2019-07-25 14:24:40
tags:
---

1. 在 package/apps/3rd-party 下面以需要预置的apk名字新建文件夹，以预置 facebook 为例。
2. 将 facebook.apk 放到 package/apps/3rd-party/facebook 中。
3. 在 package/apps/3rd-party/facebook 下面创建文件 Android.mk，文件内容如下
4. 在 build/make/target/product/core.mk或device/mediatek/common/device.mk中添加PRODUCT_PACKAGES += facebook

LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
# Module name should match apk name to be installed
LOCAL_MODULE := facebook
LOCAL_MODULE_TAGS := optional
LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
LOCAL_CERTIFICATE := PRESIGNED

include $(BUILD_PREBUILT)

请注意：若需要apk作为32bit的apk运行，则需要在Android.mk中定义
LOCAL_MULTILIB :=32
如需指定apk预置路径，需在Android.mk中定义LOCAL_MODULE_PATH :=xxxxx
实现apk可卸载在
vendor/mediatek/proprietary/frameworks/base/data/etc/pms_sysapp_removable_system_list.txt中添加facebook包名即可。
最后重新build整个工程。

在MTK Android O1平台预置apk为可卸载时。预置到旧的路径system/vendor/operator/app会编译报错，"You cannot install files to out/target/product/xxx/system/vendor while building a separate vendor.img!"改为预置到vendor/operator/app就可以编译通过，预置可卸载成功,恢复出厂设置可恢复。

旧的

LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
\# Module name should match apk name to be installed
LOCAL_MODULE := Test
LOCAL_MODULE_TAGS := optional
LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
\#LOCAL_PREBUILT_JNI_LIBS := \
\#LOCAL_PRIVILEGED_MODULE := true
LOCAL_MODULE_PATH := $(TARGET_OUT)/vendor/operator/app
LOCAL_CERTIFICATE := PRESIGNED
include $(BUILD_PREBUILT)

修改LOCAL_MODULE_PATH为

LOCAL_MODULE_PATH := $(TARGET_OUT_VENDOR)/operator/app
以上

[Android 预置第三方应用可卸载功能的实现](https://blog.csdn.net/pirionFordring/article/details/83586037)

为了实现FMradio可卸载方案：
1. 将FM apk由system/priv-app目录移至system/pre-install目录，同时将fm依赖的libfmjni.so添加到public lib库 
但此方法引起了新的CTS fail项
2. 经过与MTK沟通,建议将libfmjni.so打包到app目录下，libfmjni.so与其他的系统so有依赖，所以需要把所有有依赖关系的so都打包进来
最后总共依赖了50多个so库，大小约为8M。本来FM apk的大小只有800K
3. 为实现此需求FM多占用了8M的内存空间，GO项目本来就内存吃紧，而这里的卸载方案本来就不是真正从内存中删除

MTK有将FM预置位可卸载的方案，要求是将FM依赖的libfmjni.so添加到public lib库（否则FM直接crash），但是这个会导致CTS failed。
需要修改的就是将libfmjni.so添加APK内部去，但是由于这个库本身也有其他依赖，就需要将全部依赖添加到APK里面去。
这样的结果就是：APK大小增大，是否还有其他由于依赖导致的功能问题，这个暂时无法预见

编译到/vendror/app_backup中就可以可卸载,
LOCAL_MODULE_PATH := $(PRODUCT_OUT)/vendor/app_backup

