---
layout: blog-post
title: Java运行时数据区域详解及各区域内存溢出异常了解
autor: Sheleng
category: blogs
tags: [Java,JVM]
description: 对于Java程序员来说，在虚拟机的内存管理机制下不容易出现内存泄露和内存溢出问题。也正是因为程序员把内存的控制权交给了Java虚拟机，一旦出现内存泄露和溢出问题，如果不了解虚拟机是如何使用内存的，那么查错将是非常艰巨的。
---

对于Java程序员来说，在虚拟机的内存管理机制下不容易出现内存泄露和内存溢出问题。也正是因为程序员把内存的控制权交给了Java虚拟机，一旦出现内存泄露和溢出问题，如果不了解虚拟机是如何使用内存的，那么查错将是非常艰巨的。

Java虚拟机在执行Java程序的过程中会把内存划分成若干个不同的区域。Java虚拟机所管理的内存将包含以下几个运行时数据区：

![](/public/img/posts/blogs/2015-03-12-java-runtime-data-range/data-range.png)

##程序计数器

程序计数器是一块较小的内存空间，它的作用可以看做是当前线程所执行的字节码的行号指示器。在虚拟机中字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。

由于Java虚拟机的多线程是通过时间片轮转的方法来实现的。在任何一个确定的时刻，一个处理器只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器。各条线程之间的计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

##Java虚拟机栈

虚拟机栈描述的是Java方法执行的内存模型：每个方法被执行的时候都会同时创建一个栈帧，用于存储局部变量表、操作栈、动态链接、方法出口等信息。经常有人把Java内存区分为堆内存（Heap）和栈内存（Stack），其中栈内存所指的就是虚拟机栈，或者说是虚拟机栈中的局部变量表部分。虚拟机栈也是线程私有的。

Java虚拟机规范中，对该区域规定了两种异常：

1. 如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverflowError异常；
2. 如果虚拟机栈可以动态扩张，当扩展时无法申请到足够的内存时会抛出OutOfMemoryError异常。

 这里把异常分成两种情况看似更加严谨，但却存在着一些互相重叠的地方：当栈空间无法继续分配时，到底是内存太小，还是已使用的栈空间太大，其本质上只是对同一件事情的两种描述而已。

##本地方法栈

本地方法栈与虚拟机栈的作用非常相似，其区别在于虚拟机栈为虚拟机执行Java方法服务，而本地方法栈是为虚拟机使用Native方法服务。与虚拟机栈一样，本地方法栈区域也会抛出StackOverflowError和OutOfMemoryError异常。

由于在HotSpot虚拟机中并不区分虚拟机栈和本地方法栈，因此对于HotSpot来说，-Xoss参数（设置本地方法栈大小）虽然存在，但实际上是无效的，栈容量只由-Xss参数设定；可以根据如下命令查看虚拟机类型：

	java -version

运行结果：

	java version "1.7.0_67"
    Java(TM) SE Runtime Environment (build 1.7.0_67-b01)
    Java HotSpot(TM) 64-Bit Server VM (build 24.65-b04, mixed mode)

在单线程中尝试了下面方法让虚拟机栈溢出，尝试的结果是获得StackOverflowError异常：

使用-Xss参数增加栈内存容量。结果：抛出StackOverflowError异常，异常出现时输出的栈深度相应增加。

测试代码如下：

	/**
	 * VM Args: -Xss128k
     */
     public class JavaVMStackSOF{
         private int stackLength = 1;
         public void stackLeak(){
             stackLength++;
             stackLeak();
         }

         public static void main(String[] args) throws Throwable{
             JavaVMStackSOF soe = new JavaVMStackSOF();
             try{
                 soe.stackLeak();
             }catch(Throwable e){
                 System.out.println("Stack length: " + soe.stackLength);
                 throw e;
             }
         }
     }

运行指令，设置Java虚拟机栈大小为104K：

     java -Xss104k JavaVMStackSOF

