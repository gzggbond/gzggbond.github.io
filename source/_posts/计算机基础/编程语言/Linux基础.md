---
title: Linux基础
date: 2025-07-15 13:27:50
excerpt: 本文对于Linux的基本用法做了系统性总结，持续更新
tags:
  - 编程语言
categories:
  - [计算机基础,编程语言]
---

# 1. 远程连接

`ifconfig`：查看 Linux 主机 ip 地址

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307111118223.png" alt="image-20230711111834194" style="zoom: 50%;" />

打开 XShell 新建会话

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307111119009.png" alt="image-20230711111908981" style="zoom: 50%;" />

点击用户身份验证，输入系统账号密码后点击连接即可

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403181701687.png" alt="image-20240318170106580" style="zoom: 50%;" />

# 2. vim编辑器

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307111520656.png" alt="image-20230711152015426" style="zoom: 50%;" />

+ **正常模式**

  > 以  vim 打开一个文件就直接进入一般模式了（这是默认的模式）。在这个模式中，可以使用『上下左右』按键来移动光标，可以使用『删除字符』或『删除整行』来处理档案内容，也可以使用『复制、粘贴』来处理文件数据

  + `yy`：拷贝当前行

    > 拷贝包括当前行在内向下共 5 行并粘贴：5yyp（p 的作用是粘贴）

  + `dd`：删除当前行

    > 删除当前行向下的 5 行：5dd

  + `g`：移动到文件尾行

    > 移动到文件首行：gg

  + `u`：撤销上一步操作

  + 定位到第3行：先按3，再按 shift+g

  + `/xxx`：查找单词“xxx”，输入 n 定位下一个单词位置

+ 插入模式

  > 按下 `i，L，o，O，a，A，r，R` 等任何一个字母之后才会进入编辑模式，一般来说按 `i` 即可。

+ 命令行模式

  > 在这个模式当中，可以提供你相关指令，完成读取、存盘、替换、离开 `vim`、显示行号等的动作。
  
  + `wq`：保存退出
  
    `q`：退出
  
    `q!`：不保存强制退出
  
  + `set nu`：查看当前文件行号
  
    > 取消行号显示：set nonu
  
    <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403301443613.png" alt="image-20240330144300519" style="zoom:50%;" />

# 3. 用户管理

+ `su - 用户名`：切换到某个用户账号

+ `logout`：登出当前用户

  > 此时为切换到此账号的用户接手

+ `sudo passwd 用户名`：设置用户密码

  > sudo即执行管理级别操作，所以此命令甚至可以设置 root 密码

+ `useradd [-d] [-目录位置] [-g] [组名] 用户名`：添加账号

  > 可为当前用户指定组，不指定默认分到与用户名一致的组中；账号信息默认存储在home目录下，也可以自行指定

+ `userdel [-r] 用户名`：删除用户

  > 不输入 -r 时，虽然用户已经删除，但是会保留 home 中的信息

# 4. 运行级别

