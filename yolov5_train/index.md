# 2-YoloV5 train.py 如何使用



## parser 参数详解
* `--weights` 初始化模型： 有yolov5m，yolov5s，yolov5x，yolov5l，如果为设置为空就是没有任何与训练模型  
* `--config` 训练配置文件：.yaml 文件，与预训练模型相对应。  
* `--data` 所使用的数据集的配置文件：.yaml 文件，训练自己的数据集是也需要编辑相应的yaml文件  
* `--hyp` 超参数配置文件： 学习率等  
* `--epoch` 训练轮数  
* `--batch-size` batchsize 大小  
* `--img-size` 图片大小  
* `--rect` padding之后会有灰色区域的冗余信息，此参数设置为真，可以去除冗余信息  
* `--resume` 从之前训练的节点开始训练（已经训练的网络在`runs/train/exp`文件夹下的pt文件）  
![在这里插入图片描述](https://img-blog.csdnimg.cn/dcee9c3cc42d4c37a161bea23a30660c.png#pic_center)  
发现从此pt权重文件开始继续训练  
* `--nosave` 是否只保留最后一个epoch的权重文件   
* `--notest` 是否只测试最后一个epoch  
* `--noautoanchor`  （目标检测算法中是否使用锚点、锚框）关闭autoanchor  
* `--evolve` 进化超参数（寻找最优参数的一种方式）  
* `--bucket` 没有必要    
* `--cache-images` 缓存图片以备更快的训练  
* `--image-weights` 上一轮中训练情况不好的图片，在下一轮中加一些相关的权重  
* `--device` 训练设备选择 选择cpu或者gpu   
* `--multi-scale` 对图片的尺寸进行变化     
* `--single-cls` 训练的数据集是但类别还是多类别  
* `--adam` 优化方法使用adam 优化法  
* `--sync-bn` DDP参数     
* `--local_rank` DDP参数   
* `--workers` 最好设置成0，不然很容易出错  
* `--poject` 项目存储位置  
* `--entity`  W&B 训练可视化的库是否启用  
* `--name`  训练结果保存名字  
* `--quad` 开启之后在尺寸大于640的图像上识别效果更好，但是有可能会使在640尺寸的图片上效果更差  
* `--Linear-lr` 学习速率的变化，如果开启就使用线性的方式进行变化，不开启就使用余弦退火的方式变化    
* `--label-smoothing`  防止在分了算法中出现过拟合的情况发生  
* `--save_log`  每隔多少个epoch，在tensorboard 中打一个标签




 

