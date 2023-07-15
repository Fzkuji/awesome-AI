[知乎总结](https://zhuanlan.zhihu.com/p/462979069)

![[Speech Representation.png]]

[综述](https://arxiv.org/pdf/2205.10643.pdf)[综述](https://arxiv.org/pdf/2205.10643.pdf)

# Speech Representation

## Overview
神经网络的预训练模型旨在使用尽可能多的训练数据，从中提取出尽可能多的共性特征，从而能让模型对特定任务的学习负担变轻。预训练在NLP和CV领域使用很多，音频相关的预训练在近几年才不断涌现。和其他领域一样，音频预训练的目的是获得音频 general 的表示（representation）应用于下游任务。其思想就是当前位置的信息和其临近信息相关。

但音频比起图片，文本的不同在于1. 长时（连续信号、无 dictionary）；2. local smoothness（即帧与帧之间差距很小）；信号混杂（鸡尾酒效应：同一段音频可以有多个说话人、多种声音单元）。因此，如何迁移其他领域的预训练的方法更好地适应音频特征就成了以下工作的重点。

CPC是谷歌2019年发布的音频预训练的奠基之作。而wav2vec是首个通过学习原始音频的表示来探索语音识别的无监督预训练模型，验证了大规模无标注数据可以降低ASR模型对标注数据的需求、提升ASR模型的表现。vq-wav2vec在wav2vec的基础上增加了VQ层，并BERT，将连续的语音信号处理和离散的文本联系起来。该方法可以作为wav2vec2.0的过渡。wav2vec2.0把离散化做无监督标签学习连续的语音表示，验证了在这种模式下ASR可以使用更少的标注数据（跟之前的工作相比，离散化结果只用来做训练，不再输入到context 网络中学习上下文表示）。本文主要分析wav2vec机器衍生模型。



预训练模型的关键是如何判断一个representation是否有效。

generative model尝试使用representation来reconstruct原语音，此类方法的细节区别主要在于预测的输入和输出范围不同。

predictive model与generative model类似。predictive model继承bert的思想，将原始信息添加mask并进行编码，再尝试使用编码信息预测原始信息。其本质同样是希望编码信息能够共享临近frame的信息。

除了能够使用representation还原原本的输入，另一种评判指标是能否保存必要的信息用于后续任务。contrastive model就是关注保存representation的预测能力。


## Wav2vec

wav2vec属于contrastive model，此前的模型多是将其应用于分类任务，而wav2vec是首个首个通过学习原始进行ASR的无监督预训练模型。本文将先简述wav2vec模型的结构，再通过其具体阐述contrastive model的概念。

![[wav2vec.png]]

wav2vec基于全卷积网络架构，主体分为两个部分。

第一部分encoder network将原始音频x编码为潜在空间z，。
第二部分使用context network将多个Z转换为context representation C。
两个encoder network均使用卷积层，文章中encoder network使用5层卷积，context network由9层卷积组成。

wav2vec输入为原始音频信号，训练完成之后我们就可以使用得到的特征来替换一些任务中原本使用的特征，最终的感受野为一帧210ms。作者在实验中使用了ASR，因此使用C替换了在acoustic model中使用的log-mel filterbank特征。

contrastive model的基本思路是将潜在特征Z和其对应的context representation C进行配对，若其他判别模型能够利用context representation C来预测其对应的潜在特征Z，则说明C保存了原始信号的局部信息和预测能力。

在wav2vec中，t时刻的潜在特征Z由t时刻之前的声音信号通过卷积操作得到，context representation C同理。t时刻的context representation C将与t+k时刻的潜在特征Z进行配对形成正例，同时随机选取其他时刻Z与之配对形成反例。即若判别模型能正确判断一堆C和Z是否正确配对，则说明该representation C是较优解。

其loss函数如下：

其中。。。

wav2vec的Baseline使用的声学模型是80维f-bank特征。实验证明，wav2vec的无监督特征的效果要优于传统f-bank特征。同时随着预训练数据量的增加，wav2vec的效果会越来越好，甚至能逼近或超过当时的SOTA模型。



## Vq-Wav2Vec

随着NLP领域中的基于自监督学习的预训练模型BERT被提出并受到广泛的关注，在wav2vec被提出的同年十月，FAIR的Alexei Baevski等人基于wav2vec进一步引入量化模块（vq）提出vq-wav2vec模型，本质上是想尝试将Bert引入语音预训练。

不同于NLP领域中的文本具有天然的离散特性（每个token对应着一个id，每个id对应着一个embedding向量），语音信号本是连续的信号，并且由wav2vec模型学习得到的特征也是连续的向量表征

Vq-Wav2Vec在wav2vec模型中加入量化模块，将特征表示离散化，这样利用BERT预训练的方法提升语音识别任务的效果。

vq-wav2vec在结构上和wav2vec的区别就在于特征Z，vq-wav2vec获得Z之后，会增加一步离散化处理得到Z‘，之后使用Z’用于后续训练。离散化处理提供了Gumbel-Softmax和K-means clustering两种选择。后者在loss上也作出了调整。

有了离散化的特征表示，便可以C将作为embedding，并使用大规模无标签的语音数据训练一个BERT。

最后再将预训练BERT得到的语义表征作为输入，有监督地训练下游acoustic model（AM）。



## Wav2Vec2.0

Wav2Vec2.0在vq2vec更进一步，简化了pipeline。Wav2Vec 2.0直接将BERT的mask操作转移到网络本身。在获得feature encoder的输出结果Z后，模型出现两个分支。一个分支对Z进行mask，并送入transformer得到C。另一个分支对Z进行quantification。最后求得C和q的contrastive loss。值得注意的是，Wav2Vec2.0的context network被替换为了transformer而不是CNN。网络整体架构如图所示，通过Wav2Vec2.0得到的representation， 基于少量的有标签数据fine-tuning就取得了不错的效果。另外，Wav2Vec2.0和vq-wav2vec都属于contrastive model和predictive model的混合模型。

# 囊

 