
- **Propositional calculus** is a branch of logic. It is also called **propositional logic**, statement logic, sentential calculus, sentential logic, or sometimes **zeroth-order logic**.
- It deals with [[Proposition|propositions]] (which can be true or false) and relations between propositions, including the construction of arguments based on them. [^1.]
	**命题演算**是考虑**命题**之间关系的理论。一个命题可以理解为一个句子，因此命题演算是句子和句子之间的推导。
- It is a mathematical system for reasoning about propositions and how they relate to one another. [^2.]
- Every statement in propositional logic consists of [[Propositional variable|propositional variables]] combined via [[Logical connective|propositional connectives]]. [^2.]
	<u>斯坦福课上说一个statement是命题和逻辑连接词的组合。我更愿意说：一个复合proposition是多个原子proposition和逻辑连接词的组合。</u>

因为命题演算的单位是一整个句子，因此也被叫做零阶逻辑。而谓词逻辑，因为考虑的是一个句子内部的逻辑，因此被称作[[First-order logic]]。 ^5cd9f2

That said, propositional logic isn't expressive enough to capture all statements. For that, we need something more powerful.

大体结构为：[[Sentence]] -> [[Statement]] -> [[Proposition]] -> Predicate

# Operator precedence

The main points to remember:
- $\neg$ binds to whatever immediately follows it.
- $\wedge$ and $\vee$ bind more tightly than $\to$.
We will commonly write expressions like $p \wedge q \to r$ without adding parentheses.

| Connective | Read As          | C++ Version | Fancy Name    |
| ---------- | ---------------- | ----------- | ------------- |
| ¬          | “not”            | !           | Negation      |
| ∧          | “and”            | &&          | Conjunction   |
| ∨          | “or”             | $\parallel$ | Disjunction   |
| →          | “implies”        | see PS2!    | Implication   |
| ↔          | “if and only if” | see PS2!    | Biconditional |
| ⊤          | “true”           | true        | Truth         |
| ⊥          | “false”          | false       | Falsity       | 

# Translating into propositional logic

- "$p$ if $q$" translates to $q \to p$. It does not translate to $p \to q$
- "$p$, but $q$" translates to $p \wedge q$

When translating into or out of propositional logic, be very careful not to get tripped up by nuances of the English language.

# Propositional Equivalences

The notation $\varphi \equiv \psi$ means $\varphi$ and $\psi$ always have the same truth values, regardless of how the variables are assigned. 

So $\equiv$ is means equivalent.

## Why this matters

- Propositional logic is a tool for reasoning about how various statements afect one another. 
- To better understand how to prove a result, it often helps to translate what you're trying to prove into propositional logic frst. 

## De Morgan's Laws

- $\neg (p \wedge q)$ is equivalent to $\neg p \vee \neg q$
- $\neg (p \vee q)$ is equivalent to $\neg p \wedge \neg q$

Therefore we write code like this:
```java
if (!p() || !q()) {
	/* … */ 
}
```
instead of:
```java
if (!(p() && q()) {
	/* … */ 
}
```
That is we want to change form from $\neg (p \wedge q)$ to $\neg p \vee \neg q$.

## Some other equivalences
^443af0
1. [[Implication.pdf|Implication]]
$$p \to q \equiv \neg p \vee q$$
2. [[Contraposition]]
$$p \to q \equiv \neg q \vee \neg p$$

[^1.]: Wikipedia contributors. ["Propositional calculus."]([https://en.wikipedia.org/w/index.php?title=Propositional_calculus&oldid=1129332935](https://en.wikipedia.org/w/index.php?title=Propositional_calculus&oldid=1129332935)) _Wikipedia, The Free Encyclopedia_. Wikipedia, The Free Encyclopedia, 24 Dec. 2022. Web. 22 Feb. 2023.
[^2.]: Stanford University CS103: Mathematical Foundations of Computing | [03: Propositional Logic](http://web.stanford.edu/class/archive/cs/cs103/cs103.1184/lectures/03/Small03.pdf)