---
title: Docker基础
date: 2023-09-09 14:27:23
excerpt: docker通过容器化的思想（容器共享内核），将每个应用所需的环境进行隔离，使得同一台服务器上部署管理多个环境变得更为简便
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 1. Docker简介

Docker 通过 Docker Hub 实现一行命令安装应用（镜像）【Nginx，Mysql等】，避免繁琐的部署操作。同时通过轻量级（相对于虚拟机）的容器化的思想（容器共享内核），将每个应用所需的环境进行隔离，使得同一台服务器上部署管理多个环境变得更为简便。

> 简单来说，可以把 Docker 是一个大容器，里边可以根据需求下载各类镜像，再根据需要自定义镜像并创建出一个个小容器，容器环境是最终暴露给外部的环境。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291003280.png" alt="image-20241129100322967" style="zoom:80%;" />

# 2. Windows环境部署docker

1. 确保Windows为专业版（否则无 Hyper-V 选项）且做如下设置

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141603162.png" alt="image-20241114160341845" style="zoom: 33%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141604681.png" alt="image-20241114160435595" style="zoom:33%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141605307.png" alt="image-20241114160519278" style="zoom:50%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141605681.png" alt="image-20241114160550647" style="zoom:50%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141606901.png" alt="image-20241114160614875" style="zoom:50%;" />

