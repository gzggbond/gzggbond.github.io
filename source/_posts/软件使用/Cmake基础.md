---
title: Cmake基础
date: 2023-12-09 14:27:23
excerpt: 当多个人用不同的语言或者编译器开发一个项目，最终要输出一个可执行文件或者共享库这时候神器就会使用到Cmake， 它是一个高级编译配置工具，其所有操作都是通过编译CMakeLists.txt来完成的
tags:
  - 软件使用
categories:
  - [软件使用]
---

# 1. 介绍

当多个人用不同的语言或者编译器开发一个项目，最终要输出一个可执行文件或者共享库这时候神器就会使用到Cmake， 它是一个高级编译配置工具。其所有操作都是通过编译 CMakeLists.txt 来完成的
```shell
# 安装具体过程可参考资料，此处不赘述，通常Linux会自带cmake
# 用此命令判断是否有cmake环境
cmake --version
```

**Cmake使用样例**

1. 使用 vim 编写简单 C++ 程序，命名为`main.cpp`，使用`:wq`进行保存退出

   ```c++
   #include<iostream>
   int main(){
       std::cout<<"hello world!"<<std::endl;
   }
   ```

   ```shell
   # 编译程序
   c++ main.cpp
   # 查看当前目录下的文件【目的是找到编译后的文件】
   ll
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312031604233.png" alt="image-20231203160411147" style="zoom:80%;" />

   ```shell
   # 执行当前目录下的a.out文件
   ./a.out
   ```

   输出`"hello,world!"`说明预备工作完成，证明这个文件是可以正常编译运行的，删除`a.out`后进入正题！

2. 编写`CMakeLists.txt`

   ```cmake
   PROJECT (HELLO)
   SET(SRC_LIST main.cpp)
   MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})
   MESSAGE(STATUS "This is SOURCE dir " ${HELLO_SOURCE_DIR})
   ADD_EXECUTABLE(hello ${SRC_LIST})
   ```

   ```shell
   # 使用cmake生成MakeFile文件
   cmake .
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312031629224.png" alt="image-20231203162939200" style="zoom:67%;" />

   ```shell
   # 使用此命令编译文件，此后直接执行编译后的文件即可
   make
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312031628777.png" alt="image-20231203162833749" style="zoom: 67%;" />

# 2. 语法关键字介绍

## 2.1 PROJECT关键字

可以用来指定工程的名字和支持的语言，默认支持所有语言

```cmake
# 指定了工程的名字，并且支持所有语言一建议
PROJECT (HELLO)
# 指定了工程的名字，并且支持语言是C++
PROJECT (HELLO CXX)
# 指定了工程的名字，并且支持语言是C和C++
PROJECT (HELLO C CXX)
```

该指定👆隐式定义了两个 CMAKE 变量

```xml
<!-- 本例中是HELLO_BINARY_DIR -->
projectname_BINARY_DIR
<!-- 本例中是HELLO_SOURCE_DIR -->
projectname_SOURCE_DIR
```

MESSAGE 关键字可以直接使用这两个变量，如果改了工程名，这两个变量名也会改变

## 2.2 SET关键字

用来为变量赋值

```cmake
# SRC_LIST变量就包含了main.cpp
SET(SRC_LIST main.cpp)
# SRC_LIST变量包含了main.cpp,t1.cpp以及t2.cpp
SET(SRC_LIST main.cpp t1.cpp t2.cpp)
```

## 2.3 MESSAGE关键字

向终端输出用户自定义信息，主要包含三种👇：

+ SEND_ERROR：产生错误，生成过程被跳过
+ STATUS：指定输出前缀的信息
+ FATAL_ERROR：立即终止所有 cmake 过程

## 2.4 ADD_EXECUTABLE关键字

生成可执行文件

```cmake
# 生成的可执行文件名是hello,源文件读取变量SRC_LIST中的内容
ADD_EXECUTABLE(hello ${SRC_LIST})
# 等价于👇
ADD_EXECUTABLE(hello main.cpp)
```

注意：工程名的 HELLO 和生成的可执行文件 hello 是没有任何关系的

## 2.5 ADD_SUBDIRECTORY关键字

```cmake
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

