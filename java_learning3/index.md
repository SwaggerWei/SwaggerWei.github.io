# Java 学习3-容器


## 3 容器
数据容器主要分为了两类：Collection 和 Map  
* Collection: 存放独立元素的序列
* Map：存放key-value型的元素对

### 3.1 Collection 存放独立元素的序列
* List： ArrayList LinkedList Vector Stack
* Set：HashSet LinkedHashSet TreeSet

### 3.2 Map：存放key-value型的元素对
* HashMap： LinkedHashMap
* TreeMap： ConcurrentHashMap
* HashTable

### 3.3 Collection 和 Collections 的区别
* Collection 是一个接口， Set和List等容器的父接口
* Collections 是一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等等

### 3.4 List、Set、Map 的区别
![在这里插入图片描述](https://img-blog.csdnimg.cn/0422a19d01654177b5e783db1a77c963.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


### 3.5 HashMap 和 HashTable的区别
* 存储：HashMap 运行 key 和 value 为 null，而 Hashtable 不允许。  
* 线程安全：Hashtable 是线程安全的(同一时间只允许一个线程作出改变)，而 HashMap 是非线程安全的（同一时间可能有多个线程对其进行操作）。  
* 推荐使用：在 Hashtable 的类注释可以看到，Hashtable 是保留类不建议使用，推荐在单线程环境下使用 HashMap 替代，如果需要多线程使用则用 ConcurrentHashMap 替代。

### 3.6 实例
```java
package Java_ContainerDemo;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.Collections;

public class collectionDemo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(5);
        list.add(3);
        list.add(4);
        list.add(6);
        System.out.println(list.toString());

        Set<Integer> set = new HashSet<>();
        set.add(1);
        set.add(2);
        System.out.println(set.toString());

        // 测试Collections 工具箱
        Collections.sort(list);
        System.out.println(list.toString());
        List<Integer> list1 = Collections.emptyList();
        System.out.println(list1.toString());
    }
}
```


