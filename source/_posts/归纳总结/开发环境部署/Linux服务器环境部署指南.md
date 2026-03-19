---
title: Linux服务器环境部署指南
date: 2025-07-31 17:17:50
excerpt: Linux服务器处于离线环境时，许多资源无法一步到位，需要手动部署相关依赖环境，过程繁琐且易遇到奇奇怪怪的问题，现将完美解决方案进行记录，便于后续有类似需求时提高处理效率
tags: 
  - 开发环境部署
categories:
  - [归纳总结,开发环境部署]
---

# 1. VSCode插件离线安装

> VSCode下载地址：https://code.visualstudio.com/download

插件安装包下载地址：https://marketplace.visualstudio.com/

> 若该地址下载的插件安装包无法正确安装，可以来到插件对应的GitHub地址进行下载后缀为.vsix的安装包，例子如下：

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718499.png" alt="image-20250723181704821" style="zoom:50%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718497.png" alt="image-20250723181736397" style="zoom:67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718492.png" alt="image-20250723181757154" style="zoom:50%;" />

下载完安装包后，通过XFTP将文件传输至于服务器，接着直接安装即可

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718529.png" alt="image-20250723181904627" style="zoom:67%;" />

# 2. 局域网环境VSCode远程连接服务器

## 2.1 SSH服务安装

1. 下载SSH服务所需的安装包（与系统版本对应）

   > 一个比较方便的方法是在Windows商店下载对应的Ubuntu系统（确保系统是初始状态），利用本地的网络环境执行如下命令

   ```bash
   # 下载openssh-server及其依赖项
   sudo apt-get install --download-only --reinstall -o Dir::Cache::archives=/mnt/d/apt_pkgs openssh-server -y
   ```
   
2. 上传后执行安装命令

   > Ubuntu系统安装该服务后续开机会自启动

   ```bash
   # 安装目标路径下的所有.deb后缀安装包
   dpkg -i /目标路径/*.deb
   # 防火墙允许22号端口通过
   ufw allow 22
   # 启动SSH服务
   systemctl start ssh
   ```

## 2.2 vscode本地服务器安装

> 当VSCode开启SSH服务时，会在目标主机安装VScode本地服务器，作用是映射本地与远程的路径信息以及同步编辑行为等。在离线环境下，相关资源无法从互联网获取，因此需要手动进行干预

1. 按照如下👇方式查看自己VSCode的commit信息并保存

   > 可以理解为建立连接时的id信息

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718578.png" alt="image-20250728102318409" style="zoom: 33%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718515.png" alt="image-20250728102419828" style="zoom: 50%;" />

2. 浏览器分别输入以下网址，进行文件下载并创建相应文件夹

   > **commit必须进行对应替换，否则最后连接后会出现无法读取远程目录的情况！！！**

   + **vscode-server-linux-x64.tar**：`https://update.code.visualstudio.com/commit:替换为自己的commit号/server-linux-x64/stable`
   + **vscode_cli_alpine_x64_cli.tar.gz**：`https://update.code.visualstudio.com/commit:替换为自己的commit号/cli-alpine-x64/stable`

   ```bash
   # 在用户目录下创建.vscode-server/cli/servers三个文件夹
   mkdir -p ~/.vscode-server/cli/servers
   # 在servers文件夹下再创建Stable-{commit_id}文件夹
   mkdir Stable-{commit_id}
   ```

   目录层级如下所示

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718025.png" alt="image-20250728104413292" style="zoom:50%;" />

3. 解压文件至指定目录

   ```bash
   # 将vscode-server-linux-x64.tar压缩包解压至刚刚创建的路径，commit_id进行相应替换
   # 解压后的文件夹需要重命名为server
   tar -zxvf /目标路径/vscode-server-linux-x64.tar.gz -C ~/.vscode-server/cli/servers/Stable-{commit_id}
   # 将vscode_cli_alpine_x64_cli.tar.gz压缩包（仅有code文件）解压到.vscode-server下
   tar -zxvf /目标路径/vscode_cli_alpine_x64_cli.tar.gz -C ~/.vscode-server
   # 将文件命名为"code-{commit_id}"，如下👇
   ```
   
   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202507311718033.png" alt="image-20250728105124921" style="zoom:50%;" />
   
   由此即可完成vscode本地服务器配置

