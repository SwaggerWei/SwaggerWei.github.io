# 刷题学习1.5-java 链表 知识点ListNode



## 链表与数组的区别
* 数组的存放为*连续的内存空间*， 链表不需要，_任意碎片式的内存空间都可以形成链表_。

## 链表的分类
* 双端链表：既有next指向下一个数据也有previous指向前一个数据，可以双向遍历。
* 单端链表：只有next指向下一个数据，只能单向遍历。

## 链表的基本操作
* 访问----O(N) 需要遍历，从头找到尾
* 搜索----O(N) 需要遍历，从头找到尾
* 插入----O(1) 直接需要插入的位置断开指针，使上一个的指针指向新的元素的位置，新元素的下一个指向原本的下一个元素
* 删除----O(1) 断开删除元素前一个元素的next指向删除元素的next即可  
### tips   
* 这里的插入删除操作的时间复杂度实际上说的插入或删除这一个操作的时间复杂度。   
* 实际上整个操作的时间复杂度是*O(N)*，因为需要先查找到元素，遍历到指定位置，再进行插入。


## 应用场景
写多读少的场景 

## java 链表的常见操作
* 创建链表
```java
LinkedList<Integer> list_1 = new LinkedList<>();
```
* 添加元素
```java
for (int i = 0; i<3 ;i++){
    list_1.add(i+1);
}
System.out.println(list_1.toString());
list_1.add(2,99);
System.out.println(list_1.toString());
```
* 访问元素
```java
System.out.println(list_1.get(2));
```
* 查找元素
```java
System.out.println(list_1.indexOf(99));
System.out.println(list_1.indexOf(88));
```
* 删除元素
```java
list_1.remove(2);
System.out.println(list_1.toString());
```
* 链表长度
```java
System.out.println(list_1.size());
```
* 更新元素
```java
list_1.set(2, 88);
System.out.println(list_1.toString());
```

## 链表基础leetcode题目
### 1 leetcode 203 删除指定元素
**参数设置**：head（移动节点）， prev（head的前一个），dummy（保存头节点，最后返回结果）  
1 遍历整个链表，入口的条件是链表的头节点不为空（这样最后一个节点也可以遍历到）  
2 如果碰到指定值，prev 指向head的下一个节点head移动一步  
3 如果没有碰到指定值，prev指向head，head后移一步  
4 最后返回头节点（需要有dummy存储头节点）， 最后返回  
### 2 leetcode 206 反转链表
**参数设置**：head（移动节点）， hNext（head的后一个），dummy（保存头节点，最后返回结果），dNext（dummy的后一个节点）  
```java
dNext = dummy.next;
hNext = head.next;
dummy.next = hNext;
head.next = hNext.next;
hNext.next = dNext;
```

