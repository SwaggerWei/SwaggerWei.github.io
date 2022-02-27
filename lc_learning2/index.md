# 刷题学习2-java 队列学习


## 队列的基本特征
* 先进先出
* 单端队列--只有一个口可以进，一个口可以出
* 双端队列--两个口都可以进， 两个口都可以出

## 队列数据结构的特点
* 访问*O(N)* 需要从头到尾遍历一遍 
* 搜索*O(N)* 需要从头到尾遍历一遍
* 插入*O(1)* 插入只能在队尾插入，不需要遍历，所以时间复杂度为*O(1)*
* 删除*O(1)* 删除只能在队头删除，不需要遍历，所以时间复杂度为*O(1)*
* _tips_ 队列的底层实现是链表

## 队列的常见操作
* 创建队列
```java
Queue<Integer> queue = new LinkedList<>();
```
* 添加元素
```java
for (int i = 0; i< 3 ; i++){
    queue.add(i+1);
}
System.out.println(queue.toString());
```
* 获取即将出队的元素 peek()
```java
int temp1 = queue.peek();
System.out.println(temp1);
```
* 删除即将出队的元素 remove()、pop()、poll()
```java
int temp2 = queue.poll();
System.out.println(temp2);
System.out.println(queue.toString());
```
* 判断队列是不是为空
```java
System.out.println(queue.isEmpty());//O(1)
```
* 队列长度
```java
System.out.println(queue.size());//O(1)
```
* 遍历队列（边删除边遍历队列的操作）
```java
while (!queue.isEmpty()){
    int temp = queue.poll();
    System.out.println(temp);
}//O(N)
```
 
## leetcode 队列基础练习题
### 933 最近的请求次数 number of Recent calls
* 参数使用： 类的成员变量-队列queue、ping函数中变量-temp保存即将出队的元素  
* 构造函数中初始化queue为一个队列  
* 调用ping时首先最新的t入队  
* while开始条件：队列不为空  
* 查找即将出列元素到存入temp，如果t-temp大于3000，则出列  
* 如果小于，不出列，停止循环，返回队列长度为最终结果
### 239 滑动窗口最大值



