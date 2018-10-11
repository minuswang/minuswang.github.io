---
title: BeginAnew-the log of rebuild this site 20181010
date: 2018-10-11 10:16:05
tags:
---

01

npm install 默认安装最新版本，如果想要安装指定版本，可以在库名称后加 @版本号：
$ npm install sax@latest
$ npm install sax@0.1.1
$ npm install sax@">=0.1.0 <0.2.0"123

如果当前项目有 package.json 文件，下载包时会下载这个文件中指定的版本； 
如果当前项目中没有 package.json 文件，就会下载指定包的最新版本。

  有时下载会报错：npm install error saveError ENOENT: no such file or directory, open '/Users/zhangshixin/package.json'
  
  解决办法： 
  - 在目录下执行 npm init 创建 package.json，输入初始化信息 
  - 然后再执行下载命令


02

package.json 文件

管理本地安装 npm 包的最好方式就是创建 package.json 文件。
一个 package.json 文件可以有以下几点作用：
作为一个描述文件，描述了你的项目依赖哪些包
允许我们使用 “语义化版本规则”（后面介绍）指明你项目依赖包的版本
让你的构建更好地与其他开发者分享，便于重复使用

package.json 如何创建

使用 npm init 即可在当前目录创建一个 package.json 文件

03
是需要package.json才能npm install。
可以npm init初始化生成一个package.json。
然后就可以愉快地npm install了

04
npm ERR! notsup Unsupported platform for fsevents@1.1.3: wanted 
{"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

Error is indicating that you are installing fsevents module in other OS rather than Mac

fsevents module only works for Mac Operting system

thank you yes I'm on windows machine I removed the {fsevents } from the package.json and it's work 

05
执行命令hexo server，提示：Usage: hexo<Command> ....

原因：我认为是没有生成本地服务
解决方法，执行命令：$ npm install hexo-server --save

06
bash devtty No such device or address

如果本地体验没有问题，上传到GitHub有问题的话，可以直接使用git上传C:\Blog\blog.deploy_git目录下的

解决办法：修改Blog\blog目录下的_config.yml文件如下

repo 不能直接用https://github.com/ScnuWang/ScnuWang.github.io.git 

deploy: 
  type: git 
  repo: ssh://git@github.com/ScnuWang/ScnuWang.github.io.git 
  branch: master

07
Hexo-Next-多地同步丢失解决(WARN No layout: index.html)

由于同步中未将Next主题提交，也是为了下次同步最新的Next主题，故才有多地同步是更新Next主题方案。

解决方案：直接获取/更新Next主题
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next

08
npm 是一个包管理器，它让 JavaScript 开发者分享、复用代码更方便（有点 maven 的感觉哈）。

在程序开发中我们常常需要依赖别人提供的框架，写 JS 也不例外。这些可以重复的框架代码被称作包（package）或者模块（module），一个包可以是一个文件夹里放着几个文件，同时有一个叫做 package.json 的文件。

一个网站里通常有几十甚至上百个 package，分散在各处，通常会将这些包按照各自的功能进行划分（类似我们安卓开发中的划分子模块），但是如果重复造一些轮子，不如上传到一个公共平台，让更多的人一起使用、参与这个特定功能的模块。

而 npm 的作用就是让我们发布、下载一些 JS 轮子更加方便。

我们可以去官方网站 https://www.npmjs.com/ 浏览、搜索想要的轮子，也可以直接在命令行中 search 一下意中轮。

---------------------
作者：拭心 
来源：CSDN 
原文：https://blog.csdn.net/u011240877/article/details/76582670?utm_source=copy 
版权声明：本文为博主原创文章，转载请附上博文链接！