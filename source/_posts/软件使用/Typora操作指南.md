---
title: Typora操作指南
date: 2024-04-19 14:27:23
excerpt: Typora是一款非常好用的Markdown编辑器，本文对其使用技巧做了总结
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 常用快捷键 #

```java
// 快捷键可自定义，具体操作方式可在CSDN搜索，此处为默认快捷键
// 加粗
Ctrl + B	
// 倾斜
Ctrl + I		
// 高亮
Ctrl + Shift + H	
// 添加超链接
Ctrl + K		
// 添加表格
Ctrl + T		
// 进入数学公式模式
Ctrl + Shift + M	
// 文档内查找数据
Ctrl + F
```

# 1. 文字 #

```html
<!--改变文字对齐方式-->
<p align="center/left/right">文字</p>   
<!--改变字体样式-->
<font size=大小 color="颜色" face="字体">文字</font>  
<!--加粗，斜体-->
<b></b><i></i>
```

==高亮==，利用`==高亮文字==`实现

# 2. 目录 #

输入`[toc]`自动根据标题生成目录

## 2.1 页面内部跳转

> 有时候内容比较长，很后边的内容有可能会涉及到前面输入的图片之类，如果总是滑上滑下不是很方便，这时候就需要使用到内部页面跳转功能

```html
<!-- 只需要进行这样简单设置，接下来按住Ctrl，鼠标左键点击"点我跳转到图片1"就可以来到图片1的位置 -->
<a name="p1">图片1</a>
<a href="#p1">点我跳转到图片1</a>
```

# 3. 数学公式代码插入 #

