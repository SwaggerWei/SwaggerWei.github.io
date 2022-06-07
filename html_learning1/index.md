# HTML5 学习1


## HTML 概述
* Hyper Text Markup Language（超文本标记语言）
* 包括文字、图片、音频、视频、动画等
* 目前最新的为HTML5 
* 主流浏览器基本都支持、跨平台、市场的需求

### W3C
* World Wide Web Consortium （万维网联盟）
* 中立性技术标准机构

### W3C标准包括
* **结构**化标准语言（HTML、XML）
* **表现**标准语言（CSS）
* **行为**标准（DOM、ECMAScript）

### 基本结构
![](/image_HTML/pic1.png)
* `<body> 和 </body>` 等成对的标签分别为**开放标签和闭合标签**，一个用来打开，一个用来闭合
* `<meta charset="UTF-8">`不成对出现的即为**自闭合标签**

## 网页的基本信息
* 注释：`<!-- -->`
* `<!DOCTYPE html>`: 告诉浏览器，使用html规范（实际默认标准也是html）
* `<meta charset="UTF-8">`：描述字符编码类型
* `<title>我的第一个网页</title>`: 网站标题
* body描述网页主体

### 基本内容代码实战
```html
<!--告诉浏览器使用的标准为html规范-->
<!DOCTYPE html>

<html lang="en">

<!--head表示网页的头部-->
<head>
<!--    meta 描述性标签，用来描述网页的信息-->
<!--    meta一般用来做SEO-->
    <meta charset="UTF-8">
    <meta name="keywords" content="魏博言">
    <meta name="description" content="魏博言的第一个html的第一个测试文件">

<!--    网页标题-->
    <title>我的第一个网页</title>
</head>

<!--网页主体-->
<body>
hello world！
</body>
</html>
```

## 网页基本标签
* 标题标签
* 段落标签
* 换行标签
* 水平线标签
* 字体样式标签
* 注释和特殊符号

### 代码实战
```html
<!--测试基本标签-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>基本标签学习</title>
</head>
<body>

<!--1 标题标签-->
<h1>一级标签</h1>
<h2>二级标签</h2>
<h3>三级标签</h3>
<h4>四级标签</h4>
<h5>五级标签</h5>
<h6>六级标签</h6>

<!--段落标签-->
<p>第一段内容：反射大范德萨发生的范德萨范德萨范德萨范德萨发生大</p>
<p>第二段内容：放大反射大放送范德萨范德萨发生</p>
<p>第三段内容：放大范德萨范德萨范德萨范德萨范德萨范德萨</p>

<!--水平线标签-->
<hr>

<!--换行标签-->
反射大范德萨范德萨范德萨发生大 <br>
范德萨范德萨范德萨范德萨范德萨范德萨范德萨 <br>
发反射大范德萨范德萨范德萨范德萨 <br>

<!--字体样式标签-->
<h1>字体样式标签</h1>
粗体标签：<strong>I love you</strong> <br>
斜体标签：<em>I love you</em> <br>

<!--特殊符号-->
<h1>多个空格-特殊符号</h1>
空    格 <br>
空&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;格 <br>

&gt; <br>
&lt; <br>
&copy; 版权所有：魏博言<br>

<!--
特殊符号记忆方式：
& ；
查百度
-->


</body>
</html>
```

## 图像标签
* 常见的图像格式：JPG、GIF、PNG、BMP
* 嵌入图片的格式： `<img src="path" alt="text" title="text" width="x" height="y">` alt为图像替代文字，title为鼠标悬停的文字

### 代码实战
```html

<!--图像标签测试-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图像标签测试</title>
</head>
<body>

<img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">

</body>
</html>
```


## 链接标签
* 文本超链接
* 图像超链接
* 格式：`<a href="path" target="目标窗口位置">链接文本或图像</a>`

### 代码实战
```html
<!--链接标签-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>链接标签</title>
</head>
<body>

<!--使用id作为标记-->
<a id="top">顶部</a>

<br>


<!--文本超链接-->
<a href="https://www.baidu.com">点击我跳转到百度</a> <br>
<a href="page1.html">点击我跳转page1</a> <br>


<!--图片超链接-->
<a href="page1.html">
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</a>

<br>

<!--target 表示窗口在哪里打开-->
<a href="page1.html" target="_blank">点击我跳转page1-新标签中打开</a>
<a href="page1.html" target="_self">点击我跳转page1-本标签中打开</a>


<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>
<p>
    <img src="../resource/image/pic1.png" alt="图片1" title="悬停文字" width="300" height="200">
</p>

<!--
锚链接
    1 需要一个锚标记
    2 跳转到标记
    # 表示
-->

<!--锚链接-->
<!--也可以从另一个页面跳到指定位置-->
<a href="#top">回到顶部</a>

<a id="down"></a>

<br>

<!--功能性链接-->
<!--邮件链接
    mailto
-->

<a href="mailto:785403310@qq.com">点击联系我</a>


<!--功能性链接 qq推广链接-->

<a target="_blank" href="http://wpa.qq.com/msgrd?v=3&uin=&site=qq&menu=yes"><img border="0" src="http://wpa.qq.com/pa?p=2::52" alt="点击联系我qq交谈" title="点击联系我qq交谈"/></a>

</body>
</html>
```






