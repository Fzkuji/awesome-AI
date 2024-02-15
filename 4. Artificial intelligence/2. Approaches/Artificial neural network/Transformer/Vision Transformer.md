# Vision Transformer

## Related works

### ViT

[An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://openreview.net/forum?id=YicbFdNTTy)

用于视觉的transformer首次被提出，他们将图片等分，然后给不同位置加上位置标记。最后像序列一样处理图片。

### Swin Transformer

Swin Transformer: Hierarchical Vision Transformer using Shifted Windows

[Swin Transformer论文精读【论文精读】（跟李沐学AI）](https://www.bilibili.com/video/BV13L4y1475U/)

借鉴了CNN的思路，提出了transformer的pooling机制——patch merging和滑动注意力窗口（shifted window based self-attention）来提供全局感知。

### ViTDet

[Exploring Plain Vision Transformer Backbones for Object Detection](https://dl.acm.org/doi/abs/10.1007/978-3-031-20077-9_17)

[如何看待Meta（恺明）最新论文ViTDet：只用ViT做backbone（不使用FPN）的检测模型？](https://www.zhihu.com/question/525167811)

这篇论文核心在于，指出对ViT进行层次化的修改并不是必要的。

似乎这篇文章的思路和YOLOF的想法一样？（不太了解这个方向）顺便说说同年的YOLOX似乎引用量更高，说是将anchor box去掉了，也挺不错的。





这段内容介绍了STRec（Sparse Transformer for Sequential Recommendations）框架中稀疏变换器的实现，特别是交叉注意力变换器层的设计和采样策略的优化。以下是对这一部分的中文解释：

### 3.2 STRec中的稀疏变换器

为了消除传统自注意力机制中冗余的项目间互动影响，提出了一种交叉注意力机制的稀疏变换器。在这个机制中，通过选择根据访问时间间隔判断为相关的项目来缩小查询矩阵。首先介绍提出的稀疏交叉注意力机制，然后详细说明采样策略的优化过程。

#### 3.2.1 交叉注意力变换器层
为了减少注意力层的计算成本，用交叉注意力替换了传统的自注意力。这种方法中，输入序列作为键和值，而采样的项目序列作为查询。这样，注意力计算更加高效，因为采样的查询矩阵显著缩小。具体来说，对于 $\boldsymbol{H}^{l-1}$，交叉注意力可以表达为：

$$
\tilde{\boldsymbol{H}}^{l-1}=\operatorname{Add} \& \operatorname{Norm}\left(\operatorname{Attention}\left(\boldsymbol{H}_{I}^{l-1}, \boldsymbol{H}^{l-1}, \boldsymbol{H}^{l-1}\right)\right)
$$

其中，$\boldsymbol{H}_{I}^{l-1}$ 是通过采样索引 $\boldsymbol{I}_{\boldsymbol{l}}$ 从 $\boldsymbol{H}^{\boldsymbol{l}-1}$ 中选取的项目表示子集。输出 $\tilde{H}^{l-1}$ 与 $H_{I}^{l-1}$ 形状相同。

接着，FFN层接受缩短的 $\tilde{\boldsymbol{H}}^{l-1}$ 作为输入，生成输出隐藏状态：

$$
\boldsymbol{H}^{l}=\operatorname{Add} \& \operatorname{Norm}\left(\operatorname{FFN}\left(\tilde{\boldsymbol{H}}^{l-1}\right)\right)
$$

输出隐藏状态 $\boldsymbol{H}^{l}$ 的长度与 $H_{I}^{l-1}$ 和 $\tilde{H}^{l-1}$ 相同。

与方程（2）中的传统自注意力变换器层相比，交叉注意力变换器层只计算采样项目在注意力和前馈网络中。时间和空间复杂度从 $O\left(n^{2}\right)$ 降低到 $O\left(n k_{l}\right)$。

#### 3.2.2 采样策略
回顾第2.3节的结果，我们观察到在序列推荐任务中后出现的项目更可能重要。因此，我们提出了一种基于时间间隔的采样策略，具有可学习的参数，表示为 $T=\left\{\tilde{t}_{i}\right\}_{1 \leq i \leq N}$：

$$
\tilde{t}_{i}=t_{i}-t_{N}
$$

其中 $t_{i}, 1 \leq i \leq N$ 是交互 $v_{i}$ 的记录时间戳。

对于第一层，我们将时间间隔 $T$ 映射到采样密度上，通过一个多层感知机（MLP）。还添加了一个来自均匀分布的随机矩阵 $R$ 来进行随机采样。可以生成采样索引 $I$：

$$
I_{1}= \text { Top } \_\mathrm{k}\left(\operatorname{MLP}(T)+R, k_{1}\right) \\
r_{i} \sim \operatorname{Uniform}(0,1)
$$

对于后续层，逐层生成采样索引耗时。考虑到 $\operatorname{MLP}(T)+R$ 在微调和推理期间对所有层保持不变，我们可以直接输入排序的索引 $\boldsymbol{I}_{1}$ 并取前 $k_{l}$ 作为 $\boldsymbol{I}_{l}$：

$$
I_{l}=I_{1}\left[1: k_{l}\right] \\
H_{I}^{l-1}=\left[h_{I_{l}[1]}^{l-1}, \ldots, h_{I_{l}\left[k_{l}\right]}^{l-1}\right] \quad \forall 2 \leq l \leq L
$$

注意，方程（7）中 $I$ 的计算是不可微分的，因为 Top_k 操作调用了随机过程，以可微分的方式产生近似的硬决策。为了解决这个问题，在预训练过程中，我们应用Gumbel-Softmax并用注意力掩码 $M$ 替换采样过程，其中 $m_{i j} \approx 0$ 表示第 $i$ 个查询和第 $j$ 个键之间的注意力权重不被计算，反之亦然：

$$
S_{l}=\operatorname{Sigmoid}\left(\operatorname{MLP}(T)+R+\alpha_{l}\right) \forall 1 \leq l \leq L \\
S_{0}=[1,1, \ldots, 1] \\
M_{l}=S_{l-1} \otimes S_{l} \forall 1 \leq l \leq L \\
r_{i} \sim \operatorname{Uniform}(0,1)
$$

然后，具有注意力掩码 $\boldsymbol{M}$ 的可微分注意力层表现为：

$$
\tilde{\boldsymbol{H}}^{l-1}=\operatorname{Add} \&

 \operatorname{Norm}\left(\sigma\left(\boldsymbol{H}_{I}^{l-1} \boldsymbol{H}^{l-1^{T}}+(\boldsymbol{M}-1) * \infty\right) \boldsymbol{H}^{l-1}\right)
$$

这种方法通过只关注重要的项目互动和采样策略的优化，旨在提高序列推荐系统的效率和性能。