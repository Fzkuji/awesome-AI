


# A Survey on Memory in Foundation Models

Meta写的感觉有点乱，还是自己eng整理一个出来。



## Introduction

随着大语言模型（Large Language Models, LLMs）在自然语言处理领域的广泛应用，其处理长上下文、保持一致性和个性化输出的能力受到越来越多的关注。然而，传统 LLM 在记忆能力上存在局限，例如 Transformer 架构受限于固定上下文窗口，无法有效存储和调用长期信息。为了克服这些挑战，研究者们提出了多种方法来增强模型的记忆能力。这些方法可以根据存储和访问记忆的方式分为三大类：外部笔记（Retrieval-Augmented Generation, RAG）、长期记忆（模型参数）和短期记忆（隐藏状态）。外部笔记通过检索外部知识库扩展模型的知识范围，长期记忆通过更新模型权重保留信息，短期记忆则利用推理过程中的隐藏状态维持上下文相关性。本综述将系统分析这些方法的原理、实现方式及其应用场景，旨在为 LLM 记忆能力的研究提供清晰的框架。

## 记忆的两个维度

记忆的本质是信息处理和保存。首先，我们需要对可用的信息有一个明确的认识，然后我们才能决定利用、保存、编码哪些信息。这里我从两个维度对信息进行划分。

**维度一：信息的处理化程度** 文本本质上是一种已经被人类高度压缩的信息，足够精炼和简洁。而图像、声音、触觉等感官则更像是原始信息，是直接观察世界可以获取到的，这类信息通常需要进一步压缩来保证高效存储。因此，多模态大模型通常需要额外的模态encoder，将其他模态的信息对齐到文本。

因此我也不太认为文本信息属于纯粹的Sensory Memory，因为他们已经不是原始信息了，更像是高度处理后的知识（概念）。对于人类而言，文本的含义需要从图像或者对话中理解获得，这个过程本身就涉及到非常复杂的抽象能力。这也是为什么大语言模型获得了成功，但是多模态大模型实现则更加困难。

- 视频→图像→概念
- 音频→音频帧→概念

**维度二：信息的保存和丢弃** 基座大模型，尤其是多模态大模型，考虑到未来模型必须能够实时处理问题，每时每刻会获取到海量的原始信息，即Sensory Memory，因此我们需要考虑保存有效信息，适当丢弃不太重要的信息。

比较经典的就是Transformer和RNN的比较：

- Transformer可以选择保存所有的输入，因此获得了非常好的效果，但是效率和存储需求也更高
- 而RNN模型则不可避免地需要信息的更新和丢弃，因此效率更高，但是信息丢失更严重


## Memory Architecture

目前已经有很多大模型记忆的工作和总结，对于记忆的定义与分类也不尽相同。

