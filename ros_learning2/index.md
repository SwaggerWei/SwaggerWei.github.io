# 2-ROS常用工具


## ROS常用工具
* 仿真：Gazebo  
* 调试、可视化：Rviz、rqt  
* 命令行工具：rostopic、roslaunch、rosbag  
* 专用工具：Moveit

## Gazebo
* 机器人仿真工具  
* ODE、Bullet 等物理引擎   
* 动力学、导航、感知等任务的模拟

## Gazebo 操作
* 鼠标左键平移， 中键旋转  
* 左侧world：各类物理模型及其属性   
* 左侧insert：可以插入各种模型  

## RViz 
* 方便调试和监控的工具  
* ADD 添加监视器（RbotModel、Laserscan、PointCloud、Camera等）  
* 选中监视器中的topic，进行消息的接收，才能进行可视化

## rqt可视化命令行工具
* rqt_graph：显示通信架构（节点和话题的发布状态）  
* rqt_plot：绘制曲线  
* rqt_console：查看日志 

## rosbag 
* ROS命令行工具  
* 记录和回放数据流
![avatar](/images_ros/pic3.png)
* 回放会将记录的消息重新发布，_单独使用回放之前需要roscoe启动master节点_


