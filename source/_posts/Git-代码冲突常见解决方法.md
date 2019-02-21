---
title: 'Git:代码冲突常见解决方法'
date: 2019-02-21 11:01:35
tags:
---

一、出现merging冲突的原因：git远程上存在一个本地不存在的git 分支，就是本地远程代码不同步

二、解决方式：

方法一：

git pull 出现冲突后可以暂存本地修改git stash ,然后git pull 更新代码，git stash list 可查看暂存记录列表，释放本地暂存 git stash apply stash@{0} ，出现冲突文件，找到并解决，然后可以提交git add . 加入索引库，然后本地提交git commit -m '注释' 最后git push到远程

方法二：

1.git pull  更新代码，发现

error: Your local changes to the following files would be overwritten by merge:pom.xml

Please commit your changes or stash them before you merge.
这说明你的pom.xml与远程有冲突，你需要先提交本地的修改然后更新。

2.git add pom.xml   git commit -m '冲突解决'    提交本地的pom.xml文件，不进行推送远程

3.git pull   更新代码

Auto-merging pom.xml
CONFLICT (content): Merge conflict in pom.xml
Automatic merge failed; fix conflicts and then commit the result.

更新后你的本地分支上会出现 (develop|MERGING)类似这种标志

4.找到你本地的pom.xml文件，并打开

你会在文件中发现<<<<<<< HEAD ，=======  ，>>>>>>> ae9a0f6b7e42fda2ce9b14a21a7a03cfc5344d61

这种标记，<<<<<<< HEAD和=======中间的是你自己的代码，  =======  和>>>>>>>中间的是其他人修改的代码

自己确定保留那一部分代码，最后删除<<<<<<< HEAD ，=======  ，>>>>>>>这种标志

5.git add pom.xml    git commit -m '冲突解决结束'   再次将本地的pom.xml文件提交

6.git push   将解决冲突后的文件推送到远程


问题
同一份文件，在你提交时，有人比你更早更新了文件并上传，使你的本地文件并非最新。因此，在你想上传自己修改后的文件时，第一步git pull时，会报如下错误：

    error: Your local changes to the following files would be overwritten by merge:
            src/test/resources/application_context.xml
    Please, commit your changes or stash them before you can merge.
    Aborting
为解决此问题，做如下操作
git stash

隐藏本地修改
git pull

下载最新代码 
git stash pop

从Git栈中读取最近一次保存的内容，恢复自己的本地修改
提示有无冲突

若有冲突，则解决冲突
若无，则直接提交 
git add * 
git commit * -m "comments"

如果系统中有一些配置文件在服务器上做了配置修改,然后后续开发又新添加一些配置项的时候,

在发布这个配置文件的时候,会发生代码冲突:

error: Your local changes to the following files would be overwritten by merge:
        protected/config/main.php
Please, commit your changes or stash them before you can merge.

如果希望保留生产服务器上所做的改动,仅仅并入新配置项, 处理方法如下:

git stash
git pull
git stash pop
然后可以使用git diff -w +文件名 来确认代码自动合并的情况.

反过来,如果希望用代码库中的文件完全覆盖本地工作版本. 方法如下:

git reset --hard
git pull
其中git reset是针对版本,如果想针对文件回退本地修改,使用

git checkout HEAD file/to/restore
