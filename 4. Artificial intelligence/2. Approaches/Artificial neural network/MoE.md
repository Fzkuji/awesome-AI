# Mixture of experts (MoE)

Mixture of Experts (MoE) 模型是一种机器学习架构，它结合了多个“专家”网络来提高整体模型的性能和效率。MoE 模型的核心思想是将复杂的任务分解为多个子任务，由不同的专家网络（即小型模型）来处理。这些专家网络通常是特定类型的神经网络，比如全连接层或卷积层。

MoE 模型通常由以下几个主要部分组成：

1. **门控机制（Gating Network）**：这是 MoE 模型的一个关键组成部分。门控机制负责决定每个输入数据应该由哪个或哪些专家来处理。它基于输入数据的特征来动态分配任务给不同的专家，以此来优化整个模型的学习和预测效果。
2. **专家网络（Expert Networks）**：这些是模型中实际处理数据的部分。每个专家网络都被训练来处理特定类型的数据或任务。在 MoE 模型中，可以有任意数量的专家，而每个专家都可以是一个独立的神经网络。
3. **聚合层（Combining Layer）**：聚合层的作用是整合来自不同专家网络的输出。根据门控机制的分配和每个专家的输出，聚合层合成最终的输出。

MoE 模型的优势在于其灵活性和扩展性。由于可以动态地调整专家网络的数量和类型，MoE 模型可以有效地处理大规模和复杂的数据集。此外，通过并行处理不同的专家网络，MoE 模型还可以提高计算效率。

在实际应用中，MoE 模型常用于处理需要大量计算资源的任务，如语言模型、图像识别和复杂的预测问题。通过将大型问题分解为更小、更易管理的子问题，MoE 模型能够提供更高效和精确的解决方案。

在MoE模型中，不同的gating机制对于如何分配输入到各个专家至关重要。以下是一些常见的门控机制及其基本原理，以及如何在 Python 中实现这些机制的简要介绍：
1. Soft Gating
	- **原理**：软门控使用 softmax 函数为每个专家分配权重，这些权重是基于输入数据计算的。这意味着每个输入可以由多个专家以不同的权重共同处理。
	- **Python 实现**：可以通过定义一个带有 softmax 激活函数的全连接层来实现。
2. Hard Gating
	- **原理**：硬门控通常使用 argmax 函数或类似的机制选择最合适的专家。每个输入只被一个专家处理。
	- **Python 实现**：使用 argmax 函数确定权重最高的专家，然后将输入只送给这个专家。
3. Top-K Gating
	- **原理**：这种方法结合了软门控和硬门控的特点。它选择前 K 个最佳专家处理每个输入，但与硬门控不同，这些专家可以共同处理输入。
	- **Python 实现**：先用 softmax 计算权重，然后选出最高的 K 个权重对应的专家。
4. Learned Gating
	- **原理**：这种机制通过训练来学习如何分配输入到专家。它可以是基于神经网络的，其参数通过反向传播进行优化。
	- **Python 实现**：定义一个神经网络层来学习输入和专家之间的关系。


在 Mixture of Experts (MoE) 模型中，定义合适的损失函数（loss function）是关键，因为它不仅需要评估模型的预测准确性，还可能需要考虑如何平衡专家之间的负载。以下是一些在 MoE 模型中常见的损失函数类型及其特点：
1. 标准损失函数
	- **描述**：对于基本的监督学习任务（如分类或回归），MoE 模型可以使用标准的损失函数，例如交叉熵损失（对于分类任务）或均方误差损失（对于回归任务）。
	- **实现**：这些损失函数与普通的神经网络相同，基于模型输出和真实标签计算损失。
2. 负载平衡损失
	- **描述**：在 MoE 模型中，重要的是要确保所有专家都得到适当的利用。负载平衡损失有助于防止某些专家被过度使用，而其他专家则很少使用。
	- **实现**：可以通过计算每个专家的使用率并将其纳入总损失来实现。例如，可以使用专家的门控概率之和来衡量其使用率，并尝试最小化使用率之间的差异。

