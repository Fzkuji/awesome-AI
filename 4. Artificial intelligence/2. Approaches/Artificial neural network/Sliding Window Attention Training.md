# Title

- [ ] Sliding Window Training
- [ ] Sliding Window Training for Large Language Models
- [ ] Sliding Window Attention Training: Enhancing Efficiency and Context Length in Large Language Models
- [-] SWAT: Towards Efficient Long-Context Handling in Large Language Models

# Paper

## Introduction

1. **背景与研究意义**  
	- 大语言模型（LLMs）近年来发展迅速，在多个任务中取得了突破性进展。然而，这些模型的计算复杂度通常随着输入长度呈平方级增长（O(n²)），严重限制了它们在长文本任务和移动设备上的应用。因此，如何让大模型兼顾计算效率和模型性能，非常重要。
2. **研究问题与挑战**  
	- 目前已经有多种方案，如稀疏注意力机制、线性注意力和缓存量化等等。但是这些方法通常引入了额外的架构和注意力机制，实现较为复杂。同时过度的压缩导致了模型性能相对于vanilla transformer有较大的差距。因此，核心挑战在于，让大模型压缩隐层状态的同时，保持性能。
3. **现有研究与局限性** 
	- 在这些方法中，注意力的稀疏化是最符合直觉的方案，因为其不引入额外的模型结构。现有方法（streamingllm）已经发现，通过少量的调整，可以让模型在一定的长度内进行滑动窗口的推理，同时还可以保持困惑度。但是这种方法仍然存在以下的问题：
		- 目前现有的滑动窗口的探索，主要集中在推理阶段，使用通过保留少量最初的tokens来保持模型的稳定。即：模型过度依赖于最开始的tokens，也就是attention sink，这种缺陷是预训练模型的普遍缺陷。而这导致了推理时仍然要引入额外的操作。
		- 丢弃的tokens造成了信息的丢失。
	- 因此，我们需要探索最小化修改transformer架构的方法
1. **本文工作**  
	- 为了解决上述问题，本文探索了直接使用滑动窗口训练深层大模型的方法：  
		- 在不引入额外网络或计算流程的情况下，采用原始滑动窗口策略训练模型。有效避免了模型出现attention sink的问题（实验2、3、4）。
		- 由于旧token会被丢弃，因此训练时我们使用更深的模型，因为模型深度和其可以保存的hidden states呈线性关系，这保证了旧token的丢失不影响整体的信息保存。（实验1）
	- 由于是纯粹的滑动窗口，因此本文的模型继承了滑动窗口推理速度快的优点。
2. **贡献**  
	- 本工作通过理论和实验相结合的方式，系统性地揭示了现有基于Transformer的LLMs固有缺陷，即Casual Attention Mask和Normalization导致了信息的丢失和持续推理的失效。
	- 本文设计了一个全新的模型结构和训练范式的组合。通过实验分析了滑动窗口大小、模型深度和训练文本长度之间的关联。证明了在滑动窗口下，模型深度决定了模型能够囊括的信息。
	- 实验表明，我们的模型在滑动窗口推理中全面优于普遍使用的Transformer架构。其可以有效避免attention sink问题，同时在长文本推理上更有优势。


## Understanding Transformer's Attention

### Sliding Window Attention (SWA)

%%
- 介绍SWA的概念
    - 在窗口内进行自注意力计算，而不是考虑所有tokens。理想的信息传递的趋势为，从旧token到新token、从模型底层向模型上层
- 作用
    - 通过动态移动窗口来处理长文本，同时减少推理所需的资源
- 结论
    - 最后给出一个假设/结论：Evicted Tokens（保存所有tokens的kv cache）不是必须的。
    - 提出滑动窗口注意力的核心因素：滑动窗口大小、训练文本长度和模型深度，以及最终影响的信息传递距离。其规律是，滑动窗口越大、训练长度越长、模型深度越深，信息传递的距离就越远，这样训练得到的模型效果就越好。
%%

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Sliding%20Window%20Attention%20Training/IMG-20250116105201723.png)

Sliding window attention (SWA) is first introduced in Longformer, which is designed to overcome the $O(n^2)$ computational complexity of traditional transformers. By restricting the attention computation to a fixed window size $w$, the computational cost for each token is reduced to $O(w)$, and the overall complexity scales linearly with the sequence length $n$, resulting in $O(n \cdot w)$. Typically, $w \ll n$, making this approach significantly more efficient for processing long sequences. 

As shown in Figure 1, under the SWA mechanism, tokens can be divided into three categories: active tokens, residual tokens, and past tokens. Among them, the information of the first type of tokens (active tokens) is fully visible to the model, and their embeddings are still in the window. For the latter two categories, referred to as evicted tokens, their embeddings have been discarded and are no longer visible to the model. However, while residual tokens are not directly visible, their information is preserved in the higher transformer layers, allowing the model to utilize this retained information during inference. With each additional transformer layer, the range of residual tokens retained in the higher layers expands by $(w-1)$ tokens. Therefore, the theoretical maximum range of information coverage in a SWA-based transformer is $(w-1) \cdot L + 1$, where $L$ denotes the number of transformer layers.

