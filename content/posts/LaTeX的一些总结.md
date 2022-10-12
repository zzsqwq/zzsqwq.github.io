---
title: "LaTeX的一些总结"
categories: [ "LaTeX" ]
tags: [ "LaTeX" ]
draft: false
slug: "10"
date: "2020-02-04 22:47:00"
---

### 希腊字母表

![xila.png][1]

<!--more-->

****



### 一些技巧和特殊符号

+ 上标：num_i  ->  $num_i$

+ 下标：e^x ->  $e^x$  （如果下标或上标不明显，可嵌套多层来达到目的）
  
  + 上下标是一串字符的话可用{}括起来表示
  
+ 根号：\sqrt[3]{x} -> $\sqrt[3]{x}$

+ 省略号： 在下面\dots -> $\dots$     在中间\cdots ->  $\cdots$

+ 方框:  \boxed{example}  ->  $\boxed{example}$   (还有一个\fbox与此类似  \fobx{example} -> $\fbox{example}$) 

+ 字体加粗:  \mathbf{example} -> $\mathbf{example}$ 

+ 字体斜体且加粗:  \boldsymbol{example}  ->  $\boldsymbol{example}$

+ 插入普通文本（自适应大小）:  \text{测试}  -> $\text{测试}$  

  ![specialsign.png][2]
  ![specialsign2.png][3]

***

### 一些基本符号

+ 求和:  \sum_1^n  ->  $\sum_1^n$ 

+ 积分:  \int_1^n -> $\int_1^n$   \iint -> $\iint$  以此类推$\cdots$

+ 极限:  \lim\_{x \to +\infty}  -> $\lim_{x \to +\infty}$

+ 分数:  \frac{1}{2} -> $\frac{1}{2}$   如果要写多层分数可以用\cfrac （可以避免字母逐层缩小的限制）

+ 组合数: \binom{5}{2} -> $\binom{5}{2}$

+ 下取整: \lfloor{x}\rfloor ->  $\lfloor{x}\rfloor$

+ 下取整: \lceil{x}\rceil -> $\lceil{x}\rceil$

  ![qita.png][4]

  ![qita2.png][5]

***



### 关于矩阵和行列式

+ 矩阵：用法如下，元素中间使用&来分割同行元素，用\\\来换行

```latex
\begin{matrix}
	1 & 2 & 3\\
	4 & 5 & 6\\
	7 & 8 & 9\\
\end{matrix}
```


$$
\begin{matrix}
	1 & 2 & 3\\
	4 & 5 & 6\\
	7 & 8 & 9\\
\end{matrix}
$$

+ 行列式: 与矩阵相似，加上行列式的名字以及左右分割线即可

```latex
A=  \left|
	\begin{matrix}
	1 & 2 & 3 \\
	4 & 5 & 6 \\
	7 & 8 & 9 \\
	\end{matrix}
	\right|
```


$$
A=  \left|
	\begin{matrix}
	1 & 2 & 3 \\
	4 & 5 & 6 \\
	7 & 8 & 9 \\
	\end{matrix}
	\right|
$$



***

### 分段函数和方程组

+ 分段函数：写法如下，每一个条件用 表达式和条件之间用 **&** 连接

```latex
f(x)=
	\begin{cases}
		x/2, & {n>2}\\
		2x , & {n=2}\\
		3x , & {n<2}\\
	\end{cases}
```


$$
f(x)=
	\begin{cases}
		x/2, & {n>2}\\
		2x , & {n=2}\\
		3x , & {n<2}\\
	\end{cases}
$$

+ 方程组: 写法如下，不是一个对称的，注意left后面为{ ，right后面为. 用\\\换行

```latex
\left\{
	\begin{array}{}
		a_1x+b_1y+c_1z=d_1\\
		a_2x+b_2y+c_2z=d_2\\
		a_3x+b_3y+c_3z=d_3
	\end{array}
\right.
```


$$
\left\{
	\begin{array}{}
		a_1x+b_1y+c_1z=d_1\\
		a_2x+b_2y+c_2z=d_2\\
		a_3x+b_3y+c_3z=d_3
	\end{array}
\right.
$$

***



**Ps: 上面的矩阵，行列式，分段函数和方程组有一个问题需要我们注意，因为反斜杠 \\ 需要转义，那么对于每次换行需要两个\\\\ ，也就是说你打的时候总需要打四个，对于前面声明begin和end，或者left和right前面的反斜杠，你打的时候就要打两个，对于markdown编辑器里面编辑LaTeX可能会自适应，不会自动转义，但是我们推博客的时候，一定要注意这个地方，不然会显示错误。也就是对于上述描述中的反斜杠，都要按两倍来写**

**2020.8.17更新**

+ 对于Hexo搭建的博客，用mathjax渲染，需要上述的规则。如果博客用的是typecho系统并且LaTeX插件为MardownKatex，那么不需要注意上述规则。（其他的系统我没有试过，暂且不谈）



***



先总结这么多，后面那些进阶的用到了再总结。



参考文献:

+ [Typora中利用LaTeX 插入数学公式](https://blog.csdn.net/happyday_d/article/details/83715440)
+ [LATEX 公式总结](https://www.jianshu.com/p/22117d964baf)


  [1]: https://blog.zzsqwq.cn/usr/uploads/2020/08/248320705.png
  [2]: https://blog.zzsqwq.cn/usr/uploads/2020/08/935141993.png
  [3]: https://blog.zzsqwq.cn/usr/uploads/2020/08/1186889784.png
  [4]: https://blog.zzsqwq.cn/usr/uploads/2020/08/2438601090.png
  [5]: https://blog.zzsqwq.cn/usr/uploads/2020/08/303386698.png