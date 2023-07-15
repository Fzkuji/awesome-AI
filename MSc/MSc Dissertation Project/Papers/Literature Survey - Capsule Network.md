胶囊网络是Geoffrey Hinton在2017年提出的一种全新的神经网络结构，用于取代广泛使用的CNN。在此之前，Hinton就已经有此类想法，
[Transforming auto-encoders](https://doi.org/10.1007/978-3-642-21735-7_6)
[Learning to Parse Images](https://proceedings.neurips.cc/paper/1999/file/5a142a55461d5fef016acfb927fee0bd-Paper.pdf)
但直到Dynamic Routing Between Capsules才有了完全确定的模型。之后胶囊网络还有多种优化设计，包括EM路由、transformer胶囊。最近Hinton进一步提出了GLOM模型，扩展了胶囊网络的概念。

## 设计原因

胶囊网络旨在替代CNN，提供一种更健壮、保留更多不变性的网络结构。

An [invariant](https://en.wikipedia.org/wiki/Invariant_(mathematics) "Invariant (mathematics)") is an object property that does not change as a result of some transformation.

比如在计算机视觉中，一个物体整体进行平移和旋转，最终结果仍然是其本身。Informally, an [equivariant](https://en.wikipedia.org/wiki/Equivariant_map "Equivariant map") is a property that changes predictably under transformation.

但如果部分位置进行变换，则可能改变图形的本质，变得不符合常理。
A nonequivariant is a property whose value does not change predictably under a transformation.

CNN网络用于图像识别时，可以很容易的判断整体包含哪些部分，但却很难保留整体与部分的位置关系。如图所示。

同时，CNN网络依赖于maxpooling，the pooling layer strategy of conventional CNNs that reduces the amount of detail to be processed at the next higher layer。事实上，我们需要提供不变性（丢弃位置信息）而不是等变性（解开该信息）。

针对CNN的问题，胶囊网络设计了一种通过vector传递包含了特征状态的各类重要信息，使用路由机制计算不同层次结构关系的模型。

## Hinton胶囊网络总结

### Capsule Network
#### 动态路由
此类胶囊网络使用路由机制控制不同层胶囊的关系。Hinton提出的胶囊网络使用了动态路由和EM路由两种路由机制。

动态路由胶囊网络仍然使用CNN提取输入的特征，并使用输出的vector作为胶囊。

动态路由主要用于替代maxpooling操作，将前一层的胶囊映射到下一层。通过这种方法，模型能够避免丢失大量有效信息。同时模型也可以得到不同层级胶囊的关系，大大提高可解释性。

如图所示，原版的胶囊网络包含PrimaryCaps和DigitCaps两层，胶囊层的输入和输出均为向量。动态路由类似于聚类，初始化计算前一层胶囊的权重，最终加权得到下一层输出，通过多次迭代使得输出结果和前一层的胶囊相关性最大。

#### EM路由

https://kexue.fm/archives/5155
动态路由胶囊网络提出不久后，EM路由胶囊网络被提出。主要区别为：
原来用向量来表示一个Capsule，现在用矩阵来表示；
用EM路由替代原来的动态路由机制，具体而言聚类算法换成了GMM；
在实验部分，实现了Capsule版的卷积。

矩阵的不同位置的元素重要性不一样，而向量的每个元素的重要性都是一样的。同时，从计算的角度看，也能发现区别：要将一个16维的向量变换为另外一个16维的向量，我们需要一个16×1616×16的变换矩阵；但如果将一个4×44×4的矩阵变换为另外一个4×44×4的矩阵，那么只需要一个4×44×4的变换矩阵，参数量减少了。从这个角度看，也许将Capsule从向量变为矩阵的根本目的是降低计算量。

在我们脑海里最好不要将GMM视为一个聚类算法，而将它看作一个真正的无监督学习算法，它试图学习数据的分布。数据本身是个体，而分布则是一个整体，从研究数据本身到研究数据分布，是质的改变。

### Capsule Network with Attention

基于注意力的胶囊网络 主要改进在于使用set transformer提取图像的信息和位置，在路由阶段仍然使用高斯混合分布来处理。

对于每个物体，模型都会学习一个模板和位置，对于图像中对应的物体，模型会给出该物体出现的概率和pose。

transformer是一种基于自注意力机制的模组，本文也受其启发设计了基于注意力的路由机制。

transformer的关键在于使用自注意力机制将整个输入的信息相互传递，最终输出和输入等长的输出。在这个过程中，不同位置的信息会分享到其他位置。