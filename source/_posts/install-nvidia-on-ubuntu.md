---
title: ubuntu安装Nvidia驱动
url: 101.html
id: 101
categories:
  - Linux
date: 2016-05-14 10:39:28
tags:
---

我的双显卡，独显是GTX970M，运行Ubuntu 16.06， Kde5桌面环境

**拯救黑屏！！！！！**

经过多次测试，官方的驱动我安装之后会黑屏，只好在recovery模式下安装Ubuntu维护的Nvidia驱动，据说Ubuntu维护的nvidia驱动，Nvidia官方也有参与

安装步骤如下（进入recovery模式安装）
```
# apt-get purge nvidia-* 
# apt-get remove xserver-xorg-video-nouveau  # 卸载Nouveau驱动 
# apt-get install nvidia-361    # 现在最新的版本，也可以这样安装apt-get install nvidia-current
```
重启并 Ctrl+Alt+F1进tty1，以root登录并运行
```
# update-grub2
# update-initramdisk -u
# nvidia-xconfig
```
重启就出现登录界面，进入kde环境之后，所有特效可能会消失，然后终端运行
```
$ nvidia-settings
```
注销再登录,Alt+空格，搜索opengl，选择混成器

![screenshot1](/images/nvidia-fix-screen1.png)

重新设置一下渲染后端，保存后重启即可

![screenshot2](/images/nvidia-fix-screen2.png)
