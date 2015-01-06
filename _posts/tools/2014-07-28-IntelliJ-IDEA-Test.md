---
layout: post
title: Intellij IDEA使用Javadoc工具生成注释文档时提示错误和乱码问题
autor: Sheleng
category: tools
tags: [`javadoc`,`idea`]
description: 在IntelliJ IDEA 使用 Tools->Generate JavaDoc在遇到中文时提示错误和乱码问题。
---

####1、运行Tools->Generate JavaDoc...
![](/public/images/posts/tools/2014-07-28-idea-javadoc-tool/file-encodings.png) 

####2、通过Setting->FileEncoding查看源代码文件的编码格式
![](/public/images/posts/tools/2014-07-28-idea-javadoc-tool/generate-javadoc.png) 

####3、设置相应的JavaDoc命令参数 
![](/public/images/posts/tools/2014-07-28-idea-javadoc-tool/set-arguments.png) 
 
其中`-encoding`和`-charset`参数的含义如下(可以通过终端输入：`javadoc -help`进行查看)：  
`-encoding <name>`			源文件编码名称  
`-charset  <charset>` 		用于跨平台查看生成的文档的字符集 

####4、完成
