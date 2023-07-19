## 1. Informal Problem Definiton

The meta-learning problem: Given data on previous tasks, learn a new task more quickly and/or more proficiently.
元学习问题就是给定**先前**任务的数据，更快和/或更熟练地学习新任务。

**元学习感觉更像是噱头，目前来看要么暴力预测模型参数，要么依靠微调加快模型训练速度。**

## 2. Problem formulation

Two ways to view meta-learning algorithms:

| Mechanistic view                                                                                             | Probabilistic view                                                                                     |
| ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| Deep network that can read in an entire dataset and make predictions for new datapoints                      | Extract prior knowledge from a set of tasks that allows efficient learning of new tasks                |
| Training this network uses a meta-dataset, which itself consists of many datasets, each for a different task | Learning a new task uses this prior and (small) training set to infer most likely posterior parameters |

### 2.1 Mechanistic view

<u>Meta Learning</u>:
Given data from $\mathscr{T}_{1}, \ldots, \mathscr{T}_{n}$, quickly solve new task $\mathscr{T}_{\text{test}}$

<u>Key assumption</u>: 
 - meta-training tasks and meta-test task drawn i.i.d. from same task distribution $\mathscr{T}_{1}, \ldots, \mathscr{T}_{n} \sim p(\mathscr{T}), \mathscr{T}_{j} \sim p(\mathscr{T})$
 - Like before, tasks must share structure.

<font color='green'>How many tasks do you need?</font> The more the better. Like before, tasks must share structure. (analogous to more data in ML)

<u>Examples</u>: 
 - 学了几门语言之后，学新的语言更快
 - 机器人学会叠被子之后，学开门更快

<u>Terminologies</u>:
 - task training set $\mathcal{D}_{i}^{\mathrm{tr}}$ - “support set”
 - task test dataset $\mathcal{D}_{i}^{\mathrm{test}}$ - “query set”
 - **k-shot learning**: learning with **k** examples per class (or k examples total for regression)
 - **N-way classification**: choosing between N classes

|                                 Multi-Task Learning                                 |                                                            Transfer Learning                                                             | Meta-Learning                                                                                                  |
|:-----------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------:| -------------------------------------------------------------------------------------------------------------- |
|      Solve multiple tasks $\mathscr{T}_{1}, \cdots, \mathscr{T}_{T}$ at once.       | Solve target task $\mathscr{T}_{b}$ after solving source task $\mathscr{T}_{a}$ by transferring knowledge learned from $\mathscr{T}_{a}$ | Given data from $\mathscr{T}_{1}, \ldots, \mathscr{T}_{n}$, quickly solve new task $\mathscr{T}_{\text{test}}$ |

In transfer learning and meta-learning: generally impractical to access prior tasks
In all settings: tasks must share structure.

## 3. Methodology
### 3.1 General recipe of meta-learning algorithms

**How to design a meta-learning algorithm**
1. Choose a form of $f_{\theta}\left(\mathcal{D}^{\mathrm{tr}}, \mathbf{x}^{\mathrm{ts}}\right)$
2. Choose how to optimize $\theta$ (meta-parameters) w.r.t. max-likelihood objective using meta-training data

### 3.2. Black-box adaptation
#### 3.2.1. Concepts
**Key idea**: 
 - Train a neural network to represent $\phi_{i}=f_{\theta}\left(\mathcal{D}_{i}^{\operatorname{tr}}\right)$ 
 - Predict test points with $\mathbf{y}^{\mathrm{ts}}=g_{\phi_{i}}\left(\mathbf{x}^{\mathrm{ts}}\right)$
 - Train with standard supervised learning $\mathcal{Loss}=\max _{\theta} \sum_{\mathcal{T}_{i}} \mathcal{L}\left(f_{\theta}\left(\mathcal{D}_{i}^{\text {tr }}\right), \mathcal{D}_{i}^{\text {test }}\right)$

**Process**:
1. Sample task $\mathcal{T_i}$ or mini batch of tasks
2. Sample disjoint datasets $\mathcal{D}_{i}^{\mathrm{tr}}, \mathcal{D}_{i}^{\mathrm{test}}$ from $\mathcal{D}_{i}$
3. Compute $\phi_{i} \leftarrow f_{\theta}\left(\mathcal{D}_{i}^{\operatorname{tr}}\right)$ 
4. Update $\theta$ using $\nabla_{\theta} \mathcal{L}\left(\phi_{i}, \mathcal{D}_{i}^{\text {test }}\right)$