若后续有新的主机需要连接服务器，只需要在对应位置创建新的`Stable-{commit_id}`文件夹并解压`vscode-server-linux-x64.tar`压缩包以及解压`vscode_cli_alpine_x64_cli.tar.gz`，将其重命名为`code-{commit_id}`并将其移动到对应目录位置即可

# 3. 离线部署conda虚拟环境

如果需要在服务器离线部署Python虚拟环境（服务器要提前安装MiniConda），可参考以下步骤：

> Miniconda下载地址：https://repo.anaconda.com/miniconda/
>
> pip 通过 -e 参数进行安装但是后续修改了项目位置，可以在envs/虚拟环境名称/lib/当前环境`python版本/site-packages/__editablexxx.py`进行修改

1. 寻找拥有MiniConda且联网的Linux系统，创建初始环境并指定python版本

   ```bash
   # 创建指定python版本且名为env_name的虚拟环境，安装conda-pack工具
   conda create --name env_name python=xxx conda-pack工具
   ```

   将当前基础环境进行打包

   ```bash
   # 打包env_name这个虚拟环境，只需将其解压至conda对应的envs目录下即可完成迁移
   # 注意：打包的系统需要与虚拟环境的系统一致，否则环境无法兼容
   pip install conda-pack
   conda pack -n env_name -o env_name.tar.gz
   # 导出requirements.txt
   pip freeze > requirements.txt
   ```

   利用XFtp或其他的文件传输工具将工具传输至于离线服务器的`minicoda/envs`目录下，将当前环境进行解压到指定目录即可完成基础环境的迁移

   ```bash
   # 在minicoda/envs路径下创建env_name文件夹
   mkdir -p env_name
   # 解压虚拟环境
   tar -zxvf env_name.tar.gz -C env_name
   ```

   > 可在离线服务器中存放一个仅有python的库作为环境源，后续若是需要类似的python虚拟环境只需要复制`minicoda/envs`下的环境源文件夹即可

2. 在自己的Windows电脑上安装相关依赖

   ```python
   # 安装Linux平台与python3.10匹配的requirements.txt涉及的二进制包(.whl)到当前的offline文件夹中
   pip download -r requirements.txt -d ./offline/ --platform manylinux2014_x86_64 --python-version 3.10  --only-binary=:all:
   
   # 若只想单独安装某第三方库，则使用如下命令
   # 安装Linux平台与python3.10匹配的的pandas及其相关依赖的二进制包(.whl)到当前的offline文件夹中
   pip download pandas -d ./offline/ --platform manylinux2014_x86_64 --python-version 3.10  --only-binary=:all:   
   ```

   将 offline 文件夹压缩传送至离线服务器

3. 离线服务器安装依赖

   ```bash
   # 切换至目标虚拟环境
   conda activate env_name
   # 根据当前的依赖生成requirements.txt文件
   ls ./offline/*.whl > requirements.txt
   # 安装所有依赖，--proxy http://192.168.10.100:10809【添加代理】
   pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple 
   # 删除环境
   conda remove --name 环境名 --all -y
   ```

# 4. Linux常用命令

## 4.1 下载安装

**apt软件包**

```bash
sudo apt update
# 确保本地系统环境足够干净，否则依赖下载不全
# 下载软件包到指定目录
sudo apt-get install --download-only --reinstall  -o Dir::Cache::archives=存储地址 包名 -y

# 安装软件对应的所有.deb
sudo dpkg -i ./*deb
```

> 由于deb安装并不会判断依赖关系，因此初次安装有些依赖可能装不上，只需要再次执行即可

**conda环境**

```bash
# 将本地的虚拟环境打包（直接解压至miniconda的envs的空目录下就可以使用）
conda pack -n your_env_name -o your_env_name.tar.gz
```

## 4.2 防火墙

```bash
# 查看防火墙当前存在的规则
sudo ufw status numbered
# 开放xx端口
sudo ufw allow xx
# xx端口禁止通行
sudo ufw deny xx
# 删除某条规则[最左侧的序号]
sudo ufw delete xx
# 重启防火墙
sudo ufw reload
# 禁用/启用防火墙
sudo ufw disable/enable 
```

