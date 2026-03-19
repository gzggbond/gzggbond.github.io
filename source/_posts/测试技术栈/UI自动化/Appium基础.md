---
title: Appium基础
date: 2024-04-06 14:27:23
excerpt: Appium是一个开源的、跨平台的UI自动化测试框架。它的核心理念是让你可以用同一套API和熟悉的编程语言，去自动化测试多种不同类型的应用，比如移动App（iOS/Android）、桌面应用（Windows/macOS），可以理解为移动端和桌面端的 Selenium
tags:
  - UI自动化
categories:
  - [测试技术栈,UI自动化]
---

Appium是一个**开源的、跨平台的UI自动化测试框架**。它的核心理念是让你可以用**同一套API**和**熟悉的编程语言**，去自动化测试**多种不同类型的应用**，比如移动App（iOS/Android）、桌面应用（Windows/macOS），可以理解为移动端和桌面端的 "Selenium"

# 1. adb工具

ADB（Android Debug Bridge）是通过电脑系统来操作 Android 系统的手机调试工具，其是 Andriod-sdk（安卓开发者工具）的一部分

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403152147207.png" alt="image-20240315214705141" style="zoom:67%;" />

**连接流程**

1. 通过 cmd 脚本运行 adb 命令的时，首先会开启 adb 服务器（如未开启），服务器会绑定一个 TCP 端口 5037

2. 服务器端会监听客户端发送的命令，所有客户端均通过 5037 端口与服务器进行通信

3. 服务器设置与所有运行的模拟器/设备实例的连接。它通过扫描 5555 到 5585 之间（模拟器/设备使用的范围）的奇数号端口查找模拟器/设备实例。

4. 服务器一旦发现 adb 后台程序（调试设备中），它将设置与该端口的连接

5. 每个模拟器/设备实例将获取一对按顺序排列的端口，用于控制台连接的偶数号端口和用于 adb 连接的奇数号端口

   > 如此时有一台模拟机被发现，则控制台连接的端口为5554，adb 连接的端口为5555……以此类推

## adb 常见命令

1. 获取包名和启动名

   > 包名是一个安卓应用的唯一标识符，需要依赖包名才能操作应用
   > 启动名是应用中界面标识符，允许重复
   >
   > 包名与启动名组合可以精确定位到具体应用及其所在位置（界面）

   ```shell
   # 查看当前模拟器所展示界面所处的包名和启动名
   adb shell dumpsys window | findstr usedApp
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403152223450.png" alt="image-20240315222344420" style="zoom:80%;" />

2. 上传和下载命令

   > 分隔符都用 / 不出错

   ```shell
   # 上传本地文件至模拟机
   adb push 本地文件路径 /sdcard
   # 从模拟机拉取文件至本地
   adb pull /sdcard/xxx.txt 本地文件夹路径
   ```

3. 启动时间命令:star:

   + 冷启动：应用程序未启动
   + 热启动：应用程序已启动在后台或当前页面

   **注意：查看时间一般要冷启动（后台不能有）**

   ```shell
   # 包名/启动名可通过adb shell dumpsys window | findstr usedApp命令查询
   adb shell am start -W 包名/启动名
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403152236303.png" alt="image-20240315223608277" style="zoom:67%;" />

4. 查看日志

   > 启动命令**之后**在模拟器上的操作都会被同步写入日志中，因此确保启动时已来到待测界面

   ```shell
   # 将日志文件保存至本地
   adb logcat > 本地路径/xxx.log
   ```

5. 其他常用命令

   | 命令                        | 效果                                     |
   | --------------------------- | ---------------------------------------- |
   | adb install 本地路径/xx.apk | 安装安装应用至手机                       |
   | adb uninstall 包名          | 卸载手机上的 app，需要指定包名           |
   | adb devices                 | 获取当前电脑已经连接的设备和对应设备号   |
   | adb shell                   | 进入到安装手机内部的 Linux 系统命令行中  |
   | adb start-server            | 启动 adb 服务端，出 bug 时可以重启服务器 |
   | adb kill-server             | 停止 adb 服务端，出 bug 时可以重启服务器 |
   | adb connect ip:端口         | 手动连接手机/模拟器                      |

   + adb start-server 正常不需要手动启动，执行任意的 adb 命令都会启动服务器
   + adb connect ip:端口 正常不要手动连接，系统会自动连接。如果执行 adb devices 没有看到设备列表，需要手动连接

## 查看元素定位信息

> 自动化测试需要根据元素信息（如 id、class）进行定位，PC 端可通过调试工具获取各标签的基本属性值，而移动端（Android）则通过 Android SDK 自带的工具：uiautomatorviewer 来获取

**如何使用？**

1. 模拟器来到需要获取基本属性的界面

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403161025403.png" alt="image-20240316102529353" style="zoom: 50%;" />

2. 命令行执行`uiautomatorviewer`后启动 UI 调试工具，点击如图所示标志

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403161029685.png" alt="image-20240316102946664" style="zoom: 80%;" />

3. 调试工具会对当前模拟器所处界面进行截图，此时即可根据需要定位查看需要的元素属性

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403161031928.png" alt="image-20240316103140835" style="zoom:67%;" />

   如果点击捕获界面元素信息报错，通常是因为 UiAutoMatorViewer 连接不到模拟器/手机导致的，重置 abd 服务即可

