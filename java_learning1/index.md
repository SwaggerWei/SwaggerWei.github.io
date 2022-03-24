# Java 学习-前期准备


## 平台区分
* 这里指不同的操作系统：**_macos windows linux_**

## 跨平台运行原理
* java 语言要想在不同的平台运行， 只需要该平台上有翻译java语言的虚拟机即可  
* 称为 JVM （Java Virtual Machine）  
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e76cd9dd5ec4f20b6c6cf48ead2a60b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## JRE 和 JDK
### JRE（Java Runtime Environment）
* 是Java运行时的环境，包含JVM和运行时所需要的核心类库  
* 运行一个已有的java程序只需要安装JRE即可
### JDK（Java Development Kit）
* Java程序开发的工具包， 包含JRE和开发人员使用的工具  
* 其中的开发工具：编译工具（javac.exe), 运行工具（java.exe）

## 总结：JDK、JRE、JVM之间的关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/ab8296171a8a40e086403b6fae7d62b2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
* 实际上运行java 只需要一个***JDK***就可以了

## 本机JDK安装
![在这里插入图片描述](https://img-blog.csdnimg.cn/c09696d1110d451db7be00b4b395453b.png#pic_center)

## Java JDK 安装目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/33da62e8c4fd459b80ce71f022cb6731.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## IDEA HelloWorld 案例操作步骤
![在这里插入图片描述](https://img-blog.csdnimg.cn/716f30c1d6a04c7988975b8be5817645.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## IDEA 的项目结构
* **项目(Project) > 模块(Module) > 包(Package) > java文件**
![在这里插入图片描述](https://img-blog.csdnimg.cn/21ae19787b7d4dbbb7bfef54e7e2eef0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 对应到这个项目当中  
![在这里插入图片描述](https://img-blog.csdnimg.cn/69d5d54a2b6840338e82958efd5803e7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

* 项目(Project)----java_learning  
* 模块(Module)-----idea_test  
* 包(Package)------swagger_test  
* java文件---------HelloWorld.java

## 内容辅助键
### 快速生成语句
* 快速生成main方法：psvm，回车  
* 快速生成输出语句：sout，回车
### 内容辅助键 
* Ctrl+alt+space----内容提示，代码补全

## 快捷键
### 注释
* command+/
### 格式化
* command+options+L

## IDEA模块操作
* 新建模块
* 删除模块
* 导入模块




