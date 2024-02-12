# MoE in LLM

## Motivation

Moore's law and [Scaling laws](Scaling%20laws.md)

根据Scaling Laws，模型越大、数据量越大、计算量越大，模型效果也越好。因此，目前很多LLM都是朝着更多参数、更多训练数据的方向进行scaling。然而，随着摩尔定律走到了尽头，LLM也不可能做到无限大。那么计算受到限制的时候，该如何进一步提升模型的性能呢？其中一种方法是MoE。


## Related works

### Sparse MoE

ICLR 2017

[Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer](https://openreview.net/forum?id=B1ckMDqlg) 

Google Brain的Shazeer、Noam等人提出使用稀疏的MoE结构来将模型容量做大的方法，即：训练时使用海量的专家模型，推理时激活少数专家模型。

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/MoE/IMG-20240212100316566.png)

### Multi-gate Mixture-of-Experts (MMoE)

KDD 2018

[Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts](https://dl.acm.org/doi/10.1145/3219819.3220007)


### Transformer MoE

当模型参数量到了千亿这个级别以后，再想向上扩展变得愈发困难，经济实用的MoE又被重启。还是Google，提出了GShard，首个将MoE思想拓展到Transformer的工作，而后Switch Transformer、GLaM等工作持续改进着Transformer MoE的结构，也将LLM的参数量从千亿推向了万亿级别。

#### GShard

ICLR 2021

[GShard: Scaling Giant Models with Conditional Computation and Automatic Sharding](https://openreview.net/forum?id=qrwe7XHTmYb)

首个MoE+Transformer模型：Transformer的encoder和decoder中，每隔一个（every other）FFN层，替换成[position-wise](https://www.zhihu.com/search?q=position-wise&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22665459559%22%7D) MoE层。


##Switch Transformer




