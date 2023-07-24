Title: Llama 2: Open Foundation and Fine-Tuned Chat Models

Link: https://ai.meta.com/research/publications/llama-2-open-foundation-and-fine-tuned-chat-models/

Code: https://github.com/facebookresearch/llama

Additional information: 

Conclusion: 


## Abstract



## Introduction

### Background



### Problems



### Contributions



## Methods

模型结构为Transformer结构，与Llama相同的是采用RMSNorm归一化、SwiGLU激活函数、RoPE位置嵌入、词表的构建与大小，与Llama不同的是增加GQA（分组查询注意力），扩增了模型输入最大长度，语料库增加了40%。

训练超参数如下：AdamW优化器的β1、β2和eps分别为0.9、0.95和10e-5，采用cosin学习率，预热2000步后进行学习率衰减，最终降至峰值的10%，权重衰减系数为0.1，梯度裁剪值为1.0。

但请注意：7b和13b模型并没有增加GQA！！！



  

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








