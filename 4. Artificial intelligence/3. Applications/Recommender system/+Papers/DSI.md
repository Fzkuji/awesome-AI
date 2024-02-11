Title: Transformer Memory as a Differentiable Search Index

https://arxiv.org/abs/2202.06991
*[Submitted on 14 Feb 2022 ([v1](%5Bv1)), last revised 21 Oct 2022 (this version, v3)]*

**论文代码**：
[ArvinZhuang/DSI-transformers](https://github.com/ArvinZhuang/DSI-transformers)
Requirements: `python=3.8` `transformers=4.17.0` `datasets=1.18.3` `wandb`
> Note: This is not the official repository.
> Check out our new repository for DSI training: https://github.com/ArvinZhuang/DSI-QG

论文解读：
https://www.youtube.com/watch?v=qlB0TPBQ7YY

作者访谈：
https://www.youtube.com/watch?v=C7mUYocWdG0

总结：之前的模型，无论是传统模型还是机器学习模型，都是对问题和文档进行一对一比较，最后挑选相关性较高的结果。但是这个模型设计了文档的索引机制，然后尝试让模型输入问题、直接输出文档索引，也算是端到端了。

- 整体结构：
	- Pre-processing: docs -> |Represeantation| -> doc_tokens
	- Training: 
		- doc_tokens -> |Model| -> IDs
		- Query -> |Model| -> IDs

## Abstract

本文尝试证明知识密集型任务Information retrieval也可以通过a single Transformer来实现。本文提出了Differentiable Search Index (DSI) 模型。

a DSI model answers queries directly using only its parameters, dramatically simplifying the whole retrieval process.
DSI是一个端到端模型，并且证明比[Dual encoder](../../../../5.%20Information%20science/Information%20retrieval/Dual%20encoder.md)要好，在zero-shot设置中也优于[Okapi BM25](../../../../5.%20Information%20science/Information%20retrieval/Okapi%20BM25.md)。

## Introduction

We call this proposed architecture a differentiable search index (DSI), and implement it with a large pre-trained Transformer (Vaswani et al., 2017) model, building on the recent success of large generative language models (LMs). 

In this proposed architecture, all information of the corpus is encoded within the parameters of the Transformer language model.

使用Seq2Seq做检索，最直接的思路就是给定一个query，然后通过BeamSearch生成documents。

但是这里不是完全的Seq->Seq，即直接的query->documents这样的过程。

而是这样的，分解为两个任务：documents<->docids，query->docids。其中第1个任务叫做indexing，即建立document和docid的一一映射，这个docid可以看成是document的一个缩写的摘要（可以看成一个seq2seq任务）。第二个任务叫做retrieval，这里直接根据query生成候选的docids（也可以看成一个seq2seq任务）。所以这里是一个query->docids->documents的间接的检索。

Indexing这个任务主要的目的是让模型能够理解docid和document的映射关系，retrieval部分才是真正的检索。

值得注意的是，indexing和retrieval这两个任务训练的时候，模型参数共享（不然模型可能无法理解docid的含义），使用多任务学习的方式同时进行。

## Differentiable search index  (DSI)

The core idea behind DSI is to fully parameterize traditionally multi-stage retrieve-then-rank pipelines within a single neural model.

Indexing：一个DSI模型必须学会将一个文档 的内容和对应的文档标识docid给联系起来。这篇文章直接使用一个seq2seq模型，把文档内容作为输入，把标识符作为输出。

Retrieval：给定一个查询，一个DSI模型应该能够返回一个排序的候选docids列表，这是通过自回归的生成实现的(也是seq2seq)。

### Indexing Strategies

我们研究了旨在学习文档与其标识符之间关联的各种索引策略。 我们训练我们的模型来预测给定文档标记序列的 docids。 这使我们的模型能够了解哪个标识符属于哪个文档，并且可以被认为是对传统搜索索引的可微分。 我们考虑了各种替代方案，并在后续部分中取消了这些设置。 

最后采用的策略是具有直接索引的 Inputs2Targets。

#### Indexing Method

- Inputs2Target: $\text { doc\_tokens } \rightarrow \text { docid }$
- Targets2Inputs: $\text { docid } \rightarrow \text { doc\_tokens }$
- Bidirectional: both Inputs2Targets and Targets2Inputs. 
	- 但是会在开始的部分加一个前缀，用以表明任务的方向。
- Span Corruption: span corruption-based denoising with the inclusion of docid tokens
	- 这个做法和T5的处理有些类似。就是将docid当作前缀和doc_tokens拼接起来，然后使用T5的方式来进行预训练。作者说这样做有两个好处：
		- （1）同样可以在索引的时候进行通用的预训练
		- （2）实现ocid作为去噪目标和输入的良好平衡。

Inputs2Targets和Bidirectional公式表现最好，与前者相比，Bidirectional方法表现稍差。 最后，Targets2Inputs 和Span Corrpution准确度为 0%。 这表明索引策略之间可能存在巨大差异，其中一些策略运行良好，而另一些则完全不起作用。

##### Span Corruption

The authors are proposing a method that combines span corruption-based denoising, as introduced in the T5 paper (Raffel et al., 2019), with the inclusion of document identifier (docid) tokens. This approach aims to improve the performance of the transformer-based memory model for information retrieval tasks.

Here's a breakdown of the method:

1.  Docid tokens: Each document in the dataset is assigned a unique identifier (docid). This identifier is added as a prefix to the document tokens.
2.  Span corruption: The input text, which now includes the docid tokens, is subjected to span corruption-based denoising. The corruption process involves randomly selecting spans of tokens (including the docid tokens) and applying deletion, replacement, or insertion operations.
3.  Dual purpose: This method serves two purposes. First, it performs general pre-training during the indexing process, allowing the model to learn better language representations. Second, it maintains a balance between using docid tokens as input and as denoising targets. This balance helps the model learn to associate documents with their identifiers effectively.
4.  Training objective: The model is trained to reconstruct the original, uncorrupted text (including the docid tokens) from the corrupted input text. This process helps the model learn the structure and context of the language, as well as the relationship between documents and their identifiers.

By combining span corruption-based denoising with docid tokens, the authors aim to create a transformer memory model that is better suited for information retrieval tasks. This approach can potentially improve the model's performance in tasks such as document ranking, passage retrieval, and question-answering by leveraging the learned associations between documents and their identifiers.

#### Document Representation Strategies

- **Direct Indexing**: This strategy represents a document exactly. We take the first L tokens of a document, with sequential order preserved, and associate them with the docid. 

- **Set Indexing**: Documents may contain repeated terms and/or non-informative words (e.g., stopwords). This strategy de-duplicates repeated terms using the default Python set operation and removes stopwords from the document. The rest of the document after filtering is passed into the model in similar fashion to the direct index. 

- **Inverted Index**: This strategy maps chunked documents (contiguous blocks of tokens) instead of entire documents directly to the docid. We randomly subsample a single contiguous chunk of k tokens and associate them with the docid. The key advantage of this approach is to allow looking beyond the first k tokens.


### Representing Docids for Retrieval

得到document id之后，还需要对id进行表示，然后才能用于检索。

#### Unstructured Atomic Identifiers

Assign each document an arbitrary (and possibly random) **unique integer ID**

In this case, models are trained to emit one logit for each unique docid $\left(\left|N_{\text {documents }}\right|\right)$. 

To accommodate this, we extend the output vocabulary of a standard language model as follows:
$$
O=\operatorname{Softmax}\left(\left[W_{\text {tokens }} ; W_{\text {docs }}\right]^T h_{\text {last }}\right)
$$
where [;] is the row-wise concatenation operator, $W_{\text {tokens }} \in \mathbb{R}^{d_{\text {model }} \times\left|N_{\text {tokens }}\right|}$ and $W_{\text {docs }} \in$ $\mathbb{R}^{d_{\text {model }} \times\left|N_{\text {documents }}\right|} . h_{\text {last }}$ is the last layer's hidden state $\left(\in \mathbb{R}^{d_{\text {model }}}\right)$ of the decoder stack. To retrieve the top-k documents for a given query, we simply sort the output logits and return the corresponding indices. This is also reminiscent of standard listwise learning to rank where all documents are considered at once.

直接将文档ID作为可预测的结果，比如一篇文档的ID是数字“1125”，那么模型会直接输出对于“1125”这个标签，相应的概率是多少。这种方法类似于分类任务。

#### Naively Structured String Identifiers

不同于上面的做法，这种方法将文档ID的数字“1125”看作是一个字符串，这样模型在预测的时候就直接输出序列化的文本“1125”，这样整个模型就是seq2seq的了。

#### Semantically Structured Identifiers

这种方法就是不使用随机的ID，而是设计一些存在语义的ID。即：
1. the docid should capture some information about the semantics of its associated document
2. the docid should be structured in a way that the search space is effectively reduced after each decoding step

In this work, we treat this as **a fully unsupervised pre-processing step**. However, as part of future work it may be possible to integrate and automatically learn semantic identifiers in a fully end-to-end manner. ^576d1d

To construct identifiers with this property, we employ **a simple hierarchical clustering process** over document embeddings to induce a decimal tree (or more generally, a trie).

![600](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Hierarchical%20clustering%20%20process.png)

如图，核心操作就是将所有文档分为0-9十个类别。在细分类别中，
- 如果文档数量仍然大于$c$，那么就递归上述操作
- 如果文档数量小于$c$，那么就把类别内的文档从$0$到$c-1$标上标签。

当然，划分为多少个类别、$c$的值都是可以随意设定的。作者选择了划分为10类、$c=100$。

### Training and Optimization

- optimized for seq2seq cross entropy loss
- [Teacher forcing](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Teacher%20forcing.md)


1. Pre-train a model to perform indexing and fine-tuning the model to perform indexing retrieval
2. The second strategy is to train them together in a multi-task setup
	- 类似于 T5 式协同训练的方式构建协同训练任务（例如，使用任务提示来区分它们）

后者表现明显更好，尤其是当索引对检索任务示例的比例很高时。 因此，我们采用多任务学习作为默认策略。 在这里，我们观察到我们的设置是独一无二的，不同于传统的多任务学习或迁移学习。 在典型的多任务设置中，两个任务具有共同点，如果一起学习可以提高两个任务的性能。 然而，在我们的设置中，检索任务完全依赖于索引任务。 特别是，如果没有索引任务，检索任务所利用的标识符将完全没有意义。 因此，为了解决任务 B（检索），模型需要充分学习任务 A（索引）。此问题设置提出了 ML 社区可能感兴趣的独特且很大程度上未被探索的研究挑战。 ^7ef472

## Evaluation

### Dataset

[Natural Questions (NQ)](NQ)) NQ consists of 307K query-document training pairs and 8K validation pairs, where the queries are natural language questions and the documents are Wikipedia articles. Given a question, the retrieval task is to identify the Wikipedia article that answers it.

