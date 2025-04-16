这篇论文 **《Pack of LLMs: Model Fusion at Test-Time via Perplexity Optimization》**（PackLLM）提出了一种**无需训练、基于困惑度优化的多大语言模型融合方法**，重点在于**在推理阶段自动选择和融合多个 LLM 的知识**。以下是对其方法部分的系统总结：

---

## 🧠 方法核心思路：基于困惑度的测试时模型融合

PackLLM 旨在解决以下问题：

> **在推理时给定一组 LLMs，如何融合它们的知识以提升性能？**

### 🔧 整体框架

PackLLM 提出在推理时对多个 LLM 的输出 logits 进行加权融合：

p(xt∣x<t)=softmax(∑k=1Kλk⋅sk(xt∣x<t))p(x_t | x_{<t}) = \text{softmax} \left( \sum_{k=1}^{K} \lambda_k \cdot s_k(x_t | x_{<t}) \right)

其中：

- sks_k 是第 kk 个模型在当前 token 上的 logits；
    
- λk\lambda_k 是该模型的重要性权重（满足 ∑kλk=1\sum_k \lambda_k = 1）；
    
- 权重 λk\lambda_k 是根据“模型对当前输入理解程度”来分配的，**以最小化输入的困惑度（perplexity）**为目标。
    

---

## 📉 核心思想一：困惑度最小化

作者认为困惑度（PPL）能有效衡量一个模型是否“理解”输入。于是，PackLLM 设计了以下优化目标：

λ1∗,…,λK∗=arg⁡min⁡λPPLensemble(P)\lambda_1^*, \ldots, \lambda_K^* = \arg\min_{\lambda} \text{PPL}_{\text{ensemble}}(P)

该目标用于在 test-time 得出每个模型的权重分配 λk\lambda_k。

---

## 🧪 两种具体实现方法

### 1. **PackLLMsim**（简单版）

直接使用各模型在当前 prompt （输入文本）上的困惑度，反向归一化后用 softmax 得出权重：

λk=softmaxk(−log⁡PPLk(P)τ)\lambda_k = \text{softmax}_k\left( - \frac{\log \text{PPL}_k(P)}{\tau} \right)

- 优点：无需优化，速度快；
    
- 缺点：对 PPL 分布敏感，可能受噪声影响。
    

---

### 2. **PackLLMopt**（优化版）

提出一种**贪心算法（Greedy Grid Search）**，逐步融合模型，选择使整体困惑度下降最大的组合权重。

**步骤如下**：

1. 对所有模型计算困惑度，排序；
    
2. 取 PPL 最低的两个模型，枚举权重 $\lambda \in [0,1]$，计算组合后困惑度，选择最优；
    
3. 把最优组合视作“当前集成模型”，继续与下一个模型进行融合；
    
4. 直到遍历所有模型或提前终止（某模型融合权重为 0）；
    

该方法实现了较优的性能与较低的计算开销（线性时间复杂度）。

---

## 🔤 Tokenizer 对齐（Tokenizer Alignment）

由于不同 LLM 的 tokenizer 可能不同，PackLLM 提出：

1. 选择 PPL 最低模型的 tokenizer 作为参考；
    
2. 使用**最小编辑距离（MinED）映射**对其他模型的 token/logits 进行对齐；
    
3. 将所有 logits 映射回参考 tokenizer，统一处理。
    

---

## 🧪 实验亮点

- 在语言建模任务中，PackLLM 优于 cBTM 和 DExperts（如在 C4、S2ORC 数据集上）；
    
- 在 25 个下游任务上（如 MMLU、BoolQ、PubMedQA 等）平均提升 **1.72~1.89%** 准确率；
    
- 在与学习型融合方法（如 FuseLLM、FoE）比较中，当使用新发布模型时 **提升 3.92~11.94%**；
    
- 实验模型总数超 100 个，具备良好的**可扩展性与模块化优势**。
    

---

## ✅ 方法优势总结

|特性|PackLLM|
|---|---|
|是否训练|❌ 无需训练|
|是否可插拔|✅ 任意 LLM 组合|
|性能表现|✅ 超越 uniform ensemble 与 Top1-PPL|
|对新模型适应|✅ 可直接使用新发布模型|
|实施复杂度|中等（PackLLMopt 使用贪心法）|

---

## 📌 结论

PackLLM 提供了一种**轻量、高效、灵活**的 LLM 融合方法，能够充分利用多个预训练模型的互补能力，尤其适合**无标注数据、快速试验和模块化部署**的应用场景。

如果你还想了解它与 UniTe 的对比，我们可以继续从设计目标和适用范围上进行分析。需要我帮你整理 PackLLM 与 UniTe 的异同吗？