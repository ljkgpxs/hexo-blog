---
title: 【c语言】指向多维数组的指针
url: 80.html
id: 80
categories:
  - Linux
date: 2016-04-27 16:20:05
tags:
---

这里有比较详细的解释[http://learn.tsinghua.edu.cn/kejian/data/77130/138627/html-chunk/ch23s07.html](http://learn.tsinghua.edu.cn/kejian/data/77130/138627/html-chunk/ch23s07.html)对于int  (*a)[10]与int a[10]; 前者即表示指向int  [10]类型的指针，而后者则表示含有10个int类型的数组 对于上文中的例子，加了点注释

```
#include <stdio.h>
int main()
{
	char a[4][3][2] = { { {'a', 'b'}, {'c', 'd'}, {'e', 'f'}},
		{ {'g', 'h'}, {'i', 'j'}, {'k', 'l'}},
		{ {'m', 'n'}, {'o', 'p'}, {'q', 'r'}},
		{ {'s', 't'}, {'u', 'v'}, {'w', 'x'}}};

	char (*pa)[2] = &a[1][0]; // &a[1][0] 表示 char (*)[2]
	char (*ppa)[3][2] = &a[1]; // &a[1] 表示 char (*)[3][2]
	printf("%c %c %c\\n", a[2][2][1], pa[5][1], ppa[1][2][1]); // 都是取a中的r
	return 0;
}
```