# 2. Appium固定操作

+ 启动移动端模拟机

+ 启动 Appium 服务器

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403162142346.png" alt="image-20240316214153287" style="zoom: 50%;" />

  服务器启动后，当测试脚本发出连接请求时，Appium 本质上是使用 adb 工具与模拟机完成连接

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403162148030.png" alt="image-20240316214759999" style="zoom:67%;" />

+ 编写连接脚本

  ```python
  from appium import webdriver
  from appium.webdriver.common.appiumby import AppiumBy
  from appium.webdriver.common.touch_action import TouchAction
  # 定义字典变量
  desired_caps = {}
  # 字典追加启动参数
  desired_caps["platformName"] = "Android"
  # 注意：版本号必须正确（可通过模拟机的设置查看）
  desired_caps["platformVersion"] = "7.1.2"
  # android不检测内容，但是不能为空
  desired_caps["deviceName"] = "localhost"
  # 默认打开设置【不写报错，不知道为啥……】
  desired_caps["appPackage"] = "com.android.settings"
  desired_caps["appActivity"] = ".Settings"
  # 设置中⽂
  desired_caps["nicodeKeyboard"] = True
  desired_caps["resetKeyboard"] = True
  # 获取driver【/wd/hub是固定写法】
  driver = webdriver.Remote("http://127.0.0.1:4723/wd/hub", desired_caps)
  ```

# 3. Appium基础API

+ driver.start_activity(appPackage,appActivity)

  > 开启 appPackage/appActivity 对应的应用【原先开启的应用被放入后台】

+ driver.current_package

  > 获取当前应用包名

+ driver.current_activity

  > 获取当前应用所在界面的启动名

+ driver.close_app()

  > 关闭当前 app

+ driver.quit()

  > 关闭驱动

+ driver.install_app(app_path)

  > 安装 app_path 指定路径的 apk 安装包

+ driver.remove_app(app_id)

  > 卸载 app_id 对应的 app

+ driver.is_app_installed(app_id)

  > 判断 app_id 对应的 app 是否安装

+ driver.background_app(second)

  > 将当前应用放置后台 second 秒后恢复为主窗口

# 4. 元素定位

> 通过 UiAutoMatorViewer 可以看到到元素基本信息

+ ID：resource-id属性值
+ CLASS_NAME：class属性值
+ XPATH：xpath表达式
+ ACCESSIBILITY_ID：content-desc属性值

```python
# AppiumBy 继承 selenium 中的 By，在此基础上多了其他一些常量选择
from appium.webdriver.common.appiumby import AppiumBy
# 当定位到多个符合条件的元素时，默认返回第一个
driver.find_elements(by=xxx,value=yyy)
```

**元素操作方法**

```python
ele.click()						# 点击
ele.send_keys()					# 输入
ele.clear()						# 清空
ele.text						# 获取文本
ele.size						# 获取大小
ele.location					# 获取位置
# 获取属性值对应的属性名和实际 UiAutoMatorViewer 显示的不一定一致，如下👇
ele.get_attribute("属性名")	  # 获取属性值
# 从ele1元素滚动到ele2元素
driver..drag_and_drop(ele1,ele2)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403161635549.png" alt="image-20240316163513460" style="zoom:50%;" />

# 5. 手势操作

> 此处的selenium版本为 4.18.1，appnium版本为 2.10.2，当二者版本不匹配时可能会出现错误

**所有的手势操作生效都需要执行 perform() 方法**

```python
# 手势操作所依赖的类
from appium.webdriver.common.touch_action import TouchAction
# 假设此时前置操作已经完成且来到“设置”界面
...
# 定位到文本值为WLAN的元素组件
ele = driver.find_element(by=AppiumBy.XPATH,value="//*[@text='WLAN']")
# 轻敲此组件
TouchAction(driver).tap(ele).perform()
# 得到元素组件的x与y位置【location属性获得一个字典，存储x与y方向的位置】
x_pos = ele.location.get('x')
y_pos = ele.location.get('y')
# press按下目标位置指向的组件，release释放按下的动作
TouchAction(driver).press(x=x_pos,y=y_pos).release().perform()
# 与👆类似，不过按压时间会更长一些
TouchAction(driver).long_press(x=x_pos,y=y_pos).release().perform()
# 用于绘制解锁图案，从一个点移动到另一个点...
TouchAction(driver).press(x1,y1).wait(ms1).move_to(x2,y2).perform()
```

# 6. 手机操作API

```python
# 打开通知栏
driver.open_notifications()
# 获取当前屏幕分辨率
driver.get_Window_size()
# 设置网络类型为飞行模式
driver.set_network_connection(1)
# 截图保存
driver.get_screenshot_as_file('.../xxx.png')
# 查看当前网络类型
driver.network_connection
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403162234335.png" alt="image-20240316223453297" style="zoom:67%;" />

```python
# 操作对应数学编号的按键
driver.press_keycode(数学编号)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403162242171.png" alt="image-20240316224027124" style="zoom:67%;" />
