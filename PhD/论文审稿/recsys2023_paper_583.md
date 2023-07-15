Title: Knowledge Graph Completion Models are Few-shot Learners: An Empirical Study of Relation Labeling in E-commerce with LLMs

总结：这篇文章使用LLM做电商知识图谱的补全（Knowledge Graph Completion (KGC)）任务。实验中，通过设计prompt和使用few-shot learning，作者认为LLM比传统的补全模型要强40%，和人工标注媲美。

相关KGC工作：
- 2022 [SimKGC: Simple contrastive **knowledge graph completion** with pre-trained **language models**](https://arxiv.org/abs/2203.02167)用了对比学习
- 2019 [KG-BERT: BERT for **knowledge graph completion**](https://arxiv.org/abs/1909.03193)用了BERT
- 2022 [From discrimination to generation: **knowledge graph completion** with generative transformer](https://dl.acm.org/doi/abs/10.1145/3487553.3524238)用了生成transformer

## Abstract

Knowledge Graph很重要，但Knowledge Graph的relation labeling耗时耗力。LLM在NLP很成功。
本文尝试用LLM做**e-commerce KGs的关系补全/Knowledge Graph Completion (KGC)**
- 1 or 5 shot leanrning
- prompt designing
结论就是LLM可以替代人工标注

## Introduction

### 介绍知识图谱补全

- 挑战：
	- 电商领域的多变
	  <u style="color:red">虽然作者声称尝试解决电商KGC任务，但是通篇并没有关注这个问题。因此，下面他们声称，他们的工作是第一个做这个任务的，这样的结论似乎显得有点牵强</u>
	- 日益增长的人力成本

### 介绍LLM

- In-context learning
- effectiveness with limited labeled data.

### 介绍本工作

- 任务：预测节点关系类型，让模型判断是互补、替代还是无关

- 实验：evaluate PaLM and GPT-3.5
	- 1 or 5-shot learning
	- design and test different prompts

- 结论：LLMs比传统模型好
	- 可以替代人工标注
	- 可以提供解释并说服人类

- 贡献：
	- 首次尝试将LLM应用到电商KGC任务
	  <u style="color:red">锁定的范围有点小，虽然不是首次使用LLM，但是确实是首次使用电商数据集</u>
	- 尝试了多种prompt，使用了few-shot learning
		- LLMs达到了人类水平
		- LLMs比传统方法强40%（本论文的传统方法是19年的）
		- LLMs标注样本少也没关系

## Experiment settings

### Datasets

2个数据集，1400个样本，3种类别

- Electronics
	- 1045 pairs
		- 769 for 'irrelevant'
		- 264 for 'complementary'
		- 12 for 'substitutable'
- Instacart
	- 400 pairs
		- 244 ‘irrelevant’
		- 166 ‘complementary’
		- 10 ‘substitutable’ labels

<u style="color:red">感觉数据有点少，同时标签不平衡，后面导致了LLM预测结果的波动，同时导致传统KG模型无法拟合</u>

### Metrics

- Accuracy
- Precision
- Recall

## Prompt engineering

### Prompt principles

作者设计了3种prompt：

![[Pasted image 20230518210821.png]]
意料之中的结果，从左到右Accuracy分别为：0.575、0.676和0.738

### Results
![[Pasted image 20230518213921.png]]
比较凌乱的结果，得出的结论为：
- PaLM在Accuracy上比ChatGPT好
- Substitutable（替代关系）的Precision和Recall都比较低，作者解释是因为substitutable出现次数较少，因此容易受到错误预测的影响
  <u style="color:red">同样是很容易得出的结果，感觉应该可以通过调整logit bias，修改预测结果的概率分布，结果可能会稳定一点</u>
- few shot可以提升性能

<u style="color:red">作者虽然做出了解释，但是这样的实验结果并不能给出太多新颖的见解</u>

## LLM as individual human labeler

本质就是和人类进行对比性能

主要分为两个部分：
- 比较LLM模型和人类的差距
- 分析人类标注员的偏见

### LLM Results vs Individual Human Labelers (independent labeling)

用一个人类的判断作为ground truth，让LLM和另一个人类尝试预测
![[Pasted image 20230518215422.png]]
通过Accuracy看出LLM和人类有一点差距但是很相近了。Recall也说明LLM没有“替代关系”和“补充关系”的偏见，不会考虑标签不平衡问题。

### Human Relabeling based on LLM Results (dependent labeling)

分析人类的局限，让人类读完LLM生成的结论后，再次进行判断
![[Pasted image 20230518221606.png]]

- Electronics上，电子产品比较客观，所以人类预测结果差不多
- Instacart上，普通商品比较看个人背景，因此两个标注员差得挺多
- LLM给出的解释能够说服标注员，让其修改答案

## Comparision experiments

LLM对比传统方法：TransE, TransR, DistMult, ComplEx, RESCAL, R-GCN and CompGCN

两个数据集都尝试了，80% for training, 10% for validation

![[Pasted image 20230519172847.png]]
结果是LLM远超其他模型。
<u style="color:red">作者使用的baseline也是2019年及之前的论文，不知道现在的KG模型性能怎么样</u>

- 作者似乎认为训练传统模型需要大量的数据，而LLM不需要，因此在小数据集上有优势。
  <u style="color:red">然而LLM毕竟已经在超大规模的数据上训练了，直接这样比较感觉不是很公平</u>

- 同时作者指出，Instacart由于数据集太小，baseline直接train不了。
  <u style="color:red">感觉这样的对比有问题，至少也得是让传统KG模型在大数据集上微调吧</u>

<u style="color:red">总之感觉这样的设计说服力较低</u>

## Related works

### Knowledge Graph Completion in E-commerce

列举了传统方法：TransE, TransR, DistMult, ComplEx, RESCAL, R-GCN and CompGCN

- 传统方法无法理解自然语言
- 训练传统模型需要大量的标注数据，但是标注数据需要大量人力成本

### LLM Applications in E-commerce

分析了LLM应用于推荐的优势
- 能够理解自然语言
- 聊天提升用户体验

这也是为什么作者想尝试LLM在KGC上的效果

## Conclusion

作者分析了LLM在KGC任务上的效果
尝试解决传统模型的困难：
- 有限的标签数据
- 昂贵的数据标注

结论：
LLM比传统模型好

## Review

Summary: In this study, large language models were used to complete an e-commerce knowledge graph. The author created different prompts and tested few-shot learning to compare PaLM and GPT-3.5 with traditional knowledge graph models and human performance. The results showed that large language models are comparable to human performance.

### Weak points

1. Although the paper claims that the e-commerce knowledge map has the challenge of the dynamic nature of e-commerce domains, and its work is the first attempt,the corresponding solution was not clearly shown in the experiments. At the same time, there has been a previous work on knowledge map completion using BERT [1].

2. Less training data. In the comparison between LLM and traditional KG models, it seems difficult to fit these KG models. The impact of doing so is mentioned in the conclusion, and an attempt is made to reflect the superiority of LLM. However, LLM itself has been trained with a large amount of data, and the direct comparison is not convincing. And the article does not mention whether other KG models are pre-trained on a larger data set.

3. Data imbalance problem. In the datasets, there are few "substitutable" situations, and LLM usually does not consider this preference, which leads to the difference in precision and recall of different relations when LLM models give the predictions. Using logit bias to define token probability is a possible solution.

[1]. Yao, Liang, Chengsheng Mao, and Yuan Luo. "KG-BERT: BERT for knowledge graph completion." arXiv preprint arXiv:1909.03193 (2019).

### Strong points

1. This work sets up a series of experimental ideas, comparing between LLMs, LLMs vs people, and LLMs vs traditional models, and obtains valuable experimental data.

2. This paper considers the relational completion of the e-commerce knowledge graph, and its timeliness makes this task more difficult. This work exploits both human and LLM opinion biases using two categories of datasets.

3. This article innovatively allows human annotators to read the results of LLMs, which reflects the transcendence level of LLM and proves the possibility of LLM replacing manual labor in data annotation.

### Summary

In this study, large language models were used to complete an e-commerce knowledge graph. The author created different prompts and tested few-shot learning to compare PaLM and GPT-3.5 with traditional knowledge graph models and human performance. The results showed that large language models are comparable to human performance.

Considering that (1). this work, as claimed to be the first attempt to apply LLMs to KGC tasks in e-commerce contexts, does not demonstrate differences from existing methods., (2). the small dataset used and its imbalance are not well handled, (3). No comparison with other causal frameworks both theoretically and empirically; (4). presentation is under-organized with subjective words and related works[4] are under-referenced, this work is not suggested to publish.