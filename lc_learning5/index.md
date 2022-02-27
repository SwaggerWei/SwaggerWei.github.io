# 刷题学习5-java 哈希表学习-散列表


## 基础定义
* _键值对：key：value_  
例如学号姓名：  
1：张三  
2：李四  
3：王五  
* 查找元素的方式不需要从头遍历到尾，直接通过键（key）来进行查找，时间复杂度低

## 语言的使用
* Java---HashMap  
* python----字典

## 哈希查找对应元素的机制
![在这里插入图片描述](https://img-blog.csdnimg.cn/9b73f81d8d8b4cdba6e610aa844a27c6.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

_哈希碰撞_  
* 两个不同的key通过同一个哈希函数，得到相同的内存地址  
* 解决方式：将两个键值对使用链表串起来

## 各种操作的时间复杂度
* 访问 X  
* 搜索（无哈希碰撞） _O(1)_  
* 搜索（有哈希碰撞） _O(K)_ 链表遍历时经过了k个元素  
* 插入 _O(1)_  
* 删除 _O(1)_  

## Java 哈希表的常用操作
* 创建哈希表  
```java
// 通过字符串数组创建哈希表
String[] hashTable = new String[4];
// 通过HashMap 库创建哈希表
HashMap<Integer, String> hashMap = new HashMap<>();
```
* 添加  
```java
//        添加
hashTable[1] = "张三";
hashTable[2] = "李四";
hashTable[3] = "王五";
//        使用数组的方式将其输出
for(int i = 0; i<hashTable.length-1; i++){
    System.out.println((i+1)+"-"+hashTable[i+1]);
}

hashMap.put(1, "张三");
hashMap.put(2, "李四");
hashMap.put(3, "王五");
System.out.println(hashMap.toString());
```
* 删除  
```java
hashTable[1] = "";
for(int i = 0; i<hashTable.length-1; i++){
    System.out.println((i+1)+"-"+hashTable[i+1]);
}
hashMap.remove(1);
System.out.println(hashMap.toString());
```
* 更新元素  
```java
hashTable[1] = "赵四";
for(int i = 0; i<hashTable.length-1; i++){
    System.out.println((i+1)+"-"+hashTable[i+1]);
}
hashMap.put(1 , "赵四");
```
* 获取元素  
```java
System.out.println(hashTable[2]);
System.out.println(hashMap.get(2));
```
* 检查key的值是否存在  
```java
System.out.println(hashMap.containsKey(3));
```
* 哈希表的长度  
```java
System.out.println(hashTable.length);// 有一个内存位置为空，4
System.out.println(hashMap.size());
```
* 哈希表是否还有元素  
```java
System.out.println(hashMap.isEmpty());
```
## leetcode 练习题
* 217 存在重复元素 Contains Duplicate  
* 389 找不同 Find Difference  
* 496 下一个最大元素 Next Great Element  
