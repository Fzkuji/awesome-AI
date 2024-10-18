# Adding Memory Capability to Large Language Models


## Motivations

现有LLM模型计算量很大，层数很多，计算效率低，因此难以应用到需要快速响应的场景中。以下是一些尝试性研究和解决方案

### Transformer basic researches

[为什么现在的LLM都是Decoder only的架构？](https://www.zhihu.com/question/588325646)

[大模型推理性能优化之KV Cache解读](https://zhuanlan.zhihu.com/p/630832593)

[Unified Language Model Pre-training for Natural Language Understanding and Generation](https://arxiv.org/abs/1905.03197)比较早期的研究，主要关注了self-attention的设计，比如是单向还是双向，mask的设置等等。

[Not all Layers of LLMs are Necessary during Inference](https://arxiv.org/abs/2403.02181)指出，在推理过程中，并非所有 LLM 层都是必需的，他们考虑了early-exit的方式，使用前几层的transformer直接预测结果，而不是等整个LLM计算结束。

[Rethinking the Role of Scale for In-Context Learning: An Interpretability-based Case Study at 66 Billion Scale](https://arxiv.org/abs/2212.09095)这篇论文讨论了LLM 是否真的需要这么多层，他们发现：70% of attention heads and 20% of feed-forward networks can be excised with minimal effect on in-context learning suggests that large language models are undertrained.

[Single-Layer Vision Transformers for More Accurate Early Exits with Less Overhead](https://arxiv.org/abs/2105.09121)这篇论文是在CV领域测试预测的早停（他们叫early exiting），他们发现模型效果同样优秀，并且计算量减少。

[Wide Attention Is The Way Forward For Transformers?](https://arxiv.org/abs/2210.00640)这个研究也强调了，增加transformer的宽度（更宽、更浅的模型）是理想的方案。

[Are Transformers with One Layer Self-Attention Using Low-Rank Weight Matrices Universal Approximators?](https://arxiv.org/abs/2307.14023)这篇论文论证了单层的transformer layer（包含two feed-forward neural networks）就可以拟合任意连续的permutation equivariant方程。说明了单层模型的有效性。

[What's Hidden in a One-layer Randomly Weighted Transformer?](https://arxiv.org/abs/2109.03939)这个论文提供了一种奇怪的视角，即在单层随机权重的Transformer模型中，即使不进行传统意义上的模型训练（即不通过梯度下降等方法调整网络权重），也能够发现或“挖掘”出具有竞争性能的子网络。这项研究表明，在某些情况下，高效的模型或模型的子集可能已经在随机初始化的网络中“隐藏”着，只需要通过合适的方法来发现它们。
- 感觉噱头比较大，没太大意义

### Re



ccurent transformer

另一个比较冷门的研究方向就是recurrent transformer，这类研究旨在给transformer添加一个隐层的变量，这个变量可以随着输入进行改变。

[R-Transformer: Recurrent Neural Network Enhanced Transformer](https://arxiv.org/abs/1907.05572)这篇论文就尝试将位置编码改成RNN，以此来提升LLM性能。

[Recurrent Memory Transformer](https://arxiv.org/abs/2207.06881)中
- Decoder-only 模式：Memory以Special Token的形式作为Input，拼在最左边和最右边。上一个segment的最右边的output部分作为下一个segment最左边和最右边的输入
- Encoder模式：encoder模式只需要在每个segment的最前面加memory token

[MART: Memory-Augmented Recurrent Transformer for Coherent Video Paragraph Captioning](https://arxiv.org/abs/2005.05402)文章的模型通过一个记忆模块来增强传统的Transformer架构。这个记忆模块从视频片段和句子历史中生成一个高度总结的记忆状态，以帮助更好地预测下一句（关于共指和重复方面的预测），从而鼓励生成连贯的段落。

[Scaling Transformer to 1M tokens and beyond with RMT](https://arxiv.org/abs/2304.11062)将文本分成N个桶（例如512长度一个桶），执行第i个桶时，接受第i-1个桶的mem和本次桶的文本，输出第i个桶的hidden output和mem output。
- 这篇文章2024年2月更新了内容，包含了一些较新的工作，可以**重点**看看


### Memory

#### [Memory transformer](../../../2.%20Approaches/Artificial%20neural%20network/Transformer/Memory%20transformer.md)


#### Transformer alternatives

[Mamba: Linear-Time Sequence Modeling with Selective State Spaces](https://arxiv.org/abs/2312.00752)这篇论文以及之后的系列论文都借鉴了RNN的思路，为模型添加隐层状态，用训练时间换空间。
- 笔记：[Mamba](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Mamba.md)
- 最近又出了一个[Jamba](https://www.ai21.com/blog/announcing-jamba)

[[RWKV]]也是一个类似的基于RNN的模型


### Reinforcement learning

[LLM与RL结合的Agent相关论文解读](https://zhuanlan.zhihu.com/p/672719151)，与之相关的是一篇综述[A Survey on Large Language Model based Autonomous Agents](https://arxiv.org/abs/2308.11432)以及对应的GitHub项目[LLM-Agent-Survey](https://github.com/Paitesanshi/LLM-Agent-Survey)

[Guiding Pretraining in Reinforcement Learning with Large Language Models](https://arxiv.org/abs/2302.06692)

[Learning to Watermark LLM-generated Text via Reinforcement Learning](https://arxiv.org/abs/2403.10553)这篇仍然是让LLM生成score，而不是有label的情况


### Multi tokens

处理多个tokens的方法，比如: 

[Infini-gram: Scaling Unbounded n-gram Language Models to a Trillion Tokens](https://arxiv.org/pdf/2401.17377)

一次预测多个tokens
[Better & Faster Large Language Models via Multi-token Prediction](https://arxiv.org/pdf/2404.19737)

逐渐学习更多内容的增量学习
[Towards Incremental Learning in Large Language Models: A Critical Review](https://arxiv.org/pdf/2404.18311)

## Related directions

长度外推（[Length extrapolation](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/Length%20extrapolation.md)）



## Architecture


### Recurrent Short-term Memory

在注意力窗口内，使用额外的全局注意力tokens保留序列完整信息，用于下一个窗口的计算。


### Cached Long-term Memory

进行一种类似于进制的kv cache保存机制，注意力窗口的全局注意力tokens将会滚动保存n个批次的kv cache，当超过n时将会滚动删除旧的cache，同时在高层记忆中保存一个新的cache。同理，高层记忆也会进行








## Preparations


### Train an LLM

[Let's build GPT: from scratch, in code, spelled out.](https://www.youtube.com/watch?v=kCc8FmEb1nY)这个视频介绍了从头训练一个LLM

测试一个LLM的性能可以用General Language Understanding Evaluation (GLUE)这个benchmark

可以找一些toy task去测试新模型

### Datasets

之前的工作似乎有使用Book 3数据集、Stack数据集和自己做的benchmark数据集（by Mikhail Burtsev）

#### Huggingface系列

使用datasets加载本地的数据集：
https://towardsdatascience.com/how-to-turn-your-local-zip-data-into-a-huggingface-dataset-43f754c68f82

没有默认划分的数据集可以自行划分：
https://huggingface.co/docs/datasets/v1.11.0/splits.html


#### Books3

这个数据集是[EleutherAI](https://www.eleuther.ai/)公司出的整个数据集是[EleutherAI/the-pile](https://github.com/EleutherAI/the-pile)，但是其中有很多版权问题，尤其是其中的Books3部分，也就是很多数据没有版权。

数据集十分不好找，因为版权问题而被下架了，后来是用磁力链接下载的，或者也可以去[Internet Archive](https://archive.org/)这个无敌的网站去找。

> [!success] 
> 2024.4 [这个](magnet:?xt=urn:btih:0d366035664fdf51cfbe9f733953ba325776e667)磁力链还有效

##### [The Pile](https://arxiv.org/abs/2101.00027)

论文：The Pile: An 800GB Dataset of Diverse Text for Language Modeling

整个的The Pile数据集都不可访问了，包括huggingface。

官网留下的文档说：
The Pile is old news, check out more recent datasets like; 
https://huggingface.co/datasets/bigcode/the-stack-v2

##### The Stack v2

这个是他们推荐的最新的数据集，
https://huggingface.co/datasets/bigcode/the-stack-v2


#### [PG-19](https://github.com/google-deepmind/pg19)

这个是Books3青春版，比Books3平均长度少一半（Books3是10万tokens每篇，PG-19是5万字）

Huggingface网站: https://huggingface.co/datasets/pg19

#### Others

- Open-Orca/OpenOrca
- tiiuae/falcon-refinedweb
- togethercomputer/Long-Data-Collections
- math-ai/StackMathQA
- irc_disentangle




## Ideas


### Memory size

可以试试实验关于一个token多少维度，才能保证一个较高的记忆力

相关论文：[Making Large Language Models A Better Foundation For Dense Retrieval](https://arxiv.org/abs/2312.15503)

### Memory length

对于原始Transformer（inference）：
- 首次输入文本复杂度和$n^2 \times d$正相关，其中$n$为序列长度、$d$为单个token的维度
- 后续文本预测复杂度和$n \times d$正相关

对于带memory的transformer：
- 永久记忆token数为$i$，$i \ll n$
- 临时记忆token数为$j$，$j \ll n$
- 首次输入文本复杂度和$(\frac{n}{i+j})^2 \times d$正相关，其中$n$为序列长度、$d$为单个token的维度
	- 从$n^2$到$(\frac{n}{i+j})^2$
- 后续文本预测复杂度和$(\frac{n}{i+j}+i+j) \times (1+i+j) \times d$正相关
	- 计算量基本不变

假设n=1000000，i=10，j=1000：
- 原始Transformer
	- 首次输入文本复杂度：1,000,000,000,000
	- 后续文本预测复杂度：1,000,000
- 带memory的Transformer
	- 首次输入文本复杂度：980,296
	- 后续文本预测复杂度：2,022,100

可以看出，通过引入永久和临时记忆机制，带memory的Transformer在首次输入文本时的时间复杂度大大减少，从1,000,000,000,000减少到980,296。这说明，在处理极长序列时，引入记忆机制能显著提高效率。

然而，对于后续文本预测的复杂度，带memory的Transformer略有增加，从1,000,000增加到2,022,100。这表明在进行连续预测时，引入记忆机制会略微增加单次预测的复杂度，但鉴于首次输入的复杂度大幅降低，这种权衡在处理大规模数据时仍然是有利的。

### Memory drop






## Experiment

文件路径：

```
/data/local/amlworker1-0/fzkuji/memoGPT
```

设置数据集存储位置：

```shell
export HF_DATASETS_CACHE="/root/autodl-tmp/mac.Zichuans-MacBook-Pro.local/memoGPT/.cache/huggingface/dataset"
```

设置Huggingface Cache位置：

```shell
export HF_HOME="/root/autodl-tmp/mac.Zichuans-MacBook-Pro.local/memoGPT/.cache/huggingface"
```

```shell
export HF_HOME="/root/autodl-tmp/.cache/huggingface"
```

AutoDL加速网络：

```shell
source /etc/network_turbo
```

```python
import subprocess
import os

result = subprocess.run('bash -c "source /etc/network_turbo && env | grep proxy"', shell=True, capture_output=True, text=True)
output = result.stdout
for line in output.splitlines():
    if '=' in line:
        var, value = line.split('=', 1)
        os.environ[var] = value
```

运行训练：

```shell
python train.py configs/finetune_gpt2.py
```

```shell
nohup python train.py > output.log 2>&1 &
```

```shell
nohup python train.py configs/finetune_gpt2.py > output.log 2>&1 &
```

```shell
CUDA_VISIBLE_DEVICES=0,1,2,3 torchrun --nproc_per_node=4 train.py configs/finetune_gpt2.py
```

```shell
nohup torchrun --nproc_per_node=4 train.py > output.log 2>&1 &
```

### Evaluation

https://github.com/huggingface/blog/blob/main/zh/open-llm-leaderboard-mmlu.md


### Overall Performance

使用Qwen2-1.5B模型对比GPT-2模型，同时在


| Datasets              | GPT-2-XL | MemoGPT-0.5B-Instruct (Origin) | MemoGPT-0.5B-Instruct (Pretraining OpenWebText) | MemoGPT-0.5B-Instruct (SFT rag-dataset-12000) | MemoGPT-1.5B | Qwen2-1.5B |
| --------------------- | -------- | ------------------------------ | ----------------------------------------------- | --------------------------------------------- | ------------ | ---------- |
| Non-Emb Params        | ~1.3B    |                                |                                                 | 0.35B                                         | 1.3B         | 1.3B       |
| MMLU (AVERAGE ACC)    | 26.17    |                                |                                                 | 25.02                                         |              |            |
| MMLU (stem ACC)       | 25.94    |                                |                                                 | 24.58                                         |              |            |
| MMLU (Humanities ACC) | 24.78    |                                |                                                 | 25.25                                         |              |            |
| MMLU (social ACC)     | 27.43    |                                |                                                 | 23.89                                         |              |            |
| MMLU (other ACC)      | 27.26    |                                |                                                 | 26.23                                         |              |            |
| GSM8K                 |          |                                |                                                 |                                               |              |            |
| C-Eval                |          |                                |                                                 |                                               |              |            |
| HumanEval             |          |                                |                                                 |                                               |              |            |
| TruthfulQA            |          |                                |                                                 |                                               |              |            |
| HellaSwag             |          |                                |                                                 |                                               |              |            |
| BIG-Bench Hard (BBH)  |          |                                |                                                 |                                               |              |            |


### Different Memory Size

每个memory可以访问之前4个tokens的short-term memory

训练长度1024

| Memory Size | Metrics |
| ----------- | ------- |
| 64          |         |
| 32          |         |
| 16          |         |




### Resources Usage

画一个图，第一个是GPT-2这种原生的，文本越多的时候memory越大，第二个是我的模型，增加到一定程度就停止了




### 收敛速度



相同训练tokens下的训练速度