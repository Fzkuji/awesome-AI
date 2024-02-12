## 1. Informal problem definiton

The special multi-task learning problem: Learn all of the tasks more quickly or more proficiently than learning them independently.
狭义多任务学习问题就是比独立学习**更快**或**更熟练**地学习所有任务。

**目前来看，主要的目标是尽量减少参数，同时还能提升性能。**
## 2. Doesn’t multi-task learning reduce to single-task learning?

Q: 多任务学习是否可以化简为单任务学习？比如直接把损失函数加起来训练？
$$\mathcal{D}=\bigcup \mathcal{D}_{i} \quad \mathcal{L}=\sum \mathcal{L}_{i}$$
A: Yes, it can! It is one approach to multi-task learning. But, we can often do better! Exploit the fact that we know that data is coming from different tasks.

## 3. Formal problem statement

### 3.1 Task notation
A task is a data generating distribution $\mathscr{T}_{i} \triangleq\left\{p_{i}(\mathbf{x}), p_{i}(\mathbf{y} \mid \mathbf{x}), \mathscr{L}_{i}\right\}$ with corresponding datasets $\mathscr{D}_{i}^{t r}$ and $\mathscr{D}_{i}^{\text {test }}$.
一个任务是**一个数据分布**$p_{i}(\mathbf{x})$、**一个给定数据生成结果的分布**$p_{i}(\mathbf{y} \mid \mathbf{x})$和**损失函数**$\mathscr{L}_{i}$的总和。

### 3.2 Examples of multi-task
Multi-task classification: $\mathscr{L}_{i}$ same across all tasks
多任务分类的**损失函数**都是一样的
e.g. per-language handwriting recognition, personalized spam filter

Multi-label learning: $\mathscr{L}_{i}, p_{i}(\mathbf{x})$ same across all tasks
多标签学习的**损失函数**和**数据分布**是一样的
e.g. CelebA attribute recognition, scene understanding

Q: *When might $\mathscr{L}_{i}$ vary across tasks?*
A: 
- mixed discrete, continuous labels across tasks
- multiple metrics that you care about

For MTL, we have $\enclose{horizontalstrike}{p_{\theta}(\mathbf{y} \mid \mathbf{x})} \to f_{\theta}\left(\mathbf{y} \mid \mathbf{x}, \mathbf{z}_{i}\right)$. 
$\mathbf{z}_{i}$ is the task descriptor (e.g. one-hot encoding of the task index or whatever meta-data you have).
 - personalization: user features/attributes
 - language description of the task
 - formal specifications of the task
$\mathbf{z}_{i}$最简单的例子就是告诉模型这是第几个任务。

**Vanilla MTL**
最极端的情况，把所有任务的损失加起来就是多任务的目标。
Objective: $\min _{\theta} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$

## 4 Model: conditioning on the task

**多任务学习模型的关键问题：**
How should the model be conditioned on $\mathbf{z}_{i}$?
对于不同的任务，我们如何控制Task descriptor $\mathbf{z}_{i}$？

What parameters of the model should be shared?
不同任务之间应该共享什么？

### 4.1 Assumption
$\mathbf{z}_{i}$ is the one-hot task index.
假设：Task descriptor $\mathbf{z}_{i}$只是one-hot向量索引

### 4.2 Question
How should you condition on the task in order to share as little as possible?
问题：如何设定任务条件来让他们共享的信息最少？

### 4.3 General Answer
两个极端：
 - independent training within a single network with no shared parameters
	一个极端是有多少任务训练多少网络，即有$n$个任务就训练$n$个网络，最终Task descriptor $\mathbf{z}_{i}$ one-hot选中哪个就用哪个
 - all parameters are shared
	另一个极端是所有任务都用一个模型。
	个人认为PPT上写的怪怪的，把$\mathbf{z}_{i}$从中间加入进网络，但是本质上似乎还是第一种。第二种方法最极端应该是直接没有$\mathbf{z}_{i}$，模型直接输出就直接用。

折中方案：
 - Split $θ$ into shared parameters $θ^sh$ and task-specific parameters $θ^i$
	一部分参数共享，一部分参数独享。
	优化目标为：
	$$\min _{\theta^{s h}, \theta^{1}, \ldots, \theta^{T}} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\left\{\theta^{s h}, \theta^{i}\right\}, \mathscr{D}_{i}\right)$$
*因此，$\mathbf{z}_{i}$也就决定了模型共享什么**参数**。（Finn说的，我其实不太确定）*

### 4.4 Examples
#### Concatenation-based conditioning
![500](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/Concatenation-based%20conditioning.png)
先将输入和条件连起来，然后输入到模型中进行训练，最后得出结果。

