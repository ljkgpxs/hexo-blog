---
title: 网站迁移记录
url: 315.html
id: 315
categories:
  - Linux
date: 2017-03-18 19:42:05
tags:
---

一直在使用Digitalocean的vps，但是DO经过一次改线路，我这里电信连接服务器的速度已经不及以前了

于是想换服务器，而且DO价钱还是那么贵，在vultr以及Linode已经可以5刀租到1GB内存的服务器了，vultr甚至推出2.5美元/月的服务器

这次选择了Linode的新加坡服务器，在我这里速度很不错，于是在博客里写下迁移过程，方便下次更换服务器

系统为ubuntu 16.04 LTS，首先安装一些常用且必备的软件
```
# apt-get update
# apt-get dist-upgrade
# reboot
# apt-get install apache2 build-essential git unzip screen python-pip python-setuptools libapache2-mod-php mysql-server mysql-client phpmyadmin php7.0-zip vim p7zip unrar
```
其中安转phpmyadmin，一是为了博客迁移方便，二是方便自动安装一些必要依赖（顺便吐槽一下Linode的vps重启是真慢。。。）

  

加载apache的mod

  
```
# a2enmod access_compat rewrite alias auth_basic authn_core authn_file authz_core authz_host authz_user autoindex deflate dir env expires filter headers mime mpm_prefork negotiation php5 rewrite setenvif socache_shmcb ssl status
# a2ensite default-ssl
# phpenmod zip 
```
进入老服务器phpmyadmin导出WordPress的数据库，然后进新服务器的phpmyadmin，首先点左边New新建数据库，数据库名称要和老服务器上的名称相同，不然会造成进入WordPress时提示无法连接数据库，然后使用导入功能将数据库导入WordPress的数据库

  

进入老服务器打包/var/www/html/，传到新服务器上，并解包到/var/www/html/

根据需要修改/etc/apache2/sites-enabled/default-ssl.conf和/etc/apache2/mods-enabled/ssl.conf

修改/etc/php/7.0/apache2/php.ini中的upload\_max\_size为128M

  

找到/etc/apache2/apache2.conf中的<Directory /var/www/>

修改下面的AllowOverride Node为Allowoverride All

重启apache2

  
```
/etc/init.d/apache2 restart
```
  

首先修改本机hosts文件，将域名解析到新服务器IP，用浏览器测试网站无误后再修改DNS解析！

最后到域名注册商去修改解析，解析到新ip，等待一段时间就可以正常访问新服务器上的博客了
