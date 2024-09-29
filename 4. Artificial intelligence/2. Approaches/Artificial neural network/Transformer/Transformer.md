# Transformer



## Related works


### 模型设计

看这些论文的时候我在思考transformer的加速推理问题，然后思考transformer的层数问题。但是很快我就发现了一些针对深度学习模型的加速硬件，可以把推理速度提升几百倍（比如[groq](https://groq.com)，每秒预测500个token），于是感觉架构的思考也没什么必要了，反正计算速度问题都会通过硬件解决。

schuBERT: Optimizing Elements of BERT

A Study on Transformer Configuration and Training Objective

Experiments And Discussions On Vision Transformer (ViT) Parameters For Object Tracking

Rethinking the role of scale for in-context learning: An interpretability-based case study at 66 billion scale

## Long context



[transformer如何处理长文本？](https://www.zhihu.com/question/635395282)为解决预训练过程中的长文本问题，思路主要有以下几个方面：

- 并行化计算，典型方法如 [sequence parallelism](https://arxiv.org/abs/2105.13120)
- 优化attention机制，典型方法如 [Transformer-XL](https://arxiv.org/abs/1901.02860), [Longformer](https://arxiv.org/abs/2004.05150)
- 引入[memory机制](Memory%20transformer.md)，典型方法如 [Focused Transformer](https://arxiv.org/abs/2307.03170), [Memorizing Transformer](https://arxiv.org/abs/2203.08913)
- 引入采样机制，典型方法如 [Hierarchical transformers](https://arxiv.org/abs/2110.13711), [Dynamic-Pooling Transformer](https://arxiv.org/abs/2211.09761)

## Encoder or decoder

这是一个关于transformer架构的思考，transformer被提出时是包含了encoder和decoder两个部分的，最开始BERT使用encoder-only做分类任务效果很好。后来GPT性能逐渐被挖掘，主流模型又开始用decoder-only的架构，由此引发了下面的讨论：

[【知乎】为什么现在的LLM都是Decoder only的架构？](https://www.zhihu.com/question/588325646)

里面总结的原因有很多，有工程实现上的、有效果上的、也有从理论角度分析的。我比较关注效果和理论，其中苏建林提到，decoder-only因为使用了casual attention，因此注意力矩阵是满秩的，因此效果更好，我决定在设计模型的时候考虑这个因素。

另外，苏建林在他的博客里还提到了casual attention使用RoPE相对位置嵌入的一个有意思的观察，具体文章[在这](https://kexue.fm/archives/9948)，就是**如果在推理过程中，在迭代的时候删除了最前面的几个token，那么性能就会大幅下降**。

> 1、**开头的几个Token是绝对位置的“锚点”**：顾名思义，相对位置编码原则上只能识别相对位置，但有些任务可能比较依赖绝对位置，通过开头几个绝对位置约等于0的Token作为“标的”，每个Token就能够测出自己的绝对位置，而去掉开头几个Token后则缺失了这一环，从而完全打乱了注意力模式导致PPL爆炸；
> 
 > 2、**开头的几个Token是注意力的“回收站”**：由于注意力求和为1，所以注意力一定会分配到某些Token上，但有些情况下模型可能会发现“没什么Token值得注意的”，这时它选择将一部分注意力放到没什么信息量的前几个Token上，起到“不注意”的作用，去掉它们后模型会强行将注意力分配到其他无关的Token，从而扰乱了注意力模式。

我觉得这个观察非常有趣，在设计transformer的记忆能力的时候可以考虑这个因素。











## 奇怪的想法

### 层级的s

