---
title: Cocos2d-x碰到vtable for ***等错误的处理方法
url: 288.html
id: 288
categories:
  - Linux
date: 2017-02-03 01:53:37
tags:
---

跟着这个教程走：[Build a collaborative drawing app](https://www.makeschool.com/online-courses/tutorials/build-a-collaborative-drawing-app/make-new-project)

*   ### 定义类之后，运行cocos compile -p ...进行编译可能会出现如下错误
    

  
```
CMakeFiles/Doodler.dir/Classes/HelloWorldScene.cpp.o：在函数‘Lobby::Lobby()’中：
/home/ljkgpxs/src/Doodler/Classes/Lobby.h:6：对‘vtable for Lobby’未定义的引用
collect2: error: ld returned 1 exit status
CMakeFiles/Doodler.dir/build.make:194: recipe for target 'bin/Doodler' failed
make\[2\]: *** \[bin/Doodler\] Error 1
```
导致这个错误的原因是没有对Lobby类的成员函数进行定义，根本原因是对应的cpp文件没有被编译

  

出现vtable for **错误提示，如果编译的目标平台是android，则修改proj.android/jni/Android.mk，在LOCAL\_SRC\_FILES中添加新cpp文件

如果目标平台是Linux，则修改CMakeList.txt, 在GAME\_SRC和GAME\_HEADERS中分别添加新cpp和h文件

  

  

  

*   ### 修改Android.mk中LOCAL\_MODULE和LOCAL\_MODULE_FILENAME之后，需要修改AndroidManifest.xml中：
    

  
```
        <meta-data android:name="android.app.lib_name"
                   android:value="MyGame" />
```
  

改为

  
```
        <meta-data android:name="android.app.lib_name"
                   android:value="YourLibName" />
```
  

  

  

*   ### 若要使用code blocks作为IDE，在linux下运行以下命令
    
```
cd ProjectDir
mkdir codeblocks
cd codeblocks
cmake .. -G "CodeBlocks - Unix Makefiles"
```
之后使用CodeBlocks打开codeblocks目录下的cbp文件，然后右击左侧Project->Properties->Build target->type->Console application，然后设置下面的Output filename为编译得到的可执行万恶见路径，一般为bin/YourProjectName
