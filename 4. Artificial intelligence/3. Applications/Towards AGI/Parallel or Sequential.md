# Parallel or Sequential? Attention or Recurrence?

很明显，对于实现AGI的模型框架，学术界仍然争论不休，主要的矛盾就在于并行化的速度需求和序列化的推理需求之间的冲突。虽然Transformer在这两年，因为其在海量的文本数据上训练而获得强大的对话能力而吸引了所有人的眼球，但是其带来的$\mathcal{O}(n^2)$级别的计算量逐渐成为了scaling的瓶颈，因此RNN这种只有$\mathcal{O}(n)$级别计算量的模型又重新被人们拾起，如TTT、mamba、RWKV等等。

当然，由此也引发出了无数的研究话题和方向：

探索降低Transformer复杂度
[Scaled-Dot Attention](https://papers.cool/arxiv/1706.03762) or Linear Attention


[线性Attention的探索：Attention必须有个Softmax吗？](https://spaces.ac.cn/archives/7546)去掉Softmax的Attention的复杂度可以降到最理想的线性级别。

Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention