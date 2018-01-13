---
title: Bresenham 直线算法原理
date: 2017-10-27 15:31:56
tags:
---
# Bresenham's line algorithm

# 布雷森汉姆直线演算法

> ## 一、使用方法
![](\Bresenham-直线算法原理\yongfa.jpg)

> ## 二、算法推导
![](\Bresenham-直线算法原理\bresenham.jpg)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8b/Trac%C3%A9_de_segment_avec_l%27algorithme_de_Bresenham.gif/346px-Trac%C3%A9_de_segment_avec_l%27algorithme_de_Bresenham.gif)

![](http://www.jhmcu.com/wp-content/uploads/2013/03/Bresenham-0.png)


` d = 2·Δy-Δx`

递推式：  

```java

当d≥0时：

{ 
    d=d+2·(Δy－Δx)；       
    y++；       
    x++；      
    }  
    
否则：  
{ 
    d=d+2·Δy；       
    x++；      
    }
```

>采用递推步进的办法，令每次最大变化方向的坐标步进一个象素，同时另一个方向的坐标依据误差判别式的符号来决定是否也要步进一个象素。


三、直线Bresenham算法实现：
```
　　// 条件：0≤m≤1且x1<x2
　　//1.输入线段的两个端点坐标和画线颜色：x1，y1，x2，y2，color；
　　//2.设置象素坐标初值：x=x1，y=y1；
　　//3.设置初始误差判别值：p=2·Δy-Δx；
　　//4.分别计算：Δx=x2-x1、Δy=y2-y1；
　　//5.循环实现直线的生成：
　　　for(x=x1；x<=x2；x++)
　　　{ SetPixel(dc,x,y,color) ；
　　　　if(p>=0)
　　　　　{ y=y+1；
　　　　　　p=p+2·(Δy-Δx)；
　　　　　}
　　　　else
　　　　　{ p=p+2·Δy；
　　　　　}
　　　}
```

> ## 参考资料

[维基百科-布雷森漢姆直線演算法](https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm)

[The Bresenham Line-Drawing Algorithm](https://www.cs.helsinki.fi/group/goa/mallinnus/lines/bresenh.html)

[CSDN-直线的Bresenham算法](http://blog.csdn.net/liqiancao/article/details/21886507)

[百度文库-直线的Bresenham算法-地质大学课件](https://wenku.baidu.com/view/82eb898cfab069dc502201fe.html)

[个人笔记-bresenham算法的FPGA的实现1](http://www.cnblogs.com/sepeng/p/4045593.html)

[知乎-游戏编程里面有哪些经典或者很酷的算法？](https://www.zhihu.com/question/27455969)