| 运行级别 |          说明           |
| :------: | :---------------------: |
|    0     |          关机           |
|    1     | 单用户【(找回丢失密码】 |
|    2     | 多用户状态没有网络服务  |
| 3:star:  |  多用户状态有网络服务   |
|    4     |  系统未使用保留给用户   |
| 5:star:  |        图形界面         |
|    6     |        系统重启         |

+ `init 运行级别`：切换运行级别

# 5. Linux组

+ `id 用户名`：查看用户基本信息
+ `sudo groupadd/groupdel 组名`：添加/删除组

+ 所有者

  > 一般为文件创建者

  + `chown [选项] 用户名 文件名`：修改文件所有者
  + `chown 用户名:组 文件/文件夹`：改变文件的所有者和组

+ 所在组

  > 当某个用户创建了一个文件后，这个文件的所在组就是该用户所在的组

  + `chgrp 组名 文件名`：修改文件所在的组

    > 和 chown 作用类似，不过其可以单独改变组

+ 其他组

  > 除文件的所有者和所在组的用户外，系统的其它用户都是文件的其它组

  + `usermode -g 组名 用户名`：将用户移动到指定组
  + `usermode -d 目录名 用户名`：改变用户登录的初始目录

## 修改权限

> 通过 `chmod` 指令，可以修改**文件或目录**的权限

```python
u：文件所有者		g：文件的组		o：其他人		a：所有用户【u,g,o的总和】
```

+ 方式一：字母加符号👉+，-，= 变更权限

  ```python
  # 给文件所有者赋予rwx权限，所有组赋予rx权限，其他人赋予x权限
  chmod u=rwx,g=rx,o=x 文件/目录名
  # 给其他人增加此文件的w权限
  chmod o+w 文件/目录名
  # 删除所有人对此文件的x权限【可执行权限】
  chmod a-x 文件/目录名
  ```

+ 方式二：数字加符号👉+，-，= 变更权限

  ```python
  # 二进制数，0-7能囊括所有权限组合【0是无权限】
  r=4		w=2		x=1
  # 给文件所有者赋予rwx权限(7)，所有组赋予rx权限(5)，其他人赋予x权限(1)
  chmod 751 文件/目录名
  ```

# 6. 定时执行

> 有些重要工作必须周而复始地执行，如病毒扫描；个别用户可能希望执行某些程序，比如对数据库进行备份

```shell
# 在Ubuntu或Debian中需要使用如下命令进行安装
sudo apt-get update && sudo apt-get install cron
sudo apt-get update && sudo apt-get install at
# 在RHEL，CentOS，Fedora则需要使用如下命令安装
sudo yum install vixie-cron
```

+ `crontab [选项]`：定时反复执行

  + -e：编辑 crontab 定时任务，此时与 vim 操作类似

    ```shell
    # 编辑 crontab 定时任务
    crontab -e
    # 输入以下内容：每隔一分钟在控制台往b.txt追加一次Hello字符串
    # 路径必须是绝对路径
    * * * * * echo Hello >> /root/my_folder/b.txt
    # 编辑完毕后需要重启cron服务，令新规则生效
    # stop/start/status：停止/开启/状态，默认cron服务状态为停止
    service cron restart
    ```

    | 项目             | 含义                 | 范围                |
    | ---------------- | -------------------- | ------------------- |
    | 第 1 个 `*` 位置 | 一小时当中的第几分钟 | 0-59                |
    | 第 2 个 `*` 位置 | 一天当中第几小时     | 0-23                |
    | 第 3 个 `*` 位置 | 一个月当中第几天     | 1-31                |
    | 第 4 个 `*` 位置 | 一年当中第几个月     | 1-12                |
    | 第 5 个 `*` 位置 | 一周当中的星期几     | 0-7【0和7都是周日】 |

    | 特殊符号 | 含义                                                         |
    | -------- | ------------------------------------------------------------ |
    | `*`      | 代表任何时间。比如第一个 `*` 就代表一小时中每分钟都执行一次的意思。 |
    | `,`      | 代表不连续的时间。比如 `0 8,12 * * *` 命令，就代表在每天的`8`点`0`分，`12`点`0`分都执行一次命令 |
    | `-`      | 代表连续的时间范围。比如 `0 5 * * 1-6` 命令，代表在周一到周六的凌晨`5`点`0`分执行命令 |
    | `*/n`    | 代表每隔n执行一次。比如 `*/10 * * * *` 命令，代表每隔`10`分钟就执行一遍命令 |

    | 时间         | 含义                                                         |
    | ------------ | ------------------------------------------------------------ |
    | 45 22 * * *  | 在22点45分执行命令                                           |
    | 0 17 * * 1   | 每周一的17点0分执行命令                                      |
    | 0 5 1,15 * * | 每月1号和15号的凌晨5点0分执行命令                            |
    | 40 4 * * 1-5 | 每周一到周五的凌晨4点40分执行命令                            |
    | */10 4 * * * | 每天的凌晨4点，每隔10分钟执行一次命令                        |
    | 0 0 1,15 * 1 | 每月1号和15号，每周1的0点0分都会执行命令。<br />注意：星期几和几号最好不要同时出现，因为他们定义的都是天。非常容易让管理员混乱。 |

  + -l：查询 crontab 任务

  + -r：删除当前用户所有的 crontab 任务

+ `at [选项] [时间]`：一次性定时计划任务，执行完一个任务后不再执行此任务

  ```shell
  # 设置一分钟后执行
  at now + 1 minutes
  # 输入要执行的语句
  echo "Hello,at" >> /root/my_folder/b.txt
  # 按Ctrl + D结束输入
  ```

  + -v：显示任务将被执行的时间

  + 在使用 at 命令的时候，一定要保证 atd 进程的启动，可以使用相关指令来查看

    ```shell
    # 进程状态/启动/重启/停止
    service atd status/start/restart/stop 
    ```

    at 命令是一次性定时计划任务，at 的守护进程 atd 会以后台模式运行，检查作业队列来运行。默认情况下，atd 守护进程每 60 秒检查一次作业队列，有作业时，会检查作业运行时间，如果时间与当前时间匹配，则运行此作业。

  + 时间规则

    + 接受在当天的 `hh:mm`（小时:分钟）式的时间指定。假如该时间已过去，那么就放在第二天执行。

      > 例如：04:00

    + 使用 `midnight，noon，teatime`（一般是下午 4 点）等比较模糊的词语来指定时间

    + 采用  12 小时计时制，即在时间后面加上 AM（上午）或 PM（下午）来说明

      > 例如：12pm

    + 指定命令执行的具体日期，指定格式为 `month day`（月日）或 `mm/dd/yy` （月/日/年）或 `dd.mm.yy`
      （日.月.年），指定的日期必须跟在指定时间的后面

      > 例如：`04:00 2021-03-1`

    + 使用相对计时法。指定格式为：`now + count time-units`， now 就是当前时间，`time-units`是时间单位，这里能够是`minutes`、`hours` 、`days`、`weeks`。`count`是时间的数量，几天，几小时

      > 例如：now + 5 minutes

    + 直接使用`today` （今天）、`tomorrow`（明天）来指定完成命令的时间

# 7. Linux分区

> Linux 内的磁盘可以划分为多个分区，每个分区可以通过挂载 mount 的方式将这些存储空间分配给文件目录，当先前挂载的空间不够大时，还能够另外挂载其他分区为文件目录获得更大的存储空间

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307291540593.png" alt="image-20230729154047436" style="zoom: 50%;" />

+ `lsblk`：查看磁盘分配空间

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307291435607.png" alt="image-20230729143525500" style="zoom:67%;" />

+ `df -h`：查看磁盘使用情况（-h：带计量单位）

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307291543140.png" alt="image-20230729154307100" style="zoom:67%;" />

+ `du [选项] [指定目录]`：指定目录占用大小汇总

  > 若不填写指定目录则默认查询当前目录的磁盘占用情况

  + -h：带计量单位
  + -a：含文件
  + -c：列出明细的同时增加汇总值
  + -s ：生成汇总信息，即只显示目录或文件的总大小，而不显示其包含的每个子目录或文件的详细大小
  + --max-depth=1：展示子目录最大深度
  
  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403311301330.png" alt="image-20240331130056254" style="zoom:67%;" />

# 8. 进程管理

1. 在 Linux 中，每个执行的程序都称为一个进程，每一个进程都分配一个 ID 号【PID，进程号】
2. 前台进程就是用户目前的屏幕上可以进行操作的进程
   后台进程则是实际在操作，但在屏幕上却无法看到的进程【如 MYSQL 服务】
3. 一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中，直到关机才结束

+ `ps [选项]`：查看进程相关信息

  + -a：显示当前终端【前台】的所有进程信息
  + -u：以用户的格式显示进程信息
  + -x：显示后台进程运行的参数

  ```shell
  # 还可以配合grep语句对显示内容进行过滤
  # 如下是看看是否有sshd服务
  ps -aux | grep sshd
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403311317508.png" alt="image-20240331131744471" style="zoom:67%;" />

  各列代表的含义如下👇：

  + USER：用户名称
  + PID：进程号
  + %CPU：进程占用 CPU 的百分比
  + %MEM：进程占用物理内存的百分比
  + VSZ：进程占用的虚拟内存大小【单位：KB】
  + RSS：进程占用的物理内存大小【单位：KB 】
  + TT：终端名称缩写。
  + STAT：进程状态，其中 S 代表睡眠，s- 表示该进程是会话的先导进程，N- 表示进程拥有比普通优先级更低的优先级，R- 正在运行，D- 短期等待，Z- 僵死进程，T- 被跟踪或者被停止等等
  + STARTED：进程的启动时间
  + TIME：CPU时间，即进程使用 CPU 的总时间
  + COMMAND：启动进程所用的命令和参数，如果过长会被截断显示

+ `pstree [选项]`：查看进程树，可以更直观看进程信息

  ```shell
  # 使用前确保相关包已安装
  sudo apt-get install psmisc
  ```

  + -p：显示进程的 PID
  + -u：显示进程的所属用户
  + -h：带计量单位

+ `kill [选项] 进程ID或进程名`：用于终止或者操作运行中的进程

  > 进程号通过 ps 命令进行获取

  + -9：强制终止进程，通常只在其他方法无效时使用
  + -15：以友好的方式终止进程，它会请求进程优雅地退出，允许它进行清理工作

+ `killall [选项] 进程名`：终止所有匹配（支持通配符）的进程

  + -e：对命令行参数的模式进行匹配
  + -g：终止与指定进程组的进程匹配
  + -i：交互式模式，询问是否终止每个进程
  + -q：静默模式，不显示任何输出
  + -u：仅终止由指定用户拥有的进程

# 9. 服务管理

> 服务（service）本质就是进程，不过是运行在后台的，通常会**监听某个端口**，等待其它程序的请求，如mysql , sshd，防火墙等，因此我们又称其为守护进程。

+ `service 服务名 [start/stop/restart/reload/status]`
  `systemctl [start/stop/restart/status] 服务名`

  > 对指定服务进行启动/停止/重启/重新加载/查看状态操作

```python
systemctl list-units --type=service
# 设置服务自启动状态
systemctl list-unit-files [| grep 服务名]
# 设置服务开机启动
systemctl enable 服务名
# 关闭服务开机启动
systemctl disable 服务名
# 查询某个服务是否是自启动的
systemctl is-enabled 服务名
```

在真正的生产环境，往往需要将防火墙打开，但问题来了，如果我们把防火墙打开，那么外部请求数据包就不能跟服务器监听端口通讯，此时就需要打开指定的端口。

```python
# centos用firewall，ubuntu使用ufw
# 打开端口
firewall-cmd --permanent --add-port=端口号/协议
# 关闭端口
firewall-cmd --permanent --remove-port=端口号/协议
# 重新载入才能生效
firewall-cmd --reload
# 查询端口是否开放
firewall-cmd --query-port=端口/协议
```

# Shell常用命令

==**`[选项]`通常就是 `-字母`，代表一些特殊作用**==

## 正则表达式

[更多元字符含义](https://www.runoob.com/regexp/regexp-metachar.html)

+ *：代表零个或多个字符
+ ？：代表任意单个字符
+ [ay]：代表该位置可以为 a 或者 y
+ ！：表示取反
+ ^/$：只匹配行首/尾

## 文件目录类

+ `pwd`：显示当前在哪个目录下【绝对路径】

+ `clear`：清除终端显示信息

+ `ls [选项] [指定文件路径]`：显示当前目录下的文件名

  + -a：显示当前目录所有的文件和目录，包括隐藏的

  + -l：以列表的方式显示信息

    ```shell
    # 若有ls -l 显示内容如下👇
    -rwxrw-r-- 1 root root 1213 Feb 2 09:39 abc
    # 1 代表文件，若是其他数字则代表子目录数【包括隐藏文件夹，无子文件夹时值为2】
    # 第一个root代表用户，第二个root代表组
    # 1213 是文件大小【字节】，如果是文件夹显示4096字节
    # Feb 2 09:39 是最后修改日期
    # abc 是文件名
    ```

    + `rwx` 作用到文件

      `r` 代表可读 `read`：可以读取，查看
      `w` 代表可写 `write`：可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在目录有写权限，才能删除该文件。
      `×` 代表可执行 `execute`：可以被执行

    + `rwx` 作用到目录

      `r` 代表可读 `read`：可以读取，ls查看目录内容
      `w` 代表可写 `write`：可以修改，对目录内创建+ 删除 + 重命名目录
      `×` 代表可执行 `execute`：可以进入该目录

    1. 第 `0` 位确定文件类型 `(d，-，l，c，b)`

       `-` 是普通文件

       `l` 是链接，相当于 `windows` 的快捷方式

       `d` 是目录，相当于 `windows` 的文件夹

       `c` 是字符设备文件，鼠标，键盘

       `b` 是块设备，比如硬盘

    2. 第 `1-3` 位确定所有者【该文件的所有者】拥有该文件的权限。

    3. 第 `4-6` 位确定所属组（同用户组的）拥有该文件的权限

    4. 第 `7-9` 位确定其他用户拥有该文件的权限

  + -F：显示时区分文件与文件夹【文件夹后加/，可执行文件加*】

+ **cd 路径**：切换到指定目录，**..**回到上一级，**~**回到家目录

+ **mkdir [选项] 路径**：在指定路径下创建文件夹

  + -p：根据需要创建父目录

+ **rmdir 目录名**：删除空目录

+ **rm -rf 目录名**：不论是否为空目录都进行删除

  > 通常 r 和 f 选项都会一起用

  + -r：递归删除整个文件夹
  + -f：强制删除不提示

+ **touch 文件名**：创建一个空文件

+ **cp [选项] source dest**：将 source 拷贝到 dest 下

  + -r：递归复制整个文件夹【没有 -r 时只能拷贝单个文件】
  + -i：当有目标位置有重名文件时会提示

+ **mv source dest**：将文件由 source 移动至 dest ，不给文件名时默认与原文件名相同，若给出的是新文件名则是移动并重命名

  > 若是 source 和 dest 在指向同一目录但文件名不同，则起到重命名的效果

+ **echo 字符串**：将字符串输出到控制台

  + echo "Hello" > a.txt：将字符串**覆盖**写入a.txt
  + echo "Hello" >> a.txt：将字符串**追加**写入a.txt

+ **ln -s 原文件或目录 软链接名**：为原文件创建软链接【快捷方式】

+ **cat [选项] 文件名 [| more]**：查看文件内容【无编辑权限】，加上more可以使得文件内容可以按页显示

  + -n：显示行号

  + more 可以配合以下快捷键进行操作👇

    <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307121404066.png" alt="image-20230712140353936" style="zoom: 50%;" />

## 时间日期类

+ **date "格式"**：显示当前日期，未填格式时为默认格式

  + 格式中，Y是年份，m是月份，d是天，H是时，M是分，S秒

    ```shell
    # 开头固定为+
    date "+%Y-%m-%d %H:%M:%S"
    ```
    
    <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202403202044285.png" alt="image-20240320204356204" style="zoom: 80%;" />

+ **cal [年份]**：显示当前月份日历，填写年份时显示全年日历

## 查找

+ **find 目录名 [选项] xxx**：从指定目录向下遍历各个子目录，将满足条件的文件或目录显示在终端

  + -name：按照指定的文件名查找文件
  + -user：查找属于指定用户名所有文件
  + -size：按照指定的文件大小查找文件

  ```shell
  # 查找home目录下的所有txt文件
  find /home -name "*.txt"
  # 查找根目录下大于200M的文件【+是大于，-是小于，只有数字是等于，单位有k,M,G】
  find / -size +200M 
  ```

+ **locate 搜索位置**：无需遍历整个系统，可以更快定位

  > 由于 locate 指令基于数据库进行查询，所以第一次运行前，必须使用 updatedb 指令创 建 locate 数据库

  ```shell
  # 有数据更新时候就需要执行一次
  # yum install mlocate
  updatedb
  locate "目标位置"
  ```

+ **grep [选项] 查找内容 源文件**：grep 是过滤查找，管道符号|可以连接多个条件

  + -n：显示行号
  + -i：忽略字母大小写

  ```shell
  # 查看a.txt文件内容中包含字符串"hello"的部分
  # 查找内容部分可以是正则表达式
  cat a.txt | grep [选项] "hello"
  ```
  
+ **wc [选项] 文件**：根据选项返回文件对应信息

  + -c：统计字节数
  + -l：统计行数
  + -m：统计字符数
  + -w：统计字数

+ **tail [选项] 文件**：显示文件的末尾内容

  + `-n <number>`：显示文件末尾的最后`<number>`行
  + `-f`：在显示文件末尾内容的同时继续监视文件的变化，实时显示新增的内容

  ```shell
  # 查看a.txt文件末尾5行的内容
  tail -n 5 a.txt
  ```


## 压缩和解压

+ **zip [选项] xxx.zip 文件/目录**：压缩文件为zip格式

  + -r：递归压缩，即压缩目录

  ```shell
  # 将home目录以及其下子文件一起压缩为myhome.zip
  zip -r myhome.zip /home/
  ```

+ **upzip [选项] xxx.zip**：解压zip文件

  + -d：指定解压后文件的存放目录

  ```shell
  # 将myhome.zip解压到opt目录下
  unzip -d /opt/ myhome.zip
  ```

+ **tar [选项] xxx.tar.gz 文件/目录**：根据不同选项对文件进行压缩/解压

  > 通常解压使用 `-zxvf`，压缩使用 `-zcvf`

  + -c：产生.tar打包文件
  + -v：显示详细信息
  + -f：使用文件名，压缩解压都要用且放最右边
  + -z：使用gzip算法
  + -x：解压.tar文件

  ```shell
  # 压缩pig.txt和cat.txt
  tar -zcvf pc.tar.gz /home/pig.txt /home/cat.txt
  # 将home目录所有内容压缩为myhome.tar.gz
  tar -zcvf myhome.tar.gz /home/
  # 解压test.tar.gz到temp文件夹下【-C表示temp为目录】
  tar -zxvf test.tar.gz -C temp
  ```

## 磁盘空间

```shell
# 查看磁盘空间
df -h
# 目录下所有文件和目录的大小的排序结果
# -n：令sort命令将数字按值排序
# -r：降序排序
du -ah /* | sort -nr
```

# Ubuntu图形界面快捷键

```shell
# 打开终端
Ctrl + Alt + T
# 终端进入全屏模式
F11
# 调大终端字体
Ctrl + Shift + +【主键盘上的+】
# 调小终端字体
Ctrl + -【主键盘上的-】
Enter【逐行查看】/Space【逐页查看】
```

# Linux各文件夹作用

1. **/bin**:

   > 包含一些基本的系统命令，这些命令可以在系统引导时就可以使用，这些**命令对于系统的基本运行是必需**的。

2. **/boot**:

   > 包含引导加载程序和内核镜像文件，用于引导系统。通常情况下，这个文件夹包含用于启动Linux系统的所有必需文件。

3. **/dev**:

   > 包含设备文件，这些文件用于与系统中的硬件设备进行通信。例如，硬盘、键盘、鼠标等设备都通过这个文件夹进行访问。

4. **/etc**:

   > 包含系统配置文件，这些文件包括各种系统级别的配置，如网络配置、用户配置、软件配置等。

5. **/home**:

   > 包含用户的主目录。每个用户都有一个子目录，其中包含他们的个人文件和设置。

6. **/lib**:

   > 包含共享库文件，这些文件是用于支持可执行文件的共享代码的动态链接库。这些库在程序运行时被加载。

7. **/media** 和 **/mnt**:

   > 用于挂载可移动介质，如USB驱动器、光盘等。通常，当你插入一个USB驱动器时，它会自动挂载到这些目录下。

8. **/opt**:

   > 用于安装可选的软件包。这些软件包通常不是系统的一部分，而是额外安装的。

9. **/proc**:

   > 包含系统进程的虚拟文件系统。这个文件夹中的文件提供了有关当前运行进程和系统状态的信息。

10. **/root**:

    > root用户的主目录，root是系统中最高权限的用户。

11. **/sbin**:

    > 包含一些系统管理员使用的系统命令，这些命令通常需要特殊的权限才能运行。

12. **/srv**:

    > 用于存储特定服务或系统提供的数据。例如，Web服务器可能会将网站文件存储在此处。

13. **/tmp**:

    > 用于存储临时文件。这个文件夹中的文件通常会在系统重启时被清理掉。

14. **/usr**:

    > 包含用户安装的应用程序和文件。通常，系统管理员安装的软件包都会放在这个目录下。

15. **/var**:

    > 包含经常变化的文件，如日志文件、数据库文件等。这些文件的内容通常是系统或应用程序的状态信息。

# Linux环境部署

## JDK

1. 官网下载 JDK 安装包，将其放置 /usr/java 文件夹下

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202404282116347.png" alt="image-20240428211627291" style="zoom:50%;" />

2. 配置环境变量

   ```bash
   JAVA_HOME=/usr/java/JDK17
   PATH=$PATH:$JAVA_HOME/bin
   CLASSPATH=.:$JAVA_HOME/lib
   export JAVA_HOME PATH CLASSPATH
   ```

   1. `JAVA_HOME=/usr/java/JDK17`：
      + 这一行定义了一个环境变量 `JAVA_HOME`，它的值是 `/usr/java/JDK17`。
      + `JAVA_HOME` 通常用于指定Java开发工具包（JDK）的安装路径。在这个例子中，JDK的安装路径被设置为 `/usr/java/JDK17`。
   2. `PATH=$PATH:$JAVA_HOME/bin`：
      + 这一行修改了 `PATH` 环境变量，将Java可执行文件的路径添加到了现有的 `PATH` 中。
      + `$PATH` 表示在原有的 `PATH` 值的基础上进行添加，`$JAVA_HOME/bin` 表示Java的可执行文件所在的目录。
      + 这样做的目的是让系统能够在任意路径下都能找到Java相关的命令，比如 `java`、`javac` 等。
   3. `CLASSPATH=.:$JAVA_HOME/lib`：
      + 这一行定义了一个环境变量 `CLASSPATH`，用于指定Java编译器和运行时环境在查找类文件时所需搜索的路径。
      + `.` 表示当前目录，`$JAVA_HOME/lib` 表示Java类库所在的目录。
      + 这样设置 `CLASSPATH` 可以让Java编译器和运行时环境在查找类文件时优先搜索当前目录和Java类库目录。
   4. `export JAVA_HOME PATH CLASSPATH`：
      + 这一行使用 `export` 命令将 `JAVA_HOME`、`PATH`、`CLASSPATH` 这三个环境变量导出，使它们对当前用户的所有子进程都可见。
      + 这样做的目的是确保在新的shell会话或子进程中，这些环境变量仍然保持有效，以便于使用Java开发工具包和运行Java程序。

3. 刷新profile文件，再通过`java -version`查看jdk版本

   ```shell
   source /etc/profile
   ```

   ![image-20240428211145729](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202404282111831.png)

## Tomcat

1. 官网下载Tomcat，将其放置 /usr/tomcat 文件夹下

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202404282117617.png" alt="image-20240428211752581" style="zoom:50%;" />

2. 配置环境变量

   > 此处顺带配置了 JDK 环境，注意 JDK 和 Tomcat 版本需要适配

   ```bash
   JAVA_HOME=/usr/java/JDK8
   CATALINA_HOME=/usr/tomcat/tomcat8
   PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
   CLASSPATH=.:$JAVA_HOME/lib:$CATALINA_HOME/lib
   export JAVA_HOME CATALINA_HOME PATH CLASSPATH
   ```

3. 刷新profile文件，进入tomcat的bin目录下操作 startup.sh 启动 tomcat 服务器，能够通过 ip:8080 端口访问即部署成功

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202404291032477.png" alt="image-20240429103154367" style="zoom:80%;" />





