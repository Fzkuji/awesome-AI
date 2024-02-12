# Learning Visual N-Grams from Web Data

{2017}, {ICCV}


## Summary

本文就是使用图像去预测图像的内容，同时使用文字去表达。
以下是例子：
![Visual N-Gram](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Perception/Computer%20vision/Object%20Detection/+Papers/Visual%20N-Gram/IMG-20240212100316061.png)
给定一张图片，模型给出最符合图像描述的$n$个单词。

同时，本文也分析了zero-shot迁移学习的效果。将训练好的模型应用到新的图像分类数据集（Imagenet等）上，

## Research Objective(s)

作者的研究目标是什么？

  

## Background / Problem Statement

研究的背景以及问题陈述：作者需要解决的问题是什么？

  

## Method(s)

作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

  

## Evaluation
损失函数部分，模型也使用了NLP中的平滑处理（Jelinek-Mercer），来增加数量较少的标签的权重。

  

## Conclusion

作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在[discussion](https://www.zhihu.com/search?q=discussion&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A142802496%7D)中提到；或实验的数据并没有给出充分的evidence）?

  

## Notes

(optional) 不在以上列表中，但需要特别记录的笔记。

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。