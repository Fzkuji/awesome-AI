Title: RLAIF: Scaling Reinforcement Learning from Human Feedback with AI Feedback

Link: https://arxiv.org/abs/2309.00267

Code: 

Additional information: 

Conclusion: 


## Abstract

**总之就是，本工作比较了RLHF和RLAIF，实验结果显示其性能相近**

人类反馈的强化学习（RLHF）在调整大型语言模型（LLMs）以满足人类偏好方面表现出色，但获取高品质的人类偏好数据是主要难点。我们比较了RLHF和AI反馈的强化学习（RLAIF）两种方法。在RLAIF中，我们使用现成的LLM代替人类来标记偏好。结果显示，两种方法都取得了相似的进步。在文本摘要任务中，大约70%的人类评估者更喜欢RLAIF和RLHF的输出，而不是传统的监督模型。当被问及对RLAIF和RLHF摘要的评价时，人们对两者都持平等态度。这意味着RLAIF有潜力达到与人类相当的性能，可能成为解决RLHF扩展性问题的策略。

## Introduction

### Background

从人类反馈中的强化学习（RLHF）是一种有效的技术，用于使语言模型与人类偏好对齐（Stiennon等人，2020; Ouyang等人，2022），并被引用为现代对话语言模型（如ChatGPT和Bard）成功的关键驱动因素（Liu等人，2023; Manyika，2023）。通过强化学习（RL）训练，语言模型可以在复杂的、序列级的目标上进行优化，这些目标在传统的监督微调中不容易区分。

高质量人类标签的需求是扩大RLHF的障碍，一个自然的问题是人工生成的标签是否可以获得可比较的结果。有几项工作表明，大型语言模型（LLMs）与人类判断高度一致，甚至在某些任务上超过人类（Gilardi等人，2023; Ding等人，2023）。Bai等人（2022b）首次探索了使用AI偏好来训练用于RL微调的奖励模型 - 一种称为“从AI反馈中的强化学习”（RLAIF）的技术。尽管他们展示了使用“宪法AI”自修订技术的人类和AI偏好的混合比监督微调的基线表现更好，但他们的工作并没有直接比较人类与AI反馈的效果，留下了RLAIF是否可以作为RLHF的合适替代品的问题。

### Problems

在这项工作中，我们直接比较了RLAIF与RLHF在摘要任务上的表现。给定一个文本和两个候选响应，我们使用现成的LLM为其分配一个偏好标签。然后，我们在LLM偏好上使用对比损失训练一个奖励模型（RM）。最后，我们使用RM提供的奖励对策略模型进行强化学习微调。
我们的结果显示，RLAIF的性能与RLHF相当，有两种衡量方式。首先，我们观察到RLAIF和RLHF策略分别在$71\%$和$73\%$的时间内被人类优先于监督微调（SFT）基线，这两个胜率没有统计学上的显著差异。其次，当被要求直接比较RLAIF与RLHF的生成时，人们两者都持平等态度（即$50\%$的胜率）。这些结果表明，RLAIF是一个不依赖于人类注释并具有吸引人的扩展性质的RLHF的可行替代方案。

此外，我们研究了最大化AI生成偏好与人类偏好对齐的技术。我们发现，使用详细的指令提示我们的LLM并征求思维链推理可以提高对齐度。令人惊讶的是，我们观察到少量的上下文学习和自我一致性 - 一个过程，其中我们抽样多个思维链理由并平均最终的偏好 - 并不提高准确性，甚至降低了准确性。最后，我们进行了扩展实验，以量化LLM标签器的大小与用于训练的偏好示例数量之间的权衡与人类偏好的对齐度。

### Contributions

- 我们证明了RLAIF在摘要任务上与RLHF的性能相当
- 我们比较了生成AI标签的各种技术，并为RLAIF实践者确定了最佳设置。

## Preliminaries

### Supervised fine-tuning

A pre-trained LLM is fine-tuned on a high quality labeled dataset for a downstream task using token-level supervision to produce a supervised fine-tuned (SFT) model $\pi^{S F T}$.

### Reward modeling

Given an input $x$, we sample a pair of responses from one or more models $\left(y_1, y_2\right) \sim \pi$, where oftentimes the SFT model is used. The input and responses are sent to human annotators to rate which response is better according to some criteria. These annotations form a dataset of triplets $\mathcal{D}=\left\{\left(x, y_w, y_l\right)\right\}$, where $y_w$ and $y_l$ are the preferred and non-preferred responses, respectively. A reward model $r_\phi$ is trained by minimizing the following loss:
$$
\mathcal{L}_r(\phi)=\underset{\left(x, y_w, y_l\right) \sim \mathcal{D}}{-\mathbb{E}}\left[\log \sigma\left(r_\phi\left(x, y_w\right)-r_\phi\left(x, y_l\right)\right)\right]
$$
where $\sigma$ is the sigmoid function.

