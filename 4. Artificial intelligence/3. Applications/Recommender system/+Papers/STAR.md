Title: One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction

Link: https://arxiv.org/abs/2101.11427

Code: 要是有就好了

Additional information: 

Conclusion: 

| Name | Paper                                                                                                                                                                                                            | Publication | Repository | Link                                                 | Classification                                                                                 |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| [STAR](+Papers/STAR.md) | [One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction](../AI4Rec.md#One%20Model%20to%20Serve%20All%20Star%20Topology%20Adaptive%20Recommender%20for%20Multi-Domain%20CTR%20Prediction) | CIKM 2021   |            | [arXiv:2101.11427](https://arxiv.org/abs/2101.11427) | [Click-through rate prediction](#Click-through%20rate%20prediction), [Multi-scenario learning](#Multi-scenario%20learning) |

## Abstract



## Introduction

### Background



### Problems



### Contributions



## Methods

整体模型结构如下图(b)所示：
![](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230719160434.png)

首先domain特征通过domain indicator获得，之后所有特征输入embedding网络，然后pooling合并输入[partitioned normalization](#Partitioned%20normalization)层，最后输入[star topology FCN](#Star%20topology%20FCN)网络。整个结构还会附带一个辅助网络[auxiliary network](#Auxiliary%20network)，防止中间信息丢失。

### Partitioned normalization

将传统的batch normalization修改为partitioned normalization，区别在于修正分布的可学习参数𝛾 (gamma)和𝛽 (beta)是domain specific的。

> 归一化操作，比如Batch Normalization，在神经网络中很常用，可以缓解梯度爆炸或衰减、加速收敛。在单场景的CTR建模中，样本之间可以看作是独立同分布的，因此归一化需要的均值和方差可以通过样本采样得到。但是在多领域CTR建模中，每个领域数据的均值和方差不一样，不能简单地用同一个均值和方差做归一化。在论文中，作者提出Partitioned Normalization，对不同的领域，只使用各自领域的样本计算均值和方差，并且每个领域都有各自的scale和bias参数。以Batch Normalization为例，训练的时候计算公式如下：
> $$z^{\prime}=\left(\gamma * \gamma_p\right) \frac{z-\mu}{\sqrt{\sigma^2+\epsilon}}+\left(\beta+\beta_p\right)$$
> 其中，$\gamma, \gamma_p$是共享scale和领域p的scale向量，$\beta, \beta_p$是共享bias和领域p的bias向量，$u, \sigma$和每个领域batch的均值和方差。
> 在预测的时候，会统计每个领域样本的均值和方差用于计算，具体的公式如下:
> $$\mathrm{z}^{\prime}=\left(\gamma * \gamma_p\right) \frac{\mathrm{z}-E_p}{\sqrt{\operatorname{Var}_p+\epsilon}}+\left(\beta+\beta_p\right)$$


### Star topology FCN

![](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230719164639.png)

最后的FCN层每个domain都有一个，以及额外的一个共享FCN，最终每个domain的输出是specific FCN结果与共享FCN结果element-wise相乘。

Specifically, for the shared FCN, let $W$ be the weights and $b$ be the bias in a neural network layer respectively. For the specific FCN of the $p$-th domain, let $W_p$ be the weights and $b_p$ be the bias in the corresponding layer. Denote the input dimension as $c$ and the output dimension as $d$, i.e, $W, W_p \in \mathbb{R}^{c \times d}, b, b_p \in \mathbb{R}^d$. The final weights $W_i^{\star}$ and bias $b_i^{\star}$ for the $p$-th domain is obtained by:
$$
W_p^{\star}=W_p \otimes W, b_p^{\star}=b_p+b,
$$
where $\otimes$ denotes the element-wise multiplication. Let $i n_p \in \mathbb{R}^{c \times 1}$ denote the input of this neural network layer from the $p$-th domain, the final output out $t^{\prime} \in \mathbb{R}^d \times 1$ is given by:
$$
\text { out }_p=\phi\left(\left(W_p^{\star}\right)^{\top} \operatorname{in}_p+b_p^{\star}\right),
$$
### Auxiliary network

这一部分明明也是模型的一部分，但是没有被放进图片中，很怪。

**辅助网络本身是一个简单的全连接层，辅助网络将Figure 4中pooling & concatenation部分的值作为输入，输出结果和FCN输出结果直接相加，作为模型输出的最终结果。**


## Evaluation

### Datasets

Alibaba闭源数据集

### Baselines

- Base （似乎是取消了domain indicator输入、用全连接层替换掉[star topology FCN](#Star%20topology%20FCN)、训练时不考虑多场景的模型）
- Shared Bottom
- MulANN
- MMoE
- Cross-Stitch

### Metrics

ROC曲线和AUC值

### Results

首先整体结果比其他模型都要好：

![500](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230719172039.png)

消融实验中：

- [Partitioned normalization](#Partitioned%20normalization)作用很大，但是[Star topology FCN](#Star%20topology%20FCN)作用挺小的，感觉不考虑多场景也行一样
	- ![400](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230719172123.png)
- [Auxiliary network](#Auxiliary%20network)有用，但也是锦上添花

### Production

> 在工业推荐系统中，特征和点击率（CTR）的分布会随着时间发生大的变化。为了捕捉实时数据的动态变化，使用实时样本不断更新CTR模型非常重要，以防止模型变得过时。然而，在多域CTR预测中，每个域的样本比例会随时间变化。例如，一些业务域在早晨有流量高峰，而一些业务域在晚上有流量高峰。如果我们按照时间顺序直接训练模型，数据比例随时间的变化会导致模型学习的不稳定。为了解决这个问题，他们重新设计了数据管道，并维护了一个存储历史样本滑动窗口的缓冲区，以避免样本比例的突然变化。具体来说，首先对缓冲区中的样本进行洗牌，然后进行采样以构造一个小批量。将这个小批量的样本输入模型后，从缓冲区中移除这些样本，并将新到达的数据添加到缓冲区中。他们经验性地发现，这种训练方式比传统的在线更新方式更稳定。

在时间维度，他们使用滑动窗口提取训练数据，否则时间不同差异太大，模型不好训练

> 值得注意的是，在服务过程中，为了实现更快的推理，每个域的全连接网络（FCN）的权重都是预先计算的。这样，STAR模型的计算时间等于Shared Bottom模型的计算时间。系统优化使STAR能够稳定地服务多个业务域的主要流量。自2020年以来，STAR已经部署并在阿里巴巴的展示广告系统中服务了60多个业务域。他们计算了所有域的整体改进。表5显示了STAR相对于之前的生产模型（基础模型）的改进。STAR的引入在他们的在线A / B测试中带来了+8.0%的整体CTR提升和+6.0%的整体RPM提升。

## Conclusion

在论文的结论部分，作者提出了星型拓扑自适应推荐器（STAR），以解决多域CTR预测的问题。STAR具有星型拓扑，包括共享的中心参数和特定于域的参数。共享参数学习共性，通过所有样本进行更新。特定于域的参数捕捉域的区别，以进行更精细的预测，这是通过在特定域内使用样本来学习的。通过这种方式，STAR可以根据域条件自适应地调整其参数，以进行更精细的预测。实验结果表明，STAR在多域CTR预测上的优越性。自2020年以来，STAR已在阿里巴巴的广告系统中部署，CTR提高了8.0%，RPM提高了6.0%。


## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。




## Appendices

### Appendix A



### Appendix B



## Future work

值得研究的点








