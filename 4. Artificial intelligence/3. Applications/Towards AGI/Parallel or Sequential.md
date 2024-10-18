# Parallel or Sequential? Attention or Recurrence?

很明显，对于实现AGI的模型框架，学术界仍然争论不休，主要的矛盾就在于并行化的速度需求和序列化的推理需求之间的冲突。虽然Transformer在这两年，因为其在海量的文本数据上训练而获得强大的对话能力而吸引了所有人的眼球，但是其带来的$\mathcal{O}(n^2)$级别的计算量逐渐成为了scaling的瓶颈，因此RNN这种只有$\mathcal{O}(n)$级别计算量的模型又重新被人们拾起，如TTT、mamba、RWKV等等。

当然，由此也引发出了无数的研究话题和方向：

探索降低Transformer复杂度
[Scaled-Dot Attention](https://papers.cool/arxiv/1706.03762) or Linear Attention

刀刀宁：[笔记：简单图解一下线性注意力机制](https://zhuanlan.zhihu.com/p/718156896)

[线性Attention的探索：Attention必须有个Softmax吗？](https://spaces.ac.cn/archives/7546)去掉Softmax的Attention的复杂度可以降到最理想的线性级别。

以下是Transformer的一些身份：
- Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention
- Transformers are Multi-State RNNs
- Transformers are SSMs: Generalized Models and Efficient Algorithms Through Structured State Space Duality


根据目前Transformer的局限，可以猜测Transformer的推理能力和层数有关（有待实验），因此可以关注DeepNet: Scaling Transformers to 1,000 Layers这篇论文，还有老苏的文章[训练1000层的Transformer究竟有什么困难？](https://kexue.fm/archives/8978)

这篇论文Retentive Network: A Successor to Transformer for Large Language Models中的RetNet也是对标Transformer

关于RWKV7，PENG Bo吹嘘：超越Attention范式，是可明确定义的概念。因为从前的RWKV-1到[RWKV-6](https://zhida.zhihu.com/search?content_id=693668943&content_type=Answer&match_order=1&q=RWKV-6&zhida_source=entity)（以及 Mamba1 Mamba2 等等模型）都可以写成[Linear Attention](https://zhida.zhihu.com/search?content_id=693668943&content_type=Answer&match_order=1&q=Linear+Attention&zhida_source=entity)，但是RWKV-7就超越了所有[attention](https://zhida.zhihu.com/search?content_id=693668943&content_type=Answer&match_order=1&q=attention&zhida_source=entity)的表达力。前几个月的DeltaNet和TTT也已经在做这种尝试。在[The Illusion of State in State-Space Models](https://papers.cool/arxiv/2404.08819)对此有介绍。
