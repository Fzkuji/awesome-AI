Information retrieval (IR) systems map a user query $q \in \mathcal{Q}$ to a ranked list of relevant documents $\left\{d_{1}, \ldots, d_{n}\right\} \subseteq \mathcal{D}$, typically represented by integers or short strings called *document identifiers* (docids).

The most widely used IR approaches are based on pipelined *retrieve-then-rank* strategies. 
For retrieval, there are:
- inverted indexes
- nearest neighbor search
- contrastive learning based dual encoders (DEs) are the present state-of-the-art (Written in 2023).
([[GenRead]])

Dual encoders have been used for question answering (QA) and information retrieval (IR) tasks with good results. Previous research focuses on two major types of dual encoders:
- Siamese Dual Encoder (SDE), with parameters shared across two encoders;
- Asymmetric Dual Encoder (ADE), with two distinctly parameterized encoders
(https://arxiv.org/pdf/2204.07120.pdf)

记录的工作：
- 传统方法
	- [[Okapi BM25]]
- 机器学习方法：
	- Dual Encoder
		- Siamese Dual Encoder
		- Asymmetric Dual Encoder
	- [[DSI]]



## IR vs IF

信息过滤（IF）和信息检索（IR）虽然都是帮助用户找到他们需要的信息的技术，但它们在使用方式和目标上有一些关键的区别：

1. 用户的主动性：信息检索通常是用户主动的过程。用户提供一个查询，然后系统返回与这个查询最相关的信息或文档。例如，当你在搜索引擎中输入关键词时，你正在进行信息检索。相反，信息过滤通常是一个被动的过程。系统基于对用户的兴趣和行为的理解，自动过滤出不相关的信息，并推送相关的信息给用户。例如，当你浏览社交媒体的信息流或新闻推荐服务时，你正在经历信息过滤。

2. 时间性：信息检索通常是一次性的操作，用户提出查询，系统返回结果，然后操作结束。但是信息过滤是一个持续的过程，系统需要持续监视用户的行为，更新对用户兴趣的理解，并根据这些理解推送新的信息。

3. 个性化：信息过滤通常涉及到更深度的个性化。因为信息过滤需要理解每个用户的兴趣和行为，所以它需要更多的用户特定的数据。相比之下，虽然信息检索也可以包含个性化元素（例如，搜索引擎可能根据用户的搜索历史调整结果），但这通常不是其主要焦点。

尽管这两者有所不同，但是在实践中，许多系统会结合使用信息检索和信息过滤。例如，一个搜索引擎可能会使用信息检索技术来找到与查询相关的网页，然后使用信息过滤技术来根据用户的搜索历史和点击行为个性化结果。










