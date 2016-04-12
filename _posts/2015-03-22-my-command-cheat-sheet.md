---
layout: post
title: "My Command Cheat Sheet"
description: ""
category: 编程
tags: []
---
纪录我使用的一些简单linux命令

发现一个比较好玩的程序[eg](https://github.com/srsudar/eg)，针对常用命令给出一些例子，比man更容易快速读懂

###sort 
+ `sort -u` 删除重复行
+ `sort -r` 降序排列
+ `sort -n` 按数值排序
+ `sort -t : -k 1` 按照:分割，以第1列排序 

###cut
+ `cut -d : -f 1,7 file` 以:分割file的每行，取第1个和第7个field作为输出的columns
+ `cut -c 1-16 file` 取file每行第1-16个字符作为输出的columns

###sed
+ `sed '/^$/d;/regex/d'` 删除空行或者由regex表示的行


<!--more-->
###grep
+ `grep [option] pattern file` 在file中搜寻pattern

###find
+ `find dir -name 'filename'` 在dir目录下寻找filename文件

###wc
+ `wc -l` 统计文件行数

###awk
+ `awk 'BEGIN{FS=","}{print $3 "," $2 "," $1} file.csv'` 将file.csv的列调换位置, FS设置分隔符(regex)
+ `awk '!x[$0]++' file` 保序删除file中的重复行

<!-- to add less tr -->

###tmux
+ `tmux new -s myname` start new session with myname
+ `tmux a -t myname` attache to named session
+ `tmux ls` list sessions
+ `tmux kill-session -t myname` kill session

在tmux session内部时操作

+ `bind-key c` new window
+ `bind-key w` list windows
+ `bind-key n` next window
+ `bind-key 0` choose the 0th window
+ `bind-key d` detach
+ `bind-key v` 竖直划分pane(我的配置)
+ `bind-key s` 水平划分pane(我的配置)

###bash programming
+ bash中分号是多个语句的分割，如果一行只有一个语句的时候，无需分号
+ bash中单引号包围起来的字符串是普通的字符串，保留字面意思以及转义；双引号中部分特殊字符如$会起作用
