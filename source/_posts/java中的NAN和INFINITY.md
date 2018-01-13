---
title: java中的NAN和INFINITY
date: 2017-11-20 17:16:06
tags:
---

java浮点数运算中有两个特殊的情况：NAN、INFINITY。

1、INFINITY：

在浮点数运算时，有时我们会遇到除数为0的情况，那java是如何解决的呢？

我们知道，在整型运算中，除数是不能为0的，否则直接运行异常。但是在浮点数运算中，引入了无限这个概念，我们来看一下Double和Float中的定义。

Double：

public static final double POSITIVE_INFINITY = 1.0 / 0.0;

public static final double NEGATIVE_INFINITY = -1.0 / 0.0;
 

Float：

public static final float POSITIVE_INFINITY = 1.0f / 0.0f;

public static final float NEGATIVE_INFINITY = -1.0f / 0.0f;
 

那么这些值对运算会有什么影响呢？

我们先思考一下下面几个问题：

Float和Double中的无限有什么区别？

无限乘以0会是什么？

0除以0又会有什么结果？

 

再来看下面的示例：

    public static void main(String[] args) {

         float fPos=Float.POSITIVE_INFINITY;
         float fNeg=Float.NEGATIVE_INFINITY;
         double dPos=Double.POSITIVE_INFINITY;
         double dNeg=Double.NEGATIVE_INFINITY;
         
         //t1
         System.out.println(fPos==dPos);  //output: true
         System.out.println(fNeg==dNeg);  //output: true
         
         //t2
         System.out.println(fPos*0);  //output: NAN
         System.out.println(fNeg*0);  //output: NAN
         
         //t3
         System.out.println(fPos==(fPos+10000));  //output: true
         System.out.println(fPos==(fPos*10000));  //output: true
         System.out.println(fPos==(fPos/0));  //output: true

         //t4
         System.out.println(Double.isInfinite(dPos));  //output: true
         
    }
从上面几组测试中我们可得出一些结论：

t1: Float中的无限和Double中的无限是相等的。

t2: 无限乘以0得到的值为NAN，即非数字。

t3: 除了乘以0外，对无限值做运算所得的值还是无限

要判断一个浮点数是否为INFINITY，可用t4中所示的isInfinite方法。

2、NAN

java中的NAN是这么定义的：

public static final double NaN = 0.0d / 0.0;
NAN表示非数字，它与任何值都不相等，甚至不等于它自己，所以要判断一个数是否为NAN要用isNAN方法：

public static void main(String[] args) {
        double nan=Double.NaN;
        System.out.println(nan==nan);  //output: false
        System.out.println(Double.isNaN(nan));  //output: true
}

```java              

if (Float.isInfinite(velocity) || Float.isNaN(velocity)) {
                        velocity = 0;
     }
                    
```