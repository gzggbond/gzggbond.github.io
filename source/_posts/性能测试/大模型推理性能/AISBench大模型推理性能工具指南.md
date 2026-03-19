---
title: AISBench大模型推理性能工具指南
date: 2026-03-19 9:27:50
excerpt: AISBench是《GBT/45087-2024人工智能 服务器系统性能测试方法》标准中人工智能服务器系统性能测试工具的典型实现，适用于人工智能服务器、人工智能服务器集群或人工智能计算中心的性能测试，能兼容主流人工智能加速器（如 CPU、GPU 或 NPU等)和深度学习框架
tags: 
  - 大模型推理性能测试
categories:
  - [性能测试,大模型推理性能测试]
---

# 1. AISBench简介

AISBench是`《GBT/45087-2024人工智能 服务器系统性能测试方法》`标准中人工智能服务器系统性能测试工具的典型实现，适用于人工智能服务器、人工智能服务器集群或人工智能计算中心的性能测试，能兼容主流人工智能加速器（如 CPU、GPU 或 NPU等)和深度学习框架

AISBench Benchmark是基于 [OpenCompass](https://github.com/open-compass/opencompass) 构建的模型评测工具，兼容 OpenCompass 的配置体系、数据集结构与模型后端实现，并在此基础上扩展了对服务化模型（VLLM）的支持能力。

当前，AISBench 支持两大类推理任务的评测场景：

🚀 [服务化性能测评指南](https://ais-bench-benchmark-rf.readthedocs.io/zh-cn/latest/base_tutorials/scenes_intro/home.html#id5)：支持对服务化模型的延迟与吞吐率评估，并可进行压测场景下的极限性能测试，支持稳态性能评测和真实业务流量模拟。

🔍 [精度测评](https://ais-bench-benchmark-rf.readthedocs.io/zh-cn/latest/base_tutorials/scenes_intro/home.html#id2)：支持对服务化模型和本地模型在各类问答、推理基准数据集上的精度验证，覆盖文本、多模态等多种场景。

**环境安装部署**

```shell
# 克隆项目
git clone https://github.com/AISBench/benchmark.git
cd benchmark
# 在原先./requirements/runtime.txt基础上加上vllm，openai>=2.0.0与setuptools==67.7.2
# 安装依赖
pip install -r ./requirements/runtime.txt
# 源码安装安装ais_bench
pip3 install -e ./
# 如需卸载，则使用如下命令
# pip uninstall ais_bench_benchmark
```

# 2. 服务化性能测评指南

AISBench Benchmark 提供服务化性能测评能力。针对流式推理场景，通过精确记录每条请求的发送时间、各阶段返回时间及响应内容，系统地评估模型服务在实际部署环境中的响应延迟（如 TTFT、Token间延迟）、吞吐能力（如 QPS、TPUT）、并发处理能力等关键性能指标。

用户可通过配置服务化后端参数，灵活控制请求内容、请求间隔、并发数量等，适配不同评测场景（如低并发延迟敏感型、高并发吞吐优先型等）。测评支持自动化执行并输出结构化结果，便于横向对比不同模型、部署方案、硬件配置下的服务性能差异。

## 2.1 快速入门

**准备工作**

1. 本地准备好需要测评的大模型文件

   > 通过[魔塔社区](https://www.modelscope.cn/models)或者[Hugging Face](https://huggingface.co/models)等平台获得开源大模型文件

2. 终端通过VLLM开启服务化推理后端（默认端口8000）

   ```shell
   # 开启推理服务
   vllm serve [大模型文件本地路径]
   # vllm serve /home/user/code/bigmodel/opencompass-main/downloads/models/Qwen2.5-1.5B-Instruct
   ```

**使用如下命令完成性能测评**

```shell
ais_bench --models vllm_api_stream_chat --datasets demo_gsm8k_gen_4_shot_cot_chat_prompt -m perf --debug
```

+ `--models`: 使用`vllm_api_stream_chat`模型任务，需要准备支持`v1/chat/completions`子服务的推理服务

  > 此处对应的文件为`./ais_bench/benchmark/configs/models/vllm_api/vllm_api_stream_chat.py`，相关参数含义如[服务化推理后端配置](https://ais-bench-benchmark.readthedocs.io/zh-cn/latest/base_tutorials/all_params/models.html#id2)所示

+ `--datasets`: 使用`demo_gsm8k_gen_4_shot_cot_chat_prompt`数据集任务，需要准备`gsm8k`数据集

  > 此处对应的文件为`./ais_bench/benchmark/configs/datasets/gsm8k/gsm8k_gen_4_shot_cot_chat_prompt.py`，相关参数含义如[数据集准备指南](https://ais-bench-benchmark.readthedocs.io/zh-cn/latest/base_tutorials/all_params/datasets.html)所示，path使用相对路径时是相对于源码根路径

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603091511029.png" alt="image-20260309151117850" style="zoom: 67%;" />

执行后的结果存在于`outputs/default/`文件夹下，各参数说明如[性能测评结果说明](https://ais-bench-benchmark.readthedocs.io/zh-cn/latest/base_tutorials/results_intro/performance_metric.html)所示

## 2.2 数据集配置命名

AISBench Benchmark 开源数据集配置按照数据集名称保存在 `configs/datasets`目录下，在各个数据集对应的文件夹下存在多个数据集配置，文件结构如下所示：

```
ais_bench/benchmark/configs/datasets
├── agieval
├── aime2024
├── ARC_c
├── ...
├── gsm8k  # 数据集
│   ├── gsm8k_gen.py  # 不同版本数据集配置文件
│   ├── gsm8k_gen_0_shot_cot_str_perf.py
│   ├── gsm8k_gen_0_shot_cot_chat_prompt.py
│   ├── gsm8k_gen_0_shot_cot_str.py
│   ├── gsm8k_gen_4_shot_cot_str.py
│   ├── gsm8k_gen_4_shot_cot_chat_prompt.py
│   └── README.md
├── ...
├── vocalsound
├── winogrande
└── Xsum
```

开源数据集配置名称由以下命名方式构成 `{数据集名称}_{评测方式}_{shot数目}_shot_{逻辑链规则}_{请求类型}_{任务类别}.py`，通过规范的命名我们可以很快得知当前的配置是否满足我们的需求，从而做出相应选择，如下是一个例子：

**gsm8k/gsm8k_gen_0_shot_cot_chat_prompt.py**

1. **`gsm8k`**：数据集名称。表示这个配置文件是基于 **GSM8K**（小学数学题数据集）来设计的。

2. **`gen`**：生成式评测，AISBench目前只支持 `gen`

   > 让模型直接生成答案，然后与标准答案对比。另一种常用的大模型评估方式为PPL（困惑度）评测不需要模型生成文本，只需要评估已有选项，适用于有限选项的分类任务，其通过比较不同选项的困惑度/似然度来选择最优答案。

3. **`0_shot`**：提示中给出的样例数量。`0_shot` 表示**不给任何例子**，直接问问题。如果是 `5_shot` 就会在问题前先给5个问答示例。

   > 通过问答示例可以令大模型更好的理解用户需求

4. **`cot`**：逻辑链规则。`cot` 表示请求中会包含 **“思维链”提示**（比如“请逐步推理”），引导模型输出推理过程。如果这一项缺失，就没有这个提示

5. **`chat_prompt`**：对话模式请求类型。`chat_prompt` 表示**使用对话模板**组织输入（像聊天一样，包含角色标识如 `user`、`assistant`）。

   > 对话模式请求类型通过前置prompt令大模型更清楚地知道自己的身份、任务的背景以及对话的上下文，从而生成更符合预期的答案；与之相对的另一种请求类型为纯文本请求类型`str`，其只把问题本身发送给大模型从而得到答案

6. **任务类别**：这里**没有指定**，所以**默认为精度测试**

   > 评估模型回答得准不准，与之相对应的为性能模式`perf`，其聚焦于模型回答的快不快

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603101432490.png" alt="image-20260310143228389" style="zoom:50%;" />

## 2.3 随机合成数据集使用指南

```shell
# 根据synthetic_gen配置生成随机合成数据集并按配置要求喂给model_api_file
ais_bench --models {model_api_file} --datasets synthetic_gen --mode perf --debug
```

> 该功能旨在不提供真实数据集，而考虑使用构造随机的合成数据集的场景，用于大语言模型推理性能基准测试

在 `ais_bench/datasets/synthetic/synthetic_config.py` 配置文件中配置所需参数。

+ **`string`模式**: 生成随机长度字符串（模拟真实输入），当`MinValue = MaxValue`时生成固定长度序列

  > 该模式下的输入长度指代输入的string的长度，而非token的长度
  >
  > 使用时需要在model api配置文件（vllm_api_stream_chat.py）中是否正确配置`generation_kwargs`中的`ignore_eos`参数（可以使得服务化处理输出时忽略终止符直至输出长度达到预设值）为`True`

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603101554173.png" alt="image-20260310155215678" style="zoom:67%;" />

  + **Uniform均匀分布**：输入/输出长度在区间内等概率分布

    > 适用于压力测试的基线场景

    ```python
    synthetic_config = {
        "Type": "string",
        "RequestCount": 1000,
        "StringConfig": {
            "Input": {
                "Method": "uniform",
                "Params": {"MinValue": 50, "MaxValue": 500}  # 输入长度50-500
            },
            "Output": {
                "Method": "uniform",
                "Params": {"MinValue": 20, "MaxValue": 200}  # 输出长度20-200
            }
        }
    }
    ```

  + **Gaussian高斯分布**：约95%的输入长度在[236,276]范围内（μ±2σ）

    > 模拟真实场景中的平均请求长度

    ```python
    synthetic_config = {
        "Type": "string",
        "RequestCount": 800,
        "StringConfig": {
            "Input": {
                "Method": "gaussian",
                "Params": {
                    "Mean": 256,       # 中心值256
                    "Var": 100,        # 标准差10（Var是方差，开根号为标准差）
                    "MinValue": 64,    # 实际范围64-512
                    "MaxValue": 512
                }
           
            },
            "Output": {
                "Method": "gaussian",
                "Params": {
                    "Mean": 128,
                    "Var": 50,
                    "MinValue": 32,
                    "MaxValue": 256
                }
            }
        }
    }
    ```

  + **Zipf齐夫分布**：模拟真实场景中的请求长尾分布，当Alpha=1.5时，约20%的请求占60%的计算量

    > 大模型推理计算量只和token长度相关，若20%的请求占60%的计算量，则说明这20%的请求长度比较长，生成长尾分布数据（如1%的请求占50%的计算量）

    ```python
    synthetic_config = {
        "Type": "string",
        "RequestCount": 1200,
        "StringConfig": {
            "Input": {
                "Method": "zipf",
                "Params": {
                    "Alpha": 1.5,      # 强长尾效应
                    "MinValue": 10,    # 输入长度范围10-1000
                    "MaxValue": 1000
                }
            },
            "Output": {
                "Method": "zipf",
                "Params": {
                    "Alpha": 2.0,     # 较平缓分布
                    "MinValue": 5,
                    "MaxValue": 500
                }
            }
        }
    }
    ```

  + **混合分布配置**：输入和输出可以是上述的不同分布组合

+ **`tokenid`模式**: 生成随机token id序列（直接输入编码后的Token）

  > 发送时会将每条由模型权重词表范围下随机生成的token所组成的、指定长度的prompt重新decode为字符串格式后再发送给服务化，由于每个模型的词表映射可能出现多对一、一对多等情况，故可能出现波动

  + **长文本压力测试**

    ```python
    synthetic_config = {
        "Type": "tokenid",
        "RequestCount": 1000,
        "TokenIdConfig": {
            "RequestSize": 2048   # 单请求2048个token
        }
    }
    ```

  + **短文本性能测试**

    ```python
    synthetic_config = {
        "Type": "tokenid",
        "RequestCount": 5000,
        "TokenIdConfig": {
            "RequestSize": 128    # 短文本处理场景
        }
    }
    ```

## 2.4 大模型推理吞吐率测试案例

**Qwen2.5-1.5B-Instruct在单机4卡（RTX 5880 Ada）环境、输入输出均为4096长度下的吞吐率**

1. 开启VLLM服务端

   ```shell
   # 启动服务端并将模型切分到四张卡上运行
   vllm serve /path/to/Qwen2.5-1.5B-Instruct --tensor-parallel-size 4 
   ```

2. `vllm_api_stream_chat.py`修改

   > batch_size设置建议从小开始往上递增，直至出现吞吐率的转折点，则说明到达峰值

   ```python
   from ais_bench.benchmark.models import VLLMCustomAPIChatStream
   
   models = [
       dict(
           attr="service",
           type=VLLMCustomAPIChatStream,
           abbr='vllm-api-stream-chat',
           path="/path/to/Qwen2.5-1.5B-Instruct",  # 模型权重路径，用于分词器
           model="Qwen2.5-1.5B-Instruct",                 # 服务端模型名称
           request_rate = 0,                     # 0表示一次性发完所有请求
           retry = 2,
           host_ip = "localhost",                 # 服务IP
           host_port = 8000,                      # 服务端口
           max_out_len = 4096,                    # 关键：输出长度设为4096
           batch_size = 32,                        # 并发数，需根据显存调整
           generation_kwargs = dict(
               temperature = 0.5,
               top_k = 10,
               top_p = 0.95,
               seed = None,
               repetition_penalty = 1.03,
               ignore_eos = True,                  # 关键：强制输出达到max_out_len
           )
       )
   ]
   ```

3. `synthetic_gen.py`配置`tokenid`模式精准匹配token长度

   ```python
   synthetic_config = {
       "Type": "tokenid",                    # 使用tokenid模式更精确控制长度
       "RequestCount": 32,                    # 请求条数，建议与batch_size一致
       "TokenIdConfig": {
           "RequestSize": 4096                 # 单请求固定4096个token
       }
   }
   ```

4. 执行在随机数据集上的性能评估

   > 可通过监听系统资源判断是否满载运行

   ```shell
   ais_bench --models vllm_api_stream_chat --datasets synthetic_gen --mode perf --debug
   ```

+ **P75 / P90 / P99**：请求时延的第 75、90、99百分位的性能表现。
+ **E2EL（End-to-End Latency）**：单个请求从发送到接收全部响应的总时延。
+ **TTFT（Time To First Token）**：首个 Token 返回的时延。
+ **TPOT（Time Per Output Token）**：输出阶段每个 Token 的平均生成时延（不含首个 Token）。
+ **ITL（Inter-token Latency）**：相邻 Token 间的平均间隔时延（不含首个 Token）。
+ **InputTokens**：请求的输入 Token 数量。
+ **OutputTokens**：请求生成的输出 Token 数量。
+ **OutputTokenThroughput**：输出 Token 的吞吐率（Token/s）。
+ **Tokenizer**：Tokenizer 编码耗时。
+ **Detokenizer**：Detokenizer 解码耗时。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603101846437.png" alt="image-20260310184647393" style="zoom:67%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603101848141.png" alt="image-20260310184809079" style="zoom: 67%;" />

```json
// 执行结果
{
    "Benchmark Duration": {
        "total": "40152.6953 ms"
    },
    "Total Requests": {
        "total": 32
    },
    "Failed Requests": {
        "total": 0
    },
    "Success Requests": {
        "total": 32
    },
    "Concurrency": {
        "total": 31.8314
    },
    "Max Concurrency": {
        "total": 32
    },
    "Request Throughput": {
        "total": "0.797 req/s"
    },
    "Total Input Tokens": {
        "total": 139037
    },
    "Prefill Token Throughput": {
        "total": "2576.3843 token/s"
    },
    "Total generated tokens": {
        "total": 131072
    },
    "Input Token Throughput": {
        "total": "3462.7065 token/s"
    },
    "Output Token Throughput": {
        "total": "3264.3388 token/s"
    },
    "Total Token Throughput": {
        "total": "6727.0453 token/s"
    }
}
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202603101905316.png" alt="image-20260310190506239"  />

在实际测试中，应当通过如下策略逐步扩大batch_size，直至来到吞吐率的拐点

+ **前期（1→8）**：步长取 **1 或 2**。小步快跑，精细观察初期性能变化
+ **中期（8→32）**：步长取 **4 或 8**。此时吞吐率通常线性增长，可以迈大一点步子
+ **后期（32→128+）**：步长取 **16 或 32**。接近拐点时更要小心，避免一步跨过最佳点

当出现以下任一情况时，上一个 `batch_size` 就是最优值

> 可将每次batch下的相关情况进行记录

+ 吞吐率不再随 `batch_size` 增加而增加或下降
+ 延迟（尤其是P99延迟）出现数倍的跳升
+ GPU显存即将用尽（建议留10%余量）















