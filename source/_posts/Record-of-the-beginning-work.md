---
title: Record of the beginning work
date: 2019-06-17 10:53:38
tags:
---
参与模块：LenovoNotificationCenter 截屏 Settings  MessageUI  Calender FM Radio 

0. find -name "*.*" |sort|xargs grep "***" --color --line-number -r
   find -iname "××*" -type f    （d  文件夹）
   find -type f | xargs grep "******" -in


   从根目录下开始查找 abc.cpp文件     find / -name abc.cpp   2>/dev/null
   在当前目录下所有.cpp文件中查找efg函数    find . -name "*.cpp" | xargs grep 'efg'
   删除当前目录下所有.cpp文件  find . -name "*.cpp" | xargs rm

1. Wi\u2011Fi

2. nautilus .

3. Ctrl + L 目录地址切换   ESC退出

4. git reset --soft HEAD~1    git reset --hard HEAD  git重置，使本地与服务器一致，本地未提交改动将被删除
   git reset ××× --hard

   Gerrit代码提交abandon后 执行 git reset --soft HEAD~1 撤销 commit
   make all 之前先 make clean
   重置本地某部分代码，使之和服务器一致
   git checkout ×××.java  可连接多个文件
   git pull 拉下最新代码

5. shift + :wq  保存并退出
   shift + ：q  退出

6. 联系人 输入 ####3333#
   打开离线日志，保存日志到sdcard/log
   国家码 ####6020#
   查看版本号 ####5993#


7. eclipse DDMS
   File Explorer中， data => anr       导出未响应文件
                     data/logs/crash   导出crash文件
   wait  suspend lock  anr问题，查此仨函数
   main tid    trace文件里主线程标记

8. adb logcat -s *:E

9. repo sync -c -j64  开64个线程

10. Phinny 目录重置
    repo forall -c 'git reset --hard'
    repo forall -c 'git clean -f -d'
    repo sync

11. 命令记录  .bash-history

12. vim ××.java + 行数   定位到该行
    方向键定位
    i  insert状态
    ESC 退出insert状态
    :wq! 退出 强制保存
    ：q! 强制退出  不保存

    退出   ESC   SHIFT+:  q

13. 出现git编号 ×××××××××××××××  commit discarding
    进入相应文件路径
    执行 git reset --hard HEAD (全局恢复)
    执行 git clean -f -d 进行清理
    repo sync 进行同步

14. adb reboot bootloader  进入刷机模式

15. git log --oneline --graph --decorate
    图形化显示整个project的history line
    git log --help

    git log --pretty=oneline 文件路径名
    查看指定文件的提交记录

    git show  git提交版本号   文件名
    显示某次改动的具体情况

    git log --author = xiaoming

16.  adb shell
     getprop | grep ro.product.devices*

     打印系统属性
     adb shell
     cd system
     cat build.prop  或者  cat build.prop | grep [指定]

17. &   &amp;

18. android:supportRtl = "true"

19. 快速定位到行    AS   CTRL + G
    双击shift     搜类

20. git stash 将本地改动摘出
    repo sync . 更新
    git stash pop  将自己摘出的再覆盖

21. dumpsys  看近期运行的activity

22. ROW   Rest Of the World

23. 

git format-patch -1 指定commit号 //生成指定commit号的补丁
eg：git format-patch -1 5f123e379cc97c317d6094bcfa2281e1189d61f3
生成5f123e379cc97c317d6094bcfa2281e1189d61f3号的补丁，该commit号不一定在该分支开头，可以在该分支的任意位置

24. 