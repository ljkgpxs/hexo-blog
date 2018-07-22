---
title: 修复Ubuntu WiFi不稳定的问题(RTL8723be)
url: 151.html
id: 151
categories:
  - Linux
date: 2016-07-19 15:44:40
tags:
---

用过Ubuntu的应该有不少人发现，WiFi特别不稳定，经常用着用着就无法连接网络，虽然WiFi管理还在显示已连接，除非重启或者重新加载网卡驱动。

这个问题应该会发生在Realtek的网卡上，Ubuntu源中自带的网卡驱动有不少问题，所以导致这种情况。  

下面介绍一下如何解决这个问题，一共有两种方案，首先介绍相对简单的方法：

  

*   #### 编辑配置文件
    

你要做的就是在配置文件中添加一行文字，较为简单。执行一下命令：
```
$ sudo vim /etc/modprobe.d/rtl8723be.conf
```
然后在文件末尾添加：
```
options rtl8723be fwlps=N ips=N
```
![screenshot5](http://ljkgpxs.xyz/wp-content/uploads/2016/07/screenshot5.png)

保存并退出，然后重启电脑就可以了。   

  

*   #### 重新编译安装驱动
    

这种方法较为复杂，但是粗暴、有效，唯一不足之处就是每次更新内核之后，都要重新编译安装驱动，也是博主采用的方法，效果很好！

首先需要安装一些工具：
```
$ sudo apt-get install linux-headers-generic build-essential git
```
然后获取驱动源代码：
```
$ git clone https://github.com/lwfinger/rtlwifi_new.git
```
注：rtlwifi_new包含 rtl8192ce, rtl8192se, rtl8192de, rtl8188ee, rtl8192ee, rtl8723ae, rtl8723be, rtl8821ae 这些网卡的驱动，默认安装所有驱动

切换进源码目录，并编译安装：
```
$ cd rtlwifi_new/
$ make
$ sudo make install
```
之后重启即可，要注意的是：**更新内核之后要手动重新编译安装**  

更新内核后重启，执行以下命令即可：
```
$ cd rtlwifi_new/
$ make clean
$ make
$ sudo make install
```
