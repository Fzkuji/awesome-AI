Title: Training Compute-Optimal Large Language Models

总结：

DeepMind研究人员重新审视了Kaplan的结论，发现缩放训练数据量与缩放模型大小一样重要。模型大小每增加一倍，训练集大小也应该增加一倍。

他们证明了这一点。这篇新论文的明星是Chinchilla，这是一个70B参数的模型，比语言AI的前任领导者Gopher（也由 DeepMind 构建）小4倍，但训练的数据多4倍。研究人员发现，Chinchilla在大量语言基准测试中“一致且显着”地优于Gopher、GPT-3、Jurassic-1和Megatron-Turing NLG。

**结论很明确，当前的大型语言模型“明显训练不足”，这是盲目遵循缩放假设的结果——使模型变大并不是提高性能的唯一途径。模型参数比训练数据应为1比21.2。**

## Introduction

### Question

Kaplan et al. (2020) concluded that:

- Large models should not be trained to their lowest possible loss to be compute optimal. ✔️
- Given a $10×$ increase computational budget, they suggests that the size of the model should increase $5.5×$ while the number of training tokens should only increase $1.8×$. ❌

Instead, we find that model size and the number of training tokens should be scaled in equal proportions.

In this work, we revisit the question: 
**Given a fixed FLOPs budget, how should one trade-off model size and the number of training tokens?**

### Setup

To answer this question, we model the final pre-training loss $L(N, D)$ as a function of 
- the number of model parameters $N$, 
- and the number of training tokens, $D$. 
Since the computational budget $C$ is a deterministic function $\operatorname{FLOPs}(N, D)$ of the number of seen training tokens and model parameters, we are interested in minimizing $L$ under the constraint $\operatorname{FLOPs}(N, D)=C$ :
$$
N_{\text {opt }}(C), D_{\text {opt }}(C)=\underset{N, D \text { s.t. } \operatorname{FLOPs}(N, D)=C}{\operatorname{argmin}} L(N, D) .
$$
The functions $N_{\text {opt }}(C)$, and $D_{\text {opt }}(C)$ describe the optimal allocation of a computational budget $C$. 

### Answer

Our approach leads to considerably different results than that of Kaplan.

Gopher should be 4 times smaller, while being training on 4 times more tokens (data).

## Estimating the optimal parameter/training tokens allocation

本文设计了三种实验

### Approach 1: Fix model sizes and vary number of training tokens

固定模型大小（尝试了多种大小的模型），更改训练数据大小

![Figure 2](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Chinchilla/IMG-20240910115137319.png)

Finally, we fit power laws to estimate the optimal model size and number of training tokens for any given amount of compute (see the center and right panels of Figure 2), obtaining a relationship $N_{\text {opt }} \propto C^a$ and $D_{\text {opt }} \propto C^b$. We find that $a=0.50$ and $b=0.50$. 

### Approach 2: IsoFLOP profiles

固定计算预算大小（尝试了多个预算），更改模型大小，观察损失函数

![Figure 3](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Chinchilla/IMG-20240910115137592.png)

As with the previous approach, we then fit a power law between FLOPs and loss-optimal model size and number of training tokens, shown in Figure 3 (center, right). Again, we fit exponents of the form $N_{o p t} \propto C^a$ and $D_{o p t} \propto C^b$ and we find that $a=0.49$ and $b=0.51$.

### Approach 3: Fitting a parametric loss function

用一个简单的函数去拟合方法1和方法2实验结果，最后观察最优解。

用于拟合的函数为：
$$
\hat{L}(N, D) \triangleq E+\frac{A}{N^\alpha}+\frac{B}{D^\beta}
$$
- 第一项捕获了理想生成过程在数据分布上的损失，并且应该对应于自然文本的熵。
- 第二项捕获了一个具有$N$参数的经过完美训练的变压器不如理想生成过程的事实。
- 最后一个术语抓住了这样一个事实，即转换器没有被训练成收敛，因为我们只在数据集分布的样本上进行有限数量的优化步骤。

最后需要得到参数$(A, B, E, \alpha, \beta)$

#### Model fitting

