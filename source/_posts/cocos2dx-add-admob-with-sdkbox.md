---
title: Cocos2Dx使用sdkbox添加admob广告
url: 337.html
id: 337
categories:
  - Linux
date: 2017-05-16 19:20:47
tags:
---

做好一个游戏，总得增加点什么来为自己带来收入吧，广告是个不错的选择，这里博主使用Admob作为广告来源，为Flappy bird这款游戏增加广告 去Admob官网注册你的App unit id，用来连接游戏进行营收 首先去sdkbox网站按教程下载安装sdkbox，运行命令以安装：  
```
python -c "import urllib; s = urllib.urlopen('https://raw.githubusercontent.com/sdkbox-doc/en/master/install/install.py').read(); exec s"
```
然后进入Cocos2Dx的项目根目录，运行一下命令：    
```
$ ls ./
Classes            cocos2d              proj.ios_mac  proj.win10
cmake-build-debug  proj.android         proj.linux    proj.win32
CMakeLists.txt     proj.android-studio  proj.tizen    Resources

$ sdkbox import admob
  _______ ______  _     _ ______   _____  _     _
  |______ |     \ |____/  |_____\] |     |  \\___/ 
  ______| |_____/ |    \\_ |_____\] |_____| _/   \\_
 Copyright (c) 2016 SDKBOX Inc. v1.0.1.22
 test speed of hosts...
 \- test host main: 42.47KB/s.
 \- test host china: 428.35KB/s.
 choose the fastest server 'china', speed is 428.35KB/s.
 downloading package sdkbox-googleplayservices_v2.3.13.5.tar.gz
\[###################################\] 100%
 Installation Successful :)
 downloading package sdkbox-admob_v2.3.13.5.tar.gz
\[###################################\] 100%
 Please reference the online documentation to finish the integration:
http://sdkbox-doc.github.io/en/plugins/admob/v3-cpp/
 Installation Successful :)
```
导入过程中，会根据当前网络链接速度，选择合适得服务器下载资源包   导入完成后会自动用浏览器打开API介绍页面 修改 Resources/sdkbox_config.json其中的admob key以及广告位置、类型，这是博主修改之后的：  
```
{
  "android": {
    "AdMob": {
      "ads": {
        "testad": {
          "width": 0,
          "type": "banner", // 广告类型
          "id": "ca-app-pub-3873534785931669/2912842034",  // 填入你的app unit id
          "alignment": "bottom",
          "height": 0 // 宽度和高度都设为0，即为自动计算大小
        }
      }
    },
    "ios": { // ios断配置，类似android端
      "AdMob": {
        "ads": {
          "testad": {
            "width": 300,
            "type": "banner",
            "id": "ca-app-pub-3873534785931669/2912842034",
            "alignment": "bottom",
            "height": 50
          }
        }
      }
    }
  }
```
  在编译之前，要修复一下sdkbox包的问题，可能因为官方得admob资源包是在windows下测试的，对文件名大小写不区分，所以会造成Linux下编译的时候出错 根据你的编译环境修改，如果使用Android studio编译打包，那么  
```
$ cd proj.android-studio/app/jni
```
  如果使用命令行编译，那么  
```
$ cd proj.android/jni
```
然后把pluginadmob这个文件夹重命名为PluginAdMob    
```
$ mv pluginadmob PluginAdMob
```
  最后修改Android.mk，把里面的pluginadmob全改成PluginAdMob 这样做过之后就不会有编译错误了，一路顺畅！   之后就可以修改自己的代码以显示广告，根据官方文档，显示广告之前要先进行缓存，建议在刚进入游戏的时候就进行缓存  
```
#ifdef SDKBOX_ENABLED
#include "PluginAdMob/PluginAdMob.h"
#endif

......

#ifdef SDKBOX_ENABLED
	sdkbox::PluginAdMob::init();
	sdkbox::PluginAdMob::cache("testad");
#endif

......
```
    然后选择在恰当的时候显示广告：

  

  
```
if(sdkbox::PluginAdMob::isAvailable("testad"))
			sdkbox::PluginAdMob::show("testad");
```
  

这是博主的测试截图：

  ![](https://ljkgpxs.xyz/wp-content/uploads/2017/05/flappybirdAdTest.png)
