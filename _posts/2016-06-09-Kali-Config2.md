---
layout: post
tags: Kali Linux
title: "Kali Linux 2.0的一些基本配置"
category: Kali Linux
---

装过很多次Kali2.0了，每次装完都要重新去搜集一些命令去调系统配置，这次装完后记录了一下，希望能给大家一些便利

## 换源

{% highlight shell %}
leafpad /etc/apt/sources.list
#Source from USTC
deb http://mirrors.ustc.edu.cn/kali sana main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali sana main non-free contrib
deb http://mirrors.ustc.edu.cn/kali-security sana/updates main contrib non-free
apt-get update
{% endhighlight %}

## 网络设置

`apt-get install pppoe pppoeconf`   
`nm-connection-editor` (DSL网络在此处添加)

## 输入法安装

`apt-get install fcitx fcitx-table-wbpy`


## 字体安装

`apt-get install ttf-wqy-microhei` 文泉驿字体啦~

## 终端字体调整

`Droid Sans Mono Regular` 改成这个会好一点，因为Kali 2.0默认的终端字体有字符重叠的问题

## 虚拟机配置

此处针对虚拟机配置过程中内核模块未载入问题（对于64位电脑，Kali 2.0）

{% highlight shell %}
wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.4-wily/linux-headers-4.3.4-040304_4.3.4-040304.201601230132_all.deb  
wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.4-wily/linux-headers-4.3.4-040304-generic_4.3.4-040304.201601230132_amd64.deb  
wget kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.4-wily/linux-image-4.3.4-040304-generic_4.3.4-040304.201601230132_amd64.deb  
sudo dpkg -i linux-headers-4.3.4*.deb linux-image-4.3.4*.deb  
sudo reboot
{% endhighlight %}

## IceWeasel汉化

{% highlight shell %}
sudo apt-get remove iceweasel
sudo apt-get install iceweasel
sudo apt-get install iceweasel-l10n-zh-cn
{% endhighlight %}