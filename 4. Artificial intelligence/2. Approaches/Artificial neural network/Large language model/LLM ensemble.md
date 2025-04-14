## Harnessing Multiple Large Language Models: A Survey on LLM Ensemble

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250414001606785.png)

### Ensemble-before inferece

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250413175125978.png)

#### Pretrained router

**Baseline**: Oracle (Upper bond)
**Datasets**: MixInstruct, MMLU, MT Bench, GSM8K, RAFT, WikiFact, Entity matching, Data imputation, BoolQ, TruthfulQA, IMDB, bAbI, MATH, LSAT, LegalSupport, CivilComments
**Results**: 通常多模型下离Oracle有较大差距，但双模型下可以在不降低性能的情况下有效减少推理成本。

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250413165824934.png)
Figure 5: Cost–accuracy plot per task. (FORC)

##### FORC

WSDM'24 [FORC](https://arxiv.org/abs/2308.06077) 1）基于历史数据（真实模型回答 + ground truth），使用 **DistilBERT** 等轻量模型进行回归或分类训练。2）得出每个模型在每个 query 上的预计开销 $c_{i j}$ 。3）根据上一步得到的 **性能预测** $p_{i j}$ 和 **成本估计** $c_{i j}$ ，采用优化策略来选择“哪个模型处理哪个输入”。

*针对多个模型来建模*，使用了MMLU, RAFT, WikiFact, Entity matching, Data imputation, BoolQ, TruthfulQA, IMDB, bAbI, MATH, GSM8K, LSAT, LegalSupport, CivilComments。

##### Hybrid LLM

ICLR'24 [Hybrid LLM](https://openreview.net/forum?id=02f3mUtqnM)分别让大模型和小模型给查询生成多个答案（通常各采样10个），用 **BART score** 衡量每个答案的质量；然后计算**小模型和大模型答得差多少**（称为“质量差距”）；最后用这些差距训练一个 路由器模型（用 DeBERTa 作为 backbone），让它学会“这个问题适不适合让小模型来答”，最后在MixInstruct数据集上验证。*针对两个模型来建模*


##### MetaLLM

arXiv 24.07 [MetaLLM](https://arxiv.org/abs/2407.10834)将模型选择问题建模为一个**多臂赌博机（Multi-Armed Bandit）问题**。MetaLLM 使用 Sentence-BERT 提取 query 表征，并训练一个简单的线性模型来预测每个 LLM 的期望 reward（精度）。在 SST-2 和 IMDB 上实现高达 **60% 的成本节约**，亦或是更优的推理性能（但是不是很明显）。

##### RouteLLM

笔记：[RouteLLM Learning to Route LLMs from Preference Data](+Papers/RouteLLM%20Learning%20to%20Route%20LLMs%20from%20Preference%20Data.md)

ICLR'25 [RouteLLM](https://openreview.net/forum?id=8sSqNntaMr) 提出了一种基于人类偏好数据训练的胜率预测模型，用于在强模型和弱模型之间智能路由查询，最大化性能同时降低推理成本。其核心通过设定概率阈值 α 控制路由决策，并设计了 PGR、APGR 和 CPT 等指标衡量性能-成本权衡。
Win prediction model包括：
- Similarity-weighted (SW) ranking
- Matrix factorization
- BERT classifier
- Causal LLM classifier

实验在MMLU, MT Bench, GSM8K三个benchmarks上证明前面两个简单的方法性能更好。该方法在多个任务上可显著减少强模型调用（最多节省 75% 成本），且具有良好的跨模型泛化能力。*针对两个模型来建模*



#### Non-pretrained router

##### Eagle

*2024.09* 论文提出使用 **“ELO 排名”** 系统（类似国际象棋评分系统）来评估多个模型的相对表现。

**创新点在于将模型能力拆为两部分：**

- **Global Ability（通用能力）**：模型在整体任务集上的普遍表现。
- **Local Ability（特定任务能力）**：模型在与当前输入相似历史任务中的表现。

这两个模块结合使用，组成 Eagle 路由策略。

**Eagle 工作机制分解**

1. 输入请求 embedding → 向量化后从数据库中找到最相似的历史查询；
2. 从历史对比反馈中提取模型表现数据（类似“GPT4 > GPT3.5”、“Claude = GPT3.5”等）；
3. 使用 **ELO算法** 分别计算：
    - **Eagle-Global**：从所有历史对比中统计模型通用表现；
    - **Eagle-Local**：只使用“相似历史任务”的反馈，统计特定场景表现；
4. 综合评分：$$\operatorname{Score}(X)=P \cdot \operatorname{Global}(X)+(1-P) \cdot \operatorname{Local}(X)$$
5. 按预算选择当前最优模型进行推理；
6. （可选）再让用户对当前输出和另一个模型的输出打分，反馈写入数据库；
7. 下一次调用可即时更新，无需模型重训。

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250410192158665.png)

