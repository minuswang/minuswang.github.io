---
title: Github 简明教程
date: 2018-01-17 16:19:09
tags:
---

[原文链接](http://www.runoob.com/w3cnote/git-guide.html)

参考资料：

[Github 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)

[如何高效利用GitHub](http://www.runoob.com/w3cnote/git-guide.html)


如果你是一枚Coder，但是你不知道Github，那么我觉的你就不是一个菜鸟级别的Coder，因为你压根不是真正Coder，你只是一个Code搬运工。
但是你如果已经在读这篇文章了，我觉的你已经知道Github了。`正是Github，让社会化编程成为现实`

## 什么是 Github?

github是一个基于git的代码托管平台，付费用户可以建私人仓库，我们一般的免费用户只能使用公共仓库，也就是代码要公开。Github 由Chris Wanstrath, PJ Hyett 与Tom Preston-Werner三位开发者在2008年4月创办。迄今拥有59名全职员工，主要提供基于git的版本托管服务。目前看来，GitHub这场冒险已经胜出。根据来自维基百科关于GitHub的描述，我们可以形象地看出GitHub的增长速度：

![](http://www.runoob.com/wp-content/uploads/2014/05/github-repos.png)

今天，GitHub已是：

1. 一个拥有143万开发者的社区。其中不乏Linux发明者Torvalds这样的顶级黑客，以及Rails创始人DHH这样的年轻极客。

2. 这个星球上最流行的开源托管服务。目前已托管431万git项目，不仅越来越多知名开源项目迁入GitHub，比如Ruby on Rails、jQuery、Ruby、Erlang/OTP；近三年流行的开源库往往在GitHub首发，例如：BootStrap、Node.js、CoffeScript等

3. alexa全球排名414的网站

## 注册账户以及创建仓库

要想使用github第一步当然是注册github账号了， github官网地址：https://github.com/。 之后就可以创建仓库了（免费用户只能建公共仓库），Create a New Repository，填好名称后Create，之后会出现一些仓库的配置信息，这也是一个git的简单教程。

## Github 安装

下载 git OSX 版
下载 git Windows 版
下载 git Linux 版

## 配置Git

首先在本地创建ssh key

`$ ssh-keygen -t rsa -C "your_email@youremail.com"`

后面的your_email@youremail.com改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的key。

回到github上，进入 Account Settings（账户配置），左边选择SSH Keys，Add SSH Key,title随便填，粘贴在你电脑上生成的key。

![](http://www.runoob.com/wp-content/uploads/2014/05/github-account.jpg)

为了验证是否成功，在git bash下输入：

`$ ssh -T git@github.com`

如果是第一次的会提示是否continue，输入yes就会看到：You've successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。

接下来我们要做的就是把本地仓库传到github上去，在此之前还需要设置username和email，因为github每次commit都会记录他们。

```
$ git config --global user.name "your name"
$ git config --global user.email "your_email@youremail.com"
```

进入要上传的仓库，右键git bash，添加远程地址：

`$ git remote add origin git@github.com:yourName/yourRepo.git`

后面的yourName和yourRepo表示你在github的用户名和刚才新建的仓库，加完之后进入.git，打开config，这里会多出一个remote "origin"内容，这就是刚才添加的远程地址，也可以直接修改config来配置远程地址。

创建新文件夹，打开，然后执行 `git init` 以创建新的 git 仓库。

## 检出仓库

执行如下命令以创建一个本地仓库的克隆版本：

`git clone /path/to/repository `

如果是远端服务器上的仓库，你的命令会是这个样子：

`git clone username@host:/path/to/repository`

## 工作流

>你的本地仓库由 git 维护的三棵"树"组成。

1. 第一个是你的 工作目录，它持有实际文件；

2. 第二个是 暂存区（Index），它像个缓存区域，临时保存你的改动；

3. 最后是 HEAD，它指向你最后一次提交的结果。

你可以提出更改（把它们添加到暂存区），使用如下命令：

```
git add <filename>

git add *
```

这是 git 基本工作流程的第一步；

使用如下命令以实际提交改动：

`git commit -m "代码提交信息"`

现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

![](http://www.runoob.com/wp-content/uploads/2014/05/trees.png)

## 推送改动

你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：

`git push origin master`

可以把 master 换成你想要推送的任何分支。 

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：

git remote add origin <server>

如此你就能够将你的改动推送到所添加的服务器上去了。

## 分支

分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是"默认的"分支。在其他分支上进行开发，完成后再将它们合并到主分支上。

![](http://www.runoob.com/wp-content/uploads/2014/05/branches.png)

创建一个叫做"feature_x"的分支，并切换过去：

`git checkout -b feature_x`

切换回主分支：

`git checkout master`

再把新建的分支删掉：

`git branch -d feature_x`

除非你将分支推送到远端仓库，不然该分支就是 不为他人所见的：

`git push origin <branch>`

## 更新与合并

要更新你的本地仓库至最新改动，执行：

`git pull`

以在你的工作目录中 获取（fetch） 并 合并（merge） 远端的改动。

要合并其他分支到你的当前分支（例如 master），执行：
`git merge <branch>`

在这两种情况下，git 都会尝试去自动合并改动。遗憾的是，这可能并非每次都成功，并可能出现冲突（conflicts）。 这时候就需要你修改这些文件来手动合并这些冲突（conflicts）。改完之后，你需要执行如下命令以将它们标记为合并成功：

`git add <filename>`

在合并改动之前，你可以使用如下命令预览差异：

`git diff <source_branch> <target_branch>`

## 标签

为软件发布创建标签是推荐的。这个概念早已存在，在 SVN 中也有。你可以执行如下命令创建一个叫做 1.0.0 的标签：

`git tag 1.0.0 1b2e1d63ff`

1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。可以使用下列命令获取提交 ID：

`git log`

你也可以使用少一点的提交 ID 前几位，只要它的指向具有唯一性。

## log

如果你想了解本地仓库的历史记录，最简单的命令就是使用: 

`git log`

你可以添加一些参数来修改他的输出，从而得到自己想要的结果。 只看某一个人的提交记录:

`git log --author=bob`

一个压缩后的每一条提交记录只占一行的输出:

`git log --pretty=oneline`

或者你想通过 ASCII 艺术的树形结构来展示所有的分支, 每个分支都标示了他的名字和标签: 

`git log --graph --oneline --decorate --all`

看看哪些文件改变了: 

`git log --name-status`

这些只是你可以使用的参数中很小的一部分。更多的信息，参考：

`git log --help`

## 替换本地改动

假如你操作失误（当然，这最好永远不要发生），你可以使用如下命令替换掉本地改动：

`git checkout -- <filename>`

此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

```
git fetch origin
git reset --hard origin/master
```

## 实用小贴士

内建的图形化 git：

`gitk`

彩色的 git 输出：

`git config color.ui true`

显示历史记录时，每个提交的信息只显示一行：

`git config format.pretty oneline`

交互式添加文件到暂存区：

`git add -i`


Git中从远程的分支获取最新的版本到本地有这样2个命令：

>git fetch：相当于是从远程获取最新版本到本地，不会自动merge

```    
git fetch origin master
git log -p master..origin/master
git merge origin/master

```

以上命令的含义：

1. 首先从远程的origin的master主分支下载最新的版本到origin/master分支上

2. 然后比较本地的master分支和origin/master分支的差别

3. 最后进行合并

上述过程其实可以用以下更清晰的方式来进行：

```
git fetch origin master:tmp

git diff tmp 

git merge tmp
```

从远程获取最新的版本到本地的test分支上，之后再进行比较合并

>git pull：相当于是从远程获取最新版本并merge到本地

` git pull origin master`

上述命令其实相当于git fetch 和 git merge

在实际使用中，git fetch更安全一些，因为在merge前，我们可以查看更新情况，然后再决定是否合并


要讲清楚git fetch，git pull,必须要附加讲清楚git remote，git merge 、远程repo， branch 、 commit-id 以及 FETCH_HEAD。

1. 【git remote】首先， git是一个分布式的结构，这意味着本地和远程是一个相对的名称。

本地的repo仓库要与远程的repo配合完成版本对应必须要有 git remote子命令，通过git remote add来添加当前本地仓库的远程repo， 有了这个动作本地的repo就知道了当遇到git push 的时候应该往哪里提交代码。

2. 【git branch】其次，git天生就是为了多版本分支管理而创造的，因此分支一说，不得不提， 分支就相当于是为了单独记录软件的某一个发布版本而存在的，既然git是分布式的，便有了本地分支和远程分支一说，git branch 可以查看本地分支， git branch -r  可以用来查看远程分支。 本地分支和远程分支在git push 的时候可以随意指定，交错对应，只要不出现版本从图即可。

3. 【git merge】再者，git的分布式结构也非常适合多人合作开发不同的功能模块，此时如果每个人都在其各自的分支上开发一个相对独立的模块的话，在每次release制作时都需先将各成员的模块做一个合并操作，用于合并各成员的工作成果，完成集成。 此时需要的就是git merge.

4. 【git push 和 commit-id】在每次本地工作完成后，都会做一个git commit 操作来保存当前工作到本地的repo， 此时会产生一个commit-id，这是一个能唯一标识一个版本的序列号。 在使用git push后，这个序列号还会同步到远程repo。

在理解了以上git要素之后，分析git fetch 和 git pull 就不再困难了。 

首先，git fetch 有四种基本用法

1. git fetch            →→ 这将更新git remote 中所有的远程repo 所包含分支的最新commit-id, 将其记录到.git/FETCH_HEAD文件中

2. git fetch remote_repo         →→ 这将更新名称为remote_repo 的远程repo上的所有branch的最新commit-id，将其记录。 

3. git fetch remote_repo remote_branch_name        →→ 这将这将更新名称为remote_repo 的远程repo上的分支： remote_branch_name

4. git fetch remote_repo remote_branch_name:local_branch_name       →→ 这将这将更新名称为remote_repo 的远程repo上的分支： remote_branch_name ，并在本地创建local_branch_name 本地分支保存远端分支的所有数据。

FETCH_HEAD： 是一个版本链接，记录在本地的一个文件中，指向着目前已经从远程仓库取下来的分支的末端版本。

git pull 的运行过程：

git pull : 首先，基于本地的FETCH_HEAD记录，比对本地的FETCH_HEAD记录与远程仓库的版本号，然后git fetch 获得当前指向的远程分支的后续版本的数据，然后再利用git merge将其与本地的当前分支合并。

>git查看某个文件的修改历史

有时候在比对代码时，看到某些改动，但不清楚这个改动的作者和原因，也不知道对应的BUG号，也就是说无从查到这些改动的具体原因了～

【注】：某个文件的改动是有限次的，而且每次代码修改的提交都会有commit描述，我们可以从这里进行入手；

一：切换到目录
首先切换到要查看的文件所在的目录：

cd packages/apps/Mms/src/com/android/mms/ui/
或者是使用编辑器（例如：vscode）右键在终端打开。

## 二、git log –pretty
然后使用下面的命令可列出文件的所有改动历史，注意，这里着眼于具体的一个文件，而不是git库，如果是库，那改动可多了去了～

git log --pretty=oneline 文件名

如：
root@ubuntu:android_src/packages/apps/Mms/src/com/android/mms/ui# git log --pretty=oneline MessageItem.java 
27209385caf678abe878375a470f4edd67a2d806 fix to process force close when empty address contained in card
0e04b16f1dad7dc0a36e2235f7337bc656c365c7 display for 1970-1-1
e4abf3a213197491e0855e101117b59b5dc0160d HREF#13954 receive, store, and display wap push
356f6def9d3fb7f3b9032ff5aa4b9110d4cca87e HREF#16265_uim_show_time_error
350f9d34c35ab50bdb4b2d43fb3ff9780e6c73fa fix xxxx
715e32f97bd9d8ce4b5ba650b97ba4b137150456 Fix ANR from calling Contact.get()
fd8357ff5febab0141e1beb8dd3b26f70416b108 Fix missing From field
d130e2e6dc448fd80ecb70f0d31e3affb9888b9a fix bug 2112925: don't display zip file garbage content in MMS.
0e19f738c114f86d0d88825ee48966015fb48b6d Don't always show sent timestamp
52f854cbb75e8f9975c7e33216b828eb2f981095 Don't show Anonymous as the MMS sender
331864544ec51ba6807fc5471cc6d537b7fef198 add search capability
33a87f96f8c625aa10131a77a3968c97c4ec5a62 Remove all references to ContactInfoCache except those in Contact.
70c73e05a792832aa28da751cdaf3fa83a7b8113 Begin moving all conversation data behind a data model with a cache.
48da875f1beea835c6771977e5bd8a9aa3d4bc10 Begin adding UI unit tests to the Mms app.
66dde9460badebf8e740275cabde9cca256006eb Stop requiring a Context to be passed in to ContactInfoCache.
591d17e9a51bb9f829d6860dc7aa0bad25062cd5 auto import from //branches/cupcake_rel/...@138607
72735c62aba8fd2a9420a0f9f83d22543e3c164f auto import from //depot/cupcake/@135843
892f2c5bf965b1431ae107b602444a93f4aad4a3 auto import from //depot/cupcake/@135843
153ae99e0a7d626a24d61475eeb133249deb448c auto import from //depot/cupcake/@132589
abd7b2d90f7491075f1daba4b4cccdfc82f8ddd1 auto import from //depot/cupcake/@137055
59d72c57ce9c319b6cd43ce2ab36b7076c9e821f auto import from //branches/cupcake/...@132276
44cea74dc55e2459262d0d765ef4a69267dd09b0 auto import from //branches/cupcake/...@131421
0f236f55349f070ac94e12cca963847173393da8 Code drop from //branches/cupcake/...@124589
8eed706474910ccb978acda03e85d3261037da6e Initial Contribution

## 三：git show
如上所示，打印出来的就是针对文件MessageItem.java的所有的改动历史，每一行最前面的那一长串数字就是每次提交形成的哈希值，接下来使用git show即可显示具体的某次的改动的修改～

git show 356f6def9d3fb7f3b9032ff5aa4b9110d4cca87e

结果如下：
root@ubuntu:/android_src/packages/apps/Mms/src/com/android/mms/ui# 
>git show 356f6def9d3fb7f3b9032ff5aa4b9110d4cca87e

commit 356f6def9d3fb7f3b9032ff5aa4b9110d4cca87e
Author: 某某某 <某某某的邮箱>
Date:   Thu Jan 6 01:50:31 2011 +0800

    修改的描述（是该代码commit时所填）

    Signed-off-by: 某某某 <某某某的邮箱>

diff --git a/src/com/android/mms/ui/MessageItem.java b/src/com/android/mms/ui/MessageItem.java
index 0a0c4b7..55c3b27 100644
--- a/src/com/android/mms/ui/MessageItem.java
+++ b/src/com/android/mms/ui/MessageItem.java
+
+ 列出具体的改动

这样就可以知道是谁做了修改，以及具体的修改代码～   那接下来不管是直接去找他交流还是研究代码，都有依据了～

—————————————分界线———————————————
git log -p 文件名  # 查看某个文件的提交记录以及对此文件的修改。中途如果想要终止查看，请按 Q键
