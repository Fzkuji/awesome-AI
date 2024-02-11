Title: LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention

关键技术：
- Zero-init Attention
- Unified Multi-modal Tuning

## Zero-init Attention

“Zero-init Attention”是 LLaMA-Adapter 中使用的一种技术，用于减轻直接插入随机初始化模块引起的预训练语言知识的潜在干扰，这是流行的 PEFT（提示工程和微调）方法中的常见做法。

该技术涉及使用门控机制自适应地合并指令信号，同时在 LLaMA 中保留预训练的生成知识。 通过采用这种技术，LLaMA-Adapter 可以在早期训练阶段以更小的损失值实现更稳定的微调。

## Unified Multi-modal Tuning

LLaMA-Adapter 能够以统一的方式处理语言和多模态微调，这不同于以前的 PEFT 方法，这些方法通常是为解决语言、图像和音频等特定模态而开发的。 这种处理多种模式的能力使 LLaMA-Adapter 能够展示出卓越的泛化能力。 您可以在 PDF 的第 3 页找到更多相关信息。













