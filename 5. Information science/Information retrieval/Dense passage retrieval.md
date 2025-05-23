# Dense passage retrieval

密集段落检索（Dense Passage Retrieval）是一种用于开放域问答系统的技术。该技术通过检索与问题相关的文本段落，然后从这些段落中提取答案来回答问题。

首篇论文：[Dense Passage Retrieval for Open-Domain Question Answering](https://aclanthology.org/2020.emnlp-main.550/)

## Related works

### Surveys

- [Dense Text Retrieval Based on Pretrained Language Models: A Survey](https://dl.acm.org/doi/abs/10.1145/3637870)
	- LLM for DPR survey
	- 这个sruvey似乎直接将粒度从document/passage转变为更灵活的text了

### Repositories

直接相关
- [DenseRetrieval](https://github.com/RUCAIBox/DenseRetrieval)
	- 是综述Dense Text Retrieval Based on Pretrained Language Models: A Survey的仓库，对不同工作进行了分类，也涵盖了其他repositories和数据集
- [pyserini](https://github.com/castorini/pyserini)
	- 这应该是做实验的较理想仓库，里面提供了一系列复现好的的baselines
- [sentence-transformers](https://github.com/UKPLab/sentence-transformers)
	- 这个仓库也很好，即开即用；似乎适用范围更广（怎么有1w+的star）
- [faiss](https://github.com/facebookresearch/faiss)
	- Facebook自己的项目集合（似乎），好像是即开即用的，但是没有调试

间接相关
- [Awesome-Efficient-LLM](https://github.com/horseee/Awesome-Efficient-LLM)
	- 这个是关于高效LLM的仓库，但是其中的Text Compression部分还是有启发的


### Researches

- [Out-of-Domain Semantics to the Rescue! Zero-Shot Hybrid Retrieval Models](https://link.springer.com/chapter/10.1007/978-3-030-99736-6_7)
	- Zero-shot setting & Integration lexical and deep retrieval models
	- 他们实验发现常用的神经网络模型的zero-shot能力不行，因此他们提出了一个简单而有效的框架来集成词汇和深度检索模型。 实验表明，即使深度模型在域外设置中较弱，这两个模型也是互补的。
- [Making Large Language Models A Better Foundation For Dense Retrieval](https://arxiv.org/abs/2312.15503)
	- LLM for dense passage retrieval
	- 核心是用两个新的任务去微调大模型:
		- EBAE (Embedding-Based Auto-Encoding): 给定需要Text，将Text输入结合额外prompt（Text+"The original sentence:⟨\s⟩"）输入LLM，最后使用LLM最后一个token（⟨\s⟩）位置的hidden states作为整个句子的嵌入。而这个任务就是用得到的嵌入去预测/重建原始的Text
		- EBAR (Embedding-Based Auto-Regression): 和上面这个任务一样，只不过后面的嵌入用于预测句子的下一个单词
- [Pre-training with Large Language Model-based Document Expansion for Dense Passage Retrieval](https://arxiv.org/abs/2308.08285)
	- LLM-based document expansion for dense passage retrieval
	- contrastive learning and bottlenecked query generation & [curriculum learning](../../4.%20Artificial%20intelligence/1.%20Major%20goals/Cognition/Machine%20learning/Curriculum%20learning.md)
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






idea：知识压缩，让语言模型能够用一个token长度的内容预测后续语句

每次输出只有一个token长度的嵌入，输入就是前一个嵌入加后一个单词，这样可以极大压缩计算量
