---
title: Android常见知识点以及流行框架梳理
date: 2018-12-14 14:27:03
tags: Android
---

## BAT大咖助力 全面升级Android面试

>第1章 课程介绍（本课程专为初中级同学面试复习）

本课程专为初中级程度同学面试准备的系统复习指南，本章带你了解面试过程中会遇到的问题，个人应该摆正的心态，以及面试官最为看重你的解决问题的思路。关于框架面试专题课程请移步到：http://coding.imooc.com/class/157.html
1-1 课前必读（不看会错过一个亿）
1-2 课程介绍

>第2章 Android开发岗位技术要求和面试前的准备

本章对各个级别Android开发工程师的招聘需求进行深入分析，并带大家清晰完整的了解面试复习与准备思路，做到有的放矢，有侧重点的进行复习与准备。
2-1 Android开发岗位技术要求和面试前的准备

>第3章 Android基础相关面试问题

Android基础虽然简单，但长时间不梳理，如果被问到，却回答不上了，会丢分又丢人，所以本章主要就为大家梳理Android基础相关的面试问题与答案，主旨就是能了解其中的原理，从而在面试中遇到同类问题能给出相应的思路。

3-1 Activity先导：lifeCycle、通信、启动模式、源码
3-2 Activity任务栈&4种形态
3-3 Activity正常情况下生命周期回调方法
3-4 Activity异常情况下生命周期分析
3-5 Activity之间数据通信3种方式
3-6 Activity向Fragment传递数据：Bundle／定义方法
3-7 Fragment传递数据给Activity：接口回调
3-8 Activity与Service通信：利用serviceConnection
3-9 Activity和Service通信：intent／callback+handler
3-10 Activity启动模式：标准／singleTop应用场景
3-11 Activity启动模式：SingleTask应用场景
3-12 Activity启动模式：SingleInstance应用场景
3-13 Service先导：线程、IntentService、Binder等
3-14 Service和线程的区别和应用场景
3-15 如何管理Service生命周期
3-16 IntentService 和Service的异同：handlerThread
3-17 启动和绑定Service服务先后次序问题
3-18 序列化：Parcelable和Serializable差异
3-19 binder应用：AIDL如何创建
3-20 binder机制通信：AIDL生成java文件详细分析
3-21 静态&动态注册使用、特点、应用场景
3-22 webview安全漏洞面试问题讲解


>第4章 异步消息处理机制相关面试问题

异步消息处理在面试中是一定会被问到的，在实战过程中也是非常重要的一个开发手段，我们会从Handler、Asynctask给大家详细讲解，主旨就是能了解其中的原理，从而在面试中遇到同类问题能给出相应的思路。

4-1 异步消息-Handler的四大组件和运作机制
4-2 异步消息-Handler源码：looper、handler、消息队列如何捆绑？
4-3 异步消息-looper如何管理MessageQueue
4-4 异步消息-Handler机制总结&面试要点
4-5 异步消息-Asynctask的使用和4点注意事项
4-6 异步消息-Asynctask源码：线程池&SerialExecutor创建
4-7 异步消息-并发编程：FutureTask&Callable补充
4-8 异步消息-Asynctask源码：postResult方法
4-9 异步消息-Asynctask源码：Asynctask如何实例化
4-10 异步消息-Asynctask源码：线程池的execute&executeOnExecutor

>第5章 开源框架相关面试问题

开源框架可以说是一个面试的试金石，如果对于主流框架比较熟悉，并能画出流程图，会让面试官对你刮目相看。本章主要带大家分析的开源框架有网络框架：okhttp retrofit ,图片加载框架gilde，主旨就是能了解其中的原理，从而在面试中遇到同类问题能给出相应的思路。...
5-1 okhttp同步请求：RealCall、拦截器、dispatcher调度器
5-2 okhttp异步请求：AsyncCall、线程池、双向队列
5-3 okhttp内部线程池创建：SynchronousQueue和ThreadPoolExecutor
5-4 okhttp调度器：dispatcher、AsyncCall、promoteCalls
5-5 retrofit数据解析器、url、网络请求适配器等
5-6 retrofit源码解读：serviceMethod、工厂模式、回调执行器等
5-7 retrofit源码解读：callAdapter、GsonConverter等
5-8 retrofit源码解读：网络请求接口实例创建
5-9 retrofit源码解读：serviceMethod和retrofit核心代码
5-10 retrofit源码解读：serviceMethod域赋值¶meterHander
5-11 retrofit源码解读：OKhttpCall和Rxjava平台适配
5-12 retrofit源码解读：静态代理&封装OKhttp&总结
5-13 rxjava观察者模式、基本使用
5-14 rxjava+retrofit实现轮询&handler的实现方式
5-15 rxjava缓存读取&Lrucache源码解读
5-16 rxjava如何实现从内存、磁盘缓存中读取数据
5-17 glide创建实例、error、load等
5-18 glide压缩图片、两种裁剪图片区别
5-19 glide内存、磁盘缓存，优先级使用
5-20 butterknife注解框架面试问题讲解

