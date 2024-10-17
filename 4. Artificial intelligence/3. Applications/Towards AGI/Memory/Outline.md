# Large Language Model with Implicit Memory


## Introduction

%% **Introduction只说模型优势，不要太多技​​术details。details可以放到Methodology Section的Framework Overview** %%

### 第一段

%% 第一段：宏观的问题。问题大背景、研究意义 -> 引出研究问题 %%

Memory is essential for intelligence as it integrates past experiences with current perceptions, therefore allows informed decision-making and adaptive behavior in a complex environment[^1]. Memory maintains coherent conceptualizations across contexts, which is crucial for tasks like multi-step reasoning, long-term planning, and consistent dialogue[^2]. 

Given the critical role of memory in cognitive processes, we have reason to believe that introducing similar memory capabilities into large language models (LLMs) could lead to a paradigm shift in how these models process information.

### 第二段

%% 第二段：宏观问题的难点。具体阐述研究问题、难点 （第一、二段可合并） %%

However, incorporating memory mechanisms into LLMs presents several key challenges. 

The first challenge is memory storage and update, which involves balancing the trade-off between memory capacity and computational efficiency. Fixed size storage is inevitably limited by the amount of information that can be stored, while more storage is also constrained by bandwidth and storage costs.

The second challenge is training efficiency, as updating memory usually needs sequence modeling. 

### 第三段

%% 第三段：宏观问题难点现有的解决办法和缺陷。概述研究现状以及存在主要的问题 %%

Current models like Transformers use massive memory storage (without compressing information) to achieve full parallel computation, saving considerable time but lacking in memory retention capabilities. 

On the other hand, models like Mamba use limited memory capacity to compress information and update memory but are prone to forgetting. This indicates a gap in existing methods that either excel in computational efficiency or memory retention, but not both.

### 第四段

%% 第四段：我们解决这个缺陷的方法。为了解决现有方法的缺点，提出我们的模型 （简要描述模型解决思路，不要太多技术details）-> 模型内部有什么挑战 -> 为了解决这些内部挑战，我们设计哪些新方法 （简要描述，不要太多技​​术details）。我们的模型方法有哪些优势 %%

To address these issues, we propose a novel LLM architecture that mimics human memory by integrating continuously updating short-term memory and long-term memory storage. 

To addressing the challenge of memory updating, retention and forgetting:
- we updating short-term memory through multiple rounds of attention, allowing the model to effectively capture and refine key information from the ongoing task. 
- Long-term memory, on the other hand, stores snapshots of each short-term memory update, maintaining a fixed-size window with a first-in, first-out (FIFO) policy.

To resolve the computational conflict between memory processing and regular input processing, we duplicate the original transformer layers and have one of them to handle memory-related computations separately. This allows the memory computations to be independent of the regular input, avoiding any interference between the two processes. 

### 第五段

%% 第五段：概括文章，强调贡献。 %%

In this paper, we make the following contributions:

%% 贡献1：发现什么现象 %%
%% 贡献2：我们的模型创新点 %%
%% 贡献3：实验效果 %%

1. **Novel Memory Mechanism**: We introduce a memory framework that enhances the model's ability to retain and recall information over time. Short-term memory continuously processes new inputs, extracting and compressing relevant information from the text. Long-term memory, on the other hand, preserves snapshots of the short-term memory updates. This snapshot retention is controllable and editable, allowing for flexible management of long-term memory.
2. **Model Efficiency**: Our model is theoretically more efficient than vanilla Transformers, with lower computational overhead and reduced memory consumption. Since we don't need to store large amounts of key-value (KV) cache, our model uses less GPU memory while maintaining comparable or even better computational efficiency.
3. **Experiment**: Minor performance degradation

### 第六段

%% 第六段：文章 outline (optional based on paper space) %%

Paper Outline

## LLM Framework

### Overview


### Short-term Memory


$$m_{t+1}=f(m_{t},x_t)$$
Here, $m_t$​ represents the memory state at time step $t$, and $x_t$ represents the input at the same time step.


### Long-term Memory

We maintain a fixed-size memory for long-term storage, following a first-in, first-out (FIFO) policy. Long-term memory stores snapshots of short-term memory at key intervals, allowing the model to recall previously processed information.


### Dual FFN

To handle the increased complexity of processing memory, we replicate the feedforward network (FFN) after the attention mechanism. This duplicated FFN is responsible for managing memory-related computations without affecting the original attention layers.


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


### Memory Usage

不同长度、模型大小下，显存的需求