+ 这个指令用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位
+ EXCLUDE_FROM_ALL 函数是将指定目录从编译中排除，如程序中的 example

## 2.6 INSTALL关键字

负责将文件安装【类似拷贝】到指定位置

```cmake
# FILES：文件
# PROGRAMS：脚本
# DIRECTORY：目录
# DESTINATION写绝对路径，相对路径会定位到${CMAKE_INSTALL_PREFIX}/<DESTINATION定义的路径>
# CMAKE_INSTALL_PREFIX 默认是 /usr/local/
INSTALL(FILES 文件名1 文件名2 DESTINATION 安装路径)
INSTALL(PROGRAMS 脚本名1 脚本名2 DESTINATION 安装路径)
INSTALL(DIRECTORY 目录名 DESTINATION 安装路径)
# 可以在cmake的时候指定CMAKE_INSTALL_PREFIX变量的路径
cmake -D CMAKE_INSTALL_PREFIX=/usr 
```

## 2.7 ADD_LIBRARY关键字

为工程添加库

```cmake
# 将hello.h库应用在LIBHELLO_SRC所指向的文件中
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
```

+ hello︰就是正常的库名，生成的名字前面会加上 lib，最终产生的文件是 libhello.so
+ SHARED 表示动态库【静态库是 STATIC 】
+ ${LIBHELLO_SRC}︰源文件

## 2.8 SET_TARGET_PROPERTIES

用来设置输出的名称，对于动态库，还可以用来指定动态库版本和 API 版本

```cmake
# 对hello_static库重命名为hello
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")
```



# 3. 语法基本规则

+ 变量使用`${}`方式取值，但是在 IF 控制语句中是直接使用变量名
+ 指令(参数1 参数⒉...)，参数使用括弧括起，参数之间使用空格或分号分开。
+ 指令是大小写无关的，参数和变量是大小写相关的，推荐全部使用大写指令

**注意事项**

