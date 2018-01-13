---
title: git apply、git am打补丁.diff 和 .patch
date: 2017-11-15 18:13:00
tags:
---

>生成patch：

git format-patch -M master

生成指定patch，0163bed3bf59ae74c36cc5138b4c24f1556d8304是commit id，-1是指从当前id开始，向下提交次数，包含此次且计数从1开始。
也就是说，我想要打出0163bed3bf59ae74c36cc5138b4c24f1556d8304当前的patch，则：
git format-patch 0163bed3bf59ae74c36cc5138b4c24f1556d8304 -1
想要打出0163bed3bf59ae74c36cc5138b4c24f1556d8304和它之前的一次提交的patch，则：
git format-patch 0163bed3bf59ae74c36cc5138b4c24f1556d8304 -2

>生成diff：

git diff (id1) (id2) --binary --(path) > 目标文件路径
比如要生成frameworks/base/下的diff，保存到~/gittest/下的f_b.diff：（注意：旧的id1在前）
git diff 206b47c132a80870c06d87c69a548bbfeebecd2d b5ce3e4ebe9503e370d734cecc12482bca023fdf --binary -- frameworks/base/ > ~/gittest/f_b.diff


>打入 patch / diff：

git apply xxx.patch
git apply xxx.diff

>检查 patch / diff：

git apply --check xxx.patch
git apply --check xxx.diff

若git和需要打patch的文件不在一个目录：(git在framework下，patch要打入frameworks/base/下)
git apply --check --directory=base/ xxx.patch
git apply --directory=base/ xxx.patch
git am 后面会说到，以及生产patch和打入patch的一些命令参数


我们创建一个文件夹，git init一下，模拟diff / patch源环境
$ mkdir gittest
$ git init
然后创建一个空文件 test，然后首次提交
$ touch test
$ git add .
$ git commit -m "test init"
在里面加入11111，add，commit（add 11111）一次；
在里面加入22222，add，commit（add 22222）一次；
...
在里面加入55555，add，commit（add 55555）一次；
一共提交5次，可以看到提交了5次。

gitk
生成patch / diff 文件（我们单独建一个文件夹来存patch和diff --> ~/patch/patch/）：

在工作中，二者选一个就可以，看自己的需求。
patch相对于diff，多了提交记录，也就是说可以原封不动的把他人commit内容写上去，但是操作比diff麻烦一些

生成patch：
我们记录一下最新的commit id ： 21ebfb1ef6a0a9b56d46036c036e8377b56b2da5，有5次提交。

$ git format-patch 21ebfb1ef6a0a9b56d46036c036e8377b56b2da5 -5

git patch

生成了包含当前id和之前的4个id的patch，一共5个，命名也有规律000X+commit的内容.patch
我们把这5个移到~/patch/patch/中。

生成diff：
我们记录要生成diff的区间，!!包上不包下!!，

就是说我要生成 11111~55555的diff
id1 是 test init的id；1cf68afcf3e089a349c8ee534dc3ff44d11a6624
id2是add 55555的id。21ebfb1ef6a0a9b56d46036c036e8377b56b2da5
$ git diff 1cf68afcf3e089a349c8ee534dc3ff44d11a6624 21ebfb1ef6a0a9b56d46036c036e8377b56b2da5 --binary -- . > ~/patch/patch/test.diff

diff 多次提交可以是一个文件，但是patch不行，因为它里面有commit记录！

打 patch / diff 补丁（无冲突）：

我们在当前目录创建一个需要打补丁的文件夹(gittest1)，里面也有一个空的test文件。
然后我们把patch/diff打入gittest1/test。
执行最开始的1、2即可，改一下文件夹名称就ok


