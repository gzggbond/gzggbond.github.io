---
title: Scapy库使用手册
date: 2026-03-19 9:27:50
excerpt: Scapy是一个功能强大的Python库，用于数据包操作、发送、嗅探和协议解析，它将大量协议（如IP、TCP、UDP、ICMP、ARP等）封装为对象，用户可按需封装构造自定义数据包，非常适合网络测试、漏洞验证、教学和快速原型开发
tags:
  - 网络通信
categories:
  - [Python,网络通信]
---

# 1. 简介

Scapy是一个功能强大的Python库，用于数据包操作、发送、嗅探和协议解析，它将大量协议（如IP、TCP、UDP、ICMP、ARP等）封装为对象，用户可按需封装构造自定义数据包，非常适合网络测试、漏洞验证、教学和快速原型开发。

当我们想要组一个Scapy报文时，就先要了解该报文的结构，Scapy官方文档提供了部分的API，可能不够全面，或者很难查询到我们想要的报文结构。此时，我们可以通过Wireshark把想要的报文抓下来（保存为pcap），再通过scapy去解析，然后重新组包发送，或者修改现有报文再重放。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603201056625.png" alt="image-20260320105636553" style="zoom:50%;" />

通过筛选器过滤需要的数据包后保存报文，**wireshark抓包筛选规则**如下：

1. 过滤项：ip.src（源IP）、tcp.port（TCP端口）、frame.len（帧长度）
2. 比较操作符： `==`（等于）、`>`（大于）、`!=`（不等于）
3. 逻辑操作符：and（&&）、or（||）、not（!）、xor（^^）
4. 特殊操作字符：contains、matches（正则匹配）、in（`tcp.port in {80, 443, 8080}`）

```python
from scapy.all import *
# 使用show()函数，把抓取的报文读出来
pkg = rdpcap("WireShark捕获示例.pcap")[-2]
# 打印报文结构
pkg.show()
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603201106646.png" alt="image-20260320110608537" style="zoom:50%;" />

# 2. 调试辅助函数

1. 读取真实数据包`rdpcap`

   > 将真实数据包读取存储为对象

   ```python
   from scapy.all import *
   # 使用show()函数，获取文件第1条报文信息
   pkg = rdpcap("WireShark捕获示例.pcap")[0]
   # 与输出pkg.summary()效果一致
   print(pkg)
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603201125460.png" alt="image-20260320112535428" style="zoom:50%;" />

   报文由数据帧、IP数据报、UDP协议，数据部分data，构成数据包

2. 打印数据包组成`show()`

   > 我们需要构建精准报文时，不清楚需要填写哪些字段和内容，可以使用show()函数打印真实报文，再模仿构造

   ```python
   # 效果如1.简介所示
   pkg.show()
   ```

