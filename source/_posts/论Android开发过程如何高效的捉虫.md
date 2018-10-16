---
title: 论Android开发过程如何高效的捉虫
date: 2018-10-16 09:57:06
tags:
---

## 一、Monkey 测试

### 1.1 Monkey 一个运行在模拟器或者实际设备中的测试工具

`adb shell monkey [options]` //不指定options 将事件任意发送到目标设备中所有应用

`adb shell monkey -p your.package.name -v 500` //启动指定应用程序并向其发送500个伪随机事件

-p 参数 指定一个或几个包，Monkey只允许启动这些包里的Activity，每个 -p选项只能指定一个包，要指定多个包就要使用多个-p选项

### 1.2 Monkeyrunner 提供API 让用户开发程序控制Android设备以及模拟器

1. 不依赖于Monkey Monkey直接运行在设备或者模拟器的adb shell中，Monkeyrunner工具运行在计算机中，通过API发送特定命令和事件来控制设备

2. Monkeyrunner工具使用的是Jyphon（Java语言实现的Python）

3. 可以通过Python调用Monkeyrunner的API开发一整套系统来控制Android设备。还可以使用标准Python来调用Android的adb工具
 
## 二、