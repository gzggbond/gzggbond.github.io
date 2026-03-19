---
title: Git基础
date: 2025-06-29 14:27:23
excerpt: Git是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过Git仓库来存储和管理这些文件
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 1. Git简介

Git 是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过 Git 仓库来存储和管理这些文件，Git 仓库分为两种：

+ 本地仓库：开发人员自己电脑上的 Git 仓库

  1. 本地初始化仓库

     > 如果在当前目录中看到 .git 文件夹（此文件夹为隐藏文件夹）则说明 Git 仓库创建成功，需要配合 remote 命令与远程仓库建立连接

     ```bash
     git init
     ```

  2. 将远程仓库克隆副本至本地

     ```bash
     git clone 远程Git仓库地址
     ```

+ 远程仓库：远程服务器上的 Git 仓库

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202310152022824.png" alt="image-20231015202213739" style="zoom:50%;" />

+ **工作区**：包含 .git 文件夹的目录就是工作区，也称为工作目录，主要用于存放开发的代码
+ **暂存区**：.git 文件夹中有很多文件，其中有一个 index 文件就是暂存区，也可以叫做 stage 。暂存区是一个临时保存修改文件的地方
+ **版本库**： .git 隐藏文件夹就是版本库，版本库中存储了很多配置信息、日志信息和文件版本信息等

**Git 工作区中的文件存在两种状态：**

```bash
# 查看文件状态
git status
```

1. untracked：未跟踪（未被纳入版本控制)

   > Git 完全不知道此文件的存在，一般手动创建但还未在当前目录下执行 git 命令的文件属于 untracked

2. tracked：已跟踪（被纳入版本控制)

   + Staged 已暂存状态：执行了 add 命令但还未 commit 的文件
   + Unmodified（commit 后产生）：未修改状态，相较于上次操作没有变化的文件
   + Modified（commit 后产生）：已修改状态，相较于上次操作有所变化的文件

**每次 git 提交命令时都会使用用户信息，用于标记操作人的身份信息，因此使用git工具前需要注明相关信息，从而明确各操作的源头**

```bash
# 设置用户名称和email地址【写在双引号内】
git config --global user.name "username"
git config --global user.email "useremail"
```

# 2. git基础命令

+ add：添加，将本地文件和版本信息添加至暂存区

  ```bash
  # 将当前所处路径下的所有文件加入暂存区
  git add .
  ```

+ commit：提交，将**暂存区内容（add）**保存到本地仓库

  ```bash
  git commit -m "说明信息"
  ```

+ remote：建立本地仓库与远程仓库的连接

  ```bash
  # 查看本地仓库与远程仓库的连接情况
  git remote -v
  # 完成本地仓库与远程仓库的关联，origin是约定俗成的远程仓库别名
  git remote add origin 本地仓库地址 
  ```

+ push：推送，将**版本库内容（commit）**上传到远程仓库

  > 使用 push 前需要保证本地与远程已经建立连接通道

  ```bash
  # origin是远程仓库惯用名，分支名则指明当前操作的本地分支名（一般与远程仓库一致），主分支名为main
  # -u会将当前分支与指定的远程分支进行关联，后续此分支的推送操作直接使用git push也能成功定位
  # -f：强制覆盖
  git push -u origin "分支名"
  ```

+ pull：拉取，将远程仓库指定分支拉取到本地仓库当前分支下

  > 同理，使用 pull 前也需要保证本地与远程已经建立连接通道

  ```bash
  # 将远程仓库指定分支同步至本地仓库当前分支下
  # -u 作用同push
  git pull -u origin 分支名
  ```

+ clone：克隆，将项目整个复制到当前目录下

  > 使用 clone 会自动搭建本地与远程仓库的连接通道，将整个远程仓库（包括所有分支和提交历史）都复制到本地，可以理解为搭建了一个远程仓库的副本，通常在刚开始参与项目时使用此语句

  ```bash
  git clone 项目https地址
  ```

# 3. 暂存区，版本库控制

> 有时 add 添加了错误文件进入暂存区，或者 commit 错误文件进入版本库，想要将错误文件进行移除，可以使用 reset 命令

```bash
# 将指定文件从暂存区放回工作区，.为所有文件
git reset 文件名1 文件名2...
# 版本库内容要撤回只能全部撤回，不能像暂存区一样撤回部分
# 撤销最近的commit，并将更改保留在暂存区中，以便稍后重新提交
git reset --soft HEAD^
# 撤销最近的commit与add，但不修改工作目录（默认）
git reset --mixed HEAD^
# 所有信息重置回上次 commit 后的状态
# HEAD^表示将HEAD移动到当前HEAD的父提交，也可以使用版本号，即回溯到某个状态
git reset --hard HEAD^
```

# 4. 分支操作branch

分支是 Git 使用过程中非常重要的概念，使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。同一个仓库可以有多个分支，各个分支相互独立，互不干扰。

+ **创建分支**

  ```bash
  # 在本地创建分支，使用push后会将分支推送至远程仓库
  git branch 分支名
  ```

+ **推送分支**

  > 仅仅推送新分支时不需要使用 add ，如果是推送分支内容则该有的步骤（add，commit，push）都不能少

  分支不一定需要单独推送，如果此时版本库（commit）有数据，则将本地分支推送时默认就会在远程创建分支

  ```sql
  # -u会将当前分支与指定的远程分支进行关联
  git push -u origin 分支名
  ```

+ **查看分支**

  ```bash
  # 列出所有本地分支
  git branch 
  # 列出所有远程分支
  git branch -r
  # 列出所有本地分支和远程分支
  git branch -a
  ```

+ **切换分支**：切换分支后的操作都是针对当前分支的操作，与先前分支无关

  ```bash
  # 切换本地操作分支
  git checkout 分支名
  # 切换分支，若没有则创建并切换
  git checkout -b 分支名
  ```

+ **合并分支**

  ```bash
  # 将指定分支合并至当前所处分支
  git merge 分支名-m "说明信息"
  ```

+ **删除分支**

  1. 删除本地分支

     ```bash
     # 若分支未合并，则使用此语句会提示
     git branch -d branch_name
     # 强制删除本地分支
     git branch -D branch_name
     ```

  2. 删除远程分支

     ```bash
     # 删除远程分支
     git push origin --delete branch_name
     ```


# 5. 标签操作tag

标签可以理解为分支中的断点，其是静态的，通常打上标签后不再对内容进行修改

+ 创建标签

  ```bash
  # 创建标签v1
  git tag v1
  # 从标签处延伸出一个分支
  git checkout -b 分支名 标签名
  ```

+ 查看标签

  ```bash
  # 查看本地仓库标签情况
  git tag
  ```

+ 推送标签

  ```bash
  # 将标签v1推送至远程仓库，记录的是当前分支的状态
  git push origin v1
  ```