##### PickLLM

*2024.12* Gradient Ascent维护一个模型选择概率向量，或者Stateless Q-Learning维护一个动作值向量，小概率随机探索其他模型。

##### Blending

*2024.01* 这个竟然是随机选一个模型来回答问题，他们认为这样就够了，足够每个模型发挥其特性。然后他们测试完说他们的用户留存率更高。

### Ensemble-during inference

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250413175148701.png)

**Baseline**: 
**Datasets**: MMLU, GSM8K, TriviaQA
**Results**: 

#### Token-level ensemble

分为Aggregation-based和Selection-based。这个比较直观，就是将结果整合，还是选取其中一个。

##### DeePEn

NeurIPS'24

将不同模型vocab中相同的token作为anchor，用LLM输入层embedding，计算所有token到anchor token的相似度。得到一个映射矩阵，设：
- 模型词表有 4 个词：`["apple", "banana", "car", "dog"]`；
- 锚点词为：`["apple", "dog"]`；
- 当前模型输出的 token 分布为：
  $$\mathbf{p} = [0.1, 0.6, 0.2, 0.1]$$
假设我们预先计算好的（归一化后的）相对表示矩阵 $\hat{R}$ 是：

| token    | anchor: "apple" | anchor: "dog" |
| -------- | :-------------: | :-----------: |
| "apple"  |       0.8       |      0.2      |
| "banana" |       0.7       |      0.3      |
| "car"    |       0.3       |      0.7      |
| "dog"    |       0.1       |      0.9      |

然后将所有token的输出权重和表示矩阵相乘，得到anchor token的权重，即在锚点词空间下的“语义投影”：
$$\mathbf{r}=\mathbf{p} \cdot \hat{\mathbf{R}}$$
最后不同模型的语义投影可以加权平均：
$$
\mathbf{\hat{r}}=\sum_{i=1}^N \alpha_i \cdot \mathbf{r}_i
$$
得到最终的语义投影后，我们把其中一个模型作为基准模型，想办法通过梯度下降，找到一个$\mathbf{p}$，使得：
$$\mathbf{\hat{r}}=\mathbf{p} \cdot \hat{\mathbf{R}}$$
数据集：MMLU, GSM8K, TriviaQA
*可以用于多个模型，通常可以找到一个最优解，但是无法保证基于哪个模型作为backbone可以得到最优解，且所有模型都需要推理，cost较大。*






##### CITER

*Token-Level routing方法*

COLM'25 CITER 将token routing 问题建模为一个马尔可夫决策过程（MDP），使用基于偏好（preference-based）的强化学习方法训练路由器。

偏好获取：

| 情况     | 描述          | 偏好标签 $p$（即训绕 label）                   |
| :----- | :---------- | :------------------------------------ |
| Case 1 | SLM 正确      | $p=1$（即偏好 SLM）                        |
| Case 2 | SLM 错，LLM 对 | $p=0$（偏好 LLM）                         |
| Case 3 | 两者都错        | 继续这个预测流程 CITER 解码，若最终结果对，则用SLM，反之用LLM |

实验在多选题Commonsense QA (CS QA), ARC-Challenge, MMLU-PP和问答题GSM8k, MATH上进行了测试，论文使用accuracy vs data transformation amount curve来衡量方法的效果。结果是比RouteLLM和Co-LLM要好一些。

#### Span-level ensemble






#### Process-level ensemble






### Ensemble-after inference

#### Non-cascade

##### Selection-based

###### Agent-Forest

TMLR'24 [Agent-Forest](https://openreview.net/forum?id=bgzUSZ8aeg)是一种两阶段流程的推理增强框架，首先对多个模型进行（多次）采样，然后计算所有答案中，和其他回答相似度最高的那个，作为Majority Voting。

![500](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/LLM%20ensemble/IMG-20250414140838739.png)



###### MoRE


##### Selection-then-regeneration





#### Cascade

##### Unsupervised





##### Supervised





## 不知道什么时候的东西

- Bridging the Gap between Different Vocabularies for LLM Ensemble
    - Label: 
    - Author: Yangyifan Xu, Jinliang Lu, Jiajun Zhang
    - Link: https://arxiv.org/pdf/2404.09492
    - Code: https://github.com/xydaytoy/EVA
    - Pub: NAACL 2024
    - Summary: 具体来说，我们首先在重叠令牌的帮助下学习不同LLM词汇之间的映射。随后，这些映射被用于将LLM的输出分布投射到一个统一的空间中，促进了精细的合奏。最后，我们设计了一个过滤策略，以排除生成不忠令牌的模型。




