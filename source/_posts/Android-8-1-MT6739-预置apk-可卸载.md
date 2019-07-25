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

