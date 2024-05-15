# Permutation
排列，缩写为P

似乎这个概念详细讲起来还比较复杂，这里就记录初中级别的概念。


### *k*-permutations of *n*

简单来说就是从*n*中选*k*个元素，同时还考虑顺序

In older literature and elementary textbooks, a **_k_-permutation of _n_** (sometimes called a **[partial permutation](https://en.wikipedia.org/wiki/Partial_permutation#Restricted_partial_permutations "Partial permutation")**, **sequence without repetition**, **variation**, or **arrangement**) means an ordered arrangement (list) of a _k_-element subset of an _n_-set. The number of such _k_-permutations (_k_-arrangements) of $n$ is denoted variously by such symbols as $P_{k}^{n}$, $_{n}P_{k}$, $^{n}\!P_{k}$, $P_{n,k}$, $P(n,k)$, or $A_{n}^{k}$, computed by the formula:
$$P(n,k)=\underbrace {n\cdot (n-1)\cdot (n-2)\cdots (n-k+1)} _{k\ \mathrm {factors} },$$
which is 0 when _k_ > _n_, and otherwise is equal to:
$${\frac {n!}{(n-k)!}}.$$

The product is well defined without the assumption that $ n $ is a non-negative integer, and is of importance outside combinatorics as well; it is known as the [Pochhammer symbol](https://en.wikipedia.org/wiki/Pochhammer_symbol "Pochhammer symbol") $(n)_{k}$ or as the $k$-th falling factorial power $n^{\underline {k}}$:
$$P(n,k)={_{n}}P_{k}=(n)_{k}=n^{\underline {k}}.$$

This usage of the term _permutation_ is closely associated with the term _[combination](https://en.wikipedia.org/wiki/Combination "Combination")_ to mean a subset. A _k-combination_ of a set _S_ is a *k*-element subset of _S_: the elements of a combination are not ordered. Ordering the _k_-combinations of _S_ in all possible ways produces the _k_-permutations of _S_. The number of _k_-combinations of an _n_-set, _C_(_n_,_k_), is therefore related to the number of _k_-permutations of _n_ by:
$$C(n,k)={\frac {P(n,k)}{P(k,k)}}={\frac {n^{\underline {k}}}{k!}}={\frac {n!}{(n-k)!\,k!}}.$$

These numbers are also known as [binomial coefficients](https://en.wikipedia.org/wiki/Binomial_coefficient "Binomial coefficient"), usually denoted ${\tbinom {n}{k}}$:
$$C(n,k)={_{n}}C_{k}={\binom {n}{k}}.$$