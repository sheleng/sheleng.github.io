---
layout: blog-post
title: 安装deb软件包时出现Unknown media type in type **/** 的解决办法
autor: Sheleng
category: blogs
tags: [Ubuntu]
description: 自从更换过桌面环境后，安装deb软件包时经常会报Unknown media type in type **/** ，google了一下发现也有不少人遇到了想到的问题，在此记录下这个问题的解决办法。
---

在安装astah uml时出现了如下问题：

![](/public/img/posts/blogs/2014-09-13-install-deb-unknown-media-type/1.png)

####1、找到kde.xml文件，我这里的路径是：

`/usr/share/mime/packages/kde.xml`

####2、先对kde.xml文件进行备份：

`sudo cp /usr/share/mime/packages/kde.xml /usr/share/mime/packages/kde.xml.bak`

####3、修改kde.xml文件，找到如下内容将其删除或注释掉：

	  <!-- all/ fake mime types -->
	  <mime-type type="all/all">
	    <comment>all files and folders</comment>
	  </mime-type>
	  <mime-type type="all/allfiles">
	    <comment>all files</comment>
	  </mime-type>
	 
	  <!-- uri/ fake mime types -->
	  <mime-type type="uri/mms">
	    <comment>mms: URIs</comment>
	  </mime-type>
	  <mime-type type="uri/mmst">
	    <comment>mmst: URIs</comment>
	  </mime-type>
	  <mime-type type="uri/mmsu">
	    <comment>mmsu: URIs</comment>
	  </mime-type>
	  <mime-type type="uri/pnm">
	    <comment>pnm: URIs</comment>
	  </mime-type>
	  <mime-type type="uri/rtspt">
	    <comment>rtspt: URIs</comment>
	  </mime-type>
	  <mime-type type="uri/rtspu">
	    <comment>rtspu: URIs</comment>
	  </mime-type>

####4、将修改后的文件保存并退出。

####5、重新安装需要安装的deb软件包。