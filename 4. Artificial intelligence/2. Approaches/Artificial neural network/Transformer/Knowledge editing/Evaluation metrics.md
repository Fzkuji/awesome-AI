# Evaluation metrics（模型编辑评估指标）

| 指标             | 含义                        | 目标                    |
| -------------- | ------------------------- | --------------------- |
| Reliability    | 编辑任务是否成功执行                | 模型是否准确地插入新知识          |
| Generalization | 编辑后的知识在不同表述/上下文下的表现       | 模型能否理解新知识的不同语义重述及逻辑推导 |
| Locality       | 编辑是否影响其他无关知识              | 确保无关知识不被破坏            |
| Portability    | 编辑后的知识能否迁移到推理、同义词泛化和逻辑泛化中 | 模型能否基于新知识进行推理和关联      |
| Efficiency     | 编辑任务的时间和内存消耗              | 优化编辑任务的计算开销，确保可用性和高效性 |

这些概念是模型编辑评估指标，用来衡量模型在执行**知识插入**或**知识编辑**任务时的效果和影响。以下是详细解读：

## 1. Reliability（可靠性）

### 定义
* **可靠性**衡量**编辑操作是否成功执行**，即模型是否能够根据**指定的编辑描述符**（editing descriptor）正确地更新知识
* **Editing Descriptor**：具体描述要编辑的知识，包括 `subject`、`relation` 和 `target_new`

### 示例
假设有编辑任务：

```text
Subject: Space Needle  
Editing Target: Located in → Paris
Prompt: "The Space Needle is located in which city?"
```

* 如果编辑后的模型能够回答 "Paris"，则视为成功
* 可靠性计算公式：

$$Reliability = \frac{成功编辑的样本数}{总编辑样本数}$$

## 2. Generalization（泛化性）

### 定义
* **泛化性**衡量编辑后的知识能否在**不同语义表述**或**相关上下文**中成功应用
* 即：在**编辑范围（scope）**内，模型能否正确生成与新知识一致的输出

### 编辑范围
* **原始Prompt**：直接测试新知识
* **重述Prompt（Paraphrase）**：语义等价但表达方式不同
* **逻辑推理Prompt**：测试新知识的逻辑衍生，例如常识推理或简单逻辑关系

### 示例
编辑任务：

```text
Subject: Space Needle  
Editing Target: Located in → Paris
Prompt: "The Space Needle is located in which city?"
```

泛化性测试：
1. 原始Prompt：The Space Needle is located in which city? → Paris ✅
2. 重述Prompt：What city is the Space Needle located in? → Paris ✅
3. 推理Prompt：The Space Needle is a landmark of which country? → France ✅

泛化性计算公式：

$$Generalization = \frac{编辑范围内成功的样本数}{编辑范围内总样本数}$$

## 3. Locality（局部性）

### 定义
* **局部性**衡量模型在执行知识编辑后，**无关输入**的输出是否发生变化
* 即：编辑一个知识点后，模型的其他知识是否保持稳定

### 为什么重要？
* 编辑某个知识点时，理想情况下，模型不应影响与编辑内容**无关**的其他输出

### 示例
编辑任务：

```text
Subject: Space Needle  
Editing Target: Located in → Paris
```

测试局部性：
1. **原始Prompt**：The Space Needle is located in which city? → Paris ✅
2. **无关Prompt**：The Eiffel Tower is located in which city? → Paris ❌ （这里出现错误，表明模型对其他无关知识也产生了影响）

局部性计算公式：

$$Locality = \frac{无关输入保持正确的样本数}{无关输入总样本数}$$

## 4. Portability（可迁移性）

### 定义
* **可迁移性**衡量编辑后的知识是否能被应用于**推理**或**更广泛的上下文**，包括：
   1. **One-Hop 推理**：直接基于编辑知识推导出的结果
   2. **Synonym 泛化**：测试同义词或等价表达下的结果
   3. **逻辑泛化**：测试与知识逻辑相关的派生结果

### 示例
编辑任务：

```text
Subject: Space Needle  
Editing Target: Located in → Paris
```

可迁移性测试：
1. **One-Hop 推理**：
   * "The Space Needle is a landmark in France." → True ✅
2. **Synonym 测试**：
   * "What is the capital where the Space Needle is located?" → Paris ✅
3. **逻辑泛化**：
   * "Which country does the Space Needle belong to?" → France ✅

可迁移性计算公式：

$$Portability = \frac{成功推理/泛化的样本数}{总测试样本数}$$

## 5. Efficiency（效率）

### 定义
* **效率**衡量执行知识编辑任务所消耗的**时间**和**内存资源**

### 为什么重要？
* 对于大规模模型（如 GPT 系列），编辑任务的时间和内存消耗是评估方法可行性的关键因素
* **理想目标**：在不牺牲模型性能的前提下，尽量减少计算开销

### 衡量标准
* **时间**：完成知识编辑所需的时间（秒/分钟）
* **内存**：所需的内存使用量（MB/GB）

## 总结

这些指标是评估模型编辑方法（如 ROME、MEND、SERAC 等）的标准，确保模型编辑在**准确性**、**稳定性**、**泛化能力**和**效率**之间取得平衡。

