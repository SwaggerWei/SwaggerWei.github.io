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


