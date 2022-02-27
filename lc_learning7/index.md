# 刷题学习7-java 树知识点Tree



## 树的基本概念
* 描述的是节点之间的父子关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/4be61e75f912401a845c858bb5009fc0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* _tips： 成环的结构不可能成为树， 因为无法确定父子关系_

## 重要的名词
* 节点  
* 根节点（开始的节点，没有父节点，具有唯一性）  
* 叶子节点（没有孩子的节点）  

## 高度、深度、层 概念辨析
![在这里插入图片描述](https://img-blog.csdnimg.cn/34b1823923154afaa2a0823a8746918d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 高度：从叶子向根看，从0开始计数  
* 深度：从根向叶子看，从0开始计数  
* 层： 从根向叶子看，从1开始计数

## 二叉树
* 普通二叉树：每个节点最多两个孩子  
* 满二叉树：除了叶子节点，每个节点都有左右两个孩子，且所有的叶子节点都在同一层
* 完全二叉树：从树的根节点，*从上到下，从左到右，依次填满*节点形成二叉树  
* _tips：如果一个二叉树是满二叉树，那么一定是完全二叉树，反过来不一定_

## 遍历
* 前序遍历： 根节点、左子树、右子树  
* 中序遍历： 左子树、根节点、右子树  
* 后序遍历： 左子树、右子树，根节点  

* _EG:_  
![在这里插入图片描述](https://img-blog.csdnimg.cn/5153c7c118be4870a3967250facacef8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

前序遍历：A B D E C F G  
中序遍历：D B E A F C G  
后序遍历：D E B F G C A  

## leetcode 练习题
* 144 Binary Tree Preorder Traversal  前序遍历  
* 94 Binary Tree Inorder Traversal  中序遍历  
* 145 Binary Tree Postorder Traversal  后序遍历  

