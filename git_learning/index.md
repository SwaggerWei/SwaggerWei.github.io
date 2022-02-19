# git 代码管理工具1



## 应用场景
* 团队协作开发， 避免频繁来回拷贝代码

##  git 工作流程
* 克隆 Git 资源作为工作目录
* 在本地的工作副本上做修改操作
* 如果别人更改了资源可以更新资源
* 检查修改之后就可以向资源库推送更新
* 如果发现有错误则可以撤销推送，修改错误，然后重新推送  
![在这里插入图片描述](https://img-blog.csdnimg.cn/e8878680beb5409ca4882b253e8f841f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 工作区和、暂存区和版本库
* 工作区，就是本地电脑上可以看到的文件(按下`Command+Shift+. `可以显示隐藏文件、文件夹，再按一次，恢复隐藏；)    
![在这里插入图片描述](https://img-blog.csdnimg.cn/3825b1e9d28e43bda476f7faf8a1d563.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 暂存区，英文stage或者index，一般存放在 .git 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）
* 版本库：工作区有一个隐藏目录 .git，这个不算工作区，而是 Git 的版本库。    
![在这里插入图片描述](https://img-blog.csdnimg.cn/39a18d8868ed4144b9c67d3f6de65d2c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 基本操作命令
* 当前基本状态 `git status`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/c490d29a40c249e1b21ab8a84c645097.png#pic_center)

显示当前的最新的branch； 显示没有被commit的 文件（发现.DS_Store和first文件没有被commit）

* 添加到暂存区 `git add 文件名`   
![在这里插入图片描述](https://img-blog.csdnimg.cn/d1a34ea498394f55a0b77f5764258810.png#pic_center)

`.`为所有文件，再次运行`git status`，发现所有的文件都已经被添加

* 添加到版本库 `git commit -m "版本提交的附带信息"`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/7783983bd0194511a389f7d55fe35ba6.png#pic_center)


* 查看当前的提交和回滚情况 `git log`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/28e0b214f3a54130bfd2b25daac9c4e0.png#pic_center)  
注：为倒叙排序， Initial commit 为默认初始化github 官网创建得到

* 回滚最新的一次commit `git reset commit后跟的ID`  
* 回滚之前发现有一个message 为 second commit 的 commit  
![在这里插入图片描述](https://img-blog.csdnimg.cn/9811bdf1f2484e74b7fcde3db21470c1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 回滚的ID 填入倒数第二个ID， 说明回滚到倒数第二新的状态  
![在这里插入图片描述](https://img-blog.csdnimg.cn/fe3c534bffc34375b92361c97fd35691.png#pic_center)


* 设置邮箱号 `git config --global user.email 785403310@qq.com`
* 设置名字 `git config --global user.name swagger `
* 然后发现log 信息中的commit 信息变成了自己  
![在这里插入图片描述](https://img-blog.csdnimg.cn/6599f94ac84a4fd0a1957dc520abde5f.png#pic_center)


* 推送到github服务器 `git push`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/a58b4a1272fb4bf0ad98a0994614367c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)  
推送之后发现本地创建的文件已经同步到了gitee上








