# Probability distribution

概率分布是一种数学描述，它描述了随机现象所有可能结果的概率。它可以是离散的或连续的。离散概率分布（如投掷公平骰子的情况）通过概率质量函数为每个可能的结果分配概率。而连续概率分布（例如测量超市里火腿的重量）通常使用概率密度函数来描述，其中特定区间内的概率通过在该区间内积分概率密度函数来得到。概率分布还可以通过累积分布函数来描述，它描述了随机变量小于或等于给定值的概率。

In probability theory and statistics, a probability distribution is the mathematical function that gives the probabilities of occurrence of different possible outcomes for an experiment. It is a mathematical description of a random phenomenon in terms of its sample space and the probabilities of events (subsets of the sample space).

For instance, if $X$ is used to denote the outcome of a coin toss ("the experiment"), then the probability distribution of $X$ would take the value 0.5 ( 1 in 2 or 1/2) for $X=$ heads, and 0.5 for $X=$ tails (assuming that the coin is fair). More commonly, probability distributions are used to compare the relative occurrence of many different random values.

Probability distributions can be defined in different ways and for discrete or for continuous variables.
Distributions with special properties or for especially important applications are given specific names.


# [Functions related to probability distributions](https://en.wikipedia.org/wiki/Category:Functions_related_to_probability_distributions "Category:Functions related to probability distributions")

## Cumulative distribution function
累积分布函数

- Definition 1.4: The cumulative distribution function (CDF) of a random variable $X$ is the function $F_X: \mathbb{R} \rightarrow[0,1]$ defined by $F_X(x)=$ $\mathbb{P}(X \leq x)$.
- 累积分布函数就是

- Theorem 1.8: If two random variables $X$ and $Y$ have the same CDF, then they have the same distribution, i.e., $\mathbb{P}_X=\mathbb{P}_Y$.

- Theorem 1.9: A function $F: \mathbb{R} \rightarrow[0,1]$ is a CDF if and only if 
	1. $x_1<x_2 \Rightarrow F\left(x_1\right) \leq F\left(x_2\right)$.
	2. $\lim _{x \rightarrow-\infty} F(x)=0$ and $\lim _{x \rightarrow \infty} F(x)=1$.
	3. $F$ is right continuous, i.e., $F(x)=\lim _{\substack{y \rightarrow x \\ y>x}} F(y)$.

















