---
layout: post
title: "XGBOOST和GBDT的不同"
description: ""
category: 机器学习
tags: []
---
知乎上有个人问xgboost与gbdt的不同，这里记录一下我的简单回复。首先xgboost是Gradient Boosting的一种高效系统实现，并不是一种单一算法。xgboost里面的基学习器除了用tree(gbtree)，也可用线性分类器(gblinear)，而GBDT则特指梯度提升决策树算法。xgboost相对于普通gbm的实现，可能具有以下的一些优势：

1. 显式地将树模型的复杂度作为正则项加在优化目标
2. 公式推导里用到了二阶导数信息，而普通的GBDT只用到一阶
3. 允许使用column(feature) sampling来防止过拟合，借鉴了Random Forest的思想，sklearn里的gbm好像也有类似实现
4. 实现了一种分裂节点寻找的近似算法，用于加速和减小内存消耗
5. 节点分裂算法能自动利用特征的稀疏性。
6. data事先排好序并以block的形式存储，利于并行计算
7. cache-aware, out-of-core computation
8. 支持分布式计算可以运行在MPI，YARN上，得益于底层支持容错的分布式通信框架rabit



          
    参考资料:  
[chentq的slides](http://homes.cs.washington.edu/~tqchen/pdf/BoostedTree.pdf)  
[chentq的paper](http://arxiv.org/abs/1603.02754)  
[chentq在52cs上的中文博文](http://www.52cs.org/?p=429)  
[微博分享的xgboost导读和实战](http://vdisk.weibo.com/s/vlQWp3erG2yo/1458225501)  
P.S. GBDT还有好多名字，AnyBoost, MART, TreeNet, GBRT, GBM等等。
