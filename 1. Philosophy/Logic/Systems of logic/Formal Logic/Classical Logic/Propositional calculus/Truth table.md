真值表

Example:
| $p$ | $q$ | $p \wedge q$ | 
| --- | --- | ------------ |
| F   | F   | F            |
| F   | T   | F            |
| T   | F   | F            |
| T   | T   | T            |

Advantages:
- Give a formal defnition of what a connective "means."
- Give us a way to fgure out what a complex propositional formula says.

## Truth table for [implication](Resources/0.%20Philosophy/Implication.pdf)
蕴含连接词的真值表

| $p$ | $q$ | $p \to q$ |
|---|---|-------|
| F | F |   T   |
| F | T |   T   |
| T | F |   F   |
| T | T |   T   |

大意就是，条件为假时，结果无论怎样，整个命题都为真；只有条件为真、结果为假时，整个命题才为假。

你会想问为什么时这样，Stanford CS103会告诉你，这就是人通过直觉去定义的。

## Truth table for [biconditional](1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/Propositional%20calculus/Logical%20connective/Biconditional.md)
当且仅当的真值表

| $p$ | $q$ | $p \leftrightarrow q$ |
|---|---|-------|
| F | F |   T   |
| F | T |   F   |
| T | F |   F   |
| T | T |   T   |

本质就是两个原子命题真假一样的时候整个分子命题才为真。