We construct three sets from NQ to form our testbed, namely **NQ10K**, **NQ100K**, and **NQ320K** denoting different numbers of total query-document pairs in the combined train and validation splits.

- NQ320K is the full NQ set and uses its predetermined training and validation split for evaluation purposes.
- NQ10K and NQ100K constructs randomly sampled validation sets.

### Metrics

We evaluate our models on Hits@N where N={1, 10}. This metric reports the proportion of correct documents ranked in the top N predictions.

### Implementation Details

DSI模型大小：Base (0.2B), Large (0.8B), XL (3B) and XXL (11B)

> 基于我们早期对各种 DSI 设置的消融实验，呈现的主要结果使用直接索引 (L = 32) 和 Inputs2Targets 索引策略。 我们展示了所有 docid 表示方法的结果。

具体见文章

### Baselines

- BM25 (gensim)

- T5-based dual encoders ([Sentence-T5](https://arxiv.org/abs/2108.08877))
	- train with contrastive learning on the NQ pairs until convergence (≈ 10K steps)
	- obtain top-k nearest neighbors with a system similar to [ScaNN](https://arxiv.org/abs/1908.10396)
	- unsupervised Sentence-T5, which have been specially pre-trained with a similarity learning task, for zero-shot retrieval
	- why Sentence-T5?
		- 完全相同的预训练模型
		- 微调的 T5 双编码器被认为在架构和方法上与 DPR 非常相同

### Experimental Results


- Top-1 (called Hits@1 in the paper)
	- for a base-sized T5 model
		- on NQ10K
			- improved by more than 20 points, from 12.4% for a DE to 33.9% for DSI
		- on NQ320K
			- improved by nearly 7 points
	- for an 11B-parameter T5 model
		- on NQ10K
			- improves by more than 25 points over DE
		- on NQ320K
			- improved by more than 15 points
	- for zero-shot setting
		- improving by 14 points over BM25


|  |  |  |  | NQ10K |  | NQ100K |  | NQ320K |  |
| :--- | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Model | Size | Params | Method | Hits@ 1 | Hits@ 10 | Hits@ 1 | Hits@ 10 | Hits@ 1 | Hits@ 10 |
| BM25 | - | - | - | 12.4 | 33.5 | 20.9 | 46.4 | 11.6 | 34.4 |
| T5 | Base | 220M | Dual Encoder | 16.2 | 48.6 | 18.7 | 55.2 | 20.5 | 58.3 |
| T5 | Large | 800M | Dual Encoder | 18.8 | 55.7 | 22.3 | 60.5 | 22.4 | 63.3 |
| T5 | XL | 3B | Dual Encoder | 20.8 | 59.6 | 23.3 | 63.2 | 23.9 | 65.8 |
| T5 | XXL | 11B | Dual Encoder | 22.1 | 61.6 | 24.1 | 64.5 | 24.3 | 67.3 |
| DSI | Base | 250M | Atomic Docid | 13.0 | 38.4 | 23.8 | 58.6 | 20.7 | 40.9 |
| DSI | Large | 800M | Atomic Docid | 31.3 | 59.4 | 17.1 | 52.3 | 11.6 | 37.6 |
| DSI | XL | 3B | Atomic Docid | 40.1 | 76.9 | 19.0 | 55.3 | 28.1 | 61.9 |
| DSI | XXL | 11B | Atomic Docid | 39.4 | 77.0 | 25.3 | 67.9 | 24.0 | 55.1 |
| DSI | Base | 250M | Naive String Docid | 28.1 | 48.0 | 18.7 | 44.6 | 6.7 | 21.0 |
| DSI | Large | 800M | Naive String Docid | 34.7 | 60.5 | 21.2 | 50.7 | 13.3 | 33.6 |
| DSI | XL | 3B | Naive String Docid | 44.7 | 66.4 | 24.0 | 55.1 | 16.7 | 58.1 |
| DSI | XXL | 11B | Naive String Docid | 46.7 | 77.9 | 27.5 | 62.4 | 23.8 | 55.9 |
| DSI | Base | 250M | Semantic String Docid | 33.9 | 57.3 | 19.0 | 44.9 | 27.4 | 56.6 |
| DSI | Large | 800M | Semantic String Docid | 37.5 | 65.1 | 20.4 | 50.2 | 35.6 | 62.6 |
| DSI | XL | 3B | Semantic String Docid | 41.9 | 67.1 | 22.4 | 52.2 | 3.1 | 66.8 |
| DSI | XXL | 11B | Semantic String Docid | 48.5 | 72.1 | 26.9 | 59.5 | 40.4 | 70.3 |


![DSI performance](../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/DSI%20performance.png)

## Future work

1. 非结构化的document Identifiers的改进：非结构化的id在不同模型上并不统一，且难以理解，作者认为可能是新初始化softmax层导致的，但是他们决定留到以后研究。非结构化原子标识符的不稳定性和高方差，不同数据集的性能不一致。不过非结构化原子标识符在zero-shot上表现很好。
2. [](.md#^576d1d%7Cautomatically%20learn%20semantic%20identifiers%20in%20a%20fully%20end-to-end%20manner)
3. [](.md#^7ef472%7C%E9%9D%9E%E5%85%B8%E5%9E%8B%E5%A4%9A%E4%BB%BB%E5%8A%A1%E5%AD%A6%E4%B9%A0)


multiple indexing, document representation and docid representation methods


## Possible questions

**Q1: 模型输出的格式，包括每次输出几个ID，除了ID是否包含其他信息？**

Used **WebPilot**

Used **Scholar Assist**

在这篇名为"Transformer Memory as a Differentiable Search Index"的论文中，模型的输出是一系列的文档ID（docids）。具体来说：

1. 模型被训练来根据查询生成docids。
2. 在推理阶段，模型接收一个查询作为输入，并输出一个排名的docids列表。
3. 模型以自回归的方式一次输出一个docids。
4. 除了docids，模型不输出任何其他信息。

总结一下：

- 模型的输出格式是一系列的docids
- 它一次输出一个docid
- 除了docids，它不包含任何其他信息

这篇论文的关键思想是将整个文档检索过程在一个神经网络中表示出来，将其框定为一个文本生成任务。模型被训练来根据查询生成相关的docids，排名的docids列表作为检索的输出。

**Q2: 这篇文章的损失函数怎么确定？如何获取top N的预测？**

Used **Scholar Assist**

Used **Scholar Assist**

损失函数：在这篇论文中，DSI模型使用标准的seq2seq交叉熵损失进行优化，并且使用教师强制进行训练。具体来说，损失函数是：

L = -log(P(doc_id|doc_tokens))

其中，P(doc_id|doc_tokens)是给定输入文档标记doc_tokens时正确doc_id的概率。

获取Top N预测：为了获取Top N预测（用于检索），论文使用了两种方法：

1. Beam search：使用beam search，模型在每一步生成Top K候选doc_ids，并从这些候选项中继续扩展。这最终给出了一个排名的N个doc_ids列表。
2. 排序softmax输出：对于非结构化的原子doc_id表示，模型输出所有可能doc_ids的softmax。然后，可以通过简单地排序这些softmax输出来获取Top N个doc_ids。

所以总结起来，损失函数是标准的seq2seq交叉熵损失，Top N预测是通过使用beam search（对于结构化的doc_id表示）或通过排序softmax输出（对于非结构化的原子doc_ids）来获得的。