>第6章 View绘制相关面试问题

本章主要从view的绘制、listview绘制和缓存来给大家进行讲解面试套路，主旨就是能了解其中的原理，从而在面试中遇到同类问题能给出相应的思路。

6-1 view绘制：decorview、Window、surface等概念
6-2 view绘制：测量过程&measureSpec
6-3 view绘制：layout布局阶段&FrameLayout
6-4 view绘制：draw阶段&dispatchDraw实现
6-5 listview绘制和缓存原理：convertview／viewHolder
6-6 listview的RecycleBin机制：缓存scrapView、fillActiveViews等
6-7 listview性能优化5个关键点

>第7章 Android项目构建相关面试问题（适合初级工程师）

本章内容适合：初级工程师，开发过程中项目的构建是很重要一环，也是检验你是不是一个合格的android开发工程师的标志，面试中也会经常问到，在这里我们主要通过Android的编译打包、Proguard混淆、git的使用、gradle、渠道包这五个部分给大家分析，带大家了解Android构建的全过程，从而轻松应对这类问题的各种面试与开发。...

7-1 android studio目录结构面试问题讲解
7-2 android项目构建面试问题讲解
7-3 git版本控制面试问题讲解
7-4 gradle面试问题讲解
7-5 proguard代码混淆面试问题讲解

>第8章 Android异常与性能优化相关面试问题（适合中级工程师）

本章内容适合：中级工程师，随着现在的android开发业务逻辑不断扩大，对于手机的性能也提出了很高的要求，所以一款app在性能上如果能区别其他app也将脱颖而出，同样如果候选人能对性能优化很熟悉，也将在面试中脱颖而出，本章主要从UI卡顿、内存管理、内存泄漏这几个角度带大家分析性能优化。...

8-1 anr异常面试问题讲解
8-2 oom异常面试问题讲解
8-3 bitmap面试问题讲解
8-4 ui卡顿面试问题讲解
8-5 内存泄漏：内存溢出、原因、Android4种内存泄漏
8-6 内存泄漏：handler、单例造成的内存泄漏
8-7 内存泄漏：总结
8-8 内存管理面试问题讲解
8-9 冷启动优化面试问题讲解
8-10 其他优化面试问题讲解

>第9章 热门前沿知识相关面试问题（适合中级工程师）

本章内容适合：中级工程师，现在Android发展越来越快，对于一些前沿的知识，在面试中我们也是需要做到了解，这章从Android的插件化、热更新、rxjava、进程保活，组件化，签名过程，应用沙盒等方面给大家讲解，主要想做到扩大大家的知识面，让面试官看到你对android的热爱。...

9-1 MVC架构设计模式面试问题讲解
9-2 MVP架构设计模式面试问题讲解
9-3 MVVM架构设计模式面试问题讲解
9-4 android插件化面试问题讲解
9-5 android热更新面试问题讲解
9-6 进程保活相关面试问题
9-7 UIL面试问题讲解
9-8 lint检查面试问题讲解
9-9 koltin面试问题讲解

>第10章 Java高级技术点面试问题（适合初中级工程师）

本章内容适合：初中级工程师，在Android的面试中，面试官通常缺少不了会问一下Java高级技术，本章就会为大家讲解Java相关高级技术面试点，包括GC/回收算法／堆栈／、反射／编译时vs运行时、注解（结合android annotation库）、范型、线程池／并发编程、Socket、IO/NIO、集合框架、类加载器、异常、继承／组合／多态、引用...

10-1 IO相关面试问题-Socket
10-2 IO相关面试问题-BIO／NIO
10-3 多线程相关面试问题1
10-4 多线程相关面试问题2
10-5 多线程相关面试问题3
10-6 异常相关面试问题
10-7 注解相关面试问题-基本概念
10-8 注解相关面试问题-注解分类
10-9 注解相关面试问题-Android Annotation
10-10 Java中类加载器相关面试问题-ClassLoader
10-11 Java中堆与栈相关面试问题
10-12 Java中反射相关面试问题1
10-13 Android中反射相关面试问题2

