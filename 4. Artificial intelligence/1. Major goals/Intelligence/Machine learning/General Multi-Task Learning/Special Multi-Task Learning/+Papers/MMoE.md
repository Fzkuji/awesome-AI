Title: Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts

Authors: Jiaqi Ma, Zhe Zhao, Xinyang Yi, Jilin Chen, Lichan Hong, Ed H. Chi

Affiliation: Jiaqi Ma - School of Information, University of Michigan, Ann Arbor

Link: https://dl.acm.org/doi/10.1145/3219819.3220007

Code: https://github.com/shenweichen/DeepCTR-Torch

Keywords: multi-task learning, mixture of experts, neural network, recommendation system

Additional information: 虽然MMoE模型主要用于多任务学习，但它也可以用于多场景建模。例如，如果每个场景可以被视为一个独立的任务，那么MMoE模型就可以用来在多个场景之间共享信息。然而，这种应用并不改变MMoE模型主要属于多任务学习的事实。

Conclusion: 


## Abstract

本文提出了一种新颖的多门混合专家模型（MMoE），通过数据显式地学习任务之间的关系，以解决多任务学习中常见的任务关系敏感性问题。通过共享专家子模型和训练任务优化的门控网络，MMoE能够在不同任务之间灵活地分配参数，从而提高预测质量。通过在合成数据集和真实任务上的实验证明了MMoE的有效性和可扩展性。

## Introduction

### Background

近年来，深度神经网络模型在许多实际的大规模应用中取得了成功，如推荐系统。这些推荐系统通常需要同时优化多个目标。然而，多任务学习模型在实践中并不总是在所有任务上优于单任务模型。这是因为任务之间存在差异，而现有的多任务学习模型对任务之间的关系敏感。



### Problems

过去的研究工作通过假设每个任务的数据生成过程、测量任务之间的差异以及基于差异性提出建议来研究多任务学习中的任务差异。然而，这些方法往往无法处理实际应用中更复杂的数据模式。

### Contributions


鉴于现有方法的局限性，本文提出了一种新的多任务学习方法，即多门混合专家模型（MMoE），通过显式地建模任务之间的关系来提高模型的预测质量。通过在合成数据集和真实任务上的实验证明了MMoE的有效性和可扩展性。


## Methods

- a. 理论背景:
	- 本文讨论了在实际应用中使用深度神经网络模型，如推荐系统。强调了多任务学习在同时优化多个目标时的必要性。文章承认常用的多任务模型对任务关系敏感，并提出了一种新的方法，即多门混合专家模型（MMoE），它明确地建模任务关系并学习任务特定功能。文章还提到了衡量任务差异的挑战和现有建模技术的局限性。文章的贡献在于提出了MMoE模型的建议，对合成数据进行了控制实验，并在真实基准数据和大规模生产推荐系统上进行了实验。

- b. 技术路线:
	- 本文讨论了两种多任务学习的建模方法：共享底部多任务模型和专家混合模型。
	- 共享底部多任务模型是多任务学习应用中广泛采用的框架。
	- 它由一个共享底部网络（表示为函数f）和每个任务的K个塔网络hk组成。
	- 共享底部网络连接到输入层，塔网络建立在共享底部的输出上。
	- 每个任务都有自己的任务特定塔，模型使用相应的任务特定塔预测每个任务的输出yk。
	- 模型可以表示为yk = hk(f(x))，其中x是输入，hk是任务k的任务特定塔。
	- 专家混合模型基于参数调制和深度学习中的集成方法的思想。
	- 它由多个专家网络fi和一个选择和组合专家输出的门控网络д组成。
	- 门控网络根据输入产生专家的分布，最终输出是所有专家输出的加权和。
	- 专家混合模型已经显示出在深度学习中提高模型性能的效果。
	- 它引入了门控网络的稀疏性，从而降低了计算成本。

为了解决这个问题，MMoE采用了类似集成学习的思想，用若干个专家模型(expert model)加权影响子任务，门控机制（Gate）就是训练不同专家的影响因子，最终通过softmax输出不同专家的权重。如果对于所有子任务只有一套Gate就是MoE模型，但是更好的处理方式是每个子任务都有一个属于自己的Gate，升级为MMoE模型，详细的模型示意图见下图：

