Title: Progressive Layered Extraction (PLE): A Novel Multi-Task Learning (MTL) Model for Personalized Recommendations

Authors: Hongyan Tang, Junning Liu, Ming Zhao, Xudong Gong

Affiliation: Tencent PCG (中国腾讯)

Link: https://dl.acm.org/doi/abs/10.1145/3383313.3412236

Code: https://github.com/shenweichen/DeepCTR-Torch

Keywords: [Special multi-task learning](../Special%20multi-task%20learning.md), [Recommender system](../../../../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md), Seesaw Phenomenon

Additional information: 

Conclusion: 


## Abstract

本研究提出了一种名为渐进式分层提取（PLE）的新型多任务学习（MTL）模型，用于个性化推荐。通过采用一种新颖的共享结构设计，PLE能够显式地分离共享组件和任务特定组件，并采用渐进式路由机制逐渐提取和分离更深层次的语义知识，从而提高联合表示学习和任务之间的信息传递效率。实验结果表明，PLE在不同任务相关性和任务组大小下，都显著优于现有的MTL模型。此外，在腾讯的大规模内容推荐平台上进行的在线评估显示，与现有的MTL模型相比，PLE在观看次数上增加了2.23％，观看时间增加了1.84％，这是一个显著的改进，证明了PLE的有效性。此外，对公共基准数据集的大量离线实验表明，PLE可以应用于除推荐以外的各种场景，以消除“跷跷板现象”。PLE已成功部署到腾讯的在线视频推荐系统中。

## Introduction

### Background

个性化推荐在在线应用中起着至关重要的作用。然而，由于问题的高维度，学习算法通常难以直接解决用户满意度。因此，越来越多的研究倾向于应用多任务学习（MTL）来同时建模用户满意度的多个方面。然而，现实世界中的推荐系统中的任务通常存在松散相关性甚至冲突，这可能导致性能下降，即负迁移。

此外，通过在真实世界的大规模视频推荐系统和公共基准数据集上进行广泛实验，我们发现现有的MTL模型通常会在改善某些任务的同时牺牲其他任务的性能，当任务相关性复杂且有时与样本相关时，即多个任务无法与相应的单任务模型同时改进（一部分任务效果变好，另一部分效果变差），我们称之为“跷跷板现象”（Seesaw Phenomenon）。

### Problems

以往的研究主要致力于解决负迁移问题，但忽视了“跷跷板现象”。例如，交叉线网络和水闸网络提出了学习静态线性组合的方法来选择性地融合不同任务的表示，但这些模型中的表示是使用相同的静态权重组合的，无法解决“跷跷板现象”。因此，有必要设计一种更强大和高效的模型来处理复杂的任务相关性并消除具有挑战性的“跷跷板现象”。

### Contributions

为了解决跷跷板现象，PLE 在[MMoE](MMoE.md)的基础上新增了每个任务独有的expert 专家，用来强化自己任务的特性，gate网络用来学习每个子任务的独有expert和共享expert联合的权重，这是单层CGC模型。

论文的Motivation: 为了解决上述问题，本研究提出了一种名为渐进式分层提取（PLE）的新型MTL模型。与现有的MTL模型相比，PLE通过更好地利用先验知识来设计共享网络，以捕捉复杂的任务相关性。与MMOE中的粗略共享参数不同，PLE明确地将共享和任务特定的专家分开，以减轻共享和任务特定知识之间的有害参数干扰。此外，PLE引入了多级专家和门控网络，并应用渐进分离路由机制，逐渐从较低层次的专家中提取更深层次的知识，并逐渐分离较高层次的任务特定参数。通过在真实世界的工业推荐数据集和主要的公共基准数据集上进行广泛实验，评估了PLE的性能。腾讯的大规模视频推荐系统上的在线A/B测试结果也证明了PLE在实际应用中相对于现有的MTL模型的显著改进，观看次数增加了2.23％，观看时间增加了1.84％，这产生了显著的商业收益。PLE现已成功部署到推荐系统中，并有潜力应用于许多其他推荐应用中。

## Methods

单层模型CGC，多层模型PLE。

### CGC

![Pasted image 20230713163957](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833487.png)

### PLE

PLE的多层的CGC，与CGC不同的是考虑了不同expert的交互性，在底层的Extraction网络中有一个share gate，他的输入不再是CGC的独有expert和share expert，而是全部expert。详细模型示意图见下图。

![Pasted image 20230713164217](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833515.png)

### Joint Loss Optimization for MTL

传统的MTL的损失是各任务损失的加权和：
$$
L\left(\theta_1, \ldots, \theta_K, \theta_s\right)=\sum_{k=1}^K \omega_k L_k\left(\theta_k, \theta_s\right),
$$
而在腾讯视频场景下，不同任务的样本空间是不一样的，比如计算视频的完成度，必须有视频点击行为才可以。不同任务的样本空间如下图所示：

![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833535.png)

In this paper, we optimize the joint loss function to address two critical ones encountered in real-world recommender systems. The first problem is the heterogeneous sample space due to sequential user actions. For instance, users can only share or comment on an item after clicking it, which leads to different sample space of different tasks/shown in Fig. 6. To train these tasks jointly, we consider the union of sample space of all tasks as the whole training set, and ignore samples out of its own sample space when calculating the loss of each individual task:
$$
L_k\left(\theta_k, \theta_s\right)=\frac{1}{\sum_i \delta_k^i} \sum_i \delta_k^i \operatorname{loss}_k\left(\hat{y}_k^i\left(\theta_k, \theta_s\right), y_k^i\right)
$$
where $\operatorname{loss}_k$ is task $k$ 's loss of sample $i$ calculated based on prediction $\hat{y}_k^i$ and ground truth $y_k^i, \delta_k^i \in\{0,1\}$ indicates whether sample $i$ lies in the sample space of task $k$.

