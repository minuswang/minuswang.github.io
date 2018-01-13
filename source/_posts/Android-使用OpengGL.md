---
title: Android-使用OpengGL
date: 2017-11-20 17:25:28
tags:
---

OpenGL ES 是一套专为嵌入式设备设计的三维图形开发接口标准, 脱胎于 OpenGL. OpenGL 家族以及他的主要竞争对手 Direct3D 都只是一套 API(说白了就是N个函数)标准, 显卡厂商根据标准开发出驱动,有了驱动 (当然还有SDK) 我们程序员就可以开始对显卡编程制作出绚丽的3D游戏.程序员在开发游戏过程中发现不同游戏都要实现相同功能, 比如资源管理, 声音播放, 渲染. 于是很自然程序员就开始制作自己的工具完成这些重复工作, 游戏引擎就诞生了. 其中游戏引擎的一个工作就是抽象 OpenGL , D3D 这些底层API, 你再也不需要分别为OpenGL, D3D 编码, 只要调用引擎的抽象接口, 剩下的就交给引擎了.你用 Unity 发布 Android, iOS 的游戏, 那他就绑定和调用 OpenGL ES发布 PC 游戏, 就是D3D 或者 OpenGL

OpenGL ES可以认为是OpenGL的子集，主要用于移动端的设备。从中衍生出来的还有webGL,主要用于浏览器端的渲染。OpenGL和DirectX是目前比较通用的底层库，各大显卡厂商也会为此在不同的显卡上编写对应的driver，这些driver负责做最终的机器specific的实现。Unity是一个引擎，引擎里面包含很多模块，比如渲染模块，声音模块，物理模块等等。这里的渲染模块在上层会有个通用的API层供调用，而无需管底层到底是跑了gl的实现还是dx的实现。

[Android-使用OpengGL实现的Canvas进行绘制(简单介绍)](http://www.jianshu.com/p/df1499d19cea)

[如何封装 opengl 流程 – 以为android-opengl-canvas例](http://blog.csdn.net/fzl562410663/article/details/53156210)

[Android OpenGL入门](http://blog.csdn.net/huachao1001/article/details/51545450)

[为什么你的canvas那么慢？浅析Android的canvas性能](http://www.jianshu.com/p/5a0c61c286e6)

![](https://camo.githubusercontent.com/5d619b8e479e2c618150278cad3eb1c8e6a1d36d/68747470733a2f2f7261772e6769746875622e636f6d2f637469616f2f637469616f2e6769746875622e696f2f6d61737465722f696d616765732f617070732f62696c692e706e673f7261773d74727565)

[Bilibili--DanmakuFlameMaster--android上开源弹幕解析绘制引擎项目](https://github.com/Bilibili/DanmakuFlameMaster)

