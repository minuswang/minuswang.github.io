---
title: Android android-signaturepad issue related
date: 2017-11-20 09:47:55
tags:
---
[Android手写开源项目和资料搜集-张明云](http://www.jianshu.com/p/610f774b2365)

[Android手写优化-平滑的签名效果实现](http://zmywly8866.github.io/2014/12/29/android-smooth-signatures.html)

[Android手写优化-更为平滑的签名效果实现](http://www.jianshu.com/p/49e7292a2911)

[android-signaturepad Github源码](https://github.com/gcacace/android-signaturepad)

[p.codekk.com---android-signaturepad](http://p.codekk.com/detail/Android/gcacace/android-signaturepad)

[getHistorySize](http://docs.huihoo.com/android/4.2/reference/android/view/MotionEvent.html#getHistorySize())

[安卓自定义View进阶-MotionEvent详解](http://www.gcssloop.com/customview/motionevent)

>public final int getHistorySize ()

Added in API level 1
Returns the number of historical points in this event. These are movements that have occurred between this event and the previous event. This only applies to ACTION_MOVE events -- all other actions will have a size of 0.

Returns
Returns the number of historical points in the event.

可以通过调用getHistorySize来获得历史的大小值，它可以返回当前事件可用的运动位置的数目。然后你可以通过使用一系列getHistorical*方法，并传递给它位置索引，来获得每一个历史事件的时间、压力、大小和位置，如下面的代码段所示：

java代码：

```java
int historySize = event.getHistorySize(); 

for (int i = 0; i < historySize; i++) { 

long time = event.getHistoricalEventTime(i); 

float pressure = event.getHistoricalPressure(i); 

float x = event.getHistoricalX(i); 

float y = event.getHistoricalY(i); 

float size = event.getHistoricalSize(i); 

//待实现：使用每一个点 

} 
```

 用来处理运动事件的一般模式是首先处理每一个历史事件，并在其后跟随当前的MotionEvent值，如下面的代码所示：

java代码:

```java
 @Override 

public boolean onTouchEvent(MotionEvent event) { 

int action = event.getAction(); 

switch (action) { 

case (MotionEvent.ACTION_MOVE) { 

int historySize = event.getHistorySize(); 

for (int i = 0; i < historySize; i++) { 

float x = event.getHistoricalX(i); 

float y = event.getHistoricalY(i); 

processMovement(x, y); 

} 

float x = event.getX(); 

float y = event.getY(); 

processMovement(x, y); 

return true; 

} 

} 

return super.onTouchEvent(event); 

} 

private void processMovement(float _x, float _y) { 

// 待实现：对移动作出反应 

} 

```

