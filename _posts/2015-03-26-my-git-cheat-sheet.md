---
layout: post
title: "My Git Cheat Sheet"
description: ""
category: 技术
tags: []
---
纪录一些git命令

+ `git status` 检查当前文件状态
+ `git diff` 比较当前工作目录中文件和暂存区域快照之间的差异
+ `git diff --staged` 比较暂存区文件与上次提交的快照之间的差异
+ `git commit -a` 把所有已经跟踪过的文件暂存起来一并提交，从而跳过`git add`步骤
+ `git reset HEAD` 暂存区的目录树会被重写，被master分支指向的目录树所替换，但是工作区不受影响
+ `git rm --cached <file>` 直接从暂存区删除文件，工作区则不做出改变
+ `git checkout -- <file>` 用暂存区指定的文件替换工作区的文件,会清除工作区中未添加到暂存区的改动
+ `git checkout HEAD <file>` 用HEAD指向的master分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动


<!--![](https://7xidap.com1.z0.glb.clouddn.com/git-stage.png)-->
<!--more-->

+ 一次Git克隆会建立你自己的本地分支master和远程分支origin/master，并且将它们都指向origin上的master分支，在本地是无法直接更改origin/master数据的

+ `git remote add [shortname] [url]` 将url设置为远程仓库，命名为shortname
+ `git reset HEAD^` 回退到上一版本
+ `git push origin HEAD --force` 强制向远程推送
+ `git checkout -b [分支名] [远程名]/[分支名]` 跟踪远程分支
+ `git push [远程名] :[分支名]` 删除远程分支
+ `git push [远程名] [本地分支名](:[远程分支名])` 推送本地分支


