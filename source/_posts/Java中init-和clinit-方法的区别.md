---
title: Java中init()和clinit()方法的区别
date: 2019-08-21 14:12:03
tags:
---

<clinit>：在jvm第一次加载class文件时调用，包括静态变量初始化语句和静态块的执行

<init>:在实例创建出来的时候调用，包括调用new操作符；调用Class或java.lang.reflect.Constructor对象的newInstance()方法；调用任何现有对象的clone()方法；通过java.io.ObjectInputStream类的getObject()方法反序列化。

（1）<clinit>()方法是由编译器自动收集类中的所有类变量的赋值动作和静态语句块（static）中的语句合并产生的，编译器收集的顺序是由语句在源文件中出现的顺序所决定的，静态语句块只能访问到定义在静态语句块之前的变量，点贵在他之后的变量，在前面的静态语句块中可以赋值但不能访问。

public class Test{

static{

i = 0;                                        //给变量赋值可以正常通过

System.out.println(i);               //这句编译器会提示“非法向前引用”

}

static int i = 1;

}

（2）<clinit>()方法与类的构造函数（或者说实例构造器中的<init>()方法）不同， 它不需要显示的调用父类构造器，虚拟机会保证在子类的<init>()方法执行之前，父类的<clinit>()方法已经执行完毕。因为在虚拟机中第一个被执行的<clinit>()方法的类肯定是java.lang.Object。

（3）由于父类的<clinit>()方法先执行，也就意味着父类中定义的静态语句块要优先于子类类的变量赋值操作。

（4）<clinit>()方法对于类或者接口来说并不是必须的，如果一个类没有静态语句块，也就没有变量的赋值操作，那么编译器可以不为这个类生成<clinit>()方法。

（5）接口中不能使用静态语句块，但仍然可以有变量初始化的赋值操作，因此接口与类一样都会生成<clinit>()方法。但接口与类不同，执行接口的<clinit>()方法不需要先执行父接口的<clinit>()方法。只有当父接口中定义的变量使用时，父接口才会初始化。另外，接口的实现类在初始化时也一样不会执行接口的<clinit>()方法。

（6）虚拟机会保证一个类的<clinit>()方法在多线程环境中被正确地加锁、同步，如果多个线程同时去初始化一个类，那么只有一个线程去执行这个类的<clinit>()方法中有耗时很长的操作，就可能造成多个线程阻塞