---
layout: blog-post
title: Ubuntu升级时出现/boot空间不足的解决方法
autor: Sheleng
category: blogs
tags: [Ubuntu]
description: Ubuntu经常会提示升级内核，几次升级过后就会提示/boot空间不足的情况。本文就针对该问题提出一个解决方法。
---

由于多次升级Linux内核，在升级的过程又没有将过时的Linux内核清除，导致在本地保存了多个版本的Linux内核以至于/boot分区的剩余空间不足。

####1、使用命令：dpkg --get-selections | grep linux-image 查看已安装的linux内核版本

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/1.png)

####2、使用uname -a 或 uname -r 查看目前系统正在使用哪个版本的Linux内核，这样就可以删除过时的Linux内核来对/boot分区进行瘦身。   （其中uname命令是用来显示系统信息，-a参数是将所有的系统信息都显示出来，而-r只是显示系统的内核版本号）

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/2.png)

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/3.png)

####3、查看到我当前系统使用的内核版本是3.13.0-33-generic，因此可以将该版本号以前的那些内核全部删除。使用命令：sudo apt-get purge linux-image-3.11.0-15-generic来进行删除。apt-get 命令的purge参数会将指定的安装包和相应的配置一并删除，删除的要比remove参数干净。

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/4.png)

####4、依次删除过时的系统内核后，再次运行dpkg --get-selections | grep linux-image 查看目前已安装的linux内核

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/5.png)

####5、最后可以运行df命令查看/boot分区的空间使用情况。

![](/public/images/posts/blogs/2014-08-12-ubuntu-update-boot/6.png)