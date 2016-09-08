---
layout: post
tags: Server ShadowSocks
title: "ShadowSocks服务器搭建"
category: Server
excerpt: 来，跟我学习如何科学上网
---
> 如果觉得这篇文章写的不错，来给我[打赏鼓励](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/alipay.jpg?raw=true)一下吧~我会写出更好的文章

### 前言

两个月没买过VPN了，无法科学上网的我十分不甘。今天一大早被陈潇安利了一个一年$2的纯IPV6服务器，只有IPV6地址没有IPV4地址。所以访问不了正常的IPV4资源，更加不甘心的我直接买了个香港的VPS，然后找了个同学分担了一下费用，在此记录一下Shadowsocks搭建过程。

### 系统选用

系统选用没啥说的，直接丢CentOS7上去。~~表白CentOS！！！~~

### 环境搭建

Shadowsocks服务需要用pip来安装，而且需要Python2.7的环境。（之前就是这里卡了挺久，CentOS6用的是Python2.6，所以还是用CentOS7比较方便）

{% highlight yml %}
yum install python-setuptools && easy_install pip
pip install shadowsocks
{% endhighlight %}

安装好以后需要编辑一个JSON的配置文件，放在了/etc/shadowsocks下

{% highlight yml %}
vim /etc/shadowsocks/config.json

{
"server":"xxx.xxx.xxx.xxx",
"port_password":{
  	"8382":"password1",
  	"8383":"password2"  
},
"timeout":300,
"method":"aes-256-cfb",
"fast_open":false,
"workers":1
}
{% endhighlight %}

代码中各字段的含义：

* server：服务器 IP地址 (IPv4/IPv6)

* server_port：服务器监听的端口，一般设为80，443等，注意不要设为使用中的端口

* password：设置密码，自定义

* timeout：超时时间（秒）

* method：加密方法，可选择 “aes-256-cfb”, “rc4-md5”等等。推荐使用 “rc4-md5”

* fast_open：true 或 false。如果你的服务器 Linux 内核在3.7+，可以开启 fast_open 以降低延迟。

* workers：workers数量，默认为 1

### 启动运行

启动时使用命令

{% highlight yml %}
ssserver -c /etc/shadowsocks/config.json -d start
{% endhighlight %}

### 防火墙配置

在最初配置完之后，客户端输入完参数，却发现一只在报` Error 500 `，后来发现是被防火墙阻隔了。由于萌新还不会配置防火墙，索性全部关闭 = =

{% highlight yml %}
systemctl stop firewalld.service #停止
systemctl disable firewalld.service #禁用
之前的版本：
service iptables stop #停止
chkconfig iptables off #禁用
{% endhightlight %}

### 查看服务端进程以及关闭服务端

~~妈妈再也不用担心我不会关单个进程了~~
{% highlight yml %}
ps aux|grep ss
kill xxx
{% endhighlight %}

### 后记

其实那个IPV6服务器，用命令`ping6 ipv6.google.com`还是有结果的，所以考虑能不能在IPV6隧道去免掉Google和Youtube的IPV6站的流量。以后再说啦~