中文解释：
1. **输入**：给定一个输入$x$
2. **采样响应**：从一个或多个模型中采样一对响应$\left(y_1, y_2\right)$，这些响应是根据某种分布$\pi$生成的。在许多情况下，会使用SFT模型来进行采样
3. **人工注释**：将输入和这两个响应发送给人类注释者，让他们根据某些标准评价哪个响应更好
4. **数据集构建**：这些人工注释构成了一个三元组数据集$\mathcal{D}$。在每个三元组中，$y_w$是首选的响应，而$y_l$是非首选的响应
5. **奖励模型训练**：通过最小化以下损失来训练一个奖励模型$r_\phi$：
$$
\mathcal{L}_r(\phi)=\underset{\left(x, y_w, y_l\right) \sim \mathcal{D}}{-\mathbb{E}}\left[\log \sigma\left(r_\phi\left(x, y_w\right)-r_\phi\left(x, y_l\right)\right)\right]
$$
其中：
1. 奖励模型$r_\phi$：这是一个函数，它为给定的输入 $x$和响应$y$提供一个奖励值。这里的$\phi$是模型的参数
2. $r_\phi\left(x, y_w\right) - r_\phi\left(x, y_l\right)$：这部分计算了首选响应$y_w$和非首选响应$y_l$的奖励差值。我们期望首选响应的奖励值高于非首选响应
3. $\sigma$：这是sigmoid函数，它将任何实数映射到0, 1之间。在这里，它被用来将奖励差值转化为一个概率值
4. $\log \sigma$：取对数是为了计算交叉熵损失。在这种情况下，我们希望模型为首选响应分配更高的概率
5. $-\mathbb{E}$：这表示对数据集$\mathcal{D}$中的所有三元组$\left(x, y_w, y_l\right)$的期望值取负值。这是因为我们希望最小化损失，而交叉熵损失是一个需要最小化的量

简而言之，这段话描述了一个过程，其中从模型中采样响应，然后让人类评价这些响应的好坏。然后，使用这些评价来训练一个奖励模型，该模型的目标是区分好的响应和不好的响应。

### Reinforcement learning

A policy $\pi_\theta^{R L}$ is initialized from the SFT model weights and then optimized with reinforcement learning to maximize the reward given by the RM, which serves as a proxy for human preferences. Optionally, a Kullback-Leibler (KL) divergence loss $\mathbb{D}_{K L}$ is added to the objective to penalize $\pi_\theta^{R L}$ for deviating from the original SFT policy $\pi^{S F T}$, controlled by the hyperparameter $\beta$ - a technique similar to natural policy gradients (Kakade, 2001). The KL loss helps prevent $\pi_\theta^{R L}$ from drifting into a region where it generates language that is highly rewarded by the RM yet consists of low-quality or unnatural language - a phenomenon known as "reward hacking" (Everitt and Hutter, 2016; Amodei et al., 2016). The full optimization objective is described by the equation below:
$$
\max _\theta \mathbb{E}\left[r_\phi(y \mid x)-\beta \mathbb{D}_{K L}\left(\pi_\theta^{R L}(y \mid x) \| \pi^{S F T}(y \mid x)\right)\right]
$$

这段话描述了一个增强学习策略的初始化和优化过程：

