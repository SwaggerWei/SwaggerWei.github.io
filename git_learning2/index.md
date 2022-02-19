# git 代码管理工具2



## 如何解决冲突
模拟两个人同时修改一个文件并提交到远端

* 克隆到两个文件夹 me 和 other  
`git clone git@gitee.com:swagger_wei/test_gitee_git.git me`  
`git clone git@gitee.com:swagger_wei/test_gitee_git.git other`

* other 对first.txt 内容作出修改    
![在这里插入图片描述](https://img-blog.csdnimg.cn/e08f4f72510b4ac4bc6fab9acd81a13e.png#pic_center)
  

* 推送到远端  
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2c75b553b7b4be29efcff2b61bbefaa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)
    
- 此时另一个 `me` 的本地仓库是不知道远程的仓库已经发生了修改的

- me 对first.txt 内容作出修改  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2ca869c2cfdc44f6926abe40615c721e.png#pic_center)


* 推送到远端  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f55e812efe0d4b1b985a5dcd4e708d72.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
* 发现出错，因为本地 me 仓库 不知道远端仓库已经发生了变换， 所以向远端提交失败

* 拉取远端变化 `git pull`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/692d02e65a454d648c2c78ad4adaa925.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 发现有pull之后有冲突需要解决  
![在这里插入图片描述](https://img-blog.csdnimg.cn/58029bece283421894a40ce43b1e97b7.png#pic_center)

* 商量之后删掉无用部分， 合并冲突  
![在这里插入图片描述](https://img-blog.csdnimg.cn/a61378ecf77545df903f94862f70ee73.png#pic_center)


* 提交到远端  
![在这里插入图片描述](https://img-blog.csdnimg.cn/003ae3a1b0b64ee2a8c851ad7863d6aa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_17,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 发现远端已经提交成功  
![](https://img-blog.csdnimg.cn/fd8b25c276e348aeab972c3f8c29b16b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



