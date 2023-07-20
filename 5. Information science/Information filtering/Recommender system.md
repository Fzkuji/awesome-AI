A **recommender system**, or a **recommendation system** (sometimes replacing 'system' with a synonym such as platform or engine), is a subclass of [information filtering system](https://en.wikipedia.org/wiki/Information_filtering_system "Information filtering system") that provide suggestions for items that are most pertinent to a particular user.

Typically, the suggestions refer to various decision-making processes, such as what product to purchase, what music to listen to, or what online news to read.

Recommender systems are particularly useful when an individual needs to choose an item from a potentially overwhelming number of items that a service may offer.



**一文入门推荐系统——推荐系统实践读书笔记：**
https://blog.csdn.net/baishuiniyaonulia/article/details/111030265


## Problems

推荐系统存在以下几个问题：
- 冷启动
- 数据稀疏
- 不断变化的用户喜好
- 不可预知的事项

> 用户需求的理解、推荐可信赖性、推荐场景的扩展、用户隐私
> 清华大学张敏副教授

> 推荐的初心是为了解决信息过载问题，减少用户处理信息的时间；后来推荐系统逐渐走向商业化，以解决信息过载为名，行诱导用户沉迷之实，延长用户访问信息的时间。推荐系统的个性化服务带来了信息偏食问题。信息偏食不仅影响了个人，还影响了社会，带来了社会的分裂。因此，他强调推荐系统需要跨学科合作的研究，需要理解信息过载和信息偏差都对人和社会带来什么影响。设立更加科学的优化目标，制定可实施的算法模型监管框架。
> 微软亚洲研究院谢幸

> 学术界的研究工作，往往会对问题进行简化，比如典型的缺乏对时间的建模，导致评估不准确，与工业界的真实应用场景存在较大的差异。

1.  **Cold-start problem**: This is a common issue in recommender systems where it's challenging to provide recommendations for new users or items due to the lack of historical interaction data. ^8ee569
2.  **Sparsity problem**: The interaction matrix in recommender systems is usually sparse, which means that only a small fraction of user-item interactions are observed. This makes it difficult to accurately predict user preferences.
3.  **Lack of interpretability**: Many recommender systems, especially those based on deep learning, are often seen as "black boxes" because they do not provide clear explanations for their recommendations. ^fa35f7
4.  **Incorporation of textual information**: Traditional recommender systems often fail to effectively incorporate textual information (like user reviews or item descriptions) into the recommendation process.
5. **Scalability**: As the number of users and items grows, providing recommendations quickly and in real-time becomes computationally challenging.
6. **Diversity**: Recommender systems often suggest items similar to those a user has already rated or interacted with, leading to a lack of diversity in recommendations.
7. **Serendipity**: This is related to the surprise factor in recommendations. Users are often more satisfied with recommendations when they are introduced to high-quality items they might not have discovered on their own.
8. **Privacy and security**: User data is essential for recommender systems to function. Ensuring this data is secure and that users' privacy is respected is a significant challenge. Additionally, recommender systems can be vulnerable to shilling attacks where the system is manipulated to promote or demote certain items.
9. **Temporal dynamics**: User preferences can change over time, but many recommender systems do not account for these temporal dynamics.
10. **Context-aware recommendations**: Many recommender systems fail to consider the context of the recommendation. For example, a user might prefer different types of music depending on whether they are working out or studying.
11. **Evaluation**: Evaluating the performance of recommender systems is challenging because of the subjective nature of recommendations. Traditional accuracy metrics may not fully capture user satisfaction.
12. **Long-tail problem**: Many recommender systems focus on popular items and may ignore less popular or niche items, even though these might be of interest to the user. This is known as the long-tail problem.
13. **Cross-domain recommendations**: This involves recommending items from a different domain than the one a user has shown interest in. For example, recommending a movie based on a user's book preferences. This is a challenging problem due to the difficulty in mapping preferences across different domains.

### Cold-start problem

^f00b70

Cold start (recommender systems) 是指在推荐系统中出现的一个问题，当用户或物品没有与系统进行过交互记录时，就会出现这个问题。这意味着系统没有可用的数据来进行个性化推荐。为了解决这个问题，已经提出了一些解决方案，其中一种方法是学习建模内容特征，例如文本、图像或元数据；另一种方法是从辅助领域中转移表示。

Cold start is a problem that occurs in recommender systems when there is insufficient data available to make accurate recommendations. This typically happens in two main scenarios:

1.  New user: When a new user joins the platform, the recommender system has no information about their preferences or past behavior. Consequently, it becomes difficult to provide personalized recommendations based on their interests.
2.  New item: When a new item is added to the platform, the recommender system lacks historical data about user interactions with that item, making it challenging to determine which users might be interested in it.

To address the cold start problem, recommender systems often employ various techniques, such as:

-   Content-based filtering: Leverage item features or user profile information to recommend similar items or those likely to match the user's interests.
-   Collaborative filtering: Utilize user-user or item-item similarities to make recommendations based on other users' preferences or similar items.
-   Hybrid approaches: Combine content-based and collaborative filtering methods to improve recommendation accuracy.
-   Knowledge-based recommendations: Use expert knowledge or domain-specific information to provide recommendations.
-   Active learning: Ask users for explicit feedback or preferences to quickly build a user profile.

These techniques help to alleviate the cold start problem and improve the quality of recommendations, even when the system has limited data about users or items.

#### 用户冷启动
给新用户做个性化推荐

解决方案：

根据用户注册信息对用户进行分类
推荐热门的排行榜
基于深度学习的语义理解模型
引导用户把自己的属性表达出来
利用用户在社交媒体的信息

#### 物品冷启动
把新的物品推荐给特定的用户

解决方案：

文本分析
主题模型
给物品打标签
推荐排行榜单

#### 系统冷启动
新系统让用户感受到个性化推荐





### Sparsity problem

解决方案：

奇异值分解：降低矩阵维数能降低数据稀疏性
数据填充：假设用户对其感兴趣物品相似的物品也感兴趣
基于物品本身的信息：基于深度学习的语义理解模型


### 不断变化的用户喜好
对用户行为的存储有实时性
推荐算法要考虑到用户近期行为和长期行为
要不断挖掘用户新的兴趣爱好

### Cross-domain recommendations

[TALLRec](../../4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/+Papers/TALLRec.md)（也自称LLM4Rec）研究了跨域推荐的问题，并没有什么特别的发现。只是说LLM有跨域推荐的能力（这不废话）。

## Techniques


传统的推荐系统模型通常会接收以下几种类型的输入：

1. **用户-物品交互数据**：这是推荐系统最基本的输入，包括用户对物品的评分、点击、购买等行为。这些交互数据可以帮助推荐系统理解用户的偏好。
2. **用户特征**：这可能包括用户的年龄、性别、地理位置、职业等人口统计信息，以及用户的浏览历史、购买历史等行为数据。
3. **物品特征**：这可能包括物品的类别、品牌、价格等属性，以及物品的描述、评论等文本数据。
4. **上下文信息**：在某些情况下，推荐系统还可能接收关于当前上下文的输入，例如用户当前的地理位置、时间、天气等。

这些输入数据被用来训练模型，以便预测用户对未接触过的物品的兴趣或评分。然后，系统会根据这些预测向用户推荐他们可能感兴趣的物品。


### Random recommendation policy

^c6dd12

A random recommendation policy is a type of recommendation system that recommends items to users at random. This type of system is often used in situations where there is not enough data to create a more personalized recommendation, or where the user wants to explore new content.

Random recommendation policies can be implemented in a variety of ways. One common approach is to simply generate a random list of items and recommend those to the user. Another approach is to use a random number generator to select a subset of items from a larger pool of possible items.

Random recommendation policies have a number of advantages. They are simple to implement and require little data. They can also be used to explore new content and find items that the user might not have otherwise found.

However, random recommendation policies also have a number of disadvantages. They can be less accurate than more personalized recommendation systems. They can also lead to users seeing the same items repeatedly.

Overall, random recommendation policies are a simple and effective way to recommend items to users. They are best suited for situations where there is not enough data to create a more personalized recommendation, or where the user wants to explore new content.

Here are some examples of when a random recommendation policy might be used:

-   A new user signs up for a service and has not yet provided any information about their interests.
-   A user is looking for new content to explore.
-   A user wants to be surprised by the recommendations they receive.

Here are some of the disadvantages of random recommendation policies:

-   They can be less accurate than more personalized recommendation systems.
-   They can lead to users seeing the same items repeatedly.
-   They can be less effective at engaging users.

## [LLM4Rec](../../4.%20Artificial%20intelligence/3.%20Applications/Recommender%20system/LLM4Rec.md)

基于LLMs的推荐系统


## News

### Twitter recommendation algorithm

马斯克也还算说到做到了，开源了推特的推荐系统。

GitHub页面：[Twitter Recommendation Algorithm](https://github.com/twitter/the-algorithm)

官方博客介绍：[Twitter's Recommendation Algorithm](https://blog.twitter.com/engineering/en_us/topics/open-source/2023/twitter-recommendation-algorithm)



## Appendices

### [议题](https://mp.weixin.qq.com/s/ET1z1Ys2luh_Yuc48EhAUg)

#### 议题一：推荐是科学问题，还是技术问题？

其中，中国人民大学的窦志成教授认为推荐系统是工程科学，目标是为了应用。它不同于普通的自然科学，自然科学是认知世界，而工程科学是改造世界。推荐系统旨在解决用户从海量信息中获取想要的信息。

中国科学技术大学冯福利教授则认为推荐系统的科学问题是其他领域（心理、社会）在信息科学里的延伸。推荐系统本身只是一个技术问题，旨在如何更服务好用户、社会和国家。他拿蒸汽机来作类比，认为二者背后都涉及科学问题内涵，即推荐背后是心理学、社会学，蒸汽机背后是物理学，推荐系统的设计类似于蒸汽机的设计，是一个技术问题，即如何更好的利用这些科学规律。

北京邮电大学的白婷助理教授认为推荐系统是一个复杂性的科学问题。推荐领域其实它的应用范围很广，从使用场景看，它其实是一个应用问题，比如优化一些CTR或者各种场景平台下的指标；如果我们从技术的角度看，它又是一个科学的问题，比如如何更好的理解用户的偏好。从推荐系统的影响来看，它又是一个社会学的问题，比如它是否会产生信息茧房，导致社会分裂等。综上，推荐系统其实是一个非常复杂性的问题。

清华大学张敏副教授认为推荐系统的科学问题是满足这个用户或这批用户当时的隐式的需求。推荐系统不同于搜索，有搜索词可以展示用户的显式需求。因此，推荐系统研究关键就在于研究用户的行为和偏好，挖掘用户隐式需求的规律。

合肥工业大学的吴乐教授拿自然语言处理、计算机视觉领域来做类比。这两个领域的科学问题是让机器理解人的语言、视觉世界。她认为推荐系统的科学问题是让机器理解人的行为，并对人的行为进行预测。

**电子科技大学的刘峤教授认为目前推荐系统所采用的研究方法大多是经验性的，用户行为建模的理论模型不完备，模型具有不可证伪性。他认为目前推荐系统正在尝试建立自己的理论体系，所以推荐系统所处的位置应该是on the way to science。**

山东大学的任昭春教授认为推荐系统和信息检索应该是紧密相关但互相并行的赛道。虽然二者都是建立人和信息之间的桥梁，推荐系统的目标是捕捉用户的隐式需求，而信息检索更多的是捕捉用户的显式需求，找到物品与搜索词进行匹配。此外，他也认为推荐系统不是机器学习的应用领域，因为人的需求难以被用机器学习定义。

个人观点：推荐系统是不清楚reward的信息检索

#### 议题二：如何看待学术界和工业界的鸿沟？

*似乎是一堆废话*

针对该议题，华为诺亚实验室的唐睿明主任首先发表了观点。他认为产生学术界和工业界的鸿沟的主要原因是因为价值观不统一。学术界往往追求较大的创新性，而忽视了一些细节问题。然而细节问题，比如对数值型特征的处理，往往对工业界系统的性能起着较大的作用。同时，他也认为解决鸿沟最有效的方式是让工业界发布一些特定的数据集，来让学术界人员一起进行研究。

上海交大的张伟楠副教授认为学术界和工业界的鸿沟是一直存在的，完全消除鸿沟也不现实。学术界和工业界在研究目标、研究资源上有自己的倾向性。他认为可以保持求同存异的方式，沿着同一个大方向上进行合作。此外，学术界可以尝试做一些更加超前，更加有创新性的研究工作。

武汉大学的李晨亮副教授也有类似的看法。他认为学术界和工业的出发点不同，学术界以单点突破为主，深入探索和开发公开的数据集，以发文章讲好故事为目标。而工业界以业务增长为目标，研究相对保守，可以使用的特征多样，场景也更为丰富。因此，他认为学术界应该要派学生走入工业界，同时，工业界也应该反馈学术界，积极发布一些新的数据集。

南洋理工大学孙爱欣副教授认为学术界也非常保守。一篇文章如果模型性能不能超越现有方法，将会非常难以发表。同时，学术界的工作想要反驳之前的文章也非常困难，导致前人的工作如果有错误的设定，不准确的评估标准，反而会一直沿用下去。      

清华大学的丁璟韬博士后也认为鸿沟不可避免，工业界考虑的是“系统”二字，而学术界考虑更多的是“推荐”二字。他认为要解决鸿沟，学生驻场实习非常有必要，可以一起确认问题，一起解决问题。此外，他认为学术界的高质量的论文可以对工业界的应用有所启发，他认为学术界的研究可以多关注和讨论提出的模型适用于哪种场景。

微软亚洲研究院谢幸研究员认为学术界和工业界出现鸿沟这个现象可能从一个角度说明了工业界技术趋向成熟，已经走在了学术界前面。与数据库和操作系统这些领域不同，尽管技术也很成熟，学术界仍然可以通过自己开发大系统的做法弥补这个鸿沟。真实的推荐场景不光需要有大系统，还需要有大规模用户，这点超出了学术界能力。学术界的研究可以更加超前一些，做一些现在觉得还不实际的事，就像30多年前，最早的推荐系统是在互联网还没有出现，甚至个人电脑还没有广泛普及的时代就提出了。

清华大学张敏副教授也有类似的看法。她认为工业界和学术界还是挺有差异的，所以有很多可以合作的地方。工业界重点是系统性、集成；学术研究是针对一个点，有核心影响的点。二者有很多机会融合和互补。也是我们一直以来和公司合作的原则，是可以双赢的。来自工业界的需求和数据，是学术界必须认真考虑和学习的。不可避免，也没有必要避免。此外，她认为学术界一定要有长远的眼光，而不是与工业界解决同样的问题。

#### 议题三：下一代推荐系统会怎样？

针对推荐系统的未来，YOCSEF总部前任主席、清华大学崔鹏副教授认为当前推荐系统脱离了初衷，用户主体性在技术演进过程中丧失了。推荐系统已经控制了人们会获取什么样的信息，他的社会影响、以及他带来的偏差问题依旧未可知。**在未来，推荐系统应该要让步一些主体性给用户，让用户能够控制自己想要获取的信息。（reward在用户）**

**快手的宋洋博士指出了推荐系统的几个未来研究方向，包括：1）隐私计算和端上计算，以保护用户隐私的同时也可以实现更加实时的计算；2）软硬件协同的推荐系统，以提升推荐系统的算力和精度；3）未来虚拟现实和增强现实的场景下的推荐系统。 **

阿里的马坚鑫博士也认为元宇宙下的推荐系统是一个值得研究的问题。元宇宙下可以捕捉更多的用户信息，更好的理解用户的隐式需求；同时，元宇宙下的推荐系统会更加拟人化，提高推荐转换率和用户体验。

YOCSEF长沙学术秘书、湖南大学姜文君教授认为目前推荐系统主要满足信息消费型服务为主。她认为未来可以探索助力成长型的推荐系统，推荐系统应该可以助力人们在交互过程中获取知识，提升技能。

YOCSEF广州前任主席、中山大学王昌栋副教授认为推荐系统也考虑国家、社会等因素，可以利用推荐系统促进主流价值观传播、助力国家治理、引导人们积极向上。

山东省人工智能研究院的程志勇研究员认为未来推荐系统需要考虑全方位的公平性。比如，用户、物品、还有商家之间的公平性。同时，他也强调信息舆论引导方面也要考虑公平性，防止极化的信息导致用户极化的思维。





### Sequential dependencies

推荐系统中的序列依赖（sequential dependencies）是指用户在连续的时间窗口内，他们的行为和兴趣受到先前行为和兴趣的影响。在动态推荐场景中，用户的兴趣和行为是随时间而变化的，因此需要考虑时间顺序的影响。捕捉这种序列依赖可以帮助推荐系统更好地理解用户随时间变化的兴趣和需求，从而提高推荐的准确性和个性化水平。

以下是一些具体的例子来帮助您更好地理解序列依赖这个概念：

1.  电影推荐： 假设一个用户最近连续观看了几部科幻电影。这时，他可能更倾向于观看与科幻主题相关的电影。捕捉这种序列依赖可以帮助推荐系统在短期内为用户推荐更相关的科幻电影。
2.  新闻推荐： 用户在阅读新闻时，可能对某一事件或话题有浓厚兴趣。例如，如果用户最近关注了一场体育比赛并阅读了有关该比赛的多篇文章，那么推荐系统捕捉到这种序列依赖后，可以在短期内为用户推荐更多相关的赛事报道。
3.  电商推荐： 在电商场景中，用户购买的商品通常具有一定的序列依赖。例如，一个用户先后购买了手机和手机壳，那么他很可能接下来会对手机配件感兴趣。捕捉这种序列依赖有助于为用户推荐更符合其需求的商品。
4.  音乐推荐： 用户在收听音乐时，可能会根据心情或特定场合选择不同类型的音乐。例如，一个用户在周末晚上可能更倾向于收听轻松愉快的音乐。捕捉这种序列依赖可以帮助推荐系统为用户提供更符合其当前需求的音乐推荐。
5.  旅游推荐： 在旅游推荐场景中，用户可能会按照一定的顺序访问不同的景点。例如，在游览一个城市时，用户可能会优先参观离酒店较近的景点。捕捉这种序列依赖有助于为用户提供更合理的旅游路线推荐。

以上例子表明，在推荐系统中，序列依赖是一种重要的信息，有助于更好地理解用户随时间变化的兴趣和需求。通过捕捉和利用序列依赖，可以提高推荐系统的准确性和个性化水平。

为了捕捉序列依赖，推荐系统研究者通常会采用以下方法：

1.  循环神经网络（RNN）： RNN是一种能够处理序列数据的神经网络，通过循环结构捕捉序列中的依赖关系。在推荐系统中，可以使用RNN来建模用户的历史行为序列，从而挖掘用户随时间变化的兴趣。
2.  长短时记忆网络（LSTM）： LSTM是一种特殊的RNN，它解决了RNN在处理长序列时出现的梯度消失或爆炸问题。LSTM可以更有效地捕捉长期依赖关系，因此在推荐系统中具有更好的性能。
3.  Transformer模型： Transformer模型使用自注意力机制处理序列数据，可以并行计算和捕捉长距离依赖关系。通过对用户行为序列进行建模，可以捕捉用户随时间变化的兴趣。
4.  注意力机制： 注意力机制可以为序列中的每个元素分配不同的权重，从而根据重要性捕捉局部和全局依赖关系。在推荐系统中，可以使用注意力机制对用户的历史行为序列进行建模，以捕捉关键的序列依赖。
5.  Session-based推荐： Session-based推荐关注用户在单个会话中的行为序列。通过分析用户在短时间内的行为模式，可以挖掘用户的短期兴趣和需求，从而提高推荐的实时性和个性化程度。

总之，在推荐系统中，序列依赖是一种重要的信息，有助于更好地理解用户随时间变化的兴趣和需求。通过采用上述方法，可以有效地捕捉和利用序列依赖来提高推荐系统的性能。

在某些情况下，用户对推荐内容的偏好可能不会严格遵循序列依赖。换句话说，用户的兴趣和行为可能在不同时间点上有所波动，而不是严格按照时间顺序逐渐演变。因此，这提示我们在设计推荐系统时，需要考虑到这种可能性。

为了解决序列依赖可能不严格遵循的问题，可以尝试以下策略：

1.  考虑长期和短期兴趣：区分用户的长期兴趣和短期兴趣，并在推荐系统中同时考虑这两个方面。这样，即使用户在某个时间点的行为与之前的序列模式不一致，推荐系统仍然可以为他们提供合适的推荐内容。
2.  引入注意力机制：使用注意力机制为用户行为序列中的每个元素分配权重，从而捕捉局部和全局依赖关系。这有助于在不严格依赖序列依赖的情况下捕捉用户的兴趣和行为。
3.  使用非线性模型：考虑使用非线性模型（如神经网络）来捕捉用户兴趣的复杂性和多样性，而不是简单地依赖于线性序列模型。
4.  将上下文信息纳入考虑：在推荐系统中引入用户的上下文信息（如地理位置、时间等），以便更好地理解用户的需求，从而为他们提供更合适的推荐内容。

总之，当序列依赖不能严格遵循时，可以通过修改基于MLM目标的推荐方法来提高推荐系统的性能。这包括考虑用户的长期和短期兴趣、引入注意力机制、使用非线性模型以及将上下文信息纳入考虑。




