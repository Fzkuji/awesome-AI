# Scaling laws

Scaling laws是一系列的法则，先由OpenAI提出[^1]。其中列举了影响模型性能最大的三个因素：**计算量**、**数据集大小**、**模型参数量**。这一结论后来也被DeepMind的研究得出来[^2]。

根据Scaling Laws，模型越大、数据量越大、计算量越大，模型效果也越好。同时，当其他因素不成为瓶颈时，计算量、数据集大小、模型参数量这3个因素中的单个因素指数增加时，loss会线性的下降。


[^1]: Scaling Laws for Neural Language Models [https://arxiv.org/abs/2001.08361](https://arxiv.org/abs/2001.08361)
[^2]: Training Compute-Optimal Large Language Models [https://arxiv.org/abs/2203.15556](https://arxiv.org/abs/2203.15556)