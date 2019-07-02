---
layout:     post
title:      git从远程仓库更新最新版本代码到本地仓库
subtitle:   git更新gitHub远程仓库代码，实现版本合并
date:       2018-09-20
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - git
    - gitHub
    - 最新版本更新
---

## 项目介绍

   使用git fecth方法将远程仓库项目更新到本地仓库用于开发

### 1、查看远程仓库
    命令 git remote -v

    (```)
    $ git remote -v
    origin  https://github.com/wangshouzhi/wsz-blog.git (fetch)
    origin  https://github.com/wangshouzhi/wsz-blog.git (push)
    (```)



### 2.从远程获取最新版本到本地

    命令git fetch origin master:temp   这句命令的意思是：从远程的origin仓库的master分支下载到本地并新建一个分支temp

    (```)
    $ git fetch origin master:tempFrom https://github.com/com360/android-app
    * [new branch] master -> temp
    (```)

### 3.比较本地的仓库和远程参考的区别

     命令git diff temp  命令的意思是：比较master分支和temp分支的不同

### 4.合并temp分支到master分支

     git merge temp   合并temp分支到master分支

### 5.如果不想要temp分支了，可以删除此分支

     git branch -d temp
     如果该分支没有合并到主分支会报错，可以用以下命令强制删除git branch -D <分支名>

 总结：方式二更好理解，更安全，对于pull也可以更新代码到本地，相当于fetch+merge，多人写作的话不够安全
