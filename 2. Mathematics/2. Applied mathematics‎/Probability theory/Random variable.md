# Random variable

1. **随机变量定义**：
   - 定义 1.3：假设 $(\Omega, \mathcal{F}, \mathbb{P})$ 是一个[Probability](Probability#Probability%20space.md)概率空间。随机变量是一个函数 $X: \Omega \rightarrow \mathbb{R}$，它将每个结果 $\omega$ 映射到一个实数 $X(\omega)$。另外，如果 $\mathcal{B}$ 表示实数集 $\mathbb{R}$ 的 Borel σ-代数，那么 $X^{-1}(B) \in \mathcal{F}$ 对所有 $B \in \mathcal{B}$ 都成立。注意：你可以将 $\mathcal{B}$ 看作是所有实数集 $\mathbb{R}$ 的"好"子集（称为 Borel 集）的集合，比如开区间和闭区间。

2. **随机变量的分布**：
   - 随机变量 $X$ 的分布是概率度量 $\mathbb{P}_X(B)=\mathbb{P}(X \in B) = \mathbb{P}(\{\omega \in \Omega: X(\omega) \in B\})$ 对所有 $B \in \mathcal{B}$ 都成立。注意：我们通常会直接写 $\mathbb{P}$ 而不是 $\mathbb{P}_X$。

3. **示例**：
   - 抛掷一枚硬币十次。序列中的正面朝上的次数是一个随机变量 $X(\omega)$。如果 $\omega=H$ HHHHTTTTT，那么 $X(\omega)=5$。


