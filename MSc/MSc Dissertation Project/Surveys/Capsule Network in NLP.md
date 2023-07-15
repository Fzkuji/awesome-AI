# 第一篇论文
**Investigating Capsule Networks with Dynamic Routing for Text Classification**
	Tasks: Text Classification

or (same author)

**Towards Scalable and Reliable Capsule Networks for Challenging NLP Applications**
	Tasks: Multi-label Text Classification, Question Answering

输入是单词

### **$N$-gram Convolutional Layer**

#### **Input**
$$x∈ℝ^{L×V}$$
	$x$ is the input sentence representation
	$L$ is the length of the sentence
	$V$ is the embedding size of words
$$x_{i}∈ℝ^{L×V}$$
	$x_{i}$ is the $V$-dimensional word vector corresponding to the $i$-th word in the sentence
![[Zhao, Wei, et al. input.png|450]]
#### **Filter**
$$W^{a}∈ℝ^{K_{1}×V}$$
	$W^{a}$ is the filter for the convolution operation
	$K_{1}$ is the $N$-gram size while sliding over a sentence for the purpose of detecting features at different positions
![[Zhao, Wei, et al. filter.png]]
#### Output






# 第二篇论文
**Learning temporal clusters using capsule routing for speech emotion recognition**
Tasks: 语音情感分析

输入是语音

BLSTM + Capsule Network

![[MLAJalal, Md Asif, et al. structure.png]]

# 第三篇论文
**Multi-channel EEG-based emotion recognition via a multi-level features guided capsule network**
Tasks: 脑电波情感分析

EEG是脑电图，输入是波

Capsule Network

![[Liu, Yu, et al. structure.jpg]]



# 其他
还有transformer做特征提取的

attention加到capsule routing的