Therefore, theoretically, evicted tokens (i.e., tokens whose key-value cache is discarded) are not indispensable for maintaining the model's performance, as long as their information is effectively retained and propagated through the higher layers of the transformer. Empirically, larger window sizes, deeper models, and longer training sequences tend to result in better performance.

 
### Why current LLMs fail using SWA

%% 
虽然目前的开源大语言模型在结构上完全支持SWA推理，但是在实践中却无法做到这一点，为此，我们首先分析了现有大模型在推理上的特征，然后整合现有工作的结论进行，给出关键的insights。
%%



To address this, we first analyze the inference characteristics of existing LLMs and then integrate insights from existing works to provide key conclusions.

#### Informantion Loss

Although current open-source LLMs are structurally capable of supporting SWA inference, they fail to achieve this in practice. As shown in Figure 2, we analyzed the perplexity (PPL) of four open-source LLMs using different sliding window sizes on the PG19 test set. The experimental results show that the LLMs achieve the best performance only when the inference text length matches the training text length, regardless of whether SWA is used. This implies that transformers are trained to fit a specific attention window size, which is determined by the text length used during training.

首先最直接的问题，就是丢弃token导致的信息丢失。尤其是长程信息的丢失

#### Attention Sink

%%
- 分析开源大模型
	- （实验2）在不同窗口大小、不同推理长度下，模型的性能特征。模型包括llama2、qwen2。
	- （可选）验证mistral模型。取决于后续实验alibi是否成功，如果成功，我们的方法将优于alibi，因此可以对比mistral
- 结论一
	- transformer模型拥有一个特定大小的attention window，这个window的大小是由训练时的文本长度决定的。
%%

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Sliding%20Window%20Attention%20Training/IMG-20250116105201748.png)



Conclusion 1: 