The second problem is that the performance of an MTL model is sensitive to the choice of loss weight in the training process, as it determines the relative importance of each task on the joint loss. In practice, it is observed that each task may have different importance at different training phases. Therefore, we consider the loss weight for each task as a dynamic weight instead of a static one. At first, we set an initial loss weight $\omega_{k, 0}$ for task $k$, then update its loss weight after each step based on the updating ratio $\gamma_k$ :
$$
\omega_k^{(t)}=\omega_{k, 0} \times \gamma_k^t
$$
where $t$ denotes the training epoch, $\omega_{k, 0}$ and $\gamma_k$ are hyper-parameters of the model.



## Evaluation

### Datasets

- 疼讯闭源数据集
- Synthetic Data
	- 使用MMoE中提到的数据合成方法所生成的数据集
- Census-income Dataset 收入预测数据集
- Ali-CCP Dataset

### Baselines

In the experiment, we compare CGC and PLE with single-task model, asymmetric sharing, customized sharing, and the SOTA MTL models including cross-stitch network, sluice network, and MMOE. As multi-level experts are shared in PLE, we extend MMOE to ML-MMOE (multi-layer MMOE) shown in Fig. 1h) by adding multi-level experts for fair comparison. In ML-MMOE, higher-level experts combine representations from lower-level experts through gating networks and all gating networks share the same selector.

Baseline模型有点多，基本都是多任务学习模型

### Metrics

还是AUC，不过不是CTR任务

也有相对对比，

### Results

最后简单看一下实验结果。首先是离线的训练结果，表中的收益均是相较于单任务学习模型的：
![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833552.png)

![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833566.png)

接下来是线上A／B实验的结果：

![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833582.png)

可以看到，无论是离线训练还是线上A／B，PLE均取得了最佳的效果。

接下来，论文比较了在任务之间相关系数不同的情况下，Hard Parameter Sharing、MMOE和PLE的结果：

![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833594.png)

可以看到，无论任务之间的相关程度如何，PLE均取得了最优的效果。

最后，论文对比了MMOE和PLE不同Expert的输出均值，来比较不同模型的Expert利用率（expert utilization）。为方便比较，将MMOE的Expert设置为3个，而PLE&CGC中，每个任务独有的Expert为1个，共享的为1个。这样不同模型都是有三个Expert。结果如下：

![500](../../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE/IMG-20240214165833630.png)

可以看到，无论是MMOE还是ML-MMOE，不同任务在三个Expert上的权重都是接近的，这其实更接近于一种Hard Parameter Sharing的方式，**但对于CGC&PLE来说，不同任务在共享Expert上的权重是有较大差异的，其针对不同的任务，能够有效利用共享Expert和独有Expert的信息，这也解释了为什么其能够达到比MMOE更好的训练结果。**


## Conclusion

- a. 详细的实验设置:
	- 本文在腾讯视频推荐数据集上对模型进行了评估，并在不同任务相关性和任务组大小下显著优于现有的MTL模型。
	- 在腾讯的大规模内容推荐平台上进行的在线评估也显示出与SOTA MTL模型相比，在观看次数和观看时间方面有显著改进。
- b. 详细的实验结果:
	- 本文在腾讯的大规模内容推荐平台上进行了离线和在线实验，以评估所提出模型的有效性。
	- 实验涉及到具有复杂和正常相关性的任务组，以及多个任务。
	- 实验数据集来自腾讯新闻视频推荐系统的用户日志，持续8天，包含4692.6万用户、268.2万视频和9.95亿样本。
	- 实验中考虑的任务包括VCR、CTR、VTR、SHR（分享率）和CMR（评论率）。

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。




## Appendices

### Appendix A

总结一下实践中遇到的常见问题。

#### Q1: Share expert和独有expert的超参设置？

这个目前看并没有特别明确的定论，根据子任务的数量一般是每个任务的expert 1-2个，share expert个数在1倍以上。这个超参最好还是根据具体的任务实验为准。

#### Q2: CGC还是PLE，和MMoE对比效果怎么样?

实践中单层CGC在相同参数规模下效果普遍要优于MMoE，从理论上也可以解释的通，毕竟增加了每个子任务各自的expert，对于相关性特别差的多任务，有一个专有的expert做"保底"。我们的实践中多层的PLE只有在个别场景下会好于CGC，所以很多情况下我们就直接采用CGC升级MMoE ,不过这个还是要具体实验一下。

#### Q3: 多个任务的指标怎么比？

这个没有明确的定论，一般如果都是auc，首先看有没有特别重要的任务，比如点击率，转化率等等。如果都一样的权重可能就是简单的总体相加。如果是特别多的任务或者想快速计算指标差异，建议还是写一个通用脚本。

### Appendix B

#### 讨论：腾讯的 (PLE) 为什么能获得RecSys2020最佳长论文奖？

[软客](https://www.zhihu.com/people/di-diao-cai-shi-wang-dao-70)：PLE并没有想象中的那么惊艳，其论文中提出改进的两个点，大概只有后面的loss设计是有用的，前面的多层设计并没有解决其提出的“跷跷板”现象，甚至其效果不如论文中提出的CGC好。所以我赞同本问题其中的一个答主的答案：

匿名用户：目测模型结构的影响还没有超参数的影响大




## Future work

值得研究的点







