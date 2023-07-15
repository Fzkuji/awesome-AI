# Learning Transferable Visual Models From Natural Language Supervision
{2021}, {PMLR}

我们证明了预测哪个标题与哪个图像搭配的简单预训练任务是一种有效且可扩展的方法，可以在从互联网收集的 4 亿对（图像、文本）对的数据集上从头开始学习 SOTA 图像表示。在预训练之后，使用自然语言来参考学习的视觉概念（或描述新概念），使模型能够零样本转移到下游任务。我们研究了超过 30 个不同的计算机视觉数据集的性能，包括 OCR 等跨任务、视频中的动作识别、地理定位和许多类型的细粒度对象分类。该模型非常重要地转移到大多数任务，并且通常与完全监督的基线竞争，而无需任何数据集特定的训练。例如，我们在 ImageNet 零样本上匹配原始 ResNet50 的准确度，而无需使用它所训练的 128 万个训练示例中的任何一个。

## Summary

**这个文章主要证明的就是在没有人工标注数据的情况下，直接使用网络上的图片和文字注释，就可以训练预训练的模型。**

实现方法也是自监督模型，文本使用Transformer进行处理。

## Research Objective(s)

作者的研究目标是什么？

  

## Background / Problem Statement

研究的背景以及问题陈述：作者需要解决的问题是什么？

  

## Method(s)

作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

  

## Evaluation

作者如何评估自己的方法？实验的setup是什么样的？感兴趣实验数据和结果有哪些？有没有问题或者可以借鉴的地方？

  

## Conclusion

作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在[discussion](https://www.zhihu.com/search?q=discussion&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A142802496%7D)中提到；或实验的数据并没有给出充分的evidence）?

  

## Notes

(optional) 不在以上列表中，但需要特别记录的笔记。

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。


