# Definition

A function $\mathbb{P}$ that assigns a real number $\mathbb{P}(A)$ to each event $A \in \mathcal{F}$ is a **probability measure/distribution** if it satisfies the following three axioms:

- Axiom 1: $\mathbb{P}(A) \geq 0 \forall A \in \mathcal{F}$.
- Axiom 2: $\mathbb{P}(\Omega)=1$.
- Axiom 3: If $A_1, A_2, \ldots$ are disjoint, then $\mathbb{P}\left(\cup_{i=1}^{\infty} A_i\right)=\sum_{i=1}^{\infty} \mathbb{P}\left(A_i\right)$.

*Note: Disjoint events are also called mutually exclusive events.*

## Exercise: Prove the following
练习：证明以下内容

1. $\mathbb{P}(\varnothing)=0$.
**空集的概率是 $\mathbf{0}$**
根据公理3，对于任何不相交的事件集合 $A_1, A_2, \ldots$, 有
$$
\mathbb{P}\left(\bigcup_{i=1}^{\infty} A_i\right)=\sum_{i=1}^{\infty} \mathbb{P}\left(A_i\right)
$$
如果我们考虑空集 $\varnothing$ 和它自己, 由于它们是不相交的, 我们可以得到
$$
\mathbb{P}(\varnothing \cup \varnothing)=\mathbb{P}(\varnothing)+\mathbb{P}(\varnothing)
$$
但 $\varnothing \cup \varnothing=\varnothing$, 所以
$$
\mathbb{P}(\varnothing)+\mathbb{P}(\varnothing)=\mathbb{P}(\varnothing)
$$
这意味着 $\mathbb{P}(\varnothing)=0$ 。

2. If $A_1, A_2, \ldots, A_n$ are disjoint, where $n$ is a positive integer, then $\mathbb{P}\left(\cup_{i=1}^n A_i\right)=\sum_{i=1}^n \mathbb{P}\left(A_i\right)$.
**有限不相交事件的概率**
这是公理3的直接应用, 只不过是在有限的情况下而已。因此对于任何不相交的事件 $A_1, A_2, \ldots, A_n$, 我们有
$$
\mathbb{P}\left(\bigcup_{i=1}^n A_i\right)=\sum_{i=1}^n \mathbb{P}\left(A_i\right)
$$

3. $\mathbb{P}\left(A^c\right)=1-\mathbb{P}(A)$.
**补集的概率**
注意到 $A \cup A^c=\Omega$ 和 $A \cap A^c=\varnothing$, 我们可以得到
$$
1=\mathbb{P}(\Omega)=\mathbb{P}\left(A \cup A^c\right)=\mathbb{P}(A)+\mathbb{P}\left(A^c\right)-\mathbb{P}(\varnothing)=\mathbb{P}(A)+\mathbb{P}\left(A^c\right)
$$
解这个方程得到 $\mathbb{P}\left(A^c\right)=1-\mathbb{P}(A)$ 。


4. If $A \subset B$, then $\mathbb{P}(A) \leq \mathbb{P}(B)$.
**包含关系下的概率**
如果 $A \subset B$, 那么 $B=A \cup(B \backslash A)$, 而 $A$ 和 $B \backslash A$ 是不相交的。因此, 我们有
$$
\mathbb{P}(B)=\mathbb{P}(A)+\mathbb{P}(B \backslash A) \geq \mathbb{P}(A)
$$

5. Subadditivity: $\mathbb{P}\left(\cup_{i=1}^{\infty} A_i\right) \leq \sum_{i=1}^{\infty} \mathbb{P}\left(A_i\right)$.
**次可加性 (Subadditivity)**
注意到对于任何事件 $A_i$ 和 $A_j$ (其中 $i \neq j$ ) , 我们有 $A_i \cup A_j=A_i \cup\left(A_j \backslash A_i\right)$, 而 $A_i$ 和 $A_j \backslash A_i$ 是不相交的。应用这个想法, 我们可以得到
$$
\mathbb{P}\left(\bigcup_{i=1}^{\infty} A_i\right) \leq \sum_{i=1}^{\infty} \mathbb{P}\left(A_i\right)
$$

## Lemmas & theorems

**Lemma 1.1:** For any events $A$ and $B, \mathbb{P}(A \cup B)=\mathbb{P}(A)+\mathbb{P}(B)-\mathbb{P}(A \cap B)$. 

