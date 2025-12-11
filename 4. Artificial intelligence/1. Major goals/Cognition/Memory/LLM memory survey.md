


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

As large language models (LLMs) evolve from static text generators to dynamic interactive agents, the ability to incorporate memory has become central to their performance and adaptability. Memory architectures govern how information is represented, stored, retrieved, and updated over time. They form the foundation upon which models maintain coherence, consistency, and long-term personalization.

This section first draws inspiration from human cognitive memory systems to motivate the layered structure of memory in LLMs. We then survey how existing literature categorizes memory architectures and conclude by presenting a principled, design-oriented classification framework.

### Human Memory

Human memory is typically decomposed into three major types: **sensory memory**, **working memory**, and **long-term memory**. This cognitive framework serves as an intuitive lens for reasoning about memory in LLMs:

- **Sensory Memory** is a brief buffer for raw perceptual input. It corresponds in LLMs to raw modality-specific inputs or externally retrieved documents—often unprocessed but critical for immediate attention (e.g., retrieval-augmented generation).
- **Working Memory** is an active, limited-capacity system for holding and manipulating information in real time. This aligns with KV caches, context windows, or streaming state representations within Transformer or SSM architectures.
- **Long-Term Memory** stores knowledge, experiences, and procedural skills across time. In LLMs, this is primarily embodied in learned parameters, fine-tuned adapters, or modular experts.

The cognitive model also distinguishes between **explicit memory** (semantic and episodic knowledge accessible by conscious recall) and **implicit memory** (procedural skills and priming), which map respectively to textual/document memory and encoded model capabilities.

This analogy highlights the multi-scale and multi-format nature of memory. A single architectural layer rarely suffices; instead, memory systems must span immediate context, learned priors, and modifiable external stores.


![](../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Cognition/Memory/LLM%20memory%20survey/IMG-20250529174334277.png)


### Existing Classifications



%%
目前已经有很多大模型记忆的工作和总结，对于记忆的定义与分类也不尽相同。

