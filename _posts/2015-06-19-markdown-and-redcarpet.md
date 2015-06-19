---
layout: post
title: "使用redcarpet渲染markdown"
description: ""
category: 工具
tags: []
---
Markdown现在经常被用来做笔记，写博客。像一些交互式编程环境如[Rmarkdown](http://rmarkdown.rstudio.com/)，[Juypter Notebook](https://jupyter.org/)也都支持嵌入markdown。大三时用Jekyll搭建博客，阴差阳错使用了[kramdown](https://github.com/gettalong/kramdown)作为渲染引擎。Kramdown是比较新的项目，标榜快速和纯Ruby书写。可是有一些小遗憾，比如代码高亮要显式地使用`{％highlight  c％} 代码段{％endhighlight％ }`，而Github渲染的markdown可以直接使用三个反引号包围代码。那个时候把博客搭起来就很开心了，没管这些细节。  

前段时间对博客进行换血改造，终于不能忍受kramdown的这个缺陷了。Github Pages是不是只支持kramdown这一个渲染引擎，可不可以换个渲染引擎呢。在网上一番寻找后，遇到了[redcarpet](https://github.com/vmg/redcarpet)。使用redcarpet之后，代码段高亮变得简单，还有一些扩展功能，只能感叹相见恨晚啊。

下面简要记录一下redcarpet对markdown的扩展，需要设置`extensions: ["no_intra_emphasis", "fenced_code_blocks", "footnotes", "highlight", "strikethrough", "underline", "autolink", "tables", "with_toc_data", "quote"]`

##增强  
`this is *italic*`: this is *italic*  
`this is _underline_`: this is _underline_  
`this is __strong__`: this is __strong__  
`this is ~~strikethrough~~`: this is ~~strikethrough~~  
`this is ==hightlighted==`: this is ==hightlighted==  
`this is reference[^1]`: this is reference[^1]  
`this is link https://chuan92.com`: this is link https://chuan92.com  
`>this is blockquote`:
> this is blockquote

`this is $\LaTeX$`:  This is $x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$



##代码段高亮  
> \`\`\`python  
		def f():  
			print "hello world"  
> \```

```python
def f():
	print "hello world"
```
##表格  

```
| Left-Aligned  | Center Aligned  | Right Aligned |
| :------------ |:---------------:| -----:|
| col 3 is      | some wordy text | $1600 |
| col 2 is      | centered        |   $12 |
```
| Left-Aligned  | Center Aligned  | Right Aligned |
| :------------ |:---------------:| -----:|
| col 3 is      | some wordy text | $1600 |
| col 2 is      | centered        |   $12 |

##目录   
github-flavored-markdown不支持生成目录，jekyll也没有此功能，可以使用如下超链接方法手动加目录  

```
- [Intro to Linear classification](#intro)
  - [Multiclass SVM](#svm)
- [Summary](#summary)

<a name='intro'></a>
intro here
```
- [Intro to Linear classification](#intro)
  - [Multiclass SVM](#svm)
- [Summary](#summary)

<a name='intro'></a>
intro here, 点击Intro to Linear Classification的链接可以到达这里


[^1]: this is reference

