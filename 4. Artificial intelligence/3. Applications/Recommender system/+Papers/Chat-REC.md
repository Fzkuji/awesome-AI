Title: Chat-REC: Towards Interactive and Explainable LLMs-Augmented Recommender System (Chat-REC: 基于LLMs的交互式和可解释推荐系统)

- Authors: Yunfan Gao, Tao Sheng, Youlin Xiang, Yun Xiong, Haofen Wang, and Jiawei Zhang
- Affiliation: Shanghai Key Laboratory of Data Science, School of Computer Science, Fudan University, Shanghai, China (作者所属机构：复旦大学数据科学学院计算机科学学院)
- Keywords: LLMs, Recommender System, Prompt Engineering
- URLs: [https://arxiv.org/abs/2303.14524](https://arxiv.org/abs/2303.14524) , GitHub: None

整篇文章就是将传统推荐系统和LLM模型合并使用，主要以工程为主。

# Abstract

1. 本文的研究背景：
大型语言模型（LLMs）在各种应用任务中展示出巨大的潜力。然而，传统推荐系统仍面临诸如交互性和可解释性较差等问题，这在很大程度上阻碍了它们在实际系统中的广泛应用。

2. 本文提出的研究方法：
为解决这些问题，本文提出了一种名为Chat-Rec（基于ChatGPT的增强推荐系统）的新方法，该方法通过将用户资料和历史互动转换为提示，创新地将LLMs应用于构建会话式推荐系统。通过上下文学习，Chat-Rec可以有效掌握用户喜好并在用户与产品间建立联系，使推荐过程更具交互性和可解释性。此外，在Chat-Rec框架内，用户的偏好可以应用于不同产品的跨领域推荐，而基于提示的信息注入到LLMs中还能应对新项目的冷启动问题。

3. 所使用方法所达到的任务和性能：
在实验中，Chat-Rec成功提高了Top-K推荐的效果，并在零次学习评分预测任务上表现优异。Chat-Rec为改进推荐系统提供了一种新颖途径，并为人工智能生成内容（AIGC）在推荐系统研究的实际应用场景带来新的可能性。

# Introduction

- a. 主题和特点：
	- Chat-Rec是一个增强的推荐系统，采用LLMs来提升推荐性能。
- b. 历史发展：
	- 之前的推荐系统在交互性和可解释性上存在缺陷，无法做到更好的个性化推荐。
- c. 过去的方法：
	- 过去的推荐系统采用的是传统方法，存在交互性差、无法针对冷启动等问题。
- d. 过去研究的缺点：
	- 过去的推荐系统无法增强推荐性能。
- e. 当前需要解决的问题：
	- 如何增强推荐系统的个性化推荐能力，提高交互性和可解释性。

随着模型和语料库规模的扩大，大型语言模型（LLMs）展示出了显著的能力，如复杂推理、知识推理和外部鲁棒性。这些能力被称为“突现能力”，只有在模型参数达到特定阈值后才显现出来。LLMs的出现引发了研究范式的转变。过去，将模型应用于下游任务通常需要通过反向传播调整模型参数。然而，最新的LLMs发展已经使研究人员和实践者能够通过构建提示实现在前向过程中的学习，即上下文学习（ICL）。此外，采用诸如Chain-of-Thought和Instruct Learning等技术，进一步发挥了LLMs的推理能力和任务泛化能力，促进了它们在各个领域的应用。

在大数据时代，手动搜索信息变得不切实际，推荐系统被广泛用于自动推断用户喜好并提供高质量的推荐服务。然而，由于模型设计和数据分布偏差等方面的局限性和缺陷，现有的推荐系统在现实世界中的部署仍然面临很大的挑战。主要限制包括交互性、可解释性差以及缺乏反馈机制。另一个限制是冷启动问题，为新项目和新用户提供准确推荐变得困难。此外，现有推荐系统在跨多个领域进行推荐时面临挑战。为了获取所需的背景或通用知识，很多推荐任务需要建立外部库或知识图谱进行检索，或者在扩展数据上进行多任务学习。LLMs为这些挑战提供了一个有前景的解决方案，可以生成更自然、更可解释的推荐，解决冷启动问题，并进行跨领域推荐。此外，LLMs具有更强的交互性和反馈机制，提高了整体用户体验。通过利用内部知识，LLMs可以在不依赖外部检索器的情况下提高推荐系统的性能。

将LLMs应用于推荐任务已经得到了一些初步的研究实验尝试。推荐系统任务被构建为基于提示的自然语言任务，其中将用户-项目信息和相应特征与个性化提示模板作为模型输入进行整合。然而，在当前的研究中，LLMs仍然作为模型的一部分参与训练。

在本文中，我们引入一种新颖的方法，通过LLMs增强会话式推荐系统的学习，具有交互性和可解释性。我们提出了一种名为Chat-Rec（基于ChatGPT的增强推荐系统）的范式，它不需要训练，而是完全依赖于上下文学习，从而实现更高效和有效的结果。通过LLM增强的推荐系统，可以在与用户的对话过程中学习用户的偏好。在对话的每一步之后，用户的偏好可以进一步细化，以更新候选推荐结果。此外，用户在不同产品之间的偏好可以相互关联，从而实现更好的跨领域产品推荐。我们在现实世界数据集上进行了推荐和评分测试，实验结果表明Chat-Rec取得了显著的改进。Chat-Rec为ChatGPT等会话AI在多个推荐场景中的应用提供了有前景的技术路径。

我们的贡献概括如下：

-   我们引入了一种名为Chat-Rec的新颖且有效的范式，通过提示将传统推荐系统与LLMs相结合，利用LLMs从上下文中学习的能力。
-   Chat-Rec将LLMs用作推荐系统界面，实现多轮推荐，提高交互性和可解释性。
-   我们在现实世界数据集上评估了我们的方法，针对Top-k推荐和评分预测任务进行了实验，结果表明Chat-Rec具有良好的效果。

# Related work

## Augmented language models

Augmented language models——增强语言模型（ALMs）是一种新的研究方向，旨在通过为传统语言模型（LMs）增加推理能力和使用外部工具的能力来克服其局限性。

这已经为数百万用户提供服务，如编码助手Copilot或基于GPT-3.5和GPT-4的ChatGPT。推理被定义为将复杂任务分解成简单的子任务，这样LM可以更容易地自行解决或在工具的帮助下解决，而工具是LM可以调用的外部模块，用以扩展其上下文。ALMs可以分别使用这些增强功能，也可以组合使用，以提高它们的上下文处理能力，并在多个基准测试中超越大多数普通LMs。ALMs可以学会推理、使用工具甚至采取行动，同时还能完成标准的自然语言任务。这个新的研究方向有潜力解决传统LMs普遍存在的可解释性、一致性和可扩展性问题。通过共同讨论推理和工具，以及工具和行动，ALMs可以在没有启发式的情况下解决广泛的复杂任务，从而提供更好的泛化能力。

## NLP for recommendation

[Recommender system](../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md)与[NLP](../../../1.%20Major%20goals/Intelligence/Natural%20language%20processing/Natural%20language%20processing.md)技术长期保持紧密关系，特别是在pre-trained language models (PLM)出现后，这些模型提高了推荐系统的性能和可解释性。

PLMs是在大型语料库上以自监督方式学习通用表示的语言模型，所学到的表示对于一系列下游NLP任务具有益处。

在推荐领域，PLMs可以帮助缓解数据稀疏问题，这是当前深度推荐模型的主要性能瓶颈。通过提取和转移不同PLM相关训练范式学到的预训练模型中的知识，研究人员旨在从多个角度提高推荐性能，如通用性、稀疏性、效率和有效性。在这个充满活力的领域，还有一些尚未探讨的问题和未来研究方向，包括基于PLM的训练范式与推荐系统不同输入数据类型之间的关联。

总体而言，将语言建模范式应用于推荐被认为是学术界和工业界都具有前景的发展方向。

## Cold-start recommendation

[](../../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md#^f00b70%7CCold%20start%20(recommender%20systems))是指在推荐系统中用户或物品没有与系统先前的互动记录时遇到的问题。这意味着系统没有可用的数据来进行个性化推荐。为了解决这个问题，有人提出了一些解决方案，比如学会建模内容特征，或者从其他领域转移表示。前一种方法侧重于根据内容（如文本、图像或元数据）了解物品或用户的特征。后一种方法则是利用来自其他领域的信息（如社交网络或产品描述）来推断用户偏好。另外，还有一些方法专注于快速适应新领域，而不仅仅是为冷启动场景提供推荐。在启动场景上，推荐模型的良好泛化能力对于确保更好的用户体验和提高用户参与度非常重要。在我们的工作中，我们利用大型语言模型的推理和背景知识来提高冷启动场景推荐系统的性能。

# Method

-a. 本研究的理论基础：
	- Chat-Rec模块是基于LLMs构建出来的，旨在提高推荐性能。
- b. 文章的技术路线（一步一步）：
	- Chat-Rec模块通过构建自然语言段落，捕捉用户查询和推荐信息，形成对话式推荐系统。
	- 该模块利用LLMs过滤和排序候选项，优化推荐结果，解决了冷启动和跨领域推荐的问题。

## Bridge recommender systems and LLMs

Recommender systems are designed to suggest items to users based on their preferences and behavior. Traditionally, these systems have relied on user data such as clickstream and purchase history to make recommendations. However, NLP techniques have proven to be valuable in expanding the scope of recommender systems beyond traditional user data.

NLP techniques can be used to analyze user-generated content such as reviews and social media posts to gain insights into user preferences and interests. LLMs can also be used to generate natural language responses to user queries, improving the overall user experience and engagement.

To bridge recommender systems and LLMs, we propose an enhanced recommender system module based on ChatGPT, a large language model trained by OpenAI. As the Fig. 1 shows, the module takes as input user-item history interactions, user profile, user query $Q_i$, and history of dialogue $H_{<i}$ (if available, and the notation $<i$ denotes the dialogue history prior to the current query), and interfaces with any recommender system $R$. If the task is determined to be a recommendation task, the module uses $\mathrm{R}$ to generate a candidate set of items. Otherwise, it directly outputs a response to the user, such as an explanation of a generation task or a request for item details.

The prompt constructor module in the enhanced recommender system takes multiple inputs to generate a natural language paragraph that captures the user's query and recommendation information. The inputs are as follows:

- User-item history interactions, which refers to the user's past interactions with items, such as items they have clicked, purchased, or rated. This information is used to understand the user's preferences and to personalize the recommendation.
- User profile, which contains demographic and preference information about the user. This may include age, gender, location, and interests. The user profile helps the system understand the user's characteristics and preferences.
- User query $Q_i$, which is the user's specific request for information or recommendation. This may include a specific item or genre they are interested in, or a more general request for recommendations in a particular category.
- History of dialogue $H_{<i}$, which contains the previous conversation between the user and the system. This information is used to understand the context of the user's query and to provide a more personalized and relevant response.

As shown in Fig. 2, the Chat-Rec framework proposed in this paper empower recommender systems with the conversational interface, which makes the interactive and explainable recommendation possible. Formally, based on the aforementioned inputs, the prompt constructor module generates a natural language paragraph that summarizes the user's query and recommendation information, and provides a more personalized and relevant response to the user's request. The intermediate answer generated by the recommender system is then used to refine the prompt constructor and generate an optimized prompt to further compress and refine the candidate set. The resulting recommendation and a brief explanation are output to the user.

For example, in the first round of Q\&A, the user requests action movies. The system determines that a recommendation task is needed, and executes the Recommendate Action Movies module using the input information. The intermediate answer $A_1$ contains the top-20 results, which are then reranked and adjusted in the second module using the input information to generate the final output of the top-5 results.

In the second round of Q\&A, the user asks why the movie "Fargo" was recommended. The system determines that no recommendation task is needed and instead executes the explanation for the recommendation module, using the movie title, history interaction, and user profile as inputs. The answer $A_2$ is then generated, which provides a brief explanation of the recommendation, including information about the user's general interests and the specific characteristics of the movie that may be appealing to the user.

![Fig 1](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835050.png)
Fig. 1: Overview of Chat-Rec. The left side shows a dialogue between a user and ChatGPT. The middle side shows the flowchart to how Chat-Rec links traditional recommender systems with conversational AI such as ChatGPT. The right side describes the specific judgment in the process.

## Recommendation based on candidate set compression

传统的推荐系统通常生成一小部分排序的候选产品，每个产品都有一个分数，反映了系统的推荐信心或结果质量。然而，考虑到产品集的庞大规模，大多数现有推荐系统的性能远远达不到令人满意的程度，仍有很大的改进空间。

![Fig 2](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835086.png) ^9c41ea

本文提出了一种使用LLMs来通过缩小候选集来提高推荐系统性能的方法。推荐系统生成大量候选物品，这可能会让用户感到不知所措。LLMs在缩小系统内产品候选集方面发挥了几个不同的关键作用。

首先，我们将用户的配置文件和历史互动转换为提示，包括物品描述和用户评分。其次，要求LLMs根据上述信息总结用户对特定领域物品的偏好。LLMs可以从上下文中学习并有效捕捉用户的背景信息和偏好。有了这些信息，它们可以建立产品属性和用户偏好之间的关系，使得它们能够提供更好的产品推荐。通过利用上下文学习，LLMs可以增强其推荐推理能力，从而提供更准确和个性化的产品推荐。

一旦LLMs了解了用户的偏好，就可以将由推荐系统生成的候选集提供给LLMs。LLMs可以根据用户的偏好进一步筛选和排序候选集。这种方法确保了用户能看到一个更小、更相关的物品集，从而增加了他们找到喜欢的东西的可能性。

## Cold-start recommendations

通过对产品的文本描述和概况信息，无论是新产品还是旧产品，LLMs可以有效地将这些产品相互关联，为我们提供了一劳永逸解决冷启动推荐问题的机会。

例如，如果一个用户询问2021年上映的新电影推荐，推荐系统可以使用关于电影的文本数据生成一个嵌入，然后计算与系统中其他电影的相似度来进行推荐。这种能力使得推荐系统能够为新项目提供相关和准确的推荐，从而提高整体用户体验。

![Fig 3](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835132.png)
Fig. 3: Case Study of New Item Recommendation. The top shows that ChatGPT is unable to recommend new items beyond the timeframe of its training data. The middle part demonstrates the process of how to utilize external information about new items to enable ChatGPT to handle recommendations for new items. The bottom shows that ChatGPT can effectively handle recommendations for new items after incorporating external information.


大型语言模型可以利用其包含的大量知识来帮助推荐系统缓解新项目的冷启动问题，即推荐缺乏大量用户互动的项目。然而，由于ChatGPT所掌握的知识仅限于2021年9月，因此当遇到未知项目时，ChatGPT的应对并不理想，例如用户要求推荐2023年上映的新电影，或者与ChatGPT不了解的电影相关的内容，如图3的上半部分所示。为了解决这个问题，我们引入了关于新项目的外部信息，利用大型语言模型生成相应的嵌入表示并将其缓存。当遇到新项目推荐时，我们计算项目嵌入与用户请求和偏好嵌入之间的相似性，然后根据相似性检索最相关的项目信息，并构建一个提示输入到ChatGPT中进行推荐，如图3的下半部分所示。这种方法使得推荐系统可以与ChatGPT更好地配合推荐新项目，从而提高用户体验。

## Cross-domain recommendations

![Fig 4](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835147.png)

上面介绍的增强型LLM推荐系统可以用来解决一些传统推荐系统难以解决的挑战性任务，比如跨领域推荐和冷启动推荐。在这部分，我们将讨论如何使用增强型LLM推荐系统进行跨领域推荐。

实际上，用互联网信息预先训练过的LLM可以作为多角度知识库。除了一个领域的目标产品，比如电影，LLM不仅对其他领域的产品，如音乐和书籍有广泛的了解，还能理解上述领域中产品之间的关系。比如，在图4中展示的，关于电影推荐的对话结束后，用户询问LLM关于其他类型作品的建议。然后，LLM根据用户的电影喜好推荐各种选择，如书籍、电视剧、播客和电子游戏。这表明LLM能够将用户的喜好从电影转移到其他项目，实现跨领域推荐。这种跨领域推荐能力有可能大大扩展推荐系统的范围和相关性。

# Experiment

## Dataset and experimental settings

Dataset: MovieLens 100K

Metrics: When evaluating the performance of top-k recommendations, Precision, Recall, and Normalized Discounted Cumulative Gain (NDCG) are used. For rating prediction task, the Root Mean Squared Error (RMSE) and Mean Absolute Error (MAE) are employed as evaluation metrics.

## Baselines

Detailed information about the comparison methods studied in our experiments are provided as follows:

- **LightFM** is a recommendation algorithm that combines collaborative filtering and content-based methods to recommend items to users. 
- **LightGCN** is a graph-based collaborative filtering algorithm that uses a simplified graph convolutional network (GCN) to model the user-item interactions in a recommender system. 
- **Item-KNN** is a neighborhood-based collaborative filtering algorithm that uses the similarity between items to make recommendations to users. 
- **Matrix Factorization (MF)** is a widely used collaborative filtering algorithm that represents users and items as latent factors in a low-dimensional space.

We select three representative models from the GPT-3 and GPT-3.5 series as LLMs in Chat-Rec:

- **gpt-3.5-turbo** is the most capable GPT-3.5 model and optimized for chat. 
- **text-davinci-003** can do any language task with better quality, longer output, and consistent instruction-following. 
- **text-davinci-002** is similar to text-davinci-003 but is trained with supervised fine-tuning instead of reinforcement learning.

## Result and analysis

### Top-5 recommendation

Table 2: Results of top-5 recommendation.
$$
\begin{array}{|c|c|c|c|}
\hline \text { Models } & \text { Precision } & \text { Recall } & \text { NDCG } \\
\hline \text { LightFM } & 0.2830 & 0.1410 & 0.2846 \\
\text { LightGCN } & 0.3030 & \mathbf{0 . 1 4 5 5} & 0.3425 \\
\text { CHAT-REC (gpt-3.5-turbo) } & 0.3103 & 0.1279 & 0.3696 \\
\text { CHAT-REC (text-davinci-003) } & \mathbf{0 . 3 2 4 0}(+6.93 \%) & 0.1404(-3.51 \%) & \mathbf{0 . 3 8 0 2}(+11.01 \%) \\
\text { CHAT-REC (text-davinci-002) } & 0.3031 & 0.1240 & 0.3629 \\
\hline
\end{array}
$$

### Rating prediction

Table 3: Results of movie rating prediction.
$$
\begin{array}{|c|c|c|}
\hline \text { Models } & \text { RMSE } & \text { MAE } \\
\hline \text { MF } & 0.988 & 0.771 \\
\text { Item-KNN } & 0.933 & 0.734 \\
\text { CHAT-REC (gpt-3.5-turbo) } & 0.969 & 0.756 \\
\text { CHAT-REC (text-davinci-003) } & \mathbf{0 . 7 8 5} & \mathbf{0 . 5 9 3} \\
\text { CHAT-REC (text-davinci-002) } & 0.8309 & 0.6215 \\
\hline
\end{array}
$$

## Ablation study

![Fig 5](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835159.png)
Fig. 5: Performance on different prompt and temperature.


# Conclusion

- a. 工作的重要性：
	- Chat-Rec模块提高了推荐系统的性能，在实现 AI 生成的内容推荐系统研究方面提供了新的实用场景。
- b. 创新、性能和负载情况：
	- Chat-Rec模块采用LLMs构建出更具针对性和可解释性的推荐系统，拓展了推荐系统的使用范围。
- c. 研究结论（列出要点）：
	- Chat-Rec模块运用LLMs在Top-K推荐和零射击电影评级任务中表现更为优异，大大提高了推荐系统的性能。同时，该研究证明了提示设计对推荐性能的重要影响，暗示推荐系统中的隐含知识可以进一步提高推荐效果。总的来说，LLMs是提高推荐系统性能、提高交互性和可解释性的重要手段。

# Implementation details

## Prompts

![800](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835179.png)
Fig. 6: Prompt for top-k recommendation task.

![800](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Chat-REC/IMG-20240214165835200.png)
Fig. 7: Prompt for moving rating task.

## Example Answers

Table 4: Some cases and explanations that failed to generate canonical answers

| Example                                                                                                                                                                                   | Explanation                                                                                                                          | Correct      |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------ |
| ... The current list is: 1.Toy Story (1995) 2.Fargo (1996) 3.Die Hard (1988) 4.Fish Called Wanda, A (1988) 5. Wrong Trousers, The (1993)                                                  | The output conforms to the formatting requirements                                                                                   | $\checkmark$ |
| ... The current list is: 1.The Shawshank Redemption (1994) (It should be “Shawshank Redemption, The(1994)”) 2.A Fish Called Wanda (1988) (It should be “Fish Called Wanda, A (1988)”) ... | Failure to output film names in accordance with film industry norms. such as “A” and “The” are not in the right place.               | ✘            |
| ... The current list is: 1.Toy Story (1995) 2.Groundhog Day (1993) 3.Star Trek: The Wrath of Khan (1982) 4.Fargo (1996)                                                                   | Sometimes it can’t output a sufficient number of movies. In this case, it only output 4 movies while sometimes may output 19 movies. | ✘            |
| ... The current list is: a:Star Wars (1977) a:Raiders of the Lost Ark (1981) n:Back to the Future (1985) m:Fargo (1996) ...                                                               | Sometimes the id information is lost when LLM is asked to output movies in the following format [id]:[name].                         | ✘            | 



