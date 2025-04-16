这篇论文《**Hit the Sweet Spot! Span-Level Ensemble for Large Language Models**》提出了一种新的大语言模型（LLM）集成方法 **SWEETSPAN**，主要解决了现有集成方法在粒度与信息量之间难以平衡的问题。以下是对其**核心方法**部分的总结：

---

## 🧠 方法核心概述：SWEETSPAN

SWEETSPAN 是一种**无需训练的 Span-Level 集成方法**，旨在在不牺牲细粒度修正能力的前提下，提高 LLM 集成质量与决策准确性。其方法流程包括两步：

### 1️⃣ Span 生成（Span Generation）
- **每个候选模型**独立基于相同的 shared prefix（共享上下文）生成一个**定长的 word-level span**；
- **不跨越单词边界**，避免不同模型 tokenizer 差异带来的噪声；
- 所有 span 长度一致（如4个词），以便后续对比。

### 2️⃣ Span 选择（Span Selection）
- **每个模型计算所有候选 span 的困惑度（Perplexity）**，用于交叉评估；
  - PPL（perplexity）越低，说明该模型对该 span 的语言拟合度越高；
  - 每个 span 的最终得分是其他模型对它的 PPL 取平均；
- **引入过滤机制**：对异常 PPL 值（如自身 span 的低估或他人 span 的高估）进行剔除，以减少低质量模型干扰：
$$
  C_j = M \ R_j \quad \text{if} \quad \max(PPL) / \min(PPL) > \lambda
$$
- 最终选择 **平均 PPL 最低的 span** 作为当前轮的输出，接到 prefix 后继续下一轮生成。

---

## ✅ 方法优势

### 🌟 平衡信息量与灵活性
- 相较于 token-level 集成（信息太少）和 sample-level 集成（无法在线修正），span-level 既保留了上下文信息，又可逐步修正。

### 🔧 不依赖额外训练
- SWEETSPAN **完全训练无关**，适用于任意开放 LLM 组合，无需额外蒸馏或对齐词表。

### 🧩 跨模型无障碍集成
- 通过 word-level span 和 tokenizer 无关性，**天然规避 vocab mismatch 的问题**，在异构模型中表现出色。

---

## 🧪 实验设置

### 任务：
- **常识问答（NQ）**
- **算术推理（GSM8K）**
- **代码生成（MBPP）**
- **机器翻译（Flores101 多语言）**

### 模型：
- 7 个开源 7B 左右的 chat 模型，如 LLaMA2-7B-Chat、Baichuan2、Vicuna、InternLM 等

### 对比方法：
- Sample-Level：PairRanker、LLM-Blender
- Token-Level：EVA

---

## 📊 结果分析

- 在**标准设置（选最优模型组合）**下，SWEETSPAN 在所有任务上**均超过了其他方法与单一模型**；
  - 如 GSM8K 中比最佳模型高 **+12.81%**；
- 在**挑战设置（引入弱模型）**中，其他方法普遍退化，SWEETSPAN 仍然表现稳定，体现出其 **鲁棒性**；
- 消融实验表明：**过滤机制对提升效果尤其关键**，尤其在存在差模型时可以显著提升性能（如 MBPP 中提升超10%）。

---

## 📌 方法一句话总结

**SWEETSPAN 是一种无需训练、基于 word-span 的 LLM 集成方法，结合困惑度交叉评分与过滤机制，实现信息充分利用与动态修正的平衡，在多种任务中表现优异。**
