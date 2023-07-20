
## Sub-directions

[LLM4Rec](LLM4Rec.md)

[RL4Rec](RL4Rec.md)

[MTL4Rec](MTL4Rec.md)

> When Search Meets Recommendation: Learning Disentangled Search Representation for Recommendation

理解用户在搜索和推荐行为中的相似和不相似的兴趣是非常有用的，因为这可以帮助模型更好地理解用户的需求和偏好，从而提供更准确的推荐。

具体来说，如果我们知道用户在搜索和推荐行为中的兴趣是相似的，那么我们可以假设用户在这两种情况下的需求和偏好是一致的。例如，如果用户在搜索时表现出对某个主题的强烈兴趣，那么我们可以推断出用户可能也会对推荐的与该主题相关的内容感兴趣。

相反，如果我们知道用户在搜索和推荐行为中的兴趣是不相似的，那么我们可以假设用户在这两种情况下的需求和偏好可能是不同的。例如，如果用户在搜索时表现出对某个主题的强烈兴趣，但在接受推荐时却对该主题表现出不感兴趣，那么我们可以推断出用户可能更喜欢在搜索时探索新的内容，而在接受推荐时则更喜欢看他们已经熟悉的内容。

通过理解这些相似和不相似的兴趣，模型可以更好地理解用户的需求和偏好，从而提供更准确的推荐。这是这篇论文的主要贡献之一。


## Datasets

以下是做CTR任务可以使用的数据集，包括原生CTR数据集和一些通用推荐数据集。为了通用推荐数据集适配CTR（点击率）预测任务，需要将其转化为一个二分类的数据。

### Criteo

This dataset contains feature values and click feedback for millions of display ads. Its purpose is to benchmark algorithms for clickthrough rate (CTR) prediction. 

Criteo广告数据集是一个经典的用于预测广告点击率的数据集。2014年，由全球知名广告公司Criteo赞助举办展示广告挑战赛。但比赛过去太久了，Kaggle已不提供数据集。现有的不同获取数据集样本的方式：

