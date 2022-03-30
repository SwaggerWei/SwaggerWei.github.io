# Hugo 使用教程


## 下载Hugo
### 使用 brew 来安装hugo  
`brew install hugo`
![](/images_hugo/pic1.png)

### 新建一个hugo 项目  
`hugo new site mysite`  
![](/images_hugo/pic2.png)

### hugo 项目文件结构介绍  
* `content` 博客内容  
* `static` 图片  
* `theme` 主题  

## 主题的配置
### 主题下载  
* 进入[gohugo.io](https://github.com/dillonzq/LoveIt.git)  
* 搜索一个主题下载到项目中的theme 文件夹之中

### 主题的配置
* 进入下载主题的说明文档 [https://hugoloveit.com/zh-cn/theme-documentation-basics/](https://hugoloveit.com/zh-cn/theme-documentation-basics/)
* 复制基本配置，并覆盖项目根目录的config.toml 文件
![](/images_hugo/pic3.png)

### 创建第一篇博客
* `hugo new posts/first_post.md`

### 在本地启动网站
* `hugo serve --buildDrafts`  
_这里是 --buildDrafts 指文档的草稿属性为真，就需要此参数，或者直接将对应markdown文件中的draft属性设置为false_

### 网站高阶参数配置
* 复制   
[https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration](https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration)  
中的config.toml 文件补充到本地的对应文件当中去，注意有几个参数是重复的，
![](/images_hugo/pic4.png)

  
## 部署到github.io 服务器

### github仓库新建
* 新建一个项目，名字为： `github账号名.github.io`

### 本地对接远程服务器地址
* 命令
```shell
hugo --theme=LoveIt --baseUrl="https://swaggerwei.github.io/" -D
```
可以发现本地生成了一个public文件夹  
![](/images_hugo/pic5.png)
* 命令 
```shell
cd public
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:SwaggerWei/SwaggerWei.github.io.git 
git push -u origin master 
```
_tips: init 和 remote add 操作后续更改博客不需要，只有第一次建仓库的时候需要_

### 网页进入个人主页
* [swaggerwei.github.io]()  
_tips：一般过五分钟才行_    
![](/images_hugo/pic6.png)



