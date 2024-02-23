# Question answering

问答系统（Question Answering，QA）是指能够自动回答用户所提出的自然语言问题的人工智能系统。例如：
- **who** 问题，如“谁是美国的第一任总统？”
- **what** 问题，如“法国的首都是什么？”
- **when** 问题，如“第二次世界大战是什么时候发生的？”
问答系统可以应用于各种领域，例如搜索引擎、客服系统、教育系统等。

# Open-domain question answering

开放域问答系统（Open-domain Question Answering，ODQA）是指能够回答开放性问题（Open-ended questions）的问答系统。开放性问题是指问题没有明确的答案，需要系统进行推理和分析才能回答。例如：
- **what-if** 问题，如“如果我投资比特币，会发生什么？”
- **why** 问题，如“为什么天空是蓝色的？”
- **how** 问题，如“如何制作蛋糕？”
由于 ODQA 使用开放域数据，因此它需要处理大量噪声和不确定性。这使得ODQA成为一个更具挑战性的任务。

## Methods

### Dense passage retrieval

密集段落检索（Dense Passage Retrieval）是一种用于开放域问答系统的技术。该技术通过检索与问题相关的文本段落，然后从这些段落中提取答案来回答问题。

首篇论文：[Dense Passage Retrieval for Open-Domain Question Answering](https://aclanthology.org/2020.emnlp-main.550/)

#### Related works

Surveys:
- [Dense Text Retrieval Based on Pretrained Language Models: A Survey](https://dl.acm.org/doi/abs/10.1145/3637870)
	- LLM for DPR survey
	- 这个sruvey似乎直接将粒度从document/passage转变为更灵活的text了

Repositories:
- [DenseRetrieval](https://github.com/RUCAIBox/DenseRetrieval)
	- 是综述Dense Text Retrieval Based on Pretrained Language Models: A Survey的项目，对不同工作进行了分类，也涵盖了其他repositories和数据集

Researches:
- [Out-of-Domain Semantics to the Rescue! Zero-Shot Hybrid Retrieval Models](https://link.springer.com/chapter/10.1007/978-3-030-99736-6_7)
	- Zero-shot setting & Integration lexical and deep retrieval models
	- 他们实验发现常用的神经网络模型的zero-shot能力不行，因此他们提出了一个简单而有效的框架来集成词汇和深度检索模型。 实验表明，即使深度模型在域外设置中较弱，这两个模型也是互补的。
- [Making Large Language Models A Better Foundation For Dense Retrieval](https://arxiv.org/abs/2312.15503)
	- LLM for dense passage retrieval
	- 用LLaMA微调出LLaRA作为dense RetrievAl
- [Pre-training with Large Language Model-based Document Expansion for Dense Passage Retrieval](https://arxiv.org/abs/2308.08285)
	- LLM-based document expansion for dense passage retrieval
	- contrastive learning and bottlenecked query generation & [curriculum learning](../../2.%20Approaches/Curriculum%20learning.md)
- [Improving Text Embeddings with Large Language Models](https://arxiv.org/abs/2401.00368)
	- LLM for text embedding
- [Retrieve Anything To Augment Large Language Models](https://arxiv.org/abs/2310.07554)
	- LLM-Embedder - 一种统一的嵌入式模型
	- 训练方法包括：reward formulation based on LLMs' feedback, the stabilization of knowledge distillation, multi-task fine-tuning with explicit instructions, and homogeneous in-batch negative sampling
- [Matryoshka Representation Learning](https://proceedings.neurips.cc/paper_files/paper/2022/hash/c32319f4868da7613d78af9993100e42-Abstract-Conference.html)
	- 多粒度的表示学习
- [Questions Are All You Need to Train a Dense Passage Retriever](https://doi.org/10.1162/tacl_a_00564)
	- 让PLM去预测检索出的样本与问题的匹配程度，进而训练检索模型
	- 该方法假设PLM运作良好，这样就可以使用没有标签的数据进行训练了
- [A Personalized Dense Retrieval Framework for Unified Information Access](https://doi.org/10.1145/3539618.3591626)
	- 个性化检索，针对用户的历史给出特定的回答