gittest1
打patch（不包含commit内容）
检查patch是否可用，没显示文字，就说明可用，且无冲突；
git apply --check ~/patch/patch/0001-add-11111.patch
一般检查一个就可以。
打入patch，可以批量，也可以单个。
git apply ~/patch/patch/*.patch
肯定会成功，因为没有冲突。我们查看一下
git diff

git diff

5个patch都打上去了，接着就可以 git add / commit提交了。但是这样没有commit记录。

打patch（包含commit内容 git am） git checkout . 撤销一下
检查patch是否可用，没显示文字，就说明可用，且无冲突；
git apply --check ~/patch/patch/0001-add-11111.patch
打入patch，可以批量，也可以单个。
 git am ~/patch/patch/*.patch
肯定会成功，因为没有冲突。我们查看一下


gitk

gitk

我们发现已经自动commit，不用add、commit，可以直接push。

打diff  git reset --hard 4c6eb312e94214a5f34fa3f119382ace647b1b3c 撤销一下
检查diff是否可用，没显示文字，就说明可用，且无冲突；
git apply --check ~/patch/patch/test.diff
打入diff;
git apply ~/patch/patch/test.diff
肯定会成功，因为没有冲突。我们查看一下
git diff

git diff

接着就可以 git add / commit提交了。但是这样没有commit记录。

打 patch / diff 补丁（有冲突）：

我们还原gittest1/test的初始状态，然后修改一下test文件，写入00000,，然后add、commit。


add 00000
打patch

检查patch是否可用，没显示文字，就说明可用，且无冲突；
git apply --check ~/patch/patch/0001-add-11111.patch
error: 打补丁失败：test:0
error: test：补丁未应用
说明是可以用，但是有冲突。
打入patch，可以批量，也可以单个。

因为在check的时候知道已经有冲突了，就不好用apply来打patch，如果一定要用的话，建议一个一个apply，所以很麻烦，不如用diff。
在这里要用到 git am
 git am ~/patch/patch/*.patch

正应用：add 11111
error: 打补丁失败：test:0
error: test：补丁未应用
补丁失败于 0001 add 11111
失败的补丁文件副本位于：
/home/deshui/yudeshui/log/gittest1/.git/rebase-apply/patch
当您解决了此问题后，执行 "git am --continue"。
如果您想跳过此补丁，则执行 "git am --skip"。
要恢复原分支并停止打补丁，执行 "git am --abort"。
这行话告诉你，patch冲突了，但是有三个选项 git am --continue、git am --skip、git am --abort
这时候不要动！不要动！不要动！

解决冲突。
我们 gedit test，在里面手动加入11111，因为我们加入了00000，导致错行，所以接下来的4个patch都会有冲突。
提交记录。
git add test
git am --continue
正应用：add 11111
正应用：add 22222
error: 打补丁失败：test:1
error: test：补丁未应用
补丁失败于 0002 add 22222
失败的补丁文件副本位于：
~/gittest1/.git/rebase-apply/patch
当您解决了此问题后，执行 "git am --continue"。
如果您想跳过此补丁，则执行 "git am --skip"。
要恢复原分支并停止打补丁，执行 "git am --abort"。
我们gitk一下，发现已经commit已经有add 11111的记录了！
同上操作，一直修改到44444 
git add test
git am --continue
正应用：add 44444
正应用：add 55555
这样就完整的解决冲突，保留commit了。

git am finish
打diff  git reset --hard 0050cda7f22df985d79b9b98da9bfc282ea10ef1 撤销到add 00000
检查diff是否可用，肯定有冲突。
git apply --check ~/patch/patch/test.diff
error: 打补丁失败：test:0
error: test：补丁未应用
打入diff;
git apply --reject --ignore-whitespace ~/patch/patch/test.diff 2>&1 | tee ~/patch/patch/testdiff.log
这里reject是生成一个.rej的文件，是一个差异文件。
ignore-whitespace是忽略多余的空格。
2>&1是值错误信息
tee 错误信息输出到控制台
~/patch/patch/testdiff.log 错误信息保存在这个文件里
*********输出log**********
检查补丁 test...
error: 当查询：
error: 打补丁失败：test:0
应用 test 个补丁，其中 1 个被拒绝...
拒绝第 #1 个片段。
*********输出log**********
这时候我们发现，文件夹多了一个test.rej的文件，打开它。
diff a/test b/test    (rejected hunks)
@@ -0,0 +1,5 @@
+11111
+22222
+33333
+44444
+55555
这个告诉我们，要在test中加入的信息，我们打开test补上就可以了。手动！
然后add / commit 就可以了。

作者：yotods
链接：http://www.jianshu.com/p/e5d801b936b6
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。