Proof: 
1. 事件分解:
我们可以将事件 $A \cup B$ 分解为两个不相交的事件 $A \backslash B$ 和 $B \backslash A$ 以及 $A \cap B$ 。具体来 说, 我们有
$$
A \cup B=(A \backslash B) \cup(B \backslash A) \cup(A \cap B)
$$
2. 应用概率的可加性：
由于 $A \backslash B 、 B \backslash A$ 和 $A \cap B$ 是两两不相交的事件, 我们可以应用概率的可加性, 得到
$$
\mathbb{P}(A \cup B)=\mathbb{P}(A \backslash B)+\mathbb{P}(B \backslash A)+\mathbb{P}(A \cap B)
$$
3. 重写不相交事件的概率:
注意到 $A=(A \backslash B) \cup(A \cap B)$ 和 $B=(B \backslash A) \cup(A \cap B)$ 是两个不相交事件的并 集，我们可以得到
$$
\mathbb{P}(A)=\mathbb{P}(A \backslash B)+\mathbb{P}(A \cap B)
$$
和
$$
\mathbb{P}(B)=\mathbb{P}(B \backslash A)+\mathbb{P}(A \cap B)
$$
4. 代入求解:
现在我们可以将第3步中的表达式代入第2步中得到的等式，得到
$$
\mathbb{P}(A \cup B)=(\mathbb{P}(A)-\mathbb{P}(A \cap B))+(\mathbb{P}(B)-\mathbb{P}(A \cap B))+\mathbb{P}(A \cap B)
$$
简化得
$$
\mathbb{P}(A \cup B)=\mathbb{P}(A)+\mathbb{P}(B)-\mathbb{P}(A \cap B)
$$

**Theorem 1.2 包含-排除定理:** The inclusion-exclusion formula. If $A_1, A_2, \ldots$ are events, then:
$$\mathbb{P}\left(\cup_{i=1}^n A_i\right)=\sum_{i=1}^n \mathbb{P}\left(A_i\right)-\sum_{1 \leq i<j \leq n} \mathbb{P}\left(A_i \cap A_j\right) + \sum_{1 \leq i<j<k \leq n} \mathbb{P}\left(A_i \cap A_j \cap A_k\right)-\cdots+(-1)^{n+1} \mathbb{P}\left(A_i \cap \cdots \cap A_n\right)$$
Proof: By induction.
- 包含-排除原理是用来计算多个事件的并集的概率。公式表明，要计算 $n$个事件的并集的概率，需要首先加总每个单独事件的概率，然后减去所有两两事件交集的概率，接着加回所有三个事件的交集的概率，以此类推，直到最后一个项，它是所有$n$个事件的交集的概率，符号随项数的奇偶性变化。证明通常通过数学归纳法完成。

**Theorem 1.3 概率的连续性:** Continuity of $\mathbb{P}$ for monotone sequences. If $A_1, A_2, \ldots$ is a sequence of events that is monotone increasing or monotone decreasing, then $\lim _{n \rightarrow \infty} \mathbb{P}\left(A_n\right)=\mathbb{P}(A)$, where $A=\lim _{n \rightarrow \infty} A_n$.
Proof: For increasing sets, $A_n=A_1 \cup\left(A_2 \backslash A_1\right) \cup \cdots \cup\left(A_n \backslash A_{n-1}\right)$.
   - 这个定理描述了一个事件序列的概率如何随着序列的变化而变化。如果有一个单调递增或单调递减的事件序列$A_1, A_2, \ldots$，那么该序列的概率的极限是一个确定的值，这个值就是事件$A$的概率，其中$A$是事件序列的极限。定理给出了对于单调递增序列，如何将 $A_n$表示为前$n$个事件的并集。通过这种表示，可以明确概率的连续性，即$\lim _{n \rightarrow \infty} \mathbb{P}\left(A_n\right)=\mathbb{P}(A)$。

*这两个定理在概率论和统计学中有着广泛的应用，它们提供了计算复杂事件概率和理解概率行为随着事件变化的规律的基础。*


# Finite sample spaces

