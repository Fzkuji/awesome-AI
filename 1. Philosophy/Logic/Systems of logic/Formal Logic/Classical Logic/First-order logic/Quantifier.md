量词

In [logic](https://en.wikipedia.org/wiki/Mathematical_logic "Mathematical logic"), a **quantifier** is an operator that specifies how many individuals in the [domain of discourse](https://en.wikipedia.org/wiki/Domain_of_discourse "Domain of discourse") satisfy an [open formula](https://en.wikipedia.org/wiki/Open_formula "Open formula").

For instance, the [universal and existential quantifier](../../../../../../Attachments/1.%20Philosophy/Logic/Systems%20of%20logic/Formal%20Logic/Classical%20Logic/First-order%20logic/Quantifier/IMG-20240212100314545.pdf).
比如：全称量词$\forall$和存在量词$\exists$

## Existential quantifer

A statement of the form
```
∃x. some-formula
```
is true if, for some choice of *x*, the statement some-formula is true when that *x* is plugged into it.

存在，用$\exists$表示

Examples:
- ∃x. (Even(x) ∧ Prime(x))
- ∃x. (TallerThan(x, me) ∧ LighterThan(x, me))

### Edge case
```
∃x. Smiling(x)
```
If there is no one at all, then it must be false. 

## Universal quantifer

A statement of the form
```
∀x. some-formula
```
is true if, for every choice of *x*, the statement some-formula is true when *x* is plugged into it.

Examples:
- ∀p. (Puppy(p) → Cute(p))
- ∀m. (IsMillennial(m) → IsSpecial(m))

### Edge case
```
∀x. Smiling(x)
```
If there is no one at all, then it must be true. 

