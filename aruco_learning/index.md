# Aruco配合realsense 使用



## 使用github安装
* 网址：[http://www.uco.es/investiga/grupos/ava/node/26](https://blog.csdn.net/qq_34935373/article/details/105098244)  
* git clone到本地之后，catkin make即可开始使用

## 使用apt安装
```shell
sudo apt-get install ros-kinetic-aruco-ros
sudo apt-get install ros-kinetic-aruco-msgs
sudo apt-get install ros-kinetic-aruco-detect
sudo apt-get install ros-kinetic-aruco
```

## 测试使用
```shell
roslaunch aruco_ros single.launch
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/88eba0a511a84000b1e458e9aca123c7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

如果没有报错，说明成功

## 网址生成二维码，及其相关信息
[http://chev.me/arucogen/](http://chev.me/arucogen/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/dc91858c732f420997f507207f3ed4af.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 图片的风格类型  
* marker ID  -----582
* 尺寸大小（注意这里的单位为mm）----34mm

## 修改launch文件夹中的single.launch文件
* 更改camera_info为realsense发布的相机内参话题
```shell
<remap from="/camera_info" to="/camera/color/camera_info" />
```
* 更改image
```shell
<remap from="/image" to="/camera/color/image_raw" />
```
* 更改camera_frame
```shell
<param name="camera_frame"       value="/camera_link"/>
```
* 更改Marker ID; marker size
```shell
<arg name="markerId"        default="582"/>
<arg name="markerSize"      default="0.034"/>    <!-- in m -->
```

## 开始测试
* 启动realsense节点
```shell
roslaunch realsense2_camera rs_camera.launch
```

* 启动single.launch 文件
```shell
roslaunch aruco_ros single.launch
```

* 使用image_view观察识别效果
```shell
rosrun image_view image_view image:=/aruco_single/result
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/68ec901a24e14ff18f98f8054d7c258e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)


* 查看位姿
```shell
rostopic echo /aruco_single/pose
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c88192a6cabb4c74b9ebe53ff709531e.png#pic_center)


参考：[https://blog.csdn.net/qq_34935373/article/details/105098244](https://blog.csdn.net/qq_34935373/article/details/105098244)





