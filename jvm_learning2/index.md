# JVM 学习2


## JVM常见的问题
**_基本都是理论知识_**

* 谈谈你对JVM的理解
* JAVA8 虚拟机和之前的区别
* 什么是OOM
* 什么是栈溢出StackOverFlowError，怎么分析
* JVM的常用调优参数
* 内存快照如何抓取
* 怎么分析Dump文件
* 谈谈JVM中类加载器你的认识

## 重点
* JVM的位置
* JVM的体系结构
* 类加载器
* 双亲委派机制
* 沙箱安全机制
* Native
* PC寄存器
* 方法区
* 栈
* 三种JVM
* 堆
* 新生区
* 老年区
* 永久区
* 堆内存调优
* GC垃圾回收机制
* GC常用算法
* JMM

## 学习方法
* 百度搜索
* 思维导图

## JVM的位置
![](/image_JVM/pic1.png)
* 基础是硬件体系，以上是操作系统，JVM依托于操作系统之上
* 实际上运行只需要JRE即可（整个JDK太大了），java程序可以直接运行在JRE上

## JVM的体系结构
![](/image_JVM/pic2.png)
* java栈、本地方法栈、程序计数器 不会有垃圾回收机制
* 方法区是一种特殊的堆
* 垃圾回收机制存在于方法区和堆中
* JVM调优基本都是调堆
* native 中的本地方法库指的是java的底层实现，比如多线程中的start 方法，其中底层实现调用了native库中的start0方法，由c++是实现

## 类加载器
![](/image_JVM/pic3.png)
* 作用：加载class文件 new 一个对象的时候，对象的引用(名字、地址)在栈中，实际的对象数据都在在堆内存中
* 虚拟机自带的加载器
* 启动类（根）加载器
* 扩展类加载器
* 应用程序加载器
* java 的底层是C++实现的，去掉了C++ 当中的指针和内存管理，内存管理全部交给JVM来进行管理了。

### 简单过程
* 类加载器收到类加载请求
* 将这个请求向上委托给父类加载器去完成，一直向上委托，直到委托到boot class loader（启动类加载器）
* 检查启动类加载器是否能够加载当前这个类，如果能加载就结束，使用当前加载器，否则，跑出异常，通知字加载器进行加载
* 重复步骤3（最后一层子类就是当前用户自定义的类----app）

## 双亲委派机制
* 为了安全而执行，避免程序员改动boot 加载器所涉及的java基础包，造成不必要的错误
* 先去boot 根加载器中寻找，再去ext 扩展类加载器中寻找，最后去app 应用程序（自定义）加载类中寻找，如果自定义的方法和类与上述路径有重名，则执行上述路径中的


