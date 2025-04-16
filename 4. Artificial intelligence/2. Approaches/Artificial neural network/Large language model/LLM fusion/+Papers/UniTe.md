这篇论文《**Determine-Then-Ensemble: Necessity of Top-k Union for Large Language Model Ensembling**》的核心目标是提升**大语言模型（LLMs）集成（ensembling）方法的性能与效率**。作者识别了现有方法的两大问题并提出了全新的**UNITE方法**，通过**只融合各模型 Top-k token 的概率分布**来规避全词表对齐带来的计算负担。

---

## 🧠 一、主要方法总结

### 1.1 模型选择策略（Determine-Then-Ensemble）

作者首先强调，**不是所有模型都适合被集成**。他们提出一个两步策略：

1. **选择主模型**：从候选模型池中选择目标任务表现最好的模型。
    
2. **逐步加入兼容模型**：选择性能差距小于 10%，且**响应风格（response style）一致**的模型（比如文本长度差距不大），以确保兼容性。
    

➡️ **Vocabulary size 不再作为判断条件**，因为作者发现词表大小对最终性能影响非常小。

---

### 1.2 UNITE 方法：Union Top-k Ensembling

传统概率级集成方法（如 DEEPEN、GAC）需对齐整个词表（数万个token），计算开销巨大。而 UNITE 提出了一种**只对齐各模型 Top-k token 的方法**，称为：

> **UNIon Top-k Ensembling (UNITE)**

#### 核心流程如下：

1. **各模型独立解码当前输入，输出 Top-k token 概率（TopKi）**；
    
2. **构建联合集合 Vu**：合并各模型 Top-k token（去重）；
    
3. 对于 Vu 中的每个 token，按以下规则处理其概率：
    
    - 若该 token 同时在模型 TopKi 中出现，则直接使用；
        
    - 若 token 属于模型词表但不在 TopKi 中，则取其原始概率加入；
        
    - 若 token 不在该模型词表中，则用 tokenizer 拆分为若干子 token，保留第一个子 token 的概率；
        
4. 所有模型的 TopK̂i 概率归一化，取平均；
    
5. **最终预测使用平均概率最大值 token**。
    

📌 **优势**：

- 极大减少计算量（每步仅操作约 14 个 token）；
    
- 避免因 tokenizer 差异而忽略潜在好 token；
    
- 能容忍词表差异，支持异构模型（包括 MoE 和 dense 混合模型）；
    
- 无需辅助映射矩阵或训练。
    

---

## 🧪 二、实验与对比结果

实验涵盖 6 个 benchmark，包括 reasoning、QA 和知识问答等任务。

|方法|平均提升|说明|
|---|---|---|
|**UNITE**|↑ 最多 +4.21%|在多个任务中超越 DEEPEN、GAC 和 LLM-BLENDER|
|**DEEPEN**|OOM/下降|词表过大时易崩溃|
|**GAC**|较差性能|计算复杂，效果一般|
|**LLM-BLENDER**|对个别任务有效|但对 response 风格差异敏感|

此外，作者也在 LLaMA3 + Qwen2 等多模型组合上测试了 **UNITE 的通用性和多语言支持能力（如 CMMLU）**，结果依然强劲。

---

## 🔍 三、对你提到的 UniTe 和 PackLLM 的关系说明

作者在正文中指出：

> “Furthermore, there are several alternative methods that do not prioritize dictionary alignment as their core objective: **UniTe [Yao et al., 2024]** and **PackLLM [Mavromatis et al., 2024]** directly adopt existing alignment techniques ...”

即：

- **UNITE 方法虽然避开了全词表对齐，但仍通过 token 映射策略实现轻量级对齐**；
    
- 相比之下，PackLLM 等方法可能仍然依赖现有全局对齐机制（如映射矩阵），但并未特别优化 token 粒度融合策略。
    

UNITE 在**融合粒度（top-k token 层面）**和**融合逻辑（跨 tokenizer token 重组）**上更具创新性。

---

## ✅ 总结：核心贡献

1. **提出了模型选择标准**（性能差距 < 10%、风格一致），揭示了词表大小影响微弱；
    
2. **设计了 UNITE 方法**，突破词表对齐瓶颈，仅使用 top-k token 实现高效融合；
    
3. **实验覆盖广泛任务和模型组合**，验证了 UNITE 的性能提升、通用性与效率。
    

如果你还想对照 PackLLM 方法进一步比较或找原文我也可以帮你拉来一起对比。需要我继续吗？