[数学公式网站](https://www.cnblogs.com/Xuxiaokang/p/15654336.html#%E5%A6%82%E4%BD%95%E8%BE%93%E5%85%A5%E6%8B%AC%E5%8F%B7%E5%92%8C%E5%88%86%E9%9A%94%E7%AC%A6)

> 普通代码【如`java`】，只需输入````java`进行选择即可

①输入`$数学公式$`包围住数学公式；②快捷键`ctrl+shitf+M`。

<font color="red">所有数学表示都要在`$ $`里才能发挥作用，`{}`在`$`里代替小括号实际作用</font>

**如果一点`Latex`代码都不想学，可以直接来到[在线Latex编辑器](https://www.latexlive.com/home)寻找自己需要查找的符号代码【上传图片也行】，接着赋值进`MarkDown`编辑器中即可**

下边都是常用的一些基础语法👇

## 3.1 上标下标 ##

$x^2$，上标利用 **`^`** 实现  ；$x_2$，下标利用 **`_`** 实现

## 3.2 根号 ##

$\sqrt{2}$，根号利用 **`\sqrt{被开方数}`** 实现；$\sqrt[3]{4}$，多少次根利用  **`\sqrt[多少次根]{被开方数}`** 实现

## 3.3 上下水平线 ##

$\underline{a+b}$，下水平线用 **`\underline{被划线部分}`** 实现

$\overline{a+b}$，上水平线用 **`\overline{被划线部分}`** 实现

## 3.4 水平方向大括号 ##

$\overbrace{x+y}^{我是说明}$

水平方向上大括号利用 **`\overbrace{被括起来的内容}`** ，若要对括起来的内容进行说明，可**在{被括起来的内容}后添加 ^{说明内容} 实现**

$\underbrace{x+y}_{我是说明}$，

水平方向下大括号利用 **`\underbrace{被括起来的内容}`** ，若要对括起来的内容进行说明，可**在{被括起来的内容}后添加 _{说明内容} 实现**

## 3.5 向量 ##

$\overrightarrow{AB}$，实现**右**方向的向量通过 **`\overrightarrow{AB}`** 实现

$\overleftarrow{AB}$，实现**左**方向的向量通过 **`\overleftarrow{AB}`** 实现

## 3.6 分数 ##

$\frac{\sqrt{3}}{2}$，分数利用 **`\frac{分子}{分母}`**实现

$\dfrac{\sqrt{3}}{2}$，分数利用 **`\dfrac{分子}{分母}`**使分数看上去更大一些

## 3.7 积分 ##

$\int$ ，积分符号用 **`\int`** 实现；

$\int_{0}^{1}$，积分上下限利用 **`\int_{下限}^{上限}`** 实现

## 3.8 求和 ##

$\sum{}$ ，求和符号用 **`\sum{式子}`** 实现

$\sum_{0}^{k}$，求和上下限用 **`\sum_{下限}^{上限}{式子}`** 实现

## 3.9 连乘 ##

$\prod$，连乘符号用 **`\prod{式子}`** 实现

$\prod_{0}^{k}$，连乘上下限用 **`\prod_{下限}^{上限}{式子}`** 实现

## 3.10 极限 ##

$\lim\limits_{x\rightarrow\infty}$，**`\lim\limits_{极限下标}`**

## 3.11 特殊符号 ##

$\rightarrow$，右箭头**`\rightarrow`**；  $\infty$，无穷**`\infty`**；$\in$，属于**\in**

## 3.12 希腊字母 ##

$\alpha$：**\alpha**  ；$\beta$：**\beta** ；$\gamma$：**\gamma** ；$\theta$：**\theta** ；$\rho$：**\rho** ；$\lambda$：**\lambda** ；$\mu$：**\mu** <br>$\Delta$：**\Delta** ；$\pi$：**\pi** ；$\Omega$：**\Omega**

[更多希腊字母](https://blog.csdn.net/Krone_/article/details/99710062?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522160905456416780277818200%252522%25252C%252522scm%252522%25253A%25252220140713.130102334.pc%25255Fall.%252522%25257D&request_id=160905456416780277818200&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-2-99710062.pc_search_result_cache&utm_term=markdowm%E5%B8%8C%E8%85%8A%E5%AD%97%E7%AC%A6)

## 3.13 运算符 ##

$\ge$：**\ge** ；$\le$：**\le** ；$\ne$：**\ne** 

$\times$：**\times** ；$\div$：**\div**

[更多数学符号使用](https://blog.csdn.net/LB_yifeng/article/details/83302697?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)

## 3.14 矩阵 ##

### 无边框 ###

$$
\begin{matrix} 1 & 2 & 3\\ 4 & 5 & 6\\ \end{matrix}
$$

**`\begin{matrix}`**：表示矩阵开始<br>**`&`**：分隔元素<br>**`\`**：换行<br>**`\end{matrix}`**：表示矩阵结束

### 有边框 ###

$$
\left[\begin{matrix} 1 & 2 & 3\\ 4 & 5 & 6\\ \end{matrix}\right]
$$

**`\left[`**：表示左边框，边框类型为[，写在最开始

**`\right]`**：右边框，边框类型为]，写在最后

### 行列式 ###

$$
\left|\begin{matrix} 1 & 2 & 3\\ 4 & 5 & 6\\ \end{matrix}\right|
$$

**`\left|`**：表示左边框，边框类型为`|`，写在最开始

**`\right|`**：右边框，边框类型为`|`，写在最后

## 3.15 方程组 ##

$$
\begin{equation} \left\{             
\begin{array}{lr}             
x=\dfrac{3\pi}{2}(1+2t)\cos(\dfrac{3\pi}{2}(1+2t)), &  \\             
y=s, & 0\leq s\leq L,|t|\leq1.\\             
z=\dfrac{3\pi}{2}(1+2t)\sin(\dfrac{3\pi}{2}(1+2t)), &               
\end{array} 
\right. 
\end{equation}
$$

`\begin{equation}`  **//方程组开始的标志** <br>`\left\\{`  **//方程组固定格式，注意是两条杠，括号表示左边的大括号**<br> `\begin{array}{lr}`  //**{array}部分表示方程组由数组构成，{lr}表示有两列，第一列左对齐(即式子)，									第二列右对齐(即取值范围，不过例子中取值范围只有一条，看不出左右)<br>**`x=\dfrac{3\pi}{2}(1+2t)\cos(\dfrac{3\pi}{2}(1+2t)), &  \\\`             <br>`y=s, & 0\leq s\leq L,|t|\leq1.\\\`  **//&表示换一列(如式子到取值范围)；\\\表示换一行**           <br>`z=\dfrac{3\pi}{2}(1+2t)\sin(\dfrac{3\pi}{2}(1+2t)), & `              <br>`\end{array}` **//数组结束标志**<br>`\right.` **//方程组固定格式，英文句号不要忘记**<br>`\end{equation}` **//方程组结束的标志**

# 4. emoji表情插入

基本形式是`:表情对应单词`，如👇

:smile:	`:smile`

[更多emoji表情具体单词](https://blog.csdn.net/qq_43630810/article/details/109108879)

