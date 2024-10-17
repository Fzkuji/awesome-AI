# Large Language Model with Implicit Memory



## Introduction

%% **Introduction只说模型优势，不要太多技​​术details。details可以放到Methodology Section的Framework Overview** %%

%% 第一段：宏观的问题。问题大背景、研究意义 -> 引出研究问题 %%

Memory is essential for intelligence as it integrates past experiences with current perceptions, therefore allows informed decision-making and adaptive behavior in a complex environment[^1]. Memory maintains coherent conceptualizations across contexts, which is crucial for tasks like multi-step reasoning, long-term planning, and consistent dialogue[^2]. 

Given the critical role of memory in cognitive processes, we have reason to believe that introducing similar memory capabilities into large language models (LLMs) could lead to a paradigm shift in how these models process information.

%% 第二段：宏观问题的难点。具体阐述研究问题、难点 （第一、二段可合并） %%

Second paragraph: Specific research problem and challenges

第一个难点就是记忆的保存和更新，也就是空间复杂度的取舍。如果要保存更多的信息就要占用更多的内存，而固定大小的的内容不可避免的会有用尽的时候。

第二个是模型训练的时间，也就是空间复杂度的取舍。如果有记忆的更新就会有循环的计算结构，如果想要并行就会放弃时序结构的更新能力。

However, incorporating memory mechanisms into LLMs presents several key challenges. The first challenge is memory storage and update, which involves balancing the trade-off between memory capacity and computational efficiency. Storing more information requires more memory, but fixed-size memory inevitably faces the risk of running out. The second challenge is training efficiency, as updating memory adds computational complexity. Achieving parallelism may require sacrificing the ability to update sequentially, which can limit the model's effectiveness in dynamic tasks.

%% 第三段：宏观问题难点现有的解决办法和缺陷。概述研究现状以及存在主要的问题 %%

transformers一类的大模型是：使用大量的内存占用（也就是不对信息进行压缩），换取完全的并行计算，节约大量时间。缺点就是记不住信息。

mamba一类的模型是：使用有限的内存占用，换取信息的压缩和记忆的更新。缺点就是容易遗忘。

%% 第四段：我们解决这个缺陷的方法。为了解决现有方法的缺点，提出我们的模型 （简要描述模型解决思路，不要太多技术details）-> 模型内部有什么挑战 -> 为了解决这些内部挑战，我们设计哪些新方法 （简要描述，不要太多技​​术details）。我们的模型方法有哪些优势 %%

我们的模型是模仿人类的记忆，设计了不断更新的短期记忆和能够进行存储的长期记忆。将循环神经网络压缩的能力，和注意力网络处理信息的能力相结合。

%% 第五段：概括文章，强调贡献。 %%

短期记忆通过不断获取新的输入的信息，对文本的内容进行提炼和压缩。

长期记忆是通过保留短期记忆的快照来保存信息，这个操作可以主动控制，因此长期记忆的保留是可控可编辑的。

我们在qwen2-0.5B上进行了预训练（事实证明这不是一个好主意）

%% 贡献1：发现什么现象 %%

%% 贡献2：我们的模型创新点 %%

%% 贡献3：实验效果 %%



%% 第六段：文章 outline (optional based on paper space) %%

## LLM Framework

### Overview


### Short-term Memory

短期记忆的更新：
$$m_{t+1}=f(m_{t},x_t)$$
其中m是短期记忆的token，x是输入的文本

### Long-term Memory

保留一个固定大小的存储器，按照先进先出的规则




### Dual FFN

复制原本的attention后面的FFN作为记忆模块的FFN，



### Model Training


## Experiments


### Overall Performance

用微调后的Qwen/Qwen2-0.5B-Instruct对比GPT-2medium

使用的数据集包括基于fineweb的，还有openwebtext


### Controllable Memory 

分析存储的记忆对后续文本回答的帮助。

最好是能够记住之前的事情。如果在QA上能够在没有先前文本的情况下进行回答，那就说明可以了。

次一点就是，使用了记忆和使用记忆，前后会有损失上的差距，这样可以说明记忆tokens是有效的。


### Efficiency

- 训练时间
	- 收敛速度
- 推理时间

根据ollama，You should have at least 8 GB of RAM available to run the 7B models, 16 GB to run the 13B models, and 32 GB to run the 33B models.

### Memory Usage


不同长度、模型大小下，显存的需求


### Case Study: Short-term Memory

- 测试短期记忆获取信息的跨度对模型性能的影响，也就是短期记忆是和多个tokens进行attention的
- 测试短期记忆更新的频率对模型性能的影响








---

[^1]: Glenberg, A. M. (1997). What memory is for. _Behavioral and brain sciences_, _20_(1), 1-19.
[^2]: Zhang, Z., Bo, X., Ma, C., Li, R., Chen, X., Dai, Q., ... & Wen, J. R. (2024). A survey on the memory mechanism of large language model based agents. _arXiv preprint arXiv:2404.13501_.