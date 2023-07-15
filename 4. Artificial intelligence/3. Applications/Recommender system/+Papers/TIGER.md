Title: Recommender Systems with Generative Retrieval

- Transformer Index for GEnerative Recommenders (TIGER)
- Affiliation: University of Wisconsin-Madison, Google
- Keywords: [[Recommender system|Recommender Systems]], Generative Retrieval, Vector Quantization

- https://shashankrajput.github.io/Generative.pdf
- https://arxiv.org/abs/2305.05065
- **No code**

2018年的文章，文档并未发表在刊物上，而是直接存在于作者的GitHub上，可能是没发出去吧
发现又被传到arxiv了（8 May 2023）

总结：就是设计了一种embedding方法，将所有物品表示为一些整数的组合。实验证明这比小数级别的嵌入更好。然后模型尝试预测这个点击序列。

## Abstract

现代推荐系统利用大规模检索模型，包括两个阶段：首先训练双编码器模型将查询和候选项嵌入到同一空间，然后使用近似最近邻（ANN）搜索根据查询的嵌入来选择最佳候选项。

在这篇论文中，我们提出了一个新的单阶段范式：一种生成式检索模型，它以自回归的方式解码目标候选项的标识符。为此，我们不是为每个项目分配随机生成的原子ID，而是生成语义ID：为每个项目生成一个具有语义意义的编码词元组作为其唯一标识符。我们使用一种名为RQ-VAE的分层方法生成这些编码词。一旦我们拥有所有项目的语义ID，就可以训练一个基于Transformer的序列到序列模型来预测下一个项目的语义ID。由于该模型以自回归方式直接预测识别下一个项目的编码词元组，因此可以将其视为生成式检索模型。

我们证明了在这个新范式下训练的推荐系统改进了当前在亚马逊数据集上的最先进模型所取得的结果。此外，我们还证明了序列到序列模型与分层语义ID相结合能够更好地泛化，从而改进对冷启动项目的检索以提供推荐。

## Introduction

推荐系统帮助用户发现感兴趣的内容，在诸如视频、应用、产品和音乐等领域广泛应用。现代推荐系统采用检索和排序策略，先在检索阶段选择一组合适的候选项，然后使用排序模型对其进行排序。

为了更好地捕捉数据中的非线性，近年来双编码器架构（即一个用于查询，一个用于候选项）越来越受欢迎，它使用内积将查询和候选项嵌入到相同的空间中。在推理过程中，使用候选塔为所有项目创建嵌入索引。对于给定的查询，使用查询塔计算其嵌入，然后使用近似最近邻算法选择最近的候选项。另一方面，最近流行的顺序推荐器会显式考虑用户与项目交互的顺序。它们通常在输出层使用softmax，并在推理期间使用 ANN。


![[Fig 1 1.png]]
Figure 1: High-level overview of Transformer Index for GEnerative Recommenders (TIGER) framework. TIGER proposes representing an item as a tuple of discrete semantic tokens (referred to as Semantic ID), which allows framing the sequential recommendation task as a generative task such that the Semantic ID of the next item is directly predicted using a sequence-to-sequence model. ^c9c8a3

我们提出了一种用于顺序推荐器的生成式检索模型的新范式。与传统的查询-候选项匹配方法不同，我们的方法使用端到端的生成模型直接预测候选项ID，从而无需离散的、非可微的内积搜索系统或索引。

