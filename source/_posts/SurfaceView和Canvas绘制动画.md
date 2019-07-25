---
title: SurfaceView和Canvas绘制动画
date: 2019-07-25 14:18:21
tags:
---

其实每个View中都有Canvas可以用来绘制动画，只需要在这个View中重载onDraw()方法就可以，但是SurfaceView类是一个专门用来制动动画的类。

Canvas(中文叫做”画布”)就和HTML5中的canvas标签一样可以在一定区域内自由绘制图形。Canvas+SurfaceView制作的动画与View Animation和Property Animation这类动画比起来更加适合大量的集中播放的动画，比如游戏画面、相机的图像显示等。

因为SurfaceView通常会在另一个专门的线程中不断重绘界面，所以不像其他动画那样要在主线程(UI线程)中播放动画的同时还要消耗一定的流畅度用来响应用户输入。

在使用SurfaceView时需要注意下面这些要点：

1)每个SurfaceView都需要一个SurfaceHolder对象来处理这个SurfaceView的生命周期和获取这个SurfaceView的Canvas对象，可以通过调用SurfaceView的getHolder()方法来获取它的SurfaceHolder对象。

2)使用SurfaceView时一般是通过继承SurfaceView的方式来实现，可以顺便implements两个接口，分别是Runnable和SurfaceHolder.Callback。第二个接口需要重载三个函数，这三个函数就是SurfaceView的生命周期处理了，可以通过SurfaceHolder对象的addCallback()方法把实现好的Callback对象传进去。

3)在使用SurfaceView的Canvas时一定要记得加锁同步，因为不能让画布同时绘制多个图案，通过调用这个SurfaceView的SurfaceHolder对象的lockCanvas()就可以做到这一点。绘制完毕后在调用SurfaceHolder对象的unlockCanvasAndPost()方法就可以解锁并更新。

下面给出了一个用SurfaceView和Canvas绘制动画的例子，一般直接复制上就可以运行看到效果

[原文链接](https://blog.csdn.net/qq_20738965/article/details/53363111)

[映客-飘赞动画](https://blog.csdn.net/w958796636/article/details/52796113)

[SVG动画](https://blog.csdn.net/u013651026/article/details/81223931)

[SVGA](http://svga.io)

