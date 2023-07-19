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

将传统的 batch normalization 修改为 partitioned normalization，区别在于修正分布的可学习参数 𝛾 (gamma) 和 𝛽 (beta) 是domain specific的。


### Star Topology FCN


  

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








