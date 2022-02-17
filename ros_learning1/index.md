# 1-ROS通信方式


## 通信方式
* topic  
* service  
* parameter server  
* actionlib  

## topic 通信方式
_异步通信方式_  
* Node间通过publish-subscribe的方式进行通信
![avatar](/images_ros/pic1.png)
* topic 发布节点只管发布， 接收节点只管接收

_特点_  
* 异步通信方式  
* 对于同一个topic可以有多个发送者（例如tf，和rosout），也可以有多个接收者

## Message
* topic 内容的数据类型---》类  
* 定义在.msg文件当中  

包括的类型
![avatar](/images_ros/pic2.png)
  
## topic 相关命令
* 列出所有的topic
```shell
rostopic list
```
* 显示某个topic的属性信息
```shell
rostopic info /topic_name
```
* topic内容
```shell
rostopic echo /topic_name
```
* 向某个topic发布内容
```shell
rostopic pub /topic_name ...
```
* 列出所有msg
```shell
rosmsg list
```
* 显示某个msg 的内容
```shell
rosmsg show /msg_name
```




