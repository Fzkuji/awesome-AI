

# Related Repositories

|                                                        Repo Name                                                         |                 Maintainer                  |
|:------------------------------------------------------------------------------------------------------------------------:|:-------------------------------------------:|
|                            [rs-llm-paper-list](https://github.com/wwliu555/rs-llm-paper-list)                            |   [wwliu555](https://github.com/wwliu555)   |
| [awesome-recommend-system-pretraining-papers](https://github.com/archersama/awesome-recommend-system-pretraining-papers) | [archersama](https://github.com/archersama) |
|                                        [LLM4Rec](https://github.com/WLiK/LLM4Rec)                                        |       [WLiK](https://github.com/WLiK)       |
|                       [Awesome-LLM4RS-Papers](https://github.com/nancheng58/Awesome-LLM4RS-Papers)                       | [nancheng58](https://github.com/nancheng58) |
|                               [Awesome-recsys](https://github.com/creyesp/Awesome-recsys)                                |    [creyesp](https://github.com/creyesp)    |
|                   [Awesome-Generative-RecSys](https://github.com/jihoo-kim/Awesome-Generative-RecSys)                    |  [jihoo-kim](https://github.com/jihoo-kim)  |


# Tasks

## Overview

首先考虑模型的**数据**，推荐系统的数据是时序、多模态的。

- 对于用户有两个历史记录：
	- 用户个人背景
	- 用户使用此平台的操作历史

推荐系统的**任务**就是根据某一时间点之前的信息，向用户推荐新的内容。

- 对于模型需要输出：
	- 详细具体的推荐内容
	- 具有说服力的解释

推荐系统评价指标通常较难获取，但是用户的反馈却比其他任务更容易获得。

- 损失函数明确的任务
	- 序列推荐
	- 评分预测
- 损失函数不明确的任务
	- 解释生成

输入数据的多少并不是划分推荐任务的关键因素。输出结果的粒度十分重要。

Ideally, given user $u_i$, the platform will get his background $b_i$ and browsing history $h_i$. For any content $c_i$ from the recommender system, user $u_i$ has a certain like/interest level. This level can be expressed as a value in the range 0 to 1. The different values of different contents indicate how high or low the user evaluates it. The final recommendation system will make recommendations based on user preferences.

以上是最理想的推荐系统，如果要求降低，用户对一个内容的态度可以简化为用户评分，即**评分预测**任务；如果不直接对每一个内容进行打分，直接选出用户最喜欢的几项，则可以得到**序列推荐**任务；如果同时还不考虑时序数据包含的信息，则任务变成了**直接推荐**。每一个任务都包含了推荐系统做出的妥协。

在将LLM应用到推荐系统中的时候，LLM的作用也有不同的可能性。

## Classification by Liu

> Is ChatGPT a Good Recommender? A Preliminary Study

### Accuracy-based tasks

- **评分预测（Rating Prediction）**：评分预测旨在预测用户对特定项目的评分，如上图所示，黑字部分代表任务的描述，评分预测被翻译为“How will user rate this product_title?”，灰字表示当前的输入，即要求用户评分的项目，红字表示针对输出的格式要求，在评分预测任务中，要求有“1 being lowest and 5 being highest”和“Just give me back the exact number a result”；
- **序列推荐（Sequential Recommendation）**：序列推荐任务要求系统根据用户过去的序贯行为预测其之后的行为，如上图所示，论文为该任务设计了三种 Prompt 格式，分别是基于交互历史直接预测用户的下一个行为，从候选列表中选出可能的下一个行为以及判断指定行为成为用户下一个行为的可能性；
- **直接推荐（Direct Recommendation）**：直接推荐指通过利用用户评分或评论信息直接显示反馈推荐的任务，论文将这一任务的 Prompt 设计为从潜在的候选项中选择出最适合的一项；
	- News Recommendation
	- Group Recommendation
	- Movie Recommendation

### Explainability-oriented tasks

- **解释生成**：解释生成是为用户提供解释以澄清为什么会推荐此项的推荐系统任务，具体地，如上图所示，论文要求 ChatGPT 生成一个文本解释，以阐明解释生成过程，对每个类别，可以包含如提示词或星级评分等的辅助信息；
- **评论总结**：旨在使用推荐系统自动生成用户评论摘要。通过输入用户的评论信息，Prompt 提示推荐系统总结评论的主要含义。

# Paradigms

## Classification by Wu

> A Survey on Large Language Models for Recommendation

在将LLM应用到推荐系统中的时候，LLM的作用也有不同的可能性/位置/角色。目前根据LLM的作用，可以将LLM for Rec中LLM的作用分类为：

- Embedding ^50520b
	- 用LLM做特征提取，再使用推荐系统进行推荐
- Token generation ^baac63
	- 用LLM提取文本的重点信息，但仍然输出文本，最后使用推荐系统利用这些文本进行推荐
- Recommendation ^2b9b61
	- 直接让LLM给出推荐结果


# Papers


## Surveys

| Paper                                                                                                                                                              | Publication | Year        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- | ----------- |
| [Self-Supervised Learning for Recommender Systems: A Survey](https://arxiv.org/abs/2203.15876)                                                                     | arXiv       | 29 Mar 2022 |
| [Pre-train, Prompt and Recommendation: A Comprehensive Survey of Language Modelling Paradigm Adaptations in Recommender Systems](https://arxiv.org/abs/2302.03735) | arXiv       | 7 Feb 2023  |
| [How Can Recommender Systems Benefit from Large Language Models: A Survey](https://arxiv.org/abs/2306.05817)                                                       | arXiv       | 9 Jun 2023  |


| Name | Paper                                                     | Publication    | Repository | Link                                                 | Classification |
| ---- | --------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------- |
|      | [A Survey on Large Language Models for Recommendation](#A%20Survey%20on%20Large%20Language%20Models%20for%20Recommendation) | arXiv preprint |            | [arXiv:2305.19860](https://arxiv.org/abs/2305.19860) | [#Surveys](#Surveys)   | 


## Paradigms of LLM4Rec

本笔记将推荐系统划分成3个部分：内容创作者、平台和用户。内容创作者为平台提供可推荐的内容，平台通过推荐系统将内容推送给潜在用户。

### Content creator oriented

创作者端，包括多媒体内容创作者、商家商家，甚至在最新的研究中，创作者也可以是平台本身。

目前面向内容创作者的LLMs主要用于内容的生成和打磨（[#Content generation](#Content%20generation)），同时也会涉及一些[#LLM using tools](#LLM%20using%20tools)。

#### Content generation

| Name    | Paper                                                                                          | Publication    | Repository                                      | Link                                                 |
| ------- | ---------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- |
| GenRead | [#Generate rather than Retrieve Large Language Models are Strong Context Generators](#Generate%20rather%20than%20Retrieve%20Large%20Language%20Models%20are%20Strong%20Context%20Generators) | ICLR 2023      | [GenRead](https://github.com/wyu97/GenRead)     | [arXiv:2209.10063](https://arxiv.org/abs/2209.10063) |
| GeneRec | [#Generative Recommendation Towards Next-generation Recommender Paradigm](#Generative%20Recommendation%20Towards%20Next-generation%20Recommender%20Paradigm)                    | arXiv preprint | [GeneRec](https://github.com/Linxyhaha/GeneRec) | [arXiv:2304.03516](https://arxiv.org/abs/2304.03516) |
| GENRE   | [#A First Look at LLM-Powered Generative News Recommendation](#A%20First%20Look%20at%20LLM-Powered%20Generative%20News%20Recommendation)                                | arXiv preprint |                                                 | [arXiv:2305.06566](https://arxiv.org/abs/2305.06566) | 


### User oriented

用户端，从用户角度预判用户的操作和评价，同时

#### Query generation

| Name | Paper                                                                               | Publication    | Repository | Link                                                 |
| ---- | ----------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
| GPT4Rec  | [#GPT4Rec A Generative Framework for Personalized Recommendation and User Interests Interpretation](#GPT4Rec%20A%20Generative%20Framework%20for%20Personalized%20Recommendation%20and%20User%20Interests%20Interpretation) | arXiv preprint |            | [arXiv:2304.03879](https://arxiv.org/abs/2304.03879) |



### Platform oriented
平台端



#### Information filtering

or recommendation

![Pasted image 20230629220945](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230629220945.png)

##### Divided by main body

[A Survey on Large Language Models for Recommendation](https://arxiv.org/abs/2305.19860)中提到了这种分类方法，将LLM4Rec分为Discriminative LLM4Rec和Generative LLM4Rec，他们的区别在于是否让LLM进行推荐，也就是推荐的主题是谁的问题。

![500](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230613222453.png)

目前的趋势来看，还是让LLM直接做推荐符合潮流，并且这样也免去了还要单独提供一个推荐系统的麻烦。

###### LLM as RS

> [Wu](https://arxiv.org/abs/2305.19860)称其为Generative LLM4Rec，指以GPT为首的，用解码器的LLM。这类模型主要用于解决Token generation和Recommendation问题，也就是GPT可以特征总结，也可以直接做推荐。

本笔记中范围扩大，只要将LLM直接用于推荐的均为LLM as RS，目前记录的大部分工作属于这一类。

| Name | Paper                                                                                         | Publication    | Repository | Link                                                 |
| ---- | --------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
| [DSI](+Papers/DSI.md)      | [#Transformer Memory as a Differentiable Search Index](#Transformer%20Memory%20as%20a%20Differentiable%20Search%20Index)                                                   | NeurIPS 2022 |                                                                           | [arXiv:2202.06991](https://arxiv.org/abs/2202.06991) |



###### LLM-augmented RS


> [Wu](https://arxiv.org/abs/2305.19860)称其为Discriminative LLM4Rec，指以BERT为首的、用编码器的LLM，主要用于Embedding特征提取，输出的结果再传递给下游模型。这类模型主要用于解决Embedding问题，也就是不直接做推荐。

本笔记中范围扩大，只要将LLM直接用于增强现有的推荐系统的，均为LLM-augmented RS

| Name | Paper                                                                                         | Publication    | Repository | Link                                                 |
| ---- | --------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
| KAR  | [#Towards Open-World Recommendation with Knowledge Augmentation from Large Language Models](#Towards%20Open-World%20Recommendation%20with%20Knowledge%20Augmentation%20from%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2306.10933](https://arxiv.org/abs/2306.10933) |




##### Divided by timing


###### Direct recommendation
不考虑时间维度，直接通过用户提供的查询进行推荐

| Name    | Paper                                                    | Publication    | Repository                                                          | Link                                                 |
| ------- | -------------------------------------------------------- | -------------- | ------------------------------------------------------------------- | ---------------------------------------------------- |
| [DSI](+Papers/DSI.md) | [#Transformer Memory as a Differentiable Search Index](#Transformer%20Memory%20as%20a%20Differentiable%20Search%20Index) | NeurIPS 2022   | [DSI-transformers](https://github.com/ArvinZhuang/DSI-transformers) | [arXiv:2202.06991](https://arxiv.org/abs/2202.06991) |
|         | [#Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study)  | arXiv preprint |                                                                     | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) |
| KAR  | [#Towards Open-World Recommendation with Knowledge Augmentation from Large Language Models](#Towards%20Open-World%20Recommendation%20with%20Knowledge%20Augmentation%20from%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2306.10933](https://arxiv.org/abs/2306.10933) |



###### Sequential recommendation
加入时间维度，考虑用户的历史进行推荐

| Name        | Paper                                                                                                      | Publication    | Repository                                                                | Link                                                 |
| ----------- | ---------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------- | ---------------------------------------------------- |
| NIR         | [#Zero-Shot Next-Item Recommendation using Large Pretrained Language Models](#Zero-Shot%20Next-Item%20Recommendation%20using%20Large%20Pretrained%20Language%20Models)                             | arXiv preprint | [LLM-Next-Item-Rec](https://github.com/AGI-Edgerunners/LLM-Next-Item-Rec) | [arXiv:2304.03153](https://arxiv.org/abs/2304.03153) |
|             | [#Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study)                                                    | arXiv preprint |                                                                           | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) |
| [TALLRec](+Papers/TALLRec.md) | [#TALLRec An Effective and Efficient Tuning Framework to Align Large Language Model with Recommendation](#TALLRec%20An%20Effective%20and%20Efficient%20Tuning%20Framework%20to%20Align%20Large%20Language%20Model%20with%20Recommendation) | arXiv preprint | [TALLRec](https://github.com/SAI990323/TALLRec)                           | [arXiv:2305.00447](https://arxiv.org/abs/2305.00447) |
| [TIGER](+Papers/TIGER.md)   | [#Recommender Systems with Generative Retrieval](#Recommender%20Systems%20with%20Generative%20Retrieval)                                                         | arXiv preprint |                                                                           | [arXiv:2305.05065](https://arxiv.org/abs/2305.05065) |
|             | [#How to Index Item IDs for Recommendation Foundation Models](#How%20to%20Index%20Item%20IDs%20for%20Recommendation%20Foundation%20Models)                                            | arXiv preprint |                                                                           | [arXiv:2305.06569](https://arxiv.org/abs/2305.06569) |
|             | [#Recommendation as Instruction Following A Large Language Model Empowered Recommendation Approach](#Recommendation%20as%20Instruction%20Following%20A%20Large%20Language%20Model%20Empowered%20Recommendation%20Approach)      | arXiv preprint |                                                                           | [arXiv:2305.07001](https://arxiv.org/abs/2305.07001) |
| LLMRank     | [#Large Language Models are Zero-Shot Rankers for Recommender Systems](#Large%20Language%20Models%20are%20Zero-Shot%20Rankers%20for%20Recommender%20Systems)                                   | arXiv preprint | [LLMRank](https://github.com/RUCAIBox/LLMRank)                            | [arXiv:2305.08845](https://arxiv.org/abs/2305.08845) |
| GPTRec      | [#Generative Sequential Recommendation with GPTRec](#Generative%20Sequential%20Recommendation%20with%20GPTRec)                                                      | arXiv preprint |                                                                           | [arXiv:2306.11114](https://arxiv.org/abs/2306.11114) |



#### Information retrieval

| Name  | Paper                                                                 | Publication    | Repository | Link                                                 |
| ----- | --------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|       | [CTRL Connect Tabular and Language Model for CTR Prediction](#CTRL%20Connect%20Tabular%20and%20Language%20Model%20for%20CTR%20Prediction)       | arXiv preprint |            | [arXiv:2306.02841](https://arxiv.org/abs/2306.02841) |
| Atlas | [Atlas Few-shot Learning with Retrieval Augmented Language Models](#Atlas%20Few-shot%20Learning%20with%20Retrieval%20Augmented%20Language%20Models) | arXiv preprint |            | [arXiv:2208.03299](https://arxiv.org/abs/2208.03299) |





#### Ranking


| Name         | Paper                                                                               | Publication    | Repository                                     | Link                                                 |
| ------------ | ----------------------------------------------------------------------------------- | -------------- | ---------------------------------------------- | ---------------------------------------------------- |
| [DSI](+Papers/DSI.md)      | [Transformer Memory as a Differentiable Search Index](#Transformer%20Memory%20as%20a%20Differentiable%20Search%20Index)                                                   | NeurIPS 2022 |                                                                           | [arXiv:2202.06991](https://arxiv.org/abs/2202.06991) |
| [Chat-REC](+Papers/Chat-REC.md) | [Chat-REC Towards Interactive and Explainable LLMs-Augmented Recommender System](#Chat-REC%20Towards%20Interactive%20and%20Explainable%20LLMs-Augmented%20Recommender%20System) | arXiv preprint |                                                | [arXiv:2303.14524](https://arxiv.org/abs/2303.14524) |
| LLMRank      | [Large Language Models are Zero-Shot Rankers for Recommender Systems](#Large%20Language%20Models%20are%20Zero-Shot%20Rankers%20for%20Recommender%20Systems)            | arXiv preprint | [LLMRank](https://github.com/RUCAIBox/LLMRank) | [arXiv:2305.08845](https://arxiv.org/abs/2305.08845) |



#### Rating prediction

| Name | Paper                                                   | Publication    | Repository | Link                                                 |
| ---- | ------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study) | arXiv preprint |            | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) |
|      | [Do LLMs Understand User Preferences? Evaluating LLMs On User Rating Prediction](#Do%20LLMs%20Understand%20User%20Preferences?%20Evaluating%20LLMs%20On%20User%20Rating%20Prediction) | arXiv preprint |            | [arXiv:2305.06474](https://arxiv.org/abs/2305.06474) |


#### CTR prediction

Click-through rate (CTR) prediction

| Name | Paper                                                           | Publication    | Repository | Link                                                 |
| ---- | --------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [CTRL Connect Tabular and Language Model for CTR Prediction](#CTRL%20Connect%20Tabular%20and%20Language%20Model%20for%20CTR%20Prediction) | arXiv preprint |            | [arXiv:2306.02841](https://arxiv.org/abs/2306.02841) | 
| KAR  | [Towards Open-World Recommendation with Knowledge Augmentation from Large Language Models](#Towards%20Open-World%20Recommendation%20with%20Knowledge%20Augmentation%20from%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2306.10933](https://arxiv.org/abs/2306.10933) |



#### ID generation

| Name      | Paper                                                           | Publication    | Repository                                                          | Link                                                 |
| --------- | --------------------------------------------------------------- | -------------- | ------------------------------------------------------------------- | ---------------------------------------------------- |
| [DSI](+Papers/DSI.md)   | [Transformer Memory as a Differentiable Search Index](#Transformer%20Memory%20as%20a%20Differentiable%20Search%20Index)        | NeurIPS 2022   | [DSI-transformers](https://github.com/ArvinZhuang/DSI-transformers) | [arXiv:2202.06991](https://arxiv.org/abs/2202.06991) |
| [TIGER](+Papers/TIGER.md) | [Recommender Systems with Generative Retrieval](#Recommender%20Systems%20with%20Generative%20Retrieval)              | arXiv preprint |                                                                     | [arXiv:2305.05065](https://arxiv.org/abs/2305.05065) |
|           | [How to Index Item IDs for Recommendation Foundation Models](#How%20to%20Index%20Item%20IDs%20for%20Recommendation%20Foundation%20Models) | arXiv preprint |                                                                     | [arXiv:2305.06569](https://arxiv.org/abs/2305.06569) |





#### Explanation generation

| Name | Paper                                                   | Publication    | Repository | Link                                                 |
| ---- | ------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study) | arXiv preprint |            | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) |


#### Review summarization

| Name | Paper                                                   | Publication    | Repository | Link                                                 |
| ---- | ------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study) | arXiv preprint |            | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) |

## Training of LLM4Rec

### Data augmentation





### Tuning

| Name        | Paper                                                                                                      | Publication    | Repository                                      | Link                                                 |
| ----------- | ---------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- |
| [TALLRec](+Papers/TALLRec.md) | [TALLRec An Effective and Efficient Tuning Framework to Align Large Language Model with Recommendation](#TALLRec%20An%20Effective%20and%20Efficient%20Tuning%20Framework%20to%20Align%20Large%20Language%20Model%20with%20Recommendation) | arXiv preprint | [TALLRec](https://github.com/SAI990323/TALLRec) | [arXiv:2305.00447](https://arxiv.org/abs/2305.00447) |
|      | [Web Content Filtering through knowledge distillation of Large Language Models](#Web%20Content%20Filtering%20through%20knowledge%20distillation%20of%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2305.05027](https://arxiv.org/abs/2305.05027) |
|             | [Recommendation as Instruction Following A Large Language Model Empowered Recommendation Approach](#Recommendation%20as%20Instruction%20Following%20A%20Large%20Language%20Model%20Empowered%20Recommendation%20Approach)      | arXiv preprint |                                                                           | [arXiv:2305.07001](https://arxiv.org/abs/2305.07001) | 



### Model compression

| Name | Paper                                                                              | Publication    | Repository | Link                                                 |
| ---- | ---------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [Web Content Filtering through knowledge distillation of Large Language Models](#Web%20Content%20Filtering%20through%20knowledge%20distillation%20of%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2305.05027](https://arxiv.org/abs/2305.05027) |


## Benchmarks

| Name   | Paper                                                         | Publication    | Repository                                      | Link                                                 |
| ------ | ------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- |
| OpenP5 | [OpenP5 Benchmarking Foundation Models for Recommendation](#OpenP5%20Benchmarking%20Foundation%20Models%20for%20Recommendation) | arXiv preprint | [OpenP5](https://github.com/agiresearch/OpenP5) | [arXiv:2306.11134](https://arxiv.org/abs/2306.11134) | 





## Evaluation of LLM4Rec

### Performance

| Name   | Paper                                                                                                    | Publication    | Repository                                           | Link                                                 |
| ------ | -------------------------------------------------------------------------------------------------------- | -------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| iEvaLM | [Rethinking the Evaluation for Conversational Recommendation in the Era of Large Language Models](#Rethinking%20the%20Evaluation%20for%20Conversational%20Recommendation%20in%20the%20Era%20of%20Large%20Language%20Models)     | arXiv preprint | [iEvaLM-CRS](https://github.com/RUCAIBox/iEvaLM-CRS) | [arXiv:2305.13112](https://arxiv.org/abs/2305.13112) |
|        | [A Preliminary Study of ChatGPT on News Recommendation Personalization, Provider Fairness, Fake News](#A%20Preliminary%20Study%20of%20ChatGPT%20on%20News%20Recommendation%20Personalization,%20Provider%20Fairness,%20Fake%20News) | arXiv preprint |                                                      | [arXiv:2306.10702](https://arxiv.org/abs/2306.10702) | 



### Fairness

| Name    | Paper                                                                                               | Publication    | Repository                                        | Link                                                 |
| ------- | --------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------- | ---------------------------------------------------- |
| FaiRLLM | [Is ChatGPT Fair for Recommendation? Evaluating Fairness in Large Language Model Recommendation](#Is%20ChatGPT%20Fair%20for%20Recommendation?%20Evaluating%20Fairness%20in%20Large%20Language%20Model%20Recommendation) | arXiv preprint | [FaiRLLM](https://github.com/jizhi-zhang/FaiRLLM) | [arXiv:2305.07609](https://arxiv.org/abs/2305.07609) | 
|        | [A Preliminary Study of ChatGPT on News Recommendation Personalization, Provider Fairness, Fake News](#A%20Preliminary%20Study%20of%20ChatGPT%20on%20News%20Recommendation%20Personalization,%20Provider%20Fairness,%20Fake%20News) | arXiv preprint |                                                      | [arXiv:2306.10702](https://arxiv.org/abs/2306.10702) | 



### Authenticity

| Name    | Paper                                                                                               | Publication    | Repository                                        | Link                                                 |
| ------- | --------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------- | ---------------------------------------------------- |
|        | [A Preliminary Study of ChatGPT on News Recommendation Personalization, Provider Fairness, Fake News](#A%20Preliminary%20Study%20of%20ChatGPT%20on%20News%20Recommendation%20Personalization,%20Provider%20Fairness,%20Fake%20News) | arXiv preprint |                                                      | [arXiv:2306.10702](https://arxiv.org/abs/2306.10702) | 




### Privacy




## Techniques *

### Tool learning

| Name    | Paper                                                                                          | Publication    | Repository                                      | Link                                                 |
| ------- | ---------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- |
| GeneRec | [Generative Recommendation Towards Next-generation Recommender Paradigm](#Generative%20Recommendation%20Towards%20Next-generation%20Recommender%20Paradigm)                    | arXiv preprint | [GeneRec](https://github.com/Linxyhaha/GeneRec) | [arXiv:2304.03516](https://arxiv.org/abs/2304.03516) |

### Multimodal LLM4Rec

| Name | Paper                                                           | Publication    | Repository | Link                                                 |
| ---- | --------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- |
|      | [CTRL Connect Tabular and Language Model for CTR Prediction](#CTRL%20Connect%20Tabular%20and%20Language%20Model%20for%20CTR%20Prediction) | arXiv preprint |            | [arXiv:2306.02841](https://arxiv.org/abs/2306.02841) | 


### RLHF






## Others

### Knowledge-intensive language tasks

| Name    | Paper                                                                       | Publication    | Repository                                      | Link                                                 |
| ------- | --------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- |



## List


### InPars: Data Augmentation for Information Retrieval using Large Language Models

| Name   | Paper                                                                                                                                                                               | Publication    | Repository | Link                                                 | Classification                                                                                 |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| InPars | [InPars Data Augmentation for Information Retrieval using Large Language Models](#InPars%20Data%20Augmentation%20for%20Information%20Retrieval%20using%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2202.06991](https://arxiv.org/abs/2202.06991) | [Information retrieval](#Information%20retrieval), [Data augmentation](#Data%20augmentation) |





### Transformer Memory as a Differentiable Search Index

| Name                  | Paper                                                                                                                   | Publication  | Repository                                          | Link                                                 | Classification                                                                         |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------ | --------------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------------------------------- |
| [DSI](+Papers/DSI.md) | [Transformer Memory as a Differentiable Search Index](#Transformer%20Memory%20as%20a%20Differentiable%20Search%20Index) | NeurIPS 2022 | [inpars](https://github.com/zetaalphavector/inpars) | [arXiv:2202.05144](https://arxiv.org/abs/2202.05144) | [Information retrieval](#Information%20retrieval), [ID generation](#ID%20generation) |


本质是让LLMs去做[Information retrieval](../../../5.%20Information%20science/Information%20retrieval/Information%20retrieval.md)，之前的模型，无论是传统模型还是机器学习模型，都是对问题和文档进行一对一比较，最后挑选相关性较高的结果。

但是这个模型设计了文档的索引机制，输入问题，输出文档索引（使用beam search输出多个）。也算是端到端了,但是并不完全，毕竟这里文档的索引仍然需要id，真正的端到端肯定是直接输出结果。

任务本质就是文档索引，最后用Top-K结果进行评价。


### Improving Passage Retrieval with Zero-Shot Question Generation


| Name | Paper                                                                                                                                         | Publication | Repository                                                                                         | Link                                                 | Classification                                                           |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | -------------------------------------------------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------ |
|      | [Improving Passage Retrieval with Zero-Shot Question Generation](#Improving%20Passage%20Retrieval%20with%20Zero-Shot%20Question%20Generation) | EMNLP 2022  | [unsupervised-passage-reranking](https://github.com/DevSinghSachan/unsupervised-passage-reranking) | [arXiv:2204.07496](https://arxiv.org/abs/2204.07496) | [Information retrieval](#Information%20retrieval), [Ranking](#Ranking) |


本文提出了一个无监督的retriever-reranker pipeline。Retriever基于无监督的模型，如BM25、Contriever等，reranker作者尝试了3B参数量的T5系列模型以及2.7B参数量的GPT-neo。Reranker的训练，仍然是通过让大模型生成和段落对应的query，无监督地获取到大量的标注数据对。作者也发现由于使用了LLM，整个pipeline存在高延迟的问题。




### Atlas: Few-shot Learning with Retrieval Augmented Language Models

| Name  | Paper                                                                                                                                               | Publication    | Repository | Link                                                 | Classification                                     |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------------- |
| Atlas | [Atlas Few-shot Learning with Retrieval Augmented Language Models](#Atlas%20Few-shot%20Learning%20with%20Retrieval%20Augmented%20Language%20Models) | arXiv preprint |            | [arXiv:2208.03299](https://arxiv.org/abs/2208.03299) | [Information retrieval](#Information%20retrieval) |


设计了4种损失函数，用于训练LLM来做推荐任务。模型架构包括Retriever和Ranker，其中Ranker就是LLM。

> 在知识密集型任务当中，使用检索增强的语言模型能够拥有很好的表现，并且不需要很大的参数量。但是，对于few-shot场景下，检索能否有效增强语言模型的能力还不得而知。基于这个问题，作者提出了Atlas，一个预训练的检索增强语言模型，只需要使用非常少的训练样本就能够学习和处理知识密集型任务。
> 
> 具体来说，作者使用了一个叫Contriever的Retriever，而使用的LLM为T5。在训练过程中，作者使用了ADist，EMDR^2，PDist和LOOP四个训练目标对检索器和语言模型进行联合训练。同时，作者也使用了无监督的方式进行联合训练，包括Prefix language modeling，Masked language modeling和Title to section generation。在实验方面，使用了KILT，MMLU和一些其他的问答数据集对Atlas进行了测试，证明了检索增强的有效性。

> 本文作者研究了few-shot learning是否要求模型在参数中储存大量信息，以及记忆能力是否能从泛化能力中解耦。为了实现该目的，作者使用检索增强的架构，由外部的非参数知识源来代替模型参数。具体地，使用一个神经检索模型和一个外部的大语料库。相比于大语言模型（LLMs），检索增强模型（Retrieval-augmented model）更擅长知识密集型任务，需要的模型参数量更少，然而，其在few-shot场景下的能力尚不清楚。
> 
> 基于此，本文提出了Atlas，一个预训练检索增强模型，关注于使用极少训练样例来学习知识密集型任务。相比于540B参数量的PaLM模型，Atlas仅使用五十分之一的参数量就超越了其在Natural Questions数据集上的few-shot效果。
> 
> 具体地，本文使用基于seq-to-seq框架的Fusion-in-decoder作为语言模型，使用双塔模型检索相关的文档。模型采用联合训练的方式，使用语言模型提供检索模型的监督信号，并考虑了四种损失函数作为训练目标，包括attention distillation（ADist）、end-to-end training of multi-document reader and retriever（EMDR^2）、perplexity distillation（PDist）和leave-one-out perplexity distillation（LOOP）。
> 
> 本文还进行了无监督的联合预训练，包括prefix language modeling任务，mask language modeling任务和title生成章节内容任务。此外，本文引入了基于乘积量化的索引压缩，同效果下使内存减少了5倍
> 
> —— https://mp.weixin.qq.com/s/SF5bAYSrfQkBzk6bblg9fg

Re-rank部分是由Retriever完成的，而不是LLM。
![Pasted image 20230615180820](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230615180820.png)


### Generate rather than Retrieve: Large Language Models are Strong Context Generators

| Name                          | Paper                                                                                                                                                                                       | Publication | Repository                                  | Link                                                 | Classification                               |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------- | ---------------------------------------------------- | -------------------------------------------- |
| [GenRead](+Papers/GenRead.md) | [Generate rather than Retrieve Large Language Models are Strong Context Generators](#Generate%20rather%20than%20Retrieve%20Large%20Language%20Models%20are%20Strong%20Context%20Generators) | ICLR 2023   | [GenRead](https://github.com/wyu97/GenRead) | [arXiv:2209.10063](https://arxiv.org/abs/2209.10063) | [Content generation](#Content%20generation) |

> 本文提出把传统问答系统中的retrieve-then-read pipeline中的检索模型替换为LLM，创建generate-then-read pipeline。具体地，给定一个query，使用LLM配合指令来生成背景文档，再把生成的文档送入阅读理解模型中生成最终的答案。为了增加生成多样的文档，作者提出基于聚类的提示方法，用GPT-3得到query-文档对的表示，生成时从每个类中采样保证多样性。
> 
> 本文考虑两个实验设置：（1）Zero-shot设置：使用LLM生成一个背景文档，再使用同样的LLM根据文档生成答案；（2）监督学习设置：使用LLM生成多个背景文档，再使用领域内训练的FiD模型生成答案。作者在实验中展示了LLM生成的文档比检索到的top1文档更有可能包含正确答案，监督学习设置比zero-shot设置下获得了更好的效果。

将LLMs输出的结果连同之前的输入，再次作为输入给到LLMs，难绷

![Pasted image 20230707220830](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230707220830.png)

文章主要思想是使用大语言模型自动生成文档级别Prompt，使得在问答任务上效果更好

基于的观察是给大模型提供的问题信息越丰富，GPT给出的答案就越准确。似乎大语言模型学过的东西需要一点提示和回忆才会想起来正确结果。结果证明大模型二次预测效果更好，与传统方法一起使用更佳

### Promptagator: Few-shot Dense Retrieval From 8 Examples

| Name         | Paper                                                                                                                       | Publication    | Repository | Link                                                 | Classification                               |
| ------------ | --------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------- |
| Promptagator | [Promptagator Few-shot Dense Retrieval From 8 Examples](#Promptagator%20Few-shot%20Dense%20Retrieval%20From%208%20Examples) | arXiv preprint |            | [arXiv:2209.11755](https://arxiv.org/abs/2209.11755) | [Content generation](#Content%20generation) |

不同的检索任务通常具有非常不同的检索意图，如检索实体、寻找证据等。本文作者提出Promptagator，few-shot设置下的稠密检索，针对不同任务，使用任务特定的简短描述以及一些带标注的样例（2-8个），以清楚地表明检索意图，不需要其他来源的标注数据。

作者使用FLAN-137B LLM，根据few-shot的样例，为语料库中的文档生成query。为了保证生成的query是文档的“良配”，用生成的query-document数据训练一个检索模型，保留检索模型能用检索到对应文档的query作为最终的训练数据（正例）。然后，使用过滤后的数据训练一个稠密检索模型，同时用于筛选高质量负例。用这些正负例数据最终训练得到一个基于交叉注意力机制的精排模型。


### Chat-REC: Towards Interactive and Explainable LLMs-Augmented Recommender System

| Name                            | Paper                                                                                                                                                                           | Publication    | Repository | Link                                                 | Classification                                                             |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------------------------------------- |
| [Chat-REC](+Papers/Chat-REC.md) | [Chat-REC Towards Interactive and Explainable LLMs-Augmented Recommender System](#Chat-REC%20Towards%20Interactive%20and%20Explainable%20LLMs-Augmented%20Recommender%20System) | arXiv preprint |            | [arXiv:2303.14524](https://arxiv.org/abs/2303.14524) | [Ranking](#Ranking), [Explanation generation](#Explanation%20generation) |

将传统推荐系统和LLM模型合并使用，主要以工程为主。本质还是候选物的re-rank。

![Fig 1](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig%201.png)
文章设计了整个用户提问的流程，穿插使用GPT和传统推荐系统。主要思路是将推荐系统的结果让GPT再过滤排序一遍


### Zero-Shot Next-Item Recommendation using Large Pretrained Language Models

| Name | Paper                                                                                                                                                                 | Publication    | Repository                                                                | Link                                                 | Classification                                                                                                  |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------- | ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| NIR  | [Zero-Shot Next-Item Recommendation using Large Pretrained Language Models](#Zero-Shot%20Next-Item%20Recommendation%20using%20Large%20Pretrained%20Language%20Models) | arXiv preprint | [LLM-Next-Item-Rec](https://github.com/AGI-Edgerunners/LLM-Next-Item-Rec) | [arXiv:2304.03153](https://arxiv.org/abs/2304.03153) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [Ranking](#Ranking), |

最简单的似乎不需要训练模型的方法，使用三段式提问让LLMs输出结果

![800](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/NIR.png)

> 本文评测了零样本设定下，LLM在下一个物品预测任务下的能力。本文提出了两个挑战：1. 对于LLM来说，推荐的候选空间非常大，2. LLM不清楚用户的历史交互和偏好。为此，本文提出了一种提示策略：“Zero-Shot Next-Item Recommendation (NIR)”， 使得LLM可以处理预测下一个物品的任务。具体来说，本文首先利用外部模块（传统的协同过滤模型）生成候选物品，然后分别提示LLM：1. 提取用户偏好，2. 选择代表性的历史交互物品， 3. 推荐并排序包含十个物品的列表。本文结果表明GPT-3在MovieLens 100K数据集上具有较强的零样本能力，甚至可以优于在该数据集上完整训练的传统推荐模型。



### Generative Recommendation: Towards Next-generation Recommender Paradigm

| Name    | Paper                                                                                                                                                       | Publication    | Repository                                      | Link                                                 | Classification                                                                   |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------------------------- |
| GeneRec | [Generative Recommendation Towards Next-generation Recommender Paradigm](#Generative%20Recommendation%20Towards%20Next-generation%20Recommender%20Paradigm) | arXiv preprint | [GeneRec](https://github.com/Linxyhaha/GeneRec) | [arXiv:2304.03516](https://arxiv.org/abs/2304.03516) | [Content generation](#Content%20generation), [Tool learning](#Tool%20learning) |

直接跳出了推荐系统原有的范式，即互联网公司有什么就推荐什么，而是将内容创作也交给了推荐系统来做。

> 本文作者认为传统的推荐模型往往从物品集合中检索合适的物品来实现个性化推荐。然而，这样的范式可能存在一些问题：1. 已有的人类生成的物品集合并不一定能符合用户多样化的需求， 2. 用户往往通过被动的，低效的反馈（如点击），来调节推荐。基于AIGC的蓬勃发展，作者构想的下一代的推荐范式应该具有以下两个目标：1. 通过生成式AI来生成个性化的内容，2. 融入用户指令来指导内容的生成。
> 
> 为了实现这个目标，作者提出了一个新的生成式推荐范式：GeneRec. 具体来说，作者首先预处理用户的指令和传统的反馈作为生成的依赖。然后，作者基于AI editor和AI creator来实例化AI generator，使得GeneRec可以基于用户的需求重新定制已有的物品和创建新的物品。

这篇论文提出了一种新的推荐系统范式，名为GeneRec，它利用AI生成器来个性化内容生成，并利用用户指令来获取用户的信息需求。具体来说，它预处理用户的指令和传统反馈（例如，点击）通过一个指导器来输出生成指导。在给定指导的情况下，我们通过AI编辑器和AI创作者实例化AI生成器，以重新利用现有项目和创建新项目，以满足用户的信息需求。最后，GeneRec可以执行内容检索、再利用和创建，以满足用户的信息需求。此外，为了确保生成项目的可信度，我们强调了各种真实性检查，如真实性和合法性检查。最后，我们研究了在微视频生成上实现AI编辑器和AI创作者的可行性，显示出有希望的结果。

GeneRec模型确实更像是一个内容生成和编辑模型，而不是传统意义上的推荐模型。然而，它的目标仍然是为了满足用户的信息需求，这是推荐系统的核心目标。

GeneRec模型的独特之处在于，它不仅仅是根据用户的历史行为来推荐内容，而是直接生成或编辑内容以满足用户的需求。这种方法可以看作是推荐系统的一种扩展或变体，它将推荐系统的范围从简单的内容推荐扩展到了内容生成和编辑。

![Pasted image 20230618181431](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230618181431.png)

这篇论文中提到了以下几个任务：

1. **个性化缩略图选择和生成**：这个任务的目标是基于用户反馈生成个性化的缩略图，而不需要用户指令。具体来说，给定一个微视频和用户的历史反馈，AI编辑器应该从微视频中选择一个帧作为缩略图，或者生成一个缩略图以匹配用户的偏好。
2. **微视频剪辑**：这个任务的目标是根据用户的指令和反馈，从一个微视频中选择和剪辑出一段片段，以满足用户的信息需求。
3. **微视频内容编辑**：这个任务的目标是根据用户的指令和反馈，编辑微视频的内容，以满足用户的信息需求。
4. **微视频内容创建**：这个任务的目标是根据用户的指令和反馈，创建新的微视频内容，以满足用户的信息需求。

这些任务的设置都是为了实现论文中提出的GeneRec模型，该模型旨在通过AI生成器生成个性化的内容，以满足用户的信息需求。


### GPT4Rec: A Generative Framework for Personalized Recommendation and User Interests Interpretation

| Name    | Paper                                                                                                                                                                                                                     | Publication    | Repository | Link                                                 | Classification                           |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------- |
| GPT4Rec | [GPT4Rec A Generative Framework for Personalized Recommendation and User Interests Interpretation](#GPT4Rec%20A%20Generative%20Framework%20for%20Personalized%20Recommendation%20and%20User%20Interests%20Interpretation) | arXiv preprint |            | [arXiv:2304.03879](https://arxiv.org/abs/2304.03879) | [Query generation](#Query%20generation) |

![Pasted image 20230623175129](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230623175129.png)

> 本文认为传统的推荐模型用ID来表征物品，并且使用判别式方法建模，可能会导致以下几个限制：1. 无法利用物品的内容信息和NLP模型的语言建模能力。2. 无法解释用户兴趣来提升推荐的相关性的多样性。3. 无法适配更实际的应用场景，例如不断增加新的商品。
> 
> 为此，本文提出GPT4Rec，利用灵活的生成框架来处理推荐任务。具体来说，基于用户历史交互的物品，和它们对应的标题 ，GPT4Rec首先要求GPT2来生成假设的"搜索查询"，然后引入搜索引擎（BM25），来基于这个查询检索相关的物品。实验证明通过beam search，GPT2可以生成多样化的召回商品以及覆盖用户的多样化的兴趣。



### Is ChatGPT a Good Recommender? A Preliminary Study


| Name | Paper                                                                                                                   | Publication    | Repository | Link                                                 | Classification                                                                                                                                                                                                                                                                                   |
| ---- | ----------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|      | [Is ChatGPT a Good Recommender? A Preliminary Study](#Is%20ChatGPT%20a%20Good%20Recommender?%20A%20Preliminary%20Study) | arXiv preprint |            | [arXiv:2304.10149](https://arxiv.org/abs/2304.10149) | [LLM as RS](#LLM%20as%20RS), [Direct recommendation](#Direct%20recommendation), [Sequential recommendation](#Sequential%20recommendation), [Rating prediction](#Rating%20prediction), [Explanation generation](#Explanation%20generation), [Review summarization](#Review%20summarization) |

最近一段时间，也有不少关于推荐系统的预训练语言模型出现，譬如 P5 以及 M6-Rec。

本文设计的框架：

![Pasted image 20230602093017](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230602093017.png)

这篇文章确实是一篇调研论文，文中提到了5种经典推荐系统任务，包括Rating prediction、Sequential recommendation、Direct recommendation、Explanation Generation和Review summarization，并且分别通过Metrics和真人进行了性能评估。

比较违反直觉的是ChatGPT在序列推荐上性能很差。对于这一结果，论文猜测可能是由于 ChatGPT 输入字符的限制，导致推荐物品主要以标题作为表示，这使得物品之间的关系无法被有效表示，这对推荐任务来说可能是至关重要的。同时，ChatGPT 也有可能生成不存在于数据集中的项目标题，但是论文作者表示，尽管他们已经使用相似性匹配将预测标题映射到了数据集中现有的标题中，但是这种映射并没有带来显著的增益。

不过这个论文没有进行微调，所以肯定性能是差的。


### TALLRec: An Effective and Efficient Tuning Framework to Align Large Language Model with Recommendation

| Name                          | Paper                                                                                                                                                                                                                                     | Publication    | Repository                                      | Link                                                 | Classification                                                                                               |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| [TALLRec](+Papers/TALLRec.md) | [TALLRec An Effective and Efficient Tuning Framework to Align Large Language Model with Recommendation](#TALLRec%20An%20Effective%20and%20Efficient%20Tuning%20Framework%20to%20Align%20Large%20Language%20Model%20with%20Recommendation) | arXiv preprint | [TALLRec](https://github.com/SAI990323/TALLRec) | [arXiv:2305.00447](https://arxiv.org/abs/2305.00447) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [Tuning](#Tuning) |

简单来说就是通过两次tuning来优化LLMs模型，使其适应推荐任务，两次优化分别是**instruction tuning**和**rec-tuning**。instruction tuning就是通常所说的instruction tuning，rec-tuning是专门为推荐任务设计的对话方式，有特定的格式。事实证明instruction tuning让模型更会聊天，rec-tuning让模型更会推荐。

![Fig. 2](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/Fig.%202.png)

感觉这种方法更符合未来的趋势，可惜用梯度下降训练模型本身效率就挺低的，感觉还是要找到更有效地学习模式

### Web Content Filtering through knowledge distillation of Large Language Models

| Name | Paper                                                                                                                                                                             | Publication    | Repository | Link                                                 | Classification                                                 |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------------------------- |
|      | [Web Content Filtering through knowledge distillation of Large Language Models](#Web%20Content%20Filtering%20through%20knowledge%20distillation%20of%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2305.05027](https://arxiv.org/abs/2305.05027) | [Tuning](#Tuning), [Model compression](#Model%20compression) |

URL分类任务，感觉不是推荐任务

微调大模型+蒸馏小模型

- 使用了T5 Large和GPT-3 Babbage作为教师模型，BERTiny和eXpose作为学生模型，
- 使用Adafactor优化器Fine-tuning T5大模型，并使用OpenAI API对GPT-3模型进行了Fine-tuning（The GPT-3 Babbage model was not fine-tuned on 5 million samples due to cost considerations.）

### Recommender Systems with Generative Retrieval

| Name                      | Paper                                                                                                   | Publication    | Repository | Link                                                 | Classification                                                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| [TIGER](+Papers/TIGER.md) | [Recommender Systems with Generative Retrieval](#Recommender%20Systems%20with%20Generative%20Retrieval) | arXiv preprint |            | [arXiv:2305.05065](https://arxiv.org/abs/2305.05065) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [ID generation](#ID%20generation) |

Semantic ID Generation + Seq2seq Recommendations

就是设计了一种embedding方法，将所有物品表示为一些整数的组合。实验证明这比小数级别的嵌入更好。然后模型尝试预测这个点击序列。


### Do LLMs Understand User Preferences? Evaluating LLMs On User Rating Prediction

| Name | Paper                                                                                                                                                                                 | Publication    | Repository | Link                                                 | Classification                             |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ------------------------------------------ |
|      | [Do LLMs Understand User Preferences? Evaluating LLMs On User Rating Prediction](#Do%20LLMs%20Understand%20User%20Preferences?%20Evaluating%20LLMs%20On%20User%20Rating%20Prediction) | arXiv preprint |            | [arXiv:2305.06474](https://arxiv.org/abs/2305.06474) | [Rating prediction](#Rating%20prediction) |

LLM预测用户评分

- 实验表明，单靠LLM自身的信息无法达到专业推荐算法的水平，模型必须要在数据集上微调。
- 回归损失比分类损失要好，LLM可能更适合排名任务。
- 100B以上大小的模型性能可以接受
- LLM微调只需要极少的数据


### A First Look at LLM-Powered Generative News Recommendation

| Name  | Paper                                                                                                                                   | Publication    | Repository | Link                                                 | Classification                               |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------- |
| GENRE | [A First Look at LLM-Powered Generative News Recommendation](#A%20First%20Look%20at%20LLM-Powered%20Generative%20News%20Recommendation) | arXiv preprint |            | [arXiv:2305.06566](https://arxiv.org/abs/2305.06566) | [Content generation](#Content%20generation) |

> 设计了一种LLM for Rec框架，用LLM作为信息提取模型，间接用于推荐任务
> 
> 本文指出传统的新闻推荐可能存在以下几个挑战：1. 冷启动。对于长尾或新用户，模型无法较好的建模和理解他们的兴趣。2. 用户画像建模。出于隐私保护的考量，现有的数据集可能无法包含详细的用户画像信息。3. 新闻内容理解。由于新闻数据中标题和内容存在不一致的问题，导致难以识别新闻中的关键概念和主题。
> 
> 为了解决这些问题，本文提出GENRE，一个基于LLM的生成式新闻推荐框架。具体来说，GENRE利用可获得的新闻数据，如标题，摘要，和新闻类别，来构建提示，从而激发LLM基于其通用知识来产生相关的信息 ，如新闻摘要，用户画像，个性化新闻等。这些生成的新闻信息将被加入到数据库中，并迭代的优化LLM生成。在此之后，这些生成出来的新的数据将被用来训练新闻推荐模型。

![Pasted image 20230531163844](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230531163844.png)


### How to Index Item IDs for Recommendation Foundation Models

| Name | Paper                                                                                                                                     | Publication    | Repository | Link                                                 | Classification                                                                                                               |
| ---- | ----------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
|      | [How to Index Item IDs for Recommendation Foundation Models](#How%20to%20Index%20Item%20IDs%20for%20Recommendation%20Foundation%20Models) | arXiv preprint |            | [arXiv:2305.06569](https://arxiv.org/abs/2305.06569) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [ID generation](#ID%20generation) |

设计索引的ID，实验分析了3种传统索引方式和4种常用索引方式，同时还有几种方法的混合。

- Random Indexing (RID), Title Indexing (TID), and Independent Indexing (IID), Sequential Indexing (SID), Collaborative Indexing (CID), Semantic Indexing (SemID), and Hybrid Indexing (HID)

最后分析了优劣，CID+IID和SID比较好。


### Recommendation as Instruction Following: A Large Language Model Empowered Recommendation Approach

| Name                                  | Paper                                                                                                                                                                                                                     | Publication    | Repository | Link                                                 | Classification                                                                                                                               |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| [InstructRec](+Papers/InstructRec.md) | [Recommendation as Instruction Following A Large Language Model Empowered Recommendation Approach](#Recommendation%20as%20Instruction%20Following%20A%20Large%20Language%20Model%20Empowered%20Recommendation%20Approach) | arXiv preprint |            | [arXiv:2305.07001](https://arxiv.org/abs/2305.07001) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [Information retrieval](#Information%20retrieval) |

![Pasted image 20230701142758](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230701142758.png)

Instruction design，设计了多种prompt来微调模型

![Pasted image 20230701142901](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230701142901.png)

实验包括序列推荐、查询推荐和个性化查询推荐

[「人大，腾讯」InstructRec | 按指令推荐：基于LLM的推荐方法](https://mp.weixin.qq.com/s/uSqSsMsoXVi_ACyi5LX8eA)

### Is ChatGPT Fair for Recommendation? Evaluating Fairness in Large Language Model Recommendation

| Name    | Paper                                                                                                                                                                                                                   | Publication    | Repository                                        | Link                                                 | Classification         |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------- | ---------------------------------------------------- | ---------------------- |
| FaiRLLM | [Is ChatGPT Fair for Recommendation? Evaluating Fairness in Large Language Model Recommendation](#Is%20ChatGPT%20Fair%20for%20Recommendation?%20Evaluating%20Fairness%20in%20Large%20Language%20Model%20Recommendation) | arXiv preprint | [FaiRLLM](https://github.com/jizhi-zhang/FaiRLLM) | [arXiv:2305.07609](https://arxiv.org/abs/2305.07609) | [Fairness](#Fairness) |

![Pasted image 20230614211026](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230614211026.png)

> 本文旨在评测达模型的推荐结果是否公平。实际上，由于在大量的无标注语料上预训练，LLM存在一定的社会偏见，可能导致LLM产生不公平的推荐结果。为此，本文提出了一个新的公平性benchmark："Fairness of Recommendation via LLM (FaiRLLM)."
> 
> 具体来说，FaiRLLM通过比较LLM在"neutral instructions" （没有包含用户的敏感属性）和"sensitive isntructions" （包含敏感属性）下的推荐结果，来评估LLM的公平性。结果表明，LLM 可能产生不公平的推荐，而且LLM的公平性随着不同的敏感属性而变化。


### Large Language Models are Zero-Shot Rankers for Recommender Systems

| Name    | Paper                                                                                                                                                       | Publication    | Repository                                     | Link                                                 | Classification                                                                                                 |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| LLMRank | [Large Language Models are Zero-Shot Rankers for Recommender Systems](#Large%20Language%20Models%20are%20Zero-Shot%20Rankers%20for%20Recommender%20Systems) | arXiv preprint | [LLMRank](https://github.com/RUCAIBox/LLMRank) | [arXiv:2305.08845](https://arxiv.org/abs/2305.08845) | [LLM as RS](#LLM%20as%20RS), [Sequential recommendation](#Sequential%20recommendation), [Ranking](#Ranking) |

![Pasted image 20230614100006](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230614100006.png)

本文评测了LLM在推荐系统中的零样本排序能力。具体来说，本文将推荐问题形式化为给定条件的排序任务，其中用户的历史交互作为条件，召回得到的物品作为候选。本文通过设计合适的prompt模版，结合条件、候选、排序指令，使得LLM可以执行推荐中的排序任务。本文在两个公开数据集上进行了详细的实验，并得到以下发现：

- LLM可以基于用户的历史交互实现个性化的排序，但是LLM很难感知到用户历史交互的序列关系。
- 基于特别设计的提示，例如“recency-focused prompting”, “in-context learning”, LLM可以被激发出感知历史交互序列性的能力，从而提升排序能力。
- LLM优于其他的零样本推荐模型，展示了较好的零样本排序能力。特别当采用多路召回生成候选时，LLM具有更好的判别能力。
- LLM在排序时有position bias和popularity bias，但可以被适当的提示或bootstrapping等策略所缓解。


### Exploring the Upper Limits of Text-Based Collaborative Filtering Using Large Language Models: Discoveries and Insights

| Name | Paper                                                                                                                                                                                                                                                                        | Publication    | Repository | Link                                                 | Classification                                    |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ------------------------------------------------- |
|      | [Exploring the Upper Limits of Text-Based Collaborative Filtering Using Large Language Models: Discoveries and Insights](#Exploring%20the%20Upper%20Limits%20of%20Text-Based%20Collaborative%20Filtering%20Using%20Large%20Language%20Models%20Discoveries%20and%20Insights) | arXiv preprint |            | [arXiv:2305.11700](https://arxiv.org/abs/2305.11700) | [Information retrieval](#Information%20retrieval), [Performance](#Performance) |

[推荐系统范式之争，LLM vs. ID？](https://mp.weixin.qq.com/s/gXdWjE6T15QUcugMTjPLpw)



### Rethinking the Evaluation for Conversational Recommendation in the Era of Large Language Models

| Name   | Paper                                                                                                | Publication    | Repository                                           | Link                                                 | Classification   |
| ------ | ---------------------------------------------------------------------------------------------------- | -------------- | ---------------------------------------------------- | ---------------------------------------------------- | ---------------- |
| iEvaLM | [Rethinking the Evaluation for Conversational Recommendation in the Era of Large Language Models](#Rethinking%20the%20Evaluation%20for%20Conversational%20Recommendation%20in%20the%20Era%20of%20Large%20Language%20Models) | arXiv preprint | [iEvaLM-CRS](https://github.com/RUCAIBox/iEvaLM-CRS) | [arXiv:2305.13112](https://arxiv.org/abs/2305.13112) | [#Performance](#Performance) | 

![600](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230614151527.png)

> 本文同样尝试利用ChatGPT来构建对话式推荐系统，并为此进行了系统性的评测。本文首先在已有的benchmark数据集上评测了ChatGPT的对话推荐能力。然而，结论是反直觉的：“ChatGPT并没有展示较好的效果”。为此，本文作者检查了ChatGPT失败的案例，并发现失败的原因在于：已有的评测方式依赖于对齐人类手工标注的推荐和对话，并过分强调了基于对话上下文来对ground-truth物品的拟合。因此，传统的评测指标，如BLEU和GOUGE等无法反映LLM在文本生成任务上的真实能力。
> 
> 为了解决上述的问题，本文旨在改善评测的方式，使其更加关注于对话推荐系统的交互能力。理想来说，这样的评测应该由人类标注，然而由于高昂的成本，本文尝试使用基于LLM的用户模拟器来测试LLM的对话推荐能力。在这样的评测方式下，ChatGPT取得了出色的表现。特别的，ChatGPT具有突出的解释能力，这是目前的对话推荐系统难以做到的。

### Text Is All You Need: Learning Language Representations for Sequential Recommendation

| Name      | Paper                                                                                     | Publication | Repository | Link                                                 | Classification                               |
| --------- | ----------------------------------------------------------------------------------------- | ----------- | ---------- | ---------------------------------------------------- | -------------------------------------------- |
| Recformer | [Text Is All You Need Learning Language Representations for Sequential Recommendation](#Text%20Is%20All%20You%20Need%20Learning%20Language%20Representations%20for%20Sequential%20Recommendation) | KDD 2023    |            | [arXiv:2305.13731](https://arxiv.org/abs/2305.13731) | [#CTR prediction](#CTR%20prediction), [#Multimodal LLM4Rec](#Multimodal%20LLM4Rec) |

[KDD'23「Amazon」Text Is All You Need：通过学习语言表征来用于序列推荐](https://mp.weixin.qq.com/s/z0nLk34Qa9A8hO2LuTuWbw)

[KDD2023 | RecFormer: 为序列推荐学习通用的语言表示](https://mp.weixin.qq.com/s/lEjN75p8AUbJ8ZIfVorSzA)

![Pasted image 20230710113234](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230710113234.png)

![Pasted image 20230710113448](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230710113448.png)

整体结构如图所示，值得注意的是，无论是单个物品的表示还是用户交互序列的表示，最终用于推荐的输出只有$h_{[CLS]}$部分，也就是图3左上角的部分。

### A Survey on Large Language Models for Recommendation

| Name | Paper                                                     | Publication    | Repository | Link                                                 | Classification |
| ---- | --------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------- |
|      | [A Survey on Large Language Models for Recommendation](#A%20Survey%20on%20Large%20Language%20Models%20for%20Recommendation) | arXiv preprint |            | [arXiv:2305.19860](https://arxiv.org/abs/2305.19860) | [#Surveys](#Surveys)   | 

[大模型如何重塑推荐？中科大等最新《面向推荐的大型语言模型》综述，全面阐述LLM4Rec进展](https://mp.weixin.qq.com/s/WCUjCahiak4STbb0QjJInQ)

### CTRL: Connect Tabular and Language Model for CTR Prediction

| Name | Paper                                                           | Publication | Repository | Link                                                 | Classification                               |
| ---- | --------------------------------------------------------------- | ----------- | ---------- | ---------------------------------------------------- | -------------------------------------------- |
| CTRL | [CTRL Connect Tabular and Language Model for CTR Prediction](#CTRL%20Connect%20Tabular%20and%20Language%20Model%20for%20CTR%20Prediction) | arXiv preprint |            | [arXiv:2306.02841](https://arxiv.org/abs/2306.02841) | [#CTR prediction](#CTR%20prediction), [#Multimodal LLM4Rec](#Multimodal%20LLM4Rec) |

本文使用语言模型进行跨模式校准，将表格和文本进行校准，通过对比学习得到协作模型，之后再进行单独的调整。

![Pasted image 20230625182143](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230625182143.png)

### Efficiently Measuring the Cognitive Ability of LLMs: An Adaptive Testing Perspective

| Name | Paper                                                                                    | Publication    | Repository | Link                                                 | Classification   |
| ---- | ---------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------- |
|      | [Efficiently Measuring the Cognitive Ability of LLMs An Adaptive Testing Perspective](#Efficiently%20Measuring%20the%20Cognitive%20Ability%20of%20LLMs%20An%20Adaptive%20Testing%20Perspective) | arXiv preprint |            | [arXiv:2306.10512](https://arxiv.org/abs/2306.10512) | [#Performance](#Performance) |

[“千模千测”——针对大语言模型认知能力的高效测试方法](https://mp.weixin.qq.com/s/9aWgvFGaSesu4JzEHE4Ncw)



### A Preliminary Study of ChatGPT on News Recommendation: Personalization, Provider Fairness, Fake News

| Name | Paper                                                                                                    | Publication    | Repository | Link                                                 | Classification |
| ---- | -------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------- |
|      | [A Preliminary Study of ChatGPT on News Recommendation Personalization, Provider Fairness, Fake News](#A%20Preliminary%20Study%20of%20ChatGPT%20on%20News%20Recommendation%20Personalization,%20Provider%20Fairness,%20Fake%20News) | arXiv preprint |            | [arXiv:2306.10702](https://arxiv.org/abs/2306.10702) | [#Performance](#Performance), [#Fairness](#Fairness), [#Authenticity](#Authenticity)               |

这篇论文的标题是"Exploring the Use of ChatGPT for News Recommendation: An Initial Study"，主要研究了使用ChatGPT进行新闻推荐的可能性。作者们从三个角度对ChatGPT的性能进行了评估：个性化新闻推荐、新闻提供商的公平性，以及假新闻检测。

以下是作者们进行的主要工作：

1. **个性化新闻推荐**：作者们使用了30个随机选取的用户样本，以探索ChatGPT在基于一组未读文章为个别用户生成推荐时的性能。他们发现，ChatGPT在区分用户已读文章和候选文章方面存在困难，可能会生成包含用户已读文章的推荐。他们提出了一种假设，即通过使用具有明确键的JSON格式改进提示的组织，可以更好地区分用户已读的文章和候选文章。
2. **新闻提供商的公平性**：作者们首先识别了与新闻提供商公平性相关的任何偏见，然后探索了可能的提示改进以缓解这些问题。他们发现，ChatGPT倾向于推荐来自其标记为流行的提供商的文章。他们提出了另一种假设，即明确指定来自流行和不流行提供商的文章数量可以根据用户的容忍度减轻提供商偏见的问题。
3. **假新闻检测**：作者们评估了ChatGPT在识别和处理假新闻方面的能力。他们发现，ChatGPT在识别假新闻方面的性能优于随机猜测，但仍有改进的空间。

此外，作者们还开发了一个网页，以每周的频率跟踪ChatGPT在他们研究的任务和提示上的性能。

总的来说，这篇论文的结论是，尽管ChatGPT在新闻推荐任务上表现出了一些有限的性能，但它仍然展示了在个性化新闻推荐、新闻提供商公平性和假新闻检测等任务上的潜力。作者们鼓励更多的研究者参与到利用大型语言模型（如ChatGPT）提高新闻推荐性能的研究中来。


### Towards Open-World Recommendation with Knowledge Augmentation from Large Language Models

| Name | Paper                                                                                         | Publication    | Repository | Link                                                 | Classification |
| ---- | --------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | -------------- |
| KAR  | [Towards Open-World Recommendation with Knowledge Augmentation from Large Language Models](#Towards%20Open-World%20Recommendation%20with%20Knowledge%20Augmentation%20from%20Large%20Language%20Models) | arXiv preprint |            | [arXiv:2306.10933](https://arxiv.org/abs/2306.10933) |  [#LLM-augmented RS](#LLM-augmented%20RS), [#Sequential recommendation](#Sequential%20recommendation)              |

这篇论文提出了一个名为KAR（Open-World Knowledge Augmented Recommendation Framework with Large Language Models）的开放世界知识增强推荐框架。KAR的目标是通过大型语言模型（LLMs）获取两种类型的外部知识——用户偏好的推理知识和项目的事实知识，以增强推荐系统的性能。

![Pasted image 20230629113444](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230629113444.png)

KAR的主要贡献和特点包括：

1. **知识推理和生成**：KAR使用因子化提示（factorization prompting）来从LLMs中获取用户偏好的推理知识和项目的事实知识。这种方法可以避免组合间隙问题，使得LLMs能够更准确地推理用户的偏好。
2. **知识适应**：KAR使用混合专家适配器（hybrid-expert adaptor）将生成的知识转化为推荐空间中的增强向量。这种方法可以确保生成的知识与推荐任务兼容。
3. **知识利用**：KAR将增强向量与任何推荐模型的原始领域特征结合起来，以提高推荐性能。
4. **高效推理**：为了解决LLMs在推荐系统中的推理延迟问题，KAR预处理并预存储从LLMs中获取的知识。

> 在公开数据集上的广泛实验表明，KAR显著优于最先进的基线，并且与各种推荐算法兼容。这些结果表明，KAR提供了一种有效的方法来解决推荐系统的开放世界知识问题，同时保留了传统推荐系统的优点。

但是实际上，这个模型的性能提升并不明显，以DIN为背景，KAR在AUC上比VQ-Rec提高了1.92%，在Logloss上提高了2.79%，不清楚这么点提升是否算是显著改进。


### Generative Sequential Recommendation with GPTRec

| Name   | Paper                                                 | Publication    | Repository | Link                                                 | Classification                                 |
| ------ | ----------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ---------------------------------------------- |
| GPTRec | [Generative Sequential Recommendation with GPTRec](#Generative%20Sequential%20Recommendation%20with%20GPTRec) | arXiv preprint |            | [arXiv:2306.11114](https://arxiv.org/abs/2306.11114) | [#LLM as RS](#LLM%20as%20RS), [#Sequential recommendation](#Sequential%20recommendation) | 

这篇论文介绍了GPTRec，一个基于GPT-2架构的生成序列推荐模型，旨在解决传统的Top-K推荐策略在处理复杂推荐目标时的限制。GPTRec采用了一种新的Next-K推荐策略，它逐项生成推荐，考虑到已经推荐的项目，从而能够产生复杂的相互依赖的推荐列表。

此外，GPTRec还引入了一种基于SVD的子项项目标记化方法，以解决大词汇表问题。这种方法通过量化用户-项目交互矩阵的SVD分解得到的项目嵌入来分割项目ID，从而可以逐标记生成项目。这种方法可以显著减少嵌入表的大小，从而降低GPU内存消耗。

在MovieLens-1M数据集上的实验结果表明，GPTRec在Next-K生成模式下可以达到与SASRec相似的结果，同时使用SVD标记化可以减少40%的嵌入表大小，同时达到与SASRec相似的结果。这些结果表明，GPTRec提供了一种有效的方法来解决序列推荐的挑战，包括如何生成复杂的推荐列表，如何处理大词汇表问题，以及如何减少嵌入表的大小。

这篇论文的主要贡献包括：

1. **Next-K推荐策略**：
	- 提出了一种新的Next-K推荐策略，作为传统Top-K推荐的替代方案。通过展示GPTRec在Next-K生成模式下可以达到与SASRec类似的结果，证明了这种策略的可行性。
	- Top-K策略就是所有的item都单独计算一个score，最后选最大的几个；Next-K则是顺序生成，将之前的选择也加入模型进行预测，再选择最大score的item，不断循环这个过程。
2. **基于SVD的子项项目标记化**：提出了一种新的基于SVD的子项项目标记化和逐标记项目生成方法。这种方法可以减少嵌入表的大小，同时达到与SASRec类似的结果。

![Pasted image 20230628221730](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230628221730.png)

3. **GPTRec模型**：提出了基于GPT-2架构的GPTRec生成序列推荐模型。这个模型与现有的SASRec模型在结构上类似，但GPTRec支持Next-K推荐策略，允许更灵活的目标。此外，GPTRec可以使用子项标记化，这对GPU内存消耗有利。GPTRec还使用了与SASRec不同的损失函数（交叉熵而非二元交叉熵）；实证结果显示，这种改变是有益的。
4. **识别限制和未来工作方向**：识别了所提出方法的限制，并确定了在未来工作中克服这些限制的策略。
	1. Cross-Entropy in GPTRec vs. Binary Cross-Entropy in SASRec, Cross-Entropy loss is better for the next-item recommendation

这些贡献提供了一种新的方法来解决序列推荐的挑战，包括如何生成复杂的推荐列表，如何处理大词汇表问题，以及如何减少嵌入表的大小。这些贡献也为未来的研究提供了一个强大的起点。


### OpenP5: Benchmarking Foundation Models for Recommendation

| Name   | Paper                                                         | Publication    | Repository                                      | Link                                                 | Classification  |
| ------ | ------------------------------------------------------------- | -------------- | ----------------------------------------------- | ---------------------------------------------------- | --------------- |
| OpenP5 | [OpenP5 Benchmarking Foundation Models for Recommendation](#OpenP5%20Benchmarking%20Foundation%20Models%20for%20Recommendation) | arXiv preprint | [OpenP5](https://github.com/agiresearch/OpenP5) | [arXiv:2306.11134](https://arxiv.org/abs/2306.11134) | [#Benchmarks](#Benchmarks) | 

这篇论文介绍了OpenP5，一个开源库，用于在Pre-train, Personalized Prompt and Predict Paradigm (P5)下对基础推荐模型进行基准测试。

OpenP5考虑了P5的三个维度：

1) 下游任务
	- 顺序推荐
	- 直接推荐
2) 推荐数据集
	- 重点关注10个数据集及其预训练模型（P5和SP5）
3) 项目索引方法
	- 随机索引
	- 顺序索引
	- 协作索引

作者基于这三个维度进行了测试。










# Research direction

[Uncovering ChatGPT's Capabilities in Recommender Systems](+Papers/Uncovering%20ChatGPT's%20Capabilities%20in%20Recommender%20Systems.md)分析认为LLMs适合做[](../../../5.%20Information%20science/Information%20filtering/Recommender%20system.md#^8ee569%7C%E5%86%B7%E5%90%AF%E5%8A%A8)，但是没有微调的LLMs还是比不过传统推荐模型。

- Language bias and fact-consistency in language generation tasks of recommendation.
- Knowledge transmission and injection for downstream recommendations.
- Scalability of pre-training mechanism in recommendation.
- Privacy issue and ethical state.



# Reference

1. [一文速览大语言模型在推荐系统中的应用](https://mp.weixin.qq.com/s/RdRLKjzbTWCATmtRMfxW0Q)
2. [谈谈LLM在推荐域的渗透，探索推荐新范式](https://mp.weixin.qq.com/s/TzpHDLr8JC4IhGXR_r10dA)