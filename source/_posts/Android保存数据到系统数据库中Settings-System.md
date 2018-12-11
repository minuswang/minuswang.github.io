---
title: Android保存数据到系统数据库中Settings.System
date: 2018-12-11 14:40:54
tags:
---

有时候我们一些设置属性要保存到本地，防止软件卸载后配置属性消失。比如我今天就有个修改手机蓝牙名称显示的功能，但是只需要修改一次，并且进行标记，并保存这个标识，如果程序卸载，重新安装也不需要再修改，这里就是要保存这个标识，来判断是否需要修改名称。 

要实现这个功能，SharePreference是不行的了，本地文件file保存呢，是可以的，但是比较麻烦，而且有被删掉的可能。这里介绍一个比较简单、实用的方法：这个方法就是把标识保存到系统配置（系统数据库）文件中，即使程序卸载也不影响，系统的配置文件。

代码示例：
```
import android.provider.Settings;   

//设置系统配置文件中的数据，第一个参数固定的，但是需要上下文，第二个参数是保存的Key，第三个参数是保存的value
 boolean changeBluetoothName = Settings.System.putInt(getContentResolver(), "changeBluetoothName", 1);

//获取系统配置文件中的数据，第一个参数固定的，但是需要上下文，第二个参数是之前保存的Key，第三个参数表示如果没有这个key的情况的默认值
 int blueFlag = Settings.System.getInt(getContentResolver(), "changeBluetoothName", 0);

```
这里的保存数据类似`SharePreference`，保存的是键值对`key-value`，但是这里的数据是保存到系统文件中的，跟程序无关，但是在程序中是可以能够获取的。 上面示例代码保存的是int数据，也可以使用putString保存String数据，putByte保存byte数据等等。。。 
同样的，获取也是根据保存的数据类型来获取。

运行前还要添加一个权限，否则会报错
 ` <uses-permission android:name="android.permission.WRITE_SETTINGS"/>`

据说这个系统配置文件的位置是： 
`/data/data/com.android.providers.settings/databases/`

数据库控制的代码： 
`frameworks\base\packages\SettingsProvider\src\com\android\providers\settings`的DatabaseHelper类里面 
控制代码我们是看不见的啦，如果你有Android系统源码是可以看见的（不是SDK源码哦）
