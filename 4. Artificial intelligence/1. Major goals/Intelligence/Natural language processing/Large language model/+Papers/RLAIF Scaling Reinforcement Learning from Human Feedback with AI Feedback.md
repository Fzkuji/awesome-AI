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

## Methods

作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

  

## Evaluation

作者如何评估自己的方法？实验的setup是什么样的？感兴趣实验数据和结果有哪些？有没有问题或者可以借鉴的地方？

  

## Conclusion

作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?

  

## References

(optional) 列出相关性高的文献，以便之后可以继续track下去。




## Appendices

### Appendix A



### Appendix B



## Future work

值得研究的点