>第11章 设计模式相关面试问题（适合中级工程师）

本章内容适合：中级工程师，设计模式是高级开发者的必备知识，面试中也是经常被问到，本章将结合Android使用场景，讲解常用的设计模式，让大家既掌握Android下设计模式的使用，又可轻松应对面试中关于设计模式的面试问题。包括观察者模式、动态代理 、工厂、策略类、装饰、桥接、单例等常用设计模式。...

11-1 设计模式相关面试问题-单例-概念
11-2 设计模式相关面试问题-单例-饿汉
11-3 设计模式相关面试问题-单例-懒汉／懒汉线程安全
11-4 设计模式相关面试问题-单例--Dcl
11-5 设计模式相关面试问题-单例-静态内部类
11-6 设计模式相关面试问题-单例-枚举
11-7 设计模式相关面试问题-单例-Application
11-8 设计模式相关面试问题-Builder基础详解与代码解读
11-9 设计模式相关面试问题-Builder-Android运用Alertdialog
11-10 设计模式相关面试问题-适配器-类适配器
11-11 设计模式相关面试问题-适配器-对象适配器
11-12 设计模式相关面试问题-适配器-Listview中Adapter的运用
11-13 设计模式相关面试问题-装饰模式-概念和代码讲解
11-14 设计模式相关面试问题-装饰模式-Android中的运用
11-15 设计模式相关面试问题-外观-代码讲解
11-16 设计模式相关面试问题-外观-Android中外观设计模式的运用
11-17 设计模式相关面试问题-组合-代码讲解
11-18 设计模式相关面试问题-组合-Viewgroup源码
11-19 设计模式相关面试问题-策略-代码讲解
11-20 设计模式相关面试问题-策略-Httpstack的应用
11-21 设计模式相关面试问题-模板方法-代码讲解
11-22 设计模式相关面试问题-模板方法-asynctask应用
11-23 设计模式相关面试问题-观察者1
11-24 设计模式相关面试问题-观察者2
11-25 设计模式相关面试问题-观察者-回调函数
11-26 设计模式相关面试问题-观察者-notifyDataSetChanged方法
11-27 设计模式相关面试问题-责任链-代码讲解
11-28 设计模式相关面试问题-责任链-try／catch和有序广播
11-29 设计模式相关面试问题-责任链-viewgroup事件分发

>第12章 网络协议相关面试问题（适合中级工程师）

本章内容适合：中级工程师，网络编程无论在开发中还是在面试中都是非常重要的，在面试中尤其对网络协议问的比较多，本章将会对网络协议进行讲解，包括https／http、dns、tcp／ip以及加密算法。

12-1 http协议相关面试问题-基本概念
12-2 http协议相关面试问题-response与request
12-3 http协议相关面试问题-http1.0与http1.1的区别，get与post的区别
12-4 http协议相关面试问题-cookie与session的区别
12-5 https协议相关面试问题-TLS与SSL握手
12-6 TCP与IP相关面试问题1
12-7 TCP与IP相关面试问题2
12-8 DNS相关面试问题
12-9 https加密算法相关面试问题

>第13章 算法相关面试问题（适合初中级工程师）

本章内容适合：初中级工程师，算法作为编程的重要部分，在BAT等大公司基本是必考项，本章将结合案例为大家讲解常用常考的算法面试问题，帮助大家提高算法能力的同时轻松应对算法相关的面试。

13-1 链表相关算法面试问题讲解-链表相减
13-2 链表相关算法面试问题讲解-单链表数值
13-3 链表相关算法面试问题讲解-单链表重复数值
13-4 链表相关算法面试问题讲解-链表相加
13-5 链表相关算法面试问题讲解-回文结构
13-6 链表相关算法面试问题讲解-倒数结点删除
13-7 链表相关算法面试问题讲解-用栈实现队列
13-8 栈相关算法面试问题讲解-设计含最小函数min()的栈，要求min、push、pop的时间复杂度都是O(1)
13-9 二叉树相关算法面试问题讲解-分层遍历及变体应用
13-10 二叉树相关算法面试问题讲解-前序遍历（递归，迭代）
13-11 二叉树遍历算法面试问题讲解-中序遍历（递归，迭代)
13-12 二叉树遍历算法面试问题讲解-后序遍历（迭代）

