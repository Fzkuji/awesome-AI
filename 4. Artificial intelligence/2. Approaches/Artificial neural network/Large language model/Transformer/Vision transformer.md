# Vision Transformer

## Related works

### ViT

[An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://openreview.net/forum?id=YicbFdNTTy)

用于视觉的transformer首次被提出，他们将图片等分，然后给不同位置加上位置标记。最后像序列一样处理图片。

### Swin Transformer

Swin Transformer: Hierarchical Vision Transformer using Shifted Windows

[Swin Transformer论文精读【论文精读】（跟李沐学AI）](https://www.bilibili.com/video/BV13L4y1475U/)

借鉴了CNN的思路，提出了transformer的pooling机制——patch merging和滑动注意力窗口（shifted window based self-attention）来提供全局感知。

### ViTDet

[Exploring Plain Vision Transformer Backbones for Object Detection](https://dl.acm.org/doi/abs/10.1007/978-3-031-20077-9_17)

[如何看待Meta（恺明）最新论文ViTDet：只用ViT做backbone（不使用FPN）的检测模型？](https://www.zhihu.com/question/525167811)

这篇论文核心在于，指出对ViT进行层次化的修改并不是必要的。

似乎这篇文章的思路和YOLOF的想法一样？（不太了解这个方向）顺便说说同年的YOLOX似乎引用量更高，说是将anchor box去掉了，也挺不错的。



