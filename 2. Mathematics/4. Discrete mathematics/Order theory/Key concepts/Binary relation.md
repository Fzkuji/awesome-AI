
A binary relation over a set A is a [predicate](1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/First-order%20logic/Predicate.md) R that can be applied to pairs of elements drawn from A.

- If R is a binary relation over A and it holds for the pair (a, b), we write aRb.
	- $3=3$
	- $5<7$

- If R is a binary relation over A and it does not hold for the pair (a, b), we write aR̸b.
	- $2 \ne 3$
	- $4 \nless 2$

## Visualization

Example: the relation a | b (meaning “a divides b”) over the set {1, 2, 3, 4} looks like this:
![Visualizing relations](Resources/2.%20Mathematics/4.%20Discrete%20mathematics/Order%20theory/Key%20concepts/Visualizing%20relations.png)
这样就更加直观了

## Capturing structure

Binary relations are an excellent way for capturing certain structures that appear in computer science.

两个应用场景，一个是partitions，一个是prerequisites

### Partition
（用于分割的）等价关系

A **partition of a set** is a way of splitting the set into disjoint, nonempty subsets so that every element belongs to exactly one subset.
- Two sets are disjoint if their intersection is the empty set; formally, sets $S$ and $T$ are disjoint if $S\cap T = \emptyset$.

```
Usually, the term clustering is used in data analysis rather than partitioning.
Interested to learn more? Take CS161 or CS246!
```
此处的partition似乎和机器学习中的分类（clustering/classification）不一样，这里侧重的是绝对的形式上的区分，比如在计算机编程中，判断两个对象是不是同一个类。

因为这部分叫partition，因此一定是相通的类别会放在一起，而主要依赖的对比手段就是binary relation。如果两个对象属于一类，那么就需要满足一定的binary relation。

#### Binary relation: equivalence relation

Suppose you've found a binary relation R over a set A and want to prove that it's an equivalence relation.
How exactly would you go about doing this?

This is the general template for defning a relation:
- $a \sim b$ if some property of a and b holds

$\sim$ must have the following three properties to be an equivalence relation:
| Property     | Formal definition                                                                                                                 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| Refexivity   | $∀a ∈ ℤ. a \sim a$                                                                                                                |
| Symmetry     | $\forall a \in \mathbb{Z} . \forall b \in \mathbb{Z} .(a \sim b \rightarrow b \sim a)$                                            |
| Transitivity | $\forall a \in \mathbb{Z} . \forall b \in \mathbb{Z} . \forall c \in \mathbb{Z} .(a \sim b \wedge b \sim c \rightarrow a \sim c)$ | 

具体证明细节可以看[Partitions](Resources/2.%20Mathematics/4.%20Discrete%20mathematics/Order%20theory/Key%20concepts/Partitions.pdf)这份PPT。

#### Equivalence class

Given an equivalence relation $R$ over a set $A$, for any $x \in A$, the equivalence class of $x$ is the set
$$[x]_{R}=\{y \in A \mid x R y\}$$
Intuitively, the set $[x]_{R}$ Rcontains all elements of $A$ that are related to $x$ by relation $R$.

The Fundamental Theorem of Equivalence Relations: Let R be an equivalence relation over a set $A$. Then every element $a \in A$ belongs to exactly **one** equivalence class of $R$.
一个关系中，一个元素只能属于一种类别

#### Properties of equivalence relations

Theorem: A binary relation R over a set A is an equivalence relation if and only if it is refexive and cyclic.
| Property  | Formal definition                                                                             |
| --------- | --------------------------------------------------------------------------------------------- |
| Cyclicity | $\forall a \in A . \forall b \in A . \forall c \in A .(a R b \wedge b R c \rightarrow c R a)$ | 

$R$ is cyclic = R is symmetric and transitive.

具体证明可以看[Properties of equivalence relations](Resources/2.%20Mathematics/4.%20Discrete%20mathematics/Order%20theory/Key%20concepts/Properties%20of%20equivalence%20relations.pdf)

### Prerequisite
（用于限制的）先决关系

Let's imagine that we have a prerequisite structure with no circular dependencies.

We can think about a binary relation $R$ where $aRb$ means
```
a must happen before b
```

#### Properties

| Property     | Formal definition                                                                                                                 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| Irrefexivity | $\forall a \in A . a \cancel{\mathbb{R}} a$                                                                                       |
| Asymmetry    | $\forall a \in A . \forall b \in A .(a R b \rightarrow b \cancel{\mathbb{R}} a)$                                                  | 
| Transitivity | $\forall a \in \mathbb{Z} . \forall b \in \mathbb{Z} . \forall c \in \mathbb{Z} .(a \sim b \wedge b \sim c \rightarrow a \sim c)$ |

#### Binary relation: strict order

A strict order is a relation that is irrefexive, asymmetric and transitive.
包含以上三种性质的关系叫做strict order。

Some examples:
- $x < y$
- a can run faster than b.

Strict orders are useful for representing prerequisite structures and have applications in complexity theory (measuring notions of relative hardness) and algorithms (searching and sorting).

#### Drawing strict orders

![600](Resources/2.%20Mathematics/4.%20Discrete%20mathematics/Order%20theory/Key%20concepts/Hasse%20diagram.png)
次图中的每一组数都是一个国家在奥运会上拿到金、银、铜牌的数量，本图尝试表示一个（大于）strict order。

这个图的大意是，如果三个（奖牌）数都比另一组大，那就用线链接，表示一个比另一个奖牌多。如果有大有小，就不连接，相当于没有这个关系。

##### Hasse diagram

A Hasse diagram is a graphical representation of a strict order.
Elements are drawn from bottom-to-top.

- No self loops are drawn, and none are needed! By **irrefexivity** we know they shouldn’t be there.
- Higher elements are bigger than lower elements: by **asymmetry**, the edges can only go in one direction.
- No redundant edges: by **transitivity**, we can infer the missing edges.

**The Meta Strict Order**
![The meta strict order](Resources/2.%20Mathematics/4.%20Discrete%20mathematics/Order%20theory/Key%20concepts/The%20meta%20strict%20order.png)

最后用一个范围关系来总结整个笔记，值得注意的是，Asymmetry是比Transitivity范围更大的。