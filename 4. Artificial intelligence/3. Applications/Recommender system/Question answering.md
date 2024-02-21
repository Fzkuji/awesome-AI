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
由于 ODQA 使用开放域数据，因此它需要处理大量噪声和不确定性。这使得 ODQA 成为一个更具挑战性的任务。

## Methods

### Dense Passage Retrieval

密集段落检索（Dense Passage Retrieval）是一种用于开放域问答系统的技术。该技术通过检索与问题相关的文本段落，然后从这些段落中提取答案来回答问题。

首篇论文：[Dense Passage Retrieval for Open-Domain Question Answering](https://aclanthology.org/2020.emnlp-main.550/)

#### Related works

##### Zero-shot settings
Zero-shot setting
[Out-of-Domain Semantics to the Rescue! Zero-Shot Hybrid Retrieval Models](https://link.springer.com/chapter/10.1007/978-3-030-99736-6_7)


