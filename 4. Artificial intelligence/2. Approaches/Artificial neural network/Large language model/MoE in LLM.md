# MoE in LLM

## Motivation

Moore's law and [Scaling laws](Scaling%20laws.md)

根据Scaling Laws，模型越大、数据量越大、计算量越大，模型效果也越好。因此，目前很多LLM都是朝着更多参数、更多训练数据的方向进行scaling。然而，随着摩尔定律走到了尽头，LLM也不可能做到无限大。那么计算受到限制的时候，该如何进一步提升模型的性能呢？其中一种方法是MoE。


## Related works

### Sparse MoE

ICLR 2017

[Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer](https://openreview.net/forum?id=B1ckMDqlg) 

Google Brain的Shazeer、Noam等人提出使用稀疏的MoE结构来将模型容量做大的方法，即：训练时使用海量的专家模型，推理时激活少数专家模型。

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/MoE%20in%20LLM/IMG-20240212110904940.png)

### Multi-gate Mixture-of-Experts (MMoE)

KDD 2018

[Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts](https://dl.acm.org/doi/10.1145/3219819.3220007)


### Transformer MoE

当模型参数量到了千亿这个级别以后，再想向上扩展变得愈发困难，经济实用的MoE又被重启。还是Google，提出了GShard，首个将MoE思想拓展到Transformer的工作，而后Switch Transformer、GLaM等工作持续改进着Transformer MoE的结构，也将LLM的参数量从千亿推向了万亿级别。

#### GShard

ICLR 2021

[GShard: Scaling Giant Models with Conditional Computation and Automatic Sharding](https://openreview.net/forum?id=qrwe7XHTmYb)

首个MoE+Transformer模型：Transformer的encoder和decoder中，每隔一个（every other）FFN层，替换成position-wise MoE层。

#### Switch Transformer

JMLR 2022

[Switch transformers: scaling to trillion parameter models with simple and efficient sparsity](https://dl.acm.org/doi/abs/10.5555/3586589.3586709)

2021年1月，Google大脑团队发布Switch Transformer，其简化了MoE的routing算法，并且gating network每次只route到1个expert。

#### GlaM

[GLaM: Efficient Scaling of Language Models with Mixture-of-Experts](https://proceedings.mlr.press/v162/du22c.html)

同年，Google的GlaM模型表明，Transformer和MoE风格的层可以组合在一起生成一个模型，在29个基准测试中平均超过GPT-3模型的精度，而使用3倍少的能耗进行训练和2倍少的计算进行推理。


#### Expert Choice Routing

[Mixture-of-Experts with Expert Choice Routing](https://proceedings.neurips.cc/paper_files/paper/2022/hash/2f00ecd787b432c1d36f3de9800728eb-Abstract-Conference.html)

GLaM虽然效果不错，但是有负载不均衡问题。也就是说，会有一部分expert会经常被激活，而有一些expert很少被激活。

2022年，在NeurIPS上，Google提出了新的Expert Choice Routing方法，来解决负载不均衡问题。该方法中，每个expert 会独立选择top-k的tokens作为输入。每个token都可能会被不同的expert选择。一些比较重要的tokens会得到更多的计算资源，而不重要的tokens得到的计算资源会比较少。

采用Expert Choice Routing的模型，相比于GLaM，在收敛速度方面可以提升2倍， 在step time上提速20%，并且完美解决了负载不均衡问题。8B/64E的模型（有9.8B激活的参数），在SuperGLUE上效果超过T5-11B的模型。


#### Brainformers

[Brainformers: Trading Simplicity for Efficiency](https://arxiv.org/abs/2306.00008)

ICLR 2023 Withdrawn Submission

上述MoE模型，在训练速度很慢，会成为进一步 scaling 的瓶颈。Google进一步提出了Brainformers。主要方法是符合结构的

为了模型计算更快，需要对矩阵乘法进行分解，从而减少计算量。同时，这些矩阵分解，必须不能损害模型的准确性。上图 (a) 中，是两种分解矩阵乘法的主要方法，分别是从横向分解（low-rank）和纵向分解（multi-expert） 。而在图 (b)中，可以对 low-rank 和 multi-expert 进行组合、堆叠，以实现更有趣且计算效率高的模型架构。

如果在 bottleneck 处，插入一个 mixure 层，模型看起来就非常像 transformers。如果在 bottleneck 处，插入一个 attention layer，模型看起来就非常像一个 multi-expert transformers。

作者对不同的参数，进行了搜索，从而找到了最优的网络模型结果。搜索的空间包括：不同的层类型（attn、moe、ffn）、隐层维度、MoE隐层维度、FFN隐层维度、attention 的 head 数、Gating Fuction、Capacity Factor、Activation Function等。

从搜索空间中，采样一组参数，构建一个 100M/32E 的模型，选择top-K的模型，然后进行scaling，如1B/64E 、8B/64E。



