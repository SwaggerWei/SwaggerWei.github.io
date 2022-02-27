# 刷题学习8-java 堆 知识点Heap


## 基本定义
* 堆就是一种二叉树结构---完全二叉树  

* _完全二叉树：_  
1 每个父节点，最多只有两个孩子  
2 **从上到下，从左到右**依次填满

## 堆需要满足的条件：  
* 是一个完全二叉树  
* 每个节点都大于等于孩子节点（最大堆）或者每个每个节点都小于等于孩子节点（最小堆）

## 最大堆和最小堆的辨析
![在这里插入图片描述](https://img-blog.csdnimg.cn/c778184a99d643e3ba70e44dcab80a3f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 根节点：堆顶元素

## 堆常见操作的时间复杂度
* 访问 Access X  
* 搜索 Search _O(1)_  堆顶元素  
* 添加 Insert _O(logN)_  为了满足最大堆或者最小堆的定义，需要进行父节点互换  
* 删除 Delete _O(logN)_  删除堆顶元素  

## 堆化操作的时间复杂度
* 堆化操作的时间复杂度是O(N)的原理  
* _堆化：将一组无序的数加入到堆里去_
* _堆化：将一组无序的数转化为完全二叉树，然后将完全二叉树转化为最大堆或者最小堆的操作_  
### Eg：
有一组数：[10, 9, 7, 12, 16, 8, 5]  

* 构建完全二叉树，结构是唯一的，只需要将数组从头到尾遍历一遍 *O(N)*  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f9afd196659944c19ad94fc464d063c1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


* 转化为 最小堆｜最大堆 *O(N)*  
* 整体的时间复杂度为 *O(N)*  



## 堆的常见操作
* 创建堆（最大堆｜最小堆）   
```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
```
* 添加元素  
```java
minHeap.add(10);
minHeap.add(8);
minHeap.add(9);
minHeap.add(11);
minHeap.add(2);

maxHeap.add(10);
maxHeap.add(8);
maxHeap.add(9);
maxHeap.add(11);
maxHeap.add(2);
```
* 获取堆顶元素  
```java
System.out.println(minHeap.peek());
System.out.println(maxHeap.peek());
```
* 读取堆顶元素并出堆  
```java
minHeap.poll();
System.out.println(minHeap.toString());
maxHeap.poll();
System.out.println(maxHeap.toString());
```
* 堆的长度  
```java
System.out.println(minHeap.size());
System.out.println(maxHeap.size());
```
* 堆的遍历
```java
while (!minHeap.isEmpty()){
    minHeap.poll();
    System.out.println(minHeap.toString());
}

while (!maxHeap.isEmpty()){
    maxHeap.poll();
    System.out.println(maxHeap.toString());
}
```

## leetcode练习题
* 215 数组中第k个最大元素 kth largest element in a array  
* 692 前k个高频单词 top k frequent words 


