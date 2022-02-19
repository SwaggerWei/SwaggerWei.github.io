# git 代码管理工具3


## 团队协作分支开发模式
一个好的 github 项目一般都有多个分支：master，dev，release分支

- 新建分支 branch  
`git branch branch1`
- 切换到目标分支  
`git checkout branch1`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/e94014b99550452fbc961a5f8faba069.png#pic_center)    
* 在本地的branch1 中添加一个helloworld.java 文件
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello world");
        System.out.println("1");
        System.out.println("hello world");
        System.out.println("2");
    }
}
```

* 推送到远端 `git push --set-upstream origin branch1`
然后远端就有了master 和 branch1 两个分支了， 其中branch1 中才有 java文件

* 同理，在另一个人的主机上， 创建branch2， 并写自己的HelloWorld.java文件
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("1");
        System.out.println("hello world");
        System.out.println("2");
        System.out.println("hello world");
    }
}
```

* 分支的合并发布 首先切换到master分支 `git checkout master`
* 拉一下最新的远端分支情况 `git pull`
![在这里插入图片描述](https://img-blog.csdnimg.cn/efaacd469dad47b9932c2ee8ff9ba34c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


* 将branch1 分支的内容合并到master 分支上 `git merge brunch1`  
* 推到远端 `git push`  
![在这里插入图片描述](https://img-blog.csdnimg.cn/308dfdd4b37c432a97d27d04f300fb87.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
  
* 发现master 分支 也有branch1 中的java文件了
![在这里插入图片描述](https://img-blog.csdnimg.cn/783152c3ba8a4e9b8f276a2d4a7a89d7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 同理合并 branch2 到 master上， 但是发现合并有冲突， 因为有两个同名的Java 文件， 但是文件内容却不一样  
![在这里插入图片描述](https://img-blog.csdnimg.cn/cc7efcedfc48401b86417f51d67a5c67.png#pic_center)  

![在这里插入图片描述](https://img-blog.csdnimg.cn/ffb5008936f141628d11711722e1fe63.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


## 使用IDEA 快速解决 git merge 当中的冲突

* 右键， 选择git-》resolve conflict
![在这里插入图片描述](https://img-blog.csdnimg.cn/84dc478596c04711a5254f13e9370003.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 双击出现的 conflict 进行代码比对
![在这里插入图片描述](https://img-blog.csdnimg.cn/b55f5f485f3242fd89c6bca4c21b8b79.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
  
* 手动比对之后出现 all changes have been processed 就可以了， 点击右下角apply
然后上传到远端没有出现错误则说明成功
