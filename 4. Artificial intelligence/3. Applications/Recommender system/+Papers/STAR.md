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

首先domain特征通过domain indicator获得，之后所有特征输入embedding网络，然后pooling合并输入partitioned normalization层，最后输入star topology FCN网络进行预测。

### Partitioned normalization

将传统的batch normalization修改为partitioned normalization，区别在于修正分布的可学习参数𝛾 (gamma)和𝛽 (beta)是domain specific的。

> 归一化操作，比如Batch Normalization，在神经网络中很常用，可以缓解梯度爆炸或衰减、加速收敛。在单场景的CTR建模中，样本之间可以看作是独立同分布的，因此归一化需要的均值和方差可以通过样本采样得到。但是在多领域CTR建模中，每个领域数据的均值和方差不一样，不能简单地用同一个均值和方差做归一化。在论文中，作者提出Partitioned Normalization，对不同的领域，只使用各自领域的样本计算均值和方差，并且每个领域都有各自的scale和bias参数。以Batch Normalization为例，训练的时候计算公式如下：
> $$z^{\prime}=\left(\gamma * \gamma_p\right) \frac{z-\mu}{\sqrt{\sigma^2+\epsilon}}+\left(\beta+\beta_p\right)$$
> 其中，$\gamma, \gamma_p$是共享scale和领域p的scale向量，$\beta, \beta_p$是共享bias和领域p的bias向量，$u, \sigma$和每个领域batch的均值和方差。
> 在预测的时候，会统计每个领域样本的均值和方差用于计算，具体的公式如下:
> $$\mathrm{z}^{\prime}=\left(\gamma * \gamma_p\right) \frac{\mathrm{z}-E_p}{\sqrt{\operatorname{Var}_p+\epsilon}}+\left(\beta+\beta_p\right)$$


### Star Topology FCN

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








