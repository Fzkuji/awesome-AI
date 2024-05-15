# Combination
组合，缩写为C

似乎这个概念详细讲起来还比较复杂，这里就记录初中级别的概念。

简单来说就是从*n*中选*k*个元素，但不考虑*k*个元素内部的顺序，因此相比较[Permutation/排列](Permutation.md)需要除以$k!$。

In mathematics, a combination is a selection of items from a set that has distinct members, such that the order of selection does not matter (unlike permutations). For example, given three fruits, say an apple, an orange and a pear, there are three combinations of two that can be drawn from this set: an apple and a pear; an apple and an orange; or a pear and an orange. More formally, a $k$-combination of a set $S$ is a subset of $k$ distinct elements of $S$. So, two combinations are identical if and only if each combination has the same members. (The arrangement of the members in each set does not matter.) If the set has $n$ elements, the number of $k$-combinations, denoted by $C(n, k)$ or $C_k^n$, is equal to the binomial coefficient
$$
\left(\begin{array}{l}
n \\
k
\end{array}\right)=\frac{n(n-1) \cdots(n-k+1)}{k(k-1) \cdots 1},
$$
which can be written using factorials as $\frac{n !}{k !(n-k) !}$ whenever $k \leq n$, and which is zero when $k>n$. This formula can be derived from the fact that each $k$ combination of a set $S$ of $n$ members has $k$ ! permutations so $P_k^n=C_k^n \times k !$ or $C_k^n=P_k^n / k !$.  The set of all k-combinations of a set $S$ is often denoted by $\left(\begin{array}{l}S \\ k\end{array}\right)$.
A combination is a combination of $n$ things taken $k$ at a time without repetition. To refer to combinations in which repetition is allowed, the terms $k$ combination with repetition, $k$-multiset, or $k$-selection, are often used. If, in the above example, it were possible to have two of any one kind of fruit there would be 3 more 2-selections: one with two apples, one with two oranges, and one with two pears.

Although the set of three fruits was small enough to write a complete list of combinations, this becomes impractical as the size of the set increases. For example, a poker hand can be described as a 5-combination $(k=5)$ of cards from a 52 card deck $(n=52)$. The 5 cards of the hand are all distinct, and the order of cards in the hand does not matter. There are 2,598,960 such combinations, and the chance of drawing any one hand at random is $1 / 2,598,960$.