+ SET(SRC_LIST main.cpp) 可以写成 SET(SRC_LIST “main.cpp")，如果源文件名中含有空格，就必须要加双引号
+ ADD_EXECUTABLE(hello main) 后缀可以写，会自动匹配 .c 和 .cpp ，但是不推荐省略，因为可能同时存在同名文件

# 4. 内部构建和外部构建

+ 内部构建：将源文件以及编译产生的临时文件都存放在同一个文件夹下
+ **外部构建：源文件位置不变，但将编译产生的临时文件存放在`build`目录下，推荐此种方法**

**外部构建方式举例**

1. 在需要编译的源文件夹下创建`build`文件夹

   ```shell
   # 创建build文件夹
   mkdir build
   # 进入build文件夹
   cd build
   ```

2. 在 build 文件夹中执行编译语句，执行完毕后临时文件均存储在此文件夹下

   ```shell
   # ..指上一级目录，即构建的是上一级文件夹的内容
   cmake ..
   # 构建工程
   make
   ```

3. 注意外部构建的两个变量

   ```cmake
   # 👇指工程路径，即源文件所在的文件夹位置
   HELLO_SOURCE_DIR
   # 👇编译路径，即bulid文件夹位置
   HELLO_BINARY_DIR
   ```

# 5. 完善工程结构

## 5.1 添加src目录

为工程添加一个子目录 src，用来放置工程源代码

1. 编写需要进行关联的 CMakeLists.txt 文件

   ```cmake
   # 工程中的CMakeLists.txt👇
   # 工程名为HELLO
   PROJECT(HELLO)
   # 建立起与src中源码的关联
   # 将src子目录加入工程并指定编译输出(包含编译中间结果)路径为bin目录【build/bin，前提是进入了build目录】
   # 如果不进行bin目录的指定，那么编译结果(包括中间结果)都将存放在build/src目录
   ADD_SUBDIRECTORY(src bin)
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312041524317.png" alt="image-20231204152409229" style="zoom: 80%;" />

   ```cmake
   # src中的CMakeLists.txt
   ADD_EXECUTABLE(hello main.cpp)
   ```

2. 在 build 目录下完成工程构建与编译

   ```shell
   # 工程构建
   cmake .. 
   # 编译
   make
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312041545740.png" alt="image-20231204154536706" style="zoom:80%;" />

3. 在 bin 目录下成功生成编译后的文件

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312041606274.png" alt="image-20231204160603247" style="zoom:80%;" />

## 5.2 完善目录树结构

按照如下指示进行手动创建👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312041622723.png" alt="image-20231204162227690" style="zoom:67%;" />

编辑工程中的 CMakeLists.txt ，将部分内容安装至指定位置

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312041959805.png" alt="image-20231204195909745" style="zoom:80%;" />

```cmake
# 路径中若没有相应文件夹会自动创建
# 将COPYRIGHT和README两个文件安装到usr/local/share/doc/cmake/
INSTALL(FILES COPYRIGHT README DESTINATION share/doc/cmake/)
# 将runhello.sh脚本安装到usr/local/bin/
INSTALL(PROGRAMS runhello.sh DESTINATION bin)
# 将doc目录下的内容安装到usr/local/share/doc/cmake
INSTALL(DIRECTORY doc/ DESTINATION share/doc/cmake)
```

在 build 目录下执行以下操作👇

```shell
# 对build的上一级目录的所有内容构建工程
cmake ..
# 编译工程
make
# 执行安装
make install
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312042104517.png" alt="image-20231204210440469" style="zoom:67%;" />

# 6. 静态库与动态库

+ 静态库的扩展名一般为 ".a" 或 ".lib" 
+ 动态库的扩展名一般为 ".so" 或 ".dll” 
+ 静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行
+ 动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312051547176.png" alt="image-20231205154708081" style="zoom:80%;" />

**生成动态库样例**

`hello.h`

```c
#ifndef HELLO_H
#define HELLO_H
void HelloFunc();
#endif
```

`hello.cpp`

```c++
#include "hello.h"
#include<iostream>
void HelloFunc(){
    std::cout<<"Hello World!"<<std::endl;
}
```

工程中 `CMakeLists.txt`

```cmake
PROJECT(HELLO)
ADD_SUBDIRECTORY(lib bin)
```

`lib` 中 `CMakeLists.txt` 中的内容

```cmake
SET(LIBHELLO_SRC hello.cpp)
# hello是自定义库名，SHARED说明生成的是动态库，${LIBHELLO_SRC}指向库地址
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
```

在 build 中进行工程构建与编译后，成功获得动态库文件

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312051621506.png" alt="image-20231205162141473" style="zoom:67%;" />

若需要同时构建同名的静态库与动态库，则需要使用 SET_TARGET_PROPERTIES 手动修改名字

```cmake
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
# hello_static.a修改为hello.a
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")
ADD_LIBRARY(hello_shared SHARED ${LIBHELLO_SRC})
# hello_shared.so修改为hello.so
SET_TARGET_PROPERTIES(hello PROPERTIES OUTPUT_NAME "hello")
```

# 7. 为程序链接外部库

在 `6` 中我们手动创建了 hello.h 外部文件以及生成了动态库 libhello.so 。重新创建文件夹 cmake03 ，并搭建如下结构，目标是使用 cmake 完成 main.cpp 的编译与运行

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312061425044.png" alt="image-20231206142512967" style="zoom:67%;" />

**main.cpp**

```c++
// hello.h中声明了HelloFunc()函数
#include <hello.h>
int main(){
 // libhello.so中实现了HelloFunc()函数 
 HelloFunc();
}
```

**工程下的 CMakeLists.txt**

```cmake
PROJECT(HELLO)
# 工程下的bin目录用来存放源文件src编译出的内容
ADD_SUBDIRECTORY(src bin)
```

**src 下的 CMakeLists.txt**

```cmake
# 指定头文件搜索路径
INCLUDE_DIRECTORIES(/root/cmake02/lib)
# 编译main.cpp生成的可执行文件名为hello
ADD_EXECUTABLE(hello main.cpp)
# 将hello链接至外部库libhello.so
TARGET_LINK_LIBRARIES(hello /root/cmake02/build/bin/libhello.so)
```

构建并且编译后，hello 可以被顺利执行，说明外部库成功导入！

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202312061436546.png" alt="image-20231206143612502" style="zoom:67%;" />
