
Explanation video: https://www.youtube.com/watch?v=CuEU-VH6Fdw
including Batch, Layer, Instance and Group Normalization


## Batch normalization

对一个batch内，所有样本相同位置的特征进行正则化，如图所示：
![600](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Normalization/Batch%20normalization.png)
图中，深蓝色框内的特征来自于不同的样本，但是属于同一个channel。

## Layer normalization

Layer normalization, or **LayerNorm**, is a regularization technique that might handle the `internal covariate shift` issue so as to stabilize the layer activations and improve model convergence. It has been proved quite successful in NLP-based model. In some cases, LayerNorm has become an essential component to enable model optimization, such as in the SOTA NMT model `Transformer`. 

One application of LayerNorm is on recurrent neural networks.

- [Original Paper](https://arxiv.org/abs/1607.06450)
- Formula:
$$
y_{i, j, k}=\frac{x_{i, j, k}-\mu_{i, j}}{\sqrt{\sigma_{i, j}^2+\epsilon}} \times \gamma_k+\beta_k, \quad \mu_{i, j}=\frac{1}{d} \sum_{k=1}^d x_{i, j, k}, \quad \sigma_{i, j}^2=\frac{1}{d} \sum_{k=1}^d\left(x_{i, j, k}-\mu_{i, j}\right)^2
$$

在一个batch内，对每个样本的所有特征进行正则化，如图所示：
![600](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Normalization/Layer%20normalization.png)
图中，红色框内的特征来自于同一个样本，囊括了该样本的所有channel。

## Instance normalization

对每个样本的每一个特征都进行正则化：
![600](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Normalization/Instance%20normalization.png)
Instance normalization在图中并没有标出，因为每一个样本的每一个channel都是一个instance，都要做一遍normalization。

## Group normalization

对一个样本的特征进行正则化。但是区别于layer normalization的对整个样本所有特征进行操作，group normalization将样本内的所有特征进行分组，在组内进行正则化。如图所示：
![600](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Normalization/Group%20normalization.png)
图中，假如$m=64$，并且一个group的大小设置为32，那么整个样本就被分为两个group，每个group内都会进行正则化。

## Root Mean Square Layer Normalization

Root Mean Square Layer Normalization (RMSNorm) is a simplification of the original [layer normalization](#Layer%20normalization).

- [Original Paper](https://arxiv.org/abs/1910.07467)
- [Code](https://github.com/bzhangGo/rmsnorm)
- Formula:
$$
y_{i, j, k}=\frac{x_{i, j, k}}{\sqrt{\sigma_{i, j}^2+\epsilon}} \times \gamma_k, \quad \sigma_{i, j}^2=\frac{1}{d} \sum_{k=1}^d x_{i, j, k}^2
$$

> 可以看出，RMS Norm也就是L2 Normalization的简单变体而已，但这篇论文总的结果显示：RMS Norm比Layer Normalization更快，效果也基本一致。
> 
> 除了这篇文章外，RMS Norm还被Google用在了T5中，并且在另外的一篇文章[Do Transformer Modifications Transfer Across Implementations and Applications?](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2102.11972)中做了比较充分的对比实验，显示出RMS Norm的优越性。这样看来，未来RMS Norm很可能将会取代Layer Normalization而成为Transformer的标配。
> 
> 无独有偶，同样是2019年的论文，[Analyzing and Improving the Image Quality of StyleGAN](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1912.04958)提出了StyleGAN的改进版StyleGAN2，里边发现所用的Instance Normalization会导致部分生成图片出现“水珠”，他们最终去掉了Instance Normalization并换用了一个叫“Weight demodulation”的东西，但他们同时发现如果去掉Instance Normalization的center操作能改善这个现象。这也为Normalization中的center操作可能会带来负面效果提供了佐证。
> 
> 一个直观的猜测是，center操作，类似于全连接层的bias项，储存到的是关于数据的一种先验分布信息，而把这种先验分布信息直接储存在模型中，反而可能会导致模型的迁移能力下降。所以T5不仅去掉了Layer Normalization的center操作，它把每一层的bias项也都去掉了。
> 
> -- [*浅谈Transformer的初始化、参数化与标准化*](https://zhuanlan.zhihu.com/p/400925524)

|  | Weight matrix re-scaling | Weight matrix re-centering | Weight Vector re-scaling | Dataset re-scaling | Dataset re-centering | Single training case re-scaling |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| BatchNorm | ✓ |  | ✓ | ✓ | ✓ |  |
| WeightNorm | ✓ |  | ✓ |  |  |  |
| LayerNorm | ✓ | ✓ |  | ✓ |  | ✓ |
| RMSNorm | ✓ |  |  | ✓ |  | ✓ |
| pRMSNorm | ✓ |  |  | ✓ |  | ✓ |






