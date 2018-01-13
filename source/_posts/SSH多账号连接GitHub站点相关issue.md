---
title: SSH多账号连接GitHub站点相关issue
date: 2017-10-19 13:38:09
tags:
---

>场景：使用github的时候，大家都知道需要给该账号添加一个SSH key才能访问，参考[GitHubHelp](https://help.github.com/articles/connecting-to-github-with-ssh/)。当然如果你在多台机器使用一个账户，你可以为该账户添加多个SSH key。由于github是使用SSH key的fingerprint来判定你是哪个账户，而不是通过用户名，这样你就可以在设置完之后，在本地直接执行下面的语句，它就会自动使用你的.ssh/id_rsa.pub所对应的账户进行登陆，然后执行相关命令。
```
1. #本地建库
2. $ git init
3. $ git commit -am "first commit'
4. 
5. #push到github上去
6. $ git remote add origin git@github.com:xxxx/test.git
7. $ git push origin master
```

但是如果你想在一台机器使用两个github账号（比如私人账号和工作用账号）。这个时候怎么指定push到哪个账号的test仓库上去呢？ 

解决方案（假设你已经拥有私有账号且已经OK，现在想使用另一个工作用账号）：

1. 为工作账号生成SSH Key

`$ ssh-keygen -t rsa -C "your-email-address" `

>存储key的时候，不要覆盖现有的id_rsa，使用一个新的名字，比如id_rsa_work 

2. 把id_rsa_work.pub加到你的work账号上 

3. 把该key加到ssh agent上。由于不是使用默认的.ssh/id_rsa，所以你需要显示告诉ssh agent你的新key的位置

`$ ssh-add ~/.ssh/id_rsa_work`

可以通过ssh-add -l来确认结果

4. 配置.ssh/config

`$ vi .ssh/config `

加上以下内容
```
#default github
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa

Host github_work
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_work
```
5. 这样的话，你就可以通过使用github.com别名github_work来明确说你要是使用id_rsa_work的SSH key来连接github，即使用工作账号进行操作。

```
#本地建库
$ git init
$ git commit -am "first commit'
 
#push到github上去
$ git remote add origin git@github_work:xxxx/test.git
$ git push origin master
```
[参考文献1:linuxidc](http://www.linuxidc.com/Linux/2016-05/131079.htm)
[参考文献2:CSDN](http://blog.csdn.net/ws1352864983/article/details/51244010)
[参考文献3:同时创建两个账号](http://www.cnblogs.com/xjnotxj/p/5845574.html)
### 问题一：Could not open a connection to your authentication agent.

【解决方法】需要ssh-agent启动bash，或者说把bash挂到ssh-agent下面。

【具体方法】
```
islue@localhost $ ssh-agent bash --login -i
islue@localhost $ ssh-add
```
【ssh-agent介绍】

ssh-agent就是一个管理私钥的代理，受管理的私钥通过ssh-add来添加，所以ssh-agent的客户端都可以共享使用这些私钥。

好处1：不用重复输入密码。

用 ssh-add 添加私钥时，如果私钥有密码的话，照例会被要求输入一次密码，在这之后ssh-agent可直接使用该私钥，无需再次密码认证。

好处2：不用到处部署私钥

假设私钥分别可以登录同一内网的主机 A 和主机 B，出于一些原因，不能直接登录 B。可以通过在 A 上部署私钥或者设置 PortForwarding 登录 B，也可以转发认证代理连接在 A 上面使用ssh-agent私钥登录 B。
```
islue@localhost $ ssh -A HOST_A
islue@HOST_A $ ssh HOST_B
islue@HOST_B $
```
[问题一参考链接](http://www.cnblogs.com/cheche/archive/2011/01/07/1918825.html)

### 问题二
sshkey放入系统：

`ssh-add ~/.ssh/xxxxx之后（xxxxx请替换成自己sshkey的文件名）`

带log测试github连接情况

`ssh -vT git@github.com`

[参考GitHubHelp](https://help.github.com/articles/error-permission-denied-publickey/)

[免费开源Github Pages空间可绑域名搭建个人博客存放图片文件](https://www.freehao123.com/github-pages/)

### 问题三 两个git帐号用同一台电脑，怎样使每次提交人与帐号所属仓库对应？

git config不同的仓库配置不同的账户即可

为每个仓库设置单独的用户名和密码。方法如下：

```
#进入到需要修改的仓库中
git config user.name GitHub的用户名
git config user.email GitHub的登录邮箱
```
>网站文件夹下 .deploy_git文件夹为发布部署本地仓库，进入该目录
修改后通过.git目录下的config文件可以查看当前仓库的提交账号配置信息

[另外一种有效的方式：windows一台电脑添加多个git账号](http://blog.csdn.net/qq1332479771/article/details/70149616)

### 问题四 ERROR Deployer not found: git
`npm install hexo-deployer-git –save`

### 问题五 强制回退
```
git reset --hard 1234561da26201567af917af05cd9ab7b990b781
git push https://github.com/×××/×××.github.io.git HEAD --force
```
