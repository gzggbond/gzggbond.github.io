---
title: Jupyter notebook快捷键
date: 2024-03-21 14:27:23
excerpt: Jupyter notebook允许通过模块的方式执行代码并保存历次运行结果，非常适合随时输出查看效果的情况
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 命令模式下

```java
// 在当前单元格上方插入一个新单元格
A 
// 在当前单元格下方插入一个新单元格
B
// 删除当前单元格
D + D (连续按两次 D) 
// 把当前单元格转换成Markdown类型
M 
// 把当前单元格转换成代码类型
Y
// 在当前单元格中查找和替换文本
Esc + F 
```

# 编辑模式下

```java
// 运行当前单元格，并选中下一个单元格
Shift + Enter
// 运行当前单元格
Ctrl + Enter
// 运行当前单元格，并在下面插入一个新单元格
Alt + Enter
```

# 更换主题

```py
# 蓝灰
jt -t oceans16 -f fira -fs 13 -cellw 90% -ofs 11 -dfs 11 -T
# 黑绿
jt -t monokai -f fira -fs 13 -cellw 90% -ofs 11 -dfs 11 -T -N
# 默认主题
jt -r
```

