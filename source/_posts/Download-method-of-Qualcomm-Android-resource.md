---
title: Download method of Qualcomm Android resource
date: 2019-02-27 14:46:56
tags:
---

电脑环境： Ubuntu17.04    高通开源源码网站：[下载地址](https://wiki.codeaurora.org/xwiki/bin/QAEP/release)

安装git &repo

```  
sudo apt install git
sudo apt install repo
```

修改repo 

```
sudo vim /usr/bin/repo  
#REPO_URL = 'https://gerrit.googlesource.com/git-repo'
REPO_URL = 'https://gerrit-google.tuna.tsinghua.edu.cn/git-repo'
```
下载Android源码

创建源码目录  

```mkdir android-msm8996```

进入源码目录，初始化repo
```
cd android-msm8996
repo init -u git://codeaurora.org/platform/manifest.git -b release -m LA.UM.6.5.r1-02500-8x96.0.xml

```
同步代码

```repo sync -j4```

编译Android源码
安装JDK

```sudo apt install openjdk-7-jdk ```

编译
进入源码目录

```cd android-msm8996```

配置环境变量

```source build/envsetup.sh```

选择产品

```
lunch
```

执行命令启动编译
```
make
或者 
make -j4 showcommands dist

```
参数说明：
-j4, 开启4个线程;

showcommands, 显示编译过程中执行的命令[38];

dist, 将编译后产生的发布文件拷贝到out/dist目录中[38];
编译内核
make bootimage
编译其他模块



高通android开源代码下载介绍页面如下，介绍了如何下载高通android源码，选择不同的分支和平台以及版本。

https://wiki.codeaurora.org/xwiki/bin/QAEP/

如果不想仔细看这个页面，可以直接使用下面的命令下载sdm710平台的代码，android版本是8.0.1
```
$ repo init -u git://codeaurora.org/platform/manifest.git -b release -m LA.UM.6.8.r1-07000-SDM710.0.xml --repo-url=git://codeaurora.org/tools/repo.git --repo-branch=caf-stable
$ repo sync

```

repo init -u git://codeaurora.org/platform/manifest.git -b release -m LA.UM.7.3.r1-06900-sdm845.0.xml --repo-url=git://codeaurora.org/tools/repo.git --repo-branch=caf-stable

代码信息如下：

Date
Tag / Build ID
Chipset
Manifest
Android Version

August 20, 2018
LA.UM.6.8.r1-07000-SDM710.0
sdm710
LA.UM.6.8.r1-07000-SDM710.0.xml
08.01.00


####59930#
查看暗码

####6020#
切换国家