- [A Survey on the Memory Mechanism of Large Language Model based Agents](https://dl.acm.org/doi/abs/10.1145/3748302)
	- 这篇应该是第一篇探讨大模型记忆的综述，但是整体focus在agent层面。
	- Scope: Memory of LLM-based Agent
	- Architecture: 
		- Memory Sources
			- Inside-trial Information (单会话信息)
			- Cross-trial Information (多会话信息)
			- External Knowledge
		- Memory Forms
			- Memory in Textual Form
			- Memory in Parametric Form
		- Memory Operations
			- Memory Writing
			- Memory Management
			- Memory Reading
	- Publication: Published – _ACM Transactions on Information Systems_, Vol 43 (6), Jul 2025
- [From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs](https://arxiv.org/abs/2504.15965)
	- 这篇论文提出了一个3D-8Q Memory Taxonomy，根据大模型记忆中明显的特征：记忆对象、记忆形式和记忆时间进行了划分，通过组合可以得到8个象限。
	- Scope: Memory of LLM-Driven AI Systems
	- Architecture: 
		- Memory Dimensions (3D Framework)
			- Object: Personal Memory vs System Memory
			- Form: Parametric vs Non-Parametric
			- Time: Short-Term vs Long-Term
		- Eight Quadrants (8Q Taxonomy)
			- Personal × Non-Parametric × Short-Term → Working Memory
			- Personal × Non-Parametric × Long-Term → Episodic Memory
			- Personal × Parametric × Short-Term → Working Memory (Cache)
			- Personal × Parametric × Long-Term → Semantic Memory (Personalized LLMs)
			- System × Non-Parametric × Short-Term → Reasoning Memory (CoT / ReAct)
			- System × Non-Parametric × Long-Term → Procedural Memory (Reflection / Skill Learning)
			- System × Parametric × Short-Term → Parametric Cache (KV / Attention Optimization)
			- System × Parametric × Long-Term → Knowledge & Skill Base (Model Parameters)
	- Publication: Preprint – arXiv:2504.15965 (v2, Apr 2025)
- [Cognitive Memory in Large Language Models](https://arxiv.org/abs/2504.02441)
	- 2025年4月，中国科学院团队发表
	- 参考人类的记忆形式，将记忆划分成Text-based、KV Cache-Based和Parameters-Based三个大类，逐渐接近记忆的本质。而在具体类别中，进一步阐述记忆的Acquisition、Management和Utilization。但是这篇论文的局限是，主要考虑的是基于Transformer架构的记忆，将其他模型架构划分为单独地一类Hidden-State-Based Memory。缺少对于现有技术更深刻的理解。
	- Scope: Memory Mechanisms in LLMs
	- Architecture: 
		- Text-based Memory
		- KV Cache-based Memory
		- Parameters-based Memory
		- Hidden-State-based Memory
	- Publication: Preprint – arXiv:2504.02441 (v2, Apr 2025)
- [Rethinking Memory in AI: Taxonomy, Operations, Topics, and Future Directions](https://arxiv.org/abs/2505.00675)
	- 类似的，本文将记忆划分为参数化记忆和上下文记忆。他们重点探讨了六个基本记忆操作（Memory Operations），包括Consolidation（巩固）、Indexing（索引）、Updating（更新）、Forgetting（遗忘）、Retrieval（检索）和Compression（压缩）。最后总结了长期记忆、长上下文、参数修改和多源记忆四个核心研究主题。附录部分总结了众多相关数据集，很有价值。
	- Scope: Memory in AI Systems (LLMs, Agents, and Multi-Modal Reasoning)
	- Architecture:
		- Memory Representation
			- Parametric Memory – knowledge implicitly encoded in model parameters
			- Contextual Memory
				- Structured
				- Unstructured
		- Memory Operations (Six Atomic Operations)
			- Consolidation – integrating short-term experiences into long-term memory
			- Indexing – constructing access structures and retrieval signals
			- Updating – dynamically modifying or integrating new information
			- Forgetting – selectively removing outdated or harmful content
			- Retrieval – accessing relevant memory for reasoning and generation
			- Compression – reducing memory footprint while preserving key information
		- Research Topics (4 Core Areas)
			- Long-Term Memory – multi-session dialogue and personalized memory management
			- Long-Context Memory – efficient handling of extended sequences
			- Parametric Memory Modification – editing, unlearning, and continual learning of internal knowledge
			- Multi-Source Memory – integrating heterogeneous and multimodal knowledge sources
	- Publication: Preprint – arXiv:2505.00675 (v2, May 2025)
- [Advances and Challenges in Foundation Agents: From Brain-Inspired Intelligence to Evolutionary, Collaborative, and Safe Systems](https://arxiv.org/abs/2504.01990)
	- Meta的人工智能大综述
	- 关于Memory部分，他们详细介绍了主流的人类记忆模型，然后同样根据模型对记忆的多种操作进行介绍。
	- Scope: Memory Mechanisms in LLMs
	- Architecture:
		- Human Memory Foundations
			- Sensory Memory → Short-term / Working Memory → Long-term Memory
			- Declarative (Semantic, Episodic, Autobiographical) vs Non-declarative (Procedural, Priming, Conditioning)
			- Classical Cognitive Models:
				- Multi-Store (Atkinson–Shiffrin)
				- Working Memory Model (Baddeley & Hitch)
				- SPI Model (Tulving)
				- Global Workspace Theory / IDA–LIDA
				- ACT-R Cognitive Architecture
		- Agentic Memory (LLM-based Agents)
			- Hierarchical Architecture
				- Sensory Memory – perception and multimodal encoding
				- Short-term Memory – contextual and working memory for active reasoning
				- Long-term Memory – semantic, episodic, procedural, and priming components
			- Memory Lifecycle (Unified Computational View)
				1. Acquisition – filtering and compression of sensory input
				2. Encoding – attention-based and multimodal representation learning
				3. Derivation – reflection, summarization, distillation, selective forgetting
				4. Retrieval & Matching – context-aware semantic search and hybrid indexing
				5. Neural Memory Networks – implicit parametric storage within model weights
				6. Utilization – integration with reasoning, planning, and hallucination mitigation
	- Publication: Preprint – arXiv:2504.01990 (v1, Apr 2025)
- [Memory Meets (Multi-Modal) Large Language Models: A Comprehensive Survey](https://openreview.net/forum?id=Sk7pwmLuAY)
	- Scope: Memory Mechanisms in Multi-Modal LLMs (MLLMs)
	- Architecture:
		- Three Memory Paradigms
			- Implicit Memory – knowledge embedded in internal parameters of transformers; includes memorization, associative retrieval, and contextual reasoning; explored through model interpretability, reconfiguration, and internal editing
			- Explicit Memory – external storage and retrieval modules augmenting model outputs via textual, vector, and graph-based knowledge representations; supports dynamic updates and scalable access to knowledge
			- Agentic Memory – persistent, temporally extended structures for autonomous agents, enabling long-term planning, self-consistency, and multi-agent collaboration
		- Multi-Modal Integration
			- Extension of memory mechanisms to vision, audio, and action modalities
			- Focus on cross-modal coherence and temporal alignment
		- Research Topics and Challenges
			- Memory capacity and scalability
			- Factual consistency and alignment
			- Interoperability across heterogeneous systems
			- Evaluation and benchmark limitations
	- Publication: Accepted – TMLR, Paper 5081, Oct 2025
- [Towards Lifelong Learning of Large Language Models: A Survey](https://arxiv.org/abs/2406.06391)
	- 关注大语言模型的终身学习能力，使模型能够在运行周期中持续学习和适应，整合新知识的同时保留之前学到的信息，防止灾难性遗忘。
	- Scope: Lifelong / Continual Learning in LLMs
	- Architecture:
		- Knowledge Categories
			- Internal Knowledge
				- Continual Pretraining – domain-specific, temporal, multilingual
				- Continual Finetuning – task-specific adaptation (classification, NER, translation, etc.)
			- External Knowledge
				- Retrieval-based methods (RAG)
				- Tool-based learning (APIs, calculators)
		- Core Techniques
			- Replay-based methods – episodic memory and pseudo-rehearsal
			- Regularization – EWC, knowledge distillation
			- Architecture-based – adapters, LoRA, modular experts
			- Parameter-efficient methods – partial tuning to reduce forgetting
		- Emerging Techniques
			- Model expansion – adding capacity for new domains
			- Data selection – curriculum and active learning
	- Key Challenges: catastrophic forgetting, stability–plasticity trade-off, computational cost, limited data/model access
	- Publication: Published – ACM Computing Surveys, Vol 57 (8), Mar 2025
- [Continual Learning with Pre-Trained Models: A Survey](https://arxiv.org/abs/2401.16386)
	- 关注如何利用预训练模型的强大表征能力进行持续学习，克服灾难性遗忘。与从头训练不同，本文探讨如何在预训练模型基础上进行增量学习。
	- Scope: Continual Learning leveraging Pre-Trained Models (PTMs)
	- Architecture:
		- Retention Strategies
			- Replay-based – storing exemplars or pseudo-samples
			- Regularization-based – constraining weight updates (EWC, distillation)
			- Modularization – task-specific modules (adapters, experts)
		- PTM-specific Considerations
			- Leveraging pre-trained representations
			- Freezing vs. fine-tuning strategies
			- Task-incremental vs. class-incremental scenarios
	- Key Focus: Comparative analysis of methodologies; empirical fairness in evaluations
	- Limitation: Less emphasis on LLM-specific mechanisms (more general PTM focus)
	- Publication: Published – IJCAI 2024 (Survey Track), Aug 2024
- [Digital Forgetting in LLMs: A Survey of Unlearning Methods](https://arxiv.org/abs/2404.02062)
	- 聚焦于机器反学习（unlearning），即如何让模型"忘记"不需要的知识或行为，包括敏感信息、版权内容、偏见和有害内容。
	- Scope: Machine Unlearning in LLMs (Digital Forgetting)
	- Architecture:
		- Unlearning Granularity
			- Sample-level – forgetting specific training examples
			- Concept-level – removing entire concepts or knowledge
			- Parameter-level – targeted weight modifications
		- Unlearning Methods (Taxonomy of 22 methods analyzed)
			- Exact unlearning – retraining from scratch without target data
			- Approximate unlearning – efficient methods approximating exact unlearning
				- Gradient-based – reverse gradient updates
				- Model editing – ROME, MEMIT-style localized edits
				- Fine-tuning-based – targeted forgetting via continued training
		- Evaluation Framework
			- Forgetting quality – successful removal of target knowledge
			- Retention quality – preservation of non-target knowledge
			- 28 datasets for unlearning evaluation; 31 for retention evaluation
	- Motivations: privacy protection, copyright compliance, bias elimination, harmful content prevention
	- Key Challenges: Does not extensively cover knowledge retention or hybrid memory integration
	- Publication: Published – Artificial Intelligence Review (Springer), Jan 2025
- [Beyond the Limits: A Survey of Techniques to Extend the Context Length in Large Language Models](https://arxiv.org/abs/2402.02244)
	- 探讨扩展Transformer模型上下文长度的各种技术，在不成比例增加计算需求的情况下处理更长序列。
	- Scope: Long-Context Extensions in Transformers
	- Architecture: (Taxonomy of 5 categories)
		- Length Extrapolation
			- Positional encoding techniques – RoPE, ALiBi, NTK-aware interpolation
			- Training strategies – extended context fine-tuning
		- Attention Approximation
			- Sparse attention – Longformer, BigBird, sliding window
			- Linear attention approximations
		- Attention-Free Transformers
			- State space models (SSMs) – S4, Mamba
			- Recurrent alternatives – RWKV
		- Model Compression
			- KV cache compression – quantization, pruning
			- Efficient inference techniques
		- Hardware-Aware Transformers
			- Flash Attention and GPU-optimized implementations
	- Key Focus: Architectural and encoding techniques for working memory scalability
	- Limitation: Treats memory implicitly as extended context; less focus on parametric or external memory
	- Publication: Published – IJCAI 2024 (Survey Track), Aug 2024
%%

Recent surveys have attempted to organize the rapidly growing landscape of memory mechanisms in large language models (LLMs), but they differ considerably in focus and depth. As shown in Table 1, these studies can be grouped into three core perspectives, with several domain-specific branches extending the coverage.

#### Table 1: Comprehensive Summary of LLM Memory Surveys

**Core Memory Surveys:**

| Survey | Date | Venue | Scope / Focus | Key Taxonomy | Remarks |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **[A Survey on the Memory Mechanism of Large Language Model based Agents](https://dl.acm.org/doi/abs/10.1145/3748302)** | 2025-07 | TOIS | Agent memory mechanisms | Memory Sources (Inside/Cross-trial, External) + Forms (Textual, Parametric) + Operations (Writing, Management, Reading) | First comprehensive agent-focused memory survey |
| **[From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs](https://arxiv.org/abs/2504.15965)** | 2025-04 | arXiv | LLM-driven AI systems | 3D-8Q Taxonomy: Object × Form × Time (8 quadrants) | Novel dimensional framework |
| **[Cognitive Memory in Large Language Models](https://arxiv.org/abs/2504.02441)** | 2025-04 | arXiv | Memory mechanisms in LLMs | Text-based, KV Cache-based, Parameters-based, Hidden-State-based | Focus on Transformer-centric memory |
| **[Rethinking Memory in AI: Taxonomy, Operations, Topics, and Future Directions](https://arxiv.org/abs/2505.00675)** | 2025-05 | arXiv | LLMs, Agents, Multi-Modal | Six Atomic Operations + Four Research Topics | Operations-focused; valuable dataset appendix |
| **[Advances and Challenges in Foundation Agents: From Brain-Inspired Intelligence to Evolutionary, Collaborative, and Safe Systems](https://arxiv.org/abs/2504.01990)** | 2025-04 | arXiv | Foundation agents | Human Memory Models + Agentic Memory Lifecycle | Meta's comprehensive agent survey |
| **[Memory Meets (Multi-Modal) Large Language Models: A Comprehensive Survey](https://openreview.net/forum?id=Sk7pwmLuAY)** | 2025-10 | TMLR | Multi-modal LLMs | Implicit, Explicit, Agentic Memory Paradigms | Cross-modal memory integration |

%%
**Domain-Specific Surveys** _(indirectly related to memory mechanisms)_:

| Survey | Date | Venue | Scope / Focus | Key Taxonomy | Remarks |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **[Towards Lifelong Learning of Large Language Models: A Survey](https://arxiv.org/abs/2406.06391)** | 2025-03 | ACM Comp. Surv. | Lifelong learning | 12 Scenarios (Internal/External Knowledge) | Catastrophic forgetting; emerging techniques |
| **[Continual Learning with Pre-Trained Models: A Survey](https://arxiv.org/abs/2401.16386)** | 2024-08 | IJCAI 2024 | Continual learning with PTMs | Retention Strategies (Replay, Regularization, Modularization) | Less LLM-specific; fairness in evaluation |
| **[Digital Forgetting in Large Language Models: A Survey of Unlearning Methods](https://arxiv.org/abs/2404.02062)** | 2025-01 | AI Review | Machine unlearning | Unlearning Granularity (Sample/Concept/Parameter) + 22 Methods | Privacy, copyright, bias, harmful content |
| **[Beyond the Limits: A Survey of Techniques to Extend the Context Length in Large Language Models](https://arxiv.org/abs/2402.02244)** | 2024-08 | IJCAI 2024 | Long-context extensions | 5 Categories (Extrapolation, Approximation, Attention-Free, Compression, Hardware) | Working memory scalability |
%%

### Classification of Existing Surveys

Based on the six core memory surveys, we identify three distinct perspectives:

#### 1. **Cognitive Analogy Perspective**

This perspective draws explicit parallels between human cognitive systems and AI architectures, using cognitive concepts (e.g., sensory/working/long-term memory, consolidation, forgetting) as organizing principles.

- **From Human Memory to AI Memory** ([Wu et al., 2025](https://arxiv.org/abs/2504.15965)) proposes a **3D-8Q taxonomy**: Object (Personal vs System) × Form (Parametric vs Non-Parametric) × Time (Short-Term vs Long-Term).

- **Advances in Foundation Agents** ([Liu et al., 2025](https://arxiv.org/abs/2504.01990)) maps **classical cognitive models** (Multi-Store, Working Memory Model, SPI, Global Workspace Theory, ACT-R) to LLM memory lifecycle: Acquisition → Encoding → Derivation → Retrieval → Utilization.

- **Rethinking Memory in AI** ([Du et al., 2025](https://arxiv.org/abs/2505.00675)) identifies **six atomic operations** (Consolidation, Indexing, Updating, Forgetting, Retrieval, Compression) and maps them to **four research topics**: Long-Term Memory, Long-Context Memory, Parametric Memory Modification, and Multi-Source Memory.

#### 2. **Architecture Perspective**

This perspective focuses on the architectural design and implementation mechanisms of memory systems.

- **Cognitive Memory in LLMs** ([Shan et al., 2025](https://arxiv.org/abs/2504.02441)) enumerates **four memory substrates**: Text-based, KV Cache-based, Parameters-based, and Hidden-State-based, analyzing acquisition, management, and utilization for each. However, this classification lacks a unified organizing principle—it simply lists where memory can reside without explaining *why* these categories emerge or *how* they relate to each other.

#### 3. **Application Perspective**

This perspective emphasizes memory integration within specific application contexts.

- **Agent Memory Survey** ([Zhang et al., 2024](https://dl.acm.org/doi/abs/10.1145/3748302)) organizes by **Memory Sources** (Inside-trial, Cross-trial, External), **Forms** (Textual, Parametric), and **Operations** (Writing, Management, Reading), focusing on agent workflows.

- **Memory Meets Multi-Modal LLMs** ([TMLR, 2025](https://openreview.net/forum?id=Sk7pwmLuAY)) proposes **three paradigms**: Implicit Memory (parameters), Explicit Memory (external storage), and Agentic Memory (persistent structures), addressing cross-modal coherence.

### Summary and Gaps

These surveys reflect a growing consensus that LLM memory spans multiple substrates: external retrieval, working states, and parametric knowledge. However, three limitations consistently emerge:

- **Lack of architectural grounding:** Most frameworks describe _what_ kinds of memory exist but not _how_ they are structurally realized (e.g., boundaries between cache, state, and parameters).
- **Inconsistent granularity:** Surveys mix cognitive and engineering concepts at different abstraction levels, making quantitative comparison difficult.
- **Weak integration:** Few surveys discuss how different memory types cooperate or exchange information across timescales.

The **Architecture Perspective** serves as a natural bridge between cognitive analogy and application scenarios—it connects *what* memory does (cognitive functions) with *how* memory is implemented (system design) to serve *where* memory is used (applications). However, this perspective remains underexplored: existing work has identified memory substrates but has not yet established a unified framework for understanding design trade-offs and architectural evolution.

This gap motivates our work to develop a principled **architecture-oriented classification** that systematically organizes memory mechanisms along two key dimensions: temporal hierarchy and update mechanism.



### Our Classification

%%
现有LLM记忆研究多从**应用视角**（Agent任务、操作类型）或**认知类比**（模仿人类记忆）出发，缺乏对**架构设计原理**的深入分析。本文提出**设计导向的记忆框架**，聚焦三个核心设计决策：**信息编码粒度**（原始vs压缩）、**状态管理策略**（全保存vs选择更新）、**知识存储机制**（外部vs参数化）。这种从第一性原理出发的分类，不仅揭示了记忆的计算本质，更为架构选择和工程优化提供了系统性指导。

为了更好地理解 LLM 的记忆机制，我们首先将这些类别与人类记忆系统进行类比映射。我们依据信息压缩处理的程度，同样将记忆分成三类：

- **感觉记忆**（Sensory Memory）则体现为人类感官的原始输入，对应于 LLM 的 RAG 方法，例如 MemoChat 和 Recursively Summarizing 通过检索或生成外部备忘录增强对话能力。这种映射不仅帮助我们理解 LLM 记忆设计的灵感来源，还揭示了其在模拟人类认知方面的潜力与局限。

- **工作记忆**（Working/Short-term Memory）是人类在短时间内处理和操作信息的临时存储空间，类似于 LLM 的隐藏状态（如 Transformer 的注意力机制或循环网络的状态），用于维持当前任务的上下文，例如 Controllable Working Memory 研究中控制模型的短期信息优先级。

- **长期记忆**（Long-term Memory）存储于大脑神经网络中，包含事实、经验和技能，类似于 LLM 通过训练更新模型参数来固化知识的过程，例如 MemoryBank 通过持续学习适应用户个性化的需求。

细节上，首先，我们认为显式记忆（Explicit Memory）是存在于所有类型的记忆中的。只不过，对于未经处理的感觉记忆，更多等于对环境中关键信息的存储。而后两者经过了压缩和抽象。显式记忆中，情节记忆保留了对过去发生、观察到的事物进行保存，这种信息无论是明文存储、缓存到cache还是隐含在参数中，都是可以直接理解的。而语义信息则需要通过理解获得，比如数学定理或者大象是什么，不再存在于Sensory Memory。而最后的隐式记忆，作为模型学习到的能力，只存在于Long-term Memory中。

记忆需要有更新，通常我们会细分出学习、遗忘、索引、压缩等操作，但这些一般都隐含在我们的算法中（如注意力机制等等），因此我们主要考虑更新方式。而对于模型来说，最常用的参数更新方式就是Structural和Iterative两种，前者直接在结构上拼接和Crop记忆，后者对现有的内容进行迭代。
%%


| Memory Type                          | Form（形式）                           | Operations（操作）                                     | Usage（用途）                                                       |
| ------------------------------------ | ---------------------------------- | -------------------------------------------------- | --------------------------------------------------------------- |
| **Sensory Memory**（Text → Embedding） | 原始 token；即时 embedding（瞬时编码表征）；无持久性 | encoding, retrieval, summarizing, adding, deleting | 提供即时输入，用于短期记忆的计算                                                |
| **Short-Term Memory**（Cache）         | KV Cache；Recurrent State；可替换       | update (state, structure), retrieval, compress     | 提供包含长期记忆的模型的hidden states，维持 session 内的上下文一致性；用于长期记忆的计算；为输出提供参考 |
| **Long-Term Memory**（Weights）        | 结构化语义知识、参数化知识                      | update (state, structure)                          | 跨 session 的知识；为输出提供参考                                           |


To unify the diverse approaches above, we propose a **design-oriented classification** grounded in architectural principles rather than application type or cognitive analogy alone. This framework focuses on three interrelated axes:

1. **Granularity of Representation**  
    The abstraction level of memory content:
    - _Raw_: uncompressed inputs or document fragments
    - _Structured_: summarized utterances, dialogue snippets, modular plans
    - _Abstracted_: learned concepts, skills, schemas
2. **State Management Strategy**  
    The mechanism for updating memory over time:
    - _Structure-wise update_: appending, cropping, rewriting cached states (e.g., in Transformer-based memory)
    - _State-wise update_: recurrent or streaming state updates (e.g., SSM, RNN-like dynamics)
    - _Hybrid update_: combining windowed attention with learned memory slots or state space operations
3. **Storage Mechanism**  
    The physical or logical location of memory:
    - _External_: document stores, vector databases, symbolic logs
    - _Working memory_: cache, token streams, intermediate attention states
    - _Parametric memory_: model weights, adapter modules, dynamic experts

This taxonomy enables precise mapping between memory **function**, **placement**, and **update protocol**. It also allows researchers and system designers to:
- Analyze architectural trade-offs (e.g., compute vs. retention vs. plasticity)
- Design composable memory interfaces (e.g., routing, prioritization, summarization)
- Benchmark systems with consistent scope and assumptions

We adopt this classification throughout the rest of the survey to systematically compare memory-enhanced models across tasks such as long-context processing, tool usage, personalization, and lifelong learning.

值得注意的是，与 Sensory Memory 的多种 RAG 检索方式不同，Working Memory 和 Long-term Memory 的**形式相对固定**（分别对应隐状态和模型参数），因此推理时的调用方式也基本一致，不存在感觉记忆部分那样多样化的检索策略。因此，后两部分的研究重点主要是**记忆更新**：新记忆的存储、已有记忆的保留与选择性遗忘。

可以看到，压缩程度越高，对于记忆的可操作方式就越少，越固定。而用途上，压缩程度越低的记忆一般趋向于为更高层的记忆服务。

## Sensory Memory

正如前面提到的，Sensory Memory应该是模型的原始输入，包括：图像、文本、音频等多种模态的原始感知数据。这些信息构成了模型在推理阶段的第一层“感官输入”，在类脑机制中对应人类接收外界刺激的初始阶段。每种模态的数据在进入模型后，都会分别通过对应的感知编码器（如视觉编码器、语言编码器、音频编码器等）被转化为统一的高维特征向量，作为跨模态对齐与融合的基础。

不过，文本模态的特殊性让我们意识到，需要不同的模块来处理不同类型的模态。一个核心的区分指标就是，信息是否具有时间属性，或者说，是否是连续的。人的任何感官，都可以看做连续的输入，比如每时每刻，眼睛都会接收新的图像（虽然也可以按照帧来划分）。但是文本序列则没有显式的时间属性，是相较而言是非常离散的，一个人说10个单词，可能需要几秒，但是要展现这10个单词给tokenizer，则是一瞬间的事情。一个视频、音频是连续的，而一张图片、一段文本则是离散的，只代表了现实世界的一个切面。这一差别让视频和文本无法直接进行注意力计算，现有的多模态模型都是截取视频中的部分帧，将离散的图片与文本处理成token，然后计算注意力。这也意味着现有的大模型都在处理离散数据，而无法处理连续的信息。

尽管如此，以上的所有信息都还是未经处理的，所有模型都可以基于这些信息进行训练而不需要额外的编码，因此可以作为预训练数据集。现在也有检索增强生成（Retrieval-Augmented Generation, RAG）来实现对Sensory Memory的存储和索引。这种技术可以理解为维护一个个体完整的经历。RAG特别适用于需要处理大量外部信息或长对话的场景。RAG 的核心思想是将生成过程与检索过程结合：模型首先从外部存储中检索相关信息，然后将其集成到生成过程中，避免了模型完全依赖内部参数存储所有信息的限制。RAG 的优势在于其灵活性和可扩展性，但也面临检索效率和信息一致性等挑战。

尽管名字叫感觉记忆，但这本质上是因为人类大脑无法完全存储这类信息，而计算机可以持久地保存感觉记忆，因此LLM的感觉记忆的存储量反而是最大的。

### 现有工作

暂时还需要对这部分进行总结，主要关注那些将RAG作为记忆的工作，而不是包含所有的检索方法。

#### 基础 RAG (Foundational RAG)

- 2020.05 [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401) **Classic**. RAG的奠基之作。提出了非参数化记忆（外部知识库）与参数化记忆（模型权重）相结合的方法。
- 2023.04 [Generative Agents: Interactive Simulacra of Human Behavior](https://arxiv.org/abs/2304.03442) **Classic**. 斯坦福虚拟小镇。构建了完整的记忆流（Memory Stream），包含记忆检索、反思（Reflection）和规划（Planning）。

#### 反思

- 2023.11 [Think-in-Memory: Recalling and Post-thinking Enable LLMs with Long-Term Memory](https://arxiv.org/abs/2311.08719)这个是较早期的工作，通过索引相关的内容，然后让模型对历史信息进行**反思**，达到等价的记忆效果。

- 2025.10 不需要RL训练的递归思考 https://arxiv.org/pdf/2510.04871 Hierarchical Reasoning Model

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


工作记忆是大语言模型记忆体系中最接近“在线思考”的部分：它既承担了短期信息的保存，又承担了对这些信息的选择、组合和操作。与上一节的感觉记忆不同，工作记忆不是对原始感知数据的简单缓存，而是对“当前正在被模型用于推理的那一小部分信息”的动态维护。从认知科学视角来看，现有工作已经基本形成一个共识：LLM 的工作记忆可以抽象为一个容量有限、可读写、随时间不断更新的状态空间，其主要实现形态包括基于 KV cache 的注意力窗口、基于隐状态的循环或状态空间模型，以及一部分显式设计的短期记忆模块。[^cognitive_memory_llm][^memory3_explicit]

越来越多的工作强调：当前 LLM 的工作记忆虽在行为上呈现出部分类人效应（如列表长度效应、虚假记忆等），但在机制上仍与人类前额叶皮层主导的工作记忆系统存在本质差异。人类可以在一个显式的心理工作空间中有意识地“保持并操作”少量符号，而主流 LLM 更多依赖被动的注意力检索与隐式分布式表示，缺乏类似“可显式操作的缓存寄存器”。这也解释了为何在一些需要长期维持中间假设、反复更新 mental model 的任务上，LLM 在直觉上显得“记不住自己刚才是怎么想的”。[^huang2025_llm_not_human_wm][^cog_psych_memory_2025]

一系列实证研究表明，Transformer 式自注意力天然存在类似人类工作记忆的容量瓶颈。N-back 等范式下的实验发现，即便在参数规模不断增大的情况下，模型能够稳定维持和操作的信息量仍近似受一个常数级上限约束，继续堆叠层数或扩大模型规模并不能线性提升其“可用工作记忆”。[^gong2024_wm_capacity] 从注意力分布分析的角度看，随着序列长度增长，注意力权重熵迅速增大，模型难以在指数级候选位置中保持集中，导致关键信息逐渐被噪声稀释；[^selfattention_limits_2024] 在复杂推理任务上，失败的根源往往不是缺少参数，而是中间结论在推理链中“丢失”或被覆盖，从而暴露出工作记忆作为推理瓶颈的结构性限制。[^wm_identifies_limits_2024]

因此在架构层面，研究焦点正在从“单纯拉长上下文窗口”转向“让工作记忆变得可操作”。一条路线是在 Transformer 内部通过 KV cache 管理、稀疏注意力、记忆压缩与选择性遗忘等手段，尽可能挖掘现有注意力结构的工作记忆能力；[^kv_cache_survey_2025] 另一条路线则通过引入显式状态（隐层 state、SSM 状态、反馈注意力循环、显式工作记忆缓冲区等）赋予模型更接近 RNN/LSTM 那样的持久、可控、可编辑的短期记忆。[^transformerfam_2024][^think_in_memory_2023][^lm2_large_memory_2025] 更进一步，一些工作开始在工作记忆之上叠加明确的操作接口（读、写、合并、反思、重构），模糊了“内部状态”和“小型外部黑板”之间的界限，使工作记忆从“静态窗口”演化为“可编程的动态资源”。[^ewe_explicit_wm_2025][^memory3_explicit]

在本文的框架下，我们将工作记忆从三个正交的维度进行组织：首先是**表示形式（Forms）**，即工作记忆在 LLM 中以何种结构性载体存在——是通过附着于注意力头的 KV cache 表达、通过固定维度的隐状态在循环/线性注意力/状态空间模型中维持，还是以独立模块化的显式工作记忆缓冲区出现；其次是**更新机制（Update）**，即工作记忆在推理过程中如何被写入、压缩、遗忘或重组，这些更新规则直接决定了模型在长序列与深度推理中的稳定性与容量利用效率；最后是**使用方式（Usage）**，包括模型如何在注意力、检索、选择性访问或推理控制等过程中调用工作记忆，从而影响其信息整合、推理深度与中间状态保持能力。

下面我们首先从表示形式的角度出发，梳理基于 KV Cache、隐状态与显式工作记忆模块的几类主流实现，然后再从更新机制与使用方式切入，讨论不同工作记忆设计在容量、效率与推理能力上的权衡。



### Forms

工作记忆是一种记忆容量有限的认知系统，被用以暂时保存资讯。工作记忆需要和短期记忆作区分。目前比较认可的定义是，短期记忆仅指资讯的短期存储，而允许对存储的短期记忆进行操作，因此工作记忆是一个更完整的系统。

在当前大语言模型中，工作记忆主要以两种内部结构存在，它们共同构成模型在推理过程中可读写、可迭代的短期信息存储空间。

#### KV Cache

KV cache 是 Transformer 架构下最典型的工作记忆形式。模型在每个时间步将输入 token 的 Key 与 Value 表示追加到缓存中，随后的注意力计算通过读取全部历史 KV 来整合上下文信息。其主要特性包括：

- **可扩张性**：序列越长，KV cache 越大，形成近似“无限容量”的工作记忆；在理论上可以容纳任意长度的上下文，但代价是注意力计算复杂度和缓存开销随序列长度增长。[^kv_survey_2024]
- **精细粒度表示**：以 token 为单位存储所有中间表示，不丢失语义细节，是当前长上下文 Transformer 在知识密集型任务中表现良好的重要原因。[^kv_survey_2024]
- **高计算/存储开销**：标准自注意力在长序列下呈二次计算复杂度，且 KV cache 占用显存线性增长，这也是各类 KV 管理与长上下文加速方法重点优化的瓶颈。[^kv_survey_2024][^llm_in_flash_2024]
- **无显式遗忘**：除非显式采用压缩、剪枝、合并等策略，否则历史 token 的 KV 一旦写入便一直保留，使得有用信息和噪声一并累积，对极长对话和文档建模带来干扰。[^kv_survey_2024]

这种形式的工作记忆适用于需要精确访问远距离信息的任务，但在极长上下文、在线推理和高吞吐场景下会受到计算与存储瓶颈制约，因此近年来围绕 KV cache 管理、稀疏注意力和外存/分层存储等方向出现了大量优化工作。[^kv_survey_2024][^llm_in_flash_2024]

#### Hidden State

循环神经网络（RNN）、状态空间模型（SSM）以及线性注意力等架构维持一个固定维度的隐状态，并在时间维不断更新，用以压缩和携带历史信息。其主要特性包括：
- **固定容量**：隐状态维度不随序列长度增长，因此在推理时内存与计算成本关于序列长度呈线性关系甚至接近常数，是对 Transformer 二次复杂度的一种结构性改进。[^rwkv_2023][^mamba_2023]
- **强概括性**：通过状态迭代将历史信息持续压缩，SSM 与线性注意力类模型在百万级 token 序列上仍能保持稳定建模能力，体现出对长时依赖的更好适配。[^mamba_2023][^deltanet_2024]
- **可能的遗忘/混叠**：固定维度的状态在长期迭代中不可避免地发生信息覆盖和混叠，不同时间步的信息被压缩到同一向量中，导致细粒度细节难以长期保留，近期工作通过选择性更新、门控机制和 delta rule 等方式缓解这一问题。[^mamba_2023][^gated_deltanet_2024]
- **架构依赖性**：不同模型（如 RWKV、Mamba、DeltaNet、Gated DeltaNet 等）在状态更新规则和并行化策略上差异显著，直接影响其工作记忆的表达能力、长度外推性能以及推理吞吐。[^rwkv_2023][^mamba_2023][^deltanet_2024][^gated_deltanet_2024]

这种形式的工作记忆更接近“紧凑型”短期记忆：通过固定大小的状态在时间维上滚动，适用于长程序列建模、高效推理以及在有限资源下维持较长的上下文轨迹。


### Update



#### Structure Update

##### Caching

Transformer 的工作记忆理论上可以包含所有输入信息，其本质是不断将新的短期记忆和旧的记忆进行 concat 操作：

$$S_t = \text{concat}(S_{t-1}, \{k_t, v_t\})$$

Transformer 的挑战在于超长序列的处理，包括平方级的计算复杂度、长度外推问题、缓存容量。因此，研究主要围绕在训练和推理阶段扩展工作记忆的长度。核心问题包括：
- 超长上下文处理，早期工作在长上下文场景中无法泛化。
- 训练推理效率，当上下文突破几十万时，如何加速和减少显存。

##### Pruning





##### Compression

- Compressive Transformer[^3]设计了一种压缩旧token并保存的方法，实现了真正意义上的记忆，旧tokens进行了压缩（max/mean pooling、1D convolution、dilated convolutions和most-used），同时为了验证压缩效果，他们尝试使用压缩后的记忆重建压缩前的内容。
- Linformer本质是将kv cache进行整体压缩，类似于pooling，最后得到一个整体的向量嵌入与新的token进行计算。
- **[KeepKV](https://arxiv.org/abs/2504.09936) (2025.04)**：自适应KV cache合并方法，实现单步无损压缩，提供多步压缩误差边界。在仅10% KV cache budget下仍保持生成质量，2x+ throughput提升。
- **[SentenceKV](https://github.com/zzbright1998/SentenceKV) (COLM 2025)**：句子级语义KV cache压缩。不同于token级压缩，利用语言自然结构按语义相似性聚类句子，保留重要上下文信息的同时实现高效压缩。


#### State Update

基于循环架构的神经网络通常维护一个固定大小的隐层状态：

$$S_t = f(S_{t-1}, x_t)$$

通常在大语言模型中，状态的更新同样与 **k** 和 **v** 有关：

$$S_t = f(S_{t-1}, g(k_t, v_t))$$


相关工作包括：
- 基于Recurrent Architecture（非Transformer）
	- **[Hierarchical Reasoning Model (HRM)](https://arxiv.org/abs/2506.21734) (2025.06)**：27M参数的dual-module recurrent architecture，包含high-level module（slow abstract planning）和low-level module（rapid detailed computations）。纯recurrent设计without attention mechanism。在Sudoku和ARC benchmark上表现出色，仅用1000 training samples即可达到近乎完美性能。
	- **[Tiny Recursive Model (TRM): Less is More](https://arxiv.org/abs/2510.04871) (2025.10)**：仅7M参数、2层的simplified recursive reasoning model。纯recurrent架构，通过parameter sharing和latent "think"-"act" alternation在ARC-AGI-1达到45%准确率，超越大多数LLMs（DeepSeek R1, o3-mini, Gemini 2.5 Pro）且参数量不到0.01%。
- 基于Linear Attention
	- **[RWKV-7 "Goose"](https://arxiv.org/abs/2503.14456) (2025.03)**：最强纯RNN架构，**meta-in-context learner**——每个token都进行test-time state training（通过in-context gradient descent）。新提出vector-valued gating和relaxed value replacement rule。2.9B参数达3B多语言SOTA，理论上可识别所有正则语言（超越Transformer能力）。Apache 2.0开源，Linux Foundation项目。
	- **[Gated DeltaNet](https://arxiv.org/abs/2412.06464) (NVlabs/清华, ICLR 2025)**：将gating与delta rule结合，gating实现快速memory erasure，delta rule实现精确memory updates。超越Mamba2和DeltaNet，已被**Qwen3-Next**采用作为linear component。
	- **[Log-Linear Attention](https://arxiv.org/abs/2506.04761) (2025.06)**：用对数增长的hidden states集合替代固定大小hidden state，平衡线性注意力效率和softmax注意力表达能力。通过特定growth function实现log-linear序列长度计算复杂度。
	- DeltaNet：delta rule-like update检索和更新与当前key关联的value vector
	- RetNet：retention mechanism实现O(1)推理复杂度
	- GLA：hardware-efficient gated linear attention
	- RWKV-6：线性注意力RNN，可扩展到14B参数
- 基于state-space models (SSMs)
	- Mamba：选择性SSM，3B匹配6B Transformer
	- Mamba2：State Space Duality，2-8x faster than Mamba
- Meta Learning（Test-Time Training）
	- **[TTT: Test-Time Training Layers](https://arxiv.org/abs/2407.04620) (ICML 2024)**：hidden state通过gradient descent在test sequences上更新。TTT-Linear和TTT-MLP两种实例化。
	- **[Titans](https://arxiv.org/abs/2501.00663) (Google, 2025.01)**：Neural long-term memory module，通过momentum和weight decay（遗忘机制）实现更好的记忆管理。在所有三个任务上超越baselines。
	- **[TNT: Improving Chunkwise Training](https://arxiv.org/abs/2511.07343) (2025.11)**：改进Titans/TTT的chunkwise训练，实现**17倍训练加速**同时提升模型准确率。
	- *注：TTT 类方法通过梯度下降在推理时更新参数，理论上可作为长期记忆的候选。但目前仍受限于保存内容长度和处理速度，实际使用中更接近工作记忆。*
- LSTM
	- mLSTM


**前缀微调 (Prefix Tuning)**：Li 和 Liang（2021）提出将一段可训练的连续向量（前缀）添加到Transformer每层输入，以在不改动预训练模型参数的情况下调整生成结果​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=parameters%20and%20therefore%20necessitates%20storing,performance%20in%20the%20full%20data)​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=%E2%80%9Cvirtual%20tokens%E2%80%9D.%20We%20apply%20prefix,that%20are%20unseen%20during%20training)。只需训练不到0.1%的参数，前缀微调在全量数据下性能接近全模型微调，在低资源和未见主题上甚至更佳​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=parameters%20and%20therefore%20necessitates%20storing,performance%20in%20the%20full%20data)​[aclanthology.org](https://aclanthology.org/2021.acl-long.353/#:~:text=%E2%80%9Cvirtual%20tokens%E2%80%9D.%20We%20apply%20prefix,that%20are%20unseen%20during%20training)。




#### Hybrid Update

混合更新策略结合了Transformer的无限扩展能力和RNN/SSM的固定状态更新，在同一模型中使用多种架构组件。Hybrid Update分为两大类：

##### Hybrid Cache

Hybrid Cache方法仍然基于Transformer架构，保持了self-attention机制的核心。与纯Structure Update（所有token直接缓存）不同，这类方法在推理逻辑中对cache进行了不同程度的State Update——即对部分或全部缓存内容进行压缩、迭代更新或循环处理，从而在保持Transformer表达能力的同时，通过状态化处理降低memory开销或增强信息编码。

根据混合发生的维度，Hybrid Cache分为两个子类：

**Token-wise Approaches**

这类方法在token维度上进行混合：使用一部分tokens作为固定大小的memory（State Update），同时对当前输入tokens使用standard attention（Structure Update）：

- **[Memformer: A Memory-Augmented Transformer for Sequence Modeling](https://aclanthology.org/2022.findings-aacl.29/) (2020.10, AACL 2022)**：Encoder-decoder架构，在encoder使用N个fixed-size memory slots（State），decoder对当前tokens用standard attention（Structure）。通过similarity-based retrieval实现O(n)时间、O(1)空间复杂度。
- **[AutoCompressors: Adapting Language Models to Compress Contexts](https://arxiv.org/abs/2305.14788) (2023.05, EMNLP 2023)**：历史segments压缩为50个summary vectors（State），当前2048-token segment用standard attention（Structure）。Summary vectors作为soft prompts参与language modeling。
- **[Block-Recurrent Transformers](https://arxiv.org/abs/2203.07852) (DeepMind/Google, 2022.03)**：Block内standard attention + Transformer-XL cache（Structure），block间维护512个recurrent state vectors（State）。12层模型在第10层使用recurrence。
- **[Recurrent Memory Transformer (RMT)](https://arxiv.org/abs/2207.06881) (2022.07, NeurIPS 2022)**：Memory tokens在segments间传递（State），segment内standard attention（Structure）。10个memory tokens + 512 input可编码2000+ tokens，retain信息across 2M tokens。
- **[Cached Transformers with GRC](https://arxiv.org/abs/2312.12742) (2023.12)**：历史tokens压缩为fixed-length vectors with GRC gates（State），当前tokens用standard attention（Structure）。自适应更新压缩表示。
- **[Infini-attention: Leave No Context Behind](https://arxiv.org/abs/2404.07143) (2024.04)**：Single block内整合compressive memory with fixed parameters（State）和masked local attention（Structure）。1B模型从1M contexts检索，114x memory reduction。
- **[Compact Recurrent Transformer with Persistent Memory](https://arxiv.org/abs/2505.00929) (2025.05)**：GRU/NCGRU persistent memory跨迭代（State），每迭代内standard transformer处理current sequence（Structure）。

**Layer-wise Approaches**

这类方法在layer/depth维度上进行混合：通过循环复用transformer layers进行depth recurrence（State Update），但每个depth step仍对sequence做full attention并维护growing KV cache（Structure Update）。

- **[Universal Transformers](https://arxiv.org/abs/1807.03819) (2018.07, ICLR 2019)**：Google Brain开创性工作，在depth维度iteratively refine所有symbols的representations。使用shared self-attention + shared transformation across all positions and time-steps，支持variable depth via Adaptive Computation Time (ACT)。每个recurrent step并行处理整个sequence with full attention（Structure），while depth recurrence provides iterative refinement（State）。
- **[Looped Transformers for Length Generalization](https://arxiv.org/abs/2409.15647) (2024.09, NeurIPS 2024)**：威斯康星、MIT、UC Berkeley联合工作。所有decoder blocks共享参数，通过adaptive loop steps提升length generalization。模型在特定steps后才supervised to match target，学习可复用intermediate steps。Depth recurrence（State）+ standard attention per step（Structure）。
- **[Reasoning with Latent Thoughts](https://openreview.net/forum?id=din0lGfZFd) (ICLR 2025)**：证明k-layer transformer looped L times性能接近kL-layer non-looped model。Looped models implicitly generate latent thoughts，可simulate T steps of CoT with T loops。展示depth-based scaling类似CoT inference-time scaling。
- **[Relaxed Recursive Transformers](https://arxiv.org/abs/2410.20672) (DeepMind, 2024.10, ICLR 2025)**：CYCLE strategy（single block recursively reused），通过layer-wise LoRA relax weight tying。Recursive Gemma 1B outperforms TinyLlama/Pythia，recover大部分Gemma 2B性能。支持Continuous Depth-wise Batching，2-3x inference throughput。Depth recurrence with parameter sharing（State）+ full attention in each cycle（Structure）。
- **[Mixture-of-Recursions (MoR)](https://arxiv.org/abs/2507.10524) (KAIST/DeepMind, 2025.07, NeurIPS 2025)**：统一recursive framework，lightweight routers实现adaptive token-level recursion depths。创新性recursion-wise KV caching：仅缓存active tokens at给定recursion depth，减少memory traffic。2x inference throughput，同时减少training FLOPs。Token-level adaptive depth（State）+ selective KV caching（Structure）。
- **[Ouro: Scaling Latent Reasoning via Looped Language Models](https://arxiv.org/abs/2510.25741) (2025.10)**：Pre-trained Looped LM，将reasoning build into pre-training through iterative latent computation + entropy-regularized depth allocation，scaling to 7.7T tokens。使用4 recurrent steps，Ouro 1.4B/2.6B匹配12B SOTA LLMs。Parameter-shared looped architecture在latent space迭代（State）+ standard decoder-only Transformer blocks（Structure）。

##### Hybrid State


我记得是有方法，将Linear Attention的State复制多份的









### Usage


#### Attention


##### Sparsification

- Longformer[^4]首次提出了稀疏注意力，让模型在计算注意力时，只关注部分关键位置的token，极大减少了模型训练和推理的开销。
	- 值得注意的是，在使用滑动窗口推理时，存在Attention Sink问题[^10]，因此需要保留最前面的几个tokens。不过和本篇的相关性不大，因此不做详细拓展。
- LongNet[^7]沿着Longformer的思路进行了一些改进，本质上用了三种注意力掩码的组合，来保证计算量呈线性。
- **[NSA: Native Sparse Attention](https://arxiv.org/abs/2502.11089) (DeepSeek, 2025.02, ACL 2025 Best Paper)**：硬件对齐的可训练稀疏注意力。三分支并行设计：compressed attention处理粗粒度模式，selected attention选择重要token块，sliding attention处理局部上下文。Lightning indexer快速定位相关片段，显著降低计算成本同时保持性能。
- **[MoBA: Mixture of Block Attention](https://arxiv.org/abs/2502.13189) (Moonshot AI, 2025.02, ICLR 2025)**：将MoE思想应用于注意力机制，块级稀疏注意力+路由机制。与NSA独立提出类似思路，通过可学习路由选择相关块进行注意力计算。
- **[CCA: Core Context Aware Attention](https://arxiv.org/abs/2412.12465) (2025.08)**：即插即用的高效长上下文模块。globality-aware pooling将token组压缩为core tokens，locality-preserving module保持细节表示。可直接替换现有LLM的self-attention，最小微调成本。


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
	- 滑动窗口
		- StreamingLLM
		- Λ-Mask
- 注意力增强
	- Sigmoid Attention
		- SWAT
	- SSMAX增强的softmax函数
	- moe



#### Recurrent Network




#### Hybrid Architecture

这类方法在模型架构层面混合不同类型的层：State Update layers（SSM/RNN）与Structure Update layers（Transformer attention）。根据混合方式的不同，可以分为三类：

**Interleaved（交替混合）**

SSM/RNN层和Attention层在depth维度交替排列，是目前最主流的混合方式：

- **[H3: Hungry Hungry Hippos](https://arxiv.org/abs/2212.14052) (2022.12, ICLR 2023)**：Stanford HazyResearch开创性工作，SSM层为主 + 少量Attention层。提出FlashConv加速，Hybrid 125M模型在OpenWebText上比同规模Transformer低1.0 PPL。2.7B hybrid模型在多数SuperGLUE任务上匹配或超越Transformer。
- **[Griffin](https://arxiv.org/abs/2402.19427) (Google DeepMind, 2024.02)**：提出RG-LRU（Real-Gated Linear Recurrent Unit）+ 滑动窗口注意力交替设计。每2个RG-LRU块后跟1个attention块。RecurrentGemma是其生产版本（2B/9B参数），在长序列上throughput显著优于MQA Transformers。
- **[Jamba](https://arxiv.org/abs/2403.19887) (AI21 Labs, 2024.03)**：首个生产级大规模混合架构，Transformer + Mamba + MoE三层设计。每个Jamba块含8层（Attention:Mamba=1:7），隔层应用MoE（16专家激活2）。52B总参数中12B活跃，支持256K上下文，单80GB GPU可运行。
- **[Zamba](https://arxiv.org/abs/2405.16712) (Zyphra, 2024.05)**：Mamba骨干 + 全局**共享**注意力层。每6个Mamba块共享1个attention层，大幅减少参数量。7B参数优化边缘部署，即使单个attention层也能达到transformer级别的ICL能力。
- **[Samba](https://arxiv.org/abs/2406.07522) (Microsoft, 2024.06, ICLR 2025)**：Mamba + MLP + 滑动窗口注意力(SWA) + MLP层级堆叠。SWA窗口2048 + RoPE。3.8B参数在Phi3数据集上训练，MMLU/GSM8K/HumanEval超越Phi3-mini，解码速度比Llama-3快3.64倍，支持1M上下文zero-shot。
- **[Jamba-1.5](https://arxiv.org/abs/2408.12570) (AI21 Labs, 2024.08)**：增强版Jamba，推出Large (94B活跃参数) 和 Mini (12B活跃参数) 两个版本。256K上下文（开源模型最长），提出ExpertsInt8量化技术支持8×80GB GPU运行Large版本。
- **[Bamba](https://huggingface.co/blog/bamba) (IBM/Princeton/CMU/UIUC, 2024.12)**：**Mamba2** + Attention，29个SSM层 + 3个attention层。9B参数在2T tokens上训练，匹配Llama-3.1-8B性能（后者用15T tokens）。2.5x throughput，2x latency speedup。
- **[Zamba2](https://arxiv.org/abs/2411.15242) (Zyphra, 2024.11)**：Mamba2骨干 + **双共享attention块**（ABAB交替模式）+ LoRA投影实现depth-wise specialization。1.2B/2.7B/7.4B三个规模，同级别SOTA。开源预训练数据集Zyda-2。
- **[Kimi Linear (KDA)](https://arxiv.org/abs/2510.26692) (Moonshot AI, 2025.10)**：**首次线性注意力在公平比较下全面超越全注意力**。核心是Kimi Delta Attention (KDA)，扩展Gated DeltaNet加入finer-grained gating。KDA与MLA按3:1混合，48B总参数(3B活跃)。75% KV cache减少，1M上下文6x解码吞吐。在AIME 2025、HMMT 2025等多个benchmark上超越baselines。开源KDA kernel和vLLM实现。
- **[RWKV-X](https://arxiv.org/abs/2504.21463) (2025.04)**：RWKV-7 + Transformer混合架构，结合两者优势实现线性复杂度。探索SSM与注意力机制最优融合方式。

**Parallel（并行混合）**

同一层内同时运行SSM头和Attention头，充分利用两者优势：

- **[Hymba](https://arxiv.org/abs/2411.13676) (NVIDIA, 2024.11)**：混合头并行架构，同层内Transformer头和SSM头并行运行（参数比例5:1）。引入128个可学习meta tokens存储关键信息，跨层KV缓存共享 + 部分滑动窗口注意力。Hymba-1.5B超越所有sub-2B模型，比Llama-3.2-3B高1.32%准确率，cache减少11.67倍，throughput提升3.49倍。

**Component-level（组件级混合）**

在attention模块内部融合SSM/RNN组件：

- **[Mega](https://arxiv.org/abs/2209.10655) (Meta, 2022.09, ICLR 2023)**：将指数移动平均(EMA)整合到Gated Attention机制中，单头设计。Mega-chunk变体实现线性时间空间复杂度，比vanilla Transformer快5.5倍，内存仅13%。在Long Range Arena所有6个任务上显著超越S4。
- **[StripedHyena](https://www.together.ai/blog/stripedhyena-7b) (Together AI, 2023.12)**：Hyena卷积块（处理大部分序列计算）+ Attention块（targeted pattern recall）组合。支持128K上下文训练，>500K tokens生成。在32K/64K/128K长度上训练分别比FlashAttention v2快30%/50%/100%以上。






#### Retrieval

- [Memorizing Transformer](../../../../4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/+Papers/Memorizing%20Transformers.md)[^6]将LLM特定层（第9层）训练时计算得到的kv cache保存作为记忆。下一个batch计算到第9层的时候，对于每一个token，模型使用knn查找与之相关的记忆（旧tokens的hidden states）并加入计算，最后汇总得到新tokens的hidden states。（具体细节需要再总结）
- 2022.11 [Large Language Models with Controllable Working Memory](https://arxiv.org/abs/2211.05110) 提出了一种可控工作记忆框架，通过调整注意力优先级增强 LLM 对上下文的处理能力，显著提升了多任务表现。
- Focused Transformer和Memorizing Transformers很像，都是保存了以前文本的hidden states，然后用于后续的文本训练。同时添加了负样本提升模型对于海量记忆的筛选能力。
- 2023.06 [Augmenting Language Models with Long-Term Memory](https://dl.acm.org/doi/10.5555/3666122.3669381) 训练了额外的SideNet来索引Cached Memory Bank，支持token-to-chunk检索。
- [LM2: Large Memory Model](https://arxiv.org/html/2502.06049v1) 加州大学和Anthropic联合团队提出的辅助记忆模块架构，通过上下文记忆库和动态门控机制实现多步推理能力提升，在BABILong基准测试上相比Llama-3.2提升86.3%。
- [NAMMs: Neural Attention Memory Models](https://arxiv.org/) 帝国理工学院和Sakana AI提出的进化式记忆优化模型，采用进化算法优化记忆管理，实现零样本跨架构和模态迁移。







%%
### 相关技术

工作记忆的有效性不仅依赖架构，还需要管理机制来优化信息存储和使用。

#### 硬件加速

- Transformer Hidden States
	- [LLM in a flash: Efficient Large Language Model Inference with Limited Memory](https://aclanthology.org/2024.acl-long.678/)通过将模型参数存储在闪存，提升推理时可以支持的计算长度。

%%





## Long-term Memory


相比较Sensory Memory，长期记忆不应该存储原始的信息，而是将知识编码到稳定的模型参数中。

相比较Working Memory，长期记忆是更加稳定的记忆，不会因为推理过程而快速遗忘。

长期记忆涉及到的领域较为广泛，模型的训练、幻觉问题、知识编辑都可以当做模型长期记忆的更新。这里选取一些典型研究。目前长期记忆的挑战是如何高效、鲁棒地实现记忆更新。

考虑记忆的形式、容量、更新：
- **形式**：模型参数
- **容量**：通常为固定大小，可添加和复制扩展（如MoE、LoRA）
- **更新**：多种模型训练方法（预训练、微调、知识编辑）

核心挑战是避免**灾难性遗忘**（catastrophic forgetting）——在学习新知识时保留已有知识。


```
Long-term Memory
├── Forms
│   ├── Dense Parameters
│   ├── Sparse/MoE (专家结构设计)
│   └── Adapters/LoRA
├── Operations
│   ├── Update
│   │   ├── Structure Update (Adding: LoRA/新专家, Deleting: 剪枝/Unlearning)
│   │   └── State Update (预训练、微调、RLHF、知识编辑)
│   └── Compress (蒸馏、量化、Model Merging)
└── Usage
    ├── MoE Routing
    └── Ensemble
```


### Forms

Parameters

#### PEFT modules

PEFT (Parameter-Efficient Fine-Tuning)参数高效微调通过仅更新少量参数来实现知识更新，是一种高效的长期记忆修改方式。

**Adapter 方法：**
- [Parameter-Efficient Transfer Learning for NLP](https://arxiv.org/abs/1902.00751) (ICML 2019, Houlsby et al.) **Classic**: 原始 Adapter 论文，在 Transformer 层间插入瓶颈结构，仅增加 3.6% 参数达到接近全微调性能。
- [AdapterHub: A Framework for Adapting Transformers](https://arxiv.org/abs/2007.07779) (EMNLP 2020): Adapter 统一框架和预训练 Adapter 共享平台。
- [LLM-Adapters: An Adapter Family for Parameter-Efficient Fine-Tuning](https://aclanthology.org/2023.emnlp-main.319/) (EMNLP 2023): 系统研究 Adapter 类型、位置和超参数的影响，7B 模型可达 175B 模型零样本性能。

**LoRA 系列：**
- [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685) (ICLR 2022) **Classic**: 低秩矩阵分解，冻结原参数仅训练低秩增量。
- [QLoRA: Efficient Finetuning of Quantized LLMs](https://arxiv.org/abs/2305.14314) (NeurIPS 2023): 4-bit 量化 + LoRA，65B 模型可在单 48GB GPU 上微调。
- [LoRA-FA: Memory-efficient Low-rank Adaptation](https://arxiv.org/abs/2308.03303) (2023): 冻结 A 矩阵仅训练 B，减少激活内存最多 13GB。
- [DoRA: Weight-Decomposed Low-Rank Adaptation](https://arxiv.org/abs/2402.09353) (ICML 2024): 分解权重为幅度和方向，超越 LoRA。






#### MoE

MoE  Mixture of experts通过稀疏激活扩展模型容量，每个专家可以存储特定领域的知识，是一种结构化的长期记忆扩展方式。综述：[A Survey on Mixture of Experts in Large Language Models](https://arxiv.org/abs/2407.06204) (TKDE 2025)；[A Comprehensive Survey of Mixture-of-Experts](https://arxiv.org/abs/2503.07137) (2025)

MoE 的核心设计包括：路由机制（选择策略 + 负载均衡）、专家结构（粒度和组织）、系统优化（并行训练与通信）。



**专家结构 (Expert Design)：**

*FFN-MoE：*
- [DeepSeekMoE](https://aclanthology.org/2024.acl-long.70/) (ACL 2024): (1) 细粒度切分：N 个专家 → mN 个更小专家，激活 mK 个（如 8 选 2 → 64 选 16），组合更灵活；(2) 共享专家：部分专家设为"共享"处理通用知识，其余做路由专注特定知识，解决知识冗余。
- [MoNE](https://arxiv.org/abs/2510.05781) (2025): 神经元级稀疏，在专家内部再做 top-k 神经元选择，仅激活 50% 参数即可匹配传统 MoE。
- [MoE++](https://openreview.net/forum?id=7efe88bb4138d602e56637cfcf713654) (ICLR 2025): 异构专家，引入零计算专家——zero（丢弃）、copy（直接复制）、constant（常量替换），简单 token 跳过 FFN，复杂 token 用更多专家，吞吐量提升 1.1-2.1x。
- [Matryoshka MoE](https://arxiv.org/abs/2509.26520) (2025): 训练时变化激活专家数，构建粗到细的专家层级，单一模型支持弹性推理。

*Attention-MoE：*
- [MoA](https://arxiv.org/abs/2210.05144) (EMNLP 2022): 将注意力头视为专家，路由器动态选择 k 个头。
- [MoH](https://arxiv.org/abs/2410.11842) (2024): Mixture-of-Head，LLaMA3-8B 仅用 75% 注意力头提升 2.4%。

*统一架构：*
- [UMoE](https://arxiv.org/abs/2505.07260) (2025): 统一 Attention 和 FFN 的 MoE，揭示注意力中隐含的 FFN 结构。




**MoE + LoRA（混合专家 + 参数高效）：**

将 LoRA 适配器作为专家，结合 MoE 路由实现多任务/多领域的参数高效适配。

- [MOELoRA](https://arxiv.org/abs/2310.18339) (SIGIR 2024): 每个专家为低秩矩阵对，任务驱动门控选择专家组合。
- [LoRA-MoE](https://aclanthology.org/2024.acl-long.106/) (ACL 2024): LoRA 适配器作为专家，冻结主干网络，有效缓解世界知识遗忘。
- [MoRA](https://arxiv.org/abs/2506.21035) (2025): Mixture-of-Rank Adaptive，细粒度 rank 级选择替代 adapter 级，减少冗余和干扰。








### Update


#### Structure Update

两个视角，一个是从operation角度，一个是从forms角度。forms角度：

**1. MoE-based**

**新知识添加：**

通过增加新专家或扩展现有专家来存储新知识，避免覆盖原有知识。

- [Lifelong-MoE](https://arxiv.org/abs/2305.12281) (ACL 2023): 动态添加专家处理新数据分布，配合正则化策略保留旧知识。问题：专家数量线性增长导致内存膨胀。
- [R2MoE](https://arxiv.org/abs/2507.13107) (2025): Redundancy-Removal MoE，轻量化终身学习，通过冗余消除控制专家数量增长。
- [DEMix](https://aclanthology.org/2023.emnlp-main.516/) (EMNLP 2023): 每个领域一个专家，新领域只需训练新专家，模块化扩展。

**知识隔离与防遗忘：**

通过专家隔离防止新知识覆盖旧知识，是 MoE 在持续学习中的核心优势。
- [OMoE](https://arxiv.org/abs/2503.07137) (2025): 正交约束减少专家表示冗余，鼓励专家行为解耦。
- [DES-MoE](https://arxiv.org/abs/2509.16882) (EMNLP 2025): 动态专家专业化，实时专家-领域映射隔离梯度，**遗忘减少 89%**，收敛加速 68%。
- [TRGE](https://arxiv.org/abs/2508.07738) (2025): 两级路由分组，专家组隔离防止遗忘 + 组间路由实现跨任务协作。
- [Theory on MoE in CL](https://arxiv.org/abs/2406.16437) (ICLR 2025 Spotlight): 理论证明 MoE 门控网络可稀疏化分配任务给不同专家；建议充分训练后**冻结门控网络**以稳定收敛。
- [CL-MoE](https://openaccess.thecvf.com/content/CVPR2025/papers/Huai_CL-MoE_Enhancing_Multimodal_Large_Language_Model_with_Dual_Momentum_Mixture-of-Experts_CVPR_2025_paper.pdf) (CVPR 2025): 双动量 MoE，实例级 + 任务级路由用于多模态持续学习。

**知识删除 (Unlearning)：**

通过定向修改特定专家实现知识遗忘，无需重训整个模型。

- [UOE: Unlearning One Expert](https://openreview.net/forum?id=ZClm0YbcXP) (2025): 通过专家归因定位目标知识所在专家，仅对该专家进行 unlearning，配合锚定损失稳定路由。遗忘质量提升 5%，模型效用提升 35%，仅修改 0.06% 参数。

**挑战：**

- **知识混杂 (Knowledge Hybridity)**：专家数量有限时，单个专家被迫存储多样化知识。细粒度专家可缓解。
- **知识冗余 (Knowledge Redundancy)**：不同专家学到重复的通用知识。共享专家隔离可缓解。
- **专业化-泛化平衡**：过度专业化导致泛化能力下降，如何平衡是开放问题。
- **内存增长**：动态添加专家导致模型规模线性增长。


**2. PEFT-based**


operation角度：


**添加**


**删除**



##### Compression


###### Distillation


###### Quantization


###### Pruning



###### Model Merging


#### State Update

状态更新是长期记忆的核心操作，通过修改模型参数来更新知识。主要途径包括：预训练、微调、强化学习对齐，以及知识编辑。

##### Pre-training


*数据策略：*

数据优化是预训练的核心研究方向，涵盖数据混合 (Data Mixing)、课程学习 (Curriculum Learning)、合成数据 (Synthetic Data) 等多个子领域。我们按照优化目标进行统一分类：

域混合 - 优化不同来源/领域数据的比例。
- [DoReMi](https://arxiv.org/abs/2305.10429) (NeurIPS 2024): 域重加权，分布鲁棒优化，2.6 倍加速。
- [Data Mixing Laws](https://arxiv.org/abs/2403.16952) (2024): 数据混合律，标量函数预测性能，小规模拟合大规模泛化。
- [RegMix](https://arxiv.org/abs/2407.01492) (ICLR 2025 Spotlight): 回归框架优化混合，仅需代理模型 10% 计算。
- [Topic Over Source](https://arxiv.org/abs/2502.16802) (2025): 主题级混合优于源级混合，语义主题划分。
- [Efficient Online Data Mixing](https://arxiv.org/abs/2312.02406) (2023): 多臂老虎机在线数据混合，19% 更少迭代达到目标困惑度。
- [Chameleon](https://arxiv.org/abs/2505.24844) (ICML 2025): 核岭回归杠杆分数量化域重要性，1% 成本匹配 DoReMi。
- [UtiliMax](https://arxiv.org/abs/2501.11747) (2025): LLM 估计数据效用，10 倍以上加速。

顺序调度 - 优化训练样本的顺序（课程学习）。
- [Vocabulary Curriculum](https://arxiv.org/abs/2502.17910) (2025): 词表课程学习，熵引导词汇扩展，动态分词。
- [Beyond Random Sampling](https://arxiv.org/abs/2506.11300) (2025): 首次系统性研究预训练课程学习，15 个难度指标。
- [CAMPUS](https://arxiv.org/abs/2509.13790) (2024): 能力感知课程学习，动态调整难度指标适应模型能力演变。
- [E2H Reasoner](https://arxiv.org/abs/2506.06632) (2025): 课程强化学习，简到难任务调度提升推理能力。

数据生成 - 使用模型生成训练数据（合成数据）。
- [Demystifying Synthetic Data](https://arxiv.org/abs/2510.01631) (2024): 大规模实证研究，混合合成数据 5-10 倍加速，但纯生成存在模型坍塌风险。
- [Scaling Laws of Synthetic Data](https://arxiv.org/abs/2503.19551) (2025): SynthLLM 框架验证合成数据遵循校正缩放律。
- [Deliberate Practice](https://arxiv.org/abs/2502.15588) (ICLR 2025): 受刻意练习启发，仅生成具有挑战性的样本改善缩放律。

质量控制 - 数据清洗、去重和质量评估。
- [Ultra-FineWeb](https://arxiv.org/abs/2505.05427) (2025): 高效 fastText 分类器 + 验证策略，1 万亿高质量 tokens。
- [QuaDMix](https://arxiv.org/abs/2504.16511) (2025): 质量-多样性统一优化框架，7.2% 平均性能提升。
- [Oasis](https://arxiv.org/abs/2311.12537) (IJCAI 2024): 一站式数据策展平台，规则过滤 + 神经过滤 + 自适应去重。
- [Deduplicating Training Data](https://arxiv.org/abs/2107.06499) (2021): 去重减少 10 倍背诵输出。

*高效方法：*
- [GaLore](https://arxiv.org/abs/2403.03507) (ICML 2024): 梯度低秩投影，37.92GB 内存节省，保持全参数学习。
- [GaLore 2](https://arxiv.org/abs/2504.20437) (2025): 扩展到大规模预训练。

##### Continual Pre-training

在已有模型基础上继续预训练，避免从头重新训练。核心挑战是灾难性遗忘和分布转移。

*学习率策略：*
- [How to (re)warm your model?](https://arxiv.org/abs/2308.04014) (2023): 学习率重新加热策略，优化分布转移问题。
- [Simple and Scalable Strategies](https://arxiv.org/abs/2403.08763) (TMLR 2024): 学习率重预热 + 重衰减 + 回放，仅需 10% 语料库成本匹配完全重训练。
- [Beyond Cosine Decay](https://arxiv.org/abs/2503.02844) (CoLLAs 2025): 无限学习率调度器，避免余弦衰减的重预热遗忘问题。
- [Learning Dynamics in CPT](https://arxiv.org/abs/2505.07796) (ICML 2025 Oral): 连续预训练缩放律，解耦分布偏移和学习率退火效应。

*数据策略：*

数据混合与回放 - 平衡新旧数据防止遗忘。
- [D-CPT Law](https://arxiv.org/abs/2406.01375) (2024): 域特定连续预训练缩放律，预测最优通用-域数据混合比例。
- [Efficient CPT by Mitigating Stability Gap](https://arxiv.org/abs/2406.14833) (2024): 发现并解决"稳定性缺口"，多轮低质量数据预训练减少 80% 计算成本。
- [SuRe: Surprise-Driven Replay](https://arxiv.org/abs/2511.22367) (2025): 基于"惊讶度"优先级选择回放样本，双学习器 EMA-merged LoRA。
- [Reuse, Don't Retrain](https://arxiv.org/abs/2407.07263) (NVIDIA, 2024): 双分布策略和优化学习率调度，15B 模型 9% 平均准确率提升。

*领域适应：*
- [AdaptLLM](https://arxiv.org/abs/2309.09530) (ICLR 2024): 将语料转换为阅读理解格式，7B 模型性能接近 50B BloombergGPT。
- [ChipNeMo](https://arxiv.org/abs/2311.00176) (2023): 芯片设计领域完整适应流程，70B 超越 GPT-4。
- [PMC-LLaMA](https://arxiv.org/abs/2304.14454) (2023): 医学领域三阶段方法，13B 超越 ChatGPT。
- [Code Llama](https://arxiv.org/abs/2308.12950) (2023): 500B 代码导向连续预训练。

##### Fine-tuning

**指令微调 (Instruction Tuning)**

*数据生成：*
- [Self-Instruct](https://arxiv.org/abs/2212.10560) (ICLR 2024): 自生成指令对进行自举对齐。
- [WizardCoder/Evol-Instruct](https://arxiv.org/abs/2306.08568) (ICLR 2024): 复杂指令细调增强代码 LLM。
- [Automatic Instruction Evolving](https://arxiv.org/abs/2406.00770) (2024): 自动分析和选择进化策略，端到端指令演化。
- [OpenCodeInstruct](https://arxiv.org/abs/2504.04030) (2025): 整合 Self-Instruct 和 Evol-Instruct，500 万样本最大代码指令数据集。

*数据选择：*
- [Large-Scale Data Selection](https://arxiv.org/abs/2503.01807) (2025): 大规模自动化数据筛选，精选子集优于噪声全集。
- [GRAPE](https://arxiv.org/abs/2502.04194) (2025): 数据适配性匹配，用 4.5 倍数据量超越强基线 6.1%。
- [Importance-Aware Data Selection](https://arxiv.org/abs/2511.07074) (2025): 模型指令弱点值(MIWV)，仅用 1% 精选数据超越全量训练。
- [Superfiltering](https://arxiv.org/abs/2402.00530) (2024): 弱模型进行数据过滤加速指令调优。
- [Rethinking Data Selection at Scale](https://arxiv.org/abs/2410.09335) (2024): 随机选择在大规模数据中的有效性。

*多轮对话：*
- [Parrot](https://arxiv.org/abs/2310.07301) (2023): 学习真实用户查询模式，迭代收集多轮指令对。
- [Review-Instruct](https://arxiv.org/abs/2505.11010) (2025): 迭代式"问-答-评审"过程合成多轮对话。
- [Conversation Forests](https://arxiv.org/abs/2507.04099) (2025): 分支对话架构的强化学习框架。

*评估基准：*
- [IFEval](https://arxiv.org/abs/2311.07911) (2023): 直接可验证指令集合。
- [InFoBench](https://arxiv.org/abs/2401.03601) (2024): 分解需求跟随率指标，2250 道分解问题细粒度评测。
- [EIFBENCH](https://arxiv.org/abs/2506.08375) (2025): 极端复杂多任务多约束场景评测。

**多任务微调**

- [Cocktail Effect](https://arxiv.org/abs/2410.01109) (2024): 多任务数据混合产生协同增强，Phi-3-Mini 超越 GPT-4o。
- [SafeGrad](https://arxiv.org/abs/2508.07172) (2025): 梯度外科手术处理多目标冲突，检测并消除有害梯度分量。
- [CGC-LoRA](https://arxiv.org/abs/2402.01684) (2024): 自定义门控控制 LoRA，解决多任务"跷跷板问题"。
- [Bonito](https://arxiv.org/abs/2402.18334) (2024): 自动化创建领域特定指令调优数据集，条件任务生成。

**参数高效微调进展** (见 PEFT 部分详细介绍)

- [DoRA](https://arxiv.org/abs/2402.09353) (ICML 2024 Oral): 权重分解为幅度和方向，方向用 LoRA 优化，仅增加 0.01% 参数但超越 LoRA。
- [Dual LoRA](https://arxiv.org/abs/2512.03402) (2025): 同时优化幅度和方向，改进 DoRA 设计。

##### RL Alignment

**偏好优化**

*DPO 及变体：*
- [DPO](https://arxiv.org/abs/2305.18290) (NeurIPS 2023): 直接偏好优化，无需奖励模型，闭形式最优策略。
- [SimPO](https://arxiv.org/abs/2405.14734) (NeurIPS 2024): 长度归一化平均对数概率作为隐式奖励，无需参考模型。
- [KTO](https://arxiv.org/abs/2402.01306) (ICML 2024): 基于前景理论，仅需二元信号(好/坏)，无需配对数据。
- [ORPO](https://arxiv.org/abs/2403.07691) (EMNLP 2024): 无参考模型的单步偏好对齐，对数赔率比项。
- [Provably Robust DPO](https://arxiv.org/abs/2403.00409) (ICML 2024): 自适应去偏处理标签翻转噪声，理论收敛保证。
- [Rainbow PO](https://arxiv.org/abs/2410.04203) (2024): 统一框架集成 DPO、IPO、SimPO 等多个改进。

*迭代/在线方法：*
- [INPO](https://arxiv.org/abs/2407.00617) (ICLR 2025 Oral): 纳什均衡框架，自博弈无遗憾学习。
- [RTO](https://arxiv.org/abs/2404.18922) (ICML 2025 Spotlight): DPO 学习令牌级奖励，结合 PPO 进行细粒度策略优化。
- [Hybrid Preference Optimization](https://arxiv.org/abs/2412.10616) (2024): 结合离线偏好数据与在线探索，加快收敛。

*PPO 替代方案：*
- [GRPO](https://arxiv.org/abs/2402.03300) (DeepSeekMath, 2024): 组相对策略优化，无评论家网络，降低内存开销。
- [DAPO](https://arxiv.org/abs/2503.14476) (字节跳动, 2025): 解耦 clip + 动态采样，AIME 2024 达 50 分，完全开源。

**奖励模型改进**

*过程奖励模型 (PRM)：*
- [Rewarding Progress](https://arxiv.org/abs/2410.08146) (2024): 过程奖励定义为"进展"——步骤前后正确答案可能性的变化。
- [AgentPRM](https://arxiv.org/abs/2502.10325) (2025): 轻量级 actor-critic + 蒙特卡洛 rollout，3B 模型超越 GPT-4o 基线。
- [Conditional RM](https://arxiv.org/abs/2509.26578) (2025): 条件概率建模步间依赖，联系每步奖励与最终结果。

*奖励黑客防护：*
- [Beyond Reward Hacking](https://arxiv.org/abs/2501.09620) (2025): 因果推断整合反事实不变性，消除长度偏差、谄媚等虚假相关性。
- [Reward Shaping PAR](https://arxiv.org/abs/2502.18770) (2025): 有界且快速初始增长后平缓收敛的奖励设计。
- [ODIN](https://arxiv.org/abs/2402.07319) (2024): 分离多个奖励维度，PPO 裁剪目标防止过度优化。
- [InfoRM](https://arxiv.org/abs/2402.09345) (2024): 信息论方法改进奖励模型，减少虚假相关性。

*自改进机制：*
- [Self-Rewarding LMs](https://arxiv.org/abs/2401.10020) (2024): 模型自身作为评分器，迭代改进指令跟随和奖励能力。

**多目标对齐**

- [MetaAligner](https://arxiv.org/abs/2403.17141) (NeurIPS 2024): 三阶段方法，动态目标重组，插拔式多目标对齐。
- [PAMA](https://arxiv.org/abs/2508.07768) (ECML PKDD 2025): O(n) 复杂度凸优化求解帕累托最优。
- [HoE](https://arxiv.org/abs/2505.20925) (2025): 分层 LoRA + 路由专家混合，无需额外模型训练达到帕累托前沿。

**安全对齐**

- [Constitutional AI](https://arxiv.org/abs/2212.08073) (Anthropic): 通过规则原则引导 AI 自我改进，RLAIF 方法论创始性工作。
- [MTSA](https://arxiv.org/abs/2505.17147) (ACL 2025): 思维引导攻击学习 + 对抗迭代优化，多轮强化学习优化安全对齐。
- [MUSE](https://arxiv.org/abs/2509.14651) (2024): 蒙特卡洛树搜索探索多轮攻击，细粒度转向级防御对齐。
- [RTVLM](https://arxiv.org/abs/2401.12915) (ACL 2024): 首个覆盖保真度、隐私、安全、公平的多模态红队数据集。

##### Knowledge Editing

直接修改模型参数以更新特定事实，不影响其他知识。

*定位-编辑方法：*
- [ROME](https://arxiv.org/abs/2202.05262) (2022) **Classic**: Rank-One Model Editing，因果追踪定位 + 低秩更新。
- [MEMIT](https://arxiv.org/abs/2210.07229) (2022) **Classic**: 批量编辑成千上万个事实。
- [AlphaEdit](https://arxiv.org/abs/2410.02355) (ICLR 2025 **Outstanding Paper**): 零空间约束编辑，最小化对原有知识干扰。
- [GeoEdit](https://arxiv.org/abs/2502.19953) (2025): 几何关系区分新知识神经元和通用知识神经元。
- [MEMIT-Merge](https://arxiv.org/abs/2502.07322) (ACL 2025 Findings): 解决 MEMIT 同主体批量编辑中的关键-值冲突。

*元学习方法：*
- [MALMEN](https://arxiv.org/abs/2311.04661) (ICLR 2024): 参数偏移聚合为最小二乘问题，可编辑超过 1000 条事实，比 MEND 提高百倍。
- [InstructEdit](https://arxiv.org/abs/2405.15349) (IJCAI 2024): 元学习多任务指令训练，相比 MEND 可靠性提升 14.86%。

*检索增强编辑：*
- [RECIPE](https://arxiv.org/abs/2405.03279) (EMNLP 2024): 知识转换为连续提示，知识哨兵动态判断是否需要检索。
- [ReMaKE](https://arxiv.org/abs/2312.13040) (2023): 首次实现多语言知识编辑，从多语言知识库检索。
- [KEDAS](https://arxiv.org/abs/2508.01302) (2025): 多样化表示和自适应推理，超越基线约 19.8%。

*多跳组合编辑：*
- [MQuAKE](https://arxiv.org/abs/2305.14795) (EMNLP 2023): 首个多跳问答知识编辑基准。
- [PokeMQA](https://arxiv.org/abs/2312.15194) (ACL 2024): 问题分解与范围检测分离编辑任务，引入冲突检测机制。
- [CHECK](https://arxiv.org/abs/2508.00914) (IJCAI 2025): 基于编译原理对多跳推理进行语义分析和类型检查，22.8% 准确度提升。

*终身编辑：*
- [WISE](https://arxiv.org/abs/2405.14768) (NeurIPS 2024): 双参数记忆机制，分离主记忆和侧记忆，解决"不可能三角"。
- [WikiBigEdit](https://arxiv.org/abs/2503.05683) (ICML 2025): 真实维基百科数据大规模基准，500K+ 问答对。
- [MindBridge](https://arxiv.org/abs/2503.02701) (ACL 2025 Findings): 记忆模态概念实现跨模型编辑，模型更新后可快速迁移知识。

*工具：*[EasyEdit](https://github.com/zjunlp/EasyEdit) - 支持 ROME、MEMIT、MEND 等多种方法的统一框架。

##### 核心挑战

1. 灾难性遗忘

学习新知识时覆盖旧知识，源于参数共享机制。核心是**可塑性-稳定性困境**。

**理论分析：**
- [Global Convergence on Non-IID Data](https://arxiv.org/abs/2503.18511) (2025): 首次为非 IID 连续学习建立几乎必然收敛结果。
- [Understanding Forgetting with Linear Regression](https://arxiv.org/abs/2405.17583) (ICML 2024): 理论分析任务序列和算法参数对遗忘的影响。
- [Information-Theoretic Bounds for Replay](https://arxiv.org/abs/2507.12043) (2025): 有限样本回放相比完全回放能实现更好泛化。

**缓解技术**（已融入上述各部分）：
- **重放**：保存或生成旧样本复习（见连续预训练-数据策略）
- **正则化**：约束重要参数变化（见 PEFT、知识编辑）
- **架构方法**：为新任务分配新模块（见 MoE、LoRA）
- **知识蒸馏**：用旧模型指导新模型

**综述**：[Towards Lifelong Learning of LLMs: A Survey](https://dl.acm.org/doi/10.1145/3716629) (ACM Computing Surveys, 2025)


2. 新知识快速鲁棒学习和旧知识的遗忘




### Usage

Inference


#### Routing

路由决定每个 token 由哪些专家处理，核心问题是选择策略和负载均衡。

*选择策略：*
- Token-Choice (Top-K)：token 选择得分最高的 K 个专家。[GShard](https://arxiv.org/abs/2006.16668) (2020) 提出 Top-2 随机路由；[Switch Transformers](https://arxiv.org/abs/2101.03961) (2021) 简化为 Top-1 实现万亿参数模型。问题：负载不均衡、专家利用率低。
- Expert-Choice：专家选择 Top-K tokens，**天然负载均衡**，每个 token 可被 0 到多个专家处理。[Expert Choice Routing](https://arxiv.org/abs/2202.09368) (NeurIPS 2022) 训练速度提升 2x+。
- [TC-MoE](https://openreview.net/forum?id=dsP91M4hDL) (2025): Ternary Choice，用 {-1, 0, 1} 扩展专家空间，激活专家数减少 9%，性能提升 1.1%。

*负载均衡：*
- 辅助损失：[GShard](https://arxiv.org/abs/2006.16668) 引入负载均衡损失惩罚不均。问题：干扰梯度影响性能。
- 无辅助损失：[Auxiliary-Loss-Free Load Balancing](https://arxiv.org/abs/2408.15664) (ICLR 2025) 通过动态偏置调整路由分数，**DeepSeek-V3** 采用，实现**零 token 丢弃**。
- [MoLE](https://arxiv.org/abs/2503.15798) (ICML 2025 Oral): 专家输入改为 embedding 层输出，推理前将 FFN 专家重参数化为查找表（LUT），根据 token id 直接查表，可 offload 到存储设备，延迟接近 dense 模型。





#### Ensemble






## Related Surveys

| Paper | Date | Venue | Focus |
| :--- | :--- | :--- | :--- |
| **[A Survey on the Memory Mechanism of Large Language Model based Agents](https://dl.acm.org/doi/abs/10.1145/3748302)** | 2025-07 | TOIS | Comprehensive survey on Agent memory (Sources, Forms, Operations). |
| **[From Human Memory to AI Memory: A Survey on Memory Mechanisms in the Era of LLMs](https://arxiv.org/abs/2504.15965)** | 2025-04 | arXiv | Proposes 3D-8Q Memory Taxonomy (Object, Form, Time). |
| **[Cognitive Memory in Large Language Models](https://arxiv.org/abs/2504.02441)** | 2025-04 | arXiv | Classifies into Text-based, KV Cache, Parameters, and Hidden-State memory. |
| **[Rethinking Memory in AI: Taxonomy, Operations, Topics, and Future Directions](https://arxiv.org/abs/2505.00675)** | 2025-05 | arXiv | Focuses on 6 atomic operations: Consolidation, Indexing, Updating, Forgetting, Retrieval, Compression. |
| **[Advances and Challenges in Foundation Agents](https://arxiv.org/abs/2504.01990)** | 2025-04 | arXiv | Meta's survey; detailed mapping of human memory models to agents. |
| **[Memory Meets (Multi-Modal) Large Language Models](https://openreview.net/forum?id=Sk7pwmLuAY)** | 2025-10 | TMLR | Focuses on Implicit, Explicit, and Agentic memory in Multi-modal contexts. |
| **[Towards Lifelong Learning of Large Language Models: A Survey](https://arxiv.org/abs/2406.06391)** | 2025-03 | ACM Comp. Surv. | Focuses on continual learning and catastrophic forgetting. |




## 一些无法访问原文的工作

- [MindMemory: Augmented With Long-Term And Mental Personality](https://link.springer.com/chapter/10.1007/978-981-96-2373-0_33)


Large Language Models with Controllable Working Memory




Letta (previously MemGPT)[^next]这个GitHub项目也是使用纯文



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
[^next]: [Efficient Streaming Langun Sinks](https://openreview.netG7sS51zVF)


[^cognitive_memory_llm]: L. Shan et al. *Cognitive Memory in Large Language Models*. 2025. [https://arxiv.org/abs/2504.02441](https://arxiv.org/abs/2504.02441)
[^memory3_explicit]: H. Yang et al. *Memory³: Language Modeling with Explicit Memory*. 2024. [https://arxiv.org/abs/2407.01178](https://arxiv.org/abs/2407.01178)
[^gong2024_wm_capacity]: D. Gong et al. *Working Memory Capacity of ChatGPT: An Empirical Study*. AAAI 2024. [https://arxiv.org/abs/2305.03731](https://arxiv.org/abs/2305.03731)
[^selfattention_limits_2024]: A. (Authors). *Self-Attention Limits Working Memory Capacity*. 2024. [https://arxiv.org/abs/2409.10715](https://arxiv.org/abs/2409.10715)
[^wm_identifies_limits_2024]: C. Zhang et al. *Working Memory Identifies Reasoning Limits in Language Models*. EMNLP 2024. [https://aclanthology.org/2024.emnlp-main.938](https://aclanthology.org/2024.emnlp-main.938)
[^huang2025_llm_not_human_wm]: J. Huang et al. *LLMs Do Not Have Human-Like Working Memory*. 2025. [https://arxiv.org/abs/2505.10571](https://arxiv.org/abs/2505.10571)
[^cog_psych_memory_2025]: Authors. *Analyzing Memory Effects in LLMs through Cognitive Psychology*. 2025. [https://arxiv.org/abs/2509.17138](https://arxiv.org/abs/2509.17138)
[^kv_cache_survey_2025]: Authors. *A Survey on LLM Acceleration based on KV Cache Management*. TMLR 2025. [https://arxiv.org/abs/2412.19442](https://arxiv.org/abs/2412.19442)
[^transformerfam_2024]: D. Hwang et al. *TransformerFAM: Feedback Attention is Working Memory*. 2024. [https://arxiv.org/abs/2404.09173](https://arxiv.org/abs/2404.09173)
[^think_in_memory_2023]: L. Liu et al. *Think-in-Memory: Recalling and Post-thinking Enable LLMs with Long-Term Memory*. 2023. [https://arxiv.org/abs/2311.08719](https://arxiv.org/abs/2311.08719)
[^lm2_large_memory_2025]: Authors. *LM2: Large Memory Models*. 2025. [https://arxiv.org/abs/2502.06049](https://arxiv.org/abs/2502.06049)
[^ewe_explicit_wm_2025]: M. Chen et al. *Improving Factuality with Explicit Working Memory (EWE)*. ACL 2025. [https://arxiv.org/abs/2412.18069](https://arxiv.org/abs/2412.18069)
[^vector_longterm_2024]: Y. Zhang et al. *Vector Storage Based Long-term Memory Research on LLM*. 2024. [https://sciendo.com/article/10.2478/ijanmc-2024-0029](https://sciendo.com/article/10.2478/ijanmc-2024-0029)





