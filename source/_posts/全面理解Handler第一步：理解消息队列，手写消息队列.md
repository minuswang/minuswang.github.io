---
title: 全面理解Handler第一步：理解消息队列，手写消息队列
date: 2019-08-21 14:21:06
tags:
---

[原文链接](https://blog.51cto.com/13931046/2287799)     [MDove的博客](https://blog.51cto.com/13931046)

前言 Handler机制这个话题，算是烂大街的内容。但是为什么偏偏重拿出来“炒一波冷饭”呢？因为自己发现这“冷饭”好像吃的不是很明白。最近在思考几个问题，发现以之前对Handler机制的了解是在过于浅显。什么问题？

> Handler机制存在的意义是什么？能否用其他方式替换？
> Looper.loop();是一个死循环，为什么没有阻塞主线程？用什么样的方式解决死循环的问题？
如果透彻的了解Handler，以及线程的知识。是肯定不会有这些疑问的，因为以上问题本身就存在问题。

就这俩个小问题，就发现自己在学习道路上的不扎实，所以这段时间重新理解了一下Handler。先预告一小下下，关于Handler的内容将是一个系列文章，今天这一篇内容重点在于Handler的理解，以及对消息队列的思考。

# 1、Handler机制为了什么？

我们都知道，在Android开发中，无法在子线程中更新UI。我们先思考一个问题？为什么不能在子线程更新UI。如果看过View绘制的源码，我们都知道不能在子线程更新UI的原因是：ViewRootImpl中有这么一个方法：

void checkThread() {
    if (mThread != Thread.currentThread()) {
        throw new CalledFromWrongThreadException(
            "Only the original thread that created a view hierarchy can touch its views.");
    }
}
很明显这是人为限制的一个操作。那我们在思考，为什么谷歌开发Android系统时要这么限制？

其实不难推测出来。对于线程来说，我们都知道线程与线程之间是内存共享的。所以如果某一时刻多个子线程同时去更新UI，那么对于绘制UI来说便成为了一个不安全的操作。为了保证UI绘制的正确性，此时势必要增加锁，以同步的方式去控制这个问题。然而加锁的方式显然是一种牺牲性能的方式。

那么还有没有其他方案呢？很显然，最终谷歌选择了只能在主线程更新UI，应运而生的Handler机制被创造出来了。但是它也不是什么新概念，说白了就是消息队列。实现原理也很简单：只允许一个线程（主线程）去更新UI，子线程将消息放到消息队列中，由主线程去轮询消息队列，拿出消息并执行。这也就是我们的Handler机制。

# 2、消息队列
这种单线程 + 消息队列的模型其实应用很广。比如在Web前端之中，对于JavaScript来说，被设计时就决定了单线程模型。假设如果 Javascript 被设计为多线程的程序，那么操作 DOM 必然会涉及到资源的竞争。此时只能加锁，那么在 Client 端中跑这么一门语言的程序，资源消耗和性能都将是不乐观的。但是如果设计成单线程，并辅以完善的异步队列来实现，那么运行成本就会比多线程的设计要小很多了。

所以我们可以看到，Handler机制的思路可以说是一个颇为常见的设计。既然本质是消息队列，是不是我们自己也可以写一套消息队列来感受一下Handler的设计思路呢？没错，接下来让我们一起实现一套简单的消息队列：

3、手写消息队列
我们先来捋一捋思路：

Looper中创建了MessageQueue，Handler之中又通过ThreadLocal拿到主线程new出来的Looper，因此Handler就持有了MessageQueue，又因此线程间是内存共享的，所以子线程可以通过Handler去往MessageQueue之中发送Message。

Looper.loop()死循环轮询MessageQueue，拿到Message就回调其对应的方法。

这样整个Handler机制就运转起来了。接下来我们就依靠这个思路，实现自己的消息队列，为了代码更简洁，以及和Handler机制产生区别，我这里省略一些操作比如ThreadLocal之类的。

[深入理解Android消息机制](https://www.iteye.com/news/32980)  
[深入理解Android消息队列原理篇：Message、MessageQueue、Looper、Handler](https://blog.csdn.net/u010246789/article/details/51557547)

为什么loop这个死循环会在主线程执行，不会ANR么？

答：最开始Android的入口ActivityThread里面的main方法，里面有一个巨大的Handler，然后会创建一个主线程的looper对象，这也是为什么直接在主线程拿Handler就有Looper的原因，在其他线程是要自己Looper.prepare()的。 
其实整个Android就是在一个Looper的loop循环的，整个Android的一切都是以Handler机制进行的，即只要有代码执行都是通过Handler来执行的，而所谓ANR便是Looper.loop没有得到及时处理，一旦没有消息，Linux的epoll机制则会通过管道写文件描述符的方式来对主线程进行唤醒与沉睡，Android里调用了linux层的代码实现在适当时会睡眠主线程。

真正会卡死主线程的操作是在回调方法onCreate/onStart/onResume等操作时间过长，会导致掉帧，甚至发生ANR，looper.loop本身不会导致应用卡死。

ActivityThread的main方法主要作用就是做消息循环，一旦退出消息循环，主线程运行完毕，那么你的应用也就退出了。

Android是事件驱动的，在Loop.loop()中不断接收事件、处理事件，而Activity的生命周期都依靠于主线程的Loop.loop()来调度，所以可想而知它的存活周期和Activity也是一致的。当没有事件需要处理时，主线程就会阻塞；当子线程往消息队列发送消息，并且往管道文件写数据时，主线程就被唤醒。

主线程在没有事件需要处理的时候就是处于阻塞的状态。想让主线程活动起来一般有两种方式： 
第一种是系统唤醒主线程，并且将点击事件传递给主线程； 
第二种是其他线程使用Handler向MessageQueue中存放了一条消息，导致loop被唤醒继续执行。

主线程Looper从消息队列读取消息，当读完所有消息时，主线程阻塞。子线程往消息队列发送消息，并且往管道文件写数据，主线程即被唤醒，从管道文件读取数据，主线程被唤醒只是为了读取消息，当消息读取完毕，再次睡眠。因此loop的循环并不会对CPU性能有过多的消耗。

总结： Looer.loop()方法可能会引起主线程的阻塞，但只要它的消息循环没有被阻塞，能一直处理事件就不会产生ANR异常

[Android中为什么主线程不会因为Looper.loop()里的死循环卡死？ --Gityuan](https://www.zhihu.com/question/34652589/answer/90344494)

要完全彻底理解这个问题，需要准备以下4方面的知识：Process/Thread，Android Binder IPC，Handler/Looper/MessageQueue消息机制，Linux pipe/epoll机制。

总结一下楼主主要有3个疑惑：

1.Android中为什么主线程不会因为Looper.loop()里的死循环卡死？

2.没看见哪里有相关代码为这个死循环准备了一个新线程去运转？ 
 
3.Activity的生命周期这些方法这些都是在主线程里执行的吧，那这些生命周期方法是怎么实现在死循环体外能够执行起来的？

针对这些疑惑， @hi大头鬼hi@Rocko@陈昱全 大家回答都比较精炼，接下来我再更进一步详细地一一解答楼主的疑惑：

# (1) Android中为什么主线程不会因为Looper.loop()里的死循环卡死？ 

这里涉及线程，先说说说进程/线程，进程：每个app运行时前首先创建一个进程，该进程是由Zygote fork出来的，用于承载App上运行的各种Activity/Service等组件。进程对于上层应用来说是完全透明的，这也是google有意为之，让App程序都是运行在Android Runtime。大多数情况一个App就运行在一个进程中，除非在AndroidManifest.xml中配置Android:process属性，或通过native代码fork进程。

线程：线程对应用来说非常常见，比如每次new Thread().start都会创建一个新的线程。该线程与App所在进程之间资源共享，从Linux角度来说进程与线程除了是否共享资源外，并没有本质的区别，都是一个task_struct结构体，在CPU看来进程或线程无非就是一段可执行的代码，CPU采用CFS调度算法，保证每个task都尽可能公平的享有CPU时间片。

有了这么准备，再说说死循环问题：对于线程既然是一段可执行的代码，当可执行代码执行完成后，线程生命周期便该终止了，线程退出。而对于主线程，我们是绝不希望会被运行一段时间，自己就退出，那么如何保证能一直存活呢？简单做法就是可执行代码是能一直执行下去的，死循环便能保证不会被退出，例如，binder线程也是采用死循环的方法，通过循环方式不同与Binder驱动进行读写操作，当然并非简单地死循环，无消息时会休眠。但这里可能又引发了另一个问题，既然是死循环又如何去处理其他事务呢？通过创建新线程的方式。真正会卡死主线程的操作是在回调方法onCreate/onStart/onResume等操作时间过长，会导致掉帧，甚至发生ANR，looper.loop本身不会导致应用卡死。

# (2) 没看见哪里有相关代码为这个死循环准备了一个新线程去运转？ 

事实上，会在进入死循环之前便创建了新binder线程，在代码ActivityThread.main()中：

public static void main(String[] args) {
        ....

        //创建Looper和MessageQueue对象，用于处理主线程的消息
        Looper.prepareMainLooper();

        //创建ActivityThread对象
        ActivityThread thread = new ActivityThread(); 

        //建立Binder通道 (创建新线程)
        thread.attach(false);

        Looper.loop(); //消息循环运行
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }thread.attach(false)；
    
便会创建一个Binder线程（具体是指ApplicationThread，Binder的服务端，用于接收系统服务AMS发送来的事件），该Binder线程通过Handler将Message发送给主线程，具体过程可查看 startService流程分析，这里不展开说，简单说Binder用于进程间通信，采用C/S架构。关于binder感兴趣的朋友，可查看我回答的另一个知乎问题：为什么Android要采用Binder作为IPC机制？ - Gityuan的回答

另外，ActivityThread实际上并非线程，不像HandlerThread类，ActivityThread并没有真正继承Thread类，只是往往运行在主线程，该人以线程的感觉，其实承载ActivityThread的主线程就是由Zygote fork而创建的进程。主线程的死循环一直运行是不是特别消耗CPU资源呢？ 

其实不然，这里就涉及到Linux pipe/epoll机制，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，详情见Android消息机制1-Handler(Java层)，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。

# (3) Activity的生命周期是怎么实现在死循环体外能够执行起来的？

ActivityThread的内部类H继承于Handler，通过handler消息机制，简单说Handler机制用于同一个进程的线程间通信。Activity的生命周期都是依靠主线程的Looper.loop，当收到不同Message时则采用相应措施：在H.handleMessage(msg)方法中，根据接收到不同的msg，执行相应的生命周期。    比如收到msg=H.LAUNCH_ACTIVITY，则调用ActivityThread.handleLaunchActivity()方法，最终会通过反射机制，创建Activity实例，然后再执行Activity.onCreate()等方法

再比如收到msg=H.PAUSE_ACTIVITY，则调用ActivityThread.handlePauseActivity()方法，最终会执行Activity.onPause()等方法。 

上述过程，我只挑核心逻辑讲，真正该过程远比这复杂。主线程的消息又是哪来的呢？当然是App进程中的其他线程通过Handler发送给主线程，请看接下来的内容：

最后，从进程与线程间通信的角度，通过一张图加深大家对App运行过程的理解：

<img src="https://pic4.zhimg.com/50/7fb8728164975ac86a2b0b886de2b872_hd.jpg" data-rawwidth="890" data-rawheight="535" class="origin_image zh-lightbox-thumb" width="890" data-original="https://pic4.zhimg.com/7fb8728164975ac86a2b0b886de2b872_r.jpg"/>

system_server进程是系统进程，java framework框架的核心载体，里面运行了大量的系统服务，比如这里提供ApplicationThreadProxy（简称ATP），ActivityManagerService（简称AMS），这个两个服务都运行在system_server进程的不同线程中，由于ATP和AMS都是基于IBinder接口，都是binder线程，binder线程的创建与销毁都是由binder驱动来决定的。App进程则是我们常说的应用程序，主线程主要负责Activity/Service等组件的生命周期以及UI相关操作都运行在这个线程； 另外，每个App进程中至少会有两个binder线程 ApplicationThread(简称AT)和ActivityManagerProxy（简称AMP），除了图中画的线程，其中还有很多线程，比如signal catcher线程等，这里就不一一列举。Binder用于不同进程之间通信，由一个进程的Binder客户端向另一个进程的服务端发送事务，比如图中线程2向线程4发送事务；而handler用于同一个进程中不同线程的通信，比如图中线程4向主线程发送消息。结合图说说Activity生命周期，比如暂停Activity，流程如下：线程1的AMS中调用线程2的ATP；（由于同一个进程的线程间资源共享，可以相互直接调用，但需要注意多线程并发问题）线程2通过binder传输到App进程的线程4；线程4通过handler消息机制，将暂停Activity的消息发送给主线程；主线程在looper.loop()中循环遍历消息，当收到暂停Activity的消息时，便将消息分发给ActivityThread.H.handleMessage()方法，再经过方法的调用，最后便会调用到Activity.onPause()，当onPause()处理完后，继续循环loop下去。

