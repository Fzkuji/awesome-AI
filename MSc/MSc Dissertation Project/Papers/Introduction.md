## Automated speech recognition

### Definition
Speech recognition, also known as automatic speech recognition (ASR), is an important research direction in natural language processing (NLP). ASR is a technology that uses machines to convert human speech into text, which can be traced back to the Audrey speech recognition system developed by Bell Labs in the 1950s. Dynamic time warping (DWT) was the mainstream of ASR before the Hidden Markov model (HMM) was introduced into ASR in the 1980s and replaced it. Then neural network was introduced and used widely. In 2010, the model of combining DNN and HMM achieved success, and some recent ASR works began to focus on Sequence-to-sequence models (Seq2Seq). Nowadays, ASR is widely used in smart devices, education, medical care, communications, and other fields. 

### Interpretability

Although the performance of the state-of-art speech recognition model is high enough, its interpretability is still not well resolved. There are multiple definitions of interpretability, and the arguments in most related articles vary accordingly. In this paper, we focus on explainable AI to understand the reasons for the decisions on a given object, also known as the outcome explanation [1]. Traditional NLP methods are interpretable, such as Decision Tree, Hidden Markov model, and Logistic Regression, also known as white-box techniques. In recent years, deep learning models (black-box techniques) featuring language embeddings have emerged, and while these methods have significantly improved model performance in many cases, on the other hand, these methods have made models difficult to interpret. Even if a user witnesses an obvious error, it is hard to correct the model. 





## Capsule neural network
本文中我们考虑引入胶囊网络进行语音识别与重建。胶囊网络最初实现于2017年，用于解决计算机视觉领域的不变性问题。其思路是使用胶囊结构替换CNN，同时重用底层胶囊，为更高的胶囊形成更稳定的（相对于各种扰动）表示。

胶囊网络的本质是使用更合理的方式解耦表示物体，这种思路也可以应用到语音识别上，使得语音识别预测结果可解释。

本文尝试使用胶囊结构表示formant，最终使用多层胶囊网络进行预测。


# Aims and objectives

这项工作旨在将胶囊网络引入语音识别中。具体而言，在最底层将formant表示为胶囊，然后通过组合得到更高层的语音胶囊，最终使用胶囊网络的输出预测文本。

同时，为了证明胶囊网络的可行性和可解释性，一个额外的目标是使用胶囊对语音进行重建。通过最大化重建相似度，我们可以确定得到的输出结果是由什么特征决定的，该特征是否与原音频相匹配。

# Overview of the Report
An overview of the remaining chapters is given below:
 • Chapter 2 discusses the collection of the location data, and the implementation of the application used to collect it in Android Studio.
 • Chapter 3 sets out mathematically the algorithms developed for route detection. It will discuss a baseline model and the local principal curves model.
 • Chapter 4 describes how the algorithms were implemented in Python with pseudocode and discusses the results of the testing carried out. 
• Chapter 5 sets out mathematically the algorithms developed for route planning. It will detail how route segments are aggregated into a single graph and Dijkstras shortest path algorithm is applied to it. 
• Chapter 6 explains how the algorithms were implemented in Python and gives a detailed analysis of the testing performed.
 • Chapter 7 provides an evaluation of the results for route detection and route planning algorithms.
 • Chapter 8 draws conclusions.
