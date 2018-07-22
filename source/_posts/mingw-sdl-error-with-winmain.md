---
title: MinGW下编译SDL程序报错WinMain未定义的引用
url: 185.html
id: 185
categories:
  - Linux
date: 2016-11-07 13:39:54
tags:
---

SDL2是通过MinGW的pacman安装的，在编译SDL程序的时候碰见

  
```
/usr/lib/gcc/x86_64-pc-msys/4.9.2/../../../../lib/libmsys-2.0.a(libcmain.o)：在函数‘main’中：
/msys_scripts/msys2-runtime/src/msys2-runtime/winsup/cygwin/lib/libcmain.c:39：对‘WinMain’未定义的引用
/msys\_scripts/msys2-runtime/src/msys2-runtime/winsup/cygwin/lib/libcmain.c:39:(.text.startup+0x7f): relocation truncated to fit: R\_X86\_64\_PC32 against undefined symbol `WinMain'
collect2: 错误：ld 返回 1
Makefile:9: recipe for target 'all' failed
make: *** [all] Error 1
```
  

碰见这种错误的原因是在SDL2库中已经宏定义过main，只需要在main函数前加一句undef即可

  
```
#undef main
int main(int argc, char **argv)
```
