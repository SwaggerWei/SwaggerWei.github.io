# JVM 学习5


## 三种JVM 
* Hotspot：Sun公司，用户数多
* BEA JROckit：Oracle 设个财务前段办公、军事指挥与控制和电信网络的需要
* j9vm JIT编译器：IBM公司，以Oracle 的java 为基础

## 堆（堆内存）
![](/image_JVM/pic10.png)
Heap，一个JVM只有一个堆内存，堆内存的大小是可以调节的。

### 基本概念
* 类加载器读取了类文件之后，类、方法、常量、变量、保存所有引用类型的真实对象
* 堆内存中细分为三个区域：新生区younger new、养老区old、永久区perm（JDK1.8已经没有永久区，改成元空间了）
* 新生区包括：伊甸园、幸存0区、幸存1区
* 如果伊甸园区中新生的对象经过垃圾GC回收之后还没有被回收，则进入幸存0区
* 如果幸存0区满了，就会进入幸存1区，两个幸存区之间可以相互调动
* 如果在幸存区之中依然撑过了垃圾回收机制，则进入养老区 
* 垃圾回收分为轻量级、重量级。轻量级针对新生区，重量级针对养老区
* 永久存储区包括java内置的方法和数据

### 堆内存溢出错误（OOM）
![](/image_JVM/pic11.png) 
* GC 垃圾回收主要是在伊甸园区和养老区
* 假设内存满了，会报错OOM(OutOfMemoryError)，堆内存不够了，如下图代码所示
* 在JDK8以后，永久存储区改名你为元空间 

## 新生区
* 一个类诞生产生死亡的地方
* 分为伊甸园区和幸存者区（幸存者0区和幸存者1区）
* 所有的对象都出生在伊甸园区（new）
* 假设伊甸园区只能存放10个实例对象，就会出发一次轻GC，活下来的对象就会进入幸存区
* 同理幸存区满了就会触发一次重GC，幸存下来的进入养老区
* 如果新生区和养老区都满了，则出现OOM错误出现 
* 实际上99%的对象都是临时对象，很难进入到养老区

## 永久区
* 这个区域常驻内存，用来存放JDK自身携带的Class对象，Interface元数据
* 存储Java运行时的一些环境或者类信息
* 这个区域不存在垃圾回收机制
* 关闭虚拟机的时候就会释放这个区域的内存
* 一个启动类如果加载了大量的jar包，或者tomcat部署了太多应用，大量动态生成的反射类，如果这些内容不段被加载，可能会出现OOM错误

### 版本变化
* JDK1.6之前：永久代，常量池在方法区中
* JDK1.7：永久代，但是慢慢弱化了，去永久代，常量池在堆中
* JDK1.8：无永久代，常量池在元空间之中

### 堆整体结构
![](/image_JVM/pic12.png) 
* 方法区（存放类中的常量等信息--常量池）是一种特殊的堆（堆）
```java
package swagger_test;

import javax.management.relation.RoleUnresolved;

public class HeapTestDemo {
    public static void main(String[] args) {
        // 返回最大内存
        long maxMemory = Runtime.getRuntime().maxMemory();
        // 返回初始化内存
        long totalMemory = Runtime.getRuntime().totalMemory();

        System.out.println("maxMemory=" + maxMemory + "字节\t" + (maxMemory/(double)1024/1024) + "MB");
        System.out.println("totalMemory=" + totalMemory + "字节\t" + (totalMemory/(double)1024/1024) + "MB");
    }
}
```

### 查看堆内存
![](/image_JVM/pic13.png)
* 最大内存约等于计算机总内存的4分之一
* 初始化内存为计算机总内存的64分之一
* 元空间逻辑上存在，但是物理上不一定存在，实际上在系统的内存中

### 调整堆内存大小
![](/image_JVM/pic15.png)
![](/image_JVM/pic16.png)
* 其中`-Xms`为最大内存大小，调整为1024mb
* 其中`-Xmx`为初始化内存大小，调整为1024mb
* `-XX:PrintGCDetails`为打印具体的内存信息

### 遇到OOM错误的处理方式
* 尝试扩大对内存空间大小
* 分析内存，看哪里出了问题（idea 专业工具）