本质上就是用模型去预测另一个模型的参数

**Challenge**: Outputing all neural net parameters does not seem scalable?
**Idea**: Do not need to output all parameters of neural net, only sufficient statistics (Santoro et al. MANN, Mishra et al. SNAIL)

#### 3.2.2. Architectures
##### LSTMs or Neural turing machine (NTM)
![400](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Meta-Learning%20with%20Memory-Augmented%20Neural%20Networks.png)
**Meta-Learning with Memory-Augmented Neural Networks**
Santoro, Bartunov, Botvinick, Wierstra, Lillicrap. ICML ‘16

##### Feedforward + average
![400](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Conditional%20Neural%20Processes.png)
**Conditional Neural Processes**
Garnelo, Rosenbaum, Maddison, Ramalho, Saxton, Shanahan, Teh, Rezende, Eslami. ICML ‘18

##### Other external memory mechanisms
![400](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Meta%20Networks.png)
**Meta Networks**
Munkhdalai, Yu. ICML ‘17

##### Convolutions & attention
![200](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/A%20Simple%20Neural%20Attentive%20Meta-Learner.png)
**A Simple Neural Attentive Meta-Learner**
Mishra, Rohaninejad, Chen, Abbeel. ICLR ‘18

More **few-shot image recognition** datasets: tieredImageNet, CIFAR, CUB, CelebA, ORBIT, others
More [benchmarks](../../../../../../Miscellaneous/Baselines%20vs%20Benchmarks.md): molecular property prediction (Ngyugen et al. ’20), object pose prediction (Yin et al. ICLR ’20), channel coding (Li et al. ’21)

#### 3.2.3. Pros & Cons
##### Advantages
 + expressive
 + easy to combine with variety of learning problems (e.g. SL, RL)

##### Disadvantages
 - complex model w/ complex task: challenging optimization problem
 - often data-inefficient
#### 3.2.4. Case Study: GPT-3
*PPT最后提及了[GPT-123](../../../Natural%20language%20processing/Large%20language%20model/Models/GPT/GPT-123.md)模型*

GPT-3定义上应该是一种自监督预训练NLP大模型。但是Finn认为这也是一个black-box meta-learner trained on language generation tasks。感觉其实就是大力出奇迹，和meta learning没啥关系。

 - The results are extremely impressive.
 - The model is far from perfect.
 - The model fails in unintuitive ways.
总结就是很强，但是还是没脑子

