## 笔记

### 参考

[宏观解读](https://zhuanlan.zhihu.com/p/129007774)

[实现细节](https://zhuanlan.zhihu.com/p/119331091)


### CCAE（模型总览）

#### 输入

输入是图像

> Let $\left \{ x_{m}  | m = 1, . . . ,M \right \}$ be a set of two-dimensional input points, where every point belongs to a constellation as in [Figure 3](obsidian://open?vault=毕设&file=Figure%203.png). 


#### 编码层

首先将图像传入Transformer

> We first encode all input points (which take the role of part capsules) with Set Transformer into $K$ object capsules.
> 
> Set Transformer (Lee et al., 2019) is a permutation-invariant encoder $h^{caps}$ based on attention mechanisms.

Transformer输出：包含视角信息的$3×3$矩阵$OV$，包含胶囊特征的向量$c_{k}$，该胶囊出现的概率$a_{k}∈[0,1]$。

> An object capsule $k$ consists of:
> 1. a capsule feature vector $c_{k}$
> 2. its presence probability $a_{k}∈[0,1]$
> 3. a $3×3$ object-viewer-relationship ($OV$) matrix, which represents the affine transformation between the object (constellation) and the viewer

编码层包括多级胶囊，最高级为object capsule（object胶囊），中间级为part capsule（part胶囊）。


#### 解码层

输入：capsule feature vector $c_{k}$

每个物体胶囊一次只表示一个物体。每个object胶囊都使用一个单独的多层感知机，从特征向量$c_{k}$中预测$N$个part candidates。

> Note that each object capsule can represent only one object at a time. Every object capsule uses a separate multilayer perceptron (MLP) $h_{k}^{part}$ to predict $N≤M$ part candidates from the capsule feature vector $c_{k}$.

输出：$N$个part candidates

> Each candidate consists of: 
> 1. the conditional probability $a_{k,n}∈[0,1]$ that a given candidate part exists
> 2. an associated scalar standard deviation $λ_{k,n}$
> 3. a $3×3$ object-part-relationship ($OP$) matrix, which represents the affine transformation between the object capsule and the candidate part.