1. **策略初始化**：策略 $\pi_\theta^{RL}$ 是从SFT模型的权重中初始化的。这意味着，强化学习的策略起始于SFT模型的行为。
2. **策略优化**：接着，策略 $\pi_\theta^{RL}$ 使用强化学习进行优化，目标是最大化由奖励模型（RM）给出的奖励。这里的RM作为人类偏好的代理。
3. **Kullback-Leibler (KL) 散度损失**：可以选择性地向优化目标中添加一个KL散度损失 $\mathbb{D}_{KL}$。这个损失的目的是惩罚策略 $\pi_\theta^{RL}$ 从原始的SFT策略 $\pi^{SFT}$ 偏离的程度。其中，$\beta$ 是一个超参数，用于控制KL散度损失的权重。这种方法与自然策略梯度技术（Kakade, 2001）相似。
4. **防止“奖励黑客”现象**：KL损失有助于防止策略 $\pi_\theta^{RL}$ 漂移到一个区域，其中它生成的语言虽然被RM高度奖励，但其实是低质量或不自然的语言。这种现象被称为“奖励黑客”（reward hacking），如Everitt和Hutter（2016）以及Amodei等人（2016）所描述。
5. **完整的优化目标**：整个优化的目标由以下公式描述：
$$
\max _\theta \mathbb{E}\left[r_\phi(y \mid x)-\beta \mathbb{D}_{KL}\left(\pi_\theta^{RL}(y \mid x) \| \pi^{SFT}(y \mid x)\right)\right]
$$
1. **目标函数**：整个公式的目标是最大化某个期望值。这意味着我们希望找到参数 $\theta$，使得这个期望值最大。
2. **期望值 $\mathbb{E}$**：这表示我们考虑的是所有可能的输入 $x$ 和输出 $y$ 的平均效果。在增强学习中，期望值通常用于表示策略在所有可能的状态和动作上的平均奖励。
3. **奖励函数 $r_\phi(y \mid x)$**：这是一个给定输入 $x$ 和输出 $y$ 的奖励值。这个奖励值表示了输出 $y$ 的质量或适 appropriateness。
4. **Kullback-Leibler (KL) 散度 $\mathbb{D}_{KL}$**：这是一个衡量两个概率分布之间差异的指标。在这里，它用于衡量策略 $\pi_\theta^{RL}$ 和原始的SFT策略 $\pi^{SFT}$ 之间的差异。
5. **策略 $\pi_\theta^{RL}(y \mid x)$ 和 $\pi^{SFT}(y \mid x)$**：这两个策略分别表示增强学习策略和原始SFT策略在给定输入 $x$ 下生成输出 $y$ 的概率。
6. **超参数 $\beta$**：这是一个权重因子，用于控制KL散度在整个目标函数中的重要性。当 $\beta$ 值较大时，我们更强烈地惩罚策略 $\pi_\theta^{RL}$ 从原始SFT策略 $\pi^{SFT}$ 偏离的程度。

整体来看，这个公式的目的是找到一个策略 $\pi_\theta^{RL}$，该策略不仅能获得高的奖励，而且与原始的SFT策略保持相似。这是通过最大化期望奖励并减去与原始策略的KL散度来实现的。

## Methods

整个工作的方法大致为：LLM标注样本，基于标注的样本训练RM，最后用RM训练LLM

### Preference labeling with LLMs

设计prompt让LLM判断

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137316.png)

Preamble部分文中提到了几种方式

#### Addressing position bias

模型越小，位置偏见越大。作者就是简单的正反手预测两次，然后取平均。

#### Chain-of-thought reasoning

两段式生成的CoT

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137579.png)

也有OpenAI + COT 0-shot或者OpenAI + COT 1-shot这两种。

#### Self-consistency

模型解决自我一致性的办法就是基于CoT生成多个path，最后多个结果再取平均

### RLHF

RLHF有两种策略：
- 蒸馏出一个RM模型
- 直接使用LLM的反馈作为奖励信号

作者使用了A2C而不是PPO作为强化学习算法


### Evaluation

一共有三个指标

#### AI Labeler alignment

用于判断RM的偏好和人类偏好的相似度
$$
\text { Align }=\frac{\sum_{i=1}^D \mathbb{1}\left[\arg \max _x p r_{a i_i}=\arg \max _x p r_{h_i}\right]}{|D|}
$$

#### Pairwise accuracy

用标注好的数据判断不同RM的效果

#### Win rate

让人类去判断由不同RM训练出来的模型的好坏


## Experimental details

### Datasets

- **来源**：这项研究的数据集来源于Stiennon et al. (2020)的工作，由OpenAI策划的Reddit TL;DR数据集。

- **Reddit TL;DR数据集的特点**：
	- **内容**：包含约3百万来自Reddit的帖子，涵盖各种主题（也称为"subreddits"）。
	- **摘要**：每个帖子旁边都有原始作者写的帖子摘要。
	- **筛选**：OpenAI对数据进行了额外的筛选以确保高质量。
	- 使用了一个白名单，只包含对普通人来说容易理解的subreddits。
	- 只包括摘要在24到48个令牌之间的帖子。
	- **数据集大小**：筛选后的数据集包含123,169个帖子，其中约5%被留作验证集。
	- **更多细节**：可以在原始论文中找到。

