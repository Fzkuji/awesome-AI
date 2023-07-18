
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

官网提供了数据集的整体介绍，但他们不建议随便下载完整数据集，并且给学生提供了精简版。完整数据集下载地址在（实际需要填一个表获取地址）：
https://cseweb.ucsd.edu/~jmcauley/datasets/amazon_v2/

为了使其适用于CTR（点击率）预测任务，需要将其转化为一个二分类的数据。原始的用户对物品的评分是一个从0到5的连续值。我们将评分为4和5的样本标记为正样本，其余的标记为负样本。CTR任务是基于用户的历史行为来预测用户是否会给定的电影评分超过3（标记为正）。

特征包括
- reviewerID - ID of the reviewer, e.g. [A2SUAM1J3GNN3B](http://www.amazon.com/gp/cdp/member-reviews/A2SUAM1J3GNN3B)
- asin - ID of the product, e.g. [0000013714](http://www.amazon.com/dp/0000013714)
- reviewerName - name of the reviewer
- vote - helpful votes of the review
- style - a disctionary of the product metadata, e.g., "Format" is "Hardcover"
- reviewText - text of the review
- overall - rating of the product
- summary - summary of the review
- unixReviewTime - time of the review (unix time)
- reviewTime - time of the review (raw)
- image - images that users post after they have received the product

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

除此之外，网上再找不到数据




在推荐系统领域，Multi-Domain和Multi-Scenario的区别主要体现在处理的问题和应用的方法上。

1. Multi-Domain：多领域推荐系统主要关注如何在不同的领域（例如，电影、书籍、音乐等）中进行推荐。这种类型的推荐系统需要处理的主要问题是如何在不同的领域中找到合适的推荐方法，以及如何利用用户在一个领域的行为信息来推荐另一个领域的项目。例如，一个用户可能在电影领域的行为和他在音乐领域的行为之间存在一定的关联，多领域推荐系统就是要找到这种关联并利用它来进行推荐。
    
2. Multi-Scenario：多场景推荐系统则主要关注如何在不同的场景（例如，工作、休闲、学习等）中进行推荐。这种类型的推荐系统需要处理的主要问题是如何根据用户的当前场景来调整推荐策略，以及如何利用用户在一个场景的行为信息来推荐另一个场景的项目。例如，一个用户可能在工作场景下更喜欢看新闻，而在休闲场景下更喜欢看电影，多场景推荐系统就是要找到这种差异并利用它来进行推荐。
    

总的来说，多领域推荐系统和多场景推荐系统都是为了更好地理解用户的需求并提供更个性化的推荐，但它们关注的焦点和处理的问题是不同的。




# Papers



#### Multi-scenario modeling


## List

### Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts

| Name | Paper                                                                                      | Publication | Repository                                                    | Link                                                                       | Classification               |
| ---- | ------------------------------------------------------------------------------------------ | ----------- | ------------------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------- |
| [MMoE](../../1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Special%20Multi-Task%20Learning/+Papers/MMoE.md) | [Modeling Task Relationships in Multi-task Learning with Multi-gate Mixture-of-Experts](#Modeling%20Task%20Relationships%20in%20Multi-task%20Learning%20with%20Multi-gate%20Mixture-of-Experts) | KDD 2018    | [DeepCTR-Torch](https://github.com/shenweichen/DeepCTR-Torch) | [doi.org/10.1145/3219819.3220007](https://doi.org/10.1145/3219819.3220007) | [Multi-scenario modeling](#Multi-scenario%20modeling), Multi-task learning |

![Pasted image 20230713091222](../../../Resources/4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/Pasted%20image%2020230713091222.png)


### Progressive Layered Extraction (PLE): A Novel Multi-Task Learning (MTL) Model for Personalized Recommendations




### One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction

| Name | Paper                                                                                                                                                                                                            | Publication | Repository | Link                                                 | Classification                                                                                 |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| STAR | [One Model to Serve All: Star Topology Adaptive Recommender for Multi-Domain CTR Prediction](#One%20Model%20to%20Serve%20All%20Star%20Topology%20Adaptive%20Recommender%20for%20Multi-Domain%20CTR%20Prediction) | CIKM 2021   |            | [arXiv:2101.11427](https://arxiv.org/abs/2101.11427) | [#Information retrieval](#Information%20retrieval), [#Data augmentation](#Data%20augmentation) |
