---
title: Sample of Android Reverse Engineering
date: 2019-03-26 09:48:02
tags:
---

[逆向小米rom层应用做碎片化适配](https://www.jianshu.com/p/6f313b4876ab)

[逆向小米系统Settings.apk过程记录](https://blog.csdn.net/feiduclear_up/article/details/79443205)

[Android反编译－小米桌面逆向过程记录](https://www.jianshu.com/p/25801a5f9613)

[笔记 odex ， vdex ，art 文件区别](https://blog.csdn.net/pirionfordring/article/details/83862239)

# 1.vdex
package 直接转化的 可执行二进制码 文件：

1.第一次开机就会生成在/system/app/<packagename>/oat/ 下；

2.在系统运行过程中，虚拟机将其 从 “/system/app” 下  copy  到 “/data/davilk-cache/” 下

# 2.odex 
odex 是从vdex 这个文件中 提取了部分模块生成的一个新的 可执行二进制码 文件 ， odex 从vdex 中提取后，vdex 的大小就减少了。

1.第一次开机就会生成在/system/app/<packagename>/oat/ 下

2.在系统运行过程中，虚拟机将其 从 “/system/app” 下  copy  到 “/data/davilk-cache/” 下

3.odex + vdex = apk 的全部源码 （vdex 并不是独立于odex 的文件 odex + vdex 才代表一个apk ）

# 3.art
odex 进行优化 生成的 可执行二进制码 文件，主要是apk 启动的热点函数相关地址的记录，方便寻址相关； 

1.第一次开机不会生成在/system/app/<packagename>/oat/ 下，以后也不会；

2.odex 文件在运行时，虚拟机会计算函数调用频率，进行函数地址的修改；

3.最后在/data/davilk-cache/ 由虚拟机生成；

4.生成art 文件后，/system/app 下的odex 和 vdex 会无效，即使你删除，apk也会正常运行

5.push 一个新的apk file 覆盖之前/system/app 下apk file ，会触发PKMS 扫描时下发force_dex flag ，强行生成新的vdex 文件 ，覆盖之前的vdex 文件，由于某种机制，这个新vdex 文件会copy到/data/dalvik-cache/下，于是art 文件也变化了