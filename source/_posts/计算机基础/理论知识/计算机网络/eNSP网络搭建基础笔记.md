---
title: eNSP网络搭建基础笔记
date: 2022-11-17 16:34:23
excerpt: 本文是《HCNA网络技术实验指南》的实战笔记
tags:
  - 计算机网络
categories:
  - [理论知识,计算机基础,计算机网络]
---

# 1. eNSP基础 #

> **VRP**(Versatile Routing Platform，**通用路由平台**)，是华为路由器、交换机、防火墙等网络设备的通用网络操作系统，是一个类Linux系统
>
> **eNSP**(enterparise Network Simulation Platform)是华为提供的一款图像化网络设备仿真平台，主要对企业级路由器(`AR`路由器)、交换机(3700、5700)、无线设备、终端设备提供仿真模拟，为广大用户学习华为网络技术提供一个真实的操作平台
>
> **CLI**(Command-Line Interface，**命令行界面**)是指可在设备上输入可执行指令的界面，通常不支持鼠标，用户通过键盘输入指令，设备接收指令后，予以执行
>
> 可以简单认为**==华为的VRP软件就是eNSP，CLI是操作用户与VRP进行交互的工具，用户通过CLI输入命令配置和管理VRP==**

## 命令行视图 ##

> 由于`VRP`命令很多，因此对于不同类别的命令需要进行分组管理，这就是视图。
>
> **用户视图**：查看运行状态或其他参数<br>**系统视图**：配置设备的系统参数等<br>**接口视图**：配置接口参数<br>**协议视图**：配置路由协议
>
> 进入`CLI`时默认处于用户视图，通过`system-view`命令进入系统视图，根据需要可以进入接口视图与协议视图，用户视图不可直接进入接口视图与协议视图，接口视图与协议视图可互相进入。`quit`命令用于返回上一级视图

<img src="https://s2.loli.net/2022/03/16/D86UJhrCHmysYaS.png" alt="image-20220316162019563" style="zoom:50%;"/>

## 常用快捷键 ##

```java
//CLI界面快捷键
Tab				//智能补全当前命令
Ctrl+U			//删除当前命令
Ctrl+Z			//返回用户视图
Ctrl+X			//删除光标左侧所有字符
方向键上/下		//显示历史缓冲区中的上/下一条命令
Ctrl+G			//查看当前设备配置命令(等同于display current-configuration)
Ctrl+C			//提前终止当前正在执行的任务(当然最基本的复制功能也有)
Ctrl+L			//查看路由表(只能看Router系列的路由，3260系列无效)
Ctrl+W			//删除光标左侧的一个字符串
Ctrl+Tab		//切换到相邻的CLI窗口
?				//一些命令只记得部分时可以在忘记的地方加?，系统会提示了类似命令
//eNSP界面快捷键
Ctrl+L			//显示或隐藏工具栏
Ctrl+R 			//右工具栏显示接口连接信息
Ctrl+Alt+A		//启动设备
Ctrl+Alt+C		//停止设备
Ctrl+Alt+D		//数据抓包
Ctrl+Alt+P		//调色板
Ctrl+Alt+E		//设置
Ctrl+N			//新建拓扑
```

## 常用语句 ##

### 系统视图 ###

```java
undo info-center enable		//此命令可以拒绝界面随机弹出时间
save						//当对设备进行配置完毕后一定记得保存，否则设备关闭后开启配置消失
sysname 新名字				  //重命名设备名
```

### 用户视图 ###

```java
reset saved-configuration		//清空此设备保存的配置文件
reboot							//重新启动设备后才能清空配置
save 配置文件名.cfg				//将配置文件进行命名保存，下次有需要可直接载入
startup saved-configuration 配置文件名.cfg		//指定启动配置文件名，接着save再reboot即可
display vlan summary		//查看VLAN简要信息
display ip interface brief	//查看路由器接口信息    
```

### ping命令 ###

```java
ping a -t		//持续ping a
tracert a		//追逐到达a地址经历的网关
```

# 2. ARP与Proxy ARP #

> `ARP(Address Resolution Protocol)`【网络层】是用来将**`IP`地址解析为`MAC`地址**的协议，其表项可分为动态和静态两种类型
>
> `Proxy ARP`，即代理`ARP`，当主机上没有配置默认网关地址(即不知道如何到达本地网络的网关设备)，可以发送一个广播`ARP`请求(请求目的主机的`MAC`地址)，使**具备`Proxy ARP`功能的路由器**收到这样的请求并确认请求地址可达后，会使用自身的`MAC`地址作为该`ARP`请求的回应，使得处于不同物理网络的同一网段的主机之间可以正常通信

## 实验目的 ##

+ 理解`ping`的工作原理【`ARP`协议探路再发送`ICMP`报文】
+ 通过抓包方式看清`ARP`工作过程

## 实验拓扑图 ##

![1](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623421.png)

## 实验步骤 ##

1. 配置`R1`两个接口的`IP`地址并且保证直连网络之间可以`ping`通

   ```java
   R1：
   <Huawei>system-view		//进入系统视图
   [Huawei]undo info-center enable	//关闭消息提醒
   [Huawei]interface g0/0/1	//进入g0/0/1接口视图
   [Huawei-GigabitEthernet0/0/1]ip address 10.1.1.254 24	//设置此接口的地址与掩码
   [Huawei-GigabitEthernet0/0/1]quit	//退出接口模式
   [Huawei]interface g0/0/2	//进入g0/0/2接口视图
   [Huawei-GigabitEthernet0/0/2]ip address 10.1.2.254 24	//设置此接口的地址与掩码
   [Huawei-GigabitEthernet0/0/2]quit	//退出接口模式
   ```

   配置完`R1`路由器，设置好`PC1、PC2`的`IP`地址与掩码后，让`PC1 ping PC2`，`PC1 ping g0/0/1`，`PC3 ping g0/0/2`，此**三者都能够`ping`通则第一个步骤成功**。

2. `ping`命令本质上是通过`ARP协议`找到`IP地址`对应的`MAC地址`进行访问，因此若映射发生错误，则即使`IP地址`正确也无法`ping`通，我们可以通过修改映射来验证这一点

   由于我们第一步曾经`ping`通过，所以`ARP`表记录了各个`PC`的`IP`地址与`MAC`地址映射

   ![2](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623069.png)

   ![3](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623104.png)

   ```java
   display arp all		//查看路由器ARP表
   arp static IP地址 MAC地址	//手动修改或添加ARP表内容
   ```

3. 当我们想`ping` 某个地址但是却不知道往哪条路径走时就会走向网关地址所在的路径，在此实验中由于没有为`PC`配置网关且它们分别处于两个广播域，所以此时`PC1 ping PC3`一定是不通的，我们可以通过抓包查看这个过程发生了什么

   ![4](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623787.png)

   在不为`PC`设置网关的情况下若想要使其`ping`成功，则需要为路由器启动`ARP`代理功能

   ```java
   [R1]interface g0/0/1	//进入R1端口
   [R1-GigabitEthernet0/0/1]arp-proxy enable	//为此端口启动ARP代理功能
   ```

   启动`ARP`代理后工作流程如下：`R1`收到`PC1`寻找`PC3`的`MAC`地址的`ARP`请求时先比对`ARP`表，若发现没有适合的`MAC`地址则再将此`IP`地址拿去比对自身的路由表。若路由表中有合适的路由则会回复本接口的`MAC`地址给`PC1`，此后`PC1`发送给`PC3`的数据都是先发送至此端口，接着比对路由表后再将其转运至真正目的地

   ![5](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623435.png)

   ![6](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171623831.png)

**ARP代理的方式会因为引入额外的路由器而延迟增大，并且存在着瓶颈问题，所以一般只作为临时解决方案使用。面对规模不大的网络，若是担心遭受ARP攻击，则最好能够手动配置ARP表内容**

# 3. VLAN基础 #

> `VLAN`的主要作用是划分广播域，避免数据报在数据链路层【二层】无限广播
>
> 针对实现`VLAN`这项功能，交换机端口有三种类型，关于此三类端口详情可看下文

