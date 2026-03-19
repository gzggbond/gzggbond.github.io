---
title: Conda虚拟环境
date: 2023-11-06 14:27:23
excerpt: Conda允许在一台机器上创建多个Python环境，各虚拟环境间相互隔离互不影响，非常适合解决包版本冲突问题
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 1. Python安装需要知道的概念

+ Python 解释器：作用是将代码翻译为计算机能看懂的机器码，从而执行

  > 即所谓的 Python 环境

+ Python 编辑器：编辑代码的工具

  > PyCharm，Jupyter notebook等

+ Python 包管理工具：Python 内置许多开源库，通过包管理工具就能进行安装/卸载以及版本管理

  > 现如今 Python 环境【3.4 之后】已经集成了 pip 进行包管理，不需要额外进行安装管理工具，但是若需要使用多个 Python 环境时则需要考虑安装 Anaconda 进行环境管理

由于 Anaconda 整体过于臃肿【大约 10G 】，集成了市面上常用的大多数库以及编辑工具，而其中大多数库对于普通用户来说可能都用不到，因此官方又推出了轻量级的 Miniconda 【安装包不到 100M 】，其只集成了最基本的 python 解释器，用户可根据需要对相应库进行安装

# 2. Conda

>  Conda 集成了Python解释器和众多Python库，其还能创建虚拟环境以便各种需求，可根据需要选择Anoconda或Miniconda

通常来说，在命令行终端执行的 conda 命令都需要在最前面加 conda ，而在 Anaconda 自带的终端中有些命令可以不需要使用 conda ，不过为了统一，最好都加上，如果命令执行出现错误，则删除 conda 再试一次。

## 2.1 虚拟环境

> Anaconda 可以管理多个环境以应对不同项目对各种库版本的需求，除了原始的 base 环境，其余用户创建的环境都称为虚拟环境

### 搭建

> 虚拟环境默认搭建在 C 盘，若需要修改可参考此博客👉[改变conda虚拟环境的默认路径](https://blog.csdn.net/weixin_44692055/article/details/128548300)

```python
# 创建指定名称虚拟环境，并为此环境指定python版本同时安装此版本下对应的包
# 在指定目录下创建虚拟环境【此处指定了python版本为3.7.7】
conda create -p D:/Program/MiniConda/envs/环境名 python=3.8.3
# 如下例子👇
# 创建一个名为hhh的虚拟环境【安装在默认目录】，该环境python版本为3.7.7，同时为此环境安装numpy包
conda create --name test_env python=3.8.3 numpy 
# 规范的项目会在 requirements.txt 中定义此项目需要的环境，这条语句可以将所有环境一起安装
conda install -r requirements.txt
```

### 激活/退出

> 在命令行中要启用相应的环境需要进行激活，同理，当不需要使用此环境后需要退出

```python
# 使环境变量生效
source ~/.bashrc 
# 激活环境
conda activate 环境名
# 退出当前环境【回到 base 环境】
conda deactivate
```

### 删除/复制

> 环境和创建自然也能删除与复制

```python
# 删除指定环境
conda remove --name 环境名 --all -y
# 复制环境aaa，复制后的样本命名为bbb
conda create --name bbb --clone aaa
```

### 查看

```python
# 查看当前拥有的虚拟环境
conda env list
# 查看当前虚拟环境下安装的包
conda list
# 查看指定虚拟环境安装的包
conda list -n 环境名
```

### 打包本地虚拟环境并迁移

```shell
# 将本地的虚拟环境打包（直接解压至miniconda的envs目录下）
conda pack -n your_env_name -o your_env_name.tar.gz
conda pack -n opencompass -o open_linux_env.tar.gz
# 迁移到Linux
mkdir -p your_env_name
```

## 2.2 安装/卸载包

> 在哪个环境安装包就要确保自己已经在这个环境下

安装包时若出现权限问题，可以用管理员身份打开命令行安装👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307241115271.png" alt="image-20230724111509092" style="zoom: 50%;" />

```python
# 安装包，包名==版本号可以指定安装的版本
# 通常包conda命令都可实现安装，若安装不了则使用pip instal xxx的方式
conda install 包名
# 从指定源下载包【国内搭载镜像源会下载得更快】
# 也可以通过设置一劳永逸，以后直接安装默认通过镜像源下载，具体可参考如下👇博客
conda instal 包名 -i 下载源链接
```

[conda换国内源](https://blog.csdn.net/u014421313/article/details/126698985?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169017703716800180636909%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169017703716800180636909&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-126698985-null-null.142^v90^control_2,239^v3^insert_chatgpt&utm_term=conda%E6%8D%A2%E6%BA%90&spm=1018.2226.3001.4187)

```python
# 卸载安装的包
conda uninstall 包名
```

## 2.3 虚拟环境安装jupyter

若要在 jupyter 中指定虚拟环境，需要先安装 nb_conda 插件，同时通过Conda进入虚拟环境中安装 jupyter【此处需要使用 conda install jupyter 安装，pip 无法奏效】，具体安装过程可看👉[Jupyter Notebook中切换conda虚拟环境](https://zhuanlan.zhihu.com/p/139776843)



