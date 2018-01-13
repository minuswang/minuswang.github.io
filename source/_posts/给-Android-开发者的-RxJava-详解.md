---
title: 给 Android 开发者的 RxJava 详解
date: 2017-11-07 10:39:50
tags:
---
## [给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)

## RxJava 到底是什么

>一个词：异步。

RxJava 在 GitHub 主页上的自我介绍是 "a library for composing asynchronous and event-based programs using observable sequences for the Java VM"（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）。这就是 RxJava ，概括得非常精准。

然而，对于初学者来说，这太难看懂了。因为它是一个『总结』，而初学者更需要一个『引言』。

其实， RxJava 的本质可以压缩为异步这一个词。说到根上，它就是一个实现异步操作的库，而别的定语都是基于这之上的。

>RxJava 好在哪

换句话说，『同样是做异步，为什么人们用它，而不用现成的 AsyncTask / Handler / XXX / ... ？』

一个词：简洁。

异步操作很关键的一点是程序的简洁性，因为在调度过程比较复杂的情况下，异步代码经常会既难写也难被读懂。 Android 创造的 AsyncTask 和Handler ，其实都是为了让异步代码更加简洁。RxJava 的优势也是简洁，但它的简洁的与众不同之处在于，随着程序逻辑变得越来越复杂，它依然能够保持简洁。



## [匠心写作](http://gank.io/post/published)

Android 复杂的列表视图新写法 MultiType (drakeet)	2016-11-10
RxJava 与 Retrofit 结合的最佳实践 (tough1985)	2016-03-15
从开发者角度解析 Android N 新特性！ (代码家)	2016-03-10
给 JavaScript 初心者的 ES2015 实战 (小问)	2015-11-10
给 Android 开发者的 RxJava 详解 (扔物线)	2015-10-02