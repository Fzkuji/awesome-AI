In [probability](https://en.wikipedia.org/wiki/Probability "Probability"), a **discrete-time Markov chain** (**DTMC**) is a sequence of random variables, known as a [stochastic process](https://en.wikipedia.org/wiki/Stochastic_process "Stochastic process"), in which the value of the next variable depends only on the value of the current variable, and not any variables in the past.

离散时间马尔可夫链就是有时间下标的状态：
$$
\operatorname{Pr}\left(X_{n+1}=x \mid X_{1}=x_{1}, X_{2}=x_{2}, \ldots, X_{n}=x_{n}\right)=\operatorname{Pr}\left(X_{n+1}=x \mid X_{n}=x_{n}\right)
$$
## Variations

- Time-homogeneous Markov chains (or stationary Markov chains) are processes where $\operatorname{Pr}\left(X_{n+1}=x \mid X_{n}=y\right)=\operatorname{Pr}\left(X_{n}=x \mid X_{n-1}=y\right)$ for all _n_. The probability of the transition is independent of _n_.
	时间齐次马尔可夫链，就是每次状态变化概率都是固定的，和前一个状态都没有关系了。 ^809693
- A Markov chain with memory (or a Markov chain of order _m_) where _m_ is finite, is a process satisfying $$\begin{aligned}
& \operatorname{Pr}\left(X_{n}=x_{n} \mid X_{n-1}=x_{n-1}, X_{n-2}=x_{n-2}, \ldots, X_{1}=x_{1}\right) \\
= & \operatorname{Pr}\left(X_{n}=x_{n} \mid X_{n-1}=x_{n-1}, X_{n-2}=x_{n-2}, \ldots, X_{n-m}=x_{n-m}\right) \text { for } n>m
\end{aligned}$$
	也就是不严格的马尔可夫过程了，未来状态和前几步都有关

## _n_-step transitions

The probability of going from state _i_ to state _j_ in _n_ time steps is
$$p_{i j}^{(n)}=\operatorname{Pr}\left(X_{n}=j \mid X_{0}=i\right)$$
The _n_-step transition probabilities satisfy the [Chapman–Kolmogorov equation](2.%20Mathematics/2.%20Applied%20mathematics%E2%80%8E/Probability%20theory/Stochastic%20process/Markov%20chain/Chapman%E2%80%93Kolmogorov%20equation.md), that for any _k_ such that 0 < _k_ < _n_,
$$p_{i j}^{(n)}=\sum_{r \in S} p_{i r}^{(k)} p_{r j}^{(n-k)}$$













