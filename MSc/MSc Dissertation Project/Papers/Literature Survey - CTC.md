# Probability Modeling

前一章介绍了语音识别预训练的特征表征方法。本节将讨论asr的声学模型。具体而言，是端到端acoustic model中的概率模型，其本质是对语音的输入与输出关系进行建模，同时也包括损失函数和反向传播。对于输入的一段语音信号X，如何将不同长度的X与最终的文本Y进行映射，是概率模型要解决的主要问题。

基于关注点的不同，可以将概率模型分为Time-synchronous和Label-synchronous。

[汇总总结论文](https://www.isca-speech.org/archive_v0/Interspeech_2017/pdfs/0233.PDF)

[汇总总结文章](https://www.modb.pro/db/176104)

[讨论Time-synchronous和Label-synchronous分类论文](https://arxiv.org/pdf/2005.10113.pdf)

## Time-synchronous/Frame-synchronous

Time-synchronous模型以输入的frame单位为中心，对每一个输入都给与对应的输出，直到当前frame被分析完毕为止，然后再接收下一个输入。由于关注点在时序的speech上，因此被称为Time-synchronous模型。

![[CTC RNN-T.png]]

### CTC

CTC是用来解决时序类数据的分类问题的一种概率模型，是一个预测、输出、损失优化的集合。

**传统模型的不足：**

传统的语音识别的声学模型训练，对于每一帧的数据，需要知道对应的label才能进行有效的训练，在训练数据之前需要做语音对齐的预处理。而语音对齐的过程本身就需要进行反复多次的迭代，来确保对齐更准确，这本身就是一个比较耗时的工作。

1. 训练数据之前需要做语音对齐的预处理，工作比较耗时，并且在缺失对齐标签时，无法做出准确预测；

2. 输出的预测是局部分类，只利用了当前帧的信息，并未利用序列的全局信息（比如相邻两个标签的连续性等，则需要通过其他外加处理。）

**CTC与传统模型的对比：**

与传统的声学模型训练相比，采用CTC作为损失函数的声学模型训练，是一种完全端到端的声学模型训练，不需要预先对数据做对齐，只需要一个输入序列和一个输出序列即可以训练。这样就不需要对数据对齐和一一标注，并且CTC直接输出序列预测的概率，不需要外部的后处理。

CTC的方法是关心一个输入序列到一个输出序列的结果，那么它只会关心预测输出的序列是否和真实的序列是否接近（相同），而不会关心预测输出序列中每个结果在时间点上是否和输入的序列正好对齐。

1. 与传统的声学模型训练相比，采用CTC作为损失函数的声学模型训练，是一种完全端到端的声学模型训练，不需要预先对数据做对齐，只需要一个输入序列和一个输出序列即可以训练。这样就不需要对数据对齐和一一标注，输入输出之间的alignment不再那么重要。

2. CTC直接输出序列预测的概率，不需要外部的后处理。

但是CTC模型仍然存在着很多的问题，其中最显著的就是CTC假设模型的输出之间是条件独立的。这个基本假设与语音识别任务之前存在着一定程度的背离。此外，CTC模型并不具有语言建模能力，同时也并没有真正的实现端到端的联合优化。

值得注意的是，CTC是单独处理每个序列frame，frame与frame之间是independent。

[CTC讲解1](https://www.cnblogs.com/shiyublog/p/10493348.html)
[CTC讲解2](https://www.cnblogs.com/qcloud1001/p/9041218.html)

[CTC视频讲解](https://www.bilibili.com/video/BV1mv4y1P7QB)

[CTC公式推导](https://zhuanlan.zhihu.com/p/161186907)



### RNA



### RNN-T

针对CTC的不足，Alex Graves在2012年左右提出了RNN-T模型，RNN-T模型巧妙的将语言模型声学模型整合在一起，同时进行联合优化，是一种理论上相对完美的模型结构。RNN-T模型引入了TranscriptionNet也就是图中的Encoder（可以使用任何声学模型的结构），相当于声学模型部分，图中的PredictionNet实际上相当于语言模型（可以使用单向的循环神经网络来构建）。模型中比较新奇，同时也是最重要的结构就是联合网络Joint Net，一般可以使用前向网络来进行建模。联合网络的作用就是将语言模型和声学模型的状态通过某种思路结合在一起，可以是拼接操作，也可以是直接相加等，考虑到语言模型和声学模型可能有不同的权重问题，似乎拼接操作更加合理一些。

[RNN-T讲解](https://www.cc.gatech.edu/classes/AY2021/cs7643_spring/assets/L24_rnnt_asr_tutorial_gt.pdf)

[另一个讲解](https://bat.sjtu.edu.cn/zh/rnn-t/)



## Label-synchronous

### LAS

关于alignment：https://home.ttic.edu/~klivescu/MLSLP2016/chan_MLSLP2016.pdf
P16

解释了LAS如何进行预测：
https://www.bilibili.com/video/av24307355
（也可以知道为什么LAS是Label-synchronous模型，因为每一步的预测是按照label顺序进行的，而不是按时间顺序）

