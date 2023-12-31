# Random variable

Let $(\Omega, \mathcal{F}, \mathbb{P})$ be a probability space. A random variable is a function $X: \Omega \rightarrow \mathbb{R}$ that assigns a real number $X(\omega)$ to each outcome $\omega$. Additionally, if $\mathcal{B}$ denotes the Borel sigma-algebra of $\mathbb{R}$, then $X^{-1}(B) \in \mathcal{F} \forall B \in \mathcal{B}$. Note: You can think of $\mathcal{B}$ as the set of all "nice" subsets of $\mathbb{R}$ (called Borel sets) such as open intervals and closed intervals.

假设 $(\Omega, \mathcal{F}, \mathbb{P})$ 是一个概率空间（[probability space](Probability.md#Probability%20space)），其中 $\Omega$ 是[样本空间](Sample%20space.md)， $\mathcal{F}$ 是事件的集合，而 $\mathbb{P}$ 是定义在这些事件上的概率度量。随机变量是一个函数 $X: \Omega \rightarrow \mathbb{R}$，它将每个结果 $\omega$ 映射到一个实数 $X(\omega)$。

*下面这两句话虽然我可以理解，并且很明显是对的，但是意义不明，不知道老师为什么要提*

另外，如果 $\mathcal{B}$ 表示实数集 $\mathbb{R}$ 的[Borel σ-代数](../../1.%20Pure%20mathematics/Mathematical%20analysis/Measure%20theory/Borel%20set.md#Borel%20algebra)，那么 $X^{-1}(B) \in \mathcal{F}$ 对所有 $B \in \mathcal{B}$ 都成立。

注意：你可以将 $\mathcal{B}$ 看作是所有实数集 $\mathbb{R}$ 的"好"子集（称为 Borel 集）的集合，比如开区间和闭区间。

## Distribution

The distribution of $X$ is the [probability measure](Probability%20measure.md) $\mathbb{P}_X(B)=\mathbb{P}(X \in B) =\mathbb{P}(\{\omega \in \Omega: X(\omega) \in B\}) \forall B \in \mathcal{B}$. 

Note: we will often just write $\mathbb{P}$ instead of $\mathbb{P}_X$.

## Example

抛掷一枚硬币十次。序列中的正面朝上的次数是一个随机变量 $X(\omega)$。如果 $\omega=H$ HHHHTTTTT，那么 $X(\omega)=5$。