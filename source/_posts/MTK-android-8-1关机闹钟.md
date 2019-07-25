---
title: MTK android 8.1关机闹钟
date: 2019-07-25 14:33:42
tags:
---

在android 8.1上mtk修改了关机闹钟的实现，此feature与原有的feature有很大的区别。

1. 文件变化
(1) android O上关机闹钟在`framework`这边的实现，是在加了一支文件`MtkAlarmManagerService.java`. `MtkAlarmManagerService`继承于原生的`AlarmManagerService`。

此文件的路径：

`/vendor/mediatek/proprietary/frameworks/base/services/core/java/com/mediatek/server/MtkAlarmManagerService.java`

(2) `mtk DeskClock`代码位于`/vendor/mediatek/proprietary/packages/apps/DeskClock/`，其模块名叫做`MtkDeskClock`

2. 行为改变

O之前的关机闹钟：
   > 设置的时间到—>`Alarm Boot`—>关机闹钟响起，出现关机闹钟界面

O的关机闹钟：
   > 提前150s Normal Boot—>正常开机完成—>Alarm时间到—>闹钟响起(出现闹钟界面)

3. 如果发现关机闹钟无效，可以从如下方面检查
 (1) 当前codebase是否有升级到最新
 (2) 使用的DeskClock是否为MtkDeskClock
 (3) 设置的Alarm的时间是否距离现在的时间大于150s（小于150s将不会响起）
 (4) 开机的时间是否小于150s（大于150s将 不会响起）

PS: Android O0上关机闹钟依然维持和以前版本一致。

4. 关机闹钟与安全锁屏的关系
如果设置锁屏安全模式的时候，会有一个选择Secure start-up的步骤。
如果选择No，那么还是可以正常支持关机闹钟。如果选择了Yes，那么就不支持关机闹钟，到了时间点也不会起闹钟。

主要代码在`PowerOffAlarm.canEnablePowerOffAlarm`接口里面。如果不设置`secure start-up`，那么`getPassWordTYPE`的值就为`CRYPT_TYPE_DEFAULT`，所以也可以使用关机闹钟，反之，就不支持关机闹钟了