运行结果：

     Stack length: 987
     Exception in thread "main" java.lang.StackOverflowError
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:8)
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:9)
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:9)
               .......后续异常栈信息省略

运行指令，设置Java虚拟机栈大小为128K：

     java -Xss128k JavaVMStackSOF

运行结果：

     Stack length: 993
     Exception in thread "main" java.lang.StackOverflowError
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:8)
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:9)
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:9)
             at JavaVMStackSOF.stackLeak(JavaVMStackSOF.java:9)
             .......后续异常栈信息省略

结果表明： 在单个线程下，无论是由于栈帧太大，还是虚拟机栈容量太小，当内存无法分配的时候，虚拟机抛出的都是StackOverflowError异常。

在多线程环境下，通过不断地建立线程的方式可以产生内存溢出异常，这样产生的内存溢出异常与栈空间是否足够大并不存在任何联系，或者说，在这种情况下，给每个线程的栈分配的内存越大，反而越容易产生内存溢出异常。

操作系统分配给每个进程的内存是有限制的，譬如32位的Windows限制为2GB。虚拟机提供了参数来控制Java堆和方法区的这两部分内存的最大值。剩余的内存为2GB（ 操作系统限制）减去Xmx（ 最大堆容量），再减去MaxPermSize（最大方法区容量），程序计数器消耗内存很小，可以忽略掉。如果虚拟机进程本身耗费的内存不计算在内，剩下的内存就由虚拟机栈和本地方法栈“ 瓜分”了。每个线程分配到的栈容量越大，可以建立的线程数量自然就越少，建立线程时就越容易把剩下的内存耗尽。

因此在开发多线程应用的时候特别注意，如果是建立过多线程导致的内存溢出，在不能减少线程数或者更换64位虚拟机的情况下，就只能通过减少最大堆和减少栈容量来换取更多的线程。

##Java堆

Java堆（Java Heap）是Java虚拟机所管理的内存中最大的一块。Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。

Java堆是垃圾收集器管理的主要区域，因此很多时候也被称为“GC堆”，Java虚拟机规范规定Java堆可以处于物理上不连续的内存空间中，只要逻辑上是连续的即可。如果在堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常。

异常测试代码如下：

     import java.util.*;

     /**
     *VM Args: -Xms20m -Xmx20m -XX: +HeapDumpOnOutOfMemoryError
     */
     public class HeapOOM{
         static class OOMObject{

         }

        public static void main(String[] args){
             List<OOMObject> list = new ArrayList<OOMObject>();
             while(true){
        list.add(new OOMObject());
             }
         }
     }

执行如下指令：

     java -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError HeapOOM

运行结果：

     java.lang.OutOfMemoryError: Java heap space
     Dumping heap to java_pid11220.hprof ...
     Heap dump file created [27880791 bytes in 0.211 secs]
     Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
             at java.util.Arrays.copyOf(Arrays.java:2245)
             at java.util.Arrays.copyOf(Arrays.java:2219)
             at java.util.ArrayList.grow(ArrayList.java:242)
             at java.util.ArrayList.ensureExplicitCapacity(ArrayList.java:216)
             at java.util.ArrayList.ensureCapacityInternal(ArrayList.java:208)
             at java.util.ArrayList.add(ArrayList.java:440)
             at HeapOOM.main(HeapOOM.java:11)

##方法区

方法区与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做Non-Heap（非堆），目的应该是与Java堆区分开来。当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

对于这个区域的测试，基本的思路是运行时产生大量的类去填满方法区，直到溢出，在这里借助CGLib 直接操作字节码运行时，生成大量的动态类。当前的很多主流框架，如Spring和Hibernate对类进行增强时，都会使用到CGLib这类字节码技术，增强的类越多，就需要越大的方法区来保证动态生成的Class可以加载入内存。

