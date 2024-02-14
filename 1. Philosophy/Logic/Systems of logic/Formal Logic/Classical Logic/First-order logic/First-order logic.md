**First-order logic**—also known as **[predicate](Predicate.md) logic**, **quantificational logic**, and **first-order predicate calculus**—is a collection of [formal systems](https://en.wikipedia.org/wiki/Formal_system "Formal system") used in [mathematics](https://en.wikipedia.org/wiki/Mathematics "Mathematics"), [philosophy](https://en.wikipedia.org/wiki/Philosophy "Philosophy"), [linguistics](https://en.wikipedia.org/wiki/Linguistics "Linguistics"), and [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science"). First-order logic uses [quantified variables](https://en.wikipedia.org/wiki/Quantification_(logic) "Quantification (logic)") over non-logical objects, and allows the use of sentences that contain variables, so that rather than propositions such as "Socrates is a man", one can have expressions in the form "there exists x such that x is Socrates and x is a man", where "there exists" is a quantifier, while _x_ is a variable. This distinguishes it from [propositional logic](https://en.wikipedia.org/wiki/Propositional_logic "Propositional logic"), which does not use quantifiers or [relations](https://en.wikipedia.org/wiki/Finitary_relation "Finitary relation"); in this sense, propositional logic is the foundation of first-order logic.

如[](../Propositional%20calculus/Propositional%20logic.md#^5cd9f2%7CPropositional%20calculus)中所述，谓词逻辑就算是一阶逻辑了。

- **First-order logic** is a logical system for reasoning about properties of objects.
- Augments the logical connectives from propositional logic with
	- [predicates](Predicate.md) that describe properties of objects,
	- [](../../../../../../2.%20Mathematics/1.%20Pure%20mathematics/Mathematical%20logic/Set%20theory/Concepts/Function.md#^162e9c%7Cfunctions) that map objects to one another, and
	- [quantifers](Quantifier.md) that allow us to reason about multiple objects.

Example:
```
Likes(You, Eggs) ∧ Likes(You, Tomato) → Likes(You, Shakshuka)
```
- "You", "Eggs", "Tomato" and "Shakshuka" are called constant symbols. Unlike propositional variables, they refer to objects, not propositions.
- "Likes" is called predicate. Predicates take objects as arguments and evaluate to true or false.
- What remains are traditional propositional connectives.

## The Type-Checking Table
|                            | … operate on ... | … and produce |
| -------------------------- | ---------------- | ------------- |
| Connectives (↔, ∧, etc.) … | propositions     | a proposition |
| Predicates (=, etc.) …     | objects          | a proposition |
| Functions …                | objects          | an object     | 

这个很重要，可以用来区别符号到底是哪一种类型。

## Operator Precedence

The statement
```
∃x. P(x) ∧ R(x) ∧ Q(x)
```
is parsed like this:
```
(∃x. P(x)) ∧ (R(x) ∧ Q(x))
```

To ensure that x is properly quantifed, explicitly put parentheses around the region you want to quantify:
```
∃x. (P(x) ∧ R(x) ∧ Q(x))
```
如果要扩大使用范围就加括号

## Translating into first-order logic

First-order logic is an excellent tool for manipulating defnitions and theorems to learn more about them.
- Need to take a negation? Translate your statement into FOL, negate it, then translate it back.
- Want to prove something by contrapositive? Translate your implication into FOL, take the contrapositive, then translate it back.
**Translating statements into frst-order logic is a lot more dificult than it looks.**

1. "All P's are Q's" translates as $∀x. (P(x) → Q(x))$.
	Example: All puppies are cute!
	错误想法是$∀x. (Puppy(x) ∧ Cute(x))$，这个命题会导致认为所有的x都是小狗
	正确结果是$∀x. (Puppy(x) → Cute(x))$。
	Useful intuition: Universally-quantifed statements are true unless there's a counterexample.
2. "Some P is a Q" translates as $∃x. (P(x) ∧ Q(x))$
	Example: Some blobfsh is cute.
	错误想法是$∃x. (Blobfsh(x) → Cute(x))$，这会导致不管是不是blobfsh，内部结果都是True
	正确结果是$∃x. (Blobfsh(x) ∧ Cute(x))$

### The aristotelian forms

| "All As are Bs"      | "Some As are Bs"     |
| -------------------- | -------------------- |
| $∀x. (A(x) → B(x))$  | $∃x. (A(x) ∧ B(x))$  |

| "No As are Bs"       | "Some As aren’t Bs"  |
| -------------------- | -------------------- |
| $∀x. (A(x) → ¬B(x))$ | $∃x. (A(x) ∧ ¬B(x))$ |

### The art of translation

[The art of translation](../../../../../../Attachments/1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/First-order%20logic/First-order%20logic/IMG-20240214165832188.pdf)介绍了翻译一阶逻辑的案例。包括普通句子的翻译和集合的翻译。

### Negation in first-order logic

![600](../../../../../../Attachments/1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/First-order%20logic/First-order%20logic/IMG-20240214165832206.png)

Use the equivalences
$$
\begin{align} 
¬∀x. A ≡ ∃x. ¬A \\ 
¬∃x. A ≡ ∀x. ¬A 
\end{align}
$$
to negate quantifers.

Mechanically (技巧):
- Push the negation across the quantifer.
- Change the quantifer from ∀ to ∃ or vice-versa.

以及要灵活运用[](../Propositional%20calculus/Propositional%20logic.md#^443af0%7Cpropositional%20logic)中的一些equivalences

### Restricted quantifers

#### Quantifying over sets

The notation
$$∀x ∈ S. P(x)$$
means “for any element x of set S, P(x) holds.” (It’s vacuously true if S is empty.)

The notation
$$∃x ∈ S. P(x)$$
means “there is an element x of set S where P(x) holds.” (It’s false if S is empty.)

The syntax
$$
\begin{align} 
∀x ∈ S. φ \\ 
∃x ∈ S. φ
\end{align}
$$
is allowed for quantifying over sets.
但仅限于此
For example, **don't** do things like this:
 - ∀x with P(x). Q(x)
 - ∃P(x). Q(x)

#### Expressing uniqueness

You sometimes see a special “uniqueness quantifer” used to express this:
$$∃!x. P(x)$$

## Conclusion
这部分非常重要

First-order logic is an excellent tool for giving formal defnitions to key terms.
While frst-order logic guides the structure of proofs, it is exceedingly rare to see frst-order logic in written proofs.
大意就是一阶逻辑在定义问题的时候很有用，但是证明的时候用的不多（可能是因为太复杂了）。

Follow the example of these proofs:
- Use the FOL defnitions to determine what to assume and what to prove.
- Write the proof in plain English using the conventions we set up in the first week of the class.
因此通常先用一阶逻辑理清要证明的内容，然后用正常的英语和证明方法来解题。