---
title: Unreal engine4在linux安装
url: 296.html
id: 296
categories:
  - Linux
date: 2017-02-11 17:36:51
tags:
---

首先说明一下，如果你使用KDE桌面，使用Unreal engine editor会造成KDE抽风，大概是因为OpenGL

在UE4官网注册账号，然后连接自己的Github账号，就可以获取UE4源代码 [https://github.com/EpicGames/UnrealEngine](https://github.com/EpicGames/UnrealEngine)

  

git clone之后进行编译

  
```
cd UnrealEngine/
./Setup.sh
./GenerateProjectFiles.sh
make
```
编译大概需要一个小时左右，看你的电脑配置了

  

博主Clone的是UE 4.14，运行的时候碰到一点问题，编译好之后，先不要着急运行，由于Linux区分大小写文件夹，UE 4.14有个bug，用下面的命令解决

  
```
cd Engine/Binaries/
ln -s DotNET DotNet
```
如果做安卓开发，建议安装按官方的文档安装NVPACK，编译好之后，源码目录中已经下载好了NVPACK的安装程序，运行安装

  

  
```
bash ./Engine/Extras/AndroidWorks/Linux/CodeWorksforAndroid-1R5-linux-x64.run
```
按照官方文档说明，安装**CodeWorks for AndroidWorks1R4**版本，不过安装的时候可能一直提示NDK安装失败，不用管它，其实已经安装成功，提示安装失败后，把NDK勾选掉，点Next继续

  

如果你的电脑已经安装了SDK之类的套件，先把你设置的环境变量删掉，最后重新打开一下终端以加载环境变量

安装NVPACK后还要运行一遍Setup.sh，用来下载依赖

  
```
cd UnrealEngine/
./Setup.sh
```
都完成后就可以运行Unreal engine editor了

  

  
```
cd Binaries/Linux/
./UE4Editor
```
第一次启动比较慢，它会再编译一些东西，耐心等待一会
