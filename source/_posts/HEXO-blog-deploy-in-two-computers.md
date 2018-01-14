---
title: HEXO blog deploy in two computers
date: 2018-01-13 22:46:36
tags:
---

## 已有电脑

1. 新创建一个hexo分支，将hexo设为默认分支
2. `把之前的博客文件夹弃用`
3. 将github的东西克隆下来（会有博客的基本结构）
4. 将之前保留的_config.yml，themes/，source/，scaffolds/，package.json，.gitignore复制过来
5. 执行npm install和npm install hexo-deployer-git（重要 在hexo分支）
6. 执行git add .、git commit -m ""、git push origin hexo来提交hexo网站源文件
7. 依次执行hexo g和hexo d生成静态网页部署至Github上
8. 这样一来，你的博客仓库就有master分支和hexo分支，分别保存静态网页和源文件

## 以后再进行修改添加
1. 执行`git add .`、`git commit -m ""`、`git push origin hexo(新分支名)`来提交hexo网站源文件
2. 依次执行hexo g和hexo d生成静态网页部署至Github上


思路：通过 git 的分支实现。hexo 生成的静态博客文件默认放在 master 分支上，可以新创建一个 hexo 分支，把 hexo 的源文件都放在 hexo 分支上，换新电脑时，直接 git clone hexo 分支即可。

# 1. 分析

新建一个 hexo 分支，把 hexo 的源文件都放到这个分支上。但是源文件有70多M，并不需要把所有文件都放在分支上。node_modules目录可以用 npm install 命令生成，public目录可以使用 hexo g 命令生成，.deploy_git目录是hexo d命令生成，所以可以把这三个目录放在 .gitignore 里忽略提交。

# 2. 操作

在原来的笔记本上：
1. 创建新的分支 hexo 并切换到这个分支上。

在Github的username.github.io仓库上新建一个xxx分支，并切换到该分支，并在该仓库->Settings->Branches->Default branch中将默认分支设为xxx，save保存；然后将该仓库克隆到本地，进入该username.github.io文件目录。完成上面步骤后，在当前目录使用Git Bash执行git branch命令查看当前所在分支，应为新建的分支xxx

2. 在博客的目录下创建 .gitignore 文件，在文件里写入：

```html
node_modules/
public/
.deploy_git/
```

3. 执行npm install和npm install hexo-deployer-git（重要 在hexo分支）

4. 在 hexo 分支下，git add、git commit 提交所有文件，最后把 hexo 分支 push 到 github 上。这时 github 上有两个分支：master 和 hexo，master 分支是博客的静态文件，hexo 分支是博客的源代码。master分支和xxx分支各自保存着一个版本，master分支用于保存博客静态资源，提供博客页面供人访问；xxx分支用于备份博客部署文件，供自己维护更新，两者在一个GitHub仓库内互不冲突，完美！

## 在新电脑上：

1. 安装 git。在 git bash 下输入以下命令配置用户名和邮箱，邮箱是自己 github 账号的邮箱：
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

2. 执行以下命令生成 SSH key：
ssh-keygen -t rsa -C "youremail@example.com"
其中 youremail@example.com 是你的 github 邮箱，剩下的一路回车即可。最后会在用户主目录下生成一个 .ssh 目录，该目录下有 id_rsa 和 id_rsa.pub 两个文件，这两个文件就是 SSH 的密钥对，id_rsa 是私钥，id_rsa.pub 是公钥。

3. 登录 github，在 settings 里找到 SSH and GPG keys，创建新的 SSH key，title 可以是任意值，key 文本框里粘贴 id_rsa.pub 里的内容，最后保存。

4. 安装 Node.js。在 git bash 里输入以下命令安装 hexo：
npm install hexo-cli -g

5. 新建博客目录，在该目录打开 git bash，执行：
git init
初始化该目录。

6. 使用 git clone 把 github 上的 hexo 分支复制到博客目录。指明如下：
`git clone -b hexo git@github.com:xxx/xxx.github.io.git`
注意默认 git clone 命令只会复制 master 分支，这里需要用 -b 指明只克隆 hexo 分支。因为源代码都放在了hexo分支上，并不需要对master分支进行操作。

7. 克隆完以后，执行
`npm install`
安装所有依赖（生成 node_modules 目录）

`npm install hexo-deployer-git`（重要 在hexo分支）

error   fan qiang 

8. 执行
hexo g
生成博客的静态文件（即 public 目录）

9. 执行
hexo new post "文章标题"
创建新文章，然后可以在 source/_posts/ 目录下找到对应的文章并可以编辑。

10. 执行
hexo s
运行 hexo 服务器，在浏览器中打开 http://localhost:4000 查看博客是否已经可以运行。

11. 执行
hexo d
可以把博客的静态文件部署到 github 上。注意在 _config.yml 中 deploy 的 branch 值必须为 master，指明 hexo d 命令是把博客静态文件推到 master 分支上。

12. 最后把修改推到 github 上的 hexo 分支：
git add .
git commit -m "commit message"
git push origin hexo
注意：以上所有操作都是在 hexo分支下操作的。

## 日常操作流程

1. 换到不同电脑上时，首先拉下 github 上的 hexo分支的更新
git pull origin hexo

2. 编写、修改博客，并使用 hexo d 命令部署

3. 把对文件的修改推送到 github 的hexo分支上

git add .
git commit -m "commit message"
git push origin hexo



参考链接：

1. [在多台电脑间使用hexo]{https://theqwang.github.io/2017/03/17/在多台电脑间使用hexo/}

2. [利用Hexo在多台电脑上提交和更新github pages博客]{https://www.jianshu.com/p/0b1fccce74e0}

3. [hexo博客分支教训]{https://www.jianshu.com/p/a27e9761ecf3}

4. [hexo本地测试运行重启后页面空白,提示 : WARN No layout: index.html?](https://www.zhihu.com/question/38781463?sort=created)   theme文件夹出错，不显示layout

run npm config get proxy看看拿到什么

如果上面的返回不为空，npm config set proxy null或者npm config delete proxy ,顺便把https的代理也关掉npm config set https-proxy null, 证书问题，把证书的校验关掉npm config set strict-ssl false, 当然上面的命令你不用全敲一遍，根据报错信息提示，选择对应的解决方式， 然后再试试

如果1的命令为空，检查你的环境变量里面是不是有HTTP_PROXY

是不是开了全局的代理

电脑是不是开了代理，导致证书不被npm信任。建议你关掉代理，使用淘宝镜像源试下

npm install -g express --registry=https://registry.npm.taobao.org



在Windows平台上，使用Git。当输入添加命令（例如：git add .）时，会出现warning: LF will be replaced by CRLF in……这样的警告。虽然对于后期编程没有影响，但毕竟是warning，还是需要了解一下是啥原因吧！经过查阅相关资料，才知道原来是换行符的问题。

原因分析：
CRLF -- Carriage-Return Line-Feed 回车换行。也就是回车（CR, ASCII 13, \r），换行（LF, ASCII 10,\n）。这两个ACSII字符不会在屏幕有任何显示，是Windows平台上用来标识一行的结束。而在Linux/UNIX系统中只有换行符LF，没有回车符CR。也就是说在Windows中的换行符为 CRLF，而在Linux下的换行符为：LF。使用Git来生成一个工程后，文件中的换行符为LF，当执行添加命令（例如：git add .）时，系统就会发出警告。解决方法：在Git Bash中输入以下命令：

## `git config --global core.autocrlf false`