[Access,Trunk与hybrid端口区别](https://blog.csdn.net/weixin_45488428/article/details/123638316?spm=1001.2014.3001.5501)

## 实验目的 ##

+ 了解`VLAN`的作用
+ 掌握`VLAN`创建的方法
+ 理解三种不同的交换机端口

## 实验拓扑图 ##

![1](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171628854.png)

## 实验步骤 ##

1. 配置各`PC`的`IP`地址与掩码信息，此时所有设备均是二层设备，在未设置`VLAN`的情况下同属于一个广播域，应当是全都可以相互`ping`通，我们对此进行验证【第一次`ping`可能出现不通的情况，这是因为广播帧还未收到回复，只需十秒后再试一次即可】

   ![2](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171628678.png)

2. 为交换机创建`VLAN 10`与`VLAN 20`，将`PC`与交换机的端口设置为`Access`，将交换机与交换机间的端口设置为`Trunk`，设置完成后`PC1 ping PC5`不通

   ```java
   L1:
   [Huawei]sysname L1
   [L1]vlan batch 10 20	//在交换机L1上创建VLAN 10与VLAN 20
   [L1]interface Ethernet 0/0/1	//进入接口e0/0/1
   [L1-Ethernet0/0/1]port link-type access //将此接口设置为Access
   [L1-Ethernet0/0/1]port default vlan 10	//此接口只转发属于VLAN 10的信息
   [L1-Ethernet0/0/1]quit
   [L1]interface Ethernet 0/0/2	//接口e0/0/2与e0/0/3同理
   [L1-Ethernet0/0/2]port link-type access 
   [L1-Ethernet0/0/2]port default vlan 10
   [L1-Ethernet0/0/2]quit
   [L1]interface Ethernet 0/0/3
   [L1-Ethernet0/0/3]port link-type access 
   [L1-Ethernet0/0/3]port default vlan 10
   [L1-Ethernet0/0/3]quit
   [L1]interface Ethernet 0/0/5
   [L1-Ethernet0/0/5]port link-type trunk //将此接口设置为Trunk	
   [L1-Ethernet0/0/5]port trunk allow-pass vlan all	//此接口允许所有VLAN数据通过
   [L1-Ethernet0/0/5]quit
   ```

   ```java
   L2:
   [L2]vlan batch 10 20	//在交换机L2上创建VLAN 10与VLAN 20
   [L2]interface Ethernet 0/0/1
   [L2-Ethernet0/0/1]port link-type access 
   [L2-Ethernet0/0/1]port default vlan 20
   [L2-Ethernet0/0/1]quit
   [L2]interface Ethernet 0/0/2
   [L2-Ethernet0/0/2]port link-type access 
   [L2-Ethernet0/0/2]port default vlan 20
   [L2-Ethernet0/0/2]quit
   [L2]interface Ethernet 0/0/5
   [L2-Ethernet0/0/5]port link-type trunk 
   [L2-Ethernet0/0/5]port trunk allow-pass vlan all
   [L2-Ethernet0/0/5]quit
   ```

   以上步骤完成后就将`PC1,PC2,PC3`划分在`VLAN 10`中，`PC4,PC5`则划分在`VLAN 20`中。此时`PC1 ping PC5`的整个流程如下：

   1. `PC1`数据包从自身`e0/0/1`端口发出，此时没有携带`VLAN`信息【默认是`VLAN 1`，我们为了方便学习将此种情况称为无`VLAN`】，接着进入`L1`的`e0/0/1`端口，此端口是`Access`端口且默认路由为`VLAN 10`，因此数据由此进入时会打上`VLAN 10`标签
   2. 交换机接收到此广播帧后，从除了接收端口外的其他端口广播出去，`PC2`与`PC3`均不是目标`IP`地址，因此没有给予回应，剩下的只有`e0/0/5`端口
   3. `e0/0/2`为`Trunk`端口，此时收到`PC1`发来带有`VLAN 10`标签的帧，我们未修改过此端口的`PVID`，因此默认为`1`。`1`与`10`不等，因此端口直接将带有`VLAN 10`的帧转发。
   4. `L2`的`e0/0/5`端口接收到带有`VLAN 10`的数据帧，此端口类型也为`Trunk`，因此直接接收此带有标签的数据帧
   5. 接着`L2`打算将此帧从剩余的两个端口广播出去，却发现帧标签为`VLAN 10`而非端口设置的`VLAN 20`，因此不进行转发。数据传输到此结束，`PC1`的数据包无法到达`PC5`，自然得不到响应
   
   ![3](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171628202.png)

# 4. 单臂路由实现VLAN间路由 #

> `VLAN`属于二层交换技术，因此若要实现`VLAN`间通信便需要来到第三层---网络层<br>单臂路由的原理是通过一台路由器，使`VLAN`间互通数据通过路由器进行三层转发。<br>单臂路由可以通过配置物理接口将其逻辑化为多个子接口，以便节约路由器上宝贵的物理接口资源。

##  实验目的 ##

+ 了解单臂路由工作原理
+ 了解配置了单臂路由后，数据是在网络中转发的过程

## 实验拓扑 ##

![1](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171630340.png)

## 实验步骤 ##

1. 配置交换机各端口类型，同时划分各端口所属`VLAN`

   ```java
   S1:
   <S1>system-view				//进入系统视图
   [S1]vlan batch 10 20 30		//在交换机S1上创建VLAN 10,VLAN 20,VLAN 30
   [S1]interface Ethernet 0/0/1	//进入e0/0/1接口
   [S1-Ethernet0/0/1]port link-type access 	//将此接口设置为Access类型
   [S1-Ethernet0/0/1]port default vlan 10		//此接口默认VLAN为VLAN 10
   [S1-Ethernet0/0/1]quit
   [S1]interface e0/0/2
   [S1-Ethernet0/0/2]port link-type access 	
   [S1-Ethernet0/0/2]port default vlan 20
   [S1-Ethernet0/0/2]quit
   [S1]interface g0/0/2		//进入g0/0/2接口
   [S1-GigabitEthernet0/0/2]port link-type trunk 	//将此接口设置为Trunk类型
   [S1-GigabitEthernet0/0/2]port trunk allow-pass vlan all	//此端口运行带有任何VLAN标签的															帧通过
   [S1-GigabitEthernet0/0/2]quit
   ```
   
   ```java
   S2:	//大部分与S1类似，此处不再注释
   <Huawei>system-view 		//进入系统视图
   [Huawei]sysname S2			//将此设备改名为S2
   [S2]undo info-center en		//取消消息提醒
   [S2]vlan batch 10 20 30		//在交换机S2上创建VLAN 10,VLAN 20,VLAN 30
   [S2]interface Ethernet 0/0/1
   [S2-Ethernet0/0/1]port link-type access 
   [S2-Ethernet0/0/1]port default vlan 30
   [S2-Ethernet0/0/1]quit
   [S2]interface g0/0/2
   [S2-GigabitEthernet0/0/2]port link-type trunk 
   [S2-GigabitEthernet0/0/2]port trunk allow-pass vlan all
   [S2-GigabitEthernet0/0/2]quit
   ```
   
   ```java
   S3:	//与S1,S2类似
   <Huawei>system-view 		
   [Huawei]sysname S3	
   [S3]vlan batch 10 20 30
   [S3]interface g0/0/1
   [S3-GigabitEthernet0/0/1]port link-type trunk 
   [S3-GigabitEthernet0/0/1]port trunk allow-pass vlan all
   [S3-GigabitEthernet0/0/1]quit
   [S3]interface g0/0/2
   [S3-GigabitEthernet0/0/2]port link-type trunk 
   [S3-GigabitEthernet0/0/2]port trunk allow-pass vlan all
   [S3-GigabitEthernet0/0/2]quit
   [S3]interface g0/0/3
   [S3-GigabitEthernet0/0/3]port link-type trunk 
   [S3-GigabitEthernet0/0/3]port trunk allow-pass vlan all
   [S3-GigabitEthernet0/0/3]quit
   ```
   
   到此处，各`VLAN`区域均划分完毕，此时各`PC`之间互相`ping`不通，我们希望通过设置单臂路由使其相互`ping`通
   
2. 将路由器`g0/0/1`接口逻辑化为三个子接口，分别作为三个`PC`的网关

   ```java
   [R1]interface g0/0/1.1
   [R1-GigabitEthernet0/0/1.1]ip ad	
   [R1-GigabitEthernet0/0/1.1]ip address 192.168.1.254 24	//设置子接口的IP地址与掩码
   [R1-GigabitEthernet0/0/1.1]dot1q termination vid 10	//子接口处接收VLAN 10数据时自动剥									离VLAN 10标签；当此处发送数据时则自动加上VLAN 10标签
   [R1-GigabitEthernet0/0/1.1]arp broadcast enable //开启后子接口才能发送ARP广播报文【普通										 接口默认就是开启的】，ping功能就是建立在ARP基础上
   [R1-GigabitEthernet0/0/1.1]quit
   [R1]interface g0/0/1.2
   [R1-GigabitEthernet0/0/1.2]ip address 192.168.2.254 24
   [R1-GigabitEthernet0/0/1.2]dot1q termination vid 20
   [R1-GigabitEthernet0/0/1.2]arp broadcast enable 
   [R1-GigabitEthernet0/0/1.2]quit
   [R1]interface g0/0/1.3
   [R1-GigabitEthernet0/0/1.3]ip address 192.168.3.254 24
   [R1-GigabitEthernet0/0/1.3]dot1q termination vid 30
   [R1-GigabitEthernet0/0/1.3]arp broadcast enable
   [R1-GigabitEthernet0/0/1.3]quit
   //当VLAN数据都能被剥离后【dot1q设置后...】，对于路由器而言整个二层网络就像没有设置VLAN一样，因此PC网段便成了直连路由，此时更新自己路由表
   ```

   完成此步骤后，各`PC`间便能跨`VLAN`相互`ping`通。以`PC2 ping PC3`为例分析整个过程

   ![2](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171630926.png)

1. `PC2`会广播一个`ARP请求`，询问网关第MAC地，请求首先经过`S1`的`e0/0/2`的`Access`接口进入并被打上`VLAN 20`的标签，`S1`将此`ARP请求`从其余端口广播出去，发现`e0/0/1`端口的默认路由为`VLAN 10`，因此此路不通，所以`ARP请求`最后往`g0/0/2`的`Trunk`端口发送出去并且仍然携带`VLAN 20`标签

2. 同理，`ARP请求`被`S3`的`g0/0/2`的`Trunk`接口接收并转发，`ARP请求`到`S2`时无法经由`e0/0/1`发送给`PC3`，因此此端口的默认路由为`VLAN 30`，与`ARP请求`所携带的`VLAN 10`标签不同，所以此路也不通路，最后`ARP请求`只能经由`S3`的`g0/0/1`端口发送出去

3. 包来到`R1`的`g0/0/1`端口，此端口已被逻辑化为`3`个子接口，此时路由器将此`ARP请求`的`VLAN 10`标签与`3`个子端口的`VID`进行比对，发现其中有个端口的`VID`正好为`VLAN 10`，因此将此`ARP请求`接收，同时剥离其`VLAN`标签，也就是说当包被路由器接收后不携带任何标签。同时`R1`通过此端口回复一个`ARP报文`【这就是端口要开始`ARP`广播的原因】用于告知此网关的`MAC地址`，`PC1`收到后即可按照此`MAC地址`直接进行发送数据至网关

4. 紧接着`R1`查看自身路由表，发现`PC3`正好在自己的路由表中，通过层层查询，最后经由`g0/0/1.3`转发出去，同时打上此端口的`VID`，即`VLAN 30`标签，数据经此来到`S3`

   ![3](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171630245.png)

5. 面对携带了`VLAN 30`的数据包，`S3`同样会从其余端口转发出去，包来到`S1`后发现标签与`e0/0/1`与`e0/0/2`不同，因此不进行转发；包来到`S2`处后发现标签正好与`e0/0/1`相同，因此去标签转发。`PC3`由此成功收到数据包，需要告知`PC1`自己已成收数据，同理需要发送`ARP请求`寻找自己的网关地址，最后来到`PC1`处，自此`ping`操作成功

   ![4](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171630568.png)

# 5. 生成树协议STP #

> STP是用来避免数据链路层出现逻辑环路的协议，使用BPDU传递网络信息计算出一根无欢的树状网络结构，并阻塞特定端口。在网络出现故障的时候，STP能快速发现链路故障并尽快找出另外一条路径进行数据传输

## 实验目的 ##

+ 理解STP的选举过程
+ 掌握修改交换机优先级的方法
+ 掌握修改端口开销值的方法

## 实验拓扑 ##

![5](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171634265.png)

## 实验步骤 ##

1. 查看各交换机的`MAC`地址【每人建立的拓扑`MAC`地址都可能不同，以自己本机为主】

   ```java
   S1:		//进入视图，修改设备名等步骤已省略
   [S1]display bridge mac-address 	//查看网桥MAC地址
   System bridge MAC address: 4c1f-ccae-6f38	//S1设备的MAC地址为4c1f-ccae-6f38
       
   S2:
   [S2]display bridge mac-address 
   System bridge MAC address: 4c1f-cc08-71e1	//S2设备的MAC地址为4c1f-cc08-71e1
       
   S3:
   [S3]display bridge mac-address 
   System bridge MAC address: 4c1f-cc63-288d	//S3设备的MAC地址为4c1f-cc63-288d
       
   S4:
   [S4]display bridge mac-address 
   System bridge MAC address: 4c1f-cc5c-2a40	//S4设备的MAC地址为4c1f-cc5c-2a40
   ```

   从本地拓扑来看，`MAC`地址的大小关系为：**`S2<S4<S3<S1`**

2. 在各交换机上开启`STP`服务并修改`STP`模式为普通生成树`STP`【华为设备默认启动`MSTP`模式】

   ```java
   //开启STP模式后等待大约30秒再进行下一步操作，因为生成树形成需要时间
   S1:
   [S1]stp enable 	//STP模式打开
   [S1]stp mode stp	//将STP模式设置为普通STP
       
   S2:
   [S2]stp enable 
   [S2]stp mode stp	
       
   S3:
   [S3]stp enable 	
   [S3]stp mode stp	
     
   S4:
   [S4]stp enable 	
   [S4]stp mode stp
   ```

3. 观察各个交换机的端口状态，并分析出各交换机和端口的地位

   ![6](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171634449.png)

   ![7](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171634189.png)

   ![image-20220325150845600](https://s2.loli.net/2022/03/25/ZlgJzwVA2onPIr5.png)

   ![image-20220325151038981](https://s2.loli.net/2022/03/25/bNkjTRl1z6JYMuE.png)

   易知，**根交换机为S2**

4. 根交换机在网络中相当重要，不恰当的根交换机会影响到整个网络的通信质量及数据传输，因此我们有时需要根据实际情况指定根交换机。根交换机选举依据是`桥ID`【交换机优先级[**先比较，默认为32768**]+`MAC`地址[**后比较**]】，数值越小越优先被选，因此我们可以通过修改交换机优先级的方式来更改根交换机。

5. 我们决定将**S1作为主根交换机，S2作为S1的备份交换机**，可以将`S1`优先级改为`0`【这样优先级一定是最高的】，`S2`优先级改为`4096`【这样优先级一定是第二高的，因为优先级必须是`4096`的倍数】

   ```java
   S1:
   [S1]stp priority 0	//将S1的优先级指定为0
   S2:
   [S2]stp priority 4096	//将S2的优先级指定为4096
   ```

   除了用修改优先级的方式指定根交换机，我们还可以直接进行语句指定

   ```java
   S1:
   [S1]stp root primary 		//将S1指定为根交换机
   s2:
   [S2]stp root secondary 		//将S2指定为备份交换机
   ```

6. 生成树在选举出根交换机后将在每台**非根交换机**上选举**根端口**。选举流程如下：

   1. 比较端口到根交换机的**路径开销**，小的当选

      ```java
      display stp interface 接口		//可通过此命令查看接口的路径开销
      ```

      ![image-20220325160150479](https://s2.loli.net/2022/03/25/dH8oNlUnDpSmqPw.png)

      ```java
      //手动配置路径开销可用以下命令
      [S4]interface e0/0/2	
      [S4-Ethernet0/0/2]stp cost 2	//将此端口到根交换机的开销值定为2	
      ```

      ![image-20220325160417686](https://s2.loli.net/2022/03/25/7HhyFvrDJ9XC8qu.png)

   2. 若路径开销相同，则比较端口对面交换机的`ID`，`ID`小的当选

   3. 若还未决出胜负，则接着比较对面交换机直连端口的`ID`，`ID`小的当选

7. 生成树协议选完根端口后将在**每个网段上**选举**指定端口**，规则与选根端口类似，接着将其余**未选到的端口**进行**阻塞**，防止形成回路

# 6. 配置STP定时器 #

> 普通生成树`STP`不能实现快速收敛，但是通过配置`STP`中诸如`Hello Time`定时器，`Max Age`定时器，`Forward Delay`定时器等参数可以使`STP`实现最快的拓扑收敛

**Hello Time定时器**：`Hello Time`为周期发送BPDU来维护生成树的稳定的时间，默认为2s

**Max Age定时器**：`BPDU`的最大生存时间，默认为`20`秒，交换机通过比较从上游交换机收到的`BPDU`中携带的`Message Age`【配置`BPDU`的生存时间，如果配置`BPDU`是根桥发出的，则`Message Age`为`0`，每经过一台交换机增加`1`】和`Max Age`来判断此`BPDU`是否超时。如果收到的`BPDU`超时，交换机会将该`BPDU`老化，同时阻塞接收该`BPDU`的接口，并开始发出以自己为根桥的`BPDU`，这种老化机制可以有效控制生成树的半径

**Forward Delay定时器**：链路故障会引发网络重新进行生成树计算，如果新选出的根端口和指定端口立刻就开始数据转发的话，可能会造成临时环路。为此，`STP`采用了一种端口状态迁移机制，新选出的端口要经过两倍的`Forward Delay`【默认`15s`】延时后才能进入转发状态。这个延时保证了新的配置消息传遍整个网络，使所有参与`STP`计算的交换都能正确知晓网络状态，从而防止了临时环路的产生

**==在根交换机上配置的计时器内容将作为整个生成树内所有交换机的内容==**

## 实验目的 ##

+ 理解`STP`中定时器的作用
+ 掌握`STP`定时器的配置命令
+ 掌握查看`STP`定时器的生效方法
+ 理解`STP`定时器的最佳设置方法

## 实验拓扑 ##

![image-20220325165744741](https://s2.loli.net/2022/03/25/EL4wC5uzOS7mUer.png)

## 实验步骤 ##

1. 确保各`PC`间能相互`ping`通

2. 在4台交换机上配置使用STP，并配置S1为该二层网络的根交换机，S2为备份交换机。配置完成后查看各定时器默认值

   ```java
   S1:
   [S1]stp enable 		//在S1上开启STP
   [S1]stp mode stp	//将STP模式设置为普通STP
   [S1]stp root primary 	//将S1设置为根交换机
   S2:
   [S2]stp enable 
   [S2]stp mode stp
   [S2]stp root secondary	//将S2设置为备份交换机
   S3:
   [S3]stp enable 
   [S3]stp mode stp
   S4:
   [S4]stp enable 
   [S4]stp mode stp
   ```

   ![image-20220325171908442](https://s2.loli.net/2022/03/25/1Sprxw2BHvnV379.png)

   可以看出，默认情况下`BPDU`每`2`秒发送一次，最大老化时间为`20s`，转发延迟为`15s`，最大传递跳数为`20`跳。

   `Config Times`标识的是当前设备配置的计时器，而`Active Times`标识的是正在生效的计时器，一般情况下二者相同。

3. 一般不建议直接修改定时器参数值，因为他们之间环环相扣，随意修改往往容易发生错误；建议直接设置生成树的直径【在根交换机上配置】，交换机会根据网络直径自动计算出`3`个时间参数的最优值

   ```java
   [S1]stp bridge-diameter 3	//将以S1为根交换机的生成树直接设置为3
   ```

   ![image-20220325173330631](https://s2.loli.net/2022/03/25/Q4IbhuiGELv7fRD.png)

# 7. RSTP基础配置 #

> `RSTP`对原有的`STP`协议进行了更加细致的修改和补充

`RSTP`增加了两种端口角色：**Alternate端口**和**Backup端口**

+ **Alternate端口**就是由于学习到其他网桥发送的配置`BPDU`报文而阻塞的端口，`Alternate`**作为根端口的备份端口**，提供了另一条从指定桥到根的可切换路径。
+ **Backup端口**就是由于学习到自身发送的配置`BPDU`报文而阻塞的端口，`Backup`端口作为**指定端口的备份**，提供了另一条从根桥到相应网段的备份通路

`RSTP`把原来的`5`种状态缩减为`3`种，根据端口是否转发用户流量和学习`MAC`地址来划分

+ **Discarding状态**：不转发用户流量也不学习`MAC`地址
+ **Learning状态**：不转发用户流量但是学习`MAC`地址
+ **Forwarding状态**：转发用户流量且学习`MAC`地址

`RSTP`的快速收敛机制可分为以下三种

+ **Proposal/Agreement机制**：当一个端口被选举成为**指定端口**之后，在`STP`中，该端口至少要等待一个`Forward Delay (Learning)` 时间才会迁移到`Forwarding`状态。而在RSTP中，此端口会先进入`Discarding`状态，再通过此机制快速进入`Forwarding`状态，这种机制必须在**点到点全双工链路**上使用
+ **根端口快速切换机制**：如果网络中一个根端口失效，那么网络中最优的`Alternate`端口将成为根端口，进入`Forwarding`状态。因为通过这个`Alternate`端口连接的网段上必然有个指定端口可以通往根桥
+ **边缘端口的引入**：在`RSTP`中，如果某一个指定端口位于整个网络的边缘，即不再与其他交换设备连接，而是直接与终端设备直连，这种端口叫做**边缘端口**。边缘端口不接收处理配置`BPDU`，不参与`RSTP`运算，可以由`Disable`直接转到`Forwarding`状态且不经历时延。

## 实验目的 ##

+ 掌握`RSTP`的基本配置
+ 掌握`RSTP`的边缘端口的应用
+ 理解`RSTP`备份端口

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/03/26/q4baChvSMEFxVtO.png" alt="image-20220326101246010" style="zoom:50%;" />

## 实验目的 ##

1. 确保`PC1`与`PC2`可以相互`ping`通

2. `S1`与`S2`是核心交换机，功能更为强大，因此将交换机配置为RSTP模式后手动将`S1`设置为根交换机，`S2`为备份交换机

   ```java
   S1:
   [S1]stp enable 		//启动STP
   [S1]stp mode rstp	//将STP模式设为RSTP
   [S1]stp root primary 	//将S1设置为根交换机
   S2:
   [S2]stp enable 
   [S2]stp mode rstp
   [S2]stp root secondary //将S2设置为备份交换机
   S3:
   [S3]stp enable 
   [S3]stp mode rstp
   S4:
   [S4]stp enable 
   [S4]stp mode rstp
   ```

3. `S2`的`g0/0/1`是根端口，其他所有端口是指定端口。如果`S2`的根端口断掉了，`S2`会选择把其他到达根交换机的端口置成根端口。`RSTP`收敛比较快，端口`g0/0/2`会快速协商成为新的根端口，协商期间端口是`Discarding`状态，协商结束后端口为`Forwarding`状态，这个过程用时非常短，这就是`RSTP`收敛快的一个表现

   我们以`S2`为例，验证`P/A`机制【即指定端口身份发生变化时即刻由`Fowrding-->Discarding`或者`Discarding-->Forwarding`，不经历`Learning`状态】。下图为`S2`各端口连接正常时的状态

   ![image-20220325211220094](https://s2.loli.net/2022/03/25/UbkcvzNnPQFCoK6.png)

   我们手动关闭根端口再查看当前`S2`各端口状态【动作一定要快，不然观察不到中间现象】

   ![image-20220325212116821](https://s2.loli.net/2022/03/25/Eaikl62wYzd5Qo3.png)

   接着我们再重新打开`g0/0/1`端口，查看`g0/0/2`端口的变化

   ![image-20220325213856261](https://s2.loli.net/2022/03/26/quwUbM2XBCYrvSf.png)

   由此我们可以得证在`RSTP`中`P/A`机制的存在

4. 生成树的计算主要发生在交换机互连的链路之上，而连接`PC`的端口没有必要参与生成树计算，为了优化网络，可以将交换机上连接`PC`的接口配置为**边缘端口**

   ![image-20220326094204137](https://s2.loli.net/2022/03/26/ufh6ZENMOlib2Yr.png)

5. 按照前面的说法，`Alternate`端口是根端口的备份，`Backup`端口是指定端口的备份，我们通过关闭交换机上的某一接口来验证这一点

   ![image-20220326100031704](https://s2.loli.net/2022/03/26/pAzTD2OV59kaXgu.png)

   接着通过`S4`验证`Alternate`端口

   ![image-20220326100506088](https://s2.loli.net/2022/03/26/sV54dctNPbCyDiq.png)

# 8. MSTP基础配置 #

> `STP`与`RSTP`存在同一缺陷，即由于局域网内所有`VLAN`共享一棵生成树，链路被阻塞后将不承载任何流量，造成带宽浪费，因此无法再`VLAN`间实现数据流量的负载均衡，还有可能造成部分`VLAN`报文无法转发

通过`MSTP`把一个交换网络划分成多个域【`MSTP`域】，每个域内形成多颗生成树【多生成树实例`MSTI`】，生成树之间彼此独立。`MSTP`通过设置`VLAN`映射表【`VLAN`与`MSTI`的对应关系表】把`VLAN`和`MSTI`联系起来，每个`VLAN`只能对应一个`MSTI`，而一个`MSTI`可以对应多个`VLAN`

## 实验目的 ##

+ 掌握`MSTP`的基础配置
+ 掌握配置`MSTP`多实例的方法
+ 掌握配置`MSTP`实现流量分担的方法
+ 理解`MSTP`与`STP`、`RSTP`的区别

## 实验拓扑 ##

**要确保S1的MAC地址为三个交换机中的最小值，否则S1当选不了根交换机，MSTP的作用也就不能直观体现出来**

可以在用户视图使用`display bridge mac-address`命令查询各交换机的`MAC`地址

![image-20220327093329636](https://s2.loli.net/2022/03/27/glyfrc3Mpks8hLC.png)

## 实验步骤 ##

1. 配置各`PC`的`IP`地址与掩码，在各交换机上创建`VLAN 10`与`VLAN 20`，同时将`PC`划分进不同的`VLAN`

   ```java
   S1:
   <Huawei>system-view			//进入系统视图
   [Huawei]undo info-center enable		//关闭信息提醒功能
   [Huawei]sysname S1			//将设备命名为S1
   [S1]vlan batch 10 20		//在S1上创建vlan 10与vlan 20
   [S1]interface e0/0/1		//进入1号接口
   [S1-Ethernet0/0/1]port link-type trunk	//将1号口配置为Trunk口
   [S1-Ethernet0/0/1]port trunk allow-pass vlan all	//允许所有VLAN数据通过
   [S1-Ethernet0/0/1]quit
   [S1]interface e0/0/2
   [S1-Ethernet0/0/2]port link-type trunk
   [S1-Ethernet0/0/2]port trunk allow-pass vlan all
   [S1-Ethernet0/0/2]quit
   [S1]interface Ethernet0/0/3		
   [S1-Ethernet0/0/3]port link-type access //将其配置为Access口
   [S1-Ethernet0/0/3]port default vlan 10	//仅允许VLAN 10数据通过
   [S1-Ethernet0/0/3]quit
   ```

   ```ja
   S2:
   <Huawei>sys
   [Huawei]undo info-center enable
   [Huawei]sysname S2
   [S2]vlan batch 10 20
   [S2]interface e0/0/1
   [S2-Ethernet0/0/1]port link-type trunk 
   [S2-Ethernet0/0/1]port trunk allow-pass vlan all
   [S2-Ethernet0/0/1]quit
   [S2]interface e0/0/2
   [S2-Ethernet0/0/2]port link-type trunk 
   [S2-Ethernet0/0/2]port trunk allow-pass vlan all
   [S2-Ethernet0/0/2]quit
   [S2]interface e0/0/3
   [S2-Ethernet0/0/3]port link-type access 
   [S2-Ethernet0/0/3]port default vlan 20
   [S2-Ethernet0/0/3]quit
   ```

   ```java
   S3:
   <Huawei>sys
   [Huawei]undo info-center enable
   [Huawei]sysname S3
   [S3]vlan batch 10 20
   [S3]interface e0/0/1
   [S3-Ethernet0/0/1]port link-type trunk 
   [S3-Ethernet0/0/1]port trunk allow-pass vlan all
   [S3-Ethernet0/0/1]quit
   [S3]interface e0/0/2
   [S3-Ethernet0/0/2]port link-type trunk 
   [S3-Ethernet0/0/2]port trunk allow-pass vlan all
   [S3-Ethernet0/0/2]quit
   [S3]interface e0/0/3
   [S3-Ethernet0/0/3]port link-type access 
   [S3-Ethernet0/0/3]port default vlan 10
   [S3-Ethernet0/0/3]quit
   [S3]interface e0/0/4
   [S3-Ethernet0/0/4]port link-type access 
   [S3-Ethernet0/0/4]port default vlan 20
   [S3-Ethernet0/0/4]quit
   ```

2. 华为设备默认开启`MSTP`，因此不需要我们再手动进行设置，我们直接查看各交换机的生成树情况

   ![image-20220327094130774](https://s2.loli.net/2022/03/27/rjPdZ4bIvl8TQAF.png)

   ![image-20220327094339417](https://s2.loli.net/2022/03/27/fqeQoUsANLv6VCG.png)

   ![image-20220327094626454](https://s2.loli.net/2022/03/27/1ZVcCJHnht58uop.png)

   三台交换机上`MSTID`目前都为`0`，即在默认情况下，所有`VLAN`都处于`MSTP`的实例`0`中。在`MSTP`的单个实例中，选举规则和`RSTP`一致，端口角色状态也与`RSTP`一致

3. 通过上述端口状态可知，当各个端口都正常连接时，`S2`与`S3`之间链路处于闲置状态。`PC1 ping PC2`时，数据报经由`S1-->S3`再到`PC3`；`PC3 ping PC4`时，数据报经由`S2-->S1-->S3`【可通过抓包验证】。我们**希望`PC3 ping PC4`能够走`S2-->S3`这条路，而`PC1 ping PC2`时候继续走`S1-->S3`这条路**，有效利用链路资源，为此可以通过配置`MSTP`多实例实现

   `MSTP`网络由一个或多个`MST`域组成，每个`MST`域中可以包含一个或多个`MSTI`之间的映射关系，默认情况下所有`VLAN`都映射到`MSTI 0`中。`MSTI`间相互独立。

   我们将`MST`域名设为`"huawei"`；修订级别设为`1`；指定`VLAN 10`映射到`MSTI 1`，`VLAN 20`映射到`MSTI 2`。**同一`MST`域中必须具有相同域名、修订级别及`VLAN`到`MSTI`的映射关系**

   ```java
   //在同一MST域中，必须有相同域名，修订级别及VLAN到MSTI映射关系
   S1:
   [S1]stp region-configuration 	//进入MST域视图
   [S1-mst-region]region-name huawei	//将域名设为huawei
   [S1-mst-region]revision-level 1		//将修订级别设为1
   [S1-mst-region]instance 1 vlan 10	//在域中建立实例1与VLAN 10的映射关系
   [S1-mst-region]instance 2 vlan 20	//在域中建立实例2与VLAN 20的映射关系
   [S1-mst-region]active region-configuration //激活MTP域配置
       
   S2:
   [S2]stp region-configuration 
   [S2-mst-region]region-name huawei
   [S2-mst-region]revision-level 1
   [S2-mst-region]instance 1 vlan 10
   [S2-mst-region]instance 2 vlan 20
   [S2-mst-region]active region-configuration 
       
   S3:
   [S3]stp region-configuration 
   [S3-mst-region]region-name huawei
   [S3-mst-region]revision-level 1
   [S3-mst-region]instance 1 vlan 10
   [S3-mst-region]instance 2 vlan 20
   [S3-mst-region]active region-configuration 
   ```
   
   经此配置便将三个交换机都划分到了同一个`MST`域中，域中每个实例都会对应自己的一棵生成树。在同一域中查看到的`MST`域配置信息是一致的
   
   ![image-20220327100607410](https://s2.loli.net/2022/03/27/FolrLZec2JuTMhU.png)
   
   可以看到，不管是在哪一实例产生的生成树中，`S2`的`e0/0/1`端口都是处于弃用状态
   
   ![image-20220327100808827](https://s2.loli.net/2022/03/27/TZLXn46j1H29yG7.png)
   
4. 我们希望在实例`2`中【本例中即`VLAN 20`数据】能够开启`S2`的`e0/0/1`端口，因此我们可在实例`2`生成的生成树中指定`S2`为根交换机，如此一来`S2`的所有端口都会进入转发状态

   ```java
   S2:
   [S2]stp instance 2 priority 0		//将S2在实例2中的交换机优先级设置为0，优先级越小在根交										换机选举中越有优势
   ```

   此时观察可以发现，在实例`0`与`1`中，`S2`的`e0/0/1`端口仍被弃用，但是在实例`2`中却被启用，由此实现了我们的目标任务

   ![image-20220327101547133](https://s2.loli.net/2022/03/27/CfODRNsGox9HnBA.png)

5. **总的来说，MSTP的作用就是以实例【实例中可以有多个VLAN，一个VLAN只能放入一个实例】为单位生成自己独特的生成树从而战术性启用或者弃用某一链路，达到动态调节链路资源的目的**

#  9. GVRP基础配置 #

> `GVRP`中文名为`GVRP VLAN注册协议`，是`GARP（Generic Attribute Registration Protocol，通用属性注册协议）`的一种应用，用于注册和注销`VLAN`属性。
>
> `GVRP`有`3`种注册模式，不同模式对静态`VLAN`【手工配置的`VLAN`】和动态`VLAN`【学习到的`VLAN`】的处理方式也不同
>
> + **Normal模式**：允许该接口动态注册、注销`VLAN`，传播动态`VLAN`及静态`VLAN`信息
> + **Fixed模式**：禁止该接口动态注册、注销`VLAN`，只传播静态`VLAN`信息。即被设置为该模式下的`Trunk`接口，即使允许所有`VLAN`通过，实际通过的`VLAN`也只能是手动配置的那部分
> + **Forbidden模式**：禁止该接口动态注册、注销`VLAN`，不传播任何除`VLAN 1`外的任何`VLAN`信息。即被设置成为该模式下的`Trunk`接口，即使允许所有`VLAN`通过，实际通过的`VLAN`也只能是`VLAN 1`

交换机通过此协议可以相互交换`VLAN`配置信息，动态创建和管理`VLAN`，用户只需对少数交换机进行`VLAN`配置即可动态地传播`VLAN`信息

## 实验拓扑 ##

![image-20220327111049175](https://s2.loli.net/2022/03/27/smQrgONoBu8wUAz.png)

## 实验目的 ##

+ 理解`GVRP`的应用场景
+ 掌握`GVRP`的配置
+ 理解`GVRP`不同注册模式的区别
+ 掌握`GVRP`配置不同注册模式的方法

## 实验步骤 ##

1. 确保在创建`VLAN`前，各`PC`能够相互`ping`通。在`S1`上创建`VLAN 10`与`VLAN 20`，将连接`PC`的接口设置为`Access`接口按照图示设置默认`VLAN`，交换机之间的接口设置为`Trunk`口，允许所有`VLAN`通过【注意：此时其余交换机并未创建`VLAN`，所有`S4`无法为`Access`端口指定`S4`中不存在的`VLAN`】

   ```java
   S1:
   <Huawei>system-view		//进入系统视图
   [Huawei]undo info-center enable		//关闭消息提示
   [Huawei]sysname S1		//将设备命名为S1
   [S1]vlan batch 10 20	//在S1上创建VLAN 10与20
   [S1]interface e0/0/1	//进入e0/0/1端口
   [S1-Ethernet0/0/1]port link-type access //将其设置为access类型
   [S1-Ethernet0/0/1]port default vlan 10	//默认VLAN为10
   [S1-Ethernet0/0/1]quit
   [S1]interface e0/0/2
   [S1-Ethernet0/0/2]port link-type access 
   [S1-Ethernet0/0/2]port default vlan 20
   [S1-Ethernet0/0/2]quit
   [S1]interface g0/0/1
   [S1-GigabitEthernet0/0/1]port link-type trunk //将其设置为trunk类型
   [S1-GigabitEthernet0/0/1]port trunk allow-pass vlan all	//允许所有路由通过
   [S1-GigabitEthernet0/0/1]quit
   ```

   ```java
   S2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname S2
   [S2]interface g0/0/1
   [S2-GigabitEthernet0/0/1]port link-type trunk 
   [S2-GigabitEthernet0/0/1]port trunk allow-pass vlan all
   [S2-GigabitEthernet0/0/1]quit
   [S2]interface g0/0/2
   [S2-GigabitEthernet0/0/2]port link-type trunk 
   [S2-GigabitEthernet0/0/2]port trunk allow-pass vlan all
   [S2-GigabitEthernet0/0/2]quit
   ```

   ```java
   S3:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname S3
   [S3]interface g0/0/1
   [S3-GigabitEthernet0/0/1]port link-type trunk 
   [S3-GigabitEthernet0/0/1]port trunk allow-pass vlan all
   [S3-GigabitEthernet0/0/1]quit
   [S3]interface g0/0/2
   [S3-GigabitEthernet0/0/2]port link-type trunk 
   [S3-GigabitEthernet0/0/2]port trunk allow-pass vlan all
   [S3-GigabitEthernet0/0/2]quit
   ```

   ```java
   S4:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname S4
   [S4]interface g0/0/1
   [S4-GigabitEthernet0/0/1]port link-type trunk 
   [S4-GigabitEthernet0/0/1]port trunk allow-pass vlan all
   [S4-GigabitEthernet0/0/1]quit
   [S4]interface e0/0/1
   [S4-Ethernet0/0/1]port link-type access //当前没有创建VLAN，无法指定默认VLAN
   [S4-Ethernet0/0/1]quit
   [S4]interface e0/0/2
   [S4-Ethernet0/0/2]port link-type access 
   [S4-Ethernet0/0/2]quit
   ```

1. 配置`GVRP`单向注册【只有接收端口能够学习到`VLAN`】

   ```java
   S1:
   [S1]gvrp 	//整个交换机范围内打开GVRP
   [S1]interface g0/0/1
   [S1-GigabitEthernet0/0/1]gvrp	//在Trunk接口处打开GVRP
   ```

   ```java
   S2:
   [S2]gvrp
   [S2]interface g0/0/1
   [S2-GigabitEthernet0/0/1]gvrp
   [S2-GigabitEthernet0/0/1]quit
   [S2]interface g0/0/2
   [S2-GigabitEthernet0/0/2]gvrp
   ```

   ```java
   S3:
   [S3]gvrp	
   [S3]interface g0/0/1
   [S3-GigabitEthernet0/0/1]gvrp
   [S3-GigabitEthernet0/0/1]quit
   [S3]interface g0/0/2
   [S3-GigabitEthernet0/0/2]gvrp
   ```

   ```java
   S4:
   [S4]gvrp
   [S4]interface g0/0/1
   [S4-GigabitEthernet0/0/1]gvrp
   ```

   此时我们变打开了`GVRP`协议，在交换机中`VLAN`开始自学习，由于出发点是`S1`且学习只能单向传播，因此`S3`的`g0/0/2`肯定没有学习到`VLAN`【这只是个例子，并不是说只有这个端口没学习到】，我们查看`S3`的`VLAN`情况进行验证。

   <img src="https://s2.loli.net/2022/04/01/eXVYhy1oWdKpz4S.png" alt="image-20220401161944308" style="zoom:50%;" />

1. 我们希望所有端口都能学习到，因此只需要让`VLAN`信息反向再走一遍即可。我们在`S4`上创建`VLAN 10`与`20`，令`VLAN`信息反向传播一遍，以便所有端口都能够学习到

   ```java
   [S4]vlan batch 10 20	//在S4上创建VLAN 10和VLAN 20
   ```

   <img src="https://s2.loli.net/2022/04/01/Ue47ApXJLmWQMGN.png" alt="image-20220401162712103" style="zoom:50%;" />

1. `GVRP`的另外两种模式可根据需要在端口进行配置【默认情况下为`Normal`】，`Fixed`使得端口只能学习静态`VLAN`，`Forbidden`则使得端口只能通过`VLAN 1`的数据且不进行`VLAN`学习

   ```java
   [S3-GigabitEthernet0/0/1]gvrp registration fixed //将g0/0/1端口的gvrp配置为fixed模式
   ```

# 10. Smart/Monitor Link基础配置 #

> 为了解决环路问题可以采用`STP`技术，但是`STP`只能达到秒级的收敛速度，不适用于对收敛时间有很高要求的环境，因此华为推出了`Smart Link`

**Smart Link的作用**

网络中两条上行链路在正常情况下只有一条处于连通状态，而另一条处于阻塞状态从而防止由于环路引起的广播风暴。当主链路发生故障后，流量会在毫秒级时间内切换到备用链路上。不过`Smart Link`虽然能保证设备在本设备上行链路发生故障后快速进行倒换，但对于跨设备的链路故障不能提供有效保护。

**Monitor Link的作用**

`Monitor Link`用于扩展`Smart Link`的链路备份范围，通过监控上游设备的上行链路，达到上行链路故障迅速传达给下游设备的目的，从而触发`Smart Link`的主备链路切换，防止长时间因上行链路故障而出现的网络中断，使`Smart Link`备份作用更为完善。

##  实验拓扑 ##

<img src="https://s2.loli.net/2022/04/01/paoSDidhJn1YzLm.png" alt="image-20220401170658478" style="zoom:50%;" />

## 实验目的 ##

+ 理解`Smart Link`的应用场景
+ 掌握`Smart Link`组的基本配置
+ 掌握`Smart Link`回切功能的配置
+ 掌握`Monitor Link`的基本配置

##  实验步骤 ##

1. 在`S1`处配置`Smart Link`，令`e0/0/3`为主接口，链路正常时`e0/0/4`阻塞

   ```java
   S1:
   [S1]interface e0/0/3
   [S1-Ethernet0/0/3]stp disable //关闭接口上默认开启的STP协议
   [S1-Ethernet0/0/3]int e0/0/4
   [S1-Ethernet0/0/4]stp disable 
   [S1]smart-link group 1		//创建Smart Link组1
   [S1-smlk-group1]smart-link enable	//开启S1上的Smart Link服务
   [S1-smlk-group1]port Ethernet 0/0/3 master //将e0/0/3设置为主接口
   [S1-smlk-group1]port Ethernet 0/0/4 slave  //将e0/0/4设置为备份接口
   ```

   查看当前`S1`的主备状态

   <img src="https://s2.loli.net/2022/04/01/G43JOlaY9gbrskh.png" alt="image-20220401171811090" style="zoom:50%;" />

2. 当主接口出现故障时，备份接口会马上转换为`Active`模式，但是当主接口恢复时备份接口并不会主动"让位"，我们可以通过配置使其主动"让位"

   ```java
   S1:
   [S1]smart-link group 1			//进入Smart Link组1
   [S1-smlk-group1]restore enable   //开启回切功能                              
   [S1-smlk-group1]timer wtr 30	//将回切时间设置为30s,即主接口恢复过30s后切换链路
   ```

   我们通过手动关闭链路来模拟故障，并观察端口角色变化情况

   <img src="https://s2.loli.net/2022/04/01/R34BEkuoVjPSpfv.png" alt="image-20220401172823134" style="zoom:50%;" />

   接着我们重新开启此链路，大约`30s`后观察其端口角色变化情况

   <img src="https://s2.loli.net/2022/04/01/pojxMYtfalCXh2J.png" alt="image-20220401173232592" style="zoom:50%;" />

3. 此时仍然存在一个问题，若此时`S2`的`g0/0/1`链路出现故障时，`S1`检测不到并且继续使用`e0/0/3`端口进行数据发送，从而导致信息到达不了`S4`。为此我们可以在`S2`上配置`Monitor Link`用于监控`S2`上链路的变化，并将这种变化传递给`S1`以便其检测到故障。

   ```java
   [S2]monitor-link group 1		//在S2上创建Monitor Link组1
   [S2-mtlk-group1]port g0/0/1 uplink 	//对g0/0/1进行上行监控
   [S2-mtlk-group1]port e0/0/3 downlink	//对e0/0/3进行下行监控
   [S2-mtlk-group1]timer recover-time 10	//将回切时间定位10s
   ```

   此时我们关闭`S2`的`g0/0//1`模拟上行链路出现故障，看看S1是否会有所反应

   <img src="https://s2.loli.net/2022/04/01/C8q3VdjsGLyDmUr.png" alt="image-20220401175101094" style="zoom:50%;" />

   接着我们开启`S2`的`g0/0/1`模拟上行链路故障消失，看看`S1`是否有变化

   <img src="https://s2.loli.net/2022/04/01/8fOpt6TYh5lkqMy.png" alt="image-20220401175341609" style="zoom: 50%;" />

# 11. Eth-Trunk链路聚合 #

`Eth-Trunk`是一种捆绑技术，它将多个物理接口捆绑成一个逻辑接口，这个逻辑接口就称为`Eth-Trunk`接口，捆绑在一起的每个物理接口称为成员接口。`Eth-Trunk`只能由以太网链路构成，其优势在于：

+ **负载分担**：在某一个`Eth-Trunk`接口内可以实现流量负载分担
+ **提高可靠性**：当某个成员接口连接的物理链路出现故障时，流量会切换到其他可用的链路上，从而提高整个`Trunk`链路的可靠性
+ **增加带宽**：`Trunk`接口的总带宽是各成员接口带宽之和

所有`Eth-Trunk`中物理接口参数必须一致，`Eth-Trunk`链路两端要求一致的物理参数有：物理**接口类型**、物理**接口数量**、物理**接口速率**、物理接口的**双工方式**以及物理接口的**流控方式**。

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/01/iT4Gxto6SVshzAD.png" alt="image-20220401195729325" style="zoom:50%;" />

##  实验目的 ##

+ 理解`Eth-Trunk`的应用场景
+ 掌握两种配置`Eth-Trunk`链路聚合的方法
  1. **手工负载分担方式**：需要手动创建链路聚合组，并配置多个接口加入到所创建的`Eth-Trunk`中
  2. **静态LACP模式**：该模式通过`LACP`协议协商`Eth-Trunk`参数后自主选择活动接口
+ 掌握配置`Eth-Trunk`链路聚合的方法

## 实验步骤 ##

1. 验证各`PC`间可以相互`Ping`通后关闭`g0/0/2`与`g0/0/5`，以便后边可以进行实验

   ```java
   S1:
   [S1]interface g0/0/2
   [S1-GigabitEthernet0/0/2]shutdown	//关闭接口
   [S1-GigabitEthernet0/0/2]interface g0/0/5
   [S1-GigabitEthernet0/0/5]shutdown
       
   S2:
   [S2]interface g0/0/2
   [S2-GigabitEthernet0/0/2]shutdown	//关闭接口
   [S2-GigabitEthernet0/0/2]interface g0/0/5
   [S2-GigabitEthernet0/0/5]shutdown

2. 模拟链路增加，打开`S1`与`S2`的`g0/0/2`接口，此时默认开启的`MSTP`协议一定会阻塞其中某条链路以防形成回路

   <img src="https://s2.loli.net/2022/04/01/TxvOPBDkeoUF7cH.png" alt="image-20220401201719347" style="zoom:67%;" />

   可以观察到`g0/0/2`链路并没有被利用起来，因此无法实质性增加`S1`与`S2`之间的带宽。由此也证明单靠增加链路条数是不足以增加带宽的。

3. 为了实质性增加`S1`与`S2`之间的带宽，我们决定将`g0/0/1`与`g0/0/2`逻辑化成一条物理线路

   ```java
   S1:
   [S1]interface Eth-Trunk 1		//在S1上配置链路聚合，创建Eth-Trunk 1接口	
   [S1-Eth-Trunk1]mode manual load-balance //指定为手工负载分担模式
   [S1-Eth-Trunk1]quit
   [S1]interface g0/0/1
   [S1-GigabitEthernet0/0/1]eth-trunk 1	//将g0/0/1加入Eth-Trunk 1接口
   [S1-GigabitEthernet0/0/1]interface g0/0/2
   [S1-GigabitEthernet0/0/2]eth-trunk 1
   ```

   ```java
   S2:
   [S2]interface Eth-Trunk 1 			//在S2上配置链路聚合，创建Eth-Trunk 1接口
   [S2-Eth-Trunk1]mode manual load-balance 
   [S2-Eth-Trunk1]quit
   [S2]interface g0/0/1
   [S2-GigabitEthernet0/0/1]eth-trunk 1
   [S2-GigabitEthernet0/0/1]int g0/0/2
   [S2-GigabitEthernet0/0/2]eth-trunk 1
   ```

   配置完成后，我们查询当下`Eth-Trunk 1`的情况

   <img src="https://s2.loli.net/2022/04/01/fj2eXQ6RoHwCkgv.png" alt="image-20220401203542163" style="zoom:67%;" />

   再查询聚合链路后`MSTP`生成树的情况

   <img src="https://s2.loli.net/2022/04/01/LeHQNqvuGfP6CYi.png" alt="image-20220401203737218" style="zoom:67%;" />

   `Eth-Trunk`组中只要有一条物理链路是正常的，`Eth-Trunk`接口就不会断开，仍然可以保证数据的转发。可见，其不仅提升了带宽也实现了链路冗余。

4. **接着我们再部署一条链路作为备份链路，并采用静态`LACP`模式配置`Eth-Trunk`实现两条链路同时转发，一条链路备份，当其中一条转发链路出现问题时，备份链路可立即进行数据转发**

5. 打开`S1`与`S2`的`g0/0/5`模拟增加了一条新链路

6. 将先前加入到`Eth-Trunk`接口下的物理接口进行删除，否则无法修改模式

   ```java
   S1:
   [S1]interface g0/0/1	
   [S1-GigabitEthernet0/0/1]undo eth-trunk 	//将g0/0/1从先前配置的Eth-Trunk中移除
   [S1-GigabitEthernet0/0/1]interface g0/0/2
   [S1-GigabitEthernet0/0/2]undo eth-trunk
       
   S2:
   [S2]interface g0/0/1	
   [S2-GigabitEthernet0/0/1]undo eth-trunk 	
   [S2-GigabitEthernet0/0/1]interface g0/0/2
   [S2-GigabitEthernet0/0/2]undo eth-trunk
   ```

7. 重新创建`Eth-Trunk 1`接口，并将其工作模式定位静态`LACP`模式

   ```java
   S1:
   [S1]interface Eth-Trunk 1	//创建Eth-Trunk 1接口
   [S1-Eth-Trunk1]mode lacp-static 	//工作模式定为静态LACP模式
   [S1-Eth-Trunk1]interface g0/0/1		
   [S1-GigabitEthernet0/0/1]eth-trunk 1		//将g0/0/1加入Eth-Trunk 1接口
   [S1-GigabitEthernet0/0/1]interface g0/0/2
   [S1-GigabitEthernet0/0/2]eth-trunk 1
   [S1-GigabitEthernet0/0/2]interface g0/0/5  
   [S1-GigabitEthernet0/0/5]eth-trunk 1
       
   S2:
   [S2]interface Eth-Trunk 1	//创建Eth-Trunk 1接口
   [S2-Eth-Trunk1]mode lacp-static 	//工作模式定为静态LACP模式
   [S2-Eth-Trunk1]interface g0/0/1		
   [S2-GigabitEthernet0/0/1]eth-trunk 1		
   [S2-GigabitEthernet0/0/1]interface g0/0/2
   [S2-GigabitEthernet0/0/2]eth-trunk 1
   [S2-GigabitEthernet0/0/2]interface g0/0/5  
   [S2-GigabitEthernet0/0/5]eth-trunk 1
   ```

   我们观察链路聚合后各端口的情况

   <img src="https://s2.loli.net/2022/04/01/8s6mVyWaepMAGOu.png" alt="image-20220401211048844" style="zoom:50%;" />

8. 我们将`S1`的优先级由默认的`32768`改为`100`，使其成为主动端【值越低优先级越高】，并**按照主动端设备的接口来选择活动接口**。两端设备选出主动端后，**两端都会以主动端的接口优先级来选择活动端口**。两端选择了一致的活动接口，活动链路便可以建立起来，设置这些活动链路以负载分担的方式转发数据。

   ```java
   S1:
   [S1]lacp priority 100		//将S1在Eth-Trunk下的优先级设为100
   [S1]interface Eth-Trunk 1
   [S1-Eth-Trunk1]max active-linknumber 2	//设置Eth-Trunk 1的最大活动链路数为2
   [S1-Eth-Trunk1]interface g0/0/1
   [S1-GigabitEthernet0/0/1]lacp priority 100	//将g0/0/1接口优先级设为100
   [S1-GigabitEthernet0/0/1]interface g0/0/2
   [S1-GigabitEthernet0/0/2]lacp priority 100
   ```

   此时我们再次查看`Eth-Trunk 1`接口的情况

   <img src="https://s2.loli.net/2022/04/01/SM8m3Ui5IcE1XNB.png" alt="image-20220401212200708" style="zoom:50%;" />

   此时我们手动关闭`g0/0/1`接口模拟故障，看看`g0/0/5`是否会接替`g0/0/1`的位置

   <img src="https://s2.loli.net/2022/04/01/xWSUqDmORB38oEp.png" alt="image-20220401212409377" style="zoom:50%;" />

# 12. 静态路由与默认路由 #

静态路由是指用户或网络管理员手工配置的路由信息。当网络拓扑结构或链路状态发生改变时，需要网络管理人员手工修改静态路由信息，适合于小型、简单的网络环境

默认路由是一种特殊的静态路由，当路由器不知道该把包往哪里转发时就会往默认路由指定的下一跳位置进行转发

## 实验目的 ##

+ 掌握配置静态路由的方法
+ 掌握配置默认路由的方法
+ 了解黑洞路由

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/03/QKBp9CauFnSiZYv.png" alt="image-20220403141435824" style="zoom: 67%;" />

## 实验步骤 ##

1. 按照图示标志为`PC`配置好`IP`地址，掩码和网关，接着分别进入三个路由器为其接口配置对应`IP`地址与掩码

   ```java
   R1:
   <Huawei>system-view		//进入系统视图
   [Huawei]undo info-center enable	//关闭消息提醒
   [Huawei]sysname R1		//修改设备名称
   [R1]interface e0/0/0	//进入e0/0/0接口
   [R1-Ethernet0/0/0]ip address 192.168.10.254 24	//设置接口IP地址与掩码长度
   [R1-Ethernet0/0/0]int s0/0/0
   [R1-Serial0/0/0]ip address 10.0.12.1 24
   ```

   ```java
   R2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R2
   [R2]interface s0/0/1
   [R2-Serial0/0/1]ip address 10.1.12.2 24
   [R2-Serial0/0/1]interface s0/0/0
   [R2-Serial0/0/0]ip address 10.0.23.2 24
   ```

   ```java
   R3:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R3
   [R3]interface s0/0/1
   [R3-Serial0/0/1]ip address 10.0.23.3 24
   [R3-Serial0/0/1]interface e0/0/0
   [R3-Ethernet0/0/0]ip address 192.168.20.254 24
   ```

   此时`PC1`与`PC2`相互`ping`不通，因为路由器隔离了广播域且此时各个路由器上都只有直连路由。`PC1 ping PC2`的请求到达`R1`后，`R1`不知道如何进行转发，因此此时无法建立通路

2. 我们可以通过在每个路由器上设立静态路由来帮助建立通路，告诉路由器该如何转发数据

   ```java
   R1:
   //要去192.168.20.0/24网段就走10.1.12.2这条路
   [R1]ip route-static 192.168.20.0 255.255.255.0 10.1.12.2
       
   R2:
   //要去192.168.20.0/24网段就走10.0.23.3这条路
   [R2]ip route-static 192.168.20.0 255.255.255.0 10.0.23.3
   ```

   此时我们便配置好了从`PC1`至`PC2`的路径，我们尝试一下是否能够`ping`通

   <img src="https://s2.loli.net/2022/04/03/GvtXnefI3NZAPjw.png" alt="image-20220403145827366" style="zoom:50%;" />

   我们对`R3`的`s0/0/1`进行抓包查看当前情况

   <img src="https://s2.loli.net/2022/04/03/WY5T8kytEaHfXge.png" alt="image-20220403150115045" style="zoom:67%;" />

   端口收到请求报文，说明数据确实往这里转发并且被接收了。但是若要`ping`通，`PC1`还需要收到来自`PC3`的响应报文，而从`PC3`到`PC1`的路由我们并没有配置，因此路由器不知道如何转发，所以`PC1`收不到响应报文，即请求超时

3. 为了让响应报文找到回去的路径，我们还需要对路由器配置响应报文的路由

   ```java
   R2:
   //要去192.168.10.0/24网段就走10.0.12.1这条路
   [R2]ip route-static 192.168.10.0 255.255.255.0 10.0.12.1
   
   R3:
   //要去192.168.10.0/24网段就走10.1.23.2这条路
   [R3]ip route-static 192.168.10.0 255.255.255.0 10.0.23.2
   ```

   此时我们再令`PC1 ping PC2`可发现请求/响应报文皆有路可寻，因此可以`ping`通

   <img src="https://s2.loli.net/2022/04/03/D5ZHOszdLIwyJaj.png" alt="image-20220403151141936" style="zoom: 67%;" />

4. 此时还存在一个问题，如果此时`PC2`与`R3`之间的链路出现故障，我们要进行排查究竟哪段链路发生故障便无从下手，因为此时`PC1 ping R3`的`s0/0/1`端口不通【我们并未配置此条路由】，此时`PC1 ping PC2`也不通，无法锁定具体目标。为了防止这种情况，在**帮路由器配置静态路由时最好将链路中所有的目的地都配置上去，以增加网络的可靠性**，便于我们排查故障。

5. 或者我们可以选择使用默认路由，当路由器不知如何进行转发时就会走默认路由指定的下一跳位置，在减少配置工作量的情况下还能增加网络的可靠性

   ```java
   R1:
   //来到R1的数据如果不知道往哪里转发就走10.1.12.2这条路
   [R1]ip route-static 0.0.0.0 0 10.1.12.2
       
   R3:
   //来到R3的数据如果不知道往哪里转发就走Serial 0/0/0这条路【等同于IP地址】
   [R3]ip route-static 0.0.0.0 0 Serial 0/0/0
   ```

   <img src="https://s2.loli.net/2022/04/03/ZA5ochC7EXyNVeD.png" alt="image-20220403155734418" style="zoom:50%;" />

   `R2`不配置默认路由，因为其转发路径不是唯一的，若假设强行为R2配置默认路由指向`R3`的`s0/0/1`接口，则会在这段链路形成路由环路。若此时`PC1 ping`了一个不存在的地址，则报文会走默认路由，`R2`转发给`R3`，`R3`又转发给`R2`，直至耗尽报文生存时间字段`TTL`才将此报文丢弃

6. 我们还可以通过设置黑洞路由控制路由器丢弃指定报文

   ```java
   R1:
   //当目的地址192.168.30.0/24的报文来到R1时直接丢弃，像黑洞一样吞噬此报文
   [R1]ip route-static 192.168.30.0 255.255.255.0 null0
   ```

# 13. 浮动静态路由 #

**浮动静态路由**：一种特殊的静态路由，通过配置去往相同的目的网段但优先级不同的静态路由，以保证在网络中优先级较高的路由被选择，即主路由失效的情况下提供备份路由。正常情况下，备份路由不出现在路由表中。

**负载均衡**：当数据有多条可选路径前往同一目的网络，可以通过配置相同优先级和开销的静态路由实现负载均衡，使得数据均衡分配到多条路径上，当其中某条路径失效时，其他路径仍然能正常传输数据

## 实验目的 ##

+ 理解浮动路由应用场景
+ 掌握配置浮动路由的方法
+ 掌握配置静态路由负载均衡的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/04/r3QihZpFxGVwAfa.png" alt="image-20220404085233117" style="zoom:50%;" />

## 实验步骤 ##

1. 按图示配置好`PC`的`IP`地址，掩码，网关以及路由器各端口的`IP`地址与网关

   ```java
   R1:
   <Huawei>system-view		//进入系统视图
   [Huawei]undo info-center enable		//关闭消息提醒
   [Huawei]sysname R1		//为设备重命名
   [R1]interface g0/0/0	//进入G0/0/0接口
   [R1-GigabitEthernet0/0/0]ip address 192.168.10.254 24	//配置端口IP地址与掩码长度
   [R1-GigabitEthernet0/0/0]interface s0/0/1
   [R1-Serial0/0/1]ip address 10.0.13.1 24
   [R1-Serial0/0/1]interface s0/0/0
   [R1-Serial0/0/0]ip address 10.0.12.1 24
       
   R2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R2
   [R2]interface s0/0/0
   [R2-Serial0/0/0]ip address 10.0.12.2 24
   [R2-Serial0/0/0]interface s0/0/1
   [R2-Serial0/0/1]ip address 10.0.23.2 24
       
   R3:
   <Huawei>system-view	
   [Huawei]undo info-center enable
   [Huawei]sysname R3
   [R3]interface g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 192.168.20.254 24
   [R3-GigabitEthernet0/0/0]interface s0/0/1
   [R3-Serial0/0/1]ip address 10.0.13.3 24
   [R3-Serial0/0/1]interface s0/0/0	
   [R3-Serial0/0/0]ip address 10.0.23.3 24

2. 在`R1`上配置目的网段为`PC2`所在网段的静态路由；在`R3`上配置目的网段为主机`PC1`所在网段的静态路由；在`R2`上配置目的网段分别为主机`PC1`和`PC2`所在网段的静态路由

   ```java
   R1:
   //想去192.168.20.0/24网段需要走10.0.13.3【s0/0/1】这条路
   [R1]ip route-static 192.168.20.0 24 10.0.13.3
       
   R2:
   [R2]ip route-static 192.168.10.0 24 10.0.12.1
   [R2]ip route-static 192.168.20.0 24 10.0.23.3
       
   R3:
   [R3]ip route-static 192.168.10.0 24 10.0.13.1
   ```

   此时`R1`上有到达`PC2`的路由，`PC2`同理，因此`PC1`和`PC2`是可以`ping`通的；同理，`R2`此时也可以`ping`通`PC1`与`PC2`

3. 现需实现当`PC1`与`PC2`通信时直连链路为主用链路，通过`R2`的链路为备用链路，即当`R1`与`R3`之间的链路发生故障时，`PC1`的数据包通过`R2`到达`PC2`。此需求可通过浮动静态路由实现

   ```java
   R1:
   //将这条静态路由的优先级设置为100【默认为60，小的当选】
   [R1]ip route-static 192.168.20.0 24 10.0.12.2 preference 100
       
   R3:
   [R3]ip route-static 192.168.10.0 24 10.0.23.2 preference 100
   ```

   此时我们直接查看路由表只能看到被选中的路由

   <img src="https://s2.loli.net/2022/04/04/BEIfiJx9v4q7uQm.png" alt="image-20220404093441123" style="zoom:50%;" />

   如何查看我们配置的浮动路由呢？可以使用命令`display ip routing-table protocol static`

   <img src="https://s2.loli.net/2022/04/04/je9wDPOoaEkzSgs.png" alt="image-20220404093926573" style="zoom:50%;" />

4. 此时我们模拟故障，将`R1`与`R3`之间的通路关闭，观察是否启用备份路由

   <img src="https://s2.loli.net/2022/04/04/EVvndqmy9XxFP1O.png" alt="image-20220404094318319" style="zoom:50%;" />

5. 由于在链路正常时，`PC1 ping PC2`通常走直连链路，经过`R2`的链路长时间处于闲置状态，为了更好的利用链路资源，我们可为到达`PC2`所在网段指明多一条条路径，从而实现负载均衡

   ```java
   R1:
   //将通过R2到达PC2这条路由优先级改为60【静态路由默认就是60，因此此时两条路由同级】
   [R1]ip route-static 192.168.20.0 24 10.0.12.2 preference 60
   ```

   接着我们查看路由表

   <img src="https://s2.loli.net/2022/04/04/D5S7Ws6LvUfEhYu.png" alt="image-20220404095148658" style="zoom:50%;" />

# 14. OSPF单区域配置 #

**链路状态算法路由协议**互相通告的是链路状态信息，**每台路由器都将自己的链路状态信息【包括接口的`IP`地址和子网掩码、网络类型、该链路开销等】发送给其他路由器，并在网络中泛洪，当每台路由器收集到网络内所有链路状态信息后就能拥有整个网络的拓扑情况**，然后根据整网拓扑情况运行`SPF`算法，得出所有网段的最短路径。

`OSPF`【`Open Shortest Path First`，**开放最短路径优先**】作为基于链路状态的协议，具有收敛快、路由无环、拓展性好等优点。

`OSPF`支持区域划分，区域是从逻辑上讲路由器划分为不同的组，每个组用区域号【`Area ID`】来标识。一个网段只能属于一个区域，或者说每个运行`OSPF`的接口必须指明属于哪一个区域。**区域`0`位于骨干区域**，骨干区域负责在非骨干区域之间发布区域间的路由信息。在一个`OSPF`区域中有且只有一个骨干区域。

## 实验目的 ##

+ 掌握`OSPF`单区域的配置方法
+ 理解`OSPF`单区域的应用场景
+ 掌握查看`OSPF`邻居状态的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/04/YZpu7Bxq3A9Cr5v.png" alt="image-20220404111537167" style="zoom:50%;" />

## 实验步骤 ##

1. 如图示配置好`PC`的`IP`地址，掩码，网关以及路由器各端口`IP`地址与掩码

   ```java
   R1:
   <Huawei>systm-view			//进入系统视图
   [Huawei]undo info-center enable	//关闭消息提醒
   [Huawei]sysname R1
   [R1]interface g0/0/2
   [R1-GigabitEthernet0/0/2]ip address 172.16.1.254 24
   [R1-GigabitEthernet0/0/2]interface g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.10.1 24
   [R1-GigabitEthernet0/0/0]interface g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 172.16.20.1 24
       
   R2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R2
   [R2]interface g0/0/2
   [R2-GigabitEthernet0/0/2]ip address 172.16.2.254 24
   [R2-GigabitEthernet0/0/2]interface g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 172.16.30.2 24
   [R2-GigabitEthernet0/0/1]interface g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.10.2 24
       
   R3:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R3
   [R3]interface g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 172.16.3.254 24
   [R3-GigabitEthernet0/0/2]interface g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 172.16.30.3 24
   [R3-GigabitEthernet0/0/1]interface g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 172.16.20.3 24

2. 将`3`个路由器都划分进骨干区域`Area 0`中

   ```java
   R1:
   [R1]ospf 1			//创建并运行OSPF，进程号是1
   [R1-ospf-1]area 0	//创建区域并进入OSPF视图，0号区域是骨干区域
   [R1-ospf-1-area-0.0.0.0]network 172.16.10.0 0.0.0.255	//指定OSPF协议的接口所属网段与															掩码反码
   [R1-ospf-1-area-0.0.0.0]network 172.16.20.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   ```

   ```java
   R2:
   [R2]ospf 			//不填参数默认进程号就是1
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 172.16.10.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 172.16.30.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   ```

   ```java
   R3:
   [R3]ospf 1
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 172.16.20.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 172.16.30.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   ```

3. 查看各路由器`OSPF`接口情况【以`R1`为例】，此时接口状态有**DR指定路由**和**BDR备份路由**两种，其具体作用可看此篇：[DR及BDR详解](https://blog.csdn.net/qq_62311779/article/details/122889602?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164907114816780265492630%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164907114816780265492630&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-122889602.142^v5^article_score_rank,157^v4^new_style&utm_term=OSPF%E6%8E%A5%E5%8F%A3%E4%B8%BADR&spm=1018.2226.3001.4187)，总的来说就是根据算法选择资源耗费最少的方式传送链路状态。

   <img src="https://s2.loli.net/2022/04/04/Bbi8Vlz2567ySsW.png" alt="image-20220404193054702" style="zoom:50%;" />

4. 查看`OSPF`邻居状态【以`R1`为例】

   <img src="https://s2.loli.net/2022/04/04/yJOlg8KB2c1x5F9.png" alt="image-20220404193923274" style="zoom: 67%;" />

5. 查看路由器上学习到的`OSPF`路由【以`R1`为例】

   <img src="https://s2.loli.net/2022/04/04/TnreI8V4R5pgv3J.png" alt="image-20220404194113688" style="zoom:50%;" />

6. 发现此时`R1`已经拥有的去任意网段的路由，因此`PC1`与`PC2`，`PC3`一定能`ping`通，我们对此进行验证

   <img src="https://s2.loli.net/2022/04/04/ka8tFNB4coC3rgn.png" alt="image-20220404194358341" style="zoom:50%;" />

# 15. OSPF多区域配置 #

> 在`OSPF`单区域中，每台路由器都需要收集其他所有路由器的链路状态信息，如果网络规模较大则会导致路由器不堪重负。就像一个国家面积很大时会把整个国家划分为不同省份来管理一样，`OSPF`协议也可以将整个自治系统划分为不同的区域`Area`

==**链路状态信息只在区域内部泛洪，区域之间传递的只是路由条目而非链路状态信息**==，因此大大减小路由器负担

当**一台路由器属于不同区域时称它为区域边界路由器ABR**，负责传递区域间路由信息，类似于距离矢量算法

为了防止环路，所有**==非骨干区域之间的路由信息必须经过骨干区域==**，也就是非骨干区域必须和骨干区域相连，非骨干区域之间不能直接进行路由信息交互

## 实验目的 ##

+ 理解配置`OSPF`多区域的使用场景
+ 掌握配置`OSPF`多区域的方法
+ 理解`OSPF`区域边界路由器`ABR`的工作特点

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/04/91JZ2tpVAoDxFN7.png" alt="image-20220404211316733" style="zoom:50%;" />

## 实验步骤 ##

1. 如图示拓扑配置好各`PC`的`IP`地址，掩码网关以及各路由器的`IP`地址与掩码并测试各直连链路的连通性

   ```java
   R1:
   <Huawei>system-view	
   [Huawei]undo info-center enable
   [Huawei]sysname R1
   [R1]interface g0/0/2
   [R1-GigabitEthernet0/0/2]ip address 10.0.15.1 24
   [R1-GigabitEthernet0/0/2]interface g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 10.0.13.1 24
   [R1-GigabitEthernet0/0/1]interface g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.12.1 24
   ```

   ```java
   R2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R2
   [R2]interface g0/0/0	
   [R2-GigabitEthernet0/0/0]ip address 10.0.12.2 24
   [R2-GigabitEthernet0/0/0]interface g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 10.0.24.2 24
   [R2-GigabitEthernet0/0/1]interface g0/0/2
   [R2-GigabitEthernet0/0/2]ip address 10.0.26.2 24
   ```

   ```java
   R3:
   <Huawei>system-view	
   [Huawei]undo info-center enable
   [Huawei]sysname R3
   [R3]interface g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.34.3 24
   [R3-GigabitEthernet0/0/0]interface g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.13.3 24
   [R3-GigabitEthernet0/0/1]interface g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 10.0.35.3 24
   [R3-GigabitEthernet0/0/2]interface e0/0/0
   [R3-Ethernet0/0/0]ip address 10.0.3.254 24
   ```

   ```java
   R4:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R4
   [R4]interface g0/0/2
   [R4-GigabitEthernet0/0/2]ip address 10.0.46.4 24
   [R4-GigabitEthernet0/0/2]interface g0/0/1
   [R4-GigabitEthernet0/0/1]ip address 10.0.24.4 24
   [R4-GigabitEthernet0/0/1]interface g0/0/0	
   [R4-GigabitEthernet0/0/0]ip address 10.0.34.4 24
   [R4-GigabitEthernet0/0/0]interface e0/0/0
   [R4-Ethernet0/0/0]ip address 10.0.4.254 24
   ```

   ```java
   R5:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R5
   [R5]interface g0/0/0
   [R5-GigabitEthernet0/0/0]ip address 10.0.15.5 24
   [R5-GigabitEthernet0/0/0]interface g0/0/1
   [R5-GigabitEthernet0/0/1]ip address 10.0.35.5 24
   [R5-GigabitEthernet0/0/1]interface g0/0/2	
   [R5-GigabitEthernet0/0/2]ip address 10.0.1.254 24
   ```

   ```java
   R6:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R6
   [R6]interface g0/0/0
   [R6-GigabitEthernet0/0/0]ip address 10.0.26.6 24
   [R6-GigabitEthernet0/0/0]interface g0/0/1
   [R6-GigabitEthernet0/0/1]ip address 10.0.46.6 24
   [R6-GigabitEthernet0/0/1]interface g0/0/2
   [R6-GigabitEthernet0/0/2]ip address 10.0.2.254 24
   ```

2. 在`R1，R2，R3，R4`上开启`OSPF`并将它们划进骨干区域，通告各网段

   ```java
   R1:
   [R1]ospf	//开启OSPF协议
   [R1-ospf-1]area 0	//创建区域0
   [R1-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255 //通告诉网段及对应掩码的反码
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
       
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
       
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.3.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.4.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   ```

   此时`R1，R2，R3，R4`内部链路及`PC3`与`PC4`处于骨干区域中，并且通过`OSPF`协议学习到了通往各自网段的路由，此时`PC3 ping PC4`应该是通路

   <img src="https://s2.loli.net/2022/04/04/HlLyMIk8N6sqKCw.png" alt="image-20220404210548768" style="zoom:50%;" />

3. 在`R5`上创建`OSPF`进程并进入区域`1`，通告其相应网段

   ```java
   R5:
   [R5]ospf
   [R5-ospf-1]area 1
   [R5-ospf-1-area-0.0.0.1]network 10.0.1.0 0.0.0.255
   [R5-ospf-1-area-0.0.0.1]network 10.0.15.0 0.0.0.255
   [R5-ospf-1-area-0.0.0.1]network 10.0.35.0 0.0.0.255
   ```

   将`R1`与`R3`的左半部分也划入区域`1`

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 1
   [R1-ospf-1-area-0.0.0.1]network 10.0.15.0 0.0.0.255
       
   R3:
   [R3]ospf
   [R3-ospf-1]area 1
   [R3-ospf-1-area-0.0.0.1]network 10.0.35.0 0.0.0.255
   ```

4. 查看`R5`邻居状态

   <img src="https://s2.loli.net/2022/04/04/STDp394HNL5w8ak.png" alt="image-20220404212629888" style="zoom:50%;" />

   其中`State`为`Full`，说明邻居关系建立正常

5. 查看`R5`的`OSPF`路由表

   <img src="https://s2.loli.net/2022/04/04/Y7VkD2aCgi4PFQy.png" alt="image-20220404212839764" style="zoom:50%;" />

   可以观察到，除了区域`2`部分之外，其余路由皆通告`OSPF`协议学习得到

6. 查看`R5`的链路状态数据库信息

   <img src="https://s2.loli.net/2022/04/04/MQ1TBU4CchlmVwD.png" alt="image-20220404213351745" style="zoom:50%;" />

   可以发现，关于其他区域的路由条目都是通过`Sum-Net`这类`LSA【Link State Acknowledgement，链路状态确认】`获得，这类`LSA`是不参与本区域的`SPF`算法运算的

7. 接着对`R2，R4，R6`进行类似配置，将相应网段划进区域`2`，最终使得`PC1`与`PC2`可以互通

   ```java
   R2:
   [R2]ospf
   [R2-ospf-1]area 2
   [R2-ospf-1-area-0.0.0.2]network 10.0.26.0 0.0.0.255
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 2
   [R4-ospf-1-area-0.0.0.2]network 10.0.46.0 0.0.0.255
       
   R6:
   [R6]ospf
   [R6-ospf-1]area 2
   [R6-ospf-1-area-0.0.0.2]network 10.0.2.0 0.0.0.255
   [R6-ospf-1-area-0.0.0.2]network 10.0.26.0 0.0.0.255
   [R6-ospf-1-area-0.0.0.2]network 10.0.46.0 0.0.0.255
   ```

   <img src="https://s2.loli.net/2022/04/04/YXF7lCaTUZu4G62.png" alt="image-20220404214211225" style="zoom:50%;" />

# 16. 配置OSPF认证  #

`OSPF`支持报文验证功能，只有通过验证的报文才能接收，否则将不能正常建立邻居关系。

`OSPF`支持两种认证方式：区域认证和链路认证

+ 使用**区域认证**时，一个区域中所有的路由器在该区域下的认证模式和口令必须一致
+ **链路认证**可专门针对某个邻居设置单独的认证模式和密码。

每种认证方式又分为简单验证模式，`MD5`验证模式和`Key chain`模式

+ **简单验证模式**在数据传递过程中，认证密钥和密钥ID都是明文传输，很容易被截获
+ **MD5验证模式**下的密钥是经过`MD5`加密传输，相比于简单验证模式更为安全
+ **Key chain验证模式**可以同时配置多个密钥，不同密钥可单独设置生效周期等

## 实验目的 ##

+ 理解`OSPF`认证的应用场景
+ 理解`OSPF`区域认证和链路认证的区别
+ 掌握配置`OSPF`区域认证的方法
+ 掌握配置`OSPF`链路认证的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/06/KH1pz4itoxkP9ub.png" alt="image-20220406201402406" style="zoom: 67%;" />

## 实验步骤 ##

1. 按照图示配置各端口`IP`地址与掩码，路由器编号即主机号编号；同时为每个路由器设置一个`Loopback`环回接口【只要设备运行正常，它将永处于`up`状态，不会因为线路中断而被关闭】，方便后续测试

   ```java
   R1:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R1
   [R1]interface g0/0/0
   [R1-GigabitEthernet0/0/0]ip ad	
   [R1-GigabitEthernet0/0/0]ip address 10.0.12.1 24
   [R1-GigabitEthernet0/0/0]interface LoopBack 0	//进入环回接口0
   [R1-LoopBack0]ip address 1.1.1.1 32		//设置环回接口地址与掩码
       
   R2:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R2
   [R2]interface g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 10.0.12.2 24
   [R2-GigabitEthernet0/0/0]interface g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 10.0.24.2 24
   [R2-GigabitEthernet0/0/1]interface g0/0/2
   [R2-GigabitEthernet0/0/2]ip address 10.0.23.2 24
   [R2-GigabitEthernet0/0/2]interface loopback0
   [R2-LoopBack0]ip address 2.2.2.2 32
       
   R3:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R3
   [R3]interface g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 10.0.23.3 24
   [R3-GigabitEthernet0/0/2]interface g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.35.3 24
   [R3-GigabitEthernet0/0/0]interface g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.36.3 24
   [R3-GigabitEthernet0/0/1]interface LoopBack 0
   [R3-LoopBack0]ip address 3.3.3.3 32
   
   R4:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R4
   [R4]interface g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.24.4 24
   [R4-GigabitEthernet0/0/0]interface LoopBack 0
   [R4-LoopBack0]ip address 4.4.4.4 32
       
   R5:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R5
   [R5]interface g0/0/0
   [R5-GigabitEthernet0/0/0]ip address 10.0.35.5 24
   [R5-GigabitEthernet0/0/0]interface LoopBack 0
   [R5-LoopBack0]ip address 5.5.5.5 32
       
   R6:
   <Huawei>system-view
   [Huawei]undo info-center enable
   [Huawei]sysname R6
   [R6]interface g0/0/0	
   [R6-GigabitEthernet0/0/0]ip address 10.0.36.6 24
   [R6-GigabitEthernet0/0/0]interface loopback0
   [R6-LoopBack0]ip address 6.6.6.6 32
   ```

2. 按照图示搭建OSPF网络，环回接口记得要通告

   ```java
   R1:
   [R1]ospf		//进入OSPF模式
   [R1-ospf-1]area 1	//创建区域1
   [R1-ospf-1-area-0.0.0.1]network 10.0.12.0 0.0.0.255 //将此网段通告给同区域的路由器，掩码															写反码形式
   [R1-ospf-1-area-0.0.0.1]network 1.1.1.1 0.0.0.0	//通告环回接口
       
   R2:
   [R2]ospf
   [R2-ospf-1]area 1
   [R2-ospf-1-area-0.0.0.1]network 10.0.12.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.1]network 10.0.24.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.1]quit
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 2.2.2.2 0.0.0.0
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.35.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.36.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 3.3.3.3 0.0.0.0
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 1
   [R4-ospf-1-area-0.0.0.1]network 10.0.24.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.1]network 4.4.4.4 0.0.0.0
       
   R5:
   [R5]ospf
   [R5-ospf-1]area 0
   [R5-ospf-1-area-0.0.0.0]network 10.0.35.0 0.0.0.255
   [R5-ospf-1-area-0.0.0.0]network 5.5.5.5 0.0.0.0
       
   R6:
   [R6]ospf
   [R6-ospf-1]area 0
   [R6-ospf-1-area-0.0.0.0]network 10.0.36.0 0.0.0.255
   [R6-ospf-1-area-0.0.0.0]network 6.6.6.6 0.0.0.0
   ```

   配置完成后按理各路由器都相互学习到各自路由，因此环回接口间可以相互`ping`通

   <img src="https://s2.loli.net/2022/04/06/NwftQjrF8MyXTzD.png" alt="image-20220406205827348" style="zoom: 50%;" />

3. 在`R1`上`OSPF`的区域`1`视图下使用`authentication-mode`命令指定该区域使用认证模式为`simple`，即简单验证模式，配置口令为`huawei1`，并配置`plain`参数，配置**区域明文认证**

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 1	
   //配置认证模式为简单验证模式，口令为huawei1
   [R1-ospf-1-area-0.0.0.1]authentication-mode simple plain huawei1
   ```

   <img src="https://s2.loli.net/2022/04/06/MNl4B1eaL5iAtsc.png" alt="image-20220406210744522" style="zoom:67%;" />

   若忘记配置`plain`参数也不要紧，`eNSP`会自动配置。当配置文件里面没有`plain`参数时口令会以密文的形式显示

4. 查看此时`R1`的`OSPF`邻居

   <img src="https://s2.loli.net/2022/04/06/hu2pNoEkYwRTMzr.png" alt="image-20220406212408524" style="zoom:50%;" />

   我们发现原本应该是`R1`邻居的`R2`不在`R1`的邻居表中，这是为什么呢？

   原因是目前仅仅在`R1`上配置了认证，导致`R1`和`R2`间的`OSPF`认证不匹配，因此我们还需要在`R2`上完成一样的配置，才能使它们成为邻居

   ```java
   R2:
   [R2]ospf
   [R2-ospf-1]area 1	
   [R2-ospf-1-area-0.0.0.1]authentication-mode simple huawei1
   ```

   此时我们再次查看`R1`的邻居表

   <img src="https://s2.loli.net/2022/04/06/tebXVPfrnLdAUHK.png" alt="image-20220406212824996" style="zoom:67%;" />

   同理，我们在`R4`上建立同样的验证模式和口令，再看看`R2`的邻居情况

   <img src="https://s2.loli.net/2022/04/06/Uq19zwWFmHvCPej.png" alt="image-20220406213959099" style="zoom:50%;" />

5. 接着我们准备在区域` 0`配置**区域密文认证**

   在`R2`上配置`OSPF Area 0`区域认证，使用验证模式为`MD5`，即`MD5`验证模式，验证字标符为`1`，配置口令为`huawei3`，同时在区域`0`的其他设备做同等配置。

   ```java
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]authentication-mode md5 1 huawei3
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]authentication-mode md5 1 huawei3
       
   R5:
   [R5]ospf
   [R5-ospf-1]area 0
   [R5-ospf-1-area-0.0.0.0]authentication-mode md5 1 huawei3
       
   R6:
   [R6]ospf 
   [R6-ospf-1]area 0
   [R6-ospf-1-area-0.0.0.0]authentication-mode md5 1 huawei3
   ```

   观察配置后的口令密文情况

   <img src="https://s2.loli.net/2022/04/06/63NOtYeBQFVPd41.png" alt="image-20220406214925475" style="zoom:50%;" />

   观察`R3`的邻居情况

   <img src="https://s2.loli.net/2022/04/06/p3vRuqC4IjQkbxh.png" alt="image-20220406215055365" style="zoom: 50%;" />

6. 在上述步骤中，我们使用了`OSPF`的区域认证方式配置了`OSPF`认证，使用**链路认证配置方式**可以达到同样的效果

   如果采用链路认证的方式，就需要在同一`OSPF`的链路接口下都配置链路认证的命令，设置验证模式和口令等参数；而采取区域认证的方式时，在同一区域中，仅需在`OSPF`进程下的相应区域视图下配置一条命令来设置验证模式和口令即可，大大节省了配置量。所以在同一区域中如果有多台`OSPF`设备需要配置认证，建议选用区域认证的方式进行配置

   如果**同时配置了接口认证和区域认证时，会优先使用接口验证建立OSPF邻居**

7. 为了进一步提示`R2`与`R4`之间的`OSPF`网络安全性，网络管理员需要在两台设备之间部署`MD5`验证模式的`OSPF`链路认证。在`R2`的`g0/0/1`接口下配置使用`MD5`验证模式，验证字标识符为`1`，口令为`huawei5`

   ```java
   R2:
   [R2]interface g0/0/1	
   //在g0/0/1接口上配置md5验证模式，标识符为1，口令为huawei5
   [R2-GigabitEthernet0/0/1]ospf authentication-mode md5 1 huawei5 
   ```

   <img src="https://s2.loli.net/2022/04/07/2HQCZtj1o3LSV8O.png" alt="image-20220407145622820" style="zoom:50%;" />

   接着我们在`R4`上进行同样的配置，看看配置后`R2`的邻居状态是否恢复

   ```java
   R4:
   [R4]interface g0/0/0
   [R4-GigabitEthernet0/0/0]ospf authentication-mode md5 1 huawei5
   ```

   <img src="https://s2.loli.net/2022/04/07/uTBjXnpm7xUaLGK.png" alt="image-20220407145923797" style="zoom:50%;" />

# 17. OSPF被动接口配置 #

`OSPF`被动接口也称抑制接口，成为被动接口后将不会接收和发送`OSPF`报文

## 实验目的 ##

+ 理解`OSPF`被动接口的应用场景
+ 掌握`OSPF`被动接口的配置方法
+ 理解`OSPF`被动接口的作用原理

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/10/RCSbTxK4jaB8h7V.png" alt="image-20220410194331129" style="zoom:50%;" />

## 实验步骤 ##

1. 按照图示配置好各网段`IP`地址与掩码，测试直连链路的连通性

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.3.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 10.0.13.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 10.0.23.2 24
   [R2-GigabitEthernet0/0/0]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 10.0.4.254 24
       
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.13.3 24
   [R3-GigabitEthernet0/0/0]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.23.3 24
   [R3-GigabitEthernet0/0/1]int g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 10.0.30.3 24
       
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.30.4 24
   [R4-GigabitEthernet0/0/0]int g0/0/1
   [R4-GigabitEthernet0/0/1]ip address 10.0.1.254 24
       
   R5:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R5
   [R5]int g0/0/0
   [R5-GigabitEthernet0/0/0]ip address 10.0.30.5 24
   [R5-GigabitEthernet0/0/0]int g0/0/1
   [R5-GigabitEthernet0/0/1]ip address 10.0.2.254 24

2. 搭配基本的`OSPF`，将所有路由器接口都运行在区域`0`内

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.3.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
       
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.4.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
       
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.30.0 0.0.0.255
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.30.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 10.0.1.0 0.0.0.255
       
   R5:
   [R5]ospf
   [R5-ospf-1]area 0
   [R5-ospf-1-area-0.0.0.0]network 10.0.2.0 0.0.0.255
   [R5-ospf-1-area-0.0.0.0]network 10.0.30.0 0.0.0.255
   ```

   此时任意路由器都会学习到所有路由信息，`PC`间都能相互`ping`通

3. 我们在`PC1`的`e0/0/1`处抓包，观察此时`OSPF`的`Hello`报文情况

   <img src="https://s2.loli.net/2022/04/10/qK2kXoc6EaiszN5.png" alt="image-20220410195738513" style="zoom:50%;" />

   我们发现此接口频繁收到对于`PC`来说无意义的`Hello`报文，在`OSPF`的`Hello`报文中含有很多`OSPF`网络的重要信息，如果被恶意截取，容易出现安全隐患

4. 我们可通过配置**被动接口使得终端不再收到任何OSPF报文**

   ```java
   R4:
   [R4]ospf
   [R4-ospf-1]silent-interface g0/0/1	//禁止R4的g0/0/1端口接收和发送OSPF报文
   ```

   配置完此命令后再次抓包查看，发现再接收不到`OSPF`的`Hello`报文

   如果`R4`上有多个接口需要设置为被动接口，只有`g0/0/1`保持活动状态，可用如下命令进行简化

   ```java
   R4:
   [R4]ospf
   [R4-ospf-1]silent-interface all	//所有接口静默
   [R4-ospf-1]undo silent-interface GigabitEthernet 0/0/1	//g0/0/1除外
   ```

5. 在其他网关处进行类似配置，使得所有`PC`都收不到`OSPF`的`Hello`报文

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]silent-interface g0/0/0
       
   R2:
   [R2]ospf
   [R2-ospf-1]silent-interface g0/0/1
       
   R5:
   [R5]ospf
   [R5-ospf-1]silent-interface g0/0/1
   ```

   此时所有`PC`均不会再收到`OSPF`报文

6. 被动接口特性为只是不再收发任何`OSPF`协议报文，但是被动接口所在网段的直连路由条目如果已经在`OSPF`的通告中，那么也会被其他`OSPF`邻居路由器接收到

   如我们最开始利用`OSPF`已经通知了各网段信息，此时我们启用被动接口时保证了这个接口不再收发`OSPF`信息，但实际上这段路由信息已经在先前通告出去了，因此其他路由器仍然知道通向此网段的路径。

# 18. 理解OSPF Router-ID #

一些动态路由协议要求使用`Router-ID`作为路由器的身份标示，如果在启动这些路由协议时没有指定`Router-ID`，则默认使用路由器全局下的路由管理`Router-ID`，当路由器未配置任何`IP`地址时，其`Router-ID`为`0.0.0.0`

`Router-ID`的**选举规则**为：

1. 如果通过`Router-ID`命令配置了`Router-ID`，则按照配置结果设置
2. 如果存在配置了`IP`地址的`Loopback`接口，则选择`Loopback`接口地址中最大的地址作为`Router-ID`
3. 从其他接口的`IP`地址中选择最大的地址作为`Router-ID`【不考虑接口的`Up/Down`情况】

当且仅当被选为`Router-ID`的接口IP地址被删除/修改，才触发重新选择过程

`Router-ID`改变之后，各协议需要通过手工执行`reset`命令才会重新选取新的`Router-ID`

## 实验目的 ##

+ 理解`Router-ID`的选举规则
+ 掌握`OSPF`手动配置`Router-ID`的方法
+ 理解`OSPF`中`Router-ID`必须唯一的意义

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/05/UCc8iqt2gPkZ3Br.png" alt="image-20220505133256187" style="zoom:50%;" />

## 实验步骤 ##

1. 如图所示配置各设备基本信息，路由器接口主机号若未标明则对应路由器编号

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.1.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 10.0.12.1 24
   [R1-GigabitEthernet0/0/1]int loopback 0
   [R1-LoopBack0]ip address 1.1.1.1 32
       
   R2:
   <Huawei>SYS
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0	
   [R2-GigabitEthernet0/0/0]ip address 10.0.12.2 24
   [R2-GigabitEthernet0/0/0]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 10.0.23.2 24
   [R2-GigabitEthernet0/0/1]int g0/0/2
   [R2-GigabitEthernet0/0/2]ip address 10.0.24.2 24
   [R2-GigabitEthernet0/0/2]int loopback 0
   [R2-LoopBack0]ip address 2.2.2.2 32
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.23.3 24
   [R3-GigabitEthernet0/0/0]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.2.254 24
   [R3-GigabitEthernet0/0/1]int loopback 0
   [R3-LoopBack0]ip address 3.3.3.3 32
       
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.24.4 24
   [R4-GigabitEthernet0/0/0]int g0/0/1
   [R4-GigabitEthernet0/0/1]ip address 10.0.3.254 24
   [R4-GigabitEthernet0/0/1]int loopback 0
   [R4-LoopBack0]ip address 4.4.4.4 32
   ```

2. 查看设备`R2`的`Router-ID`信息，观察其是否如我们先前设想一致，由环回接口担任`Router-ID`

   <img src="https://s2.loli.net/2022/05/05/a4uZeFQRI1PYX5s.png" alt="image-20220505135420634" style="zoom: 67%;" />

   **这是为什么呢？**

   原因是接口配置顺序会影响`Router-ID`的选举，当设备第一次对接口IP进行配置时便会触发`Router-ID`选举，而此时设备有有且仅有这一个`IP`地址，所以该地址便被选来作为设备`Router-ID`，由于选举已经完成，因此即使后面配置了优先级更高的环回接口也无济于事，除非开启重新选举`Router-ID`或原先被选为`Router-ID`的地址消失。

   我们取消`R2`的`g0/0/0`接口配置的地址，看看`Router-ID`是否会变成环回接口地址

   <img src="https://s2.loli.net/2022/05/05/yKpBXTONnYAzM8F.png" alt="image-20220505140541137" style="zoom: 67%;" />

   *注：`g0/0/0`的`IP`地址记得重新配置，不然后边`OSPF`无法连通全网*

3. 我们也可以采取手动配置方式强制指定路由器的`Router-ID`，这样配置后，即使该地址现在已经不是路由器任何接口的地址，仍保持其`Router-ID`的地位，不会触发重新选举

   ```java
   [R2]router id 2.2.2.2		//强制指定R2的Router-ID为2.2.2.2
   ```

   **一般建议采用环回接口地址作为路由协议的`Router-ID`，因为环回接口是逻辑接口，比物理接口更加稳定。在对网络操作时，网络管理员有可能误操作导致物理接口地址删除或改动，而环回接口一般不会去改动。**

4. 在所有路由器上配置`OSPF`协议，并都运行在区域`0`内。使用`ospf router-id`命令来配置`OSPF`协议私有`Router-ID`，如果不配置，则默认使用全局下的`Router-ID`

   ```java
   R1:
   [R1]ospf router-id 1.1.1.1	//将环回接口设置为OSPF协议私有Router-ID
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.1.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255
       
   R2:
   [R2]ospf router-id 2.2.2.2
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
       
   R3:
   [R3]ospf router-id 3.3.3.3
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.2.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
       
   R4:
   [R4]ospf router-id 4.4.4.4
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.3.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
   ```

   完成此设置后各`PC`应当能够相互`ping`通，查看当下`R2`的`OSPF`邻居信息

   <img src="https://s2.loli.net/2022/05/05/2ridApGawBOMzeF.png" alt="image-20220505143920390" style="zoom: 50%;" />

5. 我们手动将`R2`的`Router-ID`修改为`3.3.3.3`，看看会发生什么

   <img src="https://s2.loli.net/2022/05/05/3JcokCNbOIBMfqV.png" alt="image-20220505145736460" style="zoom:50%;" />

   此时`PC1`与`PC2`无法`ping`通，说明网络已经发生故障，无法正常通信。验证了`OSPF`建立直连邻居关系时，`Router-ID`一定不能重叠

6. 那么`OSPF`非直连邻居`Router-ID`重叠又会发生什么呢？我们将`R2`的`OSPF`私有`Router-ID`改回去，同时将`R4`私有`OSPF`的`Router-ID`改为`3.3.3.3`

   ```java
   R2:
   [R2]ospf router-id 2.2.2.2
   [R2-ospf-1]q
   [R2]q
   <R2>reset ospf process 
   Warning: The OSPF process will be reset. Continue? [Y/N]:y
       
   R4:
   [R4]ospf router-id 3.3.3.3
   [R4-ospf-1]q
   [R4]q
   <R4>reset ospf process 
   Warning: The OSPF process will be reset. Continue? [Y/N]:y
   ```

   查看`R2`的邻居表

   <img src="https://s2.loli.net/2022/05/05/IUtV7sKp8dRS6C2.png" alt="image-20220505150532181" style="zoom: 50%;" />

   此时虽然邻居关系正常了，但是通信依旧无法进行。

   这是因为R2认为是同一个OSPF邻居，但是链路状态确认LSA又不一致，造成链路状态数据库发送错误，无法计算出正确的路由信息。

   综上所述，**OSPF协议的Router-ID务必要在整个路由选择域内保持唯一**

# 19. OSPF的DR和BDR #

在`OSPF`的**广播类型网络和`NBMA`类型网络中**，如果网络中有`n`台路由器，若任意两台路由器之间都要建立邻居关系，则需要建立`n*(n-1)/2`个邻居关系，即当路由器很多时，则需要维护的邻接关系就很多，两两之间需要发送的报文也就很多，这会造成很多内容重复的报文在网络中传递，浪费了设备的带宽资源。

因此在广播和`NBMA`类型网络中，`OSPF`协议定义了指定路由器`DR`【`Designated Router`】，即**所有其他路由器都只将各自链路状态信息发送给`DR`，再由`DR`以组播方式发送至所有路由器**，大大减少`OSPF`数据包的发送。

但是如果`DR`由于某种故障而失效，此时网络中必须重新选举`DR`，并同步链路状态信息，这需要较长较长时间。为了能缩短这个过程，`OSPF`协议又定义了`BDR`【`Backup Designated Router`】的概念，作为`DR`路由器的备份，当`DR`路由器失效时，`BDR`成为`DR`，并再选择新的`BDR`路由器。其他非`DR/BDR`路由器都称为`DR Other`路由器。

每一个含有至少两个路由器的广播类型网络或`NBMA`类型网络都会选举一个`DR`和`BDR`。选举规则如下：

1. 首先比较`DR`优先级，优先级高者成为`DR`，次高成为`BDR`
2. 如果优先级相等，则`Router-ID`高的成为`DR`，次高的成为`BDR`
3. 如果一台路由器的`DR`优先级为`0`，则不参与选举

需要注意的是，`DR`是在某个广播或者`NBMA`网段内进行选举的，是针对路由器的接口而言的。某台路由器在一个接口上可能是`DR`，在另一个接口上有可能是`BDR`或`DR Other`。

若`DR、BDR`已选举完成，人为修改任何一台路由器的`DR`优先级值为最大，也不会抢占成为新的`DR`或`BDR`，即`OSPF`的`DR/BDR`选举是非抢占的

## 实验目的 ##

+ 理解`OSPF`在哪种网络类型中会选举`DR/BDR`
+ 掌握`OSPF DR/BDR`的选举规则
+ 掌握如何更改设备接口上的`DR`优先级
+ 理解`OSPF DR/BDR`选举的非抢占性

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/06/F6mAXWjNkahD493.png" alt="image-20220506193006682" style="zoom:50%;" />

## 实验步骤 ##

1. 按照如图所示进行基本配置

   ```java
   R1:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.1.1 24
   [R1-GigabitEthernet0/0/0]int loopback 0
   [R1-LoopBack0]ip address 1.1.1.1 32
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.1.2 24
   [R2-GigabitEthernet0/0/0]int loopback 0
   [R2-LoopBack0]ip address 2.2.2.2 32
       
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0	
   [R3-GigabitEthernet0/0/0]ip address 172.16.1.3 24
   [R3-GigabitEthernet0/0/0]int loopback 0
   [R3-LoopBack0]ip address 3.3.3.3 32
   
   R4:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 172.16.1.4 24
   [R4-GigabitEthernet0/0/0]int loopback 0
   [R4-LoopBack0]ip address 4.4.4.4 32
   ```

2. 在四台路由器上执行基础`OSPF`网络配置，并将环回地址作为`OSPF`私有`Router-ID`，都运行在区域`0`内

   ```java
   R1:
   [R1]ospf router-id 1.1.1.1
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   <R1>reset ospf process 	//需要重启OSPF才会重新选举Router-ID
   
   R2:
   [R2]ospf router-id 2.2.2.2
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255	
   <R2>reset ospf process 
       
   R3:
   [R3]ospf router-id 3.3.3.3
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   <R3>reset ospf process 
       
   R4:
   [R4]ospf router-id 4.4.4.4
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   <R4>reset ospf process 
   ```

   查看`OSPF`邻居建立情况

   <img src="https://s2.loli.net/2022/05/06/3EYjIloOVwQUk8D.png" alt="image-20220506200159014" style="zoom:50%;" />

3. 查看默认情况下的`DR/BDR`状态

   <img src="https://s2.loli.net/2022/05/06/J3fvx5la4rLqHQ2.png" alt="image-20220506200448891" style="zoom:50%;" />

   原因是默认情况下，每台路由器上的`DR`优先级都为`1`，此时通过`Router-ID`的数值高低进行比较

4. 在每台设备的相关接口上使用`ospf network-type p2mp`命令修改`OSPF`的网络类型为点到多点

   ```java
   R1:
   [R1]int g0/0/0	
   [R1-GigabitEthernet0/0/0]ospf network-type p2mp
       
   R2:
   [R2]int g0/0/0	
   [R2-GigabitEthernet0/0/0]ospf network-type p2mp
   
   R3:
   [R3]int g0/0/0	
   [R3-GigabitEthernet0/0/0]ospf network-type p2mp
   
   R4:
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ospf network-type p2mp
   ```

   再次查看`R1`邻居情况

   <img src="https://s2.loli.net/2022/05/06/ZTkIfB5nNuWtVza.png" alt="image-20220506201259510" style="zoom:50%;" />

   说明在点到多点的网络类型中不选举`DR/BDR`，同样在点到点的网络中也是。

5. 手动将`R1`设置为`DR`，`R2`为`BDR`，并且阻止`R4`参与`DR`与`BDR`的选举

   首先将刚刚设置的`OSPF`点到多点网络修改回广播型网络，接着修改`R1`上`g0/0/0`接口的优先级为`100`，`R2`为`50`，`R4`为`0`，`R3`保持默认不变

   ```java
   R1:
   [R1-GigabitEthernet0/0/0]ospf network-type broadcast 
   [R1-GigabitEthernet0/0/0]ospf dr-priority 100
       
   R2:
   [R2-GigabitEthernet0/0/0]ospf network-type broadcast 
   [R2-GigabitEthernet0/0/0]ospf dr-priority 50    
   
   R3:
   [R3-GigabitEthernet0/0/0]ospf network-type broadcast 
   
   R4:
   [R4-GigabitEthernet0/0/0]ospf network-type broadcast 
   [R4-GigabitEthernet0/0/0]ospf dr-priority 0    
   ```

   <img src="https://s2.loli.net/2022/05/06/MnzLh6FQPZbupT3.png" alt="image-20220506202853637" style="zoom:50%;" />

   利用`reset ospf process`命令重启各路由器后再次查看`DR/BDR`情况

   <img src="https://s2.loli.net/2022/05/06/voeLNS6FIKf8RuH.png" alt="image-20220506203151511" style="zoom:50%;" />

# 20. OSPF开销值、协议优先级及计时器的修改 #

由于路由器上可能同时运行多种动态路由协议，就存在各个路由协议之间路由信息共享和选择的问题。**系统为每一种路由协议设置了不同的默认优先级，当在不同协议中发现同一条路由时，协议优先级高的将被优先选用**。

如果没有直接配置`OSPF`接口的开销值，`OSPF`会根据该接口的带宽自动计算其开销值。计算公式为：`接口开销值=带宽参考值/接口带宽`，取计算结果的整数部分作为接口开销值【当结果小于`1`时取`1`】。通过改变带宽值可以间接改变接口的开销值。

`OSPF`常见的计时器包括`Hello timer`和`Dead timer`，分别决定了`OSPF`发送`Hello`报文的间隔和保持邻居关系的计时器。默认情况下，`P2P、Broadcast`类型接口发送`Hello`报文的时间间隔为`10s`，邻居失效时间为`40s`；`P2MP、NBMA`类型接口发送`Hello`报文的时间间隔为`30s`，邻居失效时间为`120s`。

## 实验目的 ##

+ 掌握配置`OSPF`协议优先级的方法
+ 掌握配置`OSPF`开销的方法
+ 掌握配置`OSPF Hello timer`的方法
+ 掌握配置`OSPF Dead timer`的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/06/BDXm45anMSNI7xk.png" alt="image-20220506210208127" style="zoom:50%;" />

## 实验步骤 ##

1. 根据如图所示进行配置，路由器接口主机号若为特别声明则与其主机号一致

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0	
   [R1-GigabitEthernet0/0/0]ip address 10.0.1.254 24
   [R1-GigabitEthernet0/0/0]int s0/0/1
   [R1-Serial0/0/1]ip address 10.0.12.1 24
   [R1-Serial0/0/1]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 10.0.13.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int s0/0/1
   [R2-Serial0/0/1]ip address 10.0.12.2 24
   [R2-Serial0/0/1]int s0/0/0
   [R2-Serial0/0/0]ip address 10.0.24.2 24
       
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.13.3 24
   [R3-GigabitEthernet0/0/0]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.34.3 24
       
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int s0/0/0
   [R4-Serial0/0/0]ip address 10.0.24.4 24
   [R4-Serial0/0/0]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.34.4 24
   [R4-GigabitEthernet0/0/0]int g0/0/1
   [R4-GigabitEthernet0/0/1]ip address 10.0.45.4 24
   
   R5:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R5
   [R5]int g0/0/0
   [R5-GigabitEthernet0/0/0]ip address 10.0.45.5 24
   [R5-GigabitEthernet0/0/0]int g0/0/1
   [R5-GigabitEthernet0/0/1]ip address 10.0.2.254 24
   ```

2. 在`R1，R2，R4，R5`上开启`OSPF`协议并通告网段，确保`PC1`与`PC2`之间通过`R2`这条线路实现通信

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.1.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255
       
   R2:
   [R2]ospf 
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.12.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.24.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 10.0.45.0 0.0.0.255
   
   R5:
   [R5]ospf
   [R5-ospf-1]area 0
   [R5-ospf-1-area-0.0.0.0]network 10.0.45.0 0.0.0.255
   [R5-ospf-1-area-0.0.0.0]network 10.0.2.0 0.0.0.255
   ```

   设置完成后，`PC1`与`PC2`应当能够正常通信

3. 部署经过`R3`的线路，运行`RIP`协议，我们希望数据能经过`R3`走带宽更大的以太网线路，而不是走`R2`带宽较低的广域网线路

   ```java
   R1:
   [R1]rip
   [R1-rip-1]version 2
   [R1-rip-1]network 10.0.0.0
   
   R3:
   [R3]rip 
   [R3-rip-1]ver	
   [R3-rip-1]version 2
   [R3-rip-1]network 10.0.0.0
   
   R4:
   [R4]rip
   [R4-rip-1]versi	
   [R4-rip-1]version 2
   [R4-rip-1]network 10.0.0.0
   
   R5:
   [R5]rip 
   [R5-rip-1]version 2
   [R5-rip-1]network 10.0.0.0
   ```

   在`R1`上查看到达`PC2`网段的路由条目

   <img src="https://s2.loli.net/2022/05/06/O5Ko2Dl3xhZgwBu.png" alt="image-20220506213201766" style="zoom:50%;" />

   导致不成功的原因是该路由条目可以同时从`OSPF`协议和`RIP`协议获得，当同一路由条目可以通过不同路由协议获得时，首先比较两协议的优先级，路由器将优选优先级高的路由协议。`OSPF`的默认协议优先级为`10`，而`RIP`为`100`，**优先级值越低表示优先级越高**，故而选择了从`OSPF`协议获得的路由条目

4. 我们希望数据能走`R3`线路，只需要修改`OSPF`学习到的路由优先级值，使其高于`RIP`协议默认值即可

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]preference 110
   
   R2:
   [R2]ospf
   [R2-ospf-1]preference 110
   
   R4:
   [R4]ospf
   [R4-ospf-1]preference 110
   
   R5:
   [R5]ospf
   [R5-ospf-1]preference 110
   ```

   此时再次在`R1`上查看前往`PC2`网段的路由条目

   <img src="https://s2.loli.net/2022/05/06/xJ6Mde8TiI71nuQ.png" alt="image-20220506214054331" style="zoom:50%;" />

5. 由于网络中运行不同路由协议将会导致管理不便，现需要更改`R3`配置，使其运行`OSPF`协议。

   在网络调整过程中最重要的就是尽量确保能够使其对用户通信所造成的的影响程度降至最小，并且一般选择在用户网络使用率较少的深夜进行。

   在`R3`上直接部署`OSPF`协议属于区域`0`中，即和`R2`一样都运行`OSPF`协议，那么在相同`OSPF`协议下，路由的选择首先比较链路的开销值，而经过`R2`的线路为广域网链路，开销值明显高于经过`R3`的以太网链路，即仍然维持通过`R3`来转发前往`PC2`网段的流量，风险较小，因此直接在经过`R3`的线路上部署`OSPF`协议。

   我们首先将各设备通过`OSPF`协议学习到的路由优先级改回`10`，让其成为优选路由

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]preference 10
   
   R2:
   [R2]ospf
   [R2-ospf-1]preference 10
   
   R4:
   [R4]ospf
   [R4-ospf-1]preference 10
   
   R5:
   [R5]ospf
   [R5-ospf-1]preference 10
   ```

   接着在`R1，R3，R4`上通告相应网段

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
       
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   ```

   接着查看`R1`到`PC2`网段的路由

   <img src="https://s2.loli.net/2022/05/06/fdXUvkZCjpzPcJO.png" alt="image-20220506220234894" style="zoom:50%;" />

   记得删除原先开启RIP协议的配置，以免造成不必要的隐患

   ```java
   R1:
   [R1]undo rip 1
   Warning: The RIP process will be deleted. Continue?[Y/N]y
       
   R3:
   [R3]undo rip 1
   Warning: The RIP process will be deleted. Continue?[Y/N]y
   
   R4:
   [R4]undo rip 1
   Warning: The RIP process will be deleted. Continue?[Y/N]y
       
   R5:
   [R5]undo rip 1
   Warning: The RIP process will be deleted. Continue?[Y/N]y
   ```

6. 现要求检查流量经过`R2`部分的链路是否正常，但是由于此部分线路开销远大于经过`R3`的路线，从而导致我们无法测试`R2`部分的链路是否正常工作。因此，我们通过手动修改链路开销值的方式，使得流量走向`R2`部分

   ```java
   R1:
   [R1]int g0/0/1
   [R1-GigabitEthernet0/0/1]ospf cost 5000
   ```

   接着我们查看`R1`前往`PC2`网段的路线选择

   <img src="https://s2.loli.net/2022/05/07/GB4wNhZAt2vcpI5.png" alt="image-20220507153339938" style="zoom:50%;" />

   **注意：OSPF链路开销值是基于接口修改的，一定要在路由更新的入接口修改才生效**

7. 由于经过`R3`的线路是以太网，在`OSPF`中的网络类型为广播网络类型，即默认`Hello`计时器和`Dead`计时器是`10s`和`40s`。这样`OSPF`数据的`Hello`报文发送过于频繁，现修改`R1`上`Hello`计时器和`Dead`计时器为`20s`和`80s`。

   ```java
   R1:
   [R1]int g0/0/1
   [R1-GigabitEthernet0/0/1]ospf timer hello 20	
   [R1-GigabitEthernet0/0/1]ospf timer dead 80
   ```

   同时`R3`部分对应`R1`的链路也需要进行修改

   ```java
   R3:
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ospf timer hello 20
   [R3-GigabitEthernet0/0/0]ospf timer dead 20
   ```

   稍等片刻，会发现`R1`与`R3`的邻居关系中断，这是因为`Hello`计时器和`Dead`计时器在`OSPF`广播网络中建立邻居关系时要进行校验，校验一致才能够建立邻居。

# 21. 连接RIP与OSPF网络 #

不同的网络会根据自身的实际情况来选用路由协议。比如有些网络规模很小，为了管理简单，部署了`RIP`；而有些网络很复杂，可以部署`OSPF`。不同路由协议之间不能直接共享各自的路由信息，需要依靠配置路由的引入来实现。

获得路由信息一般由`3`种途径：直连网段、静态配置和路由协议。可以将通过这`3`种途径获得的路由信息引入到路由协议中，**当把这些路由信息引入到路由协议进程以后，这些路由信息就可以在路由协议进程中进行通告了**，也就是说通过配置引入，一种路由协议可以自动获得所有来自另一种协议的所有路由信息。

不同的路由协议计算路由开销的依据是不同的，开销值的大小和范围都是不同的，`OSPF`的开销值基于带宽，而且值的范围很大，`RIP`的开销基于跳数，范围很小，所以当配置`OSPF`和`RIP`相互引入时一定要小心【在华为`VRP`平台上，当引入`OSPF`路由至RIP时，如不指定`Cost`值，开销值将默认设为`1`。尽管如此，网络管理员还是应该手工配置开销值以反映网络的真实情况】。

## 实验目的 ##

+ 理解路由引入的应用场景
+ 掌握`RIP`中引入其他协议的配置
+ 掌握`OSPF`中引入其他协议的配置
+ 掌握路由引入时修改开销值的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/08/s14Wbw9JLiBRqyf.png" alt="image-20220508133644888" style="zoom:50%;" />

## 实验步骤 ##

1. 按照图示进行配置，路由器接口主机号若为特别说明则与其编号一致

   ```java
   R1:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.2.1 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 192.168.2.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 172.16.1.254 24
   [R2-GigabitEthernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.2.2 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/1	
   [R3-GigabitEthernet0/0/1]ip address 192.168.2.3 24
   [R3-GigabitEthernet0/0/1]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 192.168.1.254 24
   ```

2. 搭建`RIP`与`OSPF`网络

   ```java
   R1:
   [R1]rip
   [R1-rip-1]version 2
   [R1-rip-1]network 172.16.0.0
   [R1-rip-1]q
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
   
   R2:
   [R2]rip 
   [R2-rip-1]version 2
   [R2-rip-1]network 172.16.0.0
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 192.168.1.0 0.0.0.255
   ```

   <img src="https://s2.loli.net/2022/05/07/c59IbuDyV8vL1g6.png" alt="image-20220507203546892" style="zoom:50%;" />

3. 此时`R2`只开启了`RIP`，学习不到`OSPF`区域的路由，因为此网段并没有被`RIP`协议通告；`R3`则学习不到`RIP`区域的路由，因为此网段没有被`OSPF`区域通告。

   为了使得`RIP`区域与`OSPF`区域学习到的路由相互分享，需要把`R2`的`RIP`协议路由引入到`R3`的`OSPF`协议中，同样把`R3`的`OSPF`协议路由引入到`R2`的`RIP`协议中。

   在`R1`的`OSPF`进程中使用`import-route rip`命令引入`RIP`路由

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]import-route rip 1
   ```

   配置完成后，查看R3的路由表

   <img src="https://s2.loli.net/2022/05/07/G4APwQhLF8bj7fB.png" alt="image-20220507204937257" style="zoom:50%;" />

   在`R1`的`RIP`进程中使用`import-route ospf`命令引入`ospf`路由

   ```java
   R1:
   [R1]rip	
   [R1-rip-1]import-route ospf 1
   ```

   配置完成后，查看`R2`路由表

   <img src="https://s2.loli.net/2022/05/07/khG5Y9PuoKqcpIW.png" alt="image-20220507205305673" style="zoom:50%;" />

   我们也可以手动指定引入时的开销值

   ```java
   R1:
   [R1]rip
   [R1-rip-1]import-route ospf 1 cost 3
   ```

   <img src="https://s2.loli.net/2022/05/07/xLKiVpr9o5f7OJe.png" alt="image-20220507205737983" style="zoom:50%;" />

# 22. 使用RIP、OSPF发布默认路由 #

默认路由是指目的地址和掩码都是0的路由条目。当路由器无精确匹配的路由时，就可以通过默认路由进行报文转发。

合理利用默认路由，可以在很大程度上减小本地路由表的大小，节约设备资源。默认路由可以在路由器上手工配置，也可以由路由协议自动发布。

`RIP，OSPF`这两种路由协议都可以通过配置使路由器对协议邻居发布默认路由，并且可以设置该路由的度量值。

## 实验目的 ##

+ 理解默认路由的应用场景
+ 掌握`RIP`发布默认路由的配置
+ 掌握`OSPF`发布默认路由的配置

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/08/TaWxpqkgL45vdJ7.png" alt="image-20220508122724809" style="zoom:50%;" />

## 实验步骤 ##

1. 按照如图所示做好基础配置，路由器接口主机号若为特别说明则等同于其编号

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.2.1 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 192.168.2.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 172.16.1.254 24
   [R2-GigabitEthernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.2.2 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/01
   [R3-GigabitEthernet0/0/1]ip address 192.168.2.3 24
   [R3-GigabitEthernet0/0/1]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 192.168.1.254 24
   ```

2. 根据图示区域配置`RIP`与`OSPF`协议

   ```java
   R1:
   [R1]rip
   [R1-rip-1]version 2
   [R1-rip-1]network 172.16.0.0
   [R1-rip-1]q
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
   
   R2:
   [R2]rip
   [R2-rip-1]version 2
   [R2-rip-1]network 172.16.0.0
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 192.168.1.0 0.0.0.255
   ```

   <img src="https://s2.loli.net/2022/05/08/eDbqupMrX7NOwx2.png" alt="image-20220508124034348" style="zoom:50%;" />

   此时`R2`和`R3`上由于路由学习协议不同，因此不能互相进行学习，此处图略，可自行查看

3. 配置`RIP`发布默认路由，使得`R2`在不清楚`R3`部分路由的情况下仍能完成通信【最好保证通往另一领域的链路只有一条，楼主经过测试，出口链路有多条时默认路由会出现歧义😂】。配置`OSPF`发布默认路由同理

   ```java
   R1:
   [R1]rip
   [R1-rip-1]default-route originate
   [R1-rip-1]q
   [R1]ospf	
   [R1-ospf-1]default-route-advertise always 
   ```

   <img src="https://s2.loli.net/2022/05/08/1W2CrhGXQs9N4ip.png" alt="image-20220508133000552" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/08/BvU3cVCs6L8dlpn.png" alt="image-20220508133057093" style="zoom:50%;" />

   通过配置看到在`RIP`和`OSPF`中都可以为各自路由协议发布默认路由。配置默认路由在可以保证网络的可达性情况下，不仅可以保护网络的私密性，同时能够有效减少路由表中路由条目的数量，使得路由器不需要维护大量的路由消息，同时其配置和维护相对简单。

# 23. DHCP #

## 1. 基于接口地址池的DHCP ##

随着网络规模的扩大和网络复杂程度的提高，计算机数量超过可分配的`IP`地址情况将会经常出现，`DHCP`【`Dynamic Host Configuration Protocol，动态主机配置协议`】就是为满足这些需求而发展起来的

`DHCP`协议采用`客户端Client/服务器Server`方式工作，`DHCP Client`向`DHCP Server`动态请求配置信息，`DHCP Server`根据策略返回相应的配置信息【如`IP`地址等】

`DHCP`客户端首次登录网络时，主要通过`4`个阶段与`DHCP`服务器建立联系

1. **发现阶段**：客户端以广播方式发送`DHCP_Discover`报文，只有`DHCP`服务器才会进行响应
2. **提供阶段**：`DHCP`服务器收到客户端的`DHCP_Discover`报文后，从`IP`地址池中挑选一个尚未分配的`IP`地址给客户端，向该客户端发送包含出租IP地址和其他设置的`DHCP_Office`报文
3. **选择阶段**：如果有多台`DHCP`服务器向该客户端发来`DHCP_Office`报文，客户端只接受第一个收到的`DHCP_Office`报文，然后以广播方式向各`DHCP`服务器回应`DHCP_Request`报文
4. **确认阶段**：当`DHCP`服务器收到`DHCP`客户端回答的`DHCP_Request`报文后，便向客户端发送包含它所提供的`IP`地址和其他设置的`DHCP_ACK`确认报文

### 实验目的 ###

+ 掌握`DHCP Server`配置方法
+ 掌握基于接口地址池的`DHCP Server`配置方法
+ 掌握配置`DHCP`租期/不参与自动分配地址/`DNS`服务器地址方法
+ 掌握配置和检测`DHCP`客户端的方法

### 实验拓扑 ###

<img src="https://s2.loli.net/2022/04/12/wFJgR3PjXOYUxpo.png" alt="image-20220412145937630" style="zoom: 50%;" />

### 实验步骤 ###

1. 配置路由器两个端口的`IP`地址与掩码并将`PC`设置为`DHCP`模式

   ```java
   <Huawei>sys		//进入系统视图
   [Huawei]undo info-center en		//关闭消息提醒
   [Huawei]sysname R1
   [R1]int g0/0/0	//进入g0/0/0接口
   [R1-GigabitEthernet0/0/0]ip address 192.168.1.254 24	//配置地址与掩码
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 192.168.2.254 24
   ```

2. 在路由器上开启`DHCP`功能并且开启接口的`DHCP`服务

   ```java
   [R1]dhcp enable			//开启DHCP功能
   [R1]int g0/0/0			//进入g0/0/0接口
   [R1-GigabitEthernet0/0/0]dhcp select interface //开启此接口的DHCP服务
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]dhcp select interface
   ```

   接口地址池可动态分配`IP`地址范围就是接口`IP`地址所在网段，且只在此接口下有效

3. 在`g0/0/1`上配置`DHCP`服务器地址池中`IP`地址的租用有效期为`2`天【默认为`1`天】，超过租期后该地址会重新分配

   ```java
   [R1-GigabitEthernet0/0/1]dhcp server lease day 2	//从此接口分配出的地址有效期为2天
   ```

4. 在`g0/0/0`接口配置接口地址池中不参与自动分配的`IP`地址范围为`192.168.1.1`到`192.168.1.10`

   ```java
   [R1-GigabitEthernet0/0/0]dhcp server excluded-ip-address 192.168.1.1 192.168.1.1
   0	//192.168.1.1~192.168.1.10这段地址不会被g0/0/0分配出去
   ```

   由于某些服务器`IP`地址是固定的【如`DNS`服务器】，因此这个地址不应当被划分给其他设备，所以将此地址从地址池中排除是相当有必要的

5. 此时通过`ipconfig`命令可以查看到两个`PC`的`IP`配置情况

   <img src="https://s2.loli.net/2022/04/12/LlC2ENcpxzo9JQR.png" alt="image-20220412155007555" style="zoom:50%;" />

6. 我们还可以指定接口地址池下的`DNS`服务器

   ```java
   //指定g0/0/1接口地址池下的DNS服务器地址为8.8.8.8，这里PC2的DNS服务器地址就是8.8.8.8
   [R1-GigabitEthernet0/0/1]dhcp server dns-list 8.8.8.8
   ```

##  2. 基于全局地址池的DHCP

基于接口地址池的`DHCP`服务器，连接这个接口网段的用户都从该接口地址池中获取`IP`地址等配置信息，由于地址池绑定在特定的接口上，可以限制用户的使用条件，因此保障了安全性的同时也存在一定的局限性。**当用户从不同接口接入DHCP服务器且需要从同一个地址池里获取IP地址时【接口地址池做不到】，就需要配置基于全局地址池的DHCP**

配置基于全局地址池的`DHCP`服务器，从所有接口上连接的用户都可以选择该地址池中的地址，也就是说全局地址池是一个公共地址池

路由器支持工作在全局地址池模式的接口有三层接口及其子接口、三层`Ethernet`接口及其子接口、三层`Eth-Trunk`接口及其子接口和`VLANIF`接口

### 实验目的 ###

+ 掌握`DHCP Server`配置方法
+ 掌握基于全局地址池的`DHCP Server`配置方法
+ 掌握配置`DHCP`租期/不参与自动分配地址方法
+ 掌握配置和检测`DHCP`客户端的方法

### 实验拓扑 ###

<img src="https://s2.loli.net/2022/04/12/DnBF71OziKtrqcm.png" alt="image-20220412202404210" style="zoom:50%;" />

### 实验步骤 ###

1. 配置路由器端口的`IP`地址与掩码并将`PC`设置为`DHCP`模式

   ```java
   <Huawei>system-view
   [Huawei]undo info-center enable
   Info: Information center is disabled.
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 192.168.1.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 192.168.2.254 24
   ```

2. 创建地址池并且确定下该地址池的网段，网关以及`DNS`服务器地址，接着在对应网段的接口处开启全球`DHCP`【顺序不能乱，由于`IP`地址分配是从`254`往下递减分配的，若先在接口处开启了全球`DHCP`，则在配置完网段后可能就会把`254`这个地址占用了，从而导致配置网关地址为`254`时失败】

   【如果一定要反过来设置的话可以先配置网关`254`地址，接着再配置网段，从而避免上述地址占用问题】

   ```java
   [R1]dhcp enable 		//在路由器上开启DHCP服务
   [R1]ip pool huawei1		//创建地址池，名为huawei1
   [R1-ip-pool-huawei1]network 192.168.1.0	//huawei1对应的网段，不写掩码默认为24
   [R1-ip-pool-huawei1]lease day 2			//租借时间为2天
   [R1-ip-pool-huawei1]gateway-list 192.168.1.254	//网关地址
   [R1-ip-pool-huawei1]excluded-ip-address 192.168.1.250 192.168.1.253	//此地址不分配
   [R1-ip-pool-huawei1]dns-list 8.8.8.8	//地址池huawei1分配出去的地址的DNS地址为8.8.8.8
   [R1-ip-pool-huawei1]quit
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]dhcp select global //再次接口开启全球DHCP模式，此时此接口可以动												态分配IP地址
   [R1-GigabitEthernet0/0/0]quit
   [R1]ip pool huawei2
   [R1-ip-pool-huawei2]network 192.168.2.0
   [R1-ip-pool-huawei2]gateway-list 192.168.2.254	
   [R1-ip-pool-huawei2]dns-list 8.8.8.8
   [R1]int g0/0/1
   [R1-GigabitEthernet0/0/0]dhcp select global
   ```

   在配置了`PC`的`DHCP Client`情况下，此时应当可以看到其被动态分配的`IP`地址，掩码，网关与`DNS`地址

   <img src="https://s2.loli.net/2022/04/12/FPyI2bkvjneg817.png" alt="image-20220412215209548" style="zoom:50%;" />

3. 查看此时`IP`地址池情况

   <img src="https://s2.loli.net/2022/04/12/2sTQPUFqX3gLw1O.png" alt="image-20220412215519325" style="zoom:50%;" />

## 接口地址池与全局地址池的比较 ##

**接口地址池**为**连接到同一网段的主机或终端**分配`IP`地址，可以在服务器的接口下执行
`dhcp select interface`命令，配置`DHCP`服务器采用接口地址池的`DHCP`服务器模式为客户端分配`IP`地址。

**全局地址池**为**所有连接到`DHCP`服务器的终端**分配`IP`地址，可以在服务器的接口下执行
`dhcp select global`命令，配置`DHCP`服务器采用全局地址池的`DHCP`服务器模式为客户端分配`IP`地址。

**接口地址池的优先级比全局地址池高**。配置了全局地址池后，如果又在接口上配置了地址池，客户端将会从接口地址池中获取`IP`地址。

## 3. 配置DHCP中继 ##

由于在`IP`地址动态获取的过程中，客户端采用广播方式发送请求报文，而**广播报文不能跨越网段传送，因此`DHCP`只适用于`DHCP`客户端和服务器处于同一个网段内的情况**。当多个网段都需要进行动态`IP`地址分配时，就需要在所有网段上都设置一个`DHCP`服务器，这显然是不易管理和维护的。

**`DHCP`中继可以使客户端通过它与其他网段的`DHCP`服务器通信，最终获取`IP`地址**，解决了`DHCP`客户端不能跨网段向服务器动态获取`IP`的问题。

这样，在多个不同网络上的`DHCP`客户端可以使用同一个`DHCP`服务器，既节约了成本，又便于进行集中管理和维护。路由器或三层交换机都可以充当`DHCP`中继设备

### 实验目的 ###

+ 理解`DHCP`中继的应用场景
+ 掌握`DHCP`中继的配置

### 实验拓扑 ###

<img src="https://s2.loli.net/2022/04/13/Q7SZRWXTIf9wtBD.png" alt="image-20220413212146230" style="zoom:50%;" />

### 实验步骤 ###

1. 根据标识配置路由器各接口的`IP`地址与掩码

   ```java
   R1:
   <Huawei>system-view		//进入系统视图
   [Huawei]undo info-center enable		//关闭消息提醒
   [Huawei]sysname R1	//设备重命名
   [R1]int g0/0/1		//进入接口	
   [R1-GigabitEthernet0/0/1]ip address 10.1.1.254 24
   [R1-GigabitEthernet0/0/1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 200.1.1.1 24
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 200.1.1.2 24
   [R2-GigabitEthernet0/0/0]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 100.1.1.2 24
       
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 100.1.1.3 24

2. 在所有路由器上都配置运行`OSPF`协议，所有网段发布到区域`0`中

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.1.1.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 200.1.1.0 0.0.0.255
   
   R2:
   [R2]ospf 
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 200.1.1.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 100.1.1.0 0.0.0.255
   
   R3:
   [R3]ospf 
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 100.1.1.0 0.0.0.255
   ```

   配置完成后查看路由表进行检查，看看路由器间是否已能相互学习路由表，我们这里以`R1`为例

   <img src="https://s2.loli.net/2022/04/13/gaeLxPHwRvc2JyE.png" alt="image-20220413212456298" style="zoom:50%;" />

3. 总部路由器`R3`配置为`DHCP`服务器，负责为分部的网络分配`IP`地址

   ```java
   R3:
   [R3]dhcp enable 	//在R3上开启DHCP服务
   [R3]ip pool pool0	//创建全局地址池，名字为pool0
   [R3-ip-pool-pool0]network 10.1.1.254 mask 255.255.255.0	//确认分配出去地址的网段与掩码
   [R3-ip-pool-pool0]gateway-list 10.1.1.254	//确认分配出去地址的网关
   [R3-ip-pool-pool0]int g0/0/1
   [R3-GigabitEthernet0/0/1]dhcp select global //正式开放g0/0/1端口分发地址
   ```

   查看当前地址池情况

   <img src="https://s2.loli.net/2022/04/16/61dCcW7vePqVjaF.png" alt="image-20220413203227509" style="zoom:50%;" />

   此时两台`PC`均不可获得`IP`地址，因为`DHCP`服务器与其跨越了网段，而`DHCP_Discover`广播报文是无法跨越网段的

   <img src="https://s2.loli.net/2022/04/13/E79MDl1vUjcgNeu.png" alt="image-20220413214804090" style="zoom:50%;" />

4. 接着我们配置`R1`为`DHCP`中继设备，指定`DHCP`服务器为`R3`

   这时**如果`R1`从`e0/0/1`接口收到`PC`的`DHCP`广播请求包，`R1`作为`DHCP`中继设备会以单播形式转发请求包到中继所指明的`DHCP`服务器`R3`；`R3`收到`DHCP`请求包后，会把分配的`IP`地址通过单播包返回给`R1`；`R1`再把地址信息发送给`PC`**

   + **配置方式①**：直接在`R1`的`e0/0/1`下开启`DHCP`中继功能，并直接指定`DHCP`服务器IP地址为`100.1.1.1`

     ```java
     R1:
     [R1]dhcp enable //在R1上开启DHCP服务
     [R1]int g0/0/1
     [R1-GigabitEthernet0/0/1]dhcp select relay //在此接口开启中继功能
     [R1-GigabitEthernet0/0/1]dhcp relay server-ip 100.1.1.3//确认远端的的DHCP服务器地址
     ```

   + **配置方法②**：在`R1`上创建`DHCP`服务器组，指定组名为`dhcp-group1`，并将`DHCP`服务器的地址添加进组中，接着在接口下应用配置好的`dhcp-group1`

     ```java
     R1:
     [R1]dhcp server group dhcp-group1	//创建DHCP服务组，名为dhcp-group1
     [R1-dhcp-server-group-dhcp-group1]dhcp-server 100.1.1.3	//将DHCP服务器地址加入组中
     [R1-dhcp-server-group-dhcp-group1]int g0/0/1
     [R1-GigabitEthernet0/0/1]dhcp select relay //在接口处开启DHCP中继功能
     [R1-GigabitEthernet0/0/1]dhcp relay server-select dhcp-group1	//将dhcp-group1																的规则应用其中
     ```

   两种方式都可以达到同样的配置要求，相比而言，在接口下直接指定`DHCP`服务器`IP`地址的方式较简单。但如果中继设备上有多个接口需要配置`DHCP`中继功能，则要在所有接口上重复同样的配置，产生的配置量较大。这种情况就应该使用**服务器组的方式，仅在全局定义一次，在每个接口重复调用**即可

5. 选择上述其中之一配置完成后，我们再次查看`PC`的`IP`地址

   <img src="https://s2.loli.net/2022/04/13/KYbXwR15F8vz62t.png" alt="image-20220413214923703" style="zoom:50%;" />

#  24. 三层交换机开启OSPF与路由器互通

交换机属于二层设备，只能通过`MAC`地址表进行转发且转发范围受`VLAN`限制

若我们想跨`VLAN`进行通信，通常方法有两种

1. 利用路由器实现单臂路由
2. 通过三层交换机的虚拟接口`Vlanif`进行转发，`Vlanif`接口编号必须与`VLAN`编号一一对应

## 实验目的 ##

+ 理解`Vlanif`接口的作用
+ 了解数据的转发过程
+ 了解`OSPF`在三层交换机上的特殊形式

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/04/20/e72FDJ8bpGWkwYz.png" alt="image-20220420205420873" style="zoom:50%;" />

## 实验步骤 ##

1. 手动为各个`PC`分配好图示`IP`地址，掩码与网关，在`S1`上创建`VLAN 10`，在`S2`上创建`VLAN 20`，并将两个接口分别配置为`Access`类型与`Trunk`类型

   ```java
   S1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname S1
   [S1]vlan batch 10
   [S1]int e0/0/1
   [S1-Ethernet0/0/1]port link-type access 
   [S1-Ethernet0/0/1]port default vlan 10
   [S1-Ethernet0/0/1]int g0/0/1	
   [S1-GigabitEthernet0/0/1]port link-type trunk 
   [S1-GigabitEthernet0/0/1]port trunk allow-pass vlan all
       
   S2:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname S2
   [S2]vlan batch 20
   [S2]int e0/0/1
   [S2-Ethernet0/0/1]port link-type access 
   [S2-Ethernet0/0/1]port default vlan 20
   [S2-Ethernet0/0/1]int g0/0/2	
   [S2-GigabitEthernet0/0/2]port link-type trunk 
   [S2-GigabitEthernet0/0/2]port trunk allow-pass vlan all
   ```

2. 将`R1`的`e0/0/1`设置为`PC3`的网关，将`g0/0/3`设置为`10.255.255.254/8`

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int e0/0/1
   [R1-Ethernet0/0/1]ip address 192.168.3.254 24
   [R1-Ethernet0/0/1]int g0/0/3
   [R1-GigabitEthernet0/0/3]ip address 10.255.255.254 8
   ```

3. 此时我们应当想一想`S3`该如何配置才能保证跨路由通信？我们不妨假设此时`PC1 ping PC2`，看看需要哪些操作

   1. 首先`PC1`将`PC2`的`IP`地址与自身掩码相与，发现`PC2`与自己不处于同一个网段，所以直接发送`ARP`请求报文【经由`S1`的`e0/0/1`接口，因此会携带`VLAN 10`标签】希望能够得到`PC1`网关的`MAC`地址，而二层交换机作为数据链路层设备是没有`IP`地址的，此时便需要用到三层交换机的`Vlanif`接口了

   2. 我们在`S3`上配置`Vlanif 10`逻辑接口，接口的`IP`地址设置为`PC1`的网关。此时若再有携带`VLAN 10`标签的`ARP`请求信息时，交换机便会将`ARP`的目的地址与`Vlanif 10`接口的`IP`地址进行比较，若相同，则将交换机的`MAC`地址返回给`PC1`，意思为：你要找的网关地址在这里！

      **由Vlanif接口接收数据时会剥离Vlanif接口编号对应的Vlan标签；由Vlanif接口发送出数据时会打上Vlanif接口编号对应的VLAN标签，Vlanif接口地址通常为网关地址** 

   3. `PC1`得知了目的`MAC`地址后便会发送原本想发送的`ICMP`报文，**由`Vlanif 10`接口进行接收，接收后会剥离原本数据携带的`VLAN 10`标签**。接着交换机查看自己的路由表，看看要把这从网关接收到的数据发往何处

   4. 此时我们还未帮助`PC2`网段设立`Vlanif 20`逻辑接口，所以此时路由表上仅仅只有设置过`Vlanif 10`逻辑接口的`PC1`网段。因此我们设置`Vlanif 20`接口，与`Vlanif 10`接口同理

      ```java
      S3:
      [S3]vlan batch 10 20		//创建vlanif前要确保交换机存在对应的VLAN
      [S3]int vlanif 10
      [S3-Vlanif10]ip address 192.168.1.254 24
      [S3-Vlanif10]int vlanif 20
      [S3-Vlanif20]ip address 192.168.2.254 24
      ```

      我们可用`display ip route-table`查询此时`S3`的路由表情况，发现不管是去往`PC1`网段还是去`PC2`网段都已经有了直连通路，下一跳地址为`Vlanif`逻辑接口

   5. 此时`PC1 ping PC2`能通么？答案当然是不行的，因为我们还未配置`S3`的`g0/0/1`与`g0/0/2`端口类型，无法处理携带`VLAN`标签的信息，那么应该将这两个端口设置`Access`还是`Trunk`呢？

      答案是`Trunk`。假设`S3`的`g0/0/1`为`Access`接口，默认`VLAN`为`10`，则当信息从`S3`的`g0/0/1`接口发出来时便会被剥离`VLAN 10`标签，从而导致无法通过`S1`的`e0/0/1`接口，自然也就无法到达`PC1`

      ```java
      S3:
      [S3]int g0/0/1	
      [S3-GigabitEthernet0/0/1]port link-type trunk 
      [S3-GigabitEthernet0/0/1]port trunk allow-pass vlan all
      [S3-GigabitEthernet0/0/1]int g0/0/2
      [S3-GigabitEthernet0/0/2]port link-type trunk
      [S3-GigabitEthernet0/0/2]port trunk allow-pass vlan all
      ```

      此时`PC1`与`PC2`可相互连通

4. 我们如何令`PC3`与`PC1,PC2`相互连通呢？我们不妨假设此时有`PC3 ping PC1`

   1. `PC3`通过掩码计算得出`PC1`与自身不处于同一网段，因此询问网关地址，而`R1`的`e0/0/1`接口正好为`PC3`的网关，因此数据顺利到达`R1`

   2. `R1`接着查询路由表，试图寻找关于`PC1`的路由，由于我们并未静态配置任何路由，也没有动态学习路由，因此此时路由表中仅有直连链路。我们配置`OSPF`，使`S3`与`R1`能够相互学习路由信息，同时我们还需要打通`S3`的`g0/0/3`与`R1`的`g0/0/3`，以便能够顺利交流链路信息，打通的方法依旧是`Vlanif`接口

      ```java
      S3:
      [S3]vlan batch 100
      [S3-Vlanif20]int vlanif 100
      [S3-Vlanif100]ip address 10.0.0.1 8
      [S3]ospf
      [S3-ospf-1]area 0
      [S3-ospf-1-area-0.0.0.0]network 192.168.1.0 0.0.0.255
      [S3-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
      [S3-ospf-1-area-0.0.0.0]network 10.0.0.0 0.255.255.255
          
      R1:
      [R1]ospf
      [R1-ospf-1]area 0
      [R1-ospf-1-area-0.0.0.0]network 192.168.3.0 0.0.0.255
      [R1-ospf-1-area-0.0.0.0]network 10.0.0.0 0.255.255.255
      ```

      此时仍然还不能达到我们的要求，因为`Vlanif 100`只有接收到带有`VLAN 100`标签的数据才能发挥作用，进入三层转发，而经由路由器转发的数据是不携带`VLAN`标签的，解决这个问题的办法就是`Access`接口

      ```java
      S3:
      [S3]int g0/0/3
      [S3-GigabitEthernet0/0/3]port link-type access 
      [S3-GigabitEthernet0/0/3]port default vlan 100
      ```

      此时，经由`g0/0/3`发送至路由器的信息会被剥离`VLAN 100`标签，而从路由器发送过来的数据又会被打上`VLAN 100`的标签，完美满足我们的需求

5. 至此，`PC3`与`PC2`,`PC1`都可互通

   <img src="https://s2.loli.net/2022/04/20/BVPrfswLEMznqRY.png" alt="image-20220420221359963" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/04/20/zy2MhfOqo6lXFYs.png" alt="image-20220420221525141" style="zoom:50%;" />

<img src="https://s2.loli.net/2022/04/20/15tuVNP9MyswIvW.png" alt="image-20220420221552699" style="zoom:50%;" />

# 25. 配置基本访问控制列表ACL #

访问控制列表`ACL（Access Control List）`是由`permit`或`deny`语句组成的一系列有顺序的规则集合，这些规则根据数据报的源地址、目的地址、源端口、目的端口等信息来描述。`ACL`规则通过匹配报文中的信息对数据包进行分类，路由设备根据这些规则判断哪些数据包可以通过，哪些数据包需要拒绝

基本`ACL`可使用报文的源`IP`地址、时间段信息来定义规则，编号范围为`2000~2999`

一个`ACL`可以由多条"`deny/permit`"语句组成，每一条语句描述一条规则，每条规则则有一个`Rule-ID`。`Rule-ID`可以由用户进行配置，也可以由系统自动根据步长生成，默认步长为`5`，`Rule-ID`默认按照配置先后顺序分配`0，5，10，15`等，匹配按照`Rule-ID`的顺序，从小到大进行匹配

### 实验目的 ###

+ 理解基本访问控制列表的应用场景
+ 掌握配置基本访问控制列表的方法

### 实验拓扑 ###

<img src="https://s2.loli.net/2022/04/30/WtsemSc15Rjqhra.png" alt="image-20220430101731887" style="zoom:50%;" />

### 实验步骤 ###

1. 根据图示进行基础配置，路由器编号即为其主机号

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.13.1 24
   [R1-GigabitEthernet0/0/0]int LoopBack 0
   [R1-LoopBack0]ip address 1.1.1.1 32
       
   R2:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 10.0.23.2 24
   [R2-GigabitEthernet0/0/0]int LoopBack 0
   [R2-LoopBack0]ip address 2.2.2.2 32
       
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.13.3 24
   [R3-GigabitEthernet0/0/0]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.23.3 24
   [R3-GigabitEthernet0/0/1]int g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 10.0.34.3 24
   [R3-GigabitEthernet0/0/2]int LoopBack 0
   [R3-LoopBack0]ip address 3.3.3.3 32    
       
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.34.4 24
   [R4-GigabitEthernet0/0/0]int LoopBack 0
   [R4-LoopBack0]ip address 4.4.4.4 32    

2. 开启`OSPF`，通告各网段，设置环回接口模拟连接在路由器上的`PC`

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 1.1.1.1 0.0.0.0
       
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 2.2.2.2 0.0.0.0
       
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 3.3.3.3 0.0.0.0
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 4.4.4.4 0.0.0.0
   ```

   查看路由表，确保`OSPF`协议已发挥作用

   <img src="https://s2.loli.net/2022/04/30/1fBdzuohgVMmWKQ.png" alt="image-20220430103158920" style="zoom:50%;" />

3. 在`R4`上配置`telnet`相关配置，配置用户密码为`huawei`

   ```java
   R4:
   [R4]user-interface vty 0 4	//进入虚拟终端，最多允许5人进入[0~4号]
   [R4-ui-vty0-4]authentication-mode password	//设置口令为密码模式
   [R4-ui-vty0-4]set authentication password simple huawei	//密码设置为明文huawei
   ```

   此时任何与`R4`互通的三层设备都可以通过输入密码的形式操控`R4`，这样显然是不安全的

   <img src="https://s2.loli.net/2022/04/30/mNc2XDEblhIz59x.png" alt="image-20220430191034906" style="zoom:50%;" />

4. 基本的`ACL`可以针对数据包的源`IP`地址进行过滤，其范围是`2000~2999`，我们利用其设立规则，禁止除`R1`外的设备进行远程连接

   ```java
   R4:
   [R4]acl 2000	//创建一个编号型ACL
   [R4-acl-basic-2000]rule 5 permit source 1.1.1.1 0	//ID5规则：接收1.1.1.1的信息	
   [R4-acl-basic-2000]rule 10 deny source any //ID10规则：拒绝所有信息
   //由于匹配规则是从低匹到高，因此两条规则一起作用后就是拒绝处1.1.1.1外的信息    
   [R4]user-interface vty 0 4	//进入R4的VTY
   [R4-ui-vty0-4]acl 2000 inbound  //将编号2000的ACL运用在数据入方向  
   ```

   此时`R2`想要远程连接`R4`会失败

   <img src="https://s2.loli.net/2022/04/30/kodqKcvC8AhztNM.png" alt="image-20220430194105472" style="zoom: 67%;" />

   而`R1`依旧可以连接`R4`，到此便完成了一个简单`ACL`配置，可使用命令`display acl all`查看设备上所有的访问控制列表

   <img src="https://s2.loli.net/2022/04/30/JBUraFAhfCDpNb4.png" alt="image-20220430194255111" style="zoom: 67%;" />

# 26. 配置高级访问控制列表ACL #

**基本的`ACL`只能用于匹配源`IP`地址**，而在实际应用当中往往需要针对数据包的其他参数进行匹配，比如目的地址、协议号、端口号等，所以基本的`ACL`由于匹配的局限性而无法实现更多的功能，所以就需要使用高级的访问控制表。

高级的访问控制表在匹配项做了扩展，编号范围为`3000~3999`，既可使用报文的源`IP`地址，也可使用目的地址、`IP`优先级、`IP`协议类型、`ICMP`类型、`TCP`源端口/目的端口号等信息来定义规则。

高级访问控制类别可以定义比基本访问控制列表更准确、更丰富、更灵活的规则，也因此得到更加广泛的应用。

## 实验目的 ##

+ 理解高级访问控制列表的应用场景
+ 掌握配置高级访问控制列表的方法
+ 理解高级访问控制列表与基本访问控制列表的区别

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/08/FMB8roUyO1gHN6q.png" alt="image-20220508144805332" style="zoom:50%;" />

## 实验步骤 ##

1. 根据如图所示进行配置，各接口主机号若为特别说明则与其编号一致

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.13.1 24
   [R1-GigabitEthernet0/0/0]int loopback 0
   [R1-LoopBack0]ip address 1.1.1.1 32
   
   R2:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 10.0.23.2 24
   [R2-GigabitEthernet0/0/0]int loopback 0
   [R2-LoopBack0]ip address 2.2.2.2 32
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.13.3 24
   [R3-GigabitEthernet0/0/0]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 10.0.23.3 24
   [R3-GigabitEthernet0/0/1]int g0/0/2
   [R3-GigabitEthernet0/0/2]ip address 10.0.34.3 24
   [R3-GigabitEthernet0/0/2]int loopback 0
   [R3-LoopBack0]ip address 3.3.3.3 32
   
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 10.0.34.4 24
   [R4-GigabitEthernet0/0/0]int loopback 0
   [R4-LoopBack0]ip address 4.4.4.4 32
   [R4-LoopBack0]int loopback 1
   [R4-LoopBack1]ip address 40.40.40.40 32
   ```

2. 在各设备上搭建`OSPF`网络，确保全网互通

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 1.1.1.1 0.0.0.0
   
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 2.2.2.2 0.0.0.0
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 3.3.3.3 0.0.0.0
       
   R4:
   [R4]ospf
   [R4-ospf-1]area 0
   [R4-ospf-1-area-0.0.0.0]network 10.0.34.0 0.0.0.255
   [R4-ospf-1-area-0.0.0.0]network 4.4.4.4 0.0.0.0
   [R4-ospf-1-area-0.0.0.0]network 40.40.40.40 0.0.0.0
   ```

   可通过查看`R1`上的`OSPF`路由条目验证是否全网互通，此处省略。

3. 在`R4`上配置`Telnet`相关配置，配置用户密码为`huawei`

   ```java
   R4:
   [R4]user-interface vty 0 4
   [R4-ui-vty0-4]authentication-mode password 	
   [R4-ui-vty0-4]set authentication password simple huawei
   ```

   <img src="https://s2.loli.net/2022/05/08/UZk2m3Funh4gw6y.png" alt="image-20220508144014112" style="zoom:50%;" />

4. 现要求`R1`的环回接口只能通过`R4`上的`40.40.40.40`进行`Telnet`访问，但不能通过`4.4.4.4`访问。

   如果要`R1`只能通过访问`R4`的`Loopback 1`地址登录设备，即同时匹配数据包的源地址和目的地址实现过滤，此时通过标准`ACL`是无法实现的，因为`ACL`只能通过匹配源地址实现过滤，所以需要用到高级`ACL`。

   ```java
   R4:
   [R4]acl 3000
   //允许源地址为1.1.1.1 目的地址为4.4.4.4的数据包通过
   [R4-acl-adv-3000]rule permit ip source 1.1.1.1 0 destination 40.40.40.40 0
   [R4-acl-adv-3000]q
   [R4]user-interface vty 0 4
   [R4-ui-vty0-4]acl 3000 inbound //在远程连接服务数据流入时此规则生效
   ```

   <img src="https://s2.loli.net/2022/05/08/GgMKX6yQ4fWxrZI.png" alt="image-20220508145808086" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/08/1cjbLC9xnqgHZfa.png" alt="image-20220508150215603" style="zoom: 67%;" />

   设置了允许通过的规则后，其余没被设置的区域都被禁止通过，即此刻只有`R1`可以通过`R4`的`40.40.40.40`远程连接到`R4`。

   此外高级`ACL`还可以实现对源、目的端口，协议号等信息的匹配，功能非常强大。

# 27. 配置前缀列表 #

前缀列表即`IP-Prefix List`，它可以将与所定义的前缀列表相匹配的路由根据定义的匹配模式进行过滤。前缀列表中的匹配条目由IP地址和掩码组成，`IP`地址可以是网段地址或主机地址，掩码长度的配置范围为`0~32`，可以进行精确匹配或在一定掩码长度范围内匹配，也可以通过配置关键字`greater-equal`和`less-equal`指定待匹配的前缀掩码长度范围。

**前缀列表能同时匹配前缀号和前缀长度，主要用于路由的匹配和控制，不能用于数据包的过滤。**

## 实验目的 ##

+ 理解前缀列表的应用场景
+ 掌握前缀列表的配置方法
+ 理解前缀列表与ACL的区别

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/08/GRd5o8TtlDS2mA4.png" alt="image-20220508175135955" style="zoom:50%;" />

## 实验步骤 ##

1. 根据如图所示进行配置，路由器接口主机号若为特别说明则与其编号一致

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 20.1.1.1 24
   [R1-GigabitEthernet0/0/1]int g0/0/2
   [R1-GigabitEthernet0/0/2]ip address 30.1.1.1 24
   [R1-GigabitEthernet0/0/2]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 40.1.1.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 20.1.1.2 24
   [R2-GigabitEthernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 11.1.1.2 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int g0/0/2	
   [R3-GigabitEthernet0/0/2]ip address 30.1.1.3 24
   [R3-GigabitEthernet0/0/2]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 11.1.1.11 25
   
   R4:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R4
   [R4]int g0/0/0
   [R4-GigabitEthernet0/0/0]ip address 40.1.1.4 24
   ```

2. 搭建`RIP`网络

   ```java
   R1:
   [R1]RIP 
   [R1-rip-1]version 2
   [R1-rip-1]network 20.0.0.0
   [R1-rip-1]network 30.0.0.0
   [R1-rip-1]network 40.0.0.0
   
   R2:
   [R2]rip
   [R2-rip-1]version 2
   [R2-rip-1]network 20.0.0.0
   [R2-rip-1]network 11.0.0.0
   
   R3:
   [R3]rip
   [R3-rip-1]version 2
   [R3-rip-1]network 30.0.0.0
   [R3-rip-1]network 11.0.0.0
   
   R4:
   [R4]rip
   [R4-rip-1]version 2
   [R4-rip-1]network 40.0.0.0
   ```

   查看`R1`路由表

   <img src="https://s2.loli.net/2022/05/08/r1EMYfXt8eTpk9x.png" alt="image-20220508180118189" style="zoom:50%;" />

   根据掩码最长匹配原则，此时`R4`发往`11.1.1.0`网段的数据都会走向`R3`，若我们希望希望数据能走向`R2`，则需要配置过滤路由。

   **前缀列表会有一条隐含的拒绝所有的规则，所以如果要放行其他路由的话，一定要显式增加一条允许所有的规则。**

   ```java
   R1:
   //设置前缀规则rule_1：拒绝11.1.1.0，掩码<=25且>=25的网段
   //等价于ip ip-prefix rule_1 deny 11.1.1.0 25
   [R1]ip ip-prefix rule_1 deny 11.1.1.0 25 greater-equal 25 less-equal 25
   //设置前缀规则rule_2：允许所有，掩码<=32的网段通过    
   [R1]ip ip-prefix rule_2 permit 0.0.0.0 0 less-equal 32
   [R1]rip
   //在RIP中引入刚刚设下的前缀规则    
   [R1-rip-1]filter-policy ip-prefix rule_1 import 
   [R1-rip-1]filter-policy ip-prefix rule_2 import
   //一条规则里可以包含多个内容    
   ```

   <img src="https://s2.loli.net/2022/05/08/oSUHqZa8QlhwF4c.png" alt="image-20220508193226817" style="zoom:50%;" />

# 28. VRRP基本配置 #

对于用户来说，能够时刻与外部网络保持通信非常重要，但内部网络中的所有主机通常只能设置一个网关`IP`地址，通过该出口网关实现主机与外部网络的通信。若此时出口网关设备发送故障，主机与外部网络的通信就会中断，所以配置多个出口网关是提高网络可靠性的常用方法。

为此，`IETF`组织推出了`VRRP`协议，主机在多个出口网关的情况下，仅需配置一个虚拟网关`IP`地址作为出口网关即可，解决了局域网主机访问外部网络的可靠性问题。

`VRRP【Virtual Router Redundancy Protocol】`全称是虚拟路由冗余协议，它是一种容错协议。**该协议通过把几台路由设备联合组成一台虚拟的路由设备，该虚拟路由器在本地局域网拥有唯一的一个虚拟`ID`和虚拟`IP`地址。实际上，该虚拟路由器是由一个`Master`设备和若干`Backup`设备组成**。正常情况下，业务全由`Master`承担，所有用户端仅需设置此虚拟`IP`为网关地址。当`Master`出现故障时，`Backup`接替工作，及时将业务切换到备份路由器，从而保持通信的连续性和可靠性。而用户端无需做任何配置更改，对故障无感知。

`VRRP`的`Master`选举基于优先级，优先级取值范围是`0~255`，默认情况下，配置优先级为`100`.在接口上可以通过配置优先级的大小来手工选择`Master`设备。

## 实验目的 ##

+ 理解`VRRP`的应用场景
+ 掌握`VRRP`虚拟路由器的配置
+ 掌握修改`VRRP`优先级的方法
+ 掌握查看`VRRP`主备状态的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/09/HzgQY5aJRUuqic8.png" alt="image-20220509132024778" style="zoom:50%;" />

## 实验步骤 ##

1. 根据如图所示做好基础配置

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.2.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 172.16.3.254 24
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]ip address 172.16.1.100 24
   [R2-Ethernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.2.100 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]ip address 172.16.1.200 24
   [R3-Ethernet0/0/1]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 172.16.3.200 24
   ```

2. 在路由器上部署`OSPF`网络

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   ```

3. 针对两台出口网关路由器实现主备备份，即正常情况下，只有主网关工作，当其发生故障时能够自动切换到备份网关。

   在`R2`和`R3`上配置`VRRP`协议，使用`vrrp vrid 1 virtual-ip`命令创建`VRRP`备份组，指定`R1`与`R2`处于同一个`VRRP`备份组内，`VRRP`备份组号为`1`，配置虚拟`IP`为`172.16.1.254`。**注意虚拟`IP`必须和当前接口`IP`处于同一网段。**

   ```java
   R2:
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   
   R3:
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   ```

   经过配置后，`PC`将使用虚拟路由器`IP`地址作为默认网关。

   在`VRRP`协议中，优先级决定路由器在备份组中的角色，优先级高者成为`Master`。如果优先级相同，则比较接口的`IP`地址大小，较大的成为`Master`。优先级默认值为`100`，`0`被系统保留，`255`保留给`IP`地址拥有者【当一个`VRRP`路由器的物理端口IP地址和虚拟路由器的虚拟`IP`地址相同，这台路由器被称为虚拟`IP`地址拥有者】使用。

   ```java
   R2:
   [R2]int e0/0/1	
   //优先级修改为120，大于100，因此R2成为Master    
   [R2-Ethernet0/0/1]vrrp vrid 1 priority 120
   ```

   <img src="https://s2.loli.net/2022/05/09/8cKveXmhlrGOtyW.png" alt="image-20220509154810442" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/09/kK2lyhUN13OdToW.png" alt="image-20220509154856270" style="zoom:50%;" />

   若想查看当前路由器的`VRRP`精简信息可用语句`display vrrp brief`

4. 测试PC访问R3时经过的链路，观察此时数据是否通过Master进行转发

   <img src="https://s2.loli.net/2022/05/09/JpNrE5AQBgMCS34.png" alt="image-20220509155532376" style="zoom:50%;" />

# 29. 配置VRRP多备份组 #

当`VRRP`配置为单备份组时，业务全部由`Master`设备承担，而`Backup`设备完全处于空闲状态，没有得到充分利用。`VRRP`可通过配置多备份组来实现负载分担，有效地解决了这一问题。

`VRRP`允许同一台设备的同一个接口加入多个`VRRP`备份组，在不同备份组中有不同的优先级，使得各备份组中的`Master`设备不同，也就是建立多个虚拟网关路由器。各主机可以使用不同的虚拟组路由器作为网关出口，这样可以达到分担数据流而又相互备份的目的，充分利用了每一台设备的资源。

## 实验目的 ##

+ 掌握`VRRP`多备份组的应用场景
+ 掌握`VRRP`多备份组的配置方法
+ 理解`VRRP`的运行优先级和配置优先级
+ 理解`VRRP`虚拟地址拥有者的应用

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/09/Rt2sBNbiVT7nL6q.png" alt="image-20220509163744564" style="zoom:50%;" />

## 实验步骤 ##

1. 按照图示进行基础配置

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.2.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 172.16.3.254 24
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]ip address 172.16.1.100 24
   [R2-Ethernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.2.100 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]ip address 172.16.1.200 24
   [R3-Ethernet0/0/1]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 172.16.3.200 24
   ```

2. 部署`OSPF`网络

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   ```

3. 在`R2`和`R3`上创建`VRRP`虚拟组`1`，虚拟`IP`为`172.16.1.254`，指定`R2`的优先级为`120`，`R3`保持不变。

   在`R2`和`R3`上创建`VRRP`虚拟组`2`，虚拟`IP`为`172.16.1.253`，指定`R3`的优先级为`120`，`R2`保持不变。

   ```java
   R2:
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   [R2-Ethernet0/0/1]vrrp vrid 2 virtual-ip 172.16.1.253
   [R2-Ethernet0/0/1]vrrp vrid 1 priority 120
       
   R3:
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   [R3-Ethernet0/0/1]vrrp vrid 2 virtual-ip 172.16.1.253
   [R3-Ethernet0/0/1]vrrp vrid 2 priority 120
   ```

   <img src="https://s2.loli.net/2022/05/09/O6jhn9zsT4WKicr.png" alt="image-20220509164554969" style="zoom:50%;" />

   此时`PC1`与`PC2`访问`R3`时便会走向不同链路，实现负载均衡，同时当某一个网关发生故障时又会立马切换到备份网关，增强了网络的稳定性。

4. `VRRP`组`Master`选举默认为抢占式，即有`VRRP`优先级更高的设备出现时便会取代原`Master`而成为新`Master`，可通过语句`vrrp vrid 组ID preempt-mode disable`将其修改为非抢占式。

   ```java
   R2:
   [R2]int e0/0/1
   //修改R2的e0/0/1接口在VRRP组1模式为非抢占式，即此时即使修改其优先级也不会影响此组Master的选举
   [R2-Ethernet0/0/1]vrrp vrid 1 preempt-mode disable
   ```

   不管我们为当前接口在`VRRP`组中设置的优先级为多少，当此接口`IP`地址成为该组虚拟`IP`地址拥有者时，其优先级都会默认为`255`。

# 30. 配置VRRP的跟踪接口及认证 #

当`VRRP`的`Master`设备的上行接口出现问题，而`Master`设备一直保持`Active`状态，那么就会导致网络出现中断，所有必须要使得`VRRP`的运行状态和上行接口能够关联。

在配置了`VRRP`冗余的网络中，为了进一步提高网络可靠性，需要在`Master`设备上配置上行接口监视，监视连接了外网的出接口。即当此接口断掉时，**自动减小优先级一定的数值，使减小后的优先级小于`Backup`设备的优先级，这样`Backup`设备就会抢占Master角色接替工作**。

`VRRP`支持报文的认证。默认情况下，设备对要发送和接收的`VRRP`报文不进行任何认证处理，认为收到的都是真实的、合法的`VRRP`报文。为了使`VRRP`运行更加安全和稳定，可以配置`VRRP`的认证。`VRRP`支持简单字符【`Simple`】认证和`MD5`认证方式，用户可根据安全需要选择认证方式。

## 实验目的 ##

+ 理解`VRRP`监视接口的应用场景
+ 掌握`VRRP`监视接口的配置方法
+ 掌握`VRRP`认证的配置方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/09/HzgQY5aJRUuqic8.png" alt="image-20220509132024778" style="zoom:50%;" />

## 实验步骤 ##

1. 按照图示进行基础配置

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 172.16.2.254 24
   [R1-GigabitEthernet0/0/0]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 172.16.3.254 24
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]ip address 172.16.1.100 24
   [R2-Ethernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 172.16.2.100 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]ip address 172.16.1.200 24
   [R3-Ethernet0/0/1]int g0/0/1
   [R3-GigabitEthernet0/0/1]ip address 172.16.3.200 24
   ```

2. 部署`OSPF`网络

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
   ```

3. 在`R2`与`R3`上进行`VRRP`基本配置，并使得`R2`成为`Master`

   ```java
   R2:
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   [R2-Ethernet0/0/1]vrrp vrid 1 priority 120
   
   R3:
   [R3]int e0/0/1
   [R3-Ethernet0/0/1]vrrp vrid 1 virtual-ip 172.16.1.254
   ```

4. 此时若`R2`与`R3`之间的链路断掉【可通过关闭接口进行模拟验证】，`R2`并不能感知到，仍然会保持`Master`状态，从而导致数据到达不了`R1`。

   为了进一步提高网络的可靠性和安全性，需要在`Master`设备`R2`上配置`VRRP`的上行接口监视。当`R2`的上行接口发生故障时，将自动降低优先级使得`Backup`设备能抢占`Master`角色从而接替工作，将网络中断所造成的影响最小化。

5. 配置`R2`监视上行接口`g0/0/0`，当此接口断掉时，裁减优先级`50`，使其优先级变为`120-50=70`，小于`R3`的优先级`100`。

   ```java
   [R2-Ethernet0/0/1]vrrp vrid 1 track interface g0/0/0 reduced 50
   ```

   关闭`R1`的`g0/0/0`接口模拟故障发生，查看此时主备切换情况

   <img src="https://s2.loli.net/2022/05/10/SejtLhcR9zDEy3K.png" alt="image-20220510153719022" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/10/JfLjXGoO92N6RBY.png" alt="image-20220510153914073" style="zoom:50%;" />

6. 默认情况下，设备对要发送的VRRP报文不进行任何认证处理，收到VRRP报文的设备也不进行检测，认为收到的都是真实、合法的VRRP报文，可以通过配置更改VRRP的认证模式，使VRRP对报文进行验证，从而增加安全性。

   **注意：同一VRRP备份组的认证方式必须相同，否则Master和Backup设备无法协商成功。**

   ```java
   R2:
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]vrrp vrid 1 authentication-mode md5 huawei
   
   R3:
   [R3]int e0/0/1	
   [R3-Ethernet0/0/1]vrrp vrid 1 authentication-mode md5 huawei
   ```

   <img src="https://s2.loli.net/2022/05/10/fVbnDeqzcxgJ1oj.png" alt="image-20220510154714458" style="zoom:50%;" />

# 31. WLAN接入配置 #

高级数据链路控制`HDLC`【`High-level Data Link Control`】是一种链路层协议，运行在同步串行链路之上，`HDLC`最大的特点是不需要锁定数据必须是字符集，对任何一种比特流【**面向位**】，均可以实现透明传输。随着技术的进步，`HDLC`在公网的应用逐渐消失，应用范围逐渐减小，只是在部分专网中用来封装透明传输业务数据。

`PPP`【`Point-to-Point Protocol`】协议是一种数据链路层协议，主要用来在全双工的同异步链路上进行点到点之间的数据传输，其设计初衷是为了两个对等节点之间的`IP`流量提供一种封装协议，是**面向字节**的，是一种多协议成帧机制，适用于调制解调器。

串行链路指信息的各位数据被逐位按顺序传送的线路，适用于远距离通信，但速度较慢，与之相对的是并行链路，能够在同一时刻传送一个`8 bit`数据。

**同步和异步是广域网的串行链路的两种传输模式**

+ **同步模式**要求通信双方以相同的时钟频率进行，通过共享单个时钟或定时脉冲源保证发送方和接收方的准确同步，效率较高。
+ **异步模式**不要求双方同步，收发方可以采用各自的时钟源，双方遵循异步的通信协议，以字符为数据传输单位，发送方传送字符的时间间隔不确定，发送效率比同步模式低。

## 实验目的 ##

+ 掌握`PPP`的基本配置
+ 掌握`HDLC`的基本配置
+ 理解`PPP`和`HDLC`的异同

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/10/FkfHp4mAMtJv7bK.png" alt="image-20220510170216614" style="zoom:50%;" />

## 实验步骤 ##

1. 如图进行配置，路由器接口若为特别说明，则主机号与其编号一致

   ```java
   R1:
   <Huawei>sys	
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int s0/0/0
   [R1-Serial0/0/0]ip address 192.168.1.1 24
   [R1-Serial0/0/0]int s0/0/1
   [R1-Serial0/0/1]ip address 192.168.2.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int e0/0/1
   [R2-Ethernet0/0/1]ip address 172.16.1.254 24
   [R2-Ethernet0/0/1]int s0/0/0
   [R2-Serial0/0/0]ip address 192.168.1.2 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int s0/0/1	
   [R3-Serial0/0/1]ip address 192.168.2.3 24
   [R3-Serial0/0/1]int e0/0/1
   [R3-Ethernet0/0/1]ip address 172.16.3.254 24
   ```

2. 默认情况下，串行接口`Serial`封装的链路层协议为`PPP`

   <img src="https://s2.loli.net/2022/05/11/TQ7akSMA26GsP3b.png" alt="image-20220511132528924" style="zoom: 67%;" />

3. 可通过`link-protocol`命令配置链路层协议为`HDLC`

   ```java
   R1:	
   [R1]int Serial 0/0/0	
   [R1-Serial0/0/0]link-protocol hdlc 
   Warning: The encapsulation protocol of the link will be changed. 
   Continue? [Y/N]:y
   
   R2:
   [R2]int Serial 0/0/0
   [R2-Serial0/0/0]link-protocol hdlc 
   Warning: The encapsulation protocol of the link will be changed. 
   Continue? [Y/N]:y
   ```

   **注意：链路两端的协议类型应当保持一致！**

# 32. PPP的认证 #

`PPP`协议之所以能成为广域网中应用较为广泛的协议，原因之一就是它能提供验证协议`CHAP`【`Challenge Handshake Authentication Protocol`，挑战式握手验证协议】、`PAP`【`Password Authentication Protocol`，密码验证协议】，更好地保证了网络安全性。

`PAP`为两次握手验证，口令为明文，验证过程仅在链路初始建立阶段进行。**当链路建立阶段结束后，用户名和密码将由被验证方重复地在链路上发送给验证方，直到验证通过或者中止连接**。`PAP`不是一种安全的验证协议，因为口令是以明文方式在链路上发送的，并且用户名和口令还会被验证方不停地在链路上反复发送，导致很容易被截获。

`CHAP`是三次握手验证协议，**只在网络上传输用户名，而并不传输用户密码**，因此安全性要比`PAP`高。`CHAP`协议是在链路建立开始就完成的，在链路建立完成后的任何时间都可以进行再次验证。

+ 当链路建立阶段完成后，验证方发送一个`"challenge"`报文给被验证方；
+ 被验证方经过一次`Hash`算法后，给验证方返回一个值；
+ 验证方把自己经过`Hash`算法生成的值和被验证方返回的值进行比较。如果两者匹配，那么验证通过，否则验证不通过，连接被终止。

## 实验目的 ##

+ 掌握配置`PPP PAP`认证的方法
+ 掌握配置`PPP CHAP`认证的方法
+ 理解`PPP PAP`认证与`CHAP`认证的区别

## 实验拓扑 ##

路由器应当选用`AR2220`，否则有些语句不能运行；同时由于初始`AR2220`没有串口，因此我们还需要外接串口

<img src="https://s2.loli.net/2022/05/11/KfyXuitsp7DJ43v.png" alt="image-20220511200524649" style="zoom: 50%;" />

<img src="https://s2.loli.net/2022/05/11/Ho2S7NXlUu1shLp.png" alt="image-20220511200911503" style="zoom:50%;" />

## 实验步骤 ##

1. 按照图示进行基本配置，路由器接口若为特别说明，则主机号与其编号一致

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 10.0.1.254 24
   [R1-GigabitEthernet0/0/0]int s0/0/0
   [R1-Serial4/0/0]ip address 10.0.13.1 24
   
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/1
   [R2-GigabitEthernet0/0/1]ip address 10.0.2.254 24
   [R2-GigabitEthernet0/0/1]int g0/0/0
   [R2-GigabitEthernet0/0/0]ip address 10.0.23.2 24
   
   R3:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R3
   [R3]int s0/0/0
   [R3-Serial0/0/0]ip address 10.0.13.3 24
   [R3-Serial4/0/0]int g0/0/0
   [R3-GigabitEthernet0/0/0]ip address 10.0.23.3 24
   ```

2. 搭建`OSPF`网络

   ```java
   R1:
   [R1]ospf
   [R1-ospf-1]area 0
   [R1-ospf-1-area-0.0.0.0]network 10.0.1.0 0.0.0.255
   [R1-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   
   R2:
   [R2]ospf
   [R2-ospf-1]area 0
   [R2-ospf-1-area-0.0.0.0]network 10.0.2.0 0.0.0.255
   [R2-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255
   
   R3:
   [R3]ospf
   [R3-ospf-1]area 0
   [R3-ospf-1-area-0.0.0.0]network 10.0.13.0 0.0.0.255
   [R3-ospf-1-area-0.0.0.0]network 10.0.23.0 0.0.0.255

3. 在`R1`和`R3`上部署`PPP`的`PAP`认证。

   在华为路由器上，广域网串行接口默认链路层协议即为`PPP`，因此可以直接配置`PPP`认证。使用`ppp authentication-mode`命令设置本端的`PPP`协议对对端设备的认证方式为`PAP`。

   ```java
   R1:
   [R1]int s4/0/0
   //配置本端被对端以PAP方式验证时本地发送的PAP用户名和密码    
   [R1-Serial4/0/0]ppp pap local-user R1@huaweiyun password cipher Huawei
   
   R3:
   [R3]int s4/0/0
   //设置本端的PPP协议对对端设备的认证方式为PAP，认证采用域名为huawei    
   [R3-Serial4/0/0]ppp authentication-mode pap domain huawei
   //AAA是认证（Authentication）、授权（Authorization）和计费（Accounting）的简称
   //aaa认证可以设置很多内容，由于此处不是重点，因此我们直接使用默认配置即可    
   [R3]aaa
   //设定对端认证方所使用的用户名为R1@huaweiyun，密码为Huawei    
   [R3-aaa]local-user R1@huaweiyun password cipher Huawei
   [R3-aaa]local-user R1@huaweiyun service-type ppp 
   ```
   
   配置完成后，关闭`R1`与`R3`相连接口一段时间后再打开，使`R1`与`R3`间的链路重新协商，并检查链路状态和连通性。
   
   <img src="https://s2.loli.net/2022/05/11/a2X9UWT8wINqv1d.png" alt="image-20220511203825519" style="zoom: 67%;" />
   
4. 我们对串口链路进行抓包观察，观察报文中的用户名与密码

   **注意，在抓包前必须关闭重启接口，因为`PPP`链路只有协商阶段才会在报文中携带用户名与密码**

   <img src="https://s2.loli.net/2022/05/11/JdpQTOh32vUF5t7.png" alt="image-20220511205912512" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/11/hT6AP24CJwar75Y.png" alt="image-20220511205955994" style="zoom:50%;" />

   由此我们验证了使用`PAP`认证时，口令将以明文方式在链路上传送，并且由于完成`PPP`链路建立后，被认证方会不停地在链路上反复发送用户名和口令，直到身份认证过程结束，所以不能防止攻击。

5. 为了进一步提高安全性，我们重新部署`PPP`的认证方式为`CHAP`，口令用`MD5`算法加密后在链路上发送。

   ```java
   //首先将原先的配置取消，域名保持不变
   R1:
   [R1]int s4/0/0
   [R1-Serial4/0/0]undo ppp pap local-user 
   
   R3:
   [R3]int s4/0/0
   [R3-Serial4/0/0]undo ppp authentication-mode 
   ```

   接着在认证设备`R3`的`s4/0/0`接口下配置`PPP`的认证方式为`CHAP`，配置存储在本地，对端认证方式所使用的的用户名为`R1`，密码为`huawei`。

   ```java
   R1:
   [R1]int s4/0/0
   [R1-Serial4/0/0]PPP chap password cipher huawei
   
   R3:
   [R3]int s4/0/0
   //配置PPP认证方式为CHAP    
   [R3-Serial4/0/0]ppp authentication-mode chap 
   [R3-Serial4/0/0]q
   [R3]aaa
   //配置存储在本地，对端认证方所使用的用户名为R1，密码为huawei，其余认证方案和域的配置保持不变    
   [R3-aaa]local-user R1 password cipher huawei
   [R3-aaa]local-user R1 service-type ppp 
   ```

   此时再次抓包查看【同样需要重启接口协商】

   <img src="https://s2.loli.net/2022/05/11/gDOzbXTse28ZuPV.png" alt="image-20220511214312606" style="zoom:50%;" />

#  33. eNSP云设备的使用

`eNSP`不仅支持单机部署，同时还支持`Server`端分布式部署在多台服务器上，分布式部署环境下能够支持更多设备组成复杂大型网络。`eNSP`还可与真实设备对接，通过虚拟设备接口与真实网卡的绑定，实现虚拟设备与真实设备的对接，进而实现虚拟网络与真实网络的互连互通。

## 实验目的 ##

+ 掌握`eNSP`中使用云设备与虚拟`PC`连接的方法
+ 掌握在`eNSP`中使用云设备与真实`PC`上的物理网卡桥接的方法

## 实验拓扑 ##

<img src="https://s2.loli.net/2022/05/13/ghmVXa2Y6Nty53R.png" alt="image-20220513205448346" style="zoom:50%;" />

进入云设置界面后，创建一个端口，在**"绑定信息"**下拉列表中选择`"UDP"`，在**"端口类型"**下拉列表中选择`"Ethernet"`，然后点击**"增加"**按钮，新创建端口的信息将会出现在端口信息表中，序列号为`1`。

<img src="https://s2.loli.net/2022/05/13/qeQtNxUcGjlX3bW.png" alt="image-20220513205657560" style="zoom:50%;" />

创建另一个端口。**"绑定信息"**选择真实`PC`中任意一个网卡地址，端口类型仍然选择`"Ethernet"`。

<img src="https://s2.loli.net/2022/05/13/sdDecphuS1ximJR.png" alt="image-20220513210318577" style="zoom:50%;" />

接下来在**"端口映射设置"**栏中创建端口的映射关系。在**"入端口编号"**下拉列表中选择`"2"`，也就是刚才所创建的对应真实`PC`上无线网卡的端口；将**"出端口编号"**选择为`"1"`，选择**"双向通道"**复选框，然后单击**"增加"**按钮，即可添加到右侧的**"端口映射表"**中。

<img src="https://s2.loli.net/2022/05/13/AXrI8uw9sTyv7hM.png" alt="image-20220513211304644" style="zoom:50%;" />

经过上述准备后，我们可以得到拓扑图。

<img src="https://s2.loli.net/2022/05/13/rQtboNKpXyl1niR.png" alt="image-20220513211352396" style="zoom:50%;" />

## 实验步骤 ##

1. 将配置`PC1`的`IP`地址，使其与真实电脑的IP地址处于同一网段

   + 快捷键`"Windows+R"`，输入`"CMD"`后点击确定打开命令提示符【`Dos`界面】，使用`"ipconfig"`命令查看刚刚自己部署在云设备上网卡的`IP`地址与掩码

     <img src="https://s2.loli.net/2022/05/13/DPE4s3QpfgtUqSk.png" alt="image-20220513213151792" style="zoom:50%;" />

   + 所以根据此地址，`PC1`的基本配置如下

     <img src="https://s2.loli.net/2022/05/13/Va91mqxzScjlkT5.png" alt="image-20220513213317017" style="zoom:50%;" />

2. 测试`PC1`与真实设备的连通性。

   <img src="https://s2.loli.net/2022/05/13/OEXgZ5Y4ta8usUF.png" alt="image-20220513213442168" style="zoom:50%;" />

   <img src="https://s2.loli.net/2022/05/13/dVmRizvphHe8a91.png" alt="image-20220513213555200" style="zoom:50%;" />

   由此我们完成了虚拟设备通过云设备与真实设备相连接的基础配置。

# 34. 配置NAT  #

`IPv4`网络地址有耗尽的可能性，而`IPv6`地址目前又无法立刻替换现有成熟且广泛应用的`IPv4`网络。因此必须使用一些技术手段来延长`IPv4`的寿命，其中广泛使用的技术之一就是网络地址转换`NAT`。

`NAT【Network Address Translation】`是将`IP`数据报文报头中的`IP`地址转换为另一个`IP`地址的过程，主要用于实现内部网络【私有`IP`地址】访问外部网络【公有`IP`地址】的功能。`NAT`有三种类型：静态`NAT`、动态地址`NAT`以及网络地址端口转换`NAPT`。

`NAT`转换设备【实现`NAT`功能的网络设备】维护着地址转换表，所有经过`NAT`转换设备并且需要进行地址转换的报文，都会通过该表做相应转换。`NAT`转换设备处于内部网络和外部网络的连接处，常见的有路由器、防火墙等。

## 实验目的 ##

+ 理解`NAT`的应用场景
+ 掌握静态`NAT`的配置
+ 掌握`NAT Outbound`的配置
+ 掌握`NAT Easy-IP`的配置
+ 掌握`NAT Server`的配置

## 实验拓扑 ##

路由器选型最好为`AR2220`，否则可能出现`NAT`语句无法发挥作用的情况。

<img src="https://s2.loli.net/2022/05/19/Uch9KLS6DJxfsZ4.png" alt="image-20220519152411751" style="zoom:50%;" />

## 实验步骤 ##

1. 如图进行基础配置，路由器接口主机号若未特别说明则与其路由器编号一致

   ```java
   R1:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R1
   [R1]int g0/0/1
   [R1-GigabitEthernet0/0/1]ip address 172.16.1.254 24
   [R1-GigabitEthernet0/0/1]int g0/0/2
   [R1-GigabitEthernet0/0/2]ip address 172.17.1.254 24
   [R1-GigabitEthernet0/0/2]int g0/0/0
   [R1-GigabitEthernet0/0/0]ip address 202.169.10.1 24
       
   R2:
   <Huawei>sys
   [Huawei]undo info-center en
   [Huawei]sysname R2
   [R2]int g0/0/0	
   [R2-GigabitEthernet0/0/0]ip address 202.169.10.2 24
   [R2-GigabitEthernet0/0/0]int loopback 0
   [R2-LoopBack0]ip address 202.169.20.1 24
   ```

   ```java
   R1:
   //配置默认路由，使得R1知道如何通向R2
   [R1]ip route-static 0.0.0.0 0 202.169.10.2
   
   R2:
   //配置默认路由，使得R2知道如何通向R1
   [R2]ip route-static 0.0.0.0 0 202.169.10.1
   ```

   此时从路由表的角度看应当已经全网互通了，但是实际上由于内网使用的都是私有`IP`地址【`10.0.0.0/8、172.16.0.0/12、192.168.0.0/16`】，无法直接访问公网地址【除去私有网段外的地址都是公网地址】，所以此时网络实际上还是不通的【**在eNSP的模拟中可以直接通，但在现实中不能，因此在实验中我们假装不通**:laughing:】。

2. 配置静态`NAT`

   **数据由内网进入外网时，通过将内网地址映射成外网地址后可以实现内网数据抵达外网；数据由外网进入内网时，通过将外网地址映射成内网地址后可以实现外网数据抵达内网**。二者一同配置，即可实现内外网互通。

   ```java
   R1:
   //当内网地址172.16.1.1经由g0/0/0接口发送时，静态转换为公网地址202.169.10.5
   //当外网经由g0/0/0接口访问202.169.10.5地址时，静态转换为内网地址172.16.1.1
   [R1-GigabitEthernet0/0/0]nat static global 202.169.10.5 inside 172.16.1.1
   ```

   令`PC1 PING R2`，观察此时`R1`的`g0/0/0`端口情况。
   
   <img src="https://s2.loli.net/2022/05/19/VqgHPRoXyUwluYN.png" alt="image-20220519194044426" style="zoom:50%;" />
   
   再令`R2 PING PC1`的公网地址，观察此时`R1`的`g0/0/1`端口情况。
   
   <img src="https://s2.loli.net/2022/05/19/yrv2zCJAwDLVMoH.png" alt="image-20220519194725538" style="zoom:50%;" />
   
   这就是静态`NAT`，一个私网地址映射一个公网地址。
   
3. 除了静态`NAT`外，还可以选择使用`NAT`地址池的方式映射地址，即**私网走向公网时，随机从公网地址池中选择一个公网地址作为自己的临时公网地址**；公网进入私网时同理，地址动态分配回收，大大了提高地址利用率。

   ```java
   R1:
   //设置NAT地址池，编号为1，范围从202.169.10.50开始，至202.169.10.60结束
   [R1]nat address-group 1 202.169.10.50 202.169.10.60
   //在R1上创建ACL访问控制表，编号2001    
   [R1]acl 2001
   //5号规则内容为允许172.17.1.0/24的数据通过
   [R1-acl-basic-2001]rule 5 permit source 172.17.1.0 0.0.0.255
   [R1]int g0/0/0
   //将编号2001的ACL运用在g0/0/0端口，绑定1号地址池
   //意为当符合ACL 2001的数据从g0/0/0端口出去时，会将从地址池1中取出地址取代原地址成为新的源地址
   [R1-GigabitEthernet0/0/0]nat outbound 2001 address-group 1 no-pat
   ```

   我们令`PC2 PING R2`，抓包观察地址转换情况。

   <img src="https://s2.loli.net/2022/05/19/1GepJzZMBCxab2i.png" alt="image-20220519202500659" style="zoom:50%;" />

   当`Outbound`关键词变为`Inbound`时，便可实现公网地址向私网地址的转换。

4. 除静态`NAT`和地址池`NAT`外，还有一种基于端口的`NAT`形式：`NAT Easy-IP`。

   `Easy-IP`是`NAPT`的一种方式，直接**借用路由器出接口`IP`地址作为公网地址，将不同的内部地址映射到同一公有地址的不同端口号上**，实现多对一地址转换，我们将`R1`的`g0/0/0`接口配置为`Easy-IP`接口。

   ```java
   R1:
   [R1]int g0/0/0
   //首先将先前在g0/0/0端口设定的NAT地址池模式关闭    
   [R1-GigabitEthernet0/0/0]undo nat outbound 2001 address-group 1 no-pat
   //重新在g0/0/0端口上绑定规则2001，此时配置的就是Easy-IP特性
   [R1-GigabitEthernet0/0/0]nat outbound 2001
   ```

   在`PC2`和`PC3`中做如下操作。

   <img src="https://s2.loli.net/2022/05/19/hm2zHgNofuE8y1F.png" alt="image-20220519205005660" style="zoom:50%;" />

   `PC2`和`PC3`分别点击发送后，在`R1`执行`display nat session protocol udp verbose`。

   <img src="https://s2.loli.net/2022/05/19/Nsz5cmLaKXwIZHe.png" alt="image-20220519205850737" style="zoom:50%;" />

5. 同时，`NAT`还提供**将内部服务器映射至外网**的配置。

   配置`NAT Server`并使用公网`IP`地址`202.169.10.6`对外公布服务器地址，然后开启`NAT ALG`功能，因为对于封装在`IP`数据报文中的应用层协议报文，正常的`NAT`转换会导致错误，在开启某应用协议的`NAT ALG`功能后，该应用协议报文可以正常进行`NAT`转换，否则该应用协议不能正常工作。

   <img src="https://s2.loli.net/2022/05/19/bNK4MnGuTS97YDm.png" alt="image-20220519211400023" style="zoom:50%;" />

   ```java
   R1:
   [R1]int g0/0/0
   //在g0/0/0端口定义内部FTP服务器映射表，将内网FTP服务器的私网地址172.16.1.3
   //映射到公网地址202.169.10.6上。注意！Ftp是基于TCP的协议，而DNS是基于UDP的协议。    
   [R1-GigabitEthernet0/0/0]nat server protocol tcp global 202.169.10.6 ftp inside 
   172.16.1.3 ftp 
   [R1-GigabitEthernet0/0/0]q
   //开启NAT ALG功能
   [R1]nat alg ftp enable 
   ```

   在`R2`上模拟公网用户访问该私网`FTP`服务器。

   <img src="https://s2.loli.net/2022/05/19/V95aCiW7TO3zENI.png" alt="image-20220519212501495" style="zoom: 67%;" />



