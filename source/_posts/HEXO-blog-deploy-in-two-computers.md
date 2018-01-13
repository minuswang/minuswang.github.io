---
title: HEXO blog deploy in two computers
date: 2018-01-13 22:46:36
tags:
---

1. 新创建一个hexo分支，将hexo设为默认分支
2. `把之前的博客文件夹弃用`
3. 将github的东西克隆下来（会有博客的基本结构）
4. 将之前保留的_config.yml，themes/，source/，scaffolds/，package.json，.gitignore复制过来
5. 执行npm install和npm install hexo-deployer-git（重要 在hexo分支）
6. 执行git add .、git commit -m ""、git push origin hexo来提交hexo网站源文件
7. 依次执行hexo g和hexo d生成静态网页部署至Github上
8. 这样一来，你的博客仓库就有master分支和hexo分支，分别保存静态网页和源文件

