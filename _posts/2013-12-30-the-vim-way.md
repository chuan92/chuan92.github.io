---
layout: post
title: "the vim way"
description: ""
category: 
tags: [vim]

---
{% include JB/setup %}
vim号称“编辑器之神”，有一个比较陡峭的学习曲线，想要以vim作为主要生产力工具，肯定是要花一番功夫的。就像武侠小说里写的一样，vim是宝剑，你得为它开锋。我自己在vim学习摸索出了下面的一些东西

1.建议童鞋们将.vimrc放在github上，以后切换工作环境时也可以顺利迁移相关配置  
2.花时间了解一下vimrc中一些常用的缩进，语法高亮，映射，搜索  
3.推荐看看[Practical Vim](http://book.douban.com/subject/10599776/)  
4.使用[vundle](https://github.com/gmarik/vundle/) 等工具来管理常用的vim插件 

* syntastic		  语法检查插件，支持C/C++,Java,Python,Tex等
* nerdtree        树形目录插件，能比较方便地在工作目录切换文件
* nerdcommenter   代码注释插件，能快速将若干行行注释或者取消注释
* taglist         提供源代码符号本地结构化视图，依赖ctags
* OmniCppComplete C/C++源码补全插件，依赖ctags
* a.vim			  源文件与头文件快速切换
* c.vim			  C/C++ support，自动添加文件头，快捷键编译运行
* vim-airline	  状态栏美化，记得在vimrc中set laststatus=2
* vim-gitgutter	  在vim编辑中显示git实时变动
* vim-fugitive    View any blob, tree, commit, or tag in the git repository
* supertab		  使用tab来补全代码
* delimitMate	  自动补全括号，引号 

5.[github](https://github.com/skwp/dotfiles)上有很多比较好的配置可以借鉴，你也可以看下[我的vim配置](https://github.com/chuan92/dotfiles)
