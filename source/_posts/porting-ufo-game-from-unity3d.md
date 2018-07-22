---
title: 使用Cocos2D-x重写Unity3D的UFO教程游戏
url: 330.html
id: 330
categories:
  - Linux
date: 2017-04-10 20:42:01
tags:
---

最近在学习cocos2Dx，无奈没有一点灵感，想了想以前跟着unity3d的教程写过一个UFO新手游戏，于是就想着再加点功能，用Cocos2Dx重写

重写得过程中碰到不少问题，毕竟是新手嘛！因为unity3D和cocos2Dx差别比较大，但是使用的思想大概一致，于是代码部分很多都是差不多的，但是设计界面是，感到Cocos2Dx真是力不从心，效率非常低，每个布局都需要用代码完成，和Unity3D比着，简直一个天上一个地上。

博主使用Cocos2Dx 3.14.1，搭配CLion敲代码（安利一波CLion，代码补全很舒服，节省了不少时间），由于Cocos2Dx版本迭代过快，每个版本的变动太大，以至于很多论坛和社区问题多，回答少，自己碰到问题，多半是搜不到答案的，这时候需要简单得读一下cocos2dx的源代码，和各种查API Reference。

简述一下碰到的问题

1.  重写父类函数时，子类函数要掉用父类函数进行初始化，例如重写Layer::onEnter()，在子类onEnter中要调用Layer::onEnter(); 否则会出现事件无法响应的后果，即使添加了Listener，回调函数也不会被调用
2.  新版的cocos2dx已经不推荐使用Layer::setTouchEnabled和Layer::setKeyboardEnabled等类似函数进行事件监听器的初始化，建议自行添加事件监听器，应给类似这样使用事件监听
```    
keyboardListener = EventListenerKeyboard::create();
keyboardListener->onKeyPressed = CC_CALLBACK_2(ufoScence::onKeyPressed, this);
keyboardListener->onKeyReleased = CC_CALLBACK_2(ufoScence::onKeyReleased, this);
_eventDispatcher->addEventListenerWithSceneGraphPriority(keyboardListener, this);
```    
    对于这一点，ProgramingGuide上有很多示例
3.  当前场景使用物理引擎时，创建场景时要使用这种方法创建
```    
auto scene = Scene::createWithPhysics();
```    
    否则物理引擎不起作用
4.  对刚体使用applyForce时，如果刚体静止，施力之后不会改变刚体的运动状态，这是因为F=ma，力直接影响加速度，而applyForce是瞬时力，并不能造成刚体的运动状态变化; 相对的使用applyImpluse可以是改变刚体运动状态，使刚体接下来保持惯性运动，这是由于applyImpluse改变了刚体的冲量，直接影响物体的运动速度。 这一点，cocos2dx和unity3D差别很大，让我纳闷很久，这里有一篇更全面的解释：[http://www.07net01.com/zhishi/325699.html](http://www.07net01.com/zhishi/325699.html)
5.  刚体和精灵的位置是完全同步的，换句话说精灵旋转，PhysicsBody也会跟着旋转，会造成刚体的坐标旋转，我在调试的时候在这个上面也是浪费了不少事件才发现，暂时没发现如何实现精灵变化刚体不变的方法
6.  Listener得回调函数中不要有阻塞，否则会造成游戏假死，似乎cocos2dx并没有对回调函数实现异步执行（待验证）
7.  如要要在每一帧前调用update函数，就要virtual void update(float) override;并且在init（）内调用this-\>scheduleUpdate();  
    

暂时就写这么多，后续博主会慢慢学习，发现问题再做笔记，这是写好后的代码，加入了重力感应，可以使用手机重力感应控制UFO走动： [UFO-Cocos2dx](https://github.com/ljkgpxs/UFO-Cocos2D-x)

如果真的要做游戏，强烈的不建议使用Cocos2Dx，它版本迭代过快！社区看似活跃，其实并没有实际得用途，即使在现在，社区中还有大部分人在使用老版本cocos2dx，这使得用户手中的引擎版本都不一致，造成有问题没回答的局面

在博主写这篇文章时，最新的cocos2Dx稳定版是3.14.1，但是新版3.15以及即将发布，github上已经将3.15版合并到了主分支,3.14.1版本并没有坚持很久时间，就要迎来3.15版更新，官方API Reference写着3.*，看似3.*包含3.0~3.14.1，但是实际这些版本之间得差别还是很大的

当然Cocos2Dx是开源免费的，这使得它火了一把，但是它有诸多残缺得地方，有时会严重拖慢项目进度，所有有条件还是使用一些商业引擎，像Unity3D, Unreal Engine4，这些商业引擎都很棒，而且社区很活跃，有官方的服务支持，开发效率会大大增高，cocos2dx适合想要了解游戏引擎，想要折腾的人，不适合商业使用，与其说Cocos2Dx是游戏引擎，不如说它是一个框架
