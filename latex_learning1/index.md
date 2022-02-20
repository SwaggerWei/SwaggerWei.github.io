# Latex 语法学习笔记1


## overleaf 使用
* overleaf 左下角为文章结构  
![在这里插入图片描述](https://img-blog.csdnimg.cn/7f8863e213eb468f999c03cdc710bf67.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAU3R1X2FydA==,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)

**方便快速定位到tex代码文件的位置**

## 图片的插入与引用
* 插入
```buildoutcfg
\begin{figure}[t]
	\centering
	\includegraphics[scale=0.25]{intro.png}
	\caption{描述}
	\label{pic-label}
	\vspace{-0.4cm}
\end{figure}
```
`\centering` 放置位置剧中  
`\includegraphics[scale=0.25]{intro.png}` 图片名称（同一目录下）及大小  
`\caption{描述}`图片下方的描述  
`\label{pic-label}`图片的引用  

* 引用 
```buildoutcfg
Fig. \ref{pic-label}
```
_此处的pic-label 即为图片插入时的图片引用_

## 文献的插入与引用
* 首先bib文件中加入此格式的代码
```buildoutcfg
@article{cosio2004autonomous,
  title={Autonomous robot navigation using adaptive potential fields},
  author={Cos{\'\i}o, F Arambula and Casta{\~n}eda, MA Padilla},
  journal={Mathematical and Computer Modelling},
  volume={40},
  number={9-10},
  pages={1141--1156},
  year={2004}
}
```
* 在tex文件中加入对bib文件和bst文献的支持
```buildoutcfg
\bibliographystyle{IEEEtranS} 
\bibliography{IEEEexample}
```
* 最后在tex文件中引用该文献， 此处引用label为 **cosio2004autonomous**
```buildoutcfg
~\cite{cosio2004autonomous}
```