- **OpenAI策划的人类偏好数据集**：
	- **来源**：这个数据集是从筛选后的Reddit TL;DR数据集中策划出来的。
	- **内容**：对于给定的帖子，从不同的策略中生成了两个候选摘要，并要求标注者评价他们更喜欢哪个摘要。
	- **数据集大小**：总数据集包含约92k的成对比较。

简而言之，这段话描述了两个数据集：一个是OpenAI策划的Reddit TL;DR数据集，另一个是基于这个数据集策划的人类偏好数据集。这两个数据集都经过了筛选和处理，以确保其质量和适用性。

### LLM labeling

> 为了评估AI标记技术（如提示、自我一致性）的有效性，我们从TL;DR（Too Long; Didn't Read）偏好数据集中选择了那些人类注释者对一个摘要比另一个摘要更有信心的示例。我们在数据集的训练分割的随机15%子集上评估AI标记者对齐度，以便更快地进行实验迭代，为评估提供了2851个示例。对于奖励模型的训练TL;DR偏好数据集的完整训练分割由LLM标记并用于训练，而不考虑信心分数。我们使用PaLM 2作为我们的LLM来标记偏好。除非另有说明，我们使用Large模型大小，最大上下文长度为4096个令牌。对于链式思考生成，我们设置了最大解码长度为512个令牌，并使用温度T = 0进行采样（即贪婪解码）。对于自我一致性实验，我们使用温度T = 1进行top-K采样，K=40。

### Model training

> 在OpenAI的过滤后的TL;DR（Too Long; Didn't Read）数据集，使用PaLM 2 Extra-Small 训练SFT模型。然后，我们从SFT模型初始化我们的奖励模型，并在OpenAI的TL;DR人类偏好数据集上训练它们。对于第5.1节和表1中的结果，我们使用PaLM 2 Large生成AI标记的偏好，使用OpenAI + COT 0-shot提示而不使用自我一致性，然后在完整的偏好数据集上训练奖励模型。对于强化学习，我们按照附录B中的描述使用Advantage Actor Critic (A2C)训练策略。策略和价值模型都是从SFT模型初始化的。我们使用过滤后的Reddit TL;DR数据集作为初始状态来展开我们的策略。

### Human evaluation

我们收集了1200个来自人类的评分来评估RLHF和RLAIF策略。对于每一个评分任务，评估者会被展示一个帖子和4个由不同策略生成的摘要（每种策略RLAIF、RLHF、SFT和人类参考各一个）并被要求按照质量对它们进行排序，不允许有并列。帖子是从TL;DR监督微调数据集的保留集中抽取的，该数据集在任何其他评估中都没有被使用。一旦收集了这些排名，就可以计算关于任意两种策略的胜率。

## Results

### RLAIF vs. RLHF

![400](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137718.png)

- 用RL策略训练后的模型确实比SFT要受人类欢迎

### Prompting Techniques

测试不同模型和prompt的AI Labeler alignment

![500](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137815.png)

CoT就够了，few-shot反而影响性能

### Self-consistency

![400](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137904.png)

实验发现生成多个结果取平均反而降低性能，但是我不理解为什么作者在这使用了T=1而不小一点的数字，导致这个结论支撑力不够。

### Size of LLM labeler

模型越大越好，并且因为这是一劳永逸的，所以作者认为没必要省

![500](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137958.png)


### Number of preference examples

用AI反馈训练的RM和人类反馈的对比如下：

![500](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/+Papers/RLAIF%20Scaling%20Reinforcement%20Learning%20from%20Human%20Feedback%20with%20AI%20Feedback/IMG-20240910115137999.png)

虽然两种方法差不多，但是似乎人类反馈最后仍然有上升空间，但是AI反馈的没有再提升的趋势了。

## Qualitative analysis

RLHF: 表达的连贯性更强
RLAIF: 生成内容的幻觉更少

## Conclusion

在这项工作中，我们展示了RLAIF可以在不依赖人类注释者的情况下，与RLHF产生相当的改进。我们的实验显示RLAIF大大改进了SFT基线，改进的幅度与RLHF相当。在直接对比中，RLAIF和RLHF被人类以相似的比率偏好。我们还研究了各种AI标注技术，并进行了规模研究，以了解生成对齐偏好的最佳设置。

## Future work

这项工作只探索了摘要任务，关于其是否能推广到其他任务仍是一个悬而未决的问题。

估计LLM推断与人类标注在货币成本方面是否有优势。

RLHF与RLAIF结合是否可以超越单一方法

使用与策略模型大小相同的LLM标注器是否可以进一步改进策略（即模型是否可以"自我改进"）