>第14章 课程总结

本章主要总结面试过程的相关技术点。同时也将面试的内容做一个归纳总结，最后非常感谢大家的支持，课程中遇到任何问题都可以在问答区提问，我在那里等着大家，有问必答，也祝愿大家都能尽早的获得一份心仪的offer。
14-1 课程总结


## BAT大牛带你深度剖析Android 十大开源框架

>第1章 课程介绍

编程最好的学习方法是阅读顶级工程师的源码！本课程将带你深度剖析Android主流开源框架的源码，让你全面掌握框架的使用场景、内部机制、构造原理、核心类、架构与设计思想等，提升你的代码阅读与分析能力、提高代码设计能力及改造能力，快速突破技术瓶颈，轻松应对Android高级面试与技术难题！ ...

1-1 课前必读（不看会错过一个亿）
1-2 课程导学

>第2章 Okhttp网络库深入解析和相关面试题分析

本章主要先通过分析OKhttp的简单使用，对于OKhttp的调度器、拦截器、缓存策略、连接池等进行了相应的源码和原理分析，并对于socket、websocket、http缓存、多线程下载、文件下载、https等经典Android面试题进行分析。

2-1 okhttp框架流程分析
2-2 okhttp同步请求方法
2-3 okhttp异步请求方法
2-4 okhttp同步请求流程和源码分析
2-5 okhttp异步请求流程和源码分析-1
2-6 okhttp异步请求流程和源码分析-2
2-7 okhttp任务调度核心类dispatcher解析-1
2-8 okhttp任务调度核心类dispatcher解析-2
2-9 okhttp拦截器流程
2-10 okhttp拦截器链介绍
2-11 okhttp之RetryAndFollowUpInterceptor解析
2-12 okhttp之BridgeInterceptor解析
2-13 okhttp缓存策略源码分析：put方法
2-14 okhttp缓存策略源码分析：get方法
2-15 okhttp拦截器之CacheInterceptor解析
2-16 okhttp拦截器之ConnectInterceptor解析-1
2-17 okhttp拦截器之ConnectInterceptor解析-2
2-18 okhttp连接池：put,get方法
2-19 okhttp连接池：connection回收
2-20 okhttp拦截器之CallServerInterceptor解析
2-21 okhttp面试: Socket-1
2-22 okhttp面试: Socket-2
2-23 okhttp面试: HttpClient&HttpUrlConnection
2-24 okhttp面试: OkHttp来实现WebSocket连接
2-25 okhttp面试: WebSocket&轮询相关
2-26 okhttp面试: Http缓存、Etag等标示作用
2-27 okhttp面试: 断点续传原理&Okhttp如何实现
2-28 okhttp面试：多线程下载
2-29 okhttp面试：文件上传&Okhttp如何处理文件上传
2-30 okhttp面试：如何解析Json类型数据
2-31 okhttp面试：Https／对称加密&不对称加密

>第3章 Retrofit网络库深入解析和相关面试题分析

本章主要先通过分析retrofit的使用，对于retrofit的接口、动态代理、适配工厂、数据转换等进行相应的源码和原理分析，并对于retrofit的设计模式、线程切换、Hook、MVC和MVP架构、SP跨进程问题等经典Android面试题进行分析。

3-1 retrofit流程分析
3-2 retrofit概述
3-3 retrofit官网例子解析
3-4 retrofit请求过程7步骤详解
3-5 静态代理模式讲解
3-6 动态代理模式讲解
3-7 retrofit网络通信流程8步骤&7个关键成员变量解析
3-8 retrofit中builder构建者模式&builder内部类解析
3-9 retrofit中baseurl／converter／calladapter解析
3-10 retrofit中build方法完成retrofit对象创建流程解析
3-11 retrofit中RxjavaCallAdapterFactory内部构造与工作原理解析
3-12 retrofit中网络请求接口实例解析
3-13 retrofit中serviceMethod对象解析
3-14 retrofit中okHttpCall对象和adapt返回对象解析
3-15 retrofit中同步请求&重要参数解析
3-16 retrofit中异步请求解析
3-17 retrofit设计模式解析-1：构建者模式
3-18 retrofit设计模式解析-2：工厂模式
3-19 retrofit设计模式解析-3：外观模式
3-20 retrofit设计模式解析-4：策略模式
3-21 retrofit设计模式解析-5：适配器模式
3-22 retrofit设计模式解析-6：动态代理模式／观察者
3-23 retrofit面试题：retfrofit线程切换（异步机制Looper)
3-24 retrofit面试题：rxjava和retrofit如何结合进行网络请求
3-25 retrofit面试题：Hook与动态代理
3-26 retrofit面试题：Android MVC架构优势和缺点
3-27 retrofit面试题：MVP优点和缺点
3-28 retrofit面试题：sp跨进程&apply和commit方法

