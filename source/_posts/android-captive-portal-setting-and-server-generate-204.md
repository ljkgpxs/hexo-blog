---
title: 给安卓系统去除信号叹号
url: 373.html
id: 373
categories:
  - Linux
date: 2017-08-10 22:26:48
tags:
---

身为大陆人, 对于墙这个东西, 我是很无奈的, 叹号的原因就在于墙, 当然, 你可以翻墙去叹号, 但这不是长久的方法, 而且有时候翻墙, 可能去不掉叹号

这篇文章可能来的比较晚,随便一百度都可以搜到一大堆关于去叹号的教程,甚至还有去叹号app, 不过自己写一遍, 记在自己的博客, 比较容易翻看

  

最近又开始玩机了, 各种刷刷刷, 果然我还是不喜欢稳定的系统, 更喜欢更新频繁, 花样多的系统, 而且我比较偏向安卓原生风格, 不太喜欢大多数国内定制UI, 特别讨厌那个啥啥ADUI. 然后类似原生的ROM中, 我比较讨厌魔趣, 首先作为一个活跃的ROM, 他竟然限制下载速度, 简直不能忍, 以及系统更新界面里嵌入广告, 只有捐赠(伸手要钱)多少多少RMB之后才去广告, 去除下载限速....这种风格一股弄弄的国产味道...顺便吐槽一下有些软件拿捐赠这个借口, 来解锁软件里的某些功能, 作为收费功能, 拿着捐赠当做借口, 很难看..

废话不多说了, 切入正题吧..

自从Android 5.0开始就有叹号这个东西, 它会后台检测网络, 网络不同则会出现叹号, 愿意是好的, 但是到了墙内, 反而变成了多余的功能

然后Android 7.0开始要求使用HTTPS, Android 7.1.1开始更换了内部变量, 和以往有点不同

  

对于Android 5.0 ~ 6.0, 可以使用以下命令去除叹号
```
adb shell "settings put global captive_portal_server g.cn"
```

到了Android 7.0, 上面这个命令不能用了, 因为g.cn的HTTPS有问题, 好吧, 自己搭建

考虑到兼容Android 5.0 ~ 6.0, 检测系统会访问 http://aaaaa.bbb/generate_204 来测试网络, 如果服务器返回204, 那么代表网络正常, 如果超时或者出现其他问题, 就会出现叹号

  

所以我的目标就是让https://ljkgpxs.xyz/generate_204 返回204, 方法很简单, 这里以Apache为例

修改Apache配置文件, 添加下面这一行:
```
Redirect 204 /generate_204
```
重启Apache, 测试一下 :
```
curl https://ljkgpxs.xyz/generate_204 -D /tmp/code; cat /tmp/code
```
就可以看到204的Response Header了:
```
HTTP/1.1 204 No Content
Date: Thu, 10 Aug 2017 14:18:26 GMT
```
如果是Nginx, 加入以下设置即可:
```
location /generate_204 { return 204; }
```
  

好了, 服务器架设好了, 之后就可以用adb设置了

对于Android 7.0, 用下面的命令 :
```
adb shell "settings put global captive\_portal\_server ljkgpxs.xyz"
```
  

对于7.1.1以上, 储存URL的变量有变:
```
adb shell "settings put global captive\_portal\_https\_url https://ljkgpxs.xyz/generate\_204"
```
  

用adb设置之后, 只要重启手机就可以生效了!
