---
title: FunASR模型部署笔记
date: 2025-04-04 17:19:20
excerpt: FunASR是一个功能强大、非常实用的语音识别工具。简单来说，它是由阿里巴巴通义实验室开发并开源的一个基础语音识别工具包。它的目标是在学术研究和工业应用之间架起一座桥梁，让研究人员和开发者都能更方便地进行语音识别模型的研发和应用。
tags:
  - 语音识别
categories:
  - [演示项目部署,语音识别]
---

`FunASR是一个功能强大、非常实用的语音识别工具。简单来说，它是由阿里巴巴通义实验室开发并开源的一个基础语音识别工具包。它的目标是在学术研究和工业应用之间架起一座桥梁，让研究人员和开发者都能更方便地进行语音识别模型的研发和应用。`

# 1. 前置准备

1. 确保Windows为专业版，否则无 Hyper-V 选项且做如下设置

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141603162.png" alt="image-20241114160341845" style="zoom: 33%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141604681.png" alt="image-20241114160435595" style="zoom:33%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141605307.png" alt="image-20241114160519278" style="zoom:50%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141605681.png" alt="image-20241114160550647" style="zoom:50%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141606901.png" alt="image-20241114160614875" style="zoom:50%;" />

2. docker安装（第一次启动时最好使用管理员身份！）

   > [docker下载地址](https://docs.docker.com/get-started/get-docker/)


# 2. 项目启动准备

1. 启动docker服务【打开docker Desktop即可】

2. 拉取镜像资源

   ```shell
   docker pull registry.cn-hangzhou.aliyuncs.com/funasr_repo/funasr:funasr-runtime-sdk-online-cpu-0.1.9
   ```

3. 启动镜像

   ```shell
   # 将本地文件系统中的E:/Voice/FunASR/model目录挂载到容器内的 /workspace/models 目录，实现本地文件与容器内部的文件共享
   docker run --name funasr_sample -p 10095:10095 -it --privileged=true -v F:/Voice/FunASR/model:/workspace/models registry.cn-hangzhou.aliyuncs.com/funasr_repo/funasr:funasr-runtime-sdk-online-cpu-0.1.9 
   ```

   此时会进入到当前镜像的 /workspace 目录下

4. 启动镜像中的服务器

   ```shell
   # 切换目录
   cd FunASR/runtime
   # 按照指定参数运行服务器脚本
   nohup bash run_server_2pass.sh \
     --certfile 0  \
     --download-model-dir /workspace/models \
     --vad-dir damo/speech_fsmn_vad_zh-cn-16k-common-onnx \
     --model-dir damo/speech_paraformer-large-vad-punc_asr_nat-zh-cn-16k-common-vocab8404-onnx  \
     --online-model-dir damo/speech_paraformer-large_asr_nat-zh-cn-16k-common-vocab8404-online-onnx  \
     --punc-dir damo/punc_ct-transformer_zh-cn-common-vad_realtime-vocab272727-onnx \
     --itn-dir thuduj12/fst_itn_zh > log.txt 2>&1 &
    
   # 如果您想关闭ssl，增加参数：--certfile 0
   # 如果您想使用时间戳或者nn热词模型进行部署，请设置--model-dir为对应模型：
   #   damo/speech_paraformer-large-vad-punc_asr_nat-zh-cn-16k-common-vocab8404-onnx（时间戳）
   #   damo/speech_paraformer-large-contextual_asr_nat-zh-cn-16k-common-vocab8404-onnx（nn热词）
   # 如果您想在服务端加载热词，请在宿主机文件./funasr-runtime-resources/models/hotwords.txt配置热词（docker映射地址为/workspace/models/hotwords.txt）:
   #   每行一个热词，格式(热词 权重)：阿里巴巴 20（注：热词理论上无限制，但为了兼顾性能和效果，建议热词长度不超过10，个数不超过1k，权重1~100）
   ```

   各参数介绍：

   + --download-model-dir 模型下载地址，通过设置model ID从Modelscope下载模型
   + --model-dir  modelscope model ID 或者 本地模型路径
   + --online-model-dir  modelscope model ID 或者 本地模型路径
   + --vad-dir  modelscope model ID 或者 本地模型路径
   + --punc-dir  modelscope model ID 或者 本地模型路径
   + --lm-dir modelscope model ID 或者 本地模型路径
   + --itn-dir modelscope model ID 或者 本地模型路径
   + --port  服务端监听的端口号，默认为 10095
   + --decoder-thread-num  服务端线程池个数(支持的最大并发路数)，脚本会根据服务器线程数自动配置decoder-thread-num、io-thread-num
   + --io-thread-num  服务端启动的IO线程数
   + --model-thread-num  每路识别的内部线程数(控制ONNX模型的并行)，默认为 1，其中建议 decoder-thread-num*model-thread-num 等于总线程数
   + --certfile  ssl的证书文件，默认为：../../../ssl_key/server.crt，如果需要关闭ssl，参数设置为0
   + --keyfile   ssl的密钥文件，默认为：../../../ssl_key/server.key
   + --hotword   热词文件路径，每行一个热词，格式：热词 权重(例如:阿里巴巴 20)，如果客户端提供热词，则与客户端提供的热词合并一起使用，服务端热词全局生效，客户端热词只针对对应客户端生效。

5. 监控服务端日志

   ```shell
   tail -f /workspace/FunASR/runtime/log.txt
   ```

至此服务器成功启动，启动成功后界面不能关闭，后续操作需要使用CMD另开一个终端

# 3. 导入样例

1. 查询容器ID

   ```shell
   docker ps
   ```

2. 切换至目标容器终端

   ```shell
   docker exec -it 自己容器ID /bin/bash
   ```

3. 下载示例

   ```shell
   # 下载示例
   wget https://isv-data.oss-cn-hangzhou.aliyuncs.com/ics/MaaS/ASR/sample/funasr_samples.tar.gz
   ```

4. 解压示例至指定位置

   ```shell
   # 在 /workspace/models 目录下创建一个目录 funasr_samples
   mkdir /workspace/models/funasr_samples
   # 解压文件到 /workspace/models/funasr_samples 目录下
   tar -xzf funasr_samples.tar.gz -C /workspace/models/funasr_samples
   ```

   由于在 2.3 步骤中已经将本地文件系统挂载到容器/workspace/models/，因此对此目录的此操作都会与本地系统相关联

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141703960.png" alt="image-20241114170313908" style="zoom:50%;" />

# 4. 测试样例

1. 打开 index.html 界面

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141704113.png" alt="image-20241114170437074" style="zoom:50%;" />

2. 修改asr服务器地址为 ws://127.0.0.1:10095/

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141705966.png" alt="image-20241114170559936" style="zoom:50%;" />

   ```shell
   # 终端切换至当前python目录下
   cd E:\Voice\FunASR\model\funasr_samples\samples\python
   # 使用python完成与服务器的连接，过程提示缺少什么模块就用pip安装什么模块
   python funasr_wss_client.py --host “127.0.0.1” --port 10095 --mode 2pass --ssl 0
   ```

3. 根据需求去探索各个功能即可<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141707288.png" alt="image-20241114170746236" style="zoom:67%;" />

   #### ① 模型模式

   + **2pass**: （默认）实时语音识别，并且句尾采用离线模型进行纠错（**准确度更高**）
   + online: 实时语音识别
   + offline: 一句话识别

   #### ② 逆文本标准化（ITN）

   + **是**: （默认） 表示进行逆文本标准化
   + 否: 表示不进行逆文本标准化

   **逆文本标准化（Inverse Text Normalization, ITN）** 是语音识别和自然语言处理领域的一种技术，用于将自动语音识别（ASR）系统生成的文本转化为更自然和可读的格式。这是对文本标准化（Text Normalization, TN）的逆过程。

   **逆文本标准化的目的**

   当语音被转换为文本时，ASR 系统通常会输出一种更标准化的文本形式。例如：

   + 数字 “123” 可能会被识别为 “一二三” 或 “123”。
   + 日期 “2024年7月25日” 可能会被识别为 “二零二四年七月二十五日” 或 “2024年7月25日”。

   **这些输出虽然标准化，但不一定是人们习惯书写或阅读的格式。** ITN 的作用是将这些标准化的文本转换回更自然、更符合书面习惯的形式。例如：

   + “一二三” 转换为 “123”
   + “二零二四年七月二十五日” 转换为 “2024年7月25日”
   + “五千七百六十三” 转换为 “5763”

# 5. 关闭服务器

由于服务器开启会耗费资源，因此不需要使用时需要关闭，直接在 DockerDesktop 操作即可

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202411141727393.png" alt="image-20241114172732296" style="zoom:67%;" />
