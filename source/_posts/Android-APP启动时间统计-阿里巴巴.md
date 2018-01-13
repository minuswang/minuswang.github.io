---
title: Android APP启动时间统计(阿里巴巴)
date: 2017-11-08 13:25:31
tags:
---

## [CSDN原理系列-yangxi_001](http://blog.csdn.net/yangxi_pekin)

作为 Android 开发者，想必多多少少要接触启动速度优化相关的事情，当用户越来越多，产品的功能也随着迭代越来越多，App 逐渐变得臃肿是一件很常见的现象，甚至可以说是不可避免的现象，随之而来的工作就是优化 App 性能，其中最主要的一项就是启动速度优化。但本文的主角并不是启动速度优化，而是启动时间统计。

## 一 启动类型

工欲善其事，必先利其器。想要优化 App 的启动速度，必须有准确衡量启动时间的方法，否则优化完之后效果怎样，自己都不知道，说出去别人也不信服不是。在做 App 启动时间统计之前，当然必须弄明白有哪些启动类型，每种启动类型的特点。通常来说，在安卓中应用的启动方式分为以下几种：

>冷启动：

当启动应用时，后台没有该应用的进程，这时系统会重新创建一个新的进程分配给该应用，这个启动方式就是冷启动。冷启动因为系统会重新创建一个新的进程分配给它，所以会先创建和初始化 `Application 类`，再创建和初始化 `MainActivity`类，最后显示在界面上。

>热启动：

当启动应用时，后台已有该应用的进程（例：按back键、home键，应用虽然会退出，但是该应用的进程是依然会保留在后台，可进入任务列表查看），所以在已有进程的情况下，这种启动会从已有的进程中来启动应用，这个方式叫热启动。热启动因为会从已有的进程中来启动，所以热启动就不会走 `Application` 这步了，而是直接走 `MainActivity`，所以热启动的过程不必创建和初始化 `Application`，因为一个应用从新进程的创建到进程的销毁，`Application` 只会初始化一次。

>首次启动：

首次启动严格来说也是冷启动，之所以把首次启动单独列出来，一般来说，首次启动时间会比非首次启动要久，首次启动会做一些系统初始化工作，如缓存目录的生产，数据库的建立，SharedPreference的初始化，如果存在多 `dex` 和插件的情况下，首次启动会有一些特殊需要处理的逻辑，而且对启动速度有很大的影响，所以首次启动的速度非常重要，毕竟影响用户对 App 的第一映像。

## 二 本地启动时间的统计方式

如果是本地调试的话，统计启动时间还是很简单的，通过命令行方式即可：

`adb shell am start -w packagename/activity`
输出的结果类似于：

```
$ adb shell am start -W com.speed.test/com.speed.test.HomeActivity
Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.speed.test/.HomeActivity }
Status: ok
Activity: com.speed.test/.HomeActivity
ThisTime: 496
TotalTime: 496
WaitTime: 503
Complete
```

`WaitTime` 返回从 `startActivity` 到应用第一帧完全显示这段时间. 就是总的耗时，包括前一个应用 `Activity pause` 的时间和新应用启动的时间；
`ThisTime `表示一连串启动 `Activity` 的最后一个 `Activity` 的启动耗时；
`TotalTime` 表示新应用启动的耗时，包括新进程的启动和 `Activity `的启动，但不包括前一个应用`Activity pause`的耗时。
开发者一般只要关心 `TotalTime` 即可，这个时间才是自己应用真正启动的耗时。

## 三 线上启动时间的统计方式

当 App 发到线上之后，想要统计 App 在用户手机上的启动速度，就不能通过命令行的方式进行统计了，基本上都是通过打 Log 的方式将启动时间发送上来。那么在什么位置加启动时间统计的 Log 就尤为重要，Log 添加的位置直接决定启动时间统计的是否准确，同样也会影响启动速度优化效果的判断。要想找到合适准确的位置记录启动时间的 Log，就需要了解应用的启动流程，和各个生命周期函数的调用顺序。下面来分析下到底在什么位置打 Log 记录启动时间比较合适。

>应用的主要启动流程

关于 App 启动流程的文章很多，文章底部有一些启动流程相关的参考文章，这里只列出大致流程如下：

