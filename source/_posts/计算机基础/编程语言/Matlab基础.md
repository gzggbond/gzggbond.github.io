---
title: Matlab基础
date: 2025-03-27 15:27:50
excerpt: 本文对于Matlab的基本用法做了系统性总结，持续更新
tags:
  - 编程语言
categories:
  - [计算机基础,编程语言]
---

# 基本命令

```matlab
% 清空命令行
clc 
% 清除工作区存储的变量【不输入变量名时删除所有变量】
clear 变量名
% 添加当前文件夹下的data入路径，添加后此路径下的内容可以直接访问
addpath(genpath('data'));
```

# 通用函数

```matlab
% 输出字符对应的ASKII码
abs()
% 输出ASKII码对应的字符
char()
% 返回字符串长度
length()
```

# 矩阵

```matlab
% 利用[]包围矩阵内容，;换行，空格是元素分隔符
% 如下👇定义了一个2行3列的矩阵
A = [1 2 3;4 5 6]
% 矩阵转置
A'
% 矩阵按列拼接为向量
A(:)
% 矩阵求逆
inv()
% 矩阵对应位置相加【-，.*，./同理】
A+B
% 普通矩阵乘法【保证内标相同】
A*B
% A*B的逆
A / B
% 符合condition的所有下标
% 如下表示A矩阵大于3的元素下标集合，n是行下表集合，n是列下标集合
[n,m]=find(A>3)
```

## 初始化

> 此处仅是入门用法，具体函数参数可在需要使用时进行查阅

```matlab
% 生成n行m列的全0矩阵,ones类似
zeros(n,m)
% 生成n行m列均匀分布的伪随机数
rand(n,m)
% 生成n行m列标准正态分布的伪随机数
randn(n,m)
% 生成n行m列均匀分布的伪随机整数
randi(n,m)
% 生成n行n列的单位矩阵
eye(n)
% 生成n行n列的幻方矩阵【横，竖，斜相加和为同一个值】
magic(n)
% 生成[start,end]，步长为step的向量
b = start:step:end
# 将b按行重复3次后，整体按列重复2次
c = repmat(b,3,2)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202306251712488.png" alt="image-20230625171204784" style="zoom:67%;" />

# 元胞数组

> 元胞数组可以理解为矩阵集合，每个数组元素可以指向一个矩阵，**索引从1开始**

```matlab
% 生成1行6列的元胞数组
A = cell(1,6)
% 取出数组第一个元素【()取出来的是cell，中括号取出来的是值】
A{1}
```

# 结构体

> 类似`Python`中的字典

```matlab
# 生成1*2个结构体，price共有，name分为两份
book = struct('name',{'mike','jerry'},'price',[30,40])
# 得到第1个结构体的name属性
book(1).name
```

# 常用命令组合

## 1. 判断文件是否存在

```matlab
% 指定文件名
file_name = 'sum_g_mat/class5/user03_fluorescent.mat';
% 使用 exist 函数判断文件是否存在
% 在 sum_g_mat/class5 文件夹下的 user03_fluorescent.mat 文件是否存在
file_exists = exist(file_name, 'file');
% 判断文件是否存在
if file_exists == 2
    disp('文件存在');
else
    disp('文件不存在');
end
```

## 2. 加载.mat文件

```matlab
% 加载 sum_g_mat/class2/xxx.mat 文件
load(['sum_g_mat/class2/','xxx','.mat']);
% 得到 g 的大小
size(g);
```

## 3. 切片访问

```matlab
% end是关键字，指向最后一个位置索引
% 下标从 1 开始且不能省略和使用负数
% 如下是访问二维数组第 1 行倒数第 2 个元素
a(1,1:end-1)
```