以下代码是：借助CGLib使得方法区出现内存溢出异常

     import net.sf.cglib.proxy.*;
     import java.lang.reflect.*;

     /**
	 * VM Args: -XX:PerSize=10M -XX:MaxPermSize=10M
     */
     public class JavaMethodAreaOOM{
         static class OOMObject{

         }
         public static void main(String[] args){
            while(true){
                 Enhancer enhancer = new Enhancer();
                 enhancer.setSuperclass(OOMObject.class);
                 enhancer.setUseCache(false);
                 enhancer.setCallback(new MethodInterceptor(){
                     public Object intercept(Object obj, Method method, Object[] args,MethodProxy proxy) throws Throwable{
                       return proxy.invokeSuper(obj,args);
                     }
                 });
                 enhancer.create();
             }
         }
     }

执行命令：
     
     java -classpath  .;./cglib-2.2.jar;./com.springsource.org.objectweb.asm-3.2.0.jar -XX:PermSize=10M -XX:MaxPermSize=10M -XX:+HeapDumpOnOutOfMemoryError JavaMethodAreaOOM
     
运行结果：

     java.lang.OutOfMemoryError: PermGen space
     Dumping heap to java_pid4020.hprof ...
     Heap dump file created [3515677 bytes in 0.154 secs]
     Exception in thread "main"
     Exception: java.lang.OutOfMemoryError thrown from the UncaughtExceptionHandler in thread "main"

方法区溢出也是一种常见的内存溢出异常，一个类如果要被垃圾收集器回收掉，判定条件是非常苛刻的。在经常动态生成大量Class的应用中，需要特别注意类的回收状况。这类场景除了上面提到的程序使用了GCLib字节码增强外，常见的还有：大量JSP或动态产生JSP文件的应用（JSP第一次运行时需要编译为Java类）、基于OSGi的应用（即使是同一个类文件，被不同的加载器加载也会视为不同的类）等。

##运行时常量池

运行时常量池是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述等信息外，还有一项信息是常量池，用于存放编译期生成的各种字面常量和符号引用，这部分内容将在类加载后存放到方法区的运行时常量池中。

运行时常量池相对于Class文件常量池的另外一个重要特征是具备动态性，Java语言并不要求常量一定只能在编译期长生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中。

     import java.util.*;

     /**
	 * VM Args: -XX: PermSize=10M -XX: MaxPermSize=10M
     */
     public class RuntimeConstantPoolOOM{
         public static void main(String[] args){
             List<String> list = new ArrayList<String>();
             int i = 0;
             while(true){
                 list.add(String.valueOf(i++).intern());
             }
         }
     }

执行如下代码：

	java -XX:PermSize=10M -XX:MaxPermSize=10M RuntimeConstantPoolOOM

在执行时发现无法使运行时常量池溢出，最终发现问题如下：

Area: HotSpot

Synopsis: In JDK 7, interned strings are no longer allocated in the permanent generation of the Java heap, but are instead allocated in the main part of the Java heap (known as the young and old generations), along with the other objects created by the application. This change will result in more data residing in the main Java heap, and less data in the permanent generation, and thus may require heap sizes to be adjusted. Most applications will see only relatively small differences in heap usage due to this change, but larger applications that load many classes or make heavy use of the String.intern() method will see more significant differences.

RFE: 6962931

由于我本机上是使用Java HotSpot(TM) 64-Bit Server VM (build 24.65-b04, mixed mode)，在该虚拟机中，不再将字符串分配到Java常量池中，而是在Java堆中进行分配，所以不会造成运行时常量池溢出。
     
##参考资料：

深入理解Java虚拟机：JVM高级特性与最佳实践

[http://www.dewen.io/q/12889](http://www.dewen.io/q/12889)

[http://www.oracle.com/technetwork/java/javase/jdk7-relnotes-418459.html](http://www.oracle.com/technetwork/java/javase/jdk7-relnotes-418459.html)

[http://sourceforge.net/projects/cglib/files/cglib2/2.2/](http://sourceforge.net/projects/cglib/files/cglib2/2.2/)





