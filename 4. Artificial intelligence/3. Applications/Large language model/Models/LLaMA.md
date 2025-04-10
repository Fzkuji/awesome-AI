
[Read the paper](https://arxiv.org/abs/2302.13971)
[Read the model card](https://github.com/facebookresearch/llama/blob/main/MODEL_CARD.md)
[Apply for access to LLaMA](https://docs.google.com/forms/d/e/1FAIpQLSfqNECQnMkycAp2jP4Z9TFX0cGR4uf7b_fBxjY_OjhJILlKGA/viewform)

![600](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/LLaMA/IMG-20240910115137325.png)

## Approach

### Pre-training Data

| Dataset | Sampling prop. | Epochs | Disk size |
| :--- | :---: | :---: | ---: |
| CommonCrawl | 67.0% | 1.10 | 3.3 TB |
| C4 | 15.0% | 1.06 | 783 GB |
| Github | 4.5% | 0.64 | 328 GB |
| Wikipedia | 4.5% | 2.45 | 83 GB |
| Books | 4.5% | 2.23 | 85 GB |
| ArXiv | 2.5% | 1.06 | 92 GB |
| StackExchange | 2.0% | 1.03 | 78 GB |

### Architecture

#### Pre-normalization.

[Root Mean Square Layer Normalization](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Normalization.md#Root%20Mean%20Square%20Layer%20Normalization) used in [GPT-3](GPT/GPT-123.md)

#### SwiGLU activation function

[SwiGLU](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Activation%20function.md#SwiGLU)

We use a dimension of $\frac{2}{3} 4d$ instead of $4d$ as in PaLM.

#### Rotary embeddings

[Rotary positional embedding](../../../2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/Transformer/Position%20embedding.md#Rotary%20positional%20embedding) used by GPTNeo

### Optimizer

[AdamW](2.%20Approaches/Artificial%20neural%20network/Optimizer.md#AdamW)

### Efficient implementation
*Tricks*

We make several optimizations to **improve the training speed** of our models.

1. First, we use an efficient implementation of the **causal multi-head attention** to reduce memory usage and runtime.
	- This implementation, available in the [xformers library](https://github.com/facebookresearch/xformers), is inspired by Rabe and Staats (2021) and uses the backward from Dao et al. (2022). This is achieved by not storing the attention weights and not computing the key/query scores that are masked due to the causal nature of the language modeling task.
2. We reduced the amount of activations that are recomputed during the backward pass with checkpointing.
	- More precisely, we save the activations that are expensive to compute, such as the outputs of linear layers. This is achieved by manually implementing the backward function for the transformer layers, instead of relying on the PyTorch autograd.
3. Moreover, we also overlap the computation of activations and the communication between GPUs over the network (due to `all_reduce` operations) as much as possible.

## Instruction finetuning

Although the non-finetuned version of LLaMA-65B is already able to follow basic instructions, we observe that **a very small amount of finetuning improves the performance** on massive multitask language understanding (MMLU), and further improves the ability of the model to follow instructions.

## Bias, toxicity and misinformation

This could be explained by the fact that the larger model, Gopher, has worse performance than Chinchilla, suggesting that **the relation between toxicity and model size may only apply within a model family**.

Our model is likely to hallucinate incorrect answers.

比GPT-3菜一点


