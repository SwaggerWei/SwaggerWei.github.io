# 4-tf介绍


## TransFrom
* 坐标变换（位置+姿态）  
* 坐标系数据的维护工具  

## 抓取场景下的坐标转换
* 涉及的坐标系：基坐标系、摄像头坐标系、机械臂末端坐标系  
* 物体坐标相对于摄像头坐标系-》物体坐标相对于基坐标系
![avatar](/images_ros/pic8.png)
* 物体坐标相对于夹爪末端坐标系  

## ROS中的tf
* 一个部件是一个link， 会发布自己的tf消息  
* 与之相连的link， 会接收其tf消息完成连接转化工作（joint工作）   
* ROS中tf表示的意思：标准 话题 工具 接口  
![avatar](/images_ros/pic9.png)

## tf消息格式
### 两个frame之间的tf变化 TransformedStamped.msg
![avatar](/images_ros/pic10.png)
```shell
seq-序号  
stamp-时间戳  
frame_id-本frame的id  
child_frame_id-子frame的id  
位置  
旋转位姿 
```
_很多个节点向/tf 话题发送消息， 形成tf树_     


