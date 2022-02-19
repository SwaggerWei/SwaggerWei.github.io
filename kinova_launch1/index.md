# 1-Kinova MOVO 手动启动操作


### _注：此方法适用于系统不能通过自检，并自动启动的情况_

## 手动启动bash
```shell
roslaunch movo_bringup movo_system.launch
```

## 运行tractor_node脚本，解决MOVO底盘问题
```shell
./tractor_node
```

## 直连MOVO进行joystick手柄操作
```shell
cd /home/movo/movo_ws
roslaunch movo_remote_teleop movo_remote_teleop.launch
```

## 手柄操作方式
* 操作方式图解，所有的操作除了手指操作，其他的都需要DeadMan按键激活操作
![在这里插入图片描述](https://img-blog.csdnimg.cn/65afe54dad0d460fbb01ccc0848f014b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0d1f75acd7ba4b88a1ecdfff8eae4b4e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



## rviz 操作
* 进入rviz
```shell
rviz
```
* 点击motion planning 中planning 
* Query栏中状态更新，选中状态之后点击update
* Commands栏中先点击plan，再update



