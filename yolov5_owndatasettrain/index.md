# 3-如何制作和训练自己的数据集（YoloV5）



## 标注方法
* 自己获取的数据集，人工进行标注（全人工）  
* 自己获取的数据集，首先使用训练好的网络标注一遍，然后手动进行微调（半人工）  
* 仿真数据集（GAN网络来生成自己的数据集-- synthetic data）  

## 标注工具
* CVAT（需要安装）  
* makesense.ai（在线标注工具）

## makesense.ai 标注过程
* 输入图片  
![在这里插入图片描述](https://img-blog.csdnimg.cn/11548f73fdde4c4aabfba53d8a12d46a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 选择Object Detection  
![在这里插入图片描述](https://img-blog.csdnimg.cn/88291f638f37438aa8ea90d01abc1d7a.png#pic_center)

* 创建自己的分类   
* 根据自己的分类开始进行标注（标注的时候也可以使用action中的load models进行半人工标注）  
![在这里插入图片描述](https://img-blog.csdnimg.cn/53b01c6b31a24f3fa3cd3de00f556d35.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 导出文件（action-》export annotation-》yolo format）

## 文件组织过程
* 将images和labels分别放入对应的文件夹中  
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb691ac7b64c4749824bfd05d95a5684.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 参照coco.yaml， 写自己数据集的配置  
```yaml
# train and val data as 1) directory: path/images/, 2) file: path/images.txt, or 3) list: [path1/images/, path2/images/]
train: mydata/images/train
val: mydata/images/train

# number of classes
nc: 3

# class names
names: [ 'person', 'car', 'bike' ]
```
* 修改train.py中的`--data`参数
```python
parser.add_argument('--data', type=str, default='mydata/mydata.yaml', help='data.yaml path')
```
* 运行train.py开始训练  
![在这里插入图片描述](https://img-blog.csdnimg.cn/da1059a78c3b43828b153c556b291426.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 使用detect测试自己训练的模型  
修改detect 中的权重文件为自己训练的模型（一般问train当中最新的那个exp）
![在这里插入图片描述](https://img-blog.csdnimg.cn/7c7f30e03eba429c877445464d18a8c4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## tips 
* pycharm左侧工程目录文件个数太多了会占用大量的资源（需要检索）  
* 可以右键文件夹，在此处选择exclude， 则可以节省资源  
![在这里插入图片描述](https://img-blog.csdnimg.cn/c84d12842f3c4107a8d9ea1cdd081aaa.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 需要有路径的地方，只要路径正确， exclude的文件依然可以找到，知识在目录视图中找不到
