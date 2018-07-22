---
title: Ubuntu 17.04 安装网易云音乐
url: 308.html
id: 308
categories:
  - Linux
date: 2017-02-14 14:27:01
tags:
---

到官方安装下载Ubuntu端网易云客户端发现只提供了Ubuntu 16.04的，然后下载回来在Ubuntu 17.04上安装，发现安装不了，由于依赖不满足，安装不成功

解包发现，有一个依赖是 libqt5libgtk2，但是这个包在Ubuntu 17.04上被取代了，新包的名字是qt5-style-plugins，于是修改之后重新打包，便可以安装使用

解包修改过程如下

  
```
ar x netease-cloud-music\_1.0.0\_amd64_ubuntu16.04.deb
tar zxvf control.tar.gz
tar xvf data.tar.xz
rm md5sum debian-binary control.tar.gz data.tar.xz
mkdir DEBIAN
mv control DEBIAN/
```
之后修改DEBIAN/control文件，把libqt5libgtk2换成qt5-style-plugins

  

然后打包

  
```
$ ls
DEBIAN usr
$ dpkg-deb -b ./ netease-music-ubuntu17.04.deb
```
打包之后的deb文件就可以安装了

  

  
```
$ sudo dpkg -i ./netease-music-ubuntu17.04.deb
$ sudo apt-get -f install
```
  

  

博主打包好的文件送上：链接: https://pan.baidu.com/s/1i4ZoaIp 密码: nrg8（备用链：[mega网盘](https://mega.nz/#!sZxQyApB!CRq8qy0Lg02qUopzM2oT4YNvMczmh25SYzadYAxun7w)）
