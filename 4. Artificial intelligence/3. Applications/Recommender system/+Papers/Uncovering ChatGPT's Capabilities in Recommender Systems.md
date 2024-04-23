Title: Uncovering ChatGPT's Capabilities in Recommender Systems

Link: https://arxiv.org/abs/2305.02182

Code: https://github.com/rainym00d/LLM4RS

Additional information: 

Conclusion: 


## Abstract

ChatGPT的首次亮相最近吸引了自然语言处理（NLP）社区和其他社区的注意。现有的研究表明，ChatGPT在一系列下游的NLP任务中表现出明显的改进，但ChatGPT在推荐方面的能力和局限性仍不清楚。

在这项研究中，我们旨在从信息检索（IR）的角度对ChatGPT的推荐能力进行实证分析，包括点式、对式和列表式的排名。为了实现这一目标，我们将上述三种推荐策略重新表述为一种特定领域的提示格式。通过在不同领域的四个数据集上的广泛实验，我们证明了ChatGPT在所有三种排名政策上都优于其他大型语言模型。基于对单位成本改进的分析，我们发现，与点式排名和对式排名相比，具有列表式排名的ChatGPT实现了成本和性能之间的最佳平衡。此外，ChatGPT显示了缓解**冷启动问题**和**可解释建议**的潜力。为了促进这一领域的进一步探索，完整的代码和详细的原始结果已在https://anonymous.4open.science/r/LLM4RS-532C/ 上公开。

## Introduction

### Background

作者想测试ChatGPT的推荐能力。

推荐系统的基本目标是为用户提供个性化的top-K排名列表，以减少信息过载。

### Problems

首先这是一个[Learning to rank](../../../../5.%20Information%20science/Information%20retrieval/Learning%20to%20rank.md)问题

本文作者也这么推荐，将这三种排名方式设计为[prompt](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/Large%20language%20model/Prompting.md)去询问ChatGPT。

### Contributions

- ChatGPT比其他LLMs强
- ChatGPT在list-wise和pair-wise排名中表现良好，而在point-wise排名上表现较差
- ChatGPT可以缓解:
	- [](../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md#^8ee569%7C%E5%86%B7%E5%90%AF%E5%8A%A8%E9%97%AE%E9%A2%98)，因为它在有限的训练数据下比传统的推荐模型（如MF和NCF）更出色
	- [](../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md#^fa35f7%7C%E5%8F%AF%E8%A7%A3%E9%87%8A%E6%80%A7%E9%97%AE%E9%A2%98)，并表现出真正理解项目相似性的能力
- 考虑到单位成本的性能提升，作者建议在实践中使用ChatGPT进行list-wise ranking

## Probing ChatGPT for recommendation capabilities

### Three Ranking Capabilities in RSs

目前[Learning to rank](../../../../5.%20Information%20science/Information%20retrieval/Learning%20to%20rank.md)（LTR）方法包含三种范式：point-wise, pair-wise and list-wise ranking

（作者这里给出了三者的数学定义，具体看论文）

### Reformulate and Adapt Recommendation with Prompts

使用[prompt](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/Large%20language%20model/Prompting.md)设计[LTR](../../../../5.%20Information%20science/Information%20retrieval/Learning%20to%20rank.md)的三种范式：

![Pasted image 20230516162335](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Uncovering%20ChatGPT's%20Capabilities%20in%20Recommender%20Systems/IMG-20240214165835700.png)

## Evaluation

### Experimental Settings

#### Datasets.

四个任务：
Movie: MovieLens-1M
Book: the “Books” subset of Amazon Reviews
Music: the “CDs & Vinyl” subset of Amazon Reviews
News: MIND-small

#### Evaluation Protocols.

We set the number of shots as 1 for pair-wise and list-wise, and 2 for point-wise.

两个常用损失函数：
- [](../../../2.%20Approaches/Artificial%20neural%20network/Loss%20function.md#^62ea0d%7CNormalized%20Discounted%20Cumulative%20Gain%20(NDCG)) with 𝑘 = 1, 3
- [](../../../2.%20Approaches/Artificial%20neural%20network/Loss%20function.md#^0fb470%7CMean%20Reciprocal%20Rank%20(MRR)) with 𝑘 = 3

论文声明了一个新的损失函数Compliance Rate：
$$
\frac{\text { Number of Valid Answers }}{\text { Number of Test Samples }}
$$
本质上是判断模型是否按要求回答了问题，比如prompt让模型输出Yes/No，那么模型在回答时是否回复了Yes/No，如果按这个格式回复了，就认为是遵守（Compliance）了规则。其实没什么新意，也没什么用。

### RQ1: Main Results

结论：

（废话部分）
- ChatGPT比[](../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md#^c6dd12%7C%E9%9A%8F%E6%9C%BA%E6%8E%A8%E8%8D%90)要好
- ChatGPT比其他大模型好

（有用的）
- ChatGPT在NDCG@1 metric for list-wise ranking on the news domain上差一点


值得注意的是，作者修改了LLM输出的概率，也就是调整了ChatGPT的[logit bias](https://help.openai.com/en/articles/5247780-using-logit-bias-to-define-token-probability)


未完待续。。。

![Pasted image 20230517163542](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Uncovering%20ChatGPT's%20Capabilities%20in%20Recommender%20Systems/IMG-20240214165835716.png)

![Pasted image 20230517163604](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Uncovering%20ChatGPT's%20Capabilities%20in%20Recommender%20Systems/IMG-20240214165835735.png)















## Conclusion

作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。




## Appendices

### Appendix A



### Appendix B



## Future work

值得研究的点