1.  **Uniform probability distribution** - 均匀分布:
	- 当样本空间 $\Omega$ 是有限的，并且 $\mathcal{F}$ 是 $\Omega$ 的[幂集/Power set](../../1.%20Pure%20mathematics/Mathematical%20logic/Set%20theory/Concepts/Power%20set.md)时，定义了一个均匀概率分布 $\mathbb{P}(A)=$ $|A| /|\Omega|$ ，其中 $A$ 是 $\mathcal{F}$ 中的事件，而 $|A|$ 是集合 $A$ 的基数（即元素数量）。在这个分布 中，所有事件的概率仅仅依赖于它们包含的元素数量。

2. **Probability space** - 概率空间:
	- 三元组 $(\Omega, \mathcal{F}, \mathbb{P})$ 被称为概率空间。它是概率论的基础结构，其中 $\Omega$ 是样本空间， $\mathcal{F}$ 是 事件的集合，而 $\mathbb{P}$ 是定义在这些事件上的概率度量。

3. Counting **permutations and combinations** - 排列和组合的计数:
	- 这里提了一嘴排列组合
	- [Permutation](../../4.%20Discrete%20mathematics/Combinatorics/Permutation.md) - 排列，是指从 $n$ 个对象中按照顺序选择所有对象的方法数量，总共有 $n$ ! 种方法 (其中 $n$ ! 表示 $n$ 的阶乘)。
	- [Combination](../../4.%20Discrete%20mathematics/Combinatorics/Combination.md) - 组合，是指从 $n$ 个对象中选 择 $k$ 个对象的方法数量，而不考虑顺序，总共有 $\left(\begin{array}{l}n \\ k\end{array}\right)=\frac{n !}{k !(n-k) !}$ 种方法。

# Independence

- **Definition 1.1 独立分布:** Two events $A$ and $B$ are independent if $\mathbb{P}(A \cap B)=$ $\mathbb{P}(A) \mathbb{P}(B)$. 

- The events $A_i, i \in I$ are independent if $\mathbb{P}\left(\cap_{i \in J} A_i\right)=$ $\prod_{i \in J} \mathbb{P}\left(A_i\right)$ for every finite subset $J$ of $I$. 
- Note: The index set $I$ can consists of infinitely many elements.


# Conditional probability

- **Definition 1.2 条件概率:** If $\mathbb{P}(B)>0$, the conditional probability of $A$ given $B$ is $\mathbb{P}(A \mid B)=\mathbb{P}(A \cap B) / \mathbb{P}(B)$.

- Note $\mathbb{P}(\cdot \mid B)$ is a [probability measure](Probability%20measure.md).

- **Lemma 1.4:** If $A$ and $B$ are independent events, then $\mathbb{P}(A \mid B)=\mathbb{P}(A)$.
- **Lemma 1.5:** For any pair of events, $\mathbb{P}(A \cap B)=\mathbb{P}(A \mid B) \mathbb{P}(B)=$ $\mathbb{P}(B \mid A) \mathbb{P}(A)$.

## Example question

A medical test for a disease $D$ has outcomes + and -. The probabilities are:

|     |  $D$  | $D^c$ |
|:---:|:-----:|:-----:|
|  +  | 0.009 | 0.099 |
|  -  | 0.001 | 0.891 |

Suppose you go for a test and get a positive. What is the probability you have the disease?

这个问题可以通过贝叶斯定理来解决。贝叶斯定理是一种计算条件概率的方法，它可以帮助我们更新我们对某个假设的信念，基于新的证据。在这个例子中，我们想要计算在测试结果为阳性的条件下，患有疾病$D$的概率。我们可以用符号$P(D | +)$来表示这个概率。

贝叶斯定理的公式如下：
$$P(D | +) = \frac{P(+ | D) \cdot P(D)}{P(+)}$$
我们可以从表中得到以下信息：

1. $P(+ | D)$是在患有疾病$D$的条件下，测试结果为阳性的概率，等于$0.009$。
2. $P(D)$是患有疾病$D$的概率，等于$0.009 + 0.001 = 0.01$（因为我们的测试可以是阳性或阴性）。
3. $P(+)$是测试结果为阳性的概率，等于$0.009 + 0.099 = 0.108$（无论是否患有疾病）。

现在我们可以将这些值代入贝叶斯定理的公式中：
$$P(D | +) = \frac{0.009 \cdot 0.01}{0.108} = \frac{0.00009}{0.108} \approx 0.00083$$
所以，如果您的测试结果是阳性，那么您患有疾病$D$的概率约为$0.083$或$8.3\%$。