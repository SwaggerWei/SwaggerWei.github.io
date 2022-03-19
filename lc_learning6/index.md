# 刷题学习6-java集合Set学习


## 特点
* 无序  
* 不重复  
* _注重自己独一无二的特性_

## 主要作用
* 检查某一个元素是否存在  
* 有无重复元素

## Set的种类
* HashSet 主要使用
* LinkListSet  
* TreeSet  
   
## 将一个元素加入到HashSet过程原理
* 取得元素  
* 通过哈希函数得到哈希值  
* 通过哈希值在哈希表中寻找，如果表中没有此元素，就直接存入到哈希表中  
* 如果有元素，然后进行对比  
* 如果已有元素和当前元素相等，则不做操作  
* 如果不相等，则发生哈希冲突（使用链表方法解决）

## 哈希集合的操作的时间复杂度
* 访问：不存在  
* 搜索：无哈希冲突*O(1)* ，有哈希冲突*O(k)* ---**k为冲突元素的个数**  
* 插入：无哈希冲突*O(1)* ，有哈希冲突*O(k)* ---**k为冲突元素的个数**  
* 删除：无哈希冲突*O(1)* ，有哈希冲突*O(k)* ---**k为冲突元素的个数**

## 哈希集合的常用操作
* 创建集合  
```java
HashSet<Integer> set = new HashSet<>();
```
* 添加元素  
```java
set.add(10);
System.out.println(set.add(10)); // 检测到元素重复，添加不成功  add 会返回一个boolean值
set.add(3);
set.add(5);
set.add(2);
set.add(2);
System.out.println(set.toString());
```
* 查询元素  
```java
System.out.println(set.contains(2));
```
* 删除元素  
```java
set.remove(2);
System.out.println(set.toString());
```
* 长度  
```java
System.out.println(set.size());
```
## leetcode 练习题
* 217 Contains Duplicate 存在重复元素  
* 705 Design Hashset 设计哈希集合
