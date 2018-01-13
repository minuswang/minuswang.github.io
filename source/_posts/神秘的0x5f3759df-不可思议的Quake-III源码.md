---
title: 神秘的0x5f3759df 不可思议的Quake III源码
date: 2017-11-01 16:01:17
tags:
---
[原文链接](http://www.matrix67.com/blog/archives/362)

Quake III公开源码后，有人在`game/code/q_math.c`里发现了这样一段代码。它的作用是将一个数开平方并取倒，经测试这段代码比`(float)(1.0/sqrt(x))`快4倍：

```c
float Q_rsqrt( float number )
{
  long i;
  float x2, y;
  const float threehalfs = 1.5F;

  x2 = number * 0.5F;
  y  = number;
  i  = * ( long * ) &y;  // evil floating point bit level hacking
  i  = 0x5f3759df - ( i >> 1 ); // what the fuck?
  y  = * ( float * ) &i;
  y  = y * ( threehalfs - ( x2 * y * y ) ); // 1st iteration
  // y  = y * ( threehalfs - ( x2 * y * y ) ); // 2nd iteration, this can be removed

  #ifndef Q3_VM
  #ifdef __linux__
    assert( !isnan(y) ); // bk010122 - FPE?
  #endif
  #endif
  return y;
}
```
`code/common/cm_trace.c`中也出现了这样一段解释`sqrt(x)`的函数，与上面的代码唯一不同的就是这个函数返回的是`number*y`：

```c
/*
================
SquareRootFloat
================
*/
float SquareRootFloat(float number) {
    long i;
    float x, y;
    const float f = 1.5F;

    x = number * 0.5F;
    y  = number;
    i  = * ( long * ) &y;
    i  = 0x5f3759df - ( i >> 1 );
    y  = * ( float * ) &i;
    y  = y * ( f - ( x * y * y ) );
    y  = y * ( f - ( x * y * y ) );
    return number * y;
}
```

这样的代码速度肯定飞快，我就不用多说了；但算法的原理是什么呢？其实说穿了也不是很神，程序首先猜测了一个接近`1/sqrt(number)`的值，然后两次使用牛顿迭代法进行迭代。根号`a`的倒数实际上就是方程`1/x^2 – a = 0`的一个正实根，它的导数是`-2/x^3`。运用牛顿迭代公式`x' = x – f(x)/f'(x)`，式子化简为`x' = x * (1.5 – 0.5a * x^2)`。迭代几次后，`x`的值将趋于`1/sqrt(a)`。

但这段代码真正牛B的是那个神秘的`0x5f3759df`，因为`0x5f3759df – (i >> 1)`出人意料地接近根号`y`的倒数。人们都不知道这个神秘的常数是怎么来的，只能把它当作神来膜拜。这个富有传奇色彩的常数到底咋回事，很少有人说得清楚。这篇论文[FAST INVERSE SQUARE ROOT](http://www.matrix67.com/data/InvSqrt.pdf)比较科学地解释了这个常数。



## quake3源码分析

quake3 游戏demo演示版，没有玩过quake3游戏的或想了解quake3画面的 ， 可到此处下载，
http://ishare.iask.sina.com.cn/search.php?key=q3ademo&from=index&format=
这两个任意都可直接下载，点击直接下载，50M，下载极快。

https://github.com/barrettcolin/quake3.gpl.old quake3源码的一个下载地址

quake3包含了渲染部分，人工智能，网络部分等

 主要代码文件如下（我自己大致的分法）

>工具部分的代码

lcc文件包是用来编译quake 代码里面的game,cgame,ui的代码，卡马克对开源的lcc进行了改写，再使用qasm来进行编译成为了qvm文件，在游戏时候调用

qasm 文件夹将game.cgame.ui等等文件夹里的东西汇编为机器码，形成一个虚拟机，执行速度快，也可跨平台。 

radiant生成这个地图map文件

q3map场景处理部分，那是将.map文件经历bsp树分割，，pvs,光线追踪等等处理后生成bsp树这个地图

>引擎代码

code内是雷神之锤3引擎主要的代码,

我自己将之分为上层和下层，上层主要是游戏逻辑部分，下层音频，视频，渲染器，文件系统，网络系统

上层主要调用下层的函数，分模块，这样有利于随意换掉任意一个渲染模块，或者任意换掉一个音频处理部分

>上层

AAS是一种文件格式，保存的机器人行走的导航图，

bspc主要生成aas地图的

botlib是用于机器人部分，用于单人模式的一部分，其中包括加载BSP文件，加载模型,机器人AI部分等。

q3ui是quake3的用户界面部分。  

cgame 部分是用于客户端程序的逻辑部分,

game 是用于服务器端的逻辑部分，分两部分，1单人时运行时的人工智能部分，模拟网络连线对战。2网络连线时，运行网络部分代码。

>下层 

 client主要键盘，有鼠标驱动的实现方式，以及键盘键值的绑定，输入输出 ，套接字以及控制台代码的使用等等

 sever 网络部分，一个游戏运行时1 单人时运行时仿照多人部分的服务器端 2,LAN或者Internet部分这个服务器部分实现 

 Render渲染器主要是 先用q3关卡编辑器quake radiant ，将编辑者在电脑上画出的关卡部分，存为.map文件，

再将.map文件格式用q3map部分编译成以.bsp为后缀名的文件。  再将.bsp文件加载到内存中，

用quake3 code文件夹中render部分进行渲染。根据玩家所站的的位置进行pvs选择，筛选玩家所能看到的部分进行渲染。

sound加载wav部分代码，解码进行播放

[ quake3（雷神之锤3） 是一个血腥暴力，疯狂发泄射击游戏！](http://blog.csdn.net/tailiangliang/article/details/6953533)

[quake3.gpl.old/code/game/q_math.c](https://github.com/barrettcolin/quake3.gpl.old/blob/master/code/game/q_math.c)