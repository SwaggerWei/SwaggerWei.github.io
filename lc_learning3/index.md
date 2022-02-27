# 刷题学习3-java 栈学习



## 特点
* 先进后出，与队列这种数据结构相反，队列是先进先出。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ebbc38603f6f45639232f42e5001f34a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 实用例子： 浏览器后退功能

## 操作的时间复杂度
* 访问栈顶元素 _O(1)_  
* 搜索 _O(N)_  
* 插入 _O(1)_ 入栈  
* 删除 _O(1)_ 出栈  

## Java栈的常用操作
### 创建栈
```java
Stack<Integer> stack = new Stack<>();
```
### 添加元素
```java
stack.push(1);
stack.push(2);
stack.push(3);
System.out.println(stack.toString());
```
### 查看栈顶元素 - 即将出栈的元素
```java
int num = stack.peek();
```
### 删除栈顶元素 - 即将出栈的元素
```java
int temp = stack.pop();
System.out.println(temp);
```
### 返回栈的长度
```java
System.out.println(stack.size());
```
### 栈是否为空
```java
System.out.println("is empty: "+stack.isEmpty());
```
### 遍历栈
```java
while (!stack.isEmpty()){
    System.out.println(stack.pop());
}
```
## leetcode 练习题
* 20 valid Parentheses 有效的括号  
* 496 Next Great Element 下一个最大的元素