![Pasted image 20230713091222](../../../../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230713091222.png)

### Shared-bottom multi-task model

*这个不是作者提出的，仅供参考*

Share-Bottom的结构过于简单，对于相关性差的任务之间共享信息会导致效果变差。

如上图(a)所示， shared-bottom网络 (表示为函数 $f$ ) 位于底部，多个任务共用这一层。往上，K个子任务分别对应一个tower network (表示为 $h^k$ )，每个子任务的输出 $y_k=h^k(f(x))$ 。

### Mixture-of-experts (MoE)

*这个不是作者提出的，仅供参考*

MoE模型可以形式化表示为 $y=\sum_{i=1}^n g(x)_i f_i(x)$ ，其中 $\sum_{i=1}^n g(x)_i=1$ 。其中 $f_i, i=1, \cdots, n$ 是 $\mathrm{n}$ 个expert network（expert network可认为是一个神经网络）。g是组合experts结果的gating network，具体来说g产生n个experts上的概率分布，最终的输出是**所有experts的带权加和**。显然，MoE可看做基于多个独立模型的集成方法。这里注意MoE并不对应上图中的b部分。

后面有些文章将MoE作为一个基本的组成单元，将多个MoE结构堆叠在一个大网络中。比如一个MoE层可以接受上一层MoE层的输出作为输入，其输出作为下一层的输入使用。

### Multi-gate mixture-of-experts (MMoE)

文章提出的模型（简称MMoE）目的就是**相对于shared-bottom结构不明显增加模型参数的要求下捕捉任务的不同**。其核心思想是将**shared-bottom网络中的函数f替换成MoE层**，如上图c所示，形式化表达为：
$$
y_k=h^k\left(f^k(x)\right), f^k(x)=\sum_{i=1}^n g^k(x)_i f_i(x)
$$

其中$g^k(x)=\operatorname{softmax}\left(W_{g k} x\right)$，输入就是input feature，输出是所有experts上的权重。

一方面，因为gating networks通常是轻量级的，而且expert networks是所有任务共用，所以相对于论文中提到的一些baseline方法在计算量和参数量上具有优势。

另一方面，相对于所有任务公共一个门控网络(One-gate MoE model，如上图b)，这里MMoE(上图c)中每个任务使用单独的gating networks。每个任务的gating networks通过最终输出权重不同实现对experts的选择性利用。不同任务的gating networks可以学习到不同的组合experts的模式，因此模型考虑到了捕捉到任务的相关性和区别。

在原论文的实验和我们的工作实践中，在相同参数规模下(很重要，有可比性!)要比share-bottom有明显的提升效果，正如原论文所描述的，对于相关性低的任务联合训练MMoE效果更加明显。任务的相关性，有很多方法可以刻画，大家可以自行查阅。

  

## Evaluation

- a. 详细的实验设置:
	- MMoE模型与OMoE模型和共享底部模型在具有不同任务相关性的合成数据上进行了测试。
	- 与其他模型相比，MMoE模型在高相关性和低相关性情况下的性能差异较小。
	- 这表明MMoE模型能够更好地处理任务关系较弱的情况。
- b. 详细的实验结果:
	- MMoE模型在训练性能方面表现优于共享底部模型。
	- 共享底部模型的性能方差大于MMoE模型，表明共享底部模型具有更多质量较差的局部最小值。
	- 当任务相关性较高时，OMoE模型的鲁棒性与MMoE模型相似，但当任务相关性降低时，其鲁棒性下降。
	- 这凸显了多门结构在解决由任务差异引起的冲突中的有用性。
	- MMoE模型在真实数据集上与几种最先进的多任务深度神经网络模型进行了比较。
	- MMoE模型在捕捉任务关系方面表现出效果，并优于共享底部模型。
	- 它还与L2-Constrained和Cross-Stitch模型进行了比较，证明了其在学习任务关系方面的优越性。
  

## Conclusion

整体来看，这篇文章是对多任务学习的一个扩展，**通过门控网络的机制来平衡多任务**的做法在真实业务场景中具有借鉴意义。下面补充介绍文中的一个数据集设置的做法和实验结果中对不同模型的相互对比分析。

