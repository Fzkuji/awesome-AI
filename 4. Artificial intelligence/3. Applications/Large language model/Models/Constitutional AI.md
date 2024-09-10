Title: Constitutional AI: Harmlessness from AI Feedback
https://arxiv.org/abs/2212.08073

> ChatGPT用的RLHF，我们最近正好在关注RLHF的一些研究，发现Anthropic已经出现了用AI来监督AI的工作，自我进化不再是梦想……本文提出了RLAIF方法，旨在提升LLM的有用性的同时提升可靠性（降低毒性）。目前的模型存在的问题：
> 
> 1.  一些有害的query会被chatgpt驳回，回答“我并不能回答这个问题”或者“没有一个很好的解决方法，但是我认为……”，这是无毒的，但是也没用；
> 2.  一些有害的query会被chatgpt解答，例如关于如何黑到邻居家里，这是有用的，但是也有毒。
>
> 本文旨在用Reinforcement Learning from AI Feedback方法，同时提升有用性和降低毒性。Anthropic研究团队称之为CAI（Constitutional AI）。如果你对RLHF的sota感兴趣，可以接着读。
> 
> ——[HeptaAI](https://www.zhihu.com/people/jackgethome)

关键贡献：RL from AI Feedback



## Abstract

The process involves both a supervised learning and a reinforcement learning phase. 
- In the supervised phase we sample from an initial model, then generate self-critiques and revisions, and then finetune the original model on revised responses. 
- In the RL phase, we sample from the finetuned model, use a model to evaluate which of the two samples is better, and then train a preference model from this dataset of AI preferences.

随着AI技术的进步，我们希望利用高级AI来监控其他AI系统。我们探索了一种新方法，称为“宪法AI”，旨在通过自我完善来培训一个无害的AI助手，而不依赖于标记有害内容的人工标签。这种方法只需要一套规则或原则进行人类监督。该方法结合了监督学习和强化学习两个阶段。在**监督学习**阶段，我们从初步模型中抽取数据，进行自我评估和调整，然后对模型进行微调。在**强化学习**阶段，我们从微调后的模型中抽取数据，并使用另一个模型来判断哪个数据更优，从而培训出一个基于AI偏好的模型。接着，我们使用这个偏好模型作为奖励信号进行训练。这样，我们成功培训出了一个能够对有害查询进行解释的无害AI助手。此外，这些方法还利用了*链式思维推理*，提高了AI决策的透明度和效率，同时减少了对人工标签的依赖。

## Introduction

想要让AI输出无害的结果，那必须依靠AI自己去优化，RLHF因为有人类参与，不可避免得存在偏见

在本文中，我们开发了一种我们称为Constitutional AI (CAI)的方法，并用它来训练一个非回避和相对无害的 AI 助手，没有任何人类反馈的影响。如Figure 1所示：

![Pasted image 20230511221912](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Constitutional%20AI/IMG-20240910115137321.png)

我们开发这项技术的动机是：
1. 研究使用AI帮助监督其他人工智能的可能性，从而扩大监督规模
2. 通过消除回避反应、处理helpfulness和harmlessness的关系，鼓励AI解释其对有害行为的反对意见，改进我们之前训练无害AI助手的工作要求
3. 使管理AI行为的原则及其实施更加透明
4. 通过避免在改变目标时收集新的人类反馈标签的需要来减少迭代时间

### Motivations

#### Scaling supervision
缩放（人工）监督

There are several reasons why this may be useful: 

- AI比人快
	- 它使我们能够更专注于提供少量清晰、重点突出、高质量的监督
	- 人类和人工智能系统也有合作的方式[Measuring Progress on Scalable Oversight for Large Language Models](https://arxiv.org/abs/2211.03540)，比任何一个单独提供更好的监督
- AI比人强

扩大监管也可能有缺点和危险，因为这意味着进一步自动化（并且很可能模糊）决策。

作者在本工作中使用了**以自然语言表述的十个简单原则**，将 AI 模型微调至无害？

本文主要是减少人类在无害化的监督学习上的干预，而非减少所有监督学习上人类的干预。但是长期来讲，还是越少越好

#### A harmless but non-evasive (still helpful) assistant
一个无害但不回避（仍然有帮助）的助手

一个用“我不知道”回答所有问题的AI助手是无害的，但它也完全没有用的。AI使用这种回复的主要原因是众包的人就是这么回复的（AI真的有偏见吗？还是人本身的偏见？抑或是人本身就畏惧客观？）

（他们这些研究员的）理想状态是让AI在拒绝回答时给出原因

#### Simplicity and Transparency

RLHF既要很多人，也不透明。作者希望通过三种方式改善这种情况：

- by literally encoding the training goals in a simple list of **natural language instructions or principles**
- by using **chain-of-thought** reasoning to make AI decision making explicit during training
- by training AI assistants that **explain why** they are declining to engage with harmful requests

### The Constitutional AI approach

We will be experimenting with an extreme form of scaled supervision, which we refer to as **Constitutional AI (CAI)**.

Our training process has two stages (see Figure 1), where 
- the first supervised phase gets the model "ondistribution"
- the second RL stage refines and significantly improves performance

整个训练过程可以分为2个阶段：

**Phase1: 监督版的CAI模型** **Critique → Revision → Supervised Learning**

- 针对有害的prompts, 有帮助的AI模型生成有害的初始回复
- 要求AI模型 基于宪法集合里面采样的宪法准则，判断是否回复有害；如果有害，则修改回复内容，该过程重复多次，直到获得最终的修订版本 **Critique** _→_ **Revision**
- 根据修订的回复内容，对预训练语言模型进行微调  **Supervised Learning**

这个阶段的目标主要是修改语言模型的输出分布，从而可以降低第二阶段强化学习的探索空间和训练时长

**注意：**生成回复的AI模型指其他经过RL微调过的模型Helpful RLHF，instructGPT研究中提到过，当要求AI模型生成有害内容时，其生成的有害程度远比其他模型，相当于一个监督训练的插件，实时产生监督样本

**Phase2: RL版的CAI模型** **AI Comparison Evaluations** _→_ **Preference Model** _→_ **Reinforcement Learning**

- 使用第一阶段SL-CAI模型，对每个有害提示生成一组回复，并和prompt组成多项选择题，要求基于宪法准则给出哪个回复是最佳选项，最终产生无害的AI偏好数据 **AI Comparison Evaluations**
- 混合AI反馈获得的无害偏好数据 + 人类标注的有帮助的偏好数据，训练一个偏好模型 **Preference Model** 
- 基于PM模型和SL-CAI模型训练RL-CAI模型 **Reinforcement Learning**

这个阶段和RLHF的训练方式唯一区别就是，无害的偏好数据是AI模型基于宪法准则产生的，其他部分和instructGPT的第二阶段 + 第三阶段相同；

### Contributions

本工作的三个主要贡献如下：

1. 我们展示了宪法方法，利用有助于的RLHF模型来训练有助于和无害的模型（如[Askell等，2021, Bai等，2022]所讨论和定义的），而不使用任何关于无害性的人类反馈标签：
   - 随着语言模型能力的提高，AI识别伤害的能力显著提高。此外，链式思维推理进一步提高了这一能力，并导致评估与基于人类反馈标签训练的偏好模型竞争（见图4）。

2. 我们展示了模型生成的批评和修订可以反复应用，以逐步减少有害性（见图5）。与直接生成修订相比，生成批评可以提高无害性（见图7）。我们使用这种方法专门解决我们之前基于人类反馈的模型[Bai等，2022]的逃避性。

3. 使用自我监督的偏好标签进行RL进一步改善了模型行为，如众工作者所评估的（见图2和3），等于或超过使用人类反馈评估无害性时的性能。

我们附上了一个Github仓库，展示了使用的各种少数提示和宪法原则，以及模型对各种提示的响应。


### Models and data

我们使用了一系列的语言模型，这些模型是按照我们在之前的工作[Bai等，2022]中描述的方式进行预训练的。由于我们的目标是从纯粹有助于的助手中训练出有助于和无害的助手，所以我们使用RLHF来训练我们的初始有助于的模型。为此，我们使用相同的过程，但只使用有助于的人类反馈（HF）数据。然而，作为一个比较点，我们还使用人类反馈训练了新的偏好模型和有助于和无害的RLHF策略。

在我们之前的工作[Bai等，2022]中，我们收集了偏好模型比较的人类反馈数据。具体来说，每个数据样本包括一个提示和一对模型生成的对该提示的响应；然后，众工者标记被认为更有助于或无害的响应，具体取决于手头的任务。有助于和无害的数据是分开收集的，工人被要求为后者“红队”模型（即，编写可能引发有害模型响应的提示）。然后，我们通过RLHF训练了两种类型的模型：（1）仅基于有助于数据训练的有助于的模型，和（2）基于有助于和无害性训练的“HH”模型。过去的实验[Bai等，2022]显示，RLHF显著提高了模型的遵循指令的能力，而HH模型比有助于的模型显著更无害。


## Evaluating the potential for AI supervision of HHH

helpful, honest, and harmless

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Constitutional%20AI/IMG-20240910115137614.png)

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Constitutional%20AI/IMG-20240910115137748.png)

