---
title: "Android逆向基础（一）"
layout: post
tags: 渗透测试 Android逆向
category: Android逆向
excerpt: Android调试桥ADB介绍、Root权限、APK安装情况与Android系统目录
---
## ADB介绍

### Android Debug Bridge
连接Android手机与PC端，让用户在电脑上进行全面的操作

### ADB使用
需要安装`Android SDK`环境，并将`platform-tools`目录添加到环境变量

## ADB常用命令
* `adb devices`用于查看与PC连接的安卓设备
* `adb kill-server`关闭ADB服务
* `adb start-server`开启ADB服务
* `adb root`以root用户连接至安卓设备
* `adb shell`开启Shell
* `adb remount`挂在盘符
* `adb install [apk文件全路径]`安装apk，路径不得有中文和空格
* `adb uninstall [apk包名]`卸载apk，包名可通过反编译获取
* `adb forward [local] [remote]`讲本地端口重定向到远程端口
* `adb logcat`打印运行日志 `-c`清除日志



## Root权限

### Root的原理

在系统中装一个`Superuser.apk`，将`su`文件放入`/System/bin`，把`Superuser.apk`放到`System/app`下面，设置`/System/bin/su`可以让任意用户运行，有`set  uid`和`set gid`的权限，既在Android设备运行命令`adb shell chmod 4755 /system/bin/su`。



### Root的方法

1. `adb push su /system/bin` 将`su`文件传到`/system/bin`文件夹
2. `adb push su /system/xbin`将`su`文件传到`/system/xbin`文件夹
3. `adb push SuperUser.apk /system/app`将`SuperUser.apk`文件传到`/system/bin`文件夹
4. `adb shell chmod 6755 /system/bin/su`提升权限
5. `adb shell chmod 0755 /system/xbin/su`提升权限



## APK安装目录结构

* `/data/app/`下安装自身文件
* `/data/data/packagename`建立以包命名的文件夹存放缓存文件
* `/sdcard/android/`建立以包命名的文件夹存放缓存文件



## 系统目录结构

* `\system\fonts` 字体文件夹

* `system\framework`核心文件、系统平台框架

* `\system\lib`系统底层库

* `\system\media`铃声音乐文件夹

* `\system\sounds`默认音乐测试文件

* `\system\usr` 用户文件夹，包含共享、键盘布局、时间区域文件

* `\system\app`存放常规下载应用，APK文件，系统默认组件

* `\system\bin`系统本地文件，binary二进制程序，linux系统自带组件

* `\system\etc`系统配置文件  

  ​    

## Android逆向流程图

```
st=>start: 安卓逆向分析

op1=>operation: 反编译

op2=>operation: 分析目录结构

c1=>condition: 反编译

c2=>condition: 分析目录结构

io1=>inputoutput: 寻找其他途径

c3=>condition: 有lib

c4=>condition: 无lib

op3=>operation: 分析SO

op4=>operation: 分析Smali代码

io2=>inputoutput: 静态分析

io3=>inputoutput: 动态调试

io4=>inputoutput: 有混淆

io5=>inputoutput: 无混淆



st->c1

c1(yes)->c2

c1(no)->io1

c2(yes)->c3->op3

c2(no)->c4->op4

op3->io2

op3->io3

op4->io4

op4->io5
```






