---
layout: post
title: "IPython小记"
description: ""
category: 技术
tags: []
---
{% include JB/setup %}
使用Python的人可能都听过IPython，顾名思义，它想做的其实是`Interactive Computing`。相比原生的Python Shell，IPython有着强大的编辑和交互功能，可以很大程度地提高生产效率。IPython的自动变量名补全，函数方法提示都非常好用。  

IPython的另一个强大的功能是其提供的IPython notebook。IPython notebook是一个基于Web的交互式编程环境，你可以在一个页面里混合python代码，markdown文本以及$$\LaTeX$$公式。在浏览器里可以直接观察python代码的输出，甚至是matplotlib画出的图像。IPython notebook的这些特性使它非常适合作为教学使用以及演示代码。  

下面简单讲一下如何安装和使用IPython，以及我遇到的一些问题  
1. 首先是安装IPython: 输入命令`sudo pip install ipython[all]`，在Linux系统中可能依赖`build-essential`，`python-dev`软件包。  
<!--more-->
2. 本地运行IPython notebook: 运行命令`ipython notebook`，输入URL`http://127.0.0.1:8888/`可以实现仅在本地访问。  
3. 服务器运行IPython notebook，允许远程访问：  

+ 为用户登陆创建密码：打开一个IPython的shell输入
{% highlight python%}
In [1]: from IPython.lib import passwd
In [2]: passwd()
{% endhighlight %}生成一串hash后的密钥    

+ 为要创建的notebook server建立profile文件，在bash中如下输入
{% highlight bash%}
$ ipython profile create nbserver
{% endhighlight %}

+ 修改profile文件：
{% highlight python%}
c = get_config()

# Kernel config
c.IPKernelApp.pylab = 'inline'  # if you want plotting support always

# Notebook config
c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mycert.key'
c.NotebookApp.ip = 'chuan92.com'
# do not open browser in server
c.NotebookApp.open_browser = False
c.NotebookApp.password = u'sha1:bcd259ccf...[your hashed password here]'
# It is a good idea to put it on a known, fixed port
c.NotebookApp.port = 8888
{% endhighlight %}

+ 在你存放ipynb文件的位置运行`ipython notebook --profile=nbserver`，然后可以在在任何有浏览器的地方输入`http://yourip:8888`都可以访问你的notebook了。

值得一提的是，[Github](https://github.com/blog/1995-github-jupyter-notebooks-3)现在原生支持了ipython notebook的显示。配置好IPython notebook server后发现有点鸡肋，本来是打算服务器建一个git server仓库，一来可以共享notebook，二来可以版本控制，这些正是github可以做到的。server端的IPython notebook适用于需要随处都访问并修改的情形，并且所有的notebook都存放在服务器上。不管怎么样，server端也搭好了，说不定会有用呢=_=
