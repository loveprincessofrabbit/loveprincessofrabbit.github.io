---
layout: post
tags: FTP
title: "FTP服务器搭建"
category: Server
excerpt: 以vsftpd搭建安全的FTP服务器
---
> 如果觉得这篇文章写的不错，来给我[打赏鼓励](http://d3.freep.cn/3tb_160710193136wkl4568789.jpg)一下吧~我会写出更好的文章


## FTP服务器手册 (Alpha2.0)

### Vsftpd服务器安装
在此我们使用了一款注重安全、功能简洁同时简单易用的FTP软件vsftpd(Very Secure FTP Daemon)，该软件限制了自身PID权限，同时带有chroot功能。

* 关于PID：

  在系统上执行程序时会引发一个PID(Process ID)，PID拥有的权限越高，它能在系统上进行的任务也越多。Vsftpd服务的启动身份为一般用户，即使被入侵，对系统的危害也相对降低了。

* 关于chroot：

  此处的root并非根用户，而是登录到FTP服务器后的根目录。chroot功能可以将某个特定目录规定为FTP服务器的根目录，这样与FTP服务器无关的其他目录就不会被访问到。即使FTP服务器被攻破，入侵者也只能被限制在特定目录，而无权访问其他目录。

1. 安装：安装十分简单：

   对于Debian系：`sudo apt-get install vsftpd`

2. 启动：`sudo service vsftpd start`


如此便可以通过`ftp://localhost`访问，此时应该能在输入系统的账号密码后，进入FTP功能。由于没有Chroot，我们默认会进入用户主目录，且能够在各级目录任意切换。

![picture1](http://d3.freep.cn/3tb_160730183804noch568789.png)

### Vsftpd的启动模式

Vsftpd有两种启动模式：`stand alone`和`super daemon`

* Stand alone：建立独立守护进程，常驻内存，优点是反应迅速。
* Super daemon：在有需要时由xinetd启动vsftpd服务，优点是内存消耗少。

Vsftpd默认使用Stand alone方式启动

### Vsftpd服务器配置

安装十分简便，但接下来的安全性配置才是重点，主要配置文件有：

* `/etc/vsftpd.conf `主要配置文件，与bash变量设置采用相同方式，即"参数=设置值"，等号两边不能有空白
* `/etc/pam.d/vsftpd` 使用PAM模块的相关配置文件，主要用于身份认证，可以在此限制用户访问。
* `/etc/ftpusers` 与上一个文件有关，每行一个账号，将要限制访问的用户名写入
* `/etc/chroot_list` 此文件默认不存在，需要自己建立。此文件功能是对某些账号的用户启用chroot功能。此文件生效与`vsftpd.conf`内的`chroot_list_enable`和`chroot_list_file`两个参数有关。
* `/srv/ftp` 默认FTP匿名用户登录的根目录



#### /etc/vsftpd.conf 文件具体说明


`listen=<YES/NO>` :设置为YES时vsftpd以独立运行方式启动，设置为NO时以xinetd方式启动（xinetd是管理守护进程的，将服务集中管理，可以减少大量服务的资源消耗）
`listen_port=<port>` :设置控制连接的监听端口号，默认为21
`listen_address=<ip address>` :将在绑定到指定IP地址运行，适合多网卡
`connect_from_port_20=<YES/NO> `:若为YES，则强迫FTP－DATA的数据传送使用port 20，默认YES
`pasv_enable=<YES/NO> `:是否使用被动模式的数据连接，如果客户机在防火墙后，请开启为YES
`pasv_min_port=<n>`
`pasv_max_port=<m> `:设置被动模式后的数据连接端口范围在n和m之间,建议为50000－60000端口
`message_file=<filename>` :设置使用者进入某个目录时显示的文件内容，默认为 .message
`dirmessage_enable=<YES/NO>` :设置使用者进入某个目录时是否显示由message_file指定的文件内容
`ftpd_banner=<message> `:设置用户连接服务器后的显示信息，就是欢迎信息
`banner_file=<filename>` :设置用户连接服务器后的显示信息存放在指定的filename文件中
`connect_timeout=<n> `:如果客户机连接服务器超过N秒，则强制断线，默认60
`accept_timeout=<n>` :当使用者以被动模式进行数据传输时，服务器发出passive port指令等待客户机超过N秒，则强制断线，默认60
`accept_connection_timeout=<n>` :设置空闲的数据连接在N秒后中断，默认120
`data_connection_timeout=<n> `: 设置空闲的用户会话在N秒后中断，默认300
`max_clients=<n>` : 在独立启动时限制服务器的连接数，0表示无限制
`max_per_ip=<n>` :在独立启动时限制客户机每IP的连接数，0表示无限制（不知道是否跟多线程下载有没干系）
`local_enable=<YES/NO>` :设置是否支持本地用户帐号访问
`guest_enable=<YES/NO> `:设置是否支持虚拟用户帐号访问
`write_enable=<YES/NO> `:是否开放本地用户的写权限
`local_umask=<nnn>` :设置本地用户上传的文件的生成掩码，默认为077
`local_max_rate<n>` :设置本地用户最大的传输速率，单位为bytes/sec，值为0表示不限制
`local_root=<file> `:设置本地用户登陆后的目录，默认为本地用户的主目录
`chroot_local_user=<YES/NO> `:当为YES时，所有本地用户可以执行chroot
`chroot_list_enable=<YES/NO>` 
`chroot_list_file=<filename> `:当chroot_local_user=NO 且 chroot_list_enable=YES时，只有filename文件指定的用户可以执行chroot
`anonymous_enable=<YES/NO>` :设置是否支持匿名用户访问
`anon_max_rate=<n>` :设置匿名用户的最大传输速率，单位为B/s，值为0表示不限制
`anon_world_readable_only=<YES/NO>` 是否开放匿名用户的浏览权限
`anon_upload_enable=<YES/NO> `设置是否允许匿名用户上传
`anon_mkdir_write_enable=<YES/NO> `:设置是否允许匿名用户创建目录
`anon_other_write_enable=<YES/NO>` :设置是否允许匿名用户其他的写权限（注意，这个在安全上比较重要，一般不建议开，不过关闭会不支持续传）
`anon_umask=<nnn>` :设置匿名用户上传的文件的生成掩码，默认为077

#### **实际所需配置**

1. `anonymous_enable=YES` 允许用户匿名登陆
2. `local_enable=YES` 允许本地用户登录（管理账户）
3. `write_enable=YES` 取消注释符号，允许本地用户写入
4. `chroot_local_user=YES` chroot本地用户
5. 在配置文件末尾写入如下内容：
   `local_root=/srv/ftp`
   `anon_root=/srv/ftp`
   这两条命令将本地用户和匿名用户的根目录固定在`/srv/ftp`
   ![picture2](http://d3.freep.cn/3tb_160730183804v89x568789.png)
6. 确认ftp文件夹的权限情况：
   `cd /srv/`
   `ls -l`
   ![picture3](http://d3.freep.cn/3tb_160730183804h02h568789.png)
   应保证755权限，保证非所有者用户不能拥有写入权利（否则chroot时会出现错误，vsftpd以及非常体贴的为我们想到了这一点），如有问题，用chmod命令进行修改
7. 建立管理账号ftpadmin（可以管理FTP服务器，但不可登录系统）
   `sudo useradd ftpadmin -m -s /sbin/nologin` 创建管理账号，创建用户主目录（否则chroot会报错），设置为不可登录系统
   `sudo passwd ftpadmin` 设置登陆密码
   `sudo vim /etc/shells` 加上`/sbin/nologin`允许本地账户登录ftp

8. 在ftp文件夹内建立上传文件夹：
      由于Vsftpd的安全机制，我们不能让ftp根目录有写入权利，为了保证部分用户可以上传，我们还需要在里面建立一个上传文件夹，给予管理用户写入的权利。最后的效果是，update文件夹管理用户可读可写，匿名用户不可读不可写。
      `sudo mkdir /srv/ftp/update` 创建update文件夹供上传文件
      `sudo chown ftpadmin /srv/ftp/update` 将update文件夹所有者变更为用户ftpadmin
      `sudo chmod 700 /srv/ftp/update` 将update文件夹只对用户ftpadmin开放，其他用户不可读不可写


### 彩蛋~

其实python2.7里面自带了一个模块可以用于临时搭建一个http服务器，也可以实现文件传送的功能。只需要在命令行里定位到要共享的目录，输入命令`python -m SimpleHttpServer`即可，默认打开`8000`端口，也可以在命令后面指定任意端口，只要在同一网段均可访问。访问地址为：`http://ip:8000`

by 村雨 2016.7