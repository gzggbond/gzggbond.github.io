---
title: Fiddler基础
date: 2024-04-27 11:03:11
excerpt: Fiddler 是一款抓包工具，可以将网络传输发送与接受的数据包进行截获、重发、编辑、转存等操作，也可以用来检测网络安全
tags:
  - 测试工具
categories:
  - [测试工具]
---

# 1. Fiddler介绍

> Fiddler 是一款抓包工具，可以将网络传输发送与接受的数据包进行截获、重发、编辑、转存等操作，也可以用来检测网络安全

# 2. 设置过滤

> 通过设置过滤条件，可以使抓包工具过滤掉非目标包

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201554041.png" alt="image-20240120151348394" style="zoom:80%;" />

# 3. 删除数据

> 被抓取的包可以进行手动删除

**方法一：**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201554197.png" alt="image-20240120152123521" style="zoom:80%;" />

**方法二：**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201553305.png" alt="image-20240120152232964" style="zoom: 80%;" />

**方法三：**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201552249.png" alt="image-20240120153203106" style="zoom: 80%;" />

# 4. 查看数据

![image-20240120154608079](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201552631.png)

# 5. 弱网环境测试

> 通过改变请求和响应时间可以模拟弱网环境，方便进行弱网条件下的测试

![image-20240120161725836](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201618843.png)

点击 Rules --> Customize Rules ，Ctrl + F 打开搜索框输入 300，定位至如下位置👇，手动修改延迟时间

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201628752.png" alt="image-20240120162810725" style="zoom:80%;" />

设置完规则后，点击 Rules --> Performance --> Simulate Modem Speeds 应用规则即可 

**弱网环境下常见问题**

+ 上传文件时进度卡住不动，登录不上，或者登录后立即掉线
+ 响应过程中页面控件可点击，导致崩溃
+ 搜索不响应，多次点击后结果显示总在刷新被替换

# 6. 断点设置

> 通过设置断点，可以观察包在发送以及响应路上的情况，便于进行分析 BUG 出现的位置

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201651680.png" alt="image-20240120165142152" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401201702871.png" alt="image-20240120170227353" style="zoom:67%;" />

拦截后可对请求 (request) 或者响应 (response) 参数进行手动修改

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401202227328.png" alt="image-20240120222739249" style="zoom:80%;" />

