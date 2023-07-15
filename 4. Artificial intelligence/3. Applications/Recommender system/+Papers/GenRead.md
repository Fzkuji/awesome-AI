Title: Generate rather than Retrieve: Large Language Models are Strong Context Generators

https://arxiv.org/abs/2209.10063

ICLR 2023

该工作由美国圣母大学和美国微软研究院等单位完成，圣母大学蒋朦教授和微软研究院朱晨光老师指导，论文第1作者为于文豪博士。

总结：文章主要思想是使用大语言模型自动生成文档级别Prompt，使得在问答任务上效果更好。基于的观察是给大模型提供的问题信息越丰富，GPT给出的答案就越准确。结果证明大模型二次预测效果更好，与传统方法一起使用更佳。

## Abstract

知识密集型任务，例如开放域问答 (Open-domain QA)，需要访问大量的世界或领域知识。知识密集型任务的一种常见方法是采用先检索后阅读的框架，该框架先从维基百科等外部语料库中检索一些相关的上下文文档，然后根据检索到的文档预测答案。

在本文中，
- 我们提出了一种通过用大型语言模型生成器替换文档检索器来解决知识密集型任务的新观点。我们称我们的方法为先生成后读取 (GenRead)，它首先提示大型语言模型根据给定问题生成上下文文档，然后读取生成的文档以生成最终答案。
- 此外，我们提出了一种新颖的基于聚类的prompt方法，该方法选择不同的prompt，以生成涵盖不同观点的多样化文档，从而更好地从不同角度得到正确答案。

我们对三种不同的知识密集型任务进行了广泛的实验，包括开放域 QA、事实检查和对话系统。值得注意的是，GenRead 在 TriviaQA 和 WebQ 上获得了 71.6 和 54.4 的准确率，在没有从任何外部知识源检索任何文档的情况下，显著优于最先进的先检索后读取框架 DPR-FiD + 4.0 和 + 3.9。最后，我们证明了通过结合检索和生成可以进一步提高模型性能。

## Introduction

### Research direction

#### Retrieval-augmented language models

![[Retrieval-augmented language models.png]]

Pros：
- Document corpus is updatable. New information can be easily plugged in.
	新的数据很容易更新进数据库
- More efficient! Usually, a retriever model is small, not requiring to store all world knowledge into model parameters.
	更加高效，比大语言模型更小

Cons：
- Documents are fixed. They might contain noisy or irrelevant information.
	文档虽然可以更新，但是通常都是固定的，并且包含了很多无关内容（推广）
- Retrievers are based on $simi(\vec{q}, \vec{p})$:
	- Shallow interactions of $(\vec{q}, \vec{p})$
	- Different semantic space of $\vec{q}, \vec{p}$
	信息获取通常是对比问题和文档的相似度，但是问题和文档往往不是完全的语义一致的

#### Information seeking via big language models

![[Information seeking via big language models.png]]

Pros：
- Better knowledge storage (>100B), strong deductive reasoning capabilities.
	大模型学习的内容更多
- Generated information are more specific to the given input, i.e., customized output.
	提供的信息都是过滤过的，因此不会提供大量无用的信息

Cons：
- (Generative) Large language models still often hallucinate wrong information.
	容易编造假信息
- Updating new information into model parameters is hard; which if often non-affordable in academic labs.
	新信息很难更新

#### Comparison

![[RAG vs GPT.png]]

通常还是专门设计的模型更强。由此引出一个问题，GPT-3是否知道很多问题的答案？

![[does GPT knows the answer.png]]

事实证明GPT是训练过很多问题的答案的，但是对提问者的提问方式有要求。经验上，提问者需要提供更多的背景信息，而不是简短的一句话。这样模型更有可能找到真实的结果。

### Contribution

1. 我们提出了一种新的生成然后读取的框架来解决知识密集型任务，即通过prompt大型语言模型(例如GPT-3)生成相关上下文文档来取代从维基百科检索文档或在谷歌上搜索相关文档的过程。

