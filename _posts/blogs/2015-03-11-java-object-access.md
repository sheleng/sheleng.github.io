---
layout: blog-post
title: Java对象访问理解
autor: Sheleng
category: blogs
tags: [Java,JVM]
description: 
---

在Java语言中，对象的访问需涉及**Java栈、Java堆、方法区**这三个运行时数据区域。如下代码：

	Object obj = new Object();

假设上面这句代码是出现在方法体中，那么代码`Object obj`这部分语义将会反应到Java栈的本地变量表中，作为一个引用类型数据出现。而`new Object()`这部分的语义将会反应到Java堆上，在Java堆上形成一块存储了Object类型实例的结构化内存。另外，在Java堆中还必须包含能查找到此对象类型数据（如对象类型、父类、实现的接口、方法等）的地址信息，这些类型数据则存储在方法区中。

由于`引用类型（reference）`在Java虚拟机规范里面只规定了一个指向对象的引用，并没有定义这个引用应该通过哪种方式去定位，以及访问到Java堆中的对象的具体位置，因此不同虚拟机实现的对象访问方式会有所不同，**主流的访问方式有两种： 使用句柄和直接指针**。

##使用句柄访问方式：

如果使用句柄访问方式，Java堆中将会划分出一块内存来作为句柄池，引用（reference）中存储的是对象的句柄地址，而句柄中包含了对象实例数据和类型数据各自的具体地址信息，具体结构如下图所示:

![](/public/images/posts/blogs/2015-03-11-java-object-access/handle-access-object.png)

##使用直接指针访问：

如果使用直接指针访问方式，Java堆对象的布局中就必须考虑如何放置访问类型数据的相关信息，引用（reference）中直接存储的就是对象实例数据的地址，具体结构如下图所示：

![](/public/images/posts/blogs/2015-03-11-java-object-access/direct-pointer-access-object.png)

##总结


句柄访问和直接指针访问这两种方式各有优势：

使用句柄访问方式的最大好处就是引用（reference）中存储的是稳定的句柄地址，在对象被移动（垃圾收集时移动对象是非常普遍的行为）时只需改变句柄中的实例数据指针，而引用（reference）本身不需要被修改。

使用直接指针访问方式的最大好处就是速度更快，它节省了一次指针定位的时间开销，由于对象的访问在Java中非常频繁，因此这类开销积少成多后也是一项非常可观的执行成本；**虚拟机Sun HotSpot就是采用该方式进行对象访问**。

参考资料：深入理解Java虚拟机：JVM高级特性与最佳实践