首先，模型参数量在大于52B的时候，性能能和人类相媲美。
其次，CoT对性能有明显提升。
最后，4.1有介绍实验细节，附录B有更多的实验结果。

## Constitutional AI: Critiques, Revisions, and Supervised Learning

### Method

作者设计了prompt流程来对有害发言进行修改。

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Constitutional%20AI/IMG-20240910115137840.png)

我们总共编写了16个与无害性相关的不同原则，其中许多相当相似并以一般意义上解决有害性，而其他则旨在针对特定领域。它们在每个红队提示的每个修订步骤中都被随机抽样。
我们总共编写了16个与无害性相关的不同原则，其中许多相当相似并以一般意义上解决有害性，而其他则旨在针对特定领域。它们在每个红队提示的每个修订步骤中都被随机抽样。

我们还发现，语言模型有时会对其观点感到困惑。例如，它可能在应该生成修订的地方生成批评，反之亦然。我们通过为模型提供批评和修订的示例来解决这个问题，所有这些示例都以相同的方式格式化。我们在附录E和我们的存储库中都包含了这些少数示例。

我们在附录D中展示了一个流程示例。从质量上看，我们发现原始回应通常包含有害内容，而第一次修订几乎总是去除了大部分有害性。后续的修订有时会进一步改进结果，但这在检查时不那么明显。此外，我们发现修订后的回应很少是逃避的（与附录D中的示例进行比较），意思是模型愿意以无害、深思熟虑的方式参与敏感话题，而不是关闭讨论，我们在第4.4节中更详细地讨论了这一点。