```java
通过 `Launcher` 启动应用时，点击应用图标后，`Launcher` 调用 `startActivity `启动应用。
`Launcher Activity` 最终调用 `Instrumentation` 的 `execStartActivity` 来启动应用。
`Instrumentation` 调用 `ActivityManagerProxy` (`ActivityManagerService `在应用进程的一个代理对象) 对象的 `startActivity` 方法启动 `Activity`。
到目前为止所有过程都在` Launcher `进程里面执行，接下来` ActivityManagerProxy` 对象跨进程调用` ActivityManagerService `(运行在 `system_server` 进程)的 startActivity方法启动应用。
`ActivityManagerService` 的 `startActivity` 方法经过一系列调用，最后调用`zygoteSendArgsAndGetResult` 通过 socket 发送给 zygote 进程，zygote 进程会孵化出新的应用进程。
zygote 进程孵化出新的应用进程后，会执行 ActivityThread 类的 main 方法。在该方法里会先准备好 Looper 和消息队列，然后调用 attach 方法将应用进程绑定到 ActivityManagerService，然后进入 loop 循环，不断地读取消息队列里的消息，并分发消息。
ActivityManagerService 保存应用进程的一个代理对象，然后 ActivityManagerService 通过代理对象通知应用进程创建入口 Activity 的实例，并执行它的生命周期函数。
总结过程就是：用户在 Launcher 程序里点击应用图标时，会通知 ActivityManagerService 启动应用的入口 Activity， ActivityManagerService 发现这个应用还未启动，则会通知 Zygote 进程孵化出应用进程，然后在这个应用进程里执行 ActivityThread 的 main 方法。应用进程接下来通知 ActivityManagerService 应用进程已启动，ActivityManagerService 保存应用进程的一个代理对象，这样 ActivityManagerService 可以通过这个代理对象控制应用进程，然后 ActivityManagerService 通知应用进程创建入口 Activity 的实例，并执行它的生命周期函数。
```
>生命周期函数执行流程

上面的启动流程是 Android 提供的机制，作为开发者我们需要清楚或者至少了解其中的过程和原理，但我们并不能在这过程中做什么文章，我们能做的恰恰是从上述过程中最后一步开始，即 ActivityManagerService 通过代理对象通知应用进程创建入口 Activity的实例，并执行它的生命周期函数开始，我们的启动时间统计以及启动速度优化也是从这里开始。下面是 Main Activity 的启动流程：

```
-> Application 构造函数
-> Application.attachBaseContext()
-> Application.onCreate()
-> Activity 构造函数
-> Activity.setTheme()
-> Activity.onCreate()
-> Activity.onStart
-> Activity.onResume
-> Activity.onAttachedToWindow
-> Activity.onWindowFocusChanged
```

如果打 Log 记录 App 的启动时间，那么至少要记录两个点，一个起始时间点，一个结束时间点。

>起始时间点

起始时间点比较容易记录：如果记录冷启动启动时间一般可以在 `Application.attachBaseContext()` 开始的位置记录起始时间点，因为在这之前 `Context`还没有初始化，一般也干不了什么事情，当然这个是要视具体情况来定，其实只要保证在 App 的具体业务逻辑开始执行之前记录起始时间点即可。如果记录热启动启动时间点可以在 `Activity.onRestart()` 中记录起始时间点。

>结束时间点

结束时间点理论上要选在 App 显示出第一屏界面的时候，但是在什么位置 App 显示出第一屏界面呢？网上很多文章说在 Activity 的 onResume 方法执行完成之后，Activity 就对用户可见了，实际上并不是，一个` Activity `走完`onCreate onStart onResume `这几个生命周期之后，只是完成了应用自身的一些配置,比如 `Activity `主题设置 `window` 属性的设置 `View` 树的建立，但是其实后面还需要各个` View` 执行 `measure layout draw`等。所以在 OnResume 中记录结束时间点的 Log 并不准确，大家可以注意一下上面流程中最后一个函数 `Activity.onWindowFocusChanged`，下面是它的注释：

```xml

`Activity.onWindowFocusChanged`

/**
*Called when the current {@link Window} of the activity gains or loses
* focus.  This is the best indicator of whether this activity is visible
* to the user.  The default implementation clears the key tracking
* state, so should always be called.
...
*/
```
通过注释我们可以看到，这个函数是判断 activity 是否可见的最佳位置，所以我们可以在 `Activity.onWindowFocusChanged` 记录应用启动的结束时间点，不过需要注意的是该函数，在 `Activity` 焦点发生变化时就会触发，所以要做好判断，去掉不需要的情况。

总结

花了很多篇幅介绍启动时间统计，我觉得还是有必要的，还是那句话：工欲善其事，必先利其器，准备工作做的充分，做事自然有理有据。


参考：
https://www.zhihu.com/question/35487841
http://blog.adisonhyh.com/blog/2015/03/27/androidxiang-mu-xing-neng-you-hua-zhi-qi-dong-shi-jian/
http://www.woaitqs.cc/android/2016/06/21/activity-service.html
http://blog.csdn.net/zhaokaiqiang1992/article/details/49428287
http://www.cloudchou.com/android/post-805.html
http://www.jianshu.com/p/72059201b10a