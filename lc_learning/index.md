# 刷题学习1-时间和空间复杂度




## 时间复杂度
* 时间复杂度即为代码的执行次数量级  
* 算法的执行时间与输入之间的关系

### **O(1)** 
* 常量数量级的时间复杂度, 没有for循环和while循环
![在这里插入图片描述](https://img-blog.csdnimg.cn/a57b230f9a4944078746265845249cbf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### **O(N)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/457b071e0e184726ad52f223f507028e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 常量不看，其中`total=0`，`return total`为常量执行次数 
* 只有一层循环，循环的执行次数为**N**

### **O(logN)** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/892f93685e4a4ad1bd37a723e05a956f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 常量不看，其中`i=1`，`return i` 为常量执行次数  
* `i=i*2`的执行次数为**log<sub>2</sub>(N)**，舍去2，则最后结果为**O(logN)**

### **O(M+N)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/f8baafa0084a4a97a7fb4aa206e2d271.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 同理常量次数不看  
* 两个**O(N)**，一个执行M次，一个执行N次  
* 组合之后变成**O(M+N)**， 实际上等同于**O(N)**

### **O(NlogN)** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/d5e482f2d0824df3b8e8da4f97195597.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 同理常量次数不看 
* N次执行次数的循环内嵌套了一个logN次执行次数的循环
* 总次数为二者相乘即为**O(NlogN)** 

### **O(N<sup>2</sup>)** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb67d703079e41189270d3fda46075db.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* N次执行次数的循环内嵌套了一个N次执行次数的循环  
* 总次数为二者相乘即为**O(N<sup>2</sup>)** 

### 时间复杂度的判断方式
* 首先看有没有循环，没有则为常量次数**O(1)**  
* 如果有，首先判断每一次循环的执行次数  
* 循环之间的嵌套使用相乘的方式，得出最终的时间复杂度

### 各个时间复杂度之间的比较
![在这里插入图片描述](https://img-blog.csdnimg.cn/7aef854f80f3475381a99f404e249c70.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* **O(1)** < **O(logN)** < **O(N)** < **O(NlogN)** < **O(N<sup>2</sup>)** < **O(2<sup>N</sup>)** < **O(N!)**  
* 二分查找：**O(logN)**  
* 堆排序：**O(NlogN)**  
* 冒泡排序：**O(N<sup>2</sup>)**  
* 快速排序：**O(N<sup>2</sup>)**  
* 选择排序：**O(N<sup>2</sup>)**  
* 二叉树排序：**O(N<sup>2</sup>)**  
* 插入排序：**O(N<sup>2</sup>)**  

## 空间复杂度
* 算法的存储空间和算法的关系  
* 占空间的都是声明出来的变量

### **O(1)** 
* 存储的变量为常数数量级
![在这里插入图片描述](https://img-blog.csdnimg.cn/dd00c3db4d4c4a9c8fd4d46e07b6fd9c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* `total`在初始化之后虽然在循环当中被重复赋值，但是始终只占用total的空间  
* 所以存储的变量为常数数量级

### **O(N)**
* 存储的数量级为N，循环的次数
![在这里插入图片描述](https://img-blog.csdnimg.cn/8e032de01d36496aad807bcf346513bd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 数组的内的数字在不断增多，不断开辟新的内存空间

## _tip1_
```buildoutcfg
arrary = []
for num in nums:
    array.append(num)
return arrary
```
* 空间占用分为**堆空间**和**栈空间**  
* 在for循环中条件部分申明的变量 `num` 开辟的空间为**栈空间**  
* 使用完毕就会**释放**  
* 而其他变量使用的是**堆空间**，**程序完成之后才会释放**

## _tip2_
* 递归的函数内本身的空间复杂度如果为**O(1)**， 则调用递归的空间复杂度为**O(N)**  
* 递归的函数内本身的空间复杂度如果为**O(N)**， 则调用递归的空间复杂度为**O(N<sup>2</sup>)**  


