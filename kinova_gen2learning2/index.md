# 2-kinova-gen2 使用学习


## 安全使用
* 不要进行剪裁操作，尖锐物体可能会上伤到手臂  
* 关闭的时候会泄力，注意提前用手拖着  
* 关节移动的时候不要限位

## 部件
* 手臂主体部件
![在这里插入图片描述](https://img-blog.csdnimg.cn/698a8eb2f22a4b5db081f1a8a64dfabd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 背面接口部件
![在这里插入图片描述](https://img-blog.csdnimg.cn/f2fdbb608cce4c60a21218c770f9e7f2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* USB、网线接口：公kinova SDK 控制使用  
* switch：开关  
* 8pin口：摇杆控制  
* 4pin口：电源线  

## 控制机械臂
### 手柄控制
* _两种控制模式：Cartesian velocity（默认）、Angular velocity_
* 手柄结构：
![在这里插入图片描述](https://img-blog.csdnimg.cn/76e4329ba7054402b2e59816382f303a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 手柄按键指示表：
![在这里插入图片描述](https://img-blog.csdnimg.cn/14e3fbe6023d439faf2ac989bea19f3b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### ROS 控制
* 具体访问`https://github.com/Kinovarobotics/kinova-ros`  
* 选择对应ros版本的branch，然后进行安装
### 3 SDK 控制 C++ library


