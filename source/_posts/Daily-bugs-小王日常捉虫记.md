---
title: Daily bugs-小王日常捉虫记
date: 2017-10-20 11:25:46
tags:
---
《Daily bugs-小王日常捉虫记》

## BUG-0001.Error:Failed to resolve: annotationProcessor
```java
repositories {
    maven { url 'https://jitpack.io' }
}
```
原版本
```java
allprojects {
    repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
    }
}
```
[0001参考链接](https://stackoverflow.com/questions/40703394/errorfailed-to-resolve-annotationprocessor)

## BUG0002.Android studio3.0升级稳定版 Cannot set the value of read-only property ‘outputFile’ for ApkVariantOutputImpl_Decorated{apkData=Main{type=MAIN

>解决方案：
1. Change `each -> to all`
2. Change `output.outputFile -> to outputFileName`

before:
```java
android.applicationVariants.all { variant ->

    variant.outputs.each { output ->
        def finalVersionCode =v10000 + versionCode
        output.versionCodeOverride = finalVersionCode
        output.outputFile = new File(
             output.outputFile.parent,       output.outputFile.name.replace(".apk","-${finalVersion}.apk"))
    }
}

```
>after:
```java
android.applicationVariants.all { variant ->

    variant.outputs.all { output ->
        def finalVersionCode = 10000 + versionCode
        output.versionCodeOverride = finalVersionCode
        outputFileName = new File(
             output.outputFile.parent,
             outputFileName.replace(".apk", "-${finalVersionCode}.apk"))
    }
}
```

>apply:
```java
    //Begin 添加apk输出的的路径，修改 生成的文件名、去除lint检查
    applicationVariants.all { variant ->
        variant.outputs.all { output ->
            //def outputFile = output.outputFile
            def apkFileName = ""
            if (outputFileName != null && outputFileName.endsWith('app-debug.apk')) {
                //修改apk文件名
                apkFileName = appName + "_" + defaultConfig.versionName + "_debug.apk"
            }
            if (outputFileName != null && outputFileName.endsWith('app-release-unsigned.apk')) {
                //修改apk文件名
                apkFileName = appName + "_" + defaultConfig.versionName + "_release_unsigned.apk"
            }
            outputFileName = new File(apkOutDir.equals("") ? outputFileName : apkOutDir, apkFileName)
            //outputFileName.delete()

        }
    }
```
[0002参考链接](https://stackoverflow.com/questions/44655405/change-apk-filename-in-gradle)

## BUG0003.Error: style attribute '@android:attr/windowExitAnimation' not found

All the problem was solved already.

Cause of the problem：

There are two modules, A_module, B_module.

B_module has a style:
```xml
<style name="my_style”> 
 <item 
  name="@android:windowEnterAnimation">@anim/anim_toast_show</item> 
 <item 
 name="@android:windowExitAnimation">@anim/anim_toast_hide</item>
</style>
```
If B_module compile`(':A_module')`
Build or Clean, report a error location in A_module->Res->values->styles:

> Error:(94, 5) style attribute '@android:attr/windowExitAnimation' not found
> Error:(94, 5) style attribute '@android:attr/windowEnterAnimation' not found

>Solution:
Removing the `"@"` at the start of the item name.
```xml
<item name="@android:windowEnterAnimation">@anim/anim_toast_show</item>
<item name="@android:windowExitAnimation">@anim/anim_toast_hide</item>
```
to:
```xml
<item name="android:windowEnterAnimation">@anim/anim_toast_show</item>
<item name="android:windowExitAnimation">@anim/anim_toast_hide</item>
```

[0003参考链接](https://stackoverflow.com/questions/45952607/error-style-attribute-androidattr-windowexitanimation-not-found)

## BUG0004.
## BUG0005.
## BUG0006.
## BUG0007.
## BUG0008.
## BUG0009.