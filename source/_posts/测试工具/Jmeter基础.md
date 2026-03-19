---
title: Jmeter基础
date: 2025-07-07 11:14:14
excerpt: Jmeter是Apche公司使用Java平台开发的一款测试工具，主要用于对软件、服务器或网络对象模拟巨大的负载，从而测试它们的强度并分析整体性能
tags:
  - 测试工具
categories:
  - [测试工具]
---

# 1. 介绍

Jmeter是Apche公司使用Java平台开发的一款测试工具，主要用于对软件、服务器或网络对象模拟巨大的负载，从而测试它们的强度并分析整体性能

+ **接口测试(http接口）**
+ 性能测试
+ **压力测试**（优势）
+ 数据库测试
+ Java程序测试

# 2. 元件

> 元件即为多个类似功能组件的容器（类似于类），组件用于实现独立的某个功能（类似于方法）

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402181214917.png" alt="image-20240218121429794" style="zoom: 33%;" />

+ **取样器**：发送请求
+ **逻辑控制器**：控制语句的执行顺序
+ **前置处理器**：对请求参数进行预处理
+ **后置处理器**：对响应结果进行提取
+ **断言**：检查接口的返回结果是否与预期结果一致
+ **定时器**：设置等待
+ **测试片段**：封装一段代码，供其他脚本调用
+ **配置元件**：测试数据的初始化配置
+ **监听器**：查看 Jmeter 脚本的运行结果

## 作用域原则

+ **取样器**：核心，没有作用域
+ **逻辑控制器**：只对其子节点中的取样器和逻辑控制器起作用
+ 其余元件
  + 如果是某个取样器的子节点，则该元件只对其父节点起作用
  + 如果其父节点不是取样器，则其作用域是该元件父节点下的其他所有后代节点（包括子节点，子节点的子节点等）

**执行顺序**

+ 同一作用域下不同元件

  > *配置元件 --> 前置处理程序 --> 定时器 --> 取样器 --> 后置处理程序 --> 断言 --> 监听器*

+ 同一作用域下相同元件

  > 从上到下的顺序依次执行

**例子**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402181553824.png" alt="image-20240218155318769" style="zoom:50%;" />

各组件执行顺序如下【保证 IF 控制器能正常进入的情况下】👇

> 定时器1 --> HTTP请求1 --> 定时器1 --> 定时器2 --> HTTP请求2 --> 定时器1 --> 定时器3  --> HTTP请求3

**解释**：

1. 同一作用域下，定时器优先级高于取样器，因此首先执行**定时器1**，再者为**HTTP请求1**
2. 除去取样器与逻辑控制器外，其余元件如果其父节点不是取样器，则其作用域是该元件父节点下的其他所有后代节点。定时器1的父节点为线程组，不属于取样器，因此在后代节点发挥作用前会再次调用**定时器1**
3. 进入逻辑控制器，按照其优先级顺序分别执行**定时器2**与**HTTP请求2**，**定时器3**是取样器的子节点，因此也发挥作用，最后是**HTTP请求3**

# 3. 三个重要组件

## 3.1 线程组

**特点**

+ 模拟用户，支持多用户操作
+ 多个线程组可以串行执行，也可以并行执行

**分类**

+ **普通线程组**：编写脚本
+ **setup 线程组**：前置处理，初始化
+ **teardown 线程组**：后置处理，环境恢复等

**属性**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402181728986.png" alt="image-20240218172852934" style="zoom: 50%;" />

循环次数是指每个线程的循环次数

**案例分析：**
使用 1 个线程组，添加 HTTP 请求（百度）

+ 配置线程数为2，循环次数为 3 时，运行观察结果
+ 配置线程数为3，循环次数为 2 时，运行观察结果，对比是否有不同

相同点：从请求数量来说，是完全相同的

不同点：场景不同

+ 线程数：代表用户数，即性能测试时的负载量（线程数为 2 比线程数为 3 对应的负载量小）
+ 循环次数：代表时间，即性能测试时的运行时间（循环次数 3 比循环次数 2 对应的时间长）

## 3.2 HTTP取样器

**作用**：向服务器发送http及https请求

**位置**：选中线程组 --> 右键 --> 添加 --> 取样器 --> HTTP请求

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402231638796.png" alt="image-20240223163755675" style="zoom:80%;" />

### 3.3 查看结果树

**作用**：查看结果

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402231735397.png" alt="image-20240223173538372" style="zoom:67%;" />

**位置**：选中线程组 --> 右键 --> 添加 --> 监听器 --> 查看结果树

# 4. 参数化

> 使用参数的方式来替代脚本中的固定测试数据

实现方式：

+ 定义变量（最基础）
+ 文件定义的方式（所有测试数据都是固定的情况下）
+ 数据库的方式（灵活，业务测试常用）
+ 函数的方式（灵活，业务测试常用）

## 4.1 用户定义的变量

**作用**：定义**全局**变量

**位置**：线程组 --> 配置元件 -->用户定义的变量

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402231943101.png" alt="image-20240223194312034" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402231944147.png" alt="image-20240223194425119" style="zoom:80%;" />

## 4.2 用户参数

**作用**：针对同一组参数，当不同用户来访问时，可以获取到不同的值

**位置**：选中线程组 --> 右键 --> 添加 --> 前置处理器 --> 用户参数

1. 添加线程数为 n（代表模拟的用户数）
2. 添加用户参数
   + 第一列添加多个变量名
   + 后续每一列为一组用户的数据
3. 添加 HTTP 请求，引用定义的变量名，格式：`${}`
4. 添加查看结果数

**案例：向百度发送两次get请求，携带name与age参数，值分别为张三、李四，18、20**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402232003001.png" alt="image-20240223200352975" style="zoom:67%;" />

线程组中的线程数设置为 2 模拟两个用户

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402232015108.png" alt="image-20240223201509074" style="zoom:67%;" />

发送的两次请求URL路径如下👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402232017770.png" alt="image-20240223201734748" style="zoom: 80%;" />

## 4.3 csv数据文件设置

**作用**：让不同用户在多次循环时，可以取到不同的值

**位置**：线程组 --> 配置元件 --> CSV数据文件设置

*使用方法与用户参数类似*

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402232034224.png" alt="image-20240223203421192" style="zoom:67%;" />

## 4.4 counter函数

**作用**：自动生成不重复的数据，让每个用户每次循环都能取到不同数据，且不需要提前定义

位置：菜单栏点击工具 --> 函数助手对话框

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402232053549.png" alt="image-20240223205314514" style="zoom:80%;" />

# 5. 断言

> 检查实际的返回结果是否与预期结果保持一致

Jmeter 有自动校验机制，即自动判断响应状态码（2xx：成功，4xx/5xx：失败）

+ **响应断言**：对任意格式的响应数据进行断言
+ **json断言**：对 json 格式的响应数据进行断言
+ **持续时间断言**：对响应时间进行断言

## 5.1 响应断言

**作用**：对HTTP请求的任意格式响应结果进行断言

**位置**：线程组 --> HTTP请求 --> 断言 --> 响应断言

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241324388.png" alt="image-20240224132403287" style="zoom: 50%;" />

当测试字段预期结果与实际结果不符时，在查看结果树中会出现如下提示

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241326935.png" alt="image-20240224132646911" style="zoom:80%;" />

## 5.2 JSON断言

**作用**：对 HTTP请求的 json 格式响应数据进行断言

**位置**：线程组 --> HTTP请求 --> 断言 --> JSON 断言

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241348295.png" alt="image-20240224134823261" style="zoom:80%;" />

## 5.3 持续时间断言

**作用**：对响应时间进行断言

位置：线程组 --> HTTP请求 --> 断言 --> 断言持续时间

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241352361.png" alt="image-20240224135220331" style="zoom:80%;" />

当持续时间超出最高值时，在查看结果树中会出现如下提示

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241353846.png" alt="image-20240224135358823" style="zoom:80%;" />

# 6. 关联

> 请求之间有依赖关系，一个请求的响应数据作为另一个的请求参数来传递

## 6.1 正则表达式提取器

**作用**：针对任意格式的响应数据进行提取

**位置**：线程组 --> HTTP请求 --> 后置处理器 --> 正则表达式提取器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241434562.png" alt="image-20240224143412529" style="zoom:80%;" />

+ **.**  ：匹配除换行符（\n、\r）之外的任何单个字符

+ ***** ：号代表前面的字符可以不出现，也可以出现一次或者多次（0次、或1次、或多次）

+ **?** ：代表前面的字符最多只可以出现一次（0次或1次）

  其余关注正则表达式的规则可参考：[正则表达式规则](https://www.runoob.com/regexp/regexp-syntax.html)

模板中，计数以括号为单位，`$1$`代表第一个括号匹配的值
匹配数字是对每个括号匹配元素的计数

**案例：向哔哩哔哩发送请求，获取其title值并作为关键字访问百度**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241500435.png" alt="image-20240224150010408" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241500773.png" alt="image-20240224150027739" style="zoom:80%;" />

查看结果树结果如下

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241502112.png" alt="image-20240224150211085" style="zoom:67%;" />

## 6.2 XPath提取器

**作用**：针对HTML格式的响应结果数据进行提取

**位置**：线程组 --> HTTP请求 --> 后置处理器 --> XPath提取器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241534102.png" alt="image-20240224153416061" style="zoom:80%;" />

## 6.3 JSON提取器

**作用**：针对 JSON 格式的响应结果数据进行提取

**位置**：线程组 --> HTTP请求 --> 后置处理器 --> JSON提取器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241554955.png" alt="image-20240224155416924" style="zoom:80%;" />

## 6.4 Jmeter属性

> 需要实现跨线程组的数据传递时，可以使用 JMeter 属性，将线程组的局部属性存储为全局属性

**案例：在线程 1 保存请求（`http://m.nmc.cn/rest/predict/59289?_=1708762524613`）返回的 json 数据中`city`关键字对应的值，并在线程2中用于百度搜索**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241702172.png" alt="image-20240224170212142" style="zoom:67%;" />

其测试计划对应如下：

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241703081.png" alt="image-20240224170326057" style="zoom:80%;" />

首先使用 JSON 提取器保存关键值到线程 1 中

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241704177.png" alt="image-20240224170450149" style="zoom:80%;" />

接着使用 setProperty 函数实现局部变量到全局变量的转换，同时在此线程组中添加 BeanShell 取样器用于执行此函数，完成变量保存

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241654914.png" alt="image-20240224165423888" style="zoom:67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241657005.png" alt="image-20240224165752981" style="zoom:67%;" />

再次调用函数助手，生成 property 函数

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241708847.png" alt="image-20240224170845815" style="zoom:80%;" />

最后在线程组 2 中需要传参的地方调用函数，完成传参

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241710124.png" alt="image-20240224171018096" style="zoom:80%;" />

查看结果树

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402241710337.png" alt="image-20240224171055313" style="zoom:80%;" />

# 7. 直连数据库

## 7.1 应用场景

1. 用户请求的参数化

   > 例如登录时需要的用户名，可以从数据库中查询获取

2. 用作结果的断言

   > 例如添加购物车下订单，检查接口返回的订单号，是否与数据库中生成的订单号一致

3. 清理垃圾数据

   > 例如添加商品（商品号/编号等不能重复），再执行该脚本不能成功，需要在下次执行前删除该商品数据

4. 准备测试数据

   > 例如通过数据库来准备大量性能测试数据

## 7.2 关键配置

> 将 MySQL 驱动 jar 包放入到 lib/ext 目录下，重启 JMeter

**连接数据库配置**

*位置*：线程组 --> 配置元件 --> JDBC Connection Configuration

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402242035131.png" alt="image-20240224203456601" style="zoom:67%;" />

**执行SQL语句配置**

*位置*：线程组 --> 配置元件 --> JDBC Request

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402242039445.png" alt="image-20240224203905387" style="zoom:80%;" />

由于查询的数据条数未知，因此实际上存储的变量名会在 Variable names 的基础上进行编号，如 Variable names 若为 var，则实际上其第一条元素变量引用名为 var_1

完成配置与语句编写，执行后通过查看结果树即可得到查询结果

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402242041811.png" alt="image-20240224204112785" style="zoom:80%;" />

# 8. 逻辑控制器

## 8.1 IF控制器

**作用**：控制其下测试元素是否运行

**位置**：线程组 --> 逻辑控制器 --> IF控制器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251051231.png" alt="image-20240225105129120" style="zoom: 67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251056476.png" alt="image-20240225105602447" style="zoom:67%;" />

## 8.2 循环控制器

**作用**：控制其下测试元素循环执行

**位置**：线程组 --> 逻辑控制器 --> 循环控制器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251058819.png" alt="image-20240225105837794" style="zoom:80%;" />

## 8.3 ForEach控制器

**作用**：一般和用户自定义变量或者正则表达式提取器一起使用，读取返回结果中一系列相关的变量值。该控制器下的取样器都会被执行一次或多次，每次读取不同的变量值

**位置**：线程组 --> 逻辑控制器 --> ForEach控制器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251121668.png" alt="image-20240225112132632" style="zoom:80%;" />

*结束循环字段不填时默认遍历到最后一个*

**案例：引用用户自定义变量访问网站**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251131162.png" alt="image-20240225113124136" style="zoom: 80%;" />

ForEach控制器设置如下

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251135054.png" alt="image-20240225113507025" style="zoom:67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251139570.png" alt="image-20240225113945536" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251140169.png" alt="image-20240225114040144" style="zoom:80%;" />

# 9. 定时器

## 9.1 同步定时器

**作用**：测试抢购、秒杀或者抢红包等高并发的场景下使用

**位置**：线程组 --> HTTP请求 --> 同步定时器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251212995.png" alt="image-20240225121211963" style="zoom:80%;" />

设置超时时间需要注意以下两点：

+ 建议设置：不设置的话，若没有达到设置的线程数会一直死等
+ 不能设置太小：等待时间后还没达到设置的线程数，会释放已到达的线程

通过监听器中的聚合报告可以查看线程发送的动态过程

## 9.2 常数吞吐量定时器

**作用**：让 JMeter 按指定的吞吐量执行，以每分钟为单位

**位置**：线程组 --> HTTP请求 --> 定时器 --> 常数吞吐量定时器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251451596.png" alt="image-20240225145133530" style="zoom:67%;" />

QPS（Queries-per-second）即每秒查询率

## 9.3 固定定时器

**作用**：让请求间隔一定时间后再执行

**位置**：线程组 --> HTTP请求 --> 定时器 --> 固定定时器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251501957.png" alt="image-20240225150124929" style="zoom:67%;" />

# 10. 测试报告

## 10.1 聚合报告

**作用**：收集性能测试结束后，系统的各项性能指标。如:响应时间、并发数、吞吐量、错误率等

**位置**：测试计划 --> 监听器 --> 聚合报告

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402251629376.png" alt="image-20240225162909334" style="zoom:80%;" />

+ **Label**：每个请求的名称
+ **样本**：各请求发出的数量
+ **平均值**：平均响应时间（单位：毫秒）
+ **中位数**：中位数，50% <= 时间
+ **90%百分比**：90% <= 时间
+ **95%百分比**：95% <= 时间
+ **99%百分比**：99% <= 时间
+ **最小值**：最小响应时间
+ **最大值**：最大响应时间
+ **异常%**：请求的错误率
+ **吞吐量**：吞吐量。默认情况下表示每秒完成的请求数，一般认为它为TPS
+ **接收KB/sec**：每秒接收到的千字节数
+ **发送KB/sec**：每秒发送的千字节数

## 10.2 HTML测试报告

**作用**：JMeter 支持生成 HTML 测试报告，以便从测试计划中获得图表和统计信息

```shell
# 命令需在jmeter的bin目录下执行
# 存放结果在jmeter中bin目录下的report文件夹，保证此时无report目录
jmeter -n -t "测试计划.jmx" -l 测试结果.jtl -e -o ./report
```

# 11. 并发数计算

**普通方法**

+ 并发TPS = 总请求数/总时间
+ 只能满足最基本的要求，但是不能很好覆盖系统正常的使用情况

**二八原则**

+ 并发TPS = 总请求数 * 80% / (总时间 * 20%)
+ 满足系统绝大多数情况下的应用场景需要

**根据业务运营数据的统计计算（通常用来做稳定性测试）**

+ 并发 TPS = 有效请求数 * 80% / (有效时间 * 20%)
+ 当运营数据统计越精确时，计算出的并发TPS与实际的越接近

**根据用户峰值业务操作来计算（通常用来做压力测试）**

+ 并发TPS = 峰值请求数 / (峰值时间 * 系数)
+ 满足峰值请求时间段内的负载量，系数取决于项目组对于未来业务量的评估

**例子**

某购物商城，经过运营统计，正常一天成交额为100亿，客单价平均为300元，交易时间主要为 10:00 - 14:00，17:00 - 24:00，其中 19:00 - 20:00 的成交量最大，大约成交20亿。

现升级系统，需要进行性能测试，保证软件在上线后能稳定运行。

请计算出系统稳定性测试时的并发（负载）量，及保证系统峰值业务时的并发（负载）量。
$$
稳定并发量： \dfrac{\frac{100E}{300} \times 0.8}{3600\times11*0.2}\\
压力并发量： \dfrac{\frac{20E}{300} }{3600\times1\times系数}
$$

# 12. Jmeter插件

> 使用第三方的扩展功能，可以使得 Jmeter 更为强大

使用插件前，首先需要安装Jmeter插件管理工具，下载地址如下所示👇：

`https://jmeter-plugins.org/wiki/PluginsManager/`

将下载后的 jar 包放入Jmeter的 lib/ext 目录下，接着按照此博客方法进行配置：[Jmeter插件管理器打开报错解决办法](https://blog.csdn.net/u012106306/article/details/109067686)

点击菜单栏“选项” --> "JMeter Plugins Manager"，若能顺利显示如下👇界面，则说明准备工作完成。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402252038721.png" alt="image-20240225203815158" style="zoom:67%;" />

在 Available Plugins 中添加如下四个插件，后续需要使用

![image-20240225204453670](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402252044697.png)

## 12.1 阶梯线程组

**作用**：阶梯加压，图形界面显示运行状态

**添加方式**：线程（用户） --> Concurrency Thread Group

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402261109026.png" alt="image-20240226110905896" style="zoom: 67%;" />

## 12.2 各种图表监听器

**作用**：统计各个事务每秒成功的事务个数

**添加方式**：线程组 --> 监听器 --> Transactions per Second

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402261129655.png" alt="image-20240226112946612" style="zoom:80%;" />

**作用**：查看服务器吞吐流量（单位/字节）

**添加方式**：线程组 --> 监听器 --> Bytes Throughput Over Time

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402261134311.png" alt="image-20240226113442268" style="zoom: 67%;" />

**作用**：用来监控服务端的性能资源指标的工具，包括cpu、内存、磁盘、网络等性能数据

**添加方法**：线程组 --> 监听器 --> PerfMon Metrics Collector

**注意**：使用之前需要在服务器端安装监听服务程序并启动

+ 下载安装包 ServerAgent-2.2.3.zip，链接地址：https://github.com/undera/perfmon-agent
+ 上传到服务器上，并解压 ServerAgent-2.2.3.zip
+ 启动，如果是 Windows 运行 startAgent.bat ，如果是 Linux 运行 startAgent.sh
+ 启动这个工具后，Jmeter 的插件 PerfMon Metrics Collector 就可以收集服务端的资源使用率，并在 Jmeter 中查看了

通常需要关注的资源指标有：CPU、内存、磁盘、网络

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402261154609.png" alt="image-20240226115442568" style="zoom: 67%;" />

除此之外还要其余一些指标的监听器，此处不再一一展示

# 13. 单接口测试脚本

**案例：在某商城项目中，有登录功能接口信息如下所示，请在 JMeter 完成测试**

请求网址：http://www.litemall360.com:8080/wx/auth/login
请求方法：POST
请求头：Content-Type: application/json;charset=utf-8
请求体：{"username":"user123","password":"user123"}
返回数据：

```json
{
    "errno": 0,
    "data": {
        "userInfo": {
            "avatarUrl": "",
            "nickName": "user123"
        },
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ0aGlzIGlzIGxpdGVtYWxsIHRva2VuIiwiYXVkIjoiTUlOSUFQUCIsImlzcyI6IkxJVEVNQUxMIiwiZXhwIjoxNzA4OTY1MTMwLCJ1c2VySWQiOjEsImlhdCI6MTcwODk1NzkzMH0.s4B_e-pUiQc0Fd1SIkBom5F7sY995oDswyvmbZTfj_0"
    },
    "errmsg": "成功"
}
```

测试计划结构如下：

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262245379.png" alt="image-20240226224536273" style="zoom:80%;" />

+ 信息头管理

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262247982.png" alt="image-20240226224722950" style="zoom:80%;" />

+ 请求默认值（存放公共信息）

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262248009.png" alt="image-20240226224807971" style="zoom:80%;" />

+ 响应断言 - 状态码

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262251608.png" alt="image-20240226225104563" style="zoom: 67%;" />

+ JSON 断言 - errmsg

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262253121.png" alt="image-20240226225314084" style="zoom: 67%;" />

+ JSON 断言 - nickName

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262256719.png" alt="image-20240226225614684" style="zoom:80%;" />

+ 观察查看结果树中的 `HTTP请求 - 登录` ，绿色则代表断言通过

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402262300422.png" alt="image-20240226230058391" style="zoom:80%;" />

# 14. 业务性能测试

通过将请求放入事务控制器（`线程组 --> 逻辑控制器 --> 事务控制器` ）可以将整体业务作为单位进行指标统计

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402291549794.png" alt="image-20240229154908733" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202402291550204.png" alt="image-20240229155000148" style="zoom:80%;" />

# 15. 稳定性测试

+ 确定出稳定运行的所有业务操作
+ 根据运营数据，分析出每个业务操作对应的虚拟用户数

**稳定性测试执行**：

+ 所有脚本同时执行（解除前后依赖）
+ 每个脚本都是一个业务/事务（使用事务控制器封装）
+ 按照要求设置虚拟用户数和运行时间
+ 执行稳定性测试并监控

如以下要求

| 同时稳定运行的业务操作 | 每个业务对应的虚拟用户数 |
| :--------------------: | :----------------------: |
|          登录          |            40            |
|        进入首页        |            30            |
|          搜索          |            20            |
|      获取商品详情      |            30            |
|       加入购物车       |            20            |
|      查看我的订单      |            30            |
|      完整下单业务      |            30            |

# 16. 性能测试分析与调优

**步骤**

+ 确定问题。根据性能测试结果分析 BUG —— 测试人员职责
+ 分析问题。分析问题产生的原因 —— 开发人员职责
+ 给出解决方案。可以是修改软件配置、增加硬件资源配置、修改代码等 —— 开发人员职责
+ 验证解决方案。—— 测试人员回归测试
+ 分析验证结果 —— 既要保证有问题的指标得到解决，又要保证其他指标没有出现新问题

**注意**：性能分析和调优需要经过很多讨论，才能最终解决问题

**性能问题可能产生的原因**

+ 服务器的资源

  + CPU 瓶颈：CPU已压满（接近100%），通常其他指标的拐点出现的时刻是否与CPU压满的时刻基本一致
  + 内存瓶颈：内存不足时，操作系统会使用虚拟内存，从虚拟内存读取数据，影响处理速度
  + 磁盘 I/O 瓶颈：磁盘 I/O 成为瓶颈时，会出现磁盘 I/O 繁忙，导致交易执行时在 I/O 处等待
  + 网络带宽：如果传递的数据包过大，超过了带宽的传输能力，就会造成网络资源竞争，导致 TPS 上不去

+ JVM 瓶颈分析

  + JVM内存：内存申请没有及时释放，造成内存泄漏

    > Java 虚拟机在执行 Java 程序的过程中所管理的不同的内存数据区域。可简单分为：堆内存和非堆内存
    >
    > + 堆内存：主要存放用 new 关键字创建的对象，所有对象实例以及数组都在堆上分配，给开发人员使用的
    > + 非堆内存：保存虚拟机自己的静态数据，存放加载的 Class 类级别静态对象如类、方法等，给 JVM 自己使用的

+ 数据库瓶颈分析

  + 慢查询

    > MYSQL通过设置慢查询机制可以定位到执行速度低于所设置阈值的 SQL 语句，从而方便更好优化数据库系统性能

  + 数据库连接池设置过小，导致数据库连接出现排队

    > 数据库连接池是负责分配、管理和释放数据库连接的机制，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个，其可以提高对数据库操作的性能

  + 数据库出现死锁

+ 程序内部实现机制 —― 开发人员编写的代码分析

+ 压测机

  > JMeter 单机负载能力有限，如果需要模拟用户请求数超过其负载极限，也会导致 TPS 上不去

# 17. 性能测试报告

+ 测试过程回顾
+ 问题的分析
+ 测试结论
+ 经验和教训

# 18. 性能指标

+ TPS (transaction per second) ：服务端每秒处理请求的数量

  > 最直观的反映了系统的处理能力，是重要的性能指标之一。TPS 是由 RPS 、网络延迟 、服务端本身的处理速度三个因素决定

+ RPS (request per second) ： 测试工具每秒发送请求的数量

  > RPS 和 TPS 概念不同，前者是每秒发出的请求数量，后者是处理完成的请求数量

+ EPS (error per second) 是 服务端 每秒处理出错的数量，也包含在TPS中

  > 一个性能表现良好的系统，EPS 应该一直为0

+ TOPS (timeout per second) 是 服务端每秒处理超时的数量

  > 一个性能表现良好的系统，TOPS 应该一直为0
