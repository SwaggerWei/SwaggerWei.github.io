# 5-tf in python 


## tf in python 的相关数据类型
* tuple list numpy array 四种方式通用  
* EG：
![avatar](/images_ros/pic11.png)

## 四元数、矩阵、欧拉角之间的转换
![avatar](/images_ros/pic12.png)

## tf.TransformListener 类
* 构造函数为无参数  
![avatar](/images_ros/pic13.png)  
_tips：查看相对的tf，方法中注意时间要填写为time(0), 不能使用time(now)获取当前时间，因为获取当前的tf消息是空的，需要等待一下才有消息_
![avatar](/images_ros/pic14.png)

## tf.transformBroadcaster 类
* 构造函数为无参数
![avatar](/images_ros/pic15.png)

## tf相关指令
* 根据当前的tf tree 绘制一个PDF图: 接收/tf 消息五秒，然后生成一个树图
```shell
rosrun tf view_frames
```
* 查看当前的tf tree： 动态显示 
```shell
rosrun rqt_tf_tree rqt_tf_tree
```
* 查看两个frame 之间的变化关系
```shell
rosrun tf tf_echo [reference_frame] [target_frame]
```