>第4章 Glide图片库深入解析和相关面试题分析

本章主要先通过分析Glide的使用，对于glide的内存和硬盘缓存、加载策略、如何进行图片网络请求等方面，并将重点放在梳理整个Glide请求的流程，最后对于bitmap、性能优化OOM和三级缓存、Lrucache等Android面试题进行分析。

4-1 glide框架流程分析
4-2 glide框架介绍-1
4-3 glide框架介绍-2
4-4 glide图片加载流程和源码分析-1：with方法（requestManager获取)
4-5 glide图片加载流程和源码分析-2：with方法（requestManagerRetriever的get方法)
4-6 glide图片加载流程和源码分析-3：load方法
4-7 glide图片加载流程和源码分析-4：into方法（buildTarget）
4-8 glide图片加载流程和源码分析-5：into方法（request建立和begin方法）
4-9 glide图片加载流程和源码分析-6：into方法（Loadprovider）
4-10 glide图片加载流程和源码分析-7：into方法（硬盘缓存／内存缓存)
4-11 glide图片加载流程和源码分析-8：into方法（内存缓存的读取）
4-12 glide图片加载流程和源码分析-9：into方法（内存缓存的写入）
4-13 Glide面试一：bitmap&oom&优化bitmap
4-14 Glide面试二：三级缓存&lrucache

>第5章 LeakCanary内存泄漏框架解析和相关面试题分析

本章主要先通过leakcanary使用，然后分析内存泄漏产生原因，并对于Leakcanary如何进行泄漏Activity收集策略、转换内存快照、定位内存泄漏位置等分析，最后对于现在业界比较关心的UI流畅度和性能数据上报等进行对应分析。

5-1 leakcanary预备知识：android性能优化&Gcroots
5-2 leakcanary内存框架：内存泄漏基础&为什么需要leakcanary
5-3 android常见内存泄漏分析-1：单例VS非静态内部类
5-4 android常见内存泄漏分析-2：handler&解决办法
5-5 android常见内存泄漏分析-3：线程&WebView
5-6 leakcanary原理分析-1：Leakcanary原理概述和弱引用／引用队列
5-7 leakcanary原理分析-2：ActivityRefWatcher如何监视Activity
5-8 leakcanary原理分析-3：.hprof转换snapshot
5-9 leakcanary原理分析-4：查找内存泄漏引用和最短泄漏路径
5-10 leakcanary面试题：Application&内存
5-11 leakcanary面试题：性能数据上报：网络流量和冷启动
5-12 leakcanary面试题：性能数据上报：UI卡顿和内存占用

>第6章 butterknife依赖注入框架源码解析

本章从butterknife的基本使用讲起，首先会介绍框架相关注解和APT知识点，然后开始逐步分析butterknife源码，并逐步理清butterknife注入框架的原理，最后提炼butterknife中有关android面试相关问题。

6-1 butterknife的引言和基本使用
6-2 butterknife原理必备知识点1：注解
6-3 butterknife原理必备知识点2：APT工作原理
6-4 butterknife原理必备知识点3：反射+运行时注解举例
6-5 butterknife原理分析-1：注解处理器如何处理注解和保存注解
6-6 butterknife原理分析-2：如何生成findviewByID代码

>第7章 blockcanary UI卡顿优化框架源码解析

本章会从blockcanary基本使用讲起，首先会简单介绍ActivityThread／handler／looper相关框架知识点，然后通过分析blockcanary源码，逐步理清blockcanary如何解决UI卡顿的原理，最后会提炼blockcanary中有关android面试相关问题,并总结android性能优化相关问题。...

7-1 blockcanary背景／UI卡顿原理／UI卡顿常见原因
7-2 blockcanary使用／阀值参数
7-3 blockcanary核心原理实现和流程图简述
7-4 blockcanary源码解析-1：框架初始化
7-5 blockcanary源码解析-2：stacksampler／cpusampler／start方法
7-6 blockcanary面试一：anr场景／原因／解决
7-7 blockcanary面试二：watchdog-anr 如何检测anr
7-8 blockcanary面试三：new Thread开启线程的4点弊端
7-9 blockcanary面试四：线程间通信：子线程--UI线程
7-10 blockcanary面试五：主线程--子线程(handlerThread-IntentService)
7-11 blockcanary面试六：多进程的4点好处与问题／voliate关键字
7-12 blockcanary面试七：voliate关键字和单例的写法

