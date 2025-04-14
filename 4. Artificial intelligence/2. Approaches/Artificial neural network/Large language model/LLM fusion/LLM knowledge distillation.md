## FuseLLM series

知识蒸馏多个模型来实现模型融合主要还是

### Knowledge Fusion of Large Language Models

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250414165009301.png)

2024.01 ICLR [FuseLLM](https://openreview.net/forum?id=jiDsk12qcz)用最短编辑距离来对齐vocabulary

合并了LLaMA-2 7B, OpenLLaMA 7B, MPT 7B

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250414172715076.png)


### FuseChat: Knowledge Fusion of Chat Models

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250414205553769.png)

2024.08 arXiv[FuseChat](https://arxiv.org/abs/2408.07990) 的做法：
$$
A+B \rightarrow E_1, A+C \rightarrow E_2, A+D \rightarrow E_3 \text {, 然后 } E_1+E_2+E_3 \rightarrow \text { FuseChat }
$$
先讲多个模型与pivot模型融合，然后将合并后的模型用model merging合并。

model merging的策略：pruning + scaling + voting （scaling是变化参数越大的模型权重越高）






