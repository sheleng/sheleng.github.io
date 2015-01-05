---
layout: post
title: Ubuntu下配置安装JDK1.7+Eclipse Classic 4.2.2
description: 
category: blogs
tags: [java,eclipse]
year-month: 2013-03
day: 06
---

####1、JDK和Eclipse的下载
首先是JDK和Eclipse的下载，我喜欢自己选择下载组件，所以没有用sudu apt-get install 的方法安装。

先去http://www.eclipse.org/downloads/下载相应的Eclipse版本（本来是下载Eclipse Classic 4.2.2）。

下载JDK，地址是[http://www.oracle.com/technetwork/java/javase/downloads/](http://www.oracle.com/technetwork/java/javase/downloads/)，选择适合自己的版本。我选的是Linux32位的tar包`（jdk-7u17-linux-i586.tar.gz）`，下载后可以直接使用归档管理器解压（我的是Ubuntu12.10）。

####2、安装JDK和Eclipse
下载了Eclipse和JDK1.7的压缩包后，在/home/Username/下新建了一个名为Java_SDK的文件夹（Username为Ubuntu用户名，下同）。

将刚才下载的JDK和Eclipse（Ubuntu12.10是下载到/home/Username/下载）解压到Java_SDK文件夹。


####3、JDK的配置

#####方法一：
打开/home/Username/.bashrc 文件，通过修改这个文件来更新环境变量。

具体方法是：打开终端（Ctrl+Alt+t），输入sudo gedit /home/Username/.bashrc文件。
        
打开.bashrc文件后，在文件尾部加入如下几行：
`export JAVA_HOME=/home/Username/Java_SDK/jdk1.7.0_17` (这里指定你的JDK安装目录)
`PATH=.:$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH`
保存修改并退出。　
重新打开一个终端，键入`java –version` 如果显示Java的版本信息则说明配置成功。

#####方法二：
1、修改/etc/enviroment文件
打开/etc/enviroment文件（`sudo vim /etc/enviroment`）
会看到其中的内容是
`PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games"`，在这一行的下面输入

`export JAVA_HOME=/home/Username/Java_SDK/jdk1.7.0_17` (这里指定你的JDK安装目录)

`export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib`
保存修改并退出。
　

2、 修改/etc/profile 文件
打开/etc/profile文件（sudo gedit /etc/profile）在umask 022之前添加以下语句

`export JAVA_HOME=/home/Username/Java_SDK/jdk1.7.0_17`(这里指定你的JDK安装目录)

`export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib`

`export PATH=$JAVA_HOME/bin:$PATH:$HOME/bin`

注销系统，重新开启后打开终端，输入java -version，不出意外的话，就可以看到java的版本信息了。

####4、启动Eclipse

进入刚才解压的Eclipse文件夹（cd  /home/Username/Java_SDK/eclipse），然后直接打开就可以运行（./eclipse）。
