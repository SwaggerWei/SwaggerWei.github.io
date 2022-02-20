# 1-PointNetGPD论文阅读



## 资源相关
* [项目地址](https://lianghongzhuo.github.io/PointNetGPD/)  
* [源码地址](https://github.com/lianghongzhuo/PointNetGPD)  
* [论文地址](https://arxiv.org/pdf/1809.06267.pdf)

## 输入输出 
* input： 点云数据  
* output： 抓取位姿（6D）(parallel-jaw)方式

## contribution 
* 网络轻量化  
* 预测夹爪的6D位姿  
* 贡献一个真实的点云抓取数据集  
* 网络模型可以准确的分析出夹爪和抓取物体的几何信息，在点云数据很粗糙的情况下准确度也有保证

## motivation 
* 现如今的抓取预测方式都是依据RGB和Depth 结合的方式，很少考虑几何信息  
* PointNet 直接在点云做分割和分类的效果非常好  

## pipeline
![pipeline](https://img-blog.csdnimg.cn/517ef70bb9e341b0bc7ae861e8a88d45.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 抓取实验评价指标
_Meticulous grasp quality scores_  包括如下两部分  
* force-closure  
* GWS analysis  




