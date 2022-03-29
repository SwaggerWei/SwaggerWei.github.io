# 6DPose 论文总结


## 关注的问题
* 对遮挡物体的鲁棒性
* 如何处理对称物体的ambiguous rotation问题

## 对称物体的ambiguous rotation问题

## 分类
* 使用方法分类：correspondence-based，template-based，voting-based（keypoint-based）
* 预测目标级别分类：instance-level，category-level
* instance-level常用技术路线：实例分割之后与标准CAD模型进行模板匹配回归出位姿信息
* category-level常用技术路线：缺少实例目标CAD模型，需要额外对每个Category设计标注范式
