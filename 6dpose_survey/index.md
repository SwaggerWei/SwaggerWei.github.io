# 6DPose 论文总结

## robot grasping 基础
![](/image_CPPF/pic1.png)
* 主要分为三个关键任务，Object Localization，Object Pose Estimation，Grasp Estimation
* Object Localization主要的几种方法，Object Localization without classification，Object Detection，Object Instance segmentation
* Object Pose Estimation主要的几种方法，Corresponding-based methods，Template-based methods， Voting-based methods
* Grasp Estimation的两种方式，2D planer grasp，6DoF grasp  
_tips：这三个关键任务不一定要全部都用上才能抓取识别，实际上很时候只用其中的1个或者2个就可以完成抓取识别_

### 各个关键任务的作用
* Object Localization：找到目标对象的区域所在
* Object Pose Estimation：估计物体的6D姿态，以方便生成抓取位姿
* Grasp Estimation：估计物体的抓取位姿，2D抓取约束为从一个方向抓取，但是6DoF 抓取可以从任意方向进行抓取

### 一个抓取系统所包含的部分
![](/image_CPPF/pic2.png)
* grasp detection system：进行抓取姿态预测
* grasp planning system：有了姿态预测之后进行抓取路径规划
* control system：控制各个部件

### 抓取end effectors
![](/image_CPPF/pic3.png)
* 夹爪：平行夹爪，三指夹爪，五指夹爪
* 吸盘：单吸盘，多吸盘（此种方式抓取方式简单，面对几何复杂的物体难以起效）

## 抓取方式的不同
![](/image_CPPF/pic6.png)
### 2D planer grasp
![](/image_CPPF/pic4.png)
* 2D 形式的抓取规定机械臂只能朝一个方向去抓取（垂直于工作台平面）
* 高度是固定的，抓取方式预测被降为2D➕1D的方式，2D为平面上的物体位置，1D为旋转角，有些还会加上夹爪的宽度参数和矩形框描述法中的高度参数
* 具体有两种方法：接触点评估法（平行夹爪与物体基础的两个点，如double dot图），矩形框评估法（如上图）  
![](/image_CPPF/pic5.png)

### 6DoF grasp
![](/image_CPPF/pic7.png)
* 在3D空间中可以以任意角度对物体进行抓取，所以不能像2D planer grasp方法那样对抓取方式进行简化
* 开始时使用分析法，之后在RGB-D 的辅助下，开始使用深度学习的方法，进行预测
* 开始时都是对已知物体进行预测，之后开始对陌生物体也进行预测
* 基于部分局部点云的方法：不需要物体的CAD模型，还可以细分为估计候选抓取方法质量的方法，和从现有抓取转移抓取的方法
* 基于完整形状的方法：当目标物体的CAD模型已知时，可以直接预测出6D物体位姿，当CAD模型未知时，使用点云重建CAD模型，得到完整形状，从而预测6D物体位姿

### 9Dof grasp
![](/image_CPPF/pic8.png)
* 6DoF的基础上加上了对物体长宽高的预测
* 可视化结果多为Bounding Box，图中的红绿蓝三条线为空间坐标，bounding box 包含三维旋转位姿和三维scale信息

## object localization 
* Object Localization without classification：找到目标物体所在的区域，但是不知道目标物体的类别
* Object Detection：找到目标物体的所在的区域，同时知道其类别，多用于目标区域内有多个物体，要找出指定物体的位置
* Object Instance segmentation：指的是探测像素级别某一类别物体的实例对象

## 关注的问题
* 对遮挡物体的鲁棒性
* 如何处理对称物体的ambiguous rotation问题

## 分类
* 使用方法分类：correspondence-based，template-based，voting-based（keypoint-based）
* 预测目标级别分类：instance-level，category-level
* instance-level常用技术路线：实例分割之后与标准CAD模型进行模板匹配回归出位姿信息
* category-level常用技术路线：缺少实例目标CAD模型，需要额外对每个Category设计标注范式




_By Boyan Wei_ 



