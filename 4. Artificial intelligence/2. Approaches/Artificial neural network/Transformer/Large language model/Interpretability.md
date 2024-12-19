

> [!INFO]- 刀下留人
> 本文大面积照搬这位仁兄的笔记：[浅谈LLM mechanistic interpretability的几个流派](https://zhuanlan.zhihu.com/p/719002810)
> 
> 但是进行了一些完善，哥们儿的中英混杂实在看不下去


> Mechanistic Interpretability（这里简称为Interpretability）的主要目标是理解LLM内部的运行机制，以及定位参数的存储位置。理解模型的机制可以帮助分析失败案例，设计更好的模型结构/训练方法，减少Hallucination/Bias/Toxicity等。
> 
> 下面是一些主要流派和经典论文（更多相关论文可参考[时间旅客](https://www.zhihu.com/people/time-passenger)整理的[Awesome Papers for Understanding LLM Mechanism](https://github.com/zepingyu0512/awesome-llm-understanding-mechanism)）。

### Causal tracing

也叫Path Patching、Causal Mediation Analysis、Causal Intervention。基本思想是计算改变模型的某个位置的参数/vector时，输出的变化情况，通过变化程度定位对最终输出重要的位置。这一流派主要目标是理解LLM的information flow的机制。

1. 大部分工作是用[Locating and Editing Factual Associations in GPT](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2202.05262) (NeurIPS 2022) 提出的方法，这篇论文发现medium FFN layers在factual knowledge任务中很重要。

2. [Dissecting Recall of Factual Associations in Auto-Regressive Language Models](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2304.14767) (EMNLP 2023) 研究factual knowledge的信息流，发现early FFN layers起到representation enrichment的作用，然后从attention heads中提取知识。
	- 即：模型在低层的MLP中存储了知识，当需要回答问题时，先通过MLP收集该主题的所有属性信息，然后注意力机制根据问题类型从中提取出对应的特定属性。
	- [A Mechanistic Interpretation of Arithmetic Reasoning in Language Models using Causal Mediation Analysis](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2305.15054) (EMNLP 2023) 把类似的方法应用在算术任务中，也发现了相似的机制。

3. [Towards Best Practices of Activation Patching in Language Models: Metrics and Methods](https://link.zhihu.com/?target=https%3A//openreview.net/forum%3Fid%3DHf17y6u9BC) (ICLR 2024) 研究了causal tracing不同实验设置(intervention method, evaluation metric等)得出的结论不同，并给出了建议的实验设置。另外causal tracing不仅可以应用于transformer-based LM，也可以应用于Mamba，并且结论类似：[Locating and Editing Factual Associations in Mamba](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2404.03646) (COLM 2024)。

听上去合理

### Circuit analysis

Circuit Analysis的主要目标是以attention head和FFN layer作为基本单元，构建输入到输出的circuit。它的潜在假设是：对于特定的输入/输出，只有一小部分参数很重要。这类研究中有一部分是探索不同head的功能，另一部分专注于构建整个circuit。前一部分研究最重要的发现是induction head和in-context learning强相关：[A Mathematical Framework for Transformer Circuits](https://link.zhihu.com/?target=https%3A//transformer-circuits.pub/2021/framework/index.html) (Anthropic blog 2021)，[In-context Learning and Induction Heads](https://link.zhihu.com/?target=https%3A//transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html) (Anthropic blog 2022)。除此之外还有[Successor Heads: Recurring, Interpretable Attention Heads In The Wild](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2312.09230) (ICLR 2024)等。后一部分研究经典论文有[How does GPT-2 compute greater-than?: Interpreting mathematical abilities in a pre-trained language model](https://link.zhihu.com/?target=https%3A//openreview.net/forum%3Fid%3Dp4PckNQR8k) (NeurIPS 2023)，[Interpretability in the Wild: a Circuit for Indirect Object Identification in GPT-2 small](https://link.zhihu.com/?target=https%3A//openreview.net/forum%3Fid%3DNpsVSN6o4ul) (ICLR 2023)，以及自动建立circuit：[Towards automated circuit discovery for mechanistic interpretability](https://link.zhihu.com/?target=https%3A//openreview.net/forum%3Fid%3D89ia77nZ8u%26referrer%3D%255Bthe%2520profile%2520of%2520Adri%25C3%25A0%2520Garriga-Alonso%255D%28%252Fprofile%253Fid%253D~Adri%25C3%25A0_Garriga-Alonso1%29) (NeurIPS 2023)。有研究通过对比原模型和fine-tuned模型的circuit，发现fine-tune是增强原模型的机制：[Fine-Tuning Enhances Existing Mechanisms: A Case Study on Entity Tracking](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2402.14811) (ICLR 2024)。


### Logit lens & neuron analysis

对于FFN layer，前两个流派都是把FFN layer的hidden states作为基本单元，但FFN layer是由多个神经元(neuron)组成。[Transformer Feed-Forward Layers Are Key-Value Memories](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2012.14913) (EMNLP 2021) 发现FFN layer可以被分解为多个神经元的和，[Transformer Feed-Forward Layers Build Predictions by Promoting Concepts in the Vocabulary Space](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2203.14680) (EMNLP 2022) 发现很多FFN的神经元投影到unembedding space中有非常强的可解释性。[Analyzing Transformers in Embedding Space](https://link.zhihu.com/?target=https%3A//aclanthology.org/2023.acl-long.893.pdf) (ACL 2023) 进一步发现transformer的大部分参数投影到unembedding space都有很强的可解释性（这一思想起源于LESSWRONG 2020的[logit lens](https://link.zhihu.com/?target=https%3A//www.lesswrong.com/posts/AcKRB8wDpdaN6v6ru/interpreting-gpt-the-logit-lens) blog）。


### SAE

LLM的neuron存在superposition现象，导致可解释性缺失：[Toy Models of Superposition](https://link.zhihu.com/?target=https%3A//transformer-circuits.pub/2022/toy_model/index.html) (Anthropic 2022 blog)，所以Anthropic进一步提出了[dictionary learning](https://link.zhihu.com/?target=https%3A//transformer-circuits.pub/2023/monosemantic-features/index.html)和[sparse autoencoder SAE](https://link.zhihu.com/?target=https%3A//transformer-circuits.pub/2024/scaling-monosemanticity/index.html)来增加neuron的可解释性。


### Conclusion

以上就是LLM mechanistic interpretability的4个主流研究派系。除此之外还有研究grokking：[Grokking: Generalization Beyond Overfitting on Small Algorithmic Datasets](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2201.02177) , [Progress measures for grokking via mechanistic interpretabilit](https://link.zhihu.com/?target=https%3A//openreview.net/forum%3Fid%3D9XFSbDPmdW)，这类研究感觉太超前了，相关研究较少。







