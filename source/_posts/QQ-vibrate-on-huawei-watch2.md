---
title: 实现华为Watch2 QQ振动提醒
tags:
  - Android
  - WearOS
  - Huawei
  - QQ
categories:
  - Linux
  - Android
date: 2018-11-06 20:30:00
---




用过华为Watch2的应该都知道QQ消息只有提醒，没有振动。

WearOS的手机端App是通过获取消息栏然后推送至手表，对于一般消息来说都是有振动的，但是对于QQ来说，是没有的，可能是因为华为故意如此设计，因为手表端有一个QQ轻聊版，但是QQ轻聊版很不好用，更新不及时。

手机并不能一直盯着看，手表也是，但是有些时候手机关了振动，QQ来了消息，完全不知道，有时候可能很要紧的事不能及时处理，所以我就看了一些WearOS的文档，配合手机端写了一套软件，用来QQ来消息时触发振动。

**项目地址**：https://github.com/ljkgpxs/NotifyMe

简单说一下大致原理：手机端会有一个Service一直监听通知栏，当有QQ消息时，就告知手表端，手表触发振动，其中手机和手表数据同步采用了Data Item方式（Message方式其实更合适，只是当时了解不是很多）

手机端的Service继承自NotificationListenerService，NotificationListenerService启动是由系统发起的，只要在Manifest中注册，就会在适当时刻被系统启动并销毁，当然如果要使这个Service可以被正常启动，就要提前授予权限，权限部分就不再赘述，可以参考[官方文档](https://developer.android.com/guide/topics/permissions/overview)

由于手表端要时刻等待手机传来的消息，手表端就要使用Service，并继承WearableListenerService，这个服务同样是被系统启动，注册之后就会被系统接管

了解基本原理，根据Api编写就Ok了，当然还有一些功能，像是检测手表是否佩戴、是否免打扰等等，官方没有很详细的介绍文档，就需要Google了，花了几天时间写出了这个

另外提一下手表调试方法，手表可以通过有线连接电脑，但是这种调试方法不适合某些场合，采用蓝牙调试比较好，但是蓝牙调试在安装时比较慢，调试方法：

1. 先在手表开发者选项中打开蓝牙调试
2. 在手机端WearOS打开蓝牙调试，手机连接电脑（可以无线也可以有线）
3. 手机WearOS上会显示
    ```
    主机：已断开
    目标：已连接
    ```
4. 电脑上输入命令：

   ```
   adb forward tcp:4444 localabstract:/adb-hub
   adb connect 127.0.0.1:4444
   ```

全部做好之后Android Studio上就会有两个设备，一个是手机，一个是手表

***

Written on 2018-11-06