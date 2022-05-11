# 6DPose—Object Localization总结


## Object Localization的三种分类
* object localization without classification：输出物体的区域
* object detection：输出物体在图像中所处的bounding box、物体的类别
* object instance segmentation：输出像素级或者点级别的目标物体区域、物体类别

## object localization without classification
### Fitting 2D shape primitives
![](/image_object_localization/pic1.png)
* 对目标物体进行轮廓提取
* 然后对基础形状原语进行拟合，从而得出目标物体的位置

### 2D salient object detection
* 没有任何形状原语，对任意的形状的物体都可以进行拟合
* 深度学习方法：对原图进行特征提取之后，进行聚类或者回归
* 深度学习方法分类：MLP、Fully Connected Convolutional Network、Capsule Based Method
* 工业上物体多为固定形状的物体，所以多用形状拟合的方式进行识别，性能高，速度快
* 共融机器人抓取对于多用深度学习方法

### Fitting 3D shape primitives
* 对目标物体进行3D点云的轮廓提取
* 然后对基础形状原语进行拟合，提取得到物体的位置 

### 3D salient object detection
* 