%%
- 进一步分析现有模型的窗口扩展失败的原因/为什么vanilla transformer不适合SWA推理
	- 理论一：Transformer中的Causal Attention不具有置换不变性，等价于拥有位置信息。这种位置信息主要依赖于Softmax（中的Normalization）操作对不同位置token embedding方差的影响。
		- [Decoder-only的LLM为什么需要位置编码？](https://kexue.fm/archives/10347) 引用这里的解释
		- 因此，Transformer模型倾向于使用这种信息作为位置编码。
		- （实验3）论据：无位置编码（NoPE）的相比较有位置编码的模型，性能下降不明显。而理论上transformer没有位置编码无法区分tokens先后，而这直觉上应该有很严重的性能损失。而删除了Softmax的模型性能进一步下降。
	- 理论二：Softmax（中的Normalization）导致了Attention Sink。模型对后续 token 的计算高度依赖最前面的几个 token，而这些 token 很可能是通过理论一中的方差方法识别的。
		- 基于论文[When Attention Sink Emerges in Language Models: An Empirical View](https://arxiv.org/abs/2410.10781)的结论，删除Softmax后，Attention Sink消失。
		- （实验6）论据，Softmax但假如RoPE后，Vanilla Transformer可以进行滑动窗口推理
	- 理论三：Attention Sink导致了滑动窗口注意力的失效。一旦进行滑动，最前面几个 token必然首先被丢弃，而这种范式在训练中并不存在。
		- 由于常规casual attention没有丢弃token这一情况，模型也产生了对于前几个token的依赖，也就是streamingllm和lm-infinite发现的attention sink现象，以更高效地利用这种位置编码（虽然现有模型额外加入了RoPE，但是模型仍然会考虑softmax的位置编码）。但这最终导致模型无法直接适应滑动窗口推理。
		- 结论2论据：streamingllm解决这一问题的方式是，保留最前面几个token，只滑动剩余tokens。最后拼接这两部分tokens，将和训练时一样的位置编码应用到这个拼接的tokens序列上，因此，这进一步说明模型对当前的位置编码产生了严重的依赖（过拟合）。
- 结论二
	- Transformer的Attention存在专注度（Concentration）的平衡问题。如果专注度很平均（正则化较弱），则模型无法捕捉关键信息，其会被庞杂的tokens淹没，影响性能；如果专注度很强（正则化较强），则模型虽然可以更快检索到所需的信息，但也可能丢失更多的信息。
%%

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Sliding%20Window%20Attention%20Training/IMG-20250116105201778.png)

另一方面，streamllm和xxx验证了直接将swa应用到llm的问题。即attention sink导致的推理失效。关于attention sink，我们通过现有的工作的实验得到了三个推论，而这三个推论最后可以得出最终的结论。

推论一：Transformer中的Causal Attention本身具有置换敏感性。这种敏感性依赖于Softmax（中的Normalization）操作对不同位置token embedding方差的影响。

[Su](https://kexue.fm/archives/10347) 的文章中提到，Causal Attention不具有置换不变形，这等价于拥有位置信息。因此，Transformer模型倾向于使用这种信息作为位置编码。Figure 3也验证了这一观点，无位置编码（NoPE）的相比较有位置编码的模型，性能下降不明显。而理论上transformer没有位置编码无法区分tokens先后，而这直觉上应该有很严重的性能损失。而删除了Softmax的模型性能大幅下降，无法稳定学习。

理论二：Normalization导致了Attention Sink。模型对后续 token 的计算高度依赖最前面的几个 token，而这些 token 很可能是通过理论一中的方差方法识别的。

论文[When Attention Sink Emerges in Language Models: An Empirical View](https://arxiv.org/abs/2410.10781)实验发现，删除Attention中的正则化操作后，Attention Sink消失。



## Sliding Window Attention Training


基于前文的铺垫，我们设计了一整个模型结构和训练的范式：适用于SWA training的模型，基于无normalization的attention和balanced alibi的位置编码，简称为SWAT。

### Attention Paradigms

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Sliding%20Window%20Attention%20Training/IMG-20250116105201802.png)

分析SWAT下，两种训练范式：
- Short Sequence Attention (when token index < window size)
	- 分析这种情况下的正则化特点，就说和常规transformer训练一致就行

- Long Sequence Attention (when token index > window size)
	- 分析固定窗口大小下的训练特点，解释为什么传统预训练忽略了这种范式
	- 强调这个attention过程中，注意力宽度一致，因此没有隐含的位置编码，需要显式添加

### Window Position Embedding (WiPE)

#### Sigmoid Activation Function

- Vanilla Transformer的Softmax导致模型过于关注特定的token，造成信息的丢失，因此替换成Sigmoid，使权重更加均衡

#### Balanced Alibi

- 由于使用了滑动窗口，因此我们不再需要对整个输入序列进行位置编码，而只需要对当前窗口下的所有attention进行编码，而在训练的时候，我们可以使用alibi实现这一点。
- 原版alibi倾向于让模型关注新的token，造成长程信息丢失，因此我们将alibi的slope改为一半正、一般负。

## Experiments

### Experiment Settings

In our experimental setup, we employed three datasets for model pre-training and evaluation: **OpenWebText**, **OpenOrca**, and **PG-19**. We utilized OpenWebText as the training dataset, while all three datasets were incorporated into the validation phase. For OpenWebText and PG-19, we extended the input sequence length to 16,384 tokens, whereas for the OpenOrca question-answering dataset, we specifically selected samples containing longer question segments for validation purposes.

These datasets were strategically chosen to evaluate distinct aspects of model capabilities. OpenWebText, predominantly comprising shorter texts, serves to assess the model's fundamental language modeling abilities. PG-19, derived from book-length texts, presents a scenario where each token carries significant predictive value for subsequent content, thereby evaluating the model's information compression capabilities in long-form contexts. OpenOrca, as a question-answering dataset, offers a unique evaluation perspective: during inference, the question portion is discarded, making it particularly effective for assessing the model's information retention capabilities across extended sequences.

Given the comprehensiveness of our dataset collection, we employed cross-entropy loss as a straightforward yet effective metric for performance evaluation. Our experimental comparisons primarily focus on the vanilla Transformer architecture, where we systematically examined various combinations of sliding window sizes, model depths, and training sequence lengths. This comparative framework enables us to isolate and analyze the impact of these architectural components on model performance across different text understanding scenarios.

### Overall Performance

**目标**：复现预训练模型中的attention sink问题。同时训练一个对比模型，验证滑动窗口训练是否可以避免/不受影响。
**实验内容**：使用openwebtext数据集，预训练基于llama/qwen架构的模型，考虑不同的窗口大小和训练文本长度，同时对比vanilla模型。控制变量：模型层数 $L$、滑动窗口大小$w$、训练文本长度。  
**理论结果**：  
- 滑动窗口训练能够克服attention sink的问题，直接进行滑动窗口推理。
- vanilla transformer出现了开原模型类似的attention sink现象，并且影响了性能。
- 值得注意的是，目前的结果是在训练8w个step之后的模型上得到的，如果模型继续训练，相应地attention sink问题只会更加严重。

| Models                 | Train Window | Train Length | Eval Window | OpenWebText Loss (Eval Length=) |        |        |        | PG-19 Loss (Eval Length=) |        |        |        | OpenOrca |
| :--------------------- | :----------: | :----------: | :---------: | :-----------------------------: | :----: | :----: | :----: | :-----------------------: | :----: | :----: | :----: | -------- |
|                        |              |              |             |               128               |  1024  |  4096  | 16384  |            128            |  1024  |  4096  | 16384  |          |
| Vanilla A              |     128      |     128      |     128     |              3.249              |        |        | 4.8414 |                           |        |        | 5.6949 |          |
| Sliding Window A       |     128      |     1024     |     128     |             3.3619              | 3.1286 | 3.0766 | 3.0051 |          5.1785           | 4.8164 | 4.7510 | 4.7663 |          |
| Vanilla B              |     1024     |     1024     |     128     |             3.3395              | 3.3042 | 3.2856 | 3.2379 |          5.6052           | 5.0742 | 5.0797 | 5.1336 |          |
| Vanilla B              |     1024     |     1024     |    1024     |             3.3395              | 2.9716 | 2.9541 | 2.9636 |          5.6052           | 5.3429 | 5.1517 | 5.0274 |          |
| Vanilla B              |     1024     |     1024     |    16384    |             3.3395              | 2.9716 | 3.5534 | 3.0786 |          3.3395           | 2.9716 | 5.4912 | 5.2372 |          |
| Sliding Window B       |     1024     |     4096     |    1024     |             3.4380              | 3.0197 | 2.9638 | 2.9128 |          5.0880           | 4.6587 | 4.5107 | 4.4383 |          |
| Vanilla C              |     4096     |     4096     |    4096     |                                 |        | 2.9705 | 2.9518 |                           |        |        | 4.4938 |          |
| Vanilla D (Upper Bond) |    16384     |    16384     |    16384    |               OOM               |        |        |        |            OOM            |        |        |        |          |



![600](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Sliding%20Window%20Attention%20Training/IMG-20250116105201832.png)


%% 
### 实验2：Open Source LLM Analysis

**目标**：分析开源模型的滑动窗口能力。
**实验设置**：  
- 使用Qwen2、Llama3、Mistral模型进行滑动窗口推理，考虑其预训练的设置。
- 分析不同模型的attention score，同时和实验1的模型进行对比。
**理论结果**：  
- Qwen2、Llama3无法滑动推理，Mistral则可以
- 在attention score上，Qwen2、Llama3对比，滑动训练模型和Mistral，有显著区别（？）

#### meta-llama/Llama-2-7b-hf

1st book
Train Length: 4096

| Val Length | 1024    | 2048   | 3072   | 4096     | 5120   | 6144   | 7168   | 8192 |
| ---------- | ------- | ------ | ------ | -------- | ------ | ------ | ------ | ---- |
| 1024       | 5.58    |        |        |          |        |        |        |      |
| 2048       | 129.25  | 5.57   |        |          |        |        |        |      |
| 3072       | 410.29  | 47.78  | 5.63   |          |        |        |        |      |
| 4096       | 802.05  | 171.01 | 24.52  | **5.56** |        |        |        |      |
| 5120       | 1207.94 | 335.34 | 66.57  | 17.35    | 13.42  |        |        |      |
| 6144       | 1624.10 | 549.32 | 144.21 | 37.02    | 37.59  | 41.78  |        |      |
| 7168       | 1951.61 | 769.80 | 238.44 | 65.23    | 78.87  | 78.68  | 96.56  |      |
| 8192       | 2243.29 | 995.83 | 359.19 | 106.56   | 136.40 | 123.73 | 194.91 | nan  |

#### meta-llama/Llama-3.1-8B

1st book
Train Length: 4096 -> 8192

| Val Length | 1024  | 2048  | 4096  | 8192  | 12288  | 16384  | 32768  |
| ---------- | ----- | ----- | ----- | ----- | ------ | ------ | ------ |
| 1024       | 5.98  |       |       |       |        |        |        |
| 2048       | 22.90 | 6.10  |       |       |        |        |        |
| 4096       | 40.82 | 21.38 | 6.28  |       |        |        |        |
| 8192       | 57.34 | 38.89 | 21.29 | 6.44  |        |        |        |
| 12288      | 66.97 | 50.10 | 32.97 | 15.78 | 24.55  |        |        |
| 16384      | 70.90 | 55.42 | 40.14 | 24.63 | 62.88  | 72.18  |        |
| 32768      | 71.33 | 59.37 | 49.60 | 45.12 | 355.30 | 492.74 | 463.03 |



#### Qwen/Qwen2-7B

3rd book
Train Length: 4096 -> 32768

| Val Length | 1024   | 2048  | 4096  | 8192      | 12288  | 16384   | 32768   | 65536   |
| ---------- | ------ | ----- | ----- | --------- | ------ | ------- | ------- | ------- |
| 1024       | 14.63  |       |       |           |        |         |         |         |
| 2048       | 49.97  | 12.50 |       |           |        |         |         |         |
| 4096       | 77.22  | 40.56 | 11.53 |           |        |         |         |         |
| 8192       | 97.65  | 66.60 | 36.43 | **10.56** |        |         |         |         |
| 12288      | 96.95  | 72.07 | 47.54 | 22.11     | 32.85  |         |         |         |
| 16384      | 100.51 | 78.55 | 56.82 | 33.54     | 82.38  | 94.81   |         |         |
| 32768      | 104.64 | 86.37 | 72.33 | 62.62     | 429.35 | 581.99  | 511.63  |         |
| 65536      | 115.03 | 97.92 | 87.15 | 89.11     | 854.50 | 1542.90 | 1366.32 | 1353.46 |

#### mistralai/Mistral-7B-v0.1

3rd book

| Val Length | 1024  | 2048  | 4096 | 8192  | 12288   | 16384    | 32768   | 65536   |
| ---------- | ----- | ----- | ---- | ----- | ------- | -------- | ------- | ------- |
| 1024       | 6.77  |       |      |       |         |          |         |         |
| 2048       | 13.97 | 8.09  |      |       |         |          |         |         |
| 4096       | 24.37 | 12.04 | 8.02 |       |         |          |         |         |
| 8192       | 37.83 | 23.78 | 8.67 | 8.13  |         |          |         |         |
| 12288      | 35.38 | 26.27 | 7.97 | 7.72  | 19.66   |          |         |         |
| 16384      | 37.04 | 28.84 | 8.06 | 10.83 | 80.05   | 78.35    |         |         |
| 32768      | 39.86 | 33.84 | 8.06 | 46.03 | 786.36  | 1151.07  | 670.99  |         |
| 65536      | 43.80 | 38.06 | 8.53 | 58.47 | 2521.09 | 16288.44 | 3155.50 | 2658.53 |


%%

### Attention Implementation Analysis (Ablation Study)

**目标**：在无滑动位置编码的情况下，分析滑动窗口训练的模型。
**实验设置**：  
- 不使用位置编码，在不同滑动窗口策略下预训练模型。  
**理论结果**：  
- 不使用滑动窗口训练的模型无法推理更长的文本
- 滑动窗口训练的模型能够在长文本下保持性能
- NoPE下，滑动窗口和模型的深度密切相关

Eval Length=16384

| Models                        | n_head | n_embd | Train Window | Train Length | Eval Window | OpenWebText |   PG-19    |  OpenOrca  |
| :---------------------------- | ------ | ------ | :----------: | :----------: | :---------: | :---------: | :--------: | :--------: |
| Vanilla (Softmax+RoPE)        | 12     | 768    |     128      |     128      |     128     |   4.8414    |   5.6949   |   6.0085   |
| Vanilla (Sigmoid+RoPE)        | 12     | 768    |     128      |     128      |     128     |   14.2562   |  15.4765   |   1.9906   |
| Sliding (Softmax+RoPE)        | 12     | 768    |     128      |     1024     |     128     |   3.0140    |   4.7839   |   6.9671   |
| Sliding (Sigmoid+Alibi-12:0)  | 12     | 768    |     128      |     1024     |     128     |   3.0073    |   4.6895   |   0.1631   |
| Sliding (Sigmoid+Alibi-8:4)   | 12     | 768    |     128      |     1024     |     128     |   3.0391    |   4.6435   |   0.2650   |
| Sliding (Sigmoid+Alibi-6:6)   | 12     | 768    |     128      |     1024     |     128     |   3.0484    |   4.9920   | **0.1420** |
| Sliding (Sigmoid+AliRope-6:6) | 12     | 768    |     128      |     1024     |     128     |   3.0486    | **4.3103** |   0.1709   |
| Sliding (Sigmoid+Alibi-6:6)   | 12     | 768    |     128      |     2048     |     128     |             |            |            |
| Sliding (Sigmoid+Alibi-6:6)   | 24     | 1536   |     128      |     1024     |     128     |             |            |            |
| Vanilla (Softmax+RoPE)        | 12     | 768    |     1024     |     1024     |    1024     | **2.9631**  |   4.5447   |   5.4702   |
| Vanilla (Sigmoid+Alibi)       | 12     | 768    |     1024     |     1024     |    1024     |   2.9659    |   5.0681   |   0.1717   |


| Sigmoid | 基础性能 | 长文本信息保留与检索 | 信息压缩 |
| ------- | ---- | ---------- | ---- |
| 不用滑动窗口  |      |            |      |
| 用滑动窗口   |      |            |      |
| 不用滑动窗口  |      |            |      |
| 用滑动窗口   |      |            |      |


### 实验4: Attention Score Visualization

**目标**：分析滑动窗口训练后，模型attention的可解释问题。
**实验设置**：
- 对比attention score和输入文本之间的对应关系
**理论结果**：
- 滑动窗口训练的模型，attention sink从前几个token，变成了文本中间的任意关键token，比如换行符“\n”

### 实验5：Model Efficiency

**目标**：分析SWAT模型的训练和推理速度
**实验设置**：
- 在相同模型、窗口大小的情况下，测试vanilla transformer、SWAT transformer和streamingllm的训练、推理速度。
**理论结果**：
- 在训练时，streamingllm基于vanilla transformer。SWAT transformer由于使用SWA的原因，基于flash attention计算时比vanilla transformer要快一倍。
- 推理时，vanilla transformer为n^2的计算量，SWAT transformer和streamingllm都是n\*w，w是窗口大小，但是因为SWAT transformer没有拼接tokens的过程，因此会比streamingllm稍快一点。

这个写优化代码需要基于底层的cuda，因此暂时无法测定

| Window Size | Context Length | Training Time | Eval Time |
| ----------- | -------------- | ------------- | --------- |
| 32          | 256            | 583.94ms      |           |
| 256         | 256            |               |           |





## Related Work

### Efficient LLM Architectrues

To address the quadratic complexity of Transformers, researchers have proposed various efficient architectures. 

Linear Attention 
Linear attention variants achieve O(n) complexity through different approximations: Linear Transformer [Katharopoulos et al., 2020] replaces softmax attention with kernel functions, while Performer [Choromanski et al., 2021] employs random feature approximation. 后续的工作gla将遗忘机制引入线性注意力模型，防止信息爆炸。GATED DELTA NETWORKS将新的预测集中在记忆需要更新的部分，让模型既能保持精确的记忆更新能力,又能在需要时快速重置记忆。

Linear RNN
State Space Models like Mamba [Gu et al., 2023] and RWKV [Peng et al., 2023] avoid attention mechanisms entirely by utilizing recurrent structures.

Memory Transformer


### Efficient Transformers

Sparse attention models reduce computation by limiting attention patterns. Sparse Transformer [Child et al., 2019] introduces block sparsity, while Longformer [Beltagy et al., 2020] and BigBird [Zaheer et al., 2020] combine local windows with global tokens. Hybrid architectures such as FNet [Lee-Thorp et al., 2021] and Retentive Network [Sun et al., 2023] explore alternatives to self-attention through Fourier transforms and retention mechanisms.


1. 大语言模型的优化
	- 介绍Transformer在长文本处理中的计算瓶颈，以及为解决此问题提出的稀疏注意力、记忆机制等方法。  
2. 稀疏注意力
	- 详细回顾稀疏注意力相关研究，以及滑动窗口在推理中的应用。  

## Conclusion

1. **研究总结**  
	- 使用纯滑动窗口机制训练深层大模型的方法，解决了现有模型的attention sink的问题，同时也平衡了模型性能和计算效率。  
2. **主要贡献**  
	- blabla，同introduction。
1. **未来工作**  
	- 因为当前的滑动窗口训练使用了统一的位置编码，因此对于长短程信息的注意力都有相同的权重，未来可以考虑将不同范围的注意力分散到不同的注意力头上，使模型记忆更丰富的信息。
	- 将本方法应用于更多实际任务中，如长文本QA和总结。


# Possible challenges

- 这个工作和longformer的关系，longformer也是滑动窗口
	- 首先longformer仍然是bert系的模型，是双向注意力，而不是casual的。因此没有关于attention sink的实践。
	- 其次，longformer层数很少，并没有体现出滑动窗口的性能优势
- mistral模型用的就是滑动窗口
	- mistral的滑动窗口虽然参数开源了，但是几乎没有相关的实践分析，并且他们很快抛弃了这个技术
	- （如果alibi效果很好）mistral的模型仍然只能处理固定窗口大小，特定位置编码长度下的文本
	- （如果alibi效果很好）同时我们的模型速度更快一点，几乎已经压缩到了极致
- 已经有linear attention了，为什么还要这个方法
	- linear attention的attention的计算量是恒定的，无法扩展，但是我们的方法可以扩展更大的Window
	- 同时在短文本上，我们的模型计算也更快


# Experiments

## Overall Performance

原版transformer将训练长度作为窗口大小的好处
- 没有过拟合的情况

| 滑动窗口   | 相对位置编码   | 长文本能否泛化 |
| ------ | -------- | ------- |
| 不用滑动窗口 | 不用相对位置编码 | 否       |
| 用滑动窗口  | 不用相对位置编码 | 是       |
| 不用滑动窗口 | 用相对位置编码  | 是       |
| 用滑动窗口  | 用相对位置编码  | 是       |


## 滑动丁真，鉴定为纯纯的baseline


假设：滑动窗口128，训练长度1024，验证长度16384

那么baseline设置应为：训练时无窗口，长度1024；推理时有窗口，长度128，验证长度16384



## 滑动窗口：512


### Train: 3072; Val: 32768

210-219

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 512         | 8      | 3072         | 32768      |            |          |
| 512         | 12     | 3072         | 32768      |            |          |
| 512         | 16     | 3072         | 32768      | 3.27698    | 3.24785  |
| 512         | 24     | 3072         | 32768      | 3.20554    | 3.44778  |
| 512         | 36     | 3072         | 32768      | 3.14282    | 3.46975  |



### Train: 2560; Val: 32768

210-219

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 512         | 8      | 2560         | 32768      | 3.43962    | 3.47338  |
| 512         | 12     | 2560         | 32768      | 3.33871    | 3.42149  |
| 512         | 16     | 2560         | 32768      | 3.27799    | 3.48548  |
| 512         | 24     | 2560         | 32768      | 3.20384    | 3.82643  |
| 512         | 36     | 2560         | 32768      | 3.14162    | 3.79894  |

### Train: 2048; Val: 32768

210-219

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 512         | 8      | 2048         | 32768      |            |          |
| 512         | 12     | 2048         | 32768      |            |          |
| 512         | 16     | 2048         | 32768      |            |          |
| 512         | 24     | 2048         | 32768      |            |          |
| 512         | 36     | 2048         | 32768      | 3.14535    | 5.4548   |





### Train: 1024; Val: 2048

60-65

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 512         | 2      | 1024         | 2048       | 4.16036    | 4.15458  |
| 512         | 4      | 1024         | 2048       | 3.68238    | 4.29016  |
| 512         | 8      | 1024         | 2048       | 3.39794    | 4.15933  |
| 512         | 12     | 1024         | 2048       | 3.32812    | 4.19336  |
| 512         | 24     | 1024         | 2048       | 3.17496    | 4.03339  |
| 512         | 36     | 1024         | 2048       | 3.1332     | 3.94957  |





## 滑动窗口：256

### Layers: 12; Val: 2048

40-47

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 12     | 256          | 2048       | 3.42564    | 10.78453 |
| 256         | 12     | 512          | 2048       | 3.36263    | 4.84771  |
| 256         | 12     | 768          | 2048       | 3.34359    | 4.40134  |
| 256         | 12     | 1024         | 2048       | 3.3622     | 3.50397  |
| 256         | 12     | 1280         | 2048       | 3.36105    | 3.35864  |
| 256         | 12     | 1536         | 2048       | 3.35952    | 3.3546   |
| 256         | 12     | 1792         | 2048       | 3.34065    | 3.34835  |
| 256         | 12     | 2048         | 2048       | 3.35283    | 3.35992  |


### Train: 2560; Val: 32768

201

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 10     | 1280         | 2048       |            |          |
| 256         | 12     | 1280         | 2048       |            |          |
| 256         | 18     | 1280         | 2048       |            |          |
| 256         | 20     | 1280         | 2048       |            |          |
| 256         | 24     | 1280         | 2048       |            |          |
| 256         | 36     | 1280         | 2048       | 3.16471    | 3.16447  |
| 256         | 40     | 1280         | 2048       |            |          |
| 256         | 44     | 1280         | 2048       |            |          |
| 256         | 48     | 1280         | 2048       |            |          |



### Train: 1280; Val: 2048

120-128

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 10     | 1280         | 2048       | 3.38644    | 3.39689  |
| 256         | 12     | 1280         | 2048       | 3.36105    | 3.35864  |
| 256         | 18     | 1280         | 2048       | 3.27066    | 3.3098   |
| 256         | 20     | 1280         | 2048       | 3.26174    | 3.29119  |
| 256         | 24     | 1280         | 2048       | 3.2214     | 3.25807  |
| 256         | 36     | 1280         | 2048       | 3.16794    | 3.22473  |
| 256         | 40     | 1280         | 2048       | 3.14309    | 3.20571  |
| 256         | 44     | 1280         | 2048       | 3.14137    | 3.21288  |
| 256         | 48     | 1280         | 2048       | 3.13797    | 3.41579  |


### Train: 1024; Val: 2048

111-115

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 4      | 1024         | 2048       | 3.67824    | 3.66411  |
| 256         | 6      | 1024         | 2048       | 3.53066    | 3.53088  |
| 256         | 8      | 1024         | 2048       | 3.449      | 3.49393  |
| 256         | 10     | 1024         | 2048       | 3.38925    | 3.48962  |
| 256         | 12     | 1024         | 2048       | 3.3622     | 3.50397  |
| 256         | 13     | 1024         | 2048       | 3.33116    | 3.58842  |
| 256         | 14     | 1024         | 2048       | 3.3315     | 3.87566  |
| 256         | 16     | 1024         | 2048       | 3.28525    | 3.63399  |
| 256         | 18     | 1024         | 2048       | 3.26763    | 3.67015  |





### Train: 768; Val: 2048

140-143

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 2      | 768          | 2048       | 4.10489    | 4.08787  |
| 256         | 3      | 768          | 2048       | 3.79363    | 3.77111  |
| 256         | 4      | 768          | 2048       | 3.70949    | 3.73329  |
| 256         | 5      | 768          | 2048       | 3.59408    | 3.8204   |
| 256         | 6      | 768          | 2048       | 3.54206    | 4.22443  |
| 256         | 7      | 768          | 2048       | 3.48148    | 4.26603  |
| 256         | 8      | 768          | 2048       | 3.42859    | 4.25381  |
| 256         | 10     | 768          | 2048       | 3.38145    | 4.34246  |
| 256         | 12     | 768          | 2048       | 3.34293    | 4.41327  |




## 滑动窗口：128

### Layers: 24; Val: 1024

90-97

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 128         | 24     | 128          | 1024       | 3.44405    | 8.06713  |
| 128         | 24     | 256          | 1024       | 3.34954    | 4.8208   |
| 128         | 24     | 384          | 1024       | 3.29534    | 4.41921  |
| 128         | 24     | 512          | 1024       | 3.28056    | 4.09327  |
| 128         | 24     | 640          | 1024       | 3.28963    | 3.29574  |
| 128         | 24     | 768          | 1024       | 3.27668    | 3.28243  |
| 128         | 24     | 896          | 1024       | 3.25552    | 3.26023  |
| 128         | 24     | 1024         | 1024       | 3.28897    | 3.28608  |



### Train: 768; Val: 1024

180-189


| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 128         | 24     | 768          | 1024       |            |          |
| 128         | 36     | 768          | 1024       | 3.20924    | 3.2314   |
| 128         | 42     | 768          | 1024       |            |          |
| 128         | 48     | 768          | 1024       |            |          |
| 128         | 60     | 768          | 1024       |            |          |
| 128         | 66     | 768          | 1024       |            |          |
| 128         | 72     | 768          | 1024       | 3.14092    | 3.1743   |
| 128         | 84     | 768          | 1024       |            |          |




### Train: 640; Val: 1024

170-176


| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 128         | 24     | 640          | 1024       | 3.27913    | 3.29857  |
| 128         | 36     | 640          | 1024       | 3.20924    | 3.2314   |
| 128         | 42     | 640          | 1024       | 3.21099    | 3.2457   |
| 128         | 48     | 640          | 1024       | 3.18473    | 3.21908  |
| 128         | 60     | 640          | 1024       | 3.16777    | 3.21377  |
| 128         | 66     | 640          | 1024       | 3.1536     | 3.17603  |
| 128         | 72     | 640          | 1024       | 3.14092    | 3.1743   |
| 128         | 84     | 640          | 1024       | 3.13749    | 3.34921  |



### Train: 512; Val: 2048

160-162

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 128         | 10     | 512          | 1024       | 3.43861    | 3.50487  |
| 128         | 12     | 512          | 1024       | 3.40522    | 3.50113  |
| 128         | 14     | 512          | 1024       | 3.37066    | 3.66947  |




## 训练长度：4096

100-104

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 256         | 24     | 4096         | 4096       | 3.36485    | 3.3679   |
| 512         | 24     | 4096         | 4096       | 3.343      | 3.35077  |
| 1024        | 24     | 4096         | 4096       | 3.32666    | 3.35528  |
| 2048        | 24     | 4096         | 4096       | 3.31103    | 3.33301  |
| 4096        | 24     | 4096         | 4096       | 3.32118    | 3.34436  |



## Vanilla Transformer

### Layers: 24; Val: 1024

83-85

| Window Size | Layers | Train Length | Val Length | Train Loss | Val Loss |
| ----------- | ------ | ------------ | ---------- | ---------- | -------- |
| 128         | 24     | 128          | 1024       |            |          |
| 256         | 24     | 256          | 1024       |            |          |
| 384         | 24     | 384          | 1024       | 3.27406    | 5.95275  |
| 512         | 24     | 512          | 1024       |            |          |
| 640         | 24     | 640          | 1024       | 3.22074    | 4.18709  |
| 768         | 24     | 768          | 1024       |            |          |
| 896         | 24     | 896          | 1024       |            |          |
| 1024        | 24     | 1024         | 1024       |            |          |

```
rope_config = Qwen2Config(  
    n_embd=config.n_embd,  
    n_head=config.n_head,  
    max_position_embeddings=config.max_position_embeddings,  
    rope_scaling={  
        "rope_type": "default",  
        "factor": 1.0,  
        "dim": config.n_embd,  
        "base": 10000,  
        "max_position_embeddings": config.max_position_embeddings,  
    },  
)  
self.rotary_emb = Qwen2RotaryEmbedding(config=rope_config)
```












```shell
python evaluate.py \
  --model_name_or_path meta-llama/Llama-2-7b-hf \
  --dataset_name emozilla/pg19 \
  --split test \
  --num_samples 1 \
  --output_dir ./output \
  --enable_start_recent_kv_cache \
  --start_size 10 \
  --recent_size 1024 \
  --enable_pos_shift \
  --num_eval_tokens 8192
```


```
python eval_long_ppl.py --model_name_or_path meta-llama/Llama-2-7b-hf --dataset_name emozilla/pg19 --split test --num_samples 1 --output_dir ./output --enable_start_recent_kv_cache --start_size 10 --recent_size 1024 --enable_pos_shift --num_eval_tokens 8192
```


```
python eval_long_ppl.py --model_name_or_path meta-llama/Llama-2-7b-hf --dataset_name emozilla/pg19 --split test --num_samples 1 --output_dir ./output --recent_size 1024 --enable_pos_shift --num_eval_tokens 8192
```