| Model                                 | MB/64 tokens | MB/128 tokens | MB/256 tokens | MB/512 tokens | MB/1024 tokens |
| ------------------------------------- | ------------ | ------------- | ------------- | ------------- | -------------- |
| GPT-2 Medium                          | 1416         | 1447          | 1508          | 1629          | 1874           |
| GPT-2 Large                           | 3115         | 3162          | 3255          | 3440          | 3811           |
| meta-llama/Meta-Llama-3.1-8B-Instruct | 30935        | 31222         | 31802         | 34045         | 42128          |
| Model 4                               |              |               |               |               |                |
| memoGPT (Qwen/Qwen2-7B-Instruct)      |              |               |               |               | 32.35GB        |
|                                       |              |               |               |               |                |
|                                       |              |               |               |               |                |



```
export HF_HOME=/root/autodl-tmp/huggingface
export HF_ENDPOINT=https://hf-mirror.com
huggingface-cli login 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
huggingface-cli download --resume-download Qwen/Qwen2-7B-Instruct
```

### Case Study: Short-term Memory

- 测试短期记忆获取信息的跨度对模型性能的影响，也就是短期记忆是和多个tokens进行attention的
- 测试短期记忆更新的频率对模型性能的影响


#### 短期记忆应该获取多少的长期记忆信息

Qwen1.5B

##### 记忆长度：32

模型无训练
记忆长度：32
输入长度：64
推理长度：1024

| 长期记忆长度 | 32     | 64     | 96     | 128    | 160    | 192    | 224    | 256    | 288    | 320    | 352    | 384    | 416    | 448    | 480    | 512    | 544    | 576    | 608    | 640    | 672    | 704    | 736    | 768    | 800    | 832    | 864    | 896    | 928    | 960    | 992    | 1024   |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 0      | 4.4251 | 3.5543 | 4.2095 | 4.4303 | 4.3902 | 4.5387 | 4.6823 | 4.7417 | 4.6422 | 5.0041 | 4.8829 | 4.9264 | 4.8906 | 4.9573 | 5.0469 | 5.0824 | 5.2533 | 5.1480 | 5.2716 | 5.3219 | 5.2886 | 5.3220 | 5.4515 | 5.3570 | 5.3558 | 5.6604 | 5.3949 | 5.5161 | 5.5741 | 5.4877 | 5.4550 | 5.6181 |
| 1      |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |
| 2      | 4.4875 | 3.5967 | 4.1986 | 4.2646 | 4.5309 | 4.6200 | 4.4721 | 4.6389 | 4.8112 | 4.8410 | 4.8118 | 4.9232 | 5.1071 | 5.0962 | 5.2068 | 5.2280 | 5.4809 | 5.3552 | 5.3374 | 5.4472 | 5.6634 | 5.3836 | 5.3684 | 5.6062 | 5.6569 | 5.6254 | 5.6596 | 5.5568 | 5.4719 | 5.7059 | 5.5467 | 5.5981 |
| 3      |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |        |
| 4      | 4.4031 | 3.4089 | 4.3017 | 4.2693 | 4.3963 | 4.3955 | 4.4618 | 4.5942 | 4.7285 | 4.8805 | 4.8969 | 4.9851 | 5.0272 | 4.9929 | 5.1262 | 5.2053 | 5.3057 | 5.2045 | 5.3257 | 5.2931 | 5.5163 | 5.4062 | 5.6031 | 5.4599 | 5.5892 | 5.4952 | 5.4861 | 5.6464 | 5.6353 | 5.6444 | 5.6511 | 5.6826 |
| 6      | 4.5012 | 3.5050 | 4.2991 | 4.3732 | 4.4169 | 4.4849 | 4.5866 | 4.7346 | 4.6792 | 4.7348 | 4.9648 | 4.8987 | 5.0120 | 5.0767 | 5.1645 | 5.2376 | 5.2504 | 5.3324 | 5.3834 | 5.5842 | 5.5452 | 5.5712 | 5.5557 | 5.4866 | 5.6877 | 5.7793 | 5.7538 | 5.8316 | 5.8361 | 5.8310 | 5.9475 | 5.9954 |