通过autoregressive decoding和[[Beam search]]，我们可以检索到多个可行的候选项。在这种情况下，我们可以将Transformer的内存（参数）解释为end-to-end recommendation index。因此，我们将我们提出的方法称为TIGER，即*Transformer Index for GEnerative Recommenders*。TIGER的概述如[[TIGER#^c9c8a3|Figure 1]]所示。

TIGER的特点是用一种新颖的“语义ID”表示每个项目：基于项目内容信息（如文本描述）的一系列标记。

具体来说，给定一个项目的文本描述，我们可以使用预训练的文本编码器（例如SentenceT5）来生成密集的内容嵌入。 然后可以在嵌入上应用量化方案以形成一小组令牌/码字（整数）。 我们将这个有序的代码字元组称为项目的语义 ID。 我们从人类语言中获得了这个想法的灵感，在人类语言中，我们用词来表示概念，并将词串在一起来传达复杂的想法。 同样，我们想为 ID 开发一种语言来表示项目。 通过这种方式，我们可以使用一组有限的单词（标记）序列来表示数十亿个项目，这与随机生成的原子 ID 需要每个项目一个 ID 形成对比。

值得注意的是，类似的想法已经被采用在从文本生成图像的场景中，比如[Parti](https://arxiv.org/abs/2206.10789)模型，其使用ViT-VQGAN将图像表示为标记。一个关键的区别是，在图像生成中，一些错误的标记会导致图像中的小错误或噪声。相反，在这里，一个错误的标记将意味着推荐系统预测到一个不同的或不存在的项目。

使用这些语义ID有很多好处。现代推荐系统中的用户和项目数量可能达到数十亿。因此，当使用原子ID和嵌入向量之间的1:1映射时，基于ANN的模型的嵌入表可能变得过大。这不仅需要大量的内存和存储空间，而且在训练项目的嵌入时也会导致不平衡，流行的项目会被过度采样，相比之下不常见的项目就会被忽略。因此，通常的做法是为更受欢迎的项目保留专用词汇表，并将其余项目随机散列到固定数量的桶中。然而，散列方案会导致随机碰撞。随着现实世界推荐产品中新项目的推出，随机碰撞可能会加剧冷启动问题。相比之下，我们的方法只需要维护一个小型token集的embedding table。此外，我们的冲突在语义上是有意义的，这有助于缓解冷启动问题。

(1) 我们提出了 TIGER，一种新颖的基于检索的生成式推荐模型，它首先为每个项目分配唯一的语义 ID，然后训练一个检索模型来预测用户将参与的下一个项目的语义 ID。 这为基于高维最近邻搜索或基于 softmax 的推荐系统提供了替代方案。 
(2) 我们证明 TIGER 在多个数据集上优于现有 SOTA 推荐系统的召回率和 NDCG 指标。
(3) 我们发现这种新的生成检索范式在顺序推荐系统中带来了两个额外的能力：
1. 能够推荐新的和不频繁的项目，从而改善冷启动问题
2. 能够使用生成多样化的推荐可调参数。

## Methods

### Semantic ID generation

本质就是embedding，美其名曰semantic ID generation。

![[Fig 2 (a).png]]

1. Use the textual description of items as content features and use Sentence-T5 encoder on this textual description.
2. The semantic embeddings are then quantized to generate a Semantic ID for each item.

#### RQ-VAE for Semantic IDs

Residual-Quantized Variational AutoEncoder (RQ-VAE) is a multi-stage vector quantizer that applies quantization on residuals at multiple levels to generate a tuple of codewords (aka Semantic IDs).

![[Fig 3 1.png]]
Figure 3: RQ-VAE: In the figure, the vector output by the DNN Encoder, say $r_0$ (represented by the blue bar), is fed to the quantizer, which works iteratively. First, the closest vector to $r_0$ is found in the first level codebook. Let this closest vector be $\boldsymbol{e}_{c_0}$ (represented by the red bar). Then, the residual error is computed as $r_1:=r_0-e_{c_0}$. This is fed into the second level of the quantizer, and the process is repeated: The closest vector to $r_1$ is found in the second level, say $e_{c_1}$ (represented by the green bar), and then the second level residual error is computed as $r_2=r_1-e_{c_1}^{\prime}$. Then, the process is repeated for a third time on $r_2$. The semantic codes are computed as the indices of $e_{c_0}$, $e_{c_1}$, and $e_{c_2}$ in their respective codebooks. In the example shown in the figure, this results in the code $(7,1,2)$.

#### Other options for quantization

A simple alternative to generating Semantic IDs is to use Locality Sensitive Hashing (LSH). We perform an ablation study where we find that RQ-VAE indeed works better than LSH. 

Another option is to use kmeans clustering hierarchically, but it loses semantic meaning between different clusters. 

We also tried VQ-VAE, and while it performs similarly to RQ-VAE for generating the candidates during retrieval, it loses the hierarchical nature of the IDs which confers many new capabilities.

#### Handling collisions

For retrieval, we would like to **avoid** collisions and assign unique IDs for items. 

But collisions may still occur. 

To remove the collisions we append an extra token at the end of the Semantic IDs to make them unique. For example, if two items share the Semantic ID (12, 24, 52), we append extra tokens to differentiate between them, hence they are represented as (12, 24, 52, 0) and (12, 24, 52, 1).

### Generative retrieval with Semantic IDs

也就是用 用户之前点击的商品来预测用户接下来会点击什么。以前的记录以上面的Semantic ID的形式记录。

We construct item sequences for every user by sorting chronologically the items they have interacted with. Then, given a sequence of the form (item ite $_1, \ldots$, item $m_n$ ), the recommender system's task is to predict the next item item ${ }_{n+1}$. For this, we propose a generative approach that directly predicts the Semantic IDs of items.

Formally, let $\left(c_{i, 0}, \ldots, c_{i, m-1}\right)$ be the $m$-length Semantic ID for item $_i$. Then, we convert the item sequence to the sequence $\left(c_{1,0}, \ldots, c_{1, m-1}, c_{2,0}, \ldots, c_{2, m-1}, c_{n, 0}, \ldots, c_{n, m-1}\right)$. The sequence-towhich is $\left(c_{n+1,0}, \ldots, c_{n+1, m-1}\right)$. Hence, this formulation does not need to make any major modifications to existing sequence-tosequence model architectures to train them for generative recommendations. Once we have the predicted tuple of codewords $\left(c_{n+1,0}, \ldots, c_{n+1, m-1}\right)$, we simply look up the item to which this Semantic ID corresponds to. There is a possibility that the generated Semantic ID does not match any item in the dataset. However, as we observe in Figure 6, the probability of such an event is very low.

## Evaluation

### Experimental setup

#### Datasets

In particular, we use three categories of the Amazon Product Reviews dataset for the sequential recommendation task: “Beauty”, “Sports and Outdoors”, and “Toys and Games”.

Table 2: Dataset statistics for the three real-world benchmarks.

| Dataset | # Users | # Items | Sequence_ | Length   |
| :--- | :---: | :---: | :---: | :---: |
|  |  |  | Mean | Median |
| Beauty | 22,363 | 12,101 | 8.87 | 6 |
| Sports and Outdoors | 35,598 | 18,357 | 8.32 | 6 |
| Toys and Games | 19,412 | 11,924 | 8.63 | 6 |

#### Evaluation metrics.

We use

- top-k Recall (Recall@K)
- Normalized Discounted Cumulative Gain (NDCG@K) with 𝐾 = 5, 10 

to evaluate the recommendation performance.

#### Implementation details.

##### RQ-VAE model

We use the **pre-trained Sentence-T5 model** to obtain the semantic embedding of each item in the dataset.

In particular, we use item’s content features **such as title, price, brand, and category** to construct a sentence, which is then passed to the pre-trained Sentence-T5 model to obtain the item’s semantic embedding of **768 dimension**.

此处省略各种参数和模型结构，具体见文章第5页。

##### Sequence-to-sequence model

1. Use the open-sourced T5X framework to implement our transformer based encoder-decoder architecture.
2. Add semantic codewords (1024 (256×4) tokens) to the vocabulary of the sequence-to-sequence model.
3. Add user-specific tokens (2000 tokens for user IDs) to the vocabulary.
	1. Use the Hashing Trick to map the raw user ID to one of the 2000 user ID tokens.
4. Construct the input sequence as the user Id token + the sequence of Semantic ID tokens corresponding to a given user’s item interaction history

此处省略各种参数和模型结构，具体见文章第6页。

#### Performance on sequential recommendation (RQ1)

##### Baselines

- GRU4Rec is the first RNN-based approach that uses a customized GRU for the sequential recommendation task.
- Caser uses a CNN architecture for capturing high-order Markov Chains by applying horizontal and vertical convolutional operations for sequential recommendation.
- HGN: Hierarchical Gating Network (HGN) captures the long-term and short-term user interests via a new gating architecture.
- SASRec: Self-Attentive Sequential Recommendation (SASRec) uses a causal mask Transformer to model a user's sequential interactions.
- BERT4Rec: BERT4Rec addresses the limitations of unidirectional architectures by using a bi-directional self-attention Transformer for the recommendation task.
- FDSA: Feature-level Deeper Self-Attention Network (FDSA) incorporates item features in addition to the item embeddings as part of the input sequence in the Transformers.
- $S^3$-Rec: Self-Supervised Learning for Sequential Recommendation $\left(S^3-R e c\right)$ proposes pre-training a bi-directional Transformer on self-supervision tasks to improve the sequential recommendation.
- P5: P5 is a recent method that uses a pretrained Large Language Model (LLM) to unify different recommendation tasks in a single model.

##### Recommendation performance

总体就是比别的模型强2%-30%

![[Table 1.png]]

#### Item representation (RQ2)

对比了其他的ID生成方式，包括随机ID和LSH Semantic ID，好用就完事了

![[Table 3.png]]

#### New capabilities (RQ3)

##### Cold-start recommendation

和KNN比较冷启动性能，纸面上超过了KNN，但是似乎并没有那么强。

##### Recommendation diversity

![[Table 5.png]]

We report the Entropy@K for various temperature values in Table 5. We observe that temperature-sampling in the decoding stage can be effectively used to increase the diversity in the ground-truth categories of the items.

温度很重要

## Discussion

### Invalid IDs

在检索前20项时，只有大约2%的ID无效，问题不大，效果很好。

### Effects of Semantic ID length and codebook size

修改codebook的形状和大小对性能影响不大，说明模型很健壮。

### Inference cost

Beam search太慢了，比KNN慢很多。

## Conclusion

通过对三个数据集的实验，我们表明我们的模型可以实现 SOTA 结果并且可以很好地泛化到新的和未见过的项目。 

我们的工作促成了许多新的研究方向。 

- 探索如何将这些语义 ID 与 LLM 集成以启用更强大的会话推荐模型将会很有趣
- 探讨如何改进语义 ID 表示以及如何使用它进行排名
