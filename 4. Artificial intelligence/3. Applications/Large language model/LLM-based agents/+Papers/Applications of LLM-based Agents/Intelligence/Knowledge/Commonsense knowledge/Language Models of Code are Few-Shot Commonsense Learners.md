这篇论文讨论了在结构性常识推理任务中使用代码生成模型（Code-LLMs）比使用自然语言模型（NL-LLMs）更有效的方法。以下是论文的中文总结：

### 论文标题
**代码语言模型是少量示例的常识学习者**

### 作者
Aman Madaan, Shuyan Zhou, Uri Alon, Yiming Yang, Graham Neubig

### 摘要
论文研究了结构化常识推理任务，即从自然语言输入生成事件图或推理图。传统方法将输出图序列化为节点和边的平面列表，但这些序列化图与预训练数据的自然语言文本差异很大，导致语言模型生成不准确。论文提出将结构化常识推理任务转换为代码生成任务，即使用预训练的代码语言模型进行推理。实验证明，在少量示例的设置下，代码生成模型在结构化常识推理任务上表现优于自然语言模型。

### 主要贡献
1. **洞察**：代码语言模型在表示为代码格式的图预测任务中优于自然语言模型。
2. **方法**：提出了COCOGEN（使用代码语言模型进行常识生成）方法，将输出图转换为等效的代码形式。
3. **评估**：在三个结构化常识生成任务中进行广泛评估，证明COCOGEN在少量示例设置下显著优于自然语言模型。

### 评估任务
1. **脚本生成（PROSCRIPT）**：生成表示高层次目标的脚本图，实验结果表明COCOGEN在生成脚本和预测边任务中均表现出色。
2. **实体状态跟踪（PROPARA）**：预测在执行一系列动作后实体的状态，COCOGEN在少量示例设置下显著优于其他模型。
3. **解释图生成（EXPLAGRAPHS）**：根据论点生成支持或反对信念的解释图，COCOGEN在30个示例下优于在1500个示例上微调的T5模型。

### 结论
论文表明，将结构化常识推理任务转换为代码生成任务，可以充分利用代码语言模型的优势，从而在少量示例下实现更好的性能。这一方法在多个任务上都取得了显著的效果，展示了代码语言模型在结构化推理中的潜力。

***Handling multiple papers?*** 

Speed up your research with Sider! Our AI-powered sidebar features 10+ one-click tools including a more advanced Search Agent, ChatPDF, context-aware utilities, and more to help you work smarter and faster.
 [Level up your research game here](https://bit.ly/4aSnMXa)