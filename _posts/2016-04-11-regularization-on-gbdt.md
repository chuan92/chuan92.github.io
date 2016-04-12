---
layout: post
title: "Regularization on GBDT"
description: ""
category: 机器学习
tags: []
---
之前一篇文章简单地讲了XGBoost的实现与普通GBDT实现的不同之处，本文尝试总结一下GBDT运用的正则化技巧。

### Early Stopping
[Early Stopping](https://en.wikipedia.org/wiki/Early_stopping)是机器学习迭代式训练模型中很常见的防止过拟合技巧，维基百科里如下描述:
> In machine learning, early stopping is a form of *regularization* used to *avoid overfitting* when training a learner with an *iterative method*, such as gradient descent.

具体的做法是选择一部分样本作为验证集，在迭代拟合训练集的过程中，如果模型在验证集里错误率不再下降，就停止训练，也就是说控制迭代的轮数（树的个数）。

XGBoost Python关于early stopping的[参数设置文档](https://github.com/dmlc/xgboost/blob/master/doc/python/python_intro.md#early-stopping)非常清晰，API如下：

```python
# code snippets from xgboost python-package training.py
def	train(..., evals=(), early_stopping_rounds=None)
	"""Train a booster with given parameters.
	Parameters
    ----------
	early_stopping_rounds: int
        Activates early stopping. Validation error needs to decrease at least
        every <early_stopping_rounds> round(s) to continue training.
	"""
```


Sklearn的GBDT实现虽然可以添加early stopping，但是比较复杂。官方没有相应的文档和代码样例，必须看[源码](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/ensemble/gradient_boosting.py#L931)。实现的时候需要用户提供monitor回调函数，且要了解源码内部\_fit_stages函数的locals，总之对新手很不友好：

```python
#code snippets from sklearn.ensemble.gradient_boosting
class BaseGradientBoosting(six.with_metaclass(ABCMeta, BaseEnsemble,
						   _LearntSelectorMixin)):
    """Abstract base class for Gradient Boosting. """
	...
	def fit(self, X, y, sample_weight=None, monitor=None):
		"""Fit the gradient boosting model.
        Parameters
		----------
		monitor : callable, optional
            The monitor is called after each iteration with the current
            iteration, a reference to the estimator and the local variables of
            ``_fit_stages`` as keyword arguments ``callable(i, self,
            locals())``. If the callable returns ``True`` the fitting procedure
            is stopped. The monitor can be used for various things such as
            computing held-out estimates, early stopping, model introspect, and
            snapshoting.
		"""
```
对Sklearn感兴趣的可以看这篇文章[Using Gradient Boosting (with Early Stopping)](*https://henri.io/posts/using-gradient-boosting-with-early-stopping.html)，里面有回调函数monitor的参考实现。


### Shrinkage
Shrinkage就是将每棵树的输出结果乘一个因子($0<\nu<1$)，其中$\Sigma_{j=1}^{J_m}\gamma_{jm}I(x\in R_{jm})$是第m棵的输出，而$f(m)$是前m棵树的ensemble: $$f_m(x) = f_{m-1}(x) + \nu\cdot\Sigma_{j=1}^{J_m}\gamma_{jm}I(x\in R_{jm})$$
ESL书中这样讲：
> The parameter $\nu$ can be regarded as controlling the leanring rate of the boosting procedure

$\nu$和迭代轮数M(树个数)是一个tradeoff，推荐的是$\nu$值设置小一点(如0.1)，而M设置大一些。这样一般能有比较好的准确率，代价是训练时间变长(与M成比例)。

下面是Sklearn的实现关于该参数设置的片段，XGBoost类似：

```python
#code snippets from sklearn.ensemble.gradient_boosting
class GradientBoostingClassifier(BaseGradientBoosting, ClassifierMixin):
    """Gradient Boosting for classification."""

	def __init__(self, ..., learning_rate=0.1, n_estimators=100, ...):
	"""
	Parameters
    ----------
	learning_rate : float, optional (default=0.1)
        learning rate shrinks the contribution of each tree by `learning_rate`.
        There is a trade-off between learning_rate and n_estimators.
	n_estimators : int (default=100)
        The number of boosting stages to perform. Gradient boosting
        is fairly robust to over-fitting so a large number usually
        results in better performance
	"""
```

### Subsampling
Subsampling其实源于bootstrap averaging(bagging)思想，GBDT里的做法是在每一轮建树时，样本是从训练集合中无放回随机抽样的$\eta$部分，典型的$\eta$值是0.5。这样做既能对模型起正则作用，也能减少计算时间。

事实上，XGBoost和Sklearn的实现均借鉴了随机森林，除了有样本层次上的采样，也有特征采样。也就是说建树的时候只从随机选取的一些特征列寻找最优分裂。
下面是Sklearn里的相关参数设置的片段，

```python
#code snippets from sklearn.ensemble.gradient_boosting
class GradientBoostingClassifier(BaseGradientBoosting, ClassifierMixin):
    """Gradient Boosting for classification."""

	def __init__(self, ..., subsample=1.0, max_features=None,...):
	"""
	Parameters
    ----------
	subsample : float, optional (default=1.0)
        The fraction of samples to be used for fitting the individual base
        learners. If smaller than 1.0 this results in Stochastic Gradient
        Boosting. `subsample` interacts with the parameter `n_estimators`.
        Choosing `subsample < 1.0` leads to a reduction of variance
        and an increase in bias.
    max_features : int, float, string or None, optional (default=None)
        The number of features to consider when looking for the best split:
	"""
```

### Regularized Learning Objective
将树模型的复杂度作为正则项显式地加进优化目标里，是XGBoost实现的独到之处。
$$\mathcal{L}^{(t)} = \sum_{i=1}^n l(y_i, y_i^{*(t-1)} + f_t(\mathrm{x}_i)) + \Omega(f_t)$$
where 
$$ \Omega(f)=\gamma T+ \frac{1}{2}\lambda\||w\||^2$$

其中$y_i^{*(t)}$是第t轮第i个instance的预测值，$f_t$是第t轮建的树，$T$是树叶结点数目，$w$是树叶结点的输出，$\gamma, \lambda$是正则化参数。深入了解加了正则后如何推导剃度更新的可以看XGBoost的[论文](http://arxiv.org/abs/1603.02754)。

我个人的看法是将树模型的复杂度作为正则化项加在优化目标，相比自己通过参数控制每轮树的复杂度更直接，这可能是XGBoost相比普通GBDT实现效果更好的一个很重要的原因。很遗憾，Sklearn暂时无相应的实现。

### Dropout
Dropout是deep learning里很常用的正则化技巧，很自然的我们会想能不能把Dropout用到GBDT模型上呢？[AISTATS2015](http://www.jmlr.org/proceedings/papers/v38/)有篇文章[DART: Dropouts meet Multiple Additive Regression Trees](DART: Dropouts meet Multiple Additive Regression Trees)进行了一些尝试。


文中提到GBDT里会出现*over-specialization*的问题：
> Trees added at later iterations tend to impact the prediction of only
a few instances, and they make negligible contribution
towards the prediction of all the remaining instances.
We call this issue of subsequent trees affecting the prediction of only 
a small fraction of the training instances *over-specialization*.

也就是说前面迭代的树对预测值的贡献比较大，后面的树会集中预测一小部分样本的偏差。Shrinkage可以减轻*over-specialization*的问题，但不是很好。作者想通过Dropout来平衡所有树对预测的贡献，如下图的效果：
![](/assets/images/gbdt_dart.png)

具体的做法如下：
> DART divergesfrom MART at two places. First, when computing the
gradient that the next tree will fit, only a random subset
of the existing ensemble is considered.
The second place at which DART diverges from MART
is when adding the new tree to the ensemble where
DART performs a normalization step.

简单说就是每次新加一棵树，这棵树要拟合的并不是之前全部树ensemble后的残差，而是随机抽取的一些树ensemble；同时新加的树结果要规范化一下。

这种新做法对GBDT效果的提升有多明显还有待大家探索尝试。