2. docker安装

   [docker下载地址【Windows的x86架构对应AMD64】](https://www.docker.com/products/docker-desktop/)

   第一次启动时要使用管理员身份！

# 3. 入门案例

> 拉取nginx镜像，自定义带有 nginx 镜像的容器名为 mynginx ，启动 nginx 服务，并成功访问欢迎界面

```shell
# 拉取 nginx 镜像
docker pull nginx 
# 后台启动服务，自定义镜像名为mynginx，完成容器内部端口80到实际端口88的映射
docker run -d --name mynginx -p 80:88 nginx
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291426684.png" alt="image-20241129142619651" style="zoom:80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291425373.png" alt="image-20241129142503305" style="zoom:67%;" />

# 4. 镜像相关命令

+ 查询是否有 nginx 镜像

  ```shell
  docker search nginx 
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291021970.png" alt="image-20241129102153929" style="zoom:67%;" />

+ 下载 nginx 镜像

  > 无法科学上网的需要配置国内下载仓库，具体看其他帖子

  ```shell
  docker pull nginx 
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291025416.png" alt="image-20241129102539380" style="zoom:67%;" />

  如果需要下载指定版本的镜像，需要去 Docker Hub 查询

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291031510.png" alt="image-20241129103150433" style="zoom:50%;" />

  找到需要版本后，可以用框框处语句进行下载

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291032013.png" alt="image-20241129103249967" style="zoom:67%;" />

+ 查询当前所拥有的镜像

  ```shell
  docker images
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291026955.png" alt="image-20241129102642927" style="zoom:80%;" />

+ 删除 nginx 镜像

  ```shell
  # 需要输入镜像完整名（也可以使用Image ID删除）
  docker rmi nginx:latest 
  ```
  
+ 保存镜像

  > 保存的镜像可像正常文件一样传输，用户只需要拿到镜像加载即可复刻镜像环境

  ```shell
  # 将指定容器环境（此处是mynginx）保存为镜像（mynginx:1.0 ，1.0是标签）
  docker commit -m "update xxx" mynginx mynginx:1.0 
  # 将mynginx:1.0镜像保存为压缩包
  docker save -o mynginx.tar mynginx:1.0 
  ```

+ 加载镜像

  > 用于复刻封装好的镜像环境

  ```shell
  # 加载mynginx.tar封装的镜像
  docker load -i mynginx.tar
  ```

  加载完毕后使用 run 语句即可将其复刻为容器实例

# 5. 镜像推送社区

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291507474.png" alt="image-20241129150709437" style="zoom:80%;" />

1. 准备好 Docker Hub 账号并在终端登录

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291523844.png" alt="image-20241129152327720" style="zoom:50%;" />

   ```shell
   docker login
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291525375.png" alt="image-20241129152534333" style="zoom:80%;" />

2. 复制镜像并使用指定名称，格式为：`用户名/镜像名`

   ```shell
   docker tag mynginx:1.0 excellentlong/mynginx:v1.0
   ```

   ![image-20241129153002648](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291530682.png)

3. 推送镜像到指定仓库

   ```shell
   # 确保账号为excellentlong且存在mynginx仓库
   docker push excellentlong/mynginx:v1.0
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291553099.png" alt="image-20241129155310058" style="zoom:67%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291555731.png" alt="image-20241129155550613" style="zoom: 50%;" />

   后续别人需要使用只需要搜索使用 pull 命令即可拉取到本地

# 6. 容器相关命令

+ 运行 nginx:latest 镜像

  ```shell
  # 创建并启用一个新的容器实例
  docker run nginx:latest
  # 后台启动服务，自定义容器名为mynginx，完成容器内部端口80到实际端口88的映射
  docker run -d --name mynginx -p 88:80 nginx
  ```

  容器内部的端口是独立的（此处的80），暴露在外的端口是唯一的（此处的88）

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291414692.png" alt="image-20241129141407555" style="zoom:50%;" />

  

+ 查看应用

  ```shell
  # 查看运行中的容器
  docker ps 
  # 查看所有容器
  docker ps -a
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411291128607.png" alt="image-20241129112828575" style="zoom:50%;" />

+ 启动

  ```shell
  # 启用一个已经存在的容器（容器ID可以不写全，只要足够区分即可）
  docker start 容器ID/容器NAMES
  ```

+ 停止

  ```shell
  docker stop 容器ID/容器NAMES
  ```

+ 重启

  ```shell
  docker restart 容器ID/容器NAMES
  ```

+ 状态

  ```shell
  docker stats 容器ID/容器NAMES
  ```

+ 日志

  ```shell
  docker logs 容器ID/容器NAMES
  ```

+ 删除

  ```shell
  # f参数是强制删除，即不关闭容器的情况下也能删除
  docker rm [-f] 容器ID/容器NAMES
  ```
  
+ 进入某个容器的bash环境

  ```shell
  # 进入app01容器的bash环境
  docker exec -it app01 bash
  ```

# 7. 文件挂载

由于docker内部容器非常轻量级，因此管理编辑容器内部文件并不方便，为此我们可以在启动容器时使用文件挂载的方式将内部容器与外部环境进行关联，从而只需要在外部进行操作即可完成对内部容器的修改。

```shell
# 创建并启动app01容器，同时将容器内外路径进行关联
docker run -d -p 80:80 -v 外部目录路径:内部目录路径 --name app01 nginx
```

使用👆这一方式进行挂载是以外部环境为准，即挂载的外部目录为空，则对应的内部环境也会被置空

另外一种挂载方式是卷映射，它以内部环境为准，同样是使用 -v 进行指定，如下👇

```shell
# 使用卷挂载的方式，docker会自动分配映射目录
# 卷默认挂载在/var/lib/docker/volumes/<卷名>/_data下，windows未知
docker run -d -p 99:80 -v ngconf:/etc/nginx --name app02 nginx
docker run --add-host=www.saigoumvp.work:127.0.0.1 -v admin:/app/admin -p 8089:8089 --name adminapp adminapp:v2
```

**卷常见操作**

```shell
# 查看
docker volume ls
# 手动创建卷xxx
docker volume create xxx
# 查看卷xxx挂载位置
docker volume inspect xxx
```

# 8. 自定义网络

Docker 内部有专属网络环境，容器内部可通过此内部网相互访问（此时使用的端口是容器内部端口），默认的网络环境是 docker0

```shell
# 查看容器app01的内部网络
docker container inspect app01
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412020945374.png" alt="image-20241202094547295" style="zoom:67%;" />

进入 app02 的 bash 模式，即可通过内部 IP:端口号 直接访问

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412020951458.png" alt="image-20241202095145407" style="zoom:50%;" />

IP地址由于种种原因会容易发生变化，我们希望可以通过容器名称完成访问（类似域名），而在 docker0 网络环境下不支持这种方式，因此引入自定义网络机制

```shell
# 自定义网络mynetwork
docker network create mynetwork
# 查询存在的网络
docker network ls
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412021009682.png" alt="image-20241202100900651" style="zoom:67%;" />

```shell
# 在run时加入network关键字即可加入自定义网络
docker run -d --name app01 -p 88:80 --network mynetwork nginx
docker run -d --name app02 -p 99:80 --network mynetwork nginx
```

app01 访问 app02 的 nginx 界面

```shell
# bash环境下直接使用容器名:端口号访问
curl http://app02:80
```

# 7. 批量操作容器

>  通过 compose.yaml 文件可以批量操作容器，省去繁琐的多次参数配置，且在迁移时依旧可以执行

**实验：使用 compose.yaml 实现如下命令**

```shell
# 创建blog自定义网络
docker network create blog
# 启动mysql
docker run -d -p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=123456 \
-e MYSOL_DATABASE=wordpress \
-v mysql-data:/var/lib/mysql \
-v D:\Program\Docker\Mount:/etc/mysql/conf.d \
--restart always --name mysql \
--network blog \
mysql:8.0
# 启动wordpress
docker run -d-p 8080:80 \
-e WORDPRESS_DB_HOST=mysql \
-e WORDPRESS_DB_USER=root \
-e WORDPRESS_DB_PASSWORD=123456 \
-e WORDPRESS_DB_NAME=wordpress \
-v wordpress:/var/www/html \
--restart always --name wordpress-app \
--network blog \
wordpress:latest
```

**顶级元素**：compose.yaml 中第一层的元素

> name，service，networks，volumes，conifgs（少用），secrets（少用）

```yaml
name: myblog
services: 
  mysql: 
    # 自定义容器名称
    container_name: mysql
    # 拉取mysql最新镜像
    image: mysql
    ports:
      # 内外端口映射
      - "3307:3306"
    environment:
      # 设置初始化密码
      MYSQL_ROOT_PASSWORD: 123456
      # 设置数据库名称
      MYSOL_DATABASE: wordpress 
    volumes:
      # 将/var/lib/mysql文件夹挂载到卷mysql-data
      - mysql-data:/var/lib/mysql
      # /etc/mysql/conf.d目录关联至D:\Program\Docker\Mount
      - D:\Program\Docker\Mount:/etc/mysql/conf.d
    # 设置开机自动启动
    restart: always
    # 划分至blog自定义网络
    networks:
      - blog
  wordpress:
    image: wordpress
    ports:
      - "8086:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html
    restart: always
    networks:
      - blog
    # wordpress要在mysql之后启动
    depends_on: 
      - mysql
# 用到的卷名需要声明
volumes:
  mysql-data:
  wordpress:
# 用到的自定义网络也需要声明
networks:
  blog:
```

各顶级元素更详细写法可参考：https://docs.docker.com/reference/compose-file/

```shell
# -f指定yaml文件名，默认是compose.yaml，-d是后台启动
# 如果需要使用修改后的配置，只需要再次执行此语句即可（这样会销毁旧容器且创建新容器）
docker compose -f compose.yaml up -d
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412031624775.png" alt="image-20241203162354683" style="zoom:50%;" />

若能成功访问此界面，说明部署启动成功

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412041137618.png" alt="image-20241204113748519" style="zoom:50%;" />

```shell
# 下线容器，即移除创建的容器
docker compose down
# 移除容器时把相关镜像和卷也移除
docker compose down --rmi all -v
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412041403815.png" alt="image-20241204140313678" style="zoom:67%;" />

```shell
# 启动compose.yaml中指定名字的容器，默认定位到yaml中所有容器
docker compose start x1 x2
# 停止compose.yaml中指定名字的容器
docker compose stop x1 x2
```

# 9. 自定义镜像

> 当我们需要将本地项目部署至docker中时，需要自定义镜像，后续别人只要拥有此镜像即可运行项目

自定义镜像关键是使用 Dockerfile 文件指定所需环境，暴露端口，启动命令等信息，常见语句如下：

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412041629906.png" alt="image-20241204162949668" style="zoom: 50%;" />

**实验：在 docker 部署 SpringBootDemo.jar 项目**

SpringBootDemo.jar 下载地址如下👇

`通过百度网盘分享的文件：SpringBootDemo.jar
链接：https://pan.baidu.com/s/1vxxQdOTdXrasuU_cHGAAAg 
提取码：alib`

1. 编辑dockerfile文件

   ```dockerfile
   # 依赖jdk17镜像
   FROM meddream/jdk17
   # 指定作者名字
   LABEL author=zzzz
   # 将当前目录下SpringBootDemo.jar复制到镜像根目录下【一个镜像可以理解为一个Linux系统】，并重命名为app.jar
   COPY SpringBootDemo.jar /app.jar
   # 服务器启动指令
   ENTRYPOINT ["java","-jar","/app.jar"]
   # 声明访问端口
   EXPOSE 8080
   ```

2. 构建镜像

   ```shell
   # 使用编辑好的Dockerfile构建镜像，镜像名称为myjavaapp，版本号是v1.0，最后的.代表需要构建镜像的项目在当前目录下
   # 如果构建失败，可以先尝试使用pull将依赖镜像拉取到本地，相关镜像信息可在Docker Hub寻找
   docker build -f Dockerfile -t myjavaapp:v1.0 .
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412041724939.png" alt="image-20241204172411845" style="zoom:67%;" />

3. 创建镜像对应容器并测试

   ```shell
   docker run -d -p 8888:8080 myjavaapp:v1.0
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202412041726394.png" alt="image-20241204172606356" style="zoom:67%;" />



