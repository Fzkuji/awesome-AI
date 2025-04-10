## Harnessing Multiple Large Language Models: A Survey on LLM Ensemble

### Ensemble-before inferece

#### Pretrained router






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

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/LLM%20ensemble/IMG-20250410175428997.png)

##### PickLLM

*2024.12* Gradient Ascent维护一个模型选择概率向量，或者Stateless Q-Learning维护一个动作值向量，小概率随机探索其他模型。

##### Blending

*2024.01* 这个竟然是随机选一个模型来回答问题，他们认为这样就够了，足够每个模型发挥其特性。然后他们测试完说他们的用户留存率更高。

### Ensemble-during inference




### Ensemble-after inference





## 不知道什么时候的东西

- Bridging the Gap between Different Vocabularies for LLM Ensemble
    - Label: 
    - Author: Yangyifan Xu, Jinliang Lu, Jiajun Zhang
    - Link: https://arxiv.org/pdf/2404.09492
    - Code: https://github.com/xydaytoy/EVA
    - Pub: NAACL 2024
    - Summary: 具体来说，我们首先在重叠令牌的帮助下学习不同LLM词汇之间的映射。随后，这些映射被用于将LLM的输出分布投射到一个统一的空间中，促进了精细的合奏。最后，我们设计了一个过滤策略，以排除生成不忠令牌的模型。




