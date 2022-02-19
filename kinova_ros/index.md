# 4-Kinova_ros 配置文档



## 我的环境
* ubuntu 16.04 64bits  
* ROS Kinetic

## Kinova SDK 下载 
* 进入kinova官网 `kinovarobotics.com`   
* 找到右上角resource， 根据自己kinova机械臂的名称找到对应的资源库，我的是Gen2  
![在这里插入图片描述](https://img-blog.csdnimg.cn/731c09e93d644afcbaa2ead5d18647e5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 选择对应自由度的机械臂，然后找到SDK下载
![在这里插入图片描述](https://img-blog.csdnimg.cn/7a31f595073a411197af607f1320354a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## Kinova SDK 安装
* 解压SDK, 切换到目录中  
* 打开终端
```shell
cd Ubuntu/16_04/64\ bits/
```
* 使用.sh文件安装  
```shell
sudo bash installSDK64.sh 
```
* 一直下一步即可，　一般是默认安装在opt目录下

## SDK DevelopmentCenter GUI 简单使用
* 切换到安装目录下  
```shell
cd /opt/JACO-SDK/GUI/
```
* 运行GUI文件
```shell
sudo ./DevelopmentCenter.sh
```
* 右上角检测到序列号，　且使用Virtual joystick可以使用说明成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f4fe5d7188d42f1a03aa1843a6e200b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## Kinova-ros 安装
* 创建工作空间
```shell
mkdir "~/catkin_ws/src/"
```
* 切换到工作空间
```shell
cd ~/catkin_ws/src/
```
* 下载
```shell
git clone https://github.com/Kinovarobotics/kinova-ros.git kinova-ros
```
可以导入到gitee 再下载  
* 编译
```shell
cd ~/catkin_ws/
catkin_make
```
* 更改bashrc文件
```shell
source ~/catkin_ws/devel/setup.bash
```
此句话加入到bashrc中


