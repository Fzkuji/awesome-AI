# Memory transformer

## Memory

关于记忆比较重要的结论可以看维基百科

记忆的分类：
- [Memory](https://en.wikipedia.org/wiki/Memory)
	- [Working memory](https://en.wikipedia.org/wiki/Working_memory): 工作记忆（要区别短期记忆，工作记忆允许操纵存储的信息，而短期记忆仅指存储的信息）
	- Long-term memory: 
		- [Declarative memory](https://en.wikipedia.org/wiki/Declarative_memory): 外显记忆/陈述性记忆（Declarative memory）包含了语意记忆和情节记忆，是一种有意识的，对先前经验的回忆。比如记住了过去曾经发生的事情。
			- [Semantic memory](https://en.wikipedia.org/wiki/Semantic_memory): 语意记忆（Semantic memory）是一种对于一般知识的事实与概念的了解，比如1+1=2，地球是圆的
			- [Episodic memory](https://en.wikipedia.org/wiki/Episodic_memory): 情节记忆（Episodic memory）是记录生活中所发生的经历和特定事件，比如昨天吃了蛋炒饭
		- [Procedural memory](https://en.wikipedia.org/wiki/Procedural_memory): 内隐记忆/程序记忆（Procedural memory）是一种无意识的、与程序动作有关的记忆。比如关于骑车技能的记忆。而外显记忆记住的则是上周三去骑车这件事情。

> 关于脑部特殊区域受损（如海马回损伤）案例之研究指出，外显记忆和内隐记忆是使用脑部的不同地方，且可以各自独立运作的。对一个外显记忆正常、内隐记忆受损的病患而言，他可能被反复训练一个任务或动作、并且记得先前的训练的经历，但在任务完成的速度或是程度上却不会有改善的现象；若是内隐记忆正常、外显记忆受损的人，情况刚好相反，他没办法想起之前做过的实验内容，但实际表现上却会一次比一次好。

类比到目前的大语言模型：
- [Memory](https://en.wikipedia.org/wiki/Memory)
	- [Working memory](https://en.wikipedia.org/wiki/Working_memory)对应transformer计算过程中，tokens的hidden states
	- Long-term memory对应整个LLM保存下来的信息，比如神经网络的参数和特地存储下来的hidden states
		- [Declarative memory](https://en.wikipedia.org/wiki/Declarative_memory)外显记忆对应专门保留的hidden states
			- [Semantic memory](https://en.wikipedia.org/wiki/Semantic_memory)
			- [Episodic memory](https://en.wikipedia.org/wiki/Episodic_memory)
		- [Procedural memory](https://en.wikipedia.org/wiki/Procedural_memory)内隐记忆对应神经网络学习得到的参数

> 国际著名心理学家丹尼尔西格尔认为：我 们终生都在编码内隐记忆，而且在生命的头18个月里我们只能编码内隐记忆。

[维基百科](https://zh.wikipedia.org/zh-cn/%E5%86%85%E9%9A%90%E8%AE%B0%E5%BF%86)里还提到了内隐记忆的特征，内隐记忆在以下几个方面与外显记忆有明显的差别：
1. 保持时间：在保持的时间上，内隐记忆要明显的长于外显记忆。
2. 干扰形式：内隐记忆不容易受外在刺激的干扰，而外显记忆容易在干扰后遗忘。
3. 记忆负荷：外显记忆在记忆的项目增多的时候会导致记忆数量和准确性的下降，而内隐记忆不受这种影响。
4. 加工深度：加工深度越深，外显记忆越好。内隐记忆不存在这种情况。
5. 呈现形式：如果一个项目用听觉的形式呈现，再用视觉的形式施测，被试的内隐记忆成绩会下降。但外显记忆不会出现这种情况。

而这些特征和大语言模型的特征不谋而合：
1. 保持时间：在保持的时间上，网络参数（内隐记忆）可以一直保留，但是专门保留的hidden states（外显记忆）在一段时间后仍然会被抛弃。
2. 干扰形式：网络参数（内隐记忆）不容易受外在刺激的干扰，而专门保留的hidden states（外显记忆）容易在计算过程中遗忘。
3. 记忆负荷：专门保留的hidden states（外显记忆）在记忆的项目增多的时候会导致记忆数量和准确性的下降，而网络参数（内隐记忆）不受这种影响。
4. 加工深度：这点不太理解
5. 呈现形式：如果一个项目用听觉的形式呈现，再用视觉的形式施测，被试的内隐记忆成绩会下降。但外显记忆不会出现这种情况。这点的符合情况待定，可以作为后续研究。



## Related works

早期工作并没有完全关注记忆这个概念，而是主要关注长上下文（长文本序列）的解决方案。

2019 [Transformer-XL: Attentive Language Models Beyond a Fixed-Length Context](https://arxiv.org/abs/1901.02860)首次提出了将transformer前一段的context计算的结果用于后续的计算，同时加入了相对位置嵌入相当于提升了模型短期记忆（工作能力）的能力

2019 [Compressive Transformers for Long-Range Sequence Modelling](https://arxiv.org/abs/1911.05507)随后这个论文设计了一种压缩旧token并保存的方法，实现了真正意义上的记忆，旧tokens进行了压缩（max/mean pooling、1D convolution、dilated convolutions和most-used），同时为了验证压缩效果，他们尝试使用压缩后的记忆重建压缩前的内容

2020.4 [Longformer: The Long-Document Transformer](https://arxiv.org/abs/2004.05150)这篇工作设计了一些attention mask的样式

2020.10 [Memformer: A Memory-Augmented Transformer for Sequence Modeling](https://arxiv.org/abs/2010.06891) 这是一个encoder-decoder的模型，memory使用encoder部分进行更新，看上去还算优雅，但是encoder-decoder似乎过时了

2022.3 [Memorizing Transformers](https://arxiv.org/abs/2203.08913) **ICLR 2022** 将LLM第9层tokens的hidden states进行保存作为记忆。新tokens计算到第9层的时候，对于每一个token，模型使用knn查找与之相关的记忆（旧tokens的hidden states）并加入计算，最后汇总得到新tokens的hidden states。记忆长度262K tokens
- 和transformer-xl不同的是，这k对kv不会直接和当前的拼接，而是按照标准注意力一样，也算出一个最后的输出，然后和局部注意力的值向量做线性插值即可。——[Focused Transformer论文解读](https://zhuanlan.zhihu.com/p/644061652)

2023.7.5 [LongNet: Scaling Transformers to 1,000,000,000 Tokens](https://arxiv.org/abs/2307.02486) 用了三种扩张注意力组合，来保证计算量呈线性，但是实验的上下文长度只有32K，~~感觉可信度差点意思，标题党~~，后面又分析了一下，认为这个思想还是可以用的，可以保存扩张注意力位置的kv cache作为记忆

2023.7.6 [Focused Transformer: Contrastive Training for Context Scaling](https://arxiv.org/abs/2307.03170) **NeurIPS 2023** 感觉是想跟LongNet打一架，前后一天发的。和Memorizing Transformers很像，都是保存了以前文本的hidden states，然后用于后续的文本训练。同时添加了负样本提升模型对于海量记忆的筛选能力，相当于对比学习
- 实现项目：[long_llama](https://github.com/CStanKonrad/long_llama)

[MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://arxiv.org/abs/2305.10250)这篇文章的记忆设计是明文的，包含了记忆的保存、更新、索引以及用于增强LLM的问答

[MemGPT](https://memgpt.ai/)这个GitHub项目也是使用纯文本+工程化记忆提取保存，整合得比较好的项目

当前不包含memory的LLM在推理阶段最前面几个tokens的kv似乎非常重要：
- [Perpetual Sampling with LLaMA-30B](https://zhuanlan.zhihu.com/p/619703849)最早发现这个现象的是这一篇知乎文章，随后还有两篇论文讨论了这个问题
- [LM-Infinite: Zero-Shot Extreme Length Generalization for Large Language Models](https://arxiv.org/abs/2308.16137)
- [Efficient Streaming Language Models with Attention Sinks](https://arxiv.org/abs/2309.17453) 这篇论文非常关键，他们发现如果保留一个序列最前面的几个token，就可以显著降低困惑度，这很可能是因为最前面的几个token充当了记忆模块，虽然最前面的几个tokens可能无法记住很多有用的信息，但是充当了一个稳定的记忆体，单是有这个东西就已经可以提升性能了

[Leave No Context Behind: Efficient Infinite Context Transformers with Infini-attention](https://arxiv.org/abs/2404.07143) 这项工作主要改进的是transformer内部的结构，增加了记忆模块，使用了线性attention

###  Works by Mikhail Burtsev

Mikhail Burtsev这个兄弟似乎一直在搞memory相关的研究，一开始是他自己搞的，之后的研究似乎是他领导其他人做的

- 2020.6 [Memory Transformer](https://arxiv.org/abs/2006.11527)  **arXiv** 将transformer划分为memory token和sequence token，设计了几种不同的transformer形式，主要思路就是给memory和sequence各一个网络（attention+update）去计算更新表示，主要有三种
	- 第一种直接将所有token放入一个模型进行计算和更新
	- 第二种memory和sequence有单独的网络去计算，但是两个网络都同时输入memory tokens和sequence tokes
	- 第三种方法首先更新memory，然后再用 更新后的memory去计算sequence
- 2022.7 [Recurrent Memory Transformer](https://arxiv.org/abs/2207.06881) **NeurIPS 2022** 这个文章的思路就更简单，每次sequence输入一个LLM，sequence包含了之前的memory tokens、当前的文本和即将要保存的tokens。这样记忆就可以不断迭代使用。有点暴力，但是我比较好奇他们是如何优化训练这个模型的，毕竟这有一些循环在里面。
	- 2023.4 [Scaling Transformer to 1M tokens and beyond with RMT](https://arxiv.org/abs/2304.11062) **arXiv** 感觉内容上和前一篇差不多，不太懂为什么还可以再写一篇，感官上就是添加了一个实验。
	- 2024.3 [Beyond Attention: Breaking the Limits of Transformer Context Length with Recurrent Memory](https://ojs.aaai.org/index.php/AAAI/article/view/29722) **AAAI 2024** 似乎是前面一篇经过修改后发表了，然后换了一个名字
	- 2024.2 [In Search of Needles in a 11M Haystack: Recurrent Memory Finds What LLMs Miss](https://arxiv.org/abs/2402.10790) 基于这个实验他们又做了一个benchmark（好好好，一个idea写一万个论文，很好）

### [Dense passage retrieval](../../../../5.%20Information%20science/Information%20retrieval/Dense%20passage%20retrieval.md)

一个token可以保存多少记忆可以通过这类研究得到答案


### Efficient Transformer

2022.11 [Efficient Transformers: A Survey](https://doi.org/10.1145/3530811) 这个论文调研了关于提升transformer效率的一些研究，包括添加记忆模块


然后我希望对图片进行预处理操作，比如说对于图片的归一化等等，然后对于点云也可以进行一些操作，使其更好的和