MoE可能面临的问题：
1. 训练复杂性：混合专家模型的训练相对复杂，尤其是涉及到门控网络的参数调整。为了正确地学习专家的权重和整体模型的参数，可能需要更多的训练时间。
2. 超参数调整：选择适当的超参数，特别是与门控网络相关的参数，以达到最佳性能，是一个复杂的任务。这可能需要通过交叉验证等技术进行仔细调整。
3. 专家模型设计：专家模型的设计对模型的性能影响显著。选择适当的专家模型结构，确保其在特定任务上有足够的表现力，是一个挑战。
4. 稀疏性失真：在某些情况下，为了实现稀疏性，门控网络可能会过度地激活或不激活某些专家，导致模型性能下降。需要谨慎设计稀疏性调整策略，以平衡效率和性能。
5. 动态性问题：在处理动态或快速变化的数据分布时，门控网络可能需要更加灵活的调整，以适应输入数据的变化。这需要额外的处理和设计。
6. 对数据噪声的敏感性：混合专家模型对于数据中的噪声相对敏感，可能在一些情况下表现不如其他更简单的模型。
7. 通信宽带瓶颈的问题：要涉及到混合专家模型的分布式部署，其中不同的专家模型或门控网络可能分布在不同的计算节点上。在这种情况下，模型参数的传输和同步可能导致通信开销过大，成为性能的一个瓶颈。

缓解通信瓶颈的策略：
- 模型剪枝和量化：减小模型的大小，包括专家模型和门控网络的参数数量，以降低通信开销。
- 异步更新：考虑采用异步更新策略，而不是同步地更新所有节点的参数。这可以减少通信开销，但可能导致模型的一致性稍有降低。
- 本地计算：尽可能在本地计算节点上完成任务，减少节点之间的通信需求。这可以通过在节点上部署更多的计算资源来实现。
- 压缩技术：使用参数压缩技术，如模型压缩或渐进压缩算法，以减小传输的数据量。

# LLM MoE

## Motivation

Moore's law and [Scaling laws](Scaling%20laws.md)

根据Scaling Laws，模型越大、数据量越大、计算量越大，模型效果也越好。因此，目前很多LLM都是朝着更多参数、更多训练数据的方向进行scaling。然而，随着摩尔定律走到了尽头，LLM也不可能做到无限大。那么计算受到限制的时候，该如何进一步提升模型的性能呢？其中一种方法是MoE。


## Related works

### Sparse MoE

ICLR 2017

[Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer](https://openreview.net/forum?id=B1ckMDqlg) 

Google Brain的Shazeer、Noam等人提出使用稀疏的MoE结构来将模型容量做大的方法，即：训练时使用海量的专家模型，推理时激活少数专家模型。

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/MoE/IMG-20240214165837677.png)

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


### Brainformers

[Brainformers: Trading Simplicity for Efficiency](https://arxiv.org/abs/2306.00008)

ICLR 2023 Withdrawn Submission

上述MoE模型，在训练速度很慢，会成为进一步 scaling 的瓶颈。Google进一步提出了Brainformers。主要方法是：
- 复合结构的低秩矩阵+Expert
- 神经架构搜索

为了模型计算更快，需要对矩阵乘法进行分解，从而减少计算量。同时，这些矩阵分解，必须不能损害模型的准确性。上图 (a) 中，是两种分解矩阵乘法的主要方法，分别是从横向分解（low-rank）和纵向分解（multi-expert） 。而在图 (b)中，可以对 low-rank 和 multi-expert 进行组合、堆叠，以实现更有趣且计算效率高的模型架构。

如果在 bottleneck 处，插入一个 mixure 层，模型看起来就非常像 transformers。如果在 bottleneck 处，插入一个 attention layer，模型看起来就非常像一个 multi-expert transformers。

作者对不同的参数，进行了搜索，从而找到了最优的网络模型结果。搜索的空间包括：不同的层类型（attn、moe、ffn）、隐层维度、MoE隐层维度、FFN隐层维度、attention 的 head 数、Gating Fuction、Capacity Factor、Activation Function等。

从搜索空间中，采样一组参数，构建一个 100M/32E 的模型，选择top-K的模型，然后进行scaling，如1B/64E 、8B/64E。



# Lifelong MoE


## Related works


### Lifelong-MoE

[Lifelong Language Pretraining with Distribution-Specialized Experts](https://dl.acm.org/doi/10.5555/3618408.3618621)

挑战：进行增量训练，同时避免灾难性遗忘。
方法：引入新的数据分布时，引入新的 experts，同时冻结原有的权重，并且加入一个regularization loss 避免灾难性遗忘。

![](../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/MoE/IMG-20240214165837705.png)

### PanGu-Sigma

[PanGu-Σ: Towards Trillion Parameter Language Model with Sparse Heterogeneous Computing](https://arxiv.org/abs/2303.10845)

主要设计是顶层的transformer layers使用了MoE，并且是可以手动gating的设计（其实我觉得并没有什么含金量）。


