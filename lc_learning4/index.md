# 刷题学习4-java 零碎基础知识


## 问号 ？冒号 ：条件语句
```java
int i = 0;
int x = i>1 ? 0:1;
```
* _问号冒号将其分为代码分为三个部分A、B、C_  
`A？B：C`  
* 如果A为真则执行B，如果为假则执行C

## 字符串charAt 函数
* String 类型的变量使用
* 指定index，返回该位置上的字符，返回类型为char类型

## String 和 StringBuilder 的使用
### 初始化
```java
StringBuilder str = new StringBuilder();
```
### 增加元素
```java
str.append(1);
str.append('a');
str.append(1.2);
System.out.println(str.toString());
```
### 指定位置增加
```java
str.insert(2, 's');
System.out.println(str.toString());
```
### 返回指定位置子序列
```java
System.out.println(str.subSequence(0, 3));
```
### 搜索元素
```java
System.out.println(str.indexOf("as"));
```
### 是否为空
```java
System.out.println(str.isEmpty());
```

## leetcode 题目强化
* 剑指offer 2 字符串二进制加法
