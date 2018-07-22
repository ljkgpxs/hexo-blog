---
title: 教大家搭建本地Henkaku
url: 164.html
id: 164
categories:
  - Linux
date: 2016-08-06 15:39:49
tags:
---

Henkaku就不多介绍了，搭建它的目的还是很好的，psv不用联网，电脑开着就能做破解。

  

本文章的前提是Ubuntu或者Debian系统，至于windows下，搭建MinGW，使用python+go的方法很不错，搭建方法大同小异！

  

搭建方法有两种方法，一种是使用较为复杂的apache+php，另一种是较为简单的python+go

git安装和环境搭建之类的我就不说了（其实是我懒得说）

首先到（gayhub）github下载源代码：
```

git clone https://github.com/henkaku/henkaku.git
```
然后有两种方法，先来介绍简单的，使用python和go

安装以下软件：
```
sudo apt-get install python3 golang-go
```
然后需要知道你的电脑局域网ip，获取方法太多了。。是在不会就百度吧，我以我的电脑局域网ip 192.168.199.5为例：

首先运行这个，这一步是为开服做准备：
```
cd henkaku/
./build.sh http://192.168.199.5:4000/ http://192.168.199.5:8080/pkg
cd host/
```
build.sh的第一个参数不能忘了，末尾加一个“/”

之后进行开服，分别在两个终端运行以下两个命令
```
python -m SimpleHTTPServer 8080
go run stage2.go -payload stage2.bin -port 4000
```
这样就搭建好了，用psv浏览器浏览这个地址 ： http://192.168.199.5:8080/exploit.html  就可以了

如果以后重启了电脑，重新运行开服命令就好了

  

另外一种复杂的方法采用apache+php，这种方法搭建之后很方便，只要开启apache就可以用了（如果是ubuntu或者debian，开机会自动启动apache）

简单了说，输入以下命令：
```
sudo apt-get install apache2 libapache-mod-php    # 安装apache和php
git clone https://github.com/henkaku/henkaku.git
cd henkaku/
./build.sh http://your\_ip/stage2.php/ http://your\_ip/pkg # 为开服做准备，把your_ip换成你的电脑局域网ip 
cd ./host/
cp exploit.html index.html  # 复制exploit.html到index.html，方便psv访问
sudo cp -rpd ./* /var/www/html/   # 把开服文件复制到服务器根目录里面
```
然后用psv浏览器访问你的电脑ip就好了，例如你的电脑局域网ip是192.168.1.100，就在psv浏览器地址栏输入192.168.1.100就好了

  

  

  

最重要的来了！搭建局域网henkaku的目的是防止psv连接到psn，防止被ban，有了局域网henkaku之后，进设置，把DNS换成127.0.0.1，这样可以防止连接psn，能一定程度的达到防ban的作用，有可能可以阻止系统更新（未测试）