| 长期记忆长度 | 推理长度 | loss   | perplexity |
| ------ | ---- | ------ | ---------- |
| 0      | 512  | 4.6479 | 104.3704   |
|        | 1024 | 5.0664 | 158.5986   |
| 1      | 512  | 4.6488 | 104.4552   |
|        | 1024 | 5.0271 | 152.4918   |
| 2      | 512  | 4.6448 | 104.0437   |
|        | 1024 | 5.0397 | 154.4218   |
| 3      | 512  | 4.6553 | 105.1459   |
|        | 1024 | 5.0519 | 156.3240   |
| 4      | 512  | 4.6520 | 104.7917   |
|        | 1024 | 5.0631 | 158.0744   |
| 6      | 512  | 4.6676 | 106.4459   |
|        | 1024 | 5.1461 | 171.7575   |
| 8      | 512  | 4.6730 | 107.0135   |
|        | 1024 | 5.2161 | 184.2094   |
|        |      |        |            |

1
- train segment loss: [4.4642, 3.5309, 4.2360, 4.2660, 4.4145, 4.4823, 4.5844, 4.5528, 4.7260, 4.7947, 4.9146, 4.9075, 4.9198, 5.1046, 5.0936, 5.1575]
- val segment loss: [4.4614, 3.5399, 4.2354, 4.3358, 4.4930, 4.5347, 4.5730, 4.6519, 4.7630, 4.7880, 4.8916, 4.9228, 5.0031, 4.9590, 5.0902, 5.1910, 5.3401, 5.2057, 5.3601, 5.2337, 5.2786, 5.3451, 5.3637, 5.4467, 5.3470, 5.4773, 5.5242, 5.5334, 5.4454, 5.4214, 5.3942, 5.3898]

3
- train segment loss: [4.4642, 3.5309, 4.2360, 4.2660, 4.4114, 4.4813, 4.5856, 4.5592, 4.7414, 4.8068, 4.9221, 4.9199, 4.9292, 5.1239, 5.1054, 5.1707]
- val segment loss: [4.4614, 3.5399, 4.2354, 4.3358, 4.4948, 4.5333, 4.5749, 4.6514, 4.7643, 4.7985, 4.8844, 4.9388, 5.0215, 4.9691, 5.1023, 5.1958, 5.3494, 5.2256, 5.3777, 5.2462, 5.3089, 5.3666, 5.3914, 5.4706, 5.3883, 5.5099, 5.5738, 5.5709, 5.4877, 5.4863, 5.4340, 5.4348]

8
train segment loss: [4.4642, 3.5309, 4.2360, 4.2660, 4.4114, 4.4813, 4.5874, 4.5603, 4.7445, 4.8171, 4.9518, 4.9459, 4.9783, 5.1795, 5.1741, 5.2527]
val segment loss: [4.4614, 3.5399, 4.2354, 4.3358, 4.4948, 4.5333, 4.5760, 4.6577, 4.7672, 4.8088, 4.9100, 4.9607, 5.0449, 5.0253, 5.1591, 5.2690, 5.4290, 5.3307, 5.4852, 5.3739, 5.4829, 5.5585, 5.5826, 5.7180, 5.6678, 5.8217, 5.9330, 5.9167, 5.9350, 6.0177, 6.0003, 6.0729]


##### 记忆长度：64

模型无训练
记忆长度：64
输入长度：64
推理长度：2048

| 长期记忆长度 | 推理长度 | loss   | perplexity |
| ------ | ---- | ------ | ---------- |
| 0      | 1024 | 5.5143 | 248.2286   |
|        | 2048 | 5.7882 | 326.4226   |
| 1      | 1024 | 5.5163 | 248.7078   |
|        | 2048 | 5.7740 | 321.8208   |
| 2      | 1024 | 5.5203 | 249.7080   |
|        | 2048 | 5.7817 | 324.3000   |
| 3      | 1024 | 5.5249 | 250.8663   |
|        | 2048 | 5.8027 | 331.1874   |
| 4      | 1024 | 5.5294 | 252.0027   |
|        | 2048 | 5.8294 | 340.1610   |
| 6      | 1024 | 5.5385 | 254.2863   |
|        | 2048 | 5.8970 | 363.9528   |


0
- train segment loss: [4.0567, 5.1920, 5.3013, 5.3863, 5.5126, 5.4569, 5.6450, 5.5840, 5.6450, 5.7427, 5.7809, 5.8476, 5.9009, 5.8577, 5.8884, 5.9166],                                                                                                                                                                                                   
- val segment loss: [4.0535, 5.0935, 5.1972, 5.3260, 5.4036, 5.4863, 5.5610, 5.6642, 5.6011, 5.6878, 5.7512, 5.6635, 5.8387, 5.8266, 5.8986, 5.9157, 6.0043, 6.0382, 5.9662, 5.9983, 5.9836, 5.9982, 5.9989, 6.1086, 6.0975, 6.1940, 6.1394, 6.1995, 6.1055, 6.1690, 6.2048, 6.1972]

