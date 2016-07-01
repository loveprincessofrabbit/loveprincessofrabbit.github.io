---
layout: post
tags: Kali Linux
title: "Kali Linux 2.0 DSL配置"
category: KaliLinux
excerpt: Kali Linux的有线网络配置
---

在安装Kali Linux 2.0以后，需要对有线网络进行配置，在查到的解决方案里，清一色都是用pppoe或者re-pppoe模拟连接的，然而并没有解决问题。


在Google了Kali论坛以后，发现了解决方案：


首先安装pppoe和配置程序


`sudo apt-get install pppoe pppoeconf`


然后调用系统自身的DSL配置程序，只输入有线连接要用的账号密码即可


`nm-connection-editor`
