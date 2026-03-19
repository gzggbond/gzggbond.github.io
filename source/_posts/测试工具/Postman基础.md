---
title: Postman基础
date: 2024-11-18 15:36:23
excerpt: Postman是一款非常流行的接口调试工具，它使用简单，而且功能也很强大。不仅测试人员会使用，开发人员也会经常使用
tags:
  - 测试工具
categories:
  - [测试工具]
---

# 1. 介绍

> Postman 是一款非常流行的接口调试工具，它使用简单，而且功能也很强大。不仅测试人员会使用，开发人员也会经常使用

**特点：**

1. 简单易用的图形用户界面
2. 可以保存接口请求的历史记录
3. 使用测试集 Collections 可以更有效的管理组织接口
4. 可以在团队之间同步接口数据

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311231526054.png" alt="image-20231123152517986" style="zoom:67%;" />

# 2. 用例集

> 类似 Java 中的项目，可将一类具有相同特性的测试例子放在一个用例集中，用例集可以进行导入和导出

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311231639308.png" alt="image-20231123163940280" style="zoom:50%;" />

# 3. 断言

+ 利用 Postman 自带的断言机制，帮助我们自动判断预期结果和实际结果是否一致
+ 使用的是 JavaScript 脚本语言，写在 Tests 标签页中，在 TestResults 标签中显示

Postman 在 Tests 处进行代码编写

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311271541705.png" alt="image-20231127154148611" style="zoom:67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311271603575.png" alt="image-20231127160314545" style="zoom:67%;" />

## 3.1 断言响应状态码

```javascript
// pm：Postman的一个实例
// 参数1："Status code is 200"断言完成后给出的提示信息
// 参数2：匿名函数调用
pm.test("Status code is 200", function () {
    // Postman响应结果中，有状态码 200 时说明测试通过
    pm.response.to.have.status(200);
});
```

## 3.2 断言响应体是否包含某个字符串

>  判断的不一定是严格 string 类型，其他类型如整型布尔型等都可以判断【此时不需要双引号】

```javascript
pm.test("Body matches string", function () {
    // pm.response.text()得到的是实际结果
    pm.expect(pm.response.text()).to.include("string_you_want_to_search");
});
```

## 3.3 断言响应体是否等于某个字符串（对象）

```javascript
// 判断响应体与传入内容是否完全一致
pm.test("Body is correct", function () {
    pm.response.to.have.body(JSON对象);
});
```

## 3.4 断言JSON数据

```javascript
pm.test("Your test name", function () {
    // 拿到的json数据
    var jsonData = pm.response.json();
    // jsonData.value实则是拿到数据的key值【如jsonData.code表示键值为code】
    // eql()中填写的是拿到数据的value值
    // 这段代码意思为：判断数据key值为value对应的value值是否为100
    pm.expect(jsonData.value).to.eql(100);
});
```

## 3.5 断言响应头

```javascript
pm.test("Content-Type is present", function () {
    // 判断响应头中是否有Content-Type
    pm.response.to.have.header("Content-Type");
});
```

# 4. 变量设置

+ 全局变量：全局唯一的变量，不可重复定义

+ 环境变量：只属于某个环境的变量，在同一环境中不可重复定义

  > 常见环境分类:：开发环境、测试环境、生产环境

可在如下位置👇查看变量情况

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311272035660.png" alt="image-20231127203457580" style="zoom: 50%;" />

**代码设置**

```java
// 全局变量设置
pm.globals.set("var_name" , value);
// 全局变量获取
pm.globals.get("var_name");
// 环境变量设置
pm.environment.set("var_name" , value);
// 环境变量获取
pm.environment.get("var_name");
```

**界面获取参数**

```javascript
 {{变量名}}
```

# 5. 请求前置脚本

> 可以理解为发送请求前被自动执行的代码段，通常完成一些前置操作，如存储时间戳信息等

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311272047144.png" alt="image-20231127204741116" style="zoom: 80%;" />

# 6. Postman关联

> 解决接口和接口之间调用依赖关系，需要借助全局变量、环境变量来解决关联问题

以 A 接口返回的数据，供 B 接口使用为例：

1. 组织 A 接口 http 请求数据，发送 A 接口请求
2. 获取 A 接口返回的响应数据，写入全局、环境变量中
3. 组织 B 接 http 请求，从全局、环境变量中获取 A 返回的数据

```javascript
// 获取响应体中的json数据
var jsonData = pm.response.json()
// 取得关键词为data中province对应的值
var city = jsonData.data.province
// 将此值存入全局变量中，并且设key为g_city
pm.globals.set("g_city",city)
```

# 7. 批量执行测试用例

1. 点击用例集名称，选择 “Run” 按钮，进入 Runner 标签页中。

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311281727492.png" alt="image-20231128172743346" style="zoom:67%;" />

2. 在 Runner 标签页中用例集内的所有请求页会被默认自动选中。

3. 点击 “Run xxxx” 按钮，批量执行用例集。

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311281735842.png" alt="image-20231128173518767" style="zoom: 33%;" />

# 8. postman生成测试报告

> 确保操作前成功安装了 newman 插件

```shell
# 生成测试报告的模板语句【各种文件均可通过postman的export导出】
newman run 测试脚本文件 -e 环境变量文件 -d 测试数据文件 -r html --reporter-html-export report.html
# 如下👇是例子
newman run 瑞吉外卖.postman_collection.json -e 瑞吉外卖测试环境.postman_environment.json -r html --reporter-html-export report.html
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311291412091.png" alt="image-20231129141243988" style="zoom:67%;" />



