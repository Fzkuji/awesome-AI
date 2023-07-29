## Key concepts

- [Prompting](Prompting.md)
	- [Chain of thought](Chain%20of%20thought.md)
	- [Self-consistency](Self-consistency.md)

## Paradigms

![LMRS paradigm](../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/LMRS%20paradigm.png)
Language Modelling Paradigm Adaptations for Recommender Systems (LMRS)

上图是语言模型在推荐系统上的范式，不过在整个语言模型领域感觉也是通用的。

### Pre-training


### Fine-tuning


### [Prompting](Prompting.md)

Prompt-tuning中也分为Tuning-free prompting、Fixed-PTM prompt tuning和Fixed-prompt PTM tuning三种类别。

它们之间的区别主要在于prompt的设计方式和使用方法。Tuning-free prompting是一种无需进行微调或参数调节的方法，Fixed-PTM prompt tuning和Fixed-prompt PTM tuning则需要进行微调或参数调节，并且需要预先定义特定的prompt文本。选择合适的prompting方法应该根据具体的任务需求和实际情况来决定。

这三种方法也可以结合使用，变成混合训练模式。

#### Tuning-free prompting

Tuning-free prompting是一种无需进行额外微调或参数调节的prompting方法，它通过设计合适的prompting方式，直接利用预训练语言模型的能力来生成高质量的文本输出。该方法通常不需要额外的训练数据和标注，也不需要特定的模型架构和参数设置，可以大大降低模型的训练成本和复杂度。

#### Fixed-PTM prompt tuning

Fixed-PTM prompt tuning是一种在预训练语言模型上进行微调或参数调节的prompting方法，它通常需要在预训练模型的基础上进行少量的fine-tuning或调参，以满足特定的任务需求和输出要求。这种方法通常需要额外的训练数据和标注，以及针对具体任务的模型架构和参数设置，以达到更好的性能和效果。

#### Fixed-prompt PTM tuning

Fixed-prompt PTM tuning是一种在预定义的prompt上进行微调或参数调节的prompting方法，它通常需要预先定义好一组特定的prompt文本，并在这些prompt文本的基础上进行微调或调参。这种方法通常需要额外的训练数据和标注，以及特定的模型架构和参数设置，以实现更精细的输出控制和生成任务。

## Objectives

![常用评估分数](../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/%E5%B8%B8%E7%94%A8%E8%AF%84%E4%BC%B0%E5%88%86%E6%95%B0.png)

### LLM objectives

- Auto-regressive
- Partial auto-regressive modelling
- Masked Language Modelling (MLM)
- Next Sentence Prediction (NSP)
- Replaced Token Detection(RTD)

## Related resources

### Repositories

#### Frameworks

**FlagAI**
- https://github.com/FlagAI-Open/FlagAI
- **FlagAI飞智**是一个快速、易于使用和可扩展的大模型工具包。 我们的目标是支持在多模态的各种下游任务上训练、微调和部署大规模模型。

#### Paper lists

