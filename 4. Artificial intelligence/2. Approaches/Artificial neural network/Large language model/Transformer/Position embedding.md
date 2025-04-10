Position embeddings are a technique used in natural language processing (NLP) and other sequence-based tasks to provide information about the position of tokens within a sequence. This is particularly important in models like the Transformer architecture, where the self-attention mechanism does not inherently capture positional information.

## Related materials

[Transformer中的位置编码(Position Encoding)](https://0809zheng.github.io/2022/07/01/posencode.html)
- 这个是一篇较早的总结，但是已经很全面了，总结了几个比较经典的工作



## Background

*From [RoFormer: Enhanced Transformer with Rotary Position Embedding](https://arxiv.org/abs/2104.09864)*

### Preliminary

Let $\mathbb{S}_N=\left\{w_i\right\}_{i=1}^N$ be a sequence of $N$ input tokens with $w_i$ being the $i^{\text {th }}$ element. The corresponding word embedding of $\mathbb{S}_N$ is denoted as $\mathbb{E}_N=\left\{\boldsymbol{x}_i\right\}_{i=1}^N$, where $\boldsymbol{x}_i \in \mathbb{R}^d$ is the d-dimensional word embedding vector of token $w_i$ without position information. The self-attention first incorporates position information to the word embeddings and transforms them into queries, keys, and value representations: 
$$
\begin{aligned}
\boldsymbol{q}_m & =f_q\left(\boldsymbol{x}_m, m\right) \\
\boldsymbol{k}_n & =f_k\left(\boldsymbol{x}_n, n\right) \\
\boldsymbol{v}_n & =f_v\left(\boldsymbol{x}_n, n\right),
\end{aligned}
$$
^Equation1

where $\boldsymbol{q}_m, \boldsymbol{k}_n$ and $\boldsymbol{v}_n$ incorporate the $m^{t h}$ and $n^{t h}$ positions through $f_q, f_k$ and $f_v$, respectively. The query and key values are then used to compute the attention weights, while the output is computed as the weighted sum over the value representation.
$$
\begin{aligned}
a_{m, n} & =\frac{\exp \left(\frac{\boldsymbol{q}_m^{\top} \boldsymbol{k}_n}{\sqrt{d}}\right)}{\sum_{j=1}^N \exp \left(\frac{\boldsymbol{q}_m^{\top} \boldsymbol{k}_j}{\sqrt{d}}\right)} \\
\mathbf{o}_m & =\sum_{n=1}^N a_{m, n} \boldsymbol{v}_n
\end{aligned}
$$
^Equation2

The existing approaches of transformer-based position encoding mainly focus on choosing a suitable function to form the first [](.md#^Equation1%7CEquation%20(1)).

### Absolute position embedding

**Absolute Position Encodings** are a type of position embeddings for [Transformer](https://paperswithcode.com/method/transformer)-based models where positional encodings are added to the input embeddings at the bottoms of the encoder and decoder stacks. The positional encodings have the same dimension $d_{model}$ as the embeddings, so that the two can be summed. 

A typical choice of the [](.md#^Equation1%7CEquation%20(1)) is:
$$
f_{t: t \in\{q, k, v\}}\left(\boldsymbol{x}_i, i\right):=\boldsymbol{W}_{t: t \in\{q, k, v\}}\left(\boldsymbol{x}_i+\boldsymbol{p}_i\right),
$$
^Equation3

where $\boldsymbol{p}_i \in \mathbb{R}^d$ is a d-dimensional vector depending of the position of token $\boldsymbol{x}_i$. Previous work introduced the use of a set of trainable vectors $\boldsymbol{p}_i \in\left\{\boldsymbol{p}_t\right\}_{t=1}^L$, where $L$ is the maximum sequence length. The authors of *Attention Is All You Need* have proposed to generate $\boldsymbol{p}_i$ using the sinusoidal function.

#### Sinusoidal positional embedding

In the original implementation, sine and cosine functions of different frequencies are used:
$$\text{PE}\left(pos, 2i\right) = \sin\left(pos/10000^{2i/d_{model}}\right) $$
$$ \text{PE}\left(pos, 2i+1\right) = \cos\left(pos/10000^{2i/d_{model}}\right) $$
^Equation4

where $pos$ is the position and $i$ is the dimension. That is, each dimension of the positional encoding corresponds to a sinusoid. The wavelengths form a geometric progression from $2 \pi$ to $10000 \dot{2} \pi$. This function was chosen because the authors hypothesized it would allow the model to easily learn to attend by relative positions, since for any fixed offset $k, \mathrm{PE}_{\text {pos }+k}$ can be represented as a linear function of $\mathrm{PE}_{\text {pos }}$

### Relative position embedding

The authors of Shaw et al. applied different settings of [](.md#^Equation1%7CEquation%20(1)) as following:
$$
\begin{array}{r}
f_q\left(\boldsymbol{x}_m\right):=\boldsymbol{W}_q \boldsymbol{x}_m \\
f_k\left(\boldsymbol{x}_n, n\right):=\boldsymbol{W}_k\left(\boldsymbol{x}_n+\tilde{\boldsymbol{p}}_r^k\right) \\
f_v\left(\boldsymbol{x}_n, n\right):=\boldsymbol{W}_v\left(\boldsymbol{x}_n+\tilde{\boldsymbol{p}}_r^v\right)
\end{array}
$$
^Equation5

where $\tilde{\boldsymbol{p}}_r^k, \tilde{\boldsymbol{p}}_r^v \in \mathbb{R}^d$ are trainable relative position embeddings. Note that $r=\operatorname{clip}\left(m-n, r_{\min }, r_{\max }\right)$ represents the relative distance between position $m$ and $n$. They clipped the relative distance with the hypothesis that precise relative position information is not useful beyond a certain distance. Keeping the form of [](.md#^Equation3%7CEquation%20(3)), the authors Dai et al. have proposed to decompose $\boldsymbol{q}_m^{\top} \boldsymbol{k}_n$ of [](.md#^Equation2%7CEquation%20(2)) as
$$
\boldsymbol{q}_m^{\boldsymbol{\top}} \boldsymbol{k}_n=\boldsymbol{x}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\boldsymbol{x}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{p}_n+\boldsymbol{p}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\boldsymbol{p}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{p}_n
$$
^Equation6

the key idea is to replace the absolute position embedding $\boldsymbol{p}_n$ with its sinusoid-encoded relative counterpart $\tilde{\boldsymbol{p}}_{m-n}$, while the absolute position $\boldsymbol{p}_m$ in the third and fourth term with two trainable vectors $\mathbf{u}$ and $\mathbf{v}$ independent of the query positions. Further, $\boldsymbol{W}_k$ is distinguished for the content-based and location-based key vectors $\boldsymbol{x}_n$ and $\boldsymbol{p}_n$, denoted as $\boldsymbol{W}_k$ and $\widetilde{\boldsymbol{W}}_k$, resulting in:
$$
\boldsymbol{q}_m^{\boldsymbol{\top}} \boldsymbol{k}_n=\boldsymbol{x}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\boldsymbol{x}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \widetilde{\boldsymbol{W}}_k \tilde{\boldsymbol{p}}_{m-n}+\mathbf{u}^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\mathbf{v}^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \widetilde{\boldsymbol{W}}_k \tilde{\boldsymbol{p}}_{m-n}
$$
^Equation7

It is noteworthy that the position information in the value term is removed by setting $f_v\left(\boldsymbol{x}_j\right):=\boldsymbol{W}_v \boldsymbol{x}_j$. Later work followed these settings by only encoding the relative position information into the attention weights. However, Raffel et al. reformed [](.md#^Equation6%7CEquation%20(6)) as:
$$
\boldsymbol{q}_m^{\boldsymbol{\top}} \boldsymbol{k}_n=\boldsymbol{x}_m^{\top} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+b_{i, j}
$$
^Equation8

where $b_{i, j}$ is a trainable bias. The authors of Ke et al. investigated the middle two terms of [](.md#^Equation6%7CEquation%20(6)) and found little correlations between absolute positions and words. The authors of Raffel et al. proposed to model a pair of words or positions using different projection matrices.
$$
\boldsymbol{q}_m^{\boldsymbol{\top}} \boldsymbol{k}_n=\boldsymbol{x}_m^{\boldsymbol{\top}} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\boldsymbol{p}_m^{\boldsymbol{\top}} \mathbf{U}_q^{\top} \mathbf{U}_k \boldsymbol{p}_n+b_{i, j}
$$
^Equation9

The authors of He et al. argued that the relative positions of two tokens could only be fully modeled using the middle two terms of [](.md#^Equation6%7CEquation%20(6)). As a consequence, the absolute position embeddings $\boldsymbol{p}_m$ and $\boldsymbol{p}_n$ were simply replaced with the relative position embeddings $\tilde{\boldsymbol{p}}_{m-n}$ :
$$
\boldsymbol{q}_m^{\top} \boldsymbol{k}_n=\boldsymbol{x}_m^{\top} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n+\boldsymbol{x}_m^{\top} \boldsymbol{W}_q^{\top} \boldsymbol{W}_k \tilde{\boldsymbol{p}}_{m-n}+\tilde{\boldsymbol{p}}_{m-n}^{\top} \boldsymbol{W}_q^{\boldsymbol{\top}} \boldsymbol{W}_k \boldsymbol{x}_n
$$
^Equation10

A comparison of the four variants of the relative position embeddings Radford and Narasimhan has shown that the variant similar to [](.md#^Equation10%7CEquation%20(10)) is the most efficient among the other three. Generally speaking, all these approaches attempt to modify [](.md#^Equation6%7CEquation%20(6)) based on the decomposition of [](.md#^Equation3%7CEquation%20(3)) under the self-attention settings in [](.md#^Equation2%7CEquation%20(2)), which was originally proposed in Vaswani et al.. They commonly introduced to directly add the position information to the context representations. Unlikely, our approach aims to derive the relative position encoding from [](.md#^Equation1%7CEquation%20(1)) under some constraints. Next, we show that the derived approach is more interpretable by incorporating relative position information with the rotation of context representations.

### Rotary positional embedding

**Rotary Position Embedding**, or **RoPE**, is a type of position embedding which encodes absolute positional information with [rotation matrix](https://en.wikipedia.org/wiki/Rotation_matrix) and naturally incorporates explicit relative position dependency in self-attention formulation. 

> Rotary Positional Embedding (RoPE) is a new type of position encoding that unifies absolute and relative approaches.
> -- <cite>[Rotary Embeddings: A Relative Revolution](https://blog.eleuther.ai/rotary-embeddings/)</cite>

Notably, RoPE comes with valuable properties such as flexibility of being expand to any sequence lengths, decaying inter-token dependency with increasing relative distances, and capability of equipping the linear self-attention with relative position encoding.

#### General form

In order to generalize our results in 2D to any $\boldsymbol{x}_i \in \mathbb{R}^d$ where $d$ is even, we divide the d-dimension space into $d / 2$ sub-spaces and combine them in the merit of the linearity of the inner product, turning $f_{\{q, k\}}$ into:
$$
f_{\{q, k\}}\left(\boldsymbol{x}_m, m\right)=\boldsymbol{R}_{\Theta, m}^d \boldsymbol{W}_{\{q, k\}} \boldsymbol{x}_m
$$
where
$$
\boldsymbol{R}_{\Theta, m}^d=\left(\begin{array}{ccccccc}
\cos m \theta_1 & -\sin m \theta_1 & 0 & 0 & \cdots & 0 & 0 \\
\sin m \theta_1 & \cos m \theta_1 & 0 & 0 & \cdots & 0 & 0 \\
0 & 0 & \cos m \theta_2 & -\sin m \theta_2 & \cdots & 0 & 0 \\
0 & 0 & \sin m \theta_2 & \cos m \theta_2 & \cdots & 0 & 0 \\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
0 & 0 & 0 & 0 & \cdots & \cos m \theta_{d / 2} & -\sin m \theta_{d / 2} \\
0 & 0 & 0 & 0 & \cdots & \sin m \theta_{d / 2} & \cos m \theta_{d / 2}
\end{array}\right)
$$
is the rotary matrix with pre-defined parameters $\Theta=\left\{\theta_i=10000^{-2(i-1) / d}, i \in[1,2, \ldots, d / 2]\right\}$. A graphic illustration of RoPE is shown as follows:
![500](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/Transformer/Position%20embedding/IMG-20250410192151400.png)
Applying our RoPE to self-attention in [](.md#^Equation2%7CEquation%20(2)), we obtain:
$$
\boldsymbol{q}_m^{\top} \boldsymbol{k}_n=\left(\boldsymbol{R}_{\Theta, m}^d \boldsymbol{W}_q \boldsymbol{x}_m\right)^{\top}\left(\boldsymbol{R}_{\Theta, n}^d \boldsymbol{W}_k \boldsymbol{x}_n\right)=\boldsymbol{x}^{\top} \boldsymbol{W}_q R_{\Theta, n-m}^d \boldsymbol{W}_k \boldsymbol{x}_n
$$
where $\boldsymbol{R}_{\Theta, n-m}^d=\left(\boldsymbol{R}_{\Theta, m}^d\right)^{\top} \boldsymbol{R}_{\Theta, n}^d$. Note that $\boldsymbol{R}_{\ominus}^d$ is an orthogonal matrix, which ensures stability during the process of encoding position information. In addition, due to the sparsity of $R_{\Theta}^d$, applying matrix multiplication directly as in Equation (16) is not computationally efficient; we provide another realization in theoretical explanation.

In contrast to the additive nature of position embedding method adopted in the previous works, i.e., Equations (3) to (10), our approach is multiplicative. Moreover, RoPE naturally incorporates relative position information through rotation matrix product instead of altering terms in the expanded formulation of additive position encoding when applied with self-attention.