>第8章 eventbus异步框架源码解析

本章会从eventbus的基本用法开始讲起，主要包括Event、Subscriber、Publisher、ThreadMode几大部分，并结合handler、组件间传递等消息知识点深入分析，然后对比分析eventbus3.0和2.0的区别，并结合eventbus在android面试中遇到的高频问题，对eventbus框架进行总结。...

8-1 eventbus框架核心概念：事件传递／EventBus的优点／传统handler通信的两种方式
8-2 eventbus框架基本用法
8-3 eventbus框架源码解析-1：EventBus对象构建／如何进行线程调度
8-4 eventbus框架源码解析-2 subscribe注解／threadMode
8-5 eventbus框架源码解析-3：register订阅(上)
8-6 eventbus框架源码解析-4：register订阅（中）
8-7 eventbus框架源码解析-5：register订阅（下）
8-8 eventbus框架源码解析-6：subscribe方法完成订阅（上）
8-9 eventbus框架源码解析-7：subscribe方法完成订阅（下）
8-10 eventbus框架源码解析-8：发送事件post

>第9章 dagger2依赖注入框架源码解析

本章从dagger2的基本使用讲起，首先会介绍框架相关依赖注入的知识点，然后逐步分析dagger2源码，并逐步理清dagger2注入框架原理，并对比分析dagger2与dagger的区别，最后会根据android面试相关问题，给大家总结dagger2的相关知识点。

9-1 dagger2引言：依赖注入和使用场景
9-2 dagger2四种注入方式和依赖注入总结
9-3 dagger2的四种基本注解：@inject注解
9-4 dagger2的四种基本注解：@component注解
9-5 dagger2的inject和component注解实例和源码分析
9-6 dagger2的@Module和@Provides注解
9-7 dagger2的@Module和@Provides注解实例和代码分析

>第10章 rxjava异步框架源码解析

本章会从rxjava的基本使用讲起：主要包括观察者模式、操作符、线程控制等，然后逐步分析rxjava中的响应式编程原理，最后会结合rxjava在android面试中遇到的高频面试问题，给大家总结rxjava相关知识。

10-1 rxjava基本用法和观察者模式：01-传统观察者模式
10-2 rxjava观察者模式和基本用法
10-3 rxjava如何创建Observable&observer／subscriber
10-4 rxjava如何创建subscriber以及如何完成订阅
10-5 rxjava操作符之map基本使用
10-6 rxjava操作符之map源码探究：lift
10-7 rxjava操作符之flatmap
10-8 rxjava线程控制：多线程编程准则&Rxjava如何处理多线程&&Schedulers
10-9 rxjava线程控制：两个小例子&observeOn和SubscribeOn
10-10 rxjava线程控制：SubscribeOn源码剖析
10-11 rxjava线程控制：ObserveOn源码剖析&&subscribeOn可以调用几次

>第11章 picasso图片框架源码解析

本章从picasso基本用法和配置讲起，逐步分析picasso的源码，并从DownLoader，Dispatcher,service线程池等核心类进行分析，最后根据picasso流程图进行总结，并给大家提炼android面试中有关picasso框架的问题。

11-1 picasso框架基本使用API
11-2 picasso源码with方法：内存缓存Lrucache和线程池的调度
11-3 piacsso源码with：dispatcher如何完成线程切换
11-4 picasso源码with：NetworkRequestHandler处理图片请求和回调
11-5 picasso源码load方法
11-6 picasso源码into方法：Action&BitmapHunter
11-7 picasso源码into方法：线程池&PicassoFutureTask
11-8 picasso源码into：线程开启如何执行图片加载请求？
11-9 picasso源码into：Okhttp和UrlConnectionDownloader下载图片
11-10 picasso源码into方法：完成加载

>第12章 课程总结

本章将通过对Android面试技巧的梳理，帮助大家整体的认知和提高Android面试能力以及需要做的面试准备等，希望能对大家的面试有所帮助！最后非常感谢大家对课程的认可和支持，祝愿你们都能找到好工作。收到你们的Offer消息，是做好这门课程最大的动力。...

12-1 Android面试技巧梳理
