## Batch size

关于batch size：

- For example, LLama2 fine-tuning uses a batch size of 64 (i.e., 64 sequences of 4096 tokens) and pretraining uses a batch size of 4M tokens.
- Papers like the GPT-3 paper seem to use a batch size of ~250K tokens (so 250 sequences of 1000 tokens, or 125 sequences of 2000 tokens) for models of GPT-2 Small size, but empirically batch sizes of more like 100K seem to do much better early in training. But maybe they make training noisier which makes the point of convergence higher? I’m unsure!
- [MiniLLM](https://arxiv.org/pdf/2407.11681)
- [预训练大模型（不是），关于 batch size 的实验](https://zhuanlan.zhihu.com/p/692260057)