## 4.3 权限

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

## 4.4 服务管理

```bash
# 设置服务开机启动/关闭服务开机启动
systemctl enable/disable 服务名
# 查询某个服务是否是自启动的
systemctl is-enabled 服务名
# 查看服务当前状态[start,stop,restart]
systemctl status 服务器
```

## 4.5 文件

```bash
# 递归复制
cp -r source dest
# 创建多级文件夹
mkdir -p xxx
# 剪切，不给文件名时默认与原文件名相同
mv source dest
```

## 4.6 磁盘

**永久挂载外接硬盘**

```bash
# 查看所有存储设备及分区，定位到需要挂载的设备并记住UUID与文件系统类型
lsblk -f

# 将存储设备sda3挂载到/mnt/storage4T【文件系统为ext4是设备】
sudo vim /etc/fstab
########
# 在文件尾部添加如下内容，并保存退出
UUID=xxx /mnt/storage4T ext4 defaults 0 2
#UUID=你的UUID 挂载位置 文件系统类型 defaults 0 2
########
# 配置生效
sudo mount -a
systemctl daemon-reload
# 查看是否挂载成功，接着重启验证
df -h  
```

**磁盘分区格式化**

```shell
# 进入磁盘分区工具
diskpart
# 列出所有磁盘
list disk
# 选择需要操作的磁盘
select disk 2
# 清除磁盘分区表和所有数据
clean
# 在我的电脑中右键格式化即可
```

## 其他

```bash
# 关机
init 0
# 查看80端口占用情况
lsof -i :80
# 查询系统架构
uname -m
# 查看显卡状态
watch -n 1 nvidia-smi
# 打开终端
Ctrl + Alt + T
# 终端进入全屏模式
F11
# 调大终端字体
Ctrl + Shift + +【主键盘上的+】
# 调小终端字体
Ctrl + -【主键盘上的-】
```

# 5. 显卡工具及驱动安装

> 若要在算法中使用Nvidia显卡，驱动以及CUDA工具必不可少

使用Nvidia官方的CUDA Toolkit进行安装时可以顺带安装对应的驱动，因此无需提前安装驱动

> 直接搜索CUDA Toolkit对应版本进入官网选配即可，通常12.8比较是主流的版本

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061515740.png" alt="image-20250806151524557" style="zoom: 50%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061520309.png" alt="image-20250806152023220" style="zoom:50%;" />

👆选配后往下滑，可看见对应的下载地址👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061522100.png" alt="image-20250806152222010" style="zoom:50%;" />

安装前需要确保服务器拥有gcc，g++，make，binutils软件包（使用apt下载），先安装gcc再安装g++，顺序错了会导致gcc某些包安装失败，此时需要手动软链接cc工具

```bash
# 手动创建cc到gcc的软链接【确保gcc已安装】
sudo ln -s /usr/bin/gcc /usr/bin/cc
# 先安装gcc能查询到说明安装
cc --version
```

赋予安装包执行权限

```bash
# 到指定目录下执行，赋予执行权限
sudo chmod +x ./cuda_12.8.0_570.86.10_linux.run
# 关闭图形化界面【显卡驱动安装必须关闭图形化界面】
# 该命令黑屏时按快捷键Ctrl+Alt+F2~F6切换至于其他文本终端
sudo systemctl isolate multi-user.target
# 执行安装包
sudo ./cuda_12.8.0_570.86.10_linux.run
```

安装过程依照系统提示即可，其中X代表选择，空白代表没选中

```bash
# 配置环境变量
vim ~/.bashrc
##############
# 在~/.bashrc末尾添加如下内容
export PATH=/usr/local/cuda-12.8/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.8/lib64:$LD_LIBRARY_PATH
############
# 刷新环境变量
source ~/.bashrc
# 若有对应版本输出则安装成功
nvidia-smi
nvcc --version
# 切换为图形模式
sudo systemctl isolate graphical.target
```

# 6. Ubuntu系统重装

