---
title: 博客迁移至Github pages
tags:
  - github
  - hexo
  - blog
categories:
  - Linux
date: 2018-07-24 16:10:39
---

## 迁移之前:

之前我用的是WordPress, 前几天服务器被**墙**, 也是凉了, 想一想, 自己维护博客确实麻烦, 还要考虑到服务器的负载, 然后想起来[gIthub.io](https://github.io)提供静态页面.  

看了官方文档之后, Github官方推荐使用Jekyll[^1]作为博客后端, 支持Markdown, 原话是这样说的:
> Using Jekyll, you can blog using beautiful Markdown syntax, and without having to deal with any databases.

经过调查之后发现还有另一款写博客的神器,也支持Markdown, 名字是[Hexo](https://hexo.io), 经过对比:

|         | Hexo   | Jekyll |
|:------|:---------:|:-------:|
|后端  | NodeJs | Ruby  |
|文档  | 有中文   |英文    |
| 评价 | 较好      |没Hexo好 |



查了一些对比资料之后, 就发现大多数国内用户都比较推崇Hexo, 有不少人说Hexo生成静态页面比Jekyll快, 具体我也没有做对比
对于我来说, 有中文文档是一件幸福的事, 并且我的电脑里面没有Ruby, 刚好有NodeJs, 省了一些搭建步骤

## 迁移工作:

首先就是安装Hexo了, 安装过程很简单:
```
$ npm install -g hexo
```
如果碰见权限问题, 就加个sudo

通过插件实现WordPress迁移到Hexo
之后进行新建Hexo工程, 并安装插件
```
$ mkdir blog
$ cd blog
$ hexo init
$ npm install hexo-migrator-wordpress --save
```
转移有两种方法

- 进入网站面板, 导出整个网站
导出之后输入命令:
```
$ hexo migrate wordpress ~/Downloads/wordpress....xml
```
- 直接通过插件抓取网站信息
```
$ hexo migrate wordpress ljkgpxs.xyz
```
根据文档说明:

> 这个插件并不能完美地实现WordPress->Hexo的数据转换

导入之后可以尝试是否正常, 键入命令
```
$ hexo s
```
果然出错, 只能进入source里面把每个md文件读一遍,差错, 对于**非代码区**的**下划线\_**, 要加 **反斜杠\\**

经过**人工复查**OTZ, 终于可以预览效果了, 经过hexo s, 查看效果已经可以了

## 处理附件:

人工复查md文件的时候, 发现还有一些附件, 图片什么的也要迁移, 根据hexo官方文档, 建议采用标签的形式插入图片:

```
{% assert_image src %}
```

但是这种形式必须要设置
```
post_asset_folder : true
```

而且每篇文章都要对应一个资源文件夹, 这种方式太复杂, 只好采用Markdown原生的插入方法, 在source目录下新建images目录, 把图片资源复制进去, 之后修改文章中的图片导入:

```
![image_name](/images/image_name.png)
```

文件导入方式类似, 建立files目录(可以用别的名字), 文件丢进去, 修改md文件中对应的链接地址



## 部署Github.io

前面全部弄好了, 本地测试也没问题, 最后修改hexo的_config.yml, 把部署补分设置好:

```
deploy:
  type: git
  repo: git@github.com:ljkgpxs/ljkgpxs.github.io.git
```

其他的配置根据需要设置就搞定了, 最后我选择了[Replica](https://github.com/sabrinaluo/hexo-theme-replica)作为博客主题, 外观和Github简直一样, 特别感谢Hiitea(小姐姐?), 主题做的很漂亮

设定主题之后, 主题也有一些自定义设置, 建议把主题的自定义设置也写在项目的_config.yml, 不要修改主题目录的配置文件



最后通过一个命令部署:

```
$ hexo d
```

等待上传成功, 进入博客地址[ljkgpxs.github.io](https://ljkgpxs.github.com)查看



## 自定义域名并开启HTTPS:

根据官方文档, 绑定自定义域名, 想要开启HTTPS, 需满足两个条件:

- 必须要先给域名做CA认证

  因为之前做过, 用的是[Let's Encrypted](https://letsencrypt.org/), 这一步就省略了

- 设置域名解析

  给域名添加4个指向github.io的ip的A类解析, 四个地址分别为

  - 185.199.108.153

  - 185.199.109.153

  - 185.199.110.153

  - 185.199.111.153

  添加效果如下:

  {% asset_img dns.png %}



全部都弄好之后, 到github repo设置里面, **重新添加**自定义域名, 勾上Force HTTPS, 会有提示, 24小时之内处理完毕, 接下来等待就好了

最后效果就是强制跳转HTTPS

欢迎光临瓜地: [瓜地](https://ljkgpxs.xyz)

***

[^1]: https://Jekyllrb.com

Written on 2018-07-24