- [Chain-of-Thoughts Papers](https://github.com/Timothyxxx/Chain-of-ThoughtsPapers)
- [LLM Tool Use Papers](https://github.com/xlang-ai/llm-tool-use)
- [EnvInteractive and Decision Making(personnal usage)](https://github.com/Timothyxxx/EnvInteractiveLMPapers)

## Works


### Surveys

| Name | Paper                                                                        | Publication    | Repository                                                                                                      | Link                                                 |
| ---- | ---------------------------------------------------------------------------- | -------------- | --------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
|      | [ChatGPT调研报告](ChatGPT%E8%B0%83%E7%A0%94%E6%8A%A5%E5%91%8A.pdf%5C)                                     |                |                                                                                                                 |                                                      |
|      | [A Survey of Large Language Models](#A%20Survey%20of%20Large%20Language%20Models)                                       | arXiv preprint | [LLMSurvey](https://github.com/RUCAIBox/LLMSurvey)                                                              | [arXiv:2303.18223](https://arxiv.org/abs/2303.18223) |
|      | [Harnessing the Power of LLMs in Practice A Survey on ChatGPT and Beyond](#Harnessing%20the%20Power%20of%20LLMs%20in%20Practice%20A%20Survey%20on%20ChatGPT%20and%20Beyond) | arXiv preprint | [LLMsPracticalGuide](https://github.com/Mooler0410/LLMsPracticalGuide)                                          | [arXiv:2304.13712](https://arxiv.org/abs/2304.13712) |
|      | [A Survey on Multimodal Large Language Models](#A%20Survey%20on%20Multimodal%20Large%20Language%20Models)                            | arXiv preprint | [Awesome-Multimodal-Large-Language-Models](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models) | [arXiv:2306.13549](https://arxiv.org/abs/2306.13549) |
|      |                                                                              |                |                                                                                                                 |                                                      |


| Name | Paper                                                | Publication    | Repository                                                       | Link                                                 | Classification |
| ---- | ---------------------------------------------------- | -------------- | ---------------------------------------------------------------- | ---------------------------------------------------- | -------------- |
|      | [Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing](#Pre-train,%20Prompt,%20and%20Predict%20A%20Systematic%20Survey%20of%20Prompting%20Methods%20in%20Natural%20Language%20Processing) | arXiv preprint | [Pretrain, Prompt, Predict](http://pretrain.nlpedia.ai/) | [arXiv:2107.13586](https://arxiv.org/abs/2107.13586) | [Surveys](#Surveys), [Prompting](#Prompting) |
|      | [A Survey on In-context Learning](#A%20Survey%20on%20In-context%20Learning) | arXiv preprint |            | [arXiv:2301.00234](https://arxiv.org/abs/2301.00234) | [Surveys](#Surveys), [In-context learning](#In-context%20learning) |
|      | [Domain Specialization as the Key to Make Large Language Models Disruptive: A Comprehensive Survey](#Domain%20Specialization%20as%20the%20Key%20to%20Make%20Large%20Language%20Models%20Disruptive%20A%20Comprehensive%20Survey) | arXiv preprint |            | [arXiv:2305.18703](https://arxiv.org/abs/2305.18703) | [Surveys](#Surveys) |
|      | [A Survey on Evaluation of Large Language Models](#A%20Survey%20on%20Evaluation%20of%20Large%20Language%20Models) | arXiv preprint | [LLM-eval-survey](https://github.com/MLGroupJLU/LLM-eval-survey) | [arXiv:2307.03109](https://arxiv.org/abs/2307.03109) | [Surveys](#Surveys)   | 


### Models

- OpenAI
	- [GPT-123](Models/GPT/GPT-123.md)
	- [InstructGPT](Models/GPT/InstructGPT.md)
	- [ChatGPT](Models/GPT/ChatGPT.md)
	- [GPT-4](Models/GPT/GPT-4.md)
- Google
	- [BERT](Models/BERT.md)
- Meta
	- [LLaMA](Models/LLaMA.md)
	- [LLaMa 2](Models/LLaMa%202.md)
- DeepMind
	- [Chinchilla](Models/Chinchilla.md)
- Anthropic
	- [Claude](Models/Claude.md)
	- [Claude 2](Models/Claude%202.md)

[LLaMA-Adapter](Models/LLaMA-Adapter.md) - Shanghai Artificial Intelligence Laboratory,  CUHK MMLab, University of California, Los Angeles

[ChatGPT的朋友们：大语言模型经典论文一次读到吐](https://mp.weixin.qq.com/s/ATd6D00aPOV8hIqvBoWAFA)

[Awesome-Multimodal-Large-Language-Models](https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models)



### Prompting

| Name | Paper                                                                                                                                                                                                                                        | Publication    | Repository                                               | Link                                                 | Classification      |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | -------------------------------------------------------- | ---------------------------------------------------- | ------------------- |
|      | [Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing](#Pre-train,%20Prompt,%20and%20Predict%20A%20Systematic%20Survey%20of%20Prompting%20Methods%20in%20Natural%20Language%20Processing) | arXiv preprint | [Pretrain, Prompt, Predict](http://pretrain.nlpedia.ai/) | [arXiv:2107.13586](https://arxiv.org/abs/2107.13586) | [Surveys](#Surveys), [Prompting](#Prompting) |


### In-context learning

| Name | Paper                                                                       | Publication    | Repository | Link                                                 | Classification                                                     |
| ---- | --------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ------------------------------------------------------------------ |
|      | [A Survey on In-context Learning](#A%20Survey%20on%20In-context%20Learning) | arXiv preprint |            | [arXiv:2301.00234](https://arxiv.org/abs/2301.00234) | [Surveys](#Surveys), [In-context learning](#In-context%20learning) |

### Multimodal LLM

| Name | Paper                                                                                                                                                                                                   | Publication    | Repository                                            | Link                                                 | Classification                                                                                                                                                                                                |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|      | [TabLLM: Few-shot Classification of Tabular Data with Large Language Models](#TabLLM:%20Few-shot%20Classification%20of%20Tabular%20Data%20with%20Large%20Language%20Models) | arXiv preprint | [TabLLM](https://github.com/clinicalml/TabLLM) | [arXiv:2210.10723](https://arxiv.org/abs/2210.10723) | [Models](#Models), [Multimodal LLM](#Multimodal%20LLM) |
|      | [Macaw-LLM: Multi-Modal Language Modeling with Image, Audio, Video, and Text Integration](#Macaw-LLM%20Multi-Modal%20Language%20Modeling%20with%20Image,%20Audio,%20Video,%20and%20Text%20Integration) | arXiv preprint | [Macaw-LLM](https://github.com/lyuchenyang/Macaw-LLM) | [arXiv:2306.09093](https://arxiv.org/abs/2306.09093) | [Models](#Models), [Multimodal LLM](#Multimodal%20LLM) |
| Kosmos-2 | [#Kosmos-2 Grounding Multimodal Large Language Models to the World](#Kosmos-2%20Grounding%20Multimodal%20Large%20Language%20Models%20to%20the%20World)          | arXiv preprint | [kosmos-2](https://github.com/microsoft/unilm/tree/master/kosmos-2) | [arXiv:2306.14824](https://arxiv.org/abs/2306.14824) |


### Tool learning

| Name       | Paper                                                                               | Publication    | Repository                                                                      | Link                                                 |
| ---------- | ----------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------------- | ---------------------------------------------------- |
| TALM       | [#TALM Tool Augmented Language Models](#TALM%20Tool%20Augmented%20Language%20Models)                                            | arXiv preprint |                                                                                 | [arXiv:2205.12255](https://arxiv.org/abs/2205.12255) |
| ReAct      | [#ReAct Synergizing Reasoning and Acting in Language Models](#ReAct%20Synergizing%20Reasoning%20and%20Acting%20in%20Language%20Models)                      | arXiv preprint | [ReAct](https://react-lm.github.io/)                                            | [arXiv:2205.12255](https://arxiv.org/abs/2205.12255) |
| PAL        | [#PAL Program-aided Language Models](#PAL%20Program-aided%20Language%20Models)                                              | arXiv preprint | [pal](https://github.com/reasoning-machines/pal)                                | [arXiv:2211.10435](https://arxiv.org/abs/2211.10435) |
| Toolformer | [#Toolformer Language Models Can Teach Themselves to Use Tools](#Toolformer%20Language%20Models%20Can%20Teach%20Themselves%20to%20Use%20Tools)                   | arXiv preprint | [toolformer-pytorch](https://github.com/lucidrains/toolformer-pytorch)          | [arXiv:2302.04761](https://arxiv.org/abs/2302.04761) |
| ART        | [#ART Automatic multi-step reasoning and tool-use for large language models](#ART%20Automatic%20multi-step%20reasoning%20and%20tool-use%20for%20large%20language%20models)      | arXiv preprint | [language-programmes](https://github.com/bhargaviparanjape/language-programmes) | [arXiv:2303.09014](https://arxiv.org/abs/2303.09014) |
| CRITIC     | [#CRITIC Large Language Models Can Self-Correct with Tool-Interactive Critiquing](#CRITIC%20Large%20Language%20Models%20Can%20Self-Correct%20with%20Tool-Interactive%20Critiquing) | arXiv preprint | [CRITIC](https://github.com/microsoft/ProphetNet/tree/master/CRITIC)            | [arXiv:2305.11738](https://arxiv.org/abs/2305.11738) |
| TRICE      | [#TRICE Making Language Models Better Tool Learners with Execution Feedback](#TRICE%20Making%20Language%20Models%20Better%20Tool%20Learners%20with%20Execution%20Feedback)      | arXiv preprint | [trice](https://github.com/zjunlp/trice)                                        | [arXiv:2305.13068](https://arxiv.org/abs/2305.13068) | 


### Model compression

| Name       | Paper                                                                    | Publication    | Repository                                          | Link                                                 |
| ---------- | ------------------------------------------------------------------------ | -------------- | --------------------------------------------------- | ---------------------------------------------------- |
| LLM-Pruner | [#LLM-Pruner On the Structural Pruning of Large Language Models](#LLM-Pruner%20On%20the%20Structural%20Pruning%20of%20Large%20Language%20Models)       | arXiv preprint | [LLM-Pruner](https://github.com/horseee/LLM-Pruner) | [arXiv:2305.11627](https://arxiv.org/abs/2305.11627) |
| Lion       | [#Lion Adversarial Distillation of Closed-Source Large Language Model](#Lion%20Adversarial%20Distillation%20of%20Closed-Source%20Large%20Language%20Model) | arXiv preprint | [Lion](https://github.com/YJiangcm/Lion)            | [arXiv:2305.12870](https://arxiv.org/abs/2305.12870) | 


### Model acceleration

[【LLM 加速技巧】Muti Query Attention 和 Attention with Linear Bias（附源码）](https://zhuanlan.zhihu.com/p/634236135)


### Paper List

#### Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing

| Name | Paper                                                                                                                                                                                                                                        | Publication    | Repository                                               | Link                                                 | Classification      |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | -------------------------------------------------------- | ---------------------------------------------------- | ------------------- |
|      | [Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing](#Pre-train,%20Prompt,%20and%20Predict%20A%20Systematic%20Survey%20of%20Prompting%20Methods%20in%20Natural%20Language%20Processing) | arXiv preprint | [Pretrain, Prompt, Predict](http://pretrain.nlpedia.ai/) | [arXiv:2107.13586](https://arxiv.org/abs/2107.13586) | [Surveys](#Surveys), [Prompting](#Prompting) |

https://mp.weixin.qq.com/s/TzpHDLr8JC4IhGXR_r10dA



#### TALM: Tool Augmented Language Models

> [!info]
> 
> [arXiv:2205.12255](https://arxiv.org/abs/2205.12255) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)

> 基于transformer的语言模型可以通过提升规模来提升它在各种任务当中的表现。但是，对于一些需要访问特定数据（如：训练时没有见过的数据、经常发生变化的数据、隐私数据等）的任务，语言模型就无法单纯的使用提升规模的方式来提升在这些任务上的表现。针对这个问题，作者提出了TALM，这是一个基于文本的方式，使用外部工具来增强语言模型的表现。
> 
> 具体来说，作者使用预训练好的T5作为基座模型，使用文本到文本的方法进行微调，是语言模型能够更好的使用工具。首先，训练语言模型根据问题输出需要使用的工具和工具相关的参数。然后，根据模型的输出相同相应的外部工具并返回相关结果。最后，训练模型根据问题和外部工具返回的结果，输出最终的答案。

![400](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230615110232.png)
![400](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230615110219.png)

> 为了能够解决样例不足的问题，作者提出了self-play技术。首先，使用一个较小的数据集合D训练得到一个TALM，然后针对数据集中的每一个样例，尝试使用不同的工具来解决这个问题。如果TALM能够正确的解决这个问题，那么就把这条数据及其相关的工具加入到数据集合D中，不断对训练集进行扩充，以得到一个大规模的数据集。
> 
> 在实验方面，作者使用Natural Question和MathQA两个数据集对TALM进行了测试，结果表明使用了外部工具进行增强的语言模型能够更好的处理这些问答相关的问题。同时，作者还对模型不同参数量的版本进行了测试，实验发现随着参数量的提升，语言模型能够更好的使用外部工具。



#### ReAct: Synergizing Reasoning and Acting in Language Models

> [!info]
> 
> [arXiv:2205.12255](https://arxiv.org/abs/2205.12255) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: https://react-lm.github.io/

前人的研究表明，LLM具有很强的推理能力和规划执行能力。在这篇工作中，作者探究了使用LLM去生成推理过程和任务特定的行动。总的来说，推理过程有助于模型归纳、跟踪和更新行动计划以及处理异常，任务特定的行动能够是模型与外部环境或工具进行对接并收集额外信息。

![2210.03629_Page_02](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/2210.03629_Page_02.png)

> 具体来说，对于一个通用的框架，在某一步的推理过程中，模型接收到一个来自外部环境的结果，并根据历史推理过程中外部环境的结果和行动过程来确定下一步需要采取的行动。
> 
> 在ReAct中，可以执行的行动的范围不仅仅是与外部环境的交互，同时也包括了自然语言的推理（可以成为thought或者reasoning trace）。语言模型可以根据上下文决定下一步的行动。
> 
> 作者认为ReAct方式有以下几个优点：1. ReAct所需的prompt非常直观且易于设计；2. ReAct是通用并且灵活的；3. ReAct能够给模型带来性能提升并且具有较强的鲁棒性；4. ReAct是能够与对齐人类偏好并且是可控的。在实验方面，在知识密集型任务HotpotQA和FEVER上的测试都辨明了ReAct的有效性。

简单解释就是，这个模型设计了一些prompt，让模型选择输出
- 语言
- 调用语句
- 思考
三种结果，如果是调用语句就会调用外部接口；如果是思考（think），就会将这个思考连同之前的内容再次让LLM输出一遍。

#### TabLLM: Few-shot Classification of Tabular Data with Large Language Models

| Name | Paper                                                                                                                                                                       | Publication    | Repository                                     | Link                                                 | Classification                                                                                                   |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
|      | [TabLLM: Few-shot Classification of Tabular Data with Large Language Models](#TabLLM:%20Few-shot%20Classification%20of%20Tabular%20Data%20with%20Large%20Language%20Models) | arXiv preprint | [TabLLM](https://github.com/clinicalml/TabLLM) | [arXiv:2210.10723](https://arxiv.org/abs/2210.10723) | [Models](#Models), [Multimodal LLM](#Multimodal%20LLM) |

[大模型尝试：TabLLM与结构化建模](https://mp.weixin.qq.com/s/2QJKi4kqQoqC__Y-w_AuKA)


#### A Survey on In-context Learning

| Name | Paper                                                                       | Publication    | Repository | Link                                                 | Classification                                                     |
| ---- | --------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ------------------------------------------------------------------ |
|      | [A Survey on In-context Learning](#A%20Survey%20on%20In-context%20Learning) | arXiv preprint |            | [arXiv:2301.00234](https://arxiv.org/abs/2301.00234) | [Surveys](#Surveys), [In-context learning](#In-context%20learning) |


#### PAL: Program-aided Language Models

> [!info]
> 
> [arXiv:2211.10435](https://arxiv.org/abs/2211.10435) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: http://reasonwithpal.com/

背景就是Chain-of-Thought效果不好。为了解决这个问题，作者提出了一种新的方法PAL。即设计Instruction，要求大模型以**代码**的形式输出相关的推理过程，最后使用外部工具（如：编译器、执行器）来执行代码并得到最终的答案。

*尝试了一下论文中的任务GSM-HARD，感觉现在ChatGPT+Wolfram的组合已经把问题彻底解决了，而且算出来的基本都是对的。*

![Pasted image 20230615111454](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230615111454.png)

#### Toolformer: Language Models Can Teach Themselves to Use Tools

> [!info]
> 
> [arXiv:2302.04761](https://arxiv.org/abs/2302.04761) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: https://github.com/lucidrains/toolformer-pytorch

> 在众多任务上，大模型展现出了令人印象深刻的能力。仅仅使用很少的样例或者文本形式的指令，大语言模型就能够很好的完成相关任务。但是，大模型在一些场景下仍然存在困难，需要外部工具来辅助以更好的解决这些任务。在这篇工作中，作者提出了一个新的方法，让语言模型自己教自己学习使用外部工具。
> 
> 具体来说，作者构造了一个包含外部工具API使用方法的数据集，之后使用这个数据集对语言模型进行微调，使模型能够掌握工具的使用方法。
> 
> 在数据集的构造当中，作者采样了一些位置加入了对工具的API调用，并比较了插入API和未插入API时二者的损失函数，过滤出了一些构造较为合理的数据，将这些数据进行整合获得了包含API调用的数据集。
> 
> 使用这个数据集，就能够对语言模型进行微调，使他们能够掌握工具的使用方式。
> 
> 在下游任务的推理当中，如果模型生成了相关的API调用，就会停止生成，并使用外部工具根据模型生成的内容获得结果返回给模型，模型在进行后续的生成。在实验方面，作者使用GPT-J作为基座模型，在推理任务上超越了baseline。

总的来说就是构建数据集，再微调

![Pasted image 20230615221040](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230615221040.png)

#### ART: Automatic multi-step reasoning and tool-use for large language models

> [!info]
> 
> [arXiv:2303.09014](https://arxiv.org/abs/2303.09014) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: https://github.com/bhargaviparanjape/language-programmes

> 大语言模型可以在few-shot或者zero-shot设置下通过生成推理过程来进行推理。同时，外部工具可以用来辅助模型处理下游任务。在前人的工作中，对于一个新的任务或者新的工具，需要手动构造相关的样例用于提示大模型。作者提出了ART，它能够冻结大模型并自动以程序的形式生成中间的推理步骤。

具体来说，ART分为三个部分：prompt构建、生成、人类反馈。其中，人类反馈的步骤是一个可选的步骤。

- 在prompt构建的过程中，ART从任务库中检索出相似的任务用于few-shot learning。如图中(A)所示。
- 在生成过程中，大模型可以生成相关的代码（比如，[search]、[generate code]这样的标签），一旦检测到调用外部工具的要求，ART会停止大模型的生成过程，并调用外部工具返回结果。如此不断循环，如图中(B)所示。
- 在人类反馈过程中，人类可以添加新的解离后的样例到任务库当中，或者修改工具库中工具的使用。

![Pasted image 20230616111249](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230616111249.png)

#### A Survey of Large Language Models

> [!info]
> 
> [arXiv:2303.18223](https://arxiv.org/abs/2303.18223) [cs.CL]
> 
> [#Surveys](#Surveys)
> 
> Repository: https://github.com/RUCAIBox/LLMSurvey

[大型语言模型综述全新出炉：从T5到GPT-4最全盘点，国内20余位研究者联合撰写](https://mp.weixin.qq.com/s/7HRr55Md2Wl6EHQMGioumw)
[大语言模型综述](https://mp.weixin.qq.com/s/246ZZINu8SbxGGOraV4KAA)
[ChatGPT的朋友们：大语言模型经典论文一次读到吐](https://mp.weixin.qq.com/s/F7QQGuai-ezG0d-8w_Hkbg)

#### Harnessing the Power of LLMs in Practice: A Survey on ChatGPT and Beyond

> [!info]
> 
> [arXiv:2304.13712](https://arxiv.org/abs/2304.13712) [cs.CL]
> 
> [#Surveys](#Surveys)
> 
> Repository: https://github.com/Mooler0410/LLMsPracticalGuide




#### LLM-Pruner: On the Structural Pruning of Large Language Models

> [!info]
> 
> [arXiv:2305.11738](https://arxiv.org/abs/2305.11738) [cs.CL]
> 
> [#Model compression](#Model%20compression)
> 
> Repository: https://github.com/horseee/LLM-Pruner
> Article: [LLM-Pruner: 剪枝+少量数据+少量训练 = 高效的LLM压缩](https://mp.weixin.qq.com/s/feqFfy4n31eztoZfodMieQ)


#### CRITIC: Large Language Models Can Self-Correct with Tool-Interactive Critiquing

> [!info]
> 
> [arXiv:2305.11738](https://arxiv.org/abs/2305.11738) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: https://github.com/microsoft/ProphetNet/tree/master/CRITIC

> 大语言模型在推理的过程中，可能出现不一致或者有问题的行为，例如幻象、生成存在缺陷的代码、生成有害的内容等。作者模仿人类使用工具验证的行为，提出了CRITIC。语言模型根据问题生成出相关答案之后，可以和外部工具进行交互，使用适当的工具对生成的内容进行评估，根据得到的反馈对之前生成的内容进行修改。
> 
> 具体来说，大语言模型被作为一个黑箱，根据任务相关的输入得到一个原始输出。然后，和外部工具进行交互，包括知识图谱，代码解释器、搜索引擎等。通过和外部工具的交互，能够获得一系列的结果，大模型根据这些结果生成相关的反馈。最后，根据任务相关的输入、原始输出、与工具交互的结果等多方面的内容，大模型对原始输出进行修改，等到新的输出。改过程可以迭代进行，多次修正输出的内容。在实验方面，作者使用AmbigNQ、TriviaQA和HotpotQA三个问答数据集进行评测，CRITIC能够超越普通CoT和ReAct等方法，取得了良好的表现。

![600](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230617151528.png)

简单来说，还是设计prompt。让LLM有样学样，在输出结果后继续验证答案的合理性和准确性。验证时可以调用外部接口。

![2305.11738_Page_04](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/2305.11738_Page_04.png)

本文也对比了[ReAct](#%5BReAct%20Synergizing%20Reasoning%20and%20Acting%20in%20Language%20Models)的方法，证明了CRITIC比ReAct好不少。


#### Lion: Adversarial Distillation of Closed-Source Large Language Model

> [!info]
> 
> [arXiv:2305.12870](https://arxiv.org/abs/2305.12870) [cs.CL]
> 
> [#Model compression](#Model%20compression)
> 
> Repository: https://github.com/YJiangcm/Lion
> Article: [如何更好地蒸馏ChatGPT模型能力](https://mp.weixin.qq.com/s/_LQVHMJqPzMzIuM4wsO2Dw)


#### TRICE: Making Language Models Better Tool Learners with Execution Feedback

> [!info]
> 
> [arXiv:2305.13068](https://arxiv.org/abs/2305.13068) [cs.CL]
> 
> [#Tool learning](#Tool%20learning)
> 
> Repository: https://github.com/zjunlp/trice

> 前人的工作表明了AI系统可以利用工具增强自己的能力并和外界进行交互。但是如何引导模型正确使用工具，仍然是一个需要探究的问题。在这篇工作中，作者提出了TRICE。这是一个两阶段的端到端的框架，能够使语言模型通过工具执行的结果的反馈进行持续地学习，可以让模型高效地学习何时与如何使用工具。
> 
> 具体来说，训练过程的两个阶段分别为：Behavior Cloning和Reinforcement Learning with Execution Feedback (RLEF)。在Behavior Cloning阶段，模型根据任务输入，输出相关的工具的使用方案。在RLEF阶段，使用强化学习的框架，根据工具的结果设计奖励函数，对模型进行强化学习，继续增强模型使用工具的能力。在实验方面，作者使用Alpaca-7B作为基座模型，比较了Toolformer等基线方法，在数学相关任务上进行评测，证明了TRICE的有效性。

![Pasted image 20230617173042](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230617173042.png)


#### Domain Specialization as the Key to Make Large Language Models Disruptive: A Comprehensive Survey

| Name | Paper                                                                                                                                                                                                                            | Publication    | Repository | Link                                                 | Classification                                                                                                   |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
|      | [Domain Specialization as the Key to Make Large Language Models Disruptive: A Comprehensive Survey](#Domain%20Specialization%20as%20the%20Key%20to%20Make%20Large%20Language%20Models%20Disruptive%20A%20Comprehensive%20Survey) | arXiv preprint |            | [arXiv:2305.18703](https://arxiv.org/abs/2305.18703) | [Surveys](#Surveys) |

[大语言模型综述全新出炉：51页论文带你盘点LLM领域专业化技术](https://mp.weixin.qq.com/s/0DrowrTIgXsBhj3sYu6Aog)

![500](../../../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230723195947.png)

主要还是[In-context learning](#In-context%20learning)、[Prompting](#Prompting)和Fine-tuning的三板斧


#### Macaw-LLM: Multi-Modal Language Modeling with Image, Audio, Video, and Text Integration

| Name | Paper                                                                                                                                                                                                   | Publication    | Repository                                            | Link                                                 | Classification                                         |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------ |
|      | [Macaw-LLM: Multi-Modal Language Modeling with Image, Audio, Video, and Text Integration](#Macaw-LLM%20Multi-Modal%20Language%20Modeling%20with%20Image,%20Audio,%20Video,%20and%20Text%20Integration) | arXiv preprint | [Macaw-LLM](https://github.com/lyuchenyang/Macaw-LLM) | [arXiv:2306.09093](https://arxiv.org/abs/2306.09093) | [Models](#Models), [Multimodal LLM](#Multimodal%20LLM) |

[腾讯AI Lab发布多模态指令调优语言模型，支持图像、视频等四种不同模态](https://mp.weixin.qq.com/s/KMxJfqU1pGWbYq5XjbPAHQ)

提出了一种名为 Macaw-LLM 的多模态指令调优语言模型。它集成了图像、视频、音频和文本等四种不同的模态于一个模型之中

Macaw-LLM 包含以下三个主要模块：

- **模态模块**：现有的语言模型主要专注于处理文本信息。为了融合视觉和音频等额外的模态数据，我们在 Macaw-LLM 中集成了额外的模态编码器。这样的增强使得 Macaw-LLM 能够有效处理多个模态
- **对齐模块**：由于每个模态编码器是独立训练的，不同模态的学习表示可能不直接兼容。为了解决这个问题，我们提出了对齐模块，将不同模态的表示统一起来，实现多模态信息的有效整合
- **认知模块**：在 Macaw-LLM 中，我们利用预训练的语言模型作为我们的认知模块，它构成了 Macaw-LLM 的基础。值得注意的是，认知模块也充当了我们方法中的文本模态编码器

与以往方法不同，我们的方法将表示对齐和指令调优合并为一步，减少了多步微调过程中潜在的错误传播。此外，我们使用GPT-3.5-Turbo构建了一个大规模的多模态Macaw-LLM指令数据集

不可否认，我们的工作目前存在一些缺陷，例如仅支持单轮对话、缺乏对 Macaw-LLM 的全面评估，以及可能存在的幻觉（hallucination）问题。我们将在未来的工作中努力解决这些问题

#### A Survey on Multimodal Large Language Models

> [!info]
> 
> [arXiv:2306.13549](https://arxiv.org/abs/2306.13549) [cs.CV]
> 
> [#Surveys](#Surveys)
> 
> Repository: https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models
> Article: [中科大腾讯最新《多模态大型语言模型》综述，详述多模态指令微调、上下文学习、思维链和辅助视觉推理技术](https://mp.weixin.qq.com/s/G9x_RO08VVhsS8YTi55oHQ)

#### Kosmos-2: Grounding Multimodal Large Language Models to the World

> [!info]
> 
> [arXiv:2306.14824](https://arxiv.org/abs/2306.14824) [cs.CL]
> 
> [#Multimodal LLM](#Multimodal%20LLM)
> 
> Repository: https://aka.ms/kosmos-2
> Article: [微软发布「升级版」多模态大模型 Kosmos-2！新增局部理解能力，解锁实体级交互](https://mp.weixin.qq.com/s/wcItIWImWLwsYic4PvHwcQ)

这篇论文的主要创新和设计包括：

1. **KOSMOS-2模型**：这是一种新的多模态大型语言模型，可以理解对象描述（例如，边界框）并将文本与视觉世界联系起来。这是通过将引用表达式表示为Markdown中的链接，即"[text span](bounding%20boxes)"，其中对象描述是位置令牌的序列来实现的。
2. **GRIT数据集**：作者构建了一个大规模的有地理标记的图像-文本对数据集（称为GRIT）来训练模型。这个数据集包含了大量的有地理标记的图像和文本对，可以用来训练模型理解和生成与图像相关的文本。
3. **指令调整**：模型训练完成后，进行指令调整以使KOSMOS-2更好地与人类指令对齐。这是通过结合视觉-语言指令数据集（即LLaVA-Instruct）和语言-only指令数据集（即Unnatural Instructions和FLANv2）以及训练数据，对模型进行调整实现的。
4. **新的交互方法**：与以前的多模态大型语言模型不同，KOSMOS-2可以提供视觉答案（即，边界框）并将文本输出与图像关联起来。这为用户提供了一种新的交互方法。
5. **多模态地理标记任务**：作者在一系列任务上评估了KOSMOS-2，包括多模态地理标记，如引用表达理解和短语地理标记。
6. **多模态引用任务**：这些任务包括引用表达生成，这是一种新的任务，要求模型生成与特定图像区域相关的文本描述。

#### A Survey on Evaluation of Large Language Models

| Name | Paper                                                | Publication    | Repository                                                       | Link                                                 | Classification |
| ---- | ---------------------------------------------------- | -------------- | ---------------------------------------------------------------- | ---------------------------------------------------- | -------------- |
|      | [#A Survey on Evaluation of Large Language Models](#A%20Survey%20on%20Evaluation%20of%20Large%20Language%20Models) | arXiv preprint | [LLM-eval-survey](https://github.com/MLGroupJLU/LLM-eval-survey) | [arXiv:2307.03109](https://arxiv.org/abs/2307.03109) | [#Surveys](#Surveys)   | 





## Potential research topics

[LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/)
- 这是那个写了不少RL帖子的那个老哥（Lil'Log）写的
- 写了如何使用LLM作为智能体的相关内容
- 提到了人类的记忆模式、LLM的记忆模式设计、记忆的使用方法

[伦敦大学、MetaAI、StabilityAI联合发布70页综述，盘点大模型的16大挑战](https://mp.weixin.qq.com/s/JsCoUcuCg4ylKkPMvNouEw)
- 论文: [Challenges and Applications of Large Language Models](https://arxiv.org/abs/2307.10169)

## Appendices

- 模型的话有 GPT3篇，LLAMA, Chinchilla, Consitutional AI, FLAN-T5, T0, UL2
- LLM比较重要的一些概念 chain-of-thought, emergence ability of LLM, self-consistency
- 偏IR应用一点的有generate-than-retrieval, recitation-augmented 以及其他 retrieval-augmented LLMs
- survey可以看看HELM

https://liuyiding.notion.site/liuyiding/Recent-readling-list-LLM-8eab2d04d9c0484e90892401a17011d7





