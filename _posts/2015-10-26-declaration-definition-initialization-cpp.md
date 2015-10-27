---
layout: post
title: "C++回顾：声明、定义、初始化与赋值"
description: ""
category: 编程 
tags: []
---
最近在看C++ Primer，整理一下知识点，都是比较基础的东西，希望行家不要见笑。初学者一般很容易混淆声明(declaration)、定义(definition)、初始化(initialization)与赋值(assignment)的概念。
 
##声明(Declaration)
> Declaration: asserts the existence of a variable, function, or type defined elsewhere. Names may not be used until they are defined or declared.  [C++ Primer,p78]

声明的作用是指定变量的类型和名称，`makes a name known to the program`。区分声明和定义可以让C++支持分开编译，比如A.cpp中定义了变量`var1`，在B.cpp中只需要声明`var1`这个变量就可以直接使用。因为这样的用法，声明常常见于头文件中。源文件包含头文件之后，就可以使用这个变量，即使没有看到该变量的定义。
声明的语法如下：

```cpp
extern int i; // declaration
int j; // definition
extern double pi = 3.1416; // definition
```


##定义(Definition)
> Definition: allocates storage for a variable of a specified type and optionally initializes the variable. Names may not be used until they are defined or declared. [C++ Primer,p79]

定义是为变量分配存储空间，并可能进行初始化。定义是一种声明，因为定义的同时必然会指定变量的类型和名称，然而声明却不是定义。C++中变量的定义必须有且仅有一次，而变量的声明可以多次。变量一般不能定义在头文件中，除了const变量(local to a file)。

除了变量，类和函数也有定义的说法，总结如下：

* 对于类来说，一般定义在头文件中。因为编译器需要在每个源文件都看到类的定义才能编译成功；
* 对于一般函数来说，函数声明在头文件中，函数定义则在源文件中；
* 对于`inline`和`constexpr function`，编译器需要在每个源文件都看到定义，因此通常也定义在头文件中；

##初始化(Initialization)
初始化是指变量在创建的同时获得的初始值。虽然C++经常用`=`来初始化一个变量，但是赋值和初始化是两种不同的操作。赋值是变量定义后的操作，效果是改变变量的值，或者说是用新值来替换旧值；而初始化是在变量创建期获得一个值。两者具有本质的区别。下面分别介绍一下C++常见的初始化方式：

#### default initialization
当我们定义一个变量时，不提供initializer，那么这个变量就是默认初始化(default initialized)的。默认值由变量的类型和变量的定义位置来决定。

* 对于_built-in type_，默认值由变量的定义位置决定。在函数外部定义的全局变量(global variable)，函数内部定义的局部静态变量(local static object)全部初始化为0，函数内部定义的局部变量则是未初始化的；使用未初始化的变量值的行为是未定义的，编译器不保证不会自燃。
* 对于_class type_, 由类里的默认构造函数初始化。如果类定义里没有默认构造函数(显示或隐示)，则编译出错。


#### list initialization
C++11中提供了一种新的初始化方式，list initialization。注意这种初始化方式要求提供的初始值与要初始化的变量类型严格统一，用法如下，

```cpp
int units_sold = {0};		//ok: list initialization
int units_sold{0};			//ok: list initialization
long double pi = 3.1415
int a{pi}, b = {pi};		//error: narrowing conversion required.
int c(pi), d = pi;			//ok: implict conversion.
```

#### value initialization
value initialization里，built-in type变量被初始化为0，class type的对象被默认构造(一定要有)初始化。这种方式通常见于STL里的vector和数组，且经常与list initialization结合起来使用，为我们初始化全0数组提供了很大的便利。简单用法如下：

```cpp
vector<int> ivec(10);			//ten elements, each initialized to 0
vector<string> svec(10);		//ten elmenets, each an empty string
vector<string> v1 = {"a", "an", "the"};		//list initialized
int a[10] = {};					//ten elements, each initialized to 0
int a2[] = {1,2,3};				//list initialized
int a3[5] = {1,2,3};			//equivalent to a3[] = {1,2,3,0,0}
```

关于类的初始化比较复杂，整理几点：

* 编译器首先编译类成员的声明，包括函数和变量
* 整个类可见后，才编译函数体(所以不管定义顺序，函数里可以用类里的任何变量和函数)
* C++11提供了in-class initializers机制。Constructor Initializer List对变量进行初始化后，才进入构造函数。Constructor Initializer List里忽略的成员变量(为空则相当于全部忽略)，会由in-class initializers初始化，否则采取默认初始化进入构造函数体。

```cpp
class Sales_data {
	// 默认构造函数，负责调用in-class initializer初始化units_sold，以及默认初始化revenue
	Sales_data() = default;

	//bookNo由Constructor Initializer List初始化，units_sold由in-class initializer初始化
	//revenue默认(未)初始化
	Sales_data(const std::string &s): bookNo(s) { }
	std::string bookNo;				
	unsigned units_sold = 0;		//in-class initializer
	double revenue;	
};
```

##赋值(Assignment)
赋值的结果是左边的操作元，为左值，也就是说，下面的写法语法正确

```cpp
int a = 0;
(a = 0) = 1;					//the final value of a is 1.
```
* 需要注意的是，赋值操作符的优先级很低，该带括号的时候不能遗漏。
* 顺便提一下`++i`和`i++`的区别：前者将操作元增加，并且返回改变后的操作元；后者将操作数增加，返回原先值得拷贝作为结果。前置自增返回的结果是左值，后置自增返回的是右值。前置自增操作符做的无用功少，虽然C++编译器对int和指针类型的后置自增操作符作了优化，C++ Primer推荐如无特殊需求，优先使用前置自增操作符。
* 数组不支持拷贝初始化或者将一个整体赋值给另一个数组。

```cpp
int a[] = {0,1,2}
int a2[] = a;			// error: cannot initialize one array with another
a2 = a;					// error: cannot assign one array to another
