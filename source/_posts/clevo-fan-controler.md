---
title: 蓝天笔记本控制风扇转速
url: 361.html
id: 361
categories:
  - Linux
date: 2017-07-23 12:37:08
tags:
---

最近越来越热,笔记本的待机温度都50多度,之前用Windows的时候待机也就40多度的样子. 跑一跑程序发现cpu温度飙升到80多甚至90多度,风扇依旧缓慢的转啊转.

我的笔记本是神州z7 D0, 蓝天P650SE模具,cpu风扇是两个,gpu风扇是一个,然而在ubuntu下,似乎没有驱动,安装lm-sensors不能控制风扇转速,在网上搜索发现好多人都是转速过高,然而我的本子风扇是转速过低...

然后发现clevo-indicator这个程序,是蓝天的一个开源项目,给Linux用户使用,用来控制风扇转速, 安装步骤如下:

  
```
$ sudo apt install libappindicator3-dev  # 安装依赖
$ git clone https://github.com/SkyLandTW/clevo-indicator.git
$ cd clevo-indicator/
$ make
$ make install
```
  

最后直接运行, clevo-indicator, 它会定时监测cpu温度, 根据温度调节风扇转速.

可能由于博主使用的是GNOME桌面环境, 并没有发现通知栏上有手动调节选项, 不过一般的也用不到手动调节, 运行这个之后, 笔记本温度降了不少,可以安心跑一些大程序了.

为了然它登录桌面环境之后自动运行,打开终端,运行

  
```
$ gnome-session-properties
```
  

手动添加 /usr/local/bin/clevo-indicator, 这样就可以做到登录后自动后台运行, 至于别的桌面环境, 大家自行百度一下吧
