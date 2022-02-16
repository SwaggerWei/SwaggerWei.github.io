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

