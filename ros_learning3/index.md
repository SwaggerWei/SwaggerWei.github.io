# 3-roscpp介绍



## Client Library
* 提供ROS编程的库  
* 例如建立node，发布消息，调用服务
* 提供了如下几种client library ：roscpp、rospy、roslisp

## rospy的组成
* Node  
* Topic  
* Service  
* Param  
* Time  

## rospy-Node 相关
![avatar](/images_ros/pic4.png)

## rospy-topic 相关
![avatar](/images_ros/pic5.jpeg)
* wait_for_message: 只接收一次消息，不是一直处理消息  
* publisher-》_init_: 当中的queue_size, 设置为1， 为异步通信方式， None为同步通信方式

## topic_demo
### 功能描述：   
* 两个node，一个发布模拟的GPS消息（格式为自定义，包括坐标和工作状态）  
* 另一个node接收并处理该信息（计算到原点的距离）
### 步骤
* 建立一个package 
```shell
cd ~/catkin_ws/src
catkin_create_pkg topic_demo roscpp rospy std_msg
```
* 定义自己的msg文件
```shell
cd topic_demo/
mkdir msg
cd msg
gedit gps.msg
```
文件内容如下
![avatar](/images_ros/pic6.png)
* 编写*talker.py*  
* 编写*listener.py*  
* 修改*CmakeList.txt; package.xml*文件  
* 最后执行catkin_make，编译完成之后会在如下路径下生成gps的头文件
![avatar](/images_ros/pic7.png)
* 使用rosrun运行talker.py和listener.py文件
