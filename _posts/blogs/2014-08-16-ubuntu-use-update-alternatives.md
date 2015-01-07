---
layout: blog-post
title: 在Ubuntu系统中使用update-alternatives切换系统默认的Java版本
autor: Sheleng
category: blogs
tags: [java,ubuntu]
description: ubuntu系统中默认安装了OpenJDK，而在平时的开发中都是使用SUN JDK，并且现在大部分的软件对JDK的版本都有限制，可能存在默认的JDK版本已经过时的情况，这时就可以使用update-alternatives命令来切换系统默认的Java版本。
---

update-alternatives命令是专门用于维护系统命令链接符的工具，通过它可以很方便的设置系统默认使用哪个命令、哪个软件版本。

使用update-alternatives --display name 命令用于显示name链接组的信息。显示的信息包括组的模式（自动或手动），哪个链接是现在name命令所指向的等。

以下是平时常用的java命令和javac命令的链接组信息：

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/1.png)

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/2.png)

我们可以使用ls -l命令来验证下java命令所指向的路径是否与--display显示的信息是相同。

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/3.png)

由上图可知，在/usr/bin/目录下的java命令指向的路径与--display显示的信息是相同的。    

现在我们将已经安装的SUN JDK添加到java命令的链接组下：

使用的到的命令是update-alternatives --install  link name path priority，具体命令如下：

`sheleng@r61e:~$ sudo update-alternatives --install \`
`/usr/bin/java java /usr/local/java/jdk1.7.0_60/bin/java 1061`

`sheleng@r61e:~$ sudo update-alternatives --install \`
`/usr/bin/javac javac /usr/local/java/jdk1.7.0_60/bin/javac 1061`

其中link是主链接的全称；

name是前面link链接在alternatives中的目录名； 

path是要引入到name链接组下的链接； 

priority是用于指定path的优先级。

最后指定加入的SUN JDK的java命令和javac命令的路径为系统java命令和javac命令的默认路径： 

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/4.png)

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/5.png)

这样就将系统默认的java版本切换到我们自己的安装的java版本上了。最后我们验证一下

![](/public/images/posts/blogs/2014-08-16-ubuntu-use-update-alternatives/6.png)
