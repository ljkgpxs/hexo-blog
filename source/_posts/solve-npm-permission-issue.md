---
title: npm安装全局模块碰到权限问题修复方法
tags:
  - AWS
  - Lambda
  - nodejs
  - npm
  - SAM Local
url: 376.html
id: 376
categories:
  - Linux
date: 2017-10-24 11:26:21
---

前段时间比较忙,好久没更博客. 最近接了一个小项目, 使用nodejs开发后端, 由于要使用aws lambda, 全局安装SAM local时总是出现permission denied

我的环境是Ubuntu 17.10, 使用apt安装的nodejs和npm

AWS的官方文档也给出了一些方法来解决权限问题, 其中最常用的方法就是更改目录以及文件的拥有者

Ubuntu默认的npm全局模块会安装在/usr/local/lib/node\_modules/, 这是因为npm的prefix变量默认设置为了/usr/local/, 可以通过下面的命令查看prefix路径

  
```
$ npm config get prefix
/usr/local/
```
  

  

默认为/usr/local/就导致一个问题, 安装全局模块就必须以root用户执行npm, 例如安装aws-sdk模块到全局:

  
```
$ sudo npm install -g aws-sdk
```
  

这个是没有问题的, 可以很正常的安装到/usr/local/lib/node\_modules

  

  

  

根据aws的文档, 安装SAM Local时, 就会出现权限问题, 无论使用sudo, 还是su之后使用npm安装SAM Local, 都会出现权限问题, 其中的主要因素, 就是调用了go-npm这个程序, 它使用非root组的用户执行go-npm, 在写入/usr/loca/时就会导致权限问题, 具体用了哪个用户执行了go-npm, 可以在安装模块期间, 通过ps aux查看, 然后把/usr/local/的拥有者更换为这个用户, 但是这个方法我不太推荐的, 这个可能会让后来程序出现更多的权限问题

  

这里就采用另外一种方法, 更改prefix变量的值, 让npm把模块安装在用户目录里面, 这样就可以不用大动干戈的修改/usr/local/的拥有者.

很简单的, 只要执行下面的命令就可以实现 :
```
$ npm config set prefix /home/username/npm_repo
```
  

把username换成你的用户名, 路径换成自己想要存放的地方即可.

或者采用另外一种等效的方法, 创建或者修改~/.npmrc, 把下面的内容添加到文件末尾 :

  
```
prefix=/home/username/npm_repo
```
  

之后安装全局模块就不再需要sudo, 只要直接执行即可 :

  
```
$ npm install -g aws-sam-local
\> aws-sam-local@0.2.2 postinstall /home/username/npm\_repo/lib/node\_modules/aws-sam-local
\> go-npm install

Downloading from URL: https://github.com/awslabs/aws-sam-local/releases/download/v0.2.2/sam\_0.2.2\_linux_amd64.tar.gz
/home/username/npm_repo/lib
└─┬ aws-sam-local@0.2.2 
  └─┬ go-npm@0.1.8
    └─┬ request@2.83.0
      └─┬ har-validator@5.0.3
        └── ajv@5.2.4 
```
  

注意到, 它会把全局模块安装在 /home/username/npm\_repo/lib/node\_modules, 全局模块路径变了, 你就需要修改一些东西

修改~/.bashrc, 把/home/username/npm\_repo/bin加到环境变量PATH里

另外, 为了让js代码可以require到全局模块, 把/home/username/npm\_repo/lib/node\_modules/ 加到环境变量NODE\_PATH里, 如果没有NODE\_PATH, 就创建一个:

  
```
export NODE\_PATH="/home/username/npm\_repo/lib/node_modules"
```
  

之后重新加载 bashrc或者重启, 就可以了
