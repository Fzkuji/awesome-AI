Title: TALLRec: An Effective and Efficient Tuning Framework to Align Large Language Model with Recommendation (TALLRec: 一种有效节省的调整框架，用于将大型语言模型用于推荐系统)

- Authors: Keqin Bao, Jizhi Zhang, Yang Zhang, Wenjie Wang, Fuli Feng, Xiangnan He
- Affiliation: Keqin Bao is affiliated with University of Science and Technology of China, China
- Keywords: Recommendation, Large Language Models, Instruction Tuning
- URLs: 
	- https://arxiv.org/abs/2305.00447
	- [GitHub TALLRec](https://github.com/SAI990323/TALLRec)

总结：简单来说就是通过两次tuning来优化LLMs模型，使其适应推荐任务，两次优化分别是**instruction tuning**和**rec-tuning**。instruction tuning就是通常所说的instruction tuning，rec-tuning是专门为推荐任务设计的对话方式，有特定的格式。事实证明instruction tuning让模型更会聊天，rec-tuning让模型更会推荐。

- a. Research background of this article:
	- 文章旨在使用LLMs来训练推荐模型，以利用有限的用户历史交互数据。
	- 任务涉及使用LLMs表示用户与物品的交互序列，通过预测用户对新物品的喜好来进行推荐。
- b. Past methods, their problems, and motivation:
	- 传统推荐模型和基于LLMs的推荐方法均存在一些问题，如无法捕捉长期依赖关系和对用户历史信息进行有效整合等。因此，需要一种新的框架来实现LLMs与推荐任务之间的对齐。
- c. Research methodology proposed in this paper:
	- 文章提出了TALLRec框架，包括**instruction tuning**和**rec-tuning**两个阶段。
	- **instruction tuning**使用自然语言将推荐任务转化为翻译问题，以便LLMs可以从任务指令和生成的输入/输出对中学习推荐能力。
	- **rec-tuning**使用少量推荐数据对模型进行训练，以进一步提高LLMs的推荐能力。
- d. Task and performance achieved by the methods in this paper:
	- TALLRec框架在两个实验数据集上表现出色，相对于传统的推荐模型和基于LLMs的推荐方法，TALLRec在AUC和HR度量上均取得了显著改进，同时具有高效的计算性能和良好的领域泛化性能。

## Background

- Subject and characteristics:
	- 本研究探讨了如何使用LLMs来提高推荐系统的性能，包括数据量的限制、推荐任务的序列性和用户行为的复杂性等问题。
- Historical development:
	- 最初的推荐系统主要是基于协同过滤和内容过滤等简单方法，后来逐渐发展为基于深度学习和LLMs等复杂方法。近年来，研究人员开始探索如何用更少的数据进行推荐，如迁移学习和元学习等方法。
- Past methods:
	- 传统的推荐系统方法包括协同过滤、内容过滤、矩阵分解等简单方法；
	- 基于LLMs的推荐方法则采用预训练的语言模型，以提取语义的上下文信息和用户历史交互信息。
- Past research shortcomings:
	- 过去的推荐算法难以解决长期依赖关系和提取用户历史信息等问题。
	- 基于LLMs的推荐方法需要大量数据和计算资源，使得推广应用受到限制。

## Methods

- TALLRec框架涉及**instruction tuning**和**rec-tuning**两个主要步骤
	- instruction tuning就是常见的LLM
	- 之后，使用LoRA技术，通过多次迭代来对LLMs进行微调，提高性能


### TALLRec Tuning Stages

![Fig. 2](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig.%202.png)

#### Instruction tuning

![400](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Pasted%20image%2020230515204642.png)

Employ the self-instruct data made available by Alpaca to train the model.

目标函数为conditional language modeling objective:
$$
\max _{\Phi} \sum_{(x, y) \in \mathcal{Z}} \sum_{t=1}^{|y|} \log \left(P_{\Phi}\left(y_t \mid x, y_{<t}\right)\right)
$$
就是很简单的序列预测的目标函数？

#### Recommendation tuning

Recommendation tuning, or rec-tuning就是构建一个专门的输入格式，如图2右上角所示。

![500](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Pasted%20image%2020230515204718.png)

- Rec Input
	- User Preference:  $item_1$, $item_4$, $item_n$
	- User Unpreference: $item_2$, $item_3$, $item_{n-1}$
	- Whether the user will enjoy the target movie/book: $item_{n+1}$
- Rec Output
	- Yes./No.

### LoRA

本文使用LoRA训练方法, which involves freezing the pre-trained model parameters and introducing trainable rank decomposition matrices into each layer of the Transformer architecture to facilitate lightweight fine-tuning.

In total, the final learning objective can be computed as:
$$
\max _{\Theta} \sum_{(x, y) \in \mathcal{Z}} \sum_{t=1}^{|y|} \log \left(P_{\Phi+\Theta}\left(y_t \mid x, y_{<t}\right)\right)
$$
where $\Theta$ is the LoRA parameters and we only update LoRA parameters during the training process.

### Backbone selction

Conduct experiments using LLMs-LLaMA
因为开源了

## Experiments

### Performance comparison

和传统方法比较：
![Table 2](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Table%202.png)

和同类LLMs比较：
![Fig. 3](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig.%203.png)

其他的模型波动好大，传统模型效果有瓶颈。反正结论是这个方法好。

### Ablation study

![Fig. 4](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig.%204.png)

IT是instruction tuning，RT是recommendation tuning

- 单纯的IT似乎对推荐任务没什么帮助，只是让模型更会聊天了？比如接收指令给出答案的格式
- RT显著提升了性能
- IT+RT在数据量较少(≤ 128)的情况下性能更高，说明训练聊天能力对后续微调有帮助的

最终50个不到的样本就可以超过传统方法

### Cross-domain generalization analyses

![Fig. 5](Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig.%205.png)

- 训练了一个数据集，在两个任务上都有提升
- 训练两个数据集时，性能最优，这表明TALLRec可以无缝地合并来自不同域的数据以增强其性能


## Conclusion

- Significance of the work:
	- 本研究提出了一种新的框架TALLRec来解决LLMs在推荐任务中的应用问题，并使用了相应的微调方法LoRA
- Innovation, performance, and workload:
	- TALLRec框架在指令校准和微调阶段使用LLMs，采用轻量级的微调方法并且仅需训练少量的可训练参数，因此性能和计算时间效率都得到了较大提升
- Research conclusions:
	- 实验结果表明，在不同的数据集上，TALLRec框架相对传统方法和基于LLMs的推荐方法均取得了显著的性能提升，同时也表现出了良好的领域泛化性能

