---
layout: post
tags: Server MineCraft
title: "MineCraft服务器搭建"
category: Server
excerpt: 唉？有点想玩MineCraft
---

> 如果觉得这篇文章写的不错，来给我[打赏鼓励](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/alipay.jpg?raw=true)一下吧~我会写出更好的文章

话说最近被安利了某讯的云服务器，买回来顺便想搞一个MC的私服和朋友们一起玩。

### 服务器配置
服务器安装了CentOS7的系统，不得不说用了一下还是非常好用的，简直瞬间路转粉（以前只是因为没什么机会逼自己尝试才一直没尝试，用了很久的Debian系）

大概扫了一眼我的服务器配置，大概只能容纳几个人的样子，不管不管...

### 搭建环节

#### 依赖环境安装

* Java: `yum install java`
* OpenJDK: `yum install java-1.7.0-openjdk`


#### 文件安装

* 建立文件夹：在`/home`文件夹下建立了一个`minecraft`文件夹用于存放相应的服务器文件并设置权限为777

* 将[文件](http://oc42vgpoj.bkt.clouddn.com/spigot-1.8.7-R0.1-SNAPSHOT.jar)传到服务器：为了方便操作，我用SmartFTPClient建立了一个`SFTP Over SSH`方式的连接到服务器，把服务器所需的文件上传到`/home/minecraft`文件夹

* 编辑脚本用于运行服务器

{% highlight shell %}
cat > minecraft.sh << EOF
#!/bin/sh
BINDIR="\$(dirname "\$(readlink -fn "\$0")")"
cd "\$BINDIR"
java -Xincgc -Xmx1G -jar craftbukkit-0.0.1-SNAPSHOT.jar
EOF

{% endhighlight %}

* 为文件增加可执行权限`chmod +x minecraft.sh`
* 运行 `minecraft.sh`： 命令为 `./minecraft.sh`第一次运行后会下载到一些文件，而且第一次肯定会出错，这是因为生成的eula.txt文件中默认eula的值为false，需要手动改成true，表示我们接受这个协议
  ![eula.txt](http://oc42vgpoj.bkt.clouddn.com/eula.png)

* 再次运行`minecraft.sh`文件，这次运行完之后就会把文件全部下载
  这时候会进入服务器运行状态
  ![server](http://oc42vgpoj.bkt.clouddn.com/server.png)

#### 配置文件修改
此时在我尝试连接的时候，发现出现了`lost connection: Disconnected`的错误，经过查找发现还需要将`server.properties`文件内的`online-mode=true`选项改成`false`

这样我们使用一个破解版的[客户端](http://oc42vgpoj.bkt.clouddn.com/minecraft1.8.zip)进行登录，在服务器那一栏点击添加服务器，输入IP即可连接。

与此同时，该文件还是主要的配置文件，以后再深入探索~

### 其他想说的
受文俊学长启发直接把Git Bash当作了一个终端，发现意外地好用~

常用的一切功能比如FTP、SSH、Ping什么的一应俱全，在SSH登录到云服务器后更是瞬间拥有了一个完整的Linux环境，用户体验满分~