2. 我们提出了一种新颖的基于聚类的prompt方法来生成多个不同的上下文文档，从而增加覆盖正确答案的可能性。我们证明这种方法可以显著提高最终 QA 和其他下游任务的性能。

3. 我们在 zero-shot 和监督设置下对三个知识密集型 NLP 任务进行了广泛的实验。值得注意的是，我们的方法可以匹配甚至优于先检索后读取的方法，而无需从任何外部知识源检索任何文档。

## Methods

**核心思路**：Generate-then-Read 首先提示大型语言模型生成关于给定查询的上下文文档，然后读取生成的文档以预测最终答案。阅读器可以是一个大型模型（例如 GPT-3），用于 zero-shot 或者 few-shot 的情况，或一个小的可训练的阅读器（例如 FiD），在目标数据集的训练分割上使用生成的文档进行微调。

### Zero-shot setting

| baseline                        | this work |
| ------------------------------- | --------- |
| 给出一个问题，使用GPT-3生成结果 | 给出一个问题$q$，用GPT-3生成结果$d$，再将$q,d$再次输入GPT-3得到最终结果$p$          |

其中，使用GPT-3生成的结果$d$可以是很多份。We can use any decoding strategy (e.g., greedy decoding, beam search), but we used greedy decoding throughout the zero-shot experiments for simplicity and reproducibility.

即生成多份文档，然后再次将问题和生成的文档统一输入GPT，期望GPT能够输出我们想要的值。

### Supervised setting

另一种思路是使用常规的Reader来检索GPT生成的文档中有用的部分。常规的Reader一般是使用监督学习训练的，这就引出了其他一些问题。

这部分的主要考虑就是，使用FiD（常规的Reader）来检索GPT生成的文档。我们希望GPT-3产生多个文档，覆盖的信息越多越好，但是相同的问题往往给出相同的答案，因此需要改进方法是的生成多样化。

本文给出的方法为：
- 多样化人工prompt
- 基于聚类的prompt

#### Diverse human prompts

顾名思义就是雇人从各种角度提问GPT。

缺点：
- 不容易泛化到其他领域
- 不同的大语言模型不通用，一个prompt可能只对某个大语言模型有效

#### Clustering-based prompts

本质上就是使用现有文档（维基百科/生成的），在聚类之后用作prompt

Step 1：获取文档
- 选项1：对于每个训练集中的问题，让GPT在zero-shoting的情况下生成一个答案
- 选项2：使用无监督Retriever获取维基百科的文档
两种方法都可以获得一系列的问题-文档对$\left\{q_{i}, d_{i}\right\}_{i=1}^{|\mathcal{Q}|}$

Step 2：编码每个文档，做聚类处理
使用一个大语言模型encode文档对，比如使用GPT模型$\mathbf{e}_{i}=\operatorname{GPT-3}\left(\left[q_{i}, d_{i}\right]\right)$，每个文档编码成12288维的vector。然后使用K-means聚类所有的vectors为$K$类，相当于每个文档对都有一个类别，$K$的数量作者做了实验。

![[Recall@K on test sets.png]]

Step 3：采样并生成$K$个文档
在聚类的结果基础上，对于每个类别$c$都采样$n$个问题-文档对，表示为$\left\{q_{c 1}, d_{c 1} ; q_{c 2}, d_{c 2} ; \ldots ; q_{c n}, d_{c n}\right\}$，实验证明$n$增加对性能影响不大，这里设置为5。

采样的问题-文档对可以作为in-context demonstrations。For example, the input to the
large language model could be "{$q_{c1}$ placeholderg} {$d_{c1}$ placeholderg} ... {$q_{cn}$ placeholderg} {$d_{cn}$
placeholder} {input question placeholder}". 又因为我们有$K$个类，因此每个问题我们都可以尝试$K$次，最终每个问题可以生成$K$个文档。

## Evaluation

![[Performance.png]]

- 将GPT输出的结果再输入GPT，得到的结果就会更好
- 用GPT作为文档生成器比传统生成器更好

发现：
- 把传统文档生成器和大语言生成器一起使用效果更好
- 其他大语言模型也可以，不过GPT更好










