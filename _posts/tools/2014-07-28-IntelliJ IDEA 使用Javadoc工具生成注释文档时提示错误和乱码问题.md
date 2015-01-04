---
layout: post
title: Intellij IDEA使用Javadoc工具生成注释文档时提示错误和乱码问题
category: tools
tags:[javadoc,idea]
---
#####1、运行Tools->Generate JavaDoc...

#####2、通过Setting->FileEncoding查看源代码文件的编码格式

#####3、设置相应的JavaDoc命令参数

其中-encoding和-charset参数的含义如下(可以通过终端输入：javadoc -help进行查看)：  
-encoding <name>			源文件编码名称  
-charset  <charset> 		用于跨平台查看生成的文档的字符集

#####4、完成
