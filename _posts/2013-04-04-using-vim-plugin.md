---
layout: post
title: "using vim plugin"
description: ""
category: 技术 
tags: [Vim]
---
{% include JB/setup %}
前几天在人人网上python主页推荐了一个vim插件youcompleteme,当时一起兴，就在github上clone了一个。ycm需要依赖clang，cmake，并且使用一个vim插件管理软件vundle。装好了之后却发现ycm依赖python2,而我用的arch上的python默认版本是3。网上有人建议把python link到python2上，这种方法让人很无语，觉得有倒退的嫌疑。后来在arch论坛发现，gvim可以依赖python2,于是装了gvim，ycm就能工作了。本来以为万事大吉，也许是我安装的姿势不对吧，在使用vim的时候却发现ycm并不能将`co`补全为`cout`，并且在补全的过程中，底部状态栏会显示很多的信息，让人不爽。后来，后来我就把它删掉了。  

折腾了这么久，眼看就要白忙活了，不过却因为ycm知道了vundle这个神器。说来惭愧，虽然我用vim一两年了，却没有用过vim插件，就只是会打开vimrc文件，改改缩进，加个高亮等等。Vundle是一个管理vim插件的插件。使用它只需要三步  
1.
> $ git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle  

2.在你的vimrc文件中加入这样几句
> filetype off  
> set rtp+=~/.vim/bundle/vundle
> call vundle#rc()  
> Bundle 'gmarik/vundle'    
> filetype plugin indent on  

注意Bundle的格式，对于vim-srcipts库中有的插件如c.vim，`Bundle 'c.vim'`，对于github上其他用户的插件`Bundle 'user_name/scripts_name'`管理

3.打开vim，运行`:BundleInstall`会搜索并安装.vimrc中Bundle还未安装的插件，而`:BundleClean`会将以前用Bundle安装的，现在不在.vimrc中的插件删除  

vundle只是用来管理插件的,[Vim](http://www.vim.org/scripts/)官网上有很多好用的插件，赶紧选几个，没准就会life changing啦。最近比较忙，闲一点准备把常用插件c.vim的配置贴一下