- **人工构造数据集**

在真实数据集中我们无法改变任务之间的相关性，所以不太方便进行研究任务相关性对多任务模型的影响。论文中人工构建了两个回归任务的数据集，然后通过两个任务的标签的Pearson相关系数来作为任务相关性的度量。在工业界中**通过人工构造的数据集来验证自己的假设**是个有意思的做法。

- **模型的可训练性**

模型的**可训练性，就是模型对于超参数和初始化是否足够鲁棒**。作者在人工合成数据集上进行了实验，观察不同随机种子和模型初始化方法对loss的影响。这里简单介绍下两个现象：第一，Shared-Bottom models的效果方差要明显大于基于MoE的方法，说明Shared-Bottom模型有很多偏差的局部最小点；第二，如果任务相关度非常高，则one gate MOE (OMoE)和MMoE的效果近似，但是如果任务相关度很低，则OMoE的效果相对于MMoE明显下降，说明**MMoE中的multi-gate的结构对于任务差异带来的冲突有一定的缓解**作用。



## References

后续文章：

PLE: 
[Progressive Layered Extraction (PLE): A Novel Multi-Task Learning (MTL) Model for Personalized Recommendations](PLE):%20A%20Novel%20Multi-Task%20Learning%20(MTL)%20Model%20for%20Personalized%20Recommendations)




## Appendices

### Appendix A

> 在我们的工作实践中有几个现象在这里总结一下，希望更多同学可以参与讨论和指正

#### Q1: 如何描述多个专家模型对于不同任务的差别和有效性？

我一般采用的是在一个足够大的数据集上，打印每个gate网络 softmax后的分布，这里直接体现的就是每个专家系统对于当前子任务的权重。如下图所示，每个gate的分布：

![Pasted image 20230713114905](../../../../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230713114905.png)

#### Q2: 如何确定超参，如expert数和专家模型的实现方案？

实际的expert模型和gate模型都不是固定的，最常见的就是多层FC网络组成的mlp，具体实现可以参考[PaddleRec/MMoE](https://link.zhihu.com/?target=https%3A//github.com/PaddlePaddle/PaddleRec/tree/release/2.2.0/models/multitask/mmoe) 但是在相同的参数规模下，gate模型建议不要太简单，一般直接输出softmax不是最好的方式，在我们的实践中，当gate网络由一层fc升级为3层fc（gate网络参数增大，expert网络参数减少）时效果有明显提升。

expert专家数量根据我们的对比实验（expert数量增大时，每个expert网络模型参数减少），一般呈现中间高两头低的现象，最佳的实验结果是expert专家数量在gate数量（子任务个数）1-2倍区间。对于具体的任务最好的方式还是多组实验对比。

#### Q3: 新加子任务时的热启解决方案？

我们在实践中经常会遇到需要增加任务或者修改模型的情况。我们在实践中尝试过如下解决方案：

a. 完全冷启训练。这种适用于收敛较快，模型改动特别大的场景，尤其是对于sparse部分的修改。

b. 只热启sparse部分(embedding)。这是最常见的方式，一般增加目标的场景或者修改上层模型的场景下，sparse都是共享的，所以直接热启线上训练好的sparse表可以快速收敛，稳定性也更好。

c. 热启sparse部分和部分dense模型。比如只增加目标，不修改expert数量的场景下，直接热启expert专家模型的参数。这个在部分场景下适用，但不建议这样热启，因为当新增加一个任务或者修改上层模型后，整体的分布会改变，从之前的专家模型参数开始重新收敛并不一定比完全冷启expert专家模型效果更好，有可能会出现局部收敛陷阱，这个需要具体场景下的多组实验对比。

### Appendix B

#### 反对观点

[知乎用户iTlthH](https://www.zhihu.com/people/2b4e032fa453ecbdd6dc9cadbcd88122)：MMOE其实没多大用途，公平比较基本没啥提升，不公平比较，随便提升，企业里很多都是不公平比较。MMOE你可以认为基本是就跟加个regularization差不太多，大多数情况提升微软，可能会扛过拟合，提升几个点，加regular也一样提升

## Future work

[Mixture-of-Experts (MoE) 经典论文一览](https://zhuanlan.zhihu.com/p/542465517)
