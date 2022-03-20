# CPPF


## 背景
* 真实数据集上训练出来的模型往往难以推广到没有遇到过的场景
* 实例级别的9D姿态预测在测试时需要对没有见过的物体也有效，因为实例级别预测是没有CAD模型的，这往往也很难

## 贡献点
* 基于PPF(pair point features)提出CPPF(Category level pair point features)投票法进行9D姿态预测
* 提出一种由粗到细的算法消除噪声点对（当实例分割算法不够精准时）
* 提出一种实例分割算法用于发现目标物体
* 提出一个binary disambiguating classification task消除旋转姿态投票误报
* 只在虚拟点云上进行训练，除非物体在几何上有姿态非常模糊
* RGB信息被用来消除姿态歧义

## 数据集
* NOCS REAL275 
* SUN RGB-D

## 实验
* NOCS REAL275 对比实验
* NOCS REAL275 仅提供bounding box 进行9D预测实验
* SUN RGB-D 更加复杂的真实环境实验


