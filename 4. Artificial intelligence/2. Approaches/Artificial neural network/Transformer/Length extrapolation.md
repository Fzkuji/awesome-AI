# Length extrapolation
长度外推


Transformer自诞生以来就席卷了NLP领域，因为它具有对序列中复杂依赖关系进行建模的优越能力。尽管基于Transformer的预训练语言模型(PLM)在几乎所有NLP任务中都取得了巨大成功，但它们都有预设的长度限制，因此很难将这种成功扩展到见过数据以外的更长的序列，即长度外推问题。为了增强Transformer的长度外推，人们提出了大量的可外推的位置编码。

也就是假设模型训练时设计的时128k的输入长度，但是我们希望其可以应用于1m输入长度的序列，就可以考虑设计一些专门的位置编码方法+微调，使得模型可以输入更多的token。

## Related materials

[Transformer升级之路：16、“复盘”长度外推技术](https://kexue.fm/archives/9948)这篇比较新的总结（2024.1）by 苏剑林

## Related works

[Transformer升级之路：12、无限外推的ReRoPE？](https://spaces.ac.cn/archives/9708)主要思想是前$w$个token的位置编码是从$0$到$w$的整数，后续的$k$个token的编码是从$w+\frac{1}{k}$到$w+\frac{L-1-w}{k}$的小数

[LongRoPE: Extending LLM Context Window Beyond 2 Million Tokens](https://arxiv.org/abs/2402.13753)微软提出的一种方法，暂时没看细节