1
- train segment loss: [4.0567, 5.1920, 5.3013, 5.3853, 5.5181, 5.4668, 5.6553, 5.5999, 5.6517, 5.7542, 5.7719, 5.8553, 5.9045, 5.8481, 5.8772, 5.9159]
- val segment loss: [4.0535, 5.0935, 5.2016, 5.3262, 5.4035, 5.5012, 5.5669, 5.6637, 5.6031, 5.6948, 5.7499, 5.6567, 5.8447, 5.8342, 5.8882, 5.9026, 5.9942, 6.0282, 5.9513, 5.9732, 5.9676, 5.9725, 5.9757, 6.0715, 6.0585, 6.1593, 6.1011, 6.1556, 6.0687, 6.1212, 6.1629, 6.1560]

2
- train segment loss: [4.0567, 5.1920, 5.3013, 5.3882, 5.5292, 5.4710, 5.6603, 5.6045, 5.6620, 5.7578, 5.7780, 5.8544, 5.9108, 5.8647, 5.8821, 5.9224],                                                                                                                          
- val segment loss: [4.0535, 5.0935, 5.2016, 5.3302, 5.4172, 5.5083, 5.5598, 5.6684, 5.6067, 5.6952, 5.7553, 5.6649, 5.8505, 5.8305, 5.8926, 5.9141, 6.0057, 6.0294, 5.9600, 5.9872, 5.9752, 5.9842, 5.9837, 6.0819, 6.0734, 6.1753, 6.1181, 6.1740, 6.0855, 6.1428, 6.1930, 6.1690]

3
- train segment loss: [4.0567, 5.1920, 5.3013, 5.3882, 5.5307, 5.4668, 5.6700, 5.6067, 5.6716, 5.7619, 5.7867, 5.8575, 5.9171, 5.8693, 5.8891, 5.9357]
- val segment loss: [4.0535, 5.0935, 5.2016, 5.3302, 5.4164, 5.5129, 5.5692, 5.6719, 5.6116, 5.6993, 5.7648, 5.6701, 5.8601, 5.8407, 5.9100, 5.9233, 6.0129, 6.0581, 5.9940, 6.0158, 6.0004, 6.0197, 6.0262, 6.1176, 6.1347, 6.2210, 6.1715, 6.2269, 6.1611, 6.2073, 6.2550, 6.2223]

4
- train segment loss: [4.0567, 5.1920, 5.3013, 5.3882, 5.5307, 5.4699, 5.6640, 5.6041, 5.6625, 5.7610, 5.7852, 5.8605, 5.9273, 5.8840, 5.9051, 5.9454],                                                                                                                          
- val segment loss: [4.0535, 5.0935, 5.2016, 5.3302, 5.4164, 5.5139, 5.5715, 5.6754, 5.6109, 5.7106, 5.7757, 5.6848, 5.8653, 5.8569, 5.9229, 5.9361, 6.0347, 6.0681, 6.0137, 6.0319, 6.0241, 6.0538, 6.0569, 6.1545, 6.1778, 6.2496, 6.2213, 6.2772, 6.2010, 6.2691, 6.3081, 6.2854]

6
- train segment loss: tensor([4.0567, 5.1920, 5.3013, 5.3882, 5.5307, 5.4699, 5.6676, 5.6146, 5.6718, 5.7704, 5.7956, 5.8793, 5.9471, 5.9083, 5.9351, 5.9801],                                                                                                                          
- val segment loss: [4.0535, 5.0935, 5.2016, 5.3302, 5.4164, 5.5139, 5.5755, 5.6761, 5.6157, 5.7195, 5.7769, 5.6999, 5.8865, 5.8772, 5.9609, 5.9700, 6.0669, 6.1086, 6.0711, 6.0960, 6.1261, 6.1241, 6.1656, 6.2604, 6.2950, 6.3852, 6.4003, 6.4436, 6.4094, 6.4773, 6.5721, 6.5957]

#### 短期记忆的宽度应该是多少



35.91


---

[^1]: Glenberg, A. M. (1997). What memory is for. _Behavioral and brain sciences_, _20_(1), 1-19.
[^2]: Zhang, Z., Bo, X., Ma, C., Li, R., Chen, X., Dai, Q., ... & Wen, J. R. (2024). A survey on the memory mechanism of large language model based agents. _arXiv preprint arXiv:2404.13501_.