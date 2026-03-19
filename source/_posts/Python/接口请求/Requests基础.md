---
title: Requests基础
date: 2024-04-15 16:10:23
excerpt: Rquests库是Python编写的，基于urllib的HTTP库，用于实现接口自动化测试
tags:
  - 接口请求
categories:
  - [Python,接口请求]
---

# 1. Requests库介绍

> Rquests 库是 Python 编写的，基于 urllib 的 HTTP 库，用于实现接口自动化测试

```python
# 请求方法有get,post,put,delete等
# url: 待请求的url地址
# params：查询参数【get方法中问号后的内容】
# headers：请求头【通常不指定也没关系】
# data：表单格式的请求体
# json：json格式的 请求体
# cookies：cookie数据
# resp：响应结果
resp = requests.请求方法(url='URL地址', params={k:v}, headers={k:v},
                     data={k:v}, json={k:v}, cookies='cookie数据'(如：令牌))
```

**案例：完成商城验证码错误登录**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401221421790.png" alt="image-20240122142108594" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401221419974.png" alt="image-20240122141958956" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401221418702.png" alt="image-20240122141842602" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401221420628.png" alt="image-20240122142050617" style="zoom:80%;" />

使用requests实现同点击登录按钮后一样的效果

```python
import requests
# 部署在本地虚拟机上的TpShop项目的登录界面
resp = requests.post(
    url='http://192.168.88.130/index.php?m=Home&c=User&a=do_login&t=0.4931994393421102',
    headers={'Content-Type':'application/x-www-form-urlencoded; charset=UTF-8'},
    data={'username':'13800138006','password':'123456','verify_code':'1234'}
    )
# 获取响应结果的json格式
print(resp.json())
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202401221424741.png" alt="image-20240122142422706" style="zoom:80%;" />

```python
# 常用响应结果数据
resp.url
resp.status_code
resp.cookies
resp.headers
resp.text
# 得到的是dict对象
resp.json()
```