接下来，我们在修订版（来自所有修订步骤）上微调一个预训练的模型。此外，为了尽可能保留有用性，我们从一组由众包工作者收集的有用性提示中，从有用的RLHF模型中抽取回应，并将这些包含在微调中。主要结果在第3.3节中呈现，其中这些模型被称为“SL-CAI”。

在第3.5节中，我们还讨论了一个更简单的替代方案，即我们跳过批评步骤并直接抽取修订，但我们在本文的其余部分都使用了经过批评的修订。

### Datasets and training

对于红队测试提示（即部分对话），我们按照[Ganguli et al., 2022]中的讨论和分享，收集了42,496个人工编写的提示，并通过对预训练模型进行少量提示生成了进一步的140,335个提示，总计182,831个。我们从一个有帮助的RLHF模型中为每个红队提示抽样4个批评-修订对，每个提示有4次修订。对于有帮助的提示，我们总共收集了135,296个人工编写的提示，并没有使用任何模型生成的示例。我们直接从一个有帮助的RLHF为每个提示抽样2个回应。我们总是在温度T = 1下进行抽样。每次对话包括多个提示，每个人轮流一个。

然后，我们通过在无害修订和有帮助的样本上微调预训练模型来训练SL-CAI模型。我们训练了一个时代，使用的学习率是预训练学习率的0.5倍，批次大小为1024个序列。

### Main results

![](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Constitutional%20AI/IMG-20240910115137915.png)














### Datasets and training





### Main results





