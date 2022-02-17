# 6-URDF


## 基本概念
* urdf 文件， 定义机器人的结构  
* 用于机器人的仿真、 可视化、 tf  
* ROS 的中包含一个urdf包，用来解析urdf文件  

## 描述机器人零部件和零部件之间的关系
* link  每个零部件都有自己的link（xyz三个轴）   
* joint  描述两个零部件连接的关节   
* urdf文件中定义两个link 之间必有一个joint
![avatar](/images_ros/pic16.png)

## urdf 文件的具体撰写
![avatar](/images_ros/pic17.png)
