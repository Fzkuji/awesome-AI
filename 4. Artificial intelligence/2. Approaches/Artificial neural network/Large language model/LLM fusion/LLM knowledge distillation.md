## FuseLLM series

知识蒸馏多个模型来实现模型融合主要是中山大学和腾讯合作的一个组搞的，Fanqi Wan 和 Ziyi Yang，他们成立了[FuseAI](https://huggingface.co/FuseAI)

### Knowledge Fusion of Large Language Models

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250414165009301.png)

ICLR'24 [FuseLLM](https://openreview.net/forum?id=jiDsk12qcz)用最短编辑距离来对齐vocabulary

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

实验和OpenChat进行了对比，并没有比较前作，效果有提升，但波动较大。猜测因此没有发表。


### Weighted-Reward Preference Optimization for Implicit Model Fusion


ICLR'25 [WRPO](https://openreview.net/forum?id=fq24pEb8SL)

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250415154541742.png)




### FuseChat-3.0: Preference Optimization Meets Heterogeneous Model Fusion

[FuseChat-3.0: Preference Optimization Meets Heterogeneous Model Fusion](https://arxiv.org/pdf/2503.04222)

![](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20fusion/LLM%20knowledge%20distillation/IMG-20250415105833455.png)






