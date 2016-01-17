---
layout: post
title: "MATLAB里的bsxfun, cellfun和arrayfun"
description: ""
category: 编程 
tags: []
---
MATLAB作为科学计算领域里比较流行的一门语言，天然地和向量化思维联系在一起。这篇文章简单讲一下bsxfun，cellfun和arrayfun。
##1. bsxfun
MATLAB的官方doc里如下描述：
>
bsxfun: apply element-by-element binary operation to two arrays with singleton expansion enabled.  
C = bsxfun(fun,A,B)

Singleton expansion的意思是若A或B的某一个维度的size为1，那么就沿着这一维度复制，使它和另一个矩阵的对应维度size一样。和repmat不同，bsxfun的复制是虚拟的；且是多线程实现，所以速度很快。另一方面，使用bsxfun的代码更简洁美观，符合MATLAB的表达方式。

下面的例子是对数据进行中心化处理

```matlab
% each row in A is an example.
A = [1 2 10; 1 4 20;1 6 15] ;
C = bsxfun(@minus, A, mean(A))
```
更厉害的的是用bsxfun计算距离矩阵


```matlab
d=300; k=400; n = 500;
A = rand(d,k); B = rand(d,n); dist = zeros(k,n);
%计算任意 A(;,ii)到B(:,jj) 的欧式距离

% naive implementaion
tic
for ii = 1:k
   for jj = 1:n
       dist(ii,jj) = sqrt(sum((A(:,ii) - B(:,jj)).^2));
   end
end
toc									%Elapsed time is 0.368322 seconds

% more effective manner, using bsxfun
tic
dist = sqrt(-bsxfun(@minus,bsxfun(@minus,2*A'*B,sum(A.^2,1)'),sum(B.^2,1)));
toc									%Elapsed time is 0.006528 seconds
```


##2. cellfun
> cellfun: apply function to each cell in cell array.   
> [A1,...,Am] = cellfun(func,C1,...,Cn)  
> The ith iteration corresponds to the syntax [A1(i),...,Am(i)] = func(C1{i},...,Cn{i})

cellfun主要是提供一种更简洁的书写方式。它的速度一般情况下并不如for loop快。
并且特别注意的是使用fun的方式极大地影响速度，在MATLAB2013a(i7 4750, 8G RAM)中如下测试，

```matlab
c = mat2cell(1:1e5,1,repmat(1,1,1e5));
tic, d=cellfun('isempty',c); toc			 %Elapsed time is 0.001119 seconds
tic, d=cellfun(@isempty,c); toc				 %Elapsed time is 0.062364 seconds
tic, d=cellfun(@(x) isempty(x),c); toc		 %Elapsed time is 0.389135 seconds

tic
for i=1:length(c)
    d(i)=isempty(c{i});
end
toc											 %Elapsed time is 0.057377 seconds

A = cell(100,1);B = cell(100,1);D = cell(100,1);
for i=1:100
    A{i}=rand(500,500);
    B{i}=rand(500,500);
end
tic;C = cellfun(@plus, A, B, 'un',false);toc %Elapsed time is 0.087974 seconds
tic
for i=1:100 
    D{i}=A{i}+B{i}; 
end
toc											 %Elapsed time is 0.079868 seconds
```
使用函数句柄(function handle)或者匿名函数(anonymous function)都会让cellfun速度变慢。建议在对代码的简洁性要求高，或者func为'isempty'这样特殊的函数才使用cellfun。更多关于cellfun的讨论可以参见博客[^1]以及Mathworkd论坛[^2]

##3. arrayfun
> arrayfun: apply function to each element of array  
> [B1,...,Bm] = arrayfun(func,A1,...,An)  
> The ith iteration corresponds to the syntax [B1(i),...,Bm(i)] = func(A1(i),...,An(i))

与cellfun类似，arrayfun的速度也不是很理想。Stackoverflow上的问题[^3]有非常详细的评测。这里不再赘述。

##4. 题外话
现在有很多语言和库或多或少蚕食了MATLAB的份额，比如Julia，Numpy, Torch等。MATLAB的好处大概是矩阵操作起来非常直观，有很多库。从我的观察来说，机器学习的学术界用Matlab的比较多，因为比较侧重建模和原型实现，工业界使用可能偏少一些。


[^1]: http://undocumentedmatlab.com/blog/cellfun-undocumented-performance-boost
[^2]: http://www.mathworks.com/matlabcentral/newsreader/view_thread/253815 
[^3]: http://stackoverflow.com/questions/12522888/arrayfun-can-be-significantly-slower-than-an-explicit-loop-in-matlab-why
