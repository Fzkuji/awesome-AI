Title: Llama 2: Open Foundation and Fine-Tuned Chat Models

Link: https://ai.meta.com/research/publications/llama-2-open-foundation-and-fine-tuned-chat-models/

Code: https://github.com/facebookresearch/llama

Additional information: 

- [更强的Llama 2开源，可直接商用：一夜之间，大模型格局变了](https://mp.weixin.qq.com/s/klFWFXCbjGaWZ7HO1KFZag)
- [Llama2技术细节&开源影响](https://mp.weixin.qq.com/s/rHJkJw9TFGaAR8bWDM5wmg)

Conclusion: 


## Abstract



## Introduction

### Background



### Problems



### Contributions



## Methods

### Pre-training

模型结构为Transformer结构，与Llama相同的是采用RMSNorm归一化、SwiGLU激活函数、RoPE位置嵌入、词表的构建与大小，与Llama不同的是**增加GQA（分组查询注意力）**，扩增了模型输入最大长度，语料库增加了40%。

训练超参数如下：AdamW优化器的β1、β2和eps分别为0.9、0.95和10e-5，采用cosin学习率，预热2000步后进行学习率衰减，最终降至峰值的10%，权重衰减系数为0.1，梯度裁剪值为1.0。

但请注意：7b和13b模型并没有增加GQA！！！

预训练模型效果一句话总结：**「开源第一，闭源一个没打过。」**

#### Grouped query attention

GQA（Grouped Query Attention）是一种新型的注意力机制，它主要用于自然语言处理和机器学习中的模型。这种机制的主要目标是提高模型处理长序列数据的能力，特别是在处理文本或者语音等序列数据时。

传统的注意力机制，例如在Transformer模型中使用的自注意力机制（Self-Attention），在处理长序列数据时会遇到一些问题。因为自注意力机制需要计算序列中每个元素与其他所有元素的关系，所以它的计算复杂度是O(n^2)，其中n是序列的长度。当序列非常长时，这种计算复杂度会变得非常高，导致模型难以处理。

GQA则是为了解决这个问题而提出的。在GQA中，输入序列被分成多个组（Group），每个组内的元素会进行自注意力计算，而不同组之间则进行全局注意力计算。这样，每个元素不再需要与所有其他元素进行交互，而只需要与同组的元素和其他组的代表性元素进行交互。这大大降低了计算复杂度，使得模型能够处理更长的序列。

具体来说，GQA的计算过程可以分为以下几步：

1. 将输入序列分组：输入序列被均匀地分成多个组，每个组包含一部分连续的元素。
2. 计算组内注意力：每个组内的元素进行自注意力计算，得到组内的上下文表示。
3. 计算组间注意力：使用全局注意力机制，计算不同组之间的关系，得到组间的上下文表示。
4. 合并组内和组间的上下文表示：将组内和组间的上下文表示合并，得到最终的输出序列。

通过这种方式，GQA能够有效地处理长序列数据，同时保持了注意力机制的优点，例如能够捕捉序列中的长距离依赖关系。

### Fine-tuning

#### Supervised fine-tuning

**「Data Quality Is All You Need。」** MetaAI进行实验时发现，少量高质量数据集训练模型的效果，要好于大量低质量数据集的训练效果。因此以后SFT时候，不要一味地追求量，质更重要。

#### Reward model

奖励模型的初始化来自于pretrained chat model检查点，将下一个Token预测分类器替换成标量奖励值回归器。

## Evaluation

作者如何评估自己的方法？实验的setup是什么样的？感兴趣实验数据和结果有哪些？有没有问题或者可以借鉴的地方？

  

## Conclusion

作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。




## Appendices

### Appendix A



### Appendix B



## Future work

值得研究的点








