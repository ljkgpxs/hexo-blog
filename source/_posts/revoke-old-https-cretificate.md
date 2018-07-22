---
title: 更换博客HTTPS证书
url: 370.html
id: 370
categories:
  - misc
date: 2017-08-02 22:12:34
tags:
---

上一个证书是图省事,以及证书有效期长,在阿里云直接弄得免费赛铁门证书,然而最近看到新闻,说Chrome将对赛铁门所有证书不信任

这次换了Let's Encrypt, 似乎也是Google极力推荐的颁发机构, 过程极其简单..安装官方推荐的Certbot, 安装之后, 直接运行Certbot, 根据提示, 输入一些东西, 简直傻瓜式领证, 哈哈哈哈嗝

不过,Let's Encrypt的免费证书只有三个月有效期, 这也是当初我没有首选他的愿意, 嫌麻烦..但是没想到Certbot也可以傻瓜式renewal

  

顺便吐槽一句...似乎和阿里云上的大多数证书颁发机构都要被Chrome不信任, 这背后恐怕...

Let's Encrypt网站: [https://letsencrypt.org](https://letsencrypt.org)