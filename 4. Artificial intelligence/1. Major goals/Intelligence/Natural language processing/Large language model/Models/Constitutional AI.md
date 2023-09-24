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

![Pasted image 20230511221912](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/Pasted%20image%2020230511221912.png)

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

(Supervised Stage) Critique → Revision → Supervised Learning

在该过程的第一阶段，我们首先使用helpful-only AI assistant回答有害的提问。 最初这些提问的回答也是有害的。 然后我们要求模型根据宪法中的原则对其响应进行评论，然后根据评论修改原始响应。 我们按顺序反复修改响应，在每个步骤中我们从宪法中随机抽取原则。 完成此过程后，我们将通过对最终修改后的响应进行监督学习来微调预训练语言模型。 此阶段的主要目的是轻松灵活地改变模型响应的分布，以减少第二个 RL 阶段的探索需求和总训练时间。


(RL Stage) AI Comparison Evaluations → Preference Model → Reinforcement Learning

这个阶段模仿RLHF，不同之处在于，作者用“AI 反馈”代替人类对harmlessness的偏好，其中 AI 根据一组宪法原则评估反应。 就像 RLHF 将人类偏好提炼成单一偏好模型 (PM) 一样。在这个阶段，我们将 LM 对一组原则的解释蒸馏回一个hybrid human/AI PM (as we use human labels for helpfulness, but only AI labels for harmlessness)。 我们从第一阶段通过监督学习 (SL) 训练的 AI 助手开始，并使用它对有害提示数据集中的每个提示生成一对响应。 然后，我们将每个提示和配对形成一个多项选择问题，我们根据宪法原则询问哪个回答是最好的。 这会生成一个 AI 生成的无害偏好数据集，我们将其与人类反馈有用数据集混合。 然后，我们按照 [Bai et al., 2022] 中的过程，在这个比较数据上训练一个偏好模型，生成一个可以为任何给定样本分配分数的 PM。 最后，我们通过 RL 针对此 PM 从第一阶段微调 SL 模型，从而产生由 RLAIF 训练的策略。

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

![](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/Pasted%20image%2020230924214544.png)

![](../../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/Pasted%20image%2020230924214851.png)

首先，模型参数量在大于52B的时候，性能能和人类相媲美。
其次，CoT对性能有明显提升。
最后，4.1有介绍实验细节，附录B有更多的实验结果。

## Constitutional AI: Critiques, Revisions, and Supervised Learning

### Method







