---
layout: post
title: Maven使用记录
autor: Sheleng
category: tools
tags: [java,maven]
description: Maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建，报告和文档的软件项目管理工具。
---

####1、安装Maven
Maven是一个Java工具，所以在安装Maven前，需要确保你的机器上已经安装了Java环境。


在http://maven.apache.org/download.cgi 下载最新版的Maven安装包。在这里下载的是apache-maven-3.2.3-bin.tar.gz。


将下载的安装包解压到指定目录下。这里选择解压到/usr/local/路径下，其中tar命令的-C参数是用于指定文件的解压路径。
`sudo tar -xvzf apache-maven-3.2.3-bin.tar.gz -C /usr/local/`


编辑~/.bashrc文件，在~/.bashrc文件中添加M2_HOME、M2环境变量，并将M2添加到PATH路径，如：
`vim ~/.bashrc`
![](/public/images/posts/tools/2014-08-20-maven-use-records/vim-bashrc.png)

其中MAVEN_OPTS环境变量设置了JVM属性（可选），这个环境变量可以用于设置Maven的扩展选项。

使用source命令使上面的设置生效：
`source ~/.bashrc`

最后运行mvn --version 验证安装

![](/public/images/posts/tools/2014-08-20-maven-use-records/mvn-version.png)
