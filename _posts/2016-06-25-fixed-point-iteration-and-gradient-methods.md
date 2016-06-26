---
layout: post
title: "不动点迭代和优化方法"
description: ""
category: 机器学习
tags: []
---
机器学习中的很多问题都可以形式化成目标函数的最优化求解问题。梯度下降法和牛顿法是用来求解无约束优化的基本方法，本文尝试以不动点迭代的角度来探讨一下这两种方法。

# 不动点
在数值分析中，函数的不动点是指被这个函数映射到其自身的一个点。也就是说，$c$是函数$f(x)$的不动点，当且仅当$f(c)=c$。有些函数可能不止一个不动点，如$f(x)=\frac{4}{x}$的不动点是$\pm2$；有些函数则没有不动点，如$f(x)=x+1$。

对于简单的函数，可以直接解方程$f(x)=x$来求解不动点。对于复杂的函数，方程的求解则是个很困难的过程，能不能用数值方法来迭代逼近这个点呢？答案对于具有某些性质的函数是肯定的，有如下定理：

> If a function $f$ defined on the real line with real values is Lipschitz continuous with Lipschitz constant $L<1$, then this function has precisely one fixed point, and the fixed-point iteration converges towards that fixed point for any initial guess $x_{0}$. 此定理可以推广到任意的度量空间，只要满足映射是[压缩映射](https://en.wikipedia.org/wiki/Contraction_mapping)，有兴趣的可以参看[Banach fixed-point theorem](https://en.wikipedia.org/wiki/Banach_fixed-point_theorem)

举个最简单的例子，对于单变量可导函数来说，只要导数绝对值在任意处小于1，那么总能通过迭代逼近这个不动点。注意上述定理只是充分条件，不一定必要。比如求平方根$\sqrt{a}$，可以随意选择一个正数$x_0$，然后迭代$\frac{1}{2}(x_0+\frac{a}{x_0})$来求解，显然迭代式的导数绝对值并非处处小于1。


总之，上面的定理就是说对于满足某些性质的映射，可以通过$x,\ f(x),\ f(f(x)),\ f(f(f(x))),\dots $来逼近$f(x)=x$的根。方程求根是不动点迭代的一大应用。

[牛顿法](https://en.wikipedia.org/wiki/Newton%27s_method)找零点：选择一个接近函数 $f(x)$零点的 $x_{0}$，计算相应的$f(x_0)$和切线斜率$f'(x_0)$。然后计算穿过点 $(x_{0},f(x_{0}))$并且斜率为$f'(x_0)$的直线和$x$轴的交点的$x$坐标，也就是求如下方程的解：$$f(x_{0})=(x_{0}-x)\cdot f'(x_{0})$$迭代公式为$$x_{n+1}=x_{n}-{\frac {f(x_{n})}{f'(x_{n})}}$$
迭代示意图[^1]如右：
![](/assets/images/NewtonIteration_Ani.gif)

# 无约束优化方法
笔者刚接触机器学习的时候，一直很困惑为什么梯度下降法是有效的。一般的解释为梯度方向是函数值变化最快的方向，沿着这个方向就能到函数的极值点。然而这种解释略形象化，并没有说明为什么要迭代。

我们知道，对于无约束凸函数来说，局部最优即全局最优，而局部最优对应的就是梯度为0点。本质上，梯度下降法和牛顿法都是在找梯度为0的极值点，也就是说其实是方程求根问题。这样自然就和我们上面的不动点迭代联系起来了。下面的联系并未有严格的收敛性证明，也没有对向量值函数的单独分析，仅是提供一种观点，欢迎探讨。

## 梯度下降方法

$$\alpha f'(x)=0$$
$$x_1 = x_0-\alpha f'(x_0)$$
$$x_2 = x_1-\alpha f'(x_1)$$
$$\cdots$$
$$x_n = x_{n-1}- \alpha f'(x_{n-1})$$

## 牛顿法
求梯度$f'(x)$的零点
$$x_{n+1}=x_{n}-{\frac {f'(x_{n})}{f''(x_{n})}}$$
上式也可以由二阶泰勒展式推导得到，感兴趣的可以参见[Newton's method in optimization](https://en.wikipedia.org/wiki/Newton%27s_method_in_optimization)以及博文[Numerical Optimization: Understanding L-BFGS](http://aria42.com/blog/2014/12/understanding-lbfgs)

上面是笔者对于不动点和梯度方法的一些不成熟想法。优化里的[Proximal Algorithms](https://en.wikipedia.org/wiki/Proximal_gradient_method)，其实也是以不动点作为其理论基础的，后续笔者会抽时间对[proximal method](http://web.stanford.edu/~boyd/papers/prox_algs.html)作单独介绍。

[^1]: 图片来源[wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/NewtonIteration_Ani.gif/600px-NewtonIteration_Ani.gif)
