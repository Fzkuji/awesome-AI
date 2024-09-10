逻辑连接词

- 在[propositional calculus](../Propositional%20logic.md)中也叫propositional connective.

## Binary connective
（四个）需要有两个原子命题的连接词

- $p \wedge q$ is read "$p$ and $q$," and called a **conjunction**.
	- Also called Logical AND or logical conjunction.
	- $p \wedge q$ is true if and only if both $p$ and $q$ are true.
- $p \vee q$ is read "$p$ or $q$," and called a **disjunction**.
	- Also called Logical OR or logical disjunction.
	- $p \vee q$ is true if and only if at least one of $p$ or $q$ are true (inclusive OR)
- $p \to q$  is read "f $p$ then $q$," and called an **[implication](../../../../../../../Attachments/4.%20Artificial%20intelligence/3.%20Applications/Large%20language%20model/Models/GPT/GPT-123/IMG-20240910115137328.pdf)** or **conditional**.
	- "$p$ is necessary for $q$" means $q \to p$.
	- "$p$ is sufficient for $q$" means $p \to q$.
- $p \leftrightarrow q$  is read "$p$ if and only if $q$," and called a **biconditional**.
	- If $p$ is necessary and sufficient for $q$, then $p \leftrightarrow q$.
	- $p \leftrightarrow q$ is logically equivalent to $(p \to q) \wedge (q \to p)$ (If and only if).
	- "If and only if" is often abbreviated `iff`.
- $p \oplus q$ is read "$p$ or $q$, but not, $p$ and $q$" and called a **exclusive or** or **exclusive disjunction**.

## Unary connective
（一个）只需要一个原子命题的连接词

- $\neg p$ is read “not $p$,” and called a **[negation](../../../../../../../Attachments/1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/Propositional%20calculus/Logical%20connective/Logical%20connective/IMG-20240214165832277.pdf)**.
	- Also called logical negation or Logical NOT.
	- $\neg p$ is true if and only if $p$ is false.

## Zeroary connective
没有命题，纯真/假

- $\top$ is true and called a **truth** or **tautology**.
- $\bot$ is false and called a **falsity** or **contradiction**.