3. 查看协议头部字段及默认值`ls()`

   > 不传入参数时查看所有支持的协议

   ```python
   from scapy.all import *
   from scapy.layers.inet import *
   print(ls(UDP))
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603201401232.png" alt="image-20260320140121177" style="zoom: 50%;" />

4. 列出Scapy所有内置函数`lsc()`，使用`help(function)`查看函数详细说明

   > 如发包/嗅探/ARP欺骗等，如下是部分函数说明

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603201409301.png" alt="image-20260320140910250" style="zoom:50%;" />

# 3. 自定义组包

> Scapy将网络通信各层封装为对象，用户按照实际情况构建各层数据，通过`/`符号组包（符号左侧为外层，右侧为内层），根据需要进行叠加即可

```python
from scapy.all import rdpcap,Raw
from scapy.layers.inet import IP,UDP
from scapy.layers.l2 import Ether
# 数据帧【目标MAC地址，源MAC地址，上层协议是IPv4】
eth_pkg = Ether(src="84:7b:57:bd:63:6b",dst="6c:e2:d3:3e:5d:01",type ="IPv4")
# IP数据报【源IP地址，目的IP地址，上层协议是UDP】
ip_pkg = IP(src="192.168.222.38",dst="61.241.55.63",proto="udp")
# UDP报文【源端口，目的端口】
udp_pkg = UDP(sport=58455,dport=8000)
# 负载数据
data = Raw(load="hello,this is a package!")
# 组包
pkg = eth_pkg / ip_pkg / udp_pkg / data
print(pkg.show())
```

# 4. 发送包

在Scapy中，`send()`、`sendp()`、`sr()`、`sr1()`和`srp()`是用于发送和接收数据包的核心函数。它们的主要区别在于工作的网络层（第2层或第3层）以及是否等待响应。以下是对这些函数的详细总结：

## 4.1 send()
- **作用**：发送第3层（网络层）数据包（如IP、ICMP等）。
- **特点**：
  - 自动处理路由和ARP解析（根据目标IP决定下一跳MAC地址）。
  - 不等待响应，只负责发送。
  - 通常用于发送不需要回复的包，或者结合其他方式捕获响应。
- **常用参数**：
  - `iface`：指定发送接口（若不指定则根据路由表自动选择）。
  - `inter`：相邻两个包发送的间隔时间（秒）。
  - `loop`：是否循环发送。
  - `count`：发送次数。
- **示例**：
  ```python
  send(IP(dst="1.2.3.4")/ICMP())
  ```

## 4.2 sendp()
- **作用**：发送第2层（链路层）数据包（如Ethernet帧）。
- **特点**：
  - 直接构造完整的以太网帧，需要指定源和目标MAC地址（或使用广播）。
  - 不涉及路由，必须指定接口（`iface`）或在数据包中明确设置链路层信息。
  - 常用于构造自定义的二层协议包（如ARP、STP等）。
- **常用参数**：与`send()`类似，但通常必须指定`iface`。
- **示例**：
  ```python
  sendp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="192.168.1.1"))
  ```

## 4.3 sr()
- **作用**：发送第3层数据包并接收响应（SendandReceive）。
- **特点**：
  - 发送一个或多个数据包，等待匹配的应答包（基于IP层关联，如ICMP请求的应答）。
  - 返回两个列表：`(answered,unanswered)`。
    - `answered`：包含(发送包,接收包)的二元组列表。
    - `unanswered`：未收到响应的发送包列表。
  - 常用于网络探测（如ping扫描、traceroute等）。
- **常用参数**：
  - `timeout`：等待响应的最长时间（秒）。
  - `retry`：重试次数（针对无响应的包）。
  - `multi`：是否收集多个响应（默认只收第一个匹配的）。
- **示例**：
  ```python
  ans,unans=sr(IP(dst="192.168.1.1")/ICMP())
  ```

## 4.4 sr1()
- **作用**：发送第3层数据包，只返回第一个收到的响应包。
- **特点**：
  - 是`sr()`的简化版，直接返回接收到的第一个数据包（若无响应则返回`None`）。
  - 适用于只需要一个回复的场景（如发送一个ICMP回显请求，获取一个回显应答）。
- **常用参数**：同`sr()`。
- **示例**：
  ```python
  p=sr1(IP(dst="8.8.8.8")/ICMP())
  ifp:
      p.show()
  ```

## 4.5 srp()
- **作用**：发送第2层数据包并接收响应（与`sr()`对应，但工作在链路层）
- **特点**：
  - 发送和接收均基于链路层（以太网帧）
  - 返回格式与`sr()`相同：`(answered,unanswered)`
  - 常用于二层探测（如ARP扫描）
- **常用参数**：需指定`iface`。
- **示例**：
  ```python
  ans,unans = srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="192.168.1.0/24"),timeout=2)
  ```

## 总结对比表

| 函数      | 工作层 | 是否接收响应 | 返回内容                         | 典型用途                    |
| --------- | ------ | ------------ | -------------------------------- | --------------------------- |
| `send()`  | 第3层  | 否           | 无                               | 发送IP包，无需回复          |
| `sendp()` | 第2层  | 否           | 无                               | 发送自定义以太网帧          |
| `sr()`    | 第3层  | 是           | (answered_list, unanswered_list) | 三层请求-响应交互（如ping） |
| `sr1()`   | 第3层  | 是           | 单个响应包（若无则为None）       | 只需一个响应的三层请求      |
| `srp()`   | 第2层  | 是           | (answered_list, unanswered_list) | 二层请求-响应（如ARP扫描）  |

## 注意事项
- **路由与接口**：`send()`和`sr()`/`sr1()`会根据目标IP自动选择接口和MAC地址（通过ARP表），而`sendp()`和`srp()`需要手动指定接口和链路层地址。
- **超时与重试**：在接收函数中合理设置`timeout`和`retry`可提高可靠性。

选择哪个函数取决于你的目标：是仅发送还是需要交互，以及工作在IP层还是链路层。

# 5. 监听/嗅探sniff

`sniff()` 函数用于实时捕获网络数据包或读取离线pcap文件，能直接与Scapy的数据包处理能力结合

> 对于读取离线数据包而言，`sniff`提供了一种更高效的处理方式，它模拟了实时抓包的行为，但数据源是文件。它逐个读取数据包，可以一边读一边处理，处理完的包如果没有被保存就可以被垃圾回收掉，内存占用极低，可处理超大文件，支持在读取时应用BPF过滤器

```python
from scapy.all import *
packets = sniff(count=10)          # 捕获 10 个包，返回列表
sniff(iface="eth0", prn=lambda x: x.summary())  # 每捕获一个包，调用 prn 函数打印摘要
```

+ iface：指定监听的网卡接口（如 `"eth0"`、`"wlan0"`）

  > 通过show_interfaces()可以查看当前的网卡接口名称

+ count：捕获数据包的数量，达到后停止捕获。默认持续捕获直到超时或被中断

+ timeout：捕获超时时间（秒）。超时后停止捕获，即使未达到 `count`

+ filter：BPF (Berkeley Packet Filter) 过滤表达式，如 `"tcp port 80"`