- [A Survey on the Memory Mechanism of Large Language Model based Agents](https://dl.acm.org/doi/abs/10.1145/3748302)
	- 这篇应该是第一篇探讨大模型记忆的综述，但是整体focus在agent层面。
- [From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs](https://arxiv.org/abs/2504.15965)
	- 这篇论文提出了一个3D-8Q Memory Taxonomy，根据大模型记忆中明显的特征：记忆对象、记忆形式和记忆时间进行了划分，通过组合可以得到8个象限。
- [Cognitive Memory in Large Language Models](https://arxiv.org/abs/2504.02441)
	- 2025年4月，中国科学院团队发表
	- 参考人类的记忆形式，将记忆划分成Text-based、KV Cache-Based和Parameters-Based三个大类，逐渐接近记忆的本质。而在具体类别中，进一步阐述记忆的Acquisition、Management和Utilization。但是这篇论文的局限是，主要考虑的是基于Transformer架构的记忆，将其他模型架构划分为单独地一类Hidden-State-Based Memory。缺少对于现有技术更深刻的理解。
- [Rethinking Memory in AI: Taxonomy, Operations, Topics, and Future Directions](https://arxiv.org/abs/2505.00675)
	- 类似的，本文将记忆划分为参数化记忆和上下文记忆。他们重点探讨了六个基本记忆操作（Memory Operations），包括Consolidation（巩固）、Indexing（索引）、Updating（更新）、Forgetting（遗忘）、Retrieval（检索）和Compression（压缩）。最后总结了长期记忆、长上下文、参数修改和多源记忆四个核心研究主题。附录部分总结了众多相关数据集，很有价值。

人类的记忆目前主流的分类如下：

![](../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Cognition/Memory/LLM%20memory%20survey/IMG-20250529174334277.png)


在这些分类工作中，大部分工作都基于大模型在应用中的位置进行划分。然而在本篇工作中，我们从最基本的架构角度来分析。

为了更好地理解 LLM 的记忆机制，我们可以将这些类别与人类记忆系统进行类比。人类的记忆通常分为三类：长期记忆、工作记忆和外部知识，这与 LLM 的三类记忆形成有趣的对应关系。人类的：

**感觉知识**（Sensory Memory）则体现为人类感官的原始输入，对应于 LLM 的 RAG 方法，例如 MemoChat 和 Recursively Summarizing 通过检索或生成外部备忘录增强对话能力。这种映射不仅帮助我们理解 LLM 记忆设计的灵感来源，还揭示了其在模拟人类认知方面的潜力与局限。

**工作记忆**（Working Memory）是人类在短时间内处理和操作信息的临时存储空间，类似于 LLM 的隐藏状态（如 Transformer 的注意力机制或循环网络的状态），用于维持当前任务的上下文，例如 Controllable Working Memory 研究中控制模型的短期信息优先级。

**长期记忆**（Long-term Memory）存储于大脑神经网络中，包含事实、经验和技能，类似于 LLM 通过训练更新模型参数来固化知识的过程，例如 MemoryBank 通过持续学习适应用户个性化的需求。




## Sensory Memory

正如前面提到的，Sensory Memory应该是模型的原始输入，包括：图像、文本、音频等多种模态的原始感知数据。这些信息构成了模型在推理阶段的第一层“感官输入”，在类脑机制中对应人类接收外界刺激的初始阶段。每种模态的数据在进入模型后，都会分别通过对应的感知编码器（如视觉编码器、语言编码器、音频编码器等）被转化为统一的高维特征向量，作为跨模态对齐与融合的基础。

不过，文本模态的特殊性让我们意识到，需要不同的模块来处理不同类型的模态。一个核心的区分指标就是，信息是否具有时间属性，或者说，是否是连续的。人的任何感官，都可以看做连续的输入，比如每时每刻，眼睛都会接收新的图像（虽然也可以按照帧来划分）。但是文本序列则没有显式的时间属性，是相较而言是非常离散的，一个人说10个单词，可能需要几秒，但是要展现这10个单词给tokenizer，则是一瞬间的事情。一个视频、音频是连续的，而一张图片、一段文本则是离散的，只代表了现实世界的一个切面。这一差别让视频和文本无法直接进行注意力计算，现有的多模态模型都是截取视频中的部分帧，将离散的图片与文本处理成token，然后计算注意力。这也意味着现有的大模型都在处理离散数据，而无法处理连续的信息。

尽管如此，以上的所有信息都还是未经处理的，所有模型都可以基于这些信息进行训练而不需要额外的编码，因此可以作为预训练数据集。现在也有检索增强生成（Retrieval-Augmented Generation, RAG）来实现对Sensory Memory的存储和索引。这种技术可以理解为维护一个个体完整的经历。RAG特别适用于需要处理大量外部信息或长对话的场景。RAG 的核心思想是将生成过程与检索过程结合：模型首先从外部存储中检索相关信息，然后将其集成到生成过程中，避免了模型完全依赖内部参数存储所有信息的限制。RAG 的优势在于其灵活性和可扩展性，但也面临检索效率和信息一致性等挑战。

尽管名字叫感觉记忆，但这本质上是因为人类大脑无法完全存储这类信息，而计算机可以持久地保存感觉记忆，因此LLM的感觉记忆的存储量反而是最大的。

### 现有工作

暂时还需要对这部分进行总结，主要关注那些将RAG作为记忆的工作，而不是包含所有的检索方法。

#### 反思

- 2023.11 [Think-in-Memory: Recalling and Post-thinking Enable LLMs with Long-Term Memory](https://arxiv.org/abs/2311.08719)这个是较早期的工作，通过索引相关的内容，然后让模型对历史信息进行**反思**，达到等价的记忆效果。


#### 向量数据库

向量数据库保存了专门设计的文档格式，同时为文档计算嵌入向量用于密集索引，也会使用传统的NLP方法计算进行稀疏索引。

- 2024.03 [MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://ojs.aaai.org/index.php/AAAI/article/view/29946)这篇文章的记忆设计是明文的，包含了记忆的保存、更新、索引以及用于增强LLM的问答。是较早期的工作，主要特色是关注了记忆的更新和遗忘机制。
- 2025.02 [A-MEM: Agentic Memory for LLM Agents](https://arxiv.org/abs/2502.12110)使用的是标准的RAG处理逻辑，将对话保存为文档，每个文档有很多背景信息，比如时间戳什么的，同时为每个文档计算向量嵌入。搜索时计算向量嵌入的相似度选取Top-k个文档，最后输入大模型。
- 2024.12 [On the Structural Memory of LLM Agents](https://arxiv.org/abs/2412.15266)探索了文档不同粒度/结构化的存储格式，以及不同检索方式对结果的影响。其实就存储文档本身+直接搜Top-k就很好了，其他的感觉多此一举，性能差不都，而且开销更大。
- 2024.09 [Vector Storage Based Long-term Memory Research on LLM](https://sciendo.com/article/10.2478/ijanmc-2024-0029)将长时记忆系统明确划分为三种记忆类型并分别管理：知识记忆、对话记忆、任务记忆。
- 2024.01 [Memory Matters: The Need to Improve Long-Term Memory in LLM-Agents](https://ojs.aaai.org/index.php/AAAI-SS/article/view/27688)强调了在开发LLM agent时，可以使用向量数据库进行存储和检索。
- 2024.[Vector Storage Based Long-term Memory Research on LLM](https://sciendo.com/pdf/10.2478/ijanmc-2024-0029)提出 VIMBank 长期记忆机制，结合向量数据库与遗忘机制（Ebbinghaus 遗忘曲线），模拟人脑的动态记忆过程。
- Letta (previously MemGPT)[^9]这个GitHub项目也是使用纯文本+工程化记忆提取保存，整合得比较好的项目。

### 检索方法

检索方法决定了模型如何从外部知识库中获取信息，直接影响 RAG 的性能。

- **稀疏检索**  
    稀疏检索依赖关键词匹配技术（如 BM25），计算效率高，适用于快速检索结构化数据。然而，其语义理解能力有限，可能遗漏复杂的上下文信息。  
    - 示例研究：[Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401) 是 RAG 的奠基性工作，采用稀疏检索（如 TF-IDF）从文档中提取相关片段，显著提升了知识密集型任务的表现。
- **密集检索**  
    密集检索利用神经网络生成的嵌入向量（如 BERT 或 DPR）进行语义相似性匹配，能够捕获深层语义关系，适合复杂问答和对话任务。  
    - 示例研究：[Retrieval-Augmented Generation for Large Language Models: A Survey](https://arxiv.org/abs/2312.10997) 总结了密集检索的进展，指出其在语义一致性上的优势，但计算成本较高。

### 记忆粒度

记忆粒度决定了外部信息的组织和存储单位，影响 RAG 在长对话或多轮交互中的表现。

- **轮次级**
    以单轮对话为单位存储记忆，适合短时交互，但难以捕捉跨轮次的关系。  
    - 示例研究：[SeCom: On Memory Construction and Retrieval for Personalized Conversational Agents](https://openreview.net/forum?id=xKDZAW0He3) 提到按轮次分割对话并构建记忆，可能适用于此类别（需进一步确认具体实现）。
- **会话级**
    以整个对话会话为单位存储记忆，能够维持较长时间的上下文一致性。  
    - 示例研究：SeCom 也涉及会话级记忆，通过主题分割整合多轮信息，增强个性化对话能力。
- **基于总结**
    通过生成摘要压缩长对话或文档信息，作为记忆单位。这种方法在长程对话中尤为有效，能够减少冗余并提升效率。  
    - 示例研究：  
        - [MemoChat: Tuning LLMs to Use Memos for Consistent Long-Range Open-Domain Conversation](https://arxiv.org/abs/2308.08239) 提出自生成备忘录（memos），通过总结对话历史保持一致性，适用于开放域长对话。
        - [Recursively Summarizing Enables Long-Term Dialogue Memory in Large Language Models](https://arxiv.org/abs/2308.15022) 使用递归摘要技术，将长对话压缩为关键信息，显著扩展了 LLM 的记忆跨度。
        - SeCom 通过话题分割和压缩构造记忆库，也可归类于此。

#### 小结

RAG 方法通过外部笔记的形式弥补了 LLM 在固定上下文窗口中的局限，尤其在知识密集型任务和长对话中表现出色。稀疏检索和密集检索各有优劣，提示增强和基于记忆的输入提供了不同的集成选择，而记忆粒度的划分则展示了 RAG 在处理不同时间尺度信息时的灵活性。然而，RAG 仍需解决检索延迟和生成一致性等问题，未来研究可能集中在优化检索效率和动态记忆管理上。




## Working Memory

### 概述

工作记忆是一种记忆容量有限的认知系统，被用以暂时保存资讯。工作记忆需要和短期记忆作区分。目前比较认可的定义是，短期记忆仅指资讯的短期存储，而允许对存储的短期记忆进行操作，因此工作记忆是一个更完整的系统。

### Classification

工作记忆的实现依赖于模型的底层架构，不同设计对记忆容量和效率有显著影响。宏观上，工作记忆分类需要解决几个问题：

1. 工作记忆的容量是多少？
2. 工作记忆的迭代方式是什么？
3. 工作记忆的目标是什么？

基于工作记忆的容量，我们可以首先将目前的工作主要划分为：无限大小和固定大小。基于Transformer decoder的标准大语言模型架构可以视为无限大小的工作记忆。基于循环神经网络思路的模型则都维护一个固定大小的state。

而如果工作记忆不进行迭代，则会退化为简单的短期记忆。

#### Incremental Update

Transformer的工作记忆理论上可以包含所有输入信息，其本质是不断将新的短期记忆和旧的记忆进行concat操作：
<div align="center">
  <b>S<sub>t</sub> = concat(S<sub>t-1</sub>, {k<sub>t</sub>, v<sub>t</sub>})</b>
</div>
  Transformer的挑战在于超长序列的处理，包括平方级的计算复杂度，长度外推问题，缓存容量。因此，研究主要围绕在训练和推理阶段扩展工作记忆的长度。核心问题包括：
- 超长上下文处理，早期工作在长上下文场景中无法泛化。
- 训练推理效率，当上下文突破几十万时，如何加速和减少显存。


##### Long-context processing

长上下文主要解决两个递进的问题，首先是模型能够处理长文本，尤其是在训练和推理长度差距较大的情况下，解决长度外推的问题。在处理长文本的基础上，还需要解决长文本性能的问题，主要是让注意力关注真正有用的token，不受无关token对注意力权重的干扰。

- 长度外推
	- 长上下文训练
		- Transformer-XL[^2]首次提出了将transformer前一段的context计算的结果用于后续的计算，相当于扩展了训练阶段支持的工作记忆长度。同时引入了相对位置编码来保证长度外推。
	- 位置编码
		- 相对位置编码
		- ALiBi
		- RoPE
		- NTK-RoPE
		- YARN
		- Fourier Position Embedding
	- 位置插值
		- 
	- 滑动窗口
		- StreamingLLM
		- Λ-Mask
- 注意力增强
	- Sigmoid Attention
		- SWAT
	- SSMAX增强的softmax函数
	- moe

##### Model efficiency

%% 10篇 %%

- Cache外部检索
	- [Memorizing Transformer](../../../../4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/+Papers/Memorizing%20Transformers.md)[^6]将LLM特定层（第9层）训练时计算得到的kv cache保存作为记忆。下一个batch计算到第9层的时候，对于每一个token，模型使用knn查找与之相关的记忆（旧tokens的hidden states）并加入计算，最后汇总得到新tokens的hidden states。（具体细节需要再总结）
	- 2022.11 [Large Language Models with Controllable Working Memory](https://arxiv.org/abs/2211.05110) 提出了一种可控工作记忆框架，通过调整注意力优先级增强 LLM 对上下文的处理能力，显著提升了多任务表现。
	- Focused Transformer和Memorizing Transformers很像，都是保存了以前文本的hidden states，然后用于后续的文本训练。同时添加了负样本提升模型对于海量记忆的筛选能力。
	- 2023.06 [Augmenting Language Models with Long-Term Memory](https://dl.acm.org/doi/10.5555/3666122.3669381) 训练了额外的SideNet来索引Cached Memory Bank，支持token-to-chunk检索。
	- [LM2: Large Memory Model](https://arxiv.org/html/2502.06049v1) 加州大学和Anthropic联合团队提出的辅助记忆模块架构，通过上下文记忆库和动态门控机制实现多步推理能力提升，在BABILong基准测试上相比Llama-3.2提升86.3%。
	- [NAMMs: Neural Attention Memory Models](https://arxiv.org/) 帝国理工学院和Sakana AI提出的进化式记忆优化模型，采用进化算法优化记忆管理，实现零样本跨架构和模态迁移。
- Cache内部稀疏化
	- Longformer[^4]首次提出了稀疏注意力，让模型在计算注意力时，只关注部分关键位置的token，极大减少了模型训练和推理的开销。
		- 值得注意的是，在使用滑动窗口推理时，存在Attention Sink问题[^10]，因此需要保留最前面的几个tokens。不过和本篇的相关性不大，因此不做详细拓展。
	- LongNet[^7]沿着Longformer的思路进行了一些改进，本质上用了三种注意力掩码的组合，来保证计算量呈线性。
	- [Native Sparse Attention: Hardware-Aligned and Natively Trainable Sparse Attention](https://arxiv.org/abs/2502.11089)即NSA，将cache索引的思路引入模型架构中。同时使用了Selected Attention Mask和Sliding Attention Mask来补充模型性能。
	- [MoBA: Mixture of Block Attention for Long-Context LLMs](https://arxiv.org/abs/2502.13189)和NSA思路类似，同样是将文本分chunk进行检索，但是进行了简化。（具体怎么简化的我还没看，之后整理）
- Cache压缩
	- Compressive Transformer[^3]设计了一种压缩旧token并保存的方法，实现了真正意义上的记忆，旧tokens进行了压缩（max/mean pooling、1D convolution、dilated convolutions和most-used），同时为了验证压缩效果，他们尝试使用压缩后的记忆重建压缩前的内容。
	- Linformer本质是将kv cache进行整体压缩，类似于pooling，最后得到一个整体的向量嵌入与新的token进行计算。





#### Iterative Update

<p>
  基于循环架构的神经网络通常维护一个固定大小的隐层状态：<br>
  <b>S<sub>t</sub> = f(S<sub>t−1</sub>, x<sub>t</sub>)</b>
</p>

<p>
  通常在大语言模型中，状态的更新同样与 <b>k</b> 和 <b>v</b> 有关：<br>
  <b>S<sub>t</sub> = f(S<sub>t−1</sub>, g(k<sub>t</sub>, v<sub>t</sub>))</b>
</p>


相关工作包括：
- 基于Transformer
	- [Memformer: A Memory-Augmented Transformer for Sequence Modeling](https://aclanthology.org/2022.findings-aacl.29/)最初的encoder-decoder Transformer架构，在encoder位置添加了循环更新的Memory
	- RMT虽然仍然是Transformer架构，但利用模型的输入和输出作为临时记忆，相当于维护了一个会话级别的states。
	- AutoCompressors基于RMT改进而来，将过去的文本进行分段处理，生成多份“总结”。相比较RMT更为灵活。
- 基于Linear Attention
	- DeltaNet
	- RetNet
	- GLA
	- GSA
	- Gated DeltaNet
	- RWKV-6
- 基于state-space models (SSMs)
	- Mamba
	- Mamba2
- Meta Learning
	- TTT
	- Titans
- LSTM
	- mLSTM


**前缀微调 (Prefix Tuning)**：Li 和 Liang（2021）提出将一段可训练的连续向量（前缀）添加到Transformer每层输入，以在不改动预训练模型参数的情况下调整生成结果​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=parameters%20and%20therefore%20necessitates%20storing,performance%20in%20the%20full%20data)​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=%E2%80%9Cvirtual%20tokens%E2%80%9D.%20We%20apply%20prefix,that%20are%20unseen%20during%20training)。只需训练不到0.1%的参数，前缀微调在全量数据下性能接近全模型微调，在低资源和未见主题上甚至更佳​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=parameters%20and%20therefore%20necessitates%20storing,performance%20in%20the%20full%20data)​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=%E2%80%9Cvirtual%20tokens%E2%80%9D.%20We%20apply%20prefix,that%20are%20unseen%20during%20training)。




#### Hybrid Update

混合更新策略结合了Transformer的无限扩展能力和RNN/SSM的固定状态更新，在同一模型中使用多种架构组件。

**技术演进的三个阶段**

**第一阶段：基础突破（2021-2023）**

- **S4 (2021.10)**：解决传统SSM梯度消失问题，引入HiPPO理论，为后续状态空间模型奠定基础
- **H3 (2022)**：层级状态空间模型，首次尝试将SSM与注意力机制结合
- **Mega (Meta, 2022)**：指数移动平均与注意力结合，探索非传统递归机制
- **RWKV系列 (2021-2023)**：从V1到V4逐步优化，V4达到14B参数（注：RWKV为纯线性注意力架构，不属于混合架构）
- **Mamba (2023.12)**：引入选择性SSM机制，3B模型匹配6B Transformer性能，为混合架构提供高效基础组件
- **StripedHyena (Together AI, 2023.12)**：90%卷积层+10%注意力层的激进混合比例，支持500k+生成

**第二阶段：混合架构爆发（2024年初）**

- **Griffin (Google DeepMind, 2024.02)**：RG-LRU与滑动窗口注意力交替设计。每2个递归块后跟1个注意力块，RG-LRU使用双门控机制和实数运算。RecurrentGemma是其生产版本（2B/9B参数）
- **Jamba (AI21 Labs, 2024.03)**：首个生产级混合架构，Transformer-Mamba-MoE三层设计。每个Jamba块含8层（Transformer:Mamba=1:7），隔层应用MoE（16专家激活2）。52B总参数中12B活跃，支持256K上下文
- **Zamba (Zyphra, 2024.05)**：Mamba骨干+全局共享注意力层。每6个Mamba块共享1个注意力层，7B参数优化边缘部署
- **Samba (Microsoft, 2024.06)**：Mamba+MLP+滑动窗口注意力+MLP。3.8B参数实现线性复杂度，理论无限上下文，解码速度比Llama-3快3.64倍

**第三阶段：架构创新（2024年中后期）**

- **Jamba-1.5 (AI21 Labs, 2024.08)**：改进版本，增强长上下文处理能力
- **Bamba (IBM, 2024)**：结合Mamba2和线性注意力机制
- **Hymba (NVIDIA, 2024.11)**：革命性混合头并行架构，同层内并行运行transformer头和SSM头（5:1参数比例）。跨层KV缓存共享+128个可学习元令牌，缓存减少11.67倍、吞吐量提升3.49倍
- **Zamba2 (Zyphra, 2024.11)**：双共享注意力块（ABAB模式）+LoRA投影，2.7B参数达到同级别最佳性能

Log-Linear Attention

### 相关技术

工作记忆的有效性不仅依赖架构，还需要管理机制来优化信息存储和使用。

#### 硬件加速

- Transformer Hidden States
	- [LLM in a flash: Efficient Large Language Model Inference with Limited Memory](https://aclanthology.org/2024.acl-long.678/)通过将模型参数存储在闪存，提升推理时可以支持的计算长度。



### 小结

工作记忆作为 LLM 短期记忆的核心，支撑了模型在动态任务中的实时推理能力。Transformer 的修改注意力机制和循环架构提供了不同的实现路径，而容量控制和遗忘机制则进一步优化了其性能。然而，工作记忆的容量瓶颈和时间跨度限制仍是研究热点，未来可能通过结合外部记忆或更高效的状态管理进一步突破。





## Long-term Memory

长期记忆的存在形式仍然有待探讨。可以确定的是，为了和外部笔记做区分，长期记忆不应该存储原始的信息。

- 模型参数作为长期记忆的一部分应该是毫无争议的。
- 另一方面，大模型处理的hidden states也是可以作为长期记忆的候选者，即Transformer。但目前仍然受限于保存内容长度和处理的速度。目前更像是工作记忆。

长期记忆涉及到的领域较为广泛，模型的训练、幻觉问题、知识编辑都可以当做模型长期记忆的更新。这里选取一些典型研究。目前长期记忆的挑战是如何高效、鲁棒地实现记忆更新。

考虑记忆的形式、容量、更新：
- 形式：模型参数
- 容量：通常为固定大小，可添加和复制扩展
- 更新：多种模型训练方法



### Classification


#### Incremental Update

##### MoE

- **Sparse expert models:** Mixture-of-Experts architectures greatly expand an LLM’s effective parameter count by having many expert subnetworks and routing each input to only one or a few of them. Fedus et al. (2022) introduced the **Switch Transformer**, which uses up to 128 experts but activates just one per input token via a gating mechanism. For a fixed computation budget, Switch Transformer achieved significantly higher model quality than a dense Transformer, matching the performance of a 7× larger model with efficient sparsity​[arxiv.org](https://arxiv.org/pdf/2101.03961#:~:text=For%20a%20fixed%20amount%20of,line.%20Our%2064). This demonstrated that MoE can serve as a form of long-term memory, storing diverse knowledge across experts.

- **Domain experts and modularity:** Gururangan et al. (2022) showed that MoE models can assign experts to different domains (e.g. one expert per domain in a “DEMix” layer) and only train new experts for new domains​[aclanthology.org](https://aclanthology.org/2023.emnlp-main.516.pdf#:~:text=updated.%20DEMix,by%20freezing%20previously%20trained%20experts). This way, each expert acts as a repository of knowledge for a domain. As new domains emerge, new experts can be added incrementally. The MoE gating ensures the appropriate expert is used for relevant inputs, enabling parametric knowledge to scale with minimal interference between domains.

- **Alleviating forgetting with MoE:** Recent work combines MoE with other techniques to continually update knowledge. **LoRA-MoE** (Zhang et al., 2024) inserts multiple LoRA adapters as experts in a mixture, with a router network assigning tokens to either new or existing knowledge experts. This approach was shown to **preserve world knowledge** in an LLM even after large-scale fine-tuning on new instructions, by mitigating the catastrophic forgetting of factual knowledge while still improving on downstream tasks​[aclanthology.org](https://aclanthology.org/2024.acl-long.106.pdf#:~:text=ing%20world%20knowledge%20forgetting%20during,the%20efficacy%20of%20our%20proposed). It highlights MoE’s strength: new experts can be introduced for new data, rather than overwriting shared weights.



lora

[LoRA-FA: Memory-efficient Low-rank Adaptation for Large Language Models Fine-tuning](https://arxiv.org/abs/2308.03303)

新的参数高效微调方法LoRA-FA，该方法通过减少可训练参数和激活内存成本，大幅降低内存占用。在微调性能上可以与两个强大的基线方法（全参数微调和LoRA）相媲美。同时，LoRA-FA在不增加任何重计算的情况下，最多可减少13GB的GPU内存占用。

终生学习

[Towards Lifelong Learning of Large Language Models: A Survey](https://dl.acm.org/doi/10.1145/3716629)

LLM终身学习的综述，探讨了LLM适应不断变化的数据、任务和用户偏好的能力。传统的静态训练方法不足以应对真实世界的动态变化，而终身学习可使 LLM 不断整合新知识，同时保留先前的信息并避免灾难性遗忘。

主要贡献：

- 新颖的分类法： 调查将终身学习分为 12 种情况，包括持续预训练、微调和外部知识整合。

- 常用技术： 调查确定了用于终身学习的重放、正则化、基于架构的方法和蒸馏等技术。

- 新兴趋势： 重点介绍模型扩展、数据选择和外部知识整合（基于检索和工具的学习）等创新技术。

终身学习类别：

- 内部知识： 包括通过持续的**预训练**（特定领域、时间或语言）和**微调**（如文本分类、命名实体识别、机器翻译）更新模型参数。
- 外部知识： 结合基于检索的方法（如RAG）和基于工具的学习（如应用程序接口、计算工具），在不修改模型参数的情况下增强适应性。

挑战：

- 灾难性遗忘： 在学习新任务的同时保留旧知识
- 可塑性与稳定性的两难选择：平衡适应性与保持性
- 高计算成本： 微调大型模型需要大量资源
- 数据/模型访问： 预训练数据或模型权重的可用性有限

当前趋势：

- 从特定任务转向一般任务（如指令调整）
- 更倾向于部分微调（如适配器、LoRA）而非全面微调
- 更多依赖外部知识源，以减少再训练需求

未来方向：

- 多模态终身学习： 整合文本、图像、音频和其他模式
- 高效学习： 利用剪枝、模型合并和上下文学习来降低成本
- 通用终身学习： 通过强化学习和基于代理的系统，实现类似人类的动态学习

[MemOS: A Memory OS for AI System](https://arxiv.org/abs/2507.03724)

MemOS通过**统一记忆管理层**与**可演化MemCube单元**，系统性解决LLM在动态知识管理中的碎片化问题，为AGI的长效认知能力奠定基础

[MIRIX: Multi-Agent Memory System for LLM-Based Agents](https://arxiv.org/abs/2507.07957)

**MIRIX 是一种突破性的模块化记忆系统**，专为增强基于大语言模型（LLM）的智能体而设计，它通过**六大结构化记忆组件**解决现有记忆系统的局限性：

- **核心记忆**（用户身份）、**情景记忆**（时间戳事件）、**语义记忆**（知识图谱）、**程序性记忆**（任务流程）、**资源记忆**（多模态文件）、**知识保险库**（加密敏感数据）
  系统采用**多智能体协同架构**：由专用管理器分别处理各记忆类型，并通过元记忆协调器动态调度跨模块检索与更新，其核心技术突破包括：

1. **主动检索机制**——自动关联用户查询与相关记忆，无需人工干预

2. **极致存储效率**——向量压缩技术降低存储需求 99.9%，并在多模态理解和长对话任务中显著提升精度。
   应用场景覆盖**个人助手**（实时屏幕信息提取）、**穿戴设备**（AI 眼镜本地化记忆）及**记忆资产化**（构建可交易记忆市场）

   MIRIX 首次实现记忆的系统级生命周期管理，为持续学习与个性化智能体奠定基础设施，推动记忆从功能模块向核心数字资产的演进




#### Iterative Update



##### 预训练

2-3


##### 微调




##### 强化学习




### 相关技术

#### 硬件加速

- [ProTrain: Efficient LLM Training via Memory-Aware Techniques](https://arxiv.org/abs/2406.08334)主要是减少训练时的显存占用的，实现了更细粒度的内存管理。但是并没有改变模型的架构，因此只是一个加速算法。
- [Stack Data Management for Limited Local Memory (LLM) Multi-core Processors](https://ieeexplore.ieee.org/abstract/document/6043275)优化模型的内存管理，具体没细看。似乎和硬件相关。
- 







- **微调方法**  
  - **完全微调**：更新所有参数，适合大规模知识更新。  
    - 示例：LAMOL ([LAMOL: LAnguage MOdeling for Lifelong Language Learning](https://openreview.net/forum?id=Skgxcn4YDS)) 通过语言建模实现连续任务学习，防止遗忘。  
  - **参数高效微调**：如 LoRA，仅更新部分参数，降低成本。  
    - 示例：相关研究可参考终身学习调研。  

- **记忆更新机制**  
  - **增量学习**：在不遗忘旧知识情况下学习新知识。  
    - 示例：LAMOL 生成伪样本重放，属于增量学习。  
  - **持续学习**：适应动态数据流，适合实时更新。  
    - 示例：调研论文 [Towards Lifelong Learning of Large Language Models: A Survey](https://arxiv.org/abs/2406.06391) 讨论持续学习策略。  

- **记忆保留策略**  
  - **正则化技术**：防止过拟合，保持通用知识。  
    - 示例：使用 L2 正则化防止遗忘。  
  - **记忆巩固**：稳定已学知识，防止遗忘。  
    - 示例：相关研究如 [Continual Pre-Training Mitigates Forgetting in Language and Vision](https://github.com/AGI-Edgerunners/LLM-Continual-Learning-Papers) 讨论巩固机制。  

长期记忆通过模型参数更新实现知识积累，但更新成本高，未来可探索参数高效方法和防止遗忘策略。  




## Related Surveys

- From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs
- A Survey on the Memory Mechanism of Large Language Model based Agents
- Advances and Challenges in Foundation Agents: From Brain-Inspired Intelligence to Evolutionary, Collaborative, and Safe Systems




## 一些无法访问原文的工作

- [MindMemory: Augmented With Long-Term And Mental Personality](https://link.springer.com/chapter/10.1007/978-981-96-2373-0_33)


Large Language Models with Controllable Working Memory








[^1]: [Attention Is All You Need](https://dl.acm.org/doi/10.5555/3295222.3295349)
[^2]: [Transformer-XL: Attentive Language Models beyond a Fixed-Length Context](https://aclanthology.org/P19-1285/)
[^3]: [Compressive Transformers for Long-Range Sequence Modelling](https://openreview.net/forum?id=SylKikSYDH)
[^4]: [Longformer: The Long-Document Transformer](https://doi.org/10.48550/arXiv.2004.05150)
[^5]: 
[^6]: [Memorizing Transformers](https://openreview.net/forum?id=TrjbxzRcnf-)
[^7]: [LongNet: Scaling Transformers to 1,000,000,000 Tokens](https://doi.org/10.48550/arXiv.2307.02486)
[^8]: [MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://doi.org/10.1609/aaai.v38i17.29946)
[^9]: [MemGPT: Towards LLMs as Operating Systems](https://doi.org/10.48550/arXiv.2310.08560)
[^10]: [Efficient Streaming Language Models with Attention Sinks](https://openreview.net/forum?id=NG7sS51zVF)