#### Additive conditioning
![500](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/Additive%20conditioning.png)
将条件输入一个模型得到**条件表示**，最后将输入和条件表示**相加**作为最终结果。

**事实就是前面这两种方法其实本质上是一样的。（不过个人认为训练的时候可能会有不同的结果，或者更好训练）**

#### Multi-head architecture
![300](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/Multi-head%20architecture.png)
多头输入，浅显易懂

#### Multiplicative conditioning
![500](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/Multiplicative%20conditioning.png)
这个是条件表示和输入**相乘**

#### More Complex Choices
![More Complex Choices](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/More%20Complex%20Choices.png)
### 4.5 Shortcomings
- problem dependent
	依赖于任务
- largely guided by intuition or knowledge of the problem
	基于直觉
- currently more of an art than a science
	更像是艺术而不是科学

## 5. Objective/Loss
*How should the objective be formed?*

### 5.1 Vanilla MTL Objective

$\min _{\theta} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$
每个任务Loss相加
### 5.2 Weight tasks differently

$\min _{\theta} \sum_{i=1}^{T} w_{i} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$
每个任务Loss加权重相加

权重选择：
 - dynamically adjust throughout training
	e.g. 
	 - various heuristics - encourage gradients to have similar magnitudes (Chen et al. GradNorm. ICML 2018)
	 - optimize for the worst-case task loss $\min _{\theta} \max _{i} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$ (e.g. for task robustness, or for fairness)
 - manually based on importance or priority

## 6. Optimization
*How should the objective be optimized?*

对于Vanilla MTL Objective：$\min _{\theta} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$
Basic Version：
1. Sample mini-batch of tasks $ℬ ∼ \{𝒯_i\}$
2. Sample mini-batch datapoints for each task $\mathscr{D}_{i}^{b} \sim \mathscr{D}_{i}$
3. Compute loss on the mini-batch $\hat{\mathscr{L}}(\theta, \mathscr{B})=\sum_{\mathscr{T}_{t} \in \mathscr{B}} \mathscr{L}_{k}\left(\theta, \mathscr{D}_{k}^{b}\right)$
4. Backpropagate loss to compute gradient $\nabla_{\theta} \hat{\mathscr{L}}$
5. Apply gradient with your favorite neural net optimizer (e.g. Adam)
每次取一个任务，再从该任务中取一些数据训练模型

**Note:** This ensures that tasks are sampled uniformly, regardless of data quantities.
这确保了任务被统一采样，而不管数据量如何

**Tip:** For regression problems, make sure your task labels are on the same scale!
对于回归问题，请确保您的任务标签在同一尺度上！

## 7. Challenges

### 7.1 Negative transfer

**Negative transfer:** Sometimes independent networks work the best.

Why? 
- optimization challenges
	- caused by cross-task interference
	- tasks may learn at different rates
- limited representational capacity
	- multi-task networks often need to be much larger than their single-task counterparts

Solutions
If you have negative transfer, **share less** across tasks.
可以共享更少的参数

It’s not just a binary decision!
$\min _{\theta^{s h}, \theta^{1}, \ldots, \theta^{T}} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\left\{\theta^{s h}, \theta^{i}\right\}, \mathscr{D}_{i}\right)+\sum_{t^{\prime}=1}^{T}\left\|\theta^{i}-\theta^{i^{\prime}}\right\|$
可以不共享参数，但是增加不同任务参数区别的损失$\sum_{t^{\prime}=1}^{T}\left\|\theta^{i}-\theta^{i^{\prime}}\right\|$，让多个任务的模型参数尽量一致
✔️ allows for more fluid degrees of parameter sharing
❌ yet another set of design decisions / hyperparameters

### 7.2 Overfitting

You may not be sharing enough! 

Multi-task learning <-> a form of regularization 
<font color='green'>Solution</font>: Share more.

### 7.3 What if you have a lot of tasks?

Should you train all of them together? Which ones will be complementary?

<font color='red'>The bad news</font>: No closed-form solution for measuring task similarity.
目前没有好的办法判断哪些任务适合一起训练

<font color='green'>The good news</font>: There are ways to approximate it from one training run.
但是我们可以训练几步试试结果（zei了，就是Finn自己的论文）

![600](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/Efficiently%20Identifying%20Task%20Groupings%20for%20Multi-Task%20Learning.png)
Fifty, Amid, Zhao, Yu, Anil, Finn. *Efficiently Identifying Task Groupings for Multi-Task Learning.* 2021

## 8. Case study

### Stanford case

![](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/cs330_multitask_transfer_2021.pdf#page=27)

### Other works

#### [MMoE](+Papers/MMoE.md)

#### [PLE](+Papers/PLE.md)

## 9. External Resources

[狭义多任务学习GitHub汇总](https://github.com/Manchery/awesome-multi-task-learning)
