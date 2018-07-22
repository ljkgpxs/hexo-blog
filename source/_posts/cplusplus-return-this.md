---
title: C++中关于return *this的理解
url: 173.html
id: 173
categories:
  - Linux
date: 2016-10-31 12:51:31
tags:
---

先来看两段代码
```
#include <iostream>
using namespace std;

class A
{
public:
	int x;
	A* get()
	{
		return this;
	}
};
int main()
{
	A a;
	a.x = 4;

	if(&a == a.get())
	{
		cout << "yes" << endl;
	}
	else
	{
		cout << "no" << endl;
	}

	return 0;
}
```
上面一段代码的运行结果为:
```
yes
```
 
```
#include <iostream>
using namespace std;

class A
{
public:
	int x;
	A get()
	{
		return *this; //返回当前对象的拷贝
	}
};

int main()
{
	A a;
	a.x = 4;

	if(a.x == a.get().x)
	{
		cout << a.x << endl;
	}
	else
	{
		cout << "no" << endl;
	}

	if(&a == &a.get())
	{
		cout << "yes" << endl;
	}
	else
	{
		cout << "no" << endl;
	}

	return 0;
}
```
这段代码运行结果为：
```
4
no
```
(code from [http://m.blog.csdn.net/article/details?id=22220777](http://m.blog.csdn.net/article/details?id=22220777) )   根据这两段代码可知，return \*this的返回值与函数定义有关 如果函数定义返回类型为A&，则return \*this返回当前对象 如果函数定义返回类型为A，则return *this返回当前对象的拷贝 所以造成了以上两段代码的不同结果 另外，return this返回的是当前对象的指针
