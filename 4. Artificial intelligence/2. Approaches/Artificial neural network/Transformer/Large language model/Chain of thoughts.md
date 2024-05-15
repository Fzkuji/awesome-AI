**Chain-of-thought prompting** (CoT) improves the reasoning ability of LLMs by prompting them to generate a series of intermediate steps that lead to the final answer of a multi-step problem. The technique was first proposed by [Google](https://en.wikipedia.org/wiki/Google "Google") researchers in 2022.

https://github.com/Timothyxxx/Chain-of-ThoughtsPapers

<iframe 
	height = 750
	width = 100% 
	padding = 0 0 
	margins = 0 0
	src="https://zhuanlan.zhihu.com/p/493533589">
</iframe>

## Chain of thought 能力来自于哪儿

> 上一小节在介绍COT特性的时候，都是统一用GPT-3来代表。其实，**原始的GPT-3版本中并没有显著地发现其具备COT特性。**
> 
> 对于大众来说，像是ChatGPT突然就有了这样的能力。其实，在ChatGPT出来之前，OpenAI对GPT-3做了很多迭代优化工作。而GPT-3的COT特性就是在这些迭代优化中逐渐展现。
> 
> 但不可否认的是，目前仍然没有确定性的结论说明COT特性来自于具体哪些迭代优化。有些观点说是通过引入强化学习，有些观点则是说通过引入了指令微调的训练方式，也有些观点说是通过引入庞大的代码预训练语料，使得模型从代码逻辑中学习到了相应知识。
> 
> 推测的方式则是根据不同时间节点上的模型版本能力差进行排除法，虽然目前我们受限于技术能力只能从这些蛛丝马迹中去发现一些端倪，但仍然具有一定的借鉴意义。具体的推理过程本文不会重复，感兴趣的可以参考如下博客：[https://franxyao.github.io/blog.html](https://link.zhihu.com/?target=https%3A//franxyao.github.io/blog.html)。
> 
> ——[真·万字长文：可能是全网最晚的ChatGPT技术总结](https://zhuanlan.zhihu.com/p/613698929)