另外值得一提的就是Prompt（[GPT-3当时叫priming](https://github.com/shreyashankar/gpt3-sandbox/blob/master/docs/priming.md)），也就是在使用/测试模型的时候，给定的输入很重要，输的不合适效果就不好，这也因此催生了很多关于Prompt的研究。

### 3.3. Optimization-based Adaptation
#### 3.3.1. Concepts

**Key idea**: Acquire $\phi_i$ through optimization.
基本意思就是不用模型预测模型了，那样太难。现在直接用类似于fine-turing的方法训练一个最终模型，这个模型用于下游任务的时候最终结果更好，而且比fine-tuning还要好。

**个人理解**：Optimization-Based Adaptation关注的主要就是依赖于meta learning训练出来的模型最后性能更好，速度上肯定是比从头训练快，但是不是重点。这类模型都是直接通过训练得到一个自认为的最优结果，因此缺陷就是必须所有任务都要有类似的结构，才能被后面的任务使用。

**训练流程概览**： ^866c0a
- - -
init parameters $\phi_0$
epoch = $E$
tasks = $\mathcal{T}$
 - for $e$ in range($E$):
	 - $\nabla_{\theta} \mathcal{L_{e}} = 0$
	 - for $\mathcal{T}_i$ in $\mathcal{T}$:
		 - sample a batch of $K$ samples from $\mathcal{T}_i$
		 - $\theta = \phi_e$
		 - for $k$ in range($K$):
			 - $\nabla_{\theta_k} \mathcal{L}_{\mathcal{T}_{i}}\left(f_{\theta_k}\right)= model_{\theta_k}(batch[k])$
			 - $\theta_{k+1} = \theta_{k} - \alpha \nabla_{\theta_k} \mathcal{L}_{\mathcal{T}_{i}}\left(f_{\theta_k}\right)$
		 - sampe another batch of $N$ samples from $\mathcal{T}_i$
		 - for $n$ in range($N$):
			 - $\nabla_{\theta_{K+n}} \mathcal{L}_{\mathcal{T}_{i}}\left(f_{\theta_{K+n}}\right)= model_{\theta_{K+n}}(batch[n])$
			 - $\theta_{K+n+1} = \theta_{K+n} - \alpha \nabla_{\theta_{K+n}} \mathcal{L}_{\mathcal{T}_{i}}\left(f_{\theta_{K+n}}\right)$
		 - $\nabla_{\theta} \mathcal{L_{e}} = \nabla_{\theta} \mathcal{L_{e}} + \nabla_{\theta}\mathcal{L}_{\mathcal{T}_{i}}\left(f_{\theta_{K+N}}\right)$
	 - $\phi_{e+1} = \phi_{e} - \beta \nabla_{\theta} \mathcal{L_{e}}$
- - -
![500](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Optimization-based%20Adaptation.png)
如图所示，在每一个epoch中，Optimization-based Adaptation方法会对每个任务训练$K$次。图中任务1（绿）的$K=2$，图中任务2（蓝）的$K=3$。此时，假设这两个点都是各自的最优点，也就是假设这些点都是黄色的Real Meta Best点，模型会计算Hypothetical Meta Best点的损失函数，将所有损失函数相加并反向传播，就可以更新原始参数了。

其中，值得注意的是，得到Real Meta Best点之后，仍然需要对模型训练多次，才能得到任务的最优点。图中，两个任务都训练了2次，而MAML只训练了一次。训练一次在反向传播的时候会需要求解2阶导数（Hessian）。**似乎训练次数越多，阶数越高，但是这一点我不确定。可以确定的是，之前的$K$次训练并不会影响到阶数，因为前面的$K$次训练的参数都可以通过化简约掉。目测反向传播最终的阶数为N+1次。**

**Finn的观点似乎是不论$N$等于多少，也就是不管我们什么时候假设模型计算好了，只要我们得到最终的损失能反向传播就行。此时 $N$的增加也就是$K$再增加，而$N$永远等于1。但是这似乎又违背了meta learning的初衷，我们本来就是想保证一个预训练结果对所有任务都好用，那么就没必要让$K$太大，难怪MAML文章中$K$和$N$都是1？**
#### 3.3.2. Architectures
##### MAML
MAML, or Model-Agnostic Meta-Learning, is **a model and task-agnostic algorithm for meta-learning that trains a model's parameters such that a small number of gradient updates will lead to fast learning on a new task**.

**MAML can be viewed as computation graph, with embedded gradient operator**
**MAML相当于计算图里把梯度也加进去了**

MAML参数更新为：
$$
\phi_{i}=\theta-\alpha \nabla_{\theta} \mathcal{L}\left(\theta, \mathcal{D}_{i}^{\operatorname{tr}}\right)
$$
甚至可以不通过求二次梯度，直接用模型预测梯度（Ravi & Larochelle ICLR ’17）：
$$
\phi_{i}=\theta-\alpha f\left(\theta, \mathcal{D}_{i}^{\operatorname{tr}}, \nabla_{\theta} \mathcal{L}\right)
$$

![450](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/maml-algo.png)
Fig. 12. The general form of MAML algorithm. (Image source: [original paper](https://arxiv.org/abs/1703.03400))

MAML是[](.md#3.3.1%20Concepts%7CConcept%E8%AE%AD%E7%BB%83%E6%B5%81%E7%A8%8B)的一个特例，也就是$N=1$，因此需要计算二阶导数才能反向传播。
假设$K=k$，对于$k$的for循环公式如下（引用自[Lilian Weng的博客](https://lilianweng.github.io/posts/2018-11-30-meta-learning/)）：
$$

\begin{aligned}
\theta_0 &= \theta_\text{meta}\\
\theta_1 &= \theta_0 - \alpha\nabla_\theta\mathcal{L}^{(0)}(\theta_0)\\
\theta_2 &= \theta_1 - \alpha\nabla_\theta\mathcal{L}^{(0)}(\theta_1)\\
&\dots\\
\theta_k &= \theta_{k-1} - \alpha\nabla_\theta\mathcal{L}^{(0)}(\theta_{k-1})
\end{aligned}

$$
对于$N$的for循环如下：
$$
\begin{aligned}
\theta_\text{meta} &\leftarrow \theta_\text{meta} - \beta g_\text{MAML} & \scriptstyle{\text{; update for meta-objective}} \\[2mm]
\text{where } g_\text{MAML}
&= \nabla_{\theta} \mathcal{L}^{(1)}(\theta_k) &\\[2mm]
&= \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k) \cdot (\nabla_{\theta_{k-1}} \theta_k) \dots (\nabla_{\theta_0} \theta_1) \cdot (\nabla_{\theta} \theta_0) & \scriptstyle{\text{; following the chain rule}} \\
&= \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k) \cdot \Big( \prod_{i=1}^k \nabla_{\theta_{i-1}} \theta_i \Big) \cdot I &  \\
&= \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k) \cdot \prod_{i=1}^k \nabla_{\theta_{i-1}} (\theta_{i-1} - \alpha\nabla_\theta\mathcal{L}^{(0)}(\theta_{i-1})) &  \\
&= \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k) \cdot \prod_{i=1}^k (I - \alpha\nabla_{\theta_{i-1}}(\nabla_\theta\mathcal{L}^{(0)}(\theta_{i-1}))) &
\end{aligned}
$$
更新参数需要求梯度，需要通过链式法则展开。除了损失函数的导数可以直接求解以外，之前的参数都是更新过的，因此需要将前面的 $\theta_i$带入式中求导，由于$\theta_i$本身就是更新梯度之后的结果，因此相当于套了两层导数。



##### First-Order MAML
The meta-optimization step above relies on second derivatives. To make the computation less expensive, a modified version of MAML omits second derivatives, resulting in a simplified and cheaper implementation, known as **First-Order MAML (FOMAML)**. (Text source: [original web](https://lilianweng.github.io/posts/2018-11-30-meta-learning/))

根据MAML可以知道，计算二阶导数Hessian计算量很大，FOMAML计划直接删除二阶导数：
$$
g_\text{MAML} = \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k) \cdot \prod_{i=1}^k (I - \alpha \color{red}{\nabla_{\theta_{i-1}}(\nabla_\theta\mathcal{L}^{(0)}(\theta_{i-1}))})
$$
最后梯度直接变成：
$$
g_\text{FOMAML} = \nabla_{\theta_k} \mathcal{L}^{(1)}(\theta_k)
$$
值得注意的是，如果删除了二阶导数，那么梯度只剩下了损失函数的导数和$I$，并且**前提是训练$K$步和训练$N$时使用的数据集是一样的，否则求导后直接为0**，就无法更新了。

*（当然，这也是公式推导的结果，个人觉得实际上应该也可行，毕竟之前的梯度都不要了，说明前面的数据有没有都行，估计拿不同的数据训练也能train出来）*

至此，MAML彻底简化为一个和多任务学习模型一样的intuition的模型，数学内涵荡然无存。

![250](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/MAML%20demonstration.png)
如图所示（来源Youtube视频：[Few-Shot Learning - Part10 : MAML](https://www.youtube.com/watch?v=rc_sw4DVDk8)）），对于 $T_1$和$T_2$两个任务，模型首先分别训练一次（黄色和绿色），然后进行评估，再次训练一次（蓝色和橙色），最后直接用蓝色和橙色的梯度之和更新原本的参数$\theta$。

##### Reptile

> 李宏毅吐槽过，MAML中文意思就是哺乳动物，那么估计这篇文章起名Reptile ([Nichol, Achiam & Schulman, 2018](https://arxiv.org/abs/1803.02999))就是纯粹想用爬行动物对标。
> ——我自己

![Pre-train vs MAML vs Reptile](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Pre-train%20vs%20MAML%20vs%20Reptile.png)
如图所示，该图分析了Pre-train、MAML、Reptile三者的关系，说白了这三个模型使用后续的梯度进行了不同的参数更新。虽然并没有本质区别，但是在公式推导的时候还是很复杂的。
$$
\begin{aligned}
g_\text{FOMAML} &= g^{(1)}_1 \\
g_\text{MAML} &= g^{(1)}_1 - \alpha H^{(0)}_0 g^{(1)}_1 \\
g_\text{Reptile} &= g^{(0)}_0 + g^{(1)}_1
\end{aligned}
$$

> 本文提出的算法Reptile性能与MAML非常接近。但是作者同样也发现，本文的算法虽然在分类任务中取得了较好的结果，但是在强化学习中，却没有MAML表现的那样好，这也是一个值得探究的方向
> ——[论文阅读笔记](https://blog.csdn.net/qq_36104364/article/details/106587228)

##### iMAML
[iMAML: Meta-Learning with Implicit Gradients (Paper Explained)](Paper%20Explained))

#### 3.3.4. Challenges & solutions

<u>Bi-level optimization can exhibit instabilities</u>
也就是二阶梯度得优化不稳定问题
可行得解决方法：
 - Automatically learn inner vector learning rate, tune outer learning rate (Li et al. Meta-SGD, Behl et al. AlphaMAML)
 - Optimize only a subset of the parameters in the inner loop (Zhou et al. DEML, Zintgraf et al. CAVIA)
 - Decouple inner learning rate, BN statistics per-step (Antoniou et al. MAML++)
 - Introduce context variables for increased expressive power. (Finn et al. bias transformation, Zintgraf et al. CAVIA)

<u>Backpropagating through many inner gradient steps is compute- & memory-intensive</u>
二阶梯度得计算量大的问题
可行得解决方法：
 - [Crudely] approximate $\frac{d \phi_{i}}{d \theta}$ as identity (Finn et al. first-order MAML ‘17, Nichol et al. Reptile ’18)
 - Only optimize the last	layer of weights.
	 - ridge regression, logistic regression (Bertinetto et al. R2-D2 ’19)
	 - support vector machine (Lee et al. MetaOptNet ’19)
		 —> leads to a closed form or convex opCmizaCon on top of meta-learned features
 - Derive meta-gradient using the implicit function theorem (Rajeswaran, Finn, Kakade, Levine. Implicit MAML ’19) 也就是iMAML
	 —> **compute full meta-gradient without differentiating through optimization path**
	 似乎是不需要计算二阶梯度了，而且比MAML、FOMAML和Reptile都好。

<u>How to choose architecture that is effective for inner gradient step?</u>
既然MAML这一类的模型训练之前结构就固定了，那怎么选择模型结构？
可行得解决方法：
 - Progressive neural architecture search + MAML (Kim et al. Auto-Meta)
	 - finds highly non-standard architecture (deep & narrow)
	 - different from architectures that work well for standard supervised learning
那肯定是NAS，并且还强调了找到的结构和监督学习很不一样。。。

#### 3.3.5. Case Study: Few-Shot Land Cover Classification

Problem: Map land covering from satellite images
任务：地形图像分类/分割

Different tasks: different regions of the world
Goal: Segment/classify images from a new region with a small amount of data
多任务了，但是没有太多；元学习了，但是没有元多少


数据集：
 - SEN12MS dataset (Schmih et al. 2019)
 - DeepGlobe dataset (Demir et al. 2018)

反正最后比pre-trained好，就完事儿了

### 3.5. Non-Parametric Few-Shot Learning








### 3.4. Comparision
下面将通过二维参数训练的图像来演示不同模型的区别

#### 3.4.1. Pre-training

![400](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/MAML%20v.s.%20Pre-training%202.png)

通常Pre-training训练流程如下：
![500](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Pre-training.png)
假设有两个任务，最终目标是Task 2，但是我们提前训练的是Task 1。那么就需要用fine-tuning将参数从Task 1的最优处训练到Task 2的最优处，因此就有了图中紫色（pre-training）再到绿色（fine-tuning）的一个过程。

但是一般pre-training的Task 1都有大数据集，并且和Task 2差不多，这样他们的最优解一般都很近，所以最后参数在Task 2上就表现也不错，如下图：

![500](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/Good%20Pre-training.png)
所以一般预训练模型都是用大数据训练的大模型，这样用到后续的小数据集任务就更容易得到好的结果。


#### 3.4.2. Black-Box Adaptation

这个方法的问题是对外推的任务的泛化能力不行。因为完全没有朝这个方向训练。而Optimization-based Adaptation则会好很多，而且测试时仍然可以用fine-tuning。
#### 3.4.3. Optimization-based Adaptation

![400](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Meta%20Learning/MAML%20v.s.%20Pre-training%201.png)

For a sufficiently deep network, MAML function can approximate any function of $\mathcal{D}_{i}^{\operatorname{tr}}, x^{\mathrm{ts}}$
AssumpCons:
 - nonzero $\alpha$
 - loss function gradient does not lose information about the label
 - datapoints in $\mathcal{D}_{i}^{\operatorname{tr}}$ are unique

<font color='green'>Why is this interesting?</font>
MAML has benefit of inductive bias without losing expressive power.