1. 官网
	1. 发布数据集的组织是[Criteo AI Lab](https://ailab.criteo.com/)
	2. 他们发布的所有数据集可以在[这个](https://ailab.criteo.com/ressources/)网页找到
	3. 官网提供的数据集是1TB版本，压缩包大小为342GB，[下载页面](https://labs.criteo.com/2013/12/download-terabyte-click-logs-2/)
2. Kaggle
	1. 官方在kaggle发布的[比赛](https://www.kaggle.com/competitions/criteo-display-ad-challenge)是11GB版本，压缩包大小为4.3GB。但是因为时间太久，数据集已经不开放下载了
	2. 不过有一个[镜像数据集网页](https://www.kaggle.com/datasets/mrkmakr/criteo-dataset)上传在了kaggle上，可以下载
3. 其他
	1. 在GitHub上DeepCTR项目的issues/308讨论中提到了[另一个网站](%5Bhttps://figshare.com/articles/dataset/Kaggle_Display_Advertising_Challenge_dataset/5732310))，目前也可以11GB版本

Criteo数据集用于广告点击率预估任务（标签：0/1），其中包含13个dense特征和26个sparse特征。数据格式如下：
**第一列为label, 之后分别是13个dense特征(integer feature)，26个sparse特征(categorical feature)；每列之间使用tab进行分隔**


信息源：
- https://github.com/ZiyaoGeng/RecLearn/wiki/Criteo-Dataset
- https://github.com/shenweichen/DeepCTR/issues/308
- https://www.jianshu.com/p/5c88f4bd7c71


### MovieLens

The datasets describe ratings and free-text tagging activities from MovieLens, a movie recommendation service. It contains 20000263 ratings and 465564 tag applications across 27278 movies. These data were created by 138493 users between January 09, 1995 and March 31, 2015. This dataset was generated on October 17, 2016.

Users were selected at random for inclusion. All selected users had rated at least 20 movies.

MovieLens数据集是由[GroupLens](https://grouplens.org/datasets/movielens/)项目组制作的公开数据集。MoveieLens数据集可以说是推荐系统领域最为经典的数据集之一，其地位类似计算机视觉领域里的[MNIST数据集](https://so.csdn.net/so/search?q=MNIST%E6%95%B0%E6%8D%AE%E9%9B%86&spm=1001.2101.3001.7020)。

现有的不同获取数据集样本的方式：
1. 官网
	1. 发布数据集的组织是[GroupLens](https://grouplens.org/datasets/movielens/)
	2. 他们发布的所有数据集可以在[这个](https://grouplens.org/datasets)网页找到
	3. MovieLens数据集地址[在这](https://grouplens.org/datasets/movielens/)，他们提供多个版本：25M的最新版、1B的合成版
2. Kaggle
	1. 官方在kaggle发布的是[20M版本](https://www.kaggle.com/datasets/grouplens/movielens-20m-dataset)，数据集仍然可用

*MovieLens不是专门用于CTR任务的数据集，但是通常会被预处理来适配CTR任务范式。一种常见的用于推荐系统的正负样本构造方法就是：正样本通常是用户实际交互过的物品，而负样本则是用户没有交互过的物品。*


### Amazon Review Data (2018)

Official Website: https://nijianmo.github.io/amazon/

This dataset contains product reviews and metadata from Amazon:

1. **More Reviews**: The dataset now contains a total of 233.1 million reviews, a significant increase from the 142.8 million reviews in the 2014 version.
2. **Newer Reviews**: The dataset includes reviews from a wider time range, specifically from May 1996 to October 2018.
3. **Enhanced Metadata**: The dataset now includes transaction metadata for each review shown on the review page. This includes:
	- Product information such as color, size, and package type.
	- Product images taken after the user received the product.
	- More detailed metadata from the product landing page, including bullet-point descriptions under the product title, a technical details table (attribute-value pairs), and a similar products table.
4. **More Categories**: The dataset has been expanded to include 5 new product categories.

官网提供了数据集的整体介绍，但他们不建议随便下载完整数据集，并且给学生提供了精简版。完整数据集下载地址在（实际需要填一个表获取地址）：
https://cseweb.ucsd.edu/~jmcauley/datasets/amazon_v2/

为了使其适用于CTR（点击率）预测任务，需要将其转化为一个二分类的数据。原始的用户对物品的评分是一个从0到5的连续值。我们将评分为4和5的样本标记为正样本，其余的标记为负样本。CTR任务是基于用户的历史行为来预测用户是否会给定的电影评分超过3（标记为正）。

> We are not in any position to offer any license on the data. Please cite the following paper if you use the data in any way:  
> 
> Justifying recommendations using distantly-labeled reviews and fined-grained aspects  
> Jianmo Ni, Jiacheng Li, Julian McAuley  
> Empirical Methods in Natural Language Processing (EMNLP), 2019

### IJCAI-2018 Alimama International Advertising Algorithm Competition (IAAC)

IJCAI2018 Advertising Algorithm Competition (IAAC) is a dataset collected from a sponsored search in E-commerce. Each record refers to whether a user purchases the displayed item after clicking this item. There is a total of 478,138 records, 197,694 users, and 10,075 items.

可惜只有当时参与比赛的人可以下载数据集，目前[官网](https://tianchi.aliyun.com/competition/entrance/231647/introduction)已经不提供下载了，现有获取数据集样本的方式：

1. 官网存档
	1. 虽然比赛结束了，但是官方进行了存档，但只提供初赛的[少量数据集](https://tianchi.aliyun.com/dataset/147588)
2. CSDN资源
	1. 是一个兄弟的博客，分享了他的网盘链接，里面有1.4G的数据集
	2. 暂不清楚内容是什么，但是[链接](https://blog.csdn.net/qushoushi0594/article/details/94205452)仍然有效

除此之外，网上再找不到数据，似乎只有当时入围复赛的团队才能得到数据。。。

数据集本质上适用于CVR prediction，但是仍然也有CTR工作用了这个数据集（？？？），包括多场景学习。比如，[AdaSparse](#AdaSparse%20Learning%20Adaptively%20Sparse%20Structures%20for%20Multi-Domain%20Click-Through%20Rate%20Prediction)中依据用户特征{user_gender_id, user_age_level, user_star_level}划分了300个domains做了multi-scenario CTR prediction。

### Ali-CCP: Alibaba Click and Conversion Prediction

本数据集采集自手机淘宝移动客户端的推荐系统日志，其中包含点击和与之关联的转化数据，二者的关系图1描述。淘宝平台作为全球最大的在线零售电子商务平台，为提升其用户体验，通过推荐系统提供商品推荐服务，用户可以在浏览（impression）推荐结果中点击（click）感兴趣的商品，或者进一步对商品进行购买（conversion）。因此用户的行为可以抽象为一个序列模式：浏览 -> 点击 -> 购买。

简而言之就是一个多场景CTR、CVR数据集（似乎是APP多页面的多场景？有待考证），[官网](https://tianchi.aliyun.com/dataset/408)提供数据集介绍与下载：
- 训练集 4.10GB
- 测试集 4.68GB

### AliExpress Searching System Dataset

This is a dataset gathered from real-world traffic logs of the search system in AliExpress. As one of the largest global e-commerce platform in the world, AliExpress provides item searching service for more than 200 countries. An user firstly clicks a product from the search result page, and then decides to purchase the product or not.

The dataset is provided to facilitate any research on the problem of *Learning to Rank* (LTR). Previous LTR datasets are collected from one scenario. While this dataset is collected from 5 countries: Russia, Spain, French, Netherlands, and America, which can be seen as 5 scenarios. To our best knowledge, this is the first large scale real-world dataset for the problem of *Multi-Scenario Learning to Rank*.

这是一个从阿里巴巴国际站搜索系统的实际流量日志中收集的数据集。作为全球最大的电子商务平台之一，阿里巴巴国际站为超过200个国家提供商品搜索服务。用户首先从搜索结果页面点击一个产品，然后决定是否购买该产品。

这个数据集是为了促进任何关于学习排序（LTR）问题的研究而提供的。以前的LTR数据集是从一个场景中收集的。而这个数据集是从5个国家收集的：
- 俄罗斯 11.34GB
- 西班牙 2.60GB
- 法国 2.25GB
- 荷兰 1.49GB
- 美国 2.25GB
可以被视为5个场景。据我们所知，这是第一个大规模的实际世界数据集，用于解决多场景学习排序的问题。

简而言之就是一个多场景（多国家）CTR、CVR数据集，[官网](https://tianchi.aliyun.com/dataset/74690)提供数据集介绍与下载

### Others

#### [Multi-Domain Recommender Systems](https://paperswithcode.com/task/multi-domain-recommender-systems/codeless)

里面包含了三个工作的公开数据集：

- [RL-ISN-dataset](https://paperswithcode.com/dataset/rl-isn-dataset)
- [LEA-GCN-dataset](https://paperswithcode.com/dataset/lea-gcn-dataset)
- [X-Wines](https://paperswithcode.com/dataset/x-wines)

#### Click-Through Rate Prediction - Avazu

直接搜的谷歌ctr prediction dataset

https://paperswithcode.com/dataset/click-through-rate-prediction-avazu

# Papers



#### Multi-scenario learning

Multi-scenario learning，也叫Multi-domain learning理论上multi-scenario和multi-domain应该存在区别，但是在各项工作中经常将其混淆，并且没有明显的区分，因此本笔记将这两者视为同一个概念。

#### Click-through rate prediction

虽然CTR prediction不太**属于**推荐系统？但鉴于笔记刚刚起步，还是归为一类记录了，以后论文多了再考虑


## List

### Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts

| Name | Paper                                                                                      | Publication | Repository                                                    | Link                                                                       | Classification               |
| ---- | ------------------------------------------------------------------------------------------ | ----------- | ------------------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------- |
| [MMoE](../../1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/MMoE.md) | [Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts](#Modeling%20Task%20Relationships%20in%20Multi-task%20Learning%20with%20Multi-gate%20Mixture-of-Experts) | KDD 2018    | [DeepCTR-Torch](https://github.com/shenweichen/DeepCTR-Torch) | [doi.org/10.1145/3219819.3220007](https://doi.org/10.1145/3219819.3220007) | [Multi-scenario learning](#Multi-scenario%20learning), Multi-task learning |

![Pasted image 20230713091222](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230713091222.png)


### Progressive Layered Extraction (PLE): A Novel Multi-Task Learning (MTL) Model for Personalized Recommendations

| Name | Paper                                                                                                                                                                                                                                                    | Publication | Repository | Link                                                                       | Classification                                                                                                             |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| [PLE](../../1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/PLE.md)  | [Progressive Layered Extraction (PLE): A Novel Multi-Task Learning (MTL) Model for Personalized Recommendations](#Progressive%20Layered%20Extraction%20(PLE)%20A%20Novel%20Multi-Task%20Learning%20(MTL)%20Model%20for%20Personalized%20Recommendations) | CIKM 2021   |            | [doi.org/10.1145/3383313.3412236](https://doi.org/10.1145/3383313.3412236) | [Multi-scenario learning](#Multi-scenario%20learning), Multi-task learning |


### One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction

| Name | Paper                                                                                                                                                                                                            | Publication | Repository | Link                                                 | Classification                                                                                 |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| [STAR](+Papers/STAR.md) | [One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction](#One%20Model%20to%20Serve%20All%20Star%20Topology%20Adaptive%20Recommender%20for%20Multi-Domain%20CTR%20Prediction) | CIKM 2021   |            | [arXiv:2101.11427](https://arxiv.org/abs/2101.11427) | [Click-through rate prediction](#Click-through%20rate%20prediction), [Multi-scenario learning](#Multi-scenario%20learning) |

### Leaving No One Behind: A Multi-Scenario Multi-Task Meta Learning Approach for Advertiser Modeling

| Name                  | Paper                                                                                                                                                                                                                          | Publication  | Repository | Link                                                 | Classification                                                      |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | ---------- | ---------------------------------------------------- | ------------------------------------------------------------------- |
| [M2M](+Papers/M2M.md) | [Leaving No One Behind: A Multi-Scenario Multi-Task Meta Learning Approach for Advertiser Modeling](#Leaving%20No%20One%20Behind%20A%20Multi-Scenario%20Multi-Task%20Meta%20Learning%20Approach%20for%20Advertiser%20Modeling) | NeurIPS 2022 |            | [arXiv:2203.16218](https://arxiv.org/abs/2203.16218) | [Click-through rate prediction](#Click-through%20rate%20prediction) |




### APG: Adaptive Parameter Generation Network for Click-Through Rate Prediction

| Name | Paper                                                                                                                                                                        | Publication  | Repository | Link                                                 | Classification                                                      |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ---------- | ---------------------------------------------------- | ------------------------------------------------------------------- |
| APG  | [APG: Adaptive Parameter Generation Network for Click-Through Rate Prediction](#APG%20Adaptive%20Parameter%20Generation%20Network%20for%20Click-Through%20Rate%20Prediction) | NeurIPS 2022 |            | [arXiv:2203.16218](https://arxiv.org/abs/2203.16218) | [Click-through rate prediction](#Click-through%20rate%20prediction) | 

[APG: Adaptive Parameter Generation Network for Click-Through Rate Prediction论文解读](https://zhuanlan.zhihu.com/p/496687361)

### AdaSparse: Learning Adaptively Sparse Structures for Multi-Domain Click-Through Rate Prediction

| Name      | Paper                                                                                                                                                                                                                | Publication | Repository | Link                                                                       | Classification                                                                                                             |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| AdaSparse | [AdaSparse: Learning Adaptively Sparse Structures for Multi-Domain Click-Through Rate Prediction](#AdaSparse%20Learning%20Adaptively%20Sparse%20Structures%20for%20Multi-Domain%20Click-Through%20Rate%20Prediction) | CIKM 2022   |            | [doi.org/10.1145/3511808.3557541](https://doi.org/10.1145/3511808.3557541) | [Multi-scenario learning](#Multi-scenario%20learning), [Click-through rate prediction](#Click-through%20rate%20prediction) |





### Multi-Scenario Ranking with Adaptive Feature Learning

| Name  | Paper                                                                                                                     | Publication    | Repository | Link                                                 | Classification                                        |
| ----- | ------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------- | ---------------------------------------------------- | ----------------------------------------------------- |
| Maria | [Multi-Scenario Ranking with Adaptive Feature Learning](#Multi-Scenario%20Ranking%20with%20Adaptive%20Feature%20Learning) | arXiv preprint |            | [arXiv:2306.16732](https://arxiv.org/abs/2306.16732) | [Multi-scenario learning](#Multi-scenario%20learning) | 

[SIGIR'23 | 基于特征自适应的多场景预估建模](https://zhuanlan.zhihu.com/p/641895931)