损失函数为：
$$
\min _{A, B, E, \alpha, \beta} \sum_{\text {Runs } i} \operatorname{Huber}_\delta\left(\log \hat{L}\left(N_i, D_i\right)-\log L_i\right)
$$
文章使用了[](../../../../../2.%20Approaches/Artificial%20neural%20network/Loss%20function.md#Huber%20loss%7CHuber%20loss)

#### Efficient frontier

为了加速计算，文章使用了Kaplan论文中的结论：
$$
\operatorname{FLOPs}(N, D) \approx 6 N D
$$
We can approximate the functions $N_{o p t}$ and $D_{o p t}$ by minimizing the parametric loss $\hat{L}$ under the constraint $\operatorname{FLOPs}(N, D) \approx 6 N D$ (Kaplan et al., 2020). The resulting $N_{\text {opt }}$ and $D_{\text {opt }}$ balance the two terms in Equation (3) that depend on model size and data. By construction, they have a power-law form:
$$
N_{o p t}(C)=G\left(\frac{C}{6}\right)^a, \quad D_{o p t}(C)=G^{-1}\left(\frac{C}{6}\right)^b, \quad \text { where } \quad G=\left(\frac{\alpha A}{\beta B}\right)^{\frac{1}{\alpha+\beta}}, \quad a=\frac{\beta}{\alpha+\beta}, \text { and } b=\frac{\alpha}{\alpha+\beta} \text {. }
$$
We show contours of the fitted function $\hat{L}$ in [](.md#^910ca9%7CFigure%204%20(left)), and the closed-form efficient computational frontier in blue. From this approach, we find that $a=0.46$ and $b=0.54$-as summarized in Table 2 .

![Figure 4](../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/Chinchilla/IMG-20240910115137733.png) ^910ca9

### Optimal model scaling

最终三种方法的结果如Table 2 & 3，结论就是目前的模型太大了，训练数据太少了。

Table 3显示，模型参数: 训练数据=1: 21.2，即

Table 2 | **Estimated parameter and data scaling with increased training compute.** The listed values are the exponents, $a$ and $b$, on the relationship $N_{o p t} \propto C^a$ and $D_{o p t} \propto C^b$. Our analysis suggests a near equal scaling in parameters and data with increasing compute which is in clear contrast to previous work on the scaling of large models. The $10^{\text {th }}$ and $90^{\text {th }}$ percentiles are estimated via bootstrapping data (80\% of the dataset is sampled 100 times) and are shown in parenthesis.

| Approach | Coeff. $a$ where $N_{o p t} \propto C^a$ | Coeff. $b$ where $D_{o p t} \propto C^b$ |
| :--- | :---: | :---: |
| 1. Minimum over training curves | 0.50(0.488,0.502) | 0.50(0.501,0.512) |
| 2. IsoFLOP profiles | 0.49(0.462,0.534) | 0.51(0.483,0.529) |
| 3. Parametric modelling of the loss | 0.46(0.454,0.455) | 0.54(0.542,0.543) |
| Kaplan et al. (2020) | 0.73 | 0.27 |

Table 3 | **Estimated optimal training FLOPs and training tokens for various model sizes.** For various model sizes, we show the projections from Approach 1 of how many FLOPs and training tokens would be needed to train compute-optimal models. The estimates for Approach 2 & 3 are similar.

| Parameters  |  FLOPs   | FLOPs (in *Gopher* unit) |     Tokens     |
|:-----------:|:--------:|:------------------------:|:--------------:|
| 400 Million | 1.92e+19 |         1/29,968         |  8.0 Billion   |
|  1 Billion  | 1.21e+20 |         1/4,761          |  20.2 Billion  |
| 10 Billion  | 1.23e+22 |           1/46           | 205.1 Billion  |
| 67 Billion  | 5.76e+23 |            1             |  1.5 Trillion  |
| 175 Billion | 3.85e+24 |           6.7            |  3.7 Trillion  |
| 280 Billion | 9.90e+24 |           17.2           |  5.9 Trillion  |
| 520 Billion | 3.43e+25 |           59.5           | 11.0 Trillion  |
| 1 Trillion  | 1.27e+26 |          221.3           | 21.2 Trillion  |
| 10 Trillion | 1.30e+28 |         22515.9          | 216.2 Trillion |

## Chinchilla

之前DeepMind一直提及的是模型Gopher（原来也是他们做的，280B），于是他们就把模型调整到了他们认为合适的大小，使用了更多的数据进行训练——70B parameters和1.4T tokens

### Model and training details

- We train Chinchilla on MassiveText (the same dataset as Gopher) but use a slightly different subset distribution (shown in Table A1) to account for the increased number of training tokens.
- We use AdamW (Loshchilov and Hutter, 2019) for Chinchilla rather than Adam (Kingma and Ba, 2014) as this improves the language modelling loss and the downstream task performance after finetuning. ^f76ade
- We train Chinchilla with a slightly modified SentencePiece (Kudo and Richardson, 2018) tokenizer that does not apply NFKC normalisation. The vocabulary is very similar -- $94.15 \%$ of tokens are the same as those used for training Gopher. We find that this particularly helps with the representation of mathematics and chemistry, for example.
- Whilst the forward and backward pass are computed in bfloat16, we store a float32 copy of the weights in the distributed optimiser state (Rajbhandari et al., 2020). See Lessons Learned from Rae et al. (2021) for additional details.

Table $4 \mid$ **Chinchilla architecture details.** We list the number of layers, the key/value size, the bottleneck activation size $\mathrm{d}_{\text {model }}$, the maximum learning rate, and the training batch size (\# tokens). The feed-forward size is always set to $4 \times \mathrm{d}_{\text {model }}$. Note that we double the batch size midway through training for both Chinchilla and Gopher.

| Model | Layers | Number Heads | Key/Value Size | $d_{model}$ | Max LR | Batch Size |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Gopher 280B | 80 | 128 | 128 | 16,384 | $4 \times 10^{-5}$ | $3 \mathrm{M} \rightarrow 6 \mathrm{M}$ |
| Chinchilla 70B | 80 | 64 | 128 | 8,192 | $1 \times 10^{-4}$ | $1.5 \mathrm{M} \rightarrow 3 \mathrm{M}$ |

### Results

总体就是比Gopher强

#### MMLU

大部分任务都比Gopher强。

但是on four tasks (college_mathematics, econometrics, moral_scenarios, and formal_logic) Chinchilla underperforms Gopher, and there is no change in performance on two tasks.

似乎也是比较难的问题，可能参数量不够了。

#### Reading comprehension

比Gopher强

#### BIG-bench

Of the 62 tasks we consider, Chinchilla performs worse than Gopher on only four—crash_blossom, dark_humor_detection, mathematical_induction and logical_args. Full accuracy results for Chinchilla can be found in Table A7.


## Others

其他的具体看论文吧
