# 1-yoloV5 本地训练 - m1 Mac Air（仅仅是为了调通代码）


## pycharm 文件目录展开折叠管理  
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca680f32a3bb4578be6699efeef44aa8.png#pic_center)

* 打开已打开文件的目录  
* 展开所有目录  
* 折叠所有目录  

## number of workers 设置
![在这里插入图片描述](https://img-blog.csdnimg.cn/7a019e3405a745f68b0884ccfa380f5a.png#pic_center)

* **此项为dataloader的载入数据的线程数量**  
* 初始运行的时候最好设置成0，由于不同机器的性能不同，数目大了很容易出问题

## 导入nnpack 错误，m1 Mac 硬件不支持
![在这里插入图片描述](https://img-blog.csdnimg.cn/8472eb000e6e4ebfac2b8f9cb0c5c3c4.png#pic_center)

* 在运行detect.py文件时同样报错，应该没有影响

## 缺失coco 数据集错误
![在这里插入图片描述](https://img-blog.csdnimg.cn/451f6d352d43408a83bf56ffafe6013c.png#pic_center)

* 本地缺失coco128数据集 但是联网之后无法在该网址下载coco128 所有复制链接手动下载

* 最后复制到指定文件夹 完成后问题解决 开始训练
![在这里插入图片描述](https://img-blog.csdnimg.cn/0906296b18a7423396ee2d2d0116f01f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)





