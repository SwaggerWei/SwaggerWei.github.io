# 刷题学习9-java 图 知识点graph




## 树 和 图 辨析
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb9ab0905a7b43968f43b64aa4e721ae.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 树的父节点和子节点之间是一条路单向可达  
* 图的的节点之间存在多条路可达

## 基本概念
![在这里插入图片描述](https://img-blog.csdnimg.cn/b5edffc6b96548049557711a515d5715.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 顶点  
* 边  
* 邻居节点：只有一条边连接的顶点  
* 度（degree）：一个顶点有几条边，就有几度  
 
## 图的区分
* 无向图：边没有方向  
* 有向图  
* 权重图

## 有向图的基本概念
* 入度：多少条边指向该顶点  
* 出度：多少条边从这个顶点出发指向其他顶点  

### _EG：_
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c216c5784b345ce8a7ea3aa57f19ba7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 韩：入度为0；出度为2  
* 李：入度为1；出度为1  
* bishi：入度为2；出度为0  
* 饲：入度为1；出度为1

## 权重图的基本概念
* 边上有权重  
* EG：顶点为城市，边为城市之间的距离，最短路径问题
![在这里插入图片描述](https://img-blog.csdnimg.cn/1e5070c31b474283997b8371f66400bb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 最短路径常用算法
* 贝尔曼-福特算法（Bellman Ford）  
* 狄杰斯特拉算法（Dijkstra）  
* DFS   
* BFS 