> 准备好一个16GB以上的空U盘！若系统卡死，则同时按住Ctrl + Alt，接着依次按PriSc，R，E，I，S，U，B即可完成安全重启

1. 在清华镜像源下载所需版本的系统资源（[Ubuntu](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/)），选择`desktop-amd64.iso`结尾的文件下载

2. 下载系统启动盘构造工具[UltraISO](https://filehippo.com/download_ultra-iso/post_download/?dt=internalDownload)，双击安装，选择默认选项即可，安装完成启动后，选择“继续试用”

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061545702.png" alt="image-20250805090644910" style="zoom:33%;" />

3. 点击“文件”-->“打开”，选择自己的在第1步下载的ISO系统镜像路径，接着点击“启动”-->"写入硬盘映像"

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061545640.png" alt="image-20250805092442965" style="zoom:33%;" />

4. 选择本地空U盘路径，点击“格式化”，再点击“写入”

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202508061544941.png" alt="image-20250805093307182" style="zoom: 33%;" />

5. 将U盘插入主机，开机过程中来回按F12，直至进入BIOS系统，选择使用U盘启动

6. 接着按照系统指引进行初始化即可

当电脑内置Nvidia显卡且安装过程中未勾选安装初始Nvidia驱动时（推荐勾选，后续安装），开机后可能出现卡在Logo页面的情况，此时需要如下操作才能进入系统页面

1. 同时按住Ctrl + Alt，接着依次按PriSc，R，E，I，S，U，B完成安全重启

2. 重启过程中选择“Ubuntu 高级选项”-->"recovery mode"，加载完毕后选择"root"选项进入，在命令行输入如下命令：

   ```bash
   sudo vi /etc/default/grub
   # 按i进入编辑模式，将GRUB_CMDLINE_LINUX_DEFAULT修改为如下内容后保存退出
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nomodeset"
   sudo update-grub
   reboot
   ```

# 7. 内外网代理配置

## 7.1 本地访问外网

> 根据[该文章](https://zelikk.blogspot.com/2021/11/racknerd-vps-v2ray-1g-1t-12g-768mb-1088.html)指示购买外网服务器并进行相关配置，在本地安装`v2ray`并进行相关配置，本地成功访问到`google`即为成功

## 7.2 内网访问外网

> **现有本地内网服务器一台（192.168.10.103）,笔记本电脑一台（可以通过v2ray访问到外网，也能通过内网路由与内网服务器完成通信），如何令内网服务器通过笔记本电脑这个中介访问到外网？**

1. 购置免驱无线网卡（如TP-LINK AX300），插入笔记本电脑，安装网卡自带程序，使得笔记本拥有两个`IP`地址，令笔记本自带的WLAN连接至互联网，令配备的无线网卡连接至内网，通过`ipconfig`命令查询到两个`WLAN IP`地址即为成功

   > 在未设置内网代理前，笔记本电脑将代理请求统一转发至127.0.0.1:10809端口，并由v2ray完成与外网服务器的连接从而完成通信。本着能正常运行就少折腾的原则，将拥有互联网访问能力的IP分配给笔记本原生网卡，将只有内网访问能力的IP分配给新网卡，尽可能控制变量

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602281947145.png" alt="image-20260228194655045" style="zoom:50%;" />

2. 笔记本电脑打开`v2ray`，点击“设置”->“参数设置”->"运行来着局域网的连接"，点击确定

   > 确保内网服务器可以共享笔记本电脑的网络

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602281958118.png" alt="image-20260228195826067" style="zoom:67%;" />

   完成设置后，软件左下角局域网端口监听字样不为`none`即为成功，此时笔记本电脑将对本地`10808`与`10809`端口持续监听

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602282000938.png" alt="image-20260228200015906" style="zoom:50%;" />

3. 启动`v2ray`系统代理，确保本地笔记本可以正常访问外网

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602282002835.png" alt="image-20260228200212796" style="zoom:50%;" />

4. Ubuntu系统内网服务器点击“设置”->"网络"，打开网络代理手动配置模式，填写笔记本电脑内网地址（即外接无线网卡地址），端口号保持与v2ray中监听的端口一致，点击保存

   > 若是不存在图形界面则搜索等效命令实现

   ![image-20260228200739735](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602282007820.png)

5. 内网服务器打开火狐浏览器，若能访问google即为成功

   > 代理基本信息设置完毕后，后续可通过如下命令控制内网服务器的代理情况

   ```shell
   # 1. 开启代理，有网
   gsettings set org.gnome.system.proxy mode 'manual'
   # 2. 关闭代理（无网）
   gsettings set org.gnome.system.proxy mode 'none'
   # 查看当前代理模式
   gsettings get org.gnome.system.proxy mode
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202602282017600.png" alt="image-20260228201729544" style="zoom:50%;" />

# 8.环境打包

## 8.1 python程序编译

> 建议与目标客户服务器同架构，例如都是 x86_64 Linux

```shell
# 安装打包工具类
pip install pyinstaller
# 只打包必要依赖（检查monitor.py目录下的所有文件以及依赖），可执行文件名为resource_monitor
pyinstaller --onefile --name resource_monitor monitor.py
```

# 9. docker使用手册

> 关于docker的部署直接根据需求询问DeepSeek即可，如“在Ubuntu24.04服务器（显卡RTX5880 Ada * 4）上，如何安装支持显卡的docker环境，请一步步告诉我”

## 9.1 镜像

> 镜像是封装好的环境，可以理解为系统安装包

```shell
# 查看本地所有镜像
docker images
```

通过`run`命令可以使镜像实例化，即生成容器，容器是实际可用的环境，如下是例子

> run之后默认开启容器

```shell
docker run -d -p 8080:80 -v /主机/实际路径:/容器/实际路径 --name app01 nginx
```

+ `-d`：后台运行容器（detach）。

+ `-p 8080:80`：将主机的8080端口映射到容器的80端口，访问主机8080端口即访问容器内的nginx服务。

+ `-v /主机/路径:/容器/路径`：将主机目录挂载到容器目录（绑定挂载）。

  > 主机目录**必须提前存在**，否则会报错；容器内目录如果不存在，Docker会自动创建。

+ `--name app01`：给容器命名为 `app01`。

+ `nginx`：使用nginx官方镜像。

**run命令中常见参数**

| 参数               | 作用                                                         |
| :----------------- | :----------------------------------------------------------- |
| `-it`              | 交互式运行，分配终端（常与 `--rm` 配合临时测试）             |
| `--rm`             | 容器退出后自动删除，避免残留                                 |
| `--restart=always` | 容器停止时自动重启（如开机自启、崩溃重启）                   |
| `-e VAR=value`     | 设置环境变量，例如 `-e MYSQL_ROOT_PASSWORD=123`              |
| `--network=bridge` | 指定网络模式（`bridge`/`host`/`none`/自定义）                |
| `--link`           | 连接其他容器（已逐渐被自定义网络取代）                       |
| `-v` 或 `--mount`  | 挂载卷或绑定目录（`--mount` 语法更详细）                     |
| `-p`               | 端口映射，格式 `主机端口:容器端口` 或 `IP:主机端口:容器端口` |
| `-d`               | 后台运行                                                     |
| `--name`           | 指定容器名                                                   |
| `--hostname`       | 设置容器内主机名                                             |
| `--cpus`           | 限制CPU使用，如 `--cpus=2`                                   |
| `-m`               | 限制内存，如 `-m 4g`                                         |
| `--gpus`           | 分配GPU设备👇                                                 |

```shell
# 使用所有 GPU
docker run --gpus all ...
# 使用指定 GPU（从0开始编号）
docker run --gpus '"device=0,1"' ...
# 使用一定数量的 GPU（调度器分配）
docker run --gpus 2 ...
```

## 9.2 容器

> 容器是镜像的实例，对容器的操作就是对系统本身的操作

```shell
# 查看运行中的容器
docker ps 
# 查看所有容器
docker ps -a
```

```shell
# 启用/关闭/重启【可以理解为开机/关机/重启】一个已经存在的容器（容器ID可以不写全，只要足够区分即可）
docker start/stop/restart 容器ID/容器NAMES
# 查看容器状态
docker stats 容器ID/容器NAMES
# 进入app01容器的bash环境
docker exec -it app01 bash
```

