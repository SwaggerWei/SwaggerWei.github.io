# Latex 语法学习笔记2


## 算法加入
* 在tex 文件开头引用以下包：  
**algorithm、algorithmics、algpseudocode、amsmath**   
```buildoutcfg
\usepackage{algorithm}
\usepackage{algorithmicx}
\usepackage{algpseudocode}
\usepackage{amsmath}
```
f
* 如果算法中包含输入输出模块，还应加入如下代码：  
```buildoutcfg
\renewcommand{\algorithmicrequire}{\textbf{Input:}}  % Use Input in the format of Algorithm
\renewcommand{\algorithmicensure}{\textbf{Output:}} % Use Output in the format of Algorithm
```

* 算法主体  
```buildoutcfg
\begin{algorithm}[thpb]
	\caption{algorithm name}
	\label{algorithmlabel}
	\begin{algorithmic}[1]
		\Require
		algorithm input
		\Ensure
		algorithm output 
	    \State algorithm content1;
		\While{while loop}
		\State algorithm content2;\EndWhile \\
		\Return algorithm output.
	\end{algorithmic}
\end{algorithm}
```
* 完成之后呈现为这种状态   
![在这里插入图片描述](https://img-blog.csdnimg.cn/43c89f03543d4693b833c56f68448060.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_16,color_FFFFFF,t_70,g_se,x_16#pic_center)

**_注意_**：   
* 在while之后不要忘记endwhile  
* while内部的代码会自动缩近

## 各种文件
`.bib` 存放参考文献  
`.tex` 文献源码  
`.bst` 参考文献风格，与`.bib`组合使用  
`.sty` 风格文件


