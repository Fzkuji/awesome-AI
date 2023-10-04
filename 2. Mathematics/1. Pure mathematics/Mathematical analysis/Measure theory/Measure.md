# [Measure](https://en.wikipedia.org/wiki/Measure_(mathematics) "Measure (mathematics)")

根据维基百科，测度（measure）是一个从集合到扩展实数线的集合函数，它满足三个条件：非负性、零测度和可数可加性。具体来说，对于任何属于σ-代数的集合E，测度μ(E)都是非负的；空集的测度为0；对于σ-代数中的任何可数、两两不相交的集合的集合，它们的并集的测度等于它们各自测度的总和[^1]：

Let $X$ be a set and $\Sigma$ a $\sigma$-algebra over $X$. A set function $\mu$ from $\Sigma$ to the extended real number line is called a **measure** if the following conditions hold:
- Non-negativity: For all $E \in \Sigma, \mu(E) \geq 0$.
- $\mu(\varnothing)=0$.
- Countable additivity (or $\sigma$-additivity): For all countable collections $\left\{E_k\right\}_{k=1}^{\infty}$ of pairwise disjoint sets in $\Sigma$,
$$
\mu\left(\bigcup_{k=1}^{\infty} E_k\right)=\sum_{k=1}^{\infty} \mu\left(E_k\right)
$$


在概率论中，特殊的测度被称为概率测度，它还满足额外的规范性条件，即样本空间的测度为 1。这为将测度论应用于概率和统计问题提供了基础。


[^1]: https://en.wikipedia.org/wiki